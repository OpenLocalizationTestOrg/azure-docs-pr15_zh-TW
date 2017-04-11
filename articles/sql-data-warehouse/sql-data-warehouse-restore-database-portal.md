<properties
   pageTitle="還原 Azure SQL Data Warehouse （入口網站） |Microsoft Azure"
   description="還原 Azure SQL Data Warehouse azure 入口網站的任務。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="Lakshmi1812"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/21/2016"
   ms.author="lakshmir;barbkess;sonyama"/>

# <a name="restore-an-azure-sql-data-warehouse-portal"></a>還原 Azure SQL Data Warehouse （入口網站）

> [AZURE.SELECTOR]
- [概觀][]
- [入口網站][]
- [PowerShell][]
- [其餘][]

在本文中，您將學習如何還原使用 Azure 入口網站 Azure SQL Data Warehouse。

## <a name="before-you-begin"></a>開始之前

**請確認您 DTU 容量。** 每個 SQL Data Warehouse 主控具有預設 DTU 配額的 SQL server (例如 myserver.database.windows.net)。  您可以還原 SQL Data Warehouse 之前，請確認您的 SQL server 有足夠剩餘的 DTU 配額要還原的資料庫。 若要瞭解如何計算所需的 DTU 或邀請更多 DTU，請參閱[要求 DTU 配額變更][]。


## <a name="restore-an-active-or-paused-database"></a>還原使用中] 或 [暫停的資料庫

若要還原資料庫︰

1. 登入[Azure 入口網站][]
2. 在左側的畫面選取 [**瀏覽]** ，然後選取**SQL server**
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
    
3. 瀏覽至您的伺服器，然後選取
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-server.png)

4. 尋找您要還原，然後選取該 SQL Data Warehouse
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-active-dw.png)
5. 在 Data Warehouse 刀頂端，按一下 [**還原**
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-restore-from-active.png)

6. 指定新的**資料庫名稱**
7. 選取最新的**還原點**
    1. 請確定您選擇的最新的還原點。  還原點會顯示以 utc 表示，因為有時顯示的預設選項不是最新的還原點。
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-restore-blade-from-active.png)

8. 按一下**[確定]**
9. 資料庫還原程序會開始，並可以使用**通知**監視

>[AZURE.NOTE] 還原完畢後，您可以設定下列[設定您的資料庫復原之後][]，復原的資料庫。


## <a name="restore-a-deleted-database"></a>還原已刪除的資料庫

若要還原已刪除的資料庫︰

1. 登入[Azure 入口網站][]
2. 在左側的畫面選取 [**瀏覽]** ，然後選取**SQL server**
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)

3. 瀏覽至您的伺服器，然後選取
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-server.png)

4. 向您的伺服器刀上的 [作業] 區段下捲動
5. 按一下 [**刪除資料庫**] 磚
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dws.png)

6. 選取您要還原已刪除的資料庫
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dw.png)

7. 指定新的**資料庫名稱**
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-restore-blade-from-deleted.png)
    
8. 按一下**[確定]**
9. 資料庫還原程序會開始，並可以使用**通知**監視

>[AZURE.NOTE] 若要還原完畢後，請設定您的資料庫，請參閱[設定您復原後的資料庫][]。 

## <a name="next-steps"></a>後續步驟
若要瞭解 Azure SQL 資料庫版本的業務連續性功能，請閱讀[Azure SQL 資料庫 business 連續性概觀][]。

<!--Image references-->

<!--Article references-->
[Azure SQL 資料庫 business 連續性概觀]: ./sql-database-business-continuity.md
[概觀]: ./sql-data-warehouse-restore-database-overview.md
[入口網站]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[其餘]: ./sql-data-warehouse-restore-database-rest-api.md
[復原後，設定您的資料庫]: ./sql-database-disaster-recovery.md#configure-your-database-after-recovery
[要求 DTU 配額變更]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change

<!--MSDN references-->

<!--Blog references-->

<!--Other Web references-->
[Azure 入口網站]: https://portal.azure.com/
