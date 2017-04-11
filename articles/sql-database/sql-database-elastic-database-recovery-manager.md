<properties 
    pageTitle="使用復原管理員以修正晶怪地圖的問題 |Microsoft Azure" 
    description="使用 RecoveryManager 類別來解決問題晶怪地圖" 
    services="sql-database" 
    documentationCenter=""  
    manager="jhubbard"
    authors="ddove"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/05/2016" 
    ms.author="ddove"/>

# <a name="using-the-recoverymanager-class-to-fix-shard-map-problems"></a>若要修正晶怪地圖使用 RecoveryManager 類別

[RecoveryManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.aspx)課程提供 ADO.Net 應用程式輕鬆偵測並校正之間全域晶怪地圖 (GSM) 與本機晶怪地圖 (LSM) sharded 資料庫環境中的任何不一致的能力。 

GSM 和 LSM 追蹤 sharded 環境中的每個資料庫的對應。 有時候，中斷發生 GSM 和 LSM 之間。 在此情況下，使用偵測與修復符號 RecoveryManager 類別。

RecoveryManager 類別是[彈性的資料庫用戶端文件庫](sql-database-elastic-database-client-library.md)的一部分。 


![晶怪地圖][1]


字詞定義，請參閱[彈性的資料庫工具詞彙](sql-database-elastic-scale-glossary.md)。 若要瞭解如何使用**ShardMapManager**管理 sharded 解決方案中的資料，請參閱[晶怪對應管理](sql-database-elastic-scale-shard-map-management.md)。


## <a name="why-use-the-recovery-manager"></a>為什麼要使用復原管理員？

在 sharded 資料庫環境中，有一個租用戶，每個資料庫及每個伺服器的許多資料庫。 也可以有許多伺服器的環境中。 每個資料庫對應晶怪地圖，因此來電可以傳送到正確的伺服器和資料庫。 資料庫追蹤**sharding 金鑰**，請根據並為每個晶怪指派**關鍵值的範圍**。 例如，sharding 鍵可能表示的客戶名稱從"D"到 「 f 鍵 」。 **全域晶怪地圖 (GSM)**中包含的所有擊碎 （又稱資料庫） 和其對應範圍對應。 每個資料庫也包含地圖晶怪中所包含的範圍，這稱為**本機晶怪對應 (LSM)**。 當應用程式連線到晶怪時，快速擷取的應用程式快取對應。 LSM 會用於驗證快取的資料。 

GSM 和 LSM 可能會變成同步的原因如下︰

1. 其範圍確認不再使用，或重新命名的晶怪晶怪刪除。 刪除**孤立的晶怪對應**晶怪導致。 Similary，重新命名的資料庫可能會導致孤立晶怪對應。 變更的目的，根據晶怪可能需要移除或晶怪位置需要更新。 若要復原已刪除的資料庫，請參閱[還原至先前的時間點資料庫，還原已刪除的資料庫，或從資料中心中斷復原](sql-database-troubleshoot-backup-and-restore.md)。
2. 地理容錯移轉事件發生。 若要繼續，其中一個必須更新的伺服器名稱及晶怪地圖管理員應用程式中的資料庫名稱，然後更新晶怪對應中的任何及所有擊碎晶怪對應詳細資料。 若地理容錯移轉，例如復原邏輯應該自動容錯移轉工作流程中。 自動化復原動作，啟用地理啟用資料庫的 frictionless 管理能力，可避免手動人力的動作。
3. 晶怪或 ShardMapManager 資料庫會還原至先前點中的時間。

如需有關 Azure SQL 資料庫彈性資料庫工具]，[地理複寫與還原，請參閱下列動作︰ 

* [概觀︰ 雲端與 SQL 資料庫的業務連續性和資料庫損毀修復](sql-database-business-continuity.md) 
* [開始使用彈性的資料庫工具](sql-database-elastic-scale-get-started.md)  
* [ShardMap 管理](sql-database-elastic-scale-shard-map-management.md)

## <a name="retrieving-recoverymanager-from-a-shardmapmanager"></a>從 ShardMapManager 擷取 RecoveryManager 

第一個步驟是，建立 RecoveryManager 執行個體。 [GetRecoveryManager 方法](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getrecoverymanager.aspx)會傳回目前[ShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx)執行個體復原管理員。 地址晶怪地圖任何不一致，您必須先擷取特定晶怪對應 RecoveryManager。 

    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnnectionString,  
             ShardMapManagerLoadPolicy.Lazy);
             RecoveryManager rm = smm.GetRecoveryManager(); 

