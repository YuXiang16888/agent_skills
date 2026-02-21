# Kotlin 最佳實踐

全面的 Kotlin 慣用、高效且可維護程式碼編寫指南。

## 使用此技能的時機

在為 Android 應用程式、後端服務或多平台專案編寫 Kotlin 程式碼時使用此技能。這些實踐能幫助您有效地運用 Kotlin 的語言特性，同時避免常見的陷阱。

---

## 1. 空值安全處理

Kotlin 的空值安全是核心特性，請正確使用：

```kotlin
// 良好：安全呼叫運算子
val length = name?.length

// 良好：Elvis 運算子提供預設值
val displayName = name ?: "Unknown"

// 良好：作用域函式處理可空值
user?.let {
    println("User: ${it.name}")
}

// 不佳：除非絕對確定，否則避免使用 !!
val length = name!!.length  // 可能拋出 NullPointerException
```

**重點：**
- 偏好使用 `?.` 而非 `!!`
- 使用 `?:` 提供預設值
- 善用 `let`、`also`、`run` 處理可空值
- 只在確定值非空時才使用 `!!`

---

## 2. 條件判斷式

偏好使用 `when` 表達式來寫出更清晰的條件判斷：

```kotlin
// 良好：when 表達式
val result = when (status) {
    Status.SUCCESS -> "Completed"
    Status.LOADING -> "In progress"
    Status.ERROR -> "Failed"
}

// 不佳：巢狀 if-else
val result = if (status == Status.SUCCESS) {
    "Completed"
} else if (status == Status.LOADING) {
    "In progress"
} else {
    "Failed"
}
```

**重點：**
- `when` 表達式更具可讀性且型別安全
- 善用模式比對功能
- 搭配 sealed class 使用完整的 `when`

---

## 3. 並發處理

使用 Kotlin 協程進行現代非同步程式設計：

```kotlin
// 良好：協程
suspend fun fetchData(): Result {
    return withContext(Dispatchers.IO) {
        api.getData()
    }
}

// 良好：Flow 處理資料串流
fun observeData(): Flow<Data> = flow {
    while (true) {
        emit(fetchLatest())
        delay(1000)
    }
}

// 良好：結構化並發
coroutineScope {
    val deferred1 = async { fetchUser() }
    val deferred2 = async { fetchPosts() }
    Result(deferred1.await(), deferred2.await())
}

// 不佳：傳統執行緒
Thread {
    val data = api.getData()
    runOnUiThread { updateUI(data) }
}.start()
```

**重點：**
- 使用協程而非執行緒
- 使用 Flow 處理資料串流
- 善用 `suspend` 函式進行非同步操作
- 使用具有作用域的結構化並發

---

## 4. 集合操作

使用函數式風格進行集合處理：

```kotlin
// 良好：函數式操作
val activeUserNames = users
    .filter { it.isActive }
    .map { it.name }
    .sorted()

// 良好：大型資料集使用 Sequence
val result = largeList.asSequence()
    .filter { it.isValid }
    .map { it.transform() }
    .take(10)
    .toList()

// 良好：惰性初始化
val expensiveData by lazy {
    computeExpensiveData()
}
```

**重點：**
- 使用 `map`、`filter`、`reduce` 等
- 大型資料集偏好使用 `Sequence`（惰性求值）
- 使用 `lazy` 進行延遲初始化

---

## 5. 類別與物件設計

```kotlin
// 良好：data class 用於資料容器
data class User(
    val id: String,
    val name: String,
    val email: String
)

// 良好：sealed class 用於受限繼承關係
sealed class Result<out T> {
    data class Success<T>(val data: T) : Result<T>()
    data class Error(val message: String) : Result<Nothing>()
    object Loading : Result<Nothing>()
}

// 良好：object 用於單例
object DatabaseManager {
    fun getConnection() = connection
}

// 良好：companion object 取代靜態
class MyClass {
    companion object {
        const val TAG = "MyClass"
        fun create(): MyClass = MyClass()
    }
}
```

**重點：**
- 偏好使用 `data class` 儲存資料
- 使用 `sealed class` 建立受限繼承關係
- 使用 `object` 實作單例模式
- 使用 `companion object` 取代靜態成員

---

## 6. 函式設計

```kotlin
// 良好：具名參數
fun createUser(
    name: String,
    email: String,
    isAdmin: Boolean = false
) = User(name, email, isAdmin)

val user = createUser(
    name = "John",
    email = "john@example.com",
    isAdmin = true
)

// 良好：使用預設參數取代多載
fun greet(name: String, greeting: String = "Hello") = "$greeting, $name!"

// 良好：單一表達式函式
fun square(x: Int) = x * x

// 良好：擴充函式
fun String.toSlug() = lowercase().replace(" ", "-")
```

**重點：**
- 使用具名參數提高可讀性
- 使用預設參數減少多載
- 對單一表達式函式使用 `=` 語法
- 使用擴充函式取代工具類別

---

## 7. 字串處理

