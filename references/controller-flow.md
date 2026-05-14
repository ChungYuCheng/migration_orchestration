# Controller 協調流程

這份文件描述 controller 在複雜重構中的標準決策流。

## 標準節奏

```text
1. 讀取 spec / contracts / migration-map，長鏈 migration 也讀取 migration-inventory
2. 先做 Discovery Triage
3. 若 triage 顯示複雜度高 -> 派 discovery agent
4. 更新 shared truth；長鏈 migration 建立或更新 migration-inventory
5. 交給 dispatcher 產出 batches、task briefs
6. 選出下一個 migration batch
7. 判斷：
   - 低風險且已 bounded -> 直接 implement
   - 高風險或局部未知數高 -> 先派 recon
8. 收到 recon report
9. 更新 contracts / migration-map / task brief（如需要）
10. 派 implementer
11. 收到 implementation report
12. 派 reviewer
13. 若 reviewer 判斷需要 device verification -> 產生測試項目清單並執行裝置驗證
14. reviewer 回報給 controller
15. controller 判斷：
   - implementation fix -> 回 implementer
   - dispatch / recon / contract issue -> 升級處理
16. 若 task blocked -> 執行 blocked resume protocol，先分類 technical / human blocked
17. 符合 done definition -> 更新 migration-map 與必要的 migration-inventory 狀態
18. 若 context 可能 compact 或工作要交接 -> 更新 controller-state
19. 執行 continuation policy：
   - 下一批 ready 且無 human gate -> 建立下一個 task brief 並繼續
   - 下一步是 technical cohort selection 且可保守切出 -> controller 自行選 cohort、建立 batch / task brief 並繼續
   - 下一步是 technical direction / bridge sequencing / scope selection 且 inventory 可排序 -> controller 自行選方向、建立 bounded recon / scope brief / narrow implementation 並繼續
   - 長鏈 migration 缺少 inventory -> 建立 inventory backfill batch 後繼續
   - 有 stop gate -> 回報停止原因與需要的人類決策
```

## 階段模型

### Phase 0: Discovery Triage

- 快速估算 affected modules
- 快速檢查 shared boundary 是否清楚
- 快速檢查 caller graph 是否明顯
- 快速檢查 verification 路徑是否明確
- 快速判斷是否需要多個 implementer

退出條件：

- 已知道是否要進 full discovery

### Phase 1: Discovery

- 盤點模組與子系統
- 建立 caller cohorts
- 找 shared boundaries
- 找 hidden coupling
- 盤點驗證命令與 baseline
- 建立或更新全局 `migration-inventory.md`

退出條件：

- 主要 dependencies 已知
- 主要 shared boundaries 已知
- 可以根據 discovery 結果切 migration batches
- 可以根據 inventory 看懂剩餘範圍、風險與建議順序

### Phase 2: Stabilize

- 補 characterization tests
- 補 logs/metrics（必要時）
- 建 adapter / facade / compat shim
- 確保 rollback path 存在

退出條件：

- 既有行為可觀測
- 新舊路徑可共存

### Phase 3: Freeze Contracts

- 定義 stable interfaces
- 定義 shared types
- 定義 protected zones
- 定義 migration rules

退出條件：

- implementer 不需要自行重設計 shared boundary

### Phase 4: Dispatch Planning

- 根據 discovery 或 triage 結果切 batches
- 中大型 / 長鏈 migration 先參考 `migration-inventory.md` 切 batches
- 為每個 batch 寫 task brief
- 只有必要時才附 supporting excerpts 或 reference
- 標示 dependency、blocked conditions、verification

退出條件：

- 每個 batch 都有可供 implementer 執行的 bounded `task-brief`

### Phase 5: Plan Migration Batches

- 依 caller cohorts 或 bounded modules 切 batch
- 為每個 batch 定義 write scope
- 確保 local verification 可獨立執行

退出條件：

- 每個 batch 都有清楚的 owned files、acceptance criteria、verification

### Phase 6: Execute and Review

- 必要時先 recon
- 再 implement
- 最後 review
- AndroidEC UI / navigation / user flow 風險，必要時加入 device verification
- 若 blocked，先產出選項並分類；technical blocked 自動選 recommended option，human blocked 才等待使用者選擇
- batch done 後套用 continuation policy，不要只停在 resume target
- commit / clean worktree 後仍要套用 continuation policy，不要把 commit boundary 當停止點
- 下一步若是 bounded recon / inventory / slice planning，執行 Technical Recon Auto-Continue，不要只留下 recon target
- 長鏈 migration 若沒有 `migration-inventory.md`，先建立 inventory backfill batch，不要靠聊天記憶選下一批

退出條件：

