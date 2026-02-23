# dev-os — 多AI協同開發作業系統

## 這是什麼
一套可繼承的多AI開發流程規範，定義情境路由、各角色職責、與 GitHub 的交互邏輯。

## 不包含什麼
> ⚠️ **Issue Template 不在此 repo 內**
> 
> GitHub Issue Template（`.github/ISSUE_TEMPLATE/`）屬於各專案自行維護，
> GitHub 只讀取專案本身的 `.github/` 目錄，submodule 內的無效。
> 請在每個專案自行建立對應的 Issue Template。

---

## 目錄結構
```
.dev-os/
  ├── README.md              ← 你在這裡
  ├── router.md              ← 情境判斷入口（AI 每次開工先讀這份）
  ├── workflows/
  │     ├── 01-init.md
  │     ├── 02-issue-import.md
  │     ├── 03-develop.md
  │     ├── 04-review-build.md
  │     ├── 06-deploy.md
  │     └── 07-notify.md
  └── ai-roles/
        ├── planner.md       ← 高階AI角色規格
        └── reviewer.md      ← 中階AI角色規格
```

---

## GitHub Issue 交互邏輯

Issue 的狀態與欄位是本系統的核心溝通介面，所有 AI 角色都依此讀取進度與留下記錄。

### 狀態定義

| 狀態 | 代表意義 | 由誰設定 |
|------|----------|----------|
| Open | 待開發 | Planner（情境02建立） |
| Open | 修復中（Reviewer 留言後重開） | Reviewer（情境04不通過） |
| Closed | 開發完成已 push | Builder（情境03完成） |

### Comment 使用規範

| 時機 | 由誰留言 | 內容 |
|------|----------|------|
| 開發完成 | Builder | `開發完成，已 push 至 {branch}` |
| Review 不通過 | Reviewer | 問題清單，格式見 `04-review-build.md` |

### 流程示意

```
Planner 建立 issue → Open
         │
Builder 開發完成 → Comment「開發完成」→ Closed
         │
Reviewer 發現問題 → Comment 問題清單 → Reopen
         │
Planner 回情境02 → 建立修復 issue（新 issue，不修改原 issue）
```

### 重要原則
- **修復問題永遠開新 issue**，不在原 issue 上直接修改
- **重構建議不開 issue**，記錄於 release branch 的 commit message
- **人工測試不通過才回情境02建修復 issue**，AI review 只擋 build 失敗

---

## 開工起手式（給 AI 用）
```
請載入 .dev-os/router.md，依當前專案狀態判斷情境，告知目前應進入哪個階段。
```
