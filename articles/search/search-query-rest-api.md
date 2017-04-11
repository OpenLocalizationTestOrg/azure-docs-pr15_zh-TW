<properties
    pageTitle="您使用 REST API 的 Azure 搜尋索引的查詢 |Microsoft Azure |裝載的雲端搜尋服務"
    description="建立 Azure 搜尋中的搜尋查詢，以及使用搜尋參數篩選和排序搜尋結果。"
    services="search"
    documentationCenter=""
    manager="jhubbard"
    authors="ashmaka"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="query-your-azure-search-index-using-the-rest-api"></a>您使用 REST API 的 Azure 搜尋索引的查詢
> [AZURE.SELECTOR]
- [概觀](search-query-overview.md)
- [入口網站](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [其餘](search-query-rest-api.md)

本文將說明如何使用[Azure 搜尋 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx)索引的查詢。

在開始之前此逐步解說，您應該已經有[建立 Azure 搜尋索引](search-what-is-an-index.md)並[填入它的資料](search-what-is-data-import.md)。

## <a name="i-identify-your-azure-search-services-query-api-key"></a>我。 識別 Azure 搜尋服務的查詢 api 金鑰
Azure 搜尋 REST API 針對每個搜尋作業的主要元件是為您佈建的服務所產生的*api 金鑰*。 有有效的金鑰會信任，每個要求每之間傳送邀請和處理該服務的應用程式。

1. 若要尋找您的服務 api 金鑰中，您必須登入[Azure 入口網站](https://portal.azure.com/)
2. 移至您的 Azure 搜尋服務刀
3. 按一下 「 鍵 」 圖示

您的服務會有*管理員索引鍵*和*查詢鍵*。

 - 主要和次要*管理員鍵*授與所有作業，包括管理服務、 建立及刪除索引、 索引子和資料來源的功能的完整權限。 有兩個機碼，讓您可以繼續使用的第二個機碼，如果您決定要重新產生主索引鍵，反之亦然。
 - *查詢鍵*索引和文件的唯讀存取權授與，通常是分散發出搜尋要求的用戶端應用程式。

為了查詢索引，您可以使用其中一個查詢索引鍵。 管理員索引鍵也可以用的查詢，但如下這更有效地[最低權限原則](https://en.wikipedia.org/wiki/Principle_of_least_privilege)，您應該在 [應用程式碼中使用查詢鍵。

## <a name="ii-formulate-your-query"></a>II。 規劃您的查詢
有兩種方式可[搜尋您使用 REST API 的索引](https://msdn.microsoft.com/library/azure/dn798927.aspx)。 其中一個方法是 HTTP 文章要求加以邀請內文中 JSON 物件中定義查詢參數。 另一個方式是發行 HTTP GET 要求您查詢參數的要求 url 的定義位置。 請注意文章有更多[寬鬆限制](https://msdn.microsoft.com/library/azure/dn798927.aspx)比取得查詢參數的大小。 因此，建議您使用的文章，除非您有特殊的情況下使用取得其中會更方便。

文章與取得您需要提供您的*服務名稱*、*索引名稱*及適當的*API 版本*(目前的 API 使用的是`2015-02-28`發佈這份文件的時間) 要求 URL 中。 取得，*查詢字串*URL 的結尾時就會提供的查詢參數的位置。 請參閱下方的 URL 格式︰

    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2015-02-28

文章的格式是相同，但只 api-版本中的查詢字串參數。



#### <a name="example-queries"></a>範例查詢

以下是一些範例查詢命名為 「 旅館 」 索引。 下列查詢會顯示在 [取得] 與 [文章的格式。

搜尋整個索引字詞 '預算 」，並僅傳回`hotelName`欄位︰

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=budget&$select=hotelName&api-version=2015-02-28

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
{
    "search": "budget",
    "select": "hotelName"
}
```

將篩選套用到索引以尋找旅館的成本大於 $150 每晚，並傳回`hotelId`和`description`:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2015-02-28

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
{
    "search": "*",
    "filter": "baseRate lt 150",
    "select": "hotelId,description"
}
```

搜尋整個索引，也就是由特定欄位的順序 (`lastRenovationDate`) 以遞減順序前兩, 結果，並只顯示`hotelName`和`lastRenovationDate`:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$top=2&$orderby=lastRenovationDate desc&$select=hotelName,lastRenovationDate&api-version=2015-02-28

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
{
    "search": "*",
    "orderby": "lastRenovationDate desc",
    "select": "hotelName,lastRenovationDate",
    "top": 2
}
```

## <a name="iii-submit-your-http-request"></a>III。 提交 HTTP 要求
現在您有條理查詢做為您的 HTTP 要求 URL （適用於取得） 或 （適用於文章） 本文的一部分，您可以定義您要求的標頭，並提交您的查詢。

#### <a name="request-and-request-headers"></a>要求和邀請標頭
您必須定義兩個要求標頭取得，或三文章︰
1. `api-key`頁首必須設定為您在步驟中找到我上述查詢索引鍵。 請注意，您也可以使用為的管理員鍵`api-key`標題，但建議您使用的是查詢機碼，如獨佔授與索引和文件的唯讀存取權。
2. `Accept`頁首必須設定為 [ `application/json`。
3. 文章、`Content-Type`標題也應該將為`application/json`。

如要搜尋使用 Azure 搜尋 REST API，使用簡單的查詢，可以搜尋字詞 「 motel 」 的 「 旅館 」 索引的 HTTP GET 要求，請參閱下文︰

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=motel&api-version=2015-02-28
Accept: application/json
api-key: [query key]
```

以下是範例相同的查詢，這次使用 HTTP 文章︰

```
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
Content-Type: application/json
Accept: application/json
api-key: [query key]

{
    "search": "motel"
}
```

成功查詢要求會導致狀態碼`200 OK`和搜尋結果會傳回 json 回應內容中。 以下是結果上述查詢外觀，例如，假設變異數的 「 旅館 」 索引填入[Azure 搜尋 REST API 的使用中的資料匯入](search-import-data-rest-api.md)（請注意 JSON 具有格式設定以利檢視） 中的範例資料。

```JSON
{
    "value": [
        {
            "@search.score": 0.59600675,
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "description_fr": "Hôtel le moins cher en ville",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags":["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": {
                "type": "Point",
                "coordinates": [-122.131577, 49.678581],
                "crs": {
                    "type":"name",
                    "properties": {
                        "name": "EPSG:4326"
                    }
                }
            }
        }
    ]
}
```

若要深入瞭解，請造訪[搜尋文件](https://msdn.microsoft.com/library/azure/dn798927.aspx)的 「 回應 」 一節。 如需其他可能失敗時傳回的 HTTP 狀態代碼的詳細資訊，請參閱[HTTP 狀態碼 （Azure 搜尋）](https://msdn.microsoft.com/library/azure/dn798925.aspx)。
