---
name: migration-orchestration
description: Use when 進行複雜重構或漸進式 migration，且需要多個 agent、凍結共享邊界、分批遷移 callers，或在 context window 有限時維持穩定協調
---

# Migration Orchestration 重構協調

## 總覽

這個 skill 用來協調複雜重構，不是用來直接實作某一塊功能。核心原則是：先用最低成本收斂 shared reality，再分批遷移；只有一個 controller 維護 shared truth，其他 agent 一律透過 artifact 協作。

## 何時使用

- 重構跨越多個模組或子系統
- 新舊實作需要共存一段時間
- implementer 若直接開工會需要過多全域 context
- 多個 agent 容易互踩 shared boundary
- 需要 phased migration、caller migration、adapter 過渡層

不要用在：

- 單一模組、單一檔案、可直接 bounded implementation 的任務
- 不需要 staged rollout 或 shared contract 控制的局部改動

## 核心規則

- 永遠只有一個 `Controller / Master Agent`
- 只有 controller 可以更新 shared truth artifacts
- 複雜專案先做 `Discovery Triage`，只有必要時才進 `Full Discovery`
- `Recon Agent` 只調查，不實作
- `Dispatcher / Task Planner` 只負責切 task 與組 context，不修改 shared truth
- `Implementer Agent` 只在授權 scope 內實作
- `Reviewer Agent` 只驗證，不重寫需求
- 調研的完成標準是足夠支撐決策，不是追求資訊完整
- shared interface、schema、cross-cutting config 一律序列化處理
- caller migration 只能在 contracts 凍結後平行化
- worker 若需要碰 protected zone，必須回報 `BLOCKED`

## 快速流程

1. 先做 `Discovery Triage`，快速判斷是否真的需要 full discovery
2. 只有 triage 顯示複雜度夠高時，才做 `Full Discovery`
3. 補 baseline 與過渡層，讓新舊路徑可共存
4. 凍結 contracts、shared types、protected zones
5. 用 `templates/` 建立 `spec`、`contracts`、`migration-map`
6. 由 dispatcher 根據 triage / discovery 結果切出 migration batches、task briefs
7. 只有高風險 batch 才先派 recon，再 implement
8. 以 `task-brief` 作為 implementer 的主要工作入口
9. reviewer 驗證 scope、contract、tests
10. controller 更新 `migration-map` 後才派下一批

## 使用方式

- 觸發後先讀本檔，再依任務階段只讀必要的 `references/` 檔案
- 先完成 triage，再決定是否需要 discovery、recon、dispatcher 或多 implementer
- 預設用單一 `task-brief` 作為 implementer 入口
- 只有在 task-brief 放不下必要上下文，且 implementer 會因此重讀大量 repo 時，才建立額外 `context-packet`
- 不要在 contracts / protected zones 未凍結前平行派 implementer

## 必讀檔案

- 角色與權限：`references/role-boundaries.md`
- Discovery 規則：`references/discovery-phase.md`
- 協調流程：`references/controller-flow.md`
- Failure 與升級：`references/failure-and-escalation.md`
- 派工規則：`references/dispatching-rules.md`
- 平行化規則：`references/parallelization-policy.md`
- Prompts 用法：`references/prompt-usage.md`

## 必備模板

- `templates/spec.md`
- `templates/contracts.md`
- `templates/migration-map.md`
- `templates/task-brief.md`

## 可選協作格式

以下格式預設不需要落地到 repo。只有在決策複雜、需要交接、未來還會引用 reasoning，或 controller 明確要求保留時，才寫成檔案：

- `templates/discovery-report.md`
- `templates/dispatch-plan.md`
- `templates/context-packet.md`
- `templates/recon-report.md`
- `templates/implementation-report.md`
- `templates/review-report.md`

## 常見錯誤

- contract 還沒凍結就平行派 implementer
- 把 discovery / recon 當成固定義務，而不是決策工具
- 沒做 discovery 就直接切 task
- dispatcher 在沒有 stable contracts 的情況下派工
- 把整個 repo 丟給 worker，而不是 bounded task-brief
- 讓 implementer 自行修改 shared truth
- 把 migration task 切成零碎 patch，而不是完整 migration slice
- 沒更新 `migration-map` 就開始下一批
