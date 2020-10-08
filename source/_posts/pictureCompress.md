---
title: '如何將圖片壓縮並下載檔案'
date: 2019-12-25 20:52:20
categories: Javascript
tags:
  - 
---
# 實作
首先在HTML建立一個File Upload與一個canvas

```html
<input type="file" />
<canvas id="canvas"  width="800" height="600"></canvas>
```

接下來為File Upload綁定事件

```javascript
document.querySelector('input').addEventListener('change', function () {
    //取得第一個檔案
    const file= this.files[0];

    //取得canvas
    const canvas = document.getElementById('canvas');   
    
    //宣告Image物件
    let img = new Image; 

    //建立Image載入事件
    img.onload = function () {
        
        //在canvas載入圖片 
        canvas.getContext('2d').drawImage(img,0,0,800,600); 

        //回收圖片物件
        URL.revokeObjectURL(img.src);   
        
        //把canvas圖片轉成Blob格式
        canvas.toBlob((blob) => {
            //宣告FileReader物件
            var reader = new FileReader();

            //建立FileReader載入完成事件
            reader.onloadend = function () {
                console.log(reader.result.split(",")[1]);
            }
            
            //讀取Blob格式物件
            reader.readAsDataURL(blob);
        }, "image/jpeg", 0.8);
    };
    //圖片物件載入檔案
    img.src = URL.createObjectURL(file);
}, false);
```

# 參考資料
[MDN Canvas_API](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API)
