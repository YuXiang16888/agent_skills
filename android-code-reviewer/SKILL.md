---
name: android-code-reviewer
description: 當審查 Android/Kotlin 程式碼的品質、記憶體安全性、架構合規性、命名規範和錯誤處理時使用此技能。涵蓋 context 洩漏、coroutine 管理、LiveData/Flow 模式及量化程式碼標準。
---

# Android 程式碼審查技能

Android/Kotlin 程式碼的全面審查清單，涵蓋記憶體洩漏、coroutine 管理、架構標準、命名規範和錯誤處理最佳實踐。

## 何時使用此技能

適用於以下情況：
- 審查 Android/Kotlin 程式碼的記憶體洩漏與 context 安全性
- 檢查 coroutine scope 管理與生命週期感知
- 驗證 LiveData/Flow 觀察模式
- 確保架構層次合規性（UI → Domain → Data）
- 驗證命名規範與程式碼品質標準
- 審查錯誤處理實作

---

## 1. 防止 Context 洩漏

### 錯誤模式（需標記）

```kotlin
// 不良：在 ViewModel 中持有 Activity 參考
class MyViewModel : ViewModel() {
    lateinit var activity: Activity  // 記憶體洩漏！
}

// 不良：在單例中儲存 Activity Context
object MySingleton {
    lateinit var context: Context  // 若為 Activity context，會發生洩漏！
}

// 不良：非靜態內部類別持有外部 Activity 參考
class MyActivity : AppCompatActivity() {
    inner class MyTask : AsyncTask<...>() {  // 持有隱式參考！
    }
}

// 不良：在 Activity 中宣告為內部類別的 Handler
class MyActivity : AppCompatActivity() {
    val handler = object : Handler(Looper.getMainLooper()) {  // 會發生洩漏！
        override fun handleMessage(msg: Message) { }
    }
}
```

### 正確模式（應核准）

```kotlin
// 良好：在 ViewModel 中使用 Application Context（如有需要）
class MyViewModel(application: Application) : AndroidViewModel(application) {
    private val appContext = application.applicationContext
}

// 良好：使用 WeakReference 包裝 Context
class MyCallback(context: Context) {
    private val contextRef = WeakReference(context)

    fun doWork() {
        contextRef.get()?.let { ctx ->
            // 安全地使用 context
        }
    }
}

// 良好：帶有 WeakReference 的靜態內部類別
class MyActivity : AppCompatActivity() {
    private class MyHandler(activity: MyActivity) : Handler(Looper.getMainLooper()) {
        private val activityRef = WeakReference(activity)

        override fun handleMessage(msg: Message) {
            activityRef.get()?.handleResult(msg)
        }
    }
}

// 良好：使用具生命週期感知的元件
class MyObserver : DefaultLifecycleObserver {
    override fun onDestroy(owner: LifecycleOwner) {
        // 清理資源
    }
}
```

---

## 2. Coroutine Scope 管理

### 錯誤模式（需標記）

```kotlin
// 不良：使用 GlobalScope（永不取消）
GlobalScope.launch {
    fetchData()  // Activity 銷毀後仍繼續執行！
}

// 不良：建立 CoroutineScope 卻未取消
class MyActivity : AppCompatActivity() {
    private val scope = CoroutineScope(Dispatchers.Main)

    fun loadData() {
        scope.launch { /* ... */ }  // 永不取消！
    }
    // 缺少：在 onDestroy() 中呼叫 scope.cancel()
}

// 不良：在主執行緒使用 runBlocking
fun onClick() {
    runBlocking {  // 阻塞 UI 執行緒！
        fetchData()
    }
}

// 不良：未加例外處理就啟動 coroutine
viewModelScope.launch {
    fetchData()  // 拋出例外時會靜默失敗！
}
```

### 正確模式（應核准）