在此範例中，從 ShardMapManager 初始化 RecoveryManager。 包含 ShardMap ShardMapManager 也已初始化。 

由於此應用程式碼操作晶怪地圖本身，工廠方法 （在上述範例中，smmConnectionString） 中所用的認證應參考的連接字串 GSM 資料庫有讀取寫入權限的認證。 這些認證是通常是用來開啟 [連線的資料而異路由認證不同。 如需詳細資訊，請參閱[在彈性的資料庫用戶端中使用認證](sql-database-elastic-scale-manage-credentials.md)。

## <a name="removing-a-shard-from-the-shardmap-after-a-shard-is-deleted"></a>刪除晶怪之後晶怪移除 ShardMap

[DetachShard 方法](https://msdn.microsoft.com/library/azure/dn842083.aspx)從晶怪對應指定晶怪會中斷連結，並刪除以對應晶怪相關聯。  

* 位置參數是晶怪位置，特別是伺服器名稱和資料庫名稱，要中斷連結晶怪。 
* ShardMapName 參數是晶怪地圖名稱。 這只是由相同的晶怪地圖管理員管理多個晶怪地圖時所需。 選用。 

**重要事項**︰ 如果您是特定的範圍是空的更新的對應僅使用此技巧。 上述方法不檢查移動、 範圍的資料，最好檢查納入您的程式碼。

此範例會將擊碎移除晶怪地圖。 

    rm.DetachShard(s.Location, customerMap); 

地圖晶怪刪除之前 GSM 晶怪位置。 因為晶怪已遭刪除，則假設這是有意，且 sharding 金鑰範圍不再使用。 如果不符合上述情況，您可以執行還原點的時間。 若要復原晶怪從舊的時間點。 （在此情況下，檢閱下方偵測晶怪不一致的一節）。若要復原，請參閱[還原至先前的時間點資料庫，還原已刪除的資料庫，或從資料中心中斷復原](sql-database-troubleshoot-backup-and-restore.md)。

由於假定它與資料庫刪除所預期，最後的系統管理清除動作是刪除要晶怪晶怪地圖管理員中的項目。 如此可避免從不慎將資訊寫入範圍未預期的應用程式。

## <a name="to-detect-mapping-differences"></a>若要偵測對應的差異 

[DetectMappingDifferences 方法](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.detectmappingdifferences.aspx)選取其中一個晶怪地圖 （本機或全域） 傳回真實的來源，並協調 （GSM 和 LSM） 兩個晶怪地圖上的對應。

    rm.DetectMappingDifferences(location, shardMapName);

* *位置*指定伺服器名稱和資料庫名稱。 
* *ShardMapName*參數是晶怪地圖名稱。 這只是需要相同的晶怪地圖管理員所管理多個晶怪地圖。 選用。 

## <a name="to-resolve-mapping-differences"></a>若要解決對應的差異

[ResolveMappingDifferences 方法](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.resolvemappingdifferences.aspx)選取其中一個晶怪地圖 （本機或全域） 做為真實的來源，並協調 （GSM 和 LSM） 兩個晶怪地圖上的對應。

    ResolveMappingDifferences (RecoveryToken, MappingDifferenceResolution.KeepShardMapping);
   
* *RecoveryToken*參數列舉對應的特定晶怪 LSM GSM 之間的差異。 

* [MappingDifferenceResolution 列舉](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.mappingdifferenceresolution.aspx)用來指示解決晶怪對應之間的差異的方式。 
* **MappingDifferenceResolution.KeepShardMapping**建議的 LSM 包含精確對應，因此應該使用晶怪中的對應。 這通常是發生容錯移轉︰ 晶怪現在存放在新的伺服器上。 由於 （使用 RecoveryManager.DetachShard 方法） GSM 必須先移除晶怪，對應不存在於 GSM 上。 因此，必須使用 LSM 晶怪對應，重新建立。

## <a name="attach-a-shard-to-the-shardmap-after-a-shard-is-restored"></a>附加 ShardMap 晶怪之後還原晶怪 

[AttachShard 方法](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.attachshard.aspx)將指定晶怪附加至晶怪地圖。 然後，它會偵測到晶怪對應的不一致性並更新以符合在晶怪還原晶怪對應。 假定它與資料庫以反映原始資料庫名稱 （如果您之前時晶怪還原），也會重新命名自點中時間還原預設值，以新的資料庫，加上時間戳記。 

    rm.AttachShard(location, shardMapName) 

* *位置*參數是伺服器名稱和資料庫名稱，附加晶怪。 

* *ShardMapName*參數是晶怪地圖名稱。 這只是由相同的晶怪地圖管理員管理多個晶怪地圖時所需。 選用。 

此範例會將晶怪加入晶怪地圖的已從舊版點中時間最近還原。 因為已還原晶怪 （也就是在 LSM 晶怪的對應），就會與晶怪中的項目 GSM 不一致的潛在。 這個範例程式碼，外部晶怪已還原，並重新命名為原始資料庫的名稱。 因為它還原，則會假設 LSM 中的對應為受信任的對應。 

    rm.AttachShard(s.Location, customerMap); 
    var gs = rm.DetectMappingDifferences(s.Location); 
      foreach (RecoveryToken g in gs) 
       { 
       rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping); 
       } 

## <a name="updating-shard-locations-after-a-geo-failover-restore-of-the-shards"></a>更新晶怪後地理-移轉後 （還原） 的位置擊碎

係地理容錯移轉，次要資料庫會可寫入存取，且會成為新的主要資料庫。 名稱伺服器，以及可能 （根據您的設定），資料庫可能與原始的主要不同。 因此您必須固定晶怪 GSM 和 LSM 中的對應項目。 同樣地，如果資料庫會還原至不同的名稱或位置，或之前的時間，這可能會導致不一致晶怪地圖。 晶怪地圖管理員處理的開啟連接到正確的資料庫。 通訊群組根據晶怪地圖與目標應用程式的要求的 sharding 機碼中的資料。 後地理移轉後，您必須以正確的伺服器名稱、 資料庫名稱和晶怪對應復原資料庫的更新這項資訊。 

## <a name="best-practices"></a>最佳作法

地理錯誤移轉及復原是通常是雲端由管理員來管理刻意利用 Azure SQL 資料庫 business 連續性功能的應用程式的作業。 業務連續性規劃需要處理程序與程序，以確保營運可以繼續不受干擾的量值。 可用的方法應此工作流程中使用 RecoveryManager 課程的一部分，以確保 GSM 和 LSM 會保留最新版本根據復原動作。 有正確確保 GSM 和 LSM 反映正確的資訊後容錯移轉事件的 5 個基本步驟。 應用程式碼，執行下列步驟可整合至現有的工具和工作流程。 

1. 從 ShardMapManager 擷取 RecoveryManager。 
2. 卸離晶怪地圖從舊晶怪。
3. 將新的晶怪附加晶怪地圖，包括晶怪的新位置。
4. 偵測 GSM 和 LSM 之間的對應的不一致性。 
5. 解決 GSM 和 LSM，信任 LSM 之間的差異。 

此範例會執行下列步驟︰
1. 擊碎移除晶怪地圖反映晶怪容錯移轉事件前的位置。
2. 將擊碎附加至反映新的晶怪位置 （「 Configuration.SecondaryServer 」 的參數是新的伺服器名稱，但相同的資料庫名稱） 的晶怪地圖。
3. 擷取復原權杖偵測 GSM 和的每個晶怪 LSM 的對應差異。 
4. 信任的每個晶怪 LSM 從的對應，以解析不一致的情形。 

    var 擊碎 = smm。GetShards(); foreach （晶怪擊碎功能） {如果 (s.Location.Server = = Configuration.PrimaryServer) {ShardLocation slNew = 新 ShardLocation Configuration.SecondaryServer (s.Location.Database）; 
        
          rm.DetachShard(s.Location); 
        
          rm.AttachShard(slNew); 
        
          var gs = rm.DetectMappingDifferences(slNew); 
    
          foreach (RecoveryToken g in gs) 
            { 
               rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping); 
            } 
        } 
    } 



[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Image references-->
[1]: ./media/sql-database-elastic-database-recovery-manager/recovery-manager.png
 
