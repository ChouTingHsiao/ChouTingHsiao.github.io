---
title: K6 基本壓測
date: 2025-04-02 17:00:00
categories: Tool
tags: [Tool, K6]
---

# 安裝 k6

<!-- choco install k6 -y -->

```bash
# 從 Ubuntu 金鑰伺服器下載 k6 軟體倉庫的 GPG 公鑰，並儲存到指定的金鑰環中
sudo gpg --no-default-keyring --keyring /usr/share/keyrings/k6-archive-keyring.gpg --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys C5AD17C747E3415A3642D57D77C6C491D6AC1D69

# 將 k6 軟體倉庫加入到 APT 的套件來源清單中，並指定其簽名金鑰
echo "deb [signed-by=/usr/share/keyrings/k6-archive-keyring.gpg] https://dl.k6.io/deb stable main" | sudo tee /etc/apt/sources.list.d/k6.list

# 更新 APT
sudo apt-get update

# 安裝 k6
sudo apt-get install k6
```

<!--more-->

# 建立檔案 script.js

```Javascript
import http from 'k6/http';
import { sleep } from 'k6';

export default function () {
    let url = '[目標 API]';

    let payload = JSON.stringify([請求物件]);

    let params = {
        headers: {
            'accept': 'application/json, text/plain, */*',
            'authorization': '[JWT 認證]',
            'content-type': 'application/json'
        },
    };

    let res = http.post(url, payload, params);
}
```

<!-- 
使用 open api 轉換成 k6 腳本
docker pull openapitools/openapi-generator-cli

docker run --rm -v ${PWD}:/local openapitools/openapi-generator-cli generate \
    -i /location of json file/swagger.json \
    -g k6 \
    -o /local/k6-test/
    --skip-validate-spec
-->

# 測試情境

迭代測試:
情境: 2 個用戶總共執行 2 次

```bash
k6 run test.js --vus 2 --iterations 2
```

冒煙測試: 確認服務基本可用
情境: 3 個用戶 1 分內持續執行

```bash
k6 run test.js --vus 3 --duration 1m
```

負載測試: 確認在預測用戶下的執行效能
情境: 5 分鐘內增加至 100 人，在持續 30 分鐘後，用 5 分鐘內清空人數

```bash
k6 run test.js --stage "5m:100,30m:100,5m:0"
```

壓力測試: 確認流量平緩增加執行的效能
情境: 10 分鐘內增加至 200 人，在持續 30 分鐘後，用 5 分鐘內清空人數

```bash
k6 run test.js --stage "10m:200,30m:200,5m:0"
```

峰值測試: 確認流量瞬間湧入執行的效能
情境: 2 分鐘內增加至 2000 人後，用 1 分鐘內清空人數

```bash
k6 run test.js --stage "2m:2000,1m:0"
```

浸泡測試: 確認長時間持續執行的效能
情境: 5 分鐘內增加至 100 人，在持續 8 小時後，用 5 分鐘內清空人數

```bash
k6 run test.js --stage "5m:100,8h:100,5m:0"
```

斷點測試: 確認資源消耗至最大後的效能
2 小時內，用戶增加至 20000 人

```bash
k6 run test.js --stage "2h:20000"
```

# 結果顯示

## JSON 格式

```bash
k6 run --stage 30s:10 --out json=test-result.json test.js
```

## 儀錶板

```bash
K6_WEB_DASHBOARD='true' \
K6_WEB_DASHBOARD_EXPORT='html-report.html' \
k6 run --stage 30s:10 test.js;
```

<!--
$env:K6_WEB_DASHBOARD='true';
$env:K6_WEB_DASHBOARD_EXPORT='html-report.html';
k6 run --stage 30s:10 test.js;
-->

<!-- 只有當測試持續時間大於K6_WEB_DASHBOARD_PERIOD變數設定的聚合週期值的三倍時，報告才包含圖表 -->

{% note warning %}
📜 參考資料
1. [安裝K6](https://grafana.com/docs/k6/latest/set-up/install-k6/)
2. [測試情境](https://grafana.com/docs/k6/latest/testing-guides/test-types/)
3. [顯示儀錶板](https://grafana.com/docs/k6/latest/results-output/web-dashboard/)
{% endnote %}
