<properties
    pageTitle="將游標暫停並繼續資料移轉 （伸展資料庫） |Microsoft Azure"
    description="瞭解如何暫停或繼續至 Azure 資料移轉。"
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/14/2016"
    ms.author="douglasl"/>

# <a name="pause-and-resume-data-migration-stretch-database"></a>將游標暫停並繼續資料移轉 （伸展資料庫）

若要將游標暫停或繼續至 Azure 資料移轉，選取**伸展**的 SQL Server Management Studio 中，表格，然後選取 [**暫停**] 即可將游標暫停於資料移轉，或 [**繼續**] 以繼續資料移轉。 您也可以使用 Transact-sql\-SQL 暫停或繼續資料移轉。

在個別的資料表，如果您要對本機伺服器上的問題進行疑難排解，或最大化可用的網路頻寬暫停資料移轉。

## <a name="pause-data-migration"></a>暫停資料移轉

### <a name="use-sql-server-management-studio-to-pause-data-migration"></a>使用 SQL Server Management Studio 暫停資料移轉

1.  在 [物件總管，SQL Server Management Studio 中選取 [伸展\-啟用您要將游標暫停於資料移轉的資料表。

2.  向右\-按一下並選取 [**伸展**]、 [，然後選取**[暫停**。

### <a name="use-transact-sql-to-pause-data-migration"></a>使用 Transact-sql\-SQL 以將游標暫停於資料移轉
執行下列命令。

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>  
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = PAUSED ) ) ;  
GO
```

## <a name="resume-data-migration"></a>履歷表資料移轉

### <a name="use-sql-server-management-studio-to-resume-data-migration"></a>使用 SQL Server Management Studio 繼續資料移轉

1.  在 [物件總管，SQL Server Management Studio 中選取 [伸展\-啟用您要繼續資料移轉的資料表。

2.  向右\-按一下並選取 [**伸展**]、 [，然後選取**履歷表**。

### <a name="use-transact-sql-to-resume-data-migration"></a>使用 Transact-sql\-SQL 以繼續資料移轉
執行下列命令。

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>   
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = OUTBOUND ) ) ;  
 GO
```

## <a name="check-whether-migration-is-active-or-paused"></a>請檢查是否作用中或暫停移轉

### <a name="use-sql-server-management-studio-to-check-whether-migration-is-active-or-paused"></a>使用 SQL Server Management Studio 中檢查是否移轉是作用中或暫停
在 SQL Server Management Studio 中，開啟 [**伸展資料庫監視器**，檢查**升級狀態**] 欄的值。 如需詳細資訊，請參閱[監視器和疑難排解資料移轉](sql-server-stretch-database-monitor.md)。

### <a name="use-transact-sql-to-check-whether-migration-is-active-or-paused"></a>若要檢查是否移轉] 是作用中或暫停使用 TRANSACT-SQL
查詢類別目錄檢視**sys.remote_data_archive_tables** ，並檢查**is_migration_paused**資料行的值。 取得詳細資訊，請參閱[sys.remote_data_archive_tables](https://msdn.microsoft.com/library/dn935003.aspx)。

## <a name="see-also"></a>另請參閱

[ALTER TABLE (SQL)](https://msdn.microsoft.com/library/ms190273.aspx)
[監視器和疑難排解資料移轉](sql-server-stretch-database-monitor.md)
