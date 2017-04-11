<properties 
    pageTitle="建立/排程管線、 鏈結中資料工廠活動 |Microsoft Azure" 
    description="瞭解如何建立資料管線 Azure 資料工廠移動及轉換資料。 建立資料導向工作流程來產生可使用的資訊。" 
    keywords="資料管線，資料導向工作流程"
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

# <a name="pipelines-and-activities-in-azure-data-factory"></a>管線和 Azure 資料工廠的活動
本文可協助您瞭解管線和 Azure 資料工廠的活動，以及用來建構移動資料和資料處理案例的端對端的資料導向工作流程。  

> [AZURE.NOTE] 本文假設您已經透過[Azure 資料工廠簡介](data-factory-introduction.md)。 如果您沒有實作-on 體驗建立資料工廠，通過[建立您的第一個資料工廠](data-factory-build-your-first-pipeline.md)教學課程中會有幫助您了解更好這份文件。  

## <a name="what-is-a-data-pipeline"></a>什麼是資料管道的郵件？
**管線**是邏輯相關**活動**的群組。 它用來將執行的工作單位群組活動。 若要進一步瞭解管線，您需要先瞭解活動。 

## <a name="what-is-an-activity"></a>什麼是活動？
活動定義在您的資料上執行的動作。 每個活動或多個[資料集](data-factory-create-datasets.md)做為輸入所需的時間，作為輸出產生一或多個資料集。 

例如，您可以使用 [複製活動協調複製資料的資料存放區，另一個資料存放區。 同樣地，您可能會使用 HDInsight 登錄區活動 Azure HDInsight 叢集轉換您的資料上執行登錄區查詢。 Azure 資料工廠提供大範圍的[資料轉換](data-factory-data-transformation-activities.md)以及[資料移動](data-factory-data-movement-activities.md)活動。 您也可以選擇建立自訂的.NET 活動執行您自己的程式碼。 

## <a name="sample-copy-pipeline"></a>範例複製管線
在下列範例管線中，有一個活動的類型**複製**[**活動**] 區段中。 在此範例中，[複製活動](data-factory-data-movement-activities.md)將資料從 Azure Blob 儲存體到 Azure SQL 資料庫。 

    {
      "name": "CopyPipeline",
      "properties": {
        "description": "Copy data from a blob to Azure SQL table",
        "activities": [
          {
            "name": "CopyFromBlobToSQL",
            "type": "Copy",
            "inputs": [
              {
                "name": "InputDataset"
              }
            ],
            "outputs": [
              {
                "name": "OutputDataset"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "SqlSink",
                "writeBatchSize": 10000,
                "writeBatchTimeout": "60:00:00"
              }
            },
            "Policy": {
              "concurrency": 1,
              "executionPriorityOrder": "NewestFirst",
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
        ],
        "start": "2016-07-12T00:00:00Z",
        "end": "2016-07-13T00:00:00Z"
      }
    } 

請注意下列重點︰

- 在 [活動] 區段中，有其**類型**] 設定為**複製**只有一個活動。
- 輸入活動設定為 [ **InputDataset**和活動的輸出效果設定為 [ **OutputDataset**。
- 在 [ **typeProperties** ] 區段中， **BlobSource**指定為來源類型，然後**SqlSink**指定為接收類型]。

建立這個管線的完整的逐步解說，請參閱[教學課程︰ 資料複製到 SQL 資料庫 Blob 儲存體](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。 

## <a name="sample-transformation-pipeline"></a>範例轉換管線
在下列範例管線中，有一個活動的類型**HDInsightHive** [**活動**] 區段中。 在此範例中， [HDInsight 登錄區活動](data-factory-hive-activity.md)從轉換資料 Azure Blob 儲存體執行 Azure HDInsight Hadoop 叢集登錄區指令碼檔案。 

    {
        "name": "TransformPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [
                {
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                        "scriptLinkedService": "AzureStorageLinkedService",
                        "defines": {
                            "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                            "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                        }
                    },
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
                    "policy": {
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Month",
                        "interval": 1
                    },
                    "name": "RunSampleHiveActivity",
                    "linkedServiceName": "HDInsightOnDemandLinkedService"
                }
            ],
            "start": "2016-04-01T00:00:00Z",
            "end": "2016-04-02T00:00:00Z",
            "isPaused": false
        }
    }

請注意下列重點︰ 

- 在 [活動] 區段中，有其**類型**] 設定為**HDInsightHive**只有一個活動。
- 登錄區指令檔、 **partitionweblogs.hql**，儲存在 （指定 scriptLinkedService，稱為**AzureStorageLinkedService**） Azure 儲存體帳戶和**指令碼**] 資料夾中容器**adfgetstarted**。
- [**定義**] 區段中用來指定執行階段設定為登錄區設定值傳遞給登錄區指令碼 (如 ${hiveconf: inputtable}，${hiveconf:partitionedtable})。

