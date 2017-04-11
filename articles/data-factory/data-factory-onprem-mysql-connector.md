<properties 
    pageTitle="將資料從 MySQL 移 |Azure 資料工廠" 
    description="進一步瞭解如何將使用 Azure 資料工廠 MySQL 資料庫中的資料。" 
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

# <a name="move-data-from-mysql-using-azure-data-factory"></a>將資料從 MySQL 使用 Azure 資料工廠移動

本文概述如何使用複製活動 Azure 資料工廠移動到另一個資料 MySQL 從儲存的資料。 本文是根據呈現資料移動的一般概觀複製活動與支援的資料存放區組合的[資料移動活動](data-factory-data-movement-activities.md)文件。

資料工廠服務支援使用連線到內部部署 MySQL 來源資料管理閘道器。 請參閱[內部部署的位置與雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md)文章，進一步瞭解資料管理閘道器和設定閘道器的逐步指示。 

> [AZURE.NOTE] 即使 MySQL 資料庫裝載在 Azure IaaS 虛擬機器 (VM) 需要閘道器。 您可以安裝閘道器做為資料儲存區 VM 相同或不同 VM，只要閘道器可以連線至資料庫。  

資料工廠目前支援只移動資料從 MySQL 其他資料儲存區中，但無法將資料從其他資料儲存區到 MySQL。

