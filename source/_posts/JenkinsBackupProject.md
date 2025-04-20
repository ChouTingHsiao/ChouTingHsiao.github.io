---
title: Jenkins 執行備份專案命令
date: 2024-01-01 16:00:00
categories: Jenkins
tags: [Jenkins]
---

# 設定執行時間
在 Pipeline 取得執行時間
```groovy
dateNow = new Date().format( 'yyyyMMddHHmmss' )
```

<!--more-->

# 設定專案目錄與備份目錄
在 Pipeline 定義專案目錄與備份目錄
```groovy
projectPath = "[專案目錄]"
backupPath = "[備份目錄]"
```

# 移除三個月以上的備份資料
使用 ssh 命令登入 Server 移除三個月以上的備份資料
```groovy
sh "ssh -p [ssh連接Port] [帳號]@[Server位置] \"find ${backupPath}* -maxdepth 0 -type d -ctime +90 2>/dev/null | sudo xargs -r rm -rf \""
```

# 依執行時間建立備份目錄
使用 ssh 命令登入 Server 依執行時間建立備份目錄
```groovy
sh "ssh -p [ssh連接Port] [帳號]@[Server位置] \"sudo mkdir -p ${backupPath}${dateNow} \""
```

# 專案複製至備份目錄
使用 ssh 命令登入 Server 將專案複製至備份目錄
```groovy
sh "ssh -p [ssh連接Port] [帳號]@[Server位置] \"sudo cp -r ${projectPath}* ${backupPath}${dateNow}/ \""
```
