<properties
    pageTitle="複製使用 Azure 入口網站的 Azure SQL 資料庫 |Microsoft Azure"
    description="建立一份 Azure SQL 資料庫"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="09/19/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>



# <a name="copy-an-azure-sql-database-using-the-azure-portal"></a>複製使用 Azure 入口網站 Azure SQL 資料庫

> [AZURE.SELECTOR]
- [概觀](sql-database-copy.md)
- [Azure 入口網站](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T SQL](sql-database-copy-transact-sql.md)

下列步驟會顯示如何將 SQL 資料庫與[Azure 入口網站](https://portal.azure.com)複製到相同或不同的伺服器。

若要複製 SQL 資料庫，您需要下列項目︰

- Azure 的訂閱。 如果您需要的 Azure 訂閱，只要按一下 [**免費試用版**頂端的 [此頁面中，，然後回到完成這份文件。
- 若要複製 SQL 資料庫。 如果您沒有 SQL 資料庫，建立一個遵循本文中的步驟︰[建立您的第一個 Azure SQL 資料庫](sql-database-get-started.md)。


## <a name="copy-your-sql-database"></a>複製 SQL 資料庫

開啟您想要複製之資料庫的 SQL 資料庫] 頁面︰

1.  移至[Azure 入口網站](https://portal.azure.com)。
2.  按一下 [**更多服務** > **SQL 資料庫**]，然後按一下所要的資料庫。
3.  按一下 [SQL 資料庫] 頁面的 [**複製**]:

    ![SQL 資料庫](./media/sql-database-copy-portal/sql-database-copy.png)

1.  在 [**複製**] 頁面上提供的預設資料庫名稱。 如果您想要輸入不同的名稱 （在伺服器上的所有資料庫必須都具有唯一的名稱）。
2.  選取**目標伺服器**。 目標伺服器是建立資料庫複本的位置。 您可以將資料庫複製到相同的伺服器或在不同的伺服器。 您可以建立伺服器，或從清單中選取現有的伺服器。 
3.  選取**目標伺服器**、**彈性的資料庫的資料庫**及**價格層**之後將會啟用的選項。 如果伺服器的資料庫，您就可以將其複製資料庫。
3.  按一下**[確定**] 以開始複製程序。

    ![SQL 資料庫](./media/sql-database-copy-portal/copy-page.png)


## <a name="monitor-the-progress-of-the-copy-operation"></a>監控複製作業的進度

- 啟動之後複本，按一下 [詳細資料的入口網站通知]。

    ![通知][3]
 
    ![監視器][4]


## <a name="verify-the-database-is-live-on-the-server"></a>確認在伺服器上即時資料庫

- 按一下 [**更多服務** > **SQL 資料庫**，並確認新的資料庫**連線**。


## <a name="resolve-logins"></a>解決登入

若要複製作業完成後，請解決登入，請參閱[解決登入](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes)


## <a name="next-steps"></a>後續步驟

- 如需複製 Azure SQL 資料庫的概觀，請參閱[複製 Azure SQL 資料庫](sql-database-copy.md)。
- 請參閱使用 PowerShell 將資料庫複製的 [[複製 Azure SQL 資料庫使用 PowerShell](sql-database-copy-powershell.md) 。
- 請參閱[複製 Azure SQL 資料庫使用 T SQL](sql-database-copy-transact-sql.md)複製使用 SQL 資料庫。
- 瞭解[如何管理 Azure SQL 資料庫安全性損毀修復後的](sql-database-geo-replication-security-config.md)，若要深入瞭解管理使用者與登入，複製到不同的邏輯伺服器的資料庫時。



## <a name="additional-resources"></a>其他資源

- [管理登入](sql-database-manage-logins.md)
- [連線至 SQL Server Management Studio 與 SQL 資料庫，並執行範例 T SQL 查詢](sql-database-connect-query-ssms.md)
- [匯出 BACPAC 資料庫](sql-database-export.md)
- [業務連續性概觀](sql-database-business-continuity.md)
- [SQL 資料庫文件](https://azure.microsoft.com/documentation/services/sql-database/)




<!--Image references-->
[1]: ./media/sql-database-copy-portal/copy.png
[2]: ./media/sql-database-copy-portal/copy-ok.png
[3]: ./media/sql-database-copy-portal/copy-notification.png
[4]: ./media/sql-database-copy-portal/monitor-copy.png

