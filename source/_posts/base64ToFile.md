---
title: '如何將base64編碼轉成檔案'
date: 2019-12-09 20:34:09
categories: Javascript
tags:
  - base64
---
# 實作
下方是把base64轉換成檔案的方法
```javascript 
 function downloadFile(base64Str) {
    //解碼base64
    const byteCharacters = atob(base64Str);

    //建立Byte Array
    const byteNumbers = new Array(byteCharacters.length);
 
    for (let i = 0; i < byteCharacters.length; i++) {
 
       byteNumbers[i] = byteCharacters.charCodeAt(i);
 
    }
    const byteArray = new Uint8Array(byteNumbers);

    //建立二進制檔和聲明MIME Type
    const blob = new Blob([byteArray], {type: 'image/jpg'});
 
    //下載檔案
    let link = document.createElement('a');
 
    //建立檔案物件連結
    const blobUrl = URL.createObjectURL(blob);
 
    link.href = blobUrl;
 
    link.download = 'file.jpg';
 
    link.click();
 
    URL.revokeObjectURL(blobUrl);
}
```
如果瀏覽器使用IE 10以上版本，可使用 window.navigator.msSaveBlob 作為替代方法
```javascript
function downloadFile(base64Str) {
   //解碼base64
   const byteCharacters = atob(base64Str);
	
   //建立Byte Array
   const byteNumbers = new Array(byteCharacters.length);
	
   for (let i = 0; i < byteCharacters.length; i++) {
      byteNumbers[i] = byteCharacters.charCodeAt(i);
   }
   const byteArray = new Uint8Array(byteNumbers);

   //建立二進制檔和聲明MIME Type
   const blob = new Blob([byteArray], {type: 'image/jpg'});
 
   //下載檔案
   window.navigator.msSaveBlob(blob, 'file.jpg');
};
```

# 參考資料
[MDN atob](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/atob)
