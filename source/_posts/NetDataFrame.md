---
title: 使用 DataFrame 操作資料
date: 2024-05-05 11:10:00
categories: .Net
tags: [.Net, DataFrame]
---

# 套件安裝

```bash
# 套件 Microsoft.Data.Analysis 檔案轉 DataFrame 格式
dotnet add package Microsoft.Data.Analysis --version 0.23.0
```

<!--more-->

# 引用參考

```csharp
using System.IO;
using Microsoft.Data.Analysis;
```

# 載入 CSV 資料

使用 LoadCsvFromString

```csharp
string csvfile = File.ReadAllText("test.csv");
DataFrame df = DataFrame.LoadCsvFromString(csvfile, ',');
Console.WriteLine(df);
```

# 建立測試資料

```csharp
Int32DataFrameColumn id = new Int32DataFrameColumn("ID", new int[] { 1, 2 });
StringDataFrameColumn name = new StringDataFrameColumn("Name", new string[] { "張三", "李四" });
Int32DataFrameColumn age = new Int32DataFrameColumn("Age", new int?[] { 25, null });

DataFrame df = new DataFrame();
df.Columns.Add(id);
df.Columns.Add(name);
df.Columns.Add(age);

Console.WriteLine(df);
```

輸出:
| index | ID | Name | Age |
|-------|----|------|-----|
|   0   | 1  | 張三  | 25  |
|   1   | 2  | 李四  |     |

# 加入新欄位

加入欄位 Height
```csharp
df.Columns.Add(new PrimitiveDataFrameColumn<int>("Height", new List<int>{180, 179}));

Console.WriteLine(df);
```

輸出:
| index | ID | Name | Age | Height |
|-------|----|------|-----|--------|
|   0   | 1  | 張三 | 25  |  180   |
|   1   | 2  | 李四 |     |  179   |

# 顯示欄位資訊

了解欄位類型與資料筆數統計
```csharp
Console.WriteLine(df.Info());
```

輸出:
| index | Info | ID | Name | Age | Height |
|-------|------|----|------|-----|--------|
|   0   | DataType | System.Int32 | System.String | System.Int32 | System.Int32 |
|   1   | Length(excluding null values) | 2  | 2 | 1  |  2  |

# 回傳統計摘要

統計相關值，此欄為數值欄位限定
```csharp
Console.WriteLine(df.Info());
```

輸出:
| index | Description                   | ID  | Age  | Height |
|-------|-------------------------------|-----|------|--------|
|   0   | Length (excluding null values)| 2   | 1    | 2      |
|   1   | Max                           | 2   | 25   | 180    |
|   2   | Min                           | 1   | 25   | 179    |
|   3   | Mean                          | 1.5 | 12.5 | 179.5  |

# 回傳前 N 筆資料

```csharp
Console.WriteLine(df.Head(1));
```

輸出:
| index |  ID  | Name | Age | Height |
|-------|------|------|-----|--------|
|   0   |  1   | 張三  | 25  |  180   |

# 回傳後 N 筆資料

```csharp
Console.WriteLine(df.Tail(1));
```

輸出:
| index |  ID  | Name | Age | Height |
|-------|------|------|-----|--------|
|   0   |   2  | 李四  |     |  179   |

# 加入新資料

填入每一欄對應資料
```csharp
df.Append(new List<KeyValuePair<string, object>>() {
    new KeyValuePair<string, object>("ID", 3),
    new KeyValuePair<string, object>("Name", "王五"),
    new KeyValuePair<string, object>("Age", 20),
    new KeyValuePair<string, object>("Height", 170)
}, true);

Console.WriteLine(df);
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
```csharp
DataFrame dropNullData = df.DropNulls();
Console.WriteLine(dropNullData);
```

輸出:
| index | ID | Name | Age   | Height |
|-------|----|------|-------|--------|
|   0   | 1  | 張三 | 25    |  180   |
|   1   | 3  | 王五 | 20    |  170   |

2. 缺失值填充
```csharp
df.Columns["Age"] = df.Columns["Age"].FillNulls(0);
Console.WriteLine(df);
```

輸出:
| index | ID | Name | Age   | Height |
|-------|----|------|-------|--------|
|   0   | 1  | 張三 | 25     | 180    |
|   1   | 2  | 李四 | 0      | 179    |
|   2   | 3  | 王五 | 20     | 170    |

# 排序資料

```csharp
DataFrame orderByData = df.OrderByDescending("Age");
Console.WriteLine(orderByData);
```

輸出:
| index | ID | Name | Age   | Height |
|-------|----|------|-------|--------|
|   0   | 1  | 張三 | 25     | 180    |
|   1   | 3  | 王五 | 20     | 170    |
|   2   | 2  | 李四 | 0      | 179    |

# 分群資料

```csharp
DataFrame groupByData = df.GroupBy("Age").Count();
Console.WriteLine(groupByData);
```

輸出:
| index | Age |  ID  |  Name  | Height |
|-------|-----|------|--------|--------|
|   0   |  25 |   1  |   1    |   1    |
|   1   |  0  |   1  |   1    |   1    |
|   2   |  20 |   1  |   1    |   1    |

# 篩選資料

```csharp
DataFrame filterData = df.Filter(df.Columns["Age"].ElementwiseGreaterThan(1));
Console.WriteLine(filterData);
```

輸出:
| index | ID | Name | Age   | Height |
|-------|----|------|-------|--------|
|   0   | 1  | 張三 | 25    |  180   |
|   1   | 3  | 王五 | 20    |  170   |

# 合併資料

```csharp
// 建立新的列表
Int32DataFrameColumn idWeight = new Int32DataFrameColumn("ID", new int[] { 1, 2 });
Int32DataFrameColumn weight = new Int32DataFrameColumn("Weight", new int?[] { 60, 90 });

DataFrame dfWeight = new DataFrame();
dfWeight.Columns.Add(idWeight);
dfWeight.Columns.Add(weight);

// 使用 ID 做 Merge
DataFrame mergeData = dfWeight.Merge<int>(df, "ID", "ID");
Console.WriteLine(mergeData);
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
