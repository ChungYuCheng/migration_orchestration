# Device Verification Items

> Optional artifact. 只有 AndroidEC 或具備同等裝置驗證環境，且 task 影響 UI / navigation / user flow 時才使用。預設不落地，除非 controller 需要保留驗證交接內容。

## Target
- Project:
- Variant: offline
- Target screen:
- Navigation path:

## Items

### Item 1
- id:
- goal:
- expected:
- steps:

### Item 2
- id:
- goal:
- expected:
- steps:

## Result
- status: pass / fail / skipped
- failures:
  - item_id:
    expected:
    actual:
- notes:

## Controller Decision
- pass -> batch done candidate
- fail -> return to implementer / escalate review issue
- skipped -> human decision / retry later / accept verification gap
