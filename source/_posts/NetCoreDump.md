---
title: 使用 lldb 分析 Dotnet 核心傾印檔案
date: 2023-05-07 18:00:00
categories: .Net
tags: [.Net, Linux, lldb]
---

# 查詢服務資訊

使用 journalctl 查詢服務 Log
```bash
journalctl -r --identifier=[服務的identifier] --since "2 minute ago"
journalctl -r -u [服務名稱] --since "2 day ago"
```

# 取得系統產生的傾印檔案

安裝 apport
```bash
sudo apt install apport
```

進入目錄 `/var/crash` 搜尋核心傾印檔案，之後使用 apport 解壓縮
```bash
apport-unpack [核心傾印檔案].crash [解壓縮目錄]
```

<!--more-->

# 使用 ProcDump 產生傾印檔案

安裝 ProcDump
```bash
sudo apt install procdump
```

產生傾印檔案至服務工作目錄
```bash
sudo procdump [服務PID]
```

當目標進程 CPU 或記憶體使用量達到特定臨界值或低於限制值時
```bash
sudo procdump -c [CPU使用率] -m [記憶體使用MB數] [服務PID]
```

當目標進程收到特定訊號時
```bash
sudo procdump -sig [Linux 信號] [服務PID]
```

<!-- cd [yourNewUnpackDirectoryHere]/
sudo apt install gdb
gdb `cat ExecutablePath` CoreDump
堆疊: bt full
顯示一些特定的資訊: info
中斷點: b
印出程式碼: list ( l )
顯示正在執行的程式:info frame 0
對執行中的程式以進行除錯: attach PID
在拋出的異常上設置斷點: catch throw
印出變數內容: print *(long**)0x7fffffffdc98
檢查點: watch *(long**)0x7fffffffdc98
反向調試: reverse-stepi -->

<!-- export PATH="$PATH:/root/.dotnet/tools" -->

<!-- dotnet tool install -g dotnet-dump
dotnet-dump ps
dotnet-dump collect --process-id 1902 -->

# 安裝相關 Dotnet 工具加強 lldb 偵錯

安裝 dotnet-symbol 導出傾印檔案的符號
```bash
dotnet tool install -g dotnet-symbol
```

導出傾印檔案的符號
```bash
dotnet-symbol ~/dumps/dotnet/CoreDump -o ~/dumps/symbols --host-only
```

安裝 SOS 擴充偵錯工具功能
```bash
dotnet tool install -g dotnet-sos
dotnet-sos install
```

# 使用 lldb 偵錯

安裝 lldb
```bash
sudo apt-get install lldb
```

使用 lldb 分析核心傾印檔案
```bash
lldb --core ~/dumps/dotnet/CoreDump
```

在 lldb 中載入導入傾印檔案的符號
```bash
setsymbolserver -directory ~/dumps/symbols
```

在 lldb 中常用命令
```bash
執行緒清單: clrthreads
切換執行序: thread select [thread]
線程棧清單: clrstack
GC狀態:  eeheap -gc
所有託管堆物件: dso
託管堆狀態: dumpheap -stat
尋找託管堆的位址: dumpheap -mt [address]
程序最近都拋了什麼異常: dumpheap -type Exception
異常狀況資訊: pe
異常的詳細信息: pe [address]
列印異常信息: printexception [address]
分析物件位置: dumpobj [address]
分析陣列位置: dumparray [address]
```

{% note warning %}
📜 參考資料
1. [ProcDump for Linux](https://github.com/Sysinternals/ProcDump-for-Linux)
2. [重現損毀問題並進行疑難排解](https://learn.microsoft.com/zh-tw/troubleshoot/developer/webapps/aspnetcore/practice-troubleshoot-linux/lab-1-1-reproduce-troubleshoot)
3. [SOS debugging extension](https://learn.microsoft.com/en-us/dotnet/core/diagnostics/sos-debugging-extension)
{% endnote %}