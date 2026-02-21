---
name: android-mvvm-architecture
description: Android MVVM (Model-View-ViewModel) architecture pattern. Use for implementing clean architecture, ViewModel lifecycle management, LiveData/StateFlow/Flow reactive data handling, Repository pattern, ViewBinding/DataBinding integration, state management, and building testable Android applications with separation of concerns.
---

# Android MVVM 架構技能

使用 Kotlin、Jetpack 函式庫與現代響應式資料模式，在 Android 應用程式中實作 Model-View-ViewModel (MVVM) 架構模式的完整指南。

## 何時使用此技能

以下情境應觸發此技能：
- 在 Android 應用程式中實作 MVVM 架構
- 使用 ViewModel、LiveData、StateFlow 或 Flow
- 設定 ViewBinding 或 DataBinding
- 為資料層實作 Repository 模式
- 管理 UI 狀態及處理設定變更
- 建立可測試、易維護的 Android 應用程式
- 分離 UI 與業務邏輯的關注點

## MVVM 核心概念

### 架構概觀

MVVM 將應用程式分成三個獨立層次：

```
┌─────────────────────────────────────────────────────────────┐
│                         VIEW                                 │
│  (Activity/Fragment/Composable)                             │
│  - Observes ViewModel state                                  │
│  - Sends user actions to ViewModel                          │
│  - No business logic                                         │
└──────────────────────┬──────────────────────────────────────┘
                       │ Observes (LiveData/StateFlow)
                       ▼
┌─────────────────────────────────────────────────────────────┐
│                      VIEWMODEL                               │
│  - Holds UI state                                            │
│  - Processes user actions                                    │
│  - Survives configuration changes                            │
│  - NO Android framework references                           │
└──────────────────────┬──────────────────────────────────────┘
                       │ Calls
                       ▼
┌─────────────────────────────────────────────────────────────┐
│                    REPOSITORY                                │
│  - Single source of truth                                    │
│  - Abstracts data sources                                    │
│  - Coordinates local/remote data                            │
└───────────┬─────────────────────────────────┬───────────────┘
            │                                 │
            ▼                                 ▼
    ┌───────────────┐                 ┌───────────────┐
    │  Local Data   │                 │  Remote Data  │
    │  (Room DB)    │                 │  (Retrofit)   │
    └───────────────┘                 └───────────────┘
```

### Model

**Model** 代表資料與業務邏輯層：

```kotlin
// Data class representing domain entity
data class User(
    val id: Long,
    val name: String,
    val email: String,
    val avatarUrl: String?
)

// Repository interface (part of domain layer)
interface UserRepository {
    suspend fun getUser(id: Long): User
    suspend fun getUsers(): List<User>
    fun observeUsers(): Flow<List<User>>
    suspend fun saveUser(user: User)
    suspend fun deleteUser(id: Long)
}
```

### View

**View** 是 UI 層（Activity、Fragment 或 Composable）：

```kotlin
@AndroidEntryPoint
class UserListFragment : Fragment(R.layout.fragment_user_list) {

    private val viewModel: UserListViewModel by viewModels()
    private var _binding: FragmentUserListBinding? = null
    private val binding get() = _binding!!

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        _binding = FragmentUserListBinding.bind(view)

        setupObservers()
        setupClickListeners()
    }

    private fun setupObservers() {
        // Observe UI state
        viewLifecycleOwner.lifecycleScope.launch {
            viewLifecycleOwner.repeatOnLifecycle(Lifecycle.State.STARTED) {
                viewModel.uiState.collect { state ->
                    updateUI(state)
                }
            }
        }
    }

    private fun updateUI(state: UserListUiState) {
        binding.progressBar.isVisible = state.isLoading
        binding.errorText.isVisible = state.error != null
        binding.errorText.text = state.error
        binding.recyclerView.isVisible = state.users.isNotEmpty()
        // Update adapter with state.users
    }

    private fun setupClickListeners() {
        binding.refreshButton.setOnClickListener {
            viewModel.onRefreshClicked()
        }
    }

    override fun onDestroyView() {
        super.onDestroyView()
        _binding = null
    }
}
```

### ViewModel

**ViewModel** 持有 UI 狀態並處理業務邏輯：

