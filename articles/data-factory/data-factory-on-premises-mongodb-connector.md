<properties 
    pageTitle="將資料從 MongoDB 使用資料工廠移 |Microsoft Azure" 
    description="瞭解如何將資料從 Azure 資料工廠 MongoDB 資料庫。" 
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
    ms.date="08/04/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-mongodb-using-azure-data-factory"></a>將資料從 MongoDB 使用 Azure 資料工廠移動

本文概述如何使用複製活動 Azure 資料工廠，將資料從內部部署 MongoDB 資料庫移到另一個資料存放區。 本文是根據呈現資料移動的一般概觀複製活動與來源/接收資料儲存區組合複製活動支援的[資料移動活動](data-factory-data-movement-activities.md)文件。

資料工廠服務支援使用連線到內部部署 MongoDB 來源資料管理閘道器。 在設定閘道器移動資料的資料管線，請參閱若要深入瞭解資料管理閘道器的[資料管理閘道器](data-factory-data-management-gateway.md)文章和[移動來自內部部署至雲端的資料](data-factory-move-data-between-onprem-and-cloud.md)的文章，如需逐步指示。 

> [AZURE.NOTE] 您需要連線至 MongoDB，即使其會裝載於 Azure IaaS Vm 使用閘道器。 如果您嘗試執行個體 MongoDB 裝載於雲端的連線，您也可以在 IaaS VM 安裝的閘道執行個體。

資料工廠目前支援只移動資料 MongoDB 從其他資料儲存區中，但無法將資料從其他資料儲存區到 MongoDB。

## <a name="prerequisites"></a>必要條件
若要能夠連線到您的內部部署 MongoDB 資料庫 Azure 資料工廠服務，您必須安裝下列元件︰ 

- 資料管理閘道器 2.0 或同一台機器裝載資料庫的或不同的電腦，避免佔用與資料庫的資源。 資料管理閘道是安全和受管理的方式將內部部署資料來源連線至雲端服務的軟體。 請參閱[資料管理閘道器](data-factory-data-management-gateway.md)的文件的資料管理閘道器的詳細資料。
  
    當您安裝的閘道器時，它會自動安裝用來連線到 MongoDB Microsoft MongoDB ODBC 驅動程式。 

