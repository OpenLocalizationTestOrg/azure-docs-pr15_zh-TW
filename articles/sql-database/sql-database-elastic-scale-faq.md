<properties 
    pageTitle="Azure SQL 彈性小數位數的常見問題集 |Microsoft Azure" 
    description="常見問題集 Azure SQL 資料庫彈性的小數位數。" 
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
    ms.date="05/03/2016" 
    ms.author="ddove"/>

# <a name="elastic-database-tools-faq"></a>彈性的資料庫工具常見問題集 

#### <a name="if-i-have-a-single-tenant-per-shard-and-no-sharding-key-how-do-i-populate-the-sharding-key-for-the-schema-info"></a>如果我有單一租用戶每個晶怪和沒有 sharding 索引鍵時，我要如何填入 sharding 機碼的結構描述資訊？

結構描述資訊物件只會用來分割合併案例。 如果應用程式本身單一租用戶，然後就不需要分割合併的工具，因此就不需要填入的結構描述資訊物件。

#### <a name="ive-provisioned-a-database-and-i-already-have-a-shard-map-manager-how-do-i-register-this-new-database-as-a-shard"></a>我已資料庫佈建後，我已經有晶怪地圖管理員，如何為晶怪註冊此新資料庫？

請參閱**[新增晶怪應用程式使用的彈性的資料庫用戶端文件庫](sql-database-elastic-scale-add-a-shard.md)**。 

#### <a name="how-much-do-elastic-database-tools-cost"></a>彈性的資料庫工具多少費用？

使用的彈性的資料庫用戶端文件庫，不會造成任何成本。 成本累算僅適用於您用於擊碎和晶怪地圖管理員 Azure SQL 資料庫，以及您佈建分割合併工具的 web/工作者角色。

#### <a name="why-are-my-credentials-not-working-when-i-add-a-shard-from-a-different-server"></a>為什麼我的認證無法運作時新增晶怪從不同的伺服器？
不要使用認證形式的 「 使用者ID=username@servername”,改為使用 「 使用者識別碼 = 使用者名稱 」。  此外，請務必 「 使用者名稱 」 登入擁有晶怪的權限。

#### <a name="do-i-need-to-create-a-shard-map-manager-and-populate-shards-every-time-i-start-my-applications"></a>我需要建立晶怪地圖管理員，並填入擊碎，每次啟動我的應用程式？

否-建立晶怪地圖管理員 (例如， **[ShardMapManagerFactory.CreateSqlShardMapManager](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager.aspx)**) 是一次的作業。  您的應用程式應該使用通話**[ShardMapManagerFactory.TryGetSqlShardMapManager()](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx)**在應用程式啟動時間。  那里應該每個應用程式網域只有一個這類來電。

#### <a name="i-have-questions-about-using-elastic-database-tools-how-do-i-get-them-answered"></a>如有疑問使用彈性的資料庫工具，如何移除這些答案嗎？ 

請連絡我們[Azure SQL 資料庫論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)上。

#### <a name="when-i-get-a-database-connection-using-a-sharding-key-i-can-still-query-data-for-other-sharding-keys-on-the-same-shard--is-this-by-design"></a>取得使用 sharding 索引鍵的資料庫連線，當我仍然可以在同一個晶怪其他 sharding 金鑰查詢資料。  這是原本的設計所嗎？

彈性的縮放比例 Api 讓您連線到正確的資料庫 sharding 索引鍵，但不是提供 sharding 索引鍵篩選。  如有必要，請至您的查詢，來限制範圍至提供的 sharding 機碼，新增**位置**子句。

#### <a name="can-i-use-a-different-azure-database-edition-for-each-shard-in-my-shard-set"></a>我可以使用不同的 Azure 資料庫 edition 每個晶怪我晶怪集中嗎？

是的晶怪是個別的資料庫，且因此一個晶怪可能進階版時另一個是標準的版本。 此外，晶怪版本可以調整向上或向下多次晶怪的存留期間。

#### <a name="does-the-split-merge-tool-provision-or-delete-a-database-during-a-split-or-merge-operation"></a>分割合併工具佈建 （或刪除） 分割或合併作業期間資料庫？ 

[否]。 **分割**作業，目標資料庫必須已經有適當的結構描述和註冊晶怪地圖管理員]。  **合併**作業，您必須晶怪刪除晶怪地圖管理員，並刪除資料庫。

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 