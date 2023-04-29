---
title: 如何在iframe讀取或寫入字串
date: 2019-12-28 00:22:05
categories: Javascript
tags:
---

# 實作
首先在HTML建立一個iframe

```html
<iframe id="test"></iframe>
```

<!--more-->

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

URL.createObjectURL寫入iframe
```javascript
const blobx = new Blob(
    ['<base href="[URL輸入]">' + data],
    {
        type: 'text/html',
    },
)

const urlObject = URL.createObjectURL(blobx)

document.getElementById('test').setAttribute('src', urlObject)
```

# 參考資料
[MDN Document write](https://developer.mozilla.org/en-US/docs/Web/API/Document/write)