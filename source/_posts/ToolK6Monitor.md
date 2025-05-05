---
title: K6 壓測可視化
date: 2025-04-02 18:00:00
categories: Tool
tags: [Tool, K6, InfluxDB, Grafana]
---

# 安裝 InfluxDB 儲存壓測資料

使用 Docker 建立 InfluxDB 2.X

```bash
docker run -d -p 8086:8086 \
  -e DOCKER_INFLUXDB_INIT_MODE=setup \
  -e DOCKER_INFLUXDB_INIT_USERNAME=[初始帳號] \
  -e DOCKER_INFLUXDB_INIT_PASSWORD=[初始密碼] \
  -e DOCKER_INFLUXDB_INIT_ORG=[初始ORG] \
  -e DOCKER_INFLUXDB_INIT_BUCKET=[初始BUCKET] \
  -e DOCKER_INFLUXDB_INIT_RETENTION=1w \
  -e DOCKER_INFLUXDB_INIT_ADMIN_TOKEN=[初始TOKEN] \
  influxdb
```

<!--more-->

<!-- 建立完成後，使用初始帳密登入連結 http://localhost:8086/ -->

建立完成後，使用 API 檢查 BUCKET

```bash
curl --request GET "http://localhost:8086/api/v2/buckets?name=[初始BUCKET]" \
    --header "Authorization: Token [初始TOKEN]"
```

## 匯入資料

InfluxDB 2.X 需另外建置 K6

### 安裝 golang

```bash
# 加入 golang PPA
sudo add-apt-repository ppa:longsleep/golang-backports

# 更新 PPA
sudo apt update

# 安裝 golang
sudo apt install golang-go
```

<!-- choco install golang -y  -->

### 安裝套件 xk6

```bash
# 建立工作目錄
mkdir go

# 前往工作目錄
cd go

# 安裝 xk6
go install go.k6.io/xk6/cmd/xk6@latest
```

### 建置 k6.exe

使用語法 xk6 build --with [套件 1] --with [套件 2]

```bash
bin/xk6 build --with github.com/grafana/xk6-output-influxdb --with github.com/grafana/xk6-dashboard@latest
```

### 執行 k6 壓測

```bash
K6_INFLUXDB_ORGANIZATION="[初始ORG]" \
K6_INFLUXDB_BUCKET="[初始BUCKET]" \
K6_INFLUXDB_TOKEN="[初始TOKEN]" \
K6_INFLUXDB_ADDR="http://localhost:8086" \
./k6 run -o xk6-influxdb --vus 3 --duration 1m test.js
```

<!--
$env:K6_INFLUXDB_ORGANIZATION='[初始ORG]' ;
$env:K6_INFLUXDB_BUCKET='[初始BUCKET]' ;
$env:K6_INFLUXDB_TOKEN='[初始TOKEN]' ;
.\k6.exe run -o xk6-influxdb=http://localhost:8086 --vus 3 --duration 1m test.js
-->

## 查詢資料

安裝 VS Code 套件 [Flux](https://marketplace.visualstudio.com/items?itemName=influxdata.flux)，建立檔案 test.flux 內容如下，使用 F5 執行

```flux
from(bucket: "k6_test")
  |> range(start: time(v: "2025-04-08"), stop: time(v: "2025-04-09"))
  |> filter(fn: (r) => r["_measurement"] == "http_req_duration")
  |> aggregateWindow(every: duration(v: "7d"), fn: last, createEmpty: false)
  |> yield(name: "last")
```

# 安裝 Grafana 呈現壓測資料

透過 Grafana 儀表板呈現 InfluxDB 資料

## 安裝 Grafana

使用 Docker 建立 Grafana

```bash
docker run --name=grafana -d -p 3000:3000 \
  -e GF_SECURITY_ADMIN_USER=[初始帳號] \
  -e GF_SECURITY_ADMIN_PASSWORD=[初始密碼] \
  grafana/grafana
```

<!-- 建立完成後，使用初始帳密登入連結 http://localhost:3000/ -->

<!-- 左側點擊 Administration > Users and access > Service Accounts > Add service account -->

## 取得 Token

建立 Service Account

```bash
curl -X POST http://admin:admin123@localhost:3000/api/serviceaccounts \
  -H "Content-Type: application/json" \
  -d '{
	  "name": "grafana",
	  "role": "Admin",
	  "isDisabled": false
	}'
```

建立 Service Account Token，使用此 Token 呼叫特權 API

```bash
curl -X POST http://admin:admin123@localhost:3000/api/serviceaccounts/2/tokens \
  -H "Content-Type: application/json" \
  -d '{
      "name": "grafana",
      "secondsToLive": 604800
    }'
```

<!-- 左側點擊 Connections => Add new connection => 搜尋 influxdb -->

## 資料來源

透過 API 查詢資料來源

```bash
curl -X GET http://localhost:3000/api/datasources/name/influxdb_test \
    -H "Accept: application/json"  \
	-H "Content-Type: application/json"  \
	-H "Authorization: Bearer [Service_Account_Token]"
```

透過 API 建立資料來源

```bash
curl -X POST http://localhost:3000/api/datasources \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer [Service_Account_Token]" \
  -d '{
    "name": "influxdb_test",
    "type": "influxdb",
    "access": "proxy",
    "url": "http://localhost:8086",
    "basicAuth": false,
    "jsonData": {
      "version": "Flux",
      "organization": "[初始ORG]",
      "defaultBucket": "[初始BUCKET]",
      "tlsSkipVerify": false
    },
    "secureJsonData": {
      "token": "[InfluxDB初始TOKEN]"
    }
  }'
```

<!-- 左側點擊 Dashboards =>  New dashboard -->

## 儀表板

透過 API 建立儀表板

```bash
curl -X POST http://localhost:3000/api/dashboards/db \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer [Service_Account_Token]" \
  -d '{
    "dashboard": {
      "id": null,
      "uid": null,
      "title": "InfluxDB Flux Demo",
      "timezone": "browser",
      "schemaVersion": 38,
      "version": 0,
      "refresh": "30s",
      "panels": [
        {
          "type": "timeseries",
          "title": "http_req_duration",
          "datasource": {
            "type": "influxdb",
            "uid": "eei9mxy3v7egwc"
          },
          "targets": [
            {
              "refId": "A",
              "queryType": "flux",
              "query": "from(bucket: \"k6_test\") |> range(start: v.timeRangeStart, stop: v.timeRangeStop) |> filter(fn: (r) => r[\"_measurement\"] == \"http_req_duration\") |> aggregateWindow(every: v.windowPeriod, fn: last, createEmpty: false) |> yield(name: \"last\")"
            }
          ],
          "fieldConfig": {
            "defaults": {},
            "overrides": []
          },
          "gridPos": {
            "h": 9,
            "w": 12,
            "x": 0,
            "y": 0
          }
        }
      ]
    },
    "folderId": 0,
    "overwrite": true
  }'
```

{% note warning %}
📜 參考資料
1. [InfluxDB v2 OSS 文檔](https://docs.influxdata.com/influxdb/v2/api/v2/)
2. [Service account API](https://grafana.com/docs/grafana/latest/developers/http_api/serviceaccount/)
3. [Data source API](https://grafana.com/docs/grafana/latest/developers/http_api/data_source/)
4. [Dashboard API](https://grafana.com/docs/grafana/latest/developers/http_api/dashboard/)
{% endnote %}
