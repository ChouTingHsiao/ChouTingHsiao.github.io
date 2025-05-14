---
title: 使用 DataFrame 操作資料
date: 2024-05-05 11:10:00
categories: .Net
tags: [.Net, DataFrame, Polyglot Notebooks]
---

# 套件安裝

安裝套件
```c#
// 套件 Microsoft.Data.Analysis 檔案轉 DataFrame 格式
#r "nuget: Microsoft.Data.Analysis, 0.21.1"
```

引用套件
```c#
using System.IO;
using Microsoft.Data.Analysis;
```

<!--more-->

# 載入 CSV 資料

使用 LoadCsvFromString 

```c#
string csvfile = File.ReadAllText("test.csv");
DataFrame df = DataFrame.LoadCsvFromString(csvfile, ',');
df.Display();
```

# 建立測試資料

```c#
Int32DataFrameColumn id = new Int32DataFrameColumn("ID", new int[] { 1, 2 });
StringDataFrameColumn name = new StringDataFrameColumn("Name", new string[] { "張三", "李四" });
Int32DataFrameColumn age = new Int32DataFrameColumn("Age", new int?[] { 25, null });

DataFrame df = new DataFrame();
df.Columns.Add(id);
df.Columns.Add(name);
df.Columns.Add(age);

df.Display();
```

輸出:
| index | ID | Name | Age |
|-------|----|------|-----|
|   0   | 1  | 張三  | 25  |
|   1   | 2  | 李四  |     |

# 加入新欄位

加入欄位 Height
```c#
df.Columns.Add(new PrimitiveDataFrameColumn<int>("Height", new List<int>{180, 179}));

df.Display();
```

輸出:
| index | ID | Name | Age | Height |
|-------|----|------|-----|--------|
|   0   | 1  | 張三 | 25  |  180   |
|   1   | 2  | 李四 |     |  179   |

# 顯示欄位資訊

了解欄位類型與資料筆數統計
```c#
 df.Info().Display();
```

輸出:
| index | Info | ID | Name | Age | Height |
|-------|------|----|------|-----|--------|
|   0   | DataType | System.Int32 | System.String | System.Int32 | System.Int32 |
|   1   | Length(excluding null values) | 2  | 2 | 1  |  2  |

# 回傳統計摘要

統計相關值，此欄為數值欄位限定
```c#
df.Description().Display();
```

輸出:
| index | Description                   | ID  | Age  | Height |
|-------|-------------------------------|-----|------|--------|
|   0   | Length (excluding null values)| 2   | 1    | 2      |
|   1   | Max                           | 2   | 25   | 180    |
|   2   | Min                           | 1   | 25   | 179    |
|   3   | Mean                          | 1.5 | 12.5 | 179.5  |

# 回傳前 N 筆資料

```c#
df.Head(1).Display();
```

輸出:
| index |  ID  | Name | Age | Height |
|-------|------|------|-----|--------|
|   0   |  1   | 張三  | 25  |  180   |

# 回傳後 N 筆資料

```c#
df.Tail(1).Display();
```

輸出:
| index |  ID  | Name | Age | Height |
|-------|------|------|-----|--------|
|   0   |   2  | 李四  |     |  179   |

# 加入新資料

填入每一欄對應資料
```c#
df.Append(new List<KeyValuePair<string, object>>() {
    new KeyValuePair<string, object>("ID", 3),
    new KeyValuePair<string, object>("Name", "王五"),
    new KeyValuePair<string, object>("Age", 20),
    new KeyValuePair<string, object>("Height", 170)
}, true);

df.Display();
```

輸出:
| index | ID | Name | Age   | Height |
|-------|----|------|--------|--------|
|   0   | 1  | 張三 | 25     | 180    |
|   1   | 2  | 李四 | <null> | 179    |
|   2   | 3  | 王五 | 20     | 170    |

# 缺失值處理

針對表格中的缺失欄位做處理

1. 排除有空值的欄位
```c#
DataFrame dropNullData = df.DropNulls();
dropNullData.Display();
```

輸出:
| index | ID | Name | Age   | Height |
|-------|----|------|-------|--------|
|   0   | 1  | 張三 | 25    |  180   |
|   1   | 3  | 王五 | 20    |  170   |

2. 缺失值填充
```c#
df.Columns["Age"] = df.Columns["Age"].FillNulls(0);
df.Display();
```

輸出:
| index | ID | Name | Age   | Height |
|-------|----|------|-------|--------|
|   0   | 1  | 張三 | 25     | 180    |
|   1   | 2  | 李四 | 0      | 179    |
|   2   | 3  | 王五 | 20     | 170    |

# 排序資料

```c#
DataFrame orderByData = df.OrderByDescending("Age");
orderByData.Display();
```

輸出:
| index | ID | Name | Age   | Height |
|-------|----|------|-------|--------|
|   0   | 1  | 張三 | 25     | 180    |
|   1   | 3  | 王五 | 20     | 170    |
|   2   | 2  | 李四 | 0      | 179    |

# 分群資料

```c#
DataFrame groupByData = df.GroupBy("Age").Count();
groupByData.Display();
```

輸出:
| index | Age |  ID  |  Name  | Height |
|-------|-----|------|--------|--------|
|   0   |  25 |   1  |   1    |   1    |
|   1   |  0  |   1  |   1    |   1    |
|   2   |  20 |   1  |   1    |   1    |

# 篩選資料

```c#
DataFrame filterData = df.Filter(df.Columns["Age"].ElementwiseGreaterThan(1));
filterData.Display();
```

輸出:
| index | ID | Name | Age   | Height |
|-------|----|------|-------|--------|
|   0   | 1  | 張三 | 25    |  180   |
|   1   | 3  | 王五 | 20    |  170   |

# 合併資料

```c#
// 建立新的列表
Int32DataFrameColumn idWeight = new Int32DataFrameColumn("ID", new int[] { 1, 2 });
Int32DataFrameColumn weight = new Int32DataFrameColumn("Weight", new int?[] { 60, 90 });

DataFrame dfWeight = new DataFrame();
dfWeight.Columns.Add(idWeight);
dfWeight.Columns.Add(weight);

// 使用 ID 做 Merge
DataFrame mergeData = dfWeight.Merge<int>(df, "ID", "ID");
mergeData.Display();
```

輸出:
| index | ID_left | Weight | ID_right | Name |  Age  | Height |
|-------|---------|--------|----------|------|-------|--------|
|   0   |   1     |   60   |    1     | 張三 |   25  |   180  |
|   1   |   2     |   90   |    2     | 李四 |   0   |   179  |

{% note warning %}
📜 參考資料
1. [DataFrame Class](https://learn.microsoft.com/en-us/dotnet/api/microsoft.data.analysis.dataframe?view=ml-dotnet-preview&viewFallbackFrom=ml-dotnet)
{% endnote %}