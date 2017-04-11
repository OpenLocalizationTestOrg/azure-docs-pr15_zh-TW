<properties 
    pageTitle="建立資料集 Azure 資料工廠 |Microsoft Azure" 
    description="瞭解如何在 Azure 資料工廠中建立資料集，以使用 offset 等 anchorDateTime 屬性的範例。"
    keywords="建立資料集，資料集範例，位移範例"
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/13/2016" 
    ms.author="shlo"/>

# <a name="datasets-in-azure-data-factory"></a>Azure 資料工廠中的資料集
本文說明 Azure 資料工廠中的資料集，並包含例如 offset、 anchorDateTime 和位移/樣式資料庫的範例。

當您建立資料集時，您要建立指向您想要處理的資料。 處理資料 （輸入輸出） 活動和管線中所含的活動。 輸入資料集表示輸入的管線中的活動，輸出資料集表示活動的輸出效果。

資料集識別不同的資料存放區，例如資料表、 檔案、 資料夾及文件內的資料。 建立資料集之後，您可以使用其管道的活動。 例如，資料集可以複製活動或 HDInsightHive 活動的輸入輸出資料集。 Azure 入口網站可讓您所有的管線和資料的輸入與輸出的視覺版面配置。 一眼，您可以看到所有關聯與相依性的管線您所有的來源，讓您隨時都瞭解資料來自位置，就能位置。

Azure 資料工廠，您可以從資料集取得資料管線中使用複製活動。

> [AZURE.NOTE] 如果您是 Azure 資料工廠的新手，請參閱[Azure 資料工廠簡介](data-factory-introduction.md)Azure 資料工廠服務的概觀。 請參閱[建立您的第一個資料工廠](data-factory-build-your-first-pipeline.md)教學課程中建立您的第一個資料工廠。 這兩篇文章提供背景需要進一步瞭解這篇文章的資訊。 

## <a name="define-datasets"></a>定義資料集
Azure 資料工廠資料集的定義如下︰ 


    {
        "name": "<name of dataset>",
        "properties": {
            "type": "<type of dataset: AzureBlob, AzureSql etc...>",
            "external": <boolean flag to indicate external data. only for input datasets>,
            "linkedServiceName": "<Name of the linked service that refers to a data store.>",
            "structure": [
                {
                    "name": "<Name of the column>",
                    "type": "<Name of the type>"
                }
            ],
            "typeProperties": {
                "<type specific property>": "<value>",
                "<type specific property 2>": "<value 2>",
            },
            "availability": {
                "frequency": "<Specifies the time unit for data slice production. Supported frequency: Minute, Hour, Day, Week, Month>",
                "interval": "<Specifies the interval within the defined frequency. For example, frequency set to 'Hour' and interval set to 1 indicates that new data slices should be produced hourly>"
            },
           "policy": 
            {      
            }
        }
    }

下表說明在上述 JSON 屬性︰   

