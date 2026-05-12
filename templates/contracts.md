# 契約摘要

## 穩定介面
### 介面：
- Responsibility:
- Methods:
- Input constraints:
- Output guarantees:
- Error semantics:

## 共享型別
### 型別：
- Fields:
- Nullability:
- Backward compatibility notes:

## 保護區域
- Only controller may modify:
  -
- Worker must not modify:
  - public API signatures
  - shared event names
  - DB schema

## Migration 規則
- Old and new path must coexist until all callers migrate
- Do not remove fallback logic in migration tasks
- Do not rename shared types without explicit migration task
