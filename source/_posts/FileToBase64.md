---
title: 如何將檔案轉成base64編碼
date: 2019-12-09 20:34:09
categories: Javascript
tags:
  - base64
---

# 實作
首先在HTML建立一個File Upload與一個顯示結果的div

```html
<input type="file" />

<div id="result"></div>
```

<!--more-->

接下來為File Upload綁定事件

```javascript
 document.querySelector('input').addEventListener('change', function () {

        //建立FileReader去讀取File Upload的內容

        let reader = new FileReader();

        //設定初始化事件

        reader.onload = function () {

            let arrayBuffer = this.result;

            let base64 = btoa(
                new Uint8Array(arrayBuffer)
                    .reduce((data, byte) => data + String.fromCharCode(byte), '')
            );
        
          document.querySelector('#result').innerHTML = arrayBuffer + '  ' + arrayBuffer.byteLength;    
        }

       //開始讀取

       reader.readAsArrayBuffer(this.files[0]);

    }, false);
```

# 參考資料
[MDN btoa](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/btoa)