```kotlin
@HiltViewModel
class UserListViewModel @Inject constructor(
    private val userRepository: UserRepository
) : ViewModel() {

    // UI State
    private val _uiState = MutableStateFlow(UserListUiState())
    val uiState: StateFlow<UserListUiState> = _uiState.asStateFlow()

    init {
        loadUsers()
    }

    private fun loadUsers() {
        viewModelScope.launch {
            _uiState.update { it.copy(isLoading = true, error = null) }

            try {
                val users = userRepository.getUsers()
                _uiState.update {
                    it.copy(isLoading = false, users = users)
                }
            } catch (e: Exception) {
                _uiState.update {
                    it.copy(isLoading = false, error = e.message)
                }
            }
        }
    }

    fun onRefreshClicked() {
        loadUsers()
    }

    fun onUserClicked(userId: Long) {
        // Handle navigation or other actions
    }
}

// UI State data class
data class UserListUiState(
    val isLoading: Boolean = false,
    val users: List<User> = emptyList(),
    val error: String? = null
)
```

## 資料綁定機制

### 單向資料綁定

資料從 ViewModel 流向 View：

```kotlin
// ViewModel exposes state
val userName: StateFlow<String> = _userName.asStateFlow()

// View observes and updates UI
viewModel.userName.collect { name ->
    binding.nameTextView.text = name
}
```

### 雙向資料綁定（使用 DataBinding 函式庫）

```xml
<!-- layout/fragment_edit_user.xml -->
<layout xmlns:android="http://schemas.android.com/apk/res/android">
    <data>
        <variable
            name="viewModel"
            type="com.example.EditUserViewModel" />
    </data>

    <EditText
        android:text="@={viewModel.userName}"
        android:hint="Enter name" />
</layout>
```

```kotlin
// ViewModel with two-way binding
class EditUserViewModel : ViewModel() {
    val userName = MutableLiveData<String>()
}
```

## 快速參考

### ViewModel 建立模式

**模式一：使用 Hilt 依賴注入的 ViewModel**
```kotlin
@HiltViewModel
class MyViewModel @Inject constructor(
    private val repository: MyRepository,
    private val savedStateHandle: SavedStateHandle
) : ViewModel()

// In Fragment
private val viewModel: MyViewModel by viewModels()
```

**模式二：使用 Factory 的 ViewModel（不使用 Hilt）**
```kotlin
class MyViewModelFactory(
    private val repository: MyRepository
) : ViewModelProvider.Factory {
    override fun <T : ViewModel> create(modelClass: Class<T>): T {
        return MyViewModel(repository) as T
    }
}

// In Fragment
private val viewModel: MyViewModel by viewModels {
    MyViewModelFactory(repository)
}
```

**模式三：Fragment 間共享 ViewModel**
```kotlin
// Both fragments share the same ViewModel scoped to Activity
private val sharedViewModel: SharedViewModel by activityViewModels()
```

### StateFlow 與 LiveData 比較

| 功能 | StateFlow | LiveData |
|---------|-----------|----------|
| 初始值 | 必填 | 選填 |
| Null 安全 | 可設定 | 永遠可為 null |
| 生命週期感知 | 需手動配合 repeatOnLifecycle | 自動 |
| 運算子 | 完整 Flow 運算子 | 有限（Transformations） |
| 測試 | 較容易（suspend functions） | 需要特殊規則 |

**StateFlow 模式（推薦）**
```kotlin
private val _uiState = MutableStateFlow(UiState())
val uiState: StateFlow<UiState> = _uiState.asStateFlow()

// Update state
_uiState.update { currentState ->
    currentState.copy(isLoading = true)
}
```

**LiveData 模式**
```kotlin
private val _uiState = MutableLiveData<UiState>()
val uiState: LiveData<UiState> = _uiState

// Update state
_uiState.value = UiState(isLoading = true)
// or from background thread
_uiState.postValue(UiState(isLoading = true))
```

### 在 UI 中收集 Flow

```kotlin
// Safe collection pattern
viewLifecycleOwner.lifecycleScope.launch {
    viewLifecycleOwner.repeatOnLifecycle(Lifecycle.State.STARTED) {
        viewModel.uiState.collect { state ->
            // Update UI
        }
    }
}

// Multiple flows in parallel
viewLifecycleOwner.lifecycleScope.launch {
    viewLifecycleOwner.repeatOnLifecycle(Lifecycle.State.STARTED) {
        launch {
            viewModel.uiState.collect { /* ... */ }
        }
        launch {
            viewModel.events.collect { /* ... */ }
        }
    }
}
```

## 專案結構

