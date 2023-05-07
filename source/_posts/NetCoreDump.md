---
title: 使用lldb分析Dotnet核心傾印檔案
date: 2023-05-07 18:00:00
categories: .Net
tags: [.Net, lldb]
---

# 實作

## 使用apport解壓縮核心傾印檔案

安裝apport
```bash
sudo apt install apport
```

解壓縮核心傾印檔案
```bash
apport-unpack [systemGeneratedCrashReportPath].crash [yourNewUnpackDirectoryHere]
```

<!--more-->

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

<!-- export PATH="$HOME/.dotnet/tools:$PATH" -->

<!-- dotnet tool install -g dotnet-dump
dotnet-dump ps
dotnet-dump collect --process-id 1902 -->

## 安裝Dotnet工具

安裝dotnet-symbol導出傾印檔案的符號
```bash
dotnet tool install -g dotnet-symbol
```

導出傾印檔案的符號
```bash
dotnet-symbol ~/dumps/dotnet/CoreDump -o ~/dumps/symbols --host-only
```

安裝SOS擴充偵錯工具功能
```bash
dotnet tool install -g dotnet-sos
dotnet-sos install
```

## 使用lldb偵錯

安裝lldb
```bash
sudo apt-get install lldb
```

使用lldb分析核心傾印檔案
```bash
lldb --core ~/dumps/dotnet/CoreDump
```

在lldb中載入導入傾印檔案的符號
```bash
setsymbolserver -directory ~/dumps/symbols
```

在lldb中常用命令
```bash
原生執行緒清單: clrthreads
切換執行序: thread select 15
導出所有的線程棧: clrstack
所有 Managed 物件: dso
託管堆狀態: dumpheap -stat
程序最近都拋了什麼異常: dumpheap -type Exception
異常的詳細信息: pe [address]
尋找物件的位址: dumpheap -mt <address>
分析物件位置: dumpObj [address]
分析陣列位置: dumparray [address]
GC狀態:  eeheap -gc
```

## 安裝ProcDump

當目標進程 CPU 或記憶體使用量達到特定臨界值或低於限制值時，您可以使用 ProcDump 來擷取記憶體傾印檔案。
```bash
sudo apt install procdump
```

# 參考資料

[重現損毀問題並進行疑難排解](https://learn.microsoft.com/zh-tw/troubleshoot/developer/webapps/aspnetcore/practice-troubleshoot-linux/lab-1-1-reproduce-troubleshoot)