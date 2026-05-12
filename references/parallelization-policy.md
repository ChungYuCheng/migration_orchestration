# 平行化政策

這份文件定義什麼工作可以平行化，什麼工作必須序列化。

## 核心原則

> 定義 shared reality 的工作單線做；依賴 shared reality 的工作才平行做。

## 可以平行化

- 不同 caller cohorts 的 migration
- 不同 feature entrypoints 的 local migration
- 已凍結 contract 後的 bounded module changes
- 不重疊 write scope 的 focused tests
- 不改 shared interface 的 local adapter adoption

## 不可以平行化

- shared interfaces
- shared schemas / shared types
- public API redesign
- cross-cutting config
- global orchestration flow
- 同一組 core files 的多 agent 修改

## 任務大小規則

好的 migration task：

- 一個明確目標
- 一個穩定依賴
- 一組不重疊檔案
- 一個局部可跑的驗證命令
- 一個清楚的完成定義

不好的 migration task：

- 改 3 個 import
- 改一個函式名
- 抽一個 helper
- 補一個 edge case

後者可以是 worker 的內部步驟，但不應是 controller 派工單位。

## 平行化前檢查

在派出兩個以上 implementer 之前，確認：

1. write scopes 是否幾乎不重疊
2. 是否依賴同一個未定稿 contract
3. 是否需要共享尚未完成的輸出
4. 失敗是否會卡死其他批次
5. controller 是否有能力整合結果

只要有一題答案不穩定，就先不要平行化。
