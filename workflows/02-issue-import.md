# 02-issue-import.md — Issue 導入規則

## 觸發條件
- 情境01 INIT 完成後，尚無 issue
- 或情境07收到新需求清單

## 執行角色
Planner（高階AI）

---

## 執行步驟

### Step 1 — 讀取需求
讀取使用者提供的需求文件或需求描述，完整理解後再進行下一步，不得邊讀邊開 issue。

### Step 2 — 拆分 Issue
將需求拆分為多個獨立的功能單元，每個單元對應一個 issue，拆分原則：
- 同一功能群組放在同一個 issue（同 branch）
- 邏輯點之間有依賴關係時，拆成不同 issue 並標記順序
- 避免單一 issue 邏輯點超過 7 條

### Step 3 — 套用 Issue Template 建立
依照 repo `.github/ISSUE_TEMPLATE/ai-batch-prompt.yml` 格式，填入以下欄位：

**Branch 名稱（branch）**
- 格式：`ai-batch-{功能描述}`
- 範例：`ai-batch-airspace-monitor`
- 每個 issue 獨立 branch，不共用

**邏輯點 tickets（每行一個）**
- 格式：`- {元件/模組}：{具體行為描述}`
- 每條邏輯點須包含：位置 + 做什麼 + 關鍵技術細節
- 範例：
  ```
  - Socket 推播：Socket on('aircraft-update') 收到陣列，先用 Map<aircraftId, data> 濾重複
  - 飛機渲染：markers 用 trackBy(id)，update 只 setLatLng
  ```

**通知方式（notify）**
- 固定勾選：`Slack/Email 驗收通知`

### Step 4 — 一次性建立全部 Issue
所有 issue 規劃完畢並確認後，**一次性批量建立**，不逐條詢問確認。

### Step 5 — 回報摘要
建立完成後回報：
```
ISSUE導入完成
共 N 個 issue，N 個 branch
開發順序：
  1. #IssueNo — {標題}（branch: ai-batch-xxx）
  2. #IssueNo — {標題}（branch: ai-batch-xxx）
  ...
可進入情境03 DEVELOP
```

---

## 注意事項
- 此階段不進行任何程式碼撰寫或建置
- issue 內容僅供 Builder 實作參照，描述須足夠具體讓低階AI可直接執行
- 若需求不清晰，於 Step 1 完成後向使用者提問，確認後再進行 Step 2
