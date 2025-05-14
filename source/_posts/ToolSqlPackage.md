---
title: 使用 SqlPackage 備份資料庫
date: 2024-07-25 18:00:00
categories: Tool
tags: [Tool, .Net, SqlPackage, MSSQL]
---

# 安裝 SqlPackage

```powershell
dotnet tool install -g microsoft.sqlpackage
```

# 查詢版本

```powershell
sqlpackage /Version
```

<!--more-->

# 使用 BACPAC 備份

預設包含數據庫結構和數據

## 匯出資料庫

```powershell
sqlpackage /Action:Export /SourceDatabaseName:"[資料庫名稱]" /SourceServerName:"[服務器]" /SourceUser:"[帳號]" /SourcePassword:"[密碼]" /SourceTrustServerCertificate:True /TargetFile:"[檔案名稱].bacpac"
```

## 匯入資料庫

```powershell
sqlpackage /Action:Import /TargetDatabaseName:"[資料庫名稱]" /TargetServerName:"[服務器]" /SourceUser:"[帳號]" /SourcePassword:"[密碼]" /SourceTrustServerCertificate:True /SourceFile:"[檔案名稱].bacpac"
```

# 使用 DACPAC 備份

預設包含數據庫結構，但不包含數據

## 匯出資料庫

選擇包含所有資料
```powershell
sqlpackage /Action:Extract /SourceDatabaseName:"[資料庫名稱]" /SourceServerName:"[服務器]" /SourceUser:"[帳號]" /SourcePassword:"[密碼]" /SourceTrustServerCertificate:True /TargetFile:"[檔案名稱].dacpac" `
/p:ExtractAllTableData=true
```

選擇包含指定表資料
```powershell
sqlpackage /Action:Extract /SourceDatabaseName:"[資料庫名稱]" /SourceServerName:"[服務器]" /SourceUser:"[帳號]" /SourcePassword:"[密碼]" /SourceTrustServerCertificate:True /TargetFile:"[檔案名稱].dacpac" `
/p:TableData="[額外指定可包含資料的表]"
```

## 匯入資料庫

```powershell
sqlpackage /Action:Publish /TargetDatabaseName:"[資料庫名稱]" /TargetServerName:"[服務器]" /TargetUser:"[帳號]" /TargetPassword:"[密碼]" /TargetTrustServerCertificate:True /SourceFile:"[檔案名稱].dacpac" `
/p:ExcludeObjectType=UserDefinedDataTypes `
/p:ExcludeObjectType=UserDefinedTableTypes `
/p:ExcludeObjectType=ClrUserDefinedTypes `
/p:ExcludeObjectType=Users `
/p:ExcludeObjectType=RoleMembership `
/p:AllowIncompatiblePlatform=True 
```

# 搜尋額外指定的表

依資料筆數搜尋可能是主檔的資料表，並產生指令
```sql
SELECT '/p:TableData="[' + s.Name + '].[' + t.NAME + ']" `' [Package]
	,t.NAME [TableName]
	,s.Name [SchemaName]
	,p.rows [Rows]
	,SUM(a.total_pages) * 8 [TotalSpaceKB]
	,CAST(ROUND(((SUM(a.total_pages) * 8) / 1024.00), 2) AS NUMERIC(36, 2)) [TotalSpaceMB]
	,SUM(a.used_pages) * 8 [UsedSpaceKB]
	,CAST(ROUND(((SUM(a.used_pages) * 8) / 1024.00), 2) AS NUMERIC(36, 2)) [UsedSpaceMB]
	,(SUM(a.total_pages) - SUM(a.used_pages)) * 8 [UnusedSpaceKB]
	,CAST(ROUND(((SUM(a.total_pages) - SUM(a.used_pages)) * 8) / 1024.00, 2) AS NUMERIC(36, 2)) [UnusedSpaceMB]
FROM sys.tables t
INNER JOIN sys.indexes i ON t.OBJECT_ID = i.object_id
INNER JOIN sys.partitions p ON i.object_id = p.OBJECT_ID
	AND i.index_id = p.index_id
INNER JOIN sys.allocation_units a ON p.partition_id = a.container_id
LEFT OUTER JOIN sys.schemas s ON t.schema_id = s.schema_id
WHERE t.NAME NOT LIKE 'dt%'
	AND t.is_ms_shipped = 0
	AND i.OBJECT_ID > 255
	AND p.rows > [資料筆數]
GROUP BY t.Name
	,s.Name
	,p.Rows
ORDER BY TotalSpaceMB DESC
	,t.Name;
```

{% note warning %}
📜 參考資料
1. [SqlPackage](https://learn.microsoft.com/en-us/sql/tools/sqlpackage/sqlpackage?view=sql-server-ver16)
{% endnote %}