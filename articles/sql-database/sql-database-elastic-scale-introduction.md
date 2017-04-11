<properties
    pageTitle="Azure SQL 資料庫的縮放比例出 |Microsoft Azure"
    description="軟體與服務 (SaaS) 開發人員可使用這些工具雲端中輕鬆建立彈性、 可調整資料庫"
    services="sql-database"
    documentationCenter=""
    manager="jhubbard"
    authors="ddove"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="ddove"/>

# <a name="scaling-out-with-azure-sql-database"></a>擴展 Azure SQL 資料庫

您可以輕鬆地擴充 Azure SQL 資料庫時使用的**彈性的資料庫**工具。 這些工具和功能可讓您使用幾乎無限的資料庫資源的**Azure SQL 資料庫**來建立的交易工作負載及特別是軟體與服務 (SaaS) 應用程式的解決方案。 彈性的資料庫功能所組成下列動作︰

* [彈性的資料庫用戶端文件庫](sql-database-elastic-database-client-library.md)︰ 用戶端文件庫是可讓您建立和維護 sharded 資料庫的功能。  請參閱[開始使用彈性的資料庫工具](sql-database-elastic-scale-get-started.md)。
* [彈性的資料庫分割合併的工具](sql-database-elastic-scale-overview-split-and-merge.md)︰ sharded 資料庫之間移動資料。 這是很適合用於將資料從多重租用戶資料庫移至單一租用戶資料庫 （或相反）。 請參閱[彈性資料庫分割合併工具教學課程](sql-database-elastic-scale-configure-deploy-split-and-merge.md)。
* [彈性的資料庫作業](sql-database-elastic-jobs-overview.md)（預覽版本）︰ 使用工作管理大量的 Azure SQL 資料庫。 輕鬆地執行系統管理的作業，例如結構描述變更、 認證管理、 參考資料更新、 收集效能資料或使用工作的租用戶 （客戶） 遙測集合。
* [彈性的資料庫查詢](sql-database-elastic-query-overview.md)（預覽版本）︰ 可讓您執行合併多個資料庫的 SQL 查詢。 這可讓連線至 [報表工具，例如 Excel、 中、 Tableau 等等。
* [彈性的交易](sql-database-elastic-transactions-overview.md)︰ 此功能可讓您執行橫跨多個資料庫 Azure SQL 資料庫中的交易。 彈性的資料庫交易可供使用 ADO.NET.NET 應用程式，並使用[System.Transaction 類別](https://msdn.microsoft.com/library/system.transactions.aspx)的熟悉程式設計體驗與整合。

下圖顯示的架構，包括**彈性的資料庫功能**相對於資料庫的集合。

此圖中的資料庫的色彩代表結構描述。 具有相同色彩資料庫共用相同的結構描述。

1. 一組**Azure SQL 資料庫**裝載 Azure 使用 sharding 架構。
2. **彈性的資料庫用戶端文件庫**用來管理晶怪設定。
3. **彈性的資料庫集區**被放置資料庫的子集合。 (請參閱[什麼是資料庫？](sql-database-elastic-pool.md))。
4. **彈性的資料庫作業**執行排程或臨機操作 T SQL 指令碼針對所有的資料庫。
5. **分割合併工具**用來將資料移到另一個晶怪。
6. **彈性的資料庫查詢**可讓您撰寫橫跨晶怪設定中的所有資料庫的查詢。
7. **彈性的交易**可讓您執行橫跨多個資料庫的交易。 


![彈性的資料庫工具][1]


## <a name="why-use-the-tools"></a>為什麼要使用的工具？

結案 elasticity 和小數位數的雲端應用程式已經過直接 Vm 和 blob 儲存體，只需新增或減去單位，或增加 power。 但仍有保留狀態關聯式資料庫中的資料處理的挑戰。 在下列情況中出現的挑戰︰

* 成長和壓縮的工作量關聯式資料庫組件的容量。
* 管理可能會影響特定的子集資料 – 例如特別忙碌結束客戶 （租用戶） 的作用點。

一般而言，這些案例都能獲得解決，投資支援的應用程式的大規模資料庫伺服器。 不過，此選項會在雲端預先定義的商業硬體的所有處理都情況位置有限制。 不過，散發資料和許多相同結構化資料庫之間的處理 （亦稱為 「 sharding 」 擴充模式） 提供傳統調種方法都成本和 elasticity 替代方案。

## <a name="horizontal-and-vertical-scaling"></a>水平與垂直縮放比例

下圖顯示水平與垂直尺寸的縮放比例，也就是可以縮放彈性的資料庫的基本方式。

![水平與垂直 Scaleout][2]

若要新增或移除資料庫，才能調整容量或整體效能是指水平縮放比例。 這也稱為 「 縮放比例]。 Sharding，資料分割到相同的結構化的資料庫，集合中是常見的方式，來實作水平縮放比例。  

垂直調整參照增加或減少個別的資料庫的效能層級，也稱為 「 縮放]。

