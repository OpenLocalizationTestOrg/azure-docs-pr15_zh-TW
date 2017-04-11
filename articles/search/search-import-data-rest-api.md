<properties
    pageTitle="在 [使用 REST API Azure 搜尋中的資料上傳 |Microsoft Azure |裝載的雲端搜尋服務"
    description="瞭解如何上傳至 Azure 搜尋 REST API 的使用中索引的資料。"
    services="search"
    documentationCenter=""
    authors="ashmaka"
    manager="jhubbard"
    editor=""
    tags=""/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="upload-data-to-azure-search-using-the-rest-api"></a>上傳至 Azure 搜尋 REST API 的資料
> [AZURE.SELECTOR]
- [概觀](search-what-is-data-import.md)
- [.NET](search-import-data-dotnet.md)
- [其餘](search-import-data-rest-api.md)

本文說明如何使用[Azure 搜尋 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx)來匯入資料至 Azure 搜尋索引。

在開始之前此逐步解說，您應該已經[建立 Azure 搜尋索引](search-what-is-an-index.md)。

若要將您使用 REST API 的索引推文件，請您會發出 HTTP 文章要求至您的索引 URL 端點。 HTTP 要求主體本文是包含要新增、 修改或刪除的文件的 JSON 物件。

## <a name="i-identify-your-azure-search-services-admin-api-key"></a>我。 找出您的 Azure 搜尋服務管理員 api 金鑰
當發行 HTTP 要求，針對您的服務使用 REST API，*每個*API 邀請必須包含為您佈建搜尋服務所產生的 api 金鑰。 有有效的金鑰會信任，每個要求每之間傳送邀請和處理該服務的應用程式。

1. 若要尋找您的服務 api 金鑰中，您必須登入[Azure 入口網站](https://portal.azure.com/)
2. 移至您的 Azure 搜尋服務刀
3. 按一下 「 鍵 」 圖示

您的服務會有*管理員索引鍵*和*查詢鍵*。

  - 主要和次要*管理員鍵*授與所有作業，包括管理服務、 建立及刪除索引、 索引子和資料來源的功能的完整權限。 有兩個機碼，讓您可以繼續使用的第二個機碼，如果您決定要重新產生主索引鍵，反之亦然。
  - *查詢鍵*索引和文件的唯讀存取權授與，通常是分散發出搜尋要求的用戶端應用程式。

為了索引將資料匯入，您可以使用您的主要或次要管理員金鑰。

## <a name="ii-decide-which-indexing-action-to-use"></a>II。 決定要使用的編製索引的動作
使用時 REST API，您會使用 JSON 邀請內文發出 HTTP 文章要求 Azure 搜尋索引的端點 url。 在您 HTTP 邀請內文中的 JSON 物件會包含一個名為 「 值 」 包含代表您想要新增至您的索引、 更新或刪除文件的 JSON 物件 JSON 陣列。

「 值 」 陣列中的每個 JSON 物件代表要能編製索引的文件。 每個物件包含文件的索引鍵，並指定所要編製索引的動作 （上傳、 合併、 刪除等）。 根據哪一項動作] 下方選擇，僅特定欄位必須包含每份文件︰

@search.action | 描述 | 每份文件的必要欄位 | 備忘稿
--- | --- | --- | ---
`upload` | `upload`動作是類似 「 了 「 插入如果其為新增和更新/取代已經存在文件。 | 索引鍵，再加上任何其他您想要定義的欄位 | 未指定在邀請中的任何欄位時更新/取代現有的文件，必須設定為其欄位`null`。 這是欄位已經設定為非 null 值時，即使。
`merge` | 更新現有的文件，以指定的欄位。 如果文件索引中不存在，則會失敗合併列印。 | 索引鍵，再加上任何其他您想要定義的欄位 | 您指定合併列印中的任何欄位，將會取代現有的欄位，在文件中。 這包含類型的欄位`Collection(Edm.String)`。 例如，如果文件包含欄位`tags`值`["budget"]`和執行合併值`["economy", "pool"]`的`tags`，最後的數值`tags`] 欄位會`["economy", "pool"]`。 不會`["budget", "economy", "pool"]`。
`mergeOrUpload` | 此動作類似`merge`如果索引中已存在文件的指定索引鍵。 如果文件不存在，它的行為類似`upload`新的文件。 | 索引鍵，再加上任何其他您想要定義的欄位 | -
`delete` | 移除索引中指定的文件。 | 只有機碼 | 您可以指定以外的索引鍵欄位也會忽略任何欄位。 如果您想要移除文件中的個別功能變數時，使用`merge`改為並直接欄位明確設定為 null。

