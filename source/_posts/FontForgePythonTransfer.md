---
title: FontForge使用Python Script進行字型檔轉換
date: 2021-10-23 17:00:00
categories: FontForge
tags: [Font, WOFF]
---

# 實作

建立font.py檔案

<!--more-->

```python
import sys
#載入FontForge模組
import fontforge

#定義main函數
def main():	
	#載入參數
	fontFilePath = sys.argv[1]	

	#開啟字型檔
	originFile=fontforge.open(fontFilePath)	

	#選取unicode特定範圍的字型
	originFile.selection.select(("ranges", "unicode"), 0xe000, 0xf8ff)	

	#複製字型至剪貼簿
	originFile.copy()	

	#建立新的字型檔
	newFile=fontforge.font()

	#設定sfnt_names參數與原檔一致
	newFile.sfnt_names = originFile.sfnt_names	

	#設定encoding參數與原檔一致
	newFile.encoding = originFile.encoding	

	#設定size參數與原檔一致
	newFile.em = originFile.em	

	#設定layers參數與原檔一致
	newFile.layers['Fore'].is_quadratic = originFile.layers['Fore'].is_quadratic	

	#選取unicode特定範圍的字型
	newFile.selection.select(("ranges", "unicode"), 0xe000, 0xf8ff)	

	#貼上剪貼簿內容
	newFile.paste()	

	#設定字型名稱
	newFile.fontname="EUDC"	

	#保存為woff字型
	newFile.generate("EUDC.woff")	

	#關閉字型檔
	originFile.close()	

#執行main函數
main()	
```

使用FontForge執行Python Script

```bash
fontforge -script font.py [FontFilePath]
```

# 參考資料
[Python Scripting](https://fontforge.org/docs/scripting/python.html)