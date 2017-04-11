<properties
    pageTitle="變更 Azure SQL 資料庫的服務層和效能層級 |Microsoft Azure"
    description="變更服務層級和 Azure SQL 資料庫的效能層級會顯示如何向上或向下調整 SQL 資料庫。 變更 Azure SQL 資料庫的價格層。"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="10/12/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="change-the-service-tier-and-performance-level-pricing-tier-of-a-sql-database-using-the-azure-portal"></a>變更服務層和效能的層級 （價格層） 使用 Azure 入口網站的 SQL 資料庫


> [AZURE.SELECTOR]
- [**Azure 入口網站**](sql-database-scale-up.md)
- [PowerShell](sql-database-scale-up-powershell.md)


服務層效能層級描述的功能和 SQL 資料庫的資源，並可以更新為應用程式進行變更的需求。 如需詳細資訊，請參閱[服務層](sql-database-service-tiers.md)。

請注意，變更層及/或資料庫的效能層級會在新的效能層級，建立原始資料庫的複本，然後在複本的切換連線。 此程序期間會遺失任何資料，但期間長的時間，當我們切換至複本，資料庫的連線會停用，因此航班的一些交易可能復原。 這個視窗不盡相同，但平均底下 4 秒鐘，而在 99%以上的情況下會小於 30 秒。 很少，尤其是如果有大型的交易航班森積差連線的數字會停用，這個視窗可能會更長的時間。  

整個升級程序的工期而定資料庫的大小和服務層之前和之後的變更。 例如，250 GB 資料庫會變更到、，或在標準的服務層級，應該 6 小時內完成。 為相同大小會變更內進階版服務層級的效能層級的資料庫，它應該 3 小時內完成。


使用[Azure SQL 資料庫服務層和效能層級](sql-database-service-tiers.md)中的資訊，以決定適當的服務層和效能 Azure SQL 資料庫。

- 若要降級資料庫，資料庫應小於目標服務層級的最大可容許大小。 
- 升級時資料庫[地理複寫](sql-database-geo-replication-overview.md)啟用，您必須先升級其次要資料庫至想要的效能層之前升級的主要資料庫。
- 當降級服務層級，您必須先終止所有地理複寫關聯。 
- 還原服務方案是不同的各服務層。 如果您降級您可能會遺失能夠還原到時間，或有較低的備份保留期間。 如需詳細資訊，請參閱[Azure SQL 資料庫備份與還原](sql-database-business-continuity.md)。
- 變更您的資料庫價格層不會變更的最大的資料庫大小。 若要變更您的資料庫的最大大小會使用[TRANSACT-SQL (SQL T)](https://msdn.microsoft.com/library/mt574871.aspx)或[PowerShell](https://msdn.microsoft.com/library/mt619433.aspx)。
- 變更完成前，不會套用新資料庫的屬性。



**若要完成這份文件您需要下列項目︰**

- Azure SQL 資料庫。 如果您沒有 SQL 資料庫，建立一個遵循本文中的步驟︰[建立您的第一個 Azure SQL 資料庫](sql-database-get-started.md)。


## <a name="change-the-service-tier-and-performance-level-of-your-database"></a>變更您的資料庫的服務層和效能層級


開啟您想要的縮放資料庫的 SQL 資料庫刀︰

1.  在 [ [Azure 入口網站](https://portal.azure.com)中，按一下 [**更多服務** > **SQL 資料庫**。
2.  按一下您想要變更的資料庫。
3.  按一下 [ **SQL 資料庫**刀**價格層 （縮放 DTUs）**︰

    ![價格層][1]

1.  選擇新的層，然後按一下 [**選取**︰

    按一下 [**選取**送出比例要求變更價格層。 根據您的資料庫大小縮放作業需要一些時間來完成 （請參閱本文的頂端的資訊）。

    > [AZURE.NOTE] 變更您的資料庫價格層不會變更的最大的資料庫大小。 若要變更您的資料庫的最大大小會使用[TRANSACT-SQL (SQL T)](https://msdn.microsoft.com/library/mt574871.aspx)或[PowerShell](https://msdn.microsoft.com/library/mt619433.aspx)。

    ![選取價格層][2]

3.  按一下右上角的 [通知] 圖示 （鐘型）︰

    ![通知][3]

    按一下 [通知文字，以開啟詳細資料窗格中，您可以在這裡看見要求的狀態]。




## <a name="next-steps"></a>後續步驟

- 變更您使用[TRANSACT-SQL (SQL T)](https://msdn.microsoft.com/library/mt574871.aspx)或[PowerShell](https://msdn.microsoft.com/library/mt619433.aspx)資料庫最大大小。
- [不按比例縮放看]，並在](sql-database-elastic-scale-get-started.md)
- [連接及查詢 SSMS 的 SQL 資料庫](sql-database-connect-query-ssms.md)
- [匯出 Azure SQL 資料庫](sql-database-export.md)

## <a name="additional-resources"></a>其他資源

- [業務連續性概觀](sql-database-business-continuity.md)
- [SQL 資料庫文件](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-scale-up/new-tier.png
[2]: ./media/sql-database-scale-up/choose-tier.png
[3]: ./media/sql-database-scale-up/scale-notification.png
[4]: ./media/sql-database-scale-up/new-tier.png
