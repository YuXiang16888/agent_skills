# ViewBinding 與 DataBinding 指南

## 目錄
1. [ViewBinding 概覽](#viewbinding-overview)
2. [DataBinding 概覽](#databinding-overview)
3. [ViewBinding 與 DataBinding 比較](#viewbinding-vs-databinding)
4. [ViewBinding 設定與使用](#viewbinding-setup-and-usage)
5. [DataBinding 設定與使用](#databinding-setup-and-usage)
6. [綁定表達式](#binding-expressions)
7. [綁定適配器](#binding-adapters)
8. [雙向資料綁定](#two-way-data-binding)

---

## ViewBinding 概覽

ViewBinding 是一個功能，讓你更容易編寫與視圖互動的程式碼。它為每個 XML 佈局檔案生成一個綁定類別，提供所有帶有 ID 的視圖的直接參考。

### 主要優點

1. **Null 安全**：帶有適當可空性的直接視圖參考
2. **類型安全**：視圖以其正確類型存取
3. **編譯期安全**：無效視圖 ID 不會產生執行期錯誤
4. **效能**：無傳統 `findViewById` 的反射開銷
5. **簡單性**：最小設定，不需要標註

---

## DataBinding 概覽

DataBinding 允許你使用宣告式格式（而非程式化方式）在佈局中將 UI 元件綁定到應用程式的資料來源。

### 主要優點

1. **宣告式 UI**：在 XML 中直接綁定資料
2. **雙向綁定**：UI 與資料之間的自動同步
3. **可觀察資料**：資料變更時 UI 自動更新
4. **綁定表達式**：在佈局中執行簡單操作
5. **綁定適配器**：自訂屬性處理

---

## ViewBinding 與 DataBinding 比較

| 功能 | ViewBinding | DataBinding |
|---------|-------------|-------------|
| 建置時間 | 較快 | 較慢（標註處理） |
| APK 大小 | 較小 | 較大 |
| 複雜度 | 簡單 | 較複雜 |
| Null 安全 | 是 | 是 |
| 類型安全 | 是 | 是 |
| 綁定表達式 | 否 | 是 |
| 雙向綁定 | 否 | 是 |
| 可觀察資料 | 否 | 是 |
| 自訂綁定適配器 | 否 | 是 |

### 何時使用哪個

**使用 ViewBinding 時：**
- 你只需要視圖參考
- 你想要最小的開銷
- 你在程式碼中觀察資料（使用 StateFlow/LiveData）

**使用 DataBinding 時：**
- 你需要在 XML 中使用綁定表達式
- 你想要雙向資料綁定
- 你在使用 `Observable` 欄位的舊有程式碼
- 你需要自訂綁定適配器

---

## ViewBinding 設定與使用

### 啟用 ViewBinding

```kotlin
// build.gradle.kts (Module level)
android {
    buildFeatures {
        viewBinding = true
    }
}
```

### 生成的綁定類別

對於佈局檔案 `activity_main.xml`：

```xml
<!-- res/layout/activity_main.xml -->
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <TextView
        android:id="@+id/titleText"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />

    <Button
        android:id="@+id/submitButton"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Submit" />

</LinearLayout>
```

ViewBinding 生成 `ActivityMainBinding`：

```kotlin
// Auto-generated class
public final class ActivityMainBinding implements ViewBinding {
    public final TextView titleText;
    public final Button submitButton;
    // ...
}
```

### Activity 中的 ViewBinding

```kotlin
class MainActivity : AppCompatActivity() {

    private lateinit var binding: ActivityMainBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        // Inflate the binding
        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)

        // Access views directly
        binding.titleText.text = "Hello, World!"
        binding.submitButton.setOnClickListener {
            // Handle click
        }
    }
}
```

### Fragment 中的 ViewBinding

```kotlin
class UserFragment : Fragment(R.layout.fragment_user) {

    // Nullable backing property
    private var _binding: FragmentUserBinding? = null

    // Non-null accessor (only valid between onCreateView and onDestroyView)
    private val binding get() = _binding!!

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)

        // Bind to existing view
        _binding = FragmentUserBinding.bind(view)

        // Use binding
        binding.userNameText.text = "John Doe"
        binding.logoutButton.setOnClickListener {
            // Handle logout
        }
    }

    override fun onDestroyView() {
        super.onDestroyView()
        // Important: Clear the binding reference to prevent memory leaks
        _binding = null
    }
}
```

### RecyclerView Adapter 中的 ViewBinding

```kotlin
class UserAdapter(
    private val onUserClick: (User) -> Unit
) : ListAdapter<User, UserAdapter.UserViewHolder>(UserDiffCallback()) {

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
        }

        fun bind(user: User) {
            binding.apply {
                nameText.text = user.name
                emailText.text = user.email
                avatarImage.load(user.avatarUrl)
            }
        }
    }
}
```

### 含引入佈局的 ViewBinding

```xml
<!-- res/layout/layout_header.xml -->
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content">

    <TextView
        android:id="@+id/headerTitle"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />
</LinearLayout>

<!-- res/layout/activity_main.xml -->
<LinearLayout ...>

    <include
        android:id="@+id/header"
        layout="@layout/layout_header" />

    <TextView android:id="@+id/contentText" ... />

</LinearLayout>
```

```kotlin
class MainActivity : AppCompatActivity() {
    private lateinit var binding: ActivityMainBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)

        // Access included layout's views
        binding.header.headerTitle.text = "Welcome"
        binding.contentText.text = "Main content"
    }
}
```

---

## DataBinding 設定與使用

### 啟用 DataBinding

```kotlin
// build.gradle.kts (Module level)
android {
    buildFeatures {
        dataBinding = true
    }
}
```

### 佈局檔案結構

DataBinding 佈局需要 `<layout>` 根標籤：

```xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <!-- Data variables -->
    <data>
        <variable
            name="viewModel"
            type="com.example.UserViewModel" />

        <variable
            name="user"
            type="com.example.User" />
    </data>

    <!-- Layout content -->
    <androidx.constraintlayout.widget.ConstraintLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <TextView
            android:id="@+id/nameText"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@{user.name}" />

        <Button
            android:id="@+id/saveButton"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="@{() -> viewModel.onSaveClicked()}"
            android:text="Save" />

    </androidx.constraintlayout.widget.ConstraintLayout>
</layout>
```

### Activity 中的 DataBinding

```kotlin
class UserActivity : AppCompatActivity() {

    private lateinit var binding: ActivityUserBinding
    private val viewModel: UserViewModel by viewModels()

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        // Inflate using DataBindingUtil
        binding = DataBindingUtil.setContentView(this, R.layout.activity_user)

        // Set lifecycle owner for LiveData binding
        binding.lifecycleOwner = this

        // Set variables
        binding.viewModel = viewModel

        // Observe and set user
        viewModel.user.observe(this) { user ->
            binding.user = user
        }
    }
}
```

### Fragment 中的 DataBinding

```kotlin
class UserFragment : Fragment() {

    private var _binding: FragmentUserBinding? = null
    private val binding get() = _binding!!
    private val viewModel: UserViewModel by viewModels()

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View {
        _binding = FragmentUserBinding.inflate(inflater, container, false)
        return binding.root
    }

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)

        // Set lifecycle owner (use viewLifecycleOwner for fragments)
        binding.lifecycleOwner = viewLifecycleOwner

        // Set ViewModel
        binding.viewModel = viewModel
    }

    override fun onDestroyView() {
        super.onDestroyView()
        _binding = null
    }
}
```

---

## 綁定表達式

### 基本表達式

```xml
<!-- String concatenation -->
<TextView android:text="@{`Hello, ` + user.name}" />

<!-- Null coalescing -->
<TextView android:text="@{user.name ?? `Guest`}" />

<!-- Ternary operator -->
<TextView android:text="@{user.isVerified ? `Verified` : `Not Verified`}" />

<!-- Visibility based on condition -->
<TextView
    android:visibility="@{user.isVerified ? View.VISIBLE : View.GONE}"
    android:text="Verified" />

<!-- Method reference -->
<Button android:onClick="@{() -> viewModel.onButtonClicked()}" />

<!-- Method with parameter -->
<Button android:onClick="@{() -> viewModel.onItemClicked(user)}" />

<!-- Lambda with view parameter -->
<Button android:onClick="@{(view) -> viewModel.onButtonClicked(view)}" />
```

### 匯入

```xml
<data>
    <import type="android.view.View" />
    <import type="com.example.utils.StringUtils" />
    <import type="java.util.List" />

    <!-- Alias for conflicting names -->
    <import type="com.example.model.User" alias="UserModel" />

    <variable name="user" type="UserModel" />
</data>

<!-- Use imported classes -->
<TextView
    android:visibility="@{user.active ? View.VISIBLE : View.GONE}"
    android:text="@{StringUtils.formatName(user.name)}" />
```

### 集合與 Map

```xml
<data>
    <variable name="users" type="List&lt;User>" />
    <variable name="settings" type="Map&lt;String, Boolean>" />
    <variable name="index" type="int" />
</data>

<!-- Access list element -->
<TextView android:text="@{users[index].name}" />
<TextView android:text="@{users.get(index).name}" />

<!-- Access map value -->
<TextView android:text="@{settings[`darkMode`] ? `Dark` : `Light`}" />
```

### 字串資源

```xml
<!-- Simple string resource -->
<TextView android:text="@{@string/app_name}" />

<!-- Formatted string resource -->
<!-- strings.xml: <string name="welcome_message">Welcome, %s!</string> -->
<TextView android:text="@{@string/welcome_message(user.name)}" />

<!-- Plurals -->
<!-- strings.xml: <plurals name="items_count">...</plurals> -->
<TextView android:text="@{@plurals/items_count(count, count)}" />

<!-- Dimension resource -->
<View android:layout_width="@{@dimen/margin_large}" />
```

---

## 綁定適配器

自訂綁定適配器讓你可以定義自訂屬性行為。

### 簡單綁定適配器

```kotlin
// Load image with Coil
@BindingAdapter("imageUrl")
fun ImageView.loadImage(url: String?) {
    url?.let {
        load(it) {
            placeholder(R.drawable.placeholder)
            error(R.drawable.error)
        }
    }
}

// Usage in XML
// <ImageView app:imageUrl="@{user.avatarUrl}" />
```

### 含多個屬性的綁定適配器

```kotlin
@BindingAdapter("imageUrl", "placeholder", requireAll = false)
fun ImageView.loadImageWithPlaceholder(url: String?, placeholder: Drawable?) {
    load(url) {
        placeholder(placeholder ?: ColorDrawable(Color.GRAY))
        error(R.drawable.error)
    }
}

// Usage:
// <ImageView
//     app:imageUrl="@{user.avatarUrl}"
//     app:placeholder="@{@drawable/custom_placeholder}" />
```

### 可見性綁定適配器

```kotlin
@BindingAdapter("isVisible")
fun View.setIsVisible(isVisible: Boolean) {
    visibility = if (isVisible) View.VISIBLE else View.GONE
}

@BindingAdapter("isInvisible")
fun View.setIsInvisible(isInvisible: Boolean) {
    visibility = if (isInvisible) View.INVISIBLE else View.VISIBLE
}

// Usage:
// <ProgressBar app:isVisible="@{viewModel.isLoading}" />
```

### 含舊值的綁定適配器

```kotlin
@BindingAdapter("errorText")
fun TextInputLayout.setErrorText(oldError: String?, newError: String?) {
    // Only update if error changed
    if (oldError != newError) {
        error = newError
        isErrorEnabled = !newError.isNullOrEmpty()
    }
}
```

### RecyclerView 的綁定適配器

```kotlin
@BindingAdapter("items")
fun <T> RecyclerView.setItems(items: List<T>?) {
    val adapter = adapter as? ListAdapter<T, *>
    adapter?.submitList(items)
}

// Usage:
// <RecyclerView app:items="@{viewModel.users}" />
```

### 含防抖的點擊監聽器綁定適配器

```kotlin
@BindingAdapter("onSafeClick")
fun View.setOnSafeClickListener(listener: View.OnClickListener?) {
    if (listener == null) {
        setOnClickListener(null)
        return
    }

    setOnClickListener(object : View.OnClickListener {
        private var lastClickTime = 0L

        override fun onClick(v: View) {
            val currentTime = SystemClock.elapsedRealtime()
            if (currentTime - lastClickTime >= 500) {
                lastClickTime = currentTime
                listener.onClick(v)
            }
        }
    })
}

// Usage:
// <Button app:onSafeClick="@{() -> viewModel.onSubmit()}" />
```

---

## 雙向資料綁定

雙向綁定允許資料從資料來源流向 UI，也能從 UI 流回資料來源。

### 基本雙向綁定

```xml
<EditText
    android:text="@={viewModel.username}" />
```

`@={}` 語法自動：
1. 從 `viewModel.username` 設定初始文字
2. 當文字變更時更新 `viewModel.username`

### 使用 LiveData 的雙向綁定

```kotlin
@HiltViewModel
class FormViewModel @Inject constructor() : ViewModel() {

    val username = MutableLiveData<String>("")
    val email = MutableLiveData<String>("")
    val rememberMe = MutableLiveData<Boolean>(false)
}
```

```xml
<layout>
    <data>
        <variable name="viewModel" type="com.example.FormViewModel" />
    </data>

    <LinearLayout ...>
        <EditText android:text="@={viewModel.username}" />
        <EditText android:text="@={viewModel.email}" />
        <CheckBox android:checked="@={viewModel.rememberMe}" />
    </LinearLayout>
</layout>
```

### 自訂雙向綁定適配器

```kotlin
// One-way adapter (get value)
@BindingAdapter("rating")
fun RatingBar.setRating(rating: Float) {
    if (this.rating != rating) {
        this.rating = rating
    }
}

// Inverse adapter (set value)
@InverseBindingAdapter(attribute = "rating")
fun RatingBar.getRating(): Float {
    return this.rating
}

// Listener for changes
@BindingAdapter("ratingAttrChanged")
fun RatingBar.setRatingChangeListener(listener: InverseBindingListener?) {
    onRatingBarChangeListener = if (listener != null) {
        RatingBar.OnRatingBarChangeListener { _, _, _ ->
            listener.onChange()
        }
    } else {
        null
    }
}

// Usage:
// <RatingBar app:rating="@={viewModel.rating}" />
```

### 自訂視圖的雙向綁定

```kotlin
// Custom SeekBar binding
object SeekBarBindingAdapters {

    @JvmStatic
    @BindingAdapter("progress")
    fun setProgress(seekBar: SeekBar, progress: Int) {
        if (seekBar.progress != progress) {
            seekBar.progress = progress
        }
    }

    @JvmStatic
    @InverseBindingAdapter(attribute = "progress")
    fun getProgress(seekBar: SeekBar): Int {
        return seekBar.progress
    }

    @JvmStatic
    @BindingAdapter("progressAttrChanged")
    fun setProgressChangeListener(seekBar: SeekBar, listener: InverseBindingListener?) {
        seekBar.setOnSeekBarChangeListener(object : SeekBar.OnSeekBarChangeListener {
            override fun onProgressChanged(seekBar: SeekBar, progress: Int, fromUser: Boolean) {
                if (fromUser) {
                    listener?.onChange()
                }
            }

            override fun onStartTrackingTouch(seekBar: SeekBar) {}
            override fun onStopTrackingTouch(seekBar: SeekBar) {}
        })
    }
}

// Usage:
// <SeekBar app:progress="@={viewModel.volume}" />
```

### 內建雙向屬性

| 視圖 | 屬性 |
|------|-----------|
| `EditText` | `android:text` |
| `CheckBox` | `android:checked` |
| `RadioButton` | `android:checked` |
| `Switch` | `android:checked` |
| `SeekBar` | `android:progress` |
| `RatingBar` | `android:rating` |
| `TabHost` | `android:currentTab` |
| `DatePicker` | `android:year`, `android:month`, `android:day` |
| `TimePicker` | `android:hour`, `android:minute` |
| `NumberPicker` | `android:value` |

---

## 最佳實踐

### ViewBinding 最佳實踐

```kotlin
// 1. Always null out binding in onDestroyView
override fun onDestroyView() {
    super.onDestroyView()
    _binding = null
}

// 2. Use binding delegate for cleaner code
class UserFragment : Fragment(R.layout.fragment_user) {
    private val binding by viewBinding(FragmentUserBinding::bind)
}

// 3. Avoid storing binding in companion object or global variables
```

### DataBinding 最佳實踐

```kotlin
// 1. Keep binding expressions simple
// Good
android:text="@{user.name}"

// Avoid complex logic - move to ViewModel
// Bad
android:text="@{user.firstName + ` ` + user.lastName.toUpperCase()}"

// 2. Use converters for formatting
@BindingAdapter("formattedDate")
fun TextView.setFormattedDate(date: Date?) {
    text = date?.let { DateFormat.format("MMM dd, yyyy", it) }
}

// 3. Always set lifecycleOwner
binding.lifecycleOwner = viewLifecycleOwner

// 4. Prefer StateFlow/LiveData over ObservableField
// Modern approach
val userName: StateFlow<String> = _userName.asStateFlow()

// Legacy approach (avoid)
val userName = ObservableField<String>()
```

### 遷移提示

```kotlin
// Migrating from findViewById to ViewBinding
// Before
val textView = findViewById<TextView>(R.id.myTextView)
textView.text = "Hello"

// After
binding.myTextView.text = "Hello"

// Migrating from Kotlin Synthetics to ViewBinding
// Before (deprecated)
import kotlinx.android.synthetic.main.fragment_user.*
myTextView.text = "Hello"

// After
private val binding by viewBinding(FragmentUserBinding::bind)
binding.myTextView.text = "Hello"
```
