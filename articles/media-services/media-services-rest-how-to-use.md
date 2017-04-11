<properties 
    pageTitle="媒體服務 REST API 概觀 |Microsoft Azure" 
    description="媒體服務 REST API 概觀" 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="10/12/2016"
    ms.author="juliako"/>


# <a name="media-services-rest-api-overview"></a>媒體服務 REST API 概觀 

[AZURE.INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

Microsoft Azure 媒體服務是接受 OData 型 HTTP 要求的服務，並可以回應回詳細 JSON 或 atom + 發行。 媒體服務符合 Azure 設計的指導方針，因為沒有連線至媒體服務時，必須使用每個用戶端的必要 HTTP 標頭的一組，以及一組可用的選用標題。 下列各節說明標題，您可以使用時機的 HTTP 動詞建立要求和從媒體服務接收回覆。

##<a name="considerations"></a>考量 

使用其餘時，就會套用下列事項。

- 當查詢項目，就會傳回一次，因為公用其餘 v2 限制 1000年結果的查詢結果的實體 1000年的限制。 您需要使用**[略過**並**採取**(.NET) /**頂端**（剩餘） 為所述[本.NET 範例](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities)，[此 REST API 範例](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities)。 

- 當使用 JSON 並指定要在邀請中使用**__metadata**關鍵字 （例如，若要參照連結的物件） 必須[JSON 詳細資訊](http://www.odata.org/documentation/odata-version-3-0/json-verbose-format/)的格式設定**接受**標頭 （請參閱下列範例）。 Odata 將不瞭解**__metadata**屬性，在邀請中，除非您將它設定為詳細資訊。  

        POST https://media.windows.net/API/Jobs HTTP/1.1
        Content-Type: application/json;odata=verbose
        Accept: application/json;odata=verbose
        DataServiceVersion: 3.0
        MaxDataServiceVersion: 3.0
        x-ms-version: 2.11
        Authorization: Bearer <token> 
        Host: media.windows.net
        
        {
            "Name" : "NewTestJob", 
            "InputMediaAssets" : 
                [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aba5356eb-30ff-4dc6-9e5a-41e4223540e7')"}}]
        . . . 
        

## <a name="standard-http-request-headers-supported-by-media-services"></a>支援的媒體服務的標準 HTTP 要求標頭

每個呼叫您納入媒體服務，沒有必要的標題，您必須在邀請中包含的一組而也選用的標頭的一組您可能會想要包含。 下表列出必要的標題︰


頁首|類型|值
---|---|---
授權|承載者|承載者是只接受的授權機制。 值也必須包含 ACS 所提供的存取權杖。
x ms 版本|小數位數|2.11
DataServiceVersion|小數位數|3.0
MaxDataServiceVersion|小數位數|3.0



>[AZURE.NOTE] 媒體服務使用 OData 透過 REST Api 其基礎資產的中繼資料存放庫的方式公開，因為 DataServiceVersion 和 MaxDataServiceVersion 標題應包含在任何要求。不過，如果不是，然後目前媒體服務假設 DataServiceVersion 在使用的是 3.0。

下列是選擇性的標頭的一組︰

頁首|類型|值
---|---|---
日期|RFC 1123 日期|要求時間戳記
接受|內容類型|要求的內容類型的回應，如下所示︰<p> -應用程式/json; odata = 詳細資訊<p> -應用程式/atom + xml<p> 回應可能會有不同的內容類型，例如 blob 擷取時，成功回應包含 blob 串流為內容的位置。
接受編碼|Gzip 上, 平凹|GZIP 和結實時適用編碼。 附註︰ 大型的資源，媒體服務可能會忽略此標題並傳回未壓縮的資料。
接受語言|「 en"、"es 結尾，等等。|指定慣用的語言的回應。
接受字元集|字元集類型，例如 「 utf-8 」|預設值為 utf-8。
X HTTP 方法|HTTP 方法|允許用戶端或不支援 HTTP 方法，例如放入或刪除，使用這些方法，透過取得通話通道的防火牆。
內容類型|內容類型|要求要求主體放入或文章中的內容類型。
用戶端要求識別碼|字串|來電者定義的值，識別指定的要求。 如果指定，將回應訊息中包含此值，以要求對應。 <p><p>**重要**<p>值應該處於 2096b (2 k)。

## <a name="standard-http-response-headers-supported-by-media-services"></a>支援的媒體服務的標準 HTTP 回應標頭

以下是可能會傳回給您，視您要求的資源和您想要執行的動作而定的標頭的一組。


頁首|類型|值
---|---|---
要求識別碼|字串|在目前的作業，產生的服務的唯一識別碼。
用戶端要求識別碼|字串|如果有指定在原始邀請中，來電者識別碼。
日期|RFC 1123 日期|要求已處理的日期。
內容類型|變化|回應本文內容類型。
內容編碼|變化|Gzip 或上平凹、 視。


## <a name="standard-http-verbs-supported-by-media-services"></a>支援的媒體服務的標準 HTTP 動作

以下是進行 HTTP 要求時，可以使用的 HTTP 動作的完整清單︰


動詞|描述
---|---
取得|傳回目前值的物件。
文章|建立基礎資料提供的物件，或提交命令。
保留|取代物件，或建立具名的物件 （如果適用的話）。
刪除|刪除的物件。
合併列印|更新現有的物件的命名的屬性變更。
標頭|傳回物件以取得回應的中繼的資料。

##<a name="limitation"></a>限制

當查詢項目，就會傳回一次，因為公用其餘 v2 限制 1000年結果的查詢結果的實體 1000年的限制。 您需要使用**[略過**並**採取**(.NET) /**頂端**（剩餘） 為所述[本.NET 範例](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities)，[此 REST API 範例](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities)。 


## <a name="discovering-media-services-model"></a>探索媒體服務模型

若要讓媒體服務的實體更容易找到，可以使用 $metadata 作業。 其可讓您擷取所有有效的實體類型、 實體屬性、 關聯、 函數、 動作和等等。 下列範例會示範如何建構 URI: https://media.windows.net/API/$ 中繼資料。

您應該新增 「？ api version=2.x 」 URI，如果您想要檢視的中繼資料瀏覽器中，或在邀請中，不包含 x ms 版本標頭的結尾。



##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]





 
