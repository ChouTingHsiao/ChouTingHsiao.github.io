---
title: WSL2移動至其它硬碟
date: 2023-05-05 00:10:00
categories: Wsl2
tags: [ Wsl2 ]
---

# 實作

## 列出已安裝的Linux發行版

執行下方命令
```powershell
wsl -l -v
```

<!--more-->

## 遷移至磁碟機D
```powershell
# 導出已安裝的Linux發行版壓縮檔
wsl --export "Ubuntu-22.04" d:\ubuntu.tar

# 移除WSL
wsl --unregister "Ubuntu-22.04"

# 建立解壓縮的目錄
mkdir d:\Ubuntu

# Linux發行版壓縮檔解壓縮至新目錄後加入WSL
wsl --import "Ubuntu-22.04" d:\ubuntu e:\ubuntu.tar

# 刪除Linux發行版壓縮檔
del d:\ubuntu.tar
```

# 參考資料

[匯入任何要與 WSL 搭配使用的 Linux 發行版本](https://learn.microsoft.com/zh-tw/windows/wsl/use-custom-distro)