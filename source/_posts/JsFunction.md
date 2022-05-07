---
title: 如何判斷function是否存在
date: 2019-12-28 00:11:51
categories: Javascript
tags:
---

# 實作
建立一個名稱為TEST的function

```javascript
function TEST() {

    console.log('call function TEST');
    
}
```

<!--more-->

判斷function是否存在方法，並執行

```javascript
let funcName = 'TEST';

if (typeof (window[FuncName]) == "function") {
    
        //執行方法
        window[FuncName]();
    
} 
else {

        console.log('此方法不存在');
        
}  
```

# 參考資料
[MDN typeof](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/typeof)