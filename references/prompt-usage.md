# Prompt 使用方式

這份文件說明 `prompts/` 目錄中的角色 prompt 要怎麼搭配使用。

## Prompt 清單

- `controller-prompt.md`
- `discovery-agent-prompt.md`
- `dispatcher-prompt.md`
- `implementer-prompt.md`
- `reviewer-prompt.md`

## 使用原則

- 一次只允許一個 `controller`
- `discovery`、`dispatcher`、`implementer`、`reviewer` 都由 controller 指派
- 角色可以由 main controller 依序扮演，也可以在符合 `subagent-dispatch-policy.md` 時分派 sub-agent
- prompt 不是 shared truth，本身不應該承載專案真相
- discovery / recon / review / dispatch 的中間輸出，預設是協作訊息，不是 repo 文件
- 真正的專案真相應放在：
  - `spec.md`
  - `contracts.md`
  - `migration-map.md`
  - 當前 `task-brief`
  - `controller-state.md`（只有 compact / resume / 交接需要時）

## 建議順序

### 小型 migration

1. controller
2. implementer
3. reviewer

### 中型 migration

1. controller
2. discovery
3. dispatcher
4. implementer
5. reviewer

### 大型 migration

1. controller
2. discovery
3. controller 更新 shared truth
4. dispatcher
5. implementer（可多批）
6. reviewer
7. controller 更新 migration map

## 文件落地原則

預設只落地這些文件：

- `spec.md`
- `contracts.md`
- `migration-map.md`
- 當前使用中的 `task-brief`

以下內容預設不落地，只在必要時才寫成檔案：

- discovery report
- recon report
- dispatch plan
- implementation report
- review report
- 額外 context packet
- controller-state
- blocked escalation
- device verification items

## 自動續做

controller 完成一個 batch 後，應根據 `continuation-policy.md` 判斷是否能自動續做。若下一批 ready、沒有 human gate、scope 可保守切出，應直接建立下一個 `task-brief` 並繼續，不要只更新 `controller-state.md` 後停止。

## 輕量進度顯示

controller 可以在使用者回覆中顯示簡短「進度條列」與「批次狀態」，讓開發者知道目前做到哪裡。

規則：

- 預設只顯示在回覆中，不建立新文件
- 進度條列不是 shared truth
- 不取代 `migration-map.md`
- 只有使用者要求保存、context 可能 compact、或跨 session 時，才把必要進度摘要寫入 `controller-state.md`
- 若 controller 停下、commit、blocked、compact、交接或續做下一批，必須同時更新回覆最上方與 `controller-state.md` 頂部

## 餵 prompt 時應附帶的材料

### 給 controller

- `spec.md`
- `contracts.md`
- `migration-map.md`
- 目前重構目標
- `controller-state.md`（如果是 compact / resume 後接續工作）
- `blocked-escalation.md`（如果是 `BLOCKED` 後接續工作）

### 給 discovery agent

- `spec.md`
- 現有程式碼範圍
- 想調查的 migration target

### 給 dispatcher

- `spec.md`
- `contracts.md`
- `migration-map.md`
- `Discovery Report`
- `Recon Report`（如果有）

### 給 implementer

- `task-brief`
- `contracts.md` 的必要段落
- 必要時才附 supporting reference 或 excerpt

### 給 reviewer

- `task-brief`
- `contracts.md`
- implementation diff
- verification results
- `device-verification-items.md`（只有需要裝置驗證時）
