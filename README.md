# migration-orchestration

`migration-orchestration` 是一個用來協調複雜重構與漸進式 migration 的 skill。

它的設計重點是：

- 由單一 `controller` 維護 shared truth
- 先用最低成本做 triage，必要時再做 discovery / recon
- 以 `task-brief` 作為 implementer 的單一工作入口
- 預設只持久化 `spec.md`、`contracts.md`、`migration-map.md`、`task-brief.md`
- 其他 reports 預設只作為協作格式，必要時才落地

## 結構

- `SKILL.md`: 主 skill 定義
- `references/`: 流程規則、角色邊界、failure / escalation 等參考文件
- `prompts/`: controller、discovery、dispatcher、implementer、reviewer prompts
- `templates/`: `spec`、`contracts`、`migration-map`、`task-brief` 與可選報告模板

## 使用方式

這個 repo 是單一 skill repo，root 本身就是 skill 目錄。

如果你的工具支援從 GitHub repo 安裝 skill，直接使用這個 repo 即可；如果需要手動安裝，將整個 repo 複製到本機 skill 目錄，並保留目前的資料夾結構。
