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

# value() 擷取純量值
```sql
SELECT @x.value('(/Root/row/@id)[1]', 'int');
```

# query() 擷取物件或陣列
```sql
SELECT @x.query('/Root/row');
```

#  modify() 變更 JSON 字串中的值
```sql
SET @x.modify('
  replace value of (/Root/row[1]/name/text())[1]
  with "new Larry"
');

SELECT @x;
```

#  nodes() 集合轉換為資料列集
```sql
SELECT T.C.query('.')
FROM @x.nodes('/Root/row') T(c);
```

#  OPENXML 集合轉換為資料列集
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

{% note warning %}
📜 參考資料
1. [xml (Transact-SQL)](https://learn.microsoft.com/en-us/sql/t-sql/xml/xml-transact-sql?view=sql-server-ver16)
{% endnote %}