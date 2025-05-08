---
title: 使用 ScottPlot 繪製圖表
date: 2024-06-01 19:10:00
categories: .Net
tags: [.Net, ScottPlot, Polyglot Notebooks]
---

# 初始化設定

安裝套件
```c#
// 套件 Microsoft.Data.Analysis 檔案轉 DataFrame 格式
#r "nuget: Microsoft.Data.Analysis, 0.21.1"

// 套件 ScottPlot 產生圖片
#r "nuget:ScottPlot, 5.0.*"
```

引用套件
```c#
using System.IO;
using Microsoft.Data.Analysis;
using ScottPlot;
using ScottPlot.Plottables;
using ScottPlot.Statistics;

// 設定 ScottPlot 圖片格式
using Microsoft.DotNet.Interactive.Formatting;
Formatter.Register(typeof(ScottPlot.Plot), (p, w) => 
    w.Write(((ScottPlot.Plot)p).GetPngHtml(400, 300)), HtmlFormatter.MimeType);
```

<!--more-->

# 載入 CSV 資料

可參考這篇 {% post_link NetDataFrame DataFrame %} 載入 [📥 CSV 資料](test.csv)

# 建立擴充方法

使用此擴充方法取得特定 Column 資料
```c#
public static double[] GetColumn(this DataFrame df, string column)
{
    return Enumerable.Range(0, (int)df.Rows.Count).Select(x => Convert.ToDouble(df[column][x])).ToArray();
}
```

# 使用 SignalXY 繪圖
```c#
Plot myPlot = new();

// 計算資料筆數
int[] rowIndex = Enumerable.Range(0, (int)df.Rows.Count).ToArray();

// 載入 Duration 欄
double[] durationsY = df.GetColumn("Duration");

SignalXY sigAll1 = myPlot.Add.SignalXY(rowIndex, durationsY);
sigAll1.LegendText = "Duration";

// 載入 Pulse 欄
double[] pulseY = df.GetColumn("Pulse");

SignalXY sigAll2 = myPlot.Add.SignalXY(rowIndex, pulseY);
sigAll2.LegendText = "Pulse";

// 載入 Maxpulse 欄
double[] maxpulseY = df.GetColumn("Maxpulse");

SignalXY sigAll3 = myPlot.Add.SignalXY(rowIndex, maxpulseY);
sigAll3.LegendText = "Maxpulse";

// 載入 Calories 欄
double[] caloriesY = df.GetColumn("Calories");

SignalXY sigAll4 = myPlot.Add.SignalXY(rowIndex, caloriesY);
sigAll4.LegendText = "Calories";

// 設定 Legend 位置
myPlot.Legend.Alignment = ScottPlot.Alignment.UpperRight;

// 顯示圖表
myPlot
```

<!-- myPlot1.GetImage(400, 300).SaveWebp("SignalXY.webp"); -->

![SignalXY](SignalXY.webp)

# 使用 Scatter 繪圖，分析關聯性

使用「Duration」作為 x 軸，使用「Calories」作為 y 軸
```c#
Plot myPlot = new();

// 載入 Scatter
Scatter scatter = myPlot.Add.Scatter(durationsY, caloriesY);

// 移除線條
scatter.LineWidth = 0;

// 顯示圖表
myPlot
```

![Scatter1](Scatter1.webp)

使用「Duration」作為 x 軸，使用「Maxpulse」作為 y 軸
```c#
Plot myPlot = new();

// 載入 Scatter
Scatter scatter = myPlot.Add.Scatter(durationsY, maxpulseY);

// 移除線條
scatter.LineWidth = 0;

// 顯示圖表
myPlot
```

![Scatter2](Scatter2.webp)

# 使用 Bars 繪圖，分析 Duration 頻率

分析 Duration 資料分佈狀態
```c#
Plot myPlot = new();

// 載入 Duration 欄
double[] durationsY = df.GetColumn("Duration");

// 直方圖分箱數
int binCount = 10;

// 獲取直方圖數據
ScottPlot.Statistics.Histogram hist = ScottPlot.Statistics.Histogram.WithBinCount(binCount, durationsY);

// 設定 Bars
BarPlot bar = myPlot.Add.Bars(hist.Bins, hist.GetProbability());

// 設定 Y 軸刻度
myPlot.Axes.Left.TickGenerator = new ScottPlot.TickGenerators.NumericFixedInterval(20);

// 設定 Bars 的顏色與寬度
foreach(var bar in bar.Bars)
{
    bar.LineColor = ScottPlot.Color.FromColor(System.Drawing.Color.Blue);
    bar.LineWidth = 10;
    //bar.Size = hist.FirstBinSize * 0.8;
}

// 繪製機率曲線
ProbabilityDensity pd = new(durationsY);
double[] xs = Generate.Range(durationsY.Min(), durationsY.Max(), 1);
double sumBins = hist.Bins.Select(x => pd.GetY(x)).Sum();
double[] ys = pd.GetYs(xs, 1.0 / sumBins);

Scatter curve = myPlot.Add.ScatterLine(xs, ys);
curve.LineWidth = 2;
curve.LineColor = Colors.Black;
curve.LinePattern = LinePattern.DenselyDashed;

myPlot.Axes.Margins(bottom: 0);

// 顯示圖表
myPlot
```

![Histogram](Histogram.webp)

{% note warning %}
📜 參考資料
1. [scottplot](https://scottplot.net/quickstart/notebook/)
2. [pandas plotting](https://www.w3schools.com/python/pandas/pandas_plotting.asp)
{% endnote %}