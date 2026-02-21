# MVVM 進階主題

## 目錄
1. [多模組架構](#multi-module-architecture)
2. [複雜狀態管理](#complex-state-management)
3. [副作用與事件](#side-effects-and-events)
4. [分頁載入](#pagination)
5. [表單驗證](#form-validation)
6. [含防抖的搜尋](#search-with-debounce)
7. [多選狀態](#multi-selection-state)
8. [復原/重做操作](#undoredo-operations)

---

## 多模組架構

### 模組結構

```
project/
├── app/                        # Application module
│   └── src/main/java/
│       └── com.example.app/
│           ├── App.kt
│           └── MainActivity.kt
│
├── feature/                    # Feature modules
│   ├── user-list/
│   │   └── src/main/java/
│   │       └── com.example.feature.userlist/
│   │           ├── UserListFragment.kt
│   │           ├── UserListViewModel.kt
│   │           └── di/
│   │               └── UserListModule.kt
│   │
│   └── user-detail/
│       └── src/main/java/
│           └── com.example.feature.userdetail/
│               ├── UserDetailFragment.kt
│               └── UserDetailViewModel.kt
│
├── core/                       # Shared modules
│   ├── domain/                 # Business logic (no Android deps)
│   │   └── src/main/java/
│   │       └── com.example.core.domain/
│   │           ├── model/
│   │           │   └── User.kt
│   │           ├── repository/
│   │           │   └── UserRepository.kt
│   │           └── usecase/
│   │               └── GetUserUseCase.kt
│   │
│   ├── data/                   # Repository implementations
│   │   └── src/main/java/
│   │       └── com.example.core.data/
│   │           ├── repository/
│   │           │   └── UserRepositoryImpl.kt
│   │           ├── local/
│   │           │   └── UserDao.kt
│   │           └── remote/
│   │               └── UserApiService.kt
│   │
│   └── ui/                     # Shared UI components
│       └── src/main/java/
│           └── com.example.core.ui/
│               ├── BaseViewModel.kt
│               └── components/
```

### 模組依賴（build.gradle.kts）

```kotlin
// :app
dependencies {
    implementation(project(":feature:user-list"))
    implementation(project(":feature:user-detail"))
    implementation(project(":core:data"))
}

// :feature:user-list
dependencies {
    implementation(project(":core:domain"))
    implementation(project(":core:ui"))
}

// :core:data
dependencies {
    implementation(project(":core:domain"))
}

// :core:domain
// Pure Kotlin - no dependencies on other modules
dependencies {
    implementation(libs.kotlinx.coroutines.core)
}
```

### 多模組的基礎 ViewModel

```kotlin
// :core:ui
abstract class BaseViewModel : ViewModel() {

    protected val _isLoading = MutableStateFlow(false)
    val isLoading: StateFlow<Boolean> = _isLoading.asStateFlow()

    protected val _error = MutableSharedFlow<String>()
    val error: SharedFlow<String> = _error.asSharedFlow()

    protected fun launchWithLoading(block: suspend () -> Unit) {
        viewModelScope.launch {
            _isLoading.value = true
            try {
                block()
            } catch (e: Exception) {
                _error.emit(e.message ?: "Unknown error")
            } finally {
                _isLoading.value = false
            }
        }
    }
}

// :feature:user-list
class UserListViewModel @Inject constructor(
    private val getUsersUseCase: GetUsersUseCase
) : BaseViewModel() {

    private val _users = MutableStateFlow<List<User>>(emptyList())
    val users: StateFlow<List<User>> = _users.asStateFlow()

    fun loadUsers() = launchWithLoading {
        _users.value = getUsersUseCase()
    }
}
```

### 功能模組之間的導航

```kotlin
// :core:navigation - Navigation contracts
interface NavigationDestination {
    val route: String
}

sealed class UserDestinations : NavigationDestination {
    data class UserDetail(val userId: Long) : UserDestinations() {
        override val route = "user/$userId"
    }
}

// Navigation events
interface NavigationEvent {
    val destination: NavigationDestination
}

// :feature:user-list
class UserListViewModel : ViewModel() {
    private val _navigationEvents = MutableSharedFlow<NavigationEvent>()
    val navigationEvents: SharedFlow<NavigationEvent> = _navigationEvents.asSharedFlow()

    fun onUserClicked(userId: Long) {
        viewModelScope.launch {
            _navigationEvents.emit(object : NavigationEvent {
                override val destination = UserDestinations.UserDetail(userId)
            })
        }
    }
}

// :app - Handle navigation
navController.navigate(event.destination.route)
```

---

## 複雜狀態管理

### 階層式狀態

```kotlin
// Screen-level state
data class CheckoutUiState(
    val cartState: CartState = CartState(),
    val shippingState: ShippingState = ShippingState(),
    val paymentState: PaymentState = PaymentState(),
    val currentStep: CheckoutStep = CheckoutStep.CART,
    val isProcessing: Boolean = false
)

// Section states
data class CartState(
    val items: List<CartItem> = emptyList(),
    val subtotal: BigDecimal = BigDecimal.ZERO,
    val isLoading: Boolean = false
)

data class ShippingState(
    val addresses: List<Address> = emptyList(),
    val selectedAddressId: Long? = null,
    val shippingOptions: List<ShippingOption> = emptyList(),
    val selectedOptionId: String? = null
)

data class PaymentState(
    val paymentMethods: List<PaymentMethod> = emptyList(),
    val selectedMethodId: String? = null,
    val cardNumber: String = "",
    val expiryDate: String = "",
    val cvv: String = ""
)

enum class CheckoutStep { CART, SHIPPING, PAYMENT, CONFIRMATION }

// ViewModel with section updates
@HiltViewModel
class CheckoutViewModel @Inject constructor(
    private val cartRepository: CartRepository,
    private val shippingRepository: ShippingRepository
) : ViewModel() {

    private val _uiState = MutableStateFlow(CheckoutUiState())
    val uiState: StateFlow<CheckoutUiState> = _uiState.asStateFlow()

    // Update nested state
    fun selectAddress(addressId: Long) {
        _uiState.update { state ->
            state.copy(
                shippingState = state.shippingState.copy(
                    selectedAddressId = addressId
                )
            )
        }
    }

    // Update with validation
    fun updateCardNumber(number: String) {
        _uiState.update { state ->
            state.copy(
                paymentState = state.paymentState.copy(
                    cardNumber = number.filter { it.isDigit() }.take(16)
                )
            )
        }
    }
}
```

### 狀態機模式

```kotlin
// Sealed class for states
sealed class OrderState {
    object Idle : OrderState()
    object Loading : OrderState()
    data class Cart(val items: List<CartItem>) : OrderState()
    data class Shipping(val items: List<CartItem>, val address: Address?) : OrderState()
    data class Payment(val items: List<CartItem>, val address: Address) : OrderState()
    data class Processing(val order: PendingOrder) : OrderState()
    data class Success(val order: Order) : OrderState()
    data class Error(val message: String, val previousState: OrderState) : OrderState()
}

// Sealed class for events
sealed class OrderEvent {
    object LoadCart : OrderEvent()
    data class AddToCart(val product: Product) : OrderEvent()
    data class RemoveFromCart(val itemId: Long) : OrderEvent()
    object ProceedToShipping : OrderEvent()
    data class SelectAddress(val address: Address) : OrderEvent()
    object ProceedToPayment : OrderEvent()
    data class SubmitPayment(val paymentInfo: PaymentInfo) : OrderEvent()
    object RetryLastAction : OrderEvent()
}

@HiltViewModel
class OrderViewModel @Inject constructor(
    private val orderRepository: OrderRepository
) : ViewModel() {

    private val _state = MutableStateFlow<OrderState>(OrderState.Idle)
    val state: StateFlow<OrderState> = _state.asStateFlow()

    fun onEvent(event: OrderEvent) {
        val currentState = _state.value

        viewModelScope.launch {
            _state.value = when (event) {
                is OrderEvent.LoadCart -> handleLoadCart()
                is OrderEvent.ProceedToShipping -> handleProceedToShipping(currentState)
                is OrderEvent.SelectAddress -> handleSelectAddress(currentState, event.address)
                is OrderEvent.SubmitPayment -> handleSubmitPayment(currentState, event.paymentInfo)
                is OrderEvent.RetryLastAction -> handleRetry(currentState)
                else -> currentState
            }
        }
    }

    private suspend fun handleLoadCart(): OrderState {
        return try {
            _state.value = OrderState.Loading
            val items = orderRepository.getCartItems()
            OrderState.Cart(items)
        } catch (e: Exception) {
            OrderState.Error(e.message ?: "Failed to load cart", OrderState.Idle)
        }
    }

    private fun handleProceedToShipping(current: OrderState): OrderState {
        return when (current) {
            is OrderState.Cart -> OrderState.Shipping(current.items, null)
            else -> current
        }
    }
}
```

---

## 副作用與事件

### 基於 Channel 的事件（一次性事件）

```kotlin
@HiltViewModel
class UserViewModel @Inject constructor(
    private val userRepository: UserRepository
) : ViewModel() {

    // Use Channel for one-time events
    private val _events = Channel<UserEvent>(Channel.BUFFERED)
    val events: Flow<UserEvent> = _events.receiveAsFlow()

    fun deleteUser(userId: Long) {
        viewModelScope.launch {
            try {
                userRepository.deleteUser(userId)
                _events.send(UserEvent.ShowSnackbar("User deleted"))
            } catch (e: Exception) {
                _events.send(UserEvent.ShowSnackbar("Failed to delete user"))
            }
        }
    }
}

sealed class UserEvent {
    data class ShowSnackbar(val message: String) : UserEvent()
    data class Navigate(val destination: String) : UserEvent()
    object CloseScreen : UserEvent()
}

// Collecting in Fragment
viewLifecycleOwner.lifecycleScope.launch {
    viewLifecycleOwner.repeatOnLifecycle(Lifecycle.State.STARTED) {
        viewModel.events.collect { event ->
            when (event) {
                is UserEvent.ShowSnackbar -> {
                    Snackbar.make(binding.root, event.message, Snackbar.LENGTH_SHORT).show()
                }
                is UserEvent.Navigate -> {
                    findNavController().navigate(event.destination)
                }
                UserEvent.CloseScreen -> {
                    findNavController().popBackStack()
                }
            }
        }
    }
}
```

### 效果處理器模式

```kotlin
// Side effects that ViewModel can trigger
sealed class SideEffect {
    data class ShowToast(val message: String) : SideEffect()
    data class ShowDialog(val title: String, val message: String) : SideEffect()
    data class LaunchUrl(val url: String) : SideEffect()
    data class Share(val text: String) : SideEffect()
    data class RequestPermission(val permission: String) : SideEffect()
}

// Effect handler in Fragment
interface EffectHandler {
    fun handle(effect: SideEffect)
}

class UserFragment : Fragment(), EffectHandler {

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)

        viewLifecycleOwner.lifecycleScope.launch {
            viewLifecycleOwner.repeatOnLifecycle(Lifecycle.State.STARTED) {
                viewModel.sideEffects.collect { effect ->
                    handle(effect)
                }
            }
        }
    }

    override fun handle(effect: SideEffect) {
        when (effect) {
            is SideEffect.ShowToast -> {
                Toast.makeText(requireContext(), effect.message, Toast.LENGTH_SHORT).show()
            }
            is SideEffect.ShowDialog -> {
                MaterialAlertDialogBuilder(requireContext())
                    .setTitle(effect.title)
                    .setMessage(effect.message)
                    .setPositiveButton("OK", null)
                    .show()
            }
            is SideEffect.LaunchUrl -> {
                val intent = Intent(Intent.ACTION_VIEW, Uri.parse(effect.url))
                startActivity(intent)
            }
            is SideEffect.Share -> {
                val intent = Intent(Intent.ACTION_SEND).apply {
                    type = "text/plain"
                    putExtra(Intent.EXTRA_TEXT, effect.text)
                }
                startActivity(Intent.createChooser(intent, "Share"))
            }
            is SideEffect.RequestPermission -> {
                permissionLauncher.launch(effect.permission)
            }
        }
    }
}
```

---

## 分頁載入

### Paging 3 與 MVVM

```kotlin
// Repository with Paging
class UserRepositoryImpl @Inject constructor(
    private val userApi: UserApiService,
    private val userDao: UserDao
) : UserRepository {

    override fun getPagedUsers(): Flow<PagingData<User>> {
        return Pager(
            config = PagingConfig(
                pageSize = 20,
                enablePlaceholders = false,
                prefetchDistance = 5
            ),
            pagingSourceFactory = { UserPagingSource(userApi) }
        ).flow
    }
}

// PagingSource
class UserPagingSource(
    private val userApi: UserApiService
) : PagingSource<Int, User>() {

    override suspend fun load(params: LoadParams<Int>): LoadResult<Int, User> {
        val page = params.key ?: 1

        return try {
            val response = userApi.getUsers(page, params.loadSize)
            LoadResult.Page(
                data = response.users.map { it.toDomain() },
                prevKey = if (page == 1) null else page - 1,
                nextKey = if (response.users.isEmpty()) null else page + 1
            )
        } catch (e: Exception) {
            LoadResult.Error(e)
        }
    }

    override fun getRefreshKey(state: PagingState<Int, User>): Int? {
        return state.anchorPosition?.let { anchorPosition ->
            state.closestPageToPosition(anchorPosition)?.prevKey?.plus(1)
                ?: state.closestPageToPosition(anchorPosition)?.nextKey?.minus(1)
        }
    }
}

// ViewModel
@HiltViewModel
class UserListViewModel @Inject constructor(
    private val userRepository: UserRepository
) : ViewModel() {

    val users: Flow<PagingData<User>> = userRepository.getPagedUsers()
        .cachedIn(viewModelScope)
}

// Fragment with PagingDataAdapter
class UserListFragment : Fragment() {

    private val viewModel: UserListViewModel by viewModels()
    private lateinit var adapter: UserPagingAdapter

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)

        adapter = UserPagingAdapter { user ->
            viewModel.onUserClicked(user)
        }

        binding.recyclerView.adapter = adapter.withLoadStateFooter(
            footer = LoadStateAdapter { adapter.retry() }
        )

        viewLifecycleOwner.lifecycleScope.launch {
            viewModel.users.collectLatest { pagingData ->
                adapter.submitData(pagingData)
            }
        }

        // Handle load states
        viewLifecycleOwner.lifecycleScope.launch {
            adapter.loadStateFlow.collectLatest { loadStates ->
                binding.progressBar.isVisible = loadStates.refresh is LoadState.Loading
                binding.errorView.isVisible = loadStates.refresh is LoadState.Error
            }
        }
    }
}
```

### 手動分頁（不使用 Paging 3）

```kotlin
@HiltViewModel
class UserListViewModel @Inject constructor(
    private val userRepository: UserRepository
) : ViewModel() {

    private val _uiState = MutableStateFlow(PaginatedUiState())
    val uiState: StateFlow<PaginatedUiState> = _uiState.asStateFlow()

    private var currentPage = 1
    private var hasMorePages = true

    init {
        loadFirstPage()
    }

    private fun loadFirstPage() {
        currentPage = 1
        hasMorePages = true
        loadUsers(isRefresh = true)
    }

    fun loadNextPage() {
        if (_uiState.value.isLoadingMore || !hasMorePages) return
        loadUsers(isRefresh = false)
    }

    fun refresh() {
        loadFirstPage()
    }

    private fun loadUsers(isRefresh: Boolean) {
        viewModelScope.launch {
            _uiState.update {
                if (isRefresh) it.copy(isRefreshing = true)
                else it.copy(isLoadingMore = true)
            }

            try {
                val response = userRepository.getUsers(currentPage, PAGE_SIZE)

                _uiState.update { state ->
                    val newUsers = if (isRefresh) {
                        response.users
                    } else {
                        state.users + response.users
                    }

                    state.copy(
                        users = newUsers,
                        isRefreshing = false,
                        isLoadingMore = false,
                        error = null
                    )
                }

                hasMorePages = response.users.size >= PAGE_SIZE
                if (hasMorePages) currentPage++

            } catch (e: Exception) {
                _uiState.update {
                    it.copy(
                        isRefreshing = false,
                        isLoadingMore = false,
                        error = e.message
                    )
                }
            }
        }
    }

    companion object {
        private const val PAGE_SIZE = 20
    }
}

data class PaginatedUiState(
    val users: List<User> = emptyList(),
    val isRefreshing: Boolean = false,
    val isLoadingMore: Boolean = false,
    val error: String? = null
)
```

---

## 表單驗證

### 即時驗證

```kotlin
@HiltViewModel
class RegistrationViewModel @Inject constructor(
    private val authRepository: AuthRepository
) : ViewModel() {

    private val _formState = MutableStateFlow(RegistrationFormState())
    val formState: StateFlow<RegistrationFormState> = _formState.asStateFlow()

    // Derived state for form validity
    val isFormValid: StateFlow<Boolean> = _formState
        .map { it.isValid }
        .stateIn(viewModelScope, SharingStarted.WhileSubscribed(5000), false)

    fun updateEmail(email: String) {
        _formState.update { state ->
            state.copy(
                email = email,
                emailError = validateEmail(email)
            )
        }
    }

    fun updatePassword(password: String) {
        _formState.update { state ->
            state.copy(
                password = password,
                passwordError = validatePassword(password),
                // Re-validate confirm password if already entered
                confirmPasswordError = if (state.confirmPassword.isNotEmpty()) {
                    validateConfirmPassword(state.confirmPassword, password)
                } else null
            )
        }
    }

    fun updateConfirmPassword(confirmPassword: String) {
        _formState.update { state ->
            state.copy(
                confirmPassword = confirmPassword,
                confirmPasswordError = validateConfirmPassword(confirmPassword, state.password)
            )
        }
    }

    private fun validateEmail(email: String): String? {
        return when {
            email.isBlank() -> "Email is required"
            !Patterns.EMAIL_ADDRESS.matcher(email).matches() -> "Invalid email format"
            else -> null
        }
    }

    private fun validatePassword(password: String): String? {
        return when {
            password.isBlank() -> "Password is required"
            password.length < 8 -> "Password must be at least 8 characters"
            !password.any { it.isUpperCase() } -> "Password must contain uppercase letter"
            !password.any { it.isDigit() } -> "Password must contain a digit"
            else -> null
        }
    }

    private fun validateConfirmPassword(confirmPassword: String, password: String): String? {
        return when {
            confirmPassword.isBlank() -> "Please confirm password"
            confirmPassword != password -> "Passwords don't match"
            else -> null
        }
    }

    fun submit() {
        // Validate all fields
        val currentState = _formState.value
        val emailError = validateEmail(currentState.email)
        val passwordError = validatePassword(currentState.password)
        val confirmError = validateConfirmPassword(currentState.confirmPassword, currentState.password)

        if (emailError != null || passwordError != null || confirmError != null) {
            _formState.update {
                it.copy(
                    emailError = emailError,
                    passwordError = passwordError,
                    confirmPasswordError = confirmError
                )
            }
            return
        }

        // Proceed with registration
        viewModelScope.launch {
            _formState.update { it.copy(isSubmitting = true) }
            try {
                authRepository.register(currentState.email, currentState.password)
                _events.emit(RegistrationEvent.Success)
            } catch (e: Exception) {
                _formState.update {
                    it.copy(isSubmitting = false, submitError = e.message)
                }
            }
        }
    }
}

data class RegistrationFormState(
    val email: String = "",
    val emailError: String? = null,
    val password: String = "",
    val passwordError: String? = null,
    val confirmPassword: String = "",
    val confirmPasswordError: String? = null,
    val isSubmitting: Boolean = false,
    val submitError: String? = null
) {
    val isValid: Boolean
        get() = email.isNotBlank() &&
                emailError == null &&
                password.isNotBlank() &&
                passwordError == null &&
                confirmPassword.isNotBlank() &&
                confirmPasswordError == null
}
```

---

## 含防抖的搜尋

```kotlin
@HiltViewModel
class SearchViewModel @Inject constructor(
    private val searchRepository: SearchRepository
) : ViewModel() {

    private val _searchQuery = MutableStateFlow("")
    val searchQuery: StateFlow<String> = _searchQuery.asStateFlow()

    private val _uiState = MutableStateFlow(SearchUiState())
    val uiState: StateFlow<SearchUiState> = _uiState.asStateFlow()

    init {
        observeSearchQuery()
    }

    private fun observeSearchQuery() {
        viewModelScope.launch {
            _searchQuery
                .debounce(300)  // Wait 300ms after last character
                .distinctUntilChanged()
                .collectLatest { query ->
                    if (query.length >= 2) {
                        search(query)
                    } else {
                        _uiState.update { it.copy(results = emptyList(), isEmpty = true) }
                    }
                }
        }
    }

    private suspend fun search(query: String) {
        _uiState.update { it.copy(isSearching = true, error = null) }

        try {
            val results = searchRepository.search(query)
            _uiState.update {
                it.copy(
                    isSearching = false,
                    results = results,
                    isEmpty = results.isEmpty()
                )
            }
        } catch (e: Exception) {
            _uiState.update {
                it.copy(isSearching = false, error = e.message)
            }
        }
    }

    fun onSearchQueryChanged(query: String) {
        _searchQuery.value = query
    }

    fun clearSearch() {
        _searchQuery.value = ""
        _uiState.value = SearchUiState()
    }
}

data class SearchUiState(
    val isSearching: Boolean = false,
    val results: List<SearchResult> = emptyList(),
    val isEmpty: Boolean = true,
    val error: String? = null
)
```

---

## 多選狀態

```kotlin
@HiltViewModel
class GalleryViewModel @Inject constructor(
    private val photoRepository: PhotoRepository
) : ViewModel() {

    private val _uiState = MutableStateFlow(GalleryUiState())
    val uiState: StateFlow<GalleryUiState> = _uiState.asStateFlow()

    init {
        loadPhotos()
    }

    fun toggleSelection(photoId: Long) {
        _uiState.update { state ->
            val newSelection = if (state.selectedIds.contains(photoId)) {
                state.selectedIds - photoId
            } else {
                state.selectedIds + photoId
            }
            state.copy(
                selectedIds = newSelection,
                isSelectionMode = newSelection.isNotEmpty()
            )
        }
    }

    fun selectAll() {
        _uiState.update { state ->
            state.copy(
                selectedIds = state.photos.map { it.id }.toSet(),
                isSelectionMode = true
            )
        }
    }

    fun clearSelection() {
        _uiState.update { state ->
            state.copy(
                selectedIds = emptySet(),
                isSelectionMode = false
            )
        }
    }

    fun deleteSelected() {
        viewModelScope.launch {
            val selectedIds = _uiState.value.selectedIds
            try {
                photoRepository.deletePhotos(selectedIds.toList())
                _uiState.update { state ->
                    state.copy(
                        photos = state.photos.filterNot { it.id in selectedIds },
                        selectedIds = emptySet(),
                        isSelectionMode = false
                    )
                }
                _events.emit(GalleryEvent.ShowMessage("${selectedIds.size} photos deleted"))
            } catch (e: Exception) {
                _events.emit(GalleryEvent.ShowMessage("Failed to delete photos"))
            }
        }
    }
}

data class GalleryUiState(
    val photos: List<Photo> = emptyList(),
    val selectedIds: Set<Long> = emptySet(),
    val isSelectionMode: Boolean = false,
    val isLoading: Boolean = false
) {
    val selectedCount: Int get() = selectedIds.size
    val hasSelection: Boolean get() = selectedIds.isNotEmpty()
}
```

---

## 復原/重做操作

```kotlin
@HiltViewModel
class EditorViewModel @Inject constructor() : ViewModel() {

    private val undoStack = mutableListOf<EditorState>()
    private val redoStack = mutableListOf<EditorState>()

    private val _uiState = MutableStateFlow(EditorUiState())
    val uiState: StateFlow<EditorUiState> = _uiState.asStateFlow()

    val canUndo: StateFlow<Boolean> = _uiState
        .map { undoStack.isNotEmpty() }
        .stateIn(viewModelScope, SharingStarted.WhileSubscribed(5000), false)

    val canRedo: StateFlow<Boolean> = _uiState
        .map { redoStack.isNotEmpty() }
        .stateIn(viewModelScope, SharingStarted.WhileSubscribed(5000), false)

    fun updateText(text: String) {
        saveStateForUndo()
        _uiState.update { it.copy(text = text) }
        redoStack.clear()
    }

    fun undo() {
        if (undoStack.isEmpty()) return

        redoStack.add(_uiState.value.toEditorState())
        val previousState = undoStack.removeLast()
        _uiState.update { it.fromEditorState(previousState) }
    }

    fun redo() {
        if (redoStack.isEmpty()) return

        undoStack.add(_uiState.value.toEditorState())
        val nextState = redoStack.removeLast()
        _uiState.update { it.fromEditorState(nextState) }
    }

    private fun saveStateForUndo() {
        undoStack.add(_uiState.value.toEditorState())
        // Limit stack size
        if (undoStack.size > MAX_UNDO_STACK) {
            undoStack.removeAt(0)
        }
    }

    companion object {
        private const val MAX_UNDO_STACK = 50
    }
}

data class EditorState(
    val text: String,
    val cursorPosition: Int
)

data class EditorUiState(
    val text: String = "",
    val cursorPosition: Int = 0
) {
    fun toEditorState() = EditorState(text, cursorPosition)
    fun fromEditorState(state: EditorState) = copy(
        text = state.text,
        cursorPosition = state.cursorPosition
    )
}
```

---

## 摘要

這些進階模式建立在 MVVM 核心概念之上：

| 模式 | 使用場景 |
|---------|----------|
| 多模組 | 大型應用程式與團隊協作 |
| 狀態機 | 有明確轉換的複雜流程 |
| 分頁載入 | 大型資料集 |
| 表單驗證 | 具即時回饋的使用者輸入 |
| 搜尋 + 防抖 | 使用者輸入時的高效搜尋 |
| 多選 | 相簿、檔案管理員、列表 |
| 復原/重做 | 編輯器、繪圖應用程式 |

根據應用程式的複雜度選擇模式。從簡單開始，只在需要時才增加複雜度。
