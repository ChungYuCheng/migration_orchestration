# Sub-agent Dispatch Policy

這份文件定義何時可以把角色分派給 sub-agent，何時必須留在 main controller 內執行。

## 核心原則

> Main agent is always the controller. Sub-agents handle bounded work only.

角色分工不代表一定要開 sub-agent。小型或低風險工作可以由 main controller 依序扮演 discovery、dispatcher、implementer、reviewer。只有當任務 bounded、可隔離、輸入輸出明確時，才分派 sub-agent。

## 預設模式

- main agent 永遠是 controller
- controller 維護 shared truth
- controller 決定是否分派 sub-agent
- sub-agent 只處理被授權的 bounded task
- sub-agent 回報結果後，由 controller 整合

## 適合分派 sub-agent 的工作

- Full Discovery 中可獨立盤點的模組 / 子系統
- 高風險 batch 前的局部 recon
- 已有 frozen contracts 和明確 `task-brief` 的 implementation
- 針對特定 diff 的 independent review
- device verification 這種純驗證工作

## 不適合分派 sub-agent 的工作

下列工作必須留在 main controller：

- 定義或修改 `spec.md`
- 定義或修改 `contracts.md`
- 定義或修改 `migration-map.md`
- 定義 protected zones
- 決定 migration strategy
- 決定 rollout / rollback / release strategy
- blocked escalation 的選項設計與最終 routing
- continuation policy 的 final routing
- human gate 判斷
- 寫入 `controller-state.md`

sub-agent 可以提出建議，但不能直接寫入 shared truth。

## 分派前檢查

分派 sub-agent 前，controller 必須確認：

1. task 目標明確
2. write scope 明確
3. read-only scope 明確
4. protected zones 明確
5. frozen contracts 足夠
6. verification command 明確
7. blocked conditions 明確
8. sub-agent 不需要重新理解大半個 repo

任一項不成立，不要分派 implementer；先回 dispatcher、recon 或 controller 補 shared truth。

## Sub-agent Input Contract

每個 sub-agent 都必須收到最小必要輸入：

```md
## Role
- Discovery / Recon / Implementer / Reviewer / Device Verification

## Goal
-

## Inputs
- task-brief:
- contracts excerpt:
- migration-map excerpt:
- allowed write scope:
- read-only scope:
- protected zones:
- verification:

## Hard Rules
- 不修改 shared truth
- 不改變 task 目標
- 不擴大 scope
- 需要碰 protected zone 時回報 BLOCKED
- 驗證失敗時只回報事實

## Output
- status:
- files changed / inspected:
- tests run:
- assumptions:
- blockers:
- recommendation:
```

## Sub-agent Output Routing

sub-agent 完成後一律回 controller。controller 再決定：

- 更新 shared truth
- 回 implementer 修正
- 回 dispatcher 重切
- 回 recon 補調查
- 執行 blocked resume protocol
- 執行 continuation policy
- 停在人類 gate

sub-agent 不直接互相派工，不直接更新其他 agent 的任務。

## 平行化限制

可以平行分派多個 sub-agent，只有在：

- write scope 不重疊
- 不依賴同一個未凍結 contract
- 不修改 shared interface / schema / cross-cutting config
- controller 有能力整合結果
- 任一 sub-agent 失敗不會讓其他 task 的前提失效

若 shared boundary 還在變，禁止平行派 implementer。

## 單 agent 模式

若工作量不大、scope 清楚、或 sub-agent 工具不可用，controller 可以在 main agent 內依序扮演各角色。此時仍必須遵守相同的角色邊界：

- 扮演 discovery 時不實作
- 扮演 dispatcher 時不改 shared truth
- 扮演 implementer 時不改 contracts / migration-map
- 扮演 reviewer 時不擴大需求

單 agent 模式不是放寬權限，只是角色由同一個 agent 依序執行。
