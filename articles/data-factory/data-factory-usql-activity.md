<properties 
    pageTitle="從 Azure 資料工廠 Azure 資料湖分析執行 U SQL 指令碼" 
    description="瞭解如何透過 Azure 資料湖分析計算服務執行 U SQL 指令碼處理資料。" 
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
    ms.date="09/12/2016" 
    ms.author="spelluru"/>

# <a name="run-u-sql-script-on-azure-data-lake-analytics-from-azure-data-factory"></a>從 Azure 資料工廠 Azure 資料湖分析執行 U SQL 指令碼
> [AZURE.SELECTOR]
[登錄區](data-factory-hive-activity.md)  
[豬](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Hadoop 串流](data-factory-hadoop-streaming-activity.md)
[電腦學習](data-factory-azure-ml-batch-execution-activity.md) 
[預存程序](data-factory-stored-proc-activity.md)
[資料湖分析 U SQL](data-factory-usql-activity.md)
[.NET 自訂](data-factory-use-custom-activities.md)
 
Azure 資料工廠管線使用連結的計算 services 以處理連結的儲存空間服務中的資料。 包含一系列的活動的每一個活動執行特定處理作業的位置。 本文將說明在**Azure 資料湖分析**計算連結服務會執行**U SQL**指令碼**資料湖分析 U SQL 活動**。 

> [AZURE.NOTE] 
> 建立管線與資料湖分析 U SQL 活動之前，先建立 Azure 資料湖分析帳戶。 若要瞭解 Azure 資料湖分析資料，請參閱[開始使用 Azure 資料湖分析](../data-lake-analytics/data-lake-analytics-get-started-portal.md)。
>  
> 檢閱[建立您的第一個管線教學課程](data-factory-build-your-first-pipeline.md)以瞭解詳細步驟，建立資料工廠、 連結的服務、 資料集，以及管線。 使用 JSON 程式碼片段資料工廠編輯器或 Visual Studio PowerShell 的 Azure 來建立資料工廠實體。

## <a name="azure-data-lake-analytics-linked-service"></a>Azure 資料湖分析連結服務
您建立連結 Azure 資料工廠 Azure 資料湖分析計算服務**Azure 資料湖分析**連結服務。 資料湖分析 U SQL 活動管道指的是此連結的服務。 

下列範例會提供 JSON 定義 Azure 資料湖分析連結服務。 

    {
        "name": "AzureDataLakeAnalyticsLinkedService",
        "properties": {
            "type": "AzureDataLakeAnalytics",
            "typeProperties": {
                "accountName": "adftestaccount",
                "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
                "authorization": "<authcode>",
                "sessionId": "<session ID>", 
                "subscriptionId": "<subscription id>",
                "resourceGroupName": "<resource group name>"
            }
        }
    }


下表提供說明 JSON 定義中使用的內容。 

屬性 | 描述 | 所需
-------- | ----------- | --------
類型 | [類型] 屬性必須設定為︰ **AzureDataLakeAnalytics**。 | [是]
accountName | Azure 資料湖分析帳戶名稱。 | [是]
dataLakeAnalyticsUri | Azure 資料湖分析 URI。 |  無 
授權 | 按一下 [**授權**] 按鈕在 [資料工廠編輯器] 中，並完成 OAuth 登入之後，就會自動擷取授權程式碼。  | [是] 
subscriptionId | Azure 訂閱識別碼 | 不 （如果未指定的資料 factory 訂閱使用）。 
resourceGroupName | Azure 資源群組名稱 |  不 （如果未指定的資料 factory 資源群組使用）。
工作階段識別碼 | 從 OAuth 授權工作階段的工作階段識別碼。 每個工作階段 id 是唯一的而且只能使用一次。 識別碼會自動產生的資料工廠編輯器中工作階段。 | [是]

您使用 [**授權**] 按鈕來產生授權程式碼到期後某個。 請參閱下表的不同類型的使用者帳戶的到期時間。 您可能會看到下列錯誤訊息時驗證**權杖到期**︰ 認證操作錯誤︰ invalid_grant-AADSTS70002︰ 驗證認證的錯誤。 AADSTS70008︰ 提供存取權授與已到期或已撤銷。 追蹤識別碼︰ d18629e8-af88-43c5-88e3-d8419eb1fca1 相互關聯識別碼︰ fac30a0c-6be6-4e02-8d69-a776d2ffefd7 時間戳記︰ 2015年-12-15 21:09:31Z

 
| 使用者類型 | 在後到期 |
| :-------- | :----------- | 
| 不受 Azure Active Directory 的使用者帳戶(@hotmail.com,@live.com,等等。) | 12 小時 |
| 受管理的 Azure Active Directory (AAD) 的使用者帳戶 | 執行 14 天後的最後一個扇形區。 <br/><br/>90 天，如果根據 OAuth 型連結服務扇形區執行至少一次每 14 天。 |

若要避免/解決此錯誤，重新授權使用**授權**] 按鈕的時機**權杖到期**，而且重新部署的機會連結的服務。 您也可以產生以程式設計方式使用下一節中的 [程式碼的**工作階段識別碼**和**授權**屬性的值。 

  
### <a name="to-programmatically-generate-sessionid-and-authorization-values"></a>若要以程式設計方式產生工作階段識別碼和授權的值 

    if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
        linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
    {
        AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

        WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
        string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

        AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
        if (azureDataLakeStoreProperties != null)
        {
            azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeStoreProperties.Authorization = authorization;
        }

        AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
        if (azureDataLakeAnalyticsProperties != null)
        {
            azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeAnalyticsProperties.Authorization = authorization;
        }
    }

請參閱[AzureDataLakeStoreLinkedService 類別](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)、 [AzureDataLakeAnalyticsLinkedService 課程](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)]，以及[AuthorizationSessionGetResponse 類別](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx)主題的程式碼中使用的資料工廠類別的詳細資料。 新增參照︰ Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll WindowsFormsWebAuthenticationDialog 類別。 
 
 
## <a name="data-lake-analytics-u-sql-activity"></a>資料湖分析 U SQL 活動 

