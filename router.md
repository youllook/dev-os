# router.md — 多AI開發情境路由

## 角色定義

| 角色 | AI層級 | 職責 |
|------|--------|------|
| Planner | 高階（Claude Opus/Sonnet） | 規劃、情境判斷、issue 撰寫 |
| Builder | 低階（Haiku / 本地模型） | 逐條 issue 實作、push |
| Reviewer | 中階（Claude Sonnet） | review、build驗證、缺陷回報 |

---

## 情境路由表

### 判斷方式
當收到任務時，先確認以下狀態：
1. **repo 是否已初始化？**（有無 .git、有無專案結構）
2. **issue 是否已建立？**（GitHub issue 存在且有編號）
3. **當前 issue 是否已開發完畢？**（程式碼已 push）
4. **所有 issue 是否完成？**（milestone 完成率）
5. **review/build 是否通過？**

---

### 情境 01 — INIT
**觸發條件**：無 repo 或新專案引入  
**執行角色**：Planner  
**載入規則**：`workflows/01-init.md`  
**產出**：
- repo 位置確認
- git 金鑰設定確認
- 專案結構掌握摘要
- 確認後回報「INIT完成，可進入ISSUE導入」

---

### 情境 02 — ISSUE IMPORT
**觸發條件**：INIT完成，尚無 issue；或情境07收到新任務  
**執行角色**：Planner  
**載入規則**：`workflows/02-issue-import.md`  
**Issue格式**：參照 repo 內 `.github/ISSUE_TEMPLATE/` 的 Ticket 模板  
**產出**：
- 讀取完整需求文件
- 依 GitHub Issue Template 格式一次性建立所有 issue
- 確認 issue 清單後回報「ISSUE導入完成，共N條，可進入開發」

---

### 情境 03 — DEVELOP
**觸發條件**：有未完成 issue，且非 review 失敗的修復 issue  
**執行角色**：Builder  
**載入規則**：`workflows/03-develop.md`  
**產出**：
- 逐條取用最高優先 issue
- 實作完畢後 push 到 dev branch
- 關閉 issue 或標記 PR ready
- 回報「Issue #N 開發完成」

---

### 情境 04 — REVIEW & BUILD
**觸發條件**：所有當前 milestone issue 已 push，待合併  
**執行角色**：Reviewer  
**載入規則**：`workflows/04-review-build.md`  
**產出**：
- 將所有 feature branch 合併至 dev
- 執行 code review（依 reviewer.md 標準）
- 執行 build 驗證
- **若通過**：建立 `release/yyyyMMdd-{版本}` branch 歸檔，回報「通過，進入情境06」
- **若不通過**：列出問題清單，回報「不通過，回到情境02，需建立N條修復issue」

---

### 情境 06 — DEPLOY
**觸發條件**：情境04 review/build 通過  
**執行角色**：Planner  
**載入規則**：`workflows/06-deploy.md`  
**產出**：
- 合併 dev → main
- 執行建置指令
- 確認建置成功後回報「部署完成，進入情境07」

---

### 情境 07 — NOTIFY & STANDBY
**觸發條件**：情境06完成  
**執行角色**：Planner  
**載入規則**：`workflows/07-notify.md`  
**產出**：
- 產生測試通知內容（版本、異動摘要、測試重點）
- 通知人工測試，進入待機

**人工測試後判斷**：
- **測試不通過** → 開立修復 issue，回到情境03
- **測試通過，無新任務** → 流程結束，系統待機
- **測試通過，有新任務** → 收集需求清單，回到情境02

---

## 路由判斷流程（給 Brain/Planner 使用）

```
收到任務
  │
  ├─ repo未初始化？ → 情境01 INIT
  │
  ├─ 無issue / 有新需求或修復清單？ → 情境02 ISSUE IMPORT
  │
  ├─ 有未完成issue？ → 情境03 DEVELOP
  │
  ├─ 所有issue完成，未merge/review？ → 情境04 REVIEW & BUILD
  │       ├─ 通過 → merge dev → 建立 release branch → 情境06
  │       └─ 不通過 → 回情境02（修復走issue流程）
  │
  ├─ Review通過，未部署？ → 情境06 DEPLOY
  │
  └─ 已部署，待人工測試
        ├─ 測試不通過 → 回情境02（修復走issue流程）
        ├─ 通過 + 有新任務 → 情境02
        └─ 通過 + 無任務 → 待機結束
```

---

## 新專案繼承方式

```bash
# 方式A：submodule（推薦，可同步更新）
git submodule add https://github.com/yourname/dev-os .dev-os

# 方式B：直接複製
cp -r dev-os/.dev-os ./新專案/.dev-os
```

每次與AI開工時，在 prompt 開頭加入：
```
請載入 .dev-os/router.md，依當前專案狀態判斷情境，再告知我目前應進入哪個階段。
```

---

## 版本
v0.1 — 初版，待實際使用後調整細節
