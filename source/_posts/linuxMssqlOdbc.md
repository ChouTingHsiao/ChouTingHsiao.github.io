---
title: Linux Mssql Odbc手動安裝
date: 2021-10-05 22:00:00
categories: Mssql
tags: [Ubuntu]
---
# 實作

### Linux Odbc 元件配置

安裝Linux Odbc元件
```bash
sudo apt-get install unixodbc
```

<!--more-->

### Odbc Driver 安裝

登入[Microsoft網頁](https://docs.microsoft.com/en-us/sql/connect/odbc/download-odbc-driver-for-sql-server?view=sql-server-ver15)下載Odbc Driver並安裝
```bash
sudo dpkg -i msodbcsql17_17.8.1.1-1_amd64.deb
```
查看Linux Odbc Drivers設定檔位置
```bash
odbcinst -j
```
查看設定檔是否出現ODBC Driver 17 for SQL Server的設定
```bash
cat /etc/odbcinst.ini
```