<properties
   pageTitle="升級至 Azure 搜尋.NET SDK 1.1 版 |Microsoft Azure |裝載的雲端搜尋服務"
   description="升級至 Azure 搜尋.NET SDK 版本 1.1 （英文）"
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
   ms.date="08/15/2016"
   ms.author="brjohnst"/>

# <a name="upgrading-to-the-azure-search-net-sdk-version-20-preview"></a>升級至 Azure 搜尋.NET SDK 版本 2.0-預覽

如果您使用 1.1 或[Azure 搜尋.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)的較舊的版本，本文可協助您升級您的應用程式，使用 [下一步主要版本 2.0 預覽。

包括範例 SDK 的其他一般的逐步解說，請參閱[如何使用.NET 應用程式從 Azure 搜尋](search-howto-dotnet-sdk.md)。

版本 2.0-Azure 搜尋.NET SDK 的預覽包含舊版的一些變更。 這些是大多次要，讓變更您的程式碼應該要求僅輕鬆。 如需如何變更您的程式碼的相關指示，以使用新的 SDK 版本，請參閱[升級的步驟](#UpgradeSteps)。

> [AZURE.NOTE] 如果您使用的 0.13 預覽或較舊的版本，您應該升級至新版 1.1 （英文），然後再升級至 2.0 預覽。 請參閱[附錄︰ 步驟可升級至版本 1.1 （英文）](#UpgradeStepsV1)如需相關指示。

<a name="WhatsNew"></a>
## <a name="whats-new-in-version-20-preview"></a>版本 2.0 預覽中的新功能

版本 2.0 預覽是 Azure 搜尋.NET SDK 目標 Azure 搜尋 REST API，特別是 2015年 02-28-預覽預覽版本的第一個版本。 如此可讓您可以使用許多預覽功能 Azure 搜尋.NET 應用程式，包括下列︰

- [自訂分析器](https://aka.ms/customanalyzers)
- [Azure Blob 儲存體](search-howto-indexing-azure-blob-storage.md)和[Azure 資料表儲存體](search-howto-indexing-azure-tables.md)重新支援
- 透過[欄位對應](search-indexer-field-mappings.md)重新自訂
- Etag 支援]，即可啟用安全同時更新索引定義、 索引子和資料來源
- 支援.NET 核心和.NET 可攜式設定檔 111

<a name="UpgradeSteps"></a>
## <a name="steps-to-upgrade"></a>若要升級的步驟

首先，更新您 NuGet 參考`Microsoft.Azure.Search`使用 NuGet 封裝管理員主控台或以滑鼠右鍵按一下您的專案參照和 Visual Studio 中選取 [管理 NuGet 封包...]。 請確定您選取 「 包含搶鮮版 「 NuGet 「 管理套件 」 中啟用測試版封包視窗 Visual Studio 中，或使用`-IncludePrerelease`切換如果您使用的 NuGet 封裝管理員主控台。

在新的套件和相依性，具有下載 NuGet，重建專案。 視您的程式碼的結構方式而定，可能會重建成功。 如果是這樣，您準備好 ！

如果您建立失敗，您應該會看到建置錯誤，如下所示︰

    Program.cs(31,45,31,86): error CS0266: Cannot implicitly convert type 'Microsoft.Azure.Search.ISearchIndexClient' to 'Microsoft.Azure.Search.SearchIndexClient'. An explicit conversion exists (are you missing a cast?)

若要修正此建立錯誤是下一個步驟。 如什麼造成錯誤，以及如何修正此問題的詳細資訊，請參閱[重大版本 2.0 預覽中的變更](#ListOfChanges)。

您可能會看到其他建置警告相關過時的方法或屬性。 警告中會包含該如何使用，而不是遭取代之功能的相關指示。 例如，如果應用程式使用`SearchRequestOptions.RequestId`屬性，您應收到警告，指出`"This property is deprecated. Please use ClientRequestId instead."`

一旦您已修正任何建置錯誤，您可以在您的應用程式，以善用新的功能如果您想要進行變更。 在 SDK 的新功能的[版本 2.0 preview 的新增功能](#WhatsNew)的詳細資訊。

<a name="ListOfChanges"></a>
## <a name="breaking-changes-in-version-20-preview"></a>在 [版本 2.0 預覽重大變更

有只有一個版本 2.0 預覽可能需要變更程式碼除了重新建立您的應用程式的相關變更。

### <a name="indexesgetclient-return-type"></a>Indexes.GetClient 傳回類型

`Indexes.GetClient`方法有新的傳回類型。 先前，則傳回`SearchIndexClient`，但這已變更為`ISearchIndexClient`版本 2.0 預覽中。 這是支援客戶想要 mock`GetClient`方法所傳回的模擬實作單位測試`ISearchIndexClient`。

#### <a name="example"></a>範例

如果您的程式碼看起來像這樣︰

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

您可以變更為此修正任何建置錯誤︰

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

## <a name="conclusion"></a>結束時
如果您需要使用 Azure 搜尋.NET SDK 的更多詳細資料，請參閱我們最近更新[使用方法](search-howto-dotnet-sdk.md)。

歡迎您的意見反應 SDK 上。 如果您遇到問題，請隨意我們尋求協助[Azure 搜尋 MSDN 論壇](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuresearch)上。 如果您發現錯誤時，您可以[Azure.NET SDK GitHub 存放庫](https://github.com/Azure/azure-sdk-for-net/issues)檔案有問題。 請確定您的問題標題加上字首 「 搜尋 SDK: 」。

Azure 搜尋尋找內容感謝您 ！

<a name="UpgradeStepsV1"></a>
## <a name="appendix-steps-to-upgrade-to-version-11"></a>附錄︰ 步驟可升級至版本 1.1 （英文） 

> [AZURE.NOTE] 本節僅適用於 Azure 搜尋.NET SDK 版本 0.13 預覽及較舊版本的使用者。

首先，更新您 NuGet 參考`Microsoft.Azure.Search`使用 NuGet 封裝管理員主控台或以滑鼠右鍵按一下您的專案參照和 Visual Studio 中選取 [管理 NuGet 封包...]。

在新的套件和相依性，具有下載 NuGet，重建專案。

如果您先前已使用版本 1.0.0-preview、 1.0.1-preview 或 1.0.2-preview，建置應該成功，您就可以準備開始 ！

如果您先前所使用的版本 0.13.0-preview 或更舊版本，您應該會看到建立類似下列錯誤︰

    Program.cs(137,56,137,62): error CS0117: 'Microsoft.Azure.Search.Models.IndexBatch' does not contain a definition for 'Create'
    Program.cs(137,99,137,105): error CS0117: 'Microsoft.Azure.Search.Models.IndexAction' does not contain a definition for 'Create'
    Program.cs(146,41,146,54): error CS1061: 'Microsoft.Azure.Search.IndexBatchException' does not contain a definition for 'IndexResponse' and no extension method 'IndexResponse' accepting a first argument of type 'Microsoft.Azure.Search.IndexBatchException' could be found (are you missing a using directive or an assembly reference?)
    Program.cs(163,13,163,42): error CS0246: The type or namespace name 'DocumentSearchResponse' could not be found (are you missing a using directive or an assembly reference?)

下一步是修正建置錯誤一個接一個]。 大部分會要求變更已經重新命名 sdk 某些類別和方法名稱。 [清單的重大變更版本 1.1 （英文） 中的](#ListOfChangesV1)包含名稱變更的清單。

如果您使用的自訂類別模型您的文件，這些類別有非 null 的基本類型的屬性 (例如，`int`或`bool`C#)，其中您應該注意 SDK 的 1.1 版中有錯誤修正。 如需詳細資訊，請參閱[在版本 1.1 （英文） 中的錯誤修正](#BugFixesV1)。

最後，當您已修正任何建置錯誤，您就可以應用程式，以善用新的功能如果您想要進行變更。

<a name="ListOfChangesV1"></a>
### <a name="list-of-breaking-changes-in-version-11"></a>清單的重大變更版本 1.1 （英文）

下列清單是以排序可能性變更會影響應用程式碼。

#### <a name="indexbatch-and-indexaction-changes"></a>IndexBatch 和 IndexAction 變更

`IndexBatch.Create`已重新命名為`IndexBatch.New`且不再擁有`params`引數。 您可以使用`IndexBatch.New`批次的混合不同類型的動作 （合併、 刪除）。 此外，有新的靜態方法建立批次的所有動作位置都都一樣︰ `Delete`， `Merge`， `MergeOrUpload`，及`Upload`。

`IndexAction`已不再擁有公用建構函式和其內容現在不變。 您應該使用新的靜態方法動作建立不同的用途︰ `Delete`， `Merge`， `MergeOrUpload`，及`Upload`。 `IndexAction.Create`已移除。 如果您使用的文件超量的狀況，請務必使用`Upload`改為。

##### <a name="example"></a>範例

如果您的程式碼看起來像這樣︰

    var batch = IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc)));
    indexClient.Documents.Index(batch);

您可以變更為此修正任何建置錯誤︰

    var batch = IndexBatch.New(documents.Select(doc => IndexAction.Upload(doc)));
    indexClient.Documents.Index(batch);

如果您想，您可以進一步簡化，此︰

    var batch = IndexBatch.Upload(documents);
    indexClient.Documents.Index(batch);

#### <a name="indexbatchexception-changes"></a>IndexBatchException 變更

`IndexBatchException.IndexResponse`屬性重新命名為`IndexingResults`，且其類型已`IList<IndexingResult>`。

##### <a name="example"></a>範例

如果您的程式碼看起來像這樣︰

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

您可以變更為此修正任何建置錯誤︰

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

<a name="OperationMethodChanges"></a>
#### <a name="operation-method-changes"></a>操作方法變更

Azure 搜尋.NET SDK 中的每一項作業同步和非同步來電者的公開為一組方法多載。 簽章和分解的這些方法多載已變更版本 1.1 （英文）。

例如，「 取得索引統計資料] 作業中較舊版本的 SDK 公開這些簽章︰

In `IIndexOperations`:

    // Asynchronous operation with all parameters
    Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        string indexName,
        CancellationToken cancellationToken);

In `IndexOperationsExtensions`:

    // Asynchronous operation with only required parameters
    public static Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        this IIndexOperations operations,
        string indexName);

    // Synchronous operation with only required parameters
    public static IndexGetStatisticsResponse GetStatistics(
        this IIndexOperations operations,
        string indexName);

在版本 1.1 （英文） 中相同的作業的方法簽章看起來像這樣︰

In `IIndexesOperations`:

    // Asynchronous operation with lower-level HTTP features exposed
    Task<AzureOperationResponse<IndexGetStatisticsResult>> GetStatisticsWithHttpMessagesAsync(
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        Dictionary<string, List<string>> customHeaders = null,
        CancellationToken cancellationToken = default(CancellationToken));

In `IndexesOperationsExtensions`:

    // Simplified asynchronous operation
    public static Task<IndexGetStatisticsResult> GetStatisticsAsync(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        CancellationToken cancellationToken = default(CancellationToken));

    // Simplified synchronous operation
    public static IndexGetStatisticsResult GetStatistics(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions));

