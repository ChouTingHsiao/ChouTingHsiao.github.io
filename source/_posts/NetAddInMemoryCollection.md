---
title: 使用AddInMemoryCollection設定Configuration
date: 2023-01-21 18:00:00
categories: .Net
tags: [ IConfiguration ]
---

# 實作

## appsettings.json設定如下
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

## 轉換為Dictionary<string, string>
```c#
Dictionary<string, string> inMemorySettings = new Dictionary<string, string> {
    {"Logging:LogLevel:Default",  "Information"},
    {"Logging:LogLevel:Microsoft.AspNetCore",  "Warning"},
};
```

## 建立Configuration
```c#
IConfiguration configuration = new ConfigurationBuilder()
            .AddInMemoryCollection(inMemorySettings)
            .Build();
```

# 參考資料

[MemoryConfigurationBuilderExtensions.AddInMemoryCollection Method](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.configuration.memoryconfigurationbuilderextensions.addinmemorycollection?view=dotnet-plat-ext-7.0)