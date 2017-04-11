<properties 
    pageTitle="將資料從 Amazon Redshift 使用資料工廠移 |Microsoft Azure" 
    description="進一步瞭解如何將資料從 Amazon Redshift 使用 Azure 資料工廠移。" 
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
    ms.date="08/23/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>移動資料 Amazon Redshift 」 從使用 Azure 資料工廠

本文概述如何使用複製活動 Azure 資料工廠移動到另一個資料 Amazon Redshift 從儲存的資料。 本文是根據呈現資料移動的一般概觀複製活動與來源/接收資料存放區清單的[資料移動活動](data-factory-data-movement-activities.md)文件。  

資料工廠目前支援只移動資料從 Amazon Redshift 其他資料儲存區中，但無法將資料從其他資料儲存區到 Amazon Redshift。

## <a name="prerequisites"></a>必要條件

- 如果您要移動資料至內部部署資料存放區，資料管理閘道器 （使用電腦的 IP 位址） 將存取權授予 Amazon Redshift 叢集。 如需相關指示，請參閱[叢集授權存取](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html)。 
- 如果您要移動資料至 Azure 資料存放區，請參閱[Azure 資料中心的 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)計算 IP 位址範圍 （包括 SQL 範圍） 使用 Microsoft Azure 資料中心。 

