---
title: tshark 基本操作
date: 2021-10-23 19:00:00
categories: Tool
tags: [Tool, Wireshark]
---

# 安裝 Wireshark

tshark 是 Wireshark 的命令行界面，因此需安裝 Wireshark
```powershell
scoop install extras/wireshark
```

<!--more-->

# 查詢版本

查詢目前 tshark 版本
```powershell
tshark -v
```

# 網路介面

查詢網路介面
```powershell
tshark -D
```

# 擷取封包

擷取封包資料，並匯出 pcap
```powershell
tshark -i [網路介面編號] -b duration:[每隔幾秒執行] -c [指定擷取筆數] -w [匯出檔案].pcap
```

讀取 pcap 檔案
```powershell
tshark -r [匯出檔案].pcap
```

顯示指定欄位，來源 IP、目的 IP 、 TCP port
```powershell
tshark -i [網路介面編號] -T fields -e ip.src -e ip.dst -e tcp.port
```

# 解析 TLS

設定環境變數讓瀏覽器緩存 SSL KEY 至指定檔案
```powershell
setx SSLKEYLOGFILE "C:\path\to\sslkeys.log"
```

緩存封包
```powershell
tshark -i [網路介面編號] -w https_traffic.pcapng
```

使用指定 SSL KEY 讀取檔案
```powershell
tshark -r https_traffic.pcapng -o tls.keylog_file:"C:\path\to\sslkeys.log"
```

# 解析 Body

解析 HTTP Body，存入變數 hex
```powershell
$hex = tshark -r https_traffic.pcapng `
			  -Y 'http.request.method == "[HTTP的方法]" && http.request.uri == \"[URL連結]\"' `
              -T fields -e http.file_data `
              -o tls.keylog_file:"C:\path\to\sslkeys.log"
```

建立方法解析 Hex
```powershell
function Convert-HexToUtf8String {
    param([string]$hex)

    # 移除所有非十六進位字元（保險做法）
    $cleanHex = $hex -replace '[^0-9a-fA-F]', ''

    if ($cleanHex.Length % 2 -ne 0) {
        throw "Hex string 長度為奇數，無法正確解碼。"
    }

    $bytes = for ($i = 0; $i -lt $cleanHex.Length; $i += 2) {
        [Convert]::ToByte($cleanHex.Substring($i, 2), 16)
    }

    return [System.Text.Encoding]::UTF8.GetString($bytes)
}
```

Hex 轉文字
```powershell
$decoded = Convert-HexToUtf8String -hex $hex
echo $decoded
```

{% note warning %}
📜 參考資料
1. [tshark(1) Manual Page](https://www.wireshark.org/docs/man-pages/tshark.html)
{% endnote %}