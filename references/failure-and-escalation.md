# Failure 與 Escalation 規則

這份文件定義 `NEEDS_CONTEXT`、`BLOCKED`、review 退回、以及 shared truth drift 的標準處理方式。

## 核心原則

- `NEEDS_CONTEXT` 是輕度失敗，代表 handoff 品質不足
- `BLOCKED` 代表在目前 shared truth 與 task-brief 前提下，task 無法合法完成
- reviewer 不直接退回 implementer，一律先回 controller
- 相同原因重複兩次，必須升級，不得無限重試

## `NEEDS_CONTEXT`

定位：

- task 邊界可能是對的
- 但 `task-brief` 還不足以支撐安全實作

第一次：

- controller / dispatcher 補 brief
- 必要時補少量 excerpt
- 同一 task 可再派一次

第二次相同原因：

- 必須升級
- controller 應檢查：
  - scope 是否仍不夠清楚
  - brief 是否缺少關鍵 pattern / contract context
  - 是否其實應先做 recon

建議原因分類：

- `missing_scope_detail`
- `missing_contract_context`
- `missing_pattern_reference`
- `missing_verification_guidance`
- `brief_repo_mismatch`

## `BLOCKED`

定位：

- implementer 在目前前提下無法合法完成 task
- blocked task 是受控分支，不是新計畫

第一次：

- controller 允許一次局部補救，但必須先輸出有限選項與 resume target
- 若符合 technical blocked auto-selection 條件，controller 可自動選 recommended option 並繼續
- 若分類為 human blocked，必須停下等待使用者選擇
- 補救只能集中在一個方向：
  - 補 shared truth 說明
  - 微調 task scope
  - 補一輪局部 recon
  - 做 Level 1 contract correction

第二次相同原因：

- 不得直接再派 implementer
- 必須升級：
  - 回 recon
  - 回 dispatcher 重切
  - 或進入 contract change protocol

建議原因分類：

- `protected_zone`
- `contract_gap`
- `brief_repo_mismatch`
- `verification_gap`
- `scope_overlap`

特別規則：

- 若原因是 `brief_repo_mismatch` 或 shared truth drift，優先先修 shared truth
- controller 不得問開放題；必須依 `blocked-resume-protocol.md` 提出 2 到 4 個選項
- 每個選項都必須包含 `Resume target`
- technical blocked 可由 controller 自動選 recommended option；human blocked 才等待使用者選定
- 決策完成後，先更新 `controller-state.md` / `migration-map.md`，再執行補救

## Blocked Resume

`BLOCKED` 的標準恢復順序：

1. 凍結原 task，不改原目標
2. 在 `migration-map.md` 標記 batch 為 `blocked`
3. 整理 blocked reason、affected contracts、protected zones
4. 分類 technical / human blocked
5. 產出 `Blocked Escalation` 選項
6. technical blocked 自動選 recommended option；human blocked 等使用者選擇
7. 更新 shared truth 與 `controller-state.md`
8. 執行補救 task
9. 明確回到 original batch、revised task、prerequisite batch 或 independent batch

如果 controller 無法說出 active batch、active task brief、shared truth changes、next concrete action，不能派 implementer。

## Reviewer Return

- reviewer 一律回 controller，不直接回 implementer
- controller 再決定：
  - 回 implementer 修正
  - 回 dispatcher 重切
  - 回 recon 補調查
  - 進 contract protocol

第一次退回：

- 若只是 implementation fix，回 implementer

第二次相同原因退回：

- 視為 task definition 或 dispatch 可能有問題
- 必須升級 controller 判斷

## Shared Truth Drift

符合下列情況時，視為 drift：

- `task-brief` 與 repo 現況矛盾
- contract 假設已失效
- migration-map 落後到足以影響下一步決策

處理：

- controller 先停受影響批次
- 優先修 shared truth
- 必要時重做局部 recon 或重切 task

## Controller 補救優先順序

當 task 失敗時，controller 應依序思考：

1. 是不是只是 context 不夠
2. 是不是 brief 邊界不清
3. 是不是需要 recon
4. 是不是 contract 不夠穩
5. 是不是 batch 本身切錯了
