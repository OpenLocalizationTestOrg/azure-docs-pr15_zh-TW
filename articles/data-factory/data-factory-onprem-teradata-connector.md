<properties 
    pageTitle="將資料從 Teradata 移 |Azure 資料工廠" 
    description="瞭解資料工廠服務可讓您將資料從 Teradata 資料庫移 Teradata 連接器" 
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
    ms.date="09/20/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-teradata-using-azure-data-factory"></a>將資料從 Teradata 使用 Azure 資料工廠移動

本文概述如何使用複製活動 Azure 資料工廠移動到另一個資料 Teradata 從儲存的資料。 本文是根據呈現資料移動的一般概觀複製活動與支援的資料存放區組合的[資料移動活動](data-factory-data-movement-activities.md)文件。

資料工廠支援連線到內部部署 Teradata 來源透過資料管理閘道器。 請參閱[內部部署的位置與雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md)文章，進一步瞭解資料管理閘道器和設定閘道器的逐步指示。 

> [AZURE.NOTE]
即使 Teradata 裝載於 Azure IaaS VM 需要閘道器。 您可以安裝閘道器或不同的 VM 相同 IaaS VM 做為資料儲存區上，只要閘道器可以連線至資料庫。 

資料工廠支援只移動資料從其他資料儲存區 Teradata，而非至 Teradata 其他資料存放區。

## <a name="installation"></a>安裝 

