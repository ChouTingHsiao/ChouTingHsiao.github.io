---
title: 網頁自訂字型
date: 2021-10-23 18:00:00
categories: HTML
tags: [HTML, CSS, Font, FontForge]
---

# 產生字型檔

WOFF2 檔為壓縮後的檔案，通常會有更快的加載速度，可參考這篇 {% post_link PythonFontForgeTransfer FontForgeTransfer %} ，產生網頁字型 WOFF2 檔

<!--more-->

# 嵌入字型

透過 CSS 嵌入字型
```css
@font-face {
  font-family: '[字型名稱]';
  font-style: normal;
  font-weight: 400;
  src: url(/url/to/[檔案].woff2) format("woff2");
}
```

{% note warning %}
📜 參考資料
1. [@font-face](https://developer.mozilla.org/en-US/docs/Web/CSS/@font-face)
{% endnote %}