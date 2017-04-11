<properties 
    pageTitle="豬活動" 
    description="瞭解如何使用豬活動 Azure 資料工廠上視需要/您自己的 HDInsight 叢集上執行的豬指令碼。" 
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
    ms.date="08/31/2016" 
    ms.author="shlo"/>

# <a name="pig-activity"></a>豬活動
> [AZURE.SELECTOR]
[登錄區](data-factory-hive-activity.md)  
[豬](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Hadoop 串流](data-factory-hadoop-streaming-activity.md)
[電腦學習](data-factory-azure-ml-batch-execution-activity.md) 
[預存程序](data-factory-stored-proc-activity.md)
[資料湖分析 U SQL](data-factory-usql-activity.md)
[.NET 自訂](data-factory-use-custom-activities.md)

資料工廠[管線](data-factory-create-pipelines.md)的 HDInsight 豬活動上[您自己](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)或[](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)視/Linux windows HDInsight 叢集執行豬查詢。 本文是根據提供一般的概觀資料轉換及支援的轉換活動的[資料轉換活動](data-factory-data-transformation-activities.md)文件。

## <a name="syntax"></a>語法

    {
        "name": "HiveActivitySamplePipeline",
        "properties": {
        "activities": [
            {
                "name": "Pig Activity",
                "description": "description",
                "type": "HDInsightPig",
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
                    "script": "Pig script",
                    "scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
                    "defines": {
                        "param1": "param1Value"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                }
            }
        ]
      }
    }

## <a name="syntax-details"></a>語法的詳細資訊

屬性 | 描述 | 所需
-------- | ----------- | --------
名稱 | 活動名稱 | [是]
描述 | 描述 [活動用途的文字 | 無
類型 | HDinsightPig | [是]
輸入 | 由豬活動的一或多個輸入 | 無
輸出 | 所產生的豬活動的一或多個輸出 | [是]
linkedServiceName | 參照到 HDInsight 叢集註冊為連結資料工廠服務 | [是]
指令碼 | 指定指令碼內嵌的豬 | 無
指令碼路徑 | Azure blob 儲存體中儲存的豬指令碼，並提供檔案的路徑。 使用 [指令碼' 或 'scriptPath' 屬性。 無法同時使用兩者。 檔案名稱會區分大小寫。 | 無
定義 | 參照中的豬指令碼做為索引鍵/值組指定參數 | 無

## <a name="example"></a>範例

我們來看您要的分析識別玩家遊戲啟動您的公司所花費的時間的遊戲記錄的範例。
 
下列範例遊戲記錄會以逗號 （，） 分隔檔案。 它包含下列欄位 – ProfileID、 SessionStart、 持續時間、 SrcIPAddress 及遊戲類型。

    1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
    1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
    1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
    1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
    .....

處理此資料**的豬指令碼**︰

    PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);
    
    GroupProfile = Group PigSampleIn all;
    
    PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);
    
    Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');

若要執行這個豬指令碼資料工廠管線中，執行下列動作︰

1. 建立連結以註冊[您自己的 HDInsight 運算叢集](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)或設定[視 HDInsight 運算叢集](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)服務。 現在就讓我們呼叫此連結的服務**HDInsightLinkedService**。
2.  建立[連結的服務](data-factory-azure-blob-connector.md)設定 Azure Blob 儲存體主控資料的連線。 現在就讓我們呼叫此連結的服務**StorageLinkedService**。
3.  建立指向輸入與輸出資料的[資料集](data-factory-create-datasets.md)。 讓我們來撥打輸入資料集**PigSampleIn**與輸出資料集**PigSampleOut**。
4.  複製豬查詢中的檔案設定步驟 2 中 Azure Blob 儲存體。 如果 Azure 儲存空間裝載資料是不同的裝載查詢檔案，建立另一個連結的 Azure 儲存體服務。 請參閱在活動設定中的連結服務。 您可以使用**scriptPath**來指定豬指令碼檔和**scriptLinkedService**的路徑。 
    
    > [AZURE.NOTE] 您也可以使用 [**指令碼**] 屬性，以提供活動定義中內嵌的豬指令碼。 不過，我們不建議使用此方法均為逸出指令碼需求中的所有特殊字元，並可能會導致偵錯的問題。 最佳作法是執行步驟 4。
5. 建立管線與 HDInsightPig 活動。 此活動 HDInsight 叢集上執行的豬指令碼處理輸入的資料。

        {
          "name": "PigActivitySamplePipeline",
          "properties": {
            "activities": [
              {
                "name": "PigActivitySample",
                "type": "HDInsightPig",
                "inputs": [
                  {
                    "name": "PigSampleIn"
                  }
                ],
                "outputs": [
                  {
                    "name": "PigSampleOut"
                  }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                  "scriptPath": "adfwalkthrough\\scripts\\enrichlogs.pig",
                  "scriptLinkedService": "StorageLinkedService"
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                }
              }
            ]
          }
        } 
6. 部署管線。 如需詳細資訊，請參閱[建立管線](data-factory-create-pipelines.md)文章。 
7. 監視使用資料工廠監控和管理檢視的管線。 請參閱[監控和管理資料工廠管線](data-factory-monitor-manage-pipelines.md)如需詳細資訊的文章。

## <a name="specifying-parameters-for-a-pig-script"></a>指定參數的豬指令碼 

請考慮下列範例︰ ingested 每日到 Azure Blob 儲存體遊戲記錄而儲存在分割根據的日期和時間的資料夾。 您想要參數化的豬指令碼和動態傳遞輸入的資料夾的位置，在執行階段也產生輸出分割日期和時間。
 
若要使用參數化的豬指令碼，請執行下列動作︰

- 定義的參數中**定義**。

        {
            "name": "PigActivitySamplePipeline",
            "properties": {
            "activities": [
                {
                    "name": "PigActivitySample",
                    "type": "HDInsightPig",
                    "inputs": [
                        {
                            "name": "PigSampleIn"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "PigSampleOut"
                        }
                    ],
                    "linkedServiceName": "HDInsightLinkedService",
                    "typeproperties": {
                        "scriptPath": "adfwalkthrough\\scripts\\samplepig.hql",
                        "scriptLinkedService": "StorageLinkedService",
                        "defines": {
                            "Input": "$$Text.Format('wasb: //adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0: yyyy}/monthno={0: %M}/dayno={0: %d}/',SliceStart)",
                            "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)"
                        }
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    }
                }
            ]
          }
        }  
      
- 在豬指令碼，請參閱使用 「**$parameterName**」，如下列範例所示的參數︰

        PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);   
        GroupProfile = Group PigSampleIn all;       
        PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);      
        Store PigSampleOut into '$Output' USING PigStorage (','); 


## <a name="see-also"></a>另請參閱
- [登錄區活動](data-factory-hive-activity.md)
- [MapReduce 活動](data-factory-map-reduce.md)
- [Hadoop 串流活動](data-factory-hadoop-streaming-activity.md)
- [呼叫火花程式](data-factory-spark.md)
- [呼叫 R 指令碼](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)


