<properties
   pageTitle="移轉現有的資料庫來擴充 |Microsoft Azure"
   description="轉換使用彈性的資料庫工具建立晶怪地圖管理員 sharded 資料庫"
   services="sql-database"
   documentationCenter=""
   authors="ddove"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="10/24/2016"
   ms.author="ddove"/>

# <a name="migrate-existing-databases-to-scale-out"></a>移轉現有的資料庫來擴充

輕鬆地管理現有縮放出 sharded 資料庫使用 Azure SQL 資料庫資料庫工具 （例如[彈性的資料庫用戶端文件庫](sql-database-elastic-database-client-library.md)中）。 您必須先轉換的資料庫來使用[晶怪對應管理員](sql-database-elastic-scale-shard-map-management.md)現有的集合。 

## <a name="overview"></a>概觀
若要移轉現有的 sharded 資料庫︰ 

1. 準備[晶怪地圖 manager 資料庫](sql-database-elastic-scale-shard-map-management.md)。
2. 建立晶怪地圖。
3. 準備個別擊碎。  
2. 新增對應至晶怪地圖。

可以使用[.NET Framework 用戶端文件庫](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)中或位於[Azure SQL 資料庫彈性的資料庫工具指令碼](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db)的 PowerShell 指令碼實作技巧。 以下範例使用 PowerShell 指令碼。

如需有關 ShardMapManager 的詳細資訊，請參閱[晶怪對應管理](sql-database-elastic-scale-shard-map-management.md)。 如需彈性的資料庫工具的概觀，請參閱[彈性的資料庫功能概觀](sql-database-elastic-scale-introduction.md)。

## <a name="prepare-the-shard-map-manager-database"></a>準備晶怪地圖 manager 資料庫

晶怪地圖管理員是一個特殊的資料庫，包含要管理縮放出資料庫的資料。 您可以使用現有的資料庫，或建立新的資料庫。 請注意，做為晶怪地圖管理員資料庫不應該與晶怪相同的資料庫。 另請注意的 PowerShell 指令碼不會為您建立資料庫。 

## <a name="step-1-create-a-shard-map-manager"></a>步驟 1︰ 建立晶怪地圖管理員

    # Create a shard map manager. 
    New-ShardMapManager -UserName '<user_name>' 
    -Password '<password>' 
    -SqlServerName '<server_name>' 
    -SqlDatabaseName '<smm_db_name>' 
    #<server_name> and <smm_db_name> are the server name and database name 
    # for the new or existing database that should be used for storing 
    # tenant-database mapping information.

### <a name="to-retrieve-the-shard-map-manager"></a>若要擷取晶怪地圖管理員

建立之後，您可以擷取晶怪地圖管理員使用這個指令程式。 不需要此步驟中，每當您需要使用 ShardMapManager 物件。

    # Try to get a reference to the Shard Map Manager  
    $ShardMapManager = Get-ShardMapManager -UserName '<user_name>' 
    -Password '<password>' 
    -SqlServerName '<server_name>' 
    -SqlDatabaseName '<smm_db_name>' 

  
## <a name="step-2-create-the-shard-map"></a>步驟 2︰ 建立晶怪地圖

您必須先選取晶怪對應] 以建立的類型。 選擇資料庫架構而定︰ 

1. 每個資料庫的單一租用戶 （的字詞，請參閱[詞彙](sql-database-elastic-scale-glossary.md)）。 
2. 多個租用戶，每個資料庫 （也就是兩種類型）︰
    3. 清單對應
    4. 對應的範圍
 

如果是單一租用戶模型，建立**清單對應**晶怪地圖。 單一租用戶模型指派一個資料庫，每一租用戶。 這是有效的模式 SaaS 開發人員簡化管理。

![清單對應][1]

多租用戶模型將多個租用戶指派給單一資料庫 （和分散多個資料庫的租用戶的群組）。 當您預期有小型資料需要每個租用戶時，請使用此模型。 在此模式中，我們將租用戶範圍指派使用**範圍對應**的資料庫。 
 

![對應的範圍][2]

或者您可以執行多租用戶資料庫模型使用*清單對應*到單一資料庫指定多個租用戶。 例如，DB1 用來儲存為 1 和 5 的租用戶識別碼的相關資訊，並 DB2 儲存資料的租用戶 7 及租用戶 10。 

![在單一 DB Muliple 租用戶][3] 

**根據您的選擇，請選擇其中一個選項︰**