```
app/
├── src/main/java/com/example/myapp/
│   ├── data/                           # Data Layer
│   │   ├── local/
│   │   │   ├── database/
│   │   │   │   ├── AppDatabase.kt
│   │   │   │   ├── dao/
│   │   │   │   │   └── UserDao.kt
│   │   │   │   └── entity/
│   │   │   │       └── UserEntity.kt
│   │   │   └── datastore/
│   │   │       └── UserPreferences.kt
│   │   ├── remote/
│   │   │   ├── api/
│   │   │   │   └── UserApiService.kt
│   │   │   └── dto/
│   │   │       └── UserDto.kt
│   │   └── repository/
│   │       └── UserRepositoryImpl.kt
│   │
│   ├── domain/                         # Domain Layer (optional)
│   │   ├── model/
│   │   │   └── User.kt
│   │   ├── repository/
│   │   │   └── UserRepository.kt
│   │   └── usecase/
│   │       ├── GetUserUseCase.kt
│   │       └── SaveUserUseCase.kt
│   │
│   ├── ui/                             # Presentation Layer
│   │   ├── common/
│   │   │   ├── BaseViewModel.kt
│   │   │   └── UiState.kt
│   │   └── feature/
│   │       └── userlist/
│   │           ├── UserListFragment.kt
│   │           ├── UserListViewModel.kt
│   │           ├── UserListUiState.kt
│   │           └── UserListAdapter.kt
│   │
│   └── di/                             # Dependency Injection
│       ├── AppModule.kt
│       ├── DatabaseModule.kt
│       ├── NetworkModule.kt
│       └── RepositoryModule.kt
│
└── src/main/res/
    └── layout/
        └── fragment_user_list.xml
```

## 最佳實踐

### 1. ViewModel 不應持有 View 的參考

```kotlin
// BAD - Don't do this!
class BadViewModel : ViewModel() {
    lateinit var textView: TextView  // Memory leak!
    fun updateText() {
        textView.text = "Hello"  // Crashes on config change
    }
}

// GOOD - Expose state instead
class GoodViewModel : ViewModel() {
    private val _text = MutableStateFlow("")
    val text: StateFlow<String> = _text.asStateFlow()

    fun updateText() {
        _text.value = "Hello"
    }
}
```

### 2. 使用單一 UI 狀態物件

```kotlin
// GOOD - Single state object
data class UserListUiState(
    val isLoading: Boolean = false,
    val users: List<User> = emptyList(),
    val error: String? = null,
    val selectedUserId: Long? = null
)

// Update with copy
_uiState.update { it.copy(isLoading = true) }
```

### 3. 處理一次性事件

```kotlin
// Use SharedFlow for one-time events
sealed class UiEvent {
    data class ShowSnackbar(val message: String) : UiEvent()
    data class NavigateToDetail(val userId: Long) : UiEvent()
}

class MyViewModel : ViewModel() {
    private val _events = MutableSharedFlow<UiEvent>()
    val events: SharedFlow<UiEvent> = _events.asSharedFlow()

    fun onSaveClicked() {
        viewModelScope.launch {
            // ... save logic
            _events.emit(UiEvent.ShowSnackbar("Saved!"))
        }
    }
}
```

### 4. Repository 模式實作

```kotlin
class UserRepositoryImpl @Inject constructor(
    private val userDao: UserDao,
    private val userApi: UserApiService,
    private val dispatcher: CoroutineDispatcher = Dispatchers.IO
) : UserRepository {

    override fun observeUsers(): Flow<List<User>> {
        return userDao.observeAll()
            .map { entities -> entities.map { it.toDomain() } }
            .flowOn(dispatcher)
    }

    override suspend fun refreshUsers() {
        withContext(dispatcher) {
            val remoteUsers = userApi.getUsers()
            userDao.insertAll(remoteUsers.map { it.toEntity() })
        }
    }
}
```

### 5. 錯誤處理

```kotlin
sealed class Result<out T> {
    data class Success<T>(val data: T) : Result<T>()
    data class Error(val exception: Throwable) : Result<Nothing>()
    object Loading : Result<Nothing>()
}

// In ViewModel
fun loadData() {
    viewModelScope.launch {
        _uiState.update { it.copy(isLoading = true) }

        when (val result = repository.getData()) {
            is Result.Success -> {
                _uiState.update {
                    it.copy(isLoading = false, data = result.data)
                }
            }
            is Result.Error -> {
                _uiState.update {
                    it.copy(isLoading = false, error = result.exception.message)
                }
            }
            Result.Loading -> { /* Already handled */ }
        }
    }
}
```

