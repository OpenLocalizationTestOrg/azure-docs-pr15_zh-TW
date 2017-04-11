<properties 
    pageTitle="縮放出雲端資料庫之間移動資料 |Microsoft Azure" 
    description="說明如何操作擊碎和移動透過使用彈性資料庫 Api 的自我代管服務的資料。" 
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
    ms.date="05/27/2016" 
    ms.author="ddove" />

# <a name="moving-data-between-scaled-out-cloud-databases"></a>橫向擴充的雲端資料庫之間移動資料

如果您是軟體與服務開發人員]，而您的應用程式突然期經歷您節省大量的需求，您需要以容納成長。 因此，您可以新增更多的資料庫 （擊碎）。 如何您轉散發資料庫的資料而不干擾資料完整性？ 您可以使用 [**分割合併工具**來將資料從受限制的資料庫移至新的資料庫。  

分割合併工具會以 Azure web 服務。 系統管理員或開發人員使用的工具來移動 shardlets （資料從晶怪） 之間不同的資料庫 （擊碎）。 此工具使用晶怪地圖管理維護服務中繼資料的資料庫，並確保一致的對應。

![概觀][1]

## <a name="download"></a>下載
[Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/)


## <a name="documentation"></a>文件
1. [彈性的資料庫分割合併工具教學課程](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
* [分割合併的安全性設定](sql-database-elastic-scale-split-merge-security-configuration.md)
* [分割合併的安全性考量](sql-database-elastic-scale-split-merge-security-configuration.md)
* [晶怪地圖管理](sql-database-elastic-scale-shard-map-management.md)
* [移轉現有的資料庫來擴充](sql-database-elastic-convert-to-use-elastic-tools.md)
* [彈性的資料庫工具](sql-database-elastic-scale-introduction.md)
* [彈性的資料庫工具字彙](sql-database-elastic-scale-glossary.md)

## <a name="why-use-the-split-merge-tool"></a>為什麼要使用分割合併工具？

**彈性**

應用程式需要伸展彈性限制，超出單一 Azure SQL 資料庫資料庫的限制。 使用工具來移動資料，視需要以新的資料庫，同時保留完整性。

**若要放大的分割** 

您需要增加擴充爆炸整體的容量。 若要這麼做，請 sharding 資料，並將它分散到從屬參照多個資料庫，直到符合容量需求建立額外的容量。 這是 「 分割 」 功能的主要範例。 

**若要壓縮的合併列印**

容量需要縮小因為商務季節性性質。 工具可讓您時商務速度變慢，向下調整較少的小數位數單位。 彈性的小數位數分割合併服務的 「 合併 」 功能說明這項需求。 

**藉由移動 shardlets 管理作用區**

使用多個每個資料庫的租用戶，以擊碎 shardlets 配置可能會導致容量瓶頸在某些擊碎。 這需要重新配置 shardlets，或將忙碌 shardlets 移至新的或較少利用擊碎。 

## <a name="concepts--key-features"></a>概念與主要功能。

**客戶裝載的服務**

分割合併已傳送的客戶裝載的服務。 您必須部署，並裝載您的 Microsoft Azure 訂閱中的服務。 您從 NuGet 下載套件包含設定範本來完成您的特定部署中的資訊。 請參閱[分割合併教學課程](sql-database-elastic-scale-configure-deploy-split-and-merge.md)以取得詳細資料。 由於在 Azure 訂閱中，執行服務，您可以控制，並設定大部分的安全性層面的服務。 預設範本包含設定 SSL 憑證型的用戶端驗證、 儲存的認證、 DoS 保護及 IP 限制加密的選項。 您可以尋找下列文件[分割合併的安全性設定](sql-database-elastic-scale-split-merge-security-configuration.md)的安全性方面的詳細資訊。

預設部署一個工作者 web 角色與執行服務。 每個 Azure 雲端服務中使用 A1 虛擬記憶體大小。 時部署套件時，您無法修改這些設定，您可以執行的雲端服務，（透過 Azure 入口網站） 中的成功部署變更它們。 請注意技術的原因，工作者角色的必須未設定為一個以上的單一例項。 

**晶怪地圖整合**

分割合併服務互動晶怪地圖的應用程式。 使用分割合併服務分割或合併範圍或時擊碎之間移動 shardlets，服務會自動保留晶怪地圖最新狀態。 若要這麼做，連線到晶怪對應 manager 資料庫的應用程式及維護為分割或合併移動要求進度的範圍和對應的服務。 如此一來，可確保分割合併作業表示當的晶怪地圖永遠顯示最新版本的檢視。 分割，合併列印] 和 [shardlet 移動作業是由從來源晶怪將 shardlets 批次移至目標晶怪實作。 期間 shardlet 移動作業主旨，目前批次 shardlets 標示為 [離線晶怪對應中，無法用於使用**OpenConnectionForKey** API 的資料而異路由連線。 

**一致的 shardlet 連線**

移動資料啟動新的 shardlets 批次，任何晶怪地圖提供依賴資料的連線路由儲存 shardlet 晶怪就已清除及後續連線晶怪地圖 Api 從這些為了避免出現不一致的資料移動正在進行中時，shardlets 遭到封鎖。 連線至其他 shardlets 在同一個晶怪也會取得刪除，但會一次成功立即上重試]。 後移批次，shardlets 會標示為 [線上再次的目標晶怪，並從來源晶怪移除的來源資料。 服務會經歷適用於每個批次的步驟，直到所有 shardlets 已都移動。 這會導致多個連線刪除作業期間完成分割或合併移動作業的課程。  

**管理 shardlet 可用性**

限制刪除 shardlets 如上文所目前批次的連線會限制無法使用的範圍 shardlets 一批次。 這是慣用透過一種方法其中完成晶怪想要保持離線的所有其 shardlets 在分割或合併作業的課程。 批次中定義的不同 shardlets 一次移動數為大小是設定參數。 它可以定義每個分割及合併作業根據應用程式的可用性和效能的需求。 請注意，遭到鎖定晶怪對應中的範圍可能大於指定批次的大小。 這是因為服務，例如 sharding 資料中的關鍵值的實際次數大約符合批次大小，請挑選範圍大小。 這是一定要記住特別沒有嚴密填入的 sharding 索引鍵。 

**中繼資料存放區**

分割合併服務使用的資料庫，若要維持其狀態，並保留處理要求期間的記錄。 使用者在訂閱中建立此資料庫，並提供其服務部署的設定檔中的連接字串。 從使用者的組織的系統管理員，也可以連線到此資料庫檢閱要求進度並調查有關可能失敗的詳細的資訊。

**Sharding 線上狀態**

分割合併服務 （1) sharded 資料表、 （2） 參照資料表 （3） 標準的資料表之間的差異。 分割或合併移動作業的語意用之資料表的類型而定，並定義如下︰ 

* **Sharded 資料表**︰ 分割、 合併及移動作業將 shardlets 來源到目標晶怪。 整體要求順利完成之後, 這些 shardlets 已不存在的來源。 請注意目標資料表需要存在於目標晶怪，而且不能包含之前處理作業的範圍內的資料。 

* **參照資料表**︰ 的參照資料表分割，合併及移動作業來源的資料複製到目標晶怪。 但是請注意，是否目標此表格中的任一列已在給定的資料表目標晶怪會發生任何變更。 資料表必須是空的任何參照表格複製作業的處理完畢。

* **其他資料表**︰ 其他資料表可以出現在 [來源] 或 [分割及合併作業的目標。 分割合併服務會忽略這些表格的任何資料移動或複製作業。 但是請注意，它們會干擾限制的情況下這些作業。

在與 sharded 表格參照的資訊是由**結構描述資訊**Api 所提供，晶怪地圖上。 下列範例說明如何使用這些 api 指定晶怪對應管理員物件 smm 上︰ 

    // Create the schema annotations 
    SchemaInfo schemaInfo = new SchemaInfo(); 

    // Reference tables 
    schemaInfo.Add(new ReferenceTableInfo("dbo", "region")); 
    schemaInfo.Add(new ReferenceTableInfo("dbo", "nation")); 

    // Sharded tables 
    schemaInfo.Add(new ShardedTableInfo("dbo", "customer", "C_CUSTKEY")); 
    schemaInfo.Add(new ShardedTableInfo("dbo", "orders", "O_CUSTKEY")); 

    // Publish 
    smm.GetSchemaInfoCollection().Add(Configuration.ShardMapName, schemaInfo); 

表格 「 地區 」 及 「 國家 」 會定義為參照資料表，而且會複製分割或合併移動作業。 「 客戶 」 和 「 訂單 」 會定義為 sharded 的資料表。 C_CUSTKEY 和 O_CUSTKEY 作為 sharding 鍵。 

**參考完整性**

分割合併服務分析資料表之間的相依性，並使用外部索引鍵主索引鍵關聯性的階段移動參照資料表和 shardlets 的作業。 一般而言，參照資料表會複製第一次相依性的順序，然後 shardlets 複製它們中每個批次的相依性的順序。 這是必要的以便為新的資料到達時，目標晶怪 FK PK 條件約束會生效。 

**晶怪地圖一致性和最終完成**

失敗次數，有任何中斷後繼續作業及旨在完成任何進度邀請中的分割合併服務。 不過，可能會無法復原的情況下，例如，當目標晶怪的遺失或危害修復。 在這些情況下，可能會繼續應該要移動的一些 shardlets 位於來源晶怪。 服務可確保必要的資料成功複製到目標後，會只會更新 shardlet 對應。 所有的資料已複製到目標，並已成功更新對應的對應 Shardlets 只會刪除來源上。 [刪除] 作業會在幕後時範圍上已有線上目標晶怪。 分割合併服務一律可確保正確性的儲存晶怪對應中的對應。


## <a name="the-split-merge-user-interface"></a>分割合併的使用者介面

分割合併服務套件包含工作者角色及網頁角色。 網頁角色用來提交分割合併的要求以互動的方式。 使用者介面的主要元件如下所示︰

-    作業類型︰ 作業類型為控制項的這個要求服務所執行的作業類型選項] 按鈕。 您可以選擇分割、 合併及移動案例。 您也可以取消先前已提交的作業。 您可以使用分割、 合併及移動的範圍晶怪地圖服務要求。 清單晶怪地圖支援移動作業。

-    晶怪地圖︰ 要求參數的下一節涵蓋晶怪地圖和資料庫裝載晶怪地圖的資訊。 特別是您需要提供 Azure SQL 資料庫伺服器及裝載 shardmap，認證以連線至晶怪地圖資料庫及最後晶怪對應的名稱的資料庫的名稱。 目前，作業只接受一組憑證。 這些認證必須具備足夠的權限可執行擊碎使用者資料以及晶怪對應的變更。

-    來源範圍 （分割及合併）︰ 分割及合併作業程序使用高低索引鍵的範圍。 若要使用未繫結高索引鍵值指定一項作業，請核取 「 高索引鍵是最大 」] 核取方塊並高索引鍵欄位留白。 您指定的範圍值不需要確實符合對應和其晶怪地圖中的邊界。 如果您沒有在指定任何範圍界限服務會最接近的範圍為您自動推斷。 您可以使用 GetMappings.ps1 PowerShell 指令碼來擷取目前對應中指定晶怪地圖。

-    分割來源行為 （分割）︰ 分割作業定義要分割的來源範圍的點。 提供您要分割的 sharding 鍵執行此動作。 使用 [選項] 按鈕指定是否要 （不含分割鍵） 範圍的下半部移動，或是否要移動 （包括分割金鑰） 的上方。

-    來源 Shardlet （移動）︰ 移動作業不需要描述來源範圍是不同的分割或合併作業。 移動的來源只是由您計劃要移動的 sharding 索引鍵值識別。

-    目標晶怪 （分割）︰ 一旦您所提供的資訊上分割操作的來源，您必須定義您想要的資料複製到提供目標的 Azure SQL 資料庫伺服器和資料庫名稱。

-    目標範圍 （合併）︰ 合併作業將 shardlets 移至現有的晶怪。 您找出現有晶怪提供您想要使用合併列印的現有範圍邊界。

-    批次大小︰ 批次大小控制項會離線期間的資料移動，一次的 shardlets 的數。 這是時間的您是時間的區分長 shardlets 週期時，可以使用較小值的整數值。 較大的值會增加所指定的 shardlet 的時間離線，但可能會改善效能。

-    操作識別碼 （取消）︰ 如果您不再需要進行中的操作，您可以取消作業提供其作業識別碼，在此欄位中。 您可以從邀請狀態資料表擷取作業識別碼 （請參閱一節 8.1） 或從您送出要求的網頁瀏覽器中的輸出。


## <a name="requirements-and-limitations"></a>需求和限制 

分割合併服務的目前實作是受限於下列需求與限制︰ 

* 擊碎需要存在於，並在註冊晶怪對應中，才能執行這些擊碎分割合併作業。 

* 資料表或任何其他資料庫物件會自動為其作業的組件，並不會建立服務。 這表示所有 sharded 表格及參照表格的結構描述需要存在於目標晶怪之前所有分割或合併移動作業。 Sharded 表格特別必須是分割或合併移動作業所要加入新的 shardlets 在哪裡的範圍中空白。 否則，操作會失敗目標晶怪的初始一致性檢查。 另請注意，如果參照是空的資料表，而且不上其他並行並不一致性保證寫入參照表格的作業，僅複製資料的參照。 我們建議您這樣︰ 執行中時分割/合併作業，沒有其他寫入作業進行變更的參照資料表。

* 服務需要建立的唯一索引或包含以改善效能和可靠性，大型 shardlets sharding 索引鍵的索引鍵的資料列身分識別。 這個選項可讓移動資料在比只 sharding 索引鍵值更進一步資料粒度的服務。 如此有助於減少記錄檔空間和鎖定作業期間所需的最大量。 請考慮建立唯一索引或包括 sharding 鍵，在給定的資料表，如果您想要分割或合併移動要求使用該資料表的主索引鍵。 基於效能，sharding 金鑰應該前置的機碼或索引資料行。

* 要求處理期間，可能會出現在來源及目標晶怪一些 shardlet 資料。 這是為了防止失敗 shardlet 移動期間。 分割合併列印含晶怪對應的整合可確保瀏覽相依路由 Api 使用**OpenConnectionForKey**方法晶怪地圖上的資料的連線看不到任何不一致的中繼狀態。 不過，當不使用**OpenConnectionForKey**方法連線的來源或目標擊碎，不一致的中繼狀態可能會顯示時分割或合併移動要求將。 這些連線可能會顯示根據時間或基礎連線晶怪的部分] 或 [重複的結果。 這項限制目前包含所做的彈性的小數位數多-Shard-查詢的連線。

* 中繼資料資料庫分割合併服務必須共用之間不同的角色。 例如，分割合併服務在執行中執行的角色必須指向不同的中繼資料的資料庫比生產角色。
 

## <a name="billing"></a>帳單 

分割合併服務是以在您的 Microsoft Azure 訂閱雲端服務。 因此雲端服務的費用會套用至您的服務的執行個體。 除非您經常執行分割或合併移動作業，我們建議您刪除您分割合併雲端服務。 所儲存的執行成本，或部署雲端服務的執行個體。 您可以重新部署，並開始您隨時可執行的設定，每當您需要執行分割或合併作業。 
 
## <a name="monitoring"></a>監控 
### <a name="status-tables"></a>狀態表格 

分割合併服務會提供**RequestStatus**表格中的中繼資料儲存監視已完成與進行中要求的資料庫。 下表列出每個分割合併要求已提交到此執行個體分割合併服務的資料列。 讓每個要求的下列資訊︰

* **時間戳記**︰ 要求何時開始的日期和時間。

* **OperationId**: GUID 可唯一識別要求。 這項要求可以用於仍持續時取消作業。

* **狀態**︰ 要求的目前狀態。 進行中的要求，同時也會列出目前階段中要求的。

* **CancelRequest**︰ 標幟，指出是否已取消邀請。

* **進度**︰ 百分比的估計完成作業。 50 表示作業是完成大約 50%。

* **詳細資料**︰ 提供更詳細的進度報告的 XML 值。 進度報表會隨著集的資料列來源的資料複製到目標定期更新。 若失敗或例外狀況，此欄也會包含有關失敗的詳細的資訊。


### <a name="azure-diagnostics"></a>Azure 診斷

分割合併服務使用的監控和診斷根據 Azure SDK 2.5 Azure 診斷。 您可以控制診斷設定以下所述︰ [Azure 雲端服務與虛擬機器中的 [啟用診斷](../cloud-services/cloud-services-dotnet-diagnostics.md)。 下載套件包含兩個診斷設定 – 一個 web 角色，一個用於工作者角色。 服務的這些診斷設定追蹤[雲端服務的基本概念 Microsoft Azure 中](https://code.msdn.microsoft.com/windowsazure/Cloud-Service-Fundamentals-4ca72649)提供的指導方針。 包含要記錄效能計數器、 IIS 記錄、 Windows 事件記錄，以及分割合併的應用程式的事件記錄檔的定義。 

## <a name="deploy-diagnostics"></a>部署診斷程式 

若要啟用的監控和診斷程式診斷設定用於 NuGet 套件所提供的網頁和背景工作角色，請執行下列命令使用 PowerShell 的 Azure: 

    $storage_name = "<YourAzureStorageAccount>" 
    
    $key = "<YourAzureStorageAccountKey" 
    
    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key  
    
    
    $config_path = "<YourFilePath>\SplitMergeWebContent.diagnostics.xml" 
    
    $service_name = "<YourCloudServiceName>" 
    
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWeb" 
    
    
    $config_path = "<YourFilePath>\SplitMergeWorkerContent.diagnostics.xml" 
    
    $service_name = "<YourCloudServiceName>" 
    
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWorker" 

您可以尋找如何設定和部署以下診斷程式設定的詳細資訊︰ [Azure 雲端服務與虛擬機器中的 [啟用診斷](../cloud-services/cloud-services-dotnet-diagnostics.md)。 

## <a name="retrieve-diagnostics"></a>擷取診斷程式 

您可以輕鬆地存取您診斷，從 Visual Studio 伺服器總管 Azure 伺服器總管樹狀結構的組件中。 開啟 Visual Studio 執行個體，然後在功能表列中按一下 [檢視] 和 [伺服器總管。 按一下 [連線至您訂閱的 Azure Azure 圖示。 然後瀏覽至 Azure]-> [儲存]-> [ <your storage account> ]-> [表格]-> [WADLogsTable。 如需詳細資訊，請參閱[瀏覽儲存資源伺服器檔案總管](http://msdn.microsoft.com/library/azure/ff683677.aspx)。 

![WADLogsTable][2]

在上圖中醒目提示 WADLogsTable 包含從分割合併服務的應用程式記錄詳細的事件。 請注意生產部署，就能下載套件的預設設定。 因此的間隔的記錄檔和計數器取自服務執行個體是大型 （5 分鐘）。 針對測試與開發，降低間隔調整網頁或符合您需求的工作者角色診斷程式設定。 以滑鼠右鍵按一下 Visual Studio 伺服器總管] （請參閱上述） 中的角色，然後調整傳送診斷程式設定的設定] 對話方塊中︰ 

![設定][3]


## <a name="performance"></a>效能

一般而言，較佳的效能是從較高，更多效能服務層級中 Azure SQL 資料庫。 較高的 IO、 CPU 和記憶體配置較高的服務層受益大量複製及刪除分割合併服務所使用的作業。 因此，增加在定義的有限的時間層只會針對這些資料庫。

服務也會執行驗證查詢做為其標準作業的一部分。 這些驗證查詢檢查未預期的目前狀態的範圍內的資料，並確保所有分割或合併移動作業都啟動從一致的狀態。 所有這些查詢運作在 sharding 索引鍵範圍定義的作業的範圍並提供為要求定義批次的大小。 索引具有 sharding 索引鍵作為前置字元的資料行的簡報時，這些查詢執行最適合。 

此外，具有 sharding 索引鍵作為前置字元的資料行的唯一性屬性可讓使用限制消耗資源記錄空間和記憶體最佳化的方法的服務。 此唯一性屬性，才能將大型資料大小 （通常是以上 1 GB)。 

## <a name="how-to-upgrade"></a>如何升級

1. 請遵循[部署分割合併服務](sql-database-elastic-scale-configure-deploy-split-and-merge.md)中的步驟進行。
2. 變更您的雲端服務設定檔案，以反映新的設定參數您分割合併的部署。 新增必要的參數是用來加密的憑證的相關資訊。 若要比較新設定的範本檔案從現有設定下載就能輕鬆執行此動作。 請確定您新增的網頁和工作者角色 「 DataEncryptionPrimaryCertificateThumbprint 」 和 「 DataEncryptionPrimary 」 設定。
3. 部署之前，先更新至 Azure，請確定已完成所有目前使用的分割合併作業。 輕鬆地您可以藉由查詢 RequestStatus 和 PendingWorkflows 資料庫資料表中分割合併的中繼資料的後續要求。
4. 更新現有的雲端服務部署 Azure 訂閱中的分割合併的新套件與更新的服務設定檔。

您不需要佈建新升級的分割合併的中繼資料資料庫。 新的版本會自動將現有的中繼資料資料庫升級為新的版本。 

## <a name="best-practices--troubleshooting"></a>最佳作法與疑難排解
 
-    定義測試租用戶，並執行您最重要的分割或合併移動作業測試租用戶的跨多個擊碎。 確定所有的中繼資料正確的定義中晶怪地圖及限制式或外部索引鍵作業不會違反。
-    保留測試租用戶您最大的租用戶，以確保您不會遇到的資料大小的最大值的資料大小上方的資料大小相關問題。 這可協助您在移動單一租用戶的時間評估上限。 
-    請確定您的結構描述可讓刪除。 分割合併服務需要移除的資料來源晶怪後資料成功複製到目標的功能。 比方說，**刪除引動程序**可以防止服務刪除資料來源上的，並可能會導致失敗的作業。
-    Sharding 金鑰應該位於您的主索引鍵或唯一索引定義的前置字元的資料行。 確保最佳效能分割或合併驗證查詢，以及永遠操作 sharding 鍵範圍的實際資料移動和刪除作業。
-    若非您分割合併的服務資料庫的所在位置的地區碼和資料中心。 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]



<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-overview-split-and-merge/split-merge-overview.png
[2]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics.png
[3]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics-config.png
 
