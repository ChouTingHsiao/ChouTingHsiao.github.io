---
title: 如何在pi 3b+上執行Angular Protractor
date: 2019-12-25 20:59:57
categories: Angular
tags: [Protractor, Pi]
---

# 實作
首先依照硬體架構從[launchpad.net](https://launchpad.net/ubuntu/trusty/+package/chromium-chromedriver)

下載 Chromium 瀏覽器專用的 WebDriver
```bash
wget http://launchpadlibrarian.net/361669488/chromium-chromedriver_65.0.3325.181-0ubuntu0.14.04.1_armhf.deb
```

<!--more-->

接下來進行安裝
```bash 
 sudo dpkg -i chromium-chromedriver_65.0.3325.181-0ubuntu0.14.04.1_armhf.deb
```

預設會放置在
```
/usr/lib/chromium-browser/chromedriver
```

接下來更改protractor.conf.js的設定，加入下方設定
```
chromeDriver: '/usr/lib/chromium-browser/chromedriver'
```

運行Protractor時使用--no-webdriver-update選項
```bash
ng e2e --no-webdriver-update
```

# 參考資料
[樹莓派 Raspberry Pi 使用 Python + Selenium 控制 Chromium 瀏覽器](https://blog.gtwang.org/iot/raspberry-pi/raspberry-pi-install-chromium-chrome-driver/)

[Protractor config設定參考](https://github.com/angular/protractor/blob/master/lib/config.ts)