# Discovery Phase

大型或高不確定性的重構，才需要做 full discovery。預設先做 `Discovery Triage`，只有 triage 顯示複雜度夠高時才進 full discovery。

## Discovery Triage

先用最低成本回答 5 個問題：

1. 受影響模組大概幾個
2. shared boundary 清不清楚
3. caller graph 明不明顯
4. verification 路徑清不清楚
5. 是否預期需要 2 個以上 implementer

如果大多數答案都很簡單，就不要進 full discovery，直接 dispatch 或局部 recon。

## 目的

Discovery 的目標不是解決問題，而是把「不知道什麼」變成「已知的切分依據」。
完成標準不是資訊完整，而是足夠支撐下一步切分與決策。

完成 discovery 後，controller 應能回答：

- 主要模組和子系統有哪些
- 哪些 caller cohorts 會受影響
- 哪些 shared boundaries 需要凍結
- 哪些 hidden coupling 可能造成 protected zone 風險
- 驗證路徑和 baseline 是什麼
- 第一輪 migration batches 應該怎麼切

## 何時必須做

符合任一條件，再進 full discovery：

- 重構跨 3 個以上模組
- shared boundary 尚未明確
- caller graph 未知
- 新舊路徑必須共存
- 驗證路徑不清楚
- 預期需要 2 個以上 implementer

## 產出

- triage 結論
- discovery 摘要（預設給 controller，不必落地）
- 初版 caller cohorts
- 初版 protected zone 候選
- 初版 verification map
- 可供 dispatcher 使用的切分線索

## Discovery 不做什麼

- 不直接改 production code
- 不切到過於細碎的 implementer tasks
- 不定稿 final contracts
- 不代替 dispatcher 做完整 task packet
- 不追求把整個系統完全研究完
- 不應產出與 recon 高度重複的局部 task 細節

## 最低完成標準

- 已知主要 affected modules
- 已知主要 caller groups
- 已知 shared boundary 熱點
- 已知基本驗證命令
- controller 可以根據結果凍結第一版 contracts

## 何時應停止 discovery

- 已經足夠讓 controller 判斷 migration areas
- 已經足夠讓 dispatcher 切出第一輪 batches
- 再繼續調查不會明顯改善切分或風險判斷

## 何時禁止 full discovery

- 預計只改 1 到 3 個檔案
- 不碰 shared contract
- 驗證路徑明確
- 單一 implementer 可完成

此時直接 bounded implementation，必要時只做局部 recon。
