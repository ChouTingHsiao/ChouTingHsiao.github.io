---
title: WSL2 設定 Docker In Docker
date: 2024-01-01 16:00:00
categories: Wsl2
tags: [Wsl2, Docker]
---

# 掛載主機的 Docker Socket

## 建立 daemon.json
```bash
sudo nano /etc/docker/daemon.json
```

加入以下設定
```json
{
  "hosts": [ "unix:///var/run/docker.sock", "tcp://0.0.0.0:" ],
  "tls": false
}
```

## 執行命令時須掛載 docker.sock 
```bash
docker run -v /var/run/docker.sock:/var/run/docker.sock -ti [Docker 映像檔]
```

## 透過 Curl 執行 Docker Socket 建立 Containers 
```bash
curl -XPOST --unix-socket /var/run/docker.sock -d '{"Image":"[Docker 映像檔]"}' -H 'Content-Type: application/json' http://localhost/containers/create
```

## 透過 Curl 執行 Docker Socket 啟動 Containers 
```bash
curl -XPOST --unix-socket /var/run/docker.sock http://localhost/containers/[容器編號]/start
```

<!--more-->

# 使用 docker:dind 映像

可參考以下兩種方法運行 docker:dind 映像

## 需要執行帶有該--privileged選項和一些附加標誌的容器
```bash
docker run -p [本機連接Port]:2375 --privileged -e DOCKER_TLS_CERTDIR="" --name privileged-docker -d docker:dind
```

## 使用 Nestybox Sysbox 運行時執行
[安裝 Nestybox Sysbox](https://github.com/nestybox/sysbox?ref=kodekloud.com)，不需要特權模式或特殊配置，即可執行

```bash
# 開放 2375 至本機連接 Port
docker run -p [本機連接Port]:2375 -e DOCKER_TLS_CERTDIR="" --runtime=sysbox-runc --name sysbox-docker -d docker:dind
```

VSCode 使用 Docker contexts 連線至遠端 Docker

```bash
docker context create test --docker "host=tcp://127.0.0.1:[本機連接Port]"
```

{% note warning %}
📜 參考資料
1. [3 Best Ways to Run Docker in Docker Container](https://kodekloud.com/blog/run-docker-in-docker-container/)
2. [Examples using the Docker Engine SDKs and Docker API](https://docs.docker.com/engine/api/sdk/examples/)
{% endnote %}