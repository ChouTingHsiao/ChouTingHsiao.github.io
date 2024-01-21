---
title: 如何在商業邏輯層進行單元測試
date: 2022-05-01 13:00:00
categories: .Net
tags: [.Net, UnitTest, NSubstitute]
---

# 安裝 NSubstitute
安裝 Nuget 的 NSubstitute 套件

```powershell
Install-Package NSubstitute
```

<!--more-->

# 類別結構

{% plantuml %}
@startuml
!theme spacelab

participant Service << (C,#ADD1B2) RunLogic >>

participant Repository << (I,#ADD1B2) GetData >>

Service ->> Repository: 1. Run GetData 

Repository -->> Service: 2. Return Table Data

@enduml
{% endplantuml %}

# 建立模擬物件
建立 Repository 的模擬物件
```C#
Repository mockRepository = Substitute.For<Repository>();

mockRepository
.GetData(Arg.Any<string>())
.Returns(new Table {
    Id = 1,
    Name = "張三"
});
```

# 注入模擬物件
把 Repository 的模擬物件注入 Service
```C#
Service service = new Service(mockRepository);
```

使用 Service
```C#
Table result = service.RunLogic();

Assert.That(result.Name, Is.EqualTo("張三"));
```

# 驗證模擬物件
依傳入參數做回傳
```C#
mockRepository
.GetData("張三")
.Returns(new Table {
    Id = 1,
    Name = "張三"
});

mockRepository
.GetData("李四")
.Returns(new Table {
    Id = 2,
    Name = "李四"
});
```

模擬呼叫方法多次依序回傳不同內容
```C#
mockRepository
.GetData(Arg.Any<string>())
.Returns(
    new Table
    {
        Id = 1,
        Name = "張三"
    },
    new Table
    {
        Id = 2,
        Name = "李四"
    });
```

驗證呼叫方法的次數與傳入參數
```C#
mockRepository
.Received(1)
.GetData(Arg.Is<string>(x => x == "張三"));
```

{% note warning %}
📜 參考資料
1. [NSubstitute文件](https://nsubstitute.github.io/help.html)
{% endnote %}