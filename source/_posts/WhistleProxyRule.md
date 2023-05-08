---
title: Whistle 代理規則使用
date: 2023-01-20 16:10:00
categories: Whistle
tags: [Whistle]
---

# 實作
## Dns 代理設定

* 在 Rules 頁籤加入以下設定，設定 Domain 對應 Ip 位置
    ```nginx
    example.com 192.168.0.1
    ```

<!--more-->
## Http 的 StatusCode 代理設定

* 在 Rules 頁籤加入以下設定，設定狀態碼
    ```nginx
    ^https://example.com/User/Get statusCode://200
    ```

## Http 的 Headers 代理設定

* 在 Values 頁籤加入以下設定，命名為 corsheaders

    ```nginx
    Access-Control-Allow-Origin: *
    Content-Type: application/json; charset=utf8
    ```

* 在 Rules 頁籤加入以下設定，設定 Headers

    ```nginx
    ^https://example.com/User/Get resHeaders://{corsheaders}
    ```

## Http 的 Body 代理設定

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

## 網頁嵌入 Js 檔案代理設定

* 在 Values 頁籤加入以下設定，命名為 test.js
    ```javascript
    console.log('test')
    ```

* 在 Rules 頁籤加入以下設定，設定 Js 檔案
    ```nginx
    ^https://example.com/index.html jsAppend://{test.js}
    ```

## Socks 代理設定

* 在 Rules 頁籤加入以下設定，設定 Domain 對應 Ip 位置
    ```nginx
    example.com socks://127.0.0.1:8080
    ```

{% note warning %}
參考資料
[whistle](https://wproxy.org/whistle/)
{% endnote %}