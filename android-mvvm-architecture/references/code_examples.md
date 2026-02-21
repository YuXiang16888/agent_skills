# 完整 MVVM 程式碼範例

## 目錄
1. [含網路請求的使用者列表](#user-list-with-network-requests)
2. [表單處理](#form-handling)
3. [主從式導航](#master-detail-navigation)
4. [含快取的下拉重新整理](#pull-to-refresh-with-caching)
5. [登入流程](#login-flow)
6. [設定畫面](#settings-screen)

---

## 含網路請求的使用者列表

### 完整實作

#### 領域層

```kotlin
// domain/model/User.kt
data class User(
    val id: Long,
    val name: String,
    val email: String,
    val avatarUrl: String?,
    val isActive: Boolean
)

// domain/repository/UserRepository.kt
interface UserRepository {
    fun observeUsers(): Flow<List<User>>
    suspend fun refreshUsers()
    suspend fun deleteUser(id: Long)
}
```

#### 資料層

```kotlin
// data/local/entity/UserEntity.kt
@Entity(tableName = "users")
data class UserEntity(
    @PrimaryKey val id: Long,
    val name: String,
    val email: String,
    @ColumnInfo(name = "avatar_url") val avatarUrl: String?,
    @ColumnInfo(name = "is_active") val isActive: Boolean
)

// data/local/dao/UserDao.kt
@Dao
interface UserDao {
    @Query("SELECT * FROM users ORDER BY name ASC")
    fun observeAll(): Flow<List<UserEntity>>

    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun insertAll(users: List<UserEntity>)

    @Query("DELETE FROM users WHERE id = :id")
    suspend fun deleteById(id: Long)

    @Query("DELETE FROM users")
    suspend fun deleteAll()
}

// data/remote/dto/UserDto.kt
data class UserDto(
    @SerializedName("id") val id: Long,
    @SerializedName("name") val name: String,
    @SerializedName("email") val email: String,
    @SerializedName("avatar") val avatarUrl: String?,
    @SerializedName("active") val isActive: Boolean
)

// data/remote/api/UserApiService.kt
interface UserApiService {
    @GET("users")
    suspend fun getUsers(): List<UserDto>

    @DELETE("users/{id}")
    suspend fun deleteUser(@Path("id") id: Long)
}

// data/mapper/UserMapper.kt
fun UserDto.toEntity(): UserEntity = UserEntity(
    id = id,
    name = name,
    email = email,
    avatarUrl = avatarUrl,
    isActive = isActive
)

fun UserEntity.toDomain(): User = User(
    id = id,
    name = name,
    email = email,
    avatarUrl = avatarUrl,
    isActive = isActive
)

// data/repository/UserRepositoryImpl.kt
class UserRepositoryImpl @Inject constructor(
    private val userDao: UserDao,
    private val userApi: UserApiService,
    @IoDispatcher private val ioDispatcher: CoroutineDispatcher
) : UserRepository {

    override fun observeUsers(): Flow<List<User>> {
        return userDao.observeAll()
            .map { entities -> entities.map { it.toDomain() } }
            .flowOn(ioDispatcher)
    }

    override suspend fun refreshUsers() {
        withContext(ioDispatcher) {
            val remoteUsers = userApi.getUsers()
            userDao.insertAll(remoteUsers.map { it.toEntity() })
        }
    }

    override suspend fun deleteUser(id: Long) {
        withContext(ioDispatcher) {
            userApi.deleteUser(id)
            userDao.deleteById(id)
        }
    }
}
```

#### 呈現層

```kotlin
// ui/userlist/UserListUiState.kt
data class UserListUiState(
    val users: List<User> = emptyList(),
    val isLoading: Boolean = true,
    val isRefreshing: Boolean = false,
    val error: String? = null
) {
    val showContent: Boolean get() = !isLoading && error == null
    val showEmpty: Boolean get() = showContent && users.isEmpty()
}

// ui/userlist/UserListEvent.kt
sealed class UserListEvent {
    data class ShowSnackbar(val message: String) : UserListEvent()
    data class NavigateToDetail(val userId: Long) : UserListEvent()
    data class ShowDeleteConfirmation(val user: User) : UserListEvent()
}

// ui/userlist/UserListViewModel.kt
@HiltViewModel
class UserListViewModel @Inject constructor(
    private val userRepository: UserRepository
) : ViewModel() {

    private val _uiState = MutableStateFlow(UserListUiState())
    val uiState: StateFlow<UserListUiState> = _uiState.asStateFlow()

    private val _events = MutableSharedFlow<UserListEvent>()
    val events: SharedFlow<UserListEvent> = _events.asSharedFlow()

    init {
        observeUsers()
        refresh()
    }

    private fun observeUsers() {
        viewModelScope.launch {
            userRepository.observeUsers()
                .catch { error ->
                    _uiState.update { it.copy(error = error.message) }
                }
                .collect { users ->
                    _uiState.update { it.copy(users = users, isLoading = false) }
                }
        }
    }

    fun refresh() {
        viewModelScope.launch {
            _uiState.update { it.copy(isRefreshing = true, error = null) }

            try {
                userRepository.refreshUsers()
            } catch (e: Exception) {
                _uiState.update { it.copy(error = e.message) }
            } finally {
                _uiState.update { it.copy(isRefreshing = false) }
            }
        }
    }

    fun onUserClicked(user: User) {
        viewModelScope.launch {
            _events.emit(UserListEvent.NavigateToDetail(user.id))
        }
    }

    fun onUserLongClicked(user: User) {
        viewModelScope.launch {
            _events.emit(UserListEvent.ShowDeleteConfirmation(user))
        }
    }

    fun deleteUser(user: User) {
        viewModelScope.launch {
            try {
                userRepository.deleteUser(user.id)
                _events.emit(UserListEvent.ShowSnackbar("${user.name} deleted"))
            } catch (e: Exception) {
                _events.emit(UserListEvent.ShowSnackbar("Failed to delete user"))
            }
        }
    }

    fun retryLoading() {
        _uiState.update { it.copy(error = null, isLoading = true) }
        refresh()
    }
}

// ui/userlist/UserListFragment.kt
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
            setHasFixedSize(true)
        }
    }

    private fun setupSwipeRefresh() {
        binding.swipeRefresh.setOnRefreshListener {
            viewModel.refresh()
        }
    }

    private fun setupObservers() {
        viewLifecycleOwner.lifecycleScope.launch {
            viewLifecycleOwner.repeatOnLifecycle(Lifecycle.State.STARTED) {
                launch { collectUiState() }
                launch { collectEvents() }
            }
        }
    }

    private suspend fun collectUiState() {
        viewModel.uiState.collect { state ->
            binding.apply {
                swipeRefresh.isRefreshing = state.isRefreshing
                progressBar.isVisible = state.isLoading
                errorGroup.isVisible = state.error != null
                errorText.text = state.error
                emptyView.isVisible = state.showEmpty
                recyclerView.isVisible = state.showContent && state.users.isNotEmpty()
            }

            adapter.submitList(state.users)
        }
    }

    private suspend fun collectEvents() {
        viewModel.events.collect { event ->
            when (event) {
                is UserListEvent.ShowSnackbar -> {
                    Snackbar.make(binding.root, event.message, Snackbar.LENGTH_SHORT).show()
                }
                is UserListEvent.NavigateToDetail -> {
                    findNavController().navigate(
                        UserListFragmentDirections.actionToUserDetail(event.userId)
                    )
                }
                is UserListEvent.ShowDeleteConfirmation -> {
                    showDeleteDialog(event.user)
                }
            }
        }
    }

    private fun showDeleteDialog(user: User) {
        MaterialAlertDialogBuilder(requireContext())
            .setTitle("Delete User")
            .setMessage("Are you sure you want to delete ${user.name}?")
            .setPositiveButton("Delete") { _, _ ->
                viewModel.deleteUser(user)
            }
            .setNegativeButton("Cancel", null)
            .show()
    }

    override fun onDestroyView() {
        super.onDestroyView()
        _binding = null
    }
}

// ui/userlist/UserListAdapter.kt
class UserListAdapter(
    private val onUserClick: (User) -> Unit,
    private val onUserLongClick: (User) -> Unit
) : ListAdapter<User, UserListAdapter.UserViewHolder>(UserDiffCallback()) {

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): UserViewHolder {
        val binding = ItemUserBinding.inflate(
            LayoutInflater.from(parent.context),
            parent,
            false
        )
        return UserViewHolder(binding)
    }

    override fun onBindViewHolder(holder: UserViewHolder, position: Int) {
        holder.bind(getItem(position))
    }

    inner class UserViewHolder(
        private val binding: ItemUserBinding
    ) : RecyclerView.ViewHolder(binding.root) {

        init {
            binding.root.setOnClickListener {
                val position = bindingAdapterPosition
                if (position != RecyclerView.NO_POSITION) {
                    onUserClick(getItem(position))
                }
            }

            binding.root.setOnLongClickListener {
                val position = bindingAdapterPosition
                if (position != RecyclerView.NO_POSITION) {
                    onUserLongClick(getItem(position))
                    true
                } else {
                    false
                }
            }
        }

        fun bind(user: User) {
            binding.apply {
                nameText.text = user.name
                emailText.text = user.email
                statusIndicator.isVisible = user.isActive

                avatarImage.load(user.avatarUrl) {
                    placeholder(R.drawable.ic_avatar_placeholder)
                    error(R.drawable.ic_avatar_placeholder)
                    transformations(CircleCropTransformation())
                }
            }
        }
    }

    class UserDiffCallback : DiffUtil.ItemCallback<User>() {
        override fun areItemsTheSame(oldItem: User, newItem: User): Boolean {
            return oldItem.id == newItem.id
        }

        override fun areContentsTheSame(oldItem: User, newItem: User): Boolean {
            return oldItem == newItem
        }
    }
}
```

---

## 表單處理

### 註冊表單

```kotlin
// ui/registration/RegistrationFormState.kt
data class RegistrationFormState(
    val email: String = "",
    val emailError: String? = null,
    val password: String = "",
    val passwordError: String? = null,
    val confirmPassword: String = "",
    val confirmPasswordError: String? = null,
    val acceptTerms: Boolean = false,
    val acceptTermsError: String? = null,
    val isSubmitting: Boolean = false
) {
    val isValid: Boolean
        get() = email.isNotBlank() &&
                emailError == null &&
                password.isNotBlank() &&
                passwordError == null &&
                confirmPassword.isNotBlank() &&
                confirmPasswordError == null &&
                acceptTerms &&
                acceptTermsError == null

    val canSubmit: Boolean
        get() = isValid && !isSubmitting
}

// ui/registration/RegistrationViewModel.kt
@HiltViewModel
class RegistrationViewModel @Inject constructor(
    private val authRepository: AuthRepository,
    private val validators: FormValidators
) : ViewModel() {

    private val _formState = MutableStateFlow(RegistrationFormState())
    val formState: StateFlow<RegistrationFormState> = _formState.asStateFlow()

    private val _events = MutableSharedFlow<RegistrationEvent>()
    val events: SharedFlow<RegistrationEvent> = _events.asSharedFlow()

    fun updateEmail(email: String) {
        _formState.update { state ->
            state.copy(
                email = email,
                emailError = validators.validateEmail(email)
            )
        }
    }

    fun updatePassword(password: String) {
        val currentState = _formState.value
        _formState.update { state ->
            state.copy(
                password = password,
                passwordError = validators.validatePassword(password),
                confirmPasswordError = if (currentState.confirmPassword.isNotEmpty()) {
                    validators.validatePasswordMatch(currentState.confirmPassword, password)
                } else null
            )
        }
    }

    fun updateConfirmPassword(confirmPassword: String) {
        _formState.update { state ->
            state.copy(
                confirmPassword = confirmPassword,
                confirmPasswordError = validators.validatePasswordMatch(
                    confirmPassword,
                    state.password
                )
            )
        }
    }

    fun updateAcceptTerms(accept: Boolean) {
        _formState.update { state ->
            state.copy(
                acceptTerms = accept,
                acceptTermsError = if (!accept) "You must accept terms" else null
            )
        }
    }

    fun submit() {
        val state = _formState.value

        // Final validation
        val emailError = validators.validateEmail(state.email)
        val passwordError = validators.validatePassword(state.password)
        val confirmError = validators.validatePasswordMatch(state.confirmPassword, state.password)
        val termsError = if (!state.acceptTerms) "You must accept terms" else null

        if (emailError != null || passwordError != null || confirmError != null || termsError != null) {
            _formState.update {
                it.copy(
                    emailError = emailError,
                    passwordError = passwordError,
                    confirmPasswordError = confirmError,
                    acceptTermsError = termsError
                )
            }
            return
        }

        viewModelScope.launch {
            _formState.update { it.copy(isSubmitting = true) }

            try {
                authRepository.register(state.email, state.password)
                _events.emit(RegistrationEvent.Success)
            } catch (e: Exception) {
                _events.emit(RegistrationEvent.Error(e.message ?: "Registration failed"))
            } finally {
                _formState.update { it.copy(isSubmitting = false) }
            }
        }
    }
}

sealed class RegistrationEvent {
    object Success : RegistrationEvent()
    data class Error(val message: String) : RegistrationEvent()
}

// utils/FormValidators.kt
class FormValidators @Inject constructor() {

    fun validateEmail(email: String): String? {
        return when {
            email.isBlank() -> "Email is required"
            !Patterns.EMAIL_ADDRESS.matcher(email).matches() -> "Invalid email format"
            else -> null
        }
    }

    fun validatePassword(password: String): String? {
        return when {
            password.isBlank() -> "Password is required"
            password.length < 8 -> "Minimum 8 characters required"
            !password.any { it.isUpperCase() } -> "Must contain uppercase letter"
            !password.any { it.isLowerCase() } -> "Must contain lowercase letter"
            !password.any { it.isDigit() } -> "Must contain a number"
            else -> null
        }
    }

    fun validatePasswordMatch(confirm: String, password: String): String? {
        return when {
            confirm.isBlank() -> "Please confirm password"
            confirm != password -> "Passwords don't match"
            else -> null
        }
    }
}

// ui/registration/RegistrationFragment.kt
@AndroidEntryPoint
class RegistrationFragment : Fragment(R.layout.fragment_registration) {

    private val viewModel: RegistrationViewModel by viewModels()
    private var _binding: FragmentRegistrationBinding? = null
    private val binding get() = _binding!!

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        _binding = FragmentRegistrationBinding.bind(view)

        setupInputListeners()
        setupObservers()
    }

    private fun setupInputListeners() {
        binding.apply {
            emailInput.doAfterTextChanged {
                viewModel.updateEmail(it.toString())
            }

            passwordInput.doAfterTextChanged {
                viewModel.updatePassword(it.toString())
            }

            confirmPasswordInput.doAfterTextChanged {
                viewModel.updateConfirmPassword(it.toString())
            }

            termsCheckbox.setOnCheckedChangeListener { _, isChecked ->
                viewModel.updateAcceptTerms(isChecked)
            }

            submitButton.setOnClickListener {
                viewModel.submit()
            }
        }
    }

    private fun setupObservers() {
        viewLifecycleOwner.lifecycleScope.launch {
            viewLifecycleOwner.repeatOnLifecycle(Lifecycle.State.STARTED) {
                launch {
                    viewModel.formState.collect { state ->
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

    private fun updateUI(state: RegistrationFormState) {
        binding.apply {
            emailLayout.error = state.emailError
            passwordLayout.error = state.passwordError
            confirmPasswordLayout.error = state.confirmPasswordError
            termsError.apply {
                text = state.acceptTermsError
                isVisible = state.acceptTermsError != null
            }

            submitButton.isEnabled = state.canSubmit
            progressBar.isVisible = state.isSubmitting
        }
    }

    private fun handleEvent(event: RegistrationEvent) {
        when (event) {
            RegistrationEvent.Success -> {
                Toast.makeText(requireContext(), "Registration successful!", Toast.LENGTH_SHORT).show()
                findNavController().navigate(R.id.action_to_login)
            }
            is RegistrationEvent.Error -> {
                Snackbar.make(binding.root, event.message, Snackbar.LENGTH_LONG).show()
            }
        }
    }

    override fun onDestroyView() {
        super.onDestroyView()
        _binding = null
    }
}
```

---

## 登入流程

### 完整登入實作

```kotlin
// ui/login/LoginUiState.kt
data class LoginUiState(
    val email: String = "",
    val password: String = "",
    val emailError: String? = null,
    val passwordError: String? = null,
    val isLoading: Boolean = false,
    val isPasswordVisible: Boolean = false
) {
    val canSubmit: Boolean
        get() = email.isNotBlank() &&
                password.isNotBlank() &&
                emailError == null &&
                passwordError == null &&
                !isLoading
}

// ui/login/LoginViewModel.kt
@HiltViewModel
class LoginViewModel @Inject constructor(
    private val authRepository: AuthRepository,
    private val sessionManager: SessionManager
) : ViewModel() {

    private val _uiState = MutableStateFlow(LoginUiState())
    val uiState: StateFlow<LoginUiState> = _uiState.asStateFlow()

    private val _events = MutableSharedFlow<LoginEvent>()
    val events: SharedFlow<LoginEvent> = _events.asSharedFlow()

    fun updateEmail(email: String) {
        _uiState.update { state ->
            state.copy(
                email = email,
                emailError = null  // Clear error on change
            )
        }
    }

    fun updatePassword(password: String) {
        _uiState.update { state ->
            state.copy(
                password = password,
                passwordError = null
            )
        }
    }

    fun togglePasswordVisibility() {
        _uiState.update { state ->
            state.copy(isPasswordVisible = !state.isPasswordVisible)
        }
    }

    fun login() {
        val state = _uiState.value

        // Validate
        if (state.email.isBlank()) {
            _uiState.update { it.copy(emailError = "Email is required") }
            return
        }
        if (state.password.isBlank()) {
            _uiState.update { it.copy(passwordError = "Password is required") }
            return
        }

        viewModelScope.launch {
            _uiState.update { it.copy(isLoading = true) }

            try {
                val authResult = authRepository.login(state.email, state.password)
                sessionManager.saveSession(authResult)
                _events.emit(LoginEvent.NavigateToHome)
            } catch (e: InvalidCredentialsException) {
                _uiState.update {
                    it.copy(
                        isLoading = false,
                        passwordError = "Invalid email or password"
                    )
                }
            } catch (e: NetworkException) {
                _events.emit(LoginEvent.ShowError("No internet connection"))
                _uiState.update { it.copy(isLoading = false) }
            } catch (e: Exception) {
                _events.emit(LoginEvent.ShowError("Login failed: ${e.message}"))
                _uiState.update { it.copy(isLoading = false) }
            }
        }
    }

    fun onForgotPasswordClicked() {
        viewModelScope.launch {
            _events.emit(LoginEvent.NavigateToForgotPassword)
        }
    }

    fun onSignUpClicked() {
        viewModelScope.launch {
            _events.emit(LoginEvent.NavigateToSignUp)
        }
    }
}

sealed class LoginEvent {
    object NavigateToHome : LoginEvent()
    object NavigateToForgotPassword : LoginEvent()
    object NavigateToSignUp : LoginEvent()
    data class ShowError(val message: String) : LoginEvent()
}

// ui/login/LoginFragment.kt
@AndroidEntryPoint
class LoginFragment : Fragment(R.layout.fragment_login) {

    private val viewModel: LoginViewModel by viewModels()
    private var _binding: FragmentLoginBinding? = null
    private val binding get() = _binding!!

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        _binding = FragmentLoginBinding.bind(view)

        setupInputs()
        setupClickListeners()
        setupObservers()
    }

    private fun setupInputs() {
        binding.apply {
            emailInput.doAfterTextChanged {
                viewModel.updateEmail(it.toString())
            }

            passwordInput.doAfterTextChanged {
                viewModel.updatePassword(it.toString())
            }

            // Handle Enter key on password field
            passwordInput.setOnEditorActionListener { _, actionId, _ ->
                if (actionId == EditorInfo.IME_ACTION_DONE) {
                    viewModel.login()
                    true
                } else {
                    false
                }
            }
        }
    }

    private fun setupClickListeners() {
        binding.apply {
            loginButton.setOnClickListener {
                hideKeyboard()
                viewModel.login()
            }

            togglePasswordButton.setOnClickListener {
                viewModel.togglePasswordVisibility()
            }

            forgotPasswordText.setOnClickListener {
                viewModel.onForgotPasswordClicked()
            }

            signUpText.setOnClickListener {
                viewModel.onSignUpClicked()
            }
        }
    }

    private fun setupObservers() {
        viewLifecycleOwner.lifecycleScope.launch {
            viewLifecycleOwner.repeatOnLifecycle(Lifecycle.State.STARTED) {
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

    private fun updateUI(state: LoginUiState) {
        binding.apply {
            emailLayout.error = state.emailError
            passwordLayout.error = state.passwordError
            loginButton.isEnabled = state.canSubmit
            progressBar.isVisible = state.isLoading

            // Toggle password visibility
            passwordInput.inputType = if (state.isPasswordVisible) {
                InputType.TYPE_TEXT_VARIATION_VISIBLE_PASSWORD
            } else {
                InputType.TYPE_CLASS_TEXT or InputType.TYPE_TEXT_VARIATION_PASSWORD
            }
            passwordInput.setSelection(passwordInput.text?.length ?: 0)

            togglePasswordButton.setImageResource(
                if (state.isPasswordVisible) R.drawable.ic_visibility_off
                else R.drawable.ic_visibility
            )
        }
    }

    private fun handleEvent(event: LoginEvent) {
        when (event) {
            LoginEvent.NavigateToHome -> {
                findNavController().navigate(R.id.action_login_to_home)
            }
            LoginEvent.NavigateToForgotPassword -> {
                findNavController().navigate(R.id.action_login_to_forgot_password)
            }
            LoginEvent.NavigateToSignUp -> {
                findNavController().navigate(R.id.action_login_to_signup)
            }
            is LoginEvent.ShowError -> {
                Snackbar.make(binding.root, event.message, Snackbar.LENGTH_LONG).show()
            }
        }
    }

    private fun hideKeyboard() {
        val imm = requireContext().getSystemService(Context.INPUT_METHOD_SERVICE) as InputMethodManager
        imm.hideSoftInputFromWindow(binding.root.windowToken, 0)
    }

    override fun onDestroyView() {
        super.onDestroyView()
        _binding = null
    }
}
```

---

## 設定畫面

### 使用 DataStore 的偏好設定

```kotlin
// data/preferences/UserPreferences.kt
data class UserPreferences(
    val darkMode: Boolean = false,
    val notificationsEnabled: Boolean = true,
    val language: String = "en",
    val fontSize: FontSize = FontSize.MEDIUM
)

enum class FontSize { SMALL, MEDIUM, LARGE }

// data/preferences/PreferencesManager.kt
class PreferencesManager @Inject constructor(
    private val dataStore: DataStore<Preferences>
) {
    companion object {
        private val DARK_MODE = booleanPreferencesKey("dark_mode")
        private val NOTIFICATIONS = booleanPreferencesKey("notifications")
        private val LANGUAGE = stringPreferencesKey("language")
        private val FONT_SIZE = stringPreferencesKey("font_size")
    }

    val userPreferences: Flow<UserPreferences> = dataStore.data
        .catch { e ->
            if (e is IOException) {
                emit(emptyPreferences())
            } else {
                throw e
            }
        }
        .map { prefs ->
            UserPreferences(
                darkMode = prefs[DARK_MODE] ?: false,
                notificationsEnabled = prefs[NOTIFICATIONS] ?: true,
                language = prefs[LANGUAGE] ?: "en",
                fontSize = FontSize.valueOf(prefs[FONT_SIZE] ?: FontSize.MEDIUM.name)
            )
        }

    suspend fun setDarkMode(enabled: Boolean) {
        dataStore.edit { prefs ->
            prefs[DARK_MODE] = enabled
        }
    }

    suspend fun setNotificationsEnabled(enabled: Boolean) {
        dataStore.edit { prefs ->
            prefs[NOTIFICATIONS] = enabled
        }
    }

    suspend fun setLanguage(language: String) {
        dataStore.edit { prefs ->
            prefs[LANGUAGE] = language
        }
    }

    suspend fun setFontSize(size: FontSize) {
        dataStore.edit { prefs ->
            prefs[FONT_SIZE] = size.name
        }
    }
}

// ui/settings/SettingsUiState.kt
data class SettingsUiState(
    val darkMode: Boolean = false,
    val notificationsEnabled: Boolean = true,
    val language: String = "en",
    val fontSize: FontSize = FontSize.MEDIUM,
    val isLoading: Boolean = true
)

// ui/settings/SettingsViewModel.kt
@HiltViewModel
class SettingsViewModel @Inject constructor(
    private val preferencesManager: PreferencesManager,
    private val authRepository: AuthRepository
) : ViewModel() {

    private val _uiState = MutableStateFlow(SettingsUiState())
    val uiState: StateFlow<SettingsUiState> = _uiState.asStateFlow()

    private val _events = MutableSharedFlow<SettingsEvent>()
    val events: SharedFlow<SettingsEvent> = _events.asSharedFlow()

    init {
        observePreferences()
    }

    private fun observePreferences() {
        viewModelScope.launch {
            preferencesManager.userPreferences.collect { prefs ->
                _uiState.update {
                    it.copy(
                        darkMode = prefs.darkMode,
                        notificationsEnabled = prefs.notificationsEnabled,
                        language = prefs.language,
                        fontSize = prefs.fontSize,
                        isLoading = false
                    )
                }
            }
        }
    }

    fun setDarkMode(enabled: Boolean) {
        viewModelScope.launch {
            preferencesManager.setDarkMode(enabled)
        }
    }

    fun setNotificationsEnabled(enabled: Boolean) {
        viewModelScope.launch {
            preferencesManager.setNotificationsEnabled(enabled)
        }
    }

    fun setLanguage(language: String) {
        viewModelScope.launch {
            preferencesManager.setLanguage(language)
            _events.emit(SettingsEvent.RestartRequired)
        }
    }

    fun setFontSize(size: FontSize) {
        viewModelScope.launch {
            preferencesManager.setFontSize(size)
        }
    }

    fun onLogoutClicked() {
        viewModelScope.launch {
            _events.emit(SettingsEvent.ShowLogoutConfirmation)
        }
    }

    fun logout() {
        viewModelScope.launch {
            authRepository.logout()
            _events.emit(SettingsEvent.NavigateToLogin)
        }
    }
}

sealed class SettingsEvent {
    object ShowLogoutConfirmation : SettingsEvent()
    object NavigateToLogin : SettingsEvent()
    object RestartRequired : SettingsEvent()
}

// ui/settings/SettingsFragment.kt
@AndroidEntryPoint
class SettingsFragment : Fragment(R.layout.fragment_settings) {

    private val viewModel: SettingsViewModel by viewModels()
    private var _binding: FragmentSettingsBinding? = null
    private val binding get() = _binding!!

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        _binding = FragmentSettingsBinding.bind(view)

        setupListeners()
        setupObservers()
    }

    private fun setupListeners() {
        binding.apply {
            darkModeSwitch.setOnCheckedChangeListener { _, isChecked ->
                viewModel.setDarkMode(isChecked)
            }

            notificationsSwitch.setOnCheckedChangeListener { _, isChecked ->
                viewModel.setNotificationsEnabled(isChecked)
            }

            languageItem.setOnClickListener {
                showLanguageDialog()
            }

            fontSizeItem.setOnClickListener {
                showFontSizeDialog()
            }

            logoutButton.setOnClickListener {
                viewModel.onLogoutClicked()
            }
        }
    }

    private fun setupObservers() {
        viewLifecycleOwner.lifecycleScope.launch {
            viewLifecycleOwner.repeatOnLifecycle(Lifecycle.State.STARTED) {
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

    private fun updateUI(state: SettingsUiState) {
        binding.apply {
            progressBar.isVisible = state.isLoading
            settingsContent.isVisible = !state.isLoading

            darkModeSwitch.isChecked = state.darkMode
            notificationsSwitch.isChecked = state.notificationsEnabled
            languageValue.text = getLanguageDisplayName(state.language)
            fontSizeValue.text = state.fontSize.name
        }
    }

    private fun handleEvent(event: SettingsEvent) {
        when (event) {
            SettingsEvent.ShowLogoutConfirmation -> {
                showLogoutDialog()
            }
            SettingsEvent.NavigateToLogin -> {
                // Clear back stack and navigate to login
                findNavController().navigate(
                    R.id.action_settings_to_login,
                    null,
                    NavOptions.Builder()
                        .setPopUpTo(R.id.nav_graph, true)
                        .build()
                )
            }
            SettingsEvent.RestartRequired -> {
                Snackbar.make(binding.root, "Restart app to apply changes", Snackbar.LENGTH_LONG)
                    .setAction("Restart") {
                        requireActivity().recreate()
                    }
                    .show()
            }
        }
    }

    private fun showLanguageDialog() {
        val languages = arrayOf("English", "Spanish", "French", "German")
        val codes = arrayOf("en", "es", "fr", "de")

        MaterialAlertDialogBuilder(requireContext())
            .setTitle("Select Language")
            .setItems(languages) { _, which ->
                viewModel.setLanguage(codes[which])
            }
            .show()
    }

    private fun showFontSizeDialog() {
        val sizes = FontSize.values()

        MaterialAlertDialogBuilder(requireContext())
            .setTitle("Select Font Size")
            .setItems(sizes.map { it.name }.toTypedArray()) { _, which ->
                viewModel.setFontSize(sizes[which])
            }
            .show()
    }

    private fun showLogoutDialog() {
        MaterialAlertDialogBuilder(requireContext())
            .setTitle("Logout")
            .setMessage("Are you sure you want to logout?")
            .setPositiveButton("Logout") { _, _ ->
                viewModel.logout()
            }
            .setNegativeButton("Cancel", null)
            .show()
    }

    private fun getLanguageDisplayName(code: String): String {
        return when (code) {
            "en" -> "English"
            "es" -> "Spanish"
            "fr" -> "French"
            "de" -> "German"
            else -> code
        }
    }

    override fun onDestroyView() {
        super.onDestroyView()
        _binding = null
    }
}
```

---

## 摘要

這些範例示範了常見 Android 場景的完整 MVVM 實作：

| 範例 | 關鍵概念 |
|---------|--------------|
| 使用者列表 | StateFlow、Repository 模式、離線優先、下拉重新整理 |
| 表單處理 | 即時驗證、雙向綁定、錯誤狀態 |
| 登入流程 | 驗證、Session 管理、導航 |
| 設定 | DataStore 偏好設定、主題切換、語言 |

每個範例都遵循 MVVM 最佳實踐：
- UI 狀態的單一資料來源
- 單向資料流
- 具生命週期感知的觀察
- 適當的關注點分離
- 可測試架構
