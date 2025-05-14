---
title: Linux IBM i Access ODBC 手動安裝
date: 2021-10-05 22:00:00
categories: Linux
tags: [Linux, Ubuntu, ODBC, IBM i Access]
---

# 配置 ODBC 元件

安裝 Linux ODBC 元件
```bash
sudo apt-get install unixodbc
```

<!--more-->

# 安裝 ODBC Driver

登入[IBM網頁](https://www.ibm.com/support/pages/ibm-i-access-client-solutions)下載 Linux ODBC Driver 並安裝
```bash
sudo dpkg -i ibm-iaccess-1.1.0.15-1.0.amd64.deb
```

查看 Linux ODBC Drivers 設定檔位置
```bash
odbcinst -j
```

查看設定檔是否出現 IBM i Access ODBC Driver 的設定
```bash
cat /etc/odbcinst.ini
```

{% note warning %}
📜 參考資料
1. [Installing the unixODBC driver manager](https://www.ibm.com/docs/en/db2/11.5?topic=managers-installing-unixodbc-driver-manager)
{% endnote %}