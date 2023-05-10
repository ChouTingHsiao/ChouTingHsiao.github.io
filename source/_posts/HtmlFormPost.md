---
title: Json 格式資料如何拆解成 form 的輸入項
date: 2020-03-27 20:43:06
categories: Html
tags: [Html, Json]
---

# 實作
下方是 Json 格式資料
```json
{
   "item": "1",
   "items": ["1","2"],
}
```

<!--more-->

基本物件型別的拆解如下
```html
"item": "1"
可拆解成:
<input type="text" name="item" value="1"><br>
```

陣列部分需依造陣列結構做拆解
```html
"items": ["1","2"]
可拆解成:
<input type="text" name="items[0]" value="1">
<input type="text" name="items[1]" value="2">
```

最後是完整的 HTML form 設定
```html
<form action="/api/Test/Post" method="post">
  <input type="text" name="item" value="1"><br>
  <input type="text" name="items[0]" value="1"><br>
  <input type="text" name="items[1]" value="2"><br>
　<input type="submit" value="送出表單">
</form>
```

{% note warning %}
參考資料
1. [POST an array from an HTML form without javascript](https://stackoverflow.com/questions/9073690/post-an-array-from-an-html-form-without-javascript)
{% endnote %}