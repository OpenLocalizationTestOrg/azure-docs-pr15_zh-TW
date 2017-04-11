<properties
    pageTitle="建立使用.NET SDK Azure 搜尋索引 |Microsoft Azure |裝載的雲端搜尋服務"
    description="使用 Azure 搜尋.NET SDK 的程式碼中建立索引。"
    services="search"
    documentationCenter=""
    authors="brjohnstmsft"
    manager="jhubbard"
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="brjohnst"/>

# <a name="create-an-azure-search-index-using-the-net-sdk"></a>建立使用.NET SDK Azure 搜尋索引
> [AZURE.SELECTOR]
- [概觀](search-what-is-an-index.md)
- [入口網站](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [其餘](search-create-index-rest-api.md)


本文會引導您完成建立 Azure 搜尋[索引](https://msdn.microsoft.com/library/azure/dn798941.aspx)使用[Azure 搜尋.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)的程序。

追蹤本指南和之前建立索引，您應該已經[建立 Azure 搜尋服務](search-create-service-portal.md)。

請注意，在 C# 撰寫本文中的所有範例程式碼。 您可以尋找完整的來源[GitHub](http://aka.ms/search-dotnet-howto)程式碼。

## <a name="i-identify-your-azure-search-services-admin-api-key"></a>我。 找出您的 Azure 搜尋服務管理員 api 金鑰
現在您有佈建 Azure 搜尋服務，是幾乎可以發出針對您已使用.NET SDK 的 service 端點的要求。 首先，您需要取得系統 api-鍵為您佈建搜尋服務所產生的其中一項。 .NET SDK 會在每次要求傳送此 api 金鑰您的服務。 有有效的金鑰會信任，每個要求每之間傳送邀請和處理該服務的應用程式。

1. 若要尋找您的服務 api 金鑰中，您必須登入[Azure 入口網站](https://portal.azure.com/)
2. 移至您的 Azure 搜尋服務刀
3. 按一下 「 鍵 」 圖示

您的服務會有*管理員索引鍵*和*查詢鍵*。

  - 主要和次要*管理員鍵*授與所有作業，包括管理服務、 建立及刪除索引、 索引子和資料來源的功能的完整權限。 有兩個機碼，讓您可以繼續使用的第二個機碼，如果您決定要重新產生主索引鍵，反之亦然。
  - *查詢鍵*索引和文件的唯讀存取權授與，通常是分散發出搜尋要求的用戶端應用程式。

為了建立索引，您可以使用您的主要或次要管理員金鑰。

<a name="CreateSearchServiceClient"></a>
## <a name="ii-create-an-instance-of-the-searchserviceclient-class"></a>II。 建立 SearchServiceClient 類別的執行個體
若要開始使用 Azure 搜尋.NET SDK，您需要建立的執行個體`SearchServiceClient`類別。 這個類別有多個建構函式。 您想要會帶您搜尋服務的名稱和`SearchCredentials`做為參數的物件。 `SearchCredentials`換行您 api 金鑰。

下列程式碼會建立新`SearchServiceClient`使用 [搜尋服務名稱] 和 [api 金鑰儲存在應用程式的設定檔中的值 (`app.config`或`web.config`):

```csharp
string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
string adminApiKey = ConfigurationManager.AppSettings["SearchServiceAdminApiKey"];

SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
```

`SearchServiceClient`具有`Indexes`屬性。 這個屬性會提供您需要建立，清單中的所有方法更新，或刪除 Azure 搜尋索引。

> [AZURE.NOTE] `SearchServiceClient`類別來管理您的搜尋服務的連線。 為了避免出現開啟過多的連線，您應該嘗試共用一個執行個體`SearchServiceClient`應用程式中可能的話。 它的方法是安全執行緒以啟用這類共用。

<a name="DefineIndex"></a>
## <a name="iii-define-your-azure-search-index-using-the-index-class"></a>III。 定義您 Azure 搜尋索引使用`Index`類別
在單一通話`Indexes.Create`方法會建立索引。 這個方法採用做為參數`Index`定義您的 Azure 搜尋索引的物件。 您需要建立`Index`物件，並將它初始化，如下所示︰

1. 設定`Name`屬性`Index`物件索引的名稱。
2. 設定`Fields`屬性`Index`物件的陣列`Field`物件。 每個`Field`物件索引中定義的欄位行為。 您可以提供建構函式，以及資料類型 （或字串欄位分析器） 欄位的名稱。 您也可以設定其他屬性，例如`IsSearchable`，`IsFilterable`等。

請務必設計為每個索引時您搜尋的使用者體驗與企業需求請謹記`Field`必須被指派[適當的內容](https://msdn.microsoft.com/library/azure/dn798941.aspx)。 這些內容控制項的搜尋功能 （篩選、 faceting，排序全文檢索等） 套用到哪一個欄位。 您不明確設定，任何屬性`Field`課程預設為停用對應的 [搜尋] 功能，除非您特別啟用。

我們的範例中，我們已命名索引 」 旅館 」，並定義欄位，如下所示︰

```csharp
var definition = new Index()
{
    Name = "hotels",
    Fields = new[]
    {
        new Field("hotelId", DataType.String)                       { IsKey = true, IsFilterable = true },
        new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("description", DataType.String)                   { IsSearchable = true },
        new Field("description_fr", AnalyzerName.FrLucene),
        new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true, IsSortable = true },
        new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
        new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
        new Field("smokingAllowed", DataType.Boolean)               { IsFilterable = true, IsFacetable = true },
        new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
    }
};
```

我們謹慎選擇每個屬性值`Field`根據我們認為，將會使用應用程式中的方式。 例如，很可能旅館搜尋的人員將會感興趣的關鍵字上相符的項目`description`欄位中，讓我們來啟用該欄位的全文檢索搜尋設定`IsSearchable`至`true`。

請注意類型的索引中的一個欄位`DataType.String`必須指定之_索引鍵_欄位設定`IsKey`至`true`(請參閱`hotelId`上面範例中)。

上述索引定義使用的自訂語言分析器`description_fr`欄位，因為它用來儲存法文的文字。 請參閱[支援主題 MSDN 上的語言](https://msdn.microsoft.com/library/azure/dn879793.aspx)，以及相對應[的部落格文章](https://azure.microsoft.com/blog/language-support-in-azure-search/)的相關語言分析器的詳細資訊。

> [AZURE.NOTE]  請注意，藉由傳送`AnalyzerName.FrLucene`建構函式，`Field`類型的會自動`DataType.String`並會有`IsSearchable`設定為`true`。

## <a name="iv-create-the-index"></a>IV。 建立索引
您已經有初始化`Index`物件時，您可以建立索引，只要撥打`Indexes.Create`上您`SearchServiceClient`物件︰

```csharp
serviceClient.Indexes.Create(definition);
```

成功的要求，方法會以正常方式傳回。 這個方法的要求，例如無效的參數問題時，會擲回`CloudException`。

當您完成索引，並想要將其刪除時，只要撥打`Indexes.Delete`方法在您`SearchServiceClient`。 例如，這是我們想要如何刪除 「 旅館 」 索引︰

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [AZURE.NOTE] 本文中的範例程式碼使用簡單的 Azure 搜尋.NET SDK 同步的方法。 我們建議您自己的應用程式中使用非同步的方法，以將可調整和回應。 例如，在上述範例您可以使用`CreateAsync`和`DeleteAsync`而不是`Create`和`Delete`。

## <a name="next"></a>下一步
建立之後 Azure 搜尋索引，您就可以開始上傳[到索引內容](search-what-is-data-import.md)之後，您可以開始搜尋您的資料。
