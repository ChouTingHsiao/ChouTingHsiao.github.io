---
title: 在 SQL Server 操作 XML
date: 2025-01-01 19:00:00
categories: MSSQL
tags: [MSSQL, XML]
---

# 宣告 XML 資料

```sql
DECLARE @x XML;

SET @x = '<Root>  
    <row id="1"><name>Larry</name></row>  
    <row id="2"><name>moe</name></row>  
    <row id="3" />  
</Root>';
```

<!--more-->

# value 擷取純量值

```sql
SELECT @x.value('(/Root/row/@id)[1]', 'int');
```

# query 擷取物件或陣列

```sql
SELECT @x.query('/Root/row');
```

# modify 變更 JSON 字串中的值

```sql
SET @x.modify('
  replace value of (/Root/row[1]/name/text())[1]
  with "new Larry"
');

SELECT @x;
```

# XML 轉換為資料列

⭐ nodes

直接解析 XML 格式資料

```sql
SELECT T.C.query('.')
FROM @x.nodes('/Root/row') T(c);
```

⭐ OPENXML

解析 XML 字串資料,需預先定義對應格式

```sql
DECLARE @idoc INT, @doc VARCHAR(1000);

SET @doc = '<Root>  
    <row id="1"><name>Larry</name></row>  
    <row id="2"><name>moe</name></row>  
    <row id="3" />  
</Root>';

-- 載入 XML 文件
EXEC sp_xml_preparedocument @idoc OUTPUT, @doc;

-- 解析 XML 文件
SELECT *
FROM OPENXML(@idoc, '/Root/row', 1) WITH (
      id INT '@id',
    name VARCHAR(100) 'name'
);

-- 清除 XML 文件
EXEC sp_xml_removedocument @idoc;
```

# Base64 轉換(UTF-16)

字串轉 Base64

```sql
SELECT CAST(N'' AS XML).value('xs:base64Binary(xs:hexBinary(sql:column("bin")))', 'VARCHAR(MAX)')
FROM (
	SELECT CAST(N'測試' AS VARBINARY(MAX)) AS bin
	) AS t;
```

Base64 轉字串

```sql
SELECT CAST(CAST(N'' AS XML).value('xs:base64Binary("LG5mig==")', 'VARBINARY(MAX)') AS NVARCHAR(MAX))
```

{% note warning %}
📜 參考資料
1. [xml (Transact-SQL)](https://learn.microsoft.com/en-us/sql/t-sql/xml/xml-transact-sql?view=sql-server-ver16)
{% endnote %}