```kotlin
// 良好：在 ViewModel 中使用 viewModelScope
class MyViewModel : ViewModel() {
    fun loadData() {
        viewModelScope.launch {
            // ViewModel 清除時自動取消
            val data = repository.fetchData()
        }
    }
}

// 良好：在 Activity/Fragment 中使用 lifecycleScope
class MyFragment : Fragment() {
    fun loadData() {
        viewLifecycleOwner.lifecycleScope.launch {
            // 隨 view 生命週期取消
            val data = viewModel.getData()
        }
    }
}

// 良好：自訂 scope 並正確取消
class MyService : Service() {
    private val scope = CoroutineScope(SupervisorJob() + Dispatchers.Main)

    override fun onDestroy() {
        super.onDestroy()
        scope.cancel()  // 正確清理！
    }
}

// 良好：使用 supervisorScope 處理例外
viewModelScope.launch {
    supervisorScope {
        val result1 = async { fetchData1() }
        val result2 = async { fetchData2() }
        // 其中一個失敗不會取消另一個
    }
}

// 良好：使用 try-catch 處理例外
viewModelScope.launch {
    try {
        val data = repository.fetchData()
        _uiState.value = UiState.Success(data)
    } catch (e: Exception) {
        _uiState.value = UiState.Error(e.message)
    }
}

// 良好：使用 WorkManager 處理長時間背景任務
class MyWorker(context: Context, params: WorkerParameters) : CoroutineWorker(context, params) {
    override suspend fun doWork(): Result {
        return try {
            performLongRunningTask()
            Result.success()
        } catch (e: Exception) {
            Result.retry()
        }
    }
}
```

---

## 3. LiveData/Flow 觀察模式

### 錯誤模式（需標記）

```kotlin
// 不良：在 Fragment 中使用 'this' 而非 viewLifecycleOwner
class MyFragment : Fragment() {
    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        viewModel.data.observe(this) { data ->  // 應使用 viewLifecycleOwner！
            updateUI(data)
        }
    }
}

// 不良：在 Fragment 中使用 lifecycleScope 收集 Flow
class MyFragment : Fragment() {
    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        lifecycleScope.launch {  // 應使用 viewLifecycleOwner.lifecycleScope！
            viewModel.stateFlow.collect { state ->
                updateUI(state)
            }
        }
    }
}

// 不良：手動觀察 LiveData 卻未清理
class MyActivity : AppCompatActivity() {
    private val observer = Observer<Data> { updateUI(it) }

    override fun onCreate(savedInstanceState: Bundle?) {
        viewModel.data.observeForever(observer)  // 從未移除！
    }
}
```

### 正確模式（應核准）

```kotlin
// 良好：在 Fragment 中使用 viewLifecycleOwner
class MyFragment : Fragment() {
    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        viewModel.data.observe(viewLifecycleOwner) { data ->
            updateUI(data)
        }
    }
}

// 良好：使用 viewLifecycleOwner.lifecycleScope 搭配 repeatOnLifecycle
class MyFragment : Fragment() {
    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        viewLifecycleOwner.lifecycleScope.launch {
            viewLifecycleOwner.repeatOnLifecycle(Lifecycle.State.STARTED) {
                viewModel.stateFlow.collect { state ->
                    updateUI(state)
                }
            }
        }
    }
}

// 良好：使用 observeForever 時正確清理
class MyActivity : AppCompatActivity() {
    private val observer = Observer<Data> { updateUI(it) }

    override fun onCreate(savedInstanceState: Bundle?) {
        viewModel.data.observeForever(observer)
    }

    override fun onDestroy() {
        super.onDestroy()
        viewModel.data.removeObserver(observer)
    }
}

// 良好：在 Compose 中使用 collectAsState
@Composable
fun MyScreen(viewModel: MyViewModel) {
    val uiState by viewModel.uiState.collectAsState()
    // Compose 自動管理生命週期
}
```

---

## 4. 靜態參考安全性

### 安全的靜態使用（應核准）

```kotlin
// 安全：靜態參考 Application Context
object AppContextHolder {
    lateinit var appContext: Context
        private set

    fun init(context: Context) {
        appContext = context.applicationContext  // 僅限 Application 層級！
    }
}

// 安全：純資料常數
companion object {
    const val TAG = "MyClass"
    const val DEFAULT_TIMEOUT = 30_000L
    const val MAX_RETRY_COUNT = 3
}

// 安全：無狀態工具函式
object StringUtils {
    fun formatCurrency(amount: Double): String {
        return NumberFormat.getCurrencyInstance().format(amount)
    }
}

// 安全：Singleton holder 模式（雙重檢查鎖定）
class MySingleton private constructor() {
    companion object {
        @Volatile
        private var instance: MySingleton? = null

        fun getInstance(): MySingleton {
            return instance ?: synchronized(this) {
                instance ?: MySingleton().also { instance = it }
            }
        }
    }
}
```

### 不安全的靜態使用（需標記）

