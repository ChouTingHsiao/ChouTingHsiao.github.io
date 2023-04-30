---
title: WSL2開放Docker外部連線
date: 2023-04-29 21:00:00
categories: Wsl2
tags: [ Wsl2 ]
---

# 實作

## 開放Docker外部連線

建立/etc/docker/daemon.json
```bash
sudo nano /etc/docker/daemon.json
```

加入以下設定
```json
{
  "hosts": [ "tcp://0.0.0.0:" ],
  "tls": false
}
```

<!--more-->

## 確認docker連線*:2375是否存在
```bash
ss -peanut
```

## Powershell取得WSL的IP位置
```powershell
wsl -- ip -o -4 -json addr list eth0 `
| ConvertFrom-Json `
| %{ $_.addr_info.local } `
| ?{ $_ }
```

## 測試Docker命令
```bash
docker -H [YOUR_WSL_IP] ps
```

## 測試Docker命令
```bash
docker -H [YOUR_WSL_IP] ps
```

<!-- sudo dockerd& -->
<!-- docker run --rm hello-world -->

## 主機連線設定有兩種方式

1. 設定環境變數DOCKER_HOST
```powershell
[Environment]::SetEnvironmentVariable('DOCKER_HOST', "tcp://$($wslip):2375", 'User')
```

2. 設定docker context
```powershell
# 取得WSL的IP位置
$wslip = wsl -- ip -o -4 -json addr list eth0 | ConvertFrom-Json | %{ $_.addr_info.local } ` | ?{ $_ }
Write-Host "Setting Docker context 'wsl' to host=tcp://$($wslip):2375"

# 建立context
docker context create wsl --docker "host=tcp://$($wslip):2375"

# 更新context
docker context update wsl --docker "host=tcp://$($wslip):2375"

# 使用context
docker context use wsl
docker --context wsl ps

# 顯示目前設定的context
docker context ls --format="{{json .}}"
```

## 登入wsl自動啟動Docker
修改/etc/profile
```bash
sudo nano /etc/profile
```

加入以下設定
```
export DOCKER_HOST="tcp://127.0.0.1:2375"
sudo service docker start
```

## 無密碼sudo授權(不建議用在生產環境)
修改/etc/sudoers
```bash
sudo nano /etc/sudoers
```

加入以下設定
```
%ubuntu ALL=(ALL:ALL) NOPASSWD:ALL
```
