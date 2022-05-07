---
title: Linux IBM i Access Odbc手動安裝
date: 2021-10-05 22:00:00
categories: IBM i Access
tags: [Ubuntu]
---

# 實作

## Odbc元件配置
安裝Linux Odbc元件
```bash
sudo apt-get install unixodbc
```

<!--more-->

## Odbc Driver 安裝
登入[IBM網頁](https://www.ibm.com/support/pages/ibm-i-access-client-solutions)下載Linux Odbc Driver並安裝
```bash
sudo dpkg -i ibm-iaccess-1.1.0.15-1.0.amd64.deb
```
查看Linux Odbc Drivers設定檔位置
```bash
odbcinst -j
```
查看設定檔是否出現IBM i Access ODBC Driver的設定
```bash
cat /etc/odbcinst.ini
```