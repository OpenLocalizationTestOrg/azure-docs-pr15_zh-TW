<properties
    pageTitle="停用伸展資料庫並恢復遠端資料 |Microsoft Azure"
    description="瞭解如何停用伸展資料庫表格，您也可以選擇恢復遠端資料。"
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
    ms.date="08/05/2016"
    ms.author="douglasl"/>

# <a name="disable-stretch-database-and-bring-back-remote-data"></a>停用伸展資料庫並恢復遠端資料

若要停用伸展資料庫表格選取**伸展**的 SQL Server Management Studio 中的表格。 然後選取下列選項之一。

-   **停用 |從 Azure 恢復資料**。 複製 azure 資料表的遠端資料返回 SQL Server，然後停用資料表伸展資料庫。 這項作業的資料傳輸成本，而無法取消。

-   **停用 |將資料保留在 Azure**。 停用伸展資料庫資料表。  放棄遠端 Azure 中資料表的資料。

您也可以使用 Transact-sql\-停用伸展資料庫，如果是資料表或資料庫的 SQL。

您針對表格停用伸展資料庫後，資料移轉停駐點和查詢結果不再包含從遠端資料表的結果。

如果您只想要將游標暫停於資料移轉時，請參閱[暫停和履歷表伸展資料庫](sql-server-stretch-database-pause.md)。

>   [AZURE.NOTE] 如果是資料表，或為資料庫停用伸展資料庫並不會刪除遠端物件。 如果您想要刪除遠端資料表或遠端資料庫，您必須放使用 Azure 管理入口網站。 遠端物件繼續產生 Azure 成本，直到您將它們刪除。 取得詳細資訊，請參閱[SQL Server 伸展資料庫價格](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/)。

## <a name="disable-stretch-database-for-a-table"></a>停用伸展資料庫表格

### <a name="use-sql-server-management-studio-to-disable-stretch-database-for-a-table"></a>使用 SQL Server Management Studio 中停用伸展資料庫表格

1.  在 [物件總管，SQL Server Management Studio 中選取您要停用伸展資料庫的資料表。

2.  向右\-按一下並選取 [**伸展**]、 [，然後選取下列選項之一。

    -   **停用 |從 Azure 恢復資料**。 複製 azure 資料表的遠端資料返回 SQL Server，然後停用資料表伸展資料庫。 無法取消此命令。

        >   [AZURE.NOTE] 複製遠端資料從 Azure 資料表的上的一步] SQL Server 會增加資料傳輸成本。 詳細資訊，請參閱[資料傳輸定價詳細資料](https://azure.microsoft.com/pricing/details/data-transfers/)。

        所有遠端資料已複製 azure 返回 SQL Server 之後，伸展會停用的表格。

    -   **停用 |將資料保留在 Azure**。 停用伸展資料庫資料表。  放棄遠端 Azure 中資料表的資料。

    >   [AZURE.NOTE] 停用伸展資料庫表格並不會刪除遠端資料或遠端資料表。 如果您想要刪除遠端表格，您必須放使用 Azure 管理入口網站。 遠端資料表會繼續產生 Azure 成本，直到您將其刪除。 取得詳細資訊，請參閱[SQL Server 伸展資料庫價格](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/)。

### <a name="use-transact-sql-to-disable-stretch-database-for-a-table"></a>使用 Transact-sql\-SQL 以停用伸展資料庫表格

-   若要停用伸展，表格及複製 azure 資料表的遠端資料返回 SQL Server，請執行下列命令。 所有遠端資料已複製 azure 返回 SQL Server 之後，伸展會停用的表格。

    無法取消此命令。

    ```tsql
    USE <Stretch-enabled database name>;
    GO
    ALTER TABLE <Stretch-enabled table name>  
       SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = INBOUND ) ) ;
    GO
    ```
    >   [AZURE.NOTE] 複製遠端資料從 Azure 資料表的上的一步] SQL Server 會增加資料傳輸成本。 詳細資訊，請參閱[資料傳輸定價詳細資料](https://azure.microsoft.com/pricing/details/data-transfers/)。

-   若要停用伸展表格並放棄遠端資料，執行下列命令。

    ```tsql
    ALTER TABLE <table_name>
       SET ( REMOTE_DATA_ARCHIVE = OFF_WITHOUT_DATA_RECOVERY ( MIGRATION_STATE = PAUSED ) ) ;
    ```

>   [AZURE.NOTE] 停用伸展資料庫表格並不會刪除遠端資料或遠端資料表。 如果您想要刪除遠端表格，您必須放使用 Azure 管理入口網站。 遠端資料表會繼續產生 Azure 成本，直到您將其刪除。 取得詳細資訊，請參閱[SQL Server 伸展資料庫價格](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/)。

## <a name="disable-stretch-database-for-a-database"></a>停用伸展資料庫的資料庫
您可以關閉資料庫伸展資料庫之前，您可以停用個別伸展伸展資料庫\-啟用資料庫中的資料表。

### <a name="use-sql-server-management-studio-to-disable-stretch-database-for-a-database"></a>使用 SQL Server Management Studio 中停用伸展資料庫的資料庫

1.  在 [物件總管，SQL Server Management Studio 中選取您要停用伸展資料庫的資料庫。

2.  向右\-按一下並選取 [**工作**]，然後選取 [**伸展**]、 [，然後選取**停用**。

>   [AZURE.NOTE] 停用伸展資料庫的資料庫，並不會刪除遠端資料庫。 如果您想要刪除遠端資料庫，您必須放使用 Azure 管理入口網站。 遠端資料庫會繼續產生 Azure 成本，直到您將其刪除。 取得詳細資訊，請參閱[SQL Server 伸展資料庫價格](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/)。

### <a name="use-transact-sql-to-disable-stretch-database-for-a-database"></a>使用 Transact-sql\-停用資料庫伸展資料庫的 SQL
執行下列命令。

```tsql
ALTER DATABASE <database name>
    SET REMOTE_DATA_ARCHIVE = OFF ;
```

>   [AZURE.NOTE] 停用伸展資料庫的資料庫，並不會刪除遠端資料庫。 如果您想要刪除遠端資料庫，您必須放使用 Azure 管理入口網站。 遠端資料庫會繼續產生 Azure 成本，直到您將其刪除。 取得詳細資訊，請參閱[SQL Server 伸展資料庫價格](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/)。

## <a name="see-also"></a>另請參閱

[ALTER 資料庫設定選項 (TRANSACT-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)

[將游標暫停並繼續伸展資料庫](sql-server-stretch-database-pause.md)
