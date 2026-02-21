---
name: android-unit-test
description: 當撰寫 Android 單元測試、UI 測試及實踐 TDD 時使用此技能。涵蓋 JUnit 單元測試、Espresso UI 測試，以及在 Kotlin 中使用 MockK 進行模擬。目標測試覆蓋率 >80%。
---

# Android 單元測試技能

完整的 Android 測試指南，使用 JUnit、Espresso 和 MockK。目標：以 TDD 實踐達到 >80% 的測試覆蓋率。

## 📚 來源

- **JUnit**: https://junit.org/junit5/docs/current/user-guide/
- **Espresso**: https://developer.android.com/training/testing/espresso
- **MockK**: https://github.com/mockk/mockk (5,698 ⭐)

## 💡 何時使用此技能

使用時機：
- 為 ViewModel、Repository、UseCase 撰寫單元測試
- 使用 Espresso 建立 UI 測試
- 在 Kotlin 中使用 MockK 模擬依賴項
- 實踐 TDD（紅-綠-重構）
- 使用 JaCoCo 達到 >80% 的程式碼覆蓋率

---

## 📋 JUnit 單元測試

### 設定 (build.gradle.kts)

```kotlin
dependencies {
    // JUnit 5
    testImplementation("org.junit.jupiter:junit-jupiter:5.10.0")

    // JUnit 4 (for Android instrumented tests)
    testImplementation("junit:junit:4.13.2")
    androidTestImplementation("androidx.test.ext:junit:1.1.5")
}
```

### 基本 JUnit 測試結構

```kotlin
import org.junit.jupiter.api.Test
import org.junit.jupiter.api.BeforeEach
import org.junit.jupiter.api.AfterEach
import org.junit.jupiter.api.Assertions.*

class CalculatorTest {
    private lateinit var calculator: Calculator

    @BeforeEach
    fun setup() {
        calculator = Calculator()
    }

    @Test
    fun `add returns sum of two numbers`() {
        // Arrange
        val a = 5
        val b = 3

        // Act
        val result = calculator.add(a, b)

        // Assert
        assertEquals(8, result)
    }

    @Test
    fun `divide throws exception when divisor is zero`() {
        assertThrows<ArithmeticException> {
            calculator.divide(10, 0)
        }
    }
}
```

### JUnit 5 參數化測試

```kotlin
import org.junit.jupiter.params.ParameterizedTest
import org.junit.jupiter.params.provider.CsvSource
import org.junit.jupiter.params.provider.ValueSource

class ParameterizedTests {
    @ParameterizedTest
    @ValueSource(ints = [1, 2, 3, 4, 5])
    fun `number is positive`(number: Int) {
        assertTrue(number > 0)
    }

    @ParameterizedTest
    @CsvSource("1, 1, 2", "2, 3, 5", "10, 20, 30")
    fun `add returns correct sum`(a: Int, b: Int, expected: Int) {
        assertEquals(expected, Calculator().add(a, b))
    }
}
```

---

## 🎭 MockK 模擬函式庫

### 設定

```kotlin
dependencies {
    // Unit tests
    testImplementation("io.mockk:mockk:1.14.7")

    // Android instrumented tests
    androidTestImplementation("io.mockk:mockk-android:1.14.7")
    androidTestImplementation("io.mockk:mockk-agent:1.14.7")
}
```

### 基本模擬

```kotlin
import io.mockk.*

@Test
fun `basic mock example`() {
    // Create mock
    val repository = mockk<UserRepository>()

    // Define behavior
    every { repository.getUser(1) } returns User(1, "John")

    // Use mock
    val user = repository.getUser(1)

    // Verify
    assertEquals("John", user.name)
    verify { repository.getUser(1) }
    confirmVerified(repository)
}
```

### 寬鬆模擬（自動 stub 所有方法）

```kotlin
val repository = mockk<UserRepository>(relaxed = true)

// No need to define every behavior
repository.saveUser(User(1, "John")) // Returns Unit automatically
repository.getUsers() // Returns empty list automatically
```

### 使用 JUnit 5 的 MockK 注解

