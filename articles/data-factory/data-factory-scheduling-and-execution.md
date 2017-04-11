<properties
    pageTitle="排程與執行資料工廠 |Microsoft Azure"
    description="瞭解排程和執行層面 Azure 資料 Factory 應用程式模型。"
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="spelluru"/>

# <a name="data-factory-scheduling-and-execution"></a>資料工廠排程和執行
本文說明 Azure 資料 Factory 應用程式模型的排程和執行方面。 

## <a name="prerequisites"></a>必要條件
本文假設您了解資料 Factory 應用程式模型概念，包括活動、 管線、 連結的服務與資料集的基本概念。 Azure 資料工廠基本概念，請參閱下列文章︰

- [資料工廠簡介](data-factory-introduction.md)
- [管線](data-factory-create-pipelines.md)
- [資料集](data-factory-create-datasets.md) 

## <a name="schedule-an-activity"></a>排程活動

使用 JSON 活動的 [排程] 區段中，您可以指定活動的週期性的排程。 例如，您可以排程活動的每個小時，如下所示︰

    "scheduler": {
        "frequency": "Hour",
        "interval": 1
    },  

![排程器範例](./media/data-factory-scheduling-and-execution/scheduler-example.png)

如下圖所示的圖表中，指定活動的排程會建立一系列的解決視窗。 解決 windows 是一系列的固定大小、 非重疊、 連續的時間間隔。 活動這些邏輯解決視窗稱為 「*活動視窗*。

