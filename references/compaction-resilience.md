# Compaction Resilience

這份文件定義 context compact、thread resume、或 controller 交接時，如何避免大型重構流程中斷或品質下降。

## 核心原則

> Chat context is working memory, not source of truth.

所有會影響下一步決策的資訊，都必須落在 shared truth artifacts 或 controller state，不得只存在聊天上下文。

## Compact vs Clear

compact 是同一 thread 內的短中期恢復機制。clear / 新 session 是受控交接機制，不是重置流程。

若經歷多次 compact 後出現 decision drift、上下文污染、摘要過長、或 controller 開始依賴聊天記憶，先補 artifacts，再讀 `clear-handoff-policy.md` 判斷是否進入 Clear Handoff Gate。

## Compaction Gate

在下列時機，controller 必須先更新可恢復狀態，再繼續派工：

- context window 接近上限
- batch 完成後準備切下一批
- contracts、protected zones、migration rules 有變更
- task 被 `BLOCKED` 或 reviewer 要求升級
- controller 要把工作交給新 thread、新 agent、或下次 session

Compaction gate 的最低輸出：

- `migration-map.md` 更新到最新 batch 狀態
- 當前 `task-brief` 能獨立支撐 implementer 執行
- 若下一步仍依賴尚未完成的推理，建立或更新 `controller-state.md`
- `controller-state.md` 頂部的進度條列與目前位置已更新

若這次交接會建議 clear / 新 session，還必須補上 Clear Handoff Package，並確認 Clear Handoff Gate 通過。

## Resume Gate

compact 或 resume 後，controller 不得直接繼續寫 code。必須先讀：

1. `spec.md`
2. `contracts.md`
3. `migration-map.md`
4. 當前 `task-brief`
5. `controller-state.md`（如果存在）

讀完後先確認：

- current phase
- current batch
- frozen decisions
- protected zones
- open risks
- next concrete action

如果這些資訊不足，先補 shared truth 或回到 recon / dispatcher，不要讓 implementer 自行推測。

clear / 新 session 後也適用 Resume Gate，且必須額外確認 handoff package 與 repo 現況一致。

## Controller State

`controller-state.md` 是短期恢復包，不是第二份 migration plan。

只在下列情況使用：

- context compact 風險高
- migration 跨多個工作階段
- 下一步依賴最近的 controller reasoning
- 多個 batch 同時處於 ready / blocked / review 狀態

不要放：

- 完整 repo 背景
- discovery / recon 原文
- 已經穩定寫入 `spec`、`contracts`、`migration-map` 的內容
- implementation detail

應該放：

- 目前 phase
- 目前 batch
- 最新 frozen decisions
- protected zones
- unresolved risks
- 下一個具體動作
- 必要時放輕量進度摘要，但不要放完整 dashboard

## Task Brief Self-Containment

若 compact 後 implementer 品質下降，優先檢查 `task-brief`，而不是要求 agent 記住更多聊天內容。

好的 `task-brief` 必須讓 implementer 不靠聊天歷史也能安全工作：

- 目標與非目標清楚
- file scope 清楚
- frozen contracts 摘要足夠
- protected zones 清楚
- expected pattern 明確
- verification command 明確
- blocked conditions 明確

## Failure Signals

出現下列情況時，代表 compaction resilience 不足：

- resume 後 controller 不知道下一步
- implementer 需要重新探索大半個 repo
- reviewer 不知道 task 邊界
- frozen contract 被重新解釋
- migration-map 和 repo 現況矛盾
- 同一個 batch 在 compact 前後被切成不同 scope
- 多次 compact 後舊假設開始干擾目前 batch

處理順序：

1. 停止派工
2. 更新 `migration-map.md`
3. 補強當前 `task-brief`
4. 必要時建立或更新 `controller-state.md`
5. 若仍有上下文污染，執行 Clear Handoff Gate
6. 再重新判斷是否可以 implement
