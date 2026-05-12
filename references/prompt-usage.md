# Prompt 使用方式

這份文件說明 `prompts/` 目錄中的角色 prompt 要怎麼搭配使用。

## Prompt 列表

- `controller-prompt.md`
- `discovery-agent-prompt.md`
- `dispatcher-prompt.md`
- `implementer-prompt.md`
- `reviewer-prompt.md`

## 使用原則

- 一次只允許一個 `controller`
- `discovery`、`dispatcher`、`implementer`、`reviewer` 都由 controller 指派
- prompt 不是 shared truth，本身不應該承載專案真相
- discovery / recon / review / dispatch 的中間輸出，預設是協作訊息，不是 repo 文件
- 真正的專案真相應放在：
  - `spec.md`
  - `contracts.md`
  - `migration-map.md`
  - 當前 `task-brief`

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

## 餵 prompt 時應附帶的材料

### 給 controller

- `spec.md`
- `contracts.md`
- `migration-map.md`
- 目前重構目標

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
- `context-packet`
- `contracts.md` 的必要段落

### 給 reviewer

- `task-brief`
- `contracts.md`
- implementation diff
- verification results
