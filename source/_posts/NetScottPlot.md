---
title: 在 Polyglot Notebooks 使用 ScottPlot
date: 2024-06-01 19:10:00
categories: .Net
tags: [.Net, ScottPlot, Polyglot Notebooks]
---

# 初始化設定
```c#
// 套件 Microsoft.Data.Analysis 檔案轉 DataFrame 格式
#r "nuget: Microsoft.Data.Analysis, 0.21.1"
// 套件 MathNet.Numerics 進行數學計算
#r "nuget: MathNet.Numerics, 5.0.0"
// 套件 ScottPlot 產生圖片
#r "nuget:ScottPlot, 5.0.*"

using System.IO;
using Microsoft.Data.Analysis;
using MathNet.Numerics.Distributions;

// 設定 ScottPlot 圖片格式
using Microsoft.DotNet.Interactive.Formatting;
Formatter.Register(typeof(ScottPlot.Plot), (p, w) => 
    w.Write(((ScottPlot.Plot)p).GetImageHtml(400, 300)), HtmlFormatter.MimeType);
```

<!--more-->

# 載入 CSV 資料
可參考這篇 {% post_link PolyglotNotebooksDataFrame DataFrame %} 載入 [CSV 資料](test.csv)

# 使用 SignalXY 繪圖
```c#
ScottPlot.Plot myPlot1 = new();

// 計算資料筆數
int[] rowIndex = Enumerable.Range(0, (int)df.Rows.Count).ToArray();

// 載入 Duration 欄
double[] durationsY = Enumerable.Range(0, (int)df.Rows.Count).Select(x => Convert.ToDouble(df["Duration"][x])).ToArray();

ScottPlot.Plottables.SignalXY sigAll1 = myPlot1.Add.SignalXY(rowIndex, durationsY);
sigAll1.LegendText = "Duration";

// 載入 Pulse 欄
double[] pulseY = Enumerable.Range(0, (int)df.Rows.Count).Select(x => Convert.ToDouble(df["Pulse"][x])).ToArray();

ScottPlot.Plottables.SignalXY sigAll2 = myPlot1.Add.SignalXY(rowIndex, pulseY);
sigAll2.LegendText = "Pulse";

// 載入 Maxpulse 欄
double[] maxpulseY = Enumerable.Range(0, (int)df.Rows.Count).Select(x => Convert.ToDouble(df["Maxpulse"][x])).ToArray();

ScottPlot.Plottables.SignalXY sigAll3 = myPlot1.Add.SignalXY(rowIndex, maxpulseY);
sigAll3.LegendText = "Maxpulse";

// 載入 Calories 欄
double[] caloriesY = Enumerable.Range(0, (int)df.Rows.Count).Select(x => Convert.ToDouble(df["Calories"][x])).ToArray();

ScottPlot.Plottables.SignalXY sigAll4 = myPlot1.Add.SignalXY(rowIndex, caloriesY);
sigAll4.LegendText = "Calories";

// 設定 Legend 位置
myPlot1.Legend.Alignment = ScottPlot.Alignment.UpperRight;

// 顯示圖表
myPlot1
```

<!-- myPlot1.GetImage(400, 300).SaveWebp("SignalXY.webp"); -->

![SignalXY](SignalXY.webp)

# 使用 Scatter 繪圖，分析關聯性

使用「Duration」作為 x 軸，使用「Calories」作為 y 軸
```c#
ScottPlot.Plot myPlot2 = new();

// 載入 Scatter
ScottPlot.Plottables.Scatter sp1 = myPlot2.Add.Scatter(durationsY, caloriesY);

// 移除線條
sp1.LineWidth = 0;

// 顯示圖表
myPlot2
```

![Scatter1](Scatter1.webp)

使用「Duration」作為 x 軸，使用「Maxpulse」作為 y 軸
```c#
ScottPlot.Plot myPlot3 = new();

// 載入 Scatter
ScottPlot.Plottables.Scatter sp2 = myPlot3.Add.Scatter(durationsY, maxpulseY);

// 移除線條
sp2.LineWidth = 0;

// 顯示圖表
myPlot3
```

![Scatter2](Scatter2.webp)

# 使用 Bars 繪圖，分析 Duration 頻率

目前 ScottPlot 5.0 版未實現直方圖，搭配 MathNet.Numerics 做計算

```c#
ScottPlot.Plot myPlot4 = new();

// 載入 Duration 欄
double[] durationsY = Enumerable.Range(0, (int)df.Rows.Count).Select(x => Convert.ToDouble(df["Duration"][x])).ToArray();

// 直方圖分箱數
int binCount = 10;

// 獲取直方圖數據
var histogram = new MathNet.Numerics.Statistics.Histogram(durationsY, binCount);

double[] binEdges = new double[binCount + 1];
double[] binCounts = new double[binCount];

for (int i = 0; i < binCount; i++)
{
    binEdges[i] = histogram[i].LowerBound;
    binCounts[i] = histogram[i].Count;
}
binEdges[binCount] = histogram[binCount - 1].UpperBound;

// 設定 Bars
ScottPlot.Plottables.BarPlot bar = myPlot4.Add.Bars(binEdges.Take(binCount).ToArray(), binCounts);

// 設定 Y 軸刻度
myPlot4.Axes.Left.TickGenerator = new ScottPlot.TickGenerators.NumericFixedInterval(20);

// 設定 Bars 的顏色與寬度
foreach(var item in bar.Bars){
    item.BorderColor = ScottPlot.Color.FromColor(System.Drawing.Color.Blue);
    item.BorderLineWidth = 10;
}

// 顯示圖表
myPlot4
```

![Win10ProxySetting](Histogram.webp)

{% note warning %}
📜 參考資料
1. [scottplot](https://scottplot.net/quickstart/notebook/)
2. [pandas plotting](https://www.w3schools.com/python/pandas/pandas_plotting.asp)
{% endnote %}