---
title: 將方法轉換成 Emit 的方式執行
date: 2024-05-01 18:00:00
categories: .Net
tags: [.Net, Emit, Polyglot Notebooks]
---

# 原始方法

方法宣告如下
```csharp
public string Hello(string name)
{
    return $"Hello {name}";
}
```

<!--more-->

# 使用 Emit 執行

方法轉換成 Emit
```csharp
using System;
using System.Reflection;
using System.Reflection.Emit;

// 建立動態方法，方法名稱為 "Hello"，回傳型別為 string，參數1型別為 string，true 表示可以存取非公開成員
DynamicMethod helloMethod = new DynamicMethod("Hello", typeof(string), new[] { typeof(string)}, true);

// 產生 IL Code 
ILGenerator ilGenerator = helloMethod.GetILGenerator();

// 輸出字串常值 "Hello " 到堆疊
ilGenerator.Emit(OpCodes.Ldstr, "Hello ");

// 載入第一個參數（傳入的 string）到堆疊
ilGenerator.Emit(OpCodes.Ldarg_0);

// 取得 Concat 方法
MethodInfo concatMethod = typeof(string).GetMethod("Concat", new[] { typeof(string), typeof(string) });

// 將堆疊上的兩個字串相加
ilGenerator.Emit(OpCodes.Call, concatMethod);

// 回傳合併後的結果
ilGenerator.Emit(OpCodes.Ret);

// 將動態方法轉換為委派，並呼叫
Func<string, string> func = (Func<string, string>)helloMethod.CreateDelegate(typeof(Func<string, string>));

// 呼叫方法
string result = func("World");
```

{% note warning %}
📜 參考資料
1. [System.Reflection.Emit Namespace](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.emit?view=net-9.0)
{% endnote %}