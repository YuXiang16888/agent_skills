---
name: android-jetpack-compose
description: Jetpack Compose Android UI 工具套件。用於宣告式 UI 開發、狀態管理、版面配置、動畫、Material Design、主題設定、手勢、列表、圖形、導航、測試，以及從 View-based 應用程式遷移。
---

# Android-Jetpack-Compose 技能

Jetpack Compose Android UI 工具套件。用於宣告式 UI 開發、狀態管理、版面配置、動畫、Material Design、主題設定、手勢、列表、圖形、導航、測試，以及從 View-based 應用程式遷移。由官方文件自動生成。

## 使用時機

此技能應在以下情況觸發：
- 使用 jetpack-compose 進行開發
- 詢問 jetpack-compose 功能或 API
- 實作 jetpack-compose 解決方案
- 除錯 jetpack-compose 程式碼
- 學習 jetpack-compose 最佳實踐

## 快速參考

### 常見模式

**模式 1：** 您可以在 Compose 中使用您喜愛的函式庫

```
ComponentActivity
```

**模式 2：** 本頁說明如何使用現有的 Glance 元件，透過 Glance 處理尺寸並提供彈性且響應式的版面配置

```
Box
```

**模式 3：** 使用 LocalContext

```
LocalContext.current
```

**模式 4：** Composable 和方法可使用「提供者」接受資源，例如 ImageProvider，或使用像 GlanceModifier 的多載方法

```
ImageProvider
```

**模式 5：** 許多應用程式需要顯示項目集合

```
Column
```

**模式 6：** 除了 Canvas composable 之外，Compose 還提供多個實用的圖形 Modifier，有助於繪製自訂內容

```
Canvas
```

**模式 7：** 進一步了解：如果應用程式有半透明的視窗背景，且您未使用 CompositingStrategy

```
CompositingStrategy.Offscreen
```

**模式 8：** 下拉刷新元件允許使用者在應用程式內容頂端向下拖曳以重新整理資料

```
PullToRefreshBox()
```

### 程式碼範例模式

**範例 1** (bash):
```bash
implementation "androidx.compose.material3:material3:$material3_version"
```

**範例 2** (typescript):
```typescript
// Pass only the user ID when navigating to a new destination as argument
navController.navigate(Profile(id = "user1234"))
```

**範例 3** (typescript):
```typescript
composeTestRule.onNode(<<SemanticsMatcher>>, useUnmergedTree = false): SemanticsNodeInteraction
```

**範例 4** (php):
```php
data class Contact(val name: String, val number: String)
```

## 參考檔案

此技能包含 `references/` 中的完整文件：

- **accessibility.md** - 無障礙功能文件
- **animation.md** - 動畫文件
- **architecture.md** - 架構文件
- **components.md** - 元件文件
- **design_systems.md** - 設計系統文件
- **fundamentals.md** - 基礎知識文件
- **gestures.md** - 手勢文件
- **getting_started.md** - 入門文件
- **graphics.md** - 圖形文件
- **layouts.md** - 版面配置文件
- **lists.md** - 列表文件
- **migration.md** - 遷移文件
- **navigation.md** - 導航文件
- **other.md** - 其他文件
- **performance.md** - 效能文件
- **testing.md** - 測試文件
- **text_and_input.md** - 文字與輸入文件
- **tooling.md** - 工具文件

需要詳細資訊時，請使用 `view` 讀取特定的參考檔案。

## 使用此技能

### 初學者
從 getting_started 或 tutorials 參考檔案開始，了解基礎概念。

### 特定功能
使用適當的分類參考檔案（api、guides 等）以獲取詳細資訊。

### 程式碼範例
上方的快速參考區段包含從官方文件擷取的常見模式。

## 資源

### references/
從官方來源擷取的整理文件。這些檔案包含：
- 詳細說明
- 附有語言標註的程式碼範例
- 原始文件連結
- 快速導航的目錄

### scripts/
在此新增常用自動化任務的輔助腳本。

### assets/
在此新增範本、樣板或範例專案。

## 注意事項

- 此技能由官方文件自動生成
- 參考檔案保留了來源文件的結構和範例
- 程式碼範例包含語言偵測，以提供更好的語法高亮顯示
- 快速參考模式擷取自文件中的常見使用範例

## 更新

若要以最新文件重新整理此技能：
1. 使用相同設定重新執行爬蟲程式
2. 技能將以最新資訊重建