資料管理閘道器連線到 Teradata 資料庫，您需要安裝資料管理閘道器與相同系統[Teradata 的.NET 資料提供者](http://go.microsoft.com/fwlink/?LinkId=278886)。

> [AZURE.NOTE] 如需疑難排解連線/閘道器的秘訣請參閱[疑難排解閘道器問題](data-factory-data-management-gateway.md#troubleshoot-gateway-issues)相關問題。 

## <a name="copy-data-wizard"></a>複製資料精靈
建立管線將資料從 Teradata 到任何支援的接收資料存放區，最簡單的方法是使用 [複製資料精靈。 請參閱[教學課程︰ 建立使用複製精靈的管線](data-factory-copy-data-wizard-tutorial.md)的快速建立管線，使用 [複製資料] 精靈的逐步解說。 

下列範例會提供可供您建立的管線使用[Azure 入口網站](data-factory-copy-activity-tutorial-using-azure-portal.md)或[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)或[PowerShell 的 Azure](data-factory-copy-activity-tutorial-using-powershell.md)範例 JSON 定義。 即會顯示如何將 Teradata 資料複製到 Azure Blob 儲存體。 不過，可以複製資料至任何接收敘述[以下](data-factory-data-movement-activities.md#supported-data-stores)Azure 資料工廠中使用複製活動。   

### <a name="sample-copy-data-from-teradata-to-azure-blob"></a>範例︰ 從 Teradata 資料複製到 Azure Blob

此範例會示範如何將 Teradata 資料庫資料複製到 Azure Blob 儲存體。 不過，資料可以複製**直接**至任何接收敘述[以下](data-factory-data-movement-activities.md#supported-data-stores)Azure 資料工廠中使用複製活動。  
 
樣本具有下列資料工廠實體︰

1.  連結的類型[OnPremisesTeradata](data-factory-onprem-teradata-connector.md#teradata-linked-service-properties)的服務。
2.  連結的類型[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)的服務。
3.  輸入的[資料集](data-factory-create-datasets.md)的類型[RelationalTable](data-factory-onprem-teradata-connector.md#teradata-dataset-type-properties)。
4.  輸出[資料集](data-factory-create-datasets.md)的類型[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)。 
4.  [管線](data-factory-create-pipelines.md)與使用[RelationalSource](data-factory-onprem-teradata-connector.md#teradata-copy-activity-type-properties)及[BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)複製活動。

範例將資料從 Teradata 資料庫中的查詢結果到 blob 每小時。 這些範例中所用的 JSON 屬性是由下列範例的各節所述。 

第一個步驟，設定資料管理閘道器。 其指示都[位置內部部署與雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md)文件中。

**Teradata 連結服務︰**

    {
        "name": "OnPremTeradataLinkedService",
        "properties": {
            "type": "OnPremisesTeradata",
            "typeProperties": {
                "server": "<server>",
                "authenticationType": "<authentication type>",
                "username": "<username>",
                "password": "<password>",
                "gatewayName": "<gatewayName>"
            }
        }
    }

**Azure Blob 儲存體連結服務︰**

    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorageLinkedService",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
            }
        }
    }


**Teradata 輸入資料集︰**

範例假設表格 「 我的表格] 中建立 Teradata，並將其包含稱為 「 時間戳記 」 時間] 資料數列的資料行。

設定 「 外部 」: true，則會通知資料工廠服務表格外部資料 factory，並不會所產生的資料工廠中的活動。

    {
        "name": "TeradataDataSet",
        "properties": {
            "published": false,
            "type": "RelationalTable",
            "linkedServiceName": "OnPremTeradataLinkedService",
            "typeProperties": {
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }


**Azure Blob 輸出資料集︰**

每小時寫入資料至新 blob (頻率︰ 小時、 間隔︰ 1)。 根據正在處理的扇形區的開始時間，以動態方式評估 blob 的資料夾路徑。 年、 月、 日和開始時間的小時部分，則會使用的資料夾路徑。

    {
        "name": "AzureBlobTeradataDataSet",
        "properties": {
            "published": false,
            "location": {
                "type": "AzureBlobLocation",
                "folderPath": "mycontainer/teradata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
                ],
                "linkedServiceName": "AzureStorageLinkedService"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }


**管線與複製活動︰**

管線包含複製活動，設定為使用的輸入與輸出資料集，並排定執行每小時。 在管線 JSON 定義，**來源**類型設定為 [ **RelationalSource** ，**接收**類型] 設定為**BlobSink**。 指定**查詢**屬性的 SQL 查詢複製過去的小時內，選取資料。

    {
        "name": "CopyTeradataToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', SliceStart, SliceEnd)"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "TeradataDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobTeradataDataSet"
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
                    "name": "TeradataToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z",
            "isPaused": false
        }
    }


## <a name="teradata-linked-service-properties"></a>Teradata 連結服務屬性

下表提供 JSON 項目的特定 Teradata 連結服務描述。 

屬性 | 描述 | 所需
-------- | ----------- | --------
類型 | [類型] 屬性必須設定為︰ **OnPremisesTeradata** | [是]
伺服器 | Teradata 伺服器的名稱。 | [是]
識別碼 | 用來連線到 Teradata 資料庫的驗證類型。 可能的值︰ 匿名、 基本、 和 Windows。 | [是]
使用者名稱 | 如果您使用基本 」 或 「 Windows 驗證，指定使用者名稱。 | 無 
密碼 | 指定您所指定的使用者名稱的使用者帳戶的密碼。 | 無 
gatewayName | 閘道器的資料工廠服務應使用連線到內部部署 Teradata 資料庫的名稱。 | [是]

如需詳細資訊設定針對內部部署 Teradata 資料來源的認證，請參閱[設定認證與安全性](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security)。

## <a name="teradata-dataset-type-properties"></a>Teradata 資料集類型屬性

節與適用於定義資料集的內容的完整清單，請參閱[建立資料集](data-factory-create-datasets.md)。 例如結構、 可用性和資料集 JSON 原則] 區段都是類似的所有資料集類型 (Azure SQL Azure blob、 Azure 資料表等等。)。

**TypeProperties**區段不同資料集的每個類型，並提供的資料存放區中的資料位置的相關資訊。 目前沒有支援 Teradata 資料集的類型屬性。 


## <a name="teradata-copy-activity-type-properties"></a>Teradata 複製活動類型的內容

節與適用於定義活動的內容的完整清單，請參閱[建立管線](data-factory-create-pipelines.md)文章。 針對所有類型的活動有內容，例如名稱、 描述、 輸入與輸出資料表及原則。 

活動 typeProperties 一節中提供的屬性另一方面會隨著每個活動類型。 複製活動，而有所不同的來源和接收類型。

當來源類型**RelationalSource** （包括 Teradata） 的下列屬性可**typeProperties** ] 區段中︰

屬性 | 描述 | 允許的值 | 所需
-------- | ----------- | -------------- | --------
查詢 | 使用自訂查詢資料。 | SQL 查詢字串。 例如︰ 選取 * 從我的表格。 | [是]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="type-mapping-for-teradata"></a>Teradata 的類型對應

如上所述[資料移動活動](data-factory-data-movement-activities.md)文件中，複製活動會執行自動輸入轉換接收類型，使用下列步驟 2 方法來源類型︰

1. 從原生來源類型轉換成.NET 類型
2. 從.NET 類型轉換成原生接收類型

將資料移到 Teradata 中，下列對應用於 Teradata 類型.NET 類型。

Teradata 資料庫類型 | .NET framework 類型
----------------- | ---------------------------
字元 | 字串
Clob | 字串
圖形 | 字串
VarChar | 字串
VarGraphic | 字串
Blob | Byte]
位元組 | Byte]
VarByte | Byte]
BigInt | Int64
ByteInt | Int16
小數位數 | 小數位數
點兩下 | 點兩下
整數 | Int32
數字 | 點兩下
小 | Int16
日期 | 日期時間
時間 | 時段
時區的時間 | 字串
時間戳記 | 日期時間
時區的時間戳記 | DateTimeOffset
間隔日 | 時段
以小時的時間間隔日 | 時段
分鐘的時間間隔日 | 時段
以秒間隔日 | 時段
間隔小時 | 時段
分鐘的時間間隔小時 | 時段
以秒間隔小時 | 時段
間隔分鐘 | 時段
以秒間隔分鐘 | 時段
間隔第二個 | 時段
間隔年 | 字串
以月間隔年 | 字串
間隔月 | 字串
Period(Date) | 字串
Period(Time) | 字串
期間 （時區的時間） | 字串
Period(Timestamp) | 字串
期間 （時區的時間戳記） | 字串
Xml | 字串

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>效能及調整  
請參閱[複製活動的效能與微調指南](data-factory-copy-activity-performance.md)，瞭解如何要素 Azure 資料工廠，並將它最佳化的各種方式中的資料移動 （複製活動） 的效能的影響。