<properties
   pageTitle="如何使用.NET 應用程式從 Azure 搜尋 |Microsoft Azure |裝載的雲端搜尋服務"
   description="如何使用.NET 應用程式從 Azure 搜尋"
   services="search"
   documentationCenter=""
   authors="brjohnstmsft"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="dotnet"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="10/06/2016"
   ms.author="brjohnst"/>

# <a name="how-to-use-azure-search-from-a-net-application"></a>如何使用.NET 應用程式從 Azure 搜尋

本文是協助您上手並開始使用[Azure 搜尋.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)逐步解說。 您可以使用.NET SDK 實作豐富的搜尋體驗︰ 使用 Azure 搜尋應用程式中。

## <a name="whats-in-the-azure-search-sdk"></a>什麼是 Azure 中搜尋 SDK

用戶端的文件庫，包括 SDK `Microsoft.Azure.Search`。 讓您管理您的索引，資料來源和索引子，以及上傳和管理文件，並執行查詢，完全不需要處理 HTTP 和 JSON 的詳細資料。

用戶端程式庫定義的類別，如`Index`， `Field`，及`Document`、 以及作業等`Indexes.Create`和`Documents.Search`上`SearchServiceClient`和`SearchIndexClient`類別。 這些類別會組織成下列命名空間︰

