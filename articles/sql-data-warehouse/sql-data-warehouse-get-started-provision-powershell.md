<properties
   pageTitle="使用 PowerShell 來建立 SQL Data Warehouse |Microsoft Azure"
   description="使用 PowerShell 來建立 SQL Data Warehouse"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/25/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="create-sql-data-warehouse-using-powershell"></a>建立 SQL Data Warehouse 使用 PowerShell

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

本文將示範如何建立使用 PowerShell SQL Data Warehouse。

## <a name="prerequisites"></a>必要條件

若要開始，您需要︰

- **Azure 帳戶**︰ 造訪[Azure 免費試用版][]或[MSDN Azure 貸項總計][]，以建立帳戶。
- **Azure SQL server**︰ 如需詳細資訊，請參閱[建立具有 Azure 入口網站的 Azure SQL 資料庫邏輯伺服器][]或[建立使用 PowerShell 的 Azure SQL 資料庫邏輯伺服器][]。
- **資源群組**︰ 與您 Azure SQL server 中使用相同的資源群組，或瞭解[如何建立資源群組][]。
- **PowerShell 版本 1.0.3 大於或等於**︰ 您可以執行，以檢查您的版本**取得模組 ListAvailable-名稱 Azure**。  從[Microsoft 網站的平台安裝程式][]，可以安裝最新版本。  如需有關如何安裝最新版本的詳細資訊，請參閱[如何安裝和設定 PowerShell 的 Azure][]。

> [AZURE.NOTE] 建立 SQL Data Warehouse 可能會產生新的計費服務。  如需價格的詳細資訊，請參閱[SQL Data Warehouse 價格][]。

## <a name="create-a-sql-data-warehouse"></a>建立 SQL Data Warehouse

1. 開啟 Windows PowerShell。
2. 若要登入 Azure 資源管理員執行這個指令程式。

    ```Powershell
    Login-AzureRmAccount
    ```
    
3. 選取您要用於目前的工作階段的訂閱。

    ```Powershell
    Get-AzureRmSubscription -SubscriptionName "MySubscription" | Select-AzureRmSubscription
    ```

4.  建立資料庫。 此範例會建立一個名為 「 mynewsqldw 」，與服務目標層級 」 DW400 」，名為 「 sqldwserver1 」，這是名為 「 mywesteuroperesgp1 」 的 [資源] 群組中的伺服器資料庫。

    ```Powershell
    New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse" -CollationName "SQL_Latin1_General_CP1_CI_AS" -MaxSizeBytes 10995116277760
    ```

必要的參數是︰

- **RequestedServiceObjectiveName**: [DWU][]您要求的量。  支援的值為︰ DW100、 DW200、 DW300、 DW400、 DW500、 DW600、 DW1000、 DW1200、 DW1500、 DW2000、 DW3000 及 DW6000。
- **摘要**︰ 建立 SQL Data Warehouse 的名稱。
- **伺服器名稱**︰ 您用來建立的伺服器名稱 （必須是 V12）。
- **ResourceGroupName**︰ 您正在使用的資源群組。  若要尋找 [可用的資源您的訂閱中的群組，請使用取得 AzureResource。
- **版本**︰ 必須是 「 資料倉儲 「 若要建立 SQL Data Warehouse。

選用的參數為︰

- **CollationName**︰ 如果未指定的預設定序是 SQL_Latin1_General_CP1_CI_AS。  編碼選擇無法變更資料庫上。
- **MaxSizeBytes**︰ 資料庫的預設最大大小為 10 GB。


如需 [參數] 選項的詳細資訊，請參閱[新增 AzureRmSqlDatabase][]並[建立資料庫 (Azure SQL Data Warehouse)][]。

## <a name="next-steps"></a>後續步驟

完成您的 SQL Data Warehouse 佈建後您可能會想要重新[載入範例資料][]，或查看[開發][]、[載入][]時，或[移轉][]的方式。

如果您想要深入瞭解程式設計的方式管理 SQL Data Warehouse 方式，請查看我們的文章︰ 如何使用[PowerShell cmdlet 和 REST Api][]。

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[移轉]: ./sql-data-warehouse-overview-migrate.md
[開發]: ./sql-data-warehouse-overview-develop.md
[載入]: ./sql-data-warehouse-load-with-bcp.md
[載入範例資料]: ./sql-data-warehouse-load-sample-databases.md
[PowerShell cmdlet 和 REST Api]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[firewall rules]: ../sql-database-configure-firewall-settings.md

[如何安裝和設定 PowerShell 的 Azure]: ../powershell/powershell-install-configure.md
[how to create a SQL Data Warehouse from the Azure Portal]: ./sql-data-warehouse-get-started-provision.md
[建立 Azure SQL 資料庫邏輯 server Azure 入口網站]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[建立使用 PowerShell 的 Azure SQL 資料庫邏輯伺服器]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[如何建立資源群組]: ../resource-group-template-deploy-portal.md#create-resource-group

<!--MSDN references--> 
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[新 AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[建立資料庫 (Azure SQL Data Warehouse)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Microsoft Web 平台安裝程式]: https://aka.ms/webpi-azps
[SQL Data Warehouse 價格]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure 免費試用版]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure 貸項總計]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
