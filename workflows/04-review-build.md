# 04-review-build.md — Review & Build 規則

## 觸發條件
- 所有當前 milestone 的 issue 已開發完成並 push

## 執行角色
Reviewer（中階AI）

---

## 執行步驟

### Step 1 — 合併所有 Feature Branch 至 dev
依情境02回報的開發順序，逐一合併：
```
git checkout dev
git pull
git merge {branch 名稱} --no-ff -m "merge: #{IssueNo} {issue 標題}"
```
- 發生 conflict 時，停下來回報衝突位置，不自行解決
- 全部合併完成後進入 Step 2

### Step 2 — Code Review
依 `ai-roles/reviewer.md` 標準逐一檢查，重點關注：
- 邏輯點是否全部實作（對照 issue tickets 欄位逐條確認）
- 有無多餘或無關的變更
- 命名、格式是否符合專案規範
- 明顯的邏輯錯誤或安全疑慮

### Step 3 — Build 驗證
執行專案指定的 build 指令，確認：
- build 無錯誤
- 無 compilation warning 被視為 error

### Step 4 — 判斷結果

**通過條件**：review 無重大問題 + build 成功

✅ **通過流程**：
建立 release branch，commit message 含重構評估結果：
```
git checkout -b release/{yyyyMMdd}-{版本號}
git commit --allow-empty -m "release: {版本號}

重構評估：
- {重構建議1} — 位置：{模組/元件}
- {重構建議2} — 位置：{模組/元件}
（若無重構疑慮則省略此區塊）
"
git push origin release/{yyyyMMdd}-{版本號}
```
回報：
```
REVIEW & BUILD 通過
Release branch：release/{yyyyMMdd}-{版本號}
重構備註：{有/無}
可進入情境06 DEPLOY
```

❌ **不通過流程**：
在 **相關 issue 留下 comment** 列出問題：
```
Review 不通過，需修復：
  1. {問題描述} — 位置：{檔案/模組}
  2. {問題描述} — 位置：{檔案/模組}
```
並將 issue 重新設為 **Open**，然後回報：
```
REVIEW & BUILD 不通過
問題已留言於各相關 issue
請回到情境02，依問題建立 N 條修復 issue
```

---

## 禁止事項
- 不得自行修改程式碼
- 不得合併至 main
- 不得跳過 build 直接歸檔
- conflict 不得自行解決
