---
title: 使用 SqlPackage 備份資料庫
date: 2024-07-25 18:00:00
categories: .Net
tags: [.Net, SqlPackage]
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

匯出資料庫
```powershell
sqlpackage /Action:Export /SourceDatabaseName:"[資料庫名稱]" /SourceServerName:"[服務器]" /TargetFile:"[檔案名稱].bacpac"
```

匯入資料庫
```powershell
sqlpackage /Action:Import /TargetDatabaseName:"[資料庫名稱]" /TargetServerName:"[服務器]" /SourceFile:"[檔案名稱].bacpac"
```

# 使用 DACPAC 備份

預設包含數據庫結構，但不包含數據

匯出資料庫
```powershell
sqlpackage /Action:Extract /SourceDatabaseName:"[資料庫名稱]" /SourceServerName:"[服務器]" /SourceUser:"[帳號]" /SourcePassword:"[密碼]" /SourceTrustServerCertificate:True /TargetFile:"[檔案名稱].dacpac" `
/p:TableData="[額外指定可包含資料的表]"
```

匯入資料庫
```powershell
sqlpackage /Action:Publish /TargetDatabaseName:"[資料庫名稱]" /TargetServerName:"[服務器]" /TargetUser:"[帳號]" /TargetPassword:"[密碼]" /TargetTrustServerCertificate:True /SourceFile:"[檔案名稱].dacpac" `
/p:ExcludeObjectType=UserDefinedDataTypes `
/p:ExcludeObjectType=UserDefinedTableTypes `
/p:ExcludeObjectType=ClrUserDefinedTypes `
/p:ExcludeObjectType=Users `
/p:ExcludeObjectType=RoleMembership `
/p:AllowIncompatiblePlatform=True 
```

{% note warning %}
📜 參考資料
1. [SqlPackage](https://learn.microsoft.com/en-us/sql/tools/sqlpackage/sqlpackage?view=sql-server-ver16)
{% endnote %}