目前正在執行的活動視窗中，您可以存取 [活動] 視窗與活動 JSON [WindowStart](data-factory-functions-variables.md#data-factory-system-variables)和[WindowEnd](data-factory-functions-variables.md#data-factory-system-variables)系統變數相關聯的時間間隔。 您可以使用這些變數不同用途，在您的活動 JSON。 例如，您可以使用這些選取代表時間系列資料的輸入與輸出資料集的資料。

**排程器**屬性支援**可用性**屬性為相同的子資料集。 如需詳細資訊，請參閱[資料集的可用性](data-factory-create-datasets.md#Availability)。 範例︰ 在特定時間位移，排程，或設定要對齊的開頭或結尾的 「 活動 」 視窗區間處理的模式。

您可以指定的活動的**排程器**內容，但這是**選擇性**屬性。 如果您指定的屬性，它必須符合您指定的輸出資料集定義的頻率。 目前，輸出資料集是什麼磁碟機排程，因此您必須建立輸出資料集，即使活動不會產生任何輸出。 如果活動不需要任何輸入，您可以略過建立輸入資料集。

## <a name="time-series-datasets-and-data-slices"></a>時間數列資料集和資料扇形區

時間數列資料是連續的一系列資料點的通常包含的間隔時間內所做的連續度量單位。 常見的時間數列資料例子感應器和應用程式遙測資料。

與資料工廠，您可以處理批次的方式，與活動的數列資料執行的時間。 一般而言，有週期性頻率，輸入的資料送達和輸出資料產生的需求。 此頻率所模型化**可用性**指定資料集，如下所示︰

    "availability": {
      "frequency": "Hour",
      "interval": 1
    },

使用資料，並產生的活動執行的每個單位稱為資料扇形區。 下圖顯示範例有一個輸入的資料集的活動和一個輸出資料集。 這些資料集有**顯示狀態**設定為每小時的頻率。

![可用性排程器](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

上述圖表會顯示每小時資料扇形區，輸入與輸出資料集。 下圖顯示三個輸入的扇形區準備好進行處理。 10-11 AM 活動正在執行，產生的 10-11 AM 輸出扇形區。

您可以存取與目前資料集 JSON 變數[SliceStart](data-factory-functions-variables.md#data-factory-system-variables)和[SliceEnd](data-factory-functions-variables.md#data-factory-system-variables)中所產生的扇形區相關聯的時間間隔。

目前，資料工廠需要完全活動中指定的排程符合指定的輸出資料集的**顯示狀態**的排程。 因此， **WindowStart**、 **WindowEnd**、 **SliceStart**及**SliceEnd**一律對應到相同的時間週期] 與 [單一輸出扇形區。

如需不同的內容的可用性] 區段的詳細資訊，請參閱[建立資料集](data-factory-create-datasets.md)。

## <a name="move-data-from-sql-database-to-blob-storage"></a>將 SQL 資料庫資料移到 Blob 儲存體

現在就讓我們將的一些事項一起和動作建立管線將資料複製從 Azure SQL 資料庫表格到 Azure Blob 儲存體每小時。

**輸入︰ Azure SQL 資料庫資料集**

    {
        "name": "AzureSqlInput",
        "properties": {
            "published": false,
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "MyTable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }


**頻率**設為**小時**和 [可用性] 區段中**間隔**設為**1** 。

**輸出︰ Azure Blob 儲存體資料集**

    {
        "name": "AzureBlobOutput",
        "properties": {
            "published": false,
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "mypath/{Year}/{Month}/{Day}/{Hour}",
                "format": {
                    "type": "TextFormat"
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


**頻率**設為**小時**和 [可用性] 區段中**間隔**設為**1** 。



**活動︰ 複製活動**

    {
        "name": "SamplePipeline",
        "properties": {
            "description": "copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "name": "AzureSQLtoBlob",
                    "description": "copy activity",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 100000,
                            "writeBatchTimeout": "00:05:00"
                        }
                    },
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
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    }
                }
            ],
            "start": "2015-01-01T08:00:00Z",
            "end": "2015-01-01T11:00:00Z"
        }
    }


此範例顯示活動排程和資料集可用性節設定為每小時的頻率。 此範例顯示如何您可以使用**WindowStart**和**WindowEnd**選取活動相關資料執行，並將其複製到適當的**folderPath**與 blob。 **FolderPath**是參數化有每小時的另一個資料夾。

當這三個扇形區之間 8 – 11 AM 執行時，Azure SQL 資料庫中的資料是，如下所示︰

![範例輸入](./media/data-factory-scheduling-and-execution/sample-input-data.png)

管線部署後，Azure blob 填入，如下所示︰

-   檔案 mypath/2015年/1/1/8/資料。&lt;Guid&gt;.txt 資料

            10002345,334,2,2015-01-01 08:24:00.3130000
            10002345,347,15,2015-01-01 08:24:00.6570000
            10991568,2,7,2015-01-01 08:56:34.5300000

    > [AZURE.NOTE] &lt;Guid&gt;會取代為 [實際的 guid。 檔案名稱的範例︰ Data.bcde1348-7620-4f93-bb89-0eed3455890b.txt
-   檔案 mypath/2015年/1/1/9/資料。&lt;Guid&gt;.txt 資料︰

            10002345,334,1,2015-01-01 09:13:00.3900000
            24379245,569,23,2015-01-01 09:25:00.3130000
            16777799,21,115,2015-01-01 09:47:34.3130000
-   檔案 mypath/2015年/1/1/10/資料。&lt;Guid&gt;.txt 不含資料。


## <a name="active-period-for-pipeline"></a>作用中的期間的管線

[建立管線](data-factory-create-pipelines.md)推出的作用中的期間設定**開始**和**結束**屬性來指定管線的概念。

您可以在過去設定的管線作用中期間的開始日期。 資料工廠自動計算 （背景填滿） 過去的所有資料扇形區，並開始處理它們。

## <a name="parallel-processing-of-data-slices"></a>平行處理資料的扇形區
您可以設定後填滿資料扇形區，若要同時執行的活動 JSON 原則] 區段中設定**並行**屬性。 如需有關此屬性的詳細資訊，請參閱[建立管線](data-factory-create-pipelines.md)。

## <a name="rerun-a-failed-data-slice"></a>重新執行資料失敗扇形區 
您可以監視扇形區的執行豐富、 視覺化的方式。 如需詳細資訊，請參閱[監控和管理管線使用 Azure 入口網站刀](data-factory-monitor-manage-pipelines.md)或[監控和管理應用程式](data-factory-monitor-manage-app.md)。

請考慮下列範例中，會顯示兩個活動。 Activity1 作為做為輸入由 Activity2 來產生最後的輸出時間數列資料集的輸出產生扇形區時間數列資料的集。

![失敗的扇形區](./media/data-factory-scheduling-and-execution/failed-slice.png)

下圖顯示的出三個最近的扇形區，發生錯誤的 Dataset2 產生 9 10 AM 扇形區。 資料工廠會自動追蹤時間數列資料集的相依性。 如此一來，就無法啟動 9-上午 10 下游扇形區執行的活動。

資料工廠監控和管理工具可讓您輕鬆地找到問題的根本原因，並修正失敗的扇形區的診斷記錄向下切入。 您已修正問題之後，您可以輕鬆地開始以產生失敗的扇形區執行的活動。 如需如何重新執行，並了解資料扇形區狀態轉換的詳細資訊，請參閱[監控和管理管線使用 Azure 入口網站刀](data-factory-monitor-manage-pipelines.md)或[監控和管理應用程式](data-factory-monitor-manage-app.md)。

您的**Dataset2**重新執行 9-上午 10 扇形區之後，資料工廠啟動 9 10 AM 相依扇形區的執行的最後一個資料集。

![重新執行失敗扇形區](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

## <a name="run-activities-in-a-sequence"></a>序列中執行的活動
您可以藉由設定一個活動的輸出資料集，以輸入的資料集的其他活動鏈結 （另一個之後執行一個活動） 的兩個活動。 活動可以在相同的管線，或在不同的管線中。 第二個活動執行僅限時的第一個順利完成。

例如，請考慮下列情況︰

1.  管線 P1 有活動 A1，需要外部輸入資料集 D1，並產生輸出資料集 D2。
2.  管線 P2 有活動 A2，需要輸入資料集 D2，並產生輸出資料集 D3。

在此案例中，活動 A1 和 A2 位於不同的管線。 活動 A1 執行時的外部資料和提供的排程的可用性頻率為止。 活動時排程的扇形區，從 D2，就可以使用排程的可用性頻率到達，會執行 A2。 在資料集中 D2 扇形區的其中一種錯誤時，A2 才會開始執行該扇形區的可供使用。

[圖表] 檢視看起來如下圖︰

![串連兩個管線活動](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

如先前所述，可以在相同的管線活動。 [圖表] 檢視相同的管線中兩個活動看起來如下圖︰

![在相同的管線鏈結活動](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

### <a name="copy-sequentially"></a>依序複製
一個接一個連續/排列方式執行多個複本作業可能是。 例如，您可能會有兩個複製活動中使用下列輸入的資料輸出資料集的管線 （CopyActivity1 和 CopyActivity2）︰   

CopyActivity1

輸入︰ 資料集。 成果︰ Dataset2。

CopyActivity2

輸入︰ Dataset2。  成果︰ Dataset3。

只有當 CopyActivity1 已經順利執行並 Dataset2 有，就會執行 CopyActivity2。

以下是範例管線 JSON:

    {
        "name": "ChainActivities",
        "properties": {
            "description": "Run activities in sequence",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "copyBehavior": "PreserveHierarchy",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "Dataset1"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "Dataset2"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00"
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "CopyFromBlob1ToBlob2",
                    "description": "Copy data from a blob to another"
                },
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "Dataset2"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "Dataset3"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00"
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "CopyFromBlob2ToBlob3",
                    "description": "Copy data from a blob to another"
                }
            ],
            "start": "2016-08-25T01:00:00Z",
            "end": "2016-08-25T01:00:00Z",
            "isPaused": false
        }
    }

請注意範例中，指定輸出資料集的第一份複本活動 (Dataset2) 會作為第二個活動的輸入。 因此，只有準備輸出中的資料集的第一個活動時，只會執行第二個活動。  

在範例中，CopyActivity2 可以有不同的輸入，例如 Dataset3，，但您將指定 Dataset2 做為 CopyActivity2，輸入，讓活動不會執行，直到 CopyActivity1 完成。 例如︰

CopyActivity1

輸入︰ Dataset1。 成果︰ Dataset2。

CopyActivity2

輸入︰ Dataset3，Dataset2。 成果︰ Dataset4。

    {
        "name": "ChainActivities",
        "properties": {
            "description": "Run activities in sequence",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "copyBehavior": "PreserveHierarchy",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "Dataset1"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "Dataset2"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00"
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "CopyFromBlobToBlob",
                    "description": "Copy data from a blob to another"
                },
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "Dataset3"
                        },
                        {
                            "name": "Dataset2"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "Dataset4"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00"
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "CopyFromBlob3ToBlob4",
                    "description": "Copy data from a blob to another"
                }
            ],
            "start": "2017-04-25T01:00:00Z",
            "end": "2017-04-25T01:00:00Z",
            "isPaused": false
        }
    }


