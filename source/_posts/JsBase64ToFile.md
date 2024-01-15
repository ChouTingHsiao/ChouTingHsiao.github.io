---
title: 如何將 Base64 編碼轉成檔案
date: 2019-12-09 20:34:09
categories: Javascript
tags: [Javascript, Base64]
---
# Chrome base64 轉換成檔案
下方是使用 JavaScript 把 base64 轉換成檔案的方法

<!--more-->

```javascript 
 function downloadFile(base64Str) {
    // 解碼 base64
    const byteCharacters = atob(base64Str);

    // 建立 Byte Array
    const byteNumbers = new Array(byteCharacters.length);
 
    for (let i = 0; i < byteCharacters.length; i++) {
 
       byteNumbers[i] = byteCharacters.charCodeAt(i);
 
    }
    const byteArray = new Uint8Array(byteNumbers);

    // 建立二進制檔和聲明 MIME Type
    const blob = new Blob([byteArray], {type: 'image/jpg'});
 
    // 下載檔案
    let link = document.createElement('a');
 
    // 建立檔案物件連結
    const blobUrl = URL.createObjectURL(blob);
 
    link.href = blobUrl;
 
    link.download = 'file.jpg';
 
    link.click();
 
    URL.revokeObjectURL(blobUrl);
}
```

# IE 10 base64 轉換成檔案
如果瀏覽器使用 IE 10 以上版本，可使用 window.navigator.msSaveBlob 作為替代方法
```javascript
function downloadFile(base64Str) {
   // 解碼 base64
   const byteCharacters = atob(base64Str);
	
   // 建立 Byte Array
   const byteNumbers = new Array(byteCharacters.length);
	
   for (let i = 0; i < byteCharacters.length; i++) {
      byteNumbers[i] = byteCharacters.charCodeAt(i);
   }
   const byteArray = new Uint8Array(byteNumbers);

   // 建立二進制檔和聲明 MIME Type
   const blob = new Blob([byteArray], {type: 'image/jpg'});
 
   // 下載檔案
   window.navigator.msSaveBlob(blob, 'file.jpg');
};
```

{% note warning %}
📜 參考資料
1. [MDN atob](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/atob)
{% endnote %}