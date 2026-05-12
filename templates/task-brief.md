# 工作簡述： [批次名稱]

## 目標
- 用一句到兩句描述這個 task 必須完成的具體行為結果。

## 為什麼需要這個 Task
- 說明這個 task 在整體 migration 中的位置。
- 說明它依賴哪些已完成前提。
- 說明它完成後會替哪一批工作開路。

## 責任範圍
- You own:
- You do not own:

## 檔案範圍

### 允許修改
- 預設使用目錄級範圍。

### 可讀但不可修改
- 列出需要參考但不能修改的相鄰區域或 shared area。

### 不可修改
- 列出 protected zones、frozen contracts 與明確紅線。

## 必要上下文

### 穩定前提
- 列出目前可直接信任的 frozen contracts、既有 adapters、已完成批次或穩定假設。

### 預期實作路徑
- 說明這次應沿用的 pattern、adapter 或 migration path。
- 明確指出應重用哪條既有路徑，而不是重新發明一條。

### 已知陷阱
- 列出最容易踩到的 hidden coupling 或錯誤做法。
- 明確寫出「即使看起來合理，也不要這樣做」的限制。

## 驗收條件
- 列出這次完成所需的最低行為結果與邊界條件。

## 驗證方式
- `command`
  - 說明這個命令驗證什麼

## 阻塞條件
只有在下列情況發生時才停止並回報：
- this task cannot be completed without modifying a protected zone
- this task cannot be completed without changing a frozen contract
- this brief conflicts with the current repository state
- there is no viable local verification path for the required behavior

## 相關摘錄
只有在 implementer 若不看 excerpt 很可能誤用 contract、忽略既有 migrated pattern，或誤讀 shared type 時，才附上這一節。

### Pattern 摘錄
```text
Optional
```

### 契約摘錄
```text
Optional
```

### 共享型別摘錄
```text
Optional
```
