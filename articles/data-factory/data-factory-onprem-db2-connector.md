<properties 
    pageTitle="將資料從 DB2 移 |Azure 資料工廠" 
    description="進一步瞭解如何將使用 Azure 資料工廠 DB2 資料庫中的資料" 
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

# <a name="move-data-from-db2-using-azure-data-factory"></a>使用 Azure 資料工廠 DB2 移動資料
本文概述如何使用複製活動 Azure 資料工廠移動到另一個資料 DB2 從儲存的資料。 本文是根據呈現資料移動的一般概觀複製活動與支援的資料存放區組合的[資料移動活動](data-factory-data-movement-activities.md)文件。

資料工廠支援使用連線到內部部署 DB2 來源資料管理閘道器。 請參閱[內部部署的位置與雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md)文章，進一步瞭解資料管理閘道器和設定閘道器的逐步指示。 

> [AZURE.NOTE]
使用連線至 DB2，即使其會裝載於 Azure IaaS Vm 的閘道器。 如果您嘗試執行個體 DB2 裝載於雲端的連線，您也可以在 IaaS VM 安裝的閘道執行個體。

資料工廠目前支援只移動資料從其他資料儲存區 DB2，而非 DB2 至其他資料存放區。 

## <a name="installation"></a>安裝 

資料管理閘道器提供內建的 DB2 驅動程式支援下列動作︰ 

- SQLAM 9 / 10 / 11
- DB2 的 LUW (Linux，Unix，Windows)
- Z/OS 和 DB2 的 DB2 我 (又稱為 / 400)

因此您不再需要從 DB2 複製資料時，手動安裝驅動程式。

