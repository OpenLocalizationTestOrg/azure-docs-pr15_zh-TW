<properties
    pageTitle="使用.NET SDK Azure 搜尋中的資料上傳 |Microsoft Azure |裝載的雲端搜尋服務"
    description="瞭解如何上傳至使用.NET SDK Azure 搜尋索引的資料。"
    services="search"
    documentationCenter=""
    authors="brjohnstmsft"
    manager="jhubbard"
    editor=""
    tags=""/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="brjohnst"/>

# <a name="upload-data-to-azure-search-using-the-net-sdk"></a>上傳至 Azure 搜尋使用.NET SDK 的資料
> [AZURE.SELECTOR]
- [概觀](search-what-is-data-import.md)
- [.NET](search-import-data-dotnet.md)
- [其餘](search-import-data-rest-api.md)

本文說明如何使用[Azure 搜尋.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx) Azure 搜尋索引將資料匯入。

在開始之前此逐步解說，您應該已經[建立 Azure 搜尋索引](search-what-is-an-index.md)。 本文也假設您有建立`SearchServiceClient`物件，[建立使用.NET SDK Azure 搜尋索引](search-create-index-dotnet.md#CreateSearchServiceClient)中所示。

請注意，在 C# 撰寫本文中的所有範例程式碼。 您可以尋找完整的來源[GitHub](http://aka.ms/search-dotnet-howto)程式碼。

若要將您使用.NET SDK 的索引推文件，您必須︰

  1. 建立`SearchIndexClient`連線到您的搜尋索引的物件。
  2. 建立`IndexBatch`包含要新增的文件修改或刪除。
  3. 通話`Documents.Index`的方法將`SearchIndexClient`傳送`IndexBatch`至您的搜尋索引。

## <a name="i-create-an-instance-of-the-searchindexclient-class"></a>我。 建立 SearchIndexClient 類別的執行個體
若要將資料匯入您使用 Azure 搜尋.NET SDK 的索引，您需要建立的執行個體`SearchIndexClient`類別。 您可以建立此執行個體自己，但它的更容易如果您已經有`SearchServiceClient`執行個體來撥打其`Indexes.GetClient`方法。 例如，以下是您想要如何取得`SearchIndexClient`名為 「 旅館 」，從索引`SearchServiceClient`名為`serviceClient`:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

> [AZURE.NOTE] 在一般搜尋應用程式中，索引管理及母體會處理個別元件的搜尋查詢。 `Indexes.GetClient`填入索引，因為它會將儲存您的提供其他問題方便`SearchCredentials`。 它會傳遞給您用來建立管理員鍵`SearchServiceClient`至新`SearchIndexClient`。 不過，在執行查詢的應用程式組件，最好建立`SearchIndexClient`直接讓您可以將傳遞查詢索引鍵，而不是管理員鍵。 這是與[最低權限原則](https://en.wikipedia.org/wiki/Principle_of_least_privilege)一致，而且是為了讓您的應用程式更加安全。 您可以瞭解更多有關管理員索引鍵和[Azure 搜尋 REST API MSDN 上的參照](https://msdn.microsoft.com/library/azure/dn798935.aspx)中的查詢鍵。

`SearchIndexClient`具有`Documents`屬性。 這個屬性會提供您要新增、 修改、 刪除或查詢索引中的文件的所有方法。

## <a name="ii-decide-which-indexing-action-to-use"></a>II。 決定要使用的編製索引的動作
若要匯入資料使用.NET SDK，您需要封裝設定您的資料到`IndexBatch`物件。 `IndexBatch`封裝的集合`IndexAction`物件，每個包含文件，告知 Azure 搜尋 （上傳、 合併、 刪除等） 的文件上執行的動作的屬性。 根據哪一項動作] 下方選擇，僅特定欄位必須包含每份文件︰

巨集指令 | 描述 | 每份文件的必要欄位 | 備忘稿
--- | --- | --- | ---
`Upload` | `Upload`動作是類似 「 了 「 插入如果其為新增和更新/取代已經存在文件。 | 索引鍵，再加上任何其他您想要定義的欄位 | 未指定在邀請中的任何欄位時更新/取代現有的文件，必須設定為其欄位`null`。 這是欄位已經設定為非 null 值時，即使。
`Merge` | 更新現有的文件，以指定的欄位。 如果文件索引中不存在，則會失敗合併列印。 | 索引鍵，再加上任何其他您想要定義的欄位 | 您指定合併列印中的任何欄位，將會取代現有的欄位，在文件中。 這包含類型的欄位`DataType.Collection(DataType.String)`。 例如，如果文件包含欄位`tags`值`["budget"]`和執行合併值`["economy", "pool"]`的`tags`，最後的數值`tags`] 欄位會`["economy", "pool"]`。 不會`["budget", "economy", "pool"]`。
`MergeOrUpload` | 此動作類似`Merge`如果索引中已存在文件的指定索引鍵。 如果文件不存在，它的行為類似`Upload`新的文件。 | 索引鍵，再加上任何其他您想要定義的欄位 | -
`Delete` | 移除索引中指定的文件。 | 只有機碼 | 您可以指定以外的索引鍵欄位也會忽略任何欄位。 如果您想要移除文件中的個別功能變數時，使用`Merge`改為並直接欄位明確設定為 null。