請注意，在範例中，輸入的兩個資料集所指定的第二個複製活動。 當指定多個輸入時，只第一個輸入資料集用於複製資料，但其他資料集會作為相依性。 只有在符合下列條件後，會啟動 CopyActivity2:

- CopyActivity1 已順利完成，Dataset2 使用。 將資料複製到 Dataset4 時，不會使用這個資料集。 僅限作為排程的相依性的 CopyActivity2。   
- 使用 Dataset3。 此資料集代表複製到目的地的資料。  



## <a name="model-datasets-with-different-frequencies"></a>使用不同的頻率的模型資料集

範例中，輸入與輸出資料集] 和 [活動排程視窗頻率是相同。 某些情況下需要產生不同的一或多個輸入頻率頻率輸出的能力。 資料工廠支援模型這些案例。

### <a name="sample-1-produce-a-daily-output-report-for-input-data-that-is-available-every-hour"></a>範例 1︰ 產生可供每小時輸入資料的每日輸出報表

請考慮的案例中您所輸入度量單位資料從可用的感應器 Azure Blob 儲存體中的每個小時。 您想要產生每日彙總的報表，例如平均值、 最大值和最小值的統計資料與[資料工廠登錄區活動](data-factory-hive-activity.md)天。

以下是如何建立模型資料工廠此案例︰

**輸入資料集**

每小時輸入的檔案會遭到捨棄資料夾中，指定的日期。 輸入的顯示狀態設定為**小時**(頻率︰ 小時、 間隔︰ 1)。

    {
      "name": "AzureBlobInput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
          "partitionedBy": [
            { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
            { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
            { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
          ],
          "format": {
            "type": "TextFormat"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

**輸出資料集**

輸出檔案會建立每日當天的資料夾中。 輸出的顯示狀態設定為**一天**(頻率︰ 日期與時間間隔︰ 1)。


    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
          "partitionedBy": [
            { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
            { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
            { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
          ],
          "format": {
            "type": "TextFormat"
          }
        },
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

**活動︰ 登錄區管道的活動**

登錄區指令碼會收到下列程式碼片段所示，使用**WindowStart**變數的參數為適當的*日期時間*資訊。 登錄區指令碼會使用這個變數正確資料夾中的資料載入天並執行產生輸出的彙總。

        {  
            "name":"SamplePipeline",
            "properties":{  
            "start":"2015-01-01T08:00:00",
            "end":"2015-01-01T11:00:00",
            "description":"hive activity",
            "activities": [
                {
                    "name": "SampleHiveActivity",
                    "inputs": [
                        {
                            "name": "AzureBlobInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
                    "linkedServiceName": "HDInsightLinkedService",
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adftutorial\\hivequery.hql",
                        "scriptLinkedService": "StorageLinkedService",
                        "defines": {
                            "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
                            "Month": "$$Text.Format('{0:%M}',WindowStart)",
                            "Day": "$$Text.Format('{0:%d}',WindowStart)"
                        }
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },          
                    "policy": {
                        "concurrency": 1,
                        "executionPriorityOrder": "OldestFirst",
                        "retry": 2,
                        "timeout": "01:00:00"
                    }
                 }
             ]
           }
        }

下圖顯示從資料相依性的觀點案例。

![資料相依性](./media/data-factory-scheduling-and-execution/data-dependency.png)

每一天的輸出扇形區取決於 24 小時的扇形區中輸入資料集。 資料工廠來找出輸入資料落在產生的輸出扇形區為相同的時間週期內的扇形區，請以自動計算這些相依性。 如果任一 24 輸入扇形區無法使用，資料工廠會等候輸入的扇形區，啟動每日執行的活動前先做好準備。


### <a name="sample-2-specify-dependency-with-expressions-and-data-factory-functions"></a>範例 2︰ 使用運算式及資料工廠函數指定相依性

我們來看看另一種情況。 假設您有登錄區活動的程序輸入的兩個資料集。 每日，其中具有新資料，但其中每週取得新的資料。 假設您想要加入跨兩個輸入，並產生輸出，每一天。

哪些資料工廠簡單的方法查看右邊的數字自動輸入扇形區來處理資料扇形區時間期間無法運作的輸出對齊。

您必須指定的執行每項活動，資料 Factory 應使用上週的資料扇形區，每週的輸入資料集。 您在下列程式碼片段中所示使用 Azure 資料工廠函數，來實作這種行為。

**Input1: Azure blob**

第一個輸入是 Azure blob 每日更新。

    {
      "name": "AzureBlobInputDaily",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
          "partitionedBy": [
            { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
            { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
            { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
          ],
          "format": {
            "type": "TextFormat"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

**Input2: Azure blob**

Input2 是 Azure blob 每週更新。

    {
      "name": "AzureBlobInputWeekly",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
          "partitionedBy": [
            { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
            { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
            { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
          ],
          "format": {
            "type": "TextFormat"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 7
        }
      }
    }

**輸出︰ Azure blob**

輸出檔案每天資料夾中建立日期。 輸出的顯示狀態設定為 [**天**(頻率︰ 天、 間隔︰ 1)。

    {
      "name": "AzureBlobOutputDaily",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
          "partitionedBy": [
            { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
            { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
            { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
          ],
          "format": {
            "type": "TextFormat"
          }
        },
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

**活動︰ 登錄區管道的活動**

登錄區活動所需的兩個輸入的時間，並產生輸出扇形區每一天。 您可以指定每日輸出扇形區来根據每週輸入前一週的輸入的扇形區，如下所示。

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2015-01-01T08:00:00",
        "end":"2015-01-01T11:00:00",
        "description":"hive activity",
        "activities": [
          {
            "name": "SampleHiveActivity",
            "inputs": [
              {
                "name": "AzureBlobInputDaily"
              },
              {
                "name": "AzureBlobInputWeekly",
                "startTime": "Date.AddDays(SliceStart, - Date.DayOfWeek(SliceStart))",
                "endTime": "Date.AddDays(SliceEnd,  -Date.DayOfWeek(SliceEnd))"  
              }
            ],
            "outputs": [
              {
                "name": "AzureBlobOutputDaily"
              }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "type": "HDInsightHive",
            "typeProperties": {
              "scriptPath": "adftutorial\\hivequery.hql",
              "scriptLinkedService": "StorageLinkedService",
              "defines": {
                "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
                "Month": "$$Text.Format('{0:%M}',WindowStart)",
                "Day": "$$Text.Format('{0:%d}',WindowStart)"
              }
            },
            "scheduler": {
              "frequency": "Day",
              "interval": 1
            },          
            "policy": {
              "concurrency": 1,
              "executionPriorityOrder": "OldestFirst",
              "retry": 2,  
              "timeout": "01:00:00"
            }
           }
         ]
       }
    }


## <a name="data-factory-functions-and-system-variables"></a>資料工廠函數及系統變數   

函數和系統變數資料工廠支援的清單，請參閱[資料工廠函數和系統變數](data-factory-functions-variables.md)。

## <a name="data-dependency-deep-dive"></a>資料相依性深度剖析

活動執行產生資料集的扇形區，資料工廠會使用下列的*相依性模型*，來判斷 consumed 和活動所產生的資料集之間的關聯性。

輸入資料集產生的輸出資料集扇形區所需的時間範圍稱為*相依性期間*。

活動執行產生資料集的扇形區，只有在輸入的資料集相依性期間內的資料扇形區可後。 換句話說，所有輸入相依性期間內所包含的扇形區必須在**準備好**的狀態的活動執行產生輸出資料集扇形區。

若要產生的資料集扇形區，[**開始**，**結束**]，函數必須對應到相依性期間的資料集的扇形區。 這個函數是基本上的開頭和結尾的資料集的扇形區轉換的開頭和結尾的相依性期間的公式。 正式︰

    DatasetSlice = [start, end]
    DependecyPeriod = [f(start, end), g(start, end)]

**F**和**g**對應計算的開頭和結尾的輸入每一個活動的相依性期間的函數。

範例所示，相依性期間是相同的資料扇形區所產生的期間。 在下列情況下，資料工廠自動計算落在相依性期間內輸入扇形區。  

例如，在彙總範例中，每日產生輸出及輸入的資料都在每小時，資料扇形區期間是 24 小時的時間。 資料工廠尋找相關的每小時輸入切割的這段時間內，並讓輸出扇形區取決於輸入的扇形區。

您也可以提供您自己的對應的相依性期間，如下所示的範例，其中的輸入之一是每週和輸出扇形區不會產生每日。

## <a name="data-dependency-and-validation"></a>資料相依性和驗證

資料集可以有指定如何驗證扇形區執行所產生的資料之前準備好消耗驗證原則定義。 如需詳細資訊，請參閱[建立資料集](data-factory-create-datasets.md)。

在這種情況下，執行，完成的扇形區之後輸出扇形區狀態會變更為**等候**使用的**驗證**次狀態。 驗證扇形區之後，扇形區狀態變更為**好**。

如果資料扇形區已所產生的但未通過驗證，不會處理活動就會執行的下游取決於此扇形區的扇形區。

[監控和管理管線](data-factory-monitor-manage-pipelines.md)涵蓋資料工廠資料扇形區的各種狀態。

## <a name="external-data"></a>外部資料

資料集可以標示為外部 （如下所示下列 JSON 程式碼片段），意味著不產生的資料工廠。 在這種情況下，資料集原則可以有一組額外的描述驗證的參數，然後再試一次資料集的原則。 如需所有屬性的說明，請參閱[建立管線](data-factory-create-pipelines.md)。

類似於資料集所產生的資料工廠，資料扇形區的外部資料必須準備好可以處理相依扇形區之前。

    {
        "name": "AzureSqlInput",
        "properties":
        {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties":
            {
                "tableName": "MyTable"
            },
            "availability":
            {
                "frequency": "Hour",
                "interval": 1     
            },
            "external": true,
            "policy":
            {
                "externalData":
                {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }  
        }
    }


## <a name="onetime-pipeline"></a>次管線
您可以建立及排程來執行定期管線 (例如︰ 每小時或每日) 中您指定的管線定義中的開始和結束時間。 如需詳細資訊，請參閱[排程活動](#scheduling-and-execution)。 您也可以建立一次執行的管線。 若要這麼做，您必須設定**pipelineMode**屬性管線定義，以在**次**下列 JSON 範例所示。 此屬性的預設值是**排程**。

    {
        "name": "CopyPipeline",
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
                            "name": "InputDataset"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "OutputDataset"
                        }
                    ]
                    "name": "CopyActivity-0"
                }
            ]
            "pipelineMode": "OneTime"
        }
    }

請注意下列事項︰

- 未指定的管線的**開始**和**結束**時間。
- 輸入與輸出資料集的**顯示狀態**（**頻率**] 和 [指定**間隔**），即使資料工廠未使用的值。  
- 在圖表檢視不會顯示一次的管線。 此行為是設計。
- 無法更新一次性管線。 您可以複製一次性管道的郵件，將其重新命名、 更新屬性]，並部署來建立另一個。
