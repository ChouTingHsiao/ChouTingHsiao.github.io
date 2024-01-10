---
title: 使用 SSH 建立通道
date: 2023-05-07 18:00:00
categories: Linux
tags: [Linux, Ubuntu, Ssh]
---

# 本機特定端口轉發至跳板機
```bash
ssh -p [跳板機端口] -C -f -N -g -L [本地端口]:[遠端服務IP]:[遠端服務端口] [帳號]@[跳板機IP]
```

<!--more-->

# 跳板機轉發至本機特定端口
```bash
ssh -p [跳板機端口] -C -g -R 0.0.0.0:[跳板機端口]:localhost:[本地端口] [帳號]@[跳板機IP]
```

# 請求轉發至跳板，可搭配 Whistle 做 Socks 代理
```bash
ssh -p [跳板機端口] -D localhost:[本地端口] [帳號]@[跳板機IP]
```

# ssh 執行選項說明
```bash
-L: 指定將本機上的特定端口轉發到遠端主機
-R: 表示使用遠程端口轉發創建ssh隧道
-D: port動態應用級端口轉​​發
-p: 跳板機端口
-C: 使用數據壓縮
-c: cipher_spec選擇用於加密會話的密碼規範
-f: 代表後台運行程序
-F: configfile指定每個用戶的配置文件
-N: 不要執行任何遠端指令
-g: 允許遠程主機連接到本地轉發端口
```

<!-- /etc/ssh/sshd_config 加入GatewayPorts 設定，能讓所有人都連到 -->

<!-- scoop install putty
plink -v -x -a -T -C -noagent -ssh -pw "[密碼]" -P 50893 -L [本地端口]:[遠端服務IP]:[遠端服務端口] [帳號]@[跳板機IP] -->

{% note warning %}
參考資料
1. [SSH Command - Usage, Options, Configuration](https://www.ssh.com/academy/ssh/command)
{% endnote %}