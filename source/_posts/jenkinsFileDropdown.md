---
title: '使用Linux檔案列表建立Jenkins下拉選單'
date: 2021-10-05 22:00:00
categories: Jenkins
tags:
---
>### 取得檔案列表方法

使用以下Linxu指令，將預設目錄每個檔案使用一列的方式逐一列出
```bash
ls -1 /home/ubuntu
```
>### 建立Jenkins Pipeline

在Pipeline執行Linxu指令，把結果存入變數
```groovy
def fileInFolder = sh(script: "ls -1 /home/ubuntu", returnStdout: true).trim();
```
在Pipeline定義Dialog，把 parameters -> choice -> choices定義替換為上方變數 fileInFolder
```groovy
def dialogMessage = "Release ${JOB_NAME} now?"

def confirmDialog =
    input message: dialogMessage,
    parameters: [choice(name: 'action', choices: fileInFolder, description: '')],
    submitterParameter: 'releaseApprover'
```
取得用戶選取參數，使用變數 confirmDialog -> parameters -> choice -> name的定義
```groovy
confirmDialog.action
```
取得點擊確認的用戶，使用變數 confirmDialog -> submitterParameter的定義
```groovy
confirmDialog.releaseApprover