開始使用版本 1.1 （英文），Azure 搜尋.NET SDK 組織作業方法以不同方式︰

 - 選用的參數為預設值參數而現在建立模型比其他方法多載。 此有時大幅降低方法多載，數目。
 - 延伸方法隱藏的來電者之間的無關的 HTTP 詳細資料]。 例如，較舊版本的 SDK 傳回 HTTP 狀態程式碼，您通常不需要核取因為作業方法引發的回應物件`CloudException`，任何指出錯誤的狀態程式碼。 新的副檔名方法只會傳回模型物件，儲存您的程式碼中列名這些問題。
 - 相反地，核心介面現在公開，提供更多控制項的 HTTP 層級，如果您需要的方法。 您現在可以傳入要求和新中所包含的自訂 HTTP 標頭`AzureOperationResponse<T>`傳回類型可讓您直接存取`HttpRequestMessage`和`HttpResponseMessage`作業。 `AzureOperationResponse`定義在`Microsoft.Rest.Azure`命名空間取代`Hyak.Common.OperationResponse`。

#### <a name="scoringparameters-changes"></a>ScoringParameters 變更

新類別名為`ScoringParameter`已經在最新的 SDK，使其更容易提供參數計分搜尋查詢中的設定檔中新增。 先前`ScoringProfiles`屬性`SearchParameters`類別輸入為`IList<string>`;現在輸入為`IList<ScoringParameter>`。

