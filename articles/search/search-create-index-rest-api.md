<properties
    pageTitle="建立使用 REST API Azure 搜尋索引 |Microsoft Azure |裝載的雲端搜尋服務"
    description="在程式碼中使用 Azure 搜尋 HTTP REST API 建立索引。"
    services="search"
    documentationCenter=""
    authors="ashmaka"
    manager="jhubbard"
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="create-an-azure-search-index-using-the-rest-api"></a>建立使用 REST API Azure 搜尋索引
> [AZURE.SELECTOR]
- [概觀](search-what-is-an-index.md)
- [入口網站](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [其餘](search-create-index-rest-api.md)


本文會引導您完成建立 Azure 搜尋[索引](https://msdn.microsoft.com/library/azure/dn798941.aspx)使用 Azure 搜尋 REST API 的程序。

追蹤本指南和之前建立索引，您應該已經[建立 Azure 搜尋服務](search-create-service-portal.md)。

若要建立使用 REST API Azure 搜尋索引，您會發出單一 HTTP 文章要求 Azure 搜尋服務的 URL 端點。 您索引定義會包含在邀請內文中，做為語 JSON 內容。


## <a name="i-identify-your-azure-search-services-admin-api-key"></a>我。 找出您的 Azure 搜尋服務管理員 api 金鑰
現在，您有 Azure 搜尋服務佈建後，您可以發行針對您的服務 URL 端點使用 REST API HTTP 要求。 然而，*所有*API 邀請必須包含為您佈建搜尋服務所產生的 api 金鑰。 有有效的金鑰會信任，每個要求每之間傳送邀請和處理該服務的應用程式。

1. 若要尋找您的服務 api 金鑰中，您必須登入[Azure 入口網站](https://portal.azure.com/)
2. 移至您的 Azure 搜尋服務刀
3. 按一下 「 鍵 」 圖示

您的服務會有*管理員索引鍵*和*查詢鍵*。

 - 主要和次要*管理員鍵*授與所有作業，包括管理服務、 建立及刪除索引、 索引子和資料來源的功能的完整權限。 有兩個機碼，讓您可以繼續使用的第二個機碼，如果您決定要重新產生主索引鍵，反之亦然。
 - *查詢鍵*索引和文件的唯讀存取權授與，通常是分散發出搜尋要求的用戶端應用程式。

為了建立索引，您可以使用您的主要或次要管理員金鑰。

## <a name="ii-define-your-azure-search-index-using-well-formed-json"></a>II。 定義您使用語的 JSON 的 Azure 搜尋索引
單一 HTTP 文章要求您的服務將會建立索引。 將 HTTP 文章邀請內文包含單一 JSON 物件，以定義您的 Azure 搜尋索引。

1. 此 JSON 物件的第一個屬性是您索引的名稱。
2. 此 JSON 物件的第二個屬性是名為 JSON 陣列`fields`含有不同的 JSON 物件的索引中的每一個欄位。 每個 JSON 物件包含多個名稱/值組的每個欄位的欄位屬性包括 「 名稱 」、 「 類型 」 等。

請務必設計您的索引，為每個欄位必須被指派[適當的屬性](https://msdn.microsoft.com/library/azure/dn798941.aspx)時您搜尋的使用者體驗與企業需求請謹記。 這些屬性來控制的搜尋功能 （篩選、 faceting，排序全文檢索等） 套用到哪一個欄位。 您不指定任何屬性，預設會啟用對應的 [搜尋] 功能，除非您特別停用。

我們的範例中，我們已命名索引 」 旅館 」，並定義欄位，如下所示︰

```JSON
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ]
}
```

我們謹慎選擇根據我們如何認為，將會使用應用程式中每個欄位的索引屬性。 例如，`hotelId`搜尋旅館可能不會知道，讓我們來設定停用該欄位的全文檢索搜尋該人員是唯一的索引鍵`searchable`至`false`，以節省空間索引中。

請注意類型的索引中的一個欄位`Edm.String`必須指定為 'key' 欄位。

上述索引定義使用的自訂語言分析器`description_fr`欄位，因為它用來儲存法文的文字。 請參閱[支援主題 MSDN 上的語言](https://msdn.microsoft.com/library/azure/dn879793.aspx)，以及相對應[的部落格文章](https://azure.microsoft.com/blog/language-support-in-azure-search/)的相關語言分析器的詳細資訊。

## <a name="iii-issue-the-http-request"></a>III。 HTTP 要求
1. 使用您的索引定義邀請本文，為您 Azure 搜尋服務端點 URL 發出 HTTP 文章要求。 在 [URL]，請務必將主機名稱，為使用您的服務名稱，然後將適當`api-version`做為查詢字串參數 (目前的 API 使用的是`2015-02-28`發佈這份文件時)。
2. 在 [要求標頭中，指定`Content-Type`為`application/json`。 您也必須提供您的服務管理員金鑰中步驟 I 中識別出`api-key`頁首。


您就必須提供下列要求您的服務名稱] 和 [api 金鑰︰


    POST https://[service name].search.windows.net/indexes?api-version=2015-02-28
    Content-Type: application/json
    api-key: [api-key]


成功的要求，您應該會看到狀態碼 201 （建立）。 如需有關如何建立索引透過 REST API 的詳細資訊，請造訪[MSDN](https://msdn.microsoft.com/library/azure/dn798941.aspx)上的 API 參考。 如需其他可能失敗時傳回的 HTTP 狀態代碼的詳細資訊，請參閱[HTTP 狀態碼 （Azure 搜尋）](https://msdn.microsoft.com/library/azure/dn798925.aspx)。

當您完成索引，並想要將其刪除時，只要發出 HTTP 刪除要求。 例如，這是我們想要如何刪除 「 旅館 」 索引︰

    DELETE https://[service name].search.windows.net/indexes/hotels?api-version=2015-02-28
    api-key: [api-key]


## <a name="next"></a>下一步
建立之後 Azure 搜尋索引，您就可以開始上傳[到索引內容](search-what-is-data-import.md)之後，您可以開始搜尋您的資料。