## <a name="iii-construct-your-http-request-and-request-body"></a>III。 建構 HTTP 要求，並要求本文
現在您索引的動作收集必要欄位的值，是準備好要將資料匯入的實際 HTTP 要求和 JSON 邀請本文建構。

#### <a name="request-and-request-headers"></a>要求和邀請標頭
在 [URL，您需要提供您的服務名稱、 索引名稱 （「 旅館 「 在此例），以及適當的 API 版本 (目前的 API 使用的是`2015-02-28`發佈這份文件時)。 您必須定義`Content-Type`和`api-key`要求標頭。 對於後者，使用您的服務管理員機碼。

    POST https://[search service].search.windows.net/indexes/hotels/docs/index?api-version=2015-02-28
    Content-Type: application/json
    api-key: [admin key]

#### <a name="request-body"></a>邀請內文

```JSON
{
    "value": [
        {
            "@search.action": "upload",
            "hotelId": "1",
            "baseRate": 199.0,
            "description": "Best hotel in town",
            "description_fr": "Meilleur hôtel en ville",
            "hotelName": "Fancy Stay",
            "category": "Luxury",
            "tags": ["pool", "view", "wifi", "concierge"],
            "parkingIncluded": false,
            "smokingAllowed": false,
            "lastRenovationDate": "2010-06-27T00:00:00Z",
            "rating": 5,
            "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
        },
        {
            "@search.action": "upload",
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "description_fr": "Hôtel le moins cher en ville",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags": ["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
        },
        {
            "@search.action": "mergeOrUpload",
            "hotelId": "3",
            "baseRate": 129.99,
            "description": "Close to town hall and the river"
        },
        {
            "@search.action": "delete",
            "hotelId": "6"
        }
    ]
}
```

在這個範例中，我們會使用`upload`， `mergeOrUpload`，及`delete`為我們的搜尋動作。

假設此範例 「 旅館 」 索引已填入文件的數字。 請注意如何我們不需要使用時，指定所有可能的文件欄位`mergeOrUpload`和我們只指定的文件索引鍵的方式 (`hotelId`) 時使用`delete`。

此外，請注意，您只能最多可以包含 1000年文件 （或 16 MB） 中的單一編製索引的要求。

## <a name="iv-understand-your-http-response-code"></a>IV。 瞭解程式碼，HTTP 回應
#### <a name="200"></a>200
提交的成功編製索引的要求之後，您會收到的狀態碼 HTTP 回應`200 OK`。 HTTP 回應 JSON 本文會如下所示︰

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": true,
            "errorMessage": null
        },
        ...
    ]
}
```

#### <a name="207"></a>207
狀態碼`207`會傳回至少一個項目不成功索引時。 HTTP 回應 JSON 本文會包含失敗的文件的相關資訊。

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": false,
            "errorMessage": "The search service is too busy to process this document. Please try again later."
        },
        ...
    ]
}
```

> [AZURE.NOTE] 這通常表示您的搜尋服務載入達到位置編製索引作業要求會傳回開始點`503`回應。 在此情況下，我們強烈建議您的功能的用戶端程式碼，並前等待。 這可讓系統一些時間來復原，增加未來的要求將成功的可能性。 快速重試您的要求時，並只會在保留這種情況。

#### <a name="429"></a>429
狀態碼`429`當您已超過您配額的每個索引的文件數時，會傳回。

#### <a name="503"></a>503
狀態碼`503`就會傳回若無在邀請中的項目已成功建立索引。 此錯誤表示系統重度負載下，這次，就無法處理您的要求。

> [AZURE.NOTE] 在此情況下，我們強烈建議您的功能的用戶端程式碼，並前等待。 這可讓系統一些時間來復原，增加未來的要求將成功的可能性。 快速重試您的要求時，並只會在保留這種情況。

如需有關文件動作和成功/錯誤回應的詳細資訊，請參閱[新增、 更新或刪除文件](https://msdn.microsoft.com/library/azure/dn798930.aspx)。 如需其他可能失敗時傳回的 HTTP 狀態代碼的詳細資訊，請參閱[HTTP 狀態碼 （Azure 搜尋）](https://msdn.microsoft.com/library/azure/dn798925.aspx)。

## <a name="next"></a>下一步
之後填入您的 Azure 搜尋索引，就可以準備開始發出查詢搜尋文件。 如需詳細資訊，請參閱[查詢您的 Azure 搜尋索引](search-query-overview.md)。