## 常見錯誤與解決方案

### 錯誤一：未具備生命週期感知地收集 Flow

```kotlin
// BAD - Continues collecting even when app is in background
lifecycleScope.launch {
    viewModel.uiState.collect { state -> updateUI(state) }
}

// GOOD - Stops collecting when lifecycle is below STARTED
viewLifecycleOwner.lifecycleScope.launch {
    viewLifecycleOwner.repeatOnLifecycle(Lifecycle.State.STARTED) {
        viewModel.uiState.collect { state -> updateUI(state) }
    }
}
```

### 錯誤二：在初始化時使用 viewModelScope.launch

```kotlin
// BAD - Multiple observers trigger multiple loads
class BadViewModel(private val repo: Repository) : ViewModel() {
    val users = liveData {
        emit(repo.getUsers())
    }
}

// GOOD - Load once on init
class GoodViewModel(private val repo: Repository) : ViewModel() {
    private val _users = MutableStateFlow<List<User>>(emptyList())
    val users: StateFlow<List<User>> = _users.asStateFlow()

    init {
        viewModelScope.launch {
            _users.value = repo.getUsers()
        }
    }
}
```

### 錯誤三：未清除 Binding 參考

```kotlin
// BAD - Memory leak
class BadFragment : Fragment() {
    private lateinit var binding: FragmentBinding
    // binding holds reference even after view is destroyed
}

// GOOD - Clear binding in onDestroyView
class GoodFragment : Fragment() {
    private var _binding: FragmentBinding? = null
    private val binding get() = _binding!!

    override fun onDestroyView() {
        super.onDestroyView()
        _binding = null
    }
}
```

## 參考文件

此技能在 `references/` 目錄中包含完整文件：

- **core_concepts.md** - 詳細的 MVVM 理論與原則
- **viewmodel.md** - ViewModel 生命週期、SavedStateHandle、範圍
- **livedata_flow.md** - LiveData、StateFlow、SharedFlow 模式
- **data_binding.md** - ViewBinding 和 DataBinding 設定與使用
- **repository.md** - Repository 模式與資料層實作
- **best_practices.md** - 常見模式與反模式
- **advanced_topics.md** - 多模組 MVVM、複雜狀態處理
- **code_examples.md** - 常見場景的完整程式碼範例

需要詳細資訊時，請使用 `view` 讀取特定參考文件。

## 必要依賴

```kotlin
// build.gradle.kts (app level)
dependencies {
    // ViewModel
    implementation("androidx.lifecycle:lifecycle-viewmodel-ktx:2.7.0")

    // LiveData (if using)
    implementation("androidx.lifecycle:lifecycle-livedata-ktx:2.7.0")

    // Lifecycle runtime for repeatOnLifecycle
    implementation("androidx.lifecycle:lifecycle-runtime-ktx:2.7.0")

    // ViewBinding (enable in android block)
    // DataBinding (enable in android block)

    // Coroutines
    implementation("org.jetbrains.kotlinx:kotlinx-coroutines-android:1.8.0")

    // Hilt for DI
    implementation("com.google.dagger:hilt-android:2.50")
    kapt("com.google.dagger:hilt-compiler:2.50")

    // Room for local database
    implementation("androidx.room:room-runtime:2.6.1")
    implementation("androidx.room:room-ktx:2.6.1")
    kapt("androidx.room:room-compiler:2.6.1")

    // Retrofit for network
    implementation("com.squareup.retrofit2:retrofit:2.9.0")
    implementation("com.squareup.retrofit2:converter-gson:2.9.0")
}

android {
    buildFeatures {
        viewBinding = true
        // dataBinding = true  // if using DataBinding
    }
}
```

## 備註

- 本技能為提供完整 MVVM 學習路徑而生成
- 範例使用 Kotlin 與現代 Jetpack 函式庫（2024 年最佳實踐）
- 新專案推薦使用 StateFlow 而非 LiveData
- 參考文件包含詳細實作與解說

## 資源

- [Official Android Architecture Guide](https://developer.android.com/topic/architecture)
- [ViewModel Documentation](https://developer.android.com/topic/libraries/architecture/viewmodel)
- [StateFlow and SharedFlow](https://developer.android.com/kotlin/flow/stateflow-and-sharedflow)
- [Guide to App Architecture](https://developer.android.com/topic/architecture/intro)
