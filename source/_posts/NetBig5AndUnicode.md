---
title: 依字元內碼轉換 Big5 與 Unicode
date: 2022-05-22 14:00:00
categories: .Net
tags: [.Net, Linux, Font, Big5, Unicode]
---

# 轉換 Big5 字元
Big5 依傳入內碼轉成字元

```C#
// 建立兩位元陣列
byte[] codeBytes = new byte[2];

// 欲轉換內碼前兩字轉為16進制
codeBytes[0] = (byte)Convert.ToInt32("欲轉換內碼".Substring(0, 2), 16);

// 欲轉換內碼後兩字轉為16進制
codeBytes[1] = (byte)Convert.ToInt32("欲轉換內碼".Substring(2, 2), 16);

// 轉成字元
Encoding big5 = Encoding.GetEncoding(950);

string result = big5.GetString(bytes);
```

<!--more-->

# 轉換 Unicode 字元
Unicode 依傳入內碼轉成字元，與 Big5 轉換的方法雷同，只需調整內碼放置的順序

```C#
// 建立兩位元陣列
byte[] bytes = new byte[2];

// 欲轉換內碼後兩字轉為16進制
bytes[0] = Convert.ToByte(text.Substring(2, 2), 16);

// 欲轉換內碼前兩字轉為16進制
bytes[1] = Convert.ToByte(text.Substring(0, 2), 16);

// 轉成字元
Encoding unicode= Encoding.Unicode;

string result = unicode.GetString(bytes);
```

{% note warning %}
📜 參考資料
1. [Encoding.GetString Method](https://learn.microsoft.com/en-us/dotnet/api/system.text.encoding.getstring?view=net-8.0)
{% endnote %}