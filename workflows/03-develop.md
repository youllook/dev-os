# 03-develop.md — 逐條 Issue 開發規則

## 觸發條件
- 情境02 ISSUE IMPORT 完成後
- 或情境05 FIX LOOP 修復 issue 存在時

## 執行角色
Builder（低階AI）

---

## 執行步驟

### Step 1 — 取用 Issue
從 GitHub 取得最高優先未開發 issue：
- 優先處理修復 issue（來自情境05）
- 其次依情境02回報的開發順序取用
- 確認 issue 狀態為 `open` 且無人認領

### Step 2 — 建立 Branch
從 `dev`（或 `main`，依專案設定）建立新 branch：
```
git checkout dev
git pull
git checkout -b {issue 內指定的 branch 名稱}
```
- branch 名稱嚴格依照 issue 的 `branch` 欄位，不自行命名
- 每個 issue 獨立 branch，不共用、不混用

### Step 3 — 逐條實作 Tickets
依照 issue 內 `tickets` 欄位的邏輯點，由上到下逐條實作：
- 每條邏輯點實作完後才進行下一條
- 嚴格依照邏輯點描述實作，不自行擴充功能
- 遇到描述不明確時，停下來回報，不自行假設

### Step 4 — Push
所有邏輯點實作完畢後：
```
git add .
git commit -m "#{IssueNo} {issue 標題}"
git push origin {branch 名稱}
```
- **不執行 build**
- **不執行測試**
- **不開 PR**

### Step 5 — 勾選 Sub-task 並關閉 Issue
- 在 issue 內將所有 tickets sub-task 逐一勾選為完成
- 確認全部勾選後，將 issue 狀態設為 **Closed**
- 在 issue 留下 comment：
  ```
  開發完成，已 push 至 {branch 名稱}
  ```

### Step 6 — 回報
```
Issue #N 開發完成
Branch: {branch 名稱}
實作邏輯點：
  - {ticket 1}
  - {ticket 2}
  ...
等待下一條 issue 指派
```

---

## 取用下一條 Issue
回報完成後，自動回到 Step 1 取用下一條未開發 issue，直到所有 issue 完成為止。

所有 issue 完成後回報：
```
所有 Issue 開發完成，共 N 條
可進入情境04 REVIEW & BUILD
```

---

## 禁止事項
- 不得合併 branch 到 dev/main
- 不得自行修改 issue 格式或內容
- 不得執行任何 build 或測試指令
- 不得跨 issue 共用 branch
- 不得在未完成當前 issue 前取用下一條
</thinking>
