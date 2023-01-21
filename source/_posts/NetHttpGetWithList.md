---
title: Http的Get方法傳入List參數
date: 2023-01-21 18:10:00
categories: .Net
tags: []
---

# 實作

## 建立類別
```c#
public class User {
  
	public string Name { get; set; }

	public int Age { get; set; }
}
```

## 建立HttpGet方法
```c#
[ApiController]
[Route("[controller]")]
public class UserController : ControllerBase
{
  [HttpGet]
  public List<User> Get([FromQuery] List<User> user)
  {
      return user;
  }
}
```

## 組合Request的Url
```nginx
https://example.com/User?user[0].Name='張三'&user[0].Age=10&user[1].Name='李四'&user[1].Age=20
```

# 參考資料

[MemoryConfigurationBuilderExtensions.AddInMemoryCollection Method](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.configuration.memoryconfigurationbuilderextensions.addinmemorycollection?view=dotnet-plat-ext-7.0)