# Task Brief: [Batch Name]

## Goal
- Describe the concrete behavior result this task must achieve.

## Why This Task Exists
- State where this task sits in the migration.
- State what completed work it depends on.
- State what later work this task unlocks.

## Responsibility Scope
- You own:
- You do not own:

## File Scope

### You may modify
- Prefer directory-level scope.

### You may read but not modify
- List nearby dependencies or shared areas needed for reference.

### Do not modify
- List protected zones, frozen contracts, and explicit red lines.

## Required Context

### Stable Inputs
- List the frozen contracts, existing adapters, completed batches, or assumptions that can be trusted.

### Expected Implementation Path
- State the preferred pattern, adapter, or migration path to follow.
- State what route should be reused instead of reinvented.

### Known Traps
- List the most likely mistakes or hidden couplings.
- List "do not do X even if it looks reasonable" constraints.

## Acceptance Criteria
- List the minimum behavior and boundary outcomes required for completion.

## Verification
- `command`
  - explain what this command verifies

## Blocked Conditions
Stop and report only if:
- this task cannot be completed without modifying a protected zone
- this task cannot be completed without changing a frozen contract
- this brief conflicts with the current repository state
- there is no viable local verification path for the required behavior

## Relevant Excerpts
Include this section only when the implementer would otherwise likely misuse a contract, miss a required pattern, or misread a shared type.

### Pattern Excerpt
```text
Optional
```

### Contract Excerpt
```text
Optional
```

### Shared Type Excerpt
```text
Optional
```
