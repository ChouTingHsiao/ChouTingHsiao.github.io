---
title: Win10家用版安裝Docker
date: 2023-05-05 00:10:00
categories: Wsl2
tags: [ Wsl2, docker ]
---

# 實作

## 使用Scoop安裝Docker

執行下方命令安裝，請注意下方Docker只支援Ｗindows的Image
```powershell
scoop install docker
```

<!--more-->

複製Docker資料至磁碟機D，節省磁碟機C的空間
```powershell
Copy-Item 'C:\ProgramData\docker' -Destination 'd:\\docker'
```

建立C:\ProgramData\docker\config\daemon.json
```json
{
  "hosts": [ "tcp://0.0.0.0:" ],
  "tls": false,
  "data-root": "d:\\docker",
  "dns": ["8.8.8.8"]
}
```

## 安裝Hyper-V功能
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

## 安裝容器功能
```powershell
# 將批處理檔案所在的目錄設定為工作目錄
pushd "%~dp0"

# 尋找符合檔案名的所有檔案名，匯出文字檔
dir /b %SystemRoot%\servicing\Packages\*container*.mum >container.txt

# 加入套件
for /f %%i in ('findstr /i . container.txt 2^>nul') do dism /online /norestart /add-package:"%SystemRoot%\servicing\Packages\%%i"

# 移除文字檔
del container.txt

# 安裝容器套件
Dism /online /enable-feature /featurename:containers /LimitAccess /ALL
```
 
## 執行Docker
執行下方命令註冊Ｗindows服務
```powershell
dockerd --register-service ; Start-Service docker ; exit
```

# 參考資料

[Scoop Docker](https://scoop.sh/#/apps?q=docker&s=0&d=1&o=true)

[Windows Base Image](https://learn.microsoft.com/zh-tw/virtualization/windowscontainers/manage-containers/container-base-images)