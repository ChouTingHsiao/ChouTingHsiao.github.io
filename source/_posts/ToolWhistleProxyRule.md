---
title: Whistle 代理規則使用
date: 2023-01-20 16:10:00
categories: Tool
tags: [Tool, Whistle]
---

# 設定 Dns 代理

* 在 Rules 頁籤加入以下設定，設定 Domain 對應 Ip 位置
    ```nginx
    example.com 192.168.0.1
    ```

<!--more-->

# 設定 Http 的 StatusCode 代理

* 在 Rules 頁籤加入以下設定，設定狀態碼
    ```nginx
    ^https://example.com/User/Get statusCode://200
    ```

# 設定 Http 的 Headers 代理

* 在 Values 頁籤加入以下設定，命名為 corsheaders
    ```nginx
    Access-Control-Allow-Origin: *
    Content-Type: application/json; charset=utf8
    ```

* 在 Rules 頁籤加入以下設定，設定 Headers
    ```nginx
    ^https://example.com/User/Get resHeaders://{corsheaders}
    ```

# 設定 Http 的 Body 代理

* 在 Values 頁籤加入以下設定，命名為 User.json
    ```json
    {
        "Name": "張三",
        "Age": 18,
    }
    ```

* 在 Rules 頁籤加入以下設定，設定 Headers
    ```nginx
    ^https://example.com/User/Get resBody://{User.json}
    ```

# 設定網頁嵌入 Js 檔案

* 在 Values 頁籤加入以下設定，命名為 test.js
    ```javascript
    console.log('test')
    ```

* 在 Rules 頁籤加入以下設定，設定 Js 檔案
    ```nginx
    ^https://example.com/index.html jsAppend://{test.js}
    ```

# 設定 Socks 代理

搭配{% post_link LinuxSshTunnel SSH %}動態端口轉發請求

* 在 Rules 頁籤加入以下設定，設定 Domain 對應 Ip 位置
    ```nginx
    example.com socks://127.0.0.1:8080
    ```

{% note warning %}
📜 參考資料
1. [whistle](https://wproxy.org/whistle/)
{% endnote %}