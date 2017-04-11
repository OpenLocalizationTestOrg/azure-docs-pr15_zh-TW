<properties 
    pageTitle="新增使用彈性的資料庫工具晶怪 |Microsoft Azure" 
    description="如何將新擊碎晶怪使用彈性的縮放比例 Api 設定。" 
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
    ms.date="05/27/2016" 
    ms.author="ddove"/>

# <a name="adding-a-shard-using-elastic-database-tools"></a>新增晶怪使用彈性的資料庫工具

## <a name="to-add-a-shard-for-a-new-range-or-key"></a>若要新增新的範圍或鍵晶怪  

應用程式通常需要直接新增新擊碎處理預期從新的金鑰或晶怪對應已存在的索引鍵範圍的資料。 例如，sharded 應用程式的租用戶識別碼可能需要新的租用戶，提供新晶怪，或資料 sharded 月可能需要新的晶怪，佈建後，每個新的月份開始之前。 

如果新關鍵值的範圍尚未現有對應的一部分，則非常簡單，新增新晶怪，並建立關聯的新的金鑰或該晶怪的範圍。 

### <a name="example--adding-a-shard-and-its-range-to-an-existing-shard-map"></a>範例︰ 新增至現有晶怪對應的晶怪和範圍
此範例使用[TryGetShard](https://msdn.microsoft.com/library/azure/dn823929.aspx) [CreateShard](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard.aspx)， [CreateRangeMapping](https://msdn.microsoft.com/library/azure/dn807221.aspx#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.RangeShardMap`1.CreateRangeMapping(Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.RangeMappingCreationInfo{`0}))方法，並建立[ShardLocation](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardlocation.shardlocation.aspx#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardLocation.)類別的執行個體。 在下面的範例中，名為**sample_shard_2**和其所有必要的結構描述物件的資料庫已建立保留範圍 [300，400）。  

    // sm is a RangeShardMap object.
    // Add a new shard to hold the range being added. 
    Shard shard2 = null; 

    if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2)) 
    { 
        shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
    } 

    // Create the mapping and associate it with the new shard 
    sm.CreateRangeMapping(new RangeMappingCreationInfo<long> 
                            (new Range<long>(300, 400), shard2, MappingStatus.Online)); 


或者，您可以使用 Powershell 來建立新的晶怪地圖管理員。 例如，使用[以下](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db)。
## <a name="to-add-a-shard-for-an-empty-part-of-an-existing-range"></a>若要新增現有的範圍中空白的部分晶怪  

在某些情況下，您可能會有已對應至晶怪的範圍，而且部分填入資料，但現在想要會導向至不同的晶怪即將來臨的資料。 例如，您晶怪依日期範圍及必須已配置 50 天晶怪，但您想要在日期 24，未來的資料，以開啟在不同的晶怪。 彈性的資料庫[分割合併工具](sql-database-elastic-scale-overview-split-and-merge.md)可執行這項作業，但資料移動並不需要 （例如，[25，50 天的範圍的資料），亦即，如果 day 25 為 50 獨佔式，內含尚不存在） 您可以執行此完全直接使用晶怪地圖管理 Api。

### <a name="example-splitting-a-range-and-assigning-the-empty-portion-to-a-newly-added-shard"></a>範例︰ 分割範圍，並將空白部分指派給新加入的晶怪

已建立名為 「 sample_shard_2 」 和其所有必要的結構描述物件的資料庫。  

 
    // sm is a RangeShardMap object.
    // Add a new shard to hold the range we will move 
    Shard shard2 = null; 

    if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2)) 
    { 
    
        shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
    } 

    // Split the Range holding Key 25 

    sm.SplitMapping(sm.GetMappingForKey(25), 25); 

    // Map new range holding [25-50) to different shard: 
    // first take existing mapping offline 
    sm.MarkMappingOffline(sm.GetMappingForKey(25)); 
    // now map while offline to a different shard and take online 
    RangeMappingUpdate upd = new RangeMappingUpdate(); 
    upd.Shard = shard2; 
    sm.MarkMappingOnline(sm.UpdateMapping(sm.GetMappingForKey(25), upd)); 

**重要事項**︰ 如果您是特定的範圍是空的更新的對應僅使用此技巧。  上述方法不檢查移動、 範圍的資料，最好檢查納入您的程式碼。  如果資料列中移動的範圍，實際資料分配不相符更新晶怪地圖。 使用[分割合併工具](sql-database-elastic-scale-overview-split-and-merge.md)在這些情況下，請改為執行的作業。  


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 
