<properties 
    pageTitle="如何設定資料流分析 Azure 電腦學習端點 |Microsoft Azure" 
    description="在資料流分析機器語言使用者定義函數"
    keywords=""
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"
/>

# <a name="machine-learning-integration-in-stream-analytics"></a>電腦中串流分析的學習整合

資料流分析支援撥 Azure 電腦學習結束點的使用者定義函數。 REST API 支援此功能的詳細[資料流分析 REST API 文件庫](https://msdn.microsoft.com/library/azure/dn835031.aspx)中。 本文提供補充成功實作這項功能，在 [串流分析所需的資訊。 教學課程也張貼，且可[在這裡](stream-analytics-machine-learning-integration-tutorial.md)。

## <a name="overview-azure-machine-learning-terminology"></a>概觀︰ Azure 電腦學習詞彙

Microsoft Azure 電腦學習提供共同作業、 拖放工具，您可以使用來建立、 測試和部署預測狀況分析資料的解決方案。 這項工具稱為*Azure 電腦學習 Studio*。 Studio 用來互動的電腦學習資源並輕鬆建立、 測試和持續設計。 這些資源和其定義如下。

- **工作區**︰*工作區*會保留所有其他電腦學習資源管理及控制項的容器在一起的容器。
- **實驗**︰*實驗*由資料科學家利用資料集，並訓練的電腦學習模型。
- **端點**︰*端點*是用來功能做為輸入，指定的電腦學習模型並傳回計分輸出 Azure 電腦學習的物件。
- **計分 Webservice**︰ 上述*計分 webservice*是端點的集合。

每個端點有 api 批次執行和同步執行。 資料流分析使用同步執行。 [要求/回應服務](../machine-learning/machine-learning-consume-web-services.md#request-response-service-rrs)AzureML studio 中名稱為特定的服務。

## <a name="machine-learning-resources-needed-for-stream-analytics-jobs"></a>電腦學習串流分析作業所需的資源

基於串流分析的工作處理、 要求/回應端點、 [apikey](../machine-learning/machine-learning-connect-to-azure-machine-learning-web-service.md#get-an-azure-machine-learning-authorization-key)和 swagger 定義是順利執行所有必要。 資料流分析有其他端點建構 swagger 端點 url、 查閱介面，使用者會回到預設 UDF 定義。

## <a name="configure-a-stream-analytics-and-machine-learning-udf-via-rest-api"></a>設定資料流分析及電腦學習 UDF 透過 REST API

您可能會使用 REST Api 來設定您撥打 Azure 機器語言函數的工作。 步驟如下所示︰

1. 建立資料流分析工作
2. 定義輸入
3. 定義成果
4. 建立使用者定義函數 (UDF)
5. 撰寫呼叫 UDF 串流分析轉換
6. 啟動工作

## <a name="creating-a-udf-with-basic-properties"></a>建立基本的屬性 UDF

作為範例，下列範例會建立名為*newudf*繫結至 Azure 電腦學習端點純量 UDF。 請注意，您可以在所選的服務與*apiKey* API 說明頁面找到*結束點*（服務 URI） 位於服務主頁面。

````
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>  
````

範例邀請本文︰  

````
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77fb4b46bf2a30c63c078dca/services/b7be5e40fd194258796fb402c1958eaf/execute ",
                        "apiKey": "replacekeyhere"
                    }
                }
            }
        }
    }
````

## <a name="call-retrievedefaultdefinition-endpoint-for-default-udf"></a>預設 UDF 的通話 RetrieveDefaultDefinition 端點

建立基本架構 UDF 之後，需要完成 UDF 的定義。 RetreiveDefaultDefinition 結束點，可協助您取得繫結到 Azure 電腦學習端點的純量函數的預設定義。 下列內容會要求您取得預設 UDF 的定義繫結到 Azure 電腦學習端點純量函數。 具有已提供放入要求期間不會指定實際的結束點。 資料流分析通話如果明確提供在邀請中所提供的端點。 否則，它會使用原本參照的項目。 以下是單一字串參數 （句子） 及傳回單一輸出類型的 UDF 會花費的字串表示的句子的 「 舉動 」 標籤。

````
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
````

範例邀請本文︰  

````
    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }
````

樣本輸出這看起來想下方。  

````
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
````

## <a name="patch-udf-with-the-response"></a>修補程式 UDF 與回應 

現在 UDF 必須修正與先前的回應，如下所示。

````
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
````

邀請內文 （從 RetrieveDefaultDefinition 輸出）︰

````
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
````

## <a name="implement-stream-analytics-transformation-to-call-the-udf"></a>實作撥打 UDF 串流分析轉換

現在針對每一個輸入事件的查詢 UDF （以下稱 scoreTweet），然後輸出撰寫該事件的回應。  

````
    {
        "name": "transformation",
        "properties": {
            "streamingUnits": null,
            "query": "select *,scoreTweet(Tweet) TweetSentiment into blobOutput from blobInput"
        }
    }
````


## <a name="get-help"></a>取得說明
進一步協助，請嘗試我們[Azure 資料流分析論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>後續步驟

- [Azure 資料流分析簡介](stream-analytics-introduction.md)
- [開始使用 Azure 資料流狀況分析](stream-analytics-get-started.md)
- [不按比例縮放 Azure 資料流分析工作](stream-analytics-scale-jobs.md)
- [Azure 資料流分析查詢語言參照](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 資料流分析管理 REST API 參照](https://msdn.microsoft.com/library/azure/dn835031.aspx)
