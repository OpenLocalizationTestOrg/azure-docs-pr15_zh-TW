<properties 
    pageTitle="呼叫 [從 Azure 資料工廠 MapReduce 程式" 
    description="瞭解如何從 Azure 資料工廠，Azure HDInsight 叢集上執行 MapReduce 程式來處理資料。" 
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
    ms.date="09/12/2016" 
    ms.author="shlo"/>

# <a name="invoke-mapreduce-programs-from-data-factory"></a>呼叫 [從資料工廠 MapReduce 程式
> [AZURE.SELECTOR]
[登錄區](data-factory-hive-activity.md)  
[豬](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Hadoop 串流](data-factory-hadoop-streaming-activity.md)
[電腦學習](data-factory-azure-ml-batch-execution-activity.md) 
[預存程序](data-factory-stored-proc-activity.md)
[資料湖分析 U SQL](data-factory-usql-activity.md)
[.NET 自訂](data-factory-use-custom-activities.md)

資料工廠[管線](data-factory-create-pipelines.md)的 HDInsight MapReduce 活動上[您自己](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)或[](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)視/Linux windows HDInsight 叢集執行 MapReduce 程式。 本文是根據提供一般的概觀資料轉換及支援的轉換活動的[資料轉換活動](data-factory-data-transformation-activities.md)文件。

## <a name="introduction"></a>簡介 
Azure 資料工廠管線使用連結的計算 services 以處理連結的儲存空間服務中的資料。 包含一系列的活動的每一個活動執行特定處理作業的位置。 本文說明如何使用 HDInsight MapReduce 活動。
 
如需使用 HDInsight 豬及登錄區活動/Linux windows HDInsight 叢集上執行的豬/登錄區指令碼從管線詳細資訊，請參閱[豬](data-factory-pig-activity.md)和[登錄區](data-factory-hive-activity.md)。 

## <a name="json-for-hdinsight-mapreduce-activity"></a>JSON HDInsight MapReduce 活動 

在 [HDInsight 活動的 JSON 定義︰ 
 
1. 您可以將**活動****類型**為**HDInsight**。
3. 指定的**類別名稱**屬性類別的名稱。
4. 指定包括**jarFilePath**屬性的檔案名稱 JAR 檔案的路徑。
5. 指定包含 JAR 檔案**jarLinkedService**屬性 Azure Blob 儲存體參照的連結的服務。   
6. **引數**] 區段中，指定 MapReduce 程式有任何引數。 在執行階段，您會看到一些額外的引數 (例如︰ mapreduce.job.tags) 從 MapReduce 架構。 若要區別您 MapReduce 引數的引數，請考慮選項和值使用做為引數，如下列範例所示 (-s-輸入、-輸出等，還有緊接著其值)。

        {
            "name": "MahoutMapReduceSamplePipeline",
            "properties": {
                "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
                "activities": [
                    {
                        "type": "HDInsightMapReduce",
                        "typeProperties": {
                            "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                            "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                            "jarLinkedService": "StorageLinkedService",
                            "arguments": [
                                "-s",
                                "SIMILARITY_LOGLIKELIHOOD",
                                "--input",
                                "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input",
                                "--output",
                                "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/",
                                "--maxSimilaritiesPerItem",
                                "500",
                                "--tempDir",
                                "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"
                            ]
                        },
                        "inputs": [
                            {
                                "name": "MahoutInput"
                            }
                        ],
                        "outputs": [
                            {
                                "name": "MahoutOutput"
                            }
                        ],
                        "policy": {
                            "timeout": "01:00:00",
                            "concurrency": 1,
                            "retry": 3
                        },
                        "scheduler": {
                            "frequency": "Hour",
                            "interval": 1
                        },
                        "name": "MahoutActivity",
                        "description": "Custom Map Reduce to generate Mahout result",
                        "linkedServiceName": "HDInsightLinkedService"
                    }
                ],
                "start": "2014-01-03T00:00:00Z",
                "end": "2014-01-04T00:00:00Z",
                "isPaused": false,
                "hubName": "mrfactory_hub",
                "pipelineMode": "Scheduled"
            }
        }
    
    