```kotlin
// 良好：字串模板
val message = "Hello, $name! You have ${items.size} items."

// 良好：多行原始字串
val json = """
    {
        "name": "$name",
        "email": "$email"
    }
""".trimIndent()
```

---

## 8. 型別推斷

```kotlin
// 良好：讓編譯器推斷簡單型別
val name = "John"
val numbers = listOf(1, 2, 3)

// 良好：複雜情況下明確宣告型別
val callback: (Int, String) -> Boolean = { id, name ->
    validate(id, name)
}

// 良好：公開 API 應宣告回傳型別
fun fetchUsers(): List<User> = api.getUsers()
```

---

## 9. 委託屬性

```kotlin
// 良好：惰性初始化
val heavyObject by lazy {
    createHeavyObject()
}

// 良好：可觀察屬性
var name: String by Delegates.observable("") { _, old, new ->
    println("Changed from $old to $new")
}

// 良好：自訂委託
class Preference<T>(private val key: String, private val default: T) {
    operator fun getValue(thisRef: Any?, property: KProperty<*>): T =
        prefs.get(key, default)
    operator fun setValue(thisRef: Any?, property: KProperty<*>, value: T) =
        prefs.set(key, value)
}

var username by Preference("username", "")
```

---

## 10. 不可變性

```kotlin
// 良好：偏好 val 而非 var
val items = mutableListOf<String>()  // 參照不可變，內容可變
val user = User("John")              // 搭配 data class 完全不可變

// 良好：提早回傳以避免巢狀
fun process(input: String?): Result {
    if (input == null) return Result.Error("Input is null")
    if (input.isBlank()) return Result.Error("Input is blank")

    return Result.Success(input.process())
}
```

---

## 11. 可見性修飾詞

```kotlin
// 良好：最小權限原則
class UserRepository {
    private val cache = mutableMapOf<String, User>()  // 內部狀態

    internal fun clearCache() { cache.clear() }       // 模組層級存取

    fun getUser(id: String): User? = cache[id]        // 公開 API
}

// 良好：明確可見性
public class ApiService {  // 明確 public
    private val client = HttpClient()
}
```

**重點：**
- 預設使用 `private`
- 只在必要時增加可見性
- 明確宣告可見性

---

## 12. 擴充函式使用指南

```kotlin
// 良好：擴充函式靠近使用位置
// 在 UserUtils.kt 中
fun User.fullName() = "$firstName $lastName"

// 不佳：擴充核心型別
fun String.toUser() = User(this)  // 避免擴充 String

// 良好：限定作用域的擴充函式
class UserFormatter {
    fun User.formatted() = "${name.uppercase()} <${email}>"
}
```

**重點：**
- 不要過度使用擴充函式（命名空間污染）
- 將擴充函式放在靠近使用位置的檔案中
- 避免擴充核心型別如 `String`、`List`

---

## 13. 作用域函式選擇

| 函式 | 物件參照 | 回傳值 | 使用情境 |
|------|---------|--------|---------|
| `let`   | `it`   | Lambda 結果 | 空值處理、轉換 |
| `run`   | `this` | Lambda 結果 | 設定並計算 |
| `with`  | `this` | Lambda 結果 | 多項操作 |
| `apply` | `this` | 物件本身 | 物件設定 |
| `also`  | `it`   | 物件本身 | 附加操作 |

```kotlin
// let：空值處理與轉換
val length = name?.let { it.length } ?: 0

// run：設定並計算
val result = service.run {
    configure()
    execute()
}

// with：對同一物件進行多項操作
with(builder) {
    setName("Test")
    setSize(100)
    build()
}

// apply：物件設定（回傳物件本身）
val user = User().apply {
    name = "John"
    email = "john@example.com"
}

// also：附加操作（回傳物件本身）
val user = createUser().also {
    logger.log("Created user: ${it.id}")
}
```

---

## 14. 避免不良實踐

```kotlin
// 不佳：在迴圈中建立物件
for (item in items) {
    val formatter = SimpleDateFormat("yyyy-MM-dd")  // 每次迭代都會建立
}

// 良好：重複使用物件
val formatter = SimpleDateFormat("yyyy-MM-dd")
for (item in items) {
    formatter.format(item.date)
}

// 不佳：過度使用反射
val method = obj::class.java.getMethod("process")

// 不佳：濫用 Any 型別
fun process(data: Any) { }  // 失去型別安全

// 不佳：捕捉過於廣泛的例外
try {
    riskyOperation()
} catch (e: Exception) { }  // 範圍過廣

// 良好：指定特定例外
try {
    riskyOperation()
} catch (e: IOException) {
    handleIOError(e)
} catch (e: IllegalStateException) {
    handleStateError(e)
}
```

---

## 15. DSL 建構

```kotlin
// 良好：使用帶接收者的 lambda 建構 DSL
fun html(init: HTML.() -> Unit): HTML {
    return HTML().apply(init)
}

class HTML {
    fun body(init: Body.() -> Unit) { }
}

// 使用方式
html {
    body {
        p { +"Hello World" }
    }
}

// 良好：使用 @DslMarker 防止作用域洩漏
@DslMarker
annotation class HtmlDsl

@HtmlDsl
class HTML { }
```

