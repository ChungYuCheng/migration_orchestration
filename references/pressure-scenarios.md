# 壓力情境與驗證思路

這份文件不是主 skill 的一部分，而是未來驗證 skill 有沒有教對東西的測試素材。

## 情境 1：contract 未凍結就想平行派工

症狀：

- controller 還沒定好 shared interface
- implementer 已經想分頭改 callers

正確行為：

- skill 應要求先 freeze contracts
- 不能先平行做 caller migration

## 情境 2：implementer 想直接改 protected zone

症狀：

- task brief 沒授權修改 `src/payments/core/*`
- implementer 發現要碰 shared type 才能做完

正確行為：

- implementer 回報 `BLOCKED`
- controller 重新切 task 或更新 contract

## 情境 3：worker context 爆掉

症狀：

- implementer 需要重讀大量 repo 背景
- 任務沒有最小 context packet

正確行為：

- controller 重新整理 `context-packet`
- 只提供 global brief、local brief、relevant excerpts

## 情境 4：migration map 已過期

症狀：

- 某批次已完成，但下一批仍照舊假設派工
- 多個 agent 對現在遷移進度認知不同

正確行為：

- controller 先更新 `migration-map`
- 再派下一批

## 情境 5：task 切得太碎

症狀：

- 每個 worker 只做零碎 patch
- controller 的整合成本反而爆炸

正確行為：

- 改切成完整 migration slice
- 以 caller cohort 或 bounded module 為單位
