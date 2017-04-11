<properties
   pageTitle="Azure 入口網站中建立 SQL Data Warehouse |Microsoft Azure"
   description="瞭解如何建立 Azure SQL Data Warehouse Azure 入口網站中"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/25/2016"
   ms.author="barbkess;lodipalm;sonyama"/>

# <a name="create-an-azure-sql-data-warehouse"></a>建立 SQL Azure Data Warehouse

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

本教學課程中會用來建立包含 AdventureWorksDW 範例資料庫的 SQL Data Warehouse Azure 入口網站。


## <a name="prerequisites"></a>必要條件

若要開始，您需要︰

- **Azure 帳戶**︰ 造訪[Azure 免費試用版][]或[MSDN Azure 貸項總計][]，以建立帳戶。
- **Azure SQL server**︰ 如需詳細資訊請參閱[建立 Azure SQL 資料庫伺服器邏輯 Azure 入口網站][]。

> [AZURE.NOTE] 建立 SQL Data Warehouse 可能會產生新的計費服務。  如需詳細資訊，請參閱[SQL Data Warehouse 價格][]。

## <a name="create-a-sql-data-warehouse"></a>建立 SQL Data Warehouse

1. [Azure 入口網站](https://portal.azure.com)登入。

2. 按一下**[+ 新增** > **資料 + 的儲存空間** > **SQL Data Warehouse**。

    ![建立](./media/sql-data-warehouse-get-started-provision/create-sample.gif)

3. 在**SQL Data Warehouse**刀，填滿的資訊會有需要，然後按下 [建立]，若要建立。

    ![建立資料庫](./media/sql-data-warehouse-get-started-provision/create-database.png)

    - **伺服器**︰ 我們建議您先選取您的伺服器。  

    - **資料庫名稱**︰ 用來參照 SQL Data Warehouse 的名稱。  必須是唯一至伺服器。
    
    - **效能**︰ 我們建議您著手 400 [DWUs][DWU]。 您可以向左或向右調整您的資料倉庫的效能或縮放建立之後的向上或向下移動滑桿。  若要進一步瞭解 DWUs，請參閱我們的說明文件上[縮放比例](./sql-data-warehouse-manage-compute-overview.md)或我們[價格頁面][SQL Data Warehouse 價格]。 

    - **訂閱**︰ 選取此 SQL Data Warehouse 會以結算的[訂閱]。

    - **資源群組**︰[資源群組][Resource group]是設計用來協助您管理 Azure 資源的集合容器。 進一步瞭解[資源群組](../azure-resource-manager/resource-group-overview.md)。

    - **選取來源**︰ 按一下 [**選取來源** > **範例**。 Azure 會自動填入 AdventureWorksDW**選取 [範例**] 選項。

> [AZURE.NOTE] SQL Data Warehouse 的預設定序是 SQL_Latin1_General_CP1_CI_AS。 如果需要不同的編碼， [T SQL][]可使用不同的編碼建立資料庫。

4. 按一下 [**建立**]，建立您的 SQL Data Warehouse]。

5. 等待幾分鐘的時間。 當您的資料倉庫時，您應該會傳回至[Azure 入口網站](https://portal.azure.com)。 您可以在儀表板，列出您的 SQL 資料庫] 下，或您用來建立的 [資源] 群組中，以找到您的 SQL Data Warehouse。 

    ![入口網站檢視](./media/sql-data-warehouse-get-started-provision/database-portal-view.png)

[AZURE.INCLUDE [SQL Database create server](../../includes/sql-database-create-new-server-firewall-portal.md)] 

## <a name="next-steps"></a>後續步驟

現在，您必須建立 SQL Data Warehouse，您即可[連線](./sql-data-warehouse-connect-overview.md)，並開始查詢。

若要將資料載入 SQL Data Warehouse，請參閱[載入概觀](./sql-data-warehouse-overview-load.md)。

如果您想要以 SQL Data Warehouse 移轉現有的資料庫，請參閱[移轉概觀](./sql-data-warehouse-overview-migrate.md)，或使用[移轉公用程式](./sql-data-warehouse-migrate-migration-utility.md)。

防火牆規則也可以使用 TRANSACT-SQL 來設定。 如需詳細資訊，請參閱[sp_set_firewall_rule][]和[sp_set_database_firewall_rule][]。

也很好的點子，看[的最佳作法][]。

<!--Article references-->
[建立 Azure SQL 資料庫邏輯 server Azure 入口網站]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Create an Azure SQL Database logical server with PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[resource groups]: ../resource-group-template-deploy-portal.md
[最佳作法]: sql-data-warehouse-best-practices.md
[DWU]: sql-data-warehouse-overview-what-is.md#data-warehouse-units
[訂閱]: ../azure-glossary-cloud-terminology.md#subscription
[resource group]: ../azure-glossary-cloud-terminology.md#resource-group
[T SQL]: ./sql-data-warehouse-get-started-create-database-tsql.md
 
<!--MSDN references-->
[sp_set_firewall_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp_set_database_firewall_rule]: https://msdn.microsoft.com/library/dn270010.aspx

<!--Other Web references-->
[SQL Data Warehouse 價格]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure 免費試用版]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure 貸項總計]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