## <a name="installation"></a>安裝 
連線到 MySQL 資料庫的資料管理閘道器，您需要安裝資料管理閘道器與相同系統[MySQL 連接器/網路 6.6.5 的 Microsoft Windows](http://go.microsoft.com/fwlink/?LinkId=278885) 。

> [AZURE.NOTE] 如需疑難排解連線/閘道器的秘訣請參閱[疑難排解閘道器問題](data-factory-data-management-gateway.md#troubleshoot-gateway-issues)相關問題。 

## <a name="copy-data-wizard"></a>複製資料精靈
建立管線將資料從 MySQL 資料庫到任何支援的接收資料存放區，最簡單的方法是使用 [複製資料精靈。 請參閱[教學課程︰ 建立使用複製精靈的管線](data-factory-copy-data-wizard-tutorial.md)的快速建立管線，使用 [複製資料] 精靈的逐步解說。 

下列範例會提供可供您建立的管線範例 JSON 定義。 逐步指示完成逐步解說，請參閱[內部部署的位置與雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md)文件。 資料可以複製至任何接收敘述[以下](data-factory-data-movement-activities.md#supported-data-stores)Azure 資料工廠中使用複製活動。   

## <a name="sample-copy-data-from-mysql-to-azure-blob"></a>範例︰ 從 MySQL 資料複製到 Azure Blob
此範例會示範如何將內部部署 MySQL 資料庫資料複製到 Azure Blob 儲存體。 不過，資料可以複製**直接**至任何接收敘述[以下](data-factory-data-movement-activities.md#supported-data-stores)Azure 資料工廠中使用複製活動。  

> [AZURE.IMPORTANT] 這個範例提供 JSON 程式碼片段。 它不包含建立資料 factory 的逐步指示。 請參閱[內部部署的位置與雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md)文件，如需逐步指示。 
 
樣本具有下列資料工廠實體︰

1.  連結的類型[OnPremisesMySql](data-factory-onprem-mysql-connector.md#mysql-linked-service-properties)的服務。
2.  連結的類型[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)的服務。
3.  輸入的[資料集](data-factory-create-datasets.md)的類型[RelationalTable](data-factory-onprem-mysql-connector.md#mysql-dataset-type-properties)。
4.  輸出[資料集](data-factory-create-datasets.md)的類型[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)。
4.  [管線](data-factory-create-pipelines.md)與使用[RelationalSource](data-factory-onprem-mysql-connector.md#mysql-copy-activity-type-properties)及[BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)複製活動。

範例將資料從 MySQL 資料庫中的查詢結果到 blob 每小時。 這些範例中所用的 JSON 屬性是由下列範例的各節所述。 

第一個步驟，設定資料管理閘道器。 其指示都[位置內部部署與雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md)文件中。 

**連結的 MySQL 服務**

    {
      "name": "OnPremMySqlLinkedService",
      "properties": {
        "type": "OnPremisesMySql",
        "typeProperties": {
          "server": "<server name>",
          "database": "<database name>",
          "schema": "<schema name>",
          "authenticationType": "<authentication type>",
          "userName": "<user name>",
          "password": "<password>",
          "gatewayName": "<gateway>"
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

**MySQL 輸入資料集**

範例假設有在 MySQL 中建立表格 「 我的表格]，並將其包含稱為 「 timestampcolumn 」 時間] 資料數列的資料行。

設定 「 外部 」:"true"通知資料工廠服務，表格外部資料 factory，並不會所產生的資料工廠中的活動。
    
    {
        "name": "MySqlDataSet",
        "properties": {
            "published": false,
            "type": "RelationalTable",
            "linkedServiceName": "OnPremMySqlLinkedService",
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



**Azure Blob 輸出資料集**

每小時寫入資料至新 blob (頻率︰ 小時、 間隔︰ 1)。 根據正在處理的扇形區的開始時間，以動態方式評估 blob 的資料夾路徑。 年、 月、 日和開始時間的小時部分，則會使用的資料夾路徑。

    {
        "name": "AzureBlobMySqlDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/mysql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
        "name": "CopyMySqlToBlob",
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
                            "name": "MySqlDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobMySqlDataSet"
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
                    "name": "MySqlToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }



## <a name="mysql-linked-service-properties"></a>連結的 MySQL 服務屬性

下表提供 JSON 項目的特定 MySQL 連結服務描述。

| 屬性 | 描述 | 所需 |
| -------- | ----------- | -------- | 
| 類型 | [類型] 屬性必須設定為︰ **OnPremisesMySql** | [是] |
| 伺服器 | MySQL 伺服器的名稱。 | [是] |
| 資料庫 | MySQL 資料庫的名稱。 | [是] | 
| 結構描述  | 在資料庫中的結構描述的名稱。 | 無 | 
| 識別碼 | 用來連線到 MySQL 資料庫的驗證類型。 可能的值︰ 匿名、 基本、 和 Windows。 | [是] | 
| 使用者名稱 | 如果您使用基本 」 或 「 Windows 驗證，指定使用者名稱。 | 無 | 
| 密碼 | 指定您所指定的使用者名稱的使用者帳戶的密碼。 | 無 | 
| gatewayName | 閘道器的資料工廠服務應使用連線到內部部署 MySQL 資料庫的名稱。 | [是] |

如需詳細資訊設定針對內部部署 MySQL 資料來源的認證，請參閱[設定認證與安全性](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security)。

## <a name="mysql-dataset-type-properties"></a>MySQL 資料集類型屬性

節與適用於定義資料集的內容的完整清單，請參閱[建立資料集](data-factory-create-datasets.md)。 例如結構、 可用性和資料集 JSON 原則] 區段都是類似的所有資料集類型 (Azure SQL Azure blob、 Azure 資料表等等。)。

**TypeProperties**區段不同資料集的每個類型，並提供的資料存放區中的資料位置的相關資訊。 類型**RelationalTable** （包括 MySQL 資料集） 的資料集 typeProperties 區段有下列屬性

| 屬性 | 描述 | 所需 |
| -------- | ----------- | -------- |
| 表格名稱 | 在連結的服務參照到 MySQL 資料庫執行個體中資料表的名稱。 | 不需要 （如果有指定**查詢**的**RelationalSource** ） | 

## <a name="mysql-copy-activity-type-properties"></a>MySQL 複製活動類型的內容

節與適用於定義活動的內容的完整清單，請參閱[建立管線](data-factory-create-pipelines.md)文章。 例如名稱、 描述、 輸入與輸出表格的內容是原則可供所有類型的活動。 

另一方面活動**typeProperties**一節中提供的屬性會隨著每一個活動類型。 複製活動，而有所不同的來源和接收類型。

當複製活動的來源類型**RelationalSource** （包括 MySQL） 的下列屬性可 typeProperties] 區段中︰

| 屬性 | 描述 | 允許的值 | 所需 |
| -------- | ----------- | -------------- | -------- |
| 查詢 | 使用自訂查詢資料。 | SQL 查詢字串。 例如︰ 選取 * 從我的表格。 | 不需要 （如果指定的**資料集**的**表格名稱**） | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-mysql"></a>MySQL 類型對應

如上所述[資料移動活動](data-factory-data-movement-activities.md)文件中，複製活動會執行自動輸入轉換接收類型的下列兩種方式與來源類型︰

1. 從原生來源類型轉換成.NET 類型
2. 從.NET 類型轉換成原生接收類型

將資料移到 [MySQL，下列對應用於從 MySQL 類型.NET 類型。

| MySQL 資料庫類型 | .NET framework 類型 |
| ------------------- | ------------------- | 
| 未簽署的 bigint | 小數位數 |
| bigint | Int64 |
| 位元 | 小數位數 |
| blob | Byte] |
| bool |  布林值 | 
| 字元 | 字串 |
| 日期 | 日期時間 |
| 日期時間 | 日期時間 |
| 小數位數 | 小數位數 |
| 雙精確度 | 點兩下 |
| 雙引號 | 點兩下 |
| 列舉 | 字串 |
| 浮動時間 | 單一 |
| int 尚未簽章 | Int64 |
| int | Int32 |
| 未簽署的整數 | Int64 |
| 整數 | Int32 | 
| 長 varbinary | Byte] |
| 長 varchar | 字串 |
| longblob | Byte] |
| 長文字 | 字串 | 
| mediumblob |  Byte] | 
| 未簽署的 mediumint | Int64 |
| mediumint | Int32 | 
| mediumtext | 字串 |
| 數字 | 小數位數 |
| 實數 |  點兩下 |
| 設定 | 字串 |
| 未簽署的小 | Int32 |
| 小 | Int16 |
| 文字 | 字串 |
| 時間 | 時段 |
| 時間戳記 | 日期時間 |
| tinyblob | Byte] |
| 未簽署的 tinyint |  Int16 | 
| tinyint | Int16 |
| tinytext | 字串 | 
| varchar | 字串 | 
| 年 | Int | 


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>效能及調整  
請參閱[複製活動的效能與微調指南](data-factory-copy-activity-performance.md)，瞭解如何要素 Azure 資料工廠，並將它最佳化的各種方式中的資料移動 （複製活動） 的效能的影響。