```kotlin
@ExtendWith(MockKExtension::class)
class ViewModelTest {
    @MockK
    lateinit var repository: UserRepository

    @RelaxedMockK
    lateinit var analytics: AnalyticsService

    @InjectMockKs
    lateinit var viewModel: UserViewModel

    @Test
    fun `load users updates state`() {
        every { repository.getUsers() } returns listOf(User(1, "John"))

        viewModel.loadUsers()

        verify { repository.getUsers() }
    }
}
```

### 捕捉參數

```kotlin
@Test
fun `capture argument values`() {
    val repository = mockk<UserRepository>(relaxed = true)
    val slot = slot<User>()

    every { repository.saveUser(capture(slot)) } just runs

    repository.saveUser(User(1, "John"))

    assertEquals("John", slot.captured.name)
}
```

### 協程模擬

```kotlin
@Test
fun `mock suspend functions`() = runTest {
    val repository = mockk<UserRepository>()

    coEvery { repository.fetchUser(1) } returns User(1, "John")

    val user = repository.fetchUser(1)

    coVerify { repository.fetchUser(1) }
}
```

### 驗證模式

```kotlin
verify(exactly = 1) { repository.getUser(any()) }
verify(atLeast = 2) { repository.saveUser(any()) }
verify(atMost = 3) { repository.deleteUser(any()) }
verifyOrder { repository.getUser(1); repository.saveUser(any()) }
verifySequence { repository.getUser(1); repository.getUser(2) }
```

---

## 📱 Espresso UI 測試

### 設定

```kotlin
dependencies {
    androidTestImplementation("androidx.test.espresso:espresso-core:3.6.1")
    androidTestImplementation("androidx.test.espresso:espresso-intents:3.6.1")
    androidTestImplementation("androidx.test:runner:1.6.1")
    androidTestImplementation("androidx.test:rules:1.6.1")
}

android {
    defaultConfig {
        testInstrumentationRunner = "androidx.test.runner.AndroidJUnitRunner"
    }
}
```

### 基本 Espresso 測試

```kotlin
@RunWith(AndroidJUnit4::class)
@LargeTest
class LoginActivityTest {
    @get:Rule
    val activityRule = ActivityScenarioRule(LoginActivity::class.java)

    @Test
    fun loginButton_clickWithValidCredentials_navigatesToHome() {
        // Type username
        onView(withId(R.id.username_field))
            .perform(typeText("user@example.com"))

        // Type password
        onView(withId(R.id.password_field))
            .perform(typeText("password123"), closeSoftKeyboard())

        // Click login button
        onView(withId(R.id.login_button))
            .perform(click())

        // Verify navigation
        onView(withId(R.id.home_container))
            .check(matches(isDisplayed()))
    }
}
```

### 常用 Espresso 比對器

```kotlin
// By ID
onView(withId(R.id.my_view))

// By text
onView(withText("Hello"))
onView(withText(containsString("Hello")))

// Combining matchers
onView(allOf(withId(R.id.button), withText("Submit")))
onView(allOf(withText("7"), hasSibling(withText("item: 0"))))

// Hierarchy matchers
onView(withParent(withId(R.id.container)))
onView(isDescendantOfA(withId(R.id.list)))
```

### 常用 Espresso 動作

```kotlin
// Click actions
.perform(click())
.perform(longClick())
.perform(doubleClick())

// Text input
.perform(typeText("Hello"))
.perform(replaceText("New text"))
.perform(clearText())
.perform(closeSoftKeyboard())

// Scrolling
.perform(scrollTo())
.perform(swipeUp())
.perform(swipeDown())
```

### 常用 Espresso 斷言

```kotlin
// Visibility
.check(matches(isDisplayed()))
.check(matches(not(isDisplayed())))
.check(doesNotExist())

// Content
.check(matches(withText("Expected")))
.check(matches(hasErrorText("Invalid input")))

// State
.check(matches(isEnabled()))
.check(matches(isChecked()))
.check(matches(isSelected()))
```

### RecyclerView 測試

