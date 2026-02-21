# MVVM 核心概念

## 目錄
1. [什麼是 MVVM？](#what-is-mvvm)
2. [Model 層](#model-layer)
3. [View 層](#view-layer)
4. [ViewModel 層](#viewmodel-layer)
5. [資料流](#data-flow)
6. [層依賴關係](#layer-dependencies)
7. [MVVM 的優點](#benefits-of-mvvm)

---

## 什麼是 MVVM？

MVVM（Model-View-ViewModel）是一種架構模式，將應用程式分成三個主要元件：

- **Model**：代表資料與業務邏輯
- **View**：顯示 UI 並捕捉使用者互動
- **ViewModel**：作為 Model 與 View 之間的橋樑，持有 UI 狀態並處理業務邏輯

### 核心原則

1. **關注點分離**：每一層都有單一職責
2. **單向資料流**：資料從 ViewModel 流向 View，動作從 View 流向 ViewModel
3. **可測試性**：每一層都可以獨立測試
4. **生命週期感知**：ViewModel 在設定變更時存活

---

## Model 層

Model 層代表應用程式的資料與業務規則。

### 元件

#### 領域模型

```kotlin
// Pure Kotlin data class - no Android dependencies
data class User(
    val id: Long,
    val name: String,
    val email: String,
    val isVerified: Boolean,
    val createdAt: Instant
)

data class Product(
    val id: String,
    val name: String,
    val price: BigDecimal,
    val description: String,
    val imageUrl: String,
    val category: Category
)

enum class Category {
    ELECTRONICS,
    CLOTHING,
    FOOD,
    BOOKS
}
```

#### Repository 介面（領域層）

```kotlin
// Contract for data operations - no implementation details
interface UserRepository {
    suspend fun getUser(id: Long): User?
    suspend fun getAllUsers(): List<User>
    fun observeUser(id: Long): Flow<User?>
    fun observeAllUsers(): Flow<List<User>>
    suspend fun saveUser(user: User): Long
    suspend fun updateUser(user: User)
    suspend fun deleteUser(id: Long)
    suspend fun searchUsers(query: String): List<User>
}
```

#### Use Case（選用的領域層）

```kotlin
// Encapsulates a single business operation
class GetUserWithPostsUseCase @Inject constructor(
    private val userRepository: UserRepository,
    private val postRepository: PostRepository
) {
    suspend operator fun invoke(userId: Long): UserWithPosts? {
        val user = userRepository.getUser(userId) ?: return null
        val posts = postRepository.getPostsByUser(userId)
        return UserWithPosts(user, posts)
    }
}

data class UserWithPosts(
    val user: User,
    val posts: List<Post>
)
```

### 資料來源

#### 本地資料來源（Room 資料庫）

```kotlin
@Entity(tableName = "users")
data class UserEntity(
    @PrimaryKey val id: Long,
    @ColumnInfo(name = "name") val name: String,
    @ColumnInfo(name = "email") val email: String,
    @ColumnInfo(name = "is_verified") val isVerified: Boolean,
    @ColumnInfo(name = "created_at") val createdAt: Long
)

@Dao
interface UserDao {
    @Query("SELECT * FROM users")
    fun observeAll(): Flow<List<UserEntity>>

    @Query("SELECT * FROM users WHERE id = :id")
    fun observeById(id: Long): Flow<UserEntity?>

    @Query("SELECT * FROM users WHERE id = :id")
    suspend fun getById(id: Long): UserEntity?

    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun insert(user: UserEntity): Long

    @Update
    suspend fun update(user: UserEntity)

    @Delete
    suspend fun delete(user: UserEntity)

    @Query("DELETE FROM users WHERE id = :id")
    suspend fun deleteById(id: Long)

    @Query("SELECT * FROM users WHERE name LIKE '%' || :query || '%'")
    suspend fun search(query: String): List<UserEntity>
}
```

#### 遠端資料來源（Retrofit API）

```kotlin
// DTO (Data Transfer Object) for API responses
data class UserDto(
    @SerializedName("id") val id: Long,
    @SerializedName("name") val name: String,
    @SerializedName("email") val email: String,
    @SerializedName("verified") val isVerified: Boolean,
    @SerializedName("created_at") val createdAt: String
)

interface UserApiService {
    @GET("users")
    suspend fun getUsers(): List<UserDto>

    @GET("users/{id}")
    suspend fun getUser(@Path("id") id: Long): UserDto

    @POST("users")
    suspend fun createUser(@Body user: UserDto): UserDto

    @PUT("users/{id}")
    suspend fun updateUser(
        @Path("id") id: Long,
        @Body user: UserDto
    ): UserDto

    @DELETE("users/{id}")
    suspend fun deleteUser(@Path("id") id: Long)
}
```

#### Repository 實作

```kotlin
class UserRepositoryImpl @Inject constructor(
    private val userDao: UserDao,
    private val userApi: UserApiService,
    @IoDispatcher private val ioDispatcher: CoroutineDispatcher
) : UserRepository {

    override fun observeAllUsers(): Flow<List<User>> {
        return userDao.observeAll()
            .map { entities -> entities.map { it.toDomain() } }
            .flowOn(ioDispatcher)
    }

    override suspend fun getUser(id: Long): User? {
        return withContext(ioDispatcher) {
            userDao.getById(id)?.toDomain()
        }
    }

    override suspend fun refreshUsers() {
        withContext(ioDispatcher) {
            try {
                val remoteUsers = userApi.getUsers()
                userDao.insertAll(remoteUsers.map { it.toEntity() })
            } catch (e: Exception) {
                // Handle network error - local data still available
                throw RefreshException("Failed to refresh users", e)
            }
        }
    }
}

// Extension functions for mapping
fun UserEntity.toDomain(): User = User(
    id = id,
    name = name,
    email = email,
    isVerified = isVerified,
    createdAt = Instant.ofEpochMilli(createdAt)
)

fun UserDto.toEntity(): UserEntity = UserEntity(
    id = id,
    name = name,
    email = email,
    isVerified = isVerified,
    createdAt = Instant.parse(createdAt).toEpochMilli()
)
```

---

## View 層

View 層負責顯示 UI 並捕捉使用者互動。

### 基於 Activity 的 View

```kotlin
@AndroidEntryPoint
class UserDetailActivity : AppCompatActivity() {

    private val viewModel: UserDetailViewModel by viewModels()
    private lateinit var binding: ActivityUserDetailBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityUserDetailBinding.inflate(layoutInflater)
        setContentView(binding.root)

        val userId = intent.getLongExtra(EXTRA_USER_ID, -1)
        viewModel.loadUser(userId)

        setupObservers()
        setupClickListeners()
    }

    private fun setupObservers() {
        lifecycleScope.launch {
            repeatOnLifecycle(Lifecycle.State.STARTED) {
                launch { collectUiState() }
                launch { collectEvents() }
            }
        }
    }

    private suspend fun collectUiState() {
        viewModel.uiState.collect { state ->
            binding.apply {
                progressBar.isVisible = state.isLoading
                contentGroup.isVisible = !state.isLoading && state.user != null
                errorGroup.isVisible = state.error != null

                state.user?.let { user ->
                    nameText.text = user.name
                    emailText.text = user.email
                    verifiedBadge.isVisible = user.isVerified
                }

                state.error?.let { error ->
                    errorText.text = error
                }
            }
        }
    }

    private suspend fun collectEvents() {
        viewModel.events.collect { event ->
            when (event) {
                is UserDetailEvent.ShowMessage -> {
                    Snackbar.make(binding.root, event.message, Snackbar.LENGTH_SHORT).show()
                }
                is UserDetailEvent.NavigateBack -> {
                    finish()
                }
            }
        }
    }

    private fun setupClickListeners() {
        binding.editButton.setOnClickListener {
            viewModel.onEditClicked()
        }

        binding.deleteButton.setOnClickListener {
            viewModel.onDeleteClicked()
        }

        binding.retryButton.setOnClickListener {
            viewModel.onRetryClicked()
        }
    }

    companion object {
        const val EXTRA_USER_ID = "user_id"

        fun newIntent(context: Context, userId: Long): Intent {
            return Intent(context, UserDetailActivity::class.java).apply {
                putExtra(EXTRA_USER_ID, userId)
            }
        }
    }
}
```

### 基於 Fragment 的 View

```kotlin
@AndroidEntryPoint
class UserListFragment : Fragment(R.layout.fragment_user_list) {

    private val viewModel: UserListViewModel by viewModels()
    private var _binding: FragmentUserListBinding? = null
    private val binding get() = _binding!!

    private lateinit var adapter: UserListAdapter

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        _binding = FragmentUserListBinding.bind(view)

        setupRecyclerView()
        setupSwipeRefresh()
        setupObservers()
    }

    private fun setupRecyclerView() {
        adapter = UserListAdapter(
            onUserClick = { user -> viewModel.onUserClicked(user) },
            onUserLongClick = { user -> viewModel.onUserLongClicked(user) }
        )

        binding.recyclerView.apply {
            layoutManager = LinearLayoutManager(requireContext())
            adapter = this@UserListFragment.adapter
            addItemDecoration(
                DividerItemDecoration(requireContext(), DividerItemDecoration.VERTICAL)
            )
        }
    }

    private fun setupSwipeRefresh() {
        binding.swipeRefresh.setOnRefreshListener {
            viewModel.onRefresh()
        }
    }

    private fun setupObservers() {
        viewLifecycleOwner.lifecycleScope.launch {
            viewLifecycleOwner.repeatOnLifecycle(Lifecycle.State.STARTED) {
                launch {
                    viewModel.uiState.collect { state ->
                        binding.swipeRefresh.isRefreshing = state.isRefreshing
                        binding.progressBar.isVisible = state.isLoading && !state.isRefreshing
                        binding.emptyView.isVisible = state.isEmpty
                        binding.errorView.isVisible = state.error != null

                        adapter.submitList(state.users)
                    }
                }

                launch {
                    viewModel.navigationEvents.collect { event ->
                        when (event) {
                            is NavigationEvent.ToUserDetail -> {
                                findNavController().navigate(
                                    UserListFragmentDirections.actionToUserDetail(event.userId)
                                )
                            }
                        }
                    }
                }
            }
        }
    }

    override fun onDestroyView() {
        super.onDestroyView()
        _binding = null
    }
}
```

### 基於 Compose 的 View

```kotlin
@Composable
fun UserListScreen(
    viewModel: UserListViewModel = hiltViewModel(),
    onNavigateToDetail: (Long) -> Unit
) {
    val uiState by viewModel.uiState.collectAsStateWithLifecycle()

    LaunchedEffect(Unit) {
        viewModel.navigationEvents.collect { event ->
            when (event) {
                is NavigationEvent.ToUserDetail -> {
                    onNavigateToDetail(event.userId)
                }
            }
        }
    }

    UserListContent(
        uiState = uiState,
        onUserClick = { viewModel.onUserClicked(it) },
        onRefresh = { viewModel.onRefresh() },
        onRetry = { viewModel.onRetryClicked() }
    )
}

@Composable
private fun UserListContent(
    uiState: UserListUiState,
    onUserClick: (User) -> Unit,
    onRefresh: () -> Unit,
    onRetry: () -> Unit
) {
    Box(modifier = Modifier.fillMaxSize()) {
        when {
            uiState.isLoading && uiState.users.isEmpty() -> {
                CircularProgressIndicator(
                    modifier = Modifier.align(Alignment.Center)
                )
            }
            uiState.error != null && uiState.users.isEmpty() -> {
                ErrorContent(
                    message = uiState.error,
                    onRetry = onRetry,
                    modifier = Modifier.align(Alignment.Center)
                )
            }
            uiState.users.isEmpty() -> {
                EmptyContent(
                    modifier = Modifier.align(Alignment.Center)
                )
            }
            else -> {
                SwipeRefresh(
                    state = rememberSwipeRefreshState(uiState.isRefreshing),
                    onRefresh = onRefresh
                ) {
                    LazyColumn {
                        items(
                            items = uiState.users,
                            key = { it.id }
                        ) { user ->
                            UserListItem(
                                user = user,
                                onClick = { onUserClick(user) }
                            )
                        }
                    }
                }
            }
        }
    }
}
```

---

## ViewModel 層

ViewModel 作為 View 層與 Model 層之間的橋樑。

### 核心職責

1. **持有 UI 狀態**：維護 UI 的當前狀態
2. **處理使用者動作**：處理使用者互動
3. **公開可觀察資料**：提供資料流供 View 觀察
4. **在設定變更時存活**：在螢幕旋轉時保持狀態

### 基本 ViewModel 結構

```kotlin
@HiltViewModel
class UserListViewModel @Inject constructor(
    private val getUsersUseCase: GetUsersUseCase,
    private val refreshUsersUseCase: RefreshUsersUseCase
) : ViewModel() {

    // UI State
    private val _uiState = MutableStateFlow(UserListUiState())
    val uiState: StateFlow<UserListUiState> = _uiState.asStateFlow()

    // One-time events (navigation, snackbars)
    private val _navigationEvents = MutableSharedFlow<NavigationEvent>()
    val navigationEvents: SharedFlow<NavigationEvent> = _navigationEvents.asSharedFlow()

    init {
        observeUsers()
    }

    private fun observeUsers() {
        viewModelScope.launch {
            getUsersUseCase()
                .onStart {
                    _uiState.update { it.copy(isLoading = true) }
                }
                .catch { error ->
                    _uiState.update {
                        it.copy(isLoading = false, error = error.message)
                    }
                }
                .collect { users ->
                    _uiState.update {
                        it.copy(
                            isLoading = false,
                            users = users,
                            isEmpty = users.isEmpty()
                        )
                    }
                }
        }
    }

    fun onRefresh() {
        viewModelScope.launch {
            _uiState.update { it.copy(isRefreshing = true) }

            try {
                refreshUsersUseCase()
            } catch (e: Exception) {
                _uiState.update { it.copy(error = e.message) }
            } finally {
                _uiState.update { it.copy(isRefreshing = false) }
            }
        }
    }

    fun onUserClicked(user: User) {
        viewModelScope.launch {
            _navigationEvents.emit(NavigationEvent.ToUserDetail(user.id))
        }
    }

    fun onRetryClicked() {
        _uiState.update { it.copy(error = null) }
        observeUsers()
    }
}
```

---

## 資料流

### 單向資料流（UDF）

```
┌──────────────────────────────────────────────────────────┐
│                                                          │
│  ┌────────┐  User Action   ┌───────────┐                │
│  │  View  │ ─────────────► │ ViewModel │                │
│  └────────┘                └───────────┘                │
│      ▲                          │                        │
│      │                          │ Call                   │
│      │ Observe                  ▼                        │
│      │                    ┌───────────┐                  │
│      │                    │Repository │                  │
│      │                    └───────────┘                  │
│      │                          │                        │
│      │                          │ Return Data            │
│      │                          ▼                        │
│      │                    ┌───────────┐                  │
│      │ Update State       │ ViewModel │                  │
│      └────────────────────┤   State   │                  │
│                           └───────────┘                  │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

### 資料流範例

```kotlin
// 1. User clicks refresh button (View → ViewModel)
binding.refreshButton.setOnClickListener {
    viewModel.onRefresh()
}

// 2. ViewModel handles action and calls repository
fun onRefresh() {
    viewModelScope.launch {
        _uiState.update { it.copy(isRefreshing = true) }
        val result = repository.refreshUsers()  // 3. Repository fetches data
        _uiState.update { it.copy(isRefreshing = false, users = result) }
    }
}

// 4. View observes state changes and updates UI
viewModel.uiState.collect { state ->
    binding.swipeRefresh.isRefreshing = state.isRefreshing
    adapter.submitList(state.users)
}
```

---

## 層依賴關係

### 依賴規則

依賴關係應朝向領域層內部：

```
UI Layer (View + ViewModel)
         ↓
    Domain Layer (Use Cases + Repository Interfaces)
         ↓
    Data Layer (Repository Implementations + Data Sources)
```

### 模組依賴（多模組專案）

```kotlin
// :app module
dependencies {
    implementation(project(":feature:user-list"))
    implementation(project(":feature:user-detail"))
    implementation(project(":core:data"))
    implementation(project(":core:domain"))
    implementation(project(":core:ui"))
}

// :feature:user-list module
dependencies {
    implementation(project(":core:domain"))
    implementation(project(":core:ui"))
}

// :core:data module
dependencies {
    implementation(project(":core:domain"))
}

// :core:domain module
// No dependencies on other modules (pure Kotlin)
```

### 類別依賴

```kotlin
// View depends on ViewModel
class UserListFragment : Fragment() {
    private val viewModel: UserListViewModel by viewModels()
}

// ViewModel depends on Use Cases / Repository
@HiltViewModel
class UserListViewModel @Inject constructor(
    private val getUsersUseCase: GetUsersUseCase
) : ViewModel()

// Use Case depends on Repository Interface
class GetUsersUseCase @Inject constructor(
    private val userRepository: UserRepository
)

// Repository Implementation depends on Data Sources
class UserRepositoryImpl @Inject constructor(
    private val localDataSource: UserDao,
    private val remoteDataSource: UserApiService
) : UserRepository
```

---

## MVVM 的優點

### 1. 關注點分離

每一層都有單一且明確的職責：

```kotlin
// View: Only UI logic
class UserListFragment : Fragment() {
    private fun updateUI(state: UiState) {
        // Only update UI elements
    }
}

// ViewModel: Only UI state and business logic
class UserListViewModel : ViewModel() {
    fun processUserAction() {
        // Process action, update state
    }
}

// Repository: Only data operations
class UserRepository {
    suspend fun getUsers(): List<User> {
        // Fetch and coordinate data
    }
}
```

### 2. 可測試性

```kotlin
// ViewModel testing - no Android dependencies needed
@Test
fun `loading users updates state correctly`() = runTest {
    // Given
    val fakeRepository = FakeUserRepository()
    val viewModel = UserListViewModel(fakeRepository)

    // When
    viewModel.loadUsers()

    // Then
    val state = viewModel.uiState.value
    assertFalse(state.isLoading)
    assertEquals(fakeRepository.users, state.users)
}
```

### 3. 生命週期感知

```kotlin
// ViewModel survives configuration changes
class UserDetailViewModel : ViewModel() {
    // This state survives rotation
    private val _userState = MutableStateFlow<User?>(null)

    // Called only once, not on every rotation
    init {
        loadUser()
    }
}
```

### 4. 可維護性

清晰的邊界使程式碼更易於理解和修改：

```kotlin
// Adding a new feature: Search
// 1. Add to ViewModel
fun onSearchQueryChanged(query: String) {
    _uiState.update { it.copy(searchQuery = query) }
    searchUsers(query)
}

// 2. Add to View
binding.searchInput.doOnTextChanged { text, _, _, _ ->
    viewModel.onSearchQueryChanged(text.toString())
}

// 3. Add to Repository (if needed)
suspend fun searchUsers(query: String): List<User>
```

### 5. 可擴展性

MVVM 從小型應用程式到大型多模組專案都能良好擴展：

```
Small App:
├── ui/
│   └── UserListFragment.kt
│   └── UserListViewModel.kt
└── data/
    └── UserRepository.kt

Large App:
├── feature/
│   ├── user-list/
│   ├── user-detail/
│   └── settings/
├── core/
│   ├── domain/
│   ├── data/
│   └── ui/
└── app/
```

---

## 摘要

| 層次 | 職責 | Android 依賴 | 可測試性 |
|-------|---------------|---------------------|-------------|
| View | 顯示 UI、捕捉互動 | 有（Activity/Fragment/Compose） | UI 測試（Espresso/Compose） |
| ViewModel | 持有 UI 狀態、處理動作 | 最少（ViewModel 類別） | 單元測試 |
| Model | 資料與業務邏輯 | 無（純 Kotlin） | 單元測試 |

MVVM 模式若搭配現代 Android Jetpack 函式庫正確實作，可為建立可維護、可測試且可擴展的 Android 應用程式提供堅實基礎。
