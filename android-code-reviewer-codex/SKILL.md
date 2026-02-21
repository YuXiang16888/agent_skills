---
name: android-code-reviewer-codex
description: Use when you want to delegate Android/Kotlin code review to OpenAI Codex with high reasoning effort. Invokes gpt-5-codex in read-only sandbox mode for comprehensive code quality analysis.
---
# Android Code Reviewer（Codex 委派）
將 Android/Kotlin 程式碼審查委派給 OpenAI Codex，使用 gpt-5-codex 模型，以高推理強度在唯讀沙盒環境中進行全面的程式碼品質分析。

## 使用時機
適用情境：
- 希望從不同 AI 模型獲得 Android 程式碼品質的第二意見
- 審查大型 Android/Kotlin 程式碼庫，需要平行審查
- 需要以高推理強度進行全面審查
- 交叉驗證 Claude 與 Codex 之間的程式碼審查結果

不適用情境：
- 快速內嵌審查已足夠時（請直接使用 android-code-reviewer）
- 需要修改程式碼時（Codex 以唯讀模式運行）
- 無法連線至 Codex 網路時

## 指令
```bash
codex exec -m gpt-5-codex \
  --config model_reasoning_effort="high" \
  --sandbox read-only \
  --full-auto \
  --skip-git-repo-check \
  "Please use android-code-reviewer skill review code" 2>/dev/null
```

## 參數說明
| 參數 | 值 | 用途 |
|-----------|-------|---------|
| `-m gpt-5-codex` | 模型 | 使用 GPT-5 Codex 進行程式碼分析 |
| `--config model_reasoning_effort="high"` | 推理強度 | 最大推理強度以進行深度審查 |
| `--sandbox read-only` | 安全性 | 防止任何檔案被修改 |
| `--full-auto` | 模式 | 無需互動提示，自動執行 |
| `--skip-git-repo-check` | Git | 略過 git 儲存庫驗證 |
| `2>/dev/null` | 標準錯誤 | 隱藏錯誤輸出 |

## 使用範例

### 審查當前目錄
```bash
codex exec -m gpt-5-codex \
  --config model_reasoning_effort="high" \
  --sandbox read-only \
  --full-auto \
  --skip-git-repo-check \
  "Please use android-code-reviewer skill review code"
```

### 審查特定檔案
```bash
codex exec -m gpt-5-codex \
  --config model_reasoning_effort="high" \
  --sandbox read-only \
  --full-auto \
  --skip-git-repo-check \
  "Please use android-code-reviewer skill review the following files: UserViewModel.kt, UserRepository.kt"
```

### 聚焦特定面向審查
```bash
codex exec -m gpt-5-codex \
  --config model_reasoning_effort="high" \
  --sandbox read-only \
  --full-auto \
  --skip-git-repo-check \
  "Please use android-code-reviewer skill review code, focusing on memory leaks and coroutine management"
```

## 審查涵蓋範圍
Codex 所呼叫的 android-code-reviewer skill 涵蓋以下項目：
1. **記憶體安全** - Context 洩漏、WeakReference 使用、生命週期感知
2. **協程管理** - Scope 使用、取消機制、例外處理
3. **LiveData/Flow 模式** - Observer 生命週期、repeatOnLifecycle、資源釋放
4. **架構規範** - MVVM 合規性、層級分離、UseCase 模式
5. **命名慣例** - 檔案、變數、函式命名標準
6. **錯誤處理** - Result 模式、例外階層、重試邏輯
7. **程式碼品質指標** - 循環複雜度、巢狀深度、類別大小

## 輸出結果
Codex 回傳結構化的審查報告，包含：
- 已識別問題及其嚴重等級
- 顯示問題模式的程式碼片段
- 附有正確模式的修正建議
- 量化指標違規項目

## 結合 Claude 審查
如需最完整的覆蓋率，可同時執行兩種審查：
```bash
# 1. Claude 直接審查
# 在此工作階段中直接使用 android-code-reviewer skill
# 2. Codex 委派審查
codex exec -m gpt-5-codex \
  --config model_reasoning_effort="high" \
  --sandbox read-only \
  --full-auto \
  --skip-git-repo-check \
  "Please use android-code-reviewer skill review code" 2>/dev/null
# 3. 比對並整合審查結果
```

## 前置條件
- 已安裝並設定 Codex CLI
- 可存取 gpt-5-codex 模型
- android-code-reviewer skill 已存在於 Codex 環境中（`~/.codex/skills`）