大部分的雲端比例資料庫應用程式會使用下列兩個策略的組合。 例如，軟體與服務應用程式可能會使用佈建新客戶的縮放水平及垂直縮放比例允許放大或縮小資源，如有需要工作負載的每個結尾的客戶的資料庫。

* 水平縮放管理使用[彈性的資料庫用戶端文件庫](sql-database-elastic-database-client-library.md)。

* 垂直縮放比例完成使用 Azure PowerShell cmdlet 來變更服務層級，或放彈性的資料庫中的資料庫。

## <a name="sharding"></a>Sharding

*Sharding*是一種技術將大量的相同結構化資料分散到多個獨立的資料庫。 這是與雲端開發人員建立軟體與服務 (SAAS) 方案結束客戶或企業特別是常用。 這些結束客戶通常稱為 「 租用戶 」。 Sharding 可能需要的任何數字的原因︰  

* 資料的總數量太大而無法符合單一資料庫的限制式
* 整體工作負載的交易處理能力超過一個資料庫的功能
* 租用戶可能會要求彼此，實體隔離，讓不同的資料庫所需的每個租用戶
* 資料庫的各節可能需要位於不同的地區規範、 效能或淉的原因。

在其他的情況下，ingestion 的資料分佈的裝置，例如 sharding 可以用於填滿一組︰ 組織的資料庫。 例如，不同的資料庫能單獨使用每日或週。 在此情況下，sharding 可以是整數，代表日期 （簡報中的 sharded 資料表的所有資料列），然後必須路由查詢擷取日期範圍內的資訊子集應用程式的資料庫，其涵蓋問題的範圍。

在應用程式中的每次交易可以限制 sharding 索引鍵的單一值時，sharding 效果最佳。 確保所有的交易會在特定的資料庫的本機。

## <a name="multi-tenant-and-single-tenant"></a>多租用戶與單一租用戶

部分應用程式使用每個租用戶中建立不同的資料庫中的最簡單的方法。 這是提供隔離、 備份/還原功能及縮放比例租用戶資料粒度的資源**單一租用戶 sharding 圖樣**。 使用單一租用戶 sharding 每個資料庫與特定的租用戶識別碼值 （或客戶索引鍵值），但該金鑰需要不一定會出現在本身的資料。 將每個要求路由至適當的資料庫 – 的應用程式的責任並的用戶端文件庫可以簡化工作。

![與多租用戶的單一租用戶][4]

其他人案例套件多個租用戶一起到資料庫]，而不是將它們隔離成不同的資料庫。 這是一般**多租用戶 sharding 模式**–，可能會受到事實應用程式管理大量的很小的租用戶。 在多租用戶 sharding 資料庫資料表中的資料列的所有設計執行鍵識別租用戶 ID 或 sharding 鍵。 同樣地，應用程式層負責租用戶的要求路由至適當的資料庫，，這可以支援彈性的資料庫用戶端文件庫。 此外，列層級安全性可用來篩選資料列可存取的每個租用戶 – 如需詳細資訊，請參閱[多租用戶有彈性的資料庫的工具和列層級安全性的應用程式](sql-database-elastic-tools-multi-tenant-row-level-security.md)。 可能需要使用多租用戶 sharding 圖樣的轉散發資料庫之間的資料，這可協助彈性的資料庫分割合併工具。 若要進一步瞭解使用彈性的集區 SaaS 應用程式的設計模式，請參閱[Azure SQL 資料庫的多租用戶 SaaS 應用程式的設計模式](sql-database-design-patterns-multi-tenancy-saas-applications.md)。

### <a name="move-data-from-multiple-to-single-tenancy-databases"></a>將資料從多個移至單一 tenancy 資料庫

在建立 SaaS 應用程式時，通常會提供預期客戶試用版的軟體。 在此情況下，則效益使用多租用戶資料庫的資料。 不過，客戶的事時，單一租用戶資料庫是因為它會提供更佳的效能。 如果客戶資料建立試用期間，使用[分割合併工具](sql-database-elastic-scale-overview-split-and-merge.md)，將資料從多重租用戶移到新的單一租用戶資料庫。

## <a name="next-steps"></a>後續步驟

示範用戶端文件庫之範例應用程式，請參閱[開始使用彈性 Datababase 工具](sql-database-elastic-scale-get-started.md)。

若要將現有的資料庫，若要使用的工具，請參閱[將現有的資料庫來擴充](sql-database-elastic-convert-to-use-elastic-tools.md)。

若要查看的彈性的資料庫集區細節，請參閱[價格和效能考量彈性的資料庫資料庫](sql-database-elastic-pool-guidance.md)，或使用[教學課程](sql-database-elastic-pool-create-portal.md)中建立新的資料庫。  

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-introduction/tools.png
[2]:./media/sql-database-elastic-scale-introduction/h_versus_vert.png
[3]:./media/sql-database-elastic-scale-introduction/overview.png
[4]:./media/sql-database-elastic-scale-introduction/single_v_multi_tenant.png

