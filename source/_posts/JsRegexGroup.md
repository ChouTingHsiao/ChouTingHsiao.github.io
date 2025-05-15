---
title: 如何使用 Regex 群組
date: 2022-05-22 14:00:00
categories: Javascript
tags: [Javascript, Regex]
---

# 使用 Regex 群組

群組方法用法如下

<!--more-->

```javascript
// 建立規則
var myRegexp = /(?<群組名稱>.*)Test/g;

// 執行規則
var match =  myRegexp.exec('123Test');

// 取得執行結果 output: 123
console.log(match.groups.群組名稱);
```

{% note warning %}
📜 參考資料
1. [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions/Groups_and_Ranges)
{% endnote %}