```kotlin
// 不安全：靜態持有 Activity/View/Fragment
object UnsafeHolder {
    var activity: Activity? = null  // 記憶體洩漏！
    var view: View? = null  // 記憶體洩漏！
}

// 不安全：靜態集合持有容易洩漏的物件
object DataCache {
    val listeners = mutableListOf<OnDataChangedListener>()  // 洩漏！
}

// 不安全：沒有 WeakReference 的靜態 Handler
object MessageHandler {
    val handler = Handler(Looper.getMainLooper())  // 可能洩漏！
}

// 不安全：持有可變 Context 的 Singleton
object ConfigManager {
    var context: Context? = null  // 可能是 Activity context！
}
```

---

## 5. 匿名內部類別指南

### 可接受的匿名內部類別

```kotlin
// 可接受：短暫存活的點擊監聽器
button.setOnClickListener {
    // 立即執行，不持有長期參考
    navigateToDetails()
}

// 可接受：限定在單一函式範圍內的 Runnable
fun animateView() {
    view.postDelayed({
        // 短暫存活，限定在函式範圍內
        view.alpha = 1f
    }, 300)
}

// 可接受：Jetpack Compose lambda（由框架管理）
Button(onClick = {
    // Compose 自動管理生命週期
    viewModel.onButtonClicked()
}) {
    Text("Click Me")
}
```

### 不可接受的匿名內部類別（需標記）

```kotlin
// 不可接受：長時間執行的 AsyncTask 內部類別
class MyActivity : AppCompatActivity() {
    fun startWork() {
        object : AsyncTask<Void, Void, String>() {  // 持有 Activity 參考！
            override fun doInBackground(vararg params: Void?): String {
                // 長時間執行的任務
            }
        }.execute()
    }
}

// 不可接受：持有超過 Activity 生命週期的 Callback
class MyActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        networkClient.setCallback(object : Callback {  // 存活超過 Activity！
            override fun onResult(data: Data) {
                updateUI(data)  // Activity 銷毀後可能崩潰
            }
        })
    }
}

// 不可接受：生命週期超過 Activity 的監聽器
class MyActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        GlobalEventBus.register(object : EventListener {  // 從未取消註冊！
            override fun onEvent(event: Event) {
                handleEvent(event)
            }
        })
    }
}
```

---

## 6. Companion Object 使用

### 適當的 Companion Object 使用

```kotlin
class MyViewModel : ViewModel() {
    companion object {
        // 良好：常數
        private const val TAG = "MyViewModel"
        private const val DEFAULT_PAGE_SIZE = 20

        // 良好：工廠方法模式
        fun create(repository: UserRepository): MyViewModel {
            return MyViewModel().apply {
                this.repository = repository
            }
        }

        // 良好：與此類別強相關的靜態工具函式
        fun isValidUserId(id: String): Boolean {
            return id.matches(Regex("^[a-zA-Z0-9]{8,}$"))
        }
    }
}

// 良好：在 companion object 中使用 Singleton holder
class DatabaseHelper private constructor(context: Context) {
    companion object {
        @Volatile
        private var instance: DatabaseHelper? = null

        fun getInstance(context: Context): DatabaseHelper {
            return instance ?: synchronized(this) {
                instance ?: DatabaseHelper(context.applicationContext).also { instance = it }
            }
        }
    }
}
```

### 不適當的 Companion Object 使用（需標記）

```kotlin
class MyViewModel : ViewModel() {
    companion object {
        // 不良：持有可變狀態
        var currentUser: User? = null  // 應放在 ViewModel 實例中！

        // 不良：應屬於 ViewModel 的資料
        val cachedItems = mutableListOf<Item>()  // 狀態應屬於實例！

        // 不良：取代依賴注入的全域變數
        var repository: UserRepository? = null  // 使用依賴注入！

        // 不良：繁重的商業邏輯
        fun processComplexData(data: List<Item>): Result {
            // 複雜邏輯應放在獨立的類別中
            // ...
        }
    }
}
```

---

## 7. 架構標準

### 首選三層架構

```
UI 層（Activity/Fragment/Compose）
        ↓ 觀察
Domain 層（UseCase/Interactor）
        ↓ 呼叫
Data 層（Repository）
        ↓ 抽象化
資料來源（遠端 API、本地 DB、快取）
```

### ViewModel 職責

