<properties 
    pageTitle="將資料移到或從使用資料工廠 Oracle |Microsoft Azure" 
    description="瞭解如何將資料從 Oracle 資料庫的部署使用 Azure 資料工廠。" 
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

# <a name="move-data-tofrom-on-premises-oracle-using-azure-data-factory"></a>將資料從內部部署 Oracle 使用 Azure 資料工廠移動 

本文概述如何使用資料工廠複製活動移動從/到另一個資料儲存 Oracle 的資料。 本文是根據呈現資料移動的一般概觀複製活動與支援的資料存放區組合的[資料移動活動](data-factory-data-movement-activities.md)文件。

## <a name="installation"></a>安裝 
若要能夠連線到您的內部部署 Oracle 資料庫 Azure 資料工廠服務，您必須安裝下列元件︰ 

- 資料管理閘道器在同一台機器裝載資料庫的或不同的電腦上以避免競爭與資料庫的資源。 資料管理閘道處於安全及受管理的方式，連線到雲端服務的內部部署資料來源的用戶端代理程式。 請參閱[移動內部部署與雲端之間的資料](data-factory-move-data-between-onprem-and-cloud.md)文件的詳細資料管理閘道器。 
- Oracle Data Provider for.NET。 此元件會包含在[Oracle 資料存取 Windows 元件](http://www.oracle.com/technetwork/topics/dotnet/downloads/)。 在安裝閘道器主機上安裝適當的版本 （32/64 位元）。 [Oracle 的資料提供者.NET 12.1](http://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149)可以存取 Oracle 資料庫 10g Release 2 或更新版本。

    如果您選擇 「 XCopy 安裝 」，請遵循 readme.htm 中的步驟。 我們建議您選擇的安裝程式的使用者介面 (非-XCopy 其中一個)。 
 
    安裝後的提供者，請重新啟動您使用的服務] 小程式 （或） 資料管理閘道器組態管理員的電腦上的資料管理閘道器主機服務。  