| 屬性 | 描述 | 所需 | 預設值 |
| -------- | ----------- | -------- | ------- |
| 名稱 | 資料集的名稱。 命名規則，請參閱[Azure 資料工廠-命名規則](data-factory-naming-rules.md)。 | [是] | NA |
| 類型 | 輸入資料集]。 指定的其中一個支援 Azure 資料工廠類型 (例如︰ AzureBlob、 AzureSqlTable)。 <br/><br/>如需詳細資訊，請參閱[資料集類型](#Type)。 | [是] | NA |
| 結構 | 資料集的結構描述<br/><br/>如需詳細資訊，請參閱[資料集結構](#Structure)] 區段。 | [否]。 | NA |
| typeProperties | 對應到所選類型的屬性。 支援的類型和其屬性的詳細資訊，請參閱[資料集類型](#Type)一節。 | [是] | NA |
| 外部 | 若要指定是否資料集明確產生的資料工廠管道的郵件或不布林旗標。  | 無 | false | 
| 顯示狀態 | 定義處理視窗或資料集生產切割模型。 <br/><br/>如需詳細資訊，請參閱[資料集可用性](#Availability)] 區段。 <br/><br/>如需詳細資料集切割模型，請參閱[排程和執行](data-factory-scheduling-and-execution.md)文章。 | [是] | NA
| 原則 | 定義準則或資料集的扇形區，都必須符合的條件。 <br/><br/>如需詳細資訊，請參閱[資料集原則](#Policy)] 區段。 | 無 | NA |

## <a name="dataset-example"></a>資料集範例
在下列範例中，資料集表示名為 [**我的表格** **Azure SQL 資料庫**中的資料表。 

    {
        "name": "DatasetSample",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": 
            {
                "tableName": "MyTable"
            },
            "availability": 
            {
                "frequency": "Day",
                "interval": 1
            }
        }
    }

請注意下列重點︰ 

- 類型] 設定為 AzureSqlTable。
- 表格名稱類型] 屬性 （特定 AzureSqlTable 類型） 會設定為 [我的表格。
- linkedServiceName 指的是連結類型 AzureSqlDatabase 的服務。 請參閱下列連結服務的定義。 
- 可用性頻率設定為一天，間隔設定為 1，這表示扇形區會產生每日。  

AzureSqlLinkedService 定義如下︰

    {
        "name": "AzureSqlLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "description": "",
            "typeProperties": {
                "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
            }
        }
    }

在上述 JSON 中︰ 

- 類型] 設定為 AzureSqlDatabase
- 連接字串類型屬性會指定資訊以連線至 SQL Azure 資料庫。  


如您所見，連結的服務會定義如何連線到 Azure SQL 資料庫。 資料集定義哪些資料表作為輸出入管線的活動。 [活動] 區段中您的[管線](data-factory-create-pipelines.md)JSON 指定是否要將資料集使用與輸入或輸出資料集。


> [AZURE.IMPORTANT] 除非資料集所產生的 Azure 資料工廠，將其標示為**外部**。 通常這項設定適用於輸入的管線中的第一個活動。   

## <a name="Type"></a>資料集的類型
支援的資料來源與資料集類型會對齊。 請參閱參考[資料移動活動](data-factory-data-movement-activities.md#supported-data-stores)文件類型和設定資料集的相關資訊中的主題。 例如，如果您使用的從 Azure SQL 資料庫的資料，Azure SQL 資料庫的清單中按一下要查看的詳細的資訊的支援的資料儲存區。  

## <a name="Structure"></a>資料集結構
[**結構**] 區段中定義資料集的結構描述。 包含名稱和資料行資料類型的集合。  在下列範例中，資料集有三個資料行 slicetimestamp，專案名稱，然後 pageviews，及他們的年齡的類型︰ 字串與字串，小分別。

    structure:  
    [ 
        { "name": "slicetimestamp", "type": "String"},
        { "name": "projectname", "type": "String"},
        { "name": "pageviews", "type": "Decimal"}
    ]

## <a name="Availability"></a>資料集的顯示狀態
在資料集中的 [**可用性**] 區段定義處理視窗 （小時、 每天、 每週等） 或切割模型資料集。 如需資料集切割和相依性模型的詳細資訊，請參閱[排程和執行](data-factory-scheduling-and-execution.md)文件。 

下列可用性] 區段中指定的輸出資料集是產生小時 （或） 輸入資料集是每小時可用︰

    "availability": 
    {   
        "frequency": "Hour",        
        "interval": 1   
    }

下表說明您可以使用 [可用性] 區段中的內容︰ 

| 屬性 | 描述 | 所需 | 預設值 |
| -------- | ----------- | -------- | ------- |
| 頻率 | 指定資料集的扇形區生產的時間單位。<br/><br/>**支援頻率**︰ 分、 小時、 天、 週、 月 | [是] | NA |
| 間隔 | 指定加成頻率<br/><br/>「 頻率 x 間隔 」 會決定扇形區所產生的頻率。<br/><br/>如果您需要會切割下限為基礎的資料集，您可以設定**頻率****小時**，並為**1**的**時間間隔**。<br/><br/>**附註︰**如果您指定為分鐘的頻率，我們建議您總共不超過 15 設定的間隔時間 | [是] | NA |
| 樣式 | 指定是否應該在間隔開始/結束產生扇形區。<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>如果 Frequency 設定為 [月份，樣式設定為 [EndOfInterval 扇形區會產生上個月的最後一天。 如果 StartOfInterval 設定樣式，扇形區不會產生上個月的第一天。<br/><br/>如果頻率設定為一天，樣式設定為 [EndOfInterval 扇形區不會產生一天的最後一個小時內。<br/><br/>如果 frequency 不設為小時，樣式設定為 [EndOfInterval 扇形區不會產生結尾的小時。 例如，對於 1-2 下午期間的扇形區，扇形區會產生在 2 PM。 | 無 | EndOfInterval |
| anchorDateTime | 定義時間用排程器，來計算資料集的扇形區範圍的絕對位置。 <br/><br/>**附註︰**如果 AnchorDateTime 有更細微比頻率的日期部分會忽略更細緻的組件。 <br/><br/>例如，如果**間隔**是**每小時**(頻率︰ 小時與間隔︰ 1) **AnchorDateTime**包含**分和秒**，然後 AnchorDateTime**分鐘和秒鐘**部分會被略過。 | 無 | 01/01/0001 |
| 位移 | 所用的開頭和結尾的所有資料集的扇形區移動的時段。 <br/><br/>**附註︰**如果未指定 anchorDateTime] 與 [位移，則結果為合併的 shift。 | 無 | NA |

### <a name="offset-example"></a>offset 的範例

每日切割開頭的上午，而不是預設午夜 6。 

    "availability":
    {
        "frequency": "Day",
        "interval": 1,
        "offset": "06:00:00"
    }

**Frequency 不**設定為**一天**，**間隔**設定為**1** （一天）︰ 如果您想要產生上午 6 而不是預設時間的扇形區︰ 12 AM。 請記住，這次是 UTC 時間。 

## <a name="anchordatetime-example"></a>anchorDateTime 範例

**範例︰** 23 小時資料集的扇形區開始的 2007年-04-19T08:00:00

    "availability": 
    {   
        "frequency": "Hour",        
        "interval": 23, 
        "anchorDateTime":"2007-04-19T08:00:00"  
    }

## <a name="offsetstyle-example"></a>offset 樣式範例

如果您需要在每個月的資料集在特定的日期和時間 （假設在每個月 8:00 AM，3rd），使用應該執行**位移**標記以設定日期和時間。 

    {
      "name": "MyDataset",
      "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
          "tableName": "MyTable"
        },
        "availability": {
          "frequency": "Month",
          "interval": 1,
          "offset": "3.08:10:00",
          "style": "StartOfInterval"
        }
      }
    }


## <a name="Policy"></a>資料集原則

[**原則**] 區段中的資料集定義定義準則或資料集的扇形區，都必須符合的條件。

### <a name="validation-policies"></a>驗證原則

| 原則名稱 | 描述 | 套用至 | 所需 | 預設值 |
| ----------- | ----------- | ---------- | -------- | ------- |
| minimumSizeMB | 驗證的**Azure blob**中的資料符合最小的大小需求 （以 mb 計）。 | Azure Blob | 無 | NA |
|minimumRows | 驗證**Azure SQL 資料庫**或**Azure 資料表**中的資料包含最小的列數。 | <ul><li>Azure SQL 資料庫</li><li>Azure 資料表</li></ul> | 無 | NA

#### <a name="examples"></a>範例

**minimumSizeMB:**

    "policy":
    
    {
        "validation":
        {
            "minimumSizeMB": 10.0
        }
    }

**minimumRows**

    "policy":
    {
        "validation":
        {
            "minimumRows": 100
        }
    }

### <a name="external-datasets"></a>外部資料集

外部資料集是不由資料工廠執行管線所產生的項目。 如果資料集標示為**外部**時，可能會定義**ExternalData**原則影響行為的資料集的扇形區顯示狀態。 

除非資料集所產生的 Azure 資料工廠，將其標示為**外部**。 這項設定通常適用於的管線中的第一個活動的輸入除非正在使用的活動或管線鏈結。 

| 名稱 | 描述 | 所需 | 預設值  |
| ---- | ----------- | -------- | -------------- |
| dataDelay | 若要延遲的指定的扇形區的外部資料可用性核取的時間。 例如，如果資料是應該可供每小時，檢查外部資料而且對應的扇形區準備可以延遲使用 dataDelay。<br/><br/>僅適用於目前時間。  例如，如果是 1:00 PM 立即，且此值為 10 分鐘的時間，驗證啟動下午 1:10。<br/><br/>此設定不會影響過去的扇形區 (使用扇形區結束時間 + dataDelay 的扇形區 < 現在) 而不會任何延遲的處理。<br/><br/>大於 23:59 小時需要指定使用 day.hours:minutes:seconds 格式的時間。 例如，若要指定 24 小時，不是使用 24:00:00;請改用 1.00:00:00。 如果您是使用 24:00:00，它會被視為 24 天 (24.00:00:00)。 1 天和 4 小時，指定 1:04:00:00。 | 無 | 0 |
| retryInterval | 等待之間的時間失敗和下一個重新嘗試。 適用於目前時間。如果前一個嘗試失敗，我們等這之後最後嘗試。 <br/><br/>如果這是 1:00 pm 現在，我們會開始第一次嘗試。 如果要完成的第一個驗證檢查的工期，而且 1 分鐘作業失敗下, 一步重試是位於 1:00 + 1 的最小值 （工期） + 1 的最小值 （重試間隔） = 1:02 pm。 <br/><br/>過去的扇形區，如有任何延遲。 重試] 就會立即發生。 | 無 | 00:01:00 （1 分鐘） | 
| retryTimeout | 每一次重試嘗試逾時。<br/><br/>如果此屬性設定為 10 分鐘的時間，驗證必須在 10 分鐘的時間內完成。 如果花超過 10 分鐘的時間來進行驗證，重試逾時。<br/><br/>如果所有嘗試驗證都逾時，會將扇形區標示為逾時。 | 無 | 00:10:00 （10 分鐘） |
| maximumRetry | 若要檢查的外部資料可用性次數。 允許的最大值為 10。 | 無 | 3 | 

## <a name="scoped-datasets"></a>範圍的資料集
您可以建立使用**資料集**] 屬性的管線的範圍的資料集。 此管線內的活動，但不是由其他管線的活動，只可以用這些資料集。 下列範例會定義的管線與兩個資料集-InputDataset rdc 和 OutputDataset rdc-管線內使用︰  

> [AZURE.IMPORTANT] 範圍的資料集只支援一次性管線 (**pipelineMode**設定為 [ **OneTime**)。 如需詳細資訊，請參閱[Onetime 管道的郵件](data-factory-scheduling-and-execution.md#onetime-pipeline)。

    {
        "name": "CopyPipeline-rdc",
        "properties": {
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource",
                            "recursive": false
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "InputDataset-rdc"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "OutputDataset-rdc"
                        }
                    ],
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1,
                        "style": "StartOfInterval"
                    },
                    "name": "CopyActivity-0"
                }
            ],
            "start": "2016-02-28T00:00:00Z",
            "end": "2016-02-28T00:00:00Z",
            "isPaused": false,
            "pipelineMode": "OneTime",
            "expirationTime": "15.00:00:00",
            "datasets": [
                {
                    "name": "InputDataset-rdc",
                    "properties": {
                        "type": "AzureBlob",
                        "linkedServiceName": "InputLinkedService-rdc",
                        "typeProperties": {
                            "fileName": "emp.txt",
                            "folderPath": "adftutorial/input",
                            "format": {
                                "type": "TextFormat",
                                "rowDelimiter": "\n",
                                "columnDelimiter": ","
                            }
                        },
                        "availability": {
                            "frequency": "Day",
                            "interval": 1
                        },
                        "external": true,
                        "policy": {}
                    }
                },
                {
                    "name": "OutputDataset-rdc",
                    "properties": {
                        "type": "AzureBlob",
                        "linkedServiceName": "OutputLinkedService-rdc",
                        "typeProperties": {
                            "fileName": "emp.txt",
                            "folderPath": "adftutorial/output",
                            "format": {
                                "type": "TextFormat",
                                "rowDelimiter": "\n",
                                "columnDelimiter": ","
                            }
                        },
                        "availability": {
                            "frequency": "Day",
                            "interval": 1
                        },
                        "external": false,
                        "policy": {}
                    }
                }
            ]
        }
    }