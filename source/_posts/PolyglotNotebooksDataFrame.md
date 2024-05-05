---
title: 在 Polyglot Notebooks 使用 DataFrame
date: 2024-05-05 11:00:00
categories: PolyglotNotebooks
tags: [PolyglotNotebooks, DataFrame]
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

<!--more-->

# 取得前 1 筆
```c#
DataFrame headRow = df.Head(1);
Console.WriteLine(headRow);
```

# 加入新 column
```c#
df.Columns.Add(new PrimitiveDataFrameColumn<int>("AGE", new List<int>{1, 1}));
```

# 加入新 row
```c#
df.Append(new List<KeyValuePair<string, object>>() {
new KeyValuePair<string, object>("NAME", "王五"),
new KeyValuePair<string, object>("AGE", 3)
}, true);

Console.WriteLine(df);
```

# 排序資料
```c#
DataFrame orderByData = df.OrderByDescending("AGE");
Console.WriteLine(orderByData);
```

# 分群資料
```c#
GroupBy groupByData = df.GroupBy("AGE");
Console.WriteLine(groupByData);
```

# 篩選資料
```c#
DataFrame filterData = df.Filter(df.Columns["AGE"].ElementwiseGreaterThan(1));
Console.WriteLine(filterData);
```

# 合併資料
```c#
DataFrame df1 = DataFrame.LoadCsvFromString(csvfile, ',');
DataFrame mergeData = df1.Merge<string>(df, "NAME", "NAME");
Console.WriteLine(mergeData);
```

{% note warning %}
📜 參考資料
1. [DataFrame Class](https://learn.microsoft.com/en-us/dotnet/api/microsoft.data.analysis.dataframe?view=ml-dotnet-preview&viewFallbackFrom=ml-dotnet)
{% endnote %}