## <a name="copy-data-wizard"></a>複製資料精靈
建立管線從 MongoDB 資料庫的資料複製到任何支援的接收資料存放區，最簡單的方法是使用 [複製資料精靈。 請參閱[教學課程︰ 建立使用複製精靈的管線](data-factory-copy-data-wizard-tutorial.md)的快速建立管線，使用 [複製資料] 精靈的逐步解說。 

下列範例會提供可供您建立的管線使用[Azure 入口網站](data-factory-copy-activity-tutorial-using-azure-portal.md)或[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)或[PowerShell 的 Azure](data-factory-copy-activity-tutorial-using-powershell.md)範例 JSON 定義。 即會顯示如何將資料複製到 Azure Blob 儲存體 MongoDB 資料庫。 不過，可以複製資料至任何接收敘述[以下](data-factory-data-movement-activities.md#supported-data-stores)Azure 資料工廠中使用複製活動。

## <a name="sample-copy-data-from-mongodb-to-azure-blob"></a>範例︰ 資料複製 MongoDB 到 Azure Blob
此範例會示範如何將內部部署 MongoDB 資料庫將資料複製到 Azure Blob 儲存體。 不過，資料可以複製**直接**至任何接收敘述[以下](data-factory-data-movement-activities.md#supported-data-stores)Azure 資料工廠中使用複製活動。  
 
樣本具有下列資料工廠實體︰

1.  連結的類型[OnPremisesMongoDb](#linked-service-properties)的服務。
2.  連結的類型[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)的服務。
3.  輸入的[資料集](data-factory-create-datasets.md)的類型[MongoDbCollection](#dataset-type-properties)。
4.  輸出[資料集](data-factory-create-datasets.md)的類型[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)。
4.  [管線](data-factory-create-pipelines.md)與使用[MongoDbSource](#copy-activity-type-properties)及[BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)複製活動。

範例將資料從 MongoDB 資料庫中的查詢結果到 blob 每小時。 這些範例中所用的 JSON 屬性是由下列範例的各節所述。 

第一個步驟，設定資料管理閘道器，依照[資料管理閘道器](data-factory-data-management-gateway.md)文件中的指示進行。 

**連結的 MongoDB 服務**

    { 
        "name": "OnPremisesMongoDbLinkedService", 
        "properties": 
        { 
            "type": "OnPremisesMongoDb", 
            "typeProperties": 
            { 
                "authenticationType": "<Basic or Anonymous>", 
                "server": "< The IP address or host name of the MongoDB server >",  
                "port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>", 
                "username": "<username>", 
                "password": "<password>",
               "authSource": "< The database that you want to use to check your credentials for authentication. >",
                "databaseName": "<database name>",
                "gatewayName": "<mygateway>"
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

**MongoDB 輸入資料集**設定 「 外部 」:"true"通知資料工廠服務，表格外部資料 factory，並不會所產生的資料工廠中的活動。
    
    {
         "name":  "MongoDbInputDataset",
        "properties": { 
            "type": "MongoDbCollection", 
            "linkedServiceName": "OnPremisesMongoDbLinkedService", 
            "typeProperties": { 
                "collectionName": "<Collection name>"   
            }, 
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        } 
    }



**Azure Blob 輸出資料集**

每小時寫入資料至新 blob (頻率︰ 小時、 間隔︰ 1)。 根據正在處理的扇形區的開始時間，以動態方式評估 blob 的資料夾路徑。 年、 月、 日和開始時間的小時部分，則會使用的資料夾路徑。

    {
        "name": "AzureBlobOutputDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/frommongodb/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
                            "format": "%M"
                        }
                    },
                    {
                        "name": "Day",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "%d"
                        }
                    },
                    {
                        "name": "Hour",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "%H"
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

管線包含已設定為使用上述的輸入與輸出資料集複製活動和排程執行每小時。 在管線 JSON 定義，**來源**類型設定為 [ **MongoDbSource** ，**接收**類型] 設定為**BlobSink**。 指定**查詢**屬性的 SQL 查詢複製過去的小時內，選取資料。
    
    {
        "name": "CopyMongoDBToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "MongoDbSource",
                            "query": "$$Text.Format('select * from  MyTable where LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "MongoDbInputDataset"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutputDataSet"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "MongoDBToAzureBlob"
                }
            ],
            "start": "2016-06-01T18:00:00Z",
            "end": "2016-06-01T19:00:00Z"
        }
    }


## <a name="linked-service-properties"></a>連結的服務的內容

下表提供 JSON 項目的特定**OnPremisesMongoDB**連結服務描述。

| 屬性 | 描述 | 所需 |
| -------- | ----------- | -------- | 
| 類型 | [類型] 屬性必須設定為︰ **OnPremisesMongoDb** | [是] |
| 伺服器 | IP 位址或主機 MongoDB 伺服器名稱。 | [是] | 
| 連接埠 | MongoDB 伺服器接聽用戶端連線時所使用的 TCP 連接埠。 | 選擇性的預設值︰ 27017 |
| 識別碼 | 基本，或匿名。 | [是] | 
| 使用者名稱 | 若要存取 MongoDB 的使用者帳戶。 | 是] （如果使用基本驗證）。|
| 密碼 | 使用者的密碼。 |   是] （如果使用基本驗證）。 | 
| authSource | 您想要用來檢查您的認證驗證 MongoDB 資料庫的名稱。 | 選擇性 （如果使用基本驗證）。 預設︰ 使用的管理員帳戶，指定使用資料庫屬性的資料庫。 |  
| 資料庫名稱 | 您想要存取 MongoDB 資料庫的名稱。 | [是] |
| gatewayName | 存取資料存放區的閘道器的名稱。 | [是] | 
| encryptedCredential | 閘道器加密認證。 | 選擇性 |


如需詳細資訊設定針對內部部署 MongoDB 資料來源的認證，請參閱[設定認證與安全性](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security)。

## <a name="dataset-type-properties"></a>資料集類型屬性

節與適用於定義資料集的內容的完整清單，請參閱[建立資料集](data-factory-create-datasets.md)。 例如結構、 可用性和資料集 JSON 原則] 區段都是類似的所有資料集類型 (Azure SQL Azure blob、 Azure 資料表等等。)。

**TypeProperties**區段不同資料集的每個類型，並提供的資料存放區中的資料位置的相關資訊。 資料集的類型**MongoDbCollection** typeProperties 區段具有下列屬性︰

| 屬性 | 描述 | 所需 |
| -------- | ----------- | -------- |
| collectionName | 集合 MongoDB 資料庫中的名稱。 | [是] | 

## <a name="copy-activity-type-properties"></a>複製活動類型的屬性

節與適用於定義活動的內容的完整清單，請參閱[建立管線](data-factory-create-pipelines.md)文章。 例如名稱、 描述、 輸入與輸出的資料表，以及原則的內容，可針對所有類型的活動。 

另一方面活動**typeProperties**一節中提供的屬性會隨著每一個活動類型。 複製活動，而有所不同的來源和接收類型。

當來源類型**MongoDbSource**的下列屬性可 typeProperties] 區段中︰

| 屬性 | 描述 | 允許的值 | 所需 |
| -------- | ----------- | -------------- | -------- |
| 查詢 | 使用自訂查詢資料。 | SQL 92 查詢字串。 例如︰ 選取 * 從我的表格。 | 不需要 （如果指定的**資料集**的**collectionName** ） | 

## <a name="schema-by-data-factory"></a>資料工廠的結構描述
Azure 資料工廠服務推斷 MongoDB 集合中的結構描述，集合中使用的最新的 100 文件。 如果這些 100 的文件不包含完整的結構描述，某些資料欄可能會忽略複製作業期間。 

## <a name="type-mapping-for-mongodb"></a>MongoDB 類型對應

如上所述[資料移動活動](data-factory-data-movement-activities.md)文件中，複製活動會執行自動輸入轉換接收類型，使用下列步驟 2 方法來源類型︰

1. 從原生來源類型轉換成.NET 類型
2. 從.NET 類型轉換成原生接收類型

將資料移至 MongoDB 時下列對應用於 MongoDB 類型.NET 類型。

| MongoDB 類型 | .NET framework 類型 |
| ------------------- | ------------------- | 
| 二進位 | Byte] |
| 布林值 | 布林值 |
| 日期 | 日期時間 |
| NumberDouble | 點兩下 |
| NumberInt | Int32 |
| NumberLong | Int64 |
| ObjectID | 字串 |
| 字串 | 字串 |
| UUID | Guid | 
| 物件 | 重新正規化到簡維 「 _ 」 作為巢狀分隔符號的資料行 |

