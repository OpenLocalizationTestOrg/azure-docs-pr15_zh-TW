<properties 
    pageTitle="將資料從內部部署 HDFS 移 |Azure 資料工廠" 
    description="進一步瞭解如何將內部部署 HDFS 使用 Azure 資料工廠的資料。" 
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
    ms.date="09/06/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-on-premises-hdfs-using-azure-data-factory"></a>將資料從內部部署 HDFS 使用 Azure 資料工廠移動
本文概述如何使用複製活動 Azure 資料工廠將資料從內部部署 HDFS 移到另一個資料存放區。 本文是根據複製活動與支援的資料存放區組合提供的一般資料移動概觀[資料移動活動](data-factory-data-movement-activities.md)文件。

資料工廠目前支援只移動資料從內部部署 HDFS 其他資料儲存區中，但無法將資料從其他資料儲存區到內部部署 HDFS。


## <a name="enabling-connectivity"></a>啟用連線
資料工廠服務支援連線到內部部署 HDFS 使用資料管理閘道器。 請參閱[內部部署的位置與雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md)文章，進一步瞭解資料管理閘道器和設定閘道器的逐步指示。 使用連線至 HDFS，即使其會裝載於 Azure IaaS VM 的閘道器。 

雖然您可以在相同的內部部署電腦或為 HDFS Azure VM 安裝閘道器，我們建議您將閘道器安裝個別電腦/Azure IaaS VM 上。 無法在不同的電腦上的閘道器會減少資源競爭，可改善效能。 當您在不同的電腦上安裝的閘道器時，電腦應能夠存取與 HDFS 電腦。 


