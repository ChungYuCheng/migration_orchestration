# 進度顯示規則

這份文件定義輕量進度要顯示在哪裡，避免進度只藏在聊天歷史或 git diff 裡。

## 核心原則

> controller 每次停下來時，都必須讓進度可見。

進度條列是給開發者快速掌握目前做到哪裡的視圖，不是 shared truth。正式狀態仍以 `migration-map.md` 為準。

`Auto-continue: Yes` 不是停止狀態。若回覆中的目前位置顯示 `Human gate: No` 且 `Auto-continue: Yes`，controller 必須繼續執行下一個 concrete action，不應把該回覆作為 final stop。若目前位置只是選下一階段技術方向、bridge sequencing 或 scope selection，且 inventory 可排序，應顯示 controller 已選的下一項，而不是停下要求使用者選方向。

## 顯示位置

controller 必須在兩個地方顯示輕量進度：

1. 使用者回覆最上方
2. `controller-state.md` 最上方

這樣即使 Codex UI 右上角只顯示「對話產出」與 git 異動，開發者仍可點開 `controller-state.md` 看到目前進度。

## 更新時機

遇到下列時機，controller 必須更新進度表面：

- 完成 Discovery Triage
- 建立或更新 `spec.md` / `contracts.md` / `migration-map.md`
- 建立新的 `task-brief`
- batch implement / review / verify 完成
- batch 進入 `blocked`
- 執行 blocked resume protocol
- 準備 compact / resume / 跨 session 交接
- 停在 human gate
- 根據 continuation policy 自動續做下一批
- 執行 technical cohort auto-dispatch
- 執行 technical direction auto-selection
- 執行 technical recon auto-continue

## 格式

回覆與 `controller-state.md` 頂部都使用同一種短格式：

```md
## 進度條列
- [x] GD-B001 route gate
- [x] GD-B002 discovery
- [ ] GD-B003 current batch

## 目前位置
- 目前 batch:
- 下一步:
- Human gate: Yes / No
- Auto-continue: Yes / No
- Stop gate reason:
```

若需要批次表，只保留三欄：

```md
## 批次狀態
| Batch | 狀態 | 下一步 |
|---|---|---|
| GD-B004 | ready | 撰寫 task-brief |
```

## 不做什麼

- 不建立 dashboard 檔案，除非使用者明確要求
- 不把進度條列當成 shared truth
- 不用進度條列取代 `migration-map.md`
- 不在進度條列放完整 reasoning、diff、測試 log

## 與 Controller State 的關係

`controller-state.md` 仍是 compact / resume 的最小恢復包。進度條列放在最上方，只提供快速掃描；詳細恢復狀態仍放在後面的 current phase、current batch、next concrete action。
