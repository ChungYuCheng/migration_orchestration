# 角色與權限邊界

這份文件定義 migration orchestration 中各角色的責任、可修改範圍、禁止事項，以及必須遵守的規則文件。

## 拓撲

```text
User
  |
Controller / Master Agent
  |----> Recon Agent
  |<---- Recon Report
  |
  |----> Dispatcher / Task Planner
  |<---- Dispatch Plan + Task Briefs + Context Packets
  |
  |----> Implementer Agent
  |<---- Implementation Report
  |
  |----> Reviewer Agent
  |<---- Review Report
```

規則：

- agent 不直接自由對話
- 所有正式交接都經過 controller
- 只有 controller 可以更新 shared truth

## Controller / Master Agent

責任：

- 維護 `spec`、`contracts`、`migration-map`
- 決定是否需要 upfront discovery
- 定義 protected zones
- 決定哪些任務先 recon
- 切 task brief 與 context packet
- 判斷哪些批次可以平行
- 整合 recon、implementation、review 結果

可修改：

- `templates` 複製出來的工作文件
- `migration-map`
- task briefs
- context packets

禁止：

- 在 shared boundary 尚未穩定前派多個 implementer 平行改 core files
- 把未收斂的架構問題直接丟給 implementer

必須遵守：

- `spec`
- `contracts`
- `migration-map`

## Dispatcher / Task Planner

責任：

- 根據 discovery / recon 結果切 migration batches
- 產出 `task-briefs/*.md`
- 產出 `context-packet`
- 標示 dependencies、verification、protected zone constraints
- 判斷 task 是否足夠 bounded 以供 implementer 執行

可修改：

- 由 controller 授權的 task briefs
- 由 controller 授權的 context packets
- `Dispatch Plan`

禁止：

- 不修改 `spec`
- 不修改 `contracts`
- 不修改 `migration-map` 的事實狀態
- 不放寬 protected zones
- 不重新設計 migration strategy

必須遵守：

- `spec`
- `contracts`
- `migration-map`
- discovery / recon reports

## Recon Agent

責任：

- 做前期 discovery 或單一批次 recon
- 盤點依賴
- 找 hidden coupling
- 評估 protected zone 風險
- 建議最小 migration slice
- 建議驗證命令

可修改：

- 無 code 寫入權
- 只輸出 `Recon Report`

禁止：

- 不修改 production code
- 不修改 shared truth
- 不重設計 contract
- 不直接指揮 implementer

必須遵守：

- `spec`
- `contracts`
- `migration-map`

## Implementer Agent

責任：

- 在授權 scope 內完成 implementation
- 補 focused tests
- 跑指定驗證
- 回報 assumptions、風險、blocked reasons

可修改：

- `task-brief` 明確授權的 code 與 tests

禁止：

- 不改 protected zones
- 不改 frozen contracts
- 不做 unrelated cleanup
- 不更新 `migration-map`
- 不重定義任務目標

必須遵守：

- `task-brief`
- `context-packet`
- `contracts`

可回報狀態：

- `DONE`
- `DONE_WITH_CONCERNS`
- `NEEDS_CONTEXT`
- `BLOCKED`

## Reviewer Agent

責任：

- 驗證是否符合 task brief
- 驗證是否違反 contract
- 驗證測試與驗證是否足夠
- 要求 implementer 補修正

可修改：

- 不直接修改 code
- 只輸出 `Review Report`

禁止：

- 不重寫 task
- 不擴大需求
- 不修改 shared truth

必須遵守：

- `task-brief`
- `contracts`
- implementation diff

## Shared Truth Artifacts

### `spec.md`

- 定義目標、約束、非目標、風險
- 只有 controller 可修改

### `contracts.md`

- 定義 stable interfaces、shared types、protected zones、migration rules
- 預設只有 controller 可修改

### `migration-map.md`

- 記錄批次狀態、legacy -> target 對應、cleanup gates
- 只有 controller 可修改

### `context-packet.md`

- 提供 worker 最小必要上下文
- 由 controller 或 dispatcher 在 controller 授權下建立與更新

### `task-briefs/*.md`

- 定義單一 migration slice 的目標、scope、驗收、驗證
- 由 controller 或 dispatcher 在 controller 授權下建立與更新
