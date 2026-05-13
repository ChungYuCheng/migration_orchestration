# Dispatcher / Task Planner 規則

dispatcher 的工作是把 shared truth 和 discovery / triage 結果轉成 implementer 能執行的 bounded task。

在這個 skill 中，`task-brief` 是 implementer 的主要入口。預設不要依賴獨立的 `context-packet`；只有在少數極端情況，才另外附加 supporting reference。

## 目的

dispatcher 不創造策略，而是把 controller 已確認的策略轉成：

- migration batches
- task briefs
- dependency ordering
- verification instructions

## 輸入

- `spec.md`
- `contracts.md`
- `migration-map.md`
- `Discovery Report`
- triage 結論
- `Recon Report`（必要時）

## 輸出

- `task-briefs/*.md`
- 只有必要時才附加 `Relevant Excerpts` 或 supporting reference
- `Dispatch Plan` 預設不落地；只有批次切分複雜、需要交接，或 controller 要求保留 reasoning 時才產出

## 切分規則

好的 task 應滿足：

- 一個明確目標
- 一個穩定依賴
- 一組不重疊檔案
- 一個局部可跑的驗證命令
- 一個清楚的 blocked condition

好的 `task-brief` 還必須滿足：

- `Responsibility Scope` 清楚定義做什麼與不做什麼
- `File Scope` 預設用目錄級範圍
- `Required Context` 應為操作導向背景，而不是專案總覽
- implementer 讀完後不需要重新探索大半個系統
- 只有必要時才附 `Relevant Excerpts`

## Dispatcher 禁止事項

- 不修改 shared truth
- 不改變 migration strategy
- 不放寬 protected zones
- 不用模糊 wording 派工
- 不把 discovery / recon 報告原封不動轉貼成 `task-brief`

## 必填欄位

每個 task brief 至少要包含：

- Goal
- Why This Task Exists
- Responsibility Scope
- File Scope
- Required Context
- Acceptance Criteria
- Verification
- Blocked Conditions

## Scope 規則

- `You may modify` 預設使用目錄級範圍
- `You may read but not modify` 也可使用目錄級範圍
- `Do not modify` 可以混用目錄級與概念級紅線
- 若某目錄責任混雜，不能只給目錄級 scope，必須補例外限制

## Context 規則

`Required Context` 應優先回答：

- 這個 task 在 migration 的哪個位置
- 哪些 frozen contracts / adapters / assumptions 可以信任
- 這次應沿用哪個 pattern / path
- 哪些 known traps 最容易讓 implementer 走偏

不要放：

- 整份 spec 的重述
- 無關的 repo 背景
- discovery / recon 原文照貼

## 什麼情況不能派工

- contracts 尚未凍結
- protected zones 未明確
- 驗證路徑不清楚
- write scope 與其他批次高度重疊
- task 仍需要 implementer 重新理解大半個系統

## 精實原則

- 能直接 bounded implementation，就不要加 discovery 或 recon
- 能靠 triage 判斷，就不要升級成 full discovery
- 只有高風險 batch 才做 recon
- 調研若無法明顯改善切分與風險判斷，就應停止並派工

## Relevant Excerpts 規則

預設不要附 excerpt。只有當下列任一條件成立才附：

- implementer 若不看 excerpt 很可能誤用 frozen contract
- implementer 需要模仿既有 migrated pattern 才能安全實作
- 某個 shared type 很容易被誤解
- 某個 hidden coupling 無法只用文字說清楚

附 excerpt 的優先順序：

1. pattern excerpt
2. contract excerpt
3. shared type excerpt
