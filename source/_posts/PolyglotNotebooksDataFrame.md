---
title: 在 Polyglot Notebooks 使用 DataFrame
date: 2024-05-05 11:10:00
categories: Polyglot Notebooks
tags: [Polyglot Notebooks, DataFrame]
---

# 載入 CSV 資料
```c#
#r "nuget: Microsoft.Data.Analysis, 0.21.1"

using System.IO;
using Microsoft.Data.Analysis;

string csvfile = File.ReadAllText("test.csv");
DataFrame df = DataFrame.LoadCsvFromString(csvfile, ',');
Console.WriteLine(df);
```

輸出:
```c#
NAME      
張三        
李四   
```

<!--more-->

# 取得前 1 筆
```c#
DataFrame headRow = df.Head(1);

Console.WriteLine(headRow);
```

輸出:
```c#
NAME      
張三   
```

# 加入新 column
```c#
df.Columns.Add(new PrimitiveDataFrameColumn<int>("AGE", new List<int>{1, 1}));

Console.WriteLine(df);
```

輸出:
```c#
NAME      AGE       
張三        1         
李四        1         
```

# 加入新 row
```c#
df.Append(new List<KeyValuePair<string, object>>() {
new KeyValuePair<string, object>("NAME", "王五"),
new KeyValuePair<string, object>("AGE", 3)
}, true);

Console.WriteLine(df);
```

輸出:
```c#
NAME      AGE       
張三        1         
李四        1         
王五        3       
```

# 排序資料
```c#
DataFrame orderByData = df.OrderByDescending("AGE");
Console.WriteLine(orderByData);
```

輸出:
```c#
NAME      AGE       
王五        3         
李四        1         
張三        1            
```

# 分群資料
```c#
DataFrame groupByData = df.GroupBy("AGE").Count();
Console.WriteLine(groupByData);
```

輸出:
```c#
AGE       NAME      
1         2         
3         1         
```

# 篩選資料
```c#
DataFrame filterData = df.Filter(df.Columns["AGE"].ElementwiseGreaterThan(1));
Console.WriteLine(filterData);
```

輸出:
```c#
NAME      AGE       
王五        3            
```

# 合併資料
```c#
DataFrame df1 = DataFrame.LoadCsvFromString(csvfile, ',');
DataFrame mergeData = df1.Merge<string>(df, "NAME", "NAME");
Console.WriteLine(mergeData);
```

輸出:
```c#
NAME_left  NAME_right AGE        
張三         張三         1          
李四         李四         1          
```

{% note warning %}
📜 參考資料
1. [DataFrame Class](https://learn.microsoft.com/en-us/dotnet/api/microsoft.data.analysis.dataframe?view=ml-dotnet-preview&viewFallbackFrom=ml-dotnet)
{% endnote %}