- [Microsoft.Azure.Search](https://msdn.microsoft.com/library/azure/microsoft.azure.search.aspx)
- [Microsoft.Azure.Search.Models](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.aspx)

目前版本的 Azure 搜尋.NET SDK 現在推出。 如果您想要提供意見反應，我們將在下一個版本，請造訪我們的[意見反應] 頁面](https://feedback.azure.com/forums/263029-azure-search/)。

支援的.NET SDK 版本`2015-02-28`Azure 搜尋 REST API，記載於[MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx)上。 此版本現在包含 [Lucene 查詢語法和 Microsoft 語言分析器支援。 較新的功能*不*屬於此版本，例如支援`moreLikeThis`搜尋參數，是在[預覽](search-api-2015-02-28-preview.md)和尚無法使用 SDK 中。 您可以檢查在任一功能上的狀態更新的[搜尋服務版本設定](https://msdn.microsoft.com/library/azure/dn864560.aspx)。

不支援此 SDK 的其他功能包括︰

  - [管理作業](https://msdn.microsoft.com/library/azure/dn832684.aspx)。 管理作業包含佈建 Azure 搜尋服務和管理的 API 金鑰。 以下將會支援在不同的 Azure 搜尋.NET 管理 SDK 未來。

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>升級至最新版的 SDK

如果您已使用的舊版 Azure 搜尋.NET SDK，而且您想要升級至新版推出，[本主題](search-dotnet-sdk-migration.md)將解說如何。

## <a name="requirements-for-the-sdk"></a>SDK 的需求

1. Visual Studio 2013 或 Visual Studio 2015。

2. Azure 搜尋服務。 若要使用 SDK，您將需要您的服務及一或多個 API 金鑰的名稱。 [建立在入口網站的服務](search-create-service-portal.md)會協助您透過下列步驟執行。

3. 下載 Azure 搜尋.NET SDK [NuGet 套件](http://www.nuget.org/packages/Microsoft.Azure.Search)Visual Studio 中使用 「 管理 NuGet 套件 」。 只搜尋套件名稱`Microsoft.Azure.Search`NuGet.org 上。

Azure 搜尋.NET SDK 支援的.NET Framework 4.5，以及針對選取目標 Windows 8.1 和 Windows Phone 8.1 版的 Windows 市集應用程式為目標應用程式。 不支援 Silverlight。

## <a name="core-scenarios"></a>核心案例

有數種方法，您將需要執行搜尋應用程式中。 在本教學課程中，我們會核心細想這些案例︰

- 建立索引
- 填入文件的索引
- 搜尋文件使用全文檢索搜尋及篩選

下列範例程式碼說明每個。 歡迎使用您自己的應用程式中的 [程式碼片段。

### <a name="overview"></a>概觀

我們會探索範例應用程式建立一個新名為 「 飯店 」 索引填入在特定的文件，然後執行搜尋查詢。 以下是主要的程式，顯示整個流程︰

    // This sample shows how to delete, create, upload documents and query an index
    static void Main(string[] args)
    {
        // Put your search service name here. This is the hostname portion of your service URL.
        // For example, if your service URL is https://myservice.search.windows.net, then your
        // service name is myservice.
        string searchServiceName = "myservice";

        string apiKey = "Put your API admin key here.";

        SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));

        Console.WriteLine("{0}", "Deleting index...\n");
        DeleteHotelsIndexIfExists(serviceClient);

        Console.WriteLine("{0}", "Creating index...\n");
        CreateHotelsIndex(serviceClient);

        ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

        Console.WriteLine("{0}", "Uploading documents...\n");
        UploadDocuments(indexClient);

        Console.WriteLine("{0}", "Searching documents 'fancy wifi'...\n");
        SearchDocuments(indexClient, searchText: "fancy wifi");

        Console.WriteLine("\n{0}", "Filter documents with category 'Luxury'...\n");
        SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

        Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
        Console.ReadKey();
    }

我們會逐步引導透過此方法。 首先，我們需要來建立新的`SearchServiceClient`。 這個物件可讓您管理索引。 以建構，您需要提供您 Azure 搜尋服務名稱，以及系統 API 金鑰。

        // Put your search service name here. This is the hostname portion of your service URL.
        // For example, if your service URL is https://myservice.search.windows.net, then your
        // service name is myservice.
        string searchServiceName = "myservice";

        string apiKey = "Put your API admin key here.";

        SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));

> [AZURE.NOTE] 如果您提供不正確的索引鍵 （例如，查詢鍵管理員鍵是必要的位置），請`SearchServiceClient`就會擲回`CloudException`錯誤訊息 「 禁止 「 第一次電話作業方法，例如`Indexes.Create`。 如果這種情況給您，再次檢查我們 API 金鑰。

下一步幾行呼叫方法，建立名為 「 旅館 」，先將它刪除第一次已經存在的索引。 我們會逐步執行這些方法稍有更新版本。

        Console.WriteLine("{0}", "Deleting index...\n");
        DeleteHotelsIndexIfExists(serviceClient);

        Console.WriteLine("{0}", "Creating index...\n");
        CreateHotelsIndex(serviceClient);

接下來，需要填入索引。 若要這麼做，我們需要`SearchIndexClient`。 有兩種方式取得︰ 建構，或撥打`Indexes.GetClient`上`SearchServiceClient`。 我們使用後者方便時觀看。

        ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

> [AZURE.NOTE] 在一般搜尋應用程式中，索引管理及母體會處理個別元件的搜尋查詢。 `Indexes.GetClient`填入索引，因為它會將儲存您的提供其他問題方便`SearchCredentials`。 它會傳遞給您用來建立管理員鍵`SearchServiceClient`至新`SearchIndexClient`。 不過，在執行查詢的應用程式組件，最好建立`SearchIndexClient`直接讓您可以將傳遞查詢索引鍵，而不是管理員鍵。 這是與最低權限原則一致，而且是為了讓您的應用程式更加安全。 您可以瞭解更多有關管理員索引鍵和查詢鍵[以下](https://msdn.microsoft.com/library/azure/dn798935.aspx)。

現在有`SearchIndexClient`，我們可以填入索引。 這是由另一種方法，我們會逐步解說更新版本。

        Console.WriteLine("{0}", "Uploading documents...\n");
        UploadDocuments(indexClient);

最後，我們執行幾個搜尋查詢，並顯示結果]，再使用`SearchIndexClient`:

        Console.WriteLine("{0}", "Searching documents 'fancy wifi'...\n");
        SearchDocuments(indexClient, searchText: "fancy wifi");

        Console.WriteLine("\n{0}", "Filter documents with category 'Luxury'...\n");
        SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

        Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
        Console.ReadKey();

如果您具備有效的服務名稱和 API 金鑰執行這個應用程式，輸出看起來應該像這樣︰

    Deleting index...

    Creating index...

    Uploading documents...

    Searching documents 'fancy wifi'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 956-532     Name: Express Rooms     Category: Budget        Tags: [wifi, budget]

    Filter documents with category 'Luxury'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 566-518     Name: Surprisingly Expensive Suites     Category: Luxury    Tags: []
    Complete.  Press any key to end application...

應用程式的完整原始程式碼提供本文結尾處。

接下來，我們要深入瞭解每個方法呼叫`Main`。

### <a name="creating-an-index"></a>建立索引

建立之後`SearchServiceClient`下, 一個項目`Main`會是刪除 「 旅館 」 索引，如果已經存在。 是由下列方法︰

    private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
    {
        if (serviceClient.Indexes.Exists("hotels"))
        {
            serviceClient.Indexes.Delete("hotels");
        }
    }

這個方法會使用指定`SearchServiceClient`要檢查與如果索引，如果是這樣，將其刪除。

> [AZURE.NOTE] 本文中的範例程式碼使用簡單的 Azure 搜尋.NET SDK 同步的方法。 我們建議您自己的應用程式中使用非同步的方法，以將可調整和回應。 例如，在上述方法您可以使用`ExistsAsync`和`DeleteAsync`而不是`Exists`和`Delete`。

下一張、`Main`呼叫此方法來建立新的 「 旅館 」 索引︰

    private static void CreateHotelsIndex(SearchServiceClient serviceClient)
    {
        var definition = new Index()
        {
            Name = "hotels",
            Fields = new[]
            {
                new Field("hotelId", DataType.String)                       { IsKey = true },
                new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true },
                new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true },
                new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
                new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
                new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
            }
        };

        serviceClient.Indexes.Create(definition);
    }