```kotlin
// 正確：ViewModel 持有 UI 狀態、處理互動，不直接呼叫 Repository
class UserViewModel(
    private val getUserProfileUseCase: GetUserProfileUseCase,
    private val updateUserUseCase: UpdateUserUseCase
) : ViewModel() {

    private val _uiState = MutableStateFlow<UserUiState>(UserUiState.Loading)
    val uiState: StateFlow<UserUiState> = _uiState.asStateFlow()

    fun loadUserProfile(userId: String) {
        viewModelScope.launch {
            _uiState.value = UserUiState.Loading
            getUserProfileUseCase(userId)
                .onSuccess { user -> _uiState.value = UserUiState.Success(user) }
                .onFailure { error -> _uiState.value = UserUiState.Error(error.message) }
        }
    }
}

// 錯誤：ViewModel 直接呼叫 Repository
class BadViewModel(
    private val userRepository: UserRepository  // 應使用 UseCase！
) : ViewModel()
```

### Repository 職責

```kotlin
// 正確：Repository 抽象化資料來源、處理快取、映射 DTO
class UserRepositoryImpl(
    private val remoteDataSource: UserRemoteDataSource,
    private val localDataSource: UserLocalDataSource,
    private val userMapper: UserMapper
) : UserRepository {

    override suspend fun getUser(id: String): Result<User> {
        // 先嘗試快取
        localDataSource.getUser(id)?.let { cached ->
            return Result.success(userMapper.toDomain(cached))
        }

        // 從遠端獲取
        return remoteDataSource.fetchUser(id)
            .map { dto -> userMapper.toDomain(dto) }
            .onSuccess { user -> localDataSource.saveUser(userMapper.toEntity(user)) }
    }
}
```

### UseCase 規格

```kotlin
// 正確：單一職責、可組合、可測試
class GetUserProfileUseCase(
    private val userRepository: UserRepository,
    private val preferencesRepository: PreferencesRepository
) {
    suspend operator fun invoke(userId: String): Result<UserProfile> {
        val user = userRepository.getUser(userId).getOrElse { return Result.failure(it) }
        val preferences = preferencesRepository.getPreferences(userId).getOrDefault(Preferences.DEFAULT)

        return Result.success(UserProfile(user, preferences))
    }
}
```

### 狀態管理標準

```kotlin
// 正確：使用密封類別/介面表示 UI 狀態
sealed interface UserUiState {
    object Loading : UserUiState
    data class Success(val user: User) : UserUiState
    data class Error(val message: String) : UserUiState
}

// 正確：一次性事件消費（避免重複觸發）
class MyViewModel : ViewModel() {
    private val _event = MutableSharedFlow<UiEvent>()
    val event: SharedFlow<UiEvent> = _event.asSharedFlow()

    fun showMessage(message: String) {
        viewModelScope.launch {
            _event.emit(UiEvent.ShowSnackbar(message))
        }
    }
}

sealed interface UiEvent {
    data class ShowSnackbar(val message: String) : UiEvent
    data class Navigate(val route: String) : UiEvent
}
```

---

## 8. 命名規範

### 檔案命名

| 類型 | 規範 | 範例 |
|------|------|------|
| Activity | `XxxActivity.kt` | `LoginActivity.kt` |
| Fragment | `XxxFragment.kt` | `ProfileFragment.kt` |
| ViewModel | `XxxViewModel.kt` | `UserViewModel.kt` |
| Adapter | `XxxAdapter.kt` | `UserListAdapter.kt` |
| UseCase | `XxxUseCase.kt` 或動詞前綴 | `GetUserProfileUseCase.kt` |
| Repository | `XxxRepository.kt` | `UserRepository.kt` |
| Data Source | `XxxDataSource.kt` | `UserRemoteDataSource.kt` |

### 變數命名

```kotlin
// Boolean：isXxx、hasXxx、shouldXxx
val isLoading: Boolean = false
val hasError: Boolean = true
val shouldRetry: Boolean = false

// 集合：使用複數形式
val users: List<User> = emptyList()
val items: Set<Item> = emptySet()
val orderMap: Map<String, Order> = emptyMap()

// LiveData/Flow：可選加 State/Flow 後綴（依團隊規範）
val userState: StateFlow<User>
val loadingFlow: Flow<Boolean>
val userData: LiveData<User>

// 常數：UPPER_SNAKE_CASE
companion object {
    const val MAX_RETRY_COUNT = 3
    const val DEFAULT_TIMEOUT_MS = 30_000L
    const val API_BASE_URL = "https://api.example.com"
}
```

