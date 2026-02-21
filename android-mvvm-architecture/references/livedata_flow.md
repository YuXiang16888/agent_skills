# LiveData、StateFlow 與 Flow 指南

## 目錄
1. [概覽與比較](#overview-and-comparison)
2. [LiveData](#livedata)
3. [StateFlow](#stateflow)
4. [SharedFlow](#sharedflow)
5. [Flow](#flow)
6. [遷移指南](#migration-guide)
7. [最佳實踐](#best-practices)

---

## 概覽與比較

### 快速比較

| 功能 | LiveData | StateFlow | SharedFlow | Flow |
|---------|----------|-----------|------------|------|
| 初始值 | 選填 | 必填 | 不儲存 | N/A |
| 重播 | 最新值 | 最新值 | 可設定 | 無 |
| 生命週期感知 | 是（自動） | 手動（repeatOnLifecycle） | 手動 | 手動 |
| 熱/冷 | 熱 | 熱 | 熱 | 冷 |
| 多個收集器 | 是 | 是 | 是 | 每個收集器獲得自己的串流 |
| Null 值 | 允許 | 可設定 | 可設定 | 允許 |
| 運算子 | 有限（map, switchMap） | 完整 Flow 運算子 | 完整 Flow 運算子 | 完整運算子 |
| Android 依賴 | 是 | 否（純 Kotlin） | 否 | 否 |

### 何時使用哪個

```kotlin
// LiveData: Legacy projects, simple UI state
val userName: LiveData<String> = _userName

// StateFlow: UI state in modern projects (RECOMMENDED)
val uiState: StateFlow<UiState> = _uiState.asStateFlow()

// SharedFlow: One-time events (navigation, snackbars)
val events: SharedFlow<UiEvent> = _events.asSharedFlow()

// Flow: Data streams from repository, database queries
fun observeUsers(): Flow<List<User>>
```

---

## LiveData

### 基本用法

```kotlin
class UserViewModel : ViewModel() {

    // Private mutable LiveData
    private val _user = MutableLiveData<User>()

    // Public immutable LiveData
    val user: LiveData<User> = _user

    // Set value (main thread only)
    fun loadUser() {
        viewModelScope.launch {
            val user = repository.getUser()
            _user.value = user  // Main thread
        }
    }

    // Post value (any thread)
    fun loadUserBackground() {
        thread {
            val user = repository.getUser()
            _user.postValue(user)  // Background thread
        }
    }
}

// Observing in Fragment
class UserFragment : Fragment() {
    private val viewModel: UserViewModel by viewModels()

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)

        // Automatic lifecycle handling
        viewModel.user.observe(viewLifecycleOwner) { user ->
            binding.nameText.text = user.name
        }
    }
}
```

### LiveData 轉換

```kotlin
class UserViewModel : ViewModel() {

    private val _userId = MutableLiveData<Long>()
    val userId: LiveData<Long> = _userId

    // map: Transform LiveData value
    val userName: LiveData<String> = _user.map { user ->
        user?.name ?: "Unknown"
    }

    // switchMap: Switch to different LiveData based on value
    val userDetails: LiveData<UserDetails> = _userId.switchMap { id ->
        repository.observeUserDetails(id)
    }

    // MediatorLiveData: Combine multiple LiveData sources
    private val _firstName = MutableLiveData<String>()
    private val _lastName = MutableLiveData<String>()

    val fullName: LiveData<String> = MediatorLiveData<String>().apply {
        fun update() {
            val first = _firstName.value ?: ""
            val last = _lastName.value ?: ""
            value = "$first $last"
        }

        addSource(_firstName) { update() }
        addSource(_lastName) { update() }
    }
}
```

### LiveData 與協程

```kotlin
class UserViewModel : ViewModel() {

    // liveData builder - automatically cancels when inactive
    val user: LiveData<Result<User>> = liveData {
        emit(Result.Loading)
        try {
            val user = repository.getUser()
            emit(Result.Success(user))
        } catch (e: Exception) {
            emit(Result.Error(e))
        }
    }

    // Emit from suspend function
    val users: LiveData<List<User>> = liveData {
        val data = repository.getUsers()
        emit(data)

        // Continue emitting updates
        repository.observeUsers().collect { users ->
            emit(users)
        }
    }

    // Switch between LiveData sources
    val searchResults: LiveData<List<User>> = searchQuery.switchMap { query ->
        liveData {
            emit(emptyList())
            if (query.length >= 2) {
                val results = repository.search(query)
                emit(results)
            }
        }
    }
}
```

---

## StateFlow

StateFlow 是一個狀態持有可觀察流，向其收集器發出當前和新的狀態更新。**現代 Android 開發中推薦用於 UI 狀態。**

### 基本用法

```kotlin
@HiltViewModel
class UserViewModel @Inject constructor(
    private val repository: UserRepository
) : ViewModel() {

    // Private mutable StateFlow
    private val _uiState = MutableStateFlow(UserUiState())

    // Public immutable StateFlow
    val uiState: StateFlow<UserUiState> = _uiState.asStateFlow()

    init {
        loadUser()
    }

    private fun loadUser() {
        viewModelScope.launch {
            _uiState.update { it.copy(isLoading = true) }

            try {
                val user = repository.getUser()
                _uiState.update { it.copy(isLoading = false, user = user) }
            } catch (e: Exception) {
                _uiState.update { it.copy(isLoading = false, error = e.message) }
            }
        }
    }
}

data class UserUiState(
    val isLoading: Boolean = false,
    val user: User? = null,
    val error: String? = null
)
```

### 在 Fragment 中收集 StateFlow

```kotlin
@AndroidEntryPoint
class UserFragment : Fragment(R.layout.fragment_user) {

    private val viewModel: UserViewModel by viewModels()
    private var _binding: FragmentUserBinding? = null
    private val binding get() = _binding!!

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        _binding = FragmentUserBinding.bind(view)

        // CORRECT: Lifecycle-aware collection
        viewLifecycleOwner.lifecycleScope.launch {
            viewLifecycleOwner.repeatOnLifecycle(Lifecycle.State.STARTED) {
                viewModel.uiState.collect { state ->
                    updateUI(state)
                }
            }
        }
    }

    private fun updateUI(state: UserUiState) {
        binding.apply {
            progressBar.isVisible = state.isLoading
            errorText.isVisible = state.error != null
            errorText.text = state.error
            contentGroup.isVisible = state.user != null

            state.user?.let { user ->
                nameText.text = user.name
                emailText.text = user.email
            }
        }
    }

    override fun onDestroyView() {
        super.onDestroyView()
        _binding = null
    }
}
```

### 在 Compose 中收集 StateFlow

```kotlin
@Composable
fun UserScreen(
    viewModel: UserViewModel = hiltViewModel()
) {
    // collectAsStateWithLifecycle handles lifecycle automatically
    val uiState by viewModel.uiState.collectAsStateWithLifecycle()

    UserContent(uiState = uiState)
}

@Composable
private fun UserContent(uiState: UserUiState) {
    Box(modifier = Modifier.fillMaxSize()) {
        when {
            uiState.isLoading -> {
                CircularProgressIndicator(
                    modifier = Modifier.align(Alignment.Center)
                )
            }
            uiState.error != null -> {
                Text(
                    text = uiState.error,
                    modifier = Modifier.align(Alignment.Center)
                )
            }
            uiState.user != null -> {
                Column {
                    Text(text = uiState.user.name)
                    Text(text = uiState.user.email)
                }
            }
        }
    }
}
```

### StateFlow 更新模式

```kotlin
@HiltViewModel
class FormViewModel @Inject constructor() : ViewModel() {

    private val _formState = MutableStateFlow(FormState())
    val formState: StateFlow<FormState> = _formState.asStateFlow()

    // Pattern 1: Update with copy
    fun updateName(name: String) {
        _formState.update { currentState ->
            currentState.copy(name = name)
        }
    }

    // Pattern 2: Direct value assignment
    fun clearForm() {
        _formState.value = FormState()
    }

    // Pattern 3: Update multiple fields
    fun validateAndSubmit() {
        _formState.update { state ->
            state.copy(
                isSubmitting = true,
                nameError = if (state.name.isBlank()) "Name required" else null,
                emailError = if (!state.email.contains("@")) "Invalid email" else null
            )
        }
    }

    // Pattern 4: Conditional update
    fun toggleAgreeTerms() {
        _formState.update { state ->
            if (!state.isSubmitting) {
                state.copy(agreeTerms = !state.agreeTerms)
            } else {
                state // Don't update if submitting
            }
        }
    }
}

data class FormState(
    val name: String = "",
    val email: String = "",
    val nameError: String? = null,
    val emailError: String? = null,
    val agreeTerms: Boolean = false,
    val isSubmitting: Boolean = false
)
```

### 合併多個 StateFlow

```kotlin
@HiltViewModel
class DashboardViewModel @Inject constructor(
    private val userRepository: UserRepository,
    private val statsRepository: StatsRepository
) : ViewModel() {

    private val _user = MutableStateFlow<User?>(null)
    private val _stats = MutableStateFlow<Stats?>(null)
    private val _isLoading = MutableStateFlow(false)

    // Combine multiple flows into single UI state
    val uiState: StateFlow<DashboardUiState> = combine(
        _user,
        _stats,
        _isLoading
    ) { user, stats, isLoading ->
        DashboardUiState(
            user = user,
            stats = stats,
            isLoading = isLoading
        )
    }.stateIn(
        scope = viewModelScope,
        started = SharingStarted.WhileSubscribed(5000),
        initialValue = DashboardUiState()
    )
}
```

---

## SharedFlow

SharedFlow 用於不應重播給新收集器的一次性事件（導航、Snackbar、Toast）。

### 基本用法

```kotlin
@HiltViewModel
class CheckoutViewModel @Inject constructor(
    private val orderRepository: OrderRepository
) : ViewModel() {

    // UI State
    private val _uiState = MutableStateFlow(CheckoutUiState())
    val uiState: StateFlow<CheckoutUiState> = _uiState.asStateFlow()

    // One-time events
    private val _events = MutableSharedFlow<CheckoutEvent>()
    val events: SharedFlow<CheckoutEvent> = _events.asSharedFlow()

    fun onPlaceOrderClicked() {
        viewModelScope.launch {
            _uiState.update { it.copy(isPlacingOrder = true) }

            try {
                val order = orderRepository.placeOrder()
                _events.emit(CheckoutEvent.OrderPlaced(order.id))
            } catch (e: Exception) {
                _events.emit(CheckoutEvent.ShowError(e.message ?: "Order failed"))
            } finally {
                _uiState.update { it.copy(isPlacingOrder = false) }
            }
        }
    }
}

sealed class CheckoutEvent {
    data class OrderPlaced(val orderId: String) : CheckoutEvent()
    data class ShowError(val message: String) : CheckoutEvent()
    object NavigateToCart : CheckoutEvent()
}
```

### 收集 SharedFlow

```kotlin
@AndroidEntryPoint
class CheckoutFragment : Fragment() {

    private val viewModel: CheckoutViewModel by viewModels()

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)

        viewLifecycleOwner.lifecycleScope.launch {
            viewLifecycleOwner.repeatOnLifecycle(Lifecycle.State.STARTED) {
                // Collect both state and events
                launch {
                    viewModel.uiState.collect { state ->
                        updateUI(state)
                    }
                }

                launch {
                    viewModel.events.collect { event ->
                        handleEvent(event)
                    }
                }
            }
        }
    }

    private fun handleEvent(event: CheckoutEvent) {
        when (event) {
            is CheckoutEvent.OrderPlaced -> {
                findNavController().navigate(
                    CheckoutFragmentDirections.actionToOrderConfirmation(event.orderId)
                )
            }
            is CheckoutEvent.ShowError -> {
                Snackbar.make(binding.root, event.message, Snackbar.LENGTH_LONG).show()
            }
            CheckoutEvent.NavigateToCart -> {
                findNavController().navigateUp()
            }
        }
    }
}
```

### SharedFlow 設定

```kotlin
// Default: No replay, no buffer
private val _events = MutableSharedFlow<Event>()

// With replay: New collectors get last N events
private val _replayEvents = MutableSharedFlow<Event>(replay = 1)

// With buffer: Handle fast emissions
private val _bufferedEvents = MutableSharedFlow<Event>(
    replay = 0,
    extraBufferCapacity = 64,
    onBufferOverflow = BufferOverflow.DROP_OLDEST
)

// Buffer overflow strategies:
// DROP_OLDEST - Drop oldest value when buffer is full
// DROP_LATEST - Drop new value when buffer is full
// SUSPEND - Suspend until buffer has space (default)
```

---

## Flow

Flow 是一個冷非同步串流，依序發出值。非常適合來自 repository 的資料串流。

### 含 Flow 的 Repository

```kotlin
interface UserRepository {
    fun observeUsers(): Flow<List<User>>
    fun observeUser(id: Long): Flow<User?>
    suspend fun getUser(id: Long): User?
}

class UserRepositoryImpl @Inject constructor(
    private val userDao: UserDao,
    private val userApi: UserApiService
) : UserRepository {

    // Observe database changes
    override fun observeUsers(): Flow<List<User>> {
        return userDao.observeAll()
            .map { entities -> entities.map { it.toDomain() } }
    }

    // Combine local and remote data
    override fun observeUser(id: Long): Flow<User?> {
        return flow {
            // Emit local data first
            emit(userDao.getById(id)?.toDomain())

            // Fetch fresh data from network
            try {
                val remoteUser = userApi.getUser(id)
                userDao.insert(remoteUser.toEntity())
            } catch (e: Exception) {
                // Network error - local data already emitted
            }
        }.flatMapLatest { _ ->
            // Continue observing database
            userDao.observeById(id).map { it?.toDomain() }
        }
    }
}
```

### Flow 運算子

```kotlin
@HiltViewModel
class SearchViewModel @Inject constructor(
    private val searchRepository: SearchRepository
) : ViewModel() {

    private val searchQuery = MutableStateFlow("")

    val searchResults: StateFlow<List<SearchResult>> = searchQuery
        // Debounce: Wait for user to stop typing
        .debounce(300)
        // Filter: Only search if query is long enough
        .filter { it.length >= 2 }
        // DistinctUntilChanged: Don't search same query twice
        .distinctUntilChanged()
        // FlatMapLatest: Cancel previous search when new query arrives
        .flatMapLatest { query ->
            searchRepository.search(query)
        }
        // Catch: Handle errors
        .catch { e ->
            emit(emptyList())
        }
        // Convert to StateFlow
        .stateIn(
            scope = viewModelScope,
            started = SharingStarted.WhileSubscribed(5000),
            initialValue = emptyList()
        )

    fun onSearchQueryChanged(query: String) {
        searchQuery.value = query
    }
}
```

### 常用 Flow 運算子

```kotlin
// Transform each value
flow.map { value -> transform(value) }

// Filter values
flow.filter { value -> value > 10 }

// Only emit when value changes
flow.distinctUntilChanged()

// Delay emissions
flow.debounce(300) // milliseconds

// Take only first N values
flow.take(5)

// Combine with another flow
flow1.combine(flow2) { a, b -> Pair(a, b) }

// Zip flows (pairs values)
flow1.zip(flow2) { a, b -> Pair(a, b) }

// Flatten nested flows
flow.flatMapLatest { innerFlow }
flow.flatMapMerge { innerFlow }
flow.flatMapConcat { innerFlow }

// Handle errors
flow.catch { e -> emit(defaultValue) }
flow.retry(3) { e -> e is IOException }

// Side effects
flow.onStart { /* before first emission */ }
flow.onEach { value -> /* for each value */ }
flow.onCompletion { cause -> /* when flow completes */ }

// Buffer emissions
flow.buffer(64)
flow.conflate() // Keep only latest value

// Change dispatcher
flow.flowOn(Dispatchers.IO)
```

### 將 Flow 轉換為 StateFlow

```kotlin
// stateIn: Convert cold Flow to hot StateFlow
val users: StateFlow<List<User>> = repository.observeUsers()
    .stateIn(
        scope = viewModelScope,
        started = SharingStarted.WhileSubscribed(5000),
        initialValue = emptyList()
    )

// SharingStarted options:
// Eagerly - Start immediately, never stop
// Lazily - Start when first collector appears, never stop
// WhileSubscribed(stopTimeoutMillis) - Start when collector appears,
//                                      stop after timeout when no collectors
```

---

## 遷移指南

### LiveData 遷移至 StateFlow

```kotlin
// BEFORE: LiveData
class OldViewModel : ViewModel() {
    private val _user = MutableLiveData<User>()
    val user: LiveData<User> = _user

    fun loadUser() {
        viewModelScope.launch {
            _user.value = repository.getUser()
        }
    }
}

// AFTER: StateFlow
class NewViewModel : ViewModel() {
    private val _user = MutableStateFlow<User?>(null)
    val user: StateFlow<User?> = _user.asStateFlow()

    fun loadUser() {
        viewModelScope.launch {
            _user.value = repository.getUser()
        }
    }
}
```

### Observer 遷移至 Collector

```kotlin
// BEFORE: LiveData observation
viewModel.user.observe(viewLifecycleOwner) { user ->
    updateUI(user)
}

// AFTER: StateFlow collection
viewLifecycleOwner.lifecycleScope.launch {
    viewLifecycleOwner.repeatOnLifecycle(Lifecycle.State.STARTED) {
        viewModel.user.collect { user ->
            updateUI(user)
        }
    }
}

// OR in Compose
val user by viewModel.user.collectAsStateWithLifecycle()
```

### Transformations 遷移至 Flow 運算子

```kotlin
// BEFORE: LiveData Transformations
val userName: LiveData<String> = Transformations.map(user) { it.name }
val userDetails: LiveData<Details> = Transformations.switchMap(userId) { id ->
    repository.observeDetails(id)
}

// AFTER: Flow operators
val userName: StateFlow<String> = user
    .map { it.name }
    .stateIn(viewModelScope, SharingStarted.WhileSubscribed(5000), "")

val userDetails: StateFlow<Details?> = userId
    .flatMapLatest { id -> repository.observeDetails(id) }
    .stateIn(viewModelScope, SharingStarted.WhileSubscribed(5000), null)
```

---

## 最佳實踐

### 1. 使用 StateFlow 儲存 UI 狀態

```kotlin
// RECOMMENDED: Single state object with StateFlow
data class ProfileUiState(
    val isLoading: Boolean = false,
    val user: User? = null,
    val error: String? = null
)

private val _uiState = MutableStateFlow(ProfileUiState())
val uiState: StateFlow<ProfileUiState> = _uiState.asStateFlow()
```

### 2. 使用 SharedFlow 處理事件

```kotlin
// Events that should not be replayed
private val _events = MutableSharedFlow<UiEvent>()
val events: SharedFlow<UiEvent> = _events.asSharedFlow()

// Navigation, Snackbars, Toasts are events
sealed class UiEvent {
    data class ShowSnackbar(val message: String) : UiEvent()
    data class Navigate(val destination: String) : UiEvent()
}
```

### 3. 永遠使用 repeatOnLifecycle

```kotlin
// CORRECT: Stops collecting when app is in background
viewLifecycleOwner.lifecycleScope.launch {
    viewLifecycleOwner.repeatOnLifecycle(Lifecycle.State.STARTED) {
        viewModel.uiState.collect { /* ... */ }
    }
}

// WRONG: Continues collecting even when app is in background
lifecycleScope.launch {
    viewModel.uiState.collect { /* Memory leak, wasted resources */ }
}
```

### 4. 使用含超時的 WhileSubscribed

```kotlin
// Stop upstream flow when no collectors for 5 seconds
val data: StateFlow<Data> = repository.observeData()
    .stateIn(
        scope = viewModelScope,
        started = SharingStarted.WhileSubscribed(5000),
        initialValue = Data()
    )
```

### 5. 處理設定變更

```kotlin
// StateFlow automatically handles configuration changes
// because it's scoped to ViewModel

// For one-time events, use SharedFlow without replay
private val _events = MutableSharedFlow<Event>()
// New collectors after config change won't receive old events
```

### 6. 並行收集多個 Flow

```kotlin
viewLifecycleOwner.lifecycleScope.launch {
    viewLifecycleOwner.repeatOnLifecycle(Lifecycle.State.STARTED) {
        // Launch separate coroutines for each flow
        launch { viewModel.uiState.collect { updateUI(it) } }
        launch { viewModel.events.collect { handleEvent(it) } }
        launch { viewModel.messages.collect { showMessage(it) } }
    }
}
```

### 7. 避免在 Flow 中進行阻塞操作

```kotlin
// WRONG: Blocking in flow
val data = flow {
    val result = blockingNetworkCall() // DON'T
    emit(result)
}

// CORRECT: Use flowOn with IO dispatcher
val data = flow {
    val result = suspendingNetworkCall()
    emit(result)
}.flowOn(Dispatchers.IO)
```
