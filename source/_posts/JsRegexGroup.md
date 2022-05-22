---
title: 如何使用Regex群組
date: 2022-05-22 14:00:00
categories: Javascript
tags: [Regex]
---

# 實作

建立一個名稱為TEST的function

```javascript
// 建立規則
var myRegexp = /(?<群組名稱>.*)Test/g;

// 執行規則
var match =  myRegexp.exec('123Test');

// 取得執行結果 output: 123
console.log(match.groups.群組名稱);
```

<!--more-->

# 參考資料
[MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions/Groups_and_Ranges)