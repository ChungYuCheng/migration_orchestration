# Continuation Policy

這份文件定義 controller 在完成一個 batch 後，何時應該自動繼續下一批，何時必須停下等待人類決策。

## 核心原則

> Stop only at human gates, not at every batch boundary.

若下一批已 ready、shared truth 穩定、scope 可保守切出，controller 不應只留下 resume target 就停止。應自動建立下一個 task-brief，更新進度條列，並繼續推進。

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

## Batch Boundary Behavior

完成 batch 後，controller 不應只回報「下一步是 X」就停止。必須先回答：

- 下一批是否 ready?
- 是否有 human gate?
- 是否能保守切出 task brief?
- 是否可在目前 session 繼續?

若答案是可以繼續，controller 應繼續。若答案是否，才輸出停止原因與需要的人類決策。

## Safe Default

預設 autonomy 是 `standard`：

- 低風險、bounded、可回復的下一批，自動續做
- 中風險但可提出明確選項的情況，給 recommended option
- 技術性 `BLOCKED` 若符合 auto-selection criteria，自動選 recommended option，建立 remediation batch 並繼續
- 高風險或會改變 shared truth 的情況，等待人類確認

## Resume Target Rule

`controller-state.md` 的 `Resume Target` 不是停止理由。它只是在 context compact、跨 session、或遇到 Stop Gate 時提供恢復點。

若沒有 Stop Gate，controller 應把 resume target 轉成下一個 action，而不是停下來等使用者說「繼續」。
