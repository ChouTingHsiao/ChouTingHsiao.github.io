---
title: 如何判斷 Function 是否存在
date: 2019-12-28 00:11:51
categories: Javascript
tags: [Javascript, Function]
---

# 建立 function
建立一個名稱為 TEST 的 function

```javascript
function TEST() {

    console.log('call function TEST');
    
}
```

<!--more-->

# 判斷 function 是否存在
判斷 function 是否存在 Window 變數中，並執行

```javascript
let funcName = 'TEST';

if (typeof (window[FuncName]) == "function") {
        // 執行方法
        window[FuncName]();
} 
else {
        console.log('此方法不存在');
}  
```

{% note warning %}
📜 參考資料
1. [MDN typeof](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/typeof)
{% endnote %}