<properties
    pageTitle="複製活動效能與調整輔助線 |Microsoft Azure"
    description="瞭解當您使用複製活動時，會影響 Azure 資料工廠資料移動的效能的主要因素。"
    services="data-factory"
    documentationCenter=""
    authors="linda33wj"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="jingwang"/>


# <a name="copy-activity-performance-and-tuning-guide"></a>複製活動效能與調整輔助線
Azure 資料工廠複製活動的形式提供最高級安全、 穩定和高效能資料載入解決方案。 它可讓您以 tb 資料百複本每天過了各式各樣的雲端，並在內部部署資料存放區。 熾熱快速資料載入的效能是金鑰，確保您能專注於核心 「 大資料 」 問題︰ 建置進階的分析解決方案，深入見解的資料。

Azure 提供一組企業級的資料的儲存空間及資料倉庫方案，並複製活動提供高度最佳化的資料載入輕鬆設定，並設定的體驗。 含只單一複本活動，您可以來達成︰

- **Azure SQL Data Warehouse** **1.2 GBps** ，將資料載入
- 資料載入**1.0 GBps** **Azure Blob 儲存體**
- **Azure 資料湖存放**在**1.0 GBps**將資料載入


本文將說明︰

- 支援的來源和接收資料的[效能參考編號](#performance-reference)儲存可協助您規劃您的專案。
- 能夠拓展在不同的情況下，包括[平行複製](#parallel-copy)]、 [[雲端資料移動單位](#cloud-data-movement-units)] 及 [[分段複製](#staged-copy); 複製處理量的功能
- 如何調整效能及索引鍵可以影響效能的因素複製[效能調整指引](#performance-tuning-steps)。

> [AZURE.NOTE] 如果您不熟悉複製活動一般，請參閱閱讀本文前[移動使用複製活動的資料](data-factory-data-movement-activities.md)。

## <a name="performance-reference"></a>效能參照

![效能矩陣圖](./media/data-factory-copy-activity-performance/CopyPerfRef.png)

> [AZURE.NOTE] 您可以利用於預設的更多資料移動單位 (DMUs) 來達成較高的處理量執行雲端至雲端複製活動 8 中的最大 DMUs。 例如，與 100 DMUs，您可以複製資料從 Azure Blob Azure 資料湖存放區秒 1 gb 的速率。 請參閱[雲端資料移動單位](#cloud-data-movement-units)的此功能的詳細資料。 連絡[支援 Azure](https://azure.microsoft.com/support/)邀請更多 DMUs。

請注意的事項︰

- 使用下列公式的計算處理能力: [讀取來源的資料大小] / [複製活動執行持續時間]。
- 在資料表中的效能參照數字測量了單一複本活動執行使用[-](http://www.tpc.org/tpch/)資料集。
- 若要複製雲端資料儲存區之間，設定比較**cloudDataMovementUnits** 1 和 4 （或 8）。 未指定**parallelCopies** 。 請參閱[平行複製](#parallel-copy)的這些功能的詳細資料。
- Azure 資料儲存在來源與接收是相同的 Azure 區域中。
- 混合 （內部部署至雲端或內部部署的雲端） 資料移動單一閘道執行個體獨立於內部部署資料存放區的電腦上執行。 設定會列在下一個資料表。 單一活動已上執行時閘道器，[複製] 作業會耗用一小部分的測試電腦的 CPU、 記憶體或網路頻寬。
    <table>
    <tr>
        <td>CPU</td>
        <td>32 核心 2.20 GHz Intel Xeon E5 2660 v2</td>
    </tr>
    <tr>
        <td>記憶體</td>
        <td>128 GB</td>
    </tr>
    <tr>
        <td>網路</td>
        <td>網際網路介面︰ 10 Gbps;內部網路介面︰ 40 Gbps</td>
    </tr>
    </table>

## <a name="parallel-copy"></a>平行複本
您可以從來源讀取資料或寫入目的地**平行執行複製活動中**的資料。 此功能，增強的複製作業，並減少移動資料所花費的時間。

此設定為不同的活動定義中的 [**並行**] 屬性。 **並行**屬性會決定的**同時複製活動執行**數處理來自不同的活動 windows （1 至 2 上午，2 AM 3 AM、 4 AM AM 3 等） 的資料。 當您執行歷史載入時，這項功能很有用。 平行複製功能會套用至**單一執行的活動**。

讓我們來看看範例案例。 在下列範例中，從 [過去的多個扇形區需要處理。 資料工廠執行每個扇形區複製活動 （活動執行） 的執行個體︰

- 從第一個活動視窗 （1 至 2 上午） 的資料扇形區 = = > 活動執行 1
- 從第二個 [活動] 視窗 （2 到 3 上午） 的資料扇形區 = = > 活動執行 2
- 從第二個 [活動] 視窗 （3 至 4 上午） 的資料扇形區 = = > 活動執行 3

等等。

在此範例中，當**並行**值設定為 2 的**活動執行 1**和**活動執行 2**資料複製兩個活動 windows**同時**以提升資料移動效能。 不過，如果含執行 1 的活動相關多個檔案，資料移動服務複製檔案從來源到目的地一個檔案，一次。

### <a name="parallelcopies"></a>parallelCopies
您可以使用**parallelCopies**屬性，表示您要使用的複製活動的平行處理。 您可以將此屬性執行緒複製活動，可從您的來源讀取或寫入平行您接收資料儲存區中的最大的數字。

每個複製活動執行，資料工廠會決定用來複製資料來源的資料儲存及目的地資料儲存平行份數。 預設的平行份數，它會視來源與接收您所使用的類型而定。  

來源與接收 |   由服務的預設平行複製計數
------------- | -------------------------------------------------
複製檔案為基礎的儲存區 （Blob 儲存體; 之間的資料資料湖存放區。Amazon S3;內部部署檔案系統中;內部部署 HDFS) | 介於 1 到 32。 用來複製資料，兩個雲端的資料存放區或用於 （若要複製資料，或從內部部署資料儲存區） 的混合式複本的閘道器電腦實體設定之間的檔案和數字的雲端資料移動單位 (DMUs) 大小而定。
從**任何來源資料儲存至 Azure 資料表儲存體**複製資料 | 4
所有其他來源與接收組 | 1

通常，預設行為，應該能最佳處理量。 不過，若要控制主控您的資料的電腦上的載入儲存，或調整複製效能，您可以選擇覆寫預設值，並指定**parallelCopies**屬性的值。 值必須是介於 1 到 32 （兩者均 （含））。 在執行階段，為獲得最佳效能，複製活動會使用的值小於或等於您設定的值。

    "activities":[  
        {
            "name": "Sample copy activity",
            "description": "",
            "type": "Copy",
            "inputs": [{ "name": "InputDataset" }],
            "outputs": [{ "name": "OutputDataset" }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                },
                "sink": {
                    "type": "AzureDataLakeStoreSink"
                },
                "parallelCopies": 8
            }
        }
    ]

請注意的事項︰

- 當您複製檔案為基礎的儲存區之間的資料時，平行，這時檔案層級。 有單一檔案中沒有區塊。 實際的平行資料移動服務使用複製作業在執行階段的份數不超過您擁有的檔案數量。 如果複製行為是**mergeFile**，複製活動無法利用平行。
- 當您指定**parallelCopies**屬性的值時，請考慮載入增加您來源與接收資料儲存區上，並閘道器時混合式複本。 發生這種情況尤其是當您有多個活動或同時執行相同的資料存放區上執行的相同活動。 如果您發現的資料存放區 」 或 「 閘道器淹沒了負載，減少減輕載入的**parallelCopies**值。
- 當您從儲存的檔案，再為基礎儲存檔案為基礎的不是複製資料時，資料移動服務會忽略**parallelCopies**屬性。 即使指定平行，它不會套用在此情況下。

> [AZURE.NOTE] 使用**parallelCopies**功能，當您執行混合式複本時，您必須使用資料管理閘道器 1.11 或更新版本。

### <a name="cloud-data-movement-units"></a>雲端資料移動單位
**雲端資料移動單位 (DMU)**是用來代表 power （CPU、 記憶體和網路資源分派的組合） 的單一資料工廠單位。 在雲端至雲端複製作業，而不是混合式複本時，可能會使用 DMU。

根據預設，資料工廠會使用單一的雲端 DMU 來執行單一複製活動執行。 若要覆寫此預設值，請指定**cloudDataMovementUnits**屬性的值如下所示。 效能增益等級相關的資訊更多的單位輸入特定複製來源和接收設定時，您可能會收到，請參閱[效能參照](#performance-reference)。

    "activities":[  
        {
            "name": "Sample copy activity",
            "description": "",
            "type": "Copy",
            "inputs": [{ "name": "InputDataset" }],
            "outputs": [{ "name": "OutputDataset" }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                },
                "sink": {
                    "type": "AzureDataLakeStoreSink"
                },
                "cloudDataMovementUnits": 4
            }
        }
    ]

**允許值** **cloudDataMovementUnits**屬性是 1 （預設）、 2、 4 和 8。 在執行階段複製作業使用**雲端 DMUs 的實際次數**為等於或小於設定的值，根據您資料的模式。 

> [AZURE.NOTE] 如果您需要更多雲端 DMUs 為較高的處理量時，請連絡[支援 Azure](https://azure.microsoft.com/support/)。 只有當您將多個檔案從 Blob 儲存體複製到 Blob 儲存體，資料湖儲存或 Azure SQL 資料庫，而且檔案大小大於或等於 16 MB 個別時，設定 8 和目前適用於上方。

若要更有效地使用這兩個屬性，以及強化您的資料移動處理能力，請參閱[使用情況的範例](#case-study-use-parallel-copy)。 您不需要設定**parallelCopies**利用的預設行為。 如果您設定，而且**parallelCopies**太小，多個雲端 DMUs 可能不能充分運用。  

它的**重要**請記住，您會根據複製作業的總時間。 如果複製工作用一個雲端單位隨身攜帶一小時，現在所需的四個雲端單位 15 分鐘，仍會保留的整體帳單幾乎相同。 例如，您可以使用四個雲端單位。 第一個雲端單位花 10 分鐘，第二個，10 分鐘的時間，第三個，也就是 5 分鐘，第四個項目 5 分鐘，所有複製的一個活動中執行。 您會針對總複製 （移動資料） 的時間，這是 10 + 10 + 5 + 5 = 30 分鐘。 使用**parallelCopies**並不會影響帳單。

## <a name="staged-copy"></a>分段的複本
當您複製的資料來源資料存放區接收資料存放區時，您可以選擇 Blob 儲存體作為中期的暫存存放區。 臨時是在下列情況中尤其有用︰

1.  **您想要內嵌到 SQL Data Warehouse PolyBase 透過各種不同的資料存放區的資料**。 SQL Data Warehouse 會使用作為高處理量機制 PolyBase，若要將 SQL Data Warehouse 載入大量的資料。 不過，Blob 儲存體，必須使用來源資料，且必須符合其他準則。 當您從 Blob 儲存體以外的資料存放區載入資料時，您可以啟用透過中期暫存 Blob 儲存體複製資料。 在此情況下，資料工廠執行所需的資料轉換，以確保其符合 PolyBase 的需求。 然後，將資料載入 SQL Data Warehouse 使用 PolyBase。 如需詳細資訊，請參閱[使用 PolyBase 載入 Azure SQL Data Warehouse 將資料](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)。
2.  **有時所需執行混合式資料移動時 （也就是內部部署資料之間複製儲存和雲端資料儲存） 透過變得很慢的網路連線**。 若要改善效能中,，您可以壓縮資料內部，讓它的時間，將資料移到暫存雲端中的資料存放區。 然後您可以在之前載入目的地資料存放解壓縮臨時存放區中的資料。
3.  **您不想要開啟非連接埠 80 和連接埠 443 的防火牆，由於公司的 IT 原則的連接埠**。 例如，Azure SQL 資料庫接收或 Azure SQL Data Warehouse 接收從內部部署資料存放區複製資料，您需要啟動 Windows 防火牆] 及 [您的公司防火牆連接埠 1433年輸出 TCP 通訊。 在此案例中，利用閘道器至第一個複製資料至 Blob 儲存體暫存執行個體透過 HTTP 或 HTTPS 連接埠 443。 然後，將資料載入到 SQL 資料庫或 SQL Data Warehouse 從 Blob 儲存體臨時。 在此流程，您不需要啟用連接埠 1433年。

### <a name="how-staged-copy-works"></a>如何分段的複製運作
當您啟動臨時功能時，第一次資料複製來源資料存放區暫存的資料存放區 （將您自己）。 接下來，資料會從暫存的資料存放區複製接收資料存放區。 資料工廠會自動管理您的兩個階段流程。 資料工廠會資料移動完成後，也清理暫存資料從暫存的儲存空間。

在雲端複製案例 （來源和接收的資料存放區是在雲端） 中，不會使用閘道器。 資料工廠服務會執行複製作業。

![分段複製︰ 雲端的案例](media/data-factory-copy-activity-performance/staged-copy-cloud-scenario.png)

在混合式複製案例 （來源是內部部署及接收位於雲端） 閘道器將資料來源的資料存放區移到暫存的資料存放區。 資料工廠服務會將資料從暫存的資料存放區移至接收資料存放區。 雲端資料存放區中的資料複製到透過臨時的內部部署資料存放區也被支援反轉流程。

![分段複製︰ 混合式案例](media/data-factory-copy-activity-performance/staged-copy-hybrid-scenario.png)

當您啟動移動資料使用臨時存放區時，您可以指定是否要壓縮之前將資料來源的資料存放區移至 [過渡或暫存的資料存放區中，並將資料從過渡或臨時接收資料存放區的資料存放區之前然後解壓縮的資料。

目前您無法複製使用臨時存放區的兩個內部部署資料儲存區之間的資料。 我們預期推出能夠使用此選項。

### <a name="configuration"></a>設定
您可以設定 [ **enableStaging**設定，指定您是否要將資料載入目的資料儲存區之前，會進入 Blob 儲存體複製活動。 您可以將**enableStaging**設為 TRUE，指定下表所列的其他屬性。 如果您沒有帳戶，您也需要建立 Azure 儲存體或儲存空間共用 access 簽章連結服務臨時。

屬性 | 描述 | 預設值 | 所需
--------- | ----------- | ------------ | --------
**enableStaging** | 指定您是否想要複製透過臨時市集過渡的資料。 | False | 無
**linkedServiceName** | 指定[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service)或[AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service)所參照的儲存空間的執行個體中的連結的服務作為中期的暫存存放區的名稱。 <br/><br/> 您無法使用共用的 access 簽章使用儲存空間，將資料載入 SQL Data Warehouse 透過 PolyBase。 您可以使用其在所有其他案例。 | N/A | 是的當**enableStaging**設為 TRUE
**路徑** | 指定您想要包含的分段的資料的 Blob 儲存體路徑。 如果您不提供路徑，服務就會建立暫時資料的儲存容器。 <br/><br/> 只有當您使用共用的 access 簽章，使用儲存空間，或您需要的特定位置的暫時資料，請指定的路徑。 | N/A | 無
**enableCompression** | 指定其複製到目的地之前，是否應該壓縮資料。 這項設定可以降低音量傳輸的資料。 | False | 無

以下是範例的定義複製活動使用上述表格中所描述的屬性︰

    "activities":[  
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [{ "name": "OnpremisesSQLServerInput" }],
        "outputs": [{ "name": "AzureSQLDBOutput" }],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": "MyStagingBlob",
                "path": "stagingcontainer/path",
                "enableCompression": true
            }
        }
    }
    ]


### <a name="billing-impact"></a>帳單寄送的影響
您會根據兩個步驟︰ 複製工期，並複製類型。 

- 當您使用臨時期間雲端複本 （將資料從雲端的資料存放區複製到另一個雲端資料儲存區），您會 [總和的步驟 1 和 2 的複製工期] x [雲端複製單價]。
- 當您使用臨時期間混合式複本 （將資料從內部部署資料存放區複製到雲端的資料儲存區），您會 [混合式複製工期] 的 [混合式複製單價] x + [雲端複製持續時間] x [雲端複製單價]。


## <a name="performance-tuning-steps"></a>效能調整步驟
我們建議您採取這些步驟來調整您的資料工廠服務複製活動的效能︰

1.  **建立比較基準**。 在開發階段，請針對代表資料範例使用複製活動測試您的管線。 您可以使用資料工廠[切割模型](data-factory-scheduling-and-execution.md#time-series-datasets-and-data-slices)，來限制您使用的資料量。

    收集使用的**監控和管理應用程式**的執行時間和效能特性。 在您的資料工廠首頁上，選擇 [**監控和管理**]。 在樹狀檢視中，選擇 [**輸出資料集**]。 在 [**活動視窗**] 清單中，選擇 [複製活動執行]。 **活動 Windows**列出複製活動持續時間] 及 [複製資料的大小。 **活動視窗檔案總管**中，會列出處理量。 若要深入瞭解應用程式，請參閱[監視器使用及管理 Azure 資料工廠管線監控和管理應用程式](data-factory-monitor-manage-app.md)。

    ![執行 [詳細資料的活動](./media/data-factory-copy-activity-performance/mmapp-activity-run-details.png)

    稍後在文章中，您可以比較的效能與設定您的狀況複製活動的[效能參照](#performance-reference)從我們的測試。
2. **診斷及最佳化效能**。 如果您看到的效能不符合您的期望，必須找出效能障礙。 然後，最佳化效能，若要移除或縮小瓶頸的效果。 效能診斷的完整描述超出範圍的本文中，但以下是一些常見的考量︰
    - 效能功能︰
        - [平行複本](#parallel-copy)
        - [雲端資料移動單位](#cloud-data-movement-units)
        - [分段的複本](#staged-copy)   
    - [來源](#considerations-for-the-source)
    - [接收](#considerations-for-the-sink)
    - [序列化和還原序列化](#considerations-for-serialization-and-deserialization)
    - [壓縮](#considerations-for-compression)
    - [資料行對應](#considerations-for-column-mapping)
    - [資料管理閘道器](#considerations-for-data-management-gateway)
    - [其他考量](#other-considerations)

3. **展開至整個資料集的設定**。 當您滿意的執行結果和效能時，您可以展開的定義和管線作用期涵蓋整個資料集。

## <a name="considerations-for-the-source"></a>來源的考量
### <a name="general"></a>一般
請務必基礎的資料存放區不會被或對其執行其他工作負載淹沒了。 

為 Microsoft 資料存放區，請參閱[監視及調整主題](#performance-reference)的特定資料存放區，並協助您了解資料儲存效能特性、 最小化回應的時間，以及最大化處理量。

如果您將從 Blob 儲存體的資料複製到 SQL Data Warehouse 時，請考慮使用**PolyBase**提升效能。 如需詳細資訊，請參閱[使用 PolyBase 載入 Azure SQL Data Warehouse 將資料](data-factory-azure-sql-data-warehouse-connector.md###use-polybase-to-load-data-into-azure-sql-data-warehouse)。


### <a name="file-based-data-stores"></a>檔案為基礎的資料存放區
*（包括 Blob 儲存體、 資料湖存放區、 Amazon S3、 內部部署檔案系統和內部部署 HDFS）*

- **一般檔案大小和檔案計數**︰ 複製活動將資料檔案傳輸的時間。 使用相同的要移動的資料量，整體處理能力是較低，如果資料包含多個小的檔案，而不是幾個大型檔案，因為啟動安裝階段的每個檔案。 因此，如果可能的話，將小型檔案合併較大的檔案，以取得更高的處理量。
- **檔案格式和壓縮**︰ 以提升效能的其他方法，請參閱[考量序列化和還原序列化](#considerations-for-serialization-and-deserialization)和[考量壓縮](#considerations-for-compression)節。
- 針對**內部部署檔案系統**的案例，可用的**資料管理閘道**是有必要，請參閱[資料管理閘道器的考量](#considerations-for-data-management-gateway)。

### <a name="relational-data-stores"></a>關聯式資料存放區
*（包括 SQL 資料庫。SQL Data Warehouse;Amazon Redshift;SQL Server 資料庫。與 Oracle MySQL、 DB2、 Teradata、 Sybase 及 PostgreSQL 資料庫等等。）*

- **資料模式**︰ 您的表格結構描述會影響複製處理量。 大型的資料列的大小可讓您更佳的效能比小型的資料列的大小，若要複製相同的資料量。 原因是資料庫更有效率地可以擷取較少的批次包含較少的資料列的資料。
- **查詢或預存程序**︰ 最佳化的查詢或您指定的複製活動來源，以更有效率地擷取資料的預存程序邏輯。
- **內部部署關聯式資料庫**，例如 SQL Server 和 Oracle，這需要使用**資料管理閘道器**，請參閱 [] 區段中[的資料管理閘道器考量](#considerations-on-data-management-gateway)。

## <a name="considerations-for-the-sink"></a>接收的考量

### <a name="general"></a>一般
請務必基礎的資料存放區不會被或對其執行其他工作負載淹沒了。 

為 Microsoft 資料存放區，請參閱[監視及調整主題](#performance-reference)的特定資料存放區。 下列主題可以協助您瞭解資料儲存區效能特性，以及如何將最小化回應時間和最大化處理量。

如果您要以**SQL Data Warehouse** **Blob 儲存體**複製資料，請考慮使用**PolyBase**提升效能。 如需詳細資訊，請參閱[使用 PolyBase 載入 Azure SQL Data Warehouse 將資料](data-factory-azure-sql-data-warehouse-connector.md###use-polybase-to-load-data-into-azure-sql-data-warehouse)。


### <a name="file-based-data-stores"></a>檔案為基礎的資料存放區
*（包括 Blob 儲存體、 資料湖存放區、 Amazon S3、 內部部署檔案系統和內部部署 HDFS）*

- **複製行為**︰ 複製活動從不同的檔案的資料存放區複製資料時，是否有透過**copyBehavior**屬性的三個選項。 它會保留階層、 簡維階層圖]，或合併的檔案。 [保留] 或 [簡維階層幾乎不效能費用，但合併檔案會造成效能的費用，增加。
- **檔案格式和壓縮**︰ 請參閱更多的方法，以提升效能的[考量序列化和還原序列化](#considerations-for-serialization-and-deserialization)和[考量壓縮](#considerations-for-compression)節。
- **Blob 儲存體**︰ 目前 Blob 儲存體支援只會封鎖 blob 最佳化的資料傳輸和處理量。
- **內部部署檔案系統**的案例中需要使用**資料管理閘道器**，請參閱[資料管理閘道器的考量事項](#considerations-for-data-management-gateway)章節。

### <a name="relational-data-stores"></a>關聯式資料存放區
*（包括 SQL 資料庫、 SQL Data Warehouse、 SQL Server 資料庫及 Oracle 資料庫）*

- **複製行為**︰ 根據您所設定**sqlSink**的屬性，複製活動寫入到目的地資料庫資料以不同方式。
    - 根據預設，資料移動服務會使用大量複製 API，若要插入中的資料會附加模式，提供最佳效能。
    - 如果您在接收中設定預存程序，資料庫會套用，而不是一次為大量載入資料一列。 效能大幅去掉。 如果您的資料集很大時適用，請考慮使用**sqlWriterCleanupScript**屬性切換。
    - 如果您執行的每個複製活動設定**sqlWriterCleanupScript**屬性，服務觸發指令碼，然後您可以使用大量複製 API 來插入資料。 例如，整個表格，覆寫最新的資料，您可以指定指令碼大量載入新的資料來源的前，先刪除所有記錄。
- **資料模式和批次大小**︰
    - 您的表格結構描述會影響複製處理量。 若要將相同的資料量，大型資料列的大小可讓您更佳的效能比小型列大小因為資料庫更有效率地認可較少的批次的資料。
    - 複製活動插入批次一系列中的資料。 您可以使用**writeBatchSize**屬性，以批次中設定列的數。 如果您的資料有小型的列，您可以設定較高的值，以改善批次耗用和較高的處理量**writeBatchSize**屬性。 如果您的資料列大小很大，請小心增加**writeBatchSize**。 複製失敗，導致超載資料庫可能會導致高的值。
- **內部部署關聯式資料庫**，例如 SQL Server 與 Oracle，需要使用**資料管理閘道器**，請參閱[資料管理閘道器的考量事項](#considerations-for-data-management-gateway)章節。


### <a name="nosql-stores"></a>NoSQL 存放區
*（包括資料表儲存體和 Azure DocumentDB）*

- **資料表儲存體**︰
    - **分割**︰ 資料寫入交錯的磁碟分割區大幅降低效能。 排序，讓資料插入有效率地一個磁碟分割之後，您的來源資料分割索引鍵，或調整邏輯寫入到單一的磁碟分割的資料。
- 針對**DocumentDB**:
    - **批次大小**︰ **writeBatchSize**屬性設定的平行要求 DocumentDB 服務建立文件。 當您增加**writeBatchSize** ，因為更多的平行邀請傳送給 DocumentDB 時，您可以預期更佳的效能。 不過，等待節流寫入到 DocumentDB 時 （「 要求工資率很大 」 錯誤訊息是）。 各種因素可能會導致節流，包括文件大小的文件和目標集合編製索引原則的數目。 若要達到較高的複製處理量，請考慮使用更好的集合，例如 S3。

## <a name="considerations-for-serialization-and-deserialization"></a>序列化和還原序列化的考量
您輸入的資料集或輸出資料集的檔案時，就可以會發生序列化和還原序列化。 複製活動目前支援 Avro 和文字 （例如 CSV 和 TSV） 資料格式。

**複製行為**︰

-   複製檔案的資料儲存區之間的檔案︰
    - 輸入與輸出資料集兩個當有相同或沒有任何檔案的格式設定、 資料移動服務會執行任何序列化或還原序列化二進位複製。 您會看到較高比較的案例中，在其中來源與接收檔案格式設定會不同於其他處理能力。
    - 當輸入與輸出資料集兩個已文字格式和只編碼類型不同，資料移動服務只會編碼方式轉換。 它不會執行任何序列化和還原序列化，會造成某些效能負荷二進位的複本。
    - 輸入與輸出資料集兩個當有不同的檔案格式或不同的設定，例如分隔符號，資料移動服務，就會還原序列化串流轉換，然後將它序列化到您所指定的輸出格式的來源資料。 這項作業會產生更重要的效能成本相較於其他。
- 當您複製的資料存放區的不是檔案 （例如，從檔案存放區，關聯式存放區） 的檔案時，序列化或還原序列化步驟需要。 此步驟會導致效能成本。

**檔案格式**︰ 您選擇的檔案格式可能會影響到複製的效能。 例如，Avro 是簡潔的二進位格式儲存資料的中繼資料。 Hadoop 生態系統處理和查詢中有廣泛的支援。 不過，Avro 是較昂貴的序列化和還原序列化，結果比文字格式的較低複製處理量。 而進行您所選擇的整個處理流程的檔案格式。 開始使用什麼表單資料來源的資料存放區中，或從外部系統; 擷取儲存儲存、 分析處理，與查詢; 最佳的格式與何種格式中應該匯出資料至資料超市報表和視覺效果的工具。 有時的不理想的檔案格式讀寫效能可能很不錯的選擇，當您考慮整體分析程序。

## <a name="considerations-for-compression"></a>壓縮的考量
當您輸入或輸出資料集的檔案時，您可以設定執行壓縮或解壓縮，因為它會將資料寫入到目的地複製活動。 當您選擇壓縮時，您所做的權衡輸入輸出 (I/O) 和 CPU。 壓縮額外計算資源中的資料成本。 但是，減少網路 I/O 和儲存空間。 根據您的資料，您可能會看到提升整體複製處理能力。

**轉碼器**︰ 複製活動支援 gzip、 bzip2 及結實壓縮類型。 Azure HDInsight 都可以使用所有的三種類型的處理。 每個壓縮轉碼器具有以下優點。 例如，bzip2 有最低的複製處理量，，但您會收到 bzip2 最佳登錄區查詢效能，因為您可以將其分割進行處理。 Gzip 是最對稱的選項，然後最常使用它。 選擇最適合您的端對端案例的轉碼器。

**層級**︰ 您可以選擇從每個壓縮轉碼器的兩個選項︰ 快壓縮及最佳壓縮。 盡，即使未最佳化壓縮所產生的檔案時，最快的壓縮的選項將壓縮的資料。 最佳化壓縮的選項壓縮花更長的時間，並產生最少的資料。 您可以測試兩個選項]，查看其中提供更好的整體效能，在您的個案。

**A 考量**︰ 若要複製大量的內部部署存放與雲端之間的資料，請考慮使用壓縮中期 blob 儲存體。 公司網路和 Azure 服務的頻寬限制因素，而且您想要輸入的資料集輸出資料集兩個壓縮的形式，使用中期儲存空間是很有幫助的。 更明確地說，您可以將單一複本活動分成兩個複製活動。 第一份複本活動會複製到中期或暫存的 blob 壓縮表單中的 [來源的資料。 第二個複製活動臨時，將壓縮的資料複製，然後再解壓縮時寫入接收。

## <a name="considerations-for-column-mapping"></a>資料行對應的考量
您可以將**columnMappings**屬性複製活動為所有對應或輸出資料行輸入資料行的子集合。 資料移動服務讀取的資料來源之後，需要的資料上執行的資料行對應，才能寫入接收資料。 此額外處理減少複製處理量。

如果您的來源資料的儲存為可查詢，，例如，如果是 SQL 資料庫或 SQL Server，例如關聯式存放區，或如果是資料表儲存體或 DocumentDB，例如 NoSQL 商店考慮推入的資料行的篩選及重新排列 」，而不是使用資料行對應的 [**查詢**] 屬性的邏輯。 如此一來，資料移動服務讀取來源資料存放區會更有效率的資料時，就會發生預測。

## <a name="considerations-for-data-management-gateway"></a>資料管理閘道器的考量
閘道器安裝程式建議，請參閱[使用資料管理閘道器的考量](data-factory-move-data-between-onprem-and-cloud.md#Considerations-for-using-Data-Management-Gateway)。

**閘道器電腦環境**︰ 我們建議您使用的資料管理閘道器主機專用的電腦。 使用 [工具] 等來檢查 CPU、 記憶體和頻寬使用閘道器電腦上複製作業期間的效能。 如果 CPU、 記憶體或網路頻寬會出，切換到更快速的電腦。

**執行同時複製活動**︰ 多個複製活動執行相同的時間，或同時作為單一資料管理閘道執行個體。 同時工作的最大的數字會根據閘道器電腦的硬體設定計算。 他們會挑選閘道器或另一個工作逾時，直到佇列額外的工作。 若要避免資源競爭閘道器電腦上的，您可以階段複製活動排程可減少一次的佇列中複製的工作或考慮載入至多個閘道器電腦。


## <a name="other-considerations"></a>其他考量
如果您想要複製的資料大小很大，您可以調整您的商務邏輯，進一步的磁碟分割資料工廠使用切割機制的資料。 然後，排程複製活動，若要縮小資料每個複製活動執行更頻繁地執行。

小心資料集與資料工廠要求至相同的資料存放區的連接器，同時複製活動的數目。 許多的同時複製工作可能節流資料存放區，並降低整體效能，複製工作內部重試，並在某些情況下，執行失敗的 [潛在客戶。

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>範例案例︰ 從內部部署的 SQL Server 的複製至 Blob 儲存體
**案例**︰ 管線內建將內部部署的 SQL Server 資料複製到 Blob 儲存體 CSV 格式。 若要複製作業速度更快，應該 CSV 檔案壓縮 bzip2 格式。

**測試及分析**︰ 複製活動的是少於 2 個 MBps，也就是比效能事先很慢。

**效能分析及調整**︰ 若要疑難排解效能問題，我們來看看如何處理及移動資料。

1.  **讀取資料**︰ 閘道連線到 SQL Server，並將查詢傳送。 SQL Server 資料流傳送閘道，經由內部網路的回應。
2.  **Serialize 及壓縮資料**︰ 閘道器序列化為 CSV 格式的資料流並 bzip2 串流的資料。
3.  **撰寫資料**︰ 閘道會上傳 bzip2 串流提供至透過網際網路 Blob 儲存體。

如您所見，資料正在處理和串流連續的方式移動︰ SQL Server > LAN > 閘道器 > WAN > Blob 儲存體。 **整體效能被閘的管線過的最小處理能力**。

![資料工作流程](./media/data-factory-copy-activity-performance/case-study-pic-1.png)

一或多個下列因素可能會導致效能瓶頸︰

-   **來源**︰ SQL Server 本身由於負載重具有低處理量。
-   **資料管理閘道器**︰
    -   **區域網路**︰ 閘道遠的 SQL Server 的電腦，並有低頻寬連線。
    -   **閘道器**︰ 閘道器已送達載入限制執行下列作業︰
        -   **序列化**︰ 序列化為 CSV 格式的資料流具有變得很慢處理量。
        -   **壓縮**︰ 選擇變得很慢壓縮轉碼器 (例如，bzip2，這是與核心 i7 2.8 MBps)。
    -   **WAN**︰ 公司網路和 Azure 服務之間的頻寬不足 (例如，T1 = 1,544 kbps;T2 = 6,312 kbps)。
-   **接收**︰ Blob 儲存體具有低處理量。 （這種情況下是不會因為其 SLA 保證 60 MBps 的最小值。）

在此情況下，bzip2 資料壓縮可能會降低整個管道的郵件。 切換到 gzip 壓縮轉碼器可能減輕這瓶頸。


## <a name="sample-scenarios-use-parallel-copy"></a>範例案例︰ 使用平行複本  

**案例 i:**複製內部部署檔案系統的 1000 1 MB 的檔案儲存到 Blob 儲存體。

**分析及效能調整**︰ 例如，如果您已經安裝閘道器四核心在電腦上，資料工廠使用 16 平行份數，從檔案系統中將檔案移至 Blob 儲存體時，同時。 此平行執行應該會導致高處理量。 您也可以明確地指定平行複本計數。 當您複製多個小的檔案時，平行複本大幅更有效地使用資源協助處理量。

![情況 1](./media/data-factory-copy-activity-performance/scenario-1.png)

**案例 II**: Blob 儲存體中 500 MB 的 20 二進位大型物件複製到資料湖市集分析，，然後調整效能。

**分析及效能調整**︰ 在此案例中，資料工廠將資料複製 Blob 儲存體資料湖存放區使用單一複本 (**parallelCopies**設為 1) 和單一雲端資料移動單位。 您可以一同觀看處理量會關閉的說明[效能參考] 區段](#performance-reference)中。   

![案例 2](./media/data-factory-copy-activity-performance/scenario-2.png)

**案例 III**︰ 個別檔案大小大於數十種 Mb 和總量很大。

**分析及時的效能**︰ 增加**parallelCopies**無法複製更佳的效能產生由於單一雲端 DMU 資源限制。 不過，您應該指定更多雲端 DMUs 以取得更多資源執行的資料移動。 未指定**parallelCopies**屬性的值。 資料工廠會為您處理平行。 在此情況下，如果您將**cloudDataMovementUnits**為 4 時的處理能力關於四個時間發生。

![情況 3](./media/data-factory-copy-activity-performance/scenario-3.png)

## <a name="reference"></a>參照
以下是效能監視及調整參照的部分支援的資料儲存區︰

- Azure （包括 Blob 儲存體和資料表儲存體） 的儲存空間︰ [Azure 儲存體延展性目標](../storage/storage-scalability-targets.md)和[Azure 儲存體效能與延展性檢查清單](../storage//storage-performance-checklist.md)
- Azure SQL 資料庫︰ 您可以[監視效能](../sql-database/sql-database-service-tiers.md#monitoring-performance)，並檢查資料庫交易 (DTU) 單位百分比
- Azure SQL Data Warehouse︰ 其功能，以資料倉庫單位 (DWUs);請參閱[管理計算 power 中 Azure SQL Data Warehouse （概觀）](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
- Azure DocumentDB︰ [DocumentDB 中的效能層級](../documentdb/documentdb-performance-levels.md)
- 內部部署的 SQL Server︰[螢幕與效能調整](https://msdn.microsoft.com/library/ms189081.aspx)
- 內部部署檔案伺服器︰[效能調整檔案伺服器](https://msdn.microsoft.com/library/dn567661.aspx)
