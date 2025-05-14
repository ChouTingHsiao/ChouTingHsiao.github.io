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
byte[] big5Bytes = new byte[2];

// big5 數字 0 內碼為 A2AF
string big5Code = "A2AF";

// 內碼前兩字轉為16進制
big5Bytes[0] = (byte)Convert.ToInt32(big5Code.Substring(0, 2), 16);

// 內碼後兩字轉為16進制
big5Bytes[1] = (byte)Convert.ToInt32(big5Code.Substring(2, 2), 16);

// 建立 big5 編碼
Encoding big5 = Encoding.GetEncoding(950);

// 轉成字串
string result = big5.GetString(big5Bytes);
```

<!--more-->

# 轉換 Unicode 字元

Unicode 依傳入內碼轉成字元，與 Big5 轉換的方法雷同，只需調整內碼放置的順序
```C#
// 建立兩位元陣列
byte[] unicodeBytes = new byte[2];

// Unicode 數字 0 內碼為 FF10
string unicodeCode = "FF10";

// 內碼後兩字轉為16進制
unicodeBytes[0] = Convert.ToByte(unicodeCode.Substring(2, 2), 16);

// 內碼前兩字轉為16進制
unicodeBytes[1] = Convert.ToByte(unicodeCode.Substring(0, 2), 16);

// 建立 Unicode 編碼
Encoding unicode = Encoding.Unicode;

// 轉成字串
string result = unicode.GetString(unicodeBytes);
```

{% note warning %}
📜 參考資料
1. [Encoding.GetString Method](https://learn.microsoft.com/en-us/dotnet/api/system.text.encoding.getstring?view=net-8.0)
{% endnote %}