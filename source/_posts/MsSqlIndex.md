---
title: 使用 DMV 查询 Index 分佈
date: 2025-01-01 20:00:00
categories: MSSQL
tags: [MSSQL, DMV]
---

# 查询目前 Index 碎片化情形

使用 sys.dm_db_index_physical_stats 查詢資料表碎片化情形

<!--more-->

```sql
SELECT ix.name [Index Name]
	,s.name [Schema Name]
	,t.name [Table Name]
	,pc.partition_count [Partition Count]
	,ps.avg_page_space_used_in_percent [Page Space Used In Percent]
	,ps.avg_fragmentation_in_percent [Fragmentation In Percent]
FROM sys.indexes AS ix
INNER JOIN sys.tables t ON t.object_id = ix.object_id
INNER JOIN sys.schemas s ON t.schema_id = s.schema_id
INNER JOIN (
	SELECT object_id
		,index_id
		,avg_fragmentation_in_percent
		,avg_page_space_used_in_percent
		,partition_number
	FROM sys.dm_db_index_physical_stats(DB_ID(), NULL, NULL, NULL, NULL)
	) ps ON t.object_id = ps.object_id
	AND ix.index_id = ps.index_id
INNER JOIN (
	SELECT object_id
		,index_id
		,COUNT(DISTINCT partition_number) AS partition_count
	FROM sys.partitions
	GROUP BY object_id
		,index_id
	) pc ON t.object_id = pc.object_id
	AND ix.index_id = pc.index_id
WHERE ps.avg_fragmentation_in_percent > 10
	AND ix.name IS NOT NULL
	AND t.name = '[資料表名稱]';
```

# 索引分佈分析

Internal fragmentation:

使用 avg_page_space_used_in_percent 分析索引頁面內部的空間使用情況

External fragmentation:

使用 avg_fragmentation_in_percent 分析索引頁在實體儲存中的順序是否連續

# 索引重組的時機

整理索引內部的實體結構，但不刪除索引

檢查 avg_page_space_used_in_percent 的值建議介於 60 到 75 之間

檢查 avg_fragmentation_in_percent 的值建議介於 10 到 15 之間

索引重組命令:
```sql
 ALTER INDEX [Index Name] ON [Schema Name].[Table Name] REORGANIZE
```

# 索引重建的時機

刪除索引重新建立

檢查 avg_page_space_used_in_percent 的值建議小於 60

檢查 avg_fragmentation_in_percent 的值建議大於 15

索引重建命令:
```sql
ALTER INDEX [Index Name] ON [Schema Name].[Table Name] REBUILD
```

{% note warning %}
📜 參考資料
1. [sys.indexes (Transact-SQL)](https://learn.microsoft.com/zh-tw/sql/relational-databases/system-catalog-views/sys-indexes-transact-sql?view=sql-server-ver16)

2. [sys.tables (Transact-SQL)](https://learn.microsoft.com/zh-tw/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?view=sql-server-ver16)

3. [Schema catalog view - sys.schemas](https://learn.microsoft.com/en-us/sql/relational-databases/system-catalog-views/schemas-catalog-views-sys-schemas?view=sql-server-ver16)

4. [sys.dm_db_index_physical_stats (Transact-SQL)](https://learn.microsoft.com/zh-tw/sql/relational-databases/system-dynamic-management-views/sys-dm-db-index-physical-stats-transact-sql?view=sql-server-ver16)

5. [sys.partitions (Transact-SQL)](https://learn.microsoft.com/zh-tw/sql/relational-databases/system-catalog-views/sys-partitions-transact-sql?view=sql-server-ver16)

6. [ALTER INDEX (Transact-SQL)](https://learn.microsoft.com/zh-tw/sql/t-sql/statements/alter-index-transact-sql?view=sql-server-ver16)

7. [讓 SQL Server 告訴你有哪些索引應該被重建或重組](https://blog.miniasp.com/post/2009/01/18/Let-SQL-Server-Tell-You-Which-Indexes-to-Rebuild-or-Reorganize)
{% endnote %}