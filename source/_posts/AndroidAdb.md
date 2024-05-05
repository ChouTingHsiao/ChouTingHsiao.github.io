---
title: 使用 ADB 命令
date: 2024-05-05 11:30:00
categories: Android
tags: [Android, adb]
---

<!-- 宿主機: localhost 10.0.2.2 -->

# 查看 adb 版本信息
```bash
adb version
```

# 以 root 權限運行
```bash
adb root
```

# 查看連接設備

查看已連接設備
```bash
adb devices
```

多個設備，可使用 -s 選項指定裝置
```bash
adb -s [裝置名稱]
```

# 運行 adb 服務

模擬器運行 adb 服務
```bash
adb -P [模擬器端口] start-server
```

模擬器停止 adb 服務
```bash
adb kill-server
```

<!--more-->

# 應用程式安裝

```bash
adb install [apk路徑]
```

# 文件上傳下載

從設備拉取文件到本機
```bash
adb pull [設備上文件位置] [目的地文件位置]
```

從本機推送文件到設備 
```bash
adb push [本機上文件位置] [目的地文件位置]
```

# 通訊埠轉發

本機通訊埠的要求轉發至裝置通訊埠 
```bash
adb forward tcp:[本機通訊埠] tcp:[裝置通訊埠]
adb forward --list
```

裝置通訊埠的要求轉發至本機通訊埠 
```bash
adb reverse tcp:[裝置通訊埠] tcp:[本機通訊埠]
adb reverse --list
```

# 執行 shell 命令

輸入文字至輸入框
```bash
adb shell input text "hello,world"
```

基本命令
```bash
# 查看當前路徑
adb shell pwd
# 列出文件目錄
adb shell ls
# 創建目錄
adb shell mkdir [-m][-p] [目錄名稱]
# 切換目錄
adb shell cd [目錄名稱]
# 建立文件
adb shell touch [文件名稱]
# 複製文件或目錄
adb shell cp [source] [dest]
# 移動或重命名文件
adb shell mv [source] [dest]
```

網路相關命令
```bash
# 連線測試
adb shell ping [IP位置]
# 查詢網卡資訊
adb shell ip addr
# 查詢路由
adb shell route -n
# 查詢 ARP table
adb shell ip neighbour
```

應用程式管理
```bash
# 查看應用安裝路徑
adb shell pm path [應用程式名稱]
# 查看所有應用
adb shell pm list packages
# 查看系統應用
adb shell pm list packages -s
# 查看第三方應用
adb shell pm list packages -3
# 查看包名包含特定字符的應用
adb shell pm list packages [特定字符]
# 卸載應用
adb shell pm uninstall -k --user 0 [應用程式名稱]
# 清除應用空間
adb shell pm clear [應用程式名稱]
```

使用 dumpsys 查看物件詳細資訊
```bash
# 查看應用
adb shell dumpsys package [應用程式名稱]
# 查看手機CPU狀態 
adb shell dumpsys cpuinfo
# 查看內存使用情況 
adb shell dumpsys meminfo [應用程式名稱]
# 顯示磁盤使用信息
adb shell dumpsys diskstats
# 查看電池狀態 
adb shell dumpsys battery
adb shell dumpsys batteryproperties
```
 
偵錯應用程式
```bash
# 執行應用程式偵錯
adb shell am set-debug-app -w --persistent [應用程式名稱]
# 結束應用程式偵錯
adb shell am clear-debug-app
```

# 處理 sqllite
查詢應用名稱
```bash
adb shell pm list packages 
```

使用 sqlite3 執行 SQL 命令
```bash
sqlite3 /data/data/[應用名稱]/databases/[sqlite檔案名稱]
```

列出應用 databases 目錄下所有檔案
```bash
adb exec-out run-as [應用名稱] ls databases
```

下載 sqlite 檔案
```bash
adb root
adb pull /data/data/[應用名稱]/databases/[sqlite檔案名稱]
```

{% note warning %}
📜 參考資料
1. [Android Debug Bridge (adb)](https://developer.android.com/tools/adb?hl=zh-tw)
2. [dumpsys](https://developer.android.com/tools/dumpsys?hl=zh-tw)
3. [sqlite3](https://developer.android.com/tools/sqlite3?hl=zh-tw)
{% endnote %}