## <a name="copy-data-wizard"></a>複製資料精靈
建立管線將資料從內部部署 HDFS 複製最簡單的方法是使用 [複製資料精靈。 請參閱[教學課程︰ 建立使用複製精靈的管線](data-factory-copy-data-wizard-tutorial.md)的快速建立管線，使用 [複製資料] 精靈的逐步解說。 

下列範例會提供可供您建立的管線使用[Azure 入口網站](data-factory-copy-activity-tutorial-using-azure-portal.md)或[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)或[PowerShell 的 Azure](data-factory-copy-activity-tutorial-using-powershell.md)範例 JSON 定義。 即會顯示如何將內部部署 HDFS 資料複製到 Azure Blob 儲存體。 不過，可以複製資料至任何接收敘述[以下](data-factory-data-movement-activities.md#supported-data-stores)Azure 資料工廠中使用複製活動。

## <a name="sample-copy-data-from-on-premises-hdfs-to-azure-blob"></a>範例︰ 從內部部署 HDFS 資料複製到 Azure Blob

此範例會示範如何將內部部署 HDFS 資料複製到 Azure Blob 儲存體。 不過，資料可以複製**直接**至任何接收敘述[以下](data-factory-data-movement-activities.md#supported-data-stores)Azure 資料工廠中使用複製活動。  
 
樣本具有下列資料工廠實體︰

1.  連結的類型[OnPremisesHdfs](#hdfs-linked-service-properties)的服務。
2.  連結的類型[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)的服務。
3.  輸入的[資料集](data-factory-create-datasets.md)的類型[檔案共用](#hdfs-dataset-type-properties)。
4.  輸出[資料集](data-factory-create-datasets.md)的類型[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)。
4.  [管線](data-factory-create-pipelines.md)與使用[FileSystemSource](#hdfs-copy-activity-type-properties)及[BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)複製活動。

範例將資料從內部部署 HDFS 到 Azure blob 每小時。 這些範例中所用的 JSON 屬性是由下列範例的各節所述。 

第一個步驟，設定資料管理閘道器。 [位置內部部署與雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md)文件中的指示。 

**HDFS 連結服務**此範例使用 Windows 驗證。 請參閱驗證您可以使用的不同類型的[HDFS 連結服務](#hdfs-linked-service-properties)一節。 

    {
        "name": "HDFSLinkedService",
        "properties":
        {
            "type": "Hdfs",
            "typeProperties":
            {
                "authenticationType": "Windows",
                "userName": "Administrator",
                "password": "password",
                "url" : "http://<machine>:50070/webhdfs/v1/",
                "gatewayName": "mygateway"
            }
        }
    }

**Azure 連結的儲存服務**

    {
      "name": "AzureStorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**HDFS 輸入資料集**此資料集參照 HDFS 資料夾 DataTransfer/UnitTest /。 管線會複製到目的地此資料夾中的所有檔案。 

設定 「 外部 」:"true"通知資料工廠服務，資料集外部資料 factory，並不會所產生的資料工廠中的活動。
    
    {
        "name": "InputDataset",
        "properties": {
            "type": "FileShare",
            "linkedServiceName": "HDFSLinkedService",
            "typeProperties": {
                "folderPath": "DataTransfer/UnitTest/"
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval":  1
            }
        }
    }




**Azure Blob 輸出資料集**

每小時寫入資料至新 blob (頻率︰ 小時、 間隔︰ 1)。 根據正在處理的扇形區的開始時間，以動態方式評估 blob 的資料夾路徑。 年、 月、 日和開始時間的小時部分，則會使用的資料夾路徑。

    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/hdfs/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
                "format": {
                    "type": "TextFormat",
                    "rowDelimiter": "\n",
                    "columnDelimiter": "\t"
                },
                "partitionedBy": [
                    {
                        "name": "Year",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "yyyy"
                        }
                    },
                    {
                        "name": "Month",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "MM"
                        }
                    },
                    {
                        "name": "Day",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "dd"
                        }
                    },
                    {
                        "name": "Hour",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "HH"
                        }
                    }
                ]
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }



**含複製活動的管線**

管線包含複製活動，設定為使用這些輸入與輸出資料集，並排定執行每小時。 在管線 JSON 定義，**來源**類型設定為 [ **FileSystemSource** ，**接收**類型] 設定為**BlobSink**。 指定**查詢**屬性的 SQL 查詢複製過去的小時內，選取資料。
    
    {
        "name": "pipeline",
        "properties":
        {
            "activities":
            [
                {
                    "name": "HdfsToBlobCopy",
                    "inputs": [ {"name": "InputDataset"} ],
                    "outputs": [ {"name": "OutputDataset"} ],
                    "type": "Copy",
                    "typeProperties":
                    {
                        "source":
                        {
                            "type": "FileSystemSource"
                        },
                        "sink":
                        {
                            "type": "BlobSink"
                        }
                    },
                    "policy":
                    {
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 1,
                        "timeout": "00:05:00"
                    }
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }



## <a name="hdfs-linked-service-properties"></a>HDFS 連結服務的內容

下表提供描述 JSON HDFS 特定的項目連結的服務。

| 屬性 | 描述 | 所需 |
| -------- | ----------- | -------- | 
| 類型 | [類型] 屬性必須設定為︰ **Hdfs** | [是] | 
| Url | HDFS URL | [是] |
| encryptedCredential | [新增 AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx)輸出的存取權的認證。 | 無 |
| 使用者名稱 | Windows 版的使用者名稱驗證。 | 是 （適用於 Windows 驗證）
| 密碼 | Windows 驗證的密碼。 | 是 （適用於 Windows 驗證）
| 識別碼 | Windows，或匿名。 | [是] |
| gatewayName | 資料工廠服務應使用連線至 HDFS 的閘道器的名稱。 | [是] |   

如需詳細資訊的內部部署 HDFS 設定認證，請參閱[設定認證與安全性](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security)。

### <a name="using-anonymous-authentication"></a>使用匿名驗證

    {
        "name": "hdfs",
        "properties":
        {
            "type": "Hdfs",
            "typeProperties":
            {
                "authenticationType": "Anonymous",
                "userName": "hadoop",
                "url" : "http://<machine>:50070/webhdfs/v1/",
                "gatewayName": "mygateway"
            }
        }
    }


### <a name="using-windows-authentication"></a>使用 Windows 驗證
    
    {
        "name": "hdfs",
        "properties":
        {
            "type": "Hdfs",
            "typeProperties":
            {
                "authenticationType": "Windows",
                "userName": "Administrator",
                "password": "password",
                "url" : "http://<machine>:50070/webhdfs/v1/",
                "gatewayName": "mygateway"
            }
        }
    }
 


## <a name="hdfs-dataset-type-properties"></a>HDFS 資料集類型屬性

節與適用於定義資料集的內容的完整清單，請參閱[建立資料集](data-factory-create-datasets.md)。 例如結構、 可用性和資料集 JSON 原則] 區段都是類似的所有資料集類型 (Azure SQL Azure blob、 Azure 資料表等等。)。

**TypeProperties**區段不同資料集的每個類型，並提供的資料存放區中的資料位置的相關資訊。 類型 （包括 HDFS 資料集）**檔案共用**資料集 typeProperties 區段會有下列屬性

屬性 | 描述 | 所需
-------- | ----------- | --------
folderPath | 資料夾的路徑。 範例︰`myfolder`<br/><br/>使用逸出字元 ' \ 」 字串中的特殊字元。 例如︰ folder\subfolder，請指定資料夾\\\\子資料夾的 d:\samplefolder，指定 d\\\\samplefolder。<br/><br/>您可以結合此屬性與**partitionBy**有根據扇形區資料夾路徑開始/結束日期時間。 | [是]
檔案名稱 | 如果您想要參照的特定檔案的資料夾中的資料表，請在 [ **folderPath**中指定檔案的名稱。 如果您沒有指定此屬性的任何值，表格會指向資料夾中的所有檔案。<br/><br/>輸出資料集未指定的檔案名稱時所產生的檔案名稱就會在下列格式︰ <br/><br/>資料。<Guid>.txt (例如:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt | 無
partitionedBy | partitionedBy 可用來指定動態 folderPath，時間數列資料檔名。 範例︰ folderPath 參數化資料的每個小時。 | 無
取值 | 指定篩選，以用來選取子集 folderPath 中的檔案，而不是所有檔案。 <br/><br/>允許的值︰ `*` （多個字元） 和`?`（單一字元）。<br/><br/>範例 1:`"fileFilter": "*.log"`<br/>範例 2:`"fileFilter": 2014-1-?.txt"`<br/><br/>**附註**︰ 取值適用於輸入的檔案共用資料集 | 無
| 格式 | 支援下列格式類型︰ **TextFormat**、 **AvroFormat**、 **JsonFormat**、 **OrcFormat**及**ParquetFormat**。 [格式] 下的 [**類型**] 屬性設其中一個值。 請參閱[指定 TextFormat](#specifying-textformat)、[指定 AvroFormat](#specifying-avroformat)、[指定 JsonFormat](#specifying-jsonformat)、[指定 OrcFormat](#specifying-orcformat)，以及[指定 ParquetFormat](#specifying-parquetformat)區段，如需詳細資訊。 如果您想要複製檔案另存為-是檔案式存放區之間 （二進位複本），您可以略過兩邊輸入與輸出資料集的定義中的 [格式] 區段。 | 無 
| 壓縮 | 指定類型和層級壓縮的資料。 支援的類型︰ **GZip**、**結實**，及**BZip2**和支援層級是︰ **Optimal**和**最快**。 目前，壓縮設定不支援**AvroFormat**或**OrcFormat**中的資料。 如需詳細資訊，請參閱[壓縮支援](#compression-support)一節。  | 無 |



> [AZURE.NOTE] 無法同時使用檔案名稱和取值。


### <a name="using-partionedby-property"></a>使用 partionedBy 屬性

如前一節所述，您可以指定動態 folderPath，與 partitionedBy 時間數列資料檔名。 您可以執行資料工廠巨集與系統變數 SliceStart，SliceEnd 指出指定的資料扇形區邏輯的時間範圍。 

若要進一步瞭解時間數列資料集，排程及扇形區，請參閱[建立資料集](data-factory-create-datasets.md)、[排程及執行](data-factory-scheduling-and-execution.md)，並[建立管線](data-factory-create-pipelines.md)的文章。 

#### <a name="sample-1"></a>範例 1:

    "folderPath": "wikidatagateway/wikisampledataout/{Slice}",
    "partitionedBy": 
    [
        { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
    ],

在此範例中 {扇形區} 會取代格式 (YYYYMMDDHH) 中的資料工廠系統變數 SliceStart 指定的值。 SliceStart 指的是扇形區的開始時間。 FolderPath 是不同的每個扇形區項目。 例如︰ wikidatagateway/wikisampledataout/2014100103 或 wikidatagateway/wikisampledataout/2014100104。

#### <a name="sample-2"></a>範例 2:

    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
     [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],

在此範例中，年、 月、 日和 SliceStart 時間擷取到另一個變數所使用的 folderPath 和檔名] 屬性。

[AZURE.INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]   
[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="hdfs-copy-activity-type-properties"></a>HDFS 複製活動類型的屬性

節與適用於定義活動的內容的完整清單，請參閱[建立管線](data-factory-create-pipelines.md)文章。 針對所有類型的活動有內容，例如名稱、 描述、 輸入與輸出資料表及原則。 

活動 typeProperties 一節中提供的屬性另一方面會隨著每個活動類型。 複製活動，而有所不同的來源和接收類型。

複製活動時來源類型**FileSystemSource**的下列屬性會出現在 typeProperties] 區段中︰

**FileSystemSource**支援下列屬性︰

| 屬性 | 描述 | 允許的值 | 所需 |
| -------- | ----------- | -------------- | -------- |
| 遞迴 | 指出是否讀取資料遞迴子資料夾，或僅從指定的資料夾。 | True 或 False （預設值）| 無 |

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>效能及調整  
請參閱[複製活動的效能與微調指南](data-factory-copy-activity-performance.md)，瞭解如何要素 Azure 資料工廠，並將它最佳化的各種方式中的資料移動 （複製活動） 的效能的影響。