> [AZURE.NOTE]  
> 若要瞭解如何使用虛擬表格陣列的支援，請參閱下方的 [[使用虛擬資料表的複雜類型的支援](#support-for-complex-types-using-virtual-tables)章節。 

目前不支援下列 MongoDB 資料類型︰ DBPointer、 JavaScript、 Max/Min 按鍵，規則運算式、 符號、 時間戳記、 未定義

## <a name="support-for-complex-types-using-virtual-tables"></a>使用虛擬資料表的複雜類型的支援
Azure 資料工廠會使用內建的 ODBC 驅動程式連線至並複製 MongoDB 資料庫中的資料。 複雜的文件的不同類型例如陣列或物件類型，驅動程式重新正規化資料至對應的虛擬資料表。 明確地說，如果資料表包含這些資料行，驅動程式就會產生下列虛擬表格︰

-   **基底資料表**，其中包含相同的資料，除了複雜的類型] 欄的實際的資料表。 基底資料表使用它代表實際資料表使用相同的名稱。
-   **虛擬資料表**的每個複雜的類型] 欄，以展開的巢狀的資料。 虛擬資料表的命名使用名稱的實際的資料表與 「 _ 」 的分隔符號的陣列或物件的名稱。

虛擬表格參照實際的資料表，啟用存取正規化的資料驅動程式中的資料。 請參閱詳細資料] 下方的範例 > 一節。 您可以存取 MongoDB 陣列的內容查詢和加入虛擬的資料表。

