<properties 
    pageTitle="將資料移到或從 Azure 資料表 |Microsoft Azure" 
    description="瞭解如何將資料從使用 Azure 資料工廠 Azure 資料表儲存體。" 
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
    ms.date="09/13/2016" 
    ms.author="jingwang"/>

# <a name="move-data-to-and-from-azure-table-using-azure-data-factory"></a>從 Azure 資料表使用 Azure 資料工廠移動資料

本文概述如何使用複製活動 Azure 資料工廠移動從/至另一個資料存放區的 [從 Azure 資料表的資料。 本文是根據[資料移動活動](data-factory-data-movement-activities.md)文件複製活動提供資料移動和支援的資料存放區組合的一般概觀。

## <a name="copy-data-wizard"></a>複製資料精靈
建立管線將資料從 Azure 資料表儲存體複製最簡單的方法是使用 [複製資料精靈。 請參閱[教學課程︰ 建立使用複製精靈的管線](data-factory-copy-data-wizard-tutorial.md)的快速建立管線，使用 [複製資料] 精靈的逐步解說。 

下列範例會提供可供您建立的管線使用[Azure 入口網站](data-factory-copy-activity-tutorial-using-azure-portal.md)或[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)或[PowerShell 的 Azure](data-factory-copy-activity-tutorial-using-powershell.md)範例 JSON 定義。 即會顯示如何將資料複製 Azure 資料表儲存體和 Azure Blob 資料庫。 不過，資料可以複製**直接**從任何來源至任何接收敘述[以下](data-factory-data-movement-activities.md#supported-data-stores)Azure 資料工廠中使用複製活動。

## <a name="sample-copy-data-from-azure-table-to-azure-blob"></a>從 Azure 資料表至 Azure Blob 的範例︰ 複製資料

下列範例顯示︰

1.  連結的類型[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) （用於資料表及 blob 同時） 的服務。
2.  輸入的[資料集](data-factory-create-datasets.md)的類型[azure 資料表](#azure-table-dataset-type-properties)。
3.  輸出[資料集](data-factory-create-datasets.md)的類型[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)。 
3.  [管線](data-factory-create-pipelines.md)與使用[AzureTableSource](#azure-table-copy-activity-type-properties)及[BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)複製活動。 

範例會將每小時屬於 blob Azure 資料表中的預設磁碟分割的資料。 這些範例中所用的 JSON 屬性是由下列範例的各節所述。

**連結的儲存空間 azure 服務︰**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

Azure 資料工廠支援兩種類型的連結 Azure 儲存體服務︰ **AzureStorage**和**AzureStorageSas**。 第一個項，指定連線字串，其中包含 [帳戶金鑰]，並針對更新版本，指定共用 Access 簽章 (SA) Uri。 如需詳細資訊，請參閱[連結的服務](#linked-services)] 區段。  

**Azure 資料表輸入資料集︰**

範例假設 Azure 資料表中建立表格 「 我的表格]。
 
設定 「 外部 」:"true"通知資料工廠服務，資料集外部資料 factory，並不會所產生的資料工廠中的活動。

    {
      "name": "AzureTableInput",
      "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "tableName": "MyTable"
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        },
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
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
          "format": {
            "type": "TextFormat",
            "columnDelimiter": "\t",
            "rowDelimiter": "\n"
          }
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

**管線與複製活動︰**

管線包含複製活動，設定為使用的輸入與輸出資料集，並排定執行每小時。 在管線 JSON 定義，**來源**類型設定為 [ **AzureTableSource** ，**接收**類型] 設定為**BlobSink**。 具有**AzureTableSourceQuery**屬性指定 SQL 查詢選取資料從預設分割每小時複製。

    {  
        "name":"SamplePipeline",
        "properties":{  
            "start":"2014-06-01T18:00:00",
            "end":"2014-06-01T19:00:00",
            "description":"pipeline for copy activity",
            "activities":[  
                {
                    "name": "AzureTabletoBlob",
                    "description": "copy activity",
                    "type": "Copy",
                    "inputs": [
                        {
                            "name": "AzureTableInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
                    "typeProperties": {
                        "source": {
                            "type": "AzureTableSource",
                            "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },              
                    "policy": {
                        "concurrency": 1,
                        "executionPriorityOrder": "OldestFirst",
                        "retry": 0,
                        "timeout": "01:00:00"
                    }
                }
             ]  
        }
    }

## <a name="sample-copy-data-from-azure-blob-to-azure-table"></a>範例︰ 從 Azure Blob 複製資料至 Azure 資料表

下列範例顯示︰

1.  連結之服務的類型[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) （用於資料表及 blob 同時）
3.  輸入的[資料集](data-factory-create-datasets.md)的類型[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)。
4.  輸出[資料集](data-factory-create-datasets.md)的類型[azure 資料表](#azure-table-dataset-type-properties)。 
4.  [管線](data-factory-create-pipelines.md)與使用[BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)及[AzureTableSink](#azure-table-copy-activity-type-properties)複製活動。 


時間序列至 Azure blob azure 資料的範例複製表格小時。 這些範例中所用的 JSON 屬性是由下列範例的各節所述。

**Azure 儲存空間 （適用於 Azure 資料表與 Blob） 連結服務︰**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

Azure 資料工廠支援兩種類型的連結 Azure 儲存體服務︰ **AzureStorage**和**AzureStorageSas**。 第一個項，指定連線字串，其中包含 [帳戶金鑰]，並針對更新版本，指定共用 Access 簽章 (SA) Uri。 如需詳細資訊，請參閱[連結的服務](#linked-services)] 區段。 

**Azure Blob 輸入資料集︰**

資料從挑選新 blob 每小時 (頻率︰ 小時、 間隔︰ 1)。 Blob 的資料夾路徑和檔案名稱會根據正在處理的扇形區的開始時間，以動態方式評估。 年、 月和日的開始時間部分，會使用的資料夾路徑和檔案名稱會使用的開始時間的小時部分。 「 外部 」:"true"設定值在資料集外部資料 factory，並不會所產生的資料工廠活動通知資料工廠服務。
    
    {
      "name": "AzureBlobInput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
          "fileName": "{Hour}.csv",
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
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "rowDelimiter": "\n"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "externalData": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
          }
        }
      }
    }

**Azure 資料表輸出資料集︰**

此範例會將資料複製到名稱為 「 我的表格 」 Azure 資料表中的資料表。 如您預期包含 Blob CSV 檔案，請使用相同的欄數建立 Azure 的資料表。 新的資料列會新增至資料表的每個小時。 

    {
      "name": "AzureTableOutput",
      "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "tableName": "MyOutputTable"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

**管線與複製活動︰**

管線包含複製活動，設定為使用的輸入與輸出資料集，並排定執行每小時。 在管線 JSON 定義，**來源**類型設定為 [ **BlobSource** ，**接收**類型] 設定為**AzureTableSink**。 


    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
          {
            "name": "AzureBlobtoTable",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureBlobInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureTableOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "AzureTableSink",
                "writeBatchSize": 100,
                "writeBatchTimeout": "01:00:00"
              }
            },
            "scheduler": {
              "frequency": "Hour",
              "interval": 1
            },                      
            "policy": {
              "concurrency": 1,
              "executionPriorityOrder": "OldestFirst",
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
          ]
       }
    }

## <a name="linked-services"></a>連結的服務
有兩種類型的連結的服務，您可以使用連結 Azure 資料工廠 Azure blob 儲存體。 他們的年齡︰ **AzureStorage**連結的服務與**AzureStorageSas**連結服務。 Azure 儲存體連結服務會提供通用存取資料工廠至 Azure 儲存體。 而 Azure 儲存體 SA （共用 Access 簽章） 連結服務，提供資料工廠限制/時間繫結存取至 Azure 儲存體。 不有任何其他差異這兩個連結的服務。 選擇適合您需求的連結的服務。 下列各節會提供更多詳細資料，在這兩個連結的服務。

[AZURE.INCLUDE [data-factory-azure-storage-linked-services](../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="azure-table-dataset-type-properties"></a>Azure 資料表資料集類型屬性

節與適用於定義資料集的內容的完整清單，請參閱[建立資料集](data-factory-create-datasets.md)。 例如結構、 可用性和資料集 JSON 原則] 區段都是類似的所有資料集類型 (Azure SQL Azure blob、 Azure 資料表等等。)。

TypeProperties 區段不同資料集的每個類型，並提供的資料存放區中的資料位置的相關資訊。 輸入**azure 資料表**的資料集的**typeProperties**區段有下列屬性。

| 屬性 | 描述 | 所需 |
| -------- | ----------- | -------- |
| 表格名稱 | 在連結的服務指的是 Azure 資料表資料庫執行個體中資料表的名稱。 | [是]。 不 azureTableSourceQuery 指定表格名稱時，就會將資料表中的所有記錄都複製到目的地。 如果也指定 azureTableSourceQuery，則符合查詢的資料表中的記錄會複製到目的地。 |

### <a name="schema-by-data-factory"></a>資料工廠的結構描述
為例如 Azure 資料表的結構描述免費的資料存放區，資料工廠服務會以下列方法的其中一推斷的結構描述︰

1.  如果您使用 [**結構**] 屬性中的資料集定義] 指定資料的結構資料工廠服務就會接受此結構和結構描述。 在此案例中，列不包含資料行的值，null 值，如果它。
2. 如果您不指定資料的結構資料集定義中使用 [**結構**] 屬性，資料工廠會推斷的結構描述資料中使用的第一列。 在此情況下，如果第一列不包含完整的結構描述，某些資料欄的未接來電中複製作業的結果。

因此，沒有結構描述的資料來源的最佳作法是指定的結構，使用 [**結構**] 屬性的資料。

## <a name="azure-table-copy-activity-type-properties"></a>Azure 表格複製活動類型的屬性

節與適用於定義活動的內容的完整清單，請參閱[建立管線](data-factory-create-pipelines.md)文章。 例如名稱、 描述、 輸入與輸出資料集及原則的內容，可針對所有類型的活動。 

活動 typeProperties 一節中提供的屬性另一方面會隨著每個活動類型。 複製活動，而有所不同的來源和接收類型。

**AzureTableSource**支援 typeProperties] 區段中的下列屬性︰

屬性 | 描述 | 允許的值 | 所需
-------- | ----------- | -------------- | -------- 
azureTableSourceQuery | 使用自訂查詢資料。 | Azure 資料表查詢字串。 請參閱下一節中的範例。 | [否]。 不 azureTableSourceQuery 指定表格名稱時，就會將資料表中的所有記錄都複製到目的地。 如果也指定 azureTableSourceQuery，則符合查詢的資料表中的記錄會複製到目的地。
azureTableSourceIgnoreTableNotFound | 指出是否理解表格的例外狀況不存在。 | 為 TRUE<br/>FALSE | 無 |

### <a name="azuretablesourcequery-examples"></a>azureTableSourceQuery 範例

如果 Azure 資料表資料行是字串類型︰ 

    azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', SliceStart)"

如果 Azure 資料表資料行是 datetime 類型︰ 

    "azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', SliceStart, SliceEnd)"


**AzureTableSink**支援 typeProperties] 區段中的下列屬性︰


屬性 | 描述 | 允許的值 | 所需  
-------- | ----------- | -------------- | -------- 
azureTableDefaultPartitionKeyValue | 預設的磁碟分割關鍵值，可接收所。 | 字串值。 | 無 
azureTablePartitionKeyName | 指定其值做為資料分割索引鍵的資料行名稱。 如果未指定 AzureTableDefaultPartitionKeyValue 作為分割索引鍵。 | 資料行名稱。 | 無 |
azureTableRowKeyName | 指定的資料行的值會作為列索引鍵的資料行名稱。 如果未指定使用 GUID 每個資料列。 | 資料行名稱。 | 無  
azureTableInsertType | 若要插入 Azure 資料表中的資料模式。<br/><br/>此屬性可控制是否具有相符的磁碟分割和資料列的索引鍵的輸出資料表中的現有資料列有其值取代或合併。 <br/><br/>若要進一步瞭解這些設定 （合併及取代） 的運作方式，請參閱[插入或合併實體](https://msdn.microsoft.com/library/azure/hh452241.aspx)和[插入或取代實體](https://msdn.microsoft.com/library/azure/hh452242.aspx)主題。 <br/><br> 此設定會套用在列層級，而不是表格層級，並這兩個選項刪除輸出表格中輸入中不存在的資料列。 | 合併 （預設值）<br/>取代 | 無 
writeBatchSize | WriteBatchSize 或 writeBatchTimeout 叫用時，則您可以插入 Azure 資料表的資料。 | 整數 （數字的資料列）| 不 (預設︰ 10000) 
writeBatchTimeout | 表格中插入資料 Azure writeBatchSize 或 writeBatchTimeout 叫用時 | 時段<br/><br/>範例: 「 00: 20:00 」 （20 分鐘） | 不 （儲存用戶端預設逾時的預設值 90 秒）

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName
您可以使用目的地欄作為 azureTablePartitionKeyName 前，請使用翻譯工具 JSON 屬性的目的資料行對應的來源資料行。

在下列範例中，來源資料行 DivisionID 已對應至目的資料行︰ DivisionID。  

    "translator": {
        "type": "TabularTranslator",
        "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
    } 

DivisionID 已指定為分割索引鍵。 

    "sink": {
        "type": "AzureTableSink",
        "azureTablePartitionKeyName": "DivisionID",
        "writeBatchSize": 100,
        "writeBatchTimeout": "01:00:00"
    }


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-azure-table"></a>Azure 資料表類型對應

如上所述[資料移動活動](data-factory-data-movement-activities.md)文件中，複製活動執行自動輸入轉換接收類型的下列兩種方式與來源類型。

1. 從原生來源類型轉換成.NET 類型
2. 從.NET 類型轉換成原生接收類型

將資料移至與從 Azure 資料表時，下列[Azure 資料表服務來定義對應](https://msdn.microsoft.com/library/azure/dd179338.aspx)的用 Azure 資料表 OData 類型.NET 類型，反之亦然。 

| OData 資料類型 | .NET 類型 | 詳細資料 |
| --------------- | --------- | ------- |
| Edm.Binary | byte] | 位元組陣列最多 64 KB。 |
| Edm.Boolean | bool | 布林值。 |
| Edm.DateTime | 日期時間 | 64 位元值，表示為國際標準時間 (UTC)。 從第午夜 12:00 年 1 月 1，1601 開始的支援的日期時間範圍 （年），UTC。 在 [年 12 月 31 日結束的範圍 9999。 |
| Edm.Double | 雙引號 | 64 位元浮動值。 |
| Edm.Guid | Guid | 128 位元的全域唯一識別碼。 |
| Edm.Int32 | Int32 或 int | 32 位元整數。 |
| Edm.Int64 | Int64 或時間 | 64 位元整數。 |
| Edm.String | 字串 | UTF-8 16 編碼的值。 字串值可能會最多 64 KB。 |

### <a name="type-conversion-sample"></a>類型轉換的範例

下列範例會從 Azure Blob 複製資料到 Azure 資料表類型轉換。 

假設 Blob 資料集的 CSV 格式，並包含三個資料行。 這些是使用縮寫法文星期名稱的自訂 datetime 格式的日期時間資料行。 

定義 Blob 來源資料集以及類型定義的資料行，如下所示。
    
    {
        "name": " AzureBlobInput",
        "properties":
        {
             "structure": 
              [
                    { "name": "userid", "type": "Int64"},
                    { "name": "name", "type": "String"},
                    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
              ],
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/myfolder",
                "fileName":"myfile.csv",
                "format":
                {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "external": true,
            "availability":
            {
                "frequency": "Hour",
                "interval": 1,
            },
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }

從 Azure 資料表 OData 類型指定類型對應.NET 類型，您要定義表格 Azure 資料表中使用下列結構描述。 

**Azure 資料表結構描述中︰**

資料行名稱 | 類型
----------- | --------
使用者識別碼 | Edm.Int64
名稱 | Edm.String 
lastlogindate | Edm.DateTime

接下來，定義 Azure 資料表資料集，如下所示。 您不需要的類型資訊指定 [結構] 區段，因為基礎的資料存放區中已指定 [類型] 資訊。

    {
      "name": "AzureTableOutput",
      "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "tableName": "MyOutputTable"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

在此情況下，資料工廠自動會類型轉換的 [日期時間] 欄位包含自訂的日期時間格式，將資料從 Blob 移至 [Azure 資料表時，請使用 「 這個方法下列格式 」 文化特性。



[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## <a name="performance-and-tuning"></a>效能及調整  
若要瞭解要素 Azure 資料工廠，並將它最佳化的各種方式中的資料移動 （複製活動） 的效能的影響，請參閱[複製活動效能調整輔助線](data-factory-copy-activity-performance.md)。







