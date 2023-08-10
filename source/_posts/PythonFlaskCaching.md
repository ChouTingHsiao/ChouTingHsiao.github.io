---
title: 使用 Flask-Caching 緩存 Flask 呼叫方法產生的資料
date: 2023-07-07 23:00:00
categories: Python
tags: [Python, Flask, Caching]
---

# 實作

## 安裝套件

```bash
pip install Flask
pip install Flask-Caching
```

<!--more-->

## 建立 address.json 內容如下

```json
{
    "Address": "新竹市",
    "Zip": "300"
}
```

## 建立 app.py 內容如下

```python
from flask import Flask
from flask_caching import Cache
import json

config = {
    "DEBUG": True,
    "CACHE_TYPE": "SimpleCache",
    "CACHE_DEFAULT_TIMEOUT": 3000
}

app = Flask(__name__)
app.config.from_mapping(config)
cache = Cache(app)

# 使用裝飾器緩存呼叫方法產生的資料
@cache.memoize(timeout=50)
def LoadAllAddress():
    # 讀取 address.json
    file = open('address.json')
    data = json.load(file)
    return data

# 刪除緩存
@app.route('/ClearAddressCache')
def delAddress():
   # 刪除緩存呼叫方法產生的資料
   cache.delete_memoized(LoadAllAddress)
   return "Clear Success"

# 取得 address.json 的資料
@app.route('/GetAllAddress')
def AllAddress():
    return LoadAllAddress()

if __name__ == '__main__':
    app.run()
```

{% note warning %}
參考資料
1. [Flask-Caching](https://flask-caching.readthedocs.io/en/latest/)
{% endnote %}