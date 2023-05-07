---
title: WSL2 開放 Docker 外部連線
date: 2023-04-29 21:00:00
categories: Wsl2
tags: [Wsl2, Docker]
---

# 實作

## 開放 Docker 外部連線

建立 '/etc/docker/daemon.json'
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

## 確認 docker 連線 *:2375是否存在
```bash
ss -peanut
```

## Chocolatey 安裝 Docker CLI
```powershell
choco install docker-cli
```

## 主機連線設定有兩種方式

1. 設定環境變數 DOCKER_HOST
```powershell
[Environment]::SetEnvironmentVariable('DOCKER_HOST', "tcp://$($wslip):2375", 'User')
```

2. 設定 docker context
```powershell
# 取得 WSL 的 IP 位置
$wslip = wsl -- ip -o -4 -json addr list eth0 | ConvertFrom-Json | %{ $_.addr_info.local } ` | ?{ $_ }
Write-Host "Setting Docker context 'wsl' to host=tcp://$($wslip):2375"

# 建立 context
docker context create wsl --docker "host=tcp://$($wslip):2375"

# 更新 context
docker context update wsl --docker "host=tcp://$($wslip):2375"

# 使用 context
docker context use wsl
docker --context wsl ps

# 顯示目前設定的 context
docker context ls --format="{{json .}}"
```

## Powershell 取得 WSL 的 IP 位置
```powershell
wsl -- ip -o -4 -json addr list eth0 `
| ConvertFrom-Json `
| %{ $_.addr_info.local } `
| ?{ $_ }
```

## 測試 Docker 命令
```powershell
docker -H [YOUR_WSL_IP] ps
```

<!-- sudo dockerd& -->
<!-- docker run --rm hello-world -->

## 登入 wsl 自動啟動 Docker
修改/etc/profile
```bash
sudo nano /etc/profile
```

加入以下設定
```
export DOCKER_HOST="tcp://127.0.0.1:2375"
sudo service docker start
```

## 無密碼 sudo 授權(不建議用在生產環境)
修改/etc/sudoers
```bash
sudo nano /etc/sudoers
```

加入以下設定
```
%ubuntu ALL=(ALL:ALL) NOPASSWD:ALL
```

# 參考資料

[Daemon CLI (dockerd)](https://community.chocolatey.org/packages/docker-cli)

[Chocolatey Docker CLI](https://docs.docker.com/engine/reference/commandline/dockerd/#daemon-configuration-file)