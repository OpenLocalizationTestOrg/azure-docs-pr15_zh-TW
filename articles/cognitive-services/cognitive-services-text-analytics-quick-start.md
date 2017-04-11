<properties
    pageTitle="快速入門指南︰ 電腦學習文字分析 Api |Microsoft Azure"
    description="Azure 電腦學習文字分析-快速入門指南"
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

# <a name="getting-started-with-the-text-analytics-apis-to-detect-sentiment-key-phrases-topics-and-language"></a>快速入門文字分析 Api 偵測舉動、 主要片語、 主題及語言

<a name="HOLTop"></a>

本文將說明如何內建到您的服務或應用程式使用[文字分析 Api](//go.microsoft.com/fwlink/?LinkID=759711)。
您可以使用這些 Api，讓文字偵測舉動、 主要片語、 主題及語言。 [若要查看體驗的互動式示範，請按一下這裡。](//go.microsoft.com/fwlink/?LinkID=759712)

請參閱技術文件的 api [API 定義](//go.microsoft.com/fwlink/?LinkID=759346)。

本指南為版本 2 的 Api。 如需版本 1 的 Api，[請參閱本文件](../machine-learning/machine-learning-apps-text-analytics.md)的詳細資訊。

本教學課程結束時，您可以以程式設計方式偵測︰

- **舉動**-是正值或負值是文字嗎？

- **索引鍵片語**-有哪些人員討論單一文件？

- **主題**-有哪些人員討論跨多個文件？

- **語言**的語言是文字撰寫嗎？

請注意，此 API 費用 1 交易每個提交的文件。 例如，如果要求舉動 1000年文件，在單一撥號中，1000年交易會會後減去這些。



<a name="Overview"></a>
## <a name="general-overview"></a>一般的概觀 ##

這份文件是逐步指示。 我們的目標是逐步引導您完成所需訓練是模型，並指向您的資源，可讓您將其放在生產環境中的步驟。 這個練習會需要花 30 分鐘。

這些工作，您會需要編輯者，然後呼叫 RESTful 結束點，您所選擇的語言。

現在就讓我們開始吧 ！

## <a name="task-1---signing-up-for-the-text-analytics-apis"></a>任務 1-文字分析 Api 打造簽章 ####

在此工作中，您將文字分析服務的登入。

1. 瀏覽至[Azure 入口網站](//go.microsoft.com/fwlink/?LinkId=761108)中的**認知服務**，並確保**文字分析**已為 API 類型。

1. 選取一個方案。 您可以選取**5000 的交易月免費層**。 免費的計劃一樣，您將不會使用服務。 您必須登入您的 Azure 訂閱。 

1. 完成的其他欄位，並建立您的帳戶。

1. 文字分析登入之後，尋找您的**API 金鑰**。 當您將需要使用 API 服務時，請複製主索引鍵。


## <a name="task-2---detect-sentiment-key-phrases-and-languages"></a>任務 2-偵測舉動、 主要片語及語言 ####

您可以輕鬆在文字中偵測舉動、 主要片語和語言。 [示範體驗](//go.microsoft.com/fwlink/?LinkID=759712)傳回時，會以程式設計方式獲得相同的結果。

>[AZURE.TIP] 為舉動分析，我們建議您將文字分割成的句子。 這通常會導致舉動預測中較高的精確度。

請注意，支援的語言，如下所示︰

| 功能 | 支援的語言代碼 |
|:-----|:----|
| 舉動 | `en`（英文）， `es` （西班牙文） `fr` （法文）， `pt` （葡萄牙） |
| 主要片語 | `en`（英文）， `es` （西班牙文） `de` （德文）， `ja` （日文） |


1. 您必須設定下列的標題。 請注意，目前 JSON 的 api 只接受輸入的格式。 XML 不受支援。

        Ocp-Apim-Subscription-Key: <your API key>
        Content-Type: application/json
        Accept: application/json

1. 接下來，設定您輸入的資料列中 JSON 的格式。 如需舉動、 主要片語與語言，格式是一樣的。 請注意，每個 ID 都必須是唯一將 ID 傳回由系統。 可以傳送一份文件的大小上限為 10 KB，並提交輸入的總大小上限為 1 MB。 一個通話提交不超過 1000 的文件。 速率限制存在於率 100 個撥通話每分鐘-因此，建議您送出大量的單一呼叫中的文件。 語言是選擇性參數可指定如果分析非英文文字。 輸入的範例如下所示，其中的選擇性參數`language`舉動分析或鍵片語擷取是包含︰

        {
            "documents": [
                {
                    "language": "en",
                    "id": "1",
                    "text": "First document"
                },
                ...
                {
                    "language": "en",
                    "id": "100",
                    "text": "Final document"
                }
            ]
        }

1. 撥打電話**文章**與舉動、 主要片語和語言輸入系統。 Url 看起來，如下所示︰

        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment
        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases
        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages

1. 此呼叫會傳回 JSON 格式化回應識別碼，並偵測到的內容。 以下範例舉動的輸出 （以排除錯誤詳細資料）。 若是舉動，每份文件會傳回 0 和 1 之間的分數︰

        // Sentiment response
        {
            "documents": [
                {
                    "id": "1",
                    "score": "0.934"
                },
                ...
                {
                    "id": "100",
                    "score": "0.002"
                },
            ]
        }

        // Key phrases response
        {
            "documents": [
                {
                    "id": "1",
                    "keyPhrases": ["key phrase 1", ..., "key phrase n"]
                },
                ...
                {
                    "id": "100",
                    "keyPhrases": ["key phrase 1", ..., "key phrase n"]
                },
            ]
        }

        // Languages response
        {
            "documents": [
                {
                    "id": "1",
                    "detectedLanguages": [
                        {
                            "name": "English",
                            "iso6391Name": "en",
                            "score": "1"
                        }
                    ]
                },
                ...
                {
                    "id": "100",
                    "detectedLanguages": [
                        {
                            "name": "French",
                            "iso6391Name": "fr",
                            "score": "0.985"
                        }
                    ]
                }
            ]
        }


## <a name="task-3---detect-topics-in-a-corpus-of-text"></a>任務 3-偵測主題中的文字中華 ####

這是新發行的 API 頂端偵測到主題的清單會傳回提交文字記錄。 索引鍵的片語，則可為以識別主題或文字的相關詳細資訊。 API 被為了適用於簡短、 人力資源的撰寫文字，例如評論和使用者意見反應。

此 API 需要**至少文字記錄 100 個**送出，但設計用來偵測主題跨數百數以千計的記錄。 任何非英語記錄或小於 3 個單字的記錄會遭到捨棄，因此不會有主題。 主題偵測可以傳送一份文件的最大 30 KB，並提交輸入的總大小上限為 30 MB。 主題偵測是限制為 5 送出 」 每 5 分鐘的工資率。

有兩個其他**選用**的輸入的參數，以協助改善品質結果︰

- **停止文字。**  要從整個主題偵測管線排除這些字] 並關閉表單 （例如複數）。 使用此一般文字 （範例、 「 問題 」、 「 錯誤 」 和 「 使用者 」 可能適當的選項，為客戶抱怨軟體）。 每個字串應該是一個單字。
- **停止片語**-這些語句會排除傳回主題的清單。 使用此選項排除不想在結果中看到的一般主題。 例如，「 Microsoft 」 和 「 Azure 」 是適當的選項，若要排除的主題。 字串可以包含多個字。

遵循下列步驟來偵測文字中的主題。

1. 格式化 JSON 中的輸入。 這次，您可以定義停駐點的文字，並停止片語。

        {
            "documents": [
                {
                    "id": "1",
                    "text": "First document"
                },
                ...
                {
                    "id": "100",
                    "text": "Final document"
                }
            ],
            "stopWords": [
                "issue", "error", "user"
            ],
            "stopPhrases": [
                "Microsoft", "Azure"
            ]
        }

1. 任務 2 中所定義，請使用相同的頁首，請撥主題端點**文章**︰

        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/topics

1. 這會傳回`operation-location`的標頭中的回應，其中的值是查詢結果的主題的 URL 為︰

        'operation-location': 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>'

1. 查詢傳回`operation-location`定期與**GET**要求。 一次每分鐘建議。

        GET https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>

1. 端點會傳回回應，包括`{"status": "notstarted"}`之前處理、`{"status": "running"}`時處理和`{"status": "succeeded"}`輸出一旦完成。 然後，您可以使用這會以下列格式 （請注意詳細資料等錯誤格式和日期排除這個範例），輸出︰

        {
            "status": "succeeded",
            "operationProcessingResult": {
                "topics": [
                    {
                        "id": "8b89dd7e-de2b-4a48-94c0-8e7844265196"
                        "score": "5"
                        "keyPhrase": "first topic name"
                    },
                    ...
                    {
                        "id": "359ed9cb-f793-4168-9cde-cd63d24e0d6d"
                        "score": "3"
                        "keyPhrase": "final topic name"
                    }
                ],
                "topicAssignments": [
                    {
                        "topicId": "8b89dd7e-de2b-4a48-94c0-8e7844265196",
                        "documentId": "1",
                        "distance": "0.354"
                    },
                    ...
                    {
                        "topicId": "359ed9cb-f793-4168-9cde-cd63d24e0d6d",
                        "documentId": "55",
                        "distance": "0.758"
                    },            
                ]
            }
        }

請注意，從主題的成功回應`operations`端點會有下列結構描述︰

    {
            "topics" : [{
                "id" : "string",
                "score" : "number",
                "keyPhrase" : "string"
            }],
            "topicAssignments" : [{
                "documentId" : "string",
                "topicId" : "string",
                "distance" : "number"
            }],
            "errors" : [{
                "id" : "string",
                "message" : "string"
            }]
        }

每個部分的這個回應的說明如下所示︰

**主題**

| 索引鍵 | 描述 |
|:-----|:----|
| 識別碼 | 每個主題的的唯一識別碼。 |
| 成績 | 指定主題的文件的計數。 |
| keyPhrase | Summarizing 單字或片語的主題。 |

**topicAssignments**

| 索引鍵 | 描述 |
|:-----|:----|
| documentId | 文件識別碼。 可輸入中所包含的識別碼。 |
| topicId | 文件已指派給主題識別碼。 |
| 距離 | 0 和 1 之間的文件-主題聯盟分數。 較低的距離分數更為主題聯盟是。 |

**錯誤**

| 索引鍵 | 描述 |
|:-----|:----|
| 識別碼 | 輸入文件唯一識別碼錯誤是指。 |
| 訊息 | 錯誤訊息。 |

## <a name="next-steps"></a>後續步驟 ##

恭喜您 ！ 您現在已經完成使用文字分析資料。 您現在可能會想要查詢以視覺化方式呈現資料、 使用的工具，例如[Power BI](//powerbi.microsoft.com) ，以及自動化，讓您將文字資料的即時檢視您的觀點。

若要查看如何使用文字分析功能，例如舉動，做為傀儡的一部分，請參閱傀儡架構網站上的[情感傀儡](http://docs.botframework.com/en-us/bot-intelligence/language/#example-emotional-bot)範例。
