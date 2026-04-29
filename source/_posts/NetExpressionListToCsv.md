---
title: 使用 Expression Trees 把 List<T> 轉成 CSV 字串
date: 2024-05-01 18:00:00
categories: .Net
tags: [.Net, Expression]
---

# 宣告類別

類別內容如下
```csharp
public class User {
    
    public string Name { get; set; }
    
    public int Age { get; set; }
}
```

List 內容如下
```csharp
List<User> users = new List<User> {
    new User {
        Name = "Test1",
        Age = 10
    },
    new User {
        Name = "Test2",
        Age = 20
    }
};
```

<!--more-->

# 產生 Expression 表達式

建立轉換方法
```csharp
using System;
using System.Reflection;
using System.Linq.Expressions;

public static class Helper
{
    public static List<string> selectToCsv<T>(this List<T> list, string delimiter)
    {
        Type source = typeof(T);

        // 取得類別欄位
        PropertyInfo[] prop = source.GetProperties();

        // 類別使用 t 表達
        ParameterExpression t = Expression.Parameter(source, "t");

        // 取得轉成字串方法
        MethodInfo toString = typeof(object).GetMethod("ToString");

        List<ConditionalExpression> parameters = new List<ConditionalExpression>();

        foreach (PropertyInfo p in prop)
        {
            // 類別欄位使用 t.Name, t.Age 表達
            MemberExpression propertyValue = Expression.PropertyOrField(t, p.Name);

            // 非字串型別欄位進行字串轉換 t.Name.ToString(), t.Age.ToString()
            MethodCallExpression propertyToString = Expression.Call(propertyValue, toString);

            // NULL 值
            ConstantExpression constantNull = Expression.Constant(null, typeof(object));

            // 非字串型別欄位需進行字串轉換後，在做 NULL 判斷
            BinaryExpression isNull = Expression.Equal(propertyToString, constantNull);

            if (p.PropertyType == typeof(string))
            {
                // 字串型別欄位直接進行 NULL 判斷
                isNull = Expression.Equal(propertyValue, constantNull);
            }

            // 空字串值
            ConstantExpression constantEmpty = Expression.Constant(string.Empty, typeof(string));

            // IIF 判斷 t.Name == null ? "" : t.Name.ToString()
            ConditionalExpression ternary = Expression.Condition(isNull, constantEmpty, propertyToString);

            parameters.Add(ternary);
        }
       
        // 取得 Format 方法
        MethodInfo formatMethod = typeof(string).GetMethod("Format", BindingFlags.Static | BindingFlags.Public, null, new[] { typeof(string), typeof(object[]) }, null);
       
        // 產生類別欄位序號模板清單 [ {0}, {1} ]
        List<string> formatStrings = Enumerable.Range(0, prop.Length).Select(count => $"{{{count}}}").ToList();

        // 使用符號 | 串聯成字串 "{0}|{1}"
        ConstantExpression formatConstantExpression = Expression.Constant(string.Join(delimiter, formatStrings));

        // 產生類別欄位轉換陣列
        NewArrayExpression formatObjsExpression = Expression.NewArrayInit(typeof(string), parameters);

        // 產生 lambda 表達式
        Expression<Func<T, string>> lambda = (Expression<Func<T, string>>)Expression.Lambda(Expression.Call(formatMethod, formatConstantExpression, formatObjsExpression), t);

        // t => Format("{0}|{1}", new [] {IIF((t.Name == null), "", t.Name.ToString()), IIF((t.Age.ToString() == null), "", t.Age.ToString())})

        IQueryable<T> queryable = list.AsQueryable();

        return queryable.Select(lambda).ToList();
    }
}
```

# 使用方法

執行方法轉換出 CSV 列表
```csharp
List<string> csvList = users.selectToCsv("|");
```

{% note warning %}
📜 參考資料
1. [Expression Trees](https://learn.microsoft.com/en-us/dotnet/csharp/advanced-topics/expression-trees/)
{% endnote %}
