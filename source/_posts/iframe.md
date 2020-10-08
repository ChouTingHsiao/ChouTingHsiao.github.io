---
title: '如何在iframe讀取或寫入字串'
date: 2019-12-28 00:22:05
categories: Javascript
tags:
---
# 實作
首先在HTML建立一個iframe

```html
<iframe id="test"></iframe>
```

用Javascript搜尋iframe的DOM物件
```javascript
let iframe = document.getElementById('test').contentDocument;
```

字串寫入iframe
```javascript
iframe.open();

iframe.write('<a>test</a>');

iframe.close();
```

取得iframe字串
```javascript
iframe.body.innerText
```

# 參考資料
[MDN Document write](https://developer.mozilla.org/en-US/docs/Web/API/Document/write)