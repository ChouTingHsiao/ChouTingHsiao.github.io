---
title: 如何在資料存取層進行單元測試
date: 2022-05-01 12:00:00
categories: .Net
tags: [.Net, UnitTest, SQLite]
---

# 安裝 Sqlite
安裝 Nuget 的 Sqlite 套件
```powershell
Install-Package Microsoft.EntityFrameworkCore.Sqlite
```

<!--more-->

# 建立 DbContext
建立 EntityFramework 的 DbContext
```C#
public class TestDbContext : DbContext
{
    public DbSet<Table>? Table { get; set; }
    
    public TestDbContext()
    {
    }

    public TestDbContext(DbContextOptions options) : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Table>(entity => {
            entity.HasKey(x => x.Id);
        });
    }
}
```

# 使用 In-Memory 模式操作資料庫
使用 Sqlite 的 In-Memory 模式載入 DbContext
```C#
SqliteConnection connection = new SqliteConnection("Data Source=:memory:");

connection.Open();

DbContextOptions options = new DbContextOptionsBuilder()
.UseSqlite(connection)
.Options;
```

然後使用 DbContext 的設定建立資料庫
```C#
using TestDbContext context = new TestDbContext(options);
context.Database.EnsureCreated();
```

最後可開始使用 EntityFramework 開始操作資料庫
```C#
using TestDbContext context = new TestDbContext(options);
List<Table> table = context.Table.ToList();
```

# 自定義 Database Function
如果有使用 Sqlite 未定義的 Database Function , 可在程式中使用 CreateFunction 定義
```C#
SqliteConnection connection = new SqliteConnection("Data Source=:memory:");

connection.CreateFunction("current_date", (x) => DateTime.Now);

connection.Open();
```

{% note warning %}
📜 參考資料
1. [Microsoft文件](https://docs.microsoft.com/en-us/dotnet/api/microsoft.data.sqlite.sqliteconnection.createfunction?view=msdata-sqlite-6.0.0)
2. [SQLite Examples](https://csharp.hotexamples.com/examples/-/SQLiteConnection/CreateFunction/php-sqliteconnection-createfunction-method-examples.html)
{% endnote %}