> [AZURE.NOTE] 如需疑難排解連線/閘道器的秘訣請參閱[疑難排解閘道器問題](data-factory-data-management-gateway.md#troubleshoot-gateway-issues)相關問題。 

## <a name="copy-data-wizard"></a>複製資料精靈
若要建立將資料從 DB2 資料庫的管線最簡單的方法是使用複製資料精靈。 請參閱[教學課程︰ 建立使用複製精靈的管線](data-factory-copy-data-wizard-tutorial.md)的快速建立管線，使用 [複製資料] 精靈的逐步解說。 

下列範例會提供可供您建立的管線使用[Azure 入口網站](data-factory-copy-activity-tutorial-using-azure-portal.md)或[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)或[PowerShell 的 Azure](data-factory-copy-activity-tutorial-using-powershell.md)範例 JSON 定義。 即會顯示如何將資料複製 DB2 資料庫及 Azure Blob 儲存體。 不過，可以複製資料至任何接收敘述[以下](data-factory-data-movement-activities.md#supported-data-stores)Azure 資料工廠中使用複製活動。

## <a name="sample-copy-data-from-db2-to-azure-blob"></a>範例︰ 資料複製 DB2 Azure Blob

此範例會示範如何將內部部署 DB2 資料庫資料複製到 Azure Blob 儲存體。 不過，資料可以複製**直接**至任何接收敘述[以下](data-factory-data-movement-activities.md#supported-data-stores)Azure 資料工廠中使用複製活動。  
 
樣本具有下列資料工廠實體︰

1.  連結的類型[OnPremisesDb2](data-factory-onprem-db2-connector.md#db2-linked-service-properties)的服務。
2.  連結的類型[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)的服務。 
3.  輸入的[資料集](data-factory-create-datasets.md)的類型[RelationalTable](data-factory-onprem-db2-connector.md#db2-dataset-type-properties)。
4.  輸出[資料集](data-factory-create-datasets.md)的類型[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)。 
5.  [管線](data-factory-create-pipelines.md)與使用[RelationalSource](data-factory-onprem-db2-connector.md#db2-copy-activity-type-properties)及[BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)複製活動。 

範例將資料從 DB2 資料庫中的查詢結果到 Azure blob 每小時。 這些範例中所用的 JSON 屬性是由下列範例的各節所述。 

第一個步驟是，安裝和設定資料管理閘道器。 [位置內部部署與雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md)文件中的指示。

**連結的 DB2 服務︰**

    {
        "name": "OnPremDb2LinkedService",
        "properties": {
            "type": "OnPremisesDb2",
            "typeProperties": {
                "server": "<server>",
                "database": "<database>",
                "schema": "<schema>",
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

**輸入資料集 DB2:**

範例假設表格 「 我的表格] 中建立 DB2 和其包含的時間數列資料稱為 「 時間戳記] 資料行。

設定 「 外部 」: true 通知資料工廠服務，此資料集外部資料 factory，並不會所產生的資料工廠中的活動。 請注意，**類型**設定為 [ **RelationalTable**。 

    {
        "name": "Db2DataSet",
        "properties": {
            "type": "RelationalTable",
            "linkedServiceName": "OnPremDb2LinkedService",
            "typeProperties": {},
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
        "name": "AzureBlobDb2DataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/db2/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**管線與複製活動︰**

管線包含複製活動，設定為使用的輸入與輸出資料集，並排定執行每小時。 在管線 JSON 定義，**來源**類型設定為 [ **RelationalSource** ，**接收**類型] 設定為**BlobSink**。 指定**查詢**屬性的 SQL 查詢會從 [訂單] 資料表中選取資料。

    {
        "name": "CopyDb2ToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "select * from \"Orders\""
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    },
                    "inputs": [
                        {
                            "name": "Db2DataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobDb2DataSet"
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
                    "name": "Db2ToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }


## <a name="db2-linked-service-properties"></a>連結的 DB2 服務屬性

下表提供 JSON 項目的特定 DB2 連結服務描述。 

| 屬性 | 描述 | 所需 |
| -------- | ----------- | -------- | 
| 類型 | [類型] 屬性必須設定為︰ **OnPremisesDB2** | [是] |
| 伺服器 | DB2 伺服器的名稱。 | [是] |
| 資料庫 | DB2 資料庫的名稱。 | [是] |
| 結構描述 | 在資料庫中的結構描述的名稱。 結構描述名稱是區分大小寫。 | 無 |
| 識別碼 | 用來連線到 DB2 資料庫的驗證類型。 可能的值︰ 匿名、 基本、 和 Windows。 | [是] |
| 使用者名稱 | 如果您使用基本 」 或 「 Windows 驗證，指定使用者名稱。 | 無 |
| 密碼 | 指定您所指定的使用者名稱的使用者帳戶的密碼。 | 無 |
| gatewayName | 閘道器的資料工廠服務應使用連線到內部部署 DB2 資料庫的名稱。 | [是] |

如需詳細資訊設定針對內部部署 DB2 資料來源的認證，請參閱[設定認證與安全性](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security)。 


## <a name="db2-dataset-type-properties"></a>DB2 資料集類型屬性

節與適用於定義資料集的內容的完整清單，請參閱[建立資料集](data-factory-create-datasets.md)。 例如結構、 可用性和資料集 JSON 原則] 區段都是類似的所有資料集類型 (Azure SQL Azure blob、 Azure 資料表等等。)。

TypeProperties 區段不同資料集的每個類型，並提供的資料存放區中的資料位置的相關資訊。 類型 （包括 DB2 資料集） RelationalTable 資料集的 typeProperties 區段有下列屬性。

| 屬性 | 描述 | 所需 |
| -------- | ----------- | -------- | 
| 表格名稱 | 在連結的服務參照 DB2 資料庫執行個體中資料表的名稱。 表格名稱會區分大小寫。 | 不需要 （如果有指定**查詢**的**RelationalSource** ） |

## <a name="db2-copy-activity-type-properties"></a>DB2 複製活動類型的內容

節與適用於定義活動的內容的完整清單，請參閱[建立管線](data-factory-create-pipelines.md)文章。 針對所有類型的活動有內容，例如名稱、 描述、 輸入與輸出資料表及原則。 

活動 typeProperties 一節中提供的屬性另一方面會隨著每個活動類型。 複製活動，而有所不同的來源和接收類型。

複製活動時來源類型**RelationalSource** （包括 DB2） 的下列屬性會出現在 typeProperties] 區段中︰


| 屬性 | 描述 | 允許的值 | 所需 |
| -------- | ----------- | -------- | -------------- |
| 查詢 | 使用自訂查詢資料。 | SQL 查詢字串。 例如︰ `"query": "select * from "MySchema"."MyTable""`。 | 不需要 （如果指定的**資料集**的**表格名稱**）|

> [AZURE.NOTE] 結構描述和資料表名稱會區分大小寫。 將在名稱 」 」 （按兩下引號） 在查詢中。  

**範例︰**

    "query": "select * from "DB2ADMIN"."Customers""


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="type-mapping-for-db2"></a>Db2 類型對應
如上所述[資料移動活動](data-factory-data-movement-activities.md)文件中，複製活動會執行自動輸入轉換接收類型，使用下列步驟 2 方法來源類型︰

1. 從原生來源類型轉換成.NET 類型
2. 從.NET 類型轉換成原生接收類型

將資料移到 DB2 時，下列對應用於 DB2 類型.NET 類型。

DB2 資料庫類型 | .NET framework 類型 
----------------- | ------------------- 
小 | Int16
整數 | Int32
BigInt | Int64
實數 | 單一
點兩下 | 點兩下
浮動時間 | 點兩下
小數位數 | 小數位數
DecimalFloat | 小數位數
數字 | 小數位數
日期 | 日期時間
時間 | 時段
時間戳記 | 日期時間
Xml | Byte]
字元 | 字串
VarChar | 字串
LongVarChar | 字串
DB2DynArray | 字串
二進位 | Byte]
VarBinary | Byte]
LongVarBinary | Byte]
圖形 | 字串
VarGraphic | 字串
LongVarGraphic | 字串
Clob | 字串
Blob | Byte]
DbClob | 字串
小 | Int16
整數 | Int32
BigInt | Int64
實數 | 單一
點兩下 | 點兩下
浮動時間 | 點兩下
小數位數 | 小數位數
DecimalFloat | 小數位數
數字 | 小數位數
日期 | 日期時間
時間 | 時段
時間戳記 | 日期時間
Xml | Byte]
字元 | 字串


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]


[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>效能及調整  
請參閱[複製活動的效能與微調指南](data-factory-copy-activity-performance.md)，瞭解如何要素 Azure 資料工廠，並將它最佳化的各種方式中的資料移動 （複製活動） 的效能的影響。


