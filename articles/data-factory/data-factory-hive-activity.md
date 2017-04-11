<properties 
    pageTitle="登錄區活動" 
    description="瞭解如何使用登錄區活動 Azure 資料工廠上視需要/您自己的 HDInsight 叢集上執行登錄區查詢。" 
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
    ms.date="10/11/2016" 
    ms.author="shlo"/>

# <a name="hive-activity"></a>登錄區活動
> [AZURE.SELECTOR]
[登錄區](data-factory-hive-activity.md)  
[豬](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Hadoop 串流](data-factory-hadoop-streaming-activity.md)
[電腦學習](data-factory-azure-ml-batch-execution-activity.md) 
[預存程序](data-factory-stored-proc-activity.md)
[資料湖分析 U SQL](data-factory-usql-activity.md)
[.NET 自訂](data-factory-use-custom-activities.md)

HDInsight 登錄區中的活動資料工廠[管道的郵件](data-factory-create-pipelines.md)上[您自己](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)或[](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)視/Linux windows HDInsight 叢集執行登錄區查詢。 本文是根據提供一般的概觀資料轉換及支援的轉換活動的[資料轉換活動](data-factory-data-transformation-activities.md)文件。

## <a name="syntax"></a>語法

    {
        "name": "Hive Activity",
        "description": "description",
        "type": "HDInsightHive",
        "inputs": [
          {
            "name": "input tables"
          }
        ],
        "outputs": [
          {
            "name": "output tables"
          }
        ],
        "linkedServiceName": "MyHDInsightLinkedService",
        "typeProperties": {
          "script": "Hive script",
          "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
          "defines": {
            "param1": "param1Value"
          }
        },
       "scheduler": {
          "frequency": "Day",
          "interval": 1
        }
    }
    
## <a name="syntax-details"></a>語法的詳細資訊

