<properties 
    pageTitle="Hadoop 串流活動" 
    description="瞭解如何使用 Hadoop 串流活動 Azure 資料工廠上視需要/您自己的 HDInsight 叢集上執行 Hadoop 串流的程式。" 
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
    ms.date="09/20/2016" 
    ms.author="shlo"/>

# <a name="hadoop-streaming-activity"></a>Hadoop 串流活動
> [AZURE.SELECTOR]
[登錄區](data-factory-hive-activity.md)  
[豬](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Hadoop 串流](data-factory-hadoop-streaming-activity.md)
[電腦學習](data-factory-azure-ml-batch-execution-activity.md) 
[預存程序](data-factory-stored-proc-activity.md)
[資料湖分析 U SQL](data-factory-usql-activity.md)
[.NET 自訂](data-factory-use-custom-activities.md)

您可以使用 HDInsightStreamingActivity 活動叫用從 Azure 資料工廠管線 Hadoop 串流工作。 下列 JSON 程式碼片段會顯示使用 HDInsightStreamingActivity 管線 JSON 檔案中的語法。 

HDInsight 串流活動資料工廠[管道的郵件](data-factory-create-pipelines.md)上[您自己](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)或[](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)視/Linux windows HDInsight 叢集執行 Hadoop 串流程式。 本文是根據提供一般的概觀資料轉換及支援的轉換活動的[資料轉換活動](data-factory-data-transformation-activities.md)文件。

