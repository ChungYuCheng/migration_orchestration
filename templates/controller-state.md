# Controller State

> Optional artifact. 用於 context compact、resume、跨 session 交接。這不是第二份 migration plan，只記錄恢復下一步決策所需的最小狀態。

## 進度條列
> Source: `migration-inventory.md` top-level `Items`. Do not append every GD-Bxxx batch here.

- [ ] Top-level inventory item
- [-] In-progress inventory item
- [?] Needs-recon inventory item
- [!] Blocked / human-gate inventory item
- [x] Done inventory item

## 目前位置
- 目前 batch:
- 下一步:
- Human gate: Yes / No
- Auto-continue: Yes / No
- Stop gate reason:

## 批次狀態
| Batch | 狀態 | 下一步 |
|---|---|---|
|  |  |  |

## Current Phase
- Discovery Triage / Discovery / Stabilize / Freeze Contracts / Dispatch Planning / Execute and Review:

## Current Batch
- Batch ID:
- Status: planned / needs_recon / ready / blocked / done
- Dependency Type: hard / soft
- Depends on:
- Verification:

## Last Confirmed Shared Truth
- Spec:
- Contracts:
- Migration map:
- Migration inventory:
- Task brief:

## Frozen Decisions
- Decision:
  - Reason:
  - Source:

## Protected Zones
- Do not modify:
- Escalate if task requires:

## Open Risks
- Risk:
  - Impact:
  - Next action:

## Blockers
- Blocker:
  - Reason:
  - Owner:
  - Escalation path:
  - Blocked type: technical / human
  - Decision mode: auto_selected / user_selected
  - Chosen option:
  - Remediation batch:
  - Resume target:
  - Return condition:
  - Resume status:

## Next Concrete Action
- Do next:
- Do not do yet:
- Read before continuing:
- Auto-continue eligible: Yes / No
- Technical cohort auto-dispatch: Yes / No
- Technical direction auto-selection: Yes / No
- Technical recon auto-continue: Yes / No
- Selected next cohort:
- Selected technical direction:
- Recon scope:
- Next batch id:
- Stop gate reason:
- Final stop guard passed: Yes / No

## Inventory State
- Inventory file:
- Last updated batch:
- Remaining planned:
- Remaining needs_recon:
- Remaining deferred:
- Remaining blocked:
- Next inventory item:
- Next verification checkpoint:

## Clear Handoff
- Clear handoff recommended: Yes / No
- Gate status: not_checked / passed / blocked
- Reason:
- Repo / worktree:
- Branch:
- Base:
- Latest commit:
- Git status:
- Completed batch:
- Active / next batch:
- Human gate: Yes / No
- Auto-continue after resume: Yes / No
- New session first action:

## Resume Checklist
- [ ] Read `spec.md`
- [ ] Read `contracts.md`
- [ ] Read `migration-map.md`
- [ ] Read `migration-inventory.md` if this is a medium / large / long-chain migration
- [ ] Read current `task-brief`
- [ ] Confirm this state still matches the repository
