<properties
   pageTitle="若要將資料載入至 SQL Data Warehouse 使用 Redgate 的資料的平台 Studio |Microsoft Azure"
   description="瞭解如何使用 Redgate 的資料的平台 Studio 資料倉儲案例。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/13/2016"
   ms.author="mausher;barbkess"/>


# <a name="load-data-with-redgate-data-platform-studio"></a>載入 Redgate 資料平台 Studio 與資料

> [AZURE.SELECTOR]
- [Redgate](sql-data-warehouse-load-with-redgate.md)
- [資料工廠](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
- [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)
- [BCP](sql-data-warehouse-load-with-bcp.md)

本教學課程教您如何使用[Redgate 的資料的平台 Studio](http://www.red-gate.com/products/azure-development/data-platform-studio/) (DP)，將資料從內部部署的 SQL Server Azure SQL Data Warehouse。 資料平台 Studio 適用於最適當的相容性修正與最佳化，因此開始使用 SQL Data Warehouse 最快速的方法。

> [AZURE.NOTE] [Redgate](http://www.red-gate.com)是完整時間的 Microsoft 合作夥伴提供各種不同的 SQL Server 工具。 在資料的平台 Studio 這項功能已對自由供商業及非商業用途。

## <a name="before-you-begin"></a>開始之前
### <a name="create-or-identify-resources"></a>建立或識別資源

在開始之前本教學課程中，您必須具備︰

- **內部部署的 SQL Server 資料庫**︰ 您想要匯入至 SQL Data Warehouse 資料需要來自內部部署的 SQL Server (版本 2008R2 或上方)。 資料平台 Studio 無法匯入資料，直接從 Azure SQL 資料庫或文字檔案。
- **Azure 儲存體帳戶**︰ 資料的平台 Studio 之前載入 SQL Data Warehouse 階段 Azure Blob 儲存體中的資料。 儲存帳戶必須使用 「 資源管理員 」 部署模型 （預設） 而不 「 傳統 」 部署模型。 如果您沒有儲存帳戶，瞭解如何建立儲存的帳戶。 
- **SQL Data Warehouse**︰ 本教學課程中將資料從內部部署的 SQL Server 移至 SQL Data Warehouse，因此您必須具備資料倉庫線上。 如果您還沒有資料倉庫，瞭解如何建立 Azure SQL Data Warehouse。

> [AZURE.NOTE] 在同一個區域中建立的儲存空間帳戶和資料倉庫較佳的效能。

## <a name="step-1-sign-in-to-data-platform-studio-with-your-azure-account"></a>步驟 1︰ 登入資料的平台 Studio 與 Azure 帳戶
開啟網頁瀏覽器，然後瀏覽至[資料的平台 Studio](https://www.dataplatformstudio.com/)網站。 您用來建立帳戶及資料倉庫相同 Azure 帳戶登入。 如果您的電子郵件地址與公司或學校帳戶與 Microsoft 帳戶相關聯，請務必選擇具有資源的存取權的帳戶。

> [AZURE.NOTE] 如果這是您第一次使用資料的平台 Studio，會要求您授與管理 Azure 資源的應用程式權限。

## <a name="step-2-start-the-import-wizard"></a>步驟 2︰ 開始匯入精靈
從 DP 主畫面上，選取 [匯入 Azure SQL Data Warehouse 連結以啟動匯入精靈]。

![][1]

## <a name="step-3-install-the-data-platform-studio-gateway"></a>步驟 3︰ 安裝資料平台 Studio 閘道器
若要連線到您內部部署的 SQL Server 資料庫，您必須安裝 DP 閘道器。 閘道器在用戶端代理程式可存取您的內部部署環境、 擷取資料，並將它上載至您儲存的帳戶。 您的資料永遠不會經過 Redgate 的伺服器。 若要安裝的閘道器︰

1.  按一下 [**建立閘道器**] 連結
2. 下載並安裝使用所提供的安裝程式的閘道器

![][2]

> [AZURE.NOTE] 閘道器可以在來源 SQL Server 資料庫的網路存取任何電腦上安裝。 存取 Windows 驗證使用目前的使用者認證的 SQL Server 資料庫。

安裝後，閘道器狀態就會變更為 [已連線，您可以選取 [下一步]。

## <a name="step-4-identify-the-source-database"></a>步驟 4︰ 找出在來源資料庫
在 [*輸入伺服器名稱*] 文字方塊中，輸入主控您的資料庫，然後選取 [**下一步**伺服器的名稱。 然後從下拉式功能表中，選取您想要匯入資料的資料庫。

![][3]

DP 盡可能地壓縮選取的資料庫匯入的資料表。 根據預設，DP 匯入資料庫中的所有資料表。 您可以選取或取消選取表格，請展開所有資料表] 連結。 選取 [向前移動 [下一步] 按鈕。

## <a name="step-5-choose-a-storage-account-to-stage-the-data"></a>步驟 5︰ 選擇要階段資料的儲存空間帳戶
DP 會提示您輸入的階段資料的位置。 從您的訂閱選擇現有的儲存空間帳戶，然後選取 [**下一步**]。

> [AZURE.NOTE] 將所選的儲存空間帳戶中建立新的 blob 容器 DP，並使用不同的資料夾來儲存每個匯入。

![][4]

## <a name="step-6-select-a-data-warehouse"></a>步驟 6︰ 選取資料倉庫
接下來，您可以選取線上[Azure SQL Data Warehouse](http://aka.ms/sqldw)資料庫匯入資料。 一旦您已選取您的資料庫，您需要輸入認證以連線至資料庫，然後選取 [**下一步**。

![][5]

> [AZURE.NOTE] DP 合併到資料倉庫的來源資料的資料表。 DP 警告您如果資料表名稱時有此需要覆寫現有的資料表中資料倉庫。 您可以選擇性地刪除資料倉庫中任何現有的物件來播放刪除之前匯入所有現有的物件。

## <a name="step-7-import-the-data"></a>步驟 7︰ 匯入資料
DP 確認您想要匯入資料。 只要按一下 [開始匯入] 按鈕，若要開始匯入資料。

![][6]

DP 顯示顯示進度的擷取及上傳內部部署的 SQL Server 或匯入 SQL Data Warehouse 的進度的資料視覺效果。

![][7]

匯入完成後，DP 顯示資料匯入的摘要與變更報表的相容性修正程式，執行。

![][8]

## <a name="next-steps"></a>後續步驟
若要探索您在 SQL Data Warehouse 內的資料，請先檢視︰

- [查詢 Azure SQL Data Warehouse (Visual Studio)][]
- [使用 Power BI 的資料視覺化][]

若要深入瞭解 Redgate 的資料的平台 Studio:

- [請造訪 DP 首頁](http://www.dataplatformstudio.com/)
- [觀看在 Channel9 DP 的示範](https://channel9.msdn.com/Blogs/cloud-with-a-silver-lining/Loading-data-into-Azure-SQL-Datawarehouse-with-Redgate-Data-Platform-Studio)

如需移轉並載入 SQL Data Warehouse 資料的其他方式的概觀，請參閱︰

- [將您的方案移轉到 SQL Data Warehouse][]
- [將資料載入至 Azure SQL Data Warehouse](./sql-data-warehouse-overview-load.md)

如需開發秘訣，請參閱[SQL Data Warehouse 開發概觀](./sql-data-warehouse-overview-develop.md)。

<!--Image references-->
[1]: media/sql-data-warehouse-redgate/2016-10-05_15-59-56.png
[2]: media/sql-data-warehouse-redgate/2016-10-05_11-16-07.png
[3]: media/sql-data-warehouse-redgate/2016-10-05_11-17-46.png
[4]: media/sql-data-warehouse-redgate/2016-10-05_11-20-41.png
[5]: media/sql-data-warehouse-redgate/2016-10-05_11-31-24.png
[6]: media/sql-data-warehouse-redgate/2016-10-05_11-32-20.png
[7]: media/sql-data-warehouse-redgate/2016-10-05_11-49-53.png
[8]: media/sql-data-warehouse-redgate/2016-10-05_12-57-10.png

<!--Article references-->
[查詢 Azure SQL Data Warehouse (Visual Studio)]: ./sql-data-warehouse-query-visual-studio.md
[使用 Power BI 的資料視覺化]: ./sql-data-warehouse-get-started-visualize-with-power-bi.md
[將您的方案移轉到 SQL Data Warehouse]: ./sql-data-warehouse-overview-migrate.md
[Load data into Azure SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md