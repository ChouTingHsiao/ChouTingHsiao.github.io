---
title: 如何在商業邏輯層進行單元測試
date: 2022-05-01 13:00:00
categories: .Net
tags: [.Net, UnitTest, Moq]
---

# 實作
安裝 Nuget 的 Moq 套件

```powershell
Install-Package Moq
```

<!--more-->

建立 Repository 的模擬物件
```C#
Mock<Repository> mockRepository = new Mock<Repository>();

mockRepository
.Setup(x=> x.GetData(It.IsAny<string>()))
.Returns(new Table {
    Id = 1,
    Name = "張三"
});
```

把 Repository 的模擬物件注入 Service
```C#
Service service = new Service(mockRepository.Object);
```

使用 Service
```C#
Table result = service.RunLogic();

Assert.That(result.Name, Is.EqualTo("張三"));
```

依傳入參數做回傳
```C#
mockRepository
.Setup(x=> x.GetData("張三"))
.Returns(new Table {
    Id = 1,
    Name = "張三"
});

mockRepository
.Setup(x=> x.GetData("李四"))
.Returns(new Table {
    Id = 2,
    Name = "李四"
});
```

模擬呼叫方法多次依序回傳不同內容，可用 SetupSequence 做設定
```C#
mockRepository
.SetupSequence(x=> x.GetData(It.IsAny<string>()))
.Returns(new Table {
    Id = 1,
    Name = "張三"
})
.Returns(new Table {
    Id = 2,
    Name = "李四"
});
```

{% note warning %}
參考資料
[Moq文件](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior)
{% endnote %}