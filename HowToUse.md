# 在新專案 repo 根目錄執行
```bash
git submodule add https://github.com/youllook/dev-os.git .dev-os
git commit -m "chore: add dev-os submodule"
git push
```

# 在 dev-os repo 修改文件後 push
```bash
cd .dev-os
# 修改文件...
git add .
git commit -m "docs: ..."
git push
```

# 各專案要同步更新時執行
```bash
git submodule update --remote .dev-os
git commit -m "chore: update dev-os"
git push
```

# 每次開工的起手式
clone 含 submodule 的專案時要多加一個參數：
```bash
git clone --recurse-submodules {專案 repo URL}
```
或 clone 後才發現沒有 .dev-os/：
```bash
git submodule update --init
```