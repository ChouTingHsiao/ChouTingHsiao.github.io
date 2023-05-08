---
title: 如何迭代 Js 物件
date: 2020-03-27 20:42:46
categories: Javascript
tags: [Javascript, Object]
---

# 實作
使用 for...in 語法
```javascript
for (變數 in 物件) {...
}
```

<!--more-->

下方為 Sample Code
```javascript
let model = {
    item: '1',
};

for (let filed in model) {
    console.log(model[filed]);
}
```

使用 Object.keys()
```javascript
Object.keys(物件);
```

下方為 Sample Code
```javascript
let model = {
    item: '1',
};

let keys = Object.keys(model);

let length = keys.length;

for (let i = 0; i <= length; i++) {
    let key =  keys[i];
    console.log(model[key]);
}
```

Object.keys 與 for...in 不同之處
```
for-in 迴圈還會迭代出物件自其原型鏈所繼承來的可列舉屬性
```

{% note warning %}
參考資料
[MDN Object keys](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/keys)
{% endnote %}