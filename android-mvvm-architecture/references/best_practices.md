# MVVM 最佳實踐與反模式

## 目錄
1. [ViewModel 最佳實踐](#viewmodel-best-practices)
2. [狀態管理](#state-management)
3. [錯誤處理](#error-handling)
4. [Repository 模式](#repository-pattern)
5. [測試策略](#testing-strategies)
6. [常見錯誤](#common-mistakes)
7. [效能最佳化](#performance-optimization)

---

## ViewModel 最佳實踐

### 1. 永遠不要在 ViewModel 中參考 View

**MVVM 第一守則**

```kotlin
// BAD - Memory leak and crashes
class BadViewModel : ViewModel() {
    lateinit var textView: TextView  // DON'T DO THIS!

    fun updateUI() {
        textView.text = "Updated"  // Crashes on config change
    }
}

// GOOD - Expose state, let View observe
class GoodViewModel : ViewModel() {
    private val _text = MutableStateFlow("")
    val text: StateFlow<String> = _text.asStateFlow()

    fun updateText() {
        _text.value = "Updated"
    }
}
```

### 2. 永遠不要在 ViewModel 中參考 Context

```kotlin
// BAD - Leaks Activity context
class BadViewModel(private val context: Context) : ViewModel() {
    fun showToast() {
        Toast.makeText(context, "Message", Toast.LENGTH_SHORT).show()
    }
}

// GOOD - Emit events, let View handle UI operations
class GoodViewModel : ViewModel() {
    private val _events = MutableSharedFlow<UiEvent>()
    val events: SharedFlow<UiEvent> = _events.asSharedFlow()

    fun showMessage() {
        viewModelScope.launch {
            _events.emit(UiEvent.ShowToast("Message"))
        }
    }
}

// If you must use Context (for strings, etc.), use Application context
class SafeViewModel @Inject constructor(
    @ApplicationContext private val appContext: Context  // Application context is safe
) : ViewModel()
```

### 3. 保持 ViewModel 的專注性

```kotlin
// BAD - ViewModel doing too much
class GodViewModel : ViewModel() {
    fun loadUsers() { /* ... */ }
    fun loadProducts() { /* ... */ }
    fun loadOrders() { /* ... */ }
    fun processPayment() { /* ... */ }
    fun trackAnalytics() { /* ... */ }
    // 50+ more methods...
}

// GOOD - Single responsibility
class UserListViewModel : ViewModel() {
    fun loadUsers() { /* ... */ }
    fun refreshUsers() { /* ... */ }
    fun deleteUser(id: Long) { /* ... */ }
}

class CheckoutViewModel : ViewModel() {
    fun loadCart() { /* ... */ }
    fun processPayment() { /* ... */ }
}
```

### 4. 使用建構子注入

```kotlin
// BAD - Manual dependency creation
class BadViewModel : ViewModel() {
    private val repository = UserRepository(
        ApiService(),
        Database()
    )
}

// GOOD - Constructor injection with Hilt
@HiltViewModel
class GoodViewModel @Inject constructor(
    private val repository: UserRepository,
    private val analyticsService: AnalyticsService
) : ViewModel()
```

---

## 狀態管理

### 1. 單一 UI 狀態物件

```kotlin
// BAD - Multiple separate states
class FragmentedViewModel : ViewModel() {
    private val _isLoading = MutableStateFlow(false)
    private val _users = MutableStateFlow<List<User>>(emptyList())
    private val _error = MutableStateFlow<String?>(null)
    private val _selectedUserId = MutableStateFlow<Long?>(null)
    // Hard to reason about, race conditions possible
}

// GOOD - Single immutable state object
data class UserListUiState(
    val isLoading: Boolean = false,
    val users: List<User> = emptyList(),
    val error: String? = null,
    val selectedUserId: Long? = null
)

class UnifiedViewModel : ViewModel() {
    private val _uiState = MutableStateFlow(UserListUiState())
    val uiState: StateFlow<UserListUiState> = _uiState.asStateFlow()

    // Atomic updates
    fun selectUser(userId: Long) {
        _uiState.update { it.copy(selectedUserId = userId) }
    }
}
```

### 2. 不可變狀態更新

```kotlin
// BAD - Mutating state directly
class MutableStateViewModel : ViewModel() {
    private val _state = MutableStateFlow(UiState())

    fun addUser(user: User) {
        // DON'T mutate lists!
        _state.value.users.add(user)  // Won't trigger recomposition
    }
}

// GOOD - Create new state with copy
class ImmutableStateViewModel : ViewModel() {
    private val _state = MutableStateFlow(UiState())

    fun addUser(user: User) {
        _state.update { currentState ->
            currentState.copy(users = currentState.users + user)
        }
    }
}
```

### 3. 分離 UI 狀態與領域狀態

```kotlin
// Domain model - pure business logic
data class User(
    val id: Long,
    val name: String,
    val email: String,
    val createdAt: Instant
)

// UI State - includes presentation logic
data class UserListUiState(
    val users: List<UserUiModel> = emptyList(),
    val isLoading: Boolean = false,
    val error: String? = null
)

// UI Model - formatted for display
data class UserUiModel(
    val id: Long,
    val displayName: String,  // Formatted
    val email: String,
    val memberSince: String   // "Member since Jan 2024"
)

// ViewModel transforms domain to UI
class UserListViewModel : ViewModel() {
    private fun User.toUiModel(): UserUiModel = UserUiModel(
        id = id,
        displayName = name.uppercase(),
        email = email,
        memberSince = "Member since ${createdAt.formatAsDate()}"
    )
}
```

### 4. 處理載入、內容、錯誤狀態

```kotlin
// Sealed class for screen states
sealed class ScreenState<out T> {
    object Loading : ScreenState<Nothing>()
    data class Content<T>(val data: T) : ScreenState<T>()
    data class Error(val message: String) : ScreenState<Nothing>()
}

// Or use embedded flags in state
data class UserListUiState(
    val isLoading: Boolean = true,
    val users: List<User> = emptyList(),
    val error: String? = null
) {
    val showContent: Boolean get() = !isLoading && error == null && users.isNotEmpty()
    val showEmpty: Boolean get() = !isLoading && error == null && users.isEmpty()
    val showError: Boolean get() = error != null
}
```

---

## 錯誤處理

### 1. 集中式錯誤處理

```kotlin
sealed class Result<out T> {
    data class Success<T>(val data: T) : Result<T>()
    data class Error(val exception: Throwable) : Result<Nothing>()
}

// Extension for safe API calls
suspend fun <T> safeApiCall(
    block: suspend () -> T
): Result<T> = try {
    Result.Success(block())
} catch (e: CancellationException) {
    throw e  // Don't catch cancellation
} catch (e: Exception) {
    Result.Error(e)
}

// Usage in ViewModel
class UserViewModel : ViewModel() {
    fun loadUser() {
        viewModelScope.launch {
            _state.update { it.copy(isLoading = true) }

            when (val result = safeApiCall { repository.getUser() }) {
                is Result.Success -> {
                    _state.update { it.copy(isLoading = false, user = result.data) }
                }
                is Result.Error -> {
                    _state.update { it.copy(isLoading = false, error = result.exception.message) }
                }
            }
        }
    }
}
```

### 2. 使用者友善的錯誤訊息

```kotlin
class ErrorHandler @Inject constructor(
    @ApplicationContext private val context: Context
) {
    fun getDisplayMessage(throwable: Throwable): String {
        return when (throwable) {
            is UnknownHostException -> context.getString(R.string.error_no_internet)
            is SocketTimeoutException -> context.getString(R.string.error_timeout)
            is HttpException -> when (throwable.code()) {
                401 -> context.getString(R.string.error_unauthorized)
                403 -> context.getString(R.string.error_forbidden)
                404 -> context.getString(R.string.error_not_found)
                500 -> context.getString(R.string.error_server)
                else -> context.getString(R.string.error_unknown)
            }
            else -> context.getString(R.string.error_unknown)
        }
    }
}
```

### 3. 重試機制

```kotlin
@HiltViewModel
class UserListViewModel @Inject constructor(
    private val repository: UserRepository
) : ViewModel() {

    private val refreshTrigger = MutableSharedFlow<Unit>(replay = 1)

    val users: StateFlow<Result<List<User>>> = refreshTrigger
        .onStart { emit(Unit) }  // Initial load
        .flatMapLatest {
            flow {
                emit(Result.Loading)
                try {
                    val users = repository.getUsers()
                    emit(Result.Success(users))
                } catch (e: Exception) {
                    emit(Result.Error(e))
                }
            }
        }
        .stateIn(viewModelScope, SharingStarted.WhileSubscribed(5000), Result.Loading)

    fun retry() {
        viewModelScope.launch {
            refreshTrigger.emit(Unit)
        }
    }
}
```

---

## Repository 模式

### 1. 單一資料來源

```kotlin
class UserRepositoryImpl @Inject constructor(
    private val userDao: UserDao,
    private val userApi: UserApiService
) : UserRepository {

    // Database is the single source of truth
    override fun observeUsers(): Flow<List<User>> {
        return userDao.observeAll()
            .map { entities -> entities.map { it.toDomain() } }
    }

    // Refresh updates database, observers get new data automatically
    override suspend fun refreshUsers() {
        val remoteUsers = userApi.getUsers()
        userDao.insertAll(remoteUsers.map { it.toEntity() })
    }

    // Fetch fresh data, update database
    override suspend fun getUsers(): List<User> {
        try {
            refreshUsers()
        } catch (e: Exception) {
            // Offline - use cached data
        }
        return userDao.getAll().map { it.toDomain() }
    }
}
```

### 2. 離線優先策略

```kotlin
class OfflineFirstRepository @Inject constructor(
    private val localDataSource: LocalDataSource,
    private val remoteDataSource: RemoteDataSource,
    private val networkMonitor: NetworkMonitor
) : DataRepository {

    override fun observeData(): Flow<List<Data>> {
        return localDataSource.observe()
            .onStart {
                // Try to refresh when subscription starts
                if (networkMonitor.isOnline()) {
                    try {
                        val remote = remoteDataSource.fetch()
                        localDataSource.save(remote)
                    } catch (e: Exception) {
                        // Network failed, local data will be emitted
                    }
                }
            }
    }
}
```

### 3. Repository 不應了解 ViewModel

```kotlin
// BAD - Repository depends on ViewModel concepts
class BadRepository {
    fun getUsers(): LiveData<List<User>>  // Android dependency
    fun getUsersState(): StateFlow<UiState>  // UI concern
}

// GOOD - Repository returns domain types
class GoodRepository {
    suspend fun getUsers(): List<User>  // Simple return
    fun observeUsers(): Flow<List<User>>  // Kotlin Flow
}
```

---

## 測試策略

### 1. 獨立測試 ViewModel

```kotlin
@OptIn(ExperimentalCoroutinesApi::class)
class UserViewModelTest {

    @get:Rule
    val mainDispatcherRule = MainDispatcherRule()

    private lateinit var fakeRepository: FakeUserRepository
    private lateinit var viewModel: UserViewModel

    @Before
    fun setup() {
        fakeRepository = FakeUserRepository()
        viewModel = UserViewModel(fakeRepository)
    }

    @Test
    fun `loadUsers success updates state with users`() = runTest {
        // Given
        val expectedUsers = listOf(User(1, "John", "john@example.com"))
        fakeRepository.setUsers(expectedUsers)

        // When
        viewModel.loadUsers()
        advanceUntilIdle()

        // Then
        val state = viewModel.uiState.value
        assertFalse(state.isLoading)
        assertEquals(expectedUsers, state.users)
        assertNull(state.error)
    }

    @Test
    fun `loadUsers error updates state with error message`() = runTest {
        // Given
        fakeRepository.setShouldError(true)

        // When
        viewModel.loadUsers()
        advanceUntilIdle()

        // Then
        val state = viewModel.uiState.value
        assertFalse(state.isLoading)
        assertTrue(state.users.isEmpty())
        assertNotNull(state.error)
    }
}
```

### 2. 使用 Fake 而非 Mock

```kotlin
// Fake - Full control, no mocking library needed
class FakeUserRepository : UserRepository {
    private var users = listOf<User>()
    private var shouldError = false

    fun setUsers(users: List<User>) { this.users = users }
    fun setShouldError(error: Boolean) { shouldError = error }

    override suspend fun getUsers(): List<User> {
        if (shouldError) throw Exception("Test error")
        return users
    }

    override fun observeUsers(): Flow<List<User>> {
        if (shouldError) return flow { throw Exception("Test error") }
        return flowOf(users)
    }
}

// Usage in test
@Test
fun `test with fake`() = runTest {
    val fake = FakeUserRepository()
    fake.setUsers(listOf(User(1, "Test", "test@example.com")))

    val viewModel = UserViewModel(fake)
    // ...
}
```

### 3. 測試狀態轉換

```kotlin
@Test
fun `loadUsers shows loading then content`() = runTest {
    // Collect states
    val states = mutableListOf<UserListUiState>()
    val job = launch(UnconfinedTestDispatcher()) {
        viewModel.uiState.toList(states)
    }

    // Trigger load
    viewModel.loadUsers()
    advanceUntilIdle()

    // Verify state transitions
    assertTrue(states[0].isLoading)  // Initial loading
    assertFalse(states[1].isLoading)  // After load
    assertTrue(states[1].users.isNotEmpty())

    job.cancel()
}
```

---

## 常見錯誤

### 錯誤一：未具備生命週期感知地收集 Flow

```kotlin
// BAD - Continues collecting in background, wastes resources
lifecycleScope.launch {
    viewModel.uiState.collect { state ->
        updateUI(state)  // Called even when app is in background!
    }
}

// GOOD - Stops collecting when lifecycle is below STARTED
viewLifecycleOwner.lifecycleScope.launch {
    viewLifecycleOwner.repeatOnLifecycle(Lifecycle.State.STARTED) {
        viewModel.uiState.collect { state ->
            updateUI(state)
        }
    }
}
```

### 錯誤二：Fragment 中未清除 Binding

```kotlin
// BAD - Memory leak
class LeakyFragment : Fragment() {
    private lateinit var binding: FragmentBinding
    // Binding survives longer than view!
}

// GOOD - Clear binding
class SafeFragment : Fragment() {
    private var _binding: FragmentBinding? = null
    private val binding get() = _binding!!

    override fun onDestroyView() {
        super.onDestroyView()
        _binding = null  // Clear reference
    }
}
```

### 錯誤三：使用 GlobalScope

```kotlin
// BAD - Lives forever, no lifecycle awareness
GlobalScope.launch {
    val data = repository.getData()
    _state.value = data  // May crash if ViewModel is cleared
}

// GOOD - Cancelled when ViewModel is cleared
viewModelScope.launch {
    val data = repository.getData()
    _state.value = data
}
```

### 錯誤四：阻塞主執行緒

```kotlin
// BAD - Blocks UI thread
class BadViewModel : ViewModel() {
    fun loadData() {
        val data = repository.getDataSync()  // Blocking call on main thread!
        _state.value = data
    }
}

// GOOD - Use coroutines with appropriate dispatcher
class GoodViewModel : ViewModel() {
    fun loadData() {
        viewModelScope.launch {
            val data = withContext(Dispatchers.IO) {
                repository.getDataSync()
            }
            _state.value = data
        }
    }
}
```

### 錯誤五：公開 MutableStateFlow

```kotlin
// BAD - View can modify state directly
class UnsafeViewModel : ViewModel() {
    val uiState = MutableStateFlow(UiState())  // Anyone can modify!
}

// GOOD - Expose immutable StateFlow
class SafeViewModel : ViewModel() {
    private val _uiState = MutableStateFlow(UiState())
    val uiState: StateFlow<UiState> = _uiState.asStateFlow()
}
```

---

## 效能最佳化

### 1. 使用 distinctUntilChanged

```kotlin
// Prevent unnecessary recompositions
val userName: StateFlow<String> = user
    .map { it.name }
    .distinctUntilChanged()  // Only emit when name actually changes
    .stateIn(viewModelScope, SharingStarted.WhileSubscribed(5000), "")
```

### 2. 防抖使用者輸入

```kotlin
private val searchQuery = MutableStateFlow("")

val searchResults = searchQuery
    .debounce(300)  // Wait 300ms after last character
    .filter { it.length >= 2 }
    .distinctUntilChanged()
    .flatMapLatest { query ->
        repository.search(query)
    }
```

### 3. 使用含超時的 WhileSubscribed

```kotlin
// Stop upstream work 5 seconds after last collector
val data = repository.observeData()
    .stateIn(
        scope = viewModelScope,
        started = SharingStarted.WhileSubscribed(5000),
        initialValue = emptyList()
    )
```

### 4. 避免在狀態映射中進行昂貴操作

```kotlin
// BAD - Expensive computation on every state update
val filteredUsers = _users.map { users ->
    users.filter { expensiveFilter(it) }
        .sortedBy { expensiveSort(it) }
}

// GOOD - Cache expensive computations
class OptimizedViewModel : ViewModel() {
    private val _users = MutableStateFlow<List<User>>(emptyList())
    private val _filterCriteria = MutableStateFlow(FilterCriteria())

    val filteredUsers = combine(_users, _filterCriteria) { users, criteria ->
        users.filter { criteria.matches(it) }
    }.stateIn(viewModelScope, SharingStarted.WhileSubscribed(5000), emptyList())
}
```

### 5. 使用穩定的列表鍵

```kotlin
// Compose - Use stable keys for efficient diffing
LazyColumn {
    items(
        items = users,
        key = { user -> user.id }  // Stable key
    ) { user ->
        UserItem(user)
    }
}

// RecyclerView - Implement proper DiffCallback
class UserDiffCallback : DiffUtil.ItemCallback<User>() {
    override fun areItemsTheSame(oldItem: User, newItem: User): Boolean {
        return oldItem.id == newItem.id
    }

    override fun areContentsTheSame(oldItem: User, newItem: User): Boolean {
        return oldItem == newItem
    }
}
```
