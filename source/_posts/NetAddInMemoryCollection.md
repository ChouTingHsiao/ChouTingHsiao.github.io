---
title: 使用 AddInMemoryCollection 設定 Configuration
date: 2023-01-21 18:00:00
categories: .Net
tags: [.Net, IConfiguration]
---

# 實作

## appsettings.json 設定如下
```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  }
}
```

<!--more-->

## 轉換為 Dictionary<string, string>
```c#
Dictionary<string, string> inMemorySettings = new Dictionary<string, string> {
    {"Logging:LogLevel:Default",  "Information"},
    {"Logging:LogLevel:Microsoft.AspNetCore",  "Warning"},
};
```

## 建立 Configuration
```c#
IConfiguration configuration = new ConfigurationBuilder()
            .AddInMemoryCollection(inMemorySettings)
            .Build();
```

{% note warning %}
參考資料
1. [MemoryConfigurationBuilderExtensions.AddInMemoryCollection Method](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.configuration.memoryconfigurationbuilderextensions.addinmemorycollection?view=dotnet-plat-ext-7.0)
{% endnote %}