建立這個管線的完整的逐步解說，請參閱[教學課程︰ 建立您的第一個管線程序資料使用 Hadoop 叢集](data-factory-build-your-first-pipeline.md)。 

## <a name="chaining-activities"></a>串連的活動
如果您有多個活動管線中輸出的活動不輸入的另一個活動，活動可能會同時執行如果輸入的資料的活動的扇形區準備。 

您可以由輸出資料集的一個活動輸入資料集的其他活動鏈結兩個活動。 活動可以在相同的管線，或在不同的管線中。 第二個活動執行僅限時的第一個順利完成。 

例如，請考慮下列情況︰
 
1.  管線 P1 有活動 A1，需要外部輸入資料集 D1，並產生**輸出**資料集**D2**。
2.  管線 P2 有活動 A2，需要**輸入**的資料集**D2**，並產生輸出資料集 D3。
 
在這個案例中，活動 A1 時執行的外部資料可供使用，並排程的可用性頻率為止。  活動時排程的扇形區，從 D2，就可以使用排程的可用性頻率到達，會執行 A2。 在資料集中 D2 扇形區的其中一種錯誤時，A2 才會開始執行該扇形區的可供使用。

圖表檢視︰

![串連兩個管線活動](./media/data-factory-create-pipelines/chaining-two-pipelines.png)

在相同的管線兩個活動圖表檢視︰ 

![在相同的管線鏈結活動](./media/data-factory-create-pipelines/chaining-one-pipeline.png)