您可以指定您想要使用的各種方法靜態什麼的動作`IndexBatch`和`IndexAction`類別下, 一節中所示。

## <a name="iii-construct-your-indexbatch"></a>III。 建構您 IndexBatch
現在，您知道要在您的文件上執行的動作，請您準備好建構`IndexBatch`。 下列範例示範如何建立批次的一些其他的動作。 請注意，我們的範例會使用稱為的自訂類別`Hotel`對應至 「 旅館 」 索引的文件。

```csharp
var actions =
    new IndexAction<Hotel>[]
    {
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "1",
                BaseRate = 199.0,
                Description = "Best hotel in town",
                DescriptionFr = "Meilleur hôtel en ville",
                HotelName = "Fancy Stay",
                Category = "Luxury",
                Tags = new[] { "pool", "view", "wifi", "concierge" },
                ParkingIncluded = false,
                SmokingAllowed = false,
                LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero),
                Rating = 5,
                Location = GeographyPoint.Create(47.678581, -122.131577)
            }),
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "2",
                BaseRate = 79.99,
                Description = "Cheapest hotel in town",
                DescriptionFr = "Hôtel le moins cher en ville",
                HotelName = "Roach Motel",
                Category = "Budget",
                Tags = new[] { "motel", "budget" },
                ParkingIncluded = true,
                SmokingAllowed = true,
                LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                Rating = 1,
                Location = GeographyPoint.Create(49.678581, -122.131577)
            }),
        IndexAction.MergeOrUpload(
            new Hotel()
            {
                HotelId = "3",
                BaseRate = 129.99,
                Description = "Close to town hall and the river"
            }),
        IndexAction.Delete(new Hotel() { HotelId = "6" })
    };

var batch = IndexBatch.New(actions);
```

在這個範例中，我們會使用`Upload`， `MergeOrUpload`，及`Delete`我們搜尋動作，所指定的上呼叫的方法為`IndexAction`類別。

假設此範例 「 旅館 」 索引已填入文件的數字。 請注意如何我們不需要使用時，指定所有可能的文件欄位`MergeOrUpload`和我們只指定的文件索引鍵的方式 (`HotelId`) 時使用`Delete`。

此外，請注意您僅可以包含超過 1000 個文件中的單一編製索引的要求。

> [AZURE.NOTE] 在此範例中，我們要將不同的動作套用至不同的文件。 如果您想要執行相同動作該批次，而不是電話中的所有文件`IndexBatch.New`，您可以使用的其他靜態方法`IndexBatch`。 例如，您可以建立批次，則可電話`IndexBatch.Merge`， `IndexBatch.MergeOrUpload`，或`IndexBatch.Delete`。 這些方法會採用文件集合 (物件的類型`Hotel`在此範例中) 而不是`IndexAction`物件。

## <a name="iv-import-data-to-the-index"></a>IV。 將資料匯入至索引
您已經有初始化`IndexBatch`物件時，您可以將其傳送給索引，呼叫`Documents.Index`上您`SearchIndexClient`物件。 下列範例會示範如何呼叫`Index`，以及您將需要執行為一些額外步驟︰

```csharp
try
{
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

Console.WriteLine("Waiting for documents to be indexed...\n");
Thread.Sleep(2000);
```

請注意`try` / `catch`周圍通話`Index`方法。 攔截處理重要錯誤情況編製索引作業。 如果您 Azure 搜尋服務失敗索引批次中的文件的部分`IndexBatchException`擲回的`Documents.Index`。 如果您編製索引的文件重度負載下您的服務時，也可能會發生。 **我們強烈建議明確地處理此程式碼中的大小寫。** 您可以延遲並再試一次編製索引作業已失敗、 文件或您可以登入並繼續等範例會執行，或您可以執行其他根據您的應用程式的資料一致性需求項目。

最後，範例的程式碼上述兩秒延遲。 編製索引的情況非同步 Azure 搜尋服務，因此需要等候一段時間，以確保文件都可供搜尋的範例應用程式。 就像這樣的延遲只有時，通常是需要示範、 測試和範例應用程式中。