下列 JSON 程式碼片段定義的管線與資料湖分析 U SQL 活動。 活動定義具有您先前建立的連結 Azure 資料湖分析服務的參考。   
  

    {
        "name": "ComputeEventsByRegionPipeline",
        "properties": {
            "description": "This is a pipeline to compute events for en-gb locale and date less than 2012/02/19.",
            "activities": 
            [
                {
                    "type": "DataLakeAnalyticsU-SQL",
                    "typeProperties": {
                        "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                        "scriptLinkedService": "StorageLinkedService",
                        "degreeOfParallelism": 3,
                        "priority": 100,
                        "parameters": {
                            "in": "/datalake/input/SearchLog.tsv",
                            "out": "/datalake/output/Result.tsv"
                        }
                    },
                    "inputs": [
                        {
                            "name": "DataLakeTable"
                        }
                    ],
                    "outputs": 
                    [
                        {
                            "name": "EventsByRegionTable"
                        }
                    ],
                    "policy": {
                        "timeout": "06:00:00",
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 1
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "name": "EventsByRegion",
                    "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
                }
            ],
            "start": "2015-08-08T00:00:00Z",
            "end": "2015-08-08T01:00:00Z",
            "isPaused": false
        }
    }


下表說明名稱和描述的此活動特有的屬性。 

屬性 | 描述 | 所需
:-------- | :----------- | :--------
類型 | [類型] 屬性必須設定**DataLakeAnalyticsU**sql。 | [是]
scriptPath | 包含 U SQL 指令碼資料夾的路徑。 檔案的名稱會區分大小寫。 | 不需要 （如果您使用指令碼）
scriptLinkedService | 連結包含資料 factory 指令碼的儲存空間的連結的服務 | 不需要 （如果您使用指令碼）
指令碼 | 指定內嵌指令碼，而不是指定 scriptPath 和 scriptLinkedService。 例如: 「 指令碼 」: 「 建立資料庫測試 」。 | 不需要 （如果您使用 scriptPath 和 scriptLinkedService）
degreeOfParallelism | 同時使用執行工作的節點數目上限。 | 無
優先順序 | 決定執行第一個應選取 [不在佇列中所有的工作。 較低的數字、 最高優先順序。 | 無 
參數 | U SQL 指令碼的參數 | 無 

指令碼定義，請參閱[SearchLogProcessing.txt 指令碼定義](#script-definition)。 

## <a name="sample-input-and-output-datasets"></a>範例輸入與輸出資料集

### <a name="input-dataset"></a>輸入資料集
在此範例中，輸入的資料位於 Azure 資料湖存放區 （datalake/輸入資料夾中的 SearchLog.tsv 檔案）。 

    {
        "name": "DataLakeTable",
        "properties": {
            "type": "AzureDataLakeStore",
            "linkedServiceName": "AzureDataLakeStoreLinkedService",
            "typeProperties": {
                "folderPath": "datalake/input/",
                "fileName": "SearchLog.tsv",
                "format": {
                    "type": "TextFormat",
                    "rowDelimiter": "\n",
                    "columnDelimiter": "\t"
                }
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }   

### <a name="output-dataset"></a>輸出資料集
在此範例中，所產生的 U SQL 指令碼的輸出資料會儲存於 Azure 資料湖存放區 （datalake 輸出資料夾）。 

    {
        "name": "EventsByRegionTable",
        "properties": {
            "type": "AzureDataLakeStore",
            "linkedServiceName": "AzureDataLakeStoreLinkedService",
            "typeProperties": {
                "folderPath": "datalake/output/"
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }

### <a name="sample-data-lake-store-linked-service"></a>範例資料湖存放連結服務
以下是的範例 Azure 資料湖存放連結服務所使用的輸入輸出資料集的定義。 

    {
        "name": "AzureDataLakeStoreLinkedService",
        "properties": {
            "type": "AzureDataLakeStore",
            "typeProperties": {
                "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
                "sessionId": "<session ID>",
                "authorization": "<authorization URL>"
            }
        }
    }

請參閱[移動到資料，並從 Azure 資料湖存放區](data-factory-azure-datalake-connector.md)本文 JSON 屬性的說明。 

## <a name="sample-u-sql-script"></a>範例 U SQL 指令碼 

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM @in
        USING Extractors.Tsv(nullEscape:"#NULL#");
    
    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";
    
    @rs1 =
        SELECT Start, Region, Duration
        FROM @rs1
        WHERE Start <= DateTime.Parse("2012/02/19");
    
    OUTPUT @rs1   
        TO @out
          USING Outputters.Tsv(quoting:false, dateTimeFormat:null);

值**@in**和**@out**U SQL 指令碼中的參數傳遞動態 ADF，使用參數] 區段。 請參閱管線定義中的參數] 區段。

您可以在您的管線定義為 Azure 資料湖分析服務上執行的工作，以及指定其他屬性，例如 degreeOfParallelism 和優先順序。

## <a name="dynamic-parameters"></a>動態參數
和縮小範例管線定義中參數會指定硬式編碼的值。 

    "parameters": {
        "in": "/datalake/input/SearchLog.tsv",
        "out": "/datalake/output/Result.tsv"
    }

就可以改為使用動態參數。 例如︰ 

    "parameters": {
        "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
        "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
    }

在此案例中，輸入的檔案會仍 /datalake/input 資料夾中，且輸出檔案會產生 /datalake/output 資料夾中。 檔案名稱會動態根據的扇形區開始時間。  