屬性 | 描述 | 所需
-------- | ----------- | --------
名稱 | 活動名稱 | [是]
描述 | 描述 [活動用途的文字 | 無
類型 | HDinsightHive | [是]
輸入 | 輸入由登錄區活動 | 無
輸出 | 輸出所產生的登錄區活動 | [是] 
linkedServiceName | 參照到 HDInsight 叢集註冊為連結資料工廠服務 | [是] 
指令碼 | 指定登錄區指令碼內嵌 | 無
指令碼路徑 | Azure blob 儲存體中儲存的登錄區指令碼，並提供檔案的路徑。 使用 [指令碼' 或 'scriptPath' 屬性。 無法同時使用兩者。 檔案名稱會區分大小寫。 | 無 
定義 | 參照中使用 'hiveconf 」 的登錄區指令碼做為索引鍵/值組指定參數  | 無

## <a name="example"></a>範例

我們來看您要的分析找出遊戲啟動您的公司的使用者所花費的時間的遊戲記錄的範例。 

下列記錄是範例遊戲記錄，逗號 (`,`) 分隔並包含下列欄位 – ProfileID、 SessionStart、 持續時間、 SrcIPAddress 及遊戲類型。

    1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
    1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
    1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
    1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
    .....

**登錄區指令碼**處理此資料︰

    DROP TABLE IF EXISTS HiveSampleIn; 
    CREATE EXTERNAL TABLE HiveSampleIn 
    (
        ProfileID       string, 
        SessionStart    string, 
        Duration        int, 
        SrcIPAddress    string, 
        GameType        string
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/samplein/'; 
    
    DROP TABLE IF EXISTS HiveSampleOut; 
    CREATE EXTERNAL TABLE HiveSampleOut 
    (   
        ProfileID   string, 
        Duration    int
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/sampleout/';
    
    INSERT OVERWRITE TABLE HiveSampleOut
    Select 
        ProfileID,
        SUM(Duration)
    FROM HiveSampleIn Group by ProfileID

若要執行此登錄區指令碼資料工廠管線中，您必須執行下列動作

1. 建立連結以註冊[您自己的 HDInsight 運算叢集](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)或設定[視 HDInsight 運算叢集](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)服務。 現在就讓我們呼叫此連結的服務 「 HDInsightLinkedService 」。
2. 建立[連結的服務](data-factory-azure-blob-connector.md)設定 Azure Blob 儲存體主控資料的連線。 現在就讓我們呼叫此連結的服務 「 StorageLinkedService 」
3. 建立指向輸入與輸出資料的[資料集](data-factory-create-datasets.md)。 讓我們來撥打輸入的資料集 」 HiveSampleIn 」 和 「 HiveSampleOut 」 輸出資料集
4. 步驟 2 中，設定複製區查詢另存為檔案至 Azure Blob 儲存體。 如果不同的主機服務這個查詢檔案主控資料的儲存空間，建立另一個連結的 Azure 儲存體服務，以便活動中進行參照。 您可以使用**scriptPath**來指定登錄區查詢檔案和**scriptLinkedService**指定包含指令碼檔 Azure 儲存空間的路徑。 

    > [AZURE.NOTE] 您也可以使用 [**指令碼**] 屬性，以提供活動定義中內嵌的登錄區指令碼。 我們不建議使用此方法均為逸出 JSON 文件需求內的指令碼中的所有特殊字元，並可能會導致偵錯的問題。 最佳作法是執行步驟 4。
5.  建立管線與 HDInsightHive 活動。 活動程序/轉換資料。

        {
          "name": "HiveActivitySamplePipeline",
          "properties": {
            "activities": [
              {
                "name": "HiveActivitySample",
                "type": "HDInsightHive",
                "inputs": [
                  {
                    "name": "HiveSampleIn"
                  }
                ],
                "outputs": [
                  {
                    "name": "HiveSampleOut"
                  }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                  "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                  "scriptLinkedService": "StorageLinkedService"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
              }
            ]
          }
        }

6.  部署管線。 如需詳細資訊，請參閱[建立管線](data-factory-create-pipelines.md)文章。 
7.  監視使用資料工廠監控和管理檢視的管線。 請參閱[監控和管理資料工廠管線](data-factory-monitor-manage-pipelines.md)如需詳細資訊的文章。 


## <a name="specifying-parameters-for-a-hive-script"></a>指定參數的登錄區指令碼  
在此範例中，遊戲記錄會將 Azure Blob 儲存體每天 ingested，並且儲存在分割日期和時間的資料夾。 您想要參數化的登錄區指令碼和動態傳遞輸入的資料夾的位置，在執行階段也產生輸出分割日期和時間。

若要使用參數化登錄區指令碼，請執行下列動作

- 定義的參數中**定義**。

        {
            "name": "HiveActivitySamplePipeline",
            "properties": {
            "activities": [
                {
                    "name": "HiveActivitySample",
                    "type": "HDInsightHive",
                    "inputs": [
                        {
                            "name": "HiveSampleIn"
                          }
                    ],
                    "outputs": [
                        {
                            "name": "HiveSampleOut"
                        }
                    ],
                    "linkedServiceName": "HDInsightLinkedService",
                    "typeproperties": {
                        "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                        "scriptLinkedService": "StorageLinkedService",
                        "defines": {
                            "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)",
                            "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)"
                        },
                        "scheduler": {
                            "frequency": "Hour",
                            "interval": 1
                        }
                    }
                }
            ]
          }
        }

- 在登錄區指令碼，請參閱使用**${hiveconf:parameterName}**參數。 

        DROP TABLE IF EXISTS HiveSampleIn; 
        CREATE EXTERNAL TABLE HiveSampleIn 
        (
            ProfileID   string, 
            SessionStart    string, 
            Duration    int, 
            SrcIPAddress    string, 
            GameType    string
        ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Input}'; 
        
        DROP TABLE IF EXISTS HiveSampleOut; 
        CREATE EXTERNAL TABLE HiveSampleOut 
        (
            ProfileID   string, 
            Duration    int
        ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Output}';
        
        INSERT OVERWRITE TABLE HiveSampleOut
        Select 
            ProfileID,
            SUM(Duration)
        FROM HiveSampleIn Group by ProfileID


## <a name="see-also"></a>另請參閱
- [豬活動](data-factory-pig-activity.md)
- [MapReduce 活動](data-factory-map-reduce.md)
- [Hadoop 串流活動](data-factory-hadoop-streaming-activity.md)
- [呼叫火花程式](data-factory-spark.md)
- [呼叫 R 指令碼](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)









