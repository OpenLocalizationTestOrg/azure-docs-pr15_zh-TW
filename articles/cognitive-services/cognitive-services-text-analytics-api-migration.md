<properties
    pageTitle="升級至版本 2 的文字分析 API |Microsoft Azure"
    description="Azure 電腦學習文字分析-升級至新版 2"
    services="cognitive-services"
    documentationCenter=""
    authors="onewth"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cognitive-services"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="onewth"/>

# <a name="upgrading-to-version-2-of-the-text-analytics-api"></a>升級至版本 2 的文字分析 API #

本指南將引導您使用的[第一個版本的 API](../machine-learning/machine-learning-apps-text-analytics.md)使用的第二個版本升級您的程式碼的程序。 

如果您沒有使用 API，並想要深入瞭解，您可以**[進一步瞭解以下的 API](//go.microsoft.com/fwlink/?LinkID=759711)** ，或**[依照 [快速入門指南](//go.microsoft.com/fwlink/?LinkID=760860)**。 技術的參照，請參閱**[API 定義](//go.microsoft.com/fwlink/?LinkID=759346)**。

### <a name="part-1-get-a-new-key"></a>第 1 部分。 取得新的金鑰 ###

首先，您將需要從**Azure 入口網站**取得新的 API 金鑰︰

1. 瀏覽至文字分析服務，透過[Cortana 智慧圖庫](//gallery.cortanaintelligence.com/MachineLearningAPI/Text-Analytics-2)。 在這裡，您也會尋找文件和程式碼範例的連結。

1. 按一下 [**註冊**]。 此連結帶您 Azure 管理入口網站，您可以註冊服務。

1. 選取一個方案。 您可以選取**5000 的交易月免費層**。 免費的計劃一樣，您將不會使用服務。 您必須登入您的 Azure 訂閱。 

1. 文字分析登入之後，您會有**API 金鑰**。 當您將需要使用 API 服務時，請複製此鍵。

### <a name="part-2-update-the-headers"></a>第 2 部分。 更新標題 ###

更新已提交的標頭值如下所示。 請注意 [帳戶金鑰已不再編碼。

**第 1 版**

    Authorization: Basic base64encode(<your Data Market account key>)
    Accept: application/json

**第 2**

    Content-Type: application/json
    Accept: application/json
    Ocp-Apim-Subscription-Key: <your Azure Portal account key>


### <a name="part-3-update-the-base-url"></a>第 3 部分。 更新基底 URL ###

**第 1 版**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/

**第 2**

    https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/

### <a name="part-4a-update-the-formats-for-sentiment-key-phrases-and-languages"></a>組件匣 4a。 更新舉動、 主要片語和語言格式 ###

#### <a name="endpoints"></a>結束點 ####

取得結束點有現在已被取代，所以會送出所有的輸入做為文章要求。 更新下方所顯示的項目結束點。

| |第 1 版單一端點|第 1 版批次端點|第 2 端點|
|---|---|---|---|
|通話類型|取得|文章|文章|
|舉動|```GetSentiment```|```GetSentimentBatch```|```sentiment```|
|主要片語|```GetKeyPhrases```|```GetKeyPhrasesBatch```|```keyPhrases```|
|語言|```GetLanguage```|```GetLanguageBatch```|```languages```|

#### <a name="input-formats"></a>輸入的格式 ####

請注意現在接受的唯一的文章格式，因此您應該重新格式化先前會相應地使用單一文件結束點的任何輸入。 輸入不區分大小寫。

**版本 1 （批次）**

    {
      "Inputs": [
        {
          "Id": "string",
          "Text": "string"
        }
      ]
    }

**第 2**

    {
      "documents": [
        {
          "id": "string",
          "text": "string"
        }
      ]
    }

#### <a name="output-from-sentiment"></a>從舉動輸出 ####

**第 1 版**

    {
      "SentimentBatch":[{
        "Id":"string",
        "Score":"double"
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**第 2**

    {
      "documents":[{
        "id":"string",
        "score":"double"
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }

#### <a name="output-from-key-phrases"></a>從主要片語輸出 ####

**第 1 版**

    {
      "KeyPhrasesBatch":[{
        "Id":"string",
        "KeyPhrases":["string"]
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**第 2**

    {
      "documents":[{
        "id":"string",
        "keyPhrases":["string"]
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }

#### <a name="output-from-languages"></a>輸出語言 ####


**第 1 版**

    {
      "LanguageBatch":[{
        "id":"string",
        "detectedLanguages": [{
          "Score":"double"
          "Name":"string",
          "Iso6391Name":"string"
        }]
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**第 2**

    {
      "documents":[{
        "id":"string",
        "detectedLanguages": [{
          "score":"double"
          "name":"string",
          "iso6391Name":"string"
        }]
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }


### <a name="part-4b-update-the-formats-for-topics"></a>組件 4b。 更新主題的格式 ###

#### <a name="endpoints"></a>結束點 ####

| |第 1 版端點 | 第 2 端點|
|---|---|---|
|送出主題偵測 （文章）|```StartTopicDetection```|```topics```|
|擷取主題結果 （取得）|```GetTopicDetectionResult?JobId=<jobId>```|```operations/<operationId>```|

#### <a name="input-formats"></a>輸入的格式 ####

**第 1 版**

    {
      "StopWords": [
        "string"
      ],
      "StopPhrases": [
        "string"
      ], 
      "Inputs": [
        {
          "Id": "string",
          "Text": "string"
        }
      ]
    }

**第 2**

    {
      "stopWords": [
        "string"
      ],
      "stopPhrases": [
        "string"
      ],
      "documents": [
        {
          "id": "string",
          "text": "string"
        }
      ]
    }

#### <a name="submission-results"></a>送出的結果 ####

**版本 1 （文章）**

先前，完成工作之後，您會收到下列 JSON 輸出，jobId 會附加至電腦的輸出的 URL。

    {
        "odata.metadata":"<url>",
        "JobId":"<JobId>"
    }

**第 2 （文章）**

回覆會立即包含頁首的值，如下所示，其中`operation-location`的端點用於投票的結果︰

    'operation-location': 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>'

#### <a name="operation-results"></a>作業結果 ####

**版本 1 （取得）**

    {
      "TopicInfo" : [{
        "TopicId" : "string"
        "Score" : "double"
        "KeyPhrase" : "string"
      }],
      "TopicAssignment" : [{
        "Id" : "string",
        "TopicId" : "string",
        "Distance" : "double"
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**第 2 （取得）**

為之前，會傳回**定期投票輸出**（建議的期間會為每分鐘） 輸出為止。 

當主題 API 完成後，狀態讀取`succeeded`會傳回。 此然後會包含輸出結果如下所示的格式︰

    {
        "status": "succeeded",
        "createdDateTime": "string",
        "operationType": "topics",
        "processingResult": {
            "topics" : [{
            "id" : "string"
            "score" : "double"
            "keyPhrase" : "string"
          }],
          "topicAssignments" : [{
            "topicId" : "string",
            "documentId" : "string",
            "distance" : "double"
          }],
          "errors" : [{
              "id":"string",
              "message":"string"
          }]
        }
    }

### <a name="part-5-test-it"></a>第 5 部分。 測試 ！ ###

您現在應該就緒 ！ 測試您的程式碼的小的範例，以確保您可以順利處理您的資料。
