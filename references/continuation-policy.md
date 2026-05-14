# Continuation Policy

這份文件定義 controller 在完成一個 batch 後，何時應該自動繼續下一批，何時必須停下等待人類決策。

## 核心原則

> Stop only at human gates, not at every batch boundary.

若下一批已 ready、shared truth 穩定、scope 可保守切出，controller 不應只留下 resume target 就停止。應自動建立下一個 task-brief，更新進度條列，並繼續推進。

`Auto-continue: Yes` 是執行狀態，不是停止狀態。controller 不得在 final 回覆中同時宣告 `Auto-continue: Yes` 又停下等待使用者。

## Auto-Continue Gate

同時符合下列條件時，controller 應自動續做：

- `migration-map.md` 中有下一個 `ready` batch
- 或 `migration-inventory.md` 中有下一個 `planned` / `needs_recon` item 可保守轉成 bounded batch
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

1. 優先從 `migration-inventory.md` 選出下一個可行 item；若不存在，再從 `migration-map.md` 選出下一個 ready batch
2. 建立或更新該 batch 的 `task-brief`
3. 更新 `migration-map.md` 與必要的 `migration-inventory.md` 狀態
4. 更新輕量進度條列
5. 若工作跨 session 或可能 compact，更新 `controller-state.md`
6. 直接進入 implement / review / verify 流程

## Inventory Selection Order

中大型 / 長鏈 migration 選下一批時，controller 應優先依 `migration-inventory.md` 排序，不要只靠聊天脈絡或最近 batch history。

排序規則：

1. `Suggested Execution Sequence` 中第一個尚未完成且可 bounded 的 item
2. `Items` 主表中 `Next action` 明確、`Gate` 不是 human decision / route rollout 的 item
3. `needs_recon` 且可 bounded 的 item
4. `planned`、低到中風險、dependency 已滿足的 item
5. `migration-map.md` 已標成 `ready` 的 batch
6. `controller-state.md` 的 next concrete action

若長鏈 migration 尚未有 `migration-inventory.md`，下一步應是建立 inventory backfill batch。這不是 Stop Gate，也不是重新 Full Discovery；controller 應從既有 `migration-map.md`、`controller-state.md`、recon reports、task briefs 與已完成 commits 補一份短表格，然後繼續選下一批。

## Technical Direction Auto-Selection

若已經沒有剩餘低風險 body snapshot，但 `migration-inventory.md` 仍有 bridge / behavior / route readiness / device checkpoint 項目，controller 不得把「選下一階段 scope」本身當成人類決策。

適用情境：

- select next phase scope
- choose next technical direction
- choose bridge order
- action bridge vs scroll bridge vs analytics / video / WebView bridge
- classify next protected behavior slice
- choose next inventory sequence item
- select next route readiness prerequisite

同時符合下列條件時，controller 必須自行選擇：

- `migration-inventory.md` 有 `Suggested Execution Sequence`，或 `Items` 主表有 `Next action`
- 下一步可以先切成 bounded recon、scope brief、debug-only implementation、或 narrow implementation
- 不需要改 Remote Config default、不需要 rollout / rollback 決策
- 不需要接受產品語意差異
- 不會立即啟用 production route
- 可以先定義 device verification items，而不是立即要求使用者判斷

選擇規則：

1. 優先選 sequence 中第一個未完成 item
2. 若沒有 sequence，優先選能降低後續不確定性的 bounded recon
3. bridge work 優先順序通常是 action bridge scope、narrow action bridge、scroll/deferred-load bridge scope、narrow deferred-load bridge、analytics/impression recon、video/WebView behavior recon、device checkpoints、route readiness
4. `Gate` 若是 device checkpoint，先建立測試項目清單與 prerequisite batch；不要直接停下問使用者

只有下列情況才可變成 human gate：

- 需要改 Remote Config default、rollout / rollback 或 release strategy
- 需要接受明確產品語意差異
- 無法保守切成 bounded recon 或 narrow implementation
- 多個方案風險接近，且選擇會改變產品行為、交易流程或公開 route
- device verification 已有測試項目清單但結果 skipped / failed，且該風險只能靠裝置確認

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
- choose next inventory sequence item
- select next phase scope when inventory provides sequence

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

1. 從 `migration-inventory.md` 選出最低風險 bounded cohort；若 inventory 缺失，先建立 inventory backfill batch
2. 建立下一個 batch id
3. 建立或更新該 batch 的 `task-brief`
4. 更新 `migration-map.md` 與必要的 `migration-inventory.md` 狀態
5. 更新 `controller-state.md` 的進度與目前位置
6. 直接進入 implement / review / verify

只有下列情況才停止：

- 候選 cohort 風險接近，且選擇會影響產品策略或 rollout
- 無法保守切出 bounded `task-brief`
- 需要碰 protected zone
- 需要修改 frozen contract 的語意
- 需要改變使用者可見行為或交易流程
- controller 無法說清楚 next concrete action

## Technical Recon Auto-Continue

