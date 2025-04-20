---
title: 如何在 Iframe 讀取或寫入字串
date: 2019-12-28 00:22:05
categories: Javascript
tags: [Javascript, Iframe]
---

# 建立 HTML
首先在 HTML 建立一個 iframe

```html
<iframe id="test"></iframe>
```

<!--more-->

# 操作 iframe
用 Javascript 搜尋 iframe 的DOM物件
```javascript
let iframe = document.getElementById('test').contentDocument;
```

字串寫入 iframe
```javascript
iframe.open();

iframe.write('<a>test</a>');

iframe.close();
```

取得 iframe 字串
```javascript
iframe.body.innerText
```

URL.createObjectURL 寫入 iframe
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

{% note warning %}
📜 參考資料
1. [MDN Document write](https://developer.mozilla.org/en-US/docs/Web/API/Document/write)
{% endnote %}