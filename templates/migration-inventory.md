# Migration Inventory

> Long-chain migration required artifact. 用來維護全局 migration 地圖，不取代 `migration-map.md` 的批次歷史，也不取代單批 `task-brief.md`。

## Inventory Summary
- Total items:
- Done:
- Planned:
- Needs recon:
- Deferred:
- Blocked:
- Route / device checkpoint:
- Last updated batch:

## Items

| Item | Cohort | Type | Risk | Dependency | Suggested order | Verification | Status | Stop gate trigger | Notes |
|---|---|---|---|---|---|---|---|---|---|
|  |  |  | low / medium / high |  | early / middle / late / final | unit / compile / device checkpoint / manual decision | planned / needs_recon / done / deferred / blocked / route_gate / cleanup |  |  |

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
- Keep rows short; details belong in `migration-map.md`, recon reports, or task briefs.
- Prefer `needs_recon` over guessing order when risk is unknown.
- Do not mark an item `done` unless `migration-map.md` has the completed batch and verification result.
