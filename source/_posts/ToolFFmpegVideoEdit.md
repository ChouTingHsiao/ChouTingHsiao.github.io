---
title: 使用 FFmpeg 剪接影片
date: 2022-01-01 13:00:00
categories: Tool
tags: [Tool, FFmpeg]
---

# 查詢版本

```bash
ffmpeg -version
```

<!--more-->

# 剪裁影片

```bash
ffmpeg -i [輸入影片].mp4 -ss 00:00:00 -to 00:00:00 -c copy [輸出影片].mp4
```

| 參數           | 說明                    |
| ------------ | --------------------- |
| `-i`         | 指定輸入的影片檔案             |
| `-ss`        | 設定起始時間（格式：`HH:MM:SS`） |
| `-to`        | 設定結束時間（格式：`HH:MM:SS`） |
| `-c copy`    | 拷貝編碼流，不重新編碼，快速且無畫質損失  |
| `[輸出影片].mp4` | 指定輸出的影片檔案名稱           |

# 加入浮水印

```bash
ffmpeg -i [輸入影片].mp4 -vf "drawtext=text='[浮水印文字]':fontcolor=white:fontsize=24:x=10:y=10" -c:a copy [輸出影片].mp4
```

| 參數                | 說明                                      |
| ----------------- | --------------------------------------- |
| `-vf`             | 指定 video filter，此處使用 `drawtext` 套用文字浮水印 |
| `text='[文字]'`     | 浮水印的文字內容                                |
| `fontcolor=white` | 設定文字顏色                                  |
| `fontsize=24`     | 設定字體大小                                  |
| `x=10, y=10`      | 設定浮水印在畫面中的位置（左上角）                       |


# 合併影片

建立 filelist.txt 內容如下

```text
file '[輸入影片1].mp4'
file '[輸入影片2].mp4'
```

合併 filelist.txt 中的檔案，變一支影片

```bash
ffmpeg -f concat -safe 0 -i filelist.txt -c copy [輸出影片].mp4
```

| 參數                | 說明                      |
| ----------------- | ----------------------- |
| `-f concat`       | 使用 concat 模式合併多部影片      |
| `-safe 0`         | 關閉安全限制，允許使用相對路徑或特殊字元的檔名 |
| `-i filelist.txt` | 指定合併清單                  |
| `-c copy`         | 拷貝原始編碼，不重新編碼            |

# 嵌入字幕

```bash
ffmpeg -i [輸入影片].mp4 -vf subtitles=[字幕檔].srt [輸出影片].mp4
```

| 參數                        | 說明                                  |
| ------------------------- | ----------------------------------- |
| `-vf subtitles=[字幕檔].srt` | 套用 `subtitles` 濾鏡，將 `.srt` 字幕渲染到影片中 |

# 抽取音訊

```bash
ffmpeg -i [輸入影片].mp4 -q:a 0 -map a [輸出音訊].mp3
```

| 參數       | 說明                                         |
| -------- | ------------------------------------------ |
| `-q:a 0` | 設定音訊品質，數值越小品質越高（0 為最佳）                     |
| `-map a` | 選擇音訊流作為輸出來源，若影片有多條音軌可加上 `-map 0:a:0` 指定第一條 |

# 抽取圖片

```bash
ffmpeg -i [輸入影片].mp4 -ss 00:00:05 -vframes 1 [輸出圖片].jpg
```

| 參數           | 說明                     |
| ------------ | ---------------------- |
| `-ss`        | 設定擷取時間點（格式：`HH:MM:SS`） |
| `-vframes 1` | 輸出 1 幀影像，即 1 張圖片       |

{% note warning %}
📜 參考資料
1. [ffmpeg 文件](https://www.ffmpeg.org/documentation.html)
{% endnote %}