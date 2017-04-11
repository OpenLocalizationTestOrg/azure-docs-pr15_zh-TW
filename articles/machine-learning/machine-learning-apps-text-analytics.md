<properties
    pageTitle="電腦學習 Api︰ 文字分析 |Microsoft Azure"
    description="Microsoft 的電腦學習文字分析 Api 可以用於分析舉動分析、 索引鍵的片語擷取、 語言偵測及主題偵測非結構化的文字。"
    services="machine-learning"
    documentationCenter=""
    authors="onewth"
    manager="jhubbard"
    editor="cgronlun"/> 

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="onewth"/>


# <a name="machine-learning-apis-text-analytics-for-sentiment-key-phrase-extraction-language-detection-and-topic-detection"></a>電腦學習 Api︰ 舉動，文字分析按鍵片語擷取語言偵測與主題偵測

>[AZURE.NOTE] 本指南為 API 第 1 版。 2，[**請參閱這份文件**](../cognitive-services/cognitive-services-text-analytics-quick-start.md)版本。 第 2 版現在此 api 喜好的版本。

## <a name="overview"></a>概觀

文字分析 API 是文字分析[web 服務](https://datamarket.azure.com/dataset/amla/text-analytics)使用 Azure 電腦學習套件。 API 可用來分析工作，例如舉動分析與索引鍵的片語擷取、 語言偵測主題偵測非結構化的文字。 若要使用這個 API 需要沒有訓練資料︰ 只顯示您文字的資料。 此 API 使用處理技術的進階的自然語言進行最佳中類別預測。

您可以看到 [作用中的文字分析我們[示範網站](https://text-analytics-demo.azurewebsites.net/)，您也會如何實作文字分析 C# 和 Python 中尋找[範例](https://text-analytics-demo.azurewebsites.net/Home/SampleCode)的位置。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)] 

---

## <a name="sentiment-analysis"></a>舉動分析

API 會傳回 0 和 1 之間的數字分數。 1 接近的分數表示正數舉動，，0 接近的分數表示的負數舉動時。 使用分類技巧，會產生舉動分數。 輸入類別器功能包括 n-克，產生的組件的語音的標籤，以及 word 內嵌的功能。 目前英文為僅支援的語言。
 
## <a name="key-phrase-extraction"></a>索引鍵的片語擷取

API 會傳回代表談話重點中輸入的文字字串的清單。 我們會使用從 Microsoft Office 的複雜自然語言處理工具組技巧。 目前英文為僅支援的語言。

## <a name="language-detection"></a>偵測語言

API 會傳回 0 和 1 之間的偵測到的語言和數字分數。 接近 1 的分數表示的識別的語言為真，則 100%確定性。 支援 120 語言總計。

## <a name="topic-detection"></a>主題偵測

這是新發行的 API 頂端偵測到主題的清單會傳回提交文字記錄。 索引鍵的片語，則可為以識別主題或文字的相關詳細資訊。 此 API 需要最小值的文字記錄 100 送出，但設計用來偵測主題跨數以百計數以千計的記錄。 請注意，此 API 費用 1 交易每提交的文字記錄。 API 被為了適用於簡短、 人力資源的撰寫文字，例如評論和使用者意見反應。

---

## <a name="api-definition"></a>API 定義

### <a name="headers"></a>頁首

確保正確的標頭納入您的要求，應如下︰

    Authorization: Basic <creds>
    Accept: application/json
               
    Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

您可以從您的帳戶中找到您的帳戶金鑰， [Azure 資料市場](https://datamarket.azure.com/account/keys)中。 請注意目前只 JSON 會接受輸入與輸出格式。 XML 不受支援。

---

## <a name="single-response-apis"></a>單一回應 Api

### <a name="getsentiment"></a>GetSentiment

**URL** 

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment

**範例要求**

在下方通話，我們會要求舉動分析"Hello World 」 的字詞︰

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment?Text=hello+world

這會傳回回應，如下所示︰

    {
      "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
        "Score":1.0
    }

---

### <a name="getkeyphrases"></a>GetKeyPhrases

**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases

**範例要求**

在下面的撥號中，我們會要求主要片語中找到文字 」 時，保持唯一裝潢與好記的員工美好飯店 」:

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases?
    Text=It+was+a+wonderful+hotel+to+stay+at,+with+unique+decor+and+friendly+staff

這會傳回回應，如下所示︰

    {
      "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
      "KeyPhrases":[
        "wonderful hotel",
        "unique decor",
        "friendly staff"
      ]
    }
 
---

### <a name="getlanguage"></a>GetLanguage

**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguage

**範例要求**

在下面的取得撥號中，我們要求的主要文字*什麼*片語的舉動

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguages?
    Text=Hello+World

這會傳回回應，如下所示︰

    {
      "UnknownLanguage": false,
      "DetectedLanguages": [{
        "Name": "English",
        "Iso6391Name": "en",
        "Score": 1.0
      }]
    }

**選用的參數**

`NumberOfLanguagesToDetect`這是選擇性參數。 預設值為 1。

---

## <a name="batch-apis"></a>批次 Api

文字分析服務可讓您執行舉動和鍵片語擷取批次模式。 請注意，每個記錄一次交易計分計數。 舉例來說，如果要求舉動 1000年記錄在單一撥號中，1000年交易會會後減去這些。

請注意，系統中輸入的識別碼系統所傳回的識別碼。 Web 服務並不會檢查這些 Id 是唯一。 負責的來電者驗證唯一性。 


### <a name="getsentimentbatch"></a>GetSentimentBatch

**URL** 

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch

**範例要求**

下方文章通話，我們會要求的片語"Hello World 」、 「 Foo 什麼 」 和 「 我什麼 」 在邀請內文中 sentiments:

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch 

要求本文︰

    {"Inputs":
    [
        {"Id":"1","Text":"hello world"},
        {"Id":"2","Text":"hello foo world"},
        {"Id":"3","Text":"hello my world"},
    ]}

在下面的回應時，您會收到成績文字識別碼相關聯的清單︰

    {
      "odata.metadata":"<url>", 
      "SentimentBatch":
      [
        {"Score":0.9549767,"Id":"1"},
        {"Score":0.7767222,"Id":"2"},
        {"Score":0.8988889,"Id":"3"}
      ],  
      "Errors":[]
    }


---

### <a name="getkeyphrasesbatch"></a>GetKeyPhrasesBatch

**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

**範例要求**

在此範例中，我們會要求 sentiments 主要片語下列的文字中的清單︰ 

* 「 時，保持唯一裝潢與易記的員工美好旅館 」
* 「 時令人讚嘆的建立會議，很有趣的討論與 」
* 「 流量是恐怖，我花費前往機場的三個小時 」

此要求端點文章呼叫為︰

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

要求本文︰

    {"Inputs":
    [
        {"Id":"1","Text":"It was a wonderful hotel to stay at, with unique decor and friendly staff"},
        {"Id":"2","Text":"It was an amazing build conference, with very interesting talks"},
        {"Id":"3","Text":"The traffic was terrible, I spent three hours going to the airport"}
    ]}

在下面的回應時，您會收到主要文字識別碼相關聯的片語的清單︰

    { "odata.metadata":"<url>",
        "KeyPhrasesBatch":
        [
           {"KeyPhrases":["unique decor","friendly staff","wonderful hotel"],"Id":"1"},
           {"KeyPhrases":["amazing build conference","interesting talks"],"Id":"2"},
           {"KeyPhrases":["hours","traffic","airport"],"Id":"3" }
        ],
        "Errors":[]
    }

---

### GetLanguageBatch

In the POST call below, we are requesting language detection for two text inputs:

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguageBatch

Request body:

    {
      "Inputs": [
        {"Text": "hello world", "Id": "1"},
        {"Text": "c'est la vie", "Id": "2"}
      ]
    }

This returns the following response, where English is detected in the first input and French in the second input:

    {
       "LanguageBatch": [{
         "Id": "1",
         "DetectedLanguages": [{
            "Name": "英文",
            "Iso6391Name": "en",
            "Score": 1.0
         }],
         "UnknownLanguage": false
       },
       {
         "Id": "2",
         "DetectedLanguages": [{
            "Name": "法文",
            "Iso6391Name": "fr",
            "Score": 1.0
         }],
         "UnknownLanguage": false
       }],
       "Errors": []
    }

---

## <a name="topic-detection-apis"></a>主題偵測 Api

這是新發行的 API 頂端偵測到主題的清單會傳回提交文字記錄。 索引鍵的片語，則可為以識別主題或文字的相關詳細資訊。 請注意，此 API 費用 1 交易每提交的文字記錄。

此 API 需要最小值的文字記錄 100 送出，但設計用來偵測主題跨數百數以千計的記錄。


### <a name="topics--submit-job"></a>主題 – 送出工作

**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/StartTopicDetection

**範例要求**


在下列文章撥號中，我們會要求 100 的文章，其中的第一個和最後一個輸入的文章會顯示，而兩個 StopPhrases 都包含在內的一組主題。

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/StartTopicDetection HTTP/1.1

要求本文︰

    {"Inputs":[
        {"Id":"1","Text":"I loved the food at this restaurant"},
        ...,
        {"Id":"100","Text":"I hated the decor"}
    ],
    "StopPhrases":[
        "restaurant", “visitor"
    ]}

在下面的回應時，您取得 JobId 送出的工作︰

    {
        "odata.metadata":"<url>",
        "JobId":"<JobId>"
    }

一個單字或多個 word 片語不會傳回主題的清單。 可以用於篩選出非常一般的主題。 例如在旅館檢閱相關資料集，「 旅館 」 和 「 hostel 」 可能合理停駐點的片語。  

### <a name="topics--poll-for-job-results"></a>主題 – 工作的投票結果

**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetTopicDetectionResult

**範例要求**

傳遞 JobId 傳回的 「 送出工作 」 步驟以擷取結果。 我們建議您撥打這個端點狀態直到每分鐘 = 在回應中的 「 完成 」。 要花約為 10 分鐘的工作完成，或使用數千記錄的工作。

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetTopicDetectionResult?JobId=<JobId>


處理時, 回應會，如下所示︰

    {
        "odata.metadata":"<url>",
        "Status":"Running",
        "TopicInfo":[],
        "TopicAssignment":[],
        "Errors":[]
    }


API 傳回輸出 JSON 格式，以下列格式︰

    {
        "odata.metadata":"<url>",
        "Status":"Finished",
        "TopicInfo":[
        {
            "TopicId":"ed00480e-f0a0-41b3-8fe4-07c1593f4afd",
            "Score":8.0,
            "KeyPhrase":"food"
        },
        ...
        {
            "TopicId":"a5ca3f1a-fdb1-4f02-8f1b-89f2f626d692",
            "Score":6.0,
            "KeyPhrase":"decor"
            }
        ],
        "TopicAssignment":[
        {
            "Id":"1",
            "TopicId":"ed00480e-f0a0-41b3-8fe4-07c1593f4afd",
            "Distance":0.7809
        },
        ...
        {
            "Id":"100",
            "TopicId":"a5ca3f1a-fdb1-4f02-8f1b-89f2f626d692",
            "Distance":0.8034
        }
        ],
        "Errors":[]


回應的每一個組件屬性如下所示︰

**TopicInfo 屬性**

| 索引鍵 | 描述 |
|:-----|:----|
| TopicId | 每個主題的的唯一識別碼。 |
| 成績 | 指定主題的記錄數目。 |
| KeyPhrase | Summarizing 單字或片語的主題。 可以是 1 或多個字。 |

**TopicAssignment 屬性**

| 索引鍵 | 描述 |
|:-----|:----|
| 識別碼 | 記錄的識別碼。 可輸入中所包含的識別碼。 |
| TopicId | 記錄已指派給主題識別碼。 |
| 距離 | Confidence 記錄所屬的主題。 零近距離表示較高的信賴度。 |