### 函式命名

```kotlin
// 事件處理器：onXxxClicked、onXxxChanged
fun onLoginButtonClicked() { }
fun onUsernameChanged(username: String) { }
fun onItemSelected(item: Item) { }

// 取得資料：getXxx、fetchXxx（網路）、loadXxx（本地）
fun getUser(id: String): User
suspend fun fetchUserFromApi(id: String): User
fun loadUserFromCache(id: String): User?

// 設定資料：setXxx、updateXxx
fun setUserName(name: String)
fun updateUserProfile(profile: UserProfile)

// 驗證：validateXxx、checkXxx
fun validateEmail(email: String): Boolean
fun checkPermission(permission: String): Boolean
fun isValidPassword(password: String): Boolean
```

---

## 9. 錯誤處理

### API 錯誤處理模式

```kotlin
// 使用 Result<T> 或密封類別 ApiResult<T>
sealed class ApiResult<out T> {
    data class Success<T>(val data: T) : ApiResult<T>()
    data class Error(val exception: Exception, val message: String) : ApiResult<Nothing>()
    object Loading : ApiResult<Nothing>()
}

// Repository 層：捕捉並轉換為 Domain 錯誤
class UserRepositoryImpl(
    private val api: UserApi
) : UserRepository {

    override suspend fun getUser(id: String): Result<User> {
        return try {
            val response = api.getUser(id)
            if (response.isSuccessful) {
                Result.success(response.body()!!.toDomain())
            } else {
                Result.failure(mapHttpError(response.code()))
            }
        } catch (e: IOException) {
            Result.failure(NetworkException("Network unavailable", e))
        } catch (e: SocketTimeoutException) {
            Result.failure(TimeoutException("Request timed out", e))
        } catch (e: JsonParseException) {
            Result.failure(ParseException("Invalid response format", e))
        }
    }

    private fun mapHttpError(code: Int): DomainException {
        return when (code) {
            401 -> AuthenticationException("Session expired")
            403 -> AuthorizationException("Access denied")
            404 -> NotFoundException("Resource not found")
            in 500..599 -> ServerException("Server error, please try again")
            else -> UnknownException("Unknown error occurred")
        }
    }
}
```

### 例外處理原則

```kotlin
// ViewModel 層：處理商業邏輯錯誤
class UserViewModel(
    private val getUserUseCase: GetUserUseCase
) : ViewModel() {

    fun loadUser(userId: String) {
        viewModelScope.launch {
            _uiState.value = UiState.Loading

            getUserUseCase(userId)
                .onSuccess { user ->
                    _uiState.value = UiState.Success(user)
                }
                .onFailure { error ->
                    when (error) {
                        is AuthenticationException -> {
                            _event.emit(UiEvent.NavigateToLogin)
                        }
                        is NetworkException -> {
                            _uiState.value = UiState.Error(
                                message = R.string.error_network,
                                canRetry = true
                            )
                        }
                        else -> {
                            _uiState.value = UiState.Error(
                                message = R.string.error_generic,
                                canRetry = false
                            )
                            // 絕不靜默吞噬例外——至少要記錄日誌
                            Log.e(TAG, "Unexpected error", error)
                        }
                    }
                }
        }
    }
}

// UI 層：僅負責顯示錯誤訊息
@Composable
fun UserScreen(viewModel: UserViewModel) {
    val uiState by viewModel.uiState.collectAsState()

    when (val state = uiState) {
        is UiState.Error -> {
            ErrorView(
                message = stringResource(state.message),  // 已本地化！
                onRetry = if (state.canRetry) viewModel::retry else null
            )
        }
        // ...
    }
}
```

### 網路錯誤分類

```kotlin
// 自訂例外階層
sealed class DomainException(message: String, cause: Throwable? = null) : Exception(message, cause)

// HTTP 錯誤
class AuthenticationException(message: String) : DomainException(message)
class AuthorizationException(message: String) : DomainException(message)
class NotFoundException(message: String) : DomainException(message)
class ServerException(message: String) : DomainException(message)

// 網路錯誤
class NetworkException(message: String, cause: Throwable? = null) : DomainException(message, cause)
class TimeoutException(message: String, cause: Throwable? = null) : DomainException(message, cause)
class ParseException(message: String, cause: Throwable? = null) : DomainException(message, cause)
```