您可以使用[複製] 精靈](data-factory-data-movement-activities.md#data-factory-copy-wizard)以直覺方式檢視 MongoDB 資料庫包括虛擬的表格中的 [表格的清單，並預覽內的資料。 您也可以建構複製精靈中的查詢，然後驗證來查看結果。

### <a name="example"></a>範例

例如，「 ExampleTable 」 下方是 MongoDB 表格中有一個資料行具有物件的陣列，每個儲存格中 – 發票和一個資料行具有純量類型 – 評等的陣列。 

_id | 客戶名稱 | 發票 | 服務層級 | 評等
--- | ------------- | -------- | ------------- | -------
1111 | ABC | [{invoice_id: 「 123 」 的項目: 」 toaster 」、 價格: 」 456 」，折扣: 「 0.2 」}，{invoice_id: 「 124 」 的項目: 「 烤箱 」、 價格: 」 1235 」，折扣: 「 0.2 」}] | 「 銀色 」 | [5,6]
2222 | XYZ | [{invoice_id: 「 135 」 的項目: 「 冰箱 」、 價格: 「 12543 」，折扣: 「 0.0 」}] | Gold | [1，2]

驅動程式會產生多個虛擬代表此單一資料表的資料表。 第一個虛擬資料表是基底表格命名為 「 ExampleTable 」，以下所示。 基底資料表包含原始資料表的所有資料，但陣列中的資料已被省略了，並展開虛擬資料表中。

_id | 客戶名稱 | 服務層級
--- | ------------- | -------------
1111 | ABC | 「 銀色 」
2222 | XYZ | Gold

下表顯示代表原始的陣列範例中的虛擬資料表。 下列表格包含下列各項︰ 

- 回到原始的主索引鍵欄對應到資料列 （透過 _id 欄） 的原始陣列的參照
- 原始的陣列中資料的位置的指示 
- 展開的陣列中的每個項目資料

「 ExampleTable_Invoices 」 的資料表︰

_id | ExampleTable_Invoices_dim1_idx | invoice_id | 項目 | 價格 | 折扣
--- | -------------- | ---------- | ---- | ----- | --------
1111 | 0 | 123 | toaster | 456 | 0.2
1111 | 1 | 124 | 烤箱 | 1235 | 0.2
2222 | 0 | 135 | 冰箱 | 12543 | 0.0

「 ExampleTable_Ratings 」 的資料表︰

_id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings
--- | ------------- | -------------
1111 | 0 | 5
1111 | 1 | 6
2222 | 0 | 1
2222 | 1 | 2

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>效能及調整  
請參閱[複製活動的效能與微調指南](data-factory-copy-activity-performance.md)，瞭解如何要素 Azure 資料工廠，並將它最佳化的各種方式中的資料移動 （複製活動） 的效能的影響。

## <a name="next-steps"></a>後續步驟
請參閱[移動內部部署與雲端之間的資料](data-factory-move-data-between-onprem-and-cloud.md)文件以建立資料管線的逐步指示將資料從內部部署資料存放區移到 Azure 資料存放區。 

