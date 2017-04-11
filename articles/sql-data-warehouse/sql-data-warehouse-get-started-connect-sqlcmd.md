<properties
   pageTitle="查詢 Azure SQL Data Warehouse (sqlcmd) |Microsoft Azure"
   description="查詢 Azure SQL Data Warehouse sqlcmd 命令列公用程式使用。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/06/2016"
   ms.author="barbkess;sonyama"/>

# <a name="query-azure-sql-data-warehouse-sqlcmd"></a>查詢 Azure SQL Data Warehouse (sqlcmd)

> [AZURE.SELECTOR]
- [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Azure 機器學習](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 

這個逐步解說使用查詢 Azure SQL Data Warehouse [sqlcmd][]命令列公用程式。  

## <a name="1-connect"></a>1.連線

若要開始使用[sqlcmd][]，請開啟命令提示字元中，輸入**sqlcmd**後面接著 SQL Data Warehouse 資料庫的連線字串。 連接字串需要下列參數︰

+ **伺服器 (-S):**在表單中的伺服器`<`伺服器名稱`>`。 database.windows.net
+ **資料庫 (-d):**資料庫名稱。
+ **啟用引號識別項 (-我):**必須啟用引號識別項，以連線至 SQL Data Warehouse 執行個體。

若要使用 SQL Server 驗證，您需要新增的使用者名稱與密碼參數︰

+ **使用者 (-U):**在表單中的伺服器使用者`<`使用者`>`
+ **密碼 (-P):**使用者與相關聯的密碼。

例如，您的連接字串可能看起來如下所示︰

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

若要使用 Azure Active Directory 整合式驗證，您需要新增的 Azure Active Directory 參數︰

+ **Azure Active Directory 驗證 (-G):**使用 Azure Active Directory 驗證

例如，您的連接字串可能看起來如下所示︰

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [AZURE.NOTE] 您需要[啟用 Azure Active Directory 驗證](sql-data-warehouse-authentication.md)使用 Active Directory 進行驗證。

## <a name="2-query"></a>2.查詢

連線之後，您可以發行任何支援的 SQL 陳述式在執行個體。  在此範例中，查詢會送出以互動的模式。

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

以下下一個範例會示範如何執行查詢，以及在使用-Q 選項或線路 sqlcmd 以 SQL 的批次模式。

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

## <a name="next-steps"></a>後續步驟

有關 sqlcmd 中可用的選項的詳細資料的詳細資訊，請參閱[sqlcmd 文件][sqlcmd] 。

<!--Image references-->

<!--Article references-->

<!--MSDN references--> 
[sqlcmd]: https://msdn.microsoft.com/library/ms162773.aspx
[Azure portal]: https://portal.azure.com

<!--Other Web references-->
