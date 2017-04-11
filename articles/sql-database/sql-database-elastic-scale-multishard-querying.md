<properties 
    pageTitle="查詢多晶怪 |Microsoft Azure" 
    description="查詢多個執行擊碎使用的彈性的資料庫用戶端文件庫。" 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="torsteng" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/12/2016" 
    ms.author="torsteng"/>

# <a name="multi-shard-querying"></a>多晶怪查詢

## <a name="overview"></a>概觀

使用[彈性的資料庫工具](sql-database-elastic-scale-introduction.md)] 中，您可以建立 sharded 資料庫解決方案。 **查詢多晶怪**用於工作，例如資料集合/報告需要透過數種擊碎執行延伸的查詢。 （這[資料而異路由](sql-database-elastic-scale-data-dependent-routing.md)，便會執行所有工作，在單一晶怪。） 

## <a name="overview"></a>概觀

1. 取得[**RangeShardMap**](https://msdn.microsoft.com/library/azure/dn807318.aspx)或[**ListShardMap**](https://msdn.microsoft.com/library/azure/dn807370.aspx)使用[**TryGetRangeShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap.aspx)、 [**TryGetListShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap.aspx)或[**GetShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap.aspx)方法。 請參閱[**建構 ShardMapManager**](sql-database-elastic-scale-shard-map-management.md#constructing-a-shardmapmanager) ，[**會收到 RangeShardMap 或 ListShardMap**](sql-database-elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap)。
2. 建立**[MultiShardConnection](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection.aspx)**物件。
2. 建立**[MultiShardCommand](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.aspx)**。 
3. 設定**[CommandText 屬性](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.commandtext.aspx#P:Microsoft.Azure.SqlDatabase.ElasticScale.Query.MultiShardCommand.CommandText)**T SQL 命令。
3. 呼叫**[ExecuteReader 方法](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.executereader.aspx)**來執行命令。
4. 檢視使用**[MultiShardDataReader 類別](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader.aspx)**的結果。 

## <a name="example"></a>範例

下列程式碼說明多晶怪查詢時使用名為*myShardMap*指定的**ShardMap**使用方式。 

    using (MultiShardConnection conn = new MultiShardConnection( 
                                        myShardMap.GetShards(), 
                                        myShardConnectionString) 
          ) 
    { 
    using (MultiShardCommand cmd = conn.CreateCommand())
           { 
            cmd.CommandText = "SELECT c1, c2, c3 FROM ShardedTable"; 
            cmd.CommandType = CommandType.Text; 
            cmd.ExecutionOptions = MultiShardExecutionOptions.IncludeShardNameColumn; 
            cmd.ExecutionPolicy = MultiShardExecutionPolicy.PartialResults; 

            using (MultiShardDataReader sdr = cmd.ExecuteReader()) 
                { 
                    while (sdr.Read())
                        { 
                            var c1Field = sdr.GetString(0); 
                            var c2Field = sdr.GetFieldValue<int>(1); 
                            var c3Field = sdr.GetFieldValue<Int64>(2);
                        } 
                } 
           } 
    } 

 
主要差別建築的多晶怪連線。 **典型**的運作方式的單一資料庫，其中**MultiShardConnection**會採用***擊碎集合***做為輸入。 填入擊碎從晶怪對應的集合。 使用**UNION ALL**語意集結單一整體結果擊碎的集合上執行查詢。 您也可以使用**ExecutionOptions**屬性] 命令的輸出可以新增該列是來自晶怪的名稱。 

請注意**myShardMap.GetShards()**通話。 這個方法所有擊碎擷取晶怪地圖，並提供容易地在所有相關的資料庫執行查詢。 傳回的集合擊碎的多晶怪查詢可以進行修飾進一步集合執行 LINQ 查詢從**myShardMap.GetShards()**通話。 部分結果原則搭配，多晶怪查詢中的目前功能不適用於最下數百個擊碎百設計。

查詢多晶怪限制是目前擊碎及查詢的 shardlets 驗證缺乏。 時資料而異路由驗證指定晶怪晶怪對應一部分查詢時，則多晶怪查詢將不會執行這項檢查。 這可能會導致多晶怪已從晶怪地圖的資料庫上執行的查詢。

## <a name="multi-shard-queries-and-split-merge-operations"></a>多晶怪查詢及分割合併作業

多晶怪查詢不會驗證查詢資料庫 shardlets 是否正在參與進行中的分割合併作業。 （請參閱[縮放比例使用彈性的資料庫分割合併工具](sql-database-elastic-scale-overview-split-and-merge.md)）。這可能會導致不一致的相同 shardlet 資料列，顯示相同的多晶怪查詢中的多個資料庫的位置。 請注意以下事項，這些限制及執行多晶怪查詢時，請考慮清空進行中的分割合併作業和變更晶怪地圖。

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

## <a name="see-also"></a>另請參閱
**[System.Data.SqlClient](http://msdn.microsoft.com/library/System.Data.SqlClient.aspx)**類別和方法。


管理擊碎使用[彈性的資料庫用戶端文件庫](sql-database-elastic-database-client-library.md)。 包含稱為[Microsoft.Azure.SqlDatabase.ElasticScale.Query](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.aspx)可讓您要查詢以單一查詢和結果的多個擊碎命名空間。 它會提供查詢的抽象擊碎集合上。 同時也會提供替代執行原則，特別是部分的結果，透過許多擊碎查詢時，處理失敗。  

 