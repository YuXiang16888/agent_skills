---
name: android-dependency-injection
description: 當在 Android 應用程式中實作依賴注入時使用此技能。涵蓋 Hilt（推薦）和 Dagger，用於管理依賴項、作用域、模組和元件階層。
---

# Android 依賴注入技能

當使用 Hilt 或 Dagger 在 Android 應用程式中實作依賴注入時使用此技能，內容源自官方 Android 文件。

## 何時使用此技能

以下情況應觸發此技能：
- 在 Android 專案中設定 Hilt 或 Dagger
- 使用 @Inject、@Module、@Provides、@Binds 注解
- 設定依賴作用域（Singleton、ActivityScoped、ViewModelScoped）
- 建立 Hilt 模組和元件
- 使用 Hilt 進行測試（@UninstallModules、@BindValue）
- 從手動依賴注入遷移至 Hilt/Dagger

## 快速參考

### 常見模式

**模式 1：** Dagger 基礎頁面說明了 Dagger 如何幫助您自動化應用程式中的依賴注入

```
@Inject
```

**模式 2：** @Provides 方法的依賴項就是該方法的參數

```
@Provides
```

**模式 3：** Hilt 是適用於 Android 的依賴注入函式庫，可減少專案中手動依賴注入的樣板程式碼

```
hilt-android-gradle-plugin
```

**模式 4：** 應用程式上下文綁定也可使用 @ApplicationContext 取得

```
@ApplicationContext
```

**模式 5：** Activity 上下文綁定也可使用 @ActivityContext 取得

```
@ActivityContext
```

**模式 6：** 注意：在本頁中，模組的參照指的是 Gradle 模組，而非 Dagger 模組

```
app
```

**模式 7：** 例如：一個名為 login 的功能模組想要建立一個 LoginComponent，該元件依賴於 app Gradle 模組中的 AppComponent

```
login
```

### 程式碼範例模式

**範例 1：HiltViewModel** (kotlin):
```kotlin
@HiltViewModel
class ExampleViewModel @Inject constructor(
  private val savedStateHandle: SavedStateHandle,
  private val repository: ExampleRepository
) : ViewModel() {
  ...
}
```

**範例 2：Gradle 設定（Kotlin DSL）** (kotlin):
```kotlin
plugins {
  id("kotlin-kapt")
}

dependencies {
    implementation("com.google.dagger:dagger:2.x")
    kapt("com.google.dagger:dagger-compiler:2.x")
}
```

**範例 3：Gradle 設定（Groovy）** (groovy):
```groovy
dependencies {
    implementation 'com.google.dagger:dagger:2.x'
    annotationProcessor 'com.google.dagger:dagger-compiler:2.x'
}
```

**範例 4：建構函式注入** (kotlin):
```kotlin
class UserRepository @Inject constructor(
    private val localDataSource: UserLocalDataSource,
    private val remoteDataSource: UserRemoteDataSource
) { ... }
```

## 參考文件

此技能包含 `references/` 中的完整文件：

- **hilt_basics.md** - Hilt 設定、@HiltAndroidApp、@AndroidEntryPoint、@Inject、綁定
- **hilt_modules.md** - @Module、@InstallIn、@Provides、@Binds、多模組專案
- **hilt_testing.md** - 使用 Hilt 進行測試、@UninstallModules、@BindValue、HiltAndroidRule
- **other.md** - Dagger 基礎、Android 整合、手動依賴注入概覽、子元件

使用 `view` 讀取特定參考文件以獲取詳細資訊。

## 使用此技能

### 初學者
從 getting_started 或 tutorials 參考文件開始，了解基礎概念。

### 特定功能
使用對應的分類參考文件（api、guides 等）獲取詳細資訊。

### 程式碼範例
上方的快速參考部分包含從官方文件中提取的常見模式。

## 資源

### references/
從官方來源提取的整理文件，這些文件包含：
- 詳細說明
- 附帶語言標注的程式碼範例
- 原始文件的連結
- 便於快速瀏覽的目錄

### scripts/
在此新增常見自動化任務的輔助腳本。

### assets/
在此新增模板、樣板程式碼或範例專案。

## 備註

- 此技能由官方文件自動生成
- 參考文件保留了來源文件的結構和範例
- 程式碼範例包含語言偵測以提供更好的語法高亮
- 快速參考模式從文件中的常見使用範例中提取

## 更新

若要以最新文件更新此技能：
1. 使用相同設定重新執行爬取程式
2. 技能將以最新資訊重新建置