- `migration-map` 已更新
- 長鏈 migration 的 `migration-inventory` 已更新
- 下批次前沒有 stale shared truth
- 若要 compact / resume，`controller-state` 已更新
- 若曾 blocked，resume target 已明確
- 若需要 device verification，結果已處理為 pass / fail / skipped decision
- 若下一批 ready 且無 human gate，已建立下一個 task brief 並繼續執行
- 若下一步是 technical cohort selection，已自行選出下一個 bounded cohort、建立 batch / task-brief 並繼續執行
- 若下一步是 technical direction / bridge sequencing / scope selection，已依 inventory 選出下一個方向，建立 bounded recon / scope brief / narrow implementation 並繼續執行
- 若下一步是 bounded recon / inventory / slice planning，已執行 recon 或派 recon subAgent，並產出 task-brief / Stop Gate 判斷

## 完成定義（Done Definition）

一個 batch 只有在同時滿足下列條件時，才能標成 `done`：

1. implementation complete
2. review passed
3. verification complete
4. required shared truth updates applied
5. no unresolved dependency on unconfirmed assumptions
6. required device verification passed or skipped decision explicitly accepted

簡化規則：

> `done` 代表這個 batch 已經在 code、review、verification、shared truth 四個層面都完成閉環。

## 下一批啟動門檻（Next Batch Start Gate）

下一批可提前啟動，只有在 controller 能證明下列條件都成立時才行：

1. 前一批的 shared truth 不會改變下一批前提
2. 下一批 brief 不依賴前一批未確認結果
3. 兩批 write scope 不衝突
4. 即使前一批稍後被修正，也不會讓下一批 brief 失效

依賴類型：

- `hard dependency`: 前一批沒 `done`，下一批不能開始
- `soft dependency`: controller 可在安全前提下條件式提前啟動

若下一批已標記 `ready`，且符合 continuation policy 的 Auto-Continue Gate，controller 應自動續做，不需要等待使用者說「繼續」。

若下一批尚未標記 `ready`，但下一步只是 technical cohort selection，controller 應依 `continuation-policy.md` 的 Technical Cohort Auto-Dispatch 自行選 cohort 並建立下一批。

若下一批尚未標記 `ready`，但下一步只是 technical direction / bridge sequencing / scope selection，controller 應依 `continuation-policy.md` 的 Technical Direction Auto-Selection 自行選方向並建立下一批。protected behavior 的標籤本身不是 human gate；先切 bounded recon 或 narrow implementation，只有需要產品語意、rollout、公開 route 或無法 bounded 時才停。

若長鏈 migration 尚未建立 `migration-inventory.md`，controller 應先用既有 artifacts 建立 inventory backfill batch，再依 inventory selection order 選下一批。

不允許提前啟動的情況：

- 前一批仍有 open contract issue
- 前一批仍在 `blocked`
- 前一批仍可能觸發 shared truth drift

## Controller 決策規則

### 什麼情況先 recon

- task 仍跨多個模組
- hidden coupling 可能高
- protected zones 可能被碰到
- 驗證路徑不清楚
- discovery 已標記 `Recon Required: Yes`

### 什麼情況可直接 implement

- contract 已穩定
- scope 隔離清楚
- 已有相似 migration slice 完成過
- 驗證命令明確
- 不碰 protected zones

### 什麼情況禁止 recon

- 預計只改 1 到 3 個檔案
- 不碰 shared contract
- 驗證路徑明確
- 單一 implementer 可完成

### 什麼情況一定先 discovery

- 跨 3 個以上模組
- shared boundary 尚未明確
- caller graph 未知
- 新舊路徑必須共存
- 驗證路徑不清楚
- 預期需要 2 個以上 implementer

### 什麼情況可只做 triage，不做 full discovery

- 影響面疑似小到中等
- shared boundary 大致清楚
- caller graph 大致可見
- 不確定是否真的值得進 full discovery

### 什麼情況停止平行化

- 多個 task 開始想改同一批 core files
- contract 已不穩定
- 某一批改變了後續批次的前提
- `migration-map` 落後現況

### 什麼時候更新 `migration-map`

只在會影響下一步決策的關鍵狀態才更新：

- `planned`
- `needs_recon`
- `ready`
- `blocked`
- `done`

短暫狀態如 `in_progress`、`in_review` 預設不持久化。

## Controller 不可犯的錯誤

- 讓 implementer 自己定義或更新 contracts
- full discovery 已足夠還反覆調查同一問題
- 沒做 discovery 就對大型重構直接切 task
- 讓 dispatcher 在 contract 未凍結時切出大量平行工作
- shared boundary 還在變時就平行派工
- 用 micro-patches 當 migration task
- 沒先更新 `migration-map` 就進入下一批
- 長鏈 migration 沒有 `migration-inventory.md` 卻繼續只靠聊天脈絡選下一批
