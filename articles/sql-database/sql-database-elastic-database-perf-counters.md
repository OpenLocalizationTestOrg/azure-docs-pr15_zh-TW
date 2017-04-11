<properties
    pageTitle="正在晶怪對應管理員"
    description="ShardMapManager 類別及資料相依路由效能計數器"
    services="sql-database"
    documentationCenter=""
    manager="jhubbard"
    authors="SilviaDoomra"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/23/2016"
    ms.author="SilviaDoomra"/>

# <a name="performance-counters-for-shard-map-manager"></a>正在晶怪對應管理員

使用[資料相依路由](sql-database-elastic-scale-data-dependent-routing.md)時，尤其是，您就可以擷取[晶怪地圖管理員](sql-database-elastic-scale-shard-map-management.md)] 的效能。 計數器都建立 Microsoft.Azure.SqlDatabase.ElasticScale.Client 類別的方法。  

計數器用來追蹤[資料相依路由](sql-database-elastic-scale-data-dependent-routing.md)作業的效能。 這些計數器就可以存取的效能監視器，「 彈性資料庫:: 晶怪管理 」 類別下。

**最新版本︰**移至[Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)。 另請參閱[升級應用程式，使用最新的彈性的資料庫用戶端文件庫](sql-database-elastic-scale-upgrade-client-library.md)。

## <a name="prerequisites"></a>必要條件

* 若要建立效能分類和計數器，使用者必須裝載應用程式的電腦上的本機**管理員**群組的一部分。  

* 若要建立效能計數器執行個體，並更新計數器，使用者必須是**系統管理員**或**效能監視器使用者**群組的成員。 

## <a name="create-performance-category-and-counters"></a>建立效能分類和計數器 

若要建立計數器，呼叫 CreatePeformanceCategoryAndCounters 方法[ShardMapManagmentFactory 類別](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.aspx)。 以系統管理員可以執行這個方法︰ 

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

您也可以使用[這個](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283)PowerShell 指令碼來執行方法。 此方法會建立下列效能計數器︰  

* **庫存對應**︰ 晶怪地圖的快取對應的數字。
*  **每秒 DDR 作業**︰ 資料相依路由的操作晶怪對應的工資率。 在通話中的成功連線至目的地晶怪[OpenConnectionForKey()](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx)結果時，這個 [計數器會更新。 
*  **對應查閱快取操作/秒**︰ 率晶怪對應中的對應的成功的快取查閱作業。 
*  **對應查閱快取遺漏秒**︰ 率失敗快取對應晶怪對應中的查閱作業。
*  **對應新增或更新快取秒**︰ 哪些對應新增或更新的快取晶怪對應的工資率。 
*  **對應從中快取秒**︰ 用以對應則會被移除從晶怪對應的快取的工資率。 

對每個快取晶怪對應，每個程序建立的效能計數器。  


## <a name="notes"></a>備忘稿
下列事件觸發的效能計數器建立︰  

* 使用[立即載入](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy.aspx)，如果 ShardMapManager 包含任何晶怪地圖[ShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx)初始化。 包括[GetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx?f=255&MSPPError=-2147217396#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardMapManagerFactory.GetSqlShardMapManager%28System.String,Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardMapManagerLoadPolicy%29)和[TryGetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx)方法。
* 成功 （使用[GetShardMap()](https://msdn.microsoft.com/library/azure/dn824215.aspx)、 [GetListShardMap()](https://msdn.microsoft.com/library/azure/dn824212.aspx)或[GetRangeShardMap()](https://msdn.microsoft.com/library/azure/dn824173.aspx)） 晶怪對應的搜尋。 

* 成功建立使用 CreateShardMap() 晶怪地圖。

效能計數器就會更新所執行的晶怪地圖和對應的所有快取作業。 使用 [刪除的效能計數器執行個體中的 [DeleteShardMap （) reults 晶怪對應的順利移除。  

## <a name="best-practices"></a>最佳作法

* 建立效能分類和計數器應該之前建立 ShardMapManager 物件一次執行。 每次執行命令的 CreatePerformanceCategoryAndCounters() 會清除上一個計數器 （保留報告的所有執行個體的資料），並建立新的文件。  

* 每個程序建立效能計數器執行個體。 任何應用程式當機或晶怪地圖從快取移除會導致效能計數器執行個體的刪除。  

### <a name="see-also"></a>另請參閱

[彈性的資料庫功能概觀](sql-database-elastic-scale-introduction.md)  

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->

