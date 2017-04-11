<properties
    pageTitle="使用.NET SDK 您 Azure 搜尋索引的查詢 |Microsoft Azure |裝載的雲端搜尋服務"
    description="建立 Azure 搜尋中的搜尋查詢，以及使用搜尋參數篩選和排序搜尋結果。"
    services="search"
    manager="jhubbard"
    documentationCenter=""
    authors="brjohnstmsft"
/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="brjohnst"/>

# <a name="query-your-azure-search-index-using-the-net-sdk"></a>使用.NET SDK 您 Azure 搜尋索引的查詢
> [AZURE.SELECTOR]
- [概觀](search-query-overview.md)
- [入口網站](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [其餘](search-query-rest-api.md)

本文將說明如何使用[Azure 搜尋.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)索引的查詢。

在開始之前此逐步解說，您應該已經有[建立 Azure 搜尋索引](search-what-is-an-index.md)並[填入它的資料](search-what-is-data-import.md)。

請注意，在 C# 撰寫本文中的所有範例程式碼。 您可以尋找完整的來源[GitHub](http://aka.ms/search-dotnet-howto)程式碼。

## <a name="i-identify-your-azure-search-services-query-api-key"></a>我。 識別 Azure 搜尋服務的查詢 api 金鑰
現在您已經建立 Azure 搜尋索引，是幾乎準備好要發佈使用.NET SDK 的查詢。 首先，您需要取得查詢 api-鍵為您佈建搜尋服務所產生的其中一項。 .NET SDK 會在每次要求傳送此 api 金鑰您的服務。 有有效的金鑰會信任，每個要求每之間傳送邀請和處理該服務的應用程式。

1. 若要尋找您的服務 api 金鑰中，您必須登入[Azure 入口網站](https://portal.azure.com/)
2. 移至您的 Azure 搜尋服務刀
3. 按一下 「 鍵 」 圖示

您的服務會有*管理員索引鍵*和*查詢鍵*。

  - 主要和次要*管理員鍵*授與所有作業，包括管理服務、 建立及刪除索引、 索引子和資料來源的功能的完整權限。 有兩個機碼，讓您可以繼續使用的第二個機碼，如果您決定要重新產生主索引鍵，反之亦然。
  - *查詢鍵*索引和文件的唯讀存取權授與，通常是分散發出搜尋要求的用戶端應用程式。

為了查詢索引，您可以使用其中一個查詢索引鍵。 管理員索引鍵也可以用的查詢，但如下這更有效地[最低權限原則](https://en.wikipedia.org/wiki/Principle_of_least_privilege)，您應該在 [應用程式碼中使用查詢鍵。

## <a name="ii-create-an-instance-of-the-searchindexclient-class"></a>II。 建立 SearchIndexClient 類別的執行個體
若要使用 Azure 搜尋.NET SDK 發出的查詢，您需要建立的執行個體`SearchIndexClient`類別。 這個類別有多個建構函式。 您想要的項目會帶您搜尋服務名稱、 索引名稱和`SearchCredentials`做為參數的物件。 `SearchCredentials`換行您 api 金鑰。

下列程式碼會建立新`SearchIndexClient`（[建立使用.NET SDK Azure 搜尋索引](search-create-index-dotnet.md)中建立） 的 「 旅館 」 索引用值會儲存在應用程式的設定檔的 api 金鑰與搜尋服務名稱 (`app.config`或`web.config`):

```csharp
string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
string queryApiKey = ConfigurationManager.AppSettings["SearchServiceQueryApiKey"];

SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
```

`SearchIndexClient`具有`Documents`屬性。 這個屬性會提供您需要查詢 Azure 搜尋索引的所有方法。

## <a name="iii-query-your-index"></a>III。 查詢您的索引
使用.NET SDK 搜尋是非常簡單，電話`Documents.Search`方法在您`SearchIndexClient`。 此方法需要幾個參數，包括搜尋文字，並搭配`SearchParameters`可以用來，進一步縮小查詢的物件。

#### <a name="types-of-queries"></a>查詢類型
您會使用兩種主要[查詢類型](search-query-overview.md#types-of-queries)是`search`和`filter`。 A`search`查詢的一或多個字詞的搜尋索引中的所有_可搜尋_欄位中。 A`filter`查詢所有評估的布林運算式_可以篩選_索引中的欄位。

搜尋和篩選器會使用執行`Documents.Search`方法。 搜尋查詢可以傳入`searchText`參數時的篩選運算式中可傳遞`Filter`屬性`SearchParameters`類別。 若要篩選而不搜尋，只要將`"*"`的`searchText`參數。 若要搜尋不篩選，只保留`Filter`屬性取消，或不傳入`SearchParameters`完全執行個體。

#### <a name="example-queries"></a>範例查詢

下列範例會顯示一些不同的方式查詢定義[建立使用.NET SDK Azure 搜尋索引](search-create-index-dotnet.md#DefineIndex)中的 「 旅館 」 索引。 請注意，其中包含搜尋結果傳回的文件的執行個體`Hotel`在[使用.NET SDK Azure 搜尋中的資料匯入](search-import-data-dotnet.md#HotelClass)已定義的類別。 在 [程式碼範例使用`WriteDocuments`輸出主控台搜尋結果的方式。 這個方法是下一節所述。

```csharp
SearchParameters parameters;
DocumentSearchResult<Hotel> results;

Console.WriteLine("Search the entire index for the term 'budget' and return only the hotelName field:\n");

parameters =
    new SearchParameters()
    {
        Select = new[] { "hotelName" }
    };

results = indexClient.Documents.Search<Hotel>("budget", parameters);

WriteDocuments(results);

Console.Write("Apply a filter to the index to find hotels cheaper than $150 per night, ");
Console.WriteLine("and return the hotelId and description:\n");

parameters =
    new SearchParameters()
    {
        Filter = "baseRate lt 150",
        Select = new[] { "hotelId", "description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
Console.Write("in descending order, take the top two results, and show only hotelName and ");
Console.WriteLine("lastRenovationDate:\n");

parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "lastRenovationDate desc" },
        Select = new[] { "hotelName", "lastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'motel':\n");

parameters = new SearchParameters();
results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

## <a name="iv-handle-search-results"></a>IV。 處理搜尋結果
`Documents.Search`方法會傳回`DocumentSearchResult`包含的查詢結果的物件。 上一節中的範例使用一部分的方法`WriteDocuments`輸出主控台搜尋結果︰

```csharp
private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.Results)
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

以下是結果查詢前一節，假設變異數的 「 旅館 」 索引填入中[使用.NET SDK Azure 搜尋中的資料匯入](search-import-data-dotnet.md)的範例資料的外觀︰

```
Search the entire index for the term 'budget' and return only the hotelName field:

Name: Roach Motel

Apply a filter to the index to find hotels cheaper than $150 per night, and return the hotelId and description:

ID: 2   Description: Cheapest hotel in town
ID: 3   Description: Close to town hall and the river

Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Search the entire index for the term 'motel':

ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577

```

上述範例使用主控台輸出搜尋結果。 您同樣必須在自己的應用程式中顯示搜尋結果。 請參閱[GitHub 在這個範例](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetSample)，如需如何呈現 ASP.NET MVC 式 web 應用程式中的搜尋結果的範例。