##### <a name="example"></a>範例

如果您的程式碼看起來像這樣︰

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters = new[] { "featuredParam-featured", "mapCenterParam-" + lon + "," + lat };

您可以變更為此修正任何建置錯誤︰ 

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters =
        new[]
        {
            new ScoringParameter("featuredParam", new[] { "featured" }),
            new ScoringParameter("mapCenterParam", GeographyPoint.Create(lat, lon))
        };

#### <a name="model-class-changes"></a>模型類別變更

由於簽章，因此會變更所述[作業方法會變更](#OperationMethodChanges)，許多類別中`Microsoft.Azure.Search.Models`已重新命名或移除命名空間。 例如︰

 - `IndexDefinitionResponse`已被取代`AzureOperationResponse<Index>`
 - `DocumentSearchResponse`若要已重新命名`DocumentSearchResult`
 - `IndexResult`若要已重新命名`IndexingResult`
 - `Documents.Count()`現在會傳回`long`而非文件計數`DocumentCountResponse`
 - `IndexGetStatisticsResponse`若要已重新命名`IndexGetStatisticsResult`
 - `IndexListResponse`若要已重新命名`IndexListResult`

若要簡言之， `OperationResponse`-衍生存在僅換行的模型物件已移除的類別。 剩餘的類別有從其尾碼`Response`至`Result`。

##### <a name="example"></a>範例

如果您的程式碼看起來像這樣︰

    IndexerGetStatusResponse statusResponse = null;

    try
    {
        statusResponse = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = statusResponse.ExecutionInfo.LastResult;

您可以變更為此修正任何建置錯誤︰

    IndexerExecutionInfo status = null;

    try
    {
        status = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = status.LastResult;

##### <a name="response-classes-and-ienumerable"></a>回應類別和 IEnumerable

變更其他可能會影響您的程式碼會保留集合的回應類別不再實作`IEnumerable<T>`。 不過，您也可以直接存取集合屬性。 例如，如果您的程式碼看起來像這樣︰

    DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response)
    {
        Console.WriteLine(result.Document);
    }

您可以變更為此修正任何建置錯誤︰

    DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response.Results)
    {
        Console.WriteLine(result.Document);
    }

##### <a name="important-note-for-web-applications"></a>重要附註的 web 應用程式

如果您有 web 應用程式序列化`DocumentSearchResponse`直接傳送至瀏覽器的搜尋結果，您會需要變更您的程式碼或結果會不循序化正確。 例如，如果您的程式碼看起來像這樣︰

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q)
        };
    }

