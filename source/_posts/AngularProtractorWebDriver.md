---
title: 如何設定 Protractor WebDriver Version
date: 2020-08-31 22:38:57
categories: Angular
tags: [Angular, Protractor]
---

# 實作
先清除目前下載的 WebDriver

<!--more-->

```bash 
 npx  webdriver-manager clean
```
 
以 chrome 為例從[chromedriver網頁](http://chromedriver.chromium.org/)
 
查詢所需的版本號
 
接下來依版本下載 WebDriver
 
```bash
npx webdriver-manager update  --versions.chrome  [chrome版本號]
```
 
運行 Protractor 時使用 --no-webdriver-update 選項
 
```bash
 ng e2e --no-webdriver-update
```

# 參考資料
[webdriver-manager NPM](https://www.npmjs.com/package/webdriver-manager)