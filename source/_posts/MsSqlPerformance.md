---
title: 使用 DMV 查询 SQL 執行性能
date: 2025-01-01 20:00:00
categories: MSSQL
tags: [MSSQL, DMV]
---

# 查询執行中的 SQL 性能

使用 sys.dm_exec_requests 查詢目前執行中的請求

<!--more-->

```sql
SELECT r.scheduler_id [Scheduler Id]
    ,STATUS [Request Status]
    ,r.session_id [SPID]
    ,r.blocking_session_id [Blocking By]
    ,substring(ltrim(q.TEXT), r.statement_start_offset / 2 + 1, (
            CASE 
                WHEN r.statement_end_offset = - 1
                    THEN LEN(CONVERT(NVARCHAR(MAX), q.TEXT)) * 2
                ELSE r.statement_end_offset
                END - r.statement_start_offset
            ) / 2) [Running Query]
    ,q.TEXT [Full Query]
    ,r.cpu_time [CPU Time(ms)]
    ,r.start_time [Start Time]
    ,r.total_elapsed_time [Total Time(ms)]
    ,r.reads [Read]
    ,r.writes [Write]
    ,r.logical_reads [Logic Read]
    ,d.name AS [Database Name]
FROM sys.dm_exec_requests r
CROSS APPLY sys.dm_exec_sql_text(sql_handle) AS q
LEFT JOIN sys.databases d ON (r.database_id = d.database_id)
WHERE r.session_id > 50
	AND r.session_id <> @@SPID
	AND STATUS = 'running'
ORDER BY r.total_elapsed_time DESC;
```

# 查询執行過的 SQL 性能

使用 sys.dm_exec_query_stats 查詢前10名執行過的請求中，是否有任何 IO、CPU、CLR、Blocked 性能瓶頸

```sql
SELECT TOP 10 (total_logical_reads + total_logical_writes) / qs.execution_count [Avg IO]
	,(total_logical_reads + total_logical_writes) [Total IO]
	,total_worker_time / qs.execution_count [Avg CPU]
	,total_worker_time [Total CPU]
	,total_clr_time / execution_count [Avg CLR]
	,total_clr_time [Total CLR]
	,(total_elapsed_time - total_worker_time) / qs.execution_count [Avg Blocked]
	,total_elapsed_time - total_worker_time [Total Blocked]
	,qs.execution_count [Execution Count]
	,SUBSTRING(qt.TEXT, qs.statement_start_offset / 2, (
			CASE 
				WHEN qs.statement_end_offset = - 1
					THEN LEN(CONVERT(NVARCHAR(MAX), qt.TEXT)) * 2
				ELSE qs.statement_end_offset
				END - qs.statement_start_offset
			) / 2) [Running Query]
	,qt.TEXT [Full Query]
	,DB_NAME(qt.dbid) [Database Name]
FROM sys.dm_exec_query_stats AS qs
CROSS APPLY sys.dm_exec_sql_text(qs.sql_handle) AS qt
WHERE qs.creation_time >= [開始時間]
	AND qs.creation_time <= [結束時間]
	AND qs.execution_count > [查詢次數]
ORDER BY [選擇查詢指標排序 IO、CPU、CLR、Blocked] DESC;
```

{% note warning %}
📜 參考資料
1. [sys.dm_exec_requests (Transact-SQL)](https://learn.microsoft.com/zh-tw/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql?view=sql-server-ver16)

2. [sys.dm_exec_sql_text (Transact-SQL)](https://learn.microsoft.com/zh-tw/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql?view=sql-server-ver16)

3. [sys.dm_exec_query_stats (Transact-SQL)](https://learn.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql?view=sql-server-ver16)

4. [sys.databases (Transact-SQL)](https://learn.microsoft.com/zh-tw/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?view=sql-server-ver16)
{% endnote %}