您可以快速變更`.Results`搜尋回應修正搜尋結果呈現的屬性︰

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q).Results
        };
    }

您必須在這種情況下中尋找您的程式碼自己;**編譯器不會發出警告您**因為`JsonResult.Data`是類型`object`。

#### <a name="cloudexception-changes"></a>CloudException 變更

`CloudException`類別已經從`Hyak.Common`命名空間`Microsoft.Rest.Azure`命名空間。 此外，其`Error`屬性重新命名為`Body`。

#### <a name="searchserviceclient-and-searchindexclient-changes"></a>SearchServiceClient 和 SearchIndexClient 變更

類型`Credentials`屬性已變更從`SearchCredentials`為其基底的類別， `ServiceClientCredentials`。 如果您需要存取`SearchCredentials`的`SearchIndexClient`或`SearchServiceClient`，請使用 [新`SearchCredentials`屬性。

在舊版的 sdk，您可以`SearchServiceClient`和`SearchIndexClient`有原本的建構函式`HttpClient`參數。 這些已被取代使用建構函式`HttpClientHandler`和陣列`DelegatingHandler`物件。 如此可讓您更容易安裝自訂處理常式預先處理 HTTP 要求必要。

最後，建構函式會取代`Uri`和`SearchCredentials`已變更。 例如，如果您有的程式碼，看起來像這樣︰

    var client =
        new SearchServiceClient(
            new SearchCredentials("abc123"),
            new Uri("http://myservice.search.windows.net"));

您可以變更為此修正任何建置錯誤︰

    var client =
        new SearchServiceClient(
            new Uri("http://myservice.search.windows.net"),
            new SearchCredentials("abc123"));

也請注意，認證參數的類型已變更為`ServiceClientCredentials`。 這是可能會影響您的程式碼，自`SearchCredentials`衍生自`ServiceClientCredentials`。

#### <a name="passing-a-request-id"></a>傳送邀請識別碼

在較舊版本中的 SDK，您無法設定要求 ID`SearchServiceClient`或`SearchIndexClient`，它會包含在每個 REST API 邀請。 這是很適合用於搜尋服務的問題的疑難排解，如果您要連絡支援人員。 不過，則若要設定的每一項作業的唯一要求識別碼，而使用相同的 ID 所有作業的越有用處。 因此，`SetClientRequestId`的方法`SearchServiceClient`和`SearchIndexClient`已被移除。 不過，您可以將傳遞要求 ID 作業方法各透過選擇性`SearchRequestOptions`參數。

> [AZURE.NOTE] 在 SDK 的未來版本，我們會加入新的用戶端物件，全域設定要求 ID 機制一致使用其他 Azure Sdk 的方法。

#### <a name="example"></a>範例

如果您有的程式碼，看起來像這樣︰

    client.SetClientRequestId(Guid.NewGuid());
    ...
    long count = client.Documents.Count();

您可以變更為此修正任何建置錯誤︰

    long count = client.Documents.Count(new SearchRequestOptions(requestId: Guid.NewGuid()));

#### <a name="interface-name-changes"></a>介面名稱變更

為配合及其對應的屬性名稱都已作業群組介面名稱︰

 - 類型`ISearchServiceClient.Indexes`已重新命名從`IIndexOperations`至`IIndexesOperations`。
 - 類型`ISearchServiceClient.Indexers`已重新命名從`IIndexerOperations`至`IIndexersOperations`。
 - 類型`ISearchServiceClient.DataSources`已重新命名從`IDataSourceOperations`至`IDataSourcesOperations`。
 - 類型`ISearchIndexClient.Documents`已重新命名從`IDocumentOperations`至`IDocumentsOperations`。

這項變更不會影響您的程式碼，除非您建立 mock 進行測試這些介面。

<a name="BugFixesV1"></a>
### <a name="bug-fixes-in-version-11"></a>在版本 1.1 （英文） 中的錯誤修正

在舊版 Azure 搜尋.NET SDK 關於自訂模型類別的序列化時發生錯誤。 如果您建立自訂的模型課程非 null 值類型的屬性，可能會發生錯誤。

#### <a name="steps-to-reproduce"></a>重製步驟

建立自訂的模型類別非 null 值類型的屬性。 例如，新增 [公用`UnitCount`類型的屬性`int`而不是`int?`。

如果您的文件的預設值，該類型的索引 (例如，0， `int`)，會在 Azure 搜尋 null 欄位。 如果您接下來搜尋文件，`Search`通話就會擲回`JsonSerializationException`抱怨不能轉換`null`至`int`。

此外，篩選可能無法如預期由於 null 寫入的索引，而不是預期的值。

#### <a name="fix-details"></a>修正問題的詳細資料

我們有版本 1.1 （英文） 的 SDK 修正此問題。 現在，如果您有一個模型類別，像這樣︰

    public class Model
    {
        public string Key { get; set; }

        public int IntValue { get; set; }
    }

您設定`IntValue`為 0，該值正確序列化為 0，在線上且會儲存為 0，在索引中。 來回行程也如預期般運作。

有一個可能的問題，必須使用這個方法注意︰ 如果您使用的模型類型非 null 屬性時，您必須**確保**沒有任何文件索引中的包含對應的欄位的 null 值。 SDK 和 Azure 搜尋 REST API 都不會協助您強制執行這。

這是不只是假設的問題︰ 想像位置您的新欄位新增至現有的索引類型`Edm.Int32`。 之後更新索引定義 （因為所有類型都都會 nullable Azure 搜尋） 的所有文件時，會出現的新欄位的 null 值。 如果您使用非 null，然後使用模型類別`int`該欄位的屬性，您會收到`JsonSerializationException`嘗試擷取文件時，就像這樣︰

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

因此，我們還是建議模型類別中使用 nullable 類型，最佳做法。

如需此錯誤並修正的詳細資訊，請參閱[在 GitHub 此問題](https://github.com/Azure/azure-sdk-for-net/issues/1063)。
