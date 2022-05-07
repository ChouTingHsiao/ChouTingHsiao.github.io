---
title: 如何在資料存取層進行單元測試
date: 2022-05-01 12:00:00
categories: .Net
tags: [UnitTest, SQLite]
---
# 實作
安裝Nuget的Sqlite套件

```
Install-Package Microsoft.EntityFrameworkCore.Sqlite
```

<!--more-->

建立EntityFramework的DbContext
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

使用Sqlite的In-Memory模式載入DbContext
```C#
SqliteConnection connection = new SqliteConnection("Data Source=:memory:");

connection.Open();

DbContextOptions options = new DbContextOptionsBuilder()
.UseSqlite(connection)
.Options;
```

然後使用DbContext的設定建立資料庫
```C#
using TestDbContext context = new TestDbContext(options);
context.Database.EnsureCreated();
```

最後可開始使用EntityFramework開始操作資料庫
```C#
using TestDbContext context = new TestDbContext(options);
List<Table> table = context.Table.ToList();
```

如果有使用Sqlite未定義的Database Function, 可在程式中使用CreateFunction定義
```C#
SqliteConnection connection = new SqliteConnection("Data Source=:memory:");

connection.CreateFunction("current_date", (x) => DateTime.Now);

connection.Open();
```

# 參考資料

[Microsoft文件](https://docs.microsoft.com/en-us/dotnet/api/microsoft.data.sqlite.sqliteconnection.createfunction?view=msdata-sqlite-6.0.0)

[例子](https://csharp.hotexamples.com/examples/-/SQLiteConnection/CreateFunction/php-sqliteconnection-createfunction-method-examples.html)