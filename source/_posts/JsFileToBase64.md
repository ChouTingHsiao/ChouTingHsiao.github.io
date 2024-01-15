---
title: 如何將檔案轉成 Base64 編碼
date: 2019-12-09 20:34:09
categories: Javascript
tags: [Javascript, Base64]
---

# 建立 HTML
首先在 HTML 建立一個 File Upload 與一個顯示結果的 div

```html
<input type="file" />

<div id="result"></div>
```

<!--more-->

# 綁定事件

接下來為 File Upload 綁定事件

```javascript
 document.querySelector('input').addEventListener('change', function () {

        // 建立 FileReader 去讀取 File Upload 的內容

        let reader = new FileReader();

        // 設定初始化事件

        reader.onload = function () {

            let arrayBuffer = this.result;

            let base64 = btoa(
                new Uint8Array(arrayBuffer)
                    .reduce((data, byte) => data + String.fromCharCode(byte), '')
            );
        
          document.querySelector('#result').innerHTML = arrayBuffer + '  ' + arrayBuffer.byteLength;    
        }

       // 開始讀取

       reader.readAsArrayBuffer(this.files[0]);

    }, false);
```

{% note warning %}
📜 參考資料
1. [MDN btoa](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/btoa)
{% endnote %}