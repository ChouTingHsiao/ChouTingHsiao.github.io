---
title: 使用 FFmpeg 產生 HLS 影片
date: 2022-01-01 14:00:00
categories: Tool
tags: [Tool, FFmpeg]
---

# 產生影片

```bash
ffmpeg -i [輸入影片].mp4 \
  -c:v libx264 -c:a aac \
  -hls_time 10 \
  -hls_list_size 0 \
  -hls_segment_filename "segment_%03d.ts" \
  -f hls playlist.m3u8
```

| **參數**                                    | **說明**                                                  |
| ----------------------------------------- | ------------------------------------------------------- |
| `-c:v libx264`                            | 設定視頻編解碼器為 `libx264`（H.264 編解碼器），適用於 HLS 串流 |
| `-c:a aac`                                | 設定音頻編解碼器為 `aac`，適用於 HLS 串流 |
| `-hls_time 10`                            | 設定每個 HLS 分段的時長（單位：秒），此處為 10 秒，表示將影片分割成每 10 秒一個片段 |
| `-hls_list_size 0`                        | 設定播放清單的大小， `0` 表示清單包含所有的段，不會移除任何片段，若設為正整數，則只保留最新的 `N` 段 |
| `-hls_segment_filename "segment_%03d.ts"` | 設定每個 HLS 分段檔案的命名規則， `%03d` 會生成從 `001` 開始遞增的檔名 |
| `-f hls`                                  | 指定輸出格式為 HLS |
| `playlist.m3u8`                           | 指定輸出的 M3U8 播放清單檔案名稱，此檔案將包含 HLS 影片分段的播放清單 |


<!--more-->

# 模擬 http 服務

安裝 npm 套件 http-server

```bash
npm install -g http-server
```

使用 8080 啟動服務

```bash
http-server -p 8080 --cors
```

# 使用 hls.js

在網頁建立 video 標籤

```html
<video id="video" controls width="640" height="360"></video>
```

引用 hls.js CDN

```html
<script src="https://cdn.jsdelivr.net/npm/hls.js@latest"></script>
```

播放分割好的影片

```js
    const video = document.getElementById('video');
    const videoSrc = 'http://localhost:8080/playlist.m3u8'; // 根據實際路徑調整

    if (Hls.isSupported()) {
        const hls = new Hls();
        hls.loadSource(videoSrc);
        hls.attachMedia(video);
        hls.on(Hls.Events.MANIFEST_PARSED, function () {
            video.play();
        });
    } else if (video.canPlayType('application/vnd.apple.mpegurl')) {
        // Safari 原生支援 HLS
        video.src = videoSrc;
        video.addEventListener('loadedmetadata', function () {
        video.play();
        });
    } else {
        alert("您的瀏覽器不支援 HLS");
    }
```

{% note warning %}
📜 參考資料
1. [hls.js Demo](https://hlsjs.video-dev.org/demo/)
{% endnote %}