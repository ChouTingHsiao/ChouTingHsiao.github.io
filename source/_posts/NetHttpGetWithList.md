---
title: Http 的 Get 方法傳入 List 參數
date: 2023-01-21 18:10:00
categories: .Net
tags: [.Net]
---

# 建立類別
```c#
public class User {
  
	public string Name { get; set; }

	public int Age { get; set; }
}
```

<!--more-->

# 建立 HttpGet 方法
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

# 組合 Request 的 Url
```nginx
https://example.com/User?user[0].Name='張三'&user[0].Age=10&user[1].Name='李四'&user[1].Age=20
```

{% note warning %}
📜 參考資料
1. [MemoryConfigurationBuilderExtensions.AddInMemoryCollection Method](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.configuration.memoryconfigurationbuilderextensions.addinmemorycollection?view=dotnet-plat-ext-7.0)
{% endnote %}