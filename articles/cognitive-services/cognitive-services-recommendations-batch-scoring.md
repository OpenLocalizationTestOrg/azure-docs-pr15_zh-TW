
<properties
    pageTitle="取得以批次的建議︰ 電腦學習建議 API |Microsoft Azure"
    description="Azure 電腦學習建議-取得建議批次"
    services="cognitive-services"
    documentationCenter=""
    authors="luiscabrer"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cognitive-services"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/17/2016"
    ms.author="luisca"/>

# <a name="get-recommendations-in-batches"></a>取得以批次的建議

>[AZURE.NOTE] 批次中快速建議是更為複雜比快速建議一次。 檢查的 Api，如需如何取得建議單一要求的資訊︰

> [項目至項目建議](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3d4)<br>
> [使用者至項目建議](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3dd)
>
> 批次計分只適用於建置 2016 年 7 月 21 之後, 所建立。


有的情況也需要推薦一次一個以上的項目。 例如，您可能有興趣建立建議快取，或甚至分析您所收到的建議的類型。

批次計分作業，我們撥打電話給，都是非同步作業。 您需要提交要求，等待作業完成，然後收集您的結果。  

若要更精確，以下是要遵循的步驟︰

1.  如果您沒有，請建立一個 Azure 儲存體容器。
2.  描述每個 Azure Blob 儲存體您建議要求輸入的檔案上傳。
3.  著手進行評分批次工作。
4.  等待非同步以完成作業。
5.  作業完成後，請從 Blob 儲存體中收集的結果。

現在就讓我們逐步執行這些步驟。

## <a name="create-a-storage-container-if-you-dont-have-one-already"></a>如果您沒有，建立存放容器

移至[Azure 入口網站](https://portal.azure.com)，如果您沒有建立新的儲存空間帳戶。 若要這麼做，請瀏覽至**新增** > **資料** + **儲存** > **儲存的帳戶**。

您有儲存帳戶後，您必須建立位置來儲存的輸入與輸出批次執行 blob 容器。

上傳說明輸入檔案現在就讓我們每個您建議要求 Blob 儲存體-以下呼叫檔案 input.json。
有容器之後，您需要上傳檔案的描述每個您需要執行建議服務要求。

以批次可以從特定建置執行要求只有一個類型。 我們會說明如何在下一節中定義這項資訊。 現在，假設，我們所執行的項目建議出特定的建立。 輸入的檔案然後包含每個要求輸入的資訊 （在此例中種子項目）。

這是範例 input.json 檔案的外觀︰

    {
      "requests": [
      { "SeedItems": [ "C9F-00163", "FKF-00689" ] },
      { "SeedItems": [ "F34-03453" ] },
      { "SeedItems": [ "D16-3244" ] },
      { "SeedItems": [ "C9F-00163", "FKF-00689" ] },
      { "SeedItems": [ "F43-01467" ] },
      { "SeedItems": [ "BD5-06013" ] },
      { "SeedItems": [ "P45-00163", "FKF-00689" ] },
      { "SeedItems": [ "C9A-69320" ] }
      ]
    }

如您所見，檔案就會是 JSON 檔案，每一個要求位置已有必要，傳送建議要求的資訊。 建立類似 JSON 檔案的要求，您需要執行，並將其複製到您剛剛建立 Blob 儲存體中的容器。

## <a name="kick-start-the-batch-job"></a>著手進行批次工作

下一步是提交新批次工作。 如需詳細資訊，請檢查[API 參考](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/)。

API 邀請本文必須定義的位置輸入、 輸出，以及錯誤檔案需要儲存的位置。 它也需要定義存取這些位置所需的認證。 此外，您必須指定一些參數適用於整個批次 （要求時，模型/建立，使用，通話，每個結果的數字的建議的類型）。

範例要求主體應該看起來如下︰

    {
      "input": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/batchInput.json",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "output": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/batchOutput.json ",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "error": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/errors.txt",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "job": {
        "apiName": "ItemRecommend",
        "modelId": "9ac12a0a-1add-4bdc-bf42-c6517942b3a6",
        "buildId": 1015703,
        "numberOfResults": 10,
        "includeMetadata": true,
        "minimalScore": 0.0
      }
    }

以下要注意的一些重要事項︰

-   目前，應該一律設定**識別碼**，為**PublicOrSas**。

-   您需要取得共用 Access 簽章 (SA) 權杖允許建議 API 讀取和寫入從/Blob 儲存體帳戶。 在[[建議 API 頁面](../storage/storage-dotnet-shared-access-signature-part-1.md)上找如何產生 SA 權杖的詳細資訊。

-   目前支援只**apiName**是**ItemRecommend**，用於項目至項目建議。 批次處理目前不支援的建議使用者至項目。

## <a name="wait-for-the-asynchronous-operation-to-finish"></a>非同步作業，完成時，請等候

當您啟動批次作業時，回應傳回作業位置頁首，讓您追蹤作業所需的資訊。
您可以使用[擷取作業狀態 API]( https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3da)，就像您執行的追蹤建置作業的操作，以追蹤作業。

## <a name="get-the-results"></a>獲得的結果

完成作業，假設沒有錯誤之後，您可以從您的輸出收集結果 Blob 儲存體。

下列範例顯示輸出的外觀。 在此範例中，我們會示範只有兩個要求 （適用於贅述） 以批次的結果。

    {
      "results":
      [   
        {
          "request": { "seedItems": [ "DAF-00500", "P3T-00003" ] },
          "recommendations": [
            {
              "items": [
                {
                  "itemId": "F5U-00011",
                  "name": "L2 Ethernet Adapter-Win8Pro SC EN/XD/XX Hdwr",
                  "metadata": ""
                }
              ],
              "rating": 0.722,
              "reasoning": [ "People who like the selected items also like 'L2 Ethernet Adapter-Win8Pro SC EN/XD/XX Hdwr'" ]
            },
            {
              "items": [
                {
                  "itemId": "G5Y-00001",
                  "name": "Docking Station for Srf Pro/Pro2 SC EN/XD/ES Hdwr",
                  "metadata": ""
                }
              ],
              "rating": 0.718,
              "reasoning": [ "People who like the selected items also like 'Docking Station for Srf Pro/Pro2 SC EN/XD/ES Hdwr'" ]
            }
          ]
        },
        {
          "request": { "seedItems": [ "C9F-00163" ] },
          "recommendations": [
            {
              "items": [
                {
                  "itemId": "C9F-00172",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Green",
                  "metadata": ""
                }
              ],
              "rating": 0.649,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Green'" ]
            },
            {
              "items": [
                {
                  "itemId": "C9F-00171",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Orange",
                  "metadata": ""
                }
              ],
              "rating": 0.647,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Orange'" ]
            },
            {
              "items": [
                {
                  "itemId": "C9F-00170",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Yellow",
                  "metadata": ""
                }
              ],
              "rating": 0.646,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Yellow'" ]
            }       
          ]
        }
    ]}


## <a name="learn-about-the-limitations"></a>深入了解限制

-   只有一個批次工作呼叫每個訂閱，一次。
-   批次工作輸入的檔案不能超過 2 MB。