```kotlin
@Test
fun recyclerView_clicksFirstItem() {
    onView(withId(R.id.recycler_view))
        .perform(
            RecyclerViewActions.actionOnItemAtPosition<RecyclerView.ViewHolder>(
                0, click()
            )
        )
}

@Test
fun recyclerView_scrollToItem() {
    onView(withId(R.id.recycler_view))
        .perform(
            RecyclerViewActions.scrollTo<RecyclerView.ViewHolder>(
                hasDescendant(withText("Target Item"))
            )
        )
}
```

### 使用 Espresso-Intents 進行 Intent 測試

```kotlin
@get:Rule
val intentsRule = IntentsTestRule(MainActivity::class.java)

@Test
fun clickShare_launchesShareIntent() {
    // Stub external intent
    intending(hasAction(Intent.ACTION_SEND))
        .respondWith(Instrumentation.ActivityResult(Activity.RESULT_OK, null))

    // Trigger share
    onView(withId(R.id.share_button)).perform(click())

    // Verify intent
    intended(allOf(
        hasAction(Intent.ACTION_SEND),
        hasType("text/plain")
    ))
}
```

---

## 🔄 TDD 實踐（紅-綠-重構）

### TDD 工作流程

1. **紅（RED）**：先撰寫一個失敗的測試
2. **綠（GREEN）**：撰寫最少的程式碼使其通過
3. **重構（REFACTOR）**：在保持測試通過的同時改善程式碼

### TDD 範例：建立 UserValidator

```kotlin
// Step 1: RED - Write failing test
class UserValidatorTest {
    @Test
    fun `email with valid format returns true`() {
        val validator = UserValidator()
        assertTrue(validator.isValidEmail("test@example.com"))
    }
}

// Step 2: GREEN - Minimum implementation
class UserValidator {
    fun isValidEmail(email: String): Boolean {
        return email.contains("@") && email.contains(".")
    }
}

// Step 3: RED - Add more test cases
@Test
fun `email without @ returns false`() {
    assertFalse(validator.isValidEmail("testexample.com"))
}

@Test
fun `empty email returns false`() {
    assertFalse(validator.isValidEmail(""))
}

// Step 4: REFACTOR - Improve implementation
class UserValidator {
    private val emailPattern = Regex("^[A-Za-z0-9+_.-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,}\$")

    fun isValidEmail(email: String): Boolean {
        return email.isNotBlank() && emailPattern.matches(email)
    }
}
```

### Given-When-Then 模式

```kotlin
@Test
fun `given user logged in, when logout clicked, then navigates to login`() {
    // Given
    val authManager = mockk<AuthManager>()
    every { authManager.isLoggedIn } returns true
    coEvery { authManager.logout() } just runs
    val viewModel = ProfileViewModel(authManager)

    // When
    viewModel.onLogoutClicked()

    // Then
    coVerify { authManager.logout() }
    assertEquals(Screen.Login, viewModel.navigationState.value)
}
```

---

## 📊 測試覆蓋率（目標 >80%）

### JaCoCo 設定

```kotlin
// build.gradle.kts
plugins {
    id("jacoco")
}

tasks.withType<Test> {
    useJUnitPlatform()
    finalizedBy(tasks.jacocoTestReport)
}

tasks.jacocoTestReport {
    dependsOn(tasks.test)
    reports {
        xml.required.set(true)
        html.required.set(true)
    }
}

tasks.jacocoTestCoverageVerification {
    violationRules {
        rule {
            limit {
                minimum = "0.80".toBigDecimal() // 80% coverage
            }
        }
    }
}
```

### 覆蓋率最佳實踐

1. **專注於業務邏輯**：ViewModel、UseCase、Repository
2. **跳過生成的程式碼**：Data class、DI 模組
3. **同時測量行覆蓋率與分支覆蓋率**
4. **在 CI/CD 設定 80% 的門檻**

### 排除模式

```kotlin
tasks.jacocoTestReport {
    classDirectories.setFrom(
        files(classDirectories.files.map {
            fileTree(it) {
                exclude(
                    "**/BuildConfig.*",
                    "**/R.*",
                    "**/R$*.*",
                    "**/*_Factory.*",
                    "**/*_MembersInjector.*",
                    "**/di/**",
                    "**/model/**" // Data classes
                )
            }
        })
    )
}
```

