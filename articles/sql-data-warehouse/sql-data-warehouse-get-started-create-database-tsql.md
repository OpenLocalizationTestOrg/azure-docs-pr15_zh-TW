<properties
   pageTitle="建立 SQL Data Warehouse TSQL |Microsoft Azure"
   description="瞭解如何使用 TSQL 建立 Azure SQL Data Warehouse"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/24/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="create-a-sql-data-warehouse-database-by-using-transact-sql-tsql"></a>使用 TRANSACT-SQL (TSQL) 來建立 SQL Data Warehouse 資料庫

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

本文將示範如何建立使用 T SQL SQL Data Warehouse。

## <a name="prerequisites"></a>必要條件

若要開始，您需要︰ 

- **Azure 帳戶**︰ 造訪[Azure 免費試用版][]或[MSDN Azure 貸項總計][]，以建立帳戶。
- **Azure SQL server**︰ 如需詳細資訊，請參閱[建立具有 Azure 入口網站的 Azure SQL 資料庫邏輯伺服器][]或[建立使用 PowerShell 的 Azure SQL 資料庫邏輯伺服器][]。
- **資源群組**︰ 與您 Azure SQL server 中使用相同的資源群組，或瞭解[如何建立資源群組][]。
- **若要執行 T SQL 環境**︰ 您可以使用[Visual Studio][Installing Visual Studio and SSDT]， [sqlcmd][]或執行 T SQL [SSMS][] 。

> [AZURE.NOTE] 建立 SQL Data Warehouse 可能會產生新的計費服務。  如需價格的詳細資訊，請參閱[SQL Data Warehouse 價格][]。

## <a name="create-a-database-with-visual-studio"></a>使用 Visual Studio 建立資料庫

如果您是新的 Visual studio，請參閱[查詢 Azure SQL Data Warehouse (Visual Studio)][]。  若要開始，Visual Studio 中開啟 SQL Server 物件總管]，然後連線到裝載您的 SQL Data Warehouse 資料庫伺服器。  連線之後，您可以對**母片**的資料庫執行 SQL 命令來建立 SQL Data Warehouse。  這個命令建立資料庫 MySqlDwDb DW400 服務目標，並允許資料庫成長 10 TB 的最大的大小。

```sql
CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## <a name="create-a-database-with-sqlcmd"></a>使用 sqlcmd 建立資料庫

或者，您也可以在命令提示字元中執行下列動作，以執行相同的命令與 sqlcmd。

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

如果未指定的預設定序是自動分頁 SQL_Latin1_General_CP1_CI_AS。  `MAXSIZE`可以介於 250 GB 和 240 TB。  `SERVICE_OBJECTIVE`可以介於 DW100 和 DW2000 [DWU][]。  如需所有有效的值清單，請參閱[建立資料庫][]的 MSDN 文件。  以[ALTER DATABASE][] T SQL 命令，來變更 MAXSIZE 和 SERVICE_OBJECTIVE。  建立後，就無法變更定序的資料庫。   變更為變更 DWU SERVICE_OBJECTIVE 會導致重新啟動的服務，讓它取消航班中的所有查詢時，應注意。  變更 MAXSIZE 無法重新啟動服務現狀只要簡單的中繼資料作業。

## <a name="next-steps"></a>後續步驟

佈建您完成您的 SQL Data Warehouse 後可以[載入範例資料][]，或查看[開發][]、[載入][]時，或[移轉][]的方式。

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[how to create a SQL Data Warehouse from the Azure portal]: sql-data-warehouse-get-started-provision.md
[查詢 Azure SQL Data Warehouse (Visual Studio)]: sql-data-warehouse-query-visual-studio.md
[移轉]: sql-data-warehouse-overview-migrate.md
[開發]: sql-data-warehouse-overview-develop.md
[載入]: sql-data-warehouse-overview-load.md
[載入範例資料]: sql-data-warehouse-load-sample-databases.md
[建立 Azure SQL 資料庫邏輯 server Azure 入口網站]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[建立使用 PowerShell 的 Azure SQL 資料庫邏輯伺服器]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[如何建立資源群組]: ../resource-group-template-deploy-portal.md#create-resource-group
[Installing Visual Studio and SSDT]: sql-data-warehouse-install-visual-studio.md
[sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--MSDN references--> 
[建立資料庫]: https://msdn.microsoft.com/library/mt204021.aspx
[變更資料庫]: https://msdn.microsoft.com/library/mt204042.aspx
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx

<!--Other Web references-->
[SQL Data Warehouse 價格]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure 免費試用版]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure 貸項總計]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
