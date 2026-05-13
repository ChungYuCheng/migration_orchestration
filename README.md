# migration-orchestration

`migration-orchestration` 是一個用來協調複雜重構與漸進式 migration 的 skill。

它的設計重點是：

- 由單一 `controller` 維護 shared truth
- 先用最低成本做 triage，必要時再做 discovery / recon
- 以 `task-brief` 作為 implementer 的單一工作入口
- 預設只持久化 `spec.md`、`contracts.md`、`migration-map.md`、`task-brief.md`
- 在 context compact 或跨 session 時用 `controller-state.md` 保存最小恢復狀態
- 多次 compact 後可用 Clear Handoff Gate 受控切到新 session，避免舊上下文污染決策
- 在 `BLOCKED` 後用有限選項與 resume target 回到原 migration plan
- 技術性 `BLOCKED` 可由 controller 自動選 recommended option 並建立 remediation batch
- 對開發者只顯示輕量進度條列，不預設建立 dashboard 文件
- AndroidEC UI / navigation / user flow 風險才啟用 device verification
- 下一批 ready 且沒有 human gate 時自動續做，不停在 resume target
- controller 停下或交接時，在回覆與 `controller-state.md` 頂部同步顯示輕量進度
- main agent 永遠是 controller；sub-agent 只處理 bounded 且可隔離的工作
- 其他 reports 預設只作為協作格式，必要時才落地

## 結構

- `SKILL.md`: 主 skill 定義
- `references/`: 流程規則、角色邊界、failure / escalation 等參考文件
- `prompts/`: controller、discovery、dispatcher、implementer、reviewer prompts
- `templates/`: `spec`、`contracts`、`migration-map`、`task-brief`、`controller-state`、`blocked-escalation` 與可選報告模板

## 使用方式

這個 repo 是單一 skill repo，root 本身就是 skill 目錄。

如果你的工具支援從 GitHub repo 安裝 skill，直接使用這個 repo 即可；如果需要手動安裝，將整個 repo 複製到本機 skill 目錄，並保留目前的資料夾結構。
