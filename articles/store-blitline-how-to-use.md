<properties 
    pageTitle="如何使用 Blitline 圖像的處理-Azure 功能指南" 
    description="瞭解如何使用 Blitline 服務，Azure 應用程式中的程序圖像。" 
    services="" 
    documentationCenter=".net" 
    authors="blitline-dev" 
    manager="jason@blitline.com" 
    editor="jason@blitline.com"/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/09/2014" 
    ms.author="support@blitline.com"/>
# <a name="how-to-use-blitline-with-azure-and-azure-storage"></a>如何使用 Blitline Azure 與 Azure 儲存體

本指南將說明如何存取 Blitline 服務，以及如何將工作提交給 Blitline。

## <a name="what-is-blitline"></a>什麼是 Blitline？

Blitline 是價格的以雲端為基礎的圖像，處理服務，提供在成本它來建立自己的分數正在處理的企業層級的影像。

是的圖像處理完成後，一再通常重建為每個網站的基礎。 我們瞭解此因為我們建立了這些萬的時間太。 一天，我們決定，也許是我們只執行的每個人。 我們會知道怎麼做，快速並有效率地，請執行並同時儲存所有工作的人。

如需詳細資訊，請參閱[http://www.blitline.com](http://www.blitline.com)。

## <a name="what-blitline-is-not"></a>Blitline 功能不...

為闡明 Blitline 功能很適合用於，通常很容易辨識 Blitline 「 不在做什麼之前向前移動。

- Blitline 沒有 HTML 小工具上傳圖像。 公開或限制的權限可達到 Blitline，您必須具有圖像。

- Blitline 不會處理，例如 Aviary.com 即時影像

- Blitline 不接受圖像上傳，您無法將圖像直接推入 Blitline。 您必須傳送至 Azure 儲存體或 Blitline 支援的其他位置，然後判斷 Blitline 何處可取得。

- Blitline 平行人連線並不會執行任何同步處理。 這表示您必須提供意見 postback_url，我們會告訴您完成我們處理。

## <a name="create-a-blitline-account"></a>建立 Blitline 帳戶

[AZURE.INCLUDE [blitline-signup](../includes/blitline-signup.md)]

## <a name="how-to-create-a-blitline-job"></a>如何建立 Blitline 工作

Blitline 使用 JSON 定義您想要在圖像上進行的動作。 此 JSON 被由幾個簡單的欄位。

最簡單的範例如下所示︰

        json : '{
       "application_id": "MY_APP_ID",
       "src" : "http://cdn.blitline.com/filters/boys.jpeg",
       "functions" : [ {
           "name": "resize_to_fit",
           "params" : { "width": 240, "height": 140 },
           "save" : { "image_identifier" : "external_sample_1" }
       } ]
    }'

以下我們有 JSON，帶的 「 src 「 圖像 」...boys.jpeg 」 然後調整為 240 x 140 圖像的大小。

應用程式識別碼是您可以找到您在 Azure 上的 [**連線資訊**] 或 [**管理**] 索引標籤。 是您私人的識別碼，可讓您在 Blitline 上執行作業。

[儲存] 參數識別您要放置圖像，我們已處理該後的相關資訊。 在這個簡單的情況下，我們尚未定義一個。 如果沒有位置定義 Blitline 會將其儲存至本機 （和暫時） 唯一的雲端位置。 您可以從傳回 Blitline 當您進行 Blitline JSON 取得該位置。 「 圖像 」 該識別碼需要，若要找出這個特定儲存圖像時，會傳回給您。

您可以找到我們支援下列*函數*的相關詳細資訊︰ <http://www.blitline.com/docs/functions>

您也可以找到工作選項的相關文件︰ <http://www.blitline.com/docs/api>

一旦您 JSON 您只需要是**文章**將`http://api.blitline.com/job`

您會收到 JSON 回看起來像這樣︰

    {
     "results":
         {"images":
            [{
              "image_identifier":"external_sample_1",
              "s3_url":"https://s3.amazonaws.com/dev.blitline/2011110722/YOUR_APP_ID/CK3f0xBF_2bV6wf7gEZE8w.jpg"
            }],
          "job_id":"4eb8c9f72a50ee2a9900002f"
         }
    }


這會告訴您 Blitline 有收到您的要求，其具有將其放在處理佇列中，，完成具有圖像可在︰ **https://s3.amazonaws.com/dev.blitline/2011110722/YOUR\_應用程式\_ID/CK3f0xBF_2bV6wf7gEZE8w.jpg**

## <a name="how-to-save-an-image-to-your-azure-storage-account"></a>如何將圖像儲存到您的 Azure 儲存體帳戶

如果您有 Azure 儲存體帳戶時，您可以輕鬆地讓 Blitline 推處理的圖像至您 Azure 容器。 新增 「 azure_destination 「 您定義的位置和發送到 Blitline 的權限。

以下是範例︰

    job : '{
      "application_id": "YOUR_APP_ID",
      "src" : "http://www.google.com/logos/2011/houdini11-hp.jpg",
         "functions" : [{
         "name": "blur",
         "save" : {
             "image_identifier" : "YOUR_IMAGE_IDENTIFIER",
             "azure_destination" : {
                 "account_name" : "YOUR_AZURE_CONTAINER_NAME",
                 "shared_access_signature" : "SAS_THAT_GIVES_BLITLINE_PERMISSION_TO_WRITE_THIS_OBJECT_TO_CONTAINER",
               }
           }
         }]
       }'


填入 CAPITALIZED 值，使用您自己，您可以送出 http://api.blitline.com/job 此 JSON 及會處理使用模糊篩選，然後放到您 Azure 目的地 」 src 」 圖像。

###<a name="please-note"></a>請注意︰

SA 必須包含整個 SA url，包括目的檔案的檔案名稱。

範例︰

    http://blitline.blob.core.windows.net/sample/image.jpg?sr=b&sv=2012-02-12&st=2013-04-12T03%3A18%3A30Z&se=2013-04-12T04%3A18%3A30Z&sp=w&sig=Bte2hkkbwTT2sqlkkKLop2asByrE0sIfeesOwj7jNA5o%3D


您也可以讀取 Blitline 的 Azure 儲存體文件的最新版本[以下](http://www.blitline.com/docs/azure_storage)。


## <a name="next-steps"></a>後續步驟

請造訪 blitline.com 若要進一步瞭解我們其他所有功能︰

* Blitline API 端點文件<http://www.blitline.com/docs/api>
* Blitline API 函數<http://www.blitline.com/docs/functions>
* Blitline API 範例<http://www.blitline.com/docs/examples>
* 第三部分 Nuget 文件庫<http://nuget.org/packages/Blitline.Net>
