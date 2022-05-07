---
title: 如何在商業邏輯層進行單元測試
date: 2022-05-01 13:00:00
categories: .Net
tags: [UnitTest, Moq]
---
# 實作
安裝Nuget的Moq套件

```
Install-Package Moq
```

<!--more-->

建立Repository的模擬物件
```C#
Mock<Repository> mockRepository = new Mock<Repository>();

mockRepository
.Setup(x=> x.GetData(It.IsAny<string>()))
.Returns(new Table {
    Id = 1,
    Name = "張三"
});
```

把Repository的模擬物件注入Service
```C#
Service service = new Service(mockRepository.Object);
```

使用Service
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

模擬呼叫方法多次依序回傳不同內容，可用SetupSequence做設定
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

# 參考資料

[Moq文件](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior)