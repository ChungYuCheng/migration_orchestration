# Migration 地圖

> `migration-map.md` 記錄批次歷史與狀態。中大型 / 長鏈 migration 的全局 item 清單、剩餘範圍、risk 與 verification checkpoint 應放在 `migration-inventory.md`。

## 舊路徑 -> 目標路徑對應
- LegacyThing -> NewThing

## 批次狀態
- Batch A:
  - Status: planned
  - Owner:
  - Depends on:
  - Dependency Type: hard / soft
  - Verification:
  - Last Outcome:
  - Notes:
- Batch B:
  - Status: planned
  - Owner:
  - Depends on:
  - Dependency Type: hard / soft
  - Verification:
  - Last Outcome:
  - Notes:
- Batch C:
  - Status: planned
  - Owner:
  - Depends on:
  - Dependency Type: hard / soft
  - Verification:
  - Last Outcome:
  - Notes:

## 剩餘 Legacy 相依
- 

## Cleanup 關卡
- Remove adapters only after all caller batches complete
- Remove legacy-only code only after all reads and writes have migrated
