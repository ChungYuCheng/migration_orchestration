# Blocked Resume Protocol

這份文件定義 `BLOCKED` 後如何升級、讓使用者選擇處理方式，並在處理完成後回到原本 migration plan。

## 核心原則

> A blocked task is a controlled branch, not a new plan.

`BLOCKED` 發生後，controller 不得問開放題，也不得直接順手修。controller 必須保留原 batch，提出有限選項，並為每個選項標明 resume target。

## Blocked Gate

收到 `BLOCKED` 後，controller 必須先停止原 task，並整理：

- Original batch id
- Original task brief
- Original goal
- Blocked reason
- Why it blocks
- Protected zones / contracts involved
- Current migration-map status

接著把原 batch 在 `migration-map.md` 標成 `blocked`，必要時更新 `controller-state.md`。

## Option Set

controller 必須提出 2 到 4 個選項，且標出一個 recommended。

每個選項必須包含：

- What changes
- Why choose this
- Risk
- Cost
- Shared truth updates required
- Resume target

禁止輸出：

- 沒有 resume target 的選項
- 讓使用者自行設計解法的開放題
- 直接放寬 protected zone 的選項
- 跳過 shared truth update 直接繼續 implement 的選項

## Standard Options

常見選項：

### Option A: Clarify contract and resume original task

適用：

- task 切分大致正確
- 只是 frozen contract 或 protected zone 說明不足
- 不需要改變 migration strategy

完成後：

- 更新 `contracts.md`
- 補強原 `task-brief`
- 回到原 batch / 原 task

### Option B: Split prerequisite batch

適用：

- 原 task 需要先完成 adapter、facade、contract correction 或 protected-zone change
- 不能合法地在原 task 內處理

完成後：

- 建立前置 batch
- 更新 `migration-map.md`
- 原 batch 改回 `ready` 或等待前置 batch `done`

### Option C: Redispatch current batch

適用：

- 原 task scope 錯誤
- write scope 重疊
- task 需要 implementer 重新理解大半個系統

完成後：

- 回 dispatcher 重切 `task-brief`
- 更新 `migration-map.md`
- 不直接回原 task

### Option D: Pause and continue independent batch

適用：

- blocked task 需要人工或外部決策
- 有其他 independent batch 可安全推進

完成後：

- blocked batch 保持 `blocked`
- `controller-state.md` 記錄回來條件
- 切到明確不依賴 blocked 前提的 batch

## After User Decision

使用者選定選項後，controller 必須依序執行：

1. 記錄 chosen option
2. 更新 `controller-state.md`
3. 更新必要 shared truth
4. 執行選項中的 remediation task
5. 驗證 remediation 是否完成
6. 執行 resume action

resume action 必須是下列之一：

- Return to original batch
- Return to revised task brief
- Continue prerequisite batch
- Continue independent batch
- Stop for human decision

## Resume Invariant

完成 blocked remediation 後，controller 必須明確回答：

- We are returning to which batch?
- Which task brief is active now?
- What changed in shared truth?
- What remains blocked?
- What is the next concrete action?

如果無法回答，不能派 implementer。
