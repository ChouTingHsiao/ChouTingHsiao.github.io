---
title: '如 何 設 定 Protractor WebDriver Version'
date: 2020-08-31 22:38:57
categories: Angular
tags: [Protractor]
---
# 實作
先清除目前下載的WebDriver
 
```bash 
 npx  webdriver-manager clean
```
 
以chrome為例從[chromedriver.chromium.org](http://chromedriver.chromium.org/)
 
查詢所需的版本號
 
接下來依版本下載WebDriver
 
```bash
npx webdriver-manager update  --versions.chrome  [chrome版本號]
```
 
運行Protractor時使用--no-webdriver-update選項
 
```bash
 ng e2e --no-webdriver-update
```

# 參考資料
[webdriver-manager NPM](https://www.npmjs.com/package/webdriver-manager)
