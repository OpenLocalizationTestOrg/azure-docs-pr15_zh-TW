<properties 
    pageTitle="開始使用彈性的資料庫工具" 
    description="基本的彈性的資料庫工具] 功能 Azure SQL 資料庫，包括輕鬆執行範例應用程式的說明。" 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="ddove" 
    editor="CarlRabeler"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="ddove"/>

# <a name="get-started-with-elastic-database-tools"></a>開始使用彈性的資料庫工具

這份文件會向您介紹開發人員體驗所執行的範例應用程式。 範例會建立一個簡單的 sharded 應用程式，並瀏覽的彈性的資料庫工具的主要功能。 本範例示範[彈性的資料庫用戶端文件庫](sql-database-elastic-database-client-library.md)的函數

若要安裝文件庫，請移至[Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)。 請注意，如下所述的範例應用程式已安裝的文件庫。

## <a name="prerequisites"></a>必要條件

1. 需要使用 C# 的 visual Studio 2012 或更新版本。 下載免費的版本，在[Visual Studio 下載](http://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)。
2. Nuget 2.7 或更新版本。 若要取得最新版本，請參閱[安裝 NuGet](http://docs.nuget.org/docs/start-here/installing-nuget)

## <a name="download-and-run-the-sample-app"></a>下載並執行範例應用程式

**彈性 Azure SQL 資料庫，快速入門**範例應用程式說明最重要的 sharded 應用程式使用 Azure SQL 資料庫彈性工具開發體驗。 它著重於[晶怪對應管理](sql-database-elastic-scale-shard-map-management.md)、[資料相依路由](sql-database-elastic-scale-data-dependent-routing.md)和[多晶怪查詢](sql-database-elastic-scale-multishard-querying.md)的索引鍵的使用案例。 若要下載並執行範例，請遵循下列步驟︰ 

1. 開啟 Visual Studio，然後選取**檔案]-> [新增專案]-> [**。
2. 在對話方塊中，按一下 [**線上**]。

    ![新的專案 > 線上][2]
3. 然後按一下 [ **Visual C#**在 [**範例**。

    ![按一下 [Visual C#][3]
4. 在 [搜尋] 方塊中，輸入**彈性 db**若要搜尋的範例。標題**為 Azure SQL-快速入門的彈性 DB 工具**會出現。

    ![在搜尋方塊][1]
 
5. 選取範例選擇的名稱，以及新的專案的位置，請按**[確定**] 以建立專案。
6. 範例專案解決方案中開啟**app.config**檔案，然後依照檔案中的指示，新增您 Azure SQL 資料庫伺服器名稱] 和 [登入資訊 （使用者名稱和密碼）。
7. 建立並執行應用程式。 要求您時，請允許 Visual Studio 還原 NuGet 套件的解決方案。 這會從 NuGet 下載最新版的彈性的資料庫用戶端文件庫。
8. 播放使用不同的選項，若要進一步瞭解用戶端文件庫功能。 請注意輸出應用程式會在主控台中的步驟，然後隨意探索幕後的程式碼。

    ![進度][4]

恭喜順利建立並執行第一個 sharded 應用程式使用彈性的資料庫工具 Azure SQL 資料庫。 查看快速擊碎樣本建立的 Visual Studio 或 SQL Server Management Studio 至 Azure 資料庫伺服器的連線。 您會注意到新的範例晶怪資料庫及範例所建立的晶怪地圖 manager 資料庫。

> [AZURE.IMPORTANT] 建議您永遠保持同步處理與更新 Microsoft Azure SQL 資料庫使用 Management Studio 中的最新版本。 [更新 SQL Server Management Studio 中](https://msdn.microsoft.com/library/mt238290.aspx)。


### <a name="key-pieces-of-the-code-sample"></a>關鍵的程式碼範例

1. **管理擊碎和晶怪地圖**︰ 的程式碼會說明如何使用擊碎，範圍，並在中的對應檔案**ShardMapManagerSample.cs**。 您可以找到更多本主題以下資訊︰[晶怪地圖管理](http://go.microsoft.com/?linkid=9862595)。  
2. **資料相依路由**︰ **DataDependentRoutingSample.cs**會顯示的右晶怪交易的路徑。 如需詳細資訊，請參閱[資料相依路由](http://go.microsoft.com/?linkid=9862596)。 
3. **在多個擊碎查詢**︰ 跨擊碎查詢所示**MultiShardQuerySample.cs**的檔案。 如需詳細資訊，請參閱[多晶怪查詢](http://go.microsoft.com/?linkid=9862597)。
4. **新增空白擊碎**︰ 新的空白擊碎反覆運算新增執行**AddNewShardsSample.cs**檔案中的程式碼。 以下涵蓋這個主題的詳細資料︰[晶怪地圖管理](http://go.microsoft.com/?linkid=9862595)。

### <a name="other-elastic-scale-operations"></a>其他彈性的小數位數的作業

1. **分割現有的晶怪**︰ 透過**分割合併工具**提供分割擊碎的功能。 您可以找到以下這項工具的詳細資訊︰[分割合併工具概觀](sql-database-elastic-scale-overview-split-and-merge.md)。
2. **合併現有擊碎**︰ 晶怪合併也會執行使用**分割合併工具**。 如需詳細資訊，請參閱︰[分割合併工具概觀](sql-database-elastic-scale-overview-split-and-merge.md)。   


## <a name="cost"></a>成本

彈性的資料庫工具是免費的項目。 彈性的資料庫工具不會強制額外費用上方的成本，您 Azure 的使用方式。 

例如，範例應用程式會建立新的資料庫。 成本取決於您所選擇的 Azure SQL 資料庫資料庫版本和 Azure 應用程式的使用方式。

價格資訊請參閱[SQL 資料庫定價詳細資料](https://azure.microsoft.com/pricing/details/sql-database/)。

## <a name="next-steps"></a>後續步驟
更多的彈性的資料庫工具的詳細資訊，請參閱︰

* [彈性的資料庫工具的文件引導模式](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/) 
-    程式碼範例︰ 
    -    [彈性 DB 使用 Azure SQL-快速入門](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE)
    -    [彈性 DB Azure SQL-整合實體架構與使用](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
    -    [在 [指令碼中心晶怪 Elasticity](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
-    部落格︰[彈性的縮放比例公告](https://azure.microsoft.com/blog/2014/10/02/introducing-elastic-scale-preview-for-azure-sql-database/)
-    頻道 9︰[彈性的縮放比例概觀影片](http://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
-    討論論壇︰ [Azure SQL 資料庫論壇](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)
-    若要測量的效能︰[效能計數器晶怪地圖管理員](sql-database-elastic-database-client-library.md)


<!--Anchors-->
[The Elastic Scale Sample Application]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Cost]: #Cost
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png
 
