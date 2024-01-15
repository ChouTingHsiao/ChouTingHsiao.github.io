---
title: Win10 家用版安裝 Docker
date: 2023-05-05 00:10:00
categories: Wsl2
tags: [Wsl2, Docker]
---

{% note danger %}
請注意，下方 Docker 應用只支援以 Ｗindows 為基底的 Image
{% endnote %}

# 使用 Scoop 安裝 Docker
執行下方命令安裝
```powershell
scoop install docker
```

<!--more-->

# 調整安裝空間
複製 Docker 資料至磁碟機 D ，節省磁碟機 C 的空間
```powershell
Copy-Item 'C:\ProgramData\docker' -Destination 'd:\\docker'
```

建立 'C:\ProgramData\docker\config\daemon.json'
```json
{
  "hosts": [ "tcp://0.0.0.0:" ],
  "tls": false,
  "data-root": "d:\\docker",
  "dns": ["8.8.8.8"]
}
```

{% note danger %}
請注意，因 Win10 家用版本身不支援安裝下方功能，開啟後可能發生未知錯誤，僅供學習使用
{% endnote %}

# 安裝 Hyper-V 功能
```powershell
# 將批處理檔案所在的目錄設定為工作目錄
pushd "%~dp0"

# 尋找符合檔案名的所有檔案名，匯出文字檔
dir /b %SystemRoot%\servicing\Packages\*Hyper-V*.mum >hyper-v.txt

# 加入套件
for /f %%i in ('findstr /i . hyper-v.txt 2^>nul') do dism /online /norestart /add-package:"%SystemRoot%\servicing\Packages\%%i"

# 移除文字檔
del hyper-v.txt

# 安裝容器套件
Dism /online /enable-feature /featurename:Microsoft-Hyper-V-All /LimitAccess /ALL
```

以上方代碼建立 .bat 執行文件，並使用管理者權限執行

# 安裝容器功能
```powershell
# 將批處理檔案所在的目錄設定為工作目錄
pushd "%~dp0"

# 尋找符合檔案名的所有檔案名，匯出文字檔
dir /b %SystemRoot%\servicing\Packages\*containers*.mum >containers.txt

# 加入套件
for /f %%i in ('findstr /i . containers.txt 2^>nul') do dism /online /norestart /add-package:"%SystemRoot%\servicing\Packages\%%i"

# 移除文字檔
del containers.txt

# 安裝容器套件
Dism /online /enable-feature /featurename:containers /LimitAccess /ALL
```

以上方代碼建立 .bat 執行文件，並使用管理者權限執行

# 執行 Docker
執行下方命令註冊 Ｗindows 服務
```powershell
dockerd --register-service ; Start-Service docker ; exit
```

{% note warning %}
📜 參考資料
1. [Scoop Docker](https://scoop.sh/#/apps?q=docker&s=0&d=1&o=true)
2. [Windows Base Image](https://learn.microsoft.com/zh-tw/virtualization/windowscontainers/manage-containers/container-base-images)
{% endnote %}