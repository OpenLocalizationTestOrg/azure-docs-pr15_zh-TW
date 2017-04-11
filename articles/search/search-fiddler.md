<properties
    pageTitle="如何使用 Fiddler 評估，和測試 Azure 搜尋 REST Api |Microsoft Azure |裝載的雲端搜尋服務"
    description="使用 Fiddler 驗證 Azure 搜尋可用性和試用 REST Api 無程式碼的方法。"
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="mblythe"
    editor=""/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="10/17/2016"
    ms.author="heidist"/>

# <a name="use-fiddler-to-evaluate-and-test-azure-search-rest-apis"></a>使用 Fiddler 評估，和測試 Azure 搜尋 REST Api
> [AZURE.SELECTOR]
- [概觀](search-query-overview.md)
- [搜尋檔案總管](search-explorer.md)
- [Fiddler](search-fiddler.md)
- [.NET](search-query-dotnet.md)
- [其餘](search-query-rest-api.md)

本文說明如何使用 Fiddler，[從 Telerik 免費下載](http://www.telerik.com/fiddler)，問題 HTTP 要求及檢視回應使用 Azure 搜尋 REST API，而不必撰寫程式碼。 Azure 搜尋是完全管理，裝載雲端搜尋服務上透過.NET 和 REST Api 輕鬆可程式化的 Microsoft Azure。 Azure 搜尋服務 REST Api 說明[MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx)上。

下列步驟，將建立索引上, 傳文件、 查詢索引，然後查詢 [系統服務資訊。

若要完成這些步驟，您需要 Azure 搜尋服務及`api-key`。 如需如何開始的指示，請參閱[建立 Azure 搜尋服務在入口網站](search-create-service-portal.md)。

## <a name="create-an-index"></a>建立索引

1. 啟動 Fiddler。 在 [**檔案**] 功能表上，關閉**擷取流量**若要隱藏目前的工作無關的多餘 HTTP 活動。

3. [**編輯器**] 索引標籤中，您會制定看起來像以下的螢幕擷取畫面的要求。

    ![][1]

2. 選取 [**保留**]。

3. 輸入 URL，指定的服務 URL、 要求屬性和 api 版本。 要記住的一些指示︰
   + 您可以使用 HTTPS 作為前置字元。
   + 要求屬性是 「 / 索引/旅館 」。 這就是告訴建立名為 「 旅館' 索引的搜尋。
   + Api 版本是小寫，指定為 「？ api 版本 = 2015年-02 28 」。 API 版本很重要，因為 Azure 搜尋定期部署更新。 偶，服務的更新可能會造成重大變更，對 API。 因此，Azure 搜尋 」 需要每個要求 api 版本，讓您在 「 完全控制使用哪一種透過。

    完整的 URL 應該類似下面的範例中使用。

            https://my-app.search.windows.net/indexes/hotels?api-version=2015-02-28

4.  指定要求標頭，取代主機和 api 金鑰與您的服務的有效的值。

            User-Agent: Fiddler
            host: my-app.search.windows.net
            content-type: application/json
            api-key: 1111222233334444

5.  在邀請內文中貼上構成索引定義的欄位。
            
             {
            "name": "hotels",  
            "fields": [
              {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
              {"name": "baseRate", "type": "Edm.Double"},
              {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
              {"name": "hotelName", "type": "Edm.String"},
              {"name": "category", "type": "Edm.String"},
              {"name": "tags", "type": "Collection(Edm.String)"},
              {"name": "parkingIncluded", "type": "Edm.Boolean"},
              {"name": "smokingAllowed", "type": "Edm.Boolean"},
              {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
              {"name": "rating", "type": "Edm.Int32"},
              {"name": "location", "type": "Edm.GeographyPoint"}
             ]
            }

6.  按一下 [**執行**]。

幾秒數，您應該會看到 [工作階段] 清單中的 HTTP 201 回應表示已成功建立索引。

如果您收到 HTTP 504，請確認 URL 指定的 HTTPS。 如果您看到 HTTP 400 或 404，請檢查確認沒有複製貼上錯誤的要求主體。 HTTP 403 通常表示 api 金鑰 （金鑰無效或語法問題 api 金鑰指定的方式） 有問題。

## <a name="load-documents"></a>載入文件

在 [**編輯器**] 索引標籤上要求張貼文件看起來如下所示。 邀請內文包含 4 旅館搜尋資料。

   ![][2]

1. 選取 [**張貼**]。

2.  輸入的 URL，一開始就有 HTTPS，後面接著您服務的 URL，後面再加上 「 /indexes/ <'indexname ' >/文件/索引？ api 版本 = 2015年-02 28 」。 完整的 URL 應該類似下面的範例中使用。

            https://my-app.search.windows.net/indexes/hotels/docs/index?api-version=2015-02-28

3.  要求標頭應該與之前相同。 請記住以適用於您的服務的值取代主機和 api 金鑰。

            User-Agent: Fiddler
            host: my-app.search.windows.net
            content-type: application/json
            api-key: 1111222233334444

4.  邀請內文包含四個文件新增至旅館索引。

            {
            "value": [
            {
                "@search.action": "upload",
                "hotelId": "1",
                "baseRate": 199.0,
                "description": "Best hotel in town",
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
                "@search.action": "upload",
                "hotelId": "3",
                "baseRate": 279.99,
                "description": "Surprisingly expensive",
                "hotelName": "Dew Drop Inn",
                "category": "Bed and Breakfast",
                "tags": ["charming", "quaint"],
                "parkingIncluded": true,
                "smokingAllowed": false,
                "lastRenovationDate": null,
                "rating": 4,
                "location": { "type": "Point", "coordinates": [-122.33207, 47.60621] }
              },
              {
                "@search.action": "upload",
                "hotelId": "4",
                "baseRate": 220.00,
                "description": "This could be the one",
                "hotelName": "A Hotel for Everyone",
                "category": "Basic hotel",
                "tags": ["pool", "wifi"],
                "parkingIncluded": true,
                "smokingAllowed": false,
                "lastRenovationDate": null,
                "rating": 4,
                "location": { "type": "Point", "coordinates": [-122.12151, 47.67399] }
              }
             ]
            }

8.  按一下 [**執行**]。

幾秒數，您應該會看到 [工作階段] 清單中的 HTTP 200 回應。 這表示已成功建立文件。 如果您收到 207，至少一個文件無法上傳。 如果您收到 404，您會有 [頁首] 或 [邀請內文中的語法錯誤。

## <a name="query-the-index"></a>查詢索引

載入索引和文件時，您可以發出對其查詢。  在 [**編輯器**] 索引標籤上的查詢您的服務**取得**命令看起來類似以下的螢幕擷取畫面。

   ![][3]

1.  選取 [**開始**]。

2.  輸入的 URL，一開始就有 HTTPS，後面接著您服務的 URL，後面再加上 「 /indexes/ <'indexname ' > / 文件嗎？ 」，後面接著查詢參數。 透過範例中，使用下列的 URL，範例主機名稱取代為有效的服務。
    
            https://my-app.search.windows.net/indexes/hotels/docs?search=motel&facet=category&facet=rating,values:1|2|3|4|5&api-version=2015-02-28

    此查詢搜尋字詞 「 motel 」，並擷取的評等的多面向類別。

3.  要求標頭應該與之前相同。 請記住以適用於您的服務的值取代主機和 api 金鑰。

            User-Agent: Fiddler
            host: my-app.search.windows.net
            content-type: application/json
            api-key: 1111222233334444

回應碼應為 200，回應輸出應該類似以下的螢幕擷取畫面。

   ![][4]

下列範例查詢是從 MSDN 上的[搜尋索引作業 (Azure 搜尋 API)](http://msdn.microsoft.com/library/dn798927.aspx) 。 本主題中的範例查詢許多包含不允許 Fiddler 中的空格。 每個空間與 + 字元之前先 Fiddler 中的查詢字串的查詢中的貼上取代。

**之前的空格會取代︰**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2015-02-28

**空格會取代之後 +:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2015-02-28

## <a name="query-the-system"></a>查詢系統

您也可以查詢可用於取得文件計數及儲存消耗系統。 [**編輯器**] 索引標籤中，您的要求看起來類似下列，並且回覆會傳回的文件和使用空間數的數。

 ![][5]

1.  選取 [**開始**]。

2.  輸入 URL，其中包含您服務的 URL，後面再加上 「 / 統計資料索引/旅館？ api 版本 = 2015年-02 28 」:

            https://my-app.search.windows.net/indexes/hotels/stats?api-version=2015-02-28

3.  指定要求標頭，取代主機和 api 金鑰與您的服務的有效的值。

            User-Agent: Fiddler
            host: my-app.search.windows.net
            content-type: application/json
            api-key: 1111222233334444

4.  將 [要求主體保留空白。

5.  按一下 [**執行**]。 您應該會看到 [工作階段] 清單中的 HTTP 200 狀態碼。 選取項目張貼您的命令。

6.  按一下 [**檢查**] 索引標籤，按一下 [**標題**] 索引標籤，然後選取 JSON 格式。 您應該會看到的文件計數和儲存空間大小 （KB)。

## <a name="next-steps"></a>後續步驟

請參閱[管理您的搜尋服務上 Azure](search-manage.md)無程式碼的方式來管理並使用 Azure 搜尋。


<!--Image References-->
[1]: ./media/search-fiddler/AzureSearch_Fiddler1_PutIndex.png
[2]: ./media/search-fiddler/AzureSearch_Fiddler2_PostDocs.png
[3]: ./media/search-fiddler/AzureSearch_Fiddler3_Query.png
[4]: ./media/search-fiddler/AzureSearch_Fiddler4_QueryResults.png
[5]: ./media/search-fiddler/AzureSearch_Fiddler5_QueryStats.png
