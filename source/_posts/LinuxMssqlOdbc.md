---
title: Linux MSSQL ODBC 手動安裝
date: 2021-10-05 22:00:00
categories: Linux
tags: [Linux, Ubuntu, ODBC, MSSQL]
---

# 配置 Linux ODBC 元件

安裝 Linux ODBC 元件
```bash
sudo apt-get install unixodbc
```

<!--more-->

# 安裝 ODBC Driver

登入[Microsoft網頁](https://docs.microsoft.com/en-us/sql/connect/odbc/download-odbc-driver-for-sql-server?view=sql-server-ver15)下載Odbc Driver並安裝
```bash
sudo dpkg -i msodbcsql17_17.8.1.1-1_amd64.deb
```

查看 Linux ODBC Drivers 設定檔位置
```bash
odbcinst -j
```

查看設定檔是否出現 ODBC Driver 17 for SQL Server 的設定
```bash
cat /etc/odbcinst.ini
```

{% note warning %}
📜 參考資料
1. [Installing the unixODBC driver manager](https://www.ibm.com/docs/en/db2/11.5?topic=managers-installing-unixodbc-driver-manager)
{% endnote %}