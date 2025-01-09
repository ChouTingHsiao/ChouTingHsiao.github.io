---
title: 如何解析 JSON 資料
date: 2023-05-07 00:00:00
categories: .Net
tags: [.Net, JSON, Polyglot Notebooks]
---

# 建立 JSON 檔案

建立檔案 Request.json 內容如下

```json
{
    "info": {
        "address": [
            {
                "town": "Belgrade"
            },
            {
                "town": "Paris"
            },
            {
                "town": "Madrid"
            }
        ]
    }
}
```

<!--more-->

# 建立 Class 對應

Class 內容如下

```c#
public class Address
{
	public string town { get; set; }
}

public class Info
{
	public List<Address> address { get; set; }
}

public class Request
{
	public Info info { get; set; }
}
```

# 讀取資料並解析

```c#
#!import Request.cs

using System.Text.Json;
using System.IO;

using (StreamReader r = new StreamReader(@".\Request.json"))
{
	// 讀取整個檔案
	string jsonString = r.ReadToEnd();

	// 還原序列化成 Request 類別
	Request req = JsonSerializer.Deserialize<Request>(jsonString);

	req.Display();

	// 序列化 Request 類別成 JSON 字串
	string reqString = JsonSerializer.Serialize<Request>(req);

    reqString.Display();
}
```

{% note warning %}
📜 參考資料
1. [如何寫入 .NET 物件成為 JSON (序列化)](https://learn.microsoft.com/zh-tw/dotnet/standard/serialization/system-text-json/how-to)
2. [如何將 JSON 讀取為 .NET 物件 (還原序列化)](https://learn.microsoft.com/zh-tw/dotnet/standard/serialization/system-text-json/deserialization)
{% endnote %}
