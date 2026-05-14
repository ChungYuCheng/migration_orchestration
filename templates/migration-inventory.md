# Migration Inventory

> Long-chain migration required artifact. 用來維護全局 migration 地圖，不取代 `migration-map.md` 的批次歷史，也不取代單批 `task-brief.md`。

## Inventory Summary
- Total items:
- Done:
- In progress:
- Planned:
- Needs recon:
- Deferred:
- Blocked:
- Route gate:
- Device checkpoint:
- Cleanup:
- Last updated batch:

## Items

主表保持輕量。它是給人類閱讀的進度表面，也是 controller 選下一個 item 的第一層依據。

| Item | Type | Status | Next action | Gate |
|---|---|---|---|---|
|  | display / recon / bridge / route / device / cleanup | planned / in_progress / needs_recon / done / deferred / blocked / route_gate / cleanup |  | none / human decision / device checkpoint / route rollout / protected zone |

## Suggested Execution Sequence

只有剩餘工作會跨多個 future batches 時才使用本節。每一行保持簡短。

1. TBD

## Item Details

只有當某個 item 的 dependency、risk 或 verification 無法從主表看懂時，才在這裡補細節。

- Item:
  - Cohort:
  - Risk: low / medium / high
  - Dependency:
  - Suggested order: early / middle / late / final
  - Verification:
  - Notes:

## Cohort Classification

### Static / Display
-

### Data Formatting
-

### Action Bridge
-

### Recommendation / Paging / Impression
-

### Navigation / Route / External Integration
-

### Purchase / Checkout / Spec Selection
-

### Player / Media / WebView
-

### Cleanup / Route Readiness
-

## Verification Checkpoints

| Checkpoint | Trigger | Required verification | Status | Notes |
|---|---|---|---|---|
|  |  |  | planned / done / skipped / blocked |  |

## Inventory Update Rules

- Update this file when discovery identifies a new cohort.
- Update this file when a batch changes item status.
- Keep `Items` rows short; move risk, dependency, verification, and long notes to `Item Details` only when needed.
- Prefer `needs_recon` over guessing order when risk is unknown.
- Do not mark an item `done` unless `migration-map.md` has the completed batch and verification result.