---

## 16. 內聯函式

```kotlin
// 良好：高階函式使用 inline
inline fun <T> measureTime(block: () -> T): T {
    val start = System.currentTimeMillis()
    val result = block()
    println("Took ${System.currentTimeMillis() - start}ms")
    return result
}

// 良好：reified 用於存取泛型型別
inline fun <reified T> parseJson(json: String): T {
    return gson.fromJson(json, T::class.java)
}

// 使用方式
val user = parseJson<User>(jsonString)
```

**注意：** `inline` 會增加程式碼大小；請謹慎使用。

---

## 17. 運算子多載

```kotlin
// 良好：語義清晰
data class Point(val x: Int, val y: Int) {
    operator fun plus(other: Point) = Point(x + other.x, y + other.y)
}

val p1 = Point(1, 2)
val p2 = Point(3, 4)
val p3 = p1 + p2  // Point(4, 6)

// 不佳：語義不清
operator fun User.plus(other: User) = ...  // 將使用者相加是什麼意思？
```

---

## 18. 註解與文件

```kotlin
/**
 * 從遠端伺服器取得使用者資料。
 *
 * @param userId 使用者的唯一識別碼
 * @return [User] 物件，若未找到則回傳 null
 * @throws NetworkException 若網路無法使用
 */
suspend fun fetchUser(userId: String): User?

@Deprecated(
    message = "Use fetchUser() instead",
    replaceWith = ReplaceWith("fetchUser(userId)"),
    level = DeprecationLevel.WARNING
)
fun getUser(userId: String): User?
```

---

## 19. 平台互通性

```kotlin
class Utils {
    companion object {
        @JvmStatic
        fun helper() { }  // 可從 Java 以 Utils.helper() 呼叫

        @JvmField
        val TAG = "Utils"  // 可從 Java 以 Utils.TAG 存取
    }
}

// 產生 Java 多載
@JvmOverloads
fun greet(name: String, greeting: String = "Hello") = "$greeting, $name!"
// Java 看到：greet(String) 和 greet(String, String)
```

---

## 20. 測試最佳實踐

```kotlin
class UserRepositoryTest {
    private lateinit var repository: UserRepository
    private val testUser by lazy { createTestUser() }

    @BeforeEach
    fun setup() {
        repository = UserRepository()
    }

    @Test
    fun `should return user when user exists`() {
        repository.save(testUser)

        val result = repository.findById(testUser.id)

        assertThat(result).isEqualTo(testUser)
    }

    @Test
    fun `should return null when user does not exist`() {
        val result = repository.findById("nonexistent")

        assertThat(result).isNull()
    }
}
```

**重點：**
- 使用描述性的測試名稱（允許使用反引號）
- 使用 `lateinit` 和 `by lazy` 管理測試資源
- 考慮使用 Kotlin Test 或 Kotest 框架

---

## 21. 效能考量

```kotlin
// 良好：基本型別陣列
val numbers = IntArray(1000) { it }

// 不佳：裝箱陣列
val numbers = Array(1000) { it }  // 裝箱開銷

// 良好：多步驟操作的大型資料使用 Sequence
val result = (1..1_000_000).asSequence()
    .filter { it % 2 == 0 }
    .map { it * 2 }
    .take(100)
    .toList()

// 小型資料或單一操作使用集合
val result = smallList.filter { it.isValid }
```

---

## 22. 合約（Contracts）

```kotlin
@OptIn(ExperimentalContracts::class)
fun requireNotEmpty(value: String?): String {
    contract {
        returns() implies (value != null)
    }
    require(!value.isNullOrEmpty()) { "Value must not be empty" }
    return value  // 智慧轉型在此生效
}

// 使用方式
fun process(input: String?) {
    val validated = requireNotEmpty(input)
    // 此後 input 被智慧轉型為 String
    println(validated.length)
}
```

---

## 快速參考

| 類別 | 偏好 | 避免 |
|------|------|------|
| 空值安全 | `?.`, `?:`, `let` | `!!` |
| 條件判斷 | `when` | 巢狀 `if-else` |
| 非同步 | 協程、Flow | 執行緒、回呼 |
| 集合 | `map`、`filter`、Sequence | 手動迴圈進行轉換 |
| 類別 | `data class`、`sealed class` | 純類別儲存資料 |
| 函式 | 具名參數、預設值、擴充函式 | 多載、工具類別 |
| 變數 | `val` | `var` |
| 可見性 | `private` | 隱式 `public` |

---

## 相關資源

- [Kotlin 官方文件](https://kotlinlang.org/docs/)
- [Kotlin 程式碼慣例](https://kotlinlang.org/docs/coding-conventions.html)
- [協程指南](https://kotlinlang.org/docs/coroutines-guide.html)
- [Kotlin Koans](https://play.kotlinlang.org/koans/)
