<properties
   pageTitle="Azure SQL 資料庫建立多租用戶隔離與效率的應用程式"
   description="瞭解如何 SQL 資料庫建立多租用戶應用程式"
   keywords=""
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="10/13/2016"
   ms.author="carlrab"/>

# <a name="builds-multi-tenant-apps-with-azure-sql-database-with-isolation-and-efficiency"></a>建立多租用戶應用程式與隔離與效率 Azure SQL 資料庫

## <a name="leverage-elastic-pools-and-build-more-efficient-multi-tenant-apps"></a>運用彈性的資料庫，並建立更有效率多租用戶相關應用程式

如果您是 SaaS 開發撰寫多租用戶應用程式，以及處理許多客戶，您通常在必須做的取捨客戶效能、 管理及安全性。 Azure SQL 資料庫彈性資料庫集區，與您不再需要進行危害。 這些集區可協助您管理並監控多租用戶相關應用程式，並取得隔離一位客戶-資料庫的優點。 請參閱[Azure SQL 資料庫的多租用戶 SaaS 應用程式的設計模式](sql-database-design-patterns-multi-tenancy-saas-applications.md)。

![建立-多重-租用戶的應用程式](./media/sql-database-build-multi-tenant-apps/sql-database-build-multi-tenant-apps.png)

## <a name="auto-scaling-you-control"></a>自動調整大小控制

集區自動調整效能和彈性的資料庫的儲存空間容量即時。 您可以控制指派給集區的效能、 新增或移除彈性的資料庫，視情況下，並不會影響整體成本集區的定義彈性的資料庫的效能。 這表示您不必擔心管理個別資料庫的使用方式。

[閱讀文件](sql-database-elastic-pool.md)

## <a name="intelligent-management-of-your-environment"></a>智慧管理您的環境

內建的縮放建議主動識別要受益資料庫的資料庫。 這些建議允許快速最佳化以符合您的效能目標的 「 模擬 」 分析。 Rtf 效能監視及疑難排解儀表板可協助您以視覺化方式呈現歷史集區的使用情況。

[閱讀文件](sql-database-elastic-pool-guidance.md)

## <a name="performance-and-price-to-meet-your-needs"></a>效能與以符合您需求的價格

基本、 標準、 進階資料庫會提供廣泛的效能、 儲存和價格選項。 集區最多可以包含多達 400 彈性的資料庫。 自動調整進位到 1000年彈性的資料庫交易單位 (eDTU) 可彈性的資料庫。

[閱讀文件](https://azure.microsoft.com/pricing/details/sql-database/?b=16.50)

## <a name="elastic-tools"></a>彈性的工具

除了彈性的資料庫，有可協助您管理作業活動跨多個資料庫的 SQL 資料庫功能︰

**執行跨資料庫查詢及報表。**  
[彈性的資料庫查詢](sql-database-elastic-query-overview.md)可讓您執行查詢或報表，在您彈性的資料庫中的資料庫，並存取遠端一次儲存您的資料庫的許多資料庫中的資料。

**執行交叉資料庫交易。**  
[彈性的資料庫交易](sql-database-elastic-transactions-overview.md)可讓您執行交易橫跨多個 SQL 資料庫中的資料庫，並執行作業 （資料庫之間的處理財務交易時，也就是或更新庫存一個資料庫] 和 [訂單時）。

**執行數個資料庫相同的作業。**  
[彈性的資料庫作業](sql-database-elastic-jobs-overview.md)執行管理作業，例如重建索引或更新過您彈性的資料庫中的每個資料庫的結構描述。

移至首頁，若要查看所提供的其他 SQL 資料庫。
[簽出](https://azure.microsoft.com/services/sql-database/) 

## <a name="next-steps"></a>後續步驟

取得[免費的 Azure 訂閱](https://azure.microsoft.com/get-started/)及[建立您的第一個 Azure SQL 資料庫](sql-database-get-started.md)。

## <a name="additional-resources"></a>其他資源

瀏覽所有的[SQL 資料庫的功能](https://azure.microsoft.com/services/sql-database/)。
 
檢閱[技術概觀 SQL 資料庫](sql-database-technical-overview.md)。  
