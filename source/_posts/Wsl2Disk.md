---
title: WSL2 移動至其它硬碟
date: 2023-05-05 00:10:00
categories: Wsl2
tags: [Wsl2]
---

# 列出已安裝的 Linux 發行版
執行下方命令
```powershell
wsl -l -v
```

<!--more-->

# 遷移至磁碟機 D
```powershell
# 導出已安裝的 Linux 發行版壓縮檔
wsl --export "Ubuntu-22.04" d:\ubuntu.tar

# 移除 WSL
wsl --unregister "Ubuntu-22.04"

# 建立解壓縮的目錄
mkdir d:\Ubuntu

# Linux 發行版壓縮檔解壓縮至新目錄後加入 WSL
wsl --import "Ubuntu-22.04" d:\ubuntu d:\ubuntu.tar

# 刪除 Linux 發行版壓縮檔
del d:\ubuntu.tar

# 因重新建立 WSL 會以 root 為預設帳戶，需重新設定預設帳戶
ubuntu2204.exe config --default-user ubuntu
```

{% note warning %}
參考資料
1. [匯入任何要與 WSL 搭配使用的 Linux 發行版本](https://learn.microsoft.com/zh-tw/windows/wsl/use-custom-distro)
{% endnote %}