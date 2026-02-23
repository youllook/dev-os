# 01-init.md — 專案初始化規則

## 觸發條件
- 新專案建立
- 或引入現有專案至開發流程

## 執行角色
Planner（高階AI）

---

## 執行步驟

### Step 1 — 確認 Repo 位置
請使用者提供或確認以下資訊：
- GitHub repo URL
- 本地工作目錄路徑
- 預設主線 branch 名稱（通常為 `main` 或 `master`）

### Step 2 — 確認 Git 金鑰與權限
確認以下設定已就緒：
- SSH key 或 Personal Access Token 已設定
- 具備 push 與建立 branch 的權限
- 若尚未設定，停下來引導使用者完成，不繼續下一步

### Step 3 — Clone 或確認本地狀態
```
git clone {repo URL}
# 或若已有本地 repo
git fetch --all
git status
```
確認：
- 本地與遠端同步
- 無未提交的殘留變更

### Step 4 — 掌握專案結構
讀取並摘要以下內容（存在才讀）：
- `README.md` — 專案說明與啟動方式
- `package.json` / `.csproj` / 其他依賴清單 — 技術棧確認
- `.github/ISSUE_TEMPLATE/` — 確認 issue 模板存在
- 現有 branch 清單 — 了解目前開發狀態

### Step 5 — 確認 dev-os 繼承
確認 `.dev-os/` 目錄存在且包含完整 workflow 文件：
```
.dev-os/
  ├── router.md
  ├── workflows/
  └── ai-roles/
```
若不存在，引導使用者執行：
```
git submodule add https://github.com/{yourname}/dev-os .dev-os
```

### Step 6 — 回報摘要
```
INIT 完成
Repo：{repo URL}
本地路徑：{路徑}
主線 branch：{main/master}
技術棧：{摘要}
Issue Template：{存在/不存在}
dev-os：{已繼承/未繼承}

可進入情境02 ISSUE IMPORT
```

---

## 禁止事項
- 不得在權限未確認前執行任何 push 操作
- 不得自行假設 repo 結構，需實際讀取
- 不得跳過 dev-os 確認步驟
