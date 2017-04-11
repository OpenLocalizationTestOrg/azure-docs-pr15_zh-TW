<properties 
    pageTitle="將資料移到或從 Azure SQL 資料庫 |Microsoft Azure" 
    description="瞭解如何將資料從使用 Azure 資料工廠 Azure SQL 資料庫。" 
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

# <a name="move-data-to-and-from-azure-sql-database-using-azure-data-factory"></a>移動資料使用 Azure 資料工廠 Azure SQL 資料庫
本文概述如何使用複製活動 Azure 資料工廠移動從/至另一個資料存放區的 [從 Azure SQL 資料庫的資料。 本文是根據呈現資料移動的一般概觀複製活動與支援的資料存放區組合的[資料移動活動](data-factory-data-movement-activities.md)文件。 

## <a name="supported-sources-and-sinks"></a>支援的來源和接收
如需支援來源或接收複製活動的資料存放區清單，請參閱[支援的資料存放區](data-factory-data-movement-activities.md#supported-data-stores-and-formats)表。 您可以將資料移到 Azure SQL 資料庫任何支援的來源資料存放區，或從 Azure SQL 資料庫任何支援的接收資料存放區。

## <a name="create-pipeline"></a>建立管線
您可以使用不同的工具 Api 移動從 Azure SQL 資料庫的資料複製活動建立管線。  

- 複製精靈
- Azure 入口網站
- Visual Studio
- Azure PowerShell
- .NET API
- REST API

建立管線與複製活動，以不同方式的逐步指示，請參閱[複製活動教學課程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。   

## <a name="copy-data-wizard"></a>複製資料精靈
建立管線將資料從 Azure SQL 資料庫複製最簡單的方法是使用複製資料精靈。 請參閱[教學課程︰ 建立使用複製精靈的管線](data-factory-copy-data-wizard-tutorial.md)的快速建立管線，使用 [複製資料] 精靈的逐步解說。 

下列範例會提供可供您建立的管線使用[Azure 入口網站](data-factory-copy-activity-tutorial-using-azure-portal.md)或[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)或[PowerShell 的 Azure](data-factory-copy-activity-tutorial-using-powershell.md)範例 JSON 定義。 即會顯示如何將資料複製 Azure SQL 資料庫和 Azure Blob 儲存體。 不過，資料可以複製**直接**從任何來源至任何接收敘述[以下](data-factory-data-movement-activities.md#supported-data-stores)Azure 資料工廠中使用複製活動。

## <a name="sample-copy-data-from-azure-sql-database-to-azure-blob"></a>範例︰ 將資料從 Azure SQL 資料庫複製到 Azure Blob

相同定義下列資料工廠實體︰

1. 連結的類型[AzureSqlDatabase](#azure-sql-linked-service-properties)的服務。
2. 連結的類型[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)的服務。 
3. 輸入的[資料集](data-factory-create-datasets.md)的類型[AzureSqlTable](#azure-sql-dataset-type-properties)。 
4. 輸出[資料集](data-factory-create-datasets.md)的類型[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)。
4. [管線](data-factory-create-pipelines.md)與使用[SqlSource](#azure-sql-copy-activity-type-properties)及[BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)複製活動。

範例將時間序列資料 （每小時每日，等） 從 Azure SQL 資料庫中的表格至 blob 每小時。 這些範例中所用的 JSON 屬性是由下列範例的各節所述。  

**Azure SQL 連結服務**

    {
      "name": "AzureSqlLinkedService",
      "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
          "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
      }
    }

請參閱[Azure SQL 連結服務](#azure-sql-linked-service-properties)清單的此連結服務支援的屬性。 

**Azure Blob 儲存體連結服務**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

請參閱[Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)清單的此連結服務支援的屬性。 

**Azure SQL 輸入資料集**

範例假設已在 SQL Azure 中建立表格 「 我的表格]，並將其包含稱為 「 timestampcolumn 」 時間] 資料數列的資料行。 

設定 「 外部 」:"true"通知 Azure 資料工廠服務，資料集外部資料 factory，並不會所產生的資料工廠中的活動。

    {
      "name": "AzureSqlInput",
      "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
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

請參閱[Azure SQL 資料集類型屬性](#azure-sql-dataset-type-properties)清單的支援此資料集類型的屬性。  

**Azure Blob 輸出資料集**

每小時寫入資料至新 blob (頻率︰ 小時、 間隔︰ 1)。 根據正在處理的扇形區的開始時間，以動態方式評估 blob 的資料夾路徑。 年、 月、 日和開始時間的小時部分，則會使用的資料夾路徑。 

    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
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

請參閱[Azure Blob 資料集類型屬性](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)清單的支援此資料集類型的屬性。  

**含複製活動的管線**

管線包含複製活動，設定為使用的輸入與輸出資料集，並排定執行每小時。 在管線 JSON 定義，**來源**類型設定為 [ **SqlSource** ，**接收**類型] 設定為**BlobSink**。 指定**SqlReaderQuery**屬性的 SQL 查詢複製過去的小時內，選取資料。

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
          {
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureSQLInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureBlobOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "SqlSource",
                "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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

在範例中，為 SqlSource 指定**sqlReaderQuery** 。 複製活動針對 Azure SQL 資料庫來源取得資料執行這個查詢。 或者，您可以指定預存程序來指定**sqlReaderStoredProcedureName**和**storedProcedureParameters** （如果預存的程序會帶領參數）。

如果您沒有指定 sqlReaderQuery 或 sqlReaderStoredProcedureName，定義資料集 JSON [結構] 區段中的資料行用來建立對 Azure SQL 資料庫執行查詢。 例如︰ `select column1, column2 from mytable`。 如果資料集定義沒有結構，所有資料行選取的資料表。 


請參閱[Sql 來源](#sqlsource)] 區段和[BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) SqlSource 與 BlobSink 支援的屬性的清單。 


## <a name="sample-copy-data-from-azure-blob-to-azure-sql-database"></a>範例︰ 從 Azure Blob 複製資料到 Azure SQL 資料庫

範例定義下列資料工廠實體︰  

1.  連結的類型[AzureSqlDatabase](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties)的服務。
2.  連結的類型[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)的服務。
3.  輸入的[資料集](data-factory-create-datasets.md)的類型[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)。
4.  輸出[資料集](data-factory-create-datasets.md)的類型[AzureSqlTable](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties)。
4.  [管線](data-factory-create-pipelines.md)與使用[BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)及[SqlSink](data-factory-azure-sql-connector.md#azure-sql-copy-activity-type-properties)複製活動。

在 SQL Azure 中為表格的時間序列資料 （每小時每日，等） 從 Azure blob 的範例複製資料庫的每個小時。 這些範例中所用的 JSON 屬性是由下列範例的各節所述。 


**Azure SQL 連結服務**
    
    {
      "name": "AzureSqlLinkedService",
      "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
          "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
      }
    }

請參閱[Azure SQL 連結服務](#azure-sql-linked-service-properties)清單的此連結服務支援的屬性。 

**Azure Blob 儲存體連結服務**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

請參閱[Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)清單的此連結服務支援的屬性。

**Azure Blob 輸入資料集**

資料從挑選新 blob 每小時 (頻率︰ 小時、 間隔︰ 1)。 Blob 的資料夾路徑和檔案名稱會根據正在處理的扇形區的開始時間，以動態方式評估。 年、 月和日的開始時間部分，會使用的資料夾路徑和檔案名稱會使用的開始時間的小時部分。 「 外部 」:"true"設定通知的資料工廠服務，此表格外部資料 factory，而且不會所產生的資料工廠活動。

    {
      "name": "AzureBlobInput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
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

請參閱[Azure Blob 資料集類型屬性](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)清單的支援此資料集類型的屬性。

**Azure SQL 輸出資料集**

此範例會將資料複製到名稱為 「 我的表格 」 SQL Azure 中的資料表。 如您預期包含 Blob CSV 檔案，請在 [使用相同的欄數 Azure SQL 中建立表格。 新的資料列會新增至資料表的每個小時。 

    {
      "name": "AzureSqlOutput",
      "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
          "tableName": "MyOutputTable"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

請參閱[Azure SQL 資料集類型屬性](#azure-sql-dataset-type-properties)清單的支援此資料集類型的屬性。

**含複製活動的管線**

管線包含複製活動，設定為使用的輸入與輸出資料集，並排定執行每小時。 在管線 JSON 定義，**來源**類型設定為 [ **BlobSource** ，**接收**類型] 設定為**SqlSink**。

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
          {
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureBlobInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureSqlOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource",
                "blobColumnSeparators": ","
              },
              "sink": {
                "type": "SqlSink"
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

請參閱[Sql 接收](#sqlsink)區段和[BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) SqlSink 與 BlobSource 支援的屬性的清單。 


## <a name="azure-sql-linked-service-properties"></a>Azure SQL 連結服務屬性
範例，在您使用類型**AzureSqlDatabase**連結的的服務連結資料工廠 Azure SQL 資料庫。 下表提供 JSON 項目的特定 Azure SQL 連結服務描述。

| 屬性 | 描述 | 所需 |
| -------- | ----------- | -------- |
| 類型 | [類型] 屬性必須設定為︰ **AzureSqlDatabase** | [是] |
| 連接字串 | 指定連接的連接字串屬性的 Azure SQL 資料庫執行個體所需的資訊。 | [是] |

> [AZURE.NOTE] 設定[Azure SQL 資料庫防火牆](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)允許[Azure 服務存取伺服器](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)的資料庫伺服器。 此外，如果您複製到 Azure SQL 資料庫的資料，從內部部署資料來源與資料工廠閘道外部 Azure 包括，設定適當的 IP 位址範圍，傳送資料到 Azure SQL 資料庫的電腦。 

## <a name="azure-sql-dataset-type-properties"></a>Azure SQL 資料集類型屬性
範例，在您使用**AzureSqlTable**類型的資料集來代表 Azure SQL 資料庫中的資料表。 

節與適用於定義資料集的內容的完整清單，請參閱[建立資料集](data-factory-create-datasets.md)。 例如結構、 可用性和資料集 JSON 原則] 區段都是類似的所有資料集類型 (Azure SQL Azure blob、 Azure 資料表等等。)。 

TypeProperties 區段不同資料集的每個類型，並提供的資料存放區中的資料位置的相關資訊。 **TypeProperties**資料集的區段類型**AzureSqlTable**具有下列屬性︰

| 屬性 | 描述 | 所需 |
| -------- | ----------- | -------- |
| 表格名稱 | 在連結的服務指的是 Azure SQL 資料庫執行個體中資料表的名稱。 | [是] |

## <a name="azure-sql-copy-activity-type-properties"></a>Azure SQL 複製活動類型屬性
節與適用於定義活動的內容的完整清單，請參閱[建立管線](data-factory-create-pipelines.md)文章。 例如名稱、 描述、 輸入與輸出的資料表，以及原則的內容，可針對所有類型的活動。

> [AZURE.NOTE] 複製活動會只有一個輸入，而產生單一輸出。

另一方面活動**typeProperties**一節中提供的屬性會隨著每一個活動類型。 複製活動，而有所不同的來源和接收類型。 

如果您從 Azure SQL 資料庫移動資料，您可以設定來源類型中**SqlSource**複製活動。 同樣地，如果您要前往 Azure SQL 資料庫的資料，請在**SqlSink**複製活動設定接收類型。 本節提供 SqlSource 與 SqlSink 支援的屬性的清單。 

### <a name="sqlsource"></a>SqlSource

複製活動時來源類型**SqlSource**的下列屬性會出現在**typeProperties** ] 區段中︰

| 屬性 | 描述 | 允許的值 | 所需 |
| -------- | ----------- | -------------- | -------- |
| sqlReaderQuery | 使用自訂查詢資料。 | SQL 查詢字串。 範例︰ `select * from MyTable`。  | 無 |
| sqlReaderStoredProcedureName | 讀取資料來源資料表中的預存程序的名稱。 | 預存程序的名稱。 | 無 |
| storedProcedureParameters | 預存程序的參數。 | 名稱/值組。 名稱及參數的大小寫，必須符合的名稱和預存程序參數的大小寫。 | 無 |

如果 SqlSource 指定**sqlReaderQuery** ，複製活動會針對 Azure SQL 資料庫來源取得資料執行這個查詢。 或者，您可以指定預存程序來指定**sqlReaderStoredProcedureName**和**storedProcedureParameters** （如果預存的程序會帶領參數）。 

如果您沒有指定 sqlReaderQuery 或 sqlReaderStoredProcedureName，定義資料集 JSON [結構] 區段中的資料行用來建立查詢 (`select column1, column2 from mytable`) 來執行 Azure SQL 資料庫。 如果資料集定義沒有結構，所有資料行選取的資料表。 

> [AZURE.NOTE] 當您使用**sqlReaderStoredProcedureName**時，您仍需要指定 JSON 資料集中的 [**表格名稱**] 屬性的值。 不有透過這個資料表執行任何驗證。 

### <a name="sqlsource-example"></a>SqlSource 範例

    "source": {
        "type": "SqlSource",
        "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
        "storedProcedureParameters": {
            "stringData": { "value": "str3" },
            "id": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
        }
    }

**預存程序定義︰** 

    CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
    (
        @stringData varchar(20),
        @id int
    )
    AS
    SET NOCOUNT ON;
    BEGIN
         select *
         from dbo.UnitTestSrcTable
         where dbo.UnitTestSrcTable.stringData != stringData
        and dbo.UnitTestSrcTable.id != id
    END
    GO


### <a name="sqlsink"></a>SqlSink 

**SqlSink**支援下列屬性︰

| 屬性 | 描述 | 允許的值 | 所需 |
| -------- | ----------- | -------------- | -------- |
| writeBatchTimeout | 等待的時間供批次插入作業完成逾時。 | 時段<br/><br/> 範例: 「 00: 30:00 」 （30 分鐘）。 | 無 | 
| writeBatchSize | 緩衝大小到達 writeBatchSize 時，則您可以插入 SQL 資料表的資料。 | 整數 （數字的資料列）| 不 (預設︰ 10000)
| sqlWriterCleanupScript | 指定要執行，例如清除資料的特定的扇形區之後複製活動的查詢。 如需詳細資訊，請參閱[重複性一節](#repeatability-during-copy)。 | 陳述式的查詢。  | 無 |
| sliceIdentifierColumnName | 指定要填入自動產生的扇形區識別碼] 用來清理特定的扇形區，當重新執行的資料複製活動的欄名稱。 如需詳細資訊，請參閱[重複性一節](#repeatability-during-copy)。 | 具有 binary(32) 的資料類型的資料行的資料行名稱。 | 無 |
| sqlWriterStoredProcedureName | 預存程序的名稱 upserts （更新/插入） 資料到目標資料表。 | 預存程序的名稱。 | 無 |
| storedProcedureParameters | 預存程序的參數。 | 名稱/值組。 名稱及參數的大小寫，必須符合的名稱和預存程序參數的大小寫。 | 無 | 
| sqlWriterTableType | 指定要使用預存程序中的表格類型名稱。 複製活動可讓您可使用此類型的資料表 temp 表格中移動的資料。 預存程序的程式碼然後可以合併複製現有資料的資料。 | 表格類型名稱。 | 無 |

#### <a name="sqlsink-example"></a>SqlSink 範例

    "sink": {
        "type": "SqlSink",
        "writeBatchSize": 1000000,
        "writeBatchTimeout": "00:05:00",
        "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
        "sqlWriterTableType": "CopyTestTableType",
        "storedProcedureParameters": {
            "id": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" },
            "decimalData": { "value": "1", "type": "Decimal" }
        }
    }

## <a name="identity-columns-in-the-target-database"></a>識別目標資料庫中的資料行
本節提供範例從來源資料表不識別資料行的資料複製到目的資料表與身分識別資料行。 

**來源資料表︰** 

    create table dbo.SourceTbl
    (
           name varchar(100),
           age int
    )

**目的資料表︰**

    create table dbo.TargetTbl
    (
           id int identity(1,1),
           name varchar(100),
           age int
    )


注意到目標資料表具有識別資料行。 

**來源資料集 JSON 定義**

    {
        "name": "SampleSource",
        "properties": {
            "type": " SqlServerTable",
            "linkedServiceName": "TestIdentitySQL",
            "typeProperties": {
                "tableName": "SourceTbl"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }

**目的地資料集 JSON 定義**

    {
        "name": "SampleTarget",
        "properties": {
            "structure": [
                { "name": "name" },
                { "name": "age" }
            ],
            "type": "AzureSqlTable",
            "linkedServiceName": "TestIdentitySQLSource",
            "typeProperties": {
                "tableName": "TargetTbl"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": false,
            "policy": {}
        }   
    }


請注意，為您的來源及目標表格有不同的結構描述 （目標有身分識別與其他資料行）。 在此案例中，您需要**結構**屬性指定目標資料集定義，並不包含 [身分識別] 欄中。 

然後，您將對應來源資料集的目的地資料集的資料行的資料行。 如需範例，請參閱[資料行對應範例](#column-mapping-samples)一節。 

[AZURE.INCLUDE [data-factory-type-repeatability-for-sql-sources](../../includes/data-factory-type-repeatability-for-sql-sources.md)] 

[AZURE.INCLUDE [data-factory-sql-invoke-stored-procedure](../../includes/data-factory-sql-invoke-stored-procedure.md)]
 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-sql-server--azure-sql-database"></a>輸入對應的 SQL Server Azure SQL 資料庫

所述[資料移動活動](data-factory-data-movement-activities.md)文件複製活動會執行自動輸入轉換來源類型接收類型，使用下列步驟 2 方法︰

1. 從原生來源類型轉換成.NET 類型
2. 從.NET 類型轉換成原生接收類型

當資料移至與從 Azure SQL，SQL server，Sybase 下列對應使用 SQL 類型.NET 類型，反之亦然。

對應是同 ADO.NET SQL Server 資料類型的對應。

| SQL Server 資料庫引擎類型 | .NET framework 類型 |
| ------------------------------- | ------------------- |
| bigint | Int64 |
| 二進位 | Byte] |
| 位元 | 布林值 |
| 字元 | 字串，Char] |
| 日期 | 日期時間 |
| 日期時間 | 日期時間 |
| datetime2 | 日期時間 |
| Datetimeoffset | DateTimeOffset |
| 小數位數 | 小數位數 |
| FILESTREAM 屬性 (varbinary(max)) | Byte] |
| 浮動時間 | 點兩下 |
| 圖像 | Byte] | 
| int | Int32 | 
| 金錢 | 小數位數 |
| nchar | 字串，Char] |
| n | 字串，Char] |
| 數字 | 小數位數 |
| nvarchar | 字串，Char] |
| 實數 | 單一 |
| rowversion | Byte] |
| 29.998 | 日期時間 |
| 小 | Int16 |
| smallmoney | 小數位數 | 
| sql_variant | 物件 * |
| 文字 | 字串，Char] |
| 時間 | 時段 |
| 時間戳記 | Byte] |
| tinyint | 位元組 |
| 唯一 | Guid |
| varbinary |  Byte] |
| varchar | 字串，Char] |
| xml | Xml |


[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## <a name="performance-and-tuning"></a>效能及調整  
請參閱[複製活動的效能與微調指南](data-factory-copy-activity-performance.md)，瞭解如何要素 Azure 資料工廠，並將它最佳化的各種方式中的資料移動 （複製活動） 的效能的影響。