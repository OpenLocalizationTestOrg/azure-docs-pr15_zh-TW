<properties 
    pageTitle="如何建立媒體處理器 |Microsoft Azure" 
    description="瞭解如何建立媒體處理器元件編碼、 轉換格式、 加密或解密媒體內容的 Azure 媒體服務。" 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="juliako"/>


#<a name="how-to-get-a-media-processor-instance"></a>如何︰ 取得媒體處理器執行個體


> [AZURE.SELECTOR]
- [.NET](media-services-get-media-processor.md)
- [其餘](media-services-rest-get-media-processor.md)

##<a name="overview"></a>概觀

在媒體時的處理器，處理特定處理的工作，例如編碼，元件的媒體服務格式轉換加密或解密媒體內容。 當您建立任務編碼、 加密或轉換媒體內容的格式，通常會建立媒體處理器。

下表提供的名稱和描述的每一個可用的媒體處理器。

媒體處理器名稱|描述|詳細資訊
---|---|---
媒體 Encoder 標準|提供視編碼標準的功能。 |[概觀與 Azure Demand 媒體編碼器上的比較](media-services-encode-asset.md)
媒體 Encoder 進階版工作流程|可讓您執行編碼使用媒體 Encoder 進階版工作流程的工作。|[概觀與 Azure Demand 媒體編碼器上的比較](media-services-encode-asset.md)
Azure 媒體索引| 可讓您進行搜尋，媒體檔案和內容，以及產生關閉隱藏式輔助字幕追蹤] 和 [關鍵字。|[Azure 媒體索引](media-services-index-content.md)
Azure 媒體 Hyperlapse （預覽版本）|可讓您平滑出 「 碰撞 」 中使用視訊穩定視訊。 也可讓您充分設定內容到消耗美工圖案。|[Azure 媒體 Hyperlapse](media-services-hyperlapse-content.md)
Azure 媒體 Encoder|使用年限
儲存解密| 使用年限|
Azure 媒體封裝程式|使用年限|
Azure 媒體加密子|使用年限|

##<a name="get-mediaprocessor"></a>取得 MediaProcessor

>[AZURE.NOTE] 使用媒體服務 REST API 工作時，下列事項︰
>
>當存取媒體服務中的項目，您必須在 HTTP 邀請中設定特定的標題欄位和值。 如需詳細資訊，請參閱[媒體服務 REST API 開發的設定](media-services-rest-how-to-use.md)。

>成功連接之後要 https://media.windows.net，您會收到指定其他媒體服務 URI 301 重新導向。 您必須進行後續的來電至新的 URI[連線至媒體服務使用 REST API](media-services-rest-connect-programmatically.md)所述。 


下列其餘呼叫會顯示如何媒體處理器執行個體 （在此例中**Media Encoder 標準**） 的名稱。 



    
要求︰

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.11
    Host: media.windows.net
    
回應︰
        
    . . .
    
    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "Description":"Media Encoder Standard",
             "Name":"Media Encoder Standard",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }


##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="next-steps"></a>後續步驟

現在，您知道如何取得媒體處理器執行個體，請移至[如何編碼資產](media-services-rest-get-started.md)主題會顯示您如何使用 Media Encoder 標準編碼資產。
