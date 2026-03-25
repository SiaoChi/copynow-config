# CopyNow Remote Config

App 啟動時會從此 repo 抓取 `public/remote-config.json`，用來控制版本更新提示。

## URL

```
https://raw.githubusercontent.com/SiaoChi/copynow-config/main/public/remote-config.json
```

---

## 欄位說明

| 欄位 | 型別 | 說明 |
|------|------|------|
| `ios_min_version` | string | iOS 最低可用版本。低於此版本 → **強制更新**（不可關閉） |
| `ios_latest_version` | string | iOS 最新版本號。低於此版本 → **建議更新**（可按「稍後」） |
| `macos_min_version` | string | macOS 最低可用版本 |
| `macos_latest_version` | string | macOS 最新版本號 |
| `update_description` | object | 更新說明，key 為語言代碼，value 為說明文字 |

---

## 三層更新機制

App 會依以下優先順序判斷要顯示哪種 modal：

| 優先級 | 情境 | 條件 | 行為 |
|--------|------|------|------|
| 1 | **強制更新** | 目前版本 < `min_version` | 只有「立即更新」按鈕，不可關閉，導向 App Store |
| 2 | **建議更新** | 目前版本 < `latest_version` | 「立即更新」+「稍後」按鈕，按稍後後該版本不再提示 |
| 3 | **What's New** | 目前版本 = `latest_version` 且未看過 | 顯示更新說明 +「知道了」按鈕 |

---

## 操作範例

### 發布新版本 v1.0.2（一般更新）

```json
{
  "ios_min_version": "1.0.0",
  "ios_latest_version": "1.0.2",
  ...
}
```
→ v1.0.1 用戶會看到**建議更新**，v1.0.2 用戶看到 **What's New**

### 發布重大更新，舊版必須升級

```json
{
  "ios_min_version": "1.0.2",
  "ios_latest_version": "1.0.2",
  ...
}
```
→ v1.0.1 以下用戶會看到**強制更新**（不可關閉）

### iOS 和 macOS 獨立更新

iOS 和 macOS 版本號可以不同步，各自有 `min` 和 `latest` 欄位。

---

## 支援語言

`update_description` 支援以下語言代碼：

| 代碼 | 語言 |
|------|------|
| `zh-TW` | 繁體中文 |
| `zh-Hans` | 简体中文 |
| `en` | English |
| `ja` | 日本語 |
| `ko` | 한국어 |
| `es` | Español |
| `fr` | Français |
| `de` | Deutsch |
| `vi` | Tiếng Việt |
| `th` | ภาษาไทย |

App 會依用戶語言設定匹配，找不到時 fallback 到 `en`。

---

## 注意事項

- 此 repo 必須是 **public**，App 透過 `raw.githubusercontent.com` 直接抓取
- **審核通過、新版上線後**才 push 更新到此 repo
- fetch 失敗時（無網路）App 不會擋用戶，靜默跳過
- 版本比較使用 semantic versioning（major.minor.patch）
