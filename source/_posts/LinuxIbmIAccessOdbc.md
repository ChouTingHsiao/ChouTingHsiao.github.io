---
title: Linux IBM i Access Odbc 手動安裝
date: 2021-10-05 22:00:00
categories: Linux
tags: [Linux, Ubuntu, Odbc, IBM i Access]
---

# Odbc 元件配置
安裝 Linux Odbc 元件
```bash
sudo apt-get install unixodbc
```

<!--more-->

# Odbc Driver 安裝
登入[IBM網頁](https://www.ibm.com/support/pages/ibm-i-access-client-solutions)下載Linux Odbc Driver並安裝
```bash
sudo dpkg -i ibm-iaccess-1.1.0.15-1.0.amd64.deb
```

查看 Linux Odbc Drivers 設定檔位置
```bash
odbcinst -j
```

查看設定檔是否出現 IBM i Access ODBC Driver 的設定
```bash
cat /etc/odbcinst.ini
```