若下一步是 bounded recon、inventory 或 slice planning，controller 不得把這當成 human gate。高耦合或局部未知數通常代表要先 recon，不代表要停下等使用者。

適用情境：

- continue inventory
- continue recon
- inspect remaining rows
- inspect remaining high-coupling rows
- classify remaining high-coupling rows
- inspect `YouMayLikeSectionViewData`
- inspect `RecommendGridViewData`
- cut loaded snapshot slice
- cut read-only snapshot slice
- create bounded recon batch
- create loaded snapshot task brief

同時符合下列條件時，controller 必須自行推進：

- `Human gate: No`
- `Auto-continue: Yes`
- recon scope 可限制在明確檔案、row、caller 或 contract 區域
- recon 目標是產生 bounded task-brief、prerequisite batch 或 Stop Gate 判斷
- 不需要產品策略、rollout、route enablement 或 frozen contract 語意決策
- 不需要先碰 protected zone

符合時，controller 應依序：

1. 建立 bounded recon task 或直接執行 bounded recon
2. 盤點 read-only / loaded snapshot 可行性
3. 標出 protected zones、hidden coupling、verification path
4. 若可保守切出，建立下一個 batch id 與 `task-brief`
5. 更新 `migration-map.md`、必要的 `migration-inventory.md` 狀態與 `controller-state.md`
6. 直接進入 implement / review / verify

只有下列情況才停止：

- recon 發現需要產品策略、曝光、排序、paging lifecycle、交易流程或 rollout 決策
- recon 需要修改 protected zone 才能繼續
- recon 無法保守切出 bounded task-brief
- 候選方案風險接近且會改變使用者可見行為
- controller 無法說清楚 recon scope、expected output 或 verification path

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
- 下一步只是 technical direction / bridge sequencing / scope selection，且 inventory 可排序
- 下一步只是 bounded recon / inventory / slice planning
- 長鏈 migration 缺少 `migration-inventory.md`，但可由既有 artifacts backfill
- item 被標成 protected behavior / behavior bridge，但下一步可先做 bounded recon、scope brief 或 narrow implementation
- 已更新 `controller-state.md` 或 `migration-map.md`

## Batch Boundary Behavior

完成 batch 後，controller 不應只回報「下一步是 X」就停止。必須先回答：

- 下一批是否 ready?
- 是否有 human gate?
- 是否能保守切出 task brief?
- 是否可在目前 session 繼續?

若答案是可以繼續，controller 應繼續。若答案是否，才輸出停止原因與需要的人類決策。

若答案是 `Human gate: No` 且 `Auto-continue: Yes`，controller 必須把「下一步」轉成實際 action。若下一步是 technical direction / bridge sequencing / scope selection，就執行 Technical Direction Auto-Selection；若下一步是 cohort selection，就執行 Technical Cohort Auto-Dispatch；若下一步是 bounded recon / inventory / slice planning，就執行 Technical Recon Auto-Continue。

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
- 若 `Next concrete action` 是 technical direction / bridge sequencing / scope selection，禁止 final stop，改執行 Technical Direction Auto-Selection
- 若 `Next concrete action` 是 bounded recon / inventory / slice planning，禁止 final stop，改執行 Technical Recon Auto-Continue
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
- choose next technical direction
- choose bridge order
- select next phase scope
- choose next inventory sequence item

這些是 controller duty，不是 user decision。

下列 `Next concrete action` 一律視為 bounded recon / inventory / slice planning：

- continue inventory
- continue recon
- inspect remaining rows
- inspect `YouMayLikeSectionViewData`
- inspect `RecommendGridViewData`
- cut loaded snapshot slice
- create bounded recon batch
- classify remaining high-coupling rows

這些是 controller duty；只有 recon 結果碰到 Stop Gate 條件時才停。

## Safe Default

預設 autonomy 是 `standard`：

- 低風險、bounded、可回復的下一批，自動續做
- 中風險但可提出明確選項的情況，給 recommended option
- 技術性 `BLOCKED` 若符合 auto-selection criteria，自動選 recommended option，建立 remediation batch 並繼續
- 技術性 recon / inventory 若符合 auto-continue criteria，自動執行 bounded recon 或派 recon subAgent
- 技術方向 / bridge sequencing 若 inventory 可排序，自動選下一項並建立 bounded recon、scope brief 或 narrow implementation
- 高風險或會改變 shared truth 的情況，等待人類確認

## Resume Target Rule

`controller-state.md` 的 `Resume Target` 不是停止理由。它只是在 context compact、跨 session、或遇到 Stop Gate 時提供恢復點。

若沒有 Stop Gate，controller 應把 resume target 轉成下一個 action，而不是停下來等使用者說「繼續」。

## Commit Boundary Rule

commit、驗證通過、worktree clean 只是 batch boundary，不是停止理由。完成 commit 後，controller 仍必須套用 Auto-Continue Gate、Technical Cohort Auto-Dispatch 或 Technical Recon Auto-Continue。只有 Stop Gate 成立時，才能停止並要求人類決策。