---

## 10. 量化程式碼標準

### 函式複雜度

| 指標 | 上限 | 建議值 |
|------|------|--------|
| 循環複雜度 | < 10 | < 5 |
| 函式長度 | < 50 行 | < 30 行 |
| 參數數量 | < 5 個 | 超過時考慮使用 Data Class |

```kotlin
// 不良：參數過多
fun createUser(
    name: String,
    email: String,
    phone: String,
    address: String,
    city: String,
    country: String  // 6 個參數——太多了！
): User

// 良好：使用 Data Class
data class CreateUserRequest(
    val name: String,
    val email: String,
    val phone: String,
    val address: Address
)

fun createUser(request: CreateUserRequest): User
```

### 巢狀深度

| 指標 | 上限 |
|------|------|
| If/迴圈巢狀 | ≤ 3 層 |
| Lambda 巢狀 | ≤ 2 層 |

```kotlin
// 不良：過深的巢狀
fun processData(data: Data) {
    if (data.isValid) {
        for (item in data.items) {
            if (item.isActive) {
                for (subItem in item.subItems) {
                    if (subItem.matches(criteria)) {  // 深達 4 層！
                        // 處理
                    }
                }
            }
        }
    }
}

// 良好：提早返回模式
fun processData(data: Data) {
    if (!data.isValid) return

    data.items
        .filter { it.isActive }
        .flatMap { it.subItems }
        .filter { it.matches(criteria) }
        .forEach { processItem(it) }
}
```

### 類別大小

| 指標 | 上限 | 建議值 |
|------|------|--------|
| 單一類別 | < 300 行 | < 200 行 |

若超過，請考慮拆分職責。

### 魔術數字

```kotlin
// 不良：魔術數字
fun calculateDiscount(price: Double): Double {
    return if (price > 100) {
        price * 0.15  // 0.15 是什麼？
    } else {
        price * 0.05  // 0.05 是什麼？
    }
}

// 良好：使用具名常數
companion object {
    private const val DISCOUNT_THRESHOLD = 100.0
    private const val HIGH_VALUE_DISCOUNT_RATE = 0.15
    private const val STANDARD_DISCOUNT_RATE = 0.05
}

fun calculateDiscount(price: Double): Double {
    return if (price > DISCOUNT_THRESHOLD) {
        price * HIGH_VALUE_DISCOUNT_RATE
    } else {
        price * STANDARD_DISCOUNT_RATE
    }
}
```

**例外情況：** 0、1 和 -1 在用途明確時（如陣列索引、遞增等）可直接使用，無需定義常數。

---

## 快速參考清單

### 記憶體安全
- [ ] ViewModel 中無 Activity/Fragment/View 參考
- [ ] 單例中無 Activity Context
- [ ] 內部類別為靜態並使用 WeakReference
- [ ] Handler 使用 WeakReference 或具生命週期感知

### Coroutines
- [ ] 無使用 GlobalScope
- [ ] ViewModel 中使用 viewModelScope
- [ ] Fragment 中使用 viewLifecycleOwner.lifecycleScope
- [ ] 自訂 scope 在清理時有呼叫 cancel()
- [ ] 有例外處理

### LiveData/Flow
- [ ] Fragment 中使用 viewLifecycleOwner（非 `this`）
- [ ] Flow 收集使用 repeatOnLifecycle
- [ ] observeForever 有對應的 removeObserver

### 架構
- [ ] UI → Domain → Data 層次分離
- [ ] ViewModel 使用 UseCase，不直接使用 Repository
- [ ] UI 狀態使用密封類別（Loading/Success/Error）
- [ ] 一次性事件使用 SharedFlow

### 命名
- [ ] 檔案遵循 XxxActivity/XxxViewModel/XxxUseCase 命名模式
- [ ] Boolean 使用 isXxx/hasXxx/shouldXxx
- [ ] 常數使用 UPPER_SNAKE_CASE
- [ ] 事件處理器使用 onXxxClicked 模式

### 程式碼品質
- [ ] 循環複雜度 < 10
- [ ] 函式長度 < 50 行
- [ ] 參數數量 < 5（否則使用 Data Class）
- [ ] 巢狀深度 ≤ 3 層
- [ ] 無魔術數字（0、1、-1 除外）
- [ ] 類別大小 < 300 行

---

*Generated by Skill Seekers - Android Code Review Checklist v1.0.0*
