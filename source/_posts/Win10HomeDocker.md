---
title: Win10家用版安裝Docker
date: 2023-05-05 00:10:00
categories: Wsl2
tags: [ Wsl2, docker ]
---

# 實作

## 使用Scoop安裝Docker

執行下方命令
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
  "data-root": "d:\\docker"
}
```

## 安裝Hyper-V功能
```powershell
pushd "%~dp0"
dir /b %SystemRoot%\servicing\Packages\*Hyper-V*.mum >hyper-v.txt
for /f %%i in ('findstr /i . hyper-v.txt 2^>nul') do dism /online /norestart /add-package:"%SystemRoot%\servicing\Packages\%%i"
del hyper-v.txt
Dism /online /enable-feature /featurename:Microsoft-Hyper-V-All /LimitAccess /ALL
```

## 安裝容器功能
```powershell
pushd "%~dp0"
dir /b %SystemRoot%\servicing\Packages\*container*.mum >container.txt
for /f %%i in ('findstr /i . container.txt 2^>nul') do dism /online /norestart /add-package:"%SystemRoot%\servicing\Packages\%%i"
del container.txt
Dism /online /enable-feature /featurename:containers /LimitAccess /ALL
```

## 執行Docker
執行下方命令
```powershell
dockerd
```

# 參考資料

[Scoop Docker](https://scoop.sh/#/apps?q=docker&s=0&d=1&o=true)