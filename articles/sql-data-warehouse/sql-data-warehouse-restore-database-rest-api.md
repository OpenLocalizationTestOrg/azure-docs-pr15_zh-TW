<properties
   pageTitle="還原 Azure SQL Data Warehouse (REST API) |Microsoft Azure"
   description="還原 Azure SQL Data Warehouse REST API 工作。"
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

# <a name="restore-an-azure-sql-data-warehouse-rest-api"></a>還原 Azure SQL Data Warehouse (REST API)

> [AZURE.SELECTOR]
- [概觀][]
- [入口網站][]
- [PowerShell][]
- [其餘][]

在本文中，您將學習如何還原使用 REST API Azure SQL Data Warehouse。

## <a name="before-you-begin"></a>開始之前

**請確認您 DTU 容量。** 每個 SQL Data Warehouse 主控具有預設 DTU 配額的 SQL server (例如 myserver.database.windows.net)。  您可以還原 SQL Data Warehouse 之前，請確認您的 SQL server 有足夠剩餘的 DTU 配額要還原的資料庫。 若要瞭解如何計算所需的 DTU 或邀請更多 DTU，請參閱[要求 DTU 配額變更][]。

## <a name="restore-an-active-or-paused-database"></a>還原使用中] 或 [暫停的資料庫

若要還原資料庫︰

1. 取得使用取得資料庫還原點作業的資料庫還原點的清單。
2. 使用 [[建立資料庫還原要求][]作業開始您還原。
3. 使用[資料庫作業狀態][]作業來追蹤您還原的狀態。

>[AZURE.NOTE] 還原完畢後，您可以設定下列[設定您的資料庫復原之後][]，復原的資料庫。

## <a name="restore-a-deleted-database"></a>還原已刪除的資料庫

若要還原已刪除的資料庫︰

1.  列出所有可還原已刪除資料庫使用[清單可還原卸除的資料庫][]作業。
2.  取得您想要使用 [[取得可還原卸除的資料庫][]作業，還原已刪除的資料庫中的詳細資料。
3.  使用 [[建立資料庫還原要求][]作業開始您還原。
4.  使用[資料庫作業狀態][]作業來追蹤您還原的狀態。

>[AZURE.NOTE] 若要還原完畢後，請設定您的資料庫，請參閱[設定您復原後的資料庫][]。 


## <a name="next-steps"></a>後續步驟
若要瞭解 Azure SQL 資料庫版本的業務連續性功能，請閱讀[Azure SQL 資料庫 business 連續性概觀][]。

<!--Image references-->

<!--Article references-->
[Azure SQL 資料庫 business 連續性概觀]: ./sql-database-business-continuity.md
[要求 DTU 配額變更]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change
[復原後，設定您的資料庫]: ./sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: ./powershell-install-configure.md
[概觀]: ./sql-data-warehouse-restore-database-overview.md
[入口網站]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[其餘]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->
[建立資料庫還原邀請]: https://msdn.microsoft.com/library/azure/dn509571.aspx
[資料庫作業的狀態]: https://msdn.microsoft.com/library/azure/dn720371.aspx
[取得可還原卸除的資料庫]: https://msdn.microsoft.com/library/azure/dn509574.aspx
[清單可還原卸除資料庫]: https://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