若要執行 HDInsight 叢集上的任何 MapReduce jar 檔案，您可以使用 HDInsight MapReduce 活動。 下列範例 JSON 定義中的管線，HDInsight 活動會設定為執行 Mahout JAR 檔案。

## <a name="sample-on-github"></a>在 GitHub 的範例
您可以下載使用從 HDInsight MapReduce 活動的範例︰[資料工廠範例 GitHub 上](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON/MapReduce_Activity_Sample)。  

## <a name="running-the-word-count-program"></a>執行 [字數統計程式
在此範例中的管線 Azure HDInsight 叢集上執行的字數統計地圖/縮減程式。   

### <a name="linked-services"></a>連結的服務
首先，您會建立連結的服務連結由 Azure 資料 factory Azure HDInsight 叢集 Azure 儲存體。 如果您複製/貼上下列的程式碼，別忘了取代**客戶名稱**] 和 [**帳戶金鑰]**與 [名稱] 和 [索引鍵的 Azure 儲存空間。 

#### <a name="azure-storage-linked-service"></a>Azure 連結的儲存服務

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
接下來，您可以建立連結至 Azure 資料 factory 連結 Azure HDInsight 叢集服務。 如果您複製/貼上下列的程式碼， **HDInsight 叢集名稱**取代 HDInsight 叢集的名稱，然後變更使用者名稱和密碼的值。   

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
在此範例中的管線不會任何輸入。 您可以指定輸出資料集的 HDInsight MapReduce 活動。 此資料集是只是空的資料集，才能驅動管線排程。  

    {
        "name": "MROutput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "fileName": "WordCountOutput1.txt",
                "folderPath": "example/data/",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }

### <a name="pipeline"></a>管線
在此範例中的管線具有只有一個活動類型︰ HDInsightMapReduce。 某些中 JSON 重要的內容是︰ 

屬性 | 備忘稿
:-------- | :-----
類型 | 必須設定**HDInsightMapReduce**類型。 
類別名稱 | 類別的名稱︰ **wordcount**
jarFilePath | 包含類別 jar 檔案的路徑。 如果您複製/貼上下列的程式碼，別忘了變更叢集的名稱。 
jarLinkedService | Azure 儲存連結包含 jar 檔案的服務。 此連結的服務是指與 HDInsight 叢集相關聯的儲存空間。 
引數 | Wordcount 程式需要兩個引數、 輸入與輸出。 輸入的檔案是 davinci.txt 檔案。
頻率/間隔 | 這些屬性的值相符的輸出資料集。 
linkedServiceName | 指向您先前建立的連結的 HDInsight 服務。   

    {
        "name": "MRSamplePipeline",
        "properties": {
            "description": "Sample Pipeline to Run the Word Count Program",
            "activities": [
                {
                    "type": "HDInsightMapReduce",
                    "typeProperties": {
                        "className": "wordcount",
                        "jarFilePath": "<HDInsight cluster name>/example/jars/hadoop-examples.jar",
                        "jarLinkedService": "StorageLinkedService",
                        "arguments": [
                            "/example/data/gutenberg/davinci.txt",
                            "/example/data/WordCountOutput1"
                        ]
                    },
                    "outputs": [
                        {
                            "name": "MROutput"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "name": "MRActivity",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2014-01-03T00:00:00Z",
            "end": "2014-01-04T00:00:00Z"
        }
    }

## <a name="run-spark-programs"></a>執行火花程式
您可以使用 MapReduce 活動 HDInsight 火花叢集上執行火花程式。 如需詳細資訊，請參閱[從 Azure 資料工廠叫用火花程式](data-factory-spark.md)。  

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[adfgetstartedmonitoring]:data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#monitor-pipelines 

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com
 
## <a name="see-also"></a>另請參閱
- [登錄區活動](data-factory-hive-activity.md)
- [豬活動](data-factory-pig-activity.md)
- [Hadoop 串流活動](data-factory-hadoop-streaming-activity.md)
- [呼叫火花程式](data-factory-spark.md)
- [呼叫 R 指令碼](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)