## <a name="copy-data-wizard"></a>複製資料精靈
建立管線將資料從 Amazon Redshift 複製最簡單的方法是使用 [複製資料精靈。 請參閱[教學課程︰ 建立使用複製精靈的管線](data-factory-copy-data-wizard-tutorial.md)的快速建立管線，使用 [複製資料] 精靈的逐步解說。 

下列範例會提供可供您建立的管線使用[Azure 入口網站](data-factory-copy-activity-tutorial-using-azure-portal.md)或[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)或[PowerShell 的 Azure](data-factory-copy-activity-tutorial-using-powershell.md)範例 JSON 定義。 它會顯示您如何 Amazon Redshift 將資料複製到 Azure Blob 儲存體。 不過，可以接收敘述[以下](data-factory-data-movement-activities.md#supported-data-stores)的任何複製資料。

## <a name="sample-copy-data-from-amazon-redshift-to-azure-blob"></a>範例︰ 從 Amazon Redshift 資料複製到 Azure Blob
此範例會示範如何將資料從 Amazon Redshift 資料庫複製到 [Azure Blob 儲存體。 不過，資料可以複製**直接**至任何接收敘述[以下](data-factory-data-movement-activities.md#supported-data-stores)Azure 資料工廠中使用複製活動。  
 
樣本具有下列資料工廠實體︰

- 連結的類型[AmazonRedshift](#linked-service-properties)的服務。
- 連結的類型[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)的服務。
- 輸入的[資料集](data-factory-create-datasets.md)的類型[RelationalTable](#dataset-type-properties)。
- 輸出[資料集](data-factory-create-datasets.md)的類型[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)。
- [管線](data-factory-create-pipelines.md)與使用[RelationalSource](#copy-activity-type-properties)及[BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)複製活動。

範例將資料從查詢結果中 Amazon Redshift 至 blob 每小時。 這些範例中所用的 JSON 屬性是由下列範例的各節所述。 

**Amazon Redshift 連結服務**

    {
        "name": "AmazonRedshiftLinkedService",
        "properties":
        {
            "type": "AmazonRedshift",
            "typeProperties":
            {
                "server": "< The IP address or host name of the Amazon Redshift server >",
                "port": <The number of the TCP port that the Amazon Redshift server uses to listen for client connections.>,
                "database": "<The database name of the Amazon Redshift database>",
                "username": "<username>",
                "password": "<password>"
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

**Amazon Redshift 輸入資料集**

設定**「 外部 」: true**通知資料工廠服務資料集外部資料 factory，並不會所產生的資料工廠中的活動。 設定此屬性設為 true 上不會產生管道活動所輸入資料集。

    {
        "name": "AmazonRedshiftInputDataset",
        "properties": {
            "type": "RelationalTable",
            "linkedServiceName": "AmazonRedshiftLinkedService",
            "typeProperties": {
                "tableName": "<Table name>"
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
                "folderPath": "mycontainer/fromamazonredshift/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

管線包含複製活動，設定為使用的輸入與輸出資料集，並排定執行每小時。 在管線 JSON 定義，**來源**類型設定為 [ **RelationalSource** ，**接收**類型] 設定為**BlobSink**。 指定**查詢**屬性的 SQL 查詢複製過去的小時內，選取資料。
    
    {
        "name": "CopyAmazonRedshiftToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AmazonRedshiftInputDataset"
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
                    "name": "AmazonRedshiftToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }



## <a name="linked-service-properties"></a>連結的服務的內容

下表提供 JSON 項目的特定 Amazon Redshift 連結服務描述。

| 屬性 | 描述 | 所需 |
| -------- | ----------- | -------- | 
| 類型 | [類型] 屬性必須設定為︰ **AmazonRedshift**。 | [是] |
| 伺服器 | IP 位址或主機 Amazon Redshift 伺服器名稱。 | [是] |
| 連接埠 | TCP 連接埠 Amazon Redshift 伺服器接聽用戶端連線時所使用的數字。 | 否，預設值︰ 5439 |
| 資料庫 | Amazon Redshift 資料庫的名稱。 | [是] |
| 使用者名稱 | 具有資料庫的存取權的使用者名稱。| [是] |
| 密碼 | 使用者帳戶的密碼。| [是] |


## <a name="dataset-type-properties"></a>資料集類型屬性

節與適用於定義資料集的內容的完整清單，請參閱[建立資料集](data-factory-create-datasets.md)。 例如結構、 可用性及原則] 區段都是類似的所有資料集類型 (Azure SQL Azure blob、 Azure 資料表等等。)。

**TypeProperties**區段不同資料集的每個類型，並提供的資料存放區中的資料位置的相關資訊。 類型**RelationalTable** （包括 Amazon Redshift 資料集） 的資料集 typeProperties 區段有下列屬性

| 屬性 | 描述 | 所需 |
| -------- | ----------- | -------- |
| 表格名稱 | 參照中的連結服務 Amazon Redshift 資料庫資料表的名稱。 | 不需要 （如果有指定**查詢**的**RelationalSource** ） | 

## <a name="copy-activity-type-properties"></a>複製活動類型的屬性

節與適用於定義活動的內容的完整清單，請參閱[建立管線](data-factory-create-pipelines.md)文章。 針對所有類型的活動有內容，例如名稱、 描述、 輸入與輸出資料表及原則。 

另一方面活動**typeProperties**一節中提供的屬性會隨著每一個活動類型。 複製活動，而有所不同的來源和接收類型。

當複製活動的來源類型**RelationalSource** （包括 Amazon Redshift） 的下列屬性可 typeProperties] 區段中︰

| 屬性 | 描述 | 允許的值 | 所需 |
| -------- | ----------- | -------------- | -------- |
| 查詢 | 使用自訂查詢資料。 | SQL 查詢字串。 例如︰ 選取 * 從我的表格。 | 不需要 （如果指定的**資料集**的**表格名稱**） | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-amazon-redshift"></a>Amazon Redshift 類型對應

如上所述[資料移動活動](data-factory-data-movement-activities.md)文件中，複製活動會執行自動輸入轉換接收類型的下列兩種方式與來源類型︰

1. 從原生來源類型轉換成.NET 類型
2. 從.NET 類型轉換成原生接收類型

將資料移到 [Amazon Redshift，下列對應將會用於 Amazon Redshift 類型.NET 類型。

Amazon Redshift 類型 | .NET 基礎類型
-------------------- | ---------------
小 | Int16
整數 | Int32
BIGINT | Int64
小數位數 | 小數位數
實數 | 單一
雙精確度 | 點兩下
布林值 | 字串
字元 | 字串
VARCHAR | 字串
日期 | 日期時間
時間戳記 | 日期時間
文字 | 字串



[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>效能及調整  
請參閱[複製活動的效能與微調指南](data-factory-copy-activity-performance.md)，瞭解如何要素 Azure 資料工廠，並將它最佳化的各種方式中的資料移動 （複製活動） 的效能的影響。

## <a name="next-steps"></a>後續步驟
請參閱下列文章︰ 
- 如需逐步指示建立含複製活動的管線[複製活動教學課程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。 