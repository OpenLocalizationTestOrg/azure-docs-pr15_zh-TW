<properties 
    pageTitle="移動資料來源的 OData |Azure 資料工廠" 
    description="瞭解如何將資料來源的 OData 使用 Azure 資料工廠。" 
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
    ms.date="09/26/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-a-odata-source-using-azure-data-factory"></a>移動資料使用 Azure 資料工廠 OData 從來源
本文概述如何使用複製活動 Azure 資料工廠，將資料來源的 OData 移到另一個資料存放區。 本文是根據呈現資料移動的一般概觀複製活動與支援的資料存放區組合的[資料移動活動](data-factory-data-movement-activities.md)文件。

> [AZURE.NOTE] OData 連接器支援複製 OData 兩個雲端的資料和內部部署 OData 來源。 對於後者，您需要安裝資料管理閘道器。 請參閱[移動內部部署與雲端之間的資料](data-factory-move-data-between-onprem-and-cloud.md)文件的詳細資料管理閘道器。

## <a name="copy-data-wizard"></a>複製資料精靈
建立管線複製資料來源的 OData 的最簡單的方法是使用 [複製資料精靈。 請參閱[教學課程︰ 建立使用複製精靈的管線](data-factory-copy-data-wizard-tutorial.md)的快速建立管線，使用 [複製資料] 精靈的逐步解說。 

下列範例會提供可供您建立的管線使用[Azure 入口網站](data-factory-copy-activity-tutorial-using-azure-portal.md)或[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)或[PowerShell 的 Azure](data-factory-copy-activity-tutorial-using-powershell.md)範例 JSON 定義。 即會顯示如何將資料來源的 OData 複製至 Azure Blob 儲存體。 不過，可以複製資料至任何接收敘述[以下](data-factory-data-movement-activities.md#supported-data-stores)Azure 資料工廠中使用複製活動。


## <a name="sample-copy-data-from-odata-source-to-azure-blob"></a>範例︰ 複製資料來源的 OData Azure Blob

此範例會示範如何將資料來源的 OData 複製到 [Azure Blob 儲存體。 不過，資料可以複製**直接**至任何接收敘述[以下](data-factory-data-movement-activities.md#supported-data-stores)Azure 資料工廠中使用複製活動。  
 
樣本具有下列資料工廠實體︰

1.  連結的類型[OData](#odata-linked-service-properties)的服務。
2.  連結的類型[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)的服務。
3.  輸入的[資料集](data-factory-create-datasets.md)的類型[ODataResource](#odata-dataset-type-properties)。
4.  輸出[資料集](data-factory-create-datasets.md)的類型[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)。
4.  [管線](data-factory-create-pipelines.md)與使用[RelationalSource](#odata-copy-activity-type-properties)及[BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)複製活動。

此範例會將資料從查詢 Azure blob OData 來源每小時。 這些範例中所用的 JSON 屬性是由下列範例的各節所述。 

**OData 連結服務**此範例使用基本驗證。 請參閱驗證您可以使用的不同類型的[OData 連結服務](#odata-linked-service-properties)一節。 

    {
        "name": "ODataLinkedService",
        "properties": 
        {
            "type": "OData",
            "typeProperties": 
            {
                "url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Anonymous"
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

**OData 輸入資料集**

設定 「 外部 」:"true"通知資料工廠服務，資料集外部資料 factory，並不會所產生的資料工廠中的活動。
    
    {
        "name": "ODataDataset",
        "properties": 
        {
            "type": "ODataResource",
            "typeProperties": 
            {
                "path": "Products" 
            },
            "linkedServiceName": "ODataLinkedService",
            "structure": [],
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3               
            }
        }
    }

在資料集中指定**路徑**定義為選用步驟。 


**Azure Blob 輸出資料集**

每小時寫入資料至新 blob (頻率︰ 小時、 間隔︰ 1)。 根據正在處理的扇形區的開始時間，以動態方式評估 blob 的資料夾路徑。 年、 月、 日和開始時間的小時部分，則會使用的資料夾路徑。

    {
        "name": "AzureBlobODataDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/odata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

管線包含複製活動，設定為使用的輸入與輸出資料集，並排定執行每小時。 在管線 JSON 定義，**來源**類型設定為 [ **RelationalSource** ，**接收**類型] 設定為**BlobSink**。 指定**查詢**屬性的 SQL 查詢選取的最新的 （從最新） 資料來源的 OData。
    
    {
        "name": "CopyODataToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "?$select=Name, Description&$top=5",
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "ODataDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobODataDataSet"
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
                    "name": "ODataToBlob"
                }
            ],
            "start": "2016-02-01T18:00:00Z",
            "end": "2016-02-03T19:00:00Z"
        }
    }


指定**查詢**中的管線定義為選用步驟。 資料工廠服務用來擷取資料的**URL** ︰ 連結服務 （必填） 中所指定的 URL + 資料集 （可省略） + （可省略） 管道的查詢中指定的路徑。 

## <a name="odata-linked-service-properties"></a>OData 連結服務的內容

下表提供 JSON 項目的特定 OData 連結服務描述。

| 屬性 | 描述 | 所需 |
| -------- | ----------- | -------- | 
| 類型 | [類型] 屬性必須設定為︰ **OData** | [是] |
| url| OData 服務的 Url。 | [是] |
| 識別碼 | 用來連線到 OData 來源的驗證類型。 <br/><br/> 雲端 OData 的可能值的匿名與 Basic。 內部部署 OData 的可能值是匿名、 基本、 和 Windows。 | [是] | 
| 使用者名稱 | 如果您使用基本驗證，指定使用者名稱。 | 是 （僅限如果您使用基本驗證） | 
| 密碼 | 指定您所指定的使用者名稱的使用者帳戶的密碼。 | 是 （僅限如果您使用基本驗證） | 
| gatewayName | 資料工廠服務應使用連線至內部部署 OData 服務的閘道器的名稱。 如果您要複製的資料來源的內部部署 OData 只指定。 | 無 |

### <a name="using-basic-authentication"></a>使用基本驗證

    {
        "name": "inputLinkedService",
        "properties": 
        {
            "type": "OData",
            "typeProperties": 
            {
               "url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Basic",
                "username": "username",
               "password": "password"
           }
       }
    }

### <a name="using-anonymous-authentication"></a>使用匿名驗證
    
    {
        "name": "ODataLinkedService",
        "properties": 
        {
            "type": "OData",
            "typeProperties": 
            {
               "url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Anonymous"
           }
       }
    }

### <a name="using-windows-authentication-accessing-on-premises-odata-source"></a>使用 Windows 驗證存取內部部署 OData 來源

    {
        "name": "inputLinkedService",
        "properties": 
        {
            "type": "OData",
            "typeProperties": 
            {
               "url": "<endpoint of on-premises OData source e.g. Dynamics CRM>",
               "authenticationType": "Windows",
                "username": "domain\\user",
               "password": "password",
               "gatewayName": "mygateway"
           }
       }
    }



## <a name="odata-dataset-type-properties"></a>OData 資料集類型屬性

節與適用於定義資料集的內容的完整清單，請參閱[建立資料集](data-factory-create-datasets.md)。 例如結構、 可用性和資料集 JSON 原則] 區段都是類似的所有資料集類型 (Azure SQL Azure blob、 Azure 資料表等等。)。

**TypeProperties**區段不同資料集的每個類型，並提供的資料存放區中的資料位置的相關資訊。 類型**ODataResource** （包括 OData 資料集） 的資料集 typeProperties 區段有下列屬性

| 屬性 | 描述 | 所需 |
| -------- | ----------- | -------- |
| 路徑 | OData 資源的路徑 | 無 | 

## <a name="odata-copy-activity-type-properties"></a>OData 複製活動類型的屬性

節與適用於定義活動的內容的完整清單，請參閱[建立管線](data-factory-create-pipelines.md)文章。 例如名稱、 描述、 輸入與輸出的資料表，以及原則的內容，可針對所有類型的活動。 

活動 typeProperties 一節中提供的屬性另一方面會隨著每個活動類型。 複製活動，而有所不同的來源和接收類型。

當來源類型**RelationalSource** （包括 OData） 的下列屬性可 typeProperties] 區段中︰

| 屬性 | 描述 | 範例 | 所需 |
| -------- | ----------- | -------------- | -------- |
| 查詢 | 使用自訂查詢資料。 | 「？ $select = 名稱、 描述與 $top = 5 」 | 無 | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-odata"></a>OData 類型對應

如上所述[資料移動活動](data-factory-data-movement-activities.md)文件中，複製活動會執行自動輸入轉換接收類型，使用下列步驟 2 方法來源類型︰

1. 從原生來源類型轉換成.NET 類型
2. 從.NET 類型轉換成原生接收類型

移動資料從 OData 資料當儲存時，就會將 OData 資料類型對應到.NET 類型。


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>效能及調整  
請參閱[複製活動的效能與微調指南](data-factory-copy-activity-performance.md)，瞭解如何要素 Azure 資料工廠，並將它最佳化的各種方式中的資料移動 （複製活動） 的效能的影響。

