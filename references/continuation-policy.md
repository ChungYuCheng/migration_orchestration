# Continuation Policy

這份文件定義 controller 在完成一個 batch 後，何時應該自動繼續下一批，何時必須停下等待人類決策。

## 核心原則

> Stop only at human gates, not at every batch boundary.

若下一批已 ready、shared truth 穩定、scope 可保守切出，controller 不應只留下 resume target 就停止。應自動建立下一個 task-brief，更新進度條列，並繼續推進。

`Auto-continue: Yes` 是執行狀態，不是停止狀態。controller 不得在 final 回覆中同時宣告 `Auto-continue: Yes` 又停下等待使用者。

## Auto-Continue Gate

同時符合下列條件時，controller 應自動續做：

- `migration-map.md` 中有下一個 `ready` batch
- contracts / protected zones 已凍結
- 下一批不依賴未確認結果
- 下一批 write scope 不與未完成工作衝突
- 不需要修改 frozen contract
- 不需要碰 protected zone
- 不需要擴大 spec 目標或非目標
- 不需要 rollout / rollback / release 決策
- device verification 不是啟動前置條件，或已經有明確測試項目清單與處理策略
- controller 能保守切出 bounded `task-brief`

符合條件後，controller 應依序：

1. 選出下一個 ready batch
2. 建立或更新該 batch 的 `task-brief`
3. 更新輕量進度條列
4. 若工作跨 session 或可能 compact，更新 `controller-state.md`
5. 直接進入 implement / review / verify 流程

## Technical Cohort Auto-Dispatch

若下一步是技術性選擇下一個 bounded cohort，但尚未有現成 `ready` batch，controller 不得把這當成 human gate。

適用情境：

- select next bounded row cohort
- Next bounded row selection
- Next row cohort selection
- row cohort selection
- select next bounded row
- select and dispatch next bounded row / contract batch
- select next caller cohort
- select next contract bridge / adapter batch
- select next low-risk display / static row
- select next bounded cleanup batch

同時符合下列條件時，controller 必須自行選擇並派工：

- `Human gate: No`
- `Auto-continue: Yes`
- shared truth 穩定
- protected zones 已知
- 候選 cohort 可保守切成 bounded `task-brief`
- 不需要改變 spec 目標 / 非目標
- 不需要重新定義 frozen contract 語意
- 不需要 rollout / rollback / release 決策
- 不需要啟用新 route 或改 Remote Config defaults
- verification command 明確

符合時，controller 應依序：

1. 從 `migration-map.md` 選出最低風險 bounded cohort
2. 建立下一個 batch id
3. 建立或更新該 batch 的 `task-brief`
4. 更新 `migration-map.md`
5. 更新 `controller-state.md` 的進度與目前位置
6. 直接進入 implement / review / verify

只有下列情況才停止：

- 候選 cohort 風險接近，且選擇會影響產品策略或 rollout
- 無法保守切出 bounded `task-brief`
- 需要碰 protected zone
- 需要修改 frozen contract 的語意
- 需要改變使用者可見行為或交易流程
- controller 無法說清楚 next concrete action

## Stop Gate

遇到下列任一情況，controller 必須停下並請人類決策：

- 下一個 batch 不是 `ready`
- scope 無法保守切出
- 需要改變重構目標 / 非目標
- 需要修改 frozen contract
- 需要放寬 protected zone
- 需要擴大 write scope 到 core / shared area
- 需要 rollout / rollback / release 策略決策
- `BLOCKED` 被分類為 human blocked，且沒有使用者選定 remediation option
- device verification `skipped`，且該 batch 的主要風險只能靠裝置驗證確認
- controller 無法說清楚 active batch、active task brief、next concrete action

禁止把下列事項當成 Stop Gate：

- 本輪完成
- commit 已完成
- worktree clean
- branch ahead base / origin 多個 commits
- 下一步只是 technical cohort selection
- 已更新 `controller-state.md` 或 `migration-map.md`

## Batch Boundary Behavior

完成 batch 後，controller 不應只回報「下一步是 X」就停止。必須先回答：

- 下一批是否 ready?
- 是否有 human gate?
- 是否能保守切出 task brief?
- 是否可在目前 session 繼續?

若答案是可以繼續，controller 應繼續。若答案是否，才輸出停止原因與需要的人類決策。

若答案是 `Human gate: No` 且 `Auto-continue: Yes`，controller 必須把「下一步」轉成實際 action。若下一步是 cohort selection，就執行 Technical Cohort Auto-Dispatch。

## Final Stop Guard

controller 在任何 final 回覆前，必須先完成 Final Stop Guard。沒有通過 guard，不得停止。

final 回覆必須明確列出：

- Human gate: Yes / No
- Auto-continue: Yes / No
- Stop gate reason:
- Next concrete action:

判斷規則：

- 若 `Human gate: No` 且 `Auto-continue: Yes`，禁止 final stop
- 若 `Stop gate reason` 為空，禁止 final stop
- 若 `Next concrete action` 是 technical cohort selection，禁止 final stop，改執行 Technical Cohort Auto-Dispatch
- 若只因本輪已完成、已 commit、驗證通過、worktree clean、或 branch ahead 多個 commits，禁止 final stop
- 若需要停止，必須說明是哪一個 Stop Gate 成立，以及需要使用者做哪個決策

下列 `Next concrete action` 一律視為 technical cohort selection：

- Next bounded row selection
- Next row cohort selection
- select next bounded row
- select next bounded row cohort
- select and dispatch next bounded row / contract batch
- row cohort selection
- next bounded cleanup batch

這些是 controller duty，不是 user decision。

## Safe Default

預設 autonomy 是 `standard`：

- 低風險、bounded、可回復的下一批，自動續做
- 中風險但可提出明確選項的情況，給 recommended option
- 技術性 `BLOCKED` 若符合 auto-selection criteria，自動選 recommended option，建立 remediation batch 並繼續
- 高風險或會改變 shared truth 的情況，等待人類確認

## Resume Target Rule

`controller-state.md` 的 `Resume Target` 不是停止理由。它只是在 context compact、跨 session、或遇到 Stop Gate 時提供恢復點。

若沒有 Stop Gate，controller 應把 resume target 轉成下一個 action，而不是停下來等使用者說「繼續」。

## Commit Boundary Rule

commit、驗證通過、worktree clean 只是 batch boundary，不是停止理由。完成 commit 後，controller 仍必須套用 Auto-Continue Gate 或 Technical Cohort Auto-Dispatch。只有 Stop Gate 成立時，才能停止並要求人類決策。
