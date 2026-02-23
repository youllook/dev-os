# 06-deploy.md — 建置部署規則

## 觸發條件
- 情境04 REVIEW & BUILD 通過，release branch 已建立

## 執行角色
Planner（高階AI）

---

## 執行步驟

### Step 1 — 確認 Release Branch
確認情境04產出的 release branch 存在且為最新狀態：
```
git fetch --all
git branch -r | grep release
```

### Step 2 — 合併至 main
```
git checkout main
git pull
git merge release/{yyyyMMdd}-{版本號} --no-ff -m "release: {版本號}"
git push origin main
```
- 發生 conflict 時停下來回報，不自行解決

### Step 3 — 執行建置
依專案指定的部署指令執行，例如：
- `npm run build`
- `dotnet publish`
- `docker build`
- 或其他專案定義的指令

確認建置產出無錯誤。

### Step 4 — 回報
```
DEPLOY 完成
版本：{版本號}
Release branch：release/{yyyyMMdd}-{版本號}
main 已更新
建置產出：{路徑或摘要}

可進入情境07 NOTIFY
```

---

## 禁止事項
- 不得跳過 release branch 直接從 dev 合併至 main
- 不得在建置失敗時繼續進入情境07
- 建置失敗時回報錯誤，等待人工確認後再處理
