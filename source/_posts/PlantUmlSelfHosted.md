---
title: 自託管 PlantUML 服務
date: 2024-02-04 18:00:00
categories: Project
tags: [Project, PlantUML]
---

# 在 Ubuntu 安裝 openjdk
使用以下 Linxu 指令，安裝 openjdk11
```bash
sudo apt install openjdk-11-jdk
```

# [下載 PlantUML 的 Jar 檔案](https://plantuml.com/zh/download)
使用以下 Linxu 指令下載檔案
```bash
wget -O /[執行路徑]/plantuml.jar https://github.com/plantuml/plantuml/releases/download/v1.2024.0/plantuml-1.2024.0.jar
```

<!--more-->

# 建立 PlantUML 服務
使用以下 Linxu 指令建立 PlantUML 服務
```bash
sudo nano /etc/systemd/system/plantuml.service
```

服務貼上以下內容
```bash
[Unit]
Description=PlantUML
After=syslog.target network.target

[Service]
# Deploy folder location
WorkingDirectory=[執行路徑]
# Execute project by java Command
ExecStart=/usr/bin/java -jar plantuml.jar -picoweb:3060:127.0.0.1
Restart=always
# Restart service after 10 seconds if the PlantUML service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=PlantUML
User=ubuntu
MemoryMax=50%

[Install]
WantedBy=multi-user.target
```

啟用服務
```bash
systemctl daemon-reload
systemctl start plantuml
```

# 設定 Visual Studio Code

安裝 [PlantUML support for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=jebbs.plantuml) 套件，並在設定檔設定 plantuml.server 參數

```json
"plantuml.server": "https://[Domain]/plantuml"
```

建立 .puml 為附檔名的檔案，並加入以下代碼，按下 Alt + D 生成圖檔
```
@startuml
:Hello world;
:This is defined on
several **lines**;
@enduml
```

{% note warning %}
📜 參考資料
1. [PlantUML PicoWeb Server](https://plantuml.com/zh/picoweb)
{% endnote %}