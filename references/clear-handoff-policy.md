# Clear Handoff Policy

這份文件定義中大型重構經歷多次 context compact 後，何時應建議 clear / 新 session，如何避免清空 context 後流程中斷。

## 核心原則

> Clear is a controlled handoff, not a reset.

clear 不是修復流程混亂的捷徑。只有 shared truth 足夠完整、目前 batch 已到安全邊界、下一步能靠 artifacts 恢復時，才可以建議 clear。

## 何時考慮 Clear Handoff

出現下列情況時，controller 應評估是否進入 Clear Handoff Gate：

- 已經歷多次 context compact，且摘要開始變長或含混
- controller 開始依賴聊天記憶，而不是 artifacts
- decision drift：同一個 frozen decision 被重新解釋
- implementer / reviewer 需要反覆重新理解同一段 repo 背景
- 舊討論中的過期假設干擾目前 batch
- 工作將跨 session 或隔日接續
- 下一批需要比目前 session 更乾淨的決策上下文

這些是評估信號，不是自動 clear 條件。

## Clear Handoff Gate

同時符合下列條件時，才可建議 clear / 新 session：

1. 目前 active batch 不在實作中間
2. 已完成目前 batch 的 review / verification，或明確停在人類 gate
3. `migration-map.md` 已反映最新 batch 狀態
4. `controller-state.md` 已更新到最新目前位置
5. 下一個 concrete action 明確
6. current 或 next `task-brief` 能獨立執行
7. `spec.md`、`contracts.md`、protected zones 沒有未寫入的口頭決策
8. git 狀態清楚：已 commit，或列出所有未提交變更與原因
9. 沒有未處理的 human blocked option selection
10. 沒有正在等待中的 sub-agent 結果

任一項不成立，不要 clear。先補 shared truth、完成 batch 邊界、或停在人類 gate。

## 不適合 Clear 的時機

- implementer 正在改 code
- reviewer 尚未完成目前 batch
- tests 尚未跑完或結果未整理
- device verification 正在進行或結果未判讀
- human blocked 已發生但使用者尚未選 remediation option
- frozen contract / protected zone 還在討論中
- git working tree 狀態不清楚
- controller 無法說清楚下一個 concrete action

## Clear 前輸出 Handoff Package

clear 前，controller 必須在回覆與 `controller-state.md` 中留下最小 handoff package。

格式：

```md
## Clear Handoff Package
- Repo / worktree:
- Branch:
- Base:
- Latest commit:
- Git status:
- Current phase:
- Completed batch:
- Active / next batch:
- Frozen contracts:
- Protected zones:
- Open risks:
- Human gate: Yes / No
- Auto-continue after resume: Yes / No
- Next concrete action:
- Read before continuing:
  - spec.md
  - contracts.md
  - migration-map.md
  - controller-state.md
  - current task-brief
- Do not do yet:
```

這份 package 不是第二份 plan，只是讓新 session 能恢復 controller 狀態。

## Clear 後 Resume Gate

clear / 新 session 後，controller 不得直接寫 code。必須先讀：

1. `spec.md`
2. `contracts.md`
3. `migration-map.md`
4. `controller-state.md`
5. current / next `task-brief`
6. git status 與最近 commit

讀完後先確認：

- handoff package 與 repo 現況一致
- current batch / next batch 仍正確
- frozen contracts 沒有被新變更推翻
- protected zones 仍有效
- next concrete action 仍安全
- 是否符合 continuation policy 的 Auto-Continue Gate

若任一項不一致，先更新 shared truth 或回 recon / dispatcher，不要直接 implement。

## 與 Compact 的關係

- compact 是保留同一 thread 的短中期恢復機制
- clear 是跨 session / 新 thread 的乾淨交接機制
- 多次 compact 後若出現 decision drift 或上下文污染，優先補 artifacts，再評估 clear
- clear 不能取代 `task-brief`、`migration-map`、`controller-state`

## Controller 回覆規則

建議 clear 時，不要只說「請開新對話」。必須同時給出：

- 為什麼現在適合 clear
- Clear Handoff Gate 是否通過
- handoff package
- 新 session 第一個 prompt 建議
- 若不 clear，下一步會怎麼繼續

新 session 第一個 prompt 應短而明確，例如：

```md
使用 migration-orchestration 繼續這個重構。請先執行 Clear 後 Resume Gate：
- 讀 controller-state.md、migration-map.md、contracts.md、spec.md、目前 task-brief
- 確認 handoff package 與 repo 現況一致
- 若 Auto-continue = Yes，從 Next Concrete Action 繼續
```

## 常見錯誤

- 把 clear 當作解決未整理 shared truth 的方式
- 在 batch 實作中間 clear
- clear 前沒有更新 `controller-state.md`
- clear 後直接寫 code
- handoff package 沒寫 git status 或 latest commit
- 沒有標出 human gate / auto-continue 狀態