---

## 🎯 快速參考

**來自文件：**

### 常用模式

**模式 1：** 此文件說明如何設定多種常見的 Espresso 測試

```
R.id
```

**模式 2：** 標頭和頁尾使用 addHeaderView() 和 addFooterView() 方法新增至 ListView

```
ListViews
```

**模式 3：** Espresso-Web 是用於操作 Android WebView UI 元件的進入點

```
WebView
```

**模式 4：** 範例：

```
onWebView().withElement(findElement(Locator.ID, "teacher"))
```

**模式 5：** 範例：

```
.withElement(findElement(Locator.ID, "teacher"))
    .withContextualElement(findElement(Locator.ID, "person_name"))
```

**模式 6：** 範例：

```
onWebView()
    .withElement(findElement(Locator.ID, "teacher"))
    .withContextualElement(findElement(Locator.ID, "person_name"))
    .check(webMatches(getText(), containsString("Socrates")))
```

**模式 7：** 範例：

```
onWebView()
    .withElement(findElement(Locator.ID, "teacher"))
    .perform(webClick())
```

**模式 8：** Espresso 提供機制，可對兩種類型的列表中的特定項目進行捲動或操作：adapter view 和 recycler view

```
RecyclerView
```

### 程式碼範例模式

**範例 1** (json):
```json
onView(allOf(withText("7"), hasSibling(withText("item: 0"))))
    .perform(click())
```

**範例 2** (json):
```json
onView(allOf(withText("7"), hasSibling(withText("item: 0"))))
    .perform(click());
```

### 儲存庫資訊
- **首頁：** https://mockk.io
- **主題：** verification, chain-calls, matcher, argument-matchers, kotlin, mocking-framework, mock, testing, tdd, hacktoberfest
- **未解決問題：** 310
- **最後更新：** 2026-01-14

### 語言
- **Kotlin：** 86.6%
- **C++：** 6.4%
- **Java：** 5.0%
- **HTML：** 1.6%
- **JavaScript：** 0.2%
- **CMake：** 0.1%
- **Shell：** 0.1%
- **SCSS：** 0.0%

### 偵測到的設計模式

*來自 C3.1 程式碼庫分析（信心度 > 0.7）*

- **策略模式（Strategy）**：14 個實例
- **工廠模式（Factory）**：4 個實例
- **建造者模式（Builder）**：1 個實例

*共計：13 個高信心度模式*

## 🧪 程式碼範例

## 🔧 API 參考

*從程式碼庫分析中提取（C2.5）*

### buffer

# API 參考：buffer.h

**語言**：C++

**來源**：`modules/mockk-agent-android/external/slicer/export/slicer/buffer.h`

---

## ⚠️ 已知問題

*來自 GitHub 的最新問題*

*來自 GitHub 的最新問題*

- **#1486**: clearAllMocks() 拖慢測試執行速度
- **#1496**: 一個 `InjectMockKs` 對另一個使用 `lateinit var` 及建構子注入的依賴
- **#1446**: Kotlin 2.3+ 及 JUnit 6 內部問題 [`enhancement`]
- **#1021**: class java.lang.String cannot be cast to class kotlin.Result (java.lang.String is in module java.base of loader 'bootstrap'; kotlin.Result is in unnamed module of loader 'app') [`bug, coroutines`]
- **#1497**: 支援 Kotlin 注解的新預設規則

*完整清單請參閱 `references/issues.md`*

### 近期發行版本
- **1.14.7** (2025-12-06): v1.14.7
- **1.14.6** (2025-10-01): 1.14.6
- **1.14.5** (2025-07-16): 1.14.5

## 📖 參考文件

依來源整理：

- [文件](references/documentation/)
- [GitHub](references/github/)
- [程式碼庫分析](references/codebase_analysis/mockk_mockk/ARCHITECTURE.md)

---

*由 Skill Seekers 從官方文件及程式碼庫分析中整合*