如需詳細資訊，請參閱[排程和執行](#chaining-activities)。 

## <a name="scheduling-and-execution"></a>排程和執行
到目前為止，您有了解管線和活動是什麼。 您也有查看有何他們已定義且的高層級檢視中 Azure 資料工廠的活動。 現在讓我們看看取得執行的方式。 

管線是作用中只會之間的開始時間] 和 [結束時間。 它不會執行的開始時間之前或之後的結束時間。 如果已暫停管線，它不會取得執行不管開始和結束時間資料。 若要執行管線，它應該暫停。 事實上，就不會執行的管線。 這是管道，取得執行的活動。 不過會進行整體的管線內容中。 

請參閱[排程和執行](data-factory-scheduling-and-execution.md)瞭解排程和執行 Azure 資料工廠運作的方式。

## <a name="create-pipelines"></a>建立管線
Azure 資料工廠提供作者和部署管線 （其中包含一或多個活動） 的各種機制。 

### <a name="using-azure-portal"></a>使用 Azure 入口網站
您可以使用 Azure 入口網站中的資料工廠編輯器建立管道的郵件。 如需端對端逐步解說，請參閱[快速入門 Azure 資料工廠 （資料工廠編輯器）](data-factory-build-your-first-pipeline-using-editor.md) 。 

### <a name="using-visual-studio"></a>使用 Visual Studio 
您可以使用 Visual Studio 作者和 Azure 資料工廠部署管線。 如需端對端逐步解說，請參閱[Azure 資料工廠 (Visual Studio) 快速入門](data-factory-build-your-first-pipeline-using-vs.md)。 

### <a name="using-azure-powershell"></a>使用 PowerShell 的 Azure
您可以使用 PowerShell 的 Azure 中 Azure 資料工廠建立管線。 假設為 [已定義的管線 JSON c:\DPWikisample.json 在檔案中。 您可以上傳至您的 Azure 資料工廠執行個體在下列範例所示︰

    New-AzureRmDataFactoryPipeline -ResourceGroupName ADF -Name DPWikisample -DataFactoryName wikiADF -File c:\DPWikisample.json

建立資料工廠管線與的端對端逐步解說，請參閱[開始使用 Azure 資料工廠 (PowerShell 的 Azure)](data-factory-build-your-first-pipeline-using-powershell.md) 。 

### <a name="using-net-sdk"></a>使用.NET SDK
您可以建立並部署透過.NET SDK 的管線太。 若要以程式設計方式建立管線可這個機制。 如需詳細資訊，請參閱[建立、 管理及監視資料工廠以程式設計方式](data-factory-create-data-factories-programmatically.md)。 


### <a name="using-azure-resource-manager-template"></a>使用 Azure 資源管理員範本
您可以建立並部署管線使用 Azure 資源管理員範本。 如需詳細資訊，請參閱[開始使用 Azure 資料工廠 （Azure 資源管理員）](data-factory-build-your-first-pipeline-using-arm.md)。 

### <a name="using-rest-api"></a>使用 REST API
您可以建立並部署也使用 REST Api 的管線。 若要以程式設計方式建立管線可這個機制。 如需詳細資訊，請參閱[建立或更新管道的郵件](https://msdn.microsoft.com/library/azure/dn906741.aspx)。 


## <a name="monitor-and-manage-pipelines"></a>監控和管理管線  
後部署管線之後，您可以管理，並監控您的管線、 扇形區，並執行。 進一步瞭解其以下︰[監控和管理的管線](data-factory-monitor-manage-pipelines.md)。


## <a name="pipeline-json"></a>管線 JSON   
讓我們看深入瞭解管線定義 JSON 格式的方式。 管線一般的結構如下所示︰

    {
        "name": "PipelineName",
        "properties": 
        {
            "description" : "pipeline description",
            "activities":
            [
    
            ],
            "start": "<start date-time>",
            "end": "<end date-time>"
        }
    }

[**活動**] 區段中，可以有一或多個定義的活動。 每個活動具有最上層結構如下︰

    {
        "name": "ActivityName",
        "description": "description", 
        "type": "<ActivityType>",
        "inputs":  "[]",
        "outputs":  "[]",
        "linkedServiceName": "MyLinkedService",
        "typeProperties":
        {
    
        },
        "policy":
        {
        }
        "scheduler":
        {
        }
    }

下列表格說明中的活動和管線 JSON 定義的屬性︰

標記 | 描述 | 所需
--- | ----------- | --------
名稱 | 活動或管線的名稱。 指定之動作的名稱的活動或管線設定為執行<br/><ul><li>最大字元數︰ 260</li><li>必須以字母數字或底線 (_) 開頭</li><li>下列字元不允許: 」。 」，[+]，「 ? 」，「 / 」，"<"，"> 」，「 * 」，「 百分比 」、 「 & 」 」: 「，」\\」</li></ul> | [是]
描述 | 描述 [活動或管線用途的文字 | [是]
類型 | 指定活動的類型。 請參閱[資料移動活動](data-factory-data-movement-activities.md)」 和 「[資料轉換活動](data-factory-data-transformation-activities.md)文件的不同類型的活動。 | [是]
輸入 | 輸入的活動所使用的資料表<br/><br/>一個輸入的表格<br/>[輸入]: [{"名稱": 「 inputtable1 」}]，<br/><br/>輸入的兩個資料表 <br/>[輸入]: [{"名稱": 「 inputtable1 」}，{"名稱": 「 inputtable2 」}]， | [是]
輸出 | 輸出 activity.// 一個輸出資料表所使用的資料表<br/>「 輸出]: [{"名稱": 「 outputtable1 」}]，<br/><br/>兩個輸出資料表<br/>「 輸出]: [{"名稱": 「 outputtable1 」}，{"名稱": 「 outputtable2 」}]， | [是]
linkedServiceName | 活動所用的連結服務的名稱。 <br/><br/>活動可能會要求您指定的連結的服務，連結到所需的計算環境。 | 是 HDInsight 活動和 Azure 電腦學習計分活動批次 <br/><br/>無其他所有人
typeProperties | [TypeProperties] 區段中的內容活動的類型而定。 | 無
原則 | 影響活動的執行時間行為的原則。 如果未指定，則會使用預設的原則。 | 無
開始 | 開始日期-時間管道的郵件。 必須在[ISO 格式](http://en.wikipedia.org/wiki/ISO_8601)。 例如︰ 2014年-10-14T16:32:41Z。 <br/><br/>若要指定當地時間，例如預估時間可能是。 以下是範例: 「 2016年-02-27T06:00:00**-05:00**」，這是 6 AM 估計<br/><br/>開始和結束屬性一起指定管線作用中的一的期。 以僅產生輸出扇形區此作用中的期間。 | 無<br/><br/>如果您指定 [結束] 屬性的值時，您必須指定 [開始] 屬性的值。<br/><br/>開始和結束時間可以兩者都是空的建立管線。 您必須指定兩個值設定為執行管線的作用中的期間。 如果您沒有指定開始和結束時間時建立管線，您可以設定這些稍後使用設定 AzureRmDataFactoryPipelineActivePeriod 指令程式。
結束 | 結束日期-時間的管線。 如果指定必須 ISO 格式。 例如︰ 2014年-10-14T17:32:41Z <br/><br/>若要指定當地時間，例如預估時間可能是。 以下是範例: 「 2016年-02-27T06:00:00**-05:00**」，這是 6 AM 估計<br/><br/>若要執行管線，指定 9999-09 09 做為 [結束] 屬性的值。 | 無 <br/><br/>如果您指定 [開始] 屬性的值時，您必須指定結束屬性的值。<br/><br/>請參閱 [**開始**] 屬性的備忘稿。
isPaused | 如果設為 true 的管線不會執行。 預設值 = false。 您可以使用此屬性，以啟用或停用。 | 無 
排程器 | 「 排程器 」 屬性用來定義所要排程活動。 其子屬性會[在資料集中的可用性屬性](data-factory-create-datasets.md#Availability)中的項目相同。 | 無 |   
| pipelineMode | 管線執行排程的方法。 允許的值︰ 排程 （預設）、 次。<br/><br/>[排程]，表示管線執行作用期 （開始和結束時間） 根據指定的時間間隔。 「 次' 表示管線執行一次。 一次建立次管線無法修改/更新目前。 如需詳細資訊次設定，請參閱[Onetime 管道的郵件](data-factory-scheduling-and-execution.md#onetime-pipeline)。 | 無 | 
| expirationTime | 建立的管線有效，應維持能夠後的時間長度。 如果沒有任何作用中、 失敗，就會執行，等待管線刪除或自動到達到期時間。 | 無 | 
| 資料集 | 管線中定義的活動所使用的資料集的清單。 此屬性可以用於定義的特定此管線而不資料工廠中定義資料集。 在此管線中定義資料集只能用於此管線，而且無法共用。 如需詳細資訊，請參閱[Scoped 資料集](data-factory-create-datasets.md#scoped-datasets)。| 無 |  
 

### <a name="policies"></a>原則
原則影響執行階段的行為活動，特別是在表格的扇形區處理時，才。 下表提供詳細資料。

屬性 | 允許的值 | 預設值 | 描述
-------- | ----------- | -------------- | ---------------
並行 | 整數 <br/><br/>最大值︰ 10 | 1 | 同時執行的活動數目。<br/><br/>它會決定次數平行活動的不同的扇形區上就會發生。 例如，如果活動需要瀏覽可用的資料，有較大的並行值的大量可加快資料處理。 
executionPriorityOrder | NewestFirst<br/><br/>OldestFirst | OldestFirst | 決定資料扇形區處理的順序。<br/><br/>比方說，如果您有 2 切割 （一種情況下午 4，並在 5 pm 的另一個），和都執行擱置。 如果您設定為 NewestFirst executionPriorityOrder，第一次處理下午 5 的扇形區。 同樣的如果您設定為 OldestFIrst executionPriorityORder，然後在 4 PM 的扇形區處理。 
再試一次 | 整數<br/><br/>可 10 最大值。 | 3 | 重試之前的扇形區資料處理標示為 [失敗次數。 活動執行的資料扇形區重試一次最多指定重試計數。 越快越失敗後完成重試]。
逾時 | 時段 | 00:00:00 | 活動逾時。 範例︰ 00:10:00 （表示逾時 10 分鐘）<br/><br/>如果未指定值，或為 0，會出現逾時就會是無限制。<br/><br/>如果扇形區處理資料的時間超過逾時值，取消，，系統會嘗試再試一次 [處理程序。 次數取決於重試] 屬性。 發生逾時，則會將狀態設定為 [逾時。
延遲 | 時段 | 00:00:00 | 指定資料處理的扇形區開始前的延遲。<br/><br/>過去的預期的執行時間延遲後開始執行的活動資料扇形區。<br/><br/>範例︰ 00:10:00 （表示 10 分鐘的延遲）
longRetry | 整數<br/><br/>最大值︰ 10 | 1 | 長重試次數之前的扇形區執行會失敗。<br/><br/>longRetry 嘗試是由 longRetryInterval 為單行間距。 因此，如果您要指定重試之間的時間，請使用 longRetry。 如果未指定重試] 和 [longRetry，每個 longRetry 嘗試包含重和最大次數是重試 * longRetry。<br/><br/>例如，如果我們的活動原則中有下列設定︰<br/>再試一次︰ 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>假設只有一個扇形區來執行 （等待狀態） 和活動執行失敗每次。 一開始會有 3 個連續執行嘗試。 在每次嘗試時之後, 的扇形區狀態就會重試]。 第一次 3 嘗試透過之後，扇形區狀態就會 LongRetry。<br/><br/>小時後 （也就是 longRetryInteval 的值），就會的 3 個連續執行嘗試的其他設定。 之後，就無法扇形區狀態，並想要嘗試重試。 因此整體 6 嘗試所做。<br/><br/>如果執行順利完成，扇形區狀態就會準備，並嘗試重試。<br/><br/>在位置相依資料到達不確定時間或整體環境分流下的資料處理程序的情況下，可能會用於 longRetry。 在這種情況下，重試一個接一個可能沒有幫助的這樣做，而且這樣之後的間隔時間中所要的輸出結果。<br/><br/>注意︰ 未設定為 longRetry 或 longRetryInterval 最高值。 一般而言，較高的值會暗示其他系統的問題。 
longRetryInterval | 時段 | 00:00:00 | 長重試之間延遲 


## <a name="next-steps"></a>後續步驟

- 瞭解[排程和 Azure 資料工廠執行](data-factory-scheduling-and-execution.md)。  
- 瞭解[移動資料](data-factory-data-movement-activities.md)和 Azure 資料工廠中的[資料轉換功能](data-factory-data-transformation-activities.md)
- 瞭解[管理及監視 Azure 資料工廠](data-factory-monitor-manage-pipelines.md)。
- [建立並部署您第管道的郵件](data-factory-build-your-first-pipeline.md)。 
