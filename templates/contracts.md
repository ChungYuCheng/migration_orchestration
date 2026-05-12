# Contracts Brief

## Stable Interfaces
### Interface:
- Responsibility:
- Methods:
- Input constraints:
- Output guarantees:
- Error semantics:

## Shared Types
### Type:
- Fields:
- Nullability:
- Backward compatibility notes:

## Protected Zones
- Only controller may modify:
  -
- Worker must not modify:
  - public API signatures
  - shared event names
  - DB schema

## Migration Rules
- Old and new path must coexist until all callers migrate
- Do not remove fallback logic in migration tasks
- Do not rename shared types without explicit migration task