> [AZURE.NOTE] 如需疑難排解連線/閘道器的秘訣請參閱[疑難排解閘道器問題](data-factory-data-management-gateway.md#troubleshoot-gateway-issues)相關問題。 

## <a name="copy-data-wizard"></a>複製資料精靈
建立管線將從/到 Oracle 資料庫的資料複製到任何支援的接收資料存放區最簡單的方法是使用 [複製資料精靈。 請參閱[教學課程︰ 建立使用複製精靈的管線](data-factory-copy-data-wizard-tutorial.md)的快速建立管線，使用 [複製資料] 精靈的逐步解說。 

下列範例會提供可供您建立的管線使用[Azure 入口網站](data-factory-copy-activity-tutorial-using-azure-portal.md)或[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)或[PowerShell 的 Azure](data-factory-copy-activity-tutorial-using-powershell.md)範例 JSON 定義。 即會顯示如何將資料從/到 Oracle 資料庫從 Azure Blob 儲存體複製。 不過，可以複製資料至任何接收敘述[以下](data-factory-data-movement-activities.md#supported-data-stores)Azure 資料工廠中使用複製活動。   

## <a name="sample-copy-data-from-oracle-to-azure-blob"></a>範例︰ 從 Oracle 資料複製到 Azure Blob
此範例會示範如何將內部部署 Oracle 資料庫資料複製到 Azure Blob 儲存體。 不過，資料可以複製**直接**至任何接收敘述[以下](data-factory-data-movement-activities.md#supported-data-stores)Azure 資料工廠中使用複製活動。  
 
樣本具有下列資料工廠實體︰

1.  連結的類型[OnPremisesOracle](data-factory-onprem-oracle-connector.md#oracle-linked-service-properties)的服務。
2.  連結的類型[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)的服務。
3.  輸入的[資料集](data-factory-create-datasets.md)的類型[OracleTable](data-factory-onprem-oracle-connector.md#oracle-dataset-type-properties)。 
4.  輸出[資料集](data-factory-create-datasets.md)的類型[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)。
5.  [管線](data-factory-create-pipelines.md)與[OracleSource](data-factory-onprem-oracle-connector.md#oracle-copy-activity-type-properties)為來源和[BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)做接收的複製活動。

範例將資料從內部部署 Oracle 資料庫中的表格至 blob 每小時。 如需有關範例中使用的各種內容的詳細資訊，請參閱追蹤範例的各節中的文件。

**Oracle 連結服務︰**

    {
      "name": "OnPremisesOracleLinkedService",
      "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
          "ConnectionString": "data source=<data source>;User Id=<User Id>;Password=<Password>;",
          "gatewayName": "<gateway name>"
        }
      }
    }

**Azure Blob 儲存體連結服務︰**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
      }
    }

**Oracle 輸入資料集︰**

範例假設有在 Oracle 中建立表格 「 我的表格]，並將其包含稱為 「 timestampcolumn 」 時間] 資料數列的資料行。 

設定 「 外部 」:"true"通知資料工廠服務，資料集外部資料 factory，並不會所產生的資料工廠中的活動。

    {
        "name": "OracleInput",
        "properties": {
            "type": "OracleTable",
            "linkedServiceName": "OnPremisesOracleLinkedService",
            "typeProperties": {
                "tableName": "MyTable"
            },
               "external": true,
            "availability": {
                "offset": "01:00:00",
                "interval": "1",
                "anchorDateTime": "2014-02-27T12:00:00",
                "frequency": "Hour"
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

每小時寫入資料至新 blob (頻率︰ 小時、 間隔︰ 1)。 Blob 的資料夾路徑和檔案名稱會根據正在處理的扇形區的開始時間，以動態方式評估。 年、 月、 日和開始時間的小時部分，則會使用的資料夾路徑。
    
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

管線包含複製活動，設定為使用的輸入與輸出資料集，並排定執行每小時。 在管線 JSON 定義，**來源**類型設定為 [ **OracleSource** ，**接收**類型] 設定為**BlobSink**。  具有**oracleReaderQuery**屬性指定 SQL 查詢複製過去的小時內，選取資料。

    
    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
          {
            "name": "OracletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [
              {
                "name": " OracleInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureBlobOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "OracleSource",
                "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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


您需要調整查詢字串根據日期 Oracle 資料庫中的設定方式。 如果您看到下列錯誤訊息︰ 

    Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

若要變更的查詢 （使用 to_date 函數） 下列範例所示︰

    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\')  AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"

## <a name="sample-copy-data-from-azure-blob-to-oracle"></a>範例︰ 從 Azure Blob 資料複製到 Oracle
此範例會顯示如何將資料複製到內部部署 Oracle 資料庫 Azure Blob 儲存體。 不過，資料可以複製**直接**從任何來源敘述[以下](data-factory-data-movement-activities.md#supported-data-stores)Azure 資料工廠中使用複製活動。  
 
樣本具有下列資料工廠實體︰

1.  連結的類型[OnPremisesOracle](data-factory-onprem-oracle-connector.md#oracle-linked-service-properties)的服務。
2.  連結的類型[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)的服務。
3.  輸入的[資料集](data-factory-create-datasets.md)的類型[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)。
4.  輸出[資料集](data-factory-create-datasets.md)的類型[OracleTable](data-factory-onprem-oracle-connector.md#oracle-dataset-type-properties)。 
5.  [管線](data-factory-create-pipelines.md)與使用[BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)為來源[OracleSink](data-factory-onprem-oracle-connector.md#oracle-copy-activity-type-properties)為接收的複製活動。

範例將資料從 blob 至內部部署 Oracle 資料庫中資料表的每個小時。 如需有關範例中使用的各種內容的詳細資訊，請參閱追蹤範例的各節中的文件。

**Oracle 連結服務︰**

    {
      "name": "OnPremisesOracleLinkedService",
      "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
          "ConnectionString": "data source=<data source>;User Id=<User Id>;Password=<Password>;",
          "gatewayName": "<gateway name>"
        }
      }
    }

**Azure Blob 儲存體連結服務︰**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
      }
    }

**Azure Blob 輸入資料集**

資料從挑選新 blob 每小時 (頻率︰ 小時、 間隔︰ 1)。 Blob 的資料夾路徑和檔案名稱會根據正在處理的扇形區的開始時間，以動態方式評估。 年、 月和日的開始時間部分，會使用的資料夾路徑和檔案名稱會使用的開始時間的小時部分。 「 外部 」:"true"設定通知的資料工廠服務，此表格外部資料 factory，而且不會所產生的資料工廠活動。

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

**Oracle 輸出資料集︰**

範例假設 Oracle 中建立表格 「 我的表格]。 建立資料表中的欄數相同的 Oracle，如您預期 Blob CSV 檔案，以包含。 新的資料列會新增至資料表的每個小時。

    {
        "name": "OracleOutput",
        "properties": {
            "type": "OracleTable",
            "linkedServiceName": "OnPremisesOracleLinkedService",
            "typeProperties": {
                "tableName": "MyTable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": "1"
            }
        }
    }


**管線與複製活動︰**

管線包含複製活動，設定為使用的輸入與輸出資料集，並排定執行每小時。 在管線 JSON 定義，**來源**類型設定為 [ **BlobSource** ，**接收**類型設定為 [ **OracleSink**。  

    
    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
          {
            "name": "AzureBlobtoOracle",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureBlobInput"
              }
            ],
            "outputs": [
              {
                "name": "OracleOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "OracleSink"
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


## <a name="oracle-linked-service-properties"></a>Oracle 連結服務屬性

下表提供 JSON 項目的特定 Oracle 連結服務描述。 

屬性 | 描述 | 所需
-------- | ----------- | --------
類型 | [類型] 屬性必須設定為︰ **OnPremisesOracle** | [是]
連接字串 | 指定連接至 Oracle 資料庫執行個體的 [連接字串] 屬性所需的資訊。 | [是] 
gatewayName | 閘道器的名稱，用來連線到內部部署 Oracle 伺服器 | [是]

如需詳細資訊設定與內部部署 Oracle 資料來源的認證，請參閱[設定認證與安全性](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security)。
## <a name="oracle-dataset-type-properties"></a>Oracle 資料集類型屬性

節與適用於定義資料集的內容的完整清單，請參閱[建立資料集](data-factory-create-datasets.md)。 例如結構、 可用性和資料集 JSON 原則] 區段都是類似的所有資料集類型 (Oracle，Azure blob、 Azure 資料表等等。)。
 
TypeProperties 區段不同資料集的每個類型，並提供的資料存放區中的資料位置的相關資訊。 TypeProperties 資料集的區段類型 OracleTable 具有下列屬性︰

屬性 | 描述 | 所需
-------- | ----------- | --------
表格名稱 | 連結的服務參照到 Oracle 資料庫中資料表的名稱。 | 不需要 （如果指定的**OracleSource** **oracleReaderQuery** ）

## <a name="oracle-copy-activity-type-properties"></a>Oracle 複製活動類型的內容

節與適用於定義活動的內容的完整清單，請參閱[建立管線](data-factory-create-pipelines.md)文章。 例如名稱、 描述、 輸入與輸出的資料表，以及原則的內容，可針對所有類型的活動。 

> [AZURE.NOTE]
複製活動會只有一個輸入，而產生單一輸出。

活動 typeProperties 一節中提供的屬性另一方面會隨著每個活動類型。 複製活動，而有所不同的來源和接收類型。

### <a name="oraclesource"></a>OracleSource
複製活動時來源類型**OracleSource**的下列屬性會出現在**typeProperties** ] 區段中︰

屬性 | 描述 |允許的值 | 所需
-------- | ----------- | ------------- | --------
oracleReaderQuery | 使用自訂查詢資料。 | SQL 查詢字串。 例如︰ 選取*從我的表格<br/><br/>如果未指定執行 SQL 陳述式︰ 選取*從我的表格 | 不需要 （如果指定的**資料集**的**表格名稱**）

### <a name="oraclesink"></a>OracleSink
**OracleSink**支援下列屬性︰

屬性 | 描述 | 允許的值 | 所需
-------- | ----------- | -------------- | --------
writeBatchTimeout | 等待的時間供批次插入作業完成逾時。 | 時段<br/><br/> 範例︰ 00:30:00 （30 分鐘）。 | 無
writeBatchSize | 緩衝大小到達 writeBatchSize 時，則您可以插入 SQL 資料表的資料。   | 整數 （數字的資料列）| 不 (預設︰ 10000)  
sqlWriterCleanupScript | 指定要執行，例如清除資料的特定的扇形區之後複製活動的查詢。 | 陳述式的查詢。 | 無
sliceIdentifierColumnName | 指定要填入自動產生的扇形區識別碼] 用來清理特定的扇形區，當重新執行的資料複製活動的欄名稱。 | 具有 binary(32) 的資料類型的資料行的資料行名稱。 | 無


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-oracle"></a>Oracle 類型對應

所述[資料移動活動](data-factory-data-movement-activities.md)文件複製活動會執行接收類型，使用下列步驟 2 方法來源類型的自動輸入轉換︰

1. 從原生來源類型轉換成.NET 類型
2. 從.NET 類型轉換成原生接收類型

當您可以將資料從 Oracle，下列對應會使用從 Oracle 資料型別.NET 類型，反之亦然。

Oracle 資料類型 | .NET framework 資料類型
---------------- | ------------------------
BFILE | Byte]
BLOB | Byte]
字元 | 字串
CLOB | 字串
日期 | 日期時間
浮動時間 | 小數位數
整數 | 小數位數
以月間隔年 | Int32
以秒間隔日 | 時段
長 | 字串
長資料列 | Byte]
NCHAR | 字串
NCLOB | 字串
數字 | 小數位數
NVARCHAR2 | 字串
原始 | Byte]
列 ID | 字串
時間戳記 | 日期時間
本機的時區的時間戳記 | 日期時間
時區的時間戳記 | 日期時間
正負號的整數 | 數字
VARCHAR2 | 字串
XML | 字串

## <a name="troubleshooting-tips"></a>疑難排解提示

**問題︰**您會看到下列**錯誤訊息**︰ 複製活動符合無效的參數: 「 UnknownParameterName 」，詳細的訊息︰ 找不到要求的.Net Framework 資料提供者。 可能不會安裝 」。  

**可能的原因︰**

1. .NET Framework Data Provider for Oracle 未安裝。
2. .NET Framework Data Provider for Oracle.NET Framework 2.0 已安裝，而且找不到的.NET Framework 4.0 資料夾中。 

**解決/因應措施︰**

1. 如果您還沒有安裝 Oracle，[將其安裝](http://www.oracle.com/technetwork/topics/dotnet/downloads/).NET 提供者，並再試一次此案例。 
2. 如果您收到錯誤訊息，即使安裝提供者，請執行下列步驟︰ 
    1. 從開啟的.NET 2.0 的電腦設定資料夾︰ <system disk>: \Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config。
    2. 搜尋**Oracle Data Provider for.NET**程式，並您應該可以尋找項目，如下列範例 unwn 如下所示的範例取消der **system.data** -> **DbProviderFactories**:
            “<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />”
2.  這個項目複製至 machine.config 檔案在下列 v4.0 資料夾︰ <system disk>: \Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config，然後變更要 4.xxx.x.x 的版本。
3.  執行 「 < ODP.NET 已安裝路徑 > \11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll 「 安裝全域的組件快取 (GAC) `gacutil /i [provider path]`。



[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]


## <a name="performance-and-tuning"></a>效能及調整  
請參閱[複製活動的效能與微調指南](data-factory-copy-activity-performance.md)，瞭解如何要素 Azure 資料工廠，並將它最佳化的各種方式中的資料移動 （複製活動） 的效能的影響。