<a name="HotelClass"></a>
### <a name="how-the-net-sdk-handles-documents"></a>.NET SDK 處理文件的方式

您可能會想要知道如何 Azure 搜尋.NET SDK 的無法上傳的使用者定義的類別，例如執行個體`Hotel`索引。 若要協助回答的問題，我們來看看`Hotel`類別，以[建立使用.NET SDK Azure 搜尋索引](search-create-index-dotnet.md#DefineIndex)中定義的索引結構描述地圖︰

```csharp
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    public string HotelId { get; set; }

    public double? BaseRate { get; set; }

    public string Description { get; set; }

    [JsonProperty("description_fr")]
    public string DescriptionFr { get; set; }

    public string HotelName { get; set; }

    public string Category { get; set; }

    public string[] Tags { get; set; }

    public bool? ParkingIncluded { get; set; }

    public bool? SmokingAllowed { get; set; }

    public DateTimeOffset? LastRenovationDate { get; set; }

    public int? Rating { get; set; }

    public GeographyPoint Location { get; set; }

    // ToString() method omitted for brevity...
}
```

首先要注意是每一個公用屬性`Hotel`對應欄位索引定義，但一個重要的差異︰ 每個欄位的名稱 （「 駱駝情況 」），以英文小寫字母啟動時的每一個公用屬性名稱`Hotel`第一句是小寫字母 （「 Pascal 寫 」）。 這是在執行資料繫結目標結構描述會超出的應用程式開發人員控制項的.NET 應用程式中常見的案例。 不必違反.NET 命名藉由屬性名稱駱駝大小寫的指導方針，您可以告訴，將屬性名稱對應到駱駝大小寫自動 SDK`[SerializePropertyNamesAsCamelCase]`屬性。

> [AZURE.NOTE] Azure 搜尋.NET SDK 序列化和還原序列化 JSON 向您自訂的模型物件使用[NewtonSoft JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm)文件庫。 如有需要您可以自訂這個序列化。 您可以在[升級 Azure 搜尋.NET SDK 版本 1.1 （英文）](search-dotnet-sdk-migration.md#WhatsNew)，以找到更多詳細資料。 範例是使用`[JsonProperty]`在`DescriptionFr`上述範例程式碼中的屬性。

第二個重要的是關於`Hotel`類別是公用屬性的資料類型。 .NET 類型，這些屬性對應至索引定義在其對等的欄位類型。 例如，`Category`字串屬性對應至`category`欄位，也就是類型`DataType.String`。 有類似類型之間的對應`bool?`和`DataType.Boolean`，`DateTimeOffset?`和`DataType.DateTimeOffset`等。使用記錄類型對應的特定規則`Documents.Get` [MSDN](https://msdn.microsoft.com/library/azure/dn931291.aspx)上的方法。

若要為文件中使用您自己的類別這項功能可以雙向; 運作您也可以擷取搜尋結果，並有 SDK 自動還原序列化他們您選擇的類型，如下圖所示，在[下一個文件](search-query-dotnet.md)中。

> [AZURE.NOTE] Azure 搜尋.NET SDK 也支援動態輸入文件使用`Document`類別，也就是鍵值對應的欄位名稱] 欄位的值。 您不知道索引架構在設計階段，或者可能有所不便繫結至特定的模型類別，這是非常實用。 所有的方法 SDK 中處理的文件有多載搭配使用的`Document`類別，以及強型別接受多載，一般類型參數。 僅限後者用在本文中的範例。 您可以瞭解更多關於`Document` [MSDN 上](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.document.aspx)的類別。

**重要附註的相關資料類型**

當設計您自己的模型類別對應到 Azure 搜尋索引，我們建議宣告值類型的屬性，例如`bool`和`int`可為 null (例如，`bool?`而不是`bool`)。 如果您使用的非 null 的屬性，您必須**確保**沒有任何文件索引中的包含對應的欄位的 null 值。 SDK 和 Azure 搜尋服務都不會協助您強制執行這。

這是不只是假設的問題︰ 想像位置您的新欄位新增至現有的索引類型`DataType.Int32`。 之後更新索引定義 （因為所有類型都都會 nullable Azure 搜尋） 的所有文件時，會出現的新欄位的 null 值。 如果您使用非 null，然後使用模型類別`int`該欄位的屬性，您會收到`JsonSerializationException`嘗試擷取文件時，就像這樣︰

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

因此，建議您模型類別中使用 nullable 類型，最佳做法。

## <a name="next"></a>下一步
之後填入您的 Azure 搜尋索引，就可以準備開始發出查詢搜尋文件。 如需詳細資訊，請參閱[查詢您的 Azure 搜尋索引](search-query-overview.md)。
