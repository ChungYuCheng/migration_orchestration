# Device Verification Gate

這份文件定義何時把 AndroidEC 的實體裝置 / 模擬器驗證納入 migration review。這是高成本驗證 gate，只有必要時才啟用。

## 何時啟用

只有同時符合下列條件，才啟用 device verification：

- 專案是 AndroidEC，或 controller 明確知道目前專案具備同等裝置驗證環境
- task 影響 UI、navigation、Activity / Fragment / Composable 入口、使用者操作流程、dialog、列表、空狀態、視覺狀態或畫面可見行為
- reviewer 無法只靠 compile、unit test、screenshot diff 或 code review 充分判斷
- controller / reviewer 已整理好明確驗證項目清單

不要用在：

- 純文件
- 純資料模型 / mapper 且已有 focused tests
- 純 compile-level refactor
- 非 AndroidEC 且沒有明確裝置驗證環境
- 沒有測試項目清單的情況

## Input Contract

啟用前，controller 或 reviewer 必須提供：

- 測試項目清單，每項包含：
  - `id`
  - `goal`
  - `expected`
  - `steps`
- 目標畫面：
  - route、Activity、Fragment、Composable 入口，或可從 app 內導航到該畫面的路徑
- variant hint：
  - 預設 `offline`

若缺少測試項目清單，不得啟用 device verification。先回 controller 補測項。

## Output Contract

device verification 只回報觀察結果：

```text
status: pass | fail | skipped
failures:
  - item_id:
    expected:
    actual:
notes:
```

- `pass`：全部項目通過
- `fail`：至少一項未通過
- `skipped`：裝置 / 模擬器不可用，或環境無法完成部署

禁止在 device verification 階段：

- 修改 codebase
- commit
- 自行修復
- 自行決定重跑
- 擴大測試範圍
- 推導新的需求或 acceptance criteria

## 操作規則

- 優先使用實體裝置；不可用時才使用模擬器
- 使用 caller 指定 variant；未指定時預設 offline
- 啟動 app 後從 app 內流程導航到目標畫面
- 不依賴外部 deep link 觸發 `momo://`，因為此類連結可能只在 app 內部流程被消費
- 若 app 啟動需要選擇環境，選擇 Release 進入 app
- 若驗證流程需要登入但 caller 未指定帳號，使用專案既有測試帳號策略；若沒有可用策略，回報 blocked / skipped，不自行創建帳號

## UI 操作準則

- 操作前先讀取 UI 結構，依文字、resource id、可見 bounds 定位目標
- 禁止在未讀 UI 結構的情況下盲點座標
- 找不到元素時，滾動一次後重新讀取 UI 結構
- 滾動要依目前可滾動範圍計算，不使用固定螢幕尺寸假設
- 若連續兩次 UI 結構沒有變化，或同方向多次滾動仍找不到目標，停止並將該項標為 fail
- 截圖只用於視覺驗證，不作為主要導航工具
- 若操作過程中 app crash，該項標為 fail，實際結果記錄 crash 摘要，然後繼續下一項

## Review Integration

device verification 的結果由 controller 處理：

- `pass`：可進入 batch done 判斷
- `fail`：回 implementer 修正，或升級成 review issue
- `skipped`：記錄為驗證缺口，由 controller 判斷是否需要人類決策

`skipped` 不等於 pass。若 task 的主要風險只能靠裝置驗證確認，`skipped` 必須成為 human decision gate。

## 輕量原則

- 不預設每個 batch 都跑 device verification
- 不為了跑裝置驗證而擴大 task scope
- 不把 device verification 當成取代 unit test / focused test 的手段
- 只有當它能補足 review 無法確認的 UI / flow 風險時才使用
