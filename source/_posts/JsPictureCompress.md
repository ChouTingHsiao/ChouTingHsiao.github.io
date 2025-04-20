---
title: 如何將圖片壓縮並下載檔案
date: 2019-12-25 20:52:20
categories: Javascript
tags: [Javascript, Canvas]
---

# 建立 HTML
首先在 HTML 建立一個 File Upload 與一個 canvas

```html
<input type="file" />
<canvas id="canvas"  width="800" height="600"></canvas>
```

<!--more-->

# 綁定事件
接下來為 File Upload 綁定事件

```javascript
document.querySelector('input').addEventListener('change', function () {
    // 取得第一個檔案
    const file= this.files[0];

    // 取得 canvas
    const canvas = document.getElementById('canvas');   
    
    // 宣告 Image 物件
    let img = new Image; 

    // 建立 Image 載入事件
    img.onload = function () {
        
        // 在 canvas 載入圖片 
        canvas.getContext('2d').drawImage(img,0,0,800,600); 

        // 回收圖片物件
        URL.revokeObjectURL(img.src);   
        
        // 把 canvas 圖片轉成 Blob 格式
        canvas.toBlob((blob) => {
            // 宣告FileReader物件
            var reader = new FileReader();

            // 建立 FileReader 載入完成事件
            reader.onloadend = function () {
                console.log(reader.result.split(",")[1]);
            }
            
            // 讀取 Blob 格式物件
            reader.readAsDataURL(blob);
        }, "image/jpeg", 0.8);
    };
    // 圖片物件載入檔案
    img.src = URL.createObjectURL(file);
}, false);
```

{% note warning %}
📜 參考資料
1. [MDN Canvas_API](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API)
{% endnote %}