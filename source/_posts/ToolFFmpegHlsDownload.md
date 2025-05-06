---
title: 使用 FFmpeg 下載 HLS 影片
date: 2022-01-01 15:00:00
categories: Tool
tags: [Tool, FFmpeg]
---

{% note danger %}
⚠️ 請注意，本範例僅供學習參考，請遵守各平台影片使用規範與相關法令，產生之行為與後果將由使用者自行負責
{% endnote %}

# 下載檔案

使用 FFmpeg 下載檔案，並轉換成 MP4 格式，如果需要驗證 Cookie，可使用 headers 選項

```bash
ffmpeg -headers "Cookie: [Cookie替換]" -i "[m3u8網址]" -c copy -bsf:a aac_adtstoasc [輸出影片].mp4
```

參數解析

| 參數                                              | 說明                                                 |
| -------------------------------------------------| ---------------------------------------------------- |
| <span class="mkd-table">`-headers "Cookie: [Cookie替換]"`</span> | 傳送 HTTP 標頭，通常用於需要驗證或存取受限的 m3u8 資源 |
| `-i "[m3u8網址]"`          | 指定輸入來源為 m3u8 串流清單的網址                    |
| `-bsf:a aac_adtstoasc`     | 對音訊流（AAC）應用位元流過濾器（bitstream filter），將 ADTS 格式轉換為適合 MP4 的格式 (通常 m3u8 使用 ADTS 格式)|

<!--more-->

# 搜尋影片清單

但通常影片都會切割成好幾個段落，所以須要在 Chrome DevTools 的 Network 分頁中，搜尋 .m3u8 相關的檔案，並找到如下格式檔案

```text
#EXTM3U
#EXT-X-VERSION:3
#EXT-X-TARGETDURATION:4
#EXT-X-MEDIA-SEQUENCE:0
#EXTINF:2.000000,
0000.ts
...
#EXTINF:2.000000,
0003.ts
#EXT-X-ENDLIST
```

# 下載影片清單

因檔案通常會有很多，可使用下方腳本下載影片清單

```bash
#!/bin/bash

# 設定在 Chrome DevTools 找到的清單
BASE_URL="[網址]"
M3U8_URL="$BASE_URL/[檔案].m3u8"

# 使用 curl 下載 m3u8
curl -s "$M3U8_URL" -o [檔案].m3u8

# 擷取 ts 檔案名稱
grep '\.ts' [檔案].m3u8 > ts_list.txt

# 建立目錄
mkdir -p ts_files

# 下載每個 ts 檔案至 ts_files
cd ts_files
while read -r line; do
    echo "Downloading $line"
    curl -O "$BASE_URL/$line"
done < ../ts_list.txt

# 合併為 ts 檔案
cd ..
cat ts_files/*.ts > merged.ts

# 使用 FFmpeg 轉換為 MP4
ffmpeg -i merged.ts -c copy -bsf:a aac_adtstoasc output.mp4
```

{% note warning %}
📜 參考資料
1. [ffmpeg 文件](https://www.ffmpeg.org/documentation.html)
{% endnote %}