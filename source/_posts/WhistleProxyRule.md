---
title: Whistle代理規則使用
date: 2023-01-20 16:00:00
categories: Whistle
tags:
---

# 實作
## Dns代理設定

* 在Rules頁籤加入以下設定，設定Domain對應Ip位置
    ```nginx
    example.com 192.168.0.1
    ```

<!--more-->
## Http的StatusCode代理設定

* 在Rules頁籤加入以下設定，設定狀態碼
    ```nginx
    ^https://example.com/User/Get statusCode://200
    ```

## Http的Headers代理設定

* 在Values頁籤加入以下設定，命名為corsheaders

    ```nginx
    Access-Control-Allow-Origin: *
    Content-Type: application/json; charset=utf8
    ```

* 在Rules頁籤加入以下設定，設定Headers

    ```nginx
    ^https://example.com/User/Get resHeaders://{corsheaders}
    ```

## Http的Body代理設定

* 在Values頁籤加入以下設定，命名為User.json
    ```json
    {
        "Name": "張三",
        "Age": 18,
    }
    ```

* 在Rules頁籤加入以下設定，設定Headers

    ```nginx
    ^https://example.com/User/Get resBody://{User.json}
    ```

## 網頁嵌入Js檔案代理設定

* 在Values頁籤加入以下設定，命名為test.js
    ```javascript
    console.log('test')
    ```

* 在Rules頁籤加入以下設定，設定Js檔案
    ```nginx
    ^https://example.com/index.html jsAppend://{test.js}
    ```

## Socks代理設定

* 在Rules頁籤加入以下設定，設定Domain對應Ip位置
    ```nginx
    example.com socks://127.0.0.1:8080
    ```

# 參考資料
[whistle](https://wproxy.org/whistle/)