這個方法會建立新`Index`物件清單`Field`定義新的索引的結構描述的物件。 每個欄位具有名稱與資料類型，定義搜尋行為的數個屬性。 除了欄位以外，您也可以索引 （省略贅述範例） 新增計分的設定檔]、 [suggesters 或 [CORS 選項。 在 SDK 參考[MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.index_members.aspx)，以及[Azure 搜尋 REST API 參照](https://msdn.microsoft.com/library/azure/dn798935.aspx)中，您可以找到索引物件及其構成部分的相關資訊。

### <a name="populating-the-index"></a>填入索引

下一個步驟中`Main`可填入新建立索引。 這是下列方法︰

    private static void UploadDocuments(ISearchIndexClient indexClient)
    {
        var documents =
            new Hotel[]
            {
                new Hotel()
                {
                    HotelId = "1058-441",
                    HotelName = "Fancy Stay",
                    BaseRate = 199.0,
                    Category = "Luxury",
                    Tags = new[] { "pool", "view", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero),
                    Rating = 5,
                    Location = GeographyPoint.Create(47.678581, -122.131577)
                },
                new Hotel()
                {
                    HotelId = "666-437",
                    HotelName = "Roach Motel",
                    BaseRate = 79.99,
                    Category = "Budget",
                    Tags = new[] { "motel", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                    Rating = 1,
                    Location = GeographyPoint.Create(49.678581, -122.131577)
                },
                new Hotel()
                {
                    HotelId = "970-501",
                    HotelName = "Econo-Stay",
                    BaseRate = 129.99,
                    Category = "Budget",
                    Tags = new[] { "pool", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4,
                    Location = GeographyPoint.Create(46.678581, -122.131577)
                },
                new Hotel()
                {
                    HotelId = "956-532",
                    HotelName = "Express Rooms",
                    BaseRate = 129.99,
                    Category = "Budget",
                    Tags = new[] { "wifi", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4,
                    Location = GeographyPoint.Create(48.678581, -122.131577)
                },
                new Hotel()
                {
                    HotelId = "566-518",
                    HotelName = "Surprisingly Expensive Suites",
                    BaseRate = 279.99,
                    Category = "Luxury",
                    ParkingIncluded = false
                }
            };

        try
        {
            var batch = IndexBatch.Upload(documents);
            indexClient.Documents.Index(batch);
        }
        catch (IndexBatchException e)
        {
            // Sometimes when your Search service is under load, indexing will fail for some of the documents in
            // the batch. Depending on your application, you can take compensating actions like delaying and
            // retrying. For this simple demo, we just log the failed document keys and continue.
            Console.WriteLine(
                "Failed to index some of the documents: {0}",
                String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
        }

        // Wait a while for indexing to complete.
        Thread.Sleep(2000);
    }

這個方法有四個部分。 第一個建立的陣列`Hotel`物件做為要上傳至索引我們輸入資料。 此資料是硬式編碼的簡單。 在您的應用程式，您的資料很可能來自外部資料來源，例如 SQL 資料庫。

建立第二部分`IndexBatch`包含文件。 指定您想要在建立的時，在此情況下，則可電話套用至批次的作業`IndexBatch.Upload`。 批次然後上傳至 Azure 搜尋索引的`Documents.Index`方法。

> [AZURE.NOTE] 在此範例中，我們會剛才上傳文件。 如果您想要將變更合併至現有的文件或刪除文件，您可以建立批次，則可電話`IndexBatch.Merge`， `IndexBatch.MergeOrUpload`，或`IndexBatch.Delete`改為。 您也可以藉由呼叫混合不同的作業，以單一批次`IndexBatch.New`，其中使用各種`IndexAction`物件，每個會告訴 Azure 搜尋特定執行上的文件。 您可以建立每一個`IndexAction`自己作業，例如呼叫的相對應的方法來使用`IndexAction.Merge`， `IndexAction.Upload`，依此類推。

這個方法的第三部分是處理重要錯誤情況編製索引的攔截區塊。 如果您 Azure 搜尋服務失敗索引批次中的文件的部分`IndexBatchException`擲回的`Documents.Index`。 如果您編製索引的文件重度負載下您的服務時，也可能會發生。 **我們強烈建議明確地處理此程式碼中的大小寫。** 您可以延遲並再試一次編製索引作業已失敗、 文件或您可以登入並繼續等範例會執行，或您可以執行其他根據您的應用程式的資料一致性需求項目。

最後，方法延遲兩秒。 編製索引的情況非同步 Azure 搜尋服務，因此需要等候一段時間，以確保文件都可供搜尋的範例應用程式。 就像這樣的延遲只有時，通常是需要示範、 測試和範例應用程式中。

#### <a name="how-the-net-sdk-handles-documents"></a>.NET SDK 處理文件的方式

您可能會想要知道如何 Azure 搜尋.NET SDK 的無法上傳的使用者定義的類別，例如執行個體`Hotel`索引。 若要協助回答的問題，我們來看看`Hotel`類別︰

    [SerializePropertyNamesAsCamelCase]
    public class Hotel
    {
        public string HotelId { get; set; }

        public string HotelName { get; set; }

        public double? BaseRate { get; set; }

        public string Category { get; set; }

        public string[] Tags { get; set; }

        public bool? ParkingIncluded { get; set; }

        public DateTimeOffset? LastRenovationDate { get; set; }

        public int? Rating { get; set; }

        public GeographyPoint Location { get; set; }

        public override string ToString()
        {
            return String.Format(
                "ID: {0}\tName: {1}\tCategory: {2}\tTags: [{3}]",
                HotelId,
                HotelName,
                Category,
                (Tags != null) ? String.Join(", ", Tags) : String.Empty);
        }
    }

首先要注意是每一個公用屬性`Hotel`對應欄位索引定義，但一個重要的差異︰ 每個欄位的名稱 （「 駱駝情況 」），以英文小寫字母啟動時的每一個公用屬性名稱`Hotel`第一句是小寫字母 （「 Pascal 寫 」）。 這是在執行資料繫結目標結構描述會超出的應用程式開發人員控制項的.NET 應用程式中常見的案例。 不必違反.NET 命名藉由屬性名稱駱駝大小寫的指導方針，您可以告訴，將屬性名稱對應到駱駝大小寫自動 SDK`[SerializePropertyNamesAsCamelCase]`屬性。

> [AZURE.NOTE] Azure 搜尋.NET SDK 序列化和還原序列化 JSON 向您自訂的模型物件使用[NewtonSoft JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm)文件庫。 如有需要您可以自訂這個序列化。 如需詳細資訊，請參閱[自訂序列化 JSON.NET](#JsonDotNet)。
 
第二個重要的是關於`Hotel`類別是公用屬性的資料類型。 .NET 類型，這些屬性對應至索引定義在其對等的欄位類型。 例如，`Category`字串屬性對應至`category`欄位，也就是類型`Edm.String`。 有類似類型之間的對應`bool?`和`Edm.Boolean`，`DateTimeOffset?`和`Edm.DateTimeOffset`等。使用記錄類型對應的特定規則`Documents.Get` [MSDN](https://msdn.microsoft.com/library/azure/dn931291.aspx)上的方法。

若要為文件中使用您自己的類別這項功能可以雙向; 運作您也可以擷取搜尋結果，並有 SDK 自動還原序列化他們您選擇的類型，我們將會看到 [下一步] 區段中。

> [AZURE.NOTE] Azure 搜尋.NET SDK 也支援動態輸入文件使用`Document`類別，也就是鍵值對應的欄位名稱] 欄位的值。 您不知道索引架構在設計階段，或者可能有所不便繫結至特定的模型類別，這是非常實用。 所有的方法 SDK 中處理的文件有多載搭配使用的`Document`類別，以及強型別接受多載，一般類型參數。 僅限後者運用在程式碼範例在本教學課程。 您可以瞭解更多關於`Document`課程[此](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.document.aspx)。

**重要附註的相關資料類型**

當設計您自己的模型類別對應到 Azure 搜尋索引，我們建議宣告值類型的屬性，例如`bool`和`int`可為 null (例如，`bool?`而不是`bool`)。 如果您使用的非 null 的屬性，您必須**確保**沒有任何文件索引中的包含對應的欄位的 null 值。 SDK 和 Azure 搜尋服務都不會協助您強制執行這。

這是不只是假設的問題︰ 想像位置您的新欄位新增至現有的索引類型`Edm.Int32`。 之後更新索引定義 （因為所有類型都都會 nullable Azure 搜尋） 的所有文件時，會出現的新欄位的 null 值。 如果您使用非 null，然後使用模型類別`int`該欄位的屬性，您會收到`JsonSerializationException`嘗試擷取文件時，就像這樣︰

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

因此，建議您模型類別中使用 nullable 類型，最佳做法。

<a name="JsonDotNet"></a>
#### <a name="custom-serialization-with-jsonnet"></a>自訂序列化 JSON.NET

SDK 使用 JSON.NET 序列化和還原序列化文件。 您可以自訂序列化及還原序列化如有需要定義自己`JsonConverter`或`IContractResolver`（請參閱更多詳細資料的[JSON.NET 文件](http://www.newtonsoft.com/json/help/html/Introduction.htm)）。 當您要修改現有的模型類別從 Azure 搜尋與其他更進階的情況下使用的應用程式時，這可以很有用。 例如，使用自訂序列化，您可以︰

 - 包含或排除模型類別的特定屬性，從已經儲存的文件欄位。
 - 對應之間程式碼中的屬性名稱和索引中的欄位名稱。
 - 建立可用於同時對應至文件欄位的內容，以及建立對應索引定義的自訂屬性。

您可以找到實作 Azure 搜尋.NET SDK 上 GitHub 單元測試中自訂序列化的範例。 絕佳起點是[這個資料夾中](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Search/Search.Tests/Tests/Models)。 它所含的自訂序列化測試，可使用的類別。

### <a name="searching-for-documents-in-the-index"></a>搜尋索引中的文件

範例應用程式中的最後一個步驟是搜尋索引中的一些文件。 下列方法負責這項工作︰

    private static void SearchDocuments(ISearchIndexClient indexClient, string searchText, string filter = null)
    {
        // Execute search based on search text and optional filter
        var sp = new SearchParameters();

        if (!String.IsNullOrEmpty(filter))
        {
            sp.Filter = filter;
        }

        DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
        foreach (SearchResult<Hotel> result in response.Results)
        {
            Console.WriteLine(result.Document);
        }
    }

首先，此方法會建立新`SearchParameters`物件。 這用來指定其他選項，例如排序、 篩選、 分頁、 以及 faceting 查詢。 在此範例中，我們只設定`Filter`屬性。

下一步是實際執行搜尋查詢。 這是使用`Documents.Search`方法。 在此案例中，我們將傳遞搜尋文字，用來做為字串，以及先前建立的搜尋參數。 我們也指定`Hotel`類型參數`Documents.Search`，這就是告訴將搜尋結果中的文件類型的物件還原序列化 SDK `Hotel`。

最後，此方法逐一查看所有相符項目在搜尋結果中列印主控台每份文件。

讓我們來看看如何呼叫這個方法︰

    SearchDocuments(indexClient, searchText: "fancy wifi");

    SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

在第一次呼叫中，我們要尋找的所有的文件包含查詢字詞 「 美觀 」 或 「 wifi 」。 在第二個撥號中，搜尋的文字設為 「 * 」，這表示 「 找出所有項目]。 您可以找到有關搜尋查詢運算式語法的詳細資訊[以下](https://msdn.microsoft.com/library/azure/dn798920.aspx)。

第二個呼叫使用 OData`$filter`運算式， `category eq 'Luxury'`。 這會限制搜尋只傳回文件位置`category`欄位完全符合字串 「 可惜 」。 您可以瞭解更多有關 Azure 搜尋支援的 OData 語法[以下](https://msdn.microsoft.com/library/azure/dn798921.aspx)。

現在，您知道這兩個呼叫所要執行的動作，它應該看起來更清楚輸出為什麼看起來像這樣︰

    Searching documents 'fancy wifi'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 956-532     Name: Express Rooms     Category: Budget        Tags: [wifi, budget]

    Filter documents with category 'Luxury'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 566-518     Name: Surprisingly Expensive Suites     Category: Luxury    Tags: []

第一次搜尋會傳回兩個文件。 第一個具有 「 花俏 」 的名稱，而第二個可 」 wifi 」`tags`欄位。 第二個搜尋傳回兩個文件，就可能會發生是唯一的文件索引中有`category`欄位設為 「 可惜 」。

此步驟完成教學課程中，但不在此停止。 **接下來的步驟**會提供其他資源的進一步了解 Azure 搜尋。

## <a name="next-steps"></a>後續步驟

- 瀏覽至參照的[.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)及[REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) MSDN 上。
- 加深您透過[影片其他範例和教學課程](search-video-demo-tutorial-list.md)的知識。
- 閱讀相關功能和 Azure 搜尋 SDK 此版本的功能︰ [Azure 搜尋概觀](https://msdn.microsoft.com/library/azure/dn798933.aspx)
- 檢閱若要瞭解的規則命名各種物件的[命名慣例](https://msdn.microsoft.com/library/azure/dn857353.aspx)。
- 檢閱 Azure 搜尋[支援的資料類型](https://msdn.microsoft.com/library/azure/dn798938.aspx)。


## <a name="sample-application-source-code"></a>範例應用程式原始程式碼

以下是用於此逐步範例應用程式的完整原始程式碼。 請注意，您需要 Program.cs API 金鑰預留位置與服務名稱取代您自己的值，如果您想要建立和執行範例。

Program.cs:

```csharp
using System;
using System.Configuration;
using System.Linq;
using System.Threading;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;

namespace AzureSearch.SDKHowTo
{
    class Program
    {
        // This sample shows how to delete, create, upload documents and query an index
        static void Main(string[] args)
        {
            // Put your search service name here. This is the hostname portion of your service URL.
            // For example, if your service URL is https://myservice.search.windows.net, then your
            // service name is myservice.
            string searchServiceName = "myservice";

            string apiKey = "Put your API admin key here.";

            SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));

            Console.WriteLine("{0}", "Deleting index...\n");
            DeleteHotelsIndexIfExists(serviceClient);

            Console.WriteLine("{0}", "Creating index...\n");
            CreateHotelsIndex(serviceClient);

            ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

            Console.WriteLine("{0}", "Uploading documents...\n");
            UploadDocuments(indexClient);

            Console.WriteLine("{0}", "Searching documents 'fancy wifi'...\n");
            SearchDocuments(indexClient, searchText: "fancy wifi");

            Console.WriteLine("\n{0}", "Filter documents with category 'Luxury'...\n");
            SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

            Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
            Console.ReadKey();
        }

        private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
        {
            if (serviceClient.Indexes.Exists("hotels"))
            {
                serviceClient.Indexes.Delete("hotels");
            }
        }

        private static void CreateHotelsIndex(SearchServiceClient serviceClient)
        {
            var definition = new Index()
            {
                Name = "hotels",
                Fields = new[]
                {
                    new Field("hotelId", DataType.String)                       { IsKey = true },
                    new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true },
                    new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true },
                    new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
                    new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
                    new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
                    new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
                    new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
                    new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
                }
            };

            serviceClient.Indexes.Create(definition);
        }

        private static void UploadDocuments(ISearchIndexClient indexClient)
        {
            var documents =
                new Hotel[]
                {
                    new Hotel()
                    {
                        HotelId = "1058-441",
                        HotelName = "Fancy Stay",
                        BaseRate = 199.0,
                        Category = "Luxury",
                        Tags = new[] { "pool", "view", "concierge" },
                        ParkingIncluded = false,
                        LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero),
                        Rating = 5,
                        Location = GeographyPoint.Create(47.678581, -122.131577)
                    },
                    new Hotel()
                    {
                        HotelId = "666-437",
                        HotelName = "Roach Motel",
                        BaseRate = 79.99,
                        Category = "Budget",
                        Tags = new[] { "motel", "budget" },
                        ParkingIncluded = true,
                        LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                        Rating = 1,
                        Location = GeographyPoint.Create(49.678581, -122.131577)
                    },
                    new Hotel()
                    {
                        HotelId = "970-501",
                        HotelName = "Econo-Stay",
                        BaseRate = 129.99,
                        Category = "Budget",
                        Tags = new[] { "pool", "budget" },
                        ParkingIncluded = true,
                        LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                        Rating = 4,
                        Location = GeographyPoint.Create(46.678581, -122.131577)
                    },
                    new Hotel()
                    {
                        HotelId = "956-532",
                        HotelName = "Express Rooms",
                        BaseRate = 129.99,
                        Category = "Budget",
                        Tags = new[] { "wifi", "budget" },
                        ParkingIncluded = true,
                        LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                        Rating = 4,
                        Location = GeographyPoint.Create(48.678581, -122.131577)
                    },
                    new Hotel()
                    {
                        HotelId = "566-518",
                        HotelName = "Surprisingly Expensive Suites",
                        BaseRate = 279.99,
                        Category = "Luxury",
                        ParkingIncluded = false
                    }
                };

            try
            {
                var batch = IndexBatch.Upload(documents);
                indexClient.Documents.Index(batch);
            }
            catch (IndexBatchException e)
            {
                // Sometimes when your Search service is under load, indexing will fail for some of the documents in
                // the batch. Depending on your application, you can take compensating actions like delaying and
                // retrying. For this simple demo, we just log the failed document keys and continue.
                Console.WriteLine(
                    "Failed to index some of the documents: {0}",
                    String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
            }

            // Wait a while for indexing to complete.
            Thread.Sleep(2000);
        }

        private static void SearchDocuments(ISearchIndexClient indexClient, string searchText, string filter = null)
        {
            // Execute search based on search text and optional filter
            var sp = new SearchParameters();

            if (!String.IsNullOrEmpty(filter))
            {
                sp.Filter = filter;
            }

            DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
            foreach (SearchResult<Hotel> result in response.Results)
            {
                Console.WriteLine(result.Document);
            }
        }
    }
}
```

Hotel.cs:

```csharp
using System;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;

namespace AzureSearch.SDKHowTo
{
    [SerializePropertyNamesAsCamelCase]
    public class Hotel
    {
        public string HotelId { get; set; }

        public string HotelName { get; set; }

        public double? BaseRate { get; set; }

        public string Category { get; set; }

        public string[] Tags { get; set; }

        public bool? ParkingIncluded { get; set; }

        public DateTimeOffset? LastRenovationDate { get; set; }

        public int? Rating { get; set; }

        public GeographyPoint Location { get; set; }

        public override string ToString()
        {
            return String.Format(
                "ID: {0}\tName: {1}\tCategory: {2}\tTags: [{3}]",
                HotelId,
                HotelName,
                Category,
                (Tags != null) ? String.Join(", ", Tags) : String.Empty);
        }
    }
}
```
