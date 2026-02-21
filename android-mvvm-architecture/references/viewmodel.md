# ViewModel 完整指南

## 目錄
1. [ViewModel 概覽](#viewmodel-overview)
2. [ViewModel 生命週期](#viewmodel-lifecycle)
3. [建立 ViewModel](#creating-viewmodels)
4. [SavedStateHandle](#savedstatehandle)
5. [ViewModel 範圍](#viewmodel-scoping)
6. [ViewModelScope](#viewmodelscope)
7. [測試 ViewModel](#testing-viewmodels)

---

## ViewModel 概覽

`ViewModel` 類別設計用於以感知生命週期的方式儲存和管理 UI 相關資料。它允許資料在設定變更（如螢幕旋轉）時存活。

### 關鍵特性

1. **在設定變更時存活**：ViewModel 在 Activity 重建時保留
2. **生命週期感知**：當擁有者（Activity/Fragment）結束時自動清除
3. **不持有 UI 參考**：絕不應參考 View、Activity 或 Context
4. **單一職責**：管理 UI 狀態並處理業務邏輯

### 基本 ViewModel

```kotlin
class SimpleViewModel : ViewModel() {

    private val _counter = MutableStateFlow(0)
    val counter: StateFlow<Int> = _counter.asStateFlow()

    fun increment() {
        _counter.value++
    }

    fun decrement() {
        _counter.value--
    }

    // Called when ViewModel is cleared
    override fun onCleared() {
        super.onCleared()
        // Clean up resources (cancel subscriptions, close connections, etc.)
    }
}
```

---

## ViewModel 生命週期

### 生命週期圖

```
Activity/Fragment Created
         │
         ▼
   ┌─────────────────────┐
   │  ViewModel Created  │◄────── First time only
   │  (or retrieved)     │
   └─────────────────────┘
         │
         ▼
   Configuration Change (Rotation)
         │
         ▼
   ┌─────────────────────┐
   │  Activity Recreated │
   │  ViewModel RETAINED │◄────── Same ViewModel instance
   └─────────────────────┘
         │
         ▼
   Activity Finished (Back pressed, finish())
         │
         ▼
   ┌─────────────────────┐
   │  onCleared() called │
   │  ViewModel destroyed│
   └─────────────────────┘
```

### 生命週期感知範例

```kotlin
@HiltViewModel
class UserProfileViewModel @Inject constructor(
    private val userRepository: UserRepository
) : ViewModel() {

    private val _userState = MutableStateFlow<UserState>(UserState.Loading)
    val userState: StateFlow<UserState> = _userState.asStateFlow()

    // This is called once, even after rotation
    init {
        Log.d("ViewModel", "ViewModel created - loading user")
        loadUser()
    }

    private fun loadUser() {
        viewModelScope.launch {
            _userState.value = UserState.Loading
            try {
                val user = userRepository.getCurrentUser()
                _userState.value = UserState.Success(user)
            } catch (e: Exception) {
                _userState.value = UserState.Error(e.message ?: "Unknown error")
            }
        }
    }

    override fun onCleared() {
        super.onCleared()
        Log.d("ViewModel", "ViewModel cleared")
        // viewModelScope is automatically cancelled here
    }
}

sealed class UserState {
    object Loading : UserState()
    data class Success(val user: User) : UserState()
    data class Error(val message: String) : UserState()
}
```

---

## 建立 ViewModel

### 方法一：使用 `by viewModels()` 委派（推薦）

```kotlin
// In Activity
class MainActivity : AppCompatActivity() {
    private val viewModel: MainViewModel by viewModels()
}

// In Fragment
class UserFragment : Fragment() {
    private val viewModel: UserViewModel by viewModels()
}
```

### 方法二：使用自訂 Factory

```kotlin
// Custom Factory
class UserViewModelFactory(
    private val userId: Long,
    private val repository: UserRepository
) : ViewModelProvider.Factory {
    override fun <T : ViewModel> create(modelClass: Class<T>): T {
        if (modelClass.isAssignableFrom(UserViewModel::class.java)) {
            @Suppress("UNCHECKED_CAST")
            return UserViewModel(userId, repository) as T
        }
        throw IllegalArgumentException("Unknown ViewModel class")
    }
}

// Usage in Fragment
class UserFragment : Fragment() {
    private val viewModel: UserViewModel by viewModels {
        UserViewModelFactory(
            userId = requireArguments().getLong("userId"),
            repository = (requireActivity().application as MyApp).userRepository
        )
    }
}
```

### 方法三：使用 Hilt（推薦用於依賴注入）

```kotlin
// ViewModel with Hilt
@HiltViewModel
class UserViewModel @Inject constructor(
    private val userRepository: UserRepository,
    private val analyticsService: AnalyticsService,
    savedStateHandle: SavedStateHandle
) : ViewModel() {

    // Get navigation argument
    private val userId: Long = savedStateHandle["userId"]
        ?: throw IllegalArgumentException("userId is required")

    // ... rest of ViewModel
}

// Activity with Hilt
@AndroidEntryPoint
class UserActivity : AppCompatActivity() {
    private val viewModel: UserViewModel by viewModels()
}

// Fragment with Hilt
@AndroidEntryPoint
class UserFragment : Fragment() {
    private val viewModel: UserViewModel by viewModels()
}
```

### 方法四：直接使用 ViewModelProvider

```kotlin
// In Activity
class MainActivity : AppCompatActivity() {
    private lateinit var viewModel: MainViewModel

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        viewModel = ViewModelProvider(this)[MainViewModel::class.java]
    }
}

// With Factory
class UserActivity : AppCompatActivity() {
    private lateinit var viewModel: UserViewModel

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        val factory = UserViewModelFactory(userId, repository)
        viewModel = ViewModelProvider(this, factory)[UserViewModel::class.java]
    }
}
```

---

## SavedStateHandle

`SavedStateHandle` 允許 ViewModel 存取並儲存在進程死亡後仍能存活的狀態。

### 基本用法

```kotlin
@HiltViewModel
class SearchViewModel @Inject constructor(
    private val savedStateHandle: SavedStateHandle,
    private val searchRepository: SearchRepository
) : ViewModel() {

    // Automatically saved and restored
    private val searchQuery = savedStateHandle.getStateFlow("query", "")

    // Read a value
    val initialQuery: String = savedStateHandle["query"] ?: ""

    // Write a value
    fun setSearchQuery(query: String) {
        savedStateHandle["query"] = query
    }

    // Use as StateFlow
    val queryFlow: StateFlow<String> = savedStateHandle.getStateFlow("query", "")

    // Use as LiveData
    val queryLiveData: LiveData<String> = savedStateHandle.getLiveData("query", "")
}
```

### SavedStateHandle 與導航引數

```kotlin
@HiltViewModel
class ProductDetailViewModel @Inject constructor(
    savedStateHandle: SavedStateHandle,
    private val productRepository: ProductRepository
) : ViewModel() {

    // Navigation arguments are automatically available
    private val productId: String = savedStateHandle["productId"]
        ?: throw IllegalArgumentException("productId is required")

    private val _product = MutableStateFlow<Product?>(null)
    val product: StateFlow<Product?> = _product.asStateFlow()

    init {
        loadProduct()
    }

    private fun loadProduct() {
        viewModelScope.launch {
            _product.value = productRepository.getProduct(productId)
        }
    }
}

// Navigation graph
// <argument android:name="productId" app:argType="string" />
```

### SavedStateHandle 鍵管理

```kotlin
@HiltViewModel
class FormViewModel @Inject constructor(
    private val savedStateHandle: SavedStateHandle
) : ViewModel() {

    companion object {
        private const val KEY_NAME = "name"
        private const val KEY_EMAIL = "email"
        private const val KEY_STEP = "current_step"
    }

    val name: StateFlow<String> = savedStateHandle.getStateFlow(KEY_NAME, "")
    val email: StateFlow<String> = savedStateHandle.getStateFlow(KEY_EMAIL, "")
    val currentStep: StateFlow<Int> = savedStateHandle.getStateFlow(KEY_STEP, 0)

    fun updateName(name: String) {
        savedStateHandle[KEY_NAME] = name
    }

    fun updateEmail(email: String) {
        savedStateHandle[KEY_EMAIL] = email
    }

    fun goToNextStep() {
        savedStateHandle[KEY_STEP] = (savedStateHandle[KEY_STEP] ?: 0) + 1
    }
}
```

### 應儲存什麼

| 應儲存 | 不應儲存 |
|-------------|-----------------|
| 使用者輸入（文字、選擇） | 大型物件 |
| 導航引數 | Bitmap/圖片 |
| UI 狀態（捲動位置、頁籤索引） | 來自 repository 的整個列表 |
| 表單狀態 | 快取資料 |
| 多步驟流程中的當前步驟 | 可重新取得的資料 |

```kotlin
@HiltViewModel
class CheckoutViewModel @Inject constructor(
    private val savedStateHandle: SavedStateHandle,
    private val orderRepository: OrderRepository
) : ViewModel() {

    // GOOD: Save lightweight UI state
    val currentStep: StateFlow<Int> = savedStateHandle.getStateFlow("step", 0)
    val selectedPaymentMethod: StateFlow<String?> = savedStateHandle.getStateFlow("payment", null)
    val shippingAddressId: StateFlow<Long?> = savedStateHandle.getStateFlow("address_id", null)

    // DON'T save: Re-fetch from repository
    private val _cartItems = MutableStateFlow<List<CartItem>>(emptyList())
    val cartItems: StateFlow<List<CartItem>> = _cartItems.asStateFlow()

    init {
        loadCartItems() // Re-fetch on process restart
    }
}
```

---

## ViewModel 範圍

### Activity 範圍

```kotlin
// ViewModel scoped to Activity lifecycle
class MainActivity : AppCompatActivity() {
    private val viewModel: MainViewModel by viewModels()
    // ViewModel is retained until Activity is finished
}
```

### Fragment 範圍

```kotlin
// ViewModel scoped to Fragment lifecycle
class UserFragment : Fragment() {
    private val viewModel: UserViewModel by viewModels()
    // ViewModel is retained until Fragment is destroyed
}
```

### 在 Fragment 中使用 Activity 範圍的 ViewModel

```kotlin
// Share ViewModel between Activity and Fragments
class SharedViewModel : ViewModel() {
    val sharedData = MutableStateFlow<String>("")
}

// In Activity
class MainActivity : AppCompatActivity() {
    private val sharedViewModel: SharedViewModel by viewModels()
}

// In Fragment - same instance as Activity
class ChildFragment : Fragment() {
    private val sharedViewModel: SharedViewModel by activityViewModels()
}
```

### 導航圖範圍的 ViewModel

```kotlin
// ViewModel scoped to navigation graph
class CheckoutFragment : Fragment() {

    // Scoped to the checkout navigation graph
    private val checkoutViewModel: CheckoutViewModel by navGraphViewModels(R.id.checkout_graph)

    // This ViewModel is shared with all fragments in the checkout_graph
}

// Navigation graph
// <navigation android:id="@+id/checkout_graph" ...>
//     <fragment android:id="@+id/cartFragment" ... />
//     <fragment android:id="@+id/shippingFragment" ... />
//     <fragment android:id="@+id/paymentFragment" ... />
// </navigation>
```

### 父 Fragment 範圍的 ViewModel

```kotlin
// ViewModel scoped to parent fragment
class ChildFragment : Fragment() {

    // Shared with parent and sibling fragments
    private val parentViewModel: ParentViewModel by viewModels(
        ownerProducer = { requireParentFragment() }
    )
}
```

### 範圍選擇指南

```
問題：誰需要共享這個 ViewModel？

只有這個 Fragment？
    └── 使用 by viewModels()

同一 Activity 中的多個 Fragment？
    └── 使用 by activityViewModels()

導航圖中的多個 Fragment？
    └── 使用 by navGraphViewModels(R.id.nav_graph)

父 Fragment 及其子 Fragment？
    └── 使用 by viewModels({ requireParentFragment() })

跨進程死亡？
    └── 使用 SavedStateHandle
```

---

## ViewModelScope

`viewModelScope` 是與 ViewModel 生命週期綁定的協程範圍。當 ViewModel 被清除時，它會自動取消。

### 基本用法

```kotlin
@HiltViewModel
class DataViewModel @Inject constructor(
    private val repository: DataRepository
) : ViewModel() {

    private val _data = MutableStateFlow<List<Item>>(emptyList())
    val data: StateFlow<List<Item>> = _data.asStateFlow()

    fun loadData() {
        viewModelScope.launch {
            // Automatically cancelled if ViewModel is cleared
            val result = repository.fetchData()
            _data.value = result
        }
    }

    fun loadDataWithErrorHandling() {
        viewModelScope.launch {
            try {
                val result = repository.fetchData()
                _data.value = result
            } catch (e: CancellationException) {
                // Re-throw cancellation exceptions
                throw e
            } catch (e: Exception) {
                // Handle other exceptions
                _error.value = e.message
            }
        }
    }
}
```

### 多個並行操作

```kotlin
@HiltViewModel
class DashboardViewModel @Inject constructor(
    private val userRepository: UserRepository,
    private val statsRepository: StatsRepository,
    private val newsRepository: NewsRepository
) : ViewModel() {

    private val _dashboardState = MutableStateFlow(DashboardState())
    val dashboardState: StateFlow<DashboardState> = _dashboardState.asStateFlow()

    init {
        loadDashboard()
    }

    private fun loadDashboard() {
        viewModelScope.launch {
            // Load data in parallel
            val userDeferred = async { userRepository.getCurrentUser() }
            val statsDeferred = async { statsRepository.getStats() }
            val newsDeferred = async { newsRepository.getLatestNews() }

            try {
                val user = userDeferred.await()
                val stats = statsDeferred.await()
                val news = newsDeferred.await()

                _dashboardState.value = DashboardState(
                    user = user,
                    stats = stats,
                    news = news,
                    isLoading = false
                )
            } catch (e: Exception) {
                _dashboardState.value = DashboardState(
                    error = e.message,
                    isLoading = false
                )
            }
        }
    }
}
```

### 取消處理

```kotlin
@HiltViewModel
class SearchViewModel @Inject constructor(
    private val searchRepository: SearchRepository
) : ViewModel() {

    private var searchJob: Job? = null

    fun search(query: String) {
        // Cancel previous search
        searchJob?.cancel()

        searchJob = viewModelScope.launch {
            // Debounce
            delay(300)

            _searchState.update { it.copy(isSearching = true) }

            try {
                val results = searchRepository.search(query)
                _searchState.update {
                    it.copy(isSearching = false, results = results)
                }
            } catch (e: CancellationException) {
                // Search was cancelled, don't update state
                throw e
            } catch (e: Exception) {
                _searchState.update {
                    it.copy(isSearching = false, error = e.message)
                }
            }
        }
    }
}
```

### 自訂 Dispatcher

```kotlin
@HiltViewModel
class FileProcessingViewModel @Inject constructor(
    private val fileProcessor: FileProcessor,
    @IoDispatcher private val ioDispatcher: CoroutineDispatcher
) : ViewModel() {

    fun processFile(uri: Uri) {
        viewModelScope.launch {
            _state.update { it.copy(isProcessing = true) }

            // Heavy I/O work on IO dispatcher
            val result = withContext(ioDispatcher) {
                fileProcessor.process(uri)
            }

            _state.update { it.copy(isProcessing = false, result = result) }
        }
    }
}
```

---

## 測試 ViewModel

### 基本單元測試

```kotlin
@OptIn(ExperimentalCoroutinesApi::class)
class UserViewModelTest {

    @get:Rule
    val mainDispatcherRule = MainDispatcherRule()

    private lateinit var viewModel: UserViewModel
    private lateinit var fakeRepository: FakeUserRepository

    @Before
    fun setup() {
        fakeRepository = FakeUserRepository()
        viewModel = UserViewModel(fakeRepository)
    }

    @Test
    fun `initial state is loading`() = runTest {
        val state = viewModel.uiState.value
        assertTrue(state.isLoading)
    }

    @Test
    fun `loadUsers updates state with users`() = runTest {
        // Given
        val expectedUsers = listOf(
            User(1, "John", "john@example.com"),
            User(2, "Jane", "jane@example.com")
        )
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
    fun `loadUsers handles error`() = runTest {
        // Given
        fakeRepository.setShouldError(true)

        // When
        viewModel.loadUsers()
        advanceUntilIdle()

        // Then
        val state = viewModel.uiState.value
        assertFalse(state.isLoading)
        assertNotNull(state.error)
    }
}
```

### MainDispatcherRule

```kotlin
@OptIn(ExperimentalCoroutinesApi::class)
class MainDispatcherRule(
    private val testDispatcher: TestDispatcher = UnconfinedTestDispatcher()
) : TestWatcher() {

    override fun starting(description: Description) {
        Dispatchers.setMain(testDispatcher)
    }

    override fun finished(description: Description) {
        Dispatchers.resetMain()
    }
}
```

### 測試含 SavedStateHandle 的 ViewModel

```kotlin
@OptIn(ExperimentalCoroutinesApi::class)
class ProductViewModelTest {

    @get:Rule
    val mainDispatcherRule = MainDispatcherRule()

    @Test
    fun `viewModel loads product from savedStateHandle argument`() = runTest {
        // Given
        val productId = "product123"
        val savedStateHandle = SavedStateHandle(mapOf("productId" to productId))
        val fakeRepository = FakeProductRepository()
        val expectedProduct = Product(productId, "Test Product", 99.99)
        fakeRepository.setProduct(expectedProduct)

        // When
        val viewModel = ProductViewModel(savedStateHandle, fakeRepository)
        advanceUntilIdle()

        // Then
        assertEquals(expectedProduct, viewModel.product.value)
    }
}
```

### 測試隨時間變化的狀態

```kotlin
@OptIn(ExperimentalCoroutinesApi::class)
class SearchViewModelTest {

    @get:Rule
    val mainDispatcherRule = MainDispatcherRule()

    @Test
    fun `search emits loading then results`() = runTest {
        // Given
        val fakeRepository = FakeSearchRepository()
        val viewModel = SearchViewModel(fakeRepository)
        val states = mutableListOf<SearchUiState>()

        // Collect states
        val job = launch(UnconfinedTestDispatcher()) {
            viewModel.uiState.toList(states)
        }

        // When
        viewModel.search("test")
        advanceUntilIdle()

        // Then
        assertEquals(3, states.size)
        assertTrue(states[0].isLoading) // Initial loading
        assertTrue(states[1].isSearching) // Searching
        assertFalse(states[2].isSearching) // Results

        job.cancel()
    }
}
```

### 用於測試的假 Repository

```kotlin
class FakeUserRepository : UserRepository {

    private var users = listOf<User>()
    private var shouldError = false
    private var errorMessage = "Test error"

    fun setUsers(users: List<User>) {
        this.users = users
    }

    fun setShouldError(shouldError: Boolean, message: String = "Test error") {
        this.shouldError = shouldError
        this.errorMessage = message
    }

    override suspend fun getUsers(): List<User> {
        if (shouldError) throw Exception(errorMessage)
        return users
    }

    override fun observeUsers(): Flow<List<User>> {
        if (shouldError) return flow { throw Exception(errorMessage) }
        return flowOf(users)
    }

    override suspend fun getUser(id: Long): User? {
        if (shouldError) throw Exception(errorMessage)
        return users.find { it.id == id }
    }
}
```

---

## ViewModel 速查表

### 建立方式快速參考

| 場景 | 程式碼 |
|----------|------|
| Activity 中的基本用法 | `by viewModels()` |
| Fragment 中的基本用法 | `by viewModels()` |
| 使用 Hilt | `@HiltViewModel` + `by viewModels()` |
| 共享（Activity 範圍） | `by activityViewModels()` |
| 導航圖範圍 | `by navGraphViewModels(R.id.graph)` |
| 自訂 Factory | `by viewModels { MyFactory() }` |

### 狀態管理快速參考

| 任務 | 程式碼 |
|------|------|
| 可變私有狀態 | `private val _state = MutableStateFlow(State())` |
| 公開不可變狀態 | `val state: StateFlow<State> = _state.asStateFlow()` |
| 更新狀態 | `_state.update { it.copy(loading = true) }` |
| 一次性事件 | `MutableSharedFlow<Event>()` |
| 已儲存狀態 | `savedStateHandle.getStateFlow("key", default)` |

### 生命週期快速參考

| 事件 | ViewModel 行為 |
|-------|-------------------|
| Activity/Fragment 建立 | ViewModel 建立（首次）或取回 |
| 設定變更 | ViewModel 保留 |
| Activity 結束 / Fragment 移除 | 呼叫 `onCleared()`，ViewModel 銷毀 |
| viewModelScope 協程 | 在 `onCleared()` 中自動取消 |