### <a name="option-1-create-a-shard-map-for-a-list-mapping"></a>選項 1︰ 建立清單對應晶怪地圖
建立使用 ShardMapManager 物件晶怪地圖。 

    # $ShardMapManager is the shard map manager object. 
    $ShardMap = New-ListShardMap -KeyType $([int]) 
    -ListShardMapName 'ListShardMap' 
    -ShardMapManager $ShardMapManager 
 
 
### <a name="option-2-create-a-shard-map-for-a-range-mapping"></a>選項 2︰ 建立晶怪對應的範圍對應

請注意，若要使用此對應圖樣、 租用戶識別碼值必須連續的範圍，則可建立資料庫時，只要略過範圍時，可以在範圍的間距。

    # $ShardMapManager is the shard map manager object 
    # 'RangeShardMap' is the unique identifier for the range shard map.  
    $ShardMap = New-RangeShardMap 
    -KeyType $([int]) 
    -RangeShardMapName 'RangeShardMap' 
    -ShardMapManager $ShardMapManager 

### <a name="option-3-list-mappings-on-a-single-database"></a>在單一資料庫的選項 [3︰ 清單對應
設定此模式也需要建立清單對應的步驟 2，選項 1 所示。

## <a name="step-3-prepare-individual-shards"></a>步驟 3︰ 準備個別擊碎

將晶怪地圖管理員中的每個晶怪 （資料庫）。 這會準備個別資料庫儲存對應的資訊。 在每個晶怪執行這個方法。
     
    Add-Shard 
    -ShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>'
    # The $ShardMap is the shard map created in step 2.
 

## <a name="step-4-add-mappings"></a>步驟 4︰ 新增對應

對應的取決於您所建立的晶怪對應的類型。 如果您建立清單地圖時，您可以新增清單對應。 如果您建立範圍地圖時，您可以新增範圍對應。

### <a name="option-1-map-the-data-for-a-list-mapping"></a>選項 1︰ 對應清單對應的資料

每個租用戶加入清單對應對應的資料。  

    # Create the mappings and associate it with the new shards 
    Add-ListMapping 
    -KeyType $([int]) 
    -ListPoint '<tenant_id>' 
    -ListShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>' 

### <a name="option-2-map-the-data-for-a-range-mapping"></a>選項 2︰ 對應範圍對應的資料

新增所有租用戶識別碼範圍 – 資料庫關聯的範圍對應︰

    # Create the mappings and associate it with the new shards 
    Add-RangeMapping 
    -KeyType $([int]) 
    -RangeHigh '5' 
    -RangeLow '1' 
    -RangeShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>' 


### <a name="step-4-option-3-map-the-data-for-multiple-tenants-on-a-single-database"></a>步驟 4 選項 [3︰ 多個租用戶單一資料庫上的對應資料

每個租用戶中，執行新增 ListMapping （選項 1，上方）。 


## <a name="checking-the-mappings"></a>檢查對應

可使用下列命令來查詢現有擊碎及與其相關聯的對應的相關資訊︰  

    # List the shards and mappings 
    Get-Shards -ShardMap $ShardMap 
    Get-Mappings -ShardMap $ShardMap 

## <a name="summary"></a>摘要

當您完成設定之後時，就可以開始使用的彈性的資料庫用戶端文件庫。 您也可以使用[資料相依路由](sql-database-elastic-scale-data-dependent-routing.md)] 與 [[多晶怪查詢](sql-database-elastic-scale-multishard-querying.md)。

## <a name="next-steps"></a>後續步驟


從[Azure SQL 資料庫彈性資料庫工具 sripts](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db)取得 PowerShell 指令碼。

工具也是 GitHub: [Azure/彈性-db-工具](https://github.com/Azure/elastic-db-tools)。

若要移動單一租用戶模型資料或從多重租用戶模型使用分割合併工具。 請參閱[分割合併的工具](sql-database-elastic-scale-get-started.md)。

## <a name="additional-resources"></a>其他資源

多租用戶軟體為-的-服務 (SaaS) 資料庫應用程式的一般資料結構模式的詳細資訊，請參閱[Azure SQL 資料庫的多租用戶 SaaS 應用程式的設計模式](sql-database-design-patterns-multi-tenancy-saas-applications.md)。

## <a name="questions-and-feature-requests"></a>問題及功能要求

如問題，請連絡我們在[SQL 資料庫論壇](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)上和功能要求，請將它們新增至[SQL 資料庫意見反應](https://feedback.azure.com/forums/217321-sql-database/)。

<!--Image references-->
[1]: ./media/sql-database-elastic-convert-to-use-elastic-tools/listmapping.png
[2]: ./media/sql-database-elastic-convert-to-use-elastic-tools/rangemapping.png
[3]: ./media/sql-database-elastic-convert-to-use-elastic-tools/multipleonsingledb.png
 
