---
title: 在 SQL Server 操作 JSON
date: 2025-01-01 19:00:00
categories: MSSQL
tags: [MSSQL, JSON]
---

# 宣告 JSON 字串

```sql
DECLARE @json NVARCHAR(MAX);

SET @json = '{"info": {"address": [{"town": "Belgrade"}, {"town": "Paris"}, {"town":"Madrid"}]}}';
```

<!--more-->

# JSON_VALUE 擷取純量值字串

```sql
SELECT JSON_VALUE(@json, '$.info.address[0].town');
```

# JSON_QUERY 擷取物件或陣列字串

```sql
SELECT JSON_QUERY(@json, '$.info.address');
```

# JSON_MODIFY 變更 JSON 字串中的值

```sql
SET @json = JSON_MODIFY(@json, '$.info.address[1].town', 'London');

SELECT modifiedJson = @json;
```

# JSON 轉換為資料列

解析 JSON 字串資料,需預先定義對應格式
```sql
SELECT *
FROM OPENJSON(JSON_QUERY(@json, '$.info.address')) WITH (
    town NVARCHAR(50) '$.town'
);
```

{% note warning %}
📜 參考資料
1. [SQL Server 中的 JSON 資料](https://learn.microsoft.com/zh-tw/sql/relational-databases/json/json-data-sql-server?view=sql-server-ver16)
{% endnote %}