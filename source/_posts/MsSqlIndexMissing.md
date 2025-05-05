---
title: 使用 DMV 查询缺失 Index 建議
date: 2022-01-01 12:00:00
categories: MSSQL
tags: [MSSQL, DMV]
---

# 查詢缺失 Index 建議

使用 sys.dm_db_missing_index_group_stats 查詢缺失 Index 建議

<!--more-->

```sql
SELECT
    OBJECT_NAME(mid.object_id) AS TableName,
    migs.unique_compiles,
    migs.user_seeks,
    migs.avg_total_user_cost,
    migs.avg_user_impact,
    mid.equality_columns,
    mid.inequality_columns,
    mid.included_columns 
FROM sys.dm_db_missing_index_group_stats AS migs
INNER JOIN sys.dm_db_missing_index_groups AS mig ON migs.group_handle = mig.index_group_handle
INNER JOIN sys.dm_db_missing_index_details AS mid ON mig.index_handle = mid.index_handle
WHERE migs.avg_user_impact > 70
AND migs.avg_total_user_cost > 10
ORDER BY migs.user_seeks DESC;
```

# 缺失索引分析

| 欄位名稱                  | 說明                                             |
| --------------------- | ---------------------------------------------- |
| `TableName`           | 缺失索引建議所對應的資料表名稱                                |
| `unique_compiles`     | 該索引建議被查詢編譯器參考的次數（代表不同查詢計畫參考了此建議）               |
| `user_seeks`          | 該建議索引若存在，可使用 Index Seek 的查詢次數（查詢使用頻率指標）        |
| `avg_total_user_cost` | 查詢在未建立索引時的平均總成本（資源消耗）                          |
| `avg_user_impact`     | 預估建立該索引後，查詢成本可降低的比例（%），越高代表效益越大                |
| `equality_columns`    | 查詢中使用等值條件（例如 `=`、`JOIN`）的欄位名稱                  |
| `inequality_columns`  | 查詢中使用不等條件（例如 `>`, `<`, `BETWEEN`, `LIKE`）的欄位名稱 |
| `included_columns`    | 查詢中使用但不作為篩選條件的欄位，建議用 `INCLUDE` 方式加入索引中         |

# 索引建立的時機

user_seeks 次數高，且 avg_user_impact > 70%， avg_total_user_cost > 10，可考慮建立索引

索引建立命令:
```sql
CREATE NONCLUSTERED INDEX [索引名稱]
ON [Schema].[Table]([equality_columns], [inequality_columns])
INCLUDE ([included_columns]);
```

{% note warning %}
📜 參考資料
1. [sys.dm_db_missing_index_group_stats (Transact-SQL)](https://learn.microsoft.com/zh-tw/sql/relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-group-stats-transact-sql?view=sql-server-ver16)

2. [sys.dm_db_missing_index_groups (Transact-SQL)](https://learn.microsoft.com/zh-tw/sql/relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-groups-transact-sql?view=sql-server-ver16)

3. [sys.dm_db_missing_index_details (Transact-SQL)](https://learn.microsoft.com/zh-tw/sql/relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-details-transact-sql?view=sql-server-ver16)
{% endnote %}