## <a name="json-sample"></a>JSON 範例
HDInsight 叢集自動填入範例程式 （wc.exe 和 cat.exe） 及資料 (davinci.txt)。 根據預設，名稱容器 HDInsight 叢集所用的是叢集本身的名稱。 例如，如果叢集名稱 myhdicluster，blob 容器相關聯的名稱會 myhdicluster。 

    {
        "name": "HadoopStreamingPipeline",
        "properties": {
            "description": "Hadoop Streaming Demo",
            "activities": [
                {
                    "type": "HDInsightStreaming",
                    "typeProperties": {
                        "mapper": "cat.exe",
                        "reducer": "wc.exe",
                        "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                        "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                        "filePaths": [
                            "<nameofthecluster>/example/apps/wc.exe",
                            "<nameofthecluster>/example/apps/cat.exe"
                        ],
                        "fileLinkedService": "StorageLinkedService",
                        "getDebugInfo": "Failure"
                    },
                    "outputs": [
                        {
                            "name": "StreamingOutputDataset"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 1
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "name": "RunHadoopStreamingJob",
                    "description": "Run a Hadoop streaming job",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2014-01-04T00:00:00Z",
            "end": "2014-01-05T00:00:00Z"
        }
    }

請注意下列重點︰

1. 設定**linkedServiceName**指向您要在其執行串流 mapreduce 工作的 HDInsight 叢集連結服務的名稱。
2. 設**HDInsightStreaming**活動的類型。
3. [**對應**] 屬性中，指定可執行的對應程式的名稱。 在範例中，cat.exe 是可執行的對應程式。
4. [**減壓器**] 屬性中，指定減壓器可執行檔的名稱。 在範例中，wc.exe 是可執行減壓器。
5. **輸入**[類型] 屬性中，指定輸入的檔案 （包括位置） 中的對應程式的內容。 在範例︰ "wasb://adfsample@ <account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt 」: adfsample blob 容器、 範例/資料/約翰是資料夾，而 davinci.txt 會 blob。
6. **輸出**類型] 屬性中，指定減壓器輸出檔案 （包括位置）。 Hadoop 串流工作的輸出會寫入到指定此屬性的位置。
7. 在 [ **filePaths** ] 區段中，指定對應程式和減壓器可執行檔的路徑。 在範例: 「 adfsample/example/apps/wc.exe 」，adfsample 是 blob 容器、 範例/應用程式] 資料夾，而 wc.exe 會可執行檔。
8. [ **FileLinkedService** ] 屬性中，指定 Azure 儲存體連結服務，表示 Azure 包含 filePaths 一節中指定的檔案儲存空間。
9. **引數**] 屬性中，指定串流工作的引數。
10. **GetDebugInfo**屬性是選擇性的項目。 將其設定為 [失敗時, 記錄會下載只在失敗。 將其設定為 [所有時, 記錄會永遠下載不管資料的執行狀態。

> [AZURE.NOTE] 範例所示，您可以指定輸出資料集 Hadoop 串流活動**輸出**屬性。 此資料集是只是空的資料集，才能驅動管線排程。 您不需要指定任何輸入的資料集的 [**輸入**] 屬性的活動。  

    
## <a name="example"></a>範例
在此逐步解說管線 Azure HDInsight 叢集上執行的字數統計資料流地圖/縮減程式。 

### <a name="linked-services"></a>連結的服務

#### <a name="azure-storage-linked-service"></a>Azure 連結的儲存服務
首先，您會建立連結的服務連結由 Azure 資料 factory Azure HDInsight 叢集 Azure 儲存體。 如果您複製/貼上下列的程式碼，別忘了取代客戶名稱] 和 [帳戶金鑰] 與 [名稱] 和 [索引鍵的 Azure 儲存空間。 

    {
        "name": "StorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
            }
        }
    }

#### <a name="azure-hdinsight-linked-service"></a>Azure HDInsight 連結服務
接下來，您可以建立連結至 Azure 資料 factory 連結 Azure HDInsight 叢集服務。 如果您複製/貼上下列的程式碼，HDInsight 叢集名稱取代 HDInsight 叢集的名稱，然後變更使用者名稱和密碼的值。 
    
    {
        "name": "HDInsightLinkedService",
        "properties": {
            "type": "HDInsight",
            "typeProperties": {
                "clusterUri": "https://<HDInsight cluster name>.azurehdinsight.net",
                "userName": "admin",
                "password": "**********",
                "linkedServiceName": "StorageLinkedService"
            }
        }
    }

### <a name="datasets"></a>資料集

#### <a name="output-dataset"></a>輸出資料集
在此範例中的管線不會任何輸入。 您可以指定輸出資料集的 HDInsight 串流活動。 此資料集是只是空的資料集，才能驅動管線排程。 

    {
        "name": "StreamingOutputDataset",
        "properties": {
            "published": false,
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "adftutorial/streamingdata/",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                },
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }

### <a name="pipeline"></a>管線

在此範例中的管線具有只有一個活動類型︰ **HDInsightStreaming**。 

HDInsight 叢集自動填入範例程式 （wc.exe 和 cat.exe） 及資料 (davinci.txt)。 根據預設，名稱容器 HDInsight 叢集所用的是叢集本身的名稱。 例如，如果叢集名稱 myhdicluster，blob 容器相關聯的名稱會 myhdicluster。  

    {
        "name": "HadoopStreamingPipeline",
        "properties": {
            "description": "Hadoop Streaming Demo",
            "activities": [
                {
                    "type": "HDInsightStreaming",
                    "typeProperties": {
                        "mapper": "cat.exe",
                        "reducer": "wc.exe",
                        "input": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                        "output": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                        "filePaths": [
                            "<blobcontainer>/example/apps/wc.exe",
                            "<blobcontainer>/example/apps/cat.exe"
                        ],
                        "fileLinkedService": "StorageLinkedService"
                    },
                    "outputs": [
                        {
                            "name": "StreamingOutputDataset"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 1
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "name": "RunHadoopStreamingJob",
                    "description": "Run a Hadoop streaming job",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2014-01-04T00:00:00Z",
            "end": "2014-01-05T00:00:00Z"
        }
    }

## <a name="see-also"></a>另請參閱
- [登錄區活動](data-factory-hive-activity.md)
- [豬活動](data-factory-pig-activity.md)
- [MapReduce 活動](data-factory-map-reduce.md)
- [呼叫火花程式](data-factory-spark.md)
- [呼叫 R 指令碼](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

