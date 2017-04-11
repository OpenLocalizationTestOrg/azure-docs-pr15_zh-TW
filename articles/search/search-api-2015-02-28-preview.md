<properties
   pageTitle="Azure 搜尋服務 REST API 版本 2015年 02-28-預覽 |Microsoft Azure |Azure 搜尋預覽 API"
   description="Azure 搜尋服務 REST API 版本 2015年 02-28-預覽包含實驗的功能，例如自然語言分析器和 moreLikeThis 搜尋。"
   services="search"
   documentationCenter="na"
   authors="brjohnstmsft"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="rest-api"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="search"
   ms.date="09/07/2016"
   ms.author="brjohnst"/>

# <a name="azure-search-service-rest-api-version-2015-02-28-preview"></a>Azure 搜尋服務 REST API︰ 版本 2015年 02-28-預覽

本文是參照文件`api-version=2015-02-28-Preview`。 這個預覽延伸目前推出版本， [api 版本 = 2015年-02 28](https://msdn.microsoft.com/library/dn798935.aspx)，提供下列實驗功能︰

- `moreLikeThis`查詢中[搜尋文件](#SearchDocs)API 參數。 它會找出另一個特定的文件相關的其他文件。

一些其他部分`2015-02-28-Preview`REST API 的說明文件。 這些功能包括︰

- [計分的設定檔](search-api-scoring-profiles-2015-02-28-preview.md)
- [索引子](search-api-indexers-2015-02-28-preview.md)

Azure 搜尋服務中會有多個版本。 請參閱[搜尋服務版本設定](http://msdn.microsoft.com/library/azure/dn864560.aspx)的詳細資料。

## <a name="apis-in-this-document"></a>這份文件中的 Api

Azure 搜尋服務 API 支援兩個 URL 語法 API 作業︰ 簡單和 OData （如需詳細資訊，請參閱[支援的 OData (Azure 搜尋 API)](http://msdn.microsoft.com/library/azure/dn798932.aspx) ）。 下列清單會顯示簡單的語法。

[建立索引](#CreateIndex)

    POST /indexes?api-version=2015-02-28-Preview

[更新索引](#UpdateIndex)

    PUT /indexes/[index name]?api-version=2015-02-28-Preview

[取得索引](#GetIndex)

    GET /indexes/[index name]?api-version=2015-02-28-Preview

[列出索引](#ListIndexes)

    GET /indexes?api-version=2015-02-28-Preview

[取得索引統計資料](#GetIndexStats)

    GET /indexes/[index name]/stats?api-version=2015-02-28-Preview

[測試分析器](#TestAnalyzer)

    POST /indexes/[index name]/analyze?api-version=2015-02-28-Preview

[刪除索引](#DeleteIndex)

    DELETE /indexes/[index name]?api-version=2015-02-28-Preview

[新增，請刪除]，並更新索引中的資料](#AddOrUpdateDocuments)

    POST /indexes/[index name]/docs/index?api-version=2015-02-28-Preview

[搜尋文件](#SearchDocs)

    GET /indexes/[index name]/docs?[query parameters]
    POST /indexes/[index name]/docs/search?api-version=2015-02-28-Preview

[查閱文件](#LookupAPI)

     GET /indexes/[index name]/docs/[key]?[query parameters]

[計算文件](#CountDocs)

    GET /indexes/[index name]/docs/$count?api-version=2015-02-28-Preview

[建議](#Suggestions)

    GET /indexes/[index name]/docs/suggest?[query parameters]
    POST /indexes/[index name]/docs/suggest?api-version=2015-02-28-Preview

________________________________________
<a name="IndexOps"></a>
## <a name="index-operations"></a>索引作業

您可以建立及管理針對指定的索引資源簡單的 HTTP 要求 （文章，取得，將，刪除） 透過 Azure 搜尋服務中的索引。 若要建立索引，第一次張貼的說明索引結構描述 JSON 文件。 結構描述定義索引、 其資料類型，以及如何使用這些 （例如，在全文檢索搜尋、 篩選、 排序或 faceting） 的欄位。 也會定義計分的設定檔、 suggesters 及其他屬性設定索引的行為。

下列範例會提供說明用於在飯店搜尋與 [描述] 欄位中兩個語言所定義的結構描述。 請注意屬性如何控制功能變數的使用方式。 例如`hotelId`會用來作為文件索引鍵 (`"key": true`) 並排除全文檢索 (`"searchable": false`)。

    {
    "name": "hotels",  
    "fields": [
      {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
      {"name": "baseRate", "type": "Edm.Double"},
      {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
      {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
      {"name": "hotelName", "type": "Edm.String"},
      {"name": "category", "type": "Edm.String"},
      {"name": "tags", "type": "Collection(Edm.String)"},
      {"name": "parkingIncluded", "type": "Edm.Boolean"},
      {"name": "smokingAllowed", "type": "Edm.Boolean"},
      {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
      {"name": "rating", "type": "Edm.Int32"},
      {"name": "location", "type": "Edm.GeographyPoint"}
     ],
     "suggesters": [
      {
       "name": "sg",
       "searchMode": "analyzingInfixMatching",
       "sourceFields": ["hotelName"]
      }
     ]
    }

建立索引之後，您可以上傳填入索引的文件。 下一個步驟，請參閱[新增或更新文件](#AddOrUpdateDocuments)。

視訊簡介 Azure 搜尋編製索引作業，請參閱[Azure 搜尋的頻道 9 雲端封面片段](http://go.microsoft.com/fwlink/p/?LinkId=511509)。


<a name="CreateIndex"></a>
## <a name="create-index"></a>建立索引

索引是主索引所代表的意義的組織及搜尋文件在 Azure 搜尋，類似於表格組織資料庫中的記錄的方式。 每個索引有所有符合索引結構描述 （欄位名稱、 資料類型及屬性），但索引也可以指定其他結構 （suggesters、 計分的設定檔和 CORS 選項），定義搜尋的其他行為的文件集合。

您可以建立新的索引中使用的 HTTP 文章或放入要求 Azure 搜尋服務。 要求的內容是 JSON 結構描述，指定索引及設定的資訊。

    POST https://[service name].search.windows.net/indexes?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

或者，您可以使用放入，並指定上 URI 索引名稱。 如果索引不存在，它將會建立。

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]

建立索引決定儲存，並用於搜尋作業中的文件的結構。 填入索引是個別的作業。 此步驟中，您可以使用[索引](https://msdn.microsoft.com/library/azure/mt183328.aspx)（適用於支援的資料來源）] 或 [[新增、 更新或刪除文件](https://msdn.microsoft.com/library/azure/dn798930.aspx)的作業。 張貼文件時，會產生倒的索引。

**附註**︰ 索引允許的最大的數字會依價格層而有所不同。 免費的服務可讓您最多 3 的索引。 標準服務可讓每個搜尋服務 50 索引。 如需詳細資訊，請參閱[限制及限制](http://msdn.microsoft.com/library/azure/dn798934.aspx)。

**要求**

HTTPS 時需要所有服務要求。 您可使用方法文章或放入建構**建立索引**要求。 使用時的文章，您必須提供索引名稱以及索引結構描述定義邀請內文中。 索引名稱放入，就 URL 的一部分。 如果索引不存在，則會建立。 如果已經存在，其會更新為新的定義。

索引名稱必須小寫，開頭字母或數字、 有沒有斜線或點，而小於 128 個字元。 啟動之後索引名稱以字母或數字，其餘的名稱可以包含任何字母、 數字和虛線，只要不連續連字號。

`api-version`資格。 如需可用版本的清單，請參閱[搜尋服務版本設定](http://msdn.microsoft.com/library/azure/dn864560.aspx)。

**要求標頭**

下列清單說明必要與選用要求的標頭。

- `Content-Type`︰ 必要。 將此值設`application/json`
- `api-key`︰ 必要。 `api-key`用來
- 驗證您的搜尋服務要求。 是字串的值，為您的服務的唯一。 **建立索引**要求必須包含`api-key`頁首設為您的管理員金鑰 （而不是查詢鍵）。

您也必須服務名稱，以建構要求 URL。 您可以取得服務名稱和`api-key`從您的服務儀表板中 Azure 入口網站。 請參閱[建立 Azure 搜尋服務入口網站中](search-create-service-portal.md)的 [頁面導覽協助。

<a name="RequestData"></a>
**邀請本文語法**

邀請內文包含結構描述定義，包括送入此索引、 資料類型、 屬性，以及選擇性的計分用來在查詢時分數相符的文件的設定檔清單的文件中的資料欄位的清單。

請注意，一篇文章的要求，您必須在邀請內文中指定索引名稱。

在索引中只能有一個索引鍵欄位。 必須為字串欄位。 此欄位表示每個文件儲存在索引中的唯一識別碼。

索引的主要部分包括下列各項︰

- `name`
- `fields`會送到這個索引，包括名稱與資料類型定義動作，該欄位的內容。
- `suggesters`用於自動完成 」 或 「 預先輸入的查詢。
- `scoringProfiles`用於排名的自訂搜尋分數。 如需詳細資訊，請參閱[新增計分的設定檔](https://msdn.microsoft.com/library/azure/dn798928.aspx)。
- `analyzers``charFilters`， `tokenizers`，`tokenFilters`用來定義您的文件/查詢如何分成索引的/可搜尋的權杖。 如需詳細資訊，請參閱[Azure 搜尋中的分析](https://aka.ms//azsanalysis)。
- `defaultScoringProfile`用來覆寫預設計分行為。
- `corsOptions`若要允許十字形，查詢您的索引。

組織結構的要求裝載的語法如下所示。 進一步本主題中所提供的範例要求。

    {
      "name": (optional on PUT; required on POST) "name_of_index",
      "fields": [
        {
          "name": "name_of_field",
          "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
          "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
          "filterable": true (default) | false,
          "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
          "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
          "key": true | false (default, only Edm.String fields can be keys),
          "retrievable": true (default) | false,              
          "analyzer": "name of the analyzer used for search and indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
          "searchAnalyzer": "name of the search analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
          "indexAnalyzer": "name of the indexing analyzer" (only if 'searchAnalyzer' is set and 'analyzer' is not set)
        }
      ],
      "suggesters": [
        {
          "name": "name of suggester",
          "searchMode": "analyzingInfixMatching" (other modes may be added in the future),
          "sourceFields": ["field1", "field2", ...]
        }
      ],
      "scoringProfiles": [
        {
          "name": "name of scoring profile",
          "text": (optional, only applies to searchable fields) {
            "weights": {
              "searchable_field_name": relative_weight_value (positive numbers),
              ...
            }
          },
          "functions": (optional) [
            {
              "type": "magnitude | freshness | distance | tag",
              "boost": # (positive number used as multiplier for raw score != 1),
              "fieldName": "...",
              "interpolation": "constant | linear (default) | quadratic | logarithmic",
              "magnitude": {
                "boostingRangeStart": #,
                "boostingRangeEnd": #,
                "constantBoostBeyondRange": true | false (default)
              },
              "freshness": {
                "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
              },
              "distance": {
                "referencePointParameter": "...", (parameter to be passed in queries to use as reference location, see "scoringParameter" for syntax details)
                "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
              },
              "tag": {
                "tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field, see "scoringParameter" for syntax details)
              }
            }
          ],
          "functionAggregation": (optional, applies only when functions are specified)
            "sum (default) | average | minimum | maximum | firstMatching"
        }
      ],
      "analyzers":(optional)[ ... ],
      "charFilters":(optional)[ ... ],
      "tokenizers":(optional)[ ... ],
      "tokenFilters":(optional)[ ... ],
      "defaultScoringProfile": (optional) "...",
      "corsOptions": (optional) {
        "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
        "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
      }
    }

**索引屬性**

建立索引時，可以設定下列屬性。 如需計分及計分設定檔的詳細資訊，請參閱[新增計分的設定檔](https://msdn.microsoft.com/library/azure/dn798928.aspx)。

`name`-設定欄位的名稱。

`type`-設定欄位的資料類型。 支援的類型的清單，請參閱[支援的資料類型](#DataTypes)。

`searchable`-標記] 欄位為全文檢索搜尋能夠。 這表示它會進行分析，例如 [斷字期間編製索引作業。 如果您設定`searchable`欄位的值，例如 「 晴天日 」，其內部將分割成個別的權杖，「 晴天 」 和 「 日期 」。 這可讓這些條款的全文檢索搜尋。 類型的欄位`Edm.String`或`Collection(Edm.String)`是`searchable`預設。 其他類型的欄位不能`searchable`。

  - **附註**︰`searchable`欄位取用索引中的有額外間距，因為 Azure 搜尋會儲存在全文檢索搜尋的欄位值的其他語彙基元化的版本。 如果您想要在您索引中儲存空間，而您不需要在搜尋中所包含的欄位，設定`searchable`至`false`。

`filterable`-可讓功能變數，中的參照`$filter`查詢。 `filterable`不同於`searchable`中字串的處理方式。 類型的欄位`Edm.String`或`Collection(Edm.String)`的`filterable`並在製作比較的完全符合只沒有經過斷字。 例如，如果您設定此類欄位`f`」 晴天日 」，`$filter=f eq 'sunny'`會尋找沒有相符的項目，但`$filter=f eq 'sunny day'`會。 所有欄位都是`filterable`預設。

`sortable`依據預設系統結果就會依照排序分數，但在許多體驗使用者會想要作為排序依據的文件中的欄位。 類型的欄位`Collection(Edm.String)`無法`sortable`。 所有其他欄位都是`sortable`預設。

`facetable`-通常用於搜尋結果的簡報，包括類別 （例如，搜尋數位相機與品牌、 以百萬像素，價格等，請參閱點擊。） 叫用的次數。 這個選項不適用於類型的欄位`Edm.GeographyPoint`。 所有其他欄位都是`facetable`預設。

  - **附註**︰ 類型的欄位`Edm.String`的`filterable`， `sortable`，或`facetable`可在最 32 KB 的長度。 這是因為等欄位會被視為一個搜尋文字，並在 Azure 搜尋字詞的長度上限為 32 KB。 如果您需要在單一字串欄位中儲存於此的更多文字，您必須明確設定`filterable`， `sortable`，及`facetable`至`false`在您索引的定義。

  - **注意**︰ 如果欄位有一個上述屬性設定為 [無`true`(`searchable`， `filterable`， `sortable`，或`facetable`) 欄位有效排除倒索引。 適用於在查詢中，無法使用，但在搜尋結果所需的欄位時，此選項。 排除這類欄位索引中可改善效能。

`key`-標示為包含在索引中的文件的唯一識別碼] 欄位。 您必須選擇一個欄位為`key`欄位，而且必須是類型`Edm.String`。 索引鍵欄位，可以用於查閱直接透過[查閱 API](#LookupAPI)的文件。

`retrievable`-設定是否可以傳回搜尋結果中的欄位。  當您要使用的欄位 （例如，邊界） 做為篩選條件，排序或計分機制，但不是想顯示給使用者的欄位，這是很有用。 這個屬性必須`true`的`key`欄位。

`analyzer`-設定分析器搜尋時間和編製索引的時間，使用欄位的名稱。 允許一組值，請參閱[分析器](https://msdn.microsoft.com/library/mt605304.aspx)。 這個選項可供只能與`searchable`欄位，而且不能設定與 [`searchAnalyzer`或`indexAnalyzer`。  後選擇分析，就無法變更的欄位。

`searchAnalyzer`-設定欄位搜尋次使用分析程式的名稱。 允許一組值，請參閱[分析器](https://msdn.microsoft.com/library/mt605304.aspx)。 這個選項可供只能與`searchable`欄位。 必須將它設一起`indexAnalyzer`，而且其無法設定一起`analyzer`選項。 此分析器可以更新現有的欄位。

`indexAnalyzer`-設定編製索引時間欄位的使用分析程式的名稱。 允許一組值，請參閱[分析器](https://msdn.microsoft.com/library/mt605304.aspx)。 這個選項可供只能與`searchable`欄位。 必須將它設一起`searchAnalyzer`，而且其無法設定一起`analyzer`選項。 後選擇分析，就無法變更的欄位。

`suggesters`-設定搜尋模式和建議的內容來源的欄位。 如需詳細資訊，請參閱[Suggesters](#Suggesters) 。

`scoringProfiles`-定義的自訂計分行為，可讓您影響出現在搜尋結果較高的項目。 計分的設定檔所組成欄位粗細和函數。 如需有關使用計分的設定檔中的屬性，請參閱[新增計分設定檔](https://msdn.microsoft.com/library/azure/dn798928.aspx)。

<!-- This is a standalone topic in MSDN -->
<a name="LanguageSupport"></a>
**語言支援**

可搜尋的欄位進行分析最常見問題涉及斷字、 文字正規化並篩選出字詞。 根據預設，Azure 搜尋中的搜尋欄位會以分隔文字設定為下列[「 Unicode 文字分割 」](http://unicode.org/reports/tr29/)規則的項目[Apache Lucene 標準分析](http://lucene.apache.org/core/4_9_0/analyzers-common/index.html)分析。 此外，標準分析會將所有字元都轉換成小寫表單。 索引的文件和搜尋字詞可幫助分析期間編製索引和查詢處理。

Azure 搜尋支援各種不同的語言。 每個語言需要何種帳戶的指定語言特性的非標準文字分析。 Azure 搜尋提供兩種類型的分析器︰

- 由 Lucene 35 分析器。
- 50 分析器由專屬 Microsoft 自然語言處理使用 Office 和 Bing 的技術支援。

有些開發人員可能會想更容易記住、 簡單、 開啟來源 Lucene 的解決方案。 Lucene 分析器速度更快，但 Microsoft 分析器有進階功能，例如 lemmatization、 word decompounding （中的語言，如德文、 丹麥文、 荷蘭文、 瑞典文、 挪威文、 愛沙尼亞文、 完成、 匈牙利文、 斯洛伐克文） 和實體辨識 （Url、 電子郵件、 日期、 數字）。 如果可能的話，您應該執行 [Microsoft] 和 [Lucene 分析器決定哪一種最適的比較。

***如何比較***

英文 Lucene 分析器延伸標準分析。 移除文字中的 [所有格 （結尾的）、 適用於因依照[Porter 因演算法](http://tartarus.org/~martin/PorterStemmer/)，以及移除英文[停止文字](http://en.wikipedia.org/wiki/Stop_words)。

比較，在 Microsoft 分析程式會執行，而非因 lemmatization。 這表示它可以處理變化和不規則 word 表單更好哪些更多相關的搜尋結果 （監看式模組 7 [Azure 搜尋 MVA 簡報](http://www.microsoftvirtualacademy.com/training-courses/adding-microsoft-azure-search-to-your-websites-and-apps)的更多詳細資料） 中的結果。

使用 Microsoft 分析器編製索引作業是比其 Lucene 等語言根據慢兩到三個時間的平均值。 平均大小查詢應該不會大幅影響搜尋效能。

***設定***

對於每個索引定義中的欄位，您可以設定`analyzer`分析程式名稱，指定哪些語言和廠商的屬性。 編製索引作業，與該欄位的搜尋時，將會套用相同的分析。
例如，您可以讓存在於相同的索引中並列的英文、 法文和西班牙文旅館說明的不同欄位。 若要指定針對搜尋在查詢中的特定語言的欄位中使用['searchFields' 查詢參數](#SearchQueryParameters)。 您可以檢閱包含的查詢範例`analyzer`中[搜尋文件](#SearchDocs)的屬性。 

***分析程式清單***

以下是支援的語言與 Lucene 與 Microsoft 分析器名稱的清單。

<table style="font-size:12">
    <tr>
        <th>語言</th>
        <th>Microsoft 分析程式名稱</th>
        <th>Lucene 分析程式名稱</th>
    </tr>
    <tr>
        <td>阿拉伯文</td>
        <td>ar.microsoft</td>
        <td>ar.lucene</td>      
    </tr>
    <tr>
        <td>亞美尼亞文</td>
        <td></td>
        <td>hy.lucene</td>
    </tr>
    <tr>
        <td>孟加拉文</td>
        <td>bn.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>巴斯克文</td>
        <td></td>
        <td>eu.lucene</td>
    </tr>
    <tr>
        <td>保加利亞文</td>
        <td>bg.microsoft</td>
        <td>bg.lucene</td>
    </tr>
    <tr>
        <td>卡達隆尼亞</td>
        <td>ca.microsoft</td>
        <td>ca.lucene</td>          
    </tr>
    <tr>
        <td>簡體中文</td>
        <td>曆法 Hans.microsoft</td>
        <td>曆法 Hans.lucene</td>     
    </tr>
    <tr>
        <td>繁體中文</td>
        <td>曆法 Hant.microsoft</td>
        <td>曆法 Hant.lucene</td>     
    <tr>
    <tr>
        <td>克羅埃西亞文</td>
        <td>hr.microsoft</td>
        <td/></td>
    </tr>
    <tr>
        <td>捷克</td>
        <td>cs.microsoft</td>
        <td>cs.lucene</td>      
    </tr>    
    <tr>
        <td>丹麥文</td>
        <td>da.microsoft</td>
        <td>da.lucene</td>      
    </tr>    
    <tr>
        <td>荷蘭文</td>
        <td>nl.microsoft</td>
        <td>nl.lucene</td>  
    </tr>    
    <tr>
        <td>英文</td>        
        <td>en.microsoft</td>
        <td>en.lucene</td>      
    </tr>
    <tr>
        <td>愛沙尼亞文</td>
        <td>et.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>芬蘭文</td>
        <td>fi.microsoft</td>
        <td>fi.lucene</td>      
    </tr>    
    <tr>
        <td>法文</td>
        <td>fr.microsoft</td>
        <td>fr.lucene</td>      
    </tr>
    <tr>
        <td>Galician</td>
        <td></td>
        <td>gl.lucene</td>      
    </tr>
    <tr>
        <td>德文</td>
        <td>de.microsoft</td>
        <td>de.lucene</td>      
    </tr>
    <tr>
        <td>希臘文</td>
        <td>el.microsoft</td>
        <td>el.lucene</td>      
    </tr>
    <tr>
        <td>古吉拉特文</td>
        <td>gu.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>希伯來文</td>
        <td>he.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>印度文</td>
        <td>hi.microsoft</td>
        <td>hi.lucene</td>      
    </tr>
    <tr>
        <td>匈牙利文</td>      
        <td>hu.microsoft</td>
        <td>hu.lucene</td>
    </tr>
    <tr>
        <td>冰島文</td>
        <td>is.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>印尼文 （馬來文）</td>
        <td>id.microsoft</td>
        <td>id.lucene</td>      
    </tr>
    <tr>
        <td>愛爾蘭文</td>
        <td></td>
        <td>ga.lucene</td>
    </tr>
    <tr>
        <td>義大利文</td>
        <td>it.microsoft</td>
        <td>it.lucene</td>      
    </tr>
    <tr>
        <td>日文</td>
        <td>ja.microsoft</td>
        <td>ja.lucene</td>
        
    </tr>
    <tr>
        <td>坎那達文</td>
        <td>ka.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>韓文</td>
        <td>ko.microsoft</td>
        <td>ko.lucene</td>
    </tr>
    <tr>
        <td>拉脫維亞文</td>        
        <td>lv.microsoft</td>
        <td>lv.lucene</td>  
    </tr>
    <tr>
        <td>立陶宛文</td>
        <td>lt.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>馬來亞拉姆文</td>
        <td>ml.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>馬來文 （拉丁）</td>
        <td>ms.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>馬拉提文</td>
        <td>mr.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>挪威文</td>
        <td>nb.microsoft</td>
        <td>no.lucene</td>      
    </tr>
    <tr>
        <td>波斯</td>
        <td></td>
        <td>fa.lucene</td>      
    </tr>
    <tr>
        <td>波蘭文</td>
        <td>pl.microsoft</td>
        <td>pl.lucene</td>      
    </tr>
    <tr>
        <td>葡萄牙文 （巴西）</td>
        <td>pt Br.microsoft</td>
        <td>pt Br.lucene</td>       
    </tr>
    <tr>
        <td>葡萄牙文 （葡萄牙）</td>
        <td>pt Pt.microsoft</td>        
        <td>pt Pt.lucene</td>
    </tr>
    <tr>
        <td>旁遮普文</td>
        <td>pa.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>羅馬尼亞文</td>
        <td>ro.microsoft</td>
        <td>ro.lucene</td>
    </tr>
    <tr>
        <td>俄文</td>
        <td>ru.microsoft</td>
        <td>ru.lucene</td>  
    </tr>
    <tr>
        <td>塞爾維亞文 （斯拉夫）</td>
        <td>sr cyrillic.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>塞爾維亞文 （拉丁）</td>
        <td>sr latin.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>斯洛伐克文</td>
        <td>sk.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>斯洛維尼亞文</td>
        <td>sl.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>西班牙文</td>
        <td>es.microsoft</td>
        <td>es.lucene</td>
    </tr>
    <tr>
        <td>瑞典文</td>
        <td>sv.microsoft</td>
        <td>sv.lucene</td>
    </tr>

    <tr>
        <td>坦米爾文</td>
        <td>ta.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>特拉古文</td>
        <td>te.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>泰文</td>
        <td>th.microsoft</td>
        <td>th.lucene</td>
    </tr>
    <tr>
        <td>土耳其文</td>
        <td>tr.microsoft</td>
        <td>tr.lucene</td>      
    </tr>
    <tr>
        <td>烏克蘭文</td>
        <td>uk.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>烏都文</td>
        <td>your.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>越南文</td>
        <td>vi.microsoft</td>
        <td></td>
    </tr>
    <td colspan="3">此外 Azure 搜尋提供適用於多種語言的分析程式設定</td>
    <tr>
        <td>標準 ASCII 摺疊</td>
        <td>standardasciifolding.lucene</td>
        <td>
        <ul>
            <li>Unicode 文字分割 (標準 Tokenizer)</li>
            <li>ASCII 摺疊篩選-將轉換到其 ASCII 對應項目不屬於組的第一次 127 ASCII 字元的 Unicode 字元。 這是很適合用於移除讀音符號。</li>
        </ul>
        </td>
    </tr>
</table>

[Apache Lucene 語言分析器](http://lucene.apache.org/core/4_9_0/analyzers-common/overview-summary.html)被可所有分析器<i>lucene</i>將附註的名稱。 您可以找到有關摺疊篩選 ASCII[以下](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)。

**Suggesters**

A`suggester`定義欄位索引中的用來支援在搜尋中自動完成。 通常部分搜尋字串會傳送到[建議 API](#Suggestions) ，使用者會輸入搜尋查詢，而 API 會傳回一組建議文句。 您在索引中定義 suggester 會決定哪些變數用來建立預先輸入搜尋字詞。 設定的詳細資訊，請參閱[Suggesters](#Suggesters) 。

**計分的設定檔**

A`scoringProfile`定義的自訂計分行為，可讓您影響出現在搜尋結果較高的項目。 計分的設定檔所組成欄位粗細和函數。 若要使用它們，您可以指定設定檔由上查詢字串的名稱。

計分的設定檔的預設值的運作方式的詳細說明，來計算每個項目的結果集中搜尋分數。 您可以使用內部未命名的計分的設定檔。 或者，設定`defaultScoringProfile`為預設值，使用自訂設定檔叫用在查詢字串未指定自訂的設定檔時。

如需詳細資訊，請參閱[新增計分設定檔] 的搜尋索引 (Azure 搜尋服務 REST API)](search-api-scoring-profiles-2015-02-28-preview.md) 。

**CORS 選項**

用戶端 Javascript 無法呼叫任何 Api，根據預設，因為瀏覽器會防止所有的十字形，要求。 設定來啟用 CORS （十字形，資源共用）`corsOptions`屬性，以允許十字形，查詢您的索引。 請注意只查詢 Api 支援 CORS 基於安全性理由。 CORS 可以設定下列選項︰

- `allowedOrigins`（必填）︰ 這是授與存取您的索引的來源清單。 從這些來源的任何 Javascript 程式碼服務這表示將允許來查詢您的索引 （假設它提供正確的 API 金鑰）。 每個原點通常是表單的`protocol://fully-qualified-domain-name:port`雖然通常省略連接埠。 請參閱[這篇文章](http://go.microsoft.com/fwlink/?LinkId=330822)︰ 如需詳細資訊。
 - 如果您想要允許存取所有的來源，包括`*`中單一項目`allowedOrigins`陣列。 請注意，**建議您不要生產搜尋服務的練習。** 不過，可能很適合用於開發或偵錯。
- `maxAgeInSeconds`（選用）︰ 瀏覽器使用這個值來判斷的活動 （秒） 快取 CORS 預先回應。 必須為非負整數。 這個值是較大，會更佳的效能，但愈長 CORS 原則變更才會生效。 如果沒有設定，將會使用預設工期為 5 分鐘。

<a name="CreateUpdateIndexExample"></a>
**邀請本文範例**

    {
      "name": "hotels",  
      "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String"},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean"},
        {"name": "smokingAllowed", "type": "Edm.Boolean"},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
      ],
      "suggesters": [
        {
          "name": "sg",
          "searchMode": "analyzingInfixMatching",
          "sourceFields": ["hotelName"]
        }
      ]
    }

**回應**

成功的要求: 「 201 已建立 」。

依預設回應內容會包含索引定義所建立的 JSON。 如果`Prefer`要求標頭設定為 [`return=minimal`會是空的回應內容與成功狀態碼會出現 「 204 任何內容 」，而不是 「 201 已建立 」。 這是則為 true，不論是否將或文章用來建立索引。

**註解**

目前為有限的支援索引結構描述更新。 目前不支援任何需要變更欄位類型，例如重新編製索引的結構描述更新。 雖然您無法變更現有欄位或已刪除的新欄位可以隨時新增至現有的索引。 當新增新的欄位時，在索引中的所有現有文件會自動提供該欄位的 null 值。 會使用沒有額外的儲存空間，直到新文件會新增至索引。

<a name="Suggesters"></a>
## <a name="suggesters"></a>Suggesters

Azure 搜尋建議功能是預先輸入，或按一下 [自動完成] 查詢功能，提供可能的搜尋字詞，在搜尋方塊中輸入部分的字串輸入回應的清單。 您可能會查詢建議使用商業 web 搜尋引擎時︰ 在使用 Bing 輸入 「.NET 」，產生的條款清單 」.NET 4.5 」 「.NET Framework 3.5 」 等等。 使用搜尋服務 REST API 時, 實作自訂 Azure 搜尋應用程式中的 [建議的需求如下︰

- 啟用建議新增**suggester**建築索引中, 提供的名稱、 搜尋模式與預先輸入叫用的欄位的清單。 例如，如果您指定 「 cityName 」 作為來源] 欄位中，輸入 「 海 」 的部分搜尋字串會導致 「 西雅圖 」、 「 Seaside 」，並 「 Seatac 」 （三個實際縣/市名稱） 為 [查詢建議提供給使用者。

- 呼叫 [應用程式碼中的 [[建議 API](#Suggestions)叫用的建議。 通常部分搜尋字串會傳送到服務中，使用者會輸入搜尋查詢，而此 API 會傳回一組建議文句。

本文說明如何設定**suggester**。 您也應該檢閱 suggester 的使用方式的詳細資料的[建議 API](#Suggestions) 。

**使用方式**

`Suggesters`建立索引及工作用於建議特定的文件，而非寬鬆的字詞或片語。 最佳候選欄位是標題、 名稱，以及其他簡要片語可以識別項目。 效果不重複的欄位，例如類別及標籤] 或很長的欄位，例如 [描述] 或 [註解欄位。

做為索引定義的一部分，您可以新增至單一 suggester`suggesters`集合。 定義 suggester 的內容，包括下列各項︰

- `name`: Suggester 名稱。 呼叫時，使用名稱 suggester `suggest` API。
- `searchMode`︰ 用來搜尋候選片語策略。 僅限目前支援的模式是`analyzingInfixMatching`，會執行有彈性相符的片語的開頭或中間句子。
- `sourceFields`︰ 建議的內容來源的一或多個欄位清單。 僅限類型的欄位`Edm.String`和`Collection(Edm.String)`可能會建議的來源。 可以使用沒有設定自訂語言分析器的欄位。

**Suggester 範例**

Suggester 是索引的一部分。 只有一個 suggester 可以存在於`suggesters`集合中目前的版本，同時欄位集合及`scoringProfiles`。

        {
          "name": "hotels",
          "fields": [
             . . .
           ],
          "suggesters": [
            {
            "name": "sg",
            "searchMode": "analyzingInfixMatching",
            "sourceFields: ["hotelName", "category"]
            }
          ],
          "scoringProfiles": [
             . . .
          ]
        }

> [AZURE.NOTE]  如果您使用的 Azure 搜尋公用預覽版本`suggesters`取代舊的布林值屬性 (`"suggestions": false`) 的只支援首碼建議的簡短字串 （3 25 個字元）。 其取代， `suggesters`，支援 infix 相符的開頭或中間欄位的內容，找到符合的項目與更好的搜尋字串中的錯誤。 開始使用推出的版本，這是現在只建議 API 實作。 舊版`suggestions`屬性中所`api-version=2014-07-31-Preview`仍會繼續執行該版本，但不是在操作`2015-02-28`或更新版本的 Azure 搜尋。

<a name="UpdateIndex"></a>
## <a name="update-index"></a>更新索引

您可以更新現有的索引中 Azure 搜尋使用 HTTP 保留要求。 將新欄位新增到現有的結構描述、 修改 CORS 選項]，並修改計分的設定檔，可以包含更新。 如需詳細資訊，請參閱[新增計分設定檔](https://msdn.microsoft.com/library/azure/dn798928.aspx)。 您指定為要求 URI 上更新索引的名稱︰

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**重要︰**支援索引結構描述更新僅限於不需要重建搜尋索引的作業。 目前不支援任何需要重新編製索引，例如變更欄位類型的結構描述更新。 雖然無法變更或刪除現有的欄位，可以隨時新增新的欄位。 相同適用於`suggesters`。 新欄位可能會新增至 suggester 同時新增欄位，但無法移除欄位`suggesters`和現有的欄位不會新增至`suggesters`。

新增索引的新欄位，在索引中的所有現有文件會自動有該欄位的 null 值。 會使用沒有額外的儲存空間，直到新文件會新增至索引。

**要求**

HTTPS 時需要所有服務要求。 **更新索引**要求建構使用 HTTP 放置。 索引名稱放入，就 URL 的一部分。 如果索引不存在，則會建立。 如果已經存在的索引，其會更新為新的定義。

索引名稱必須小寫，開頭字母或數字、 有沒有斜線或點，而小於 128 個字元。 啟動之後索引名稱以字母或數字，其餘的名稱可以包含任何字母、 數字和虛線，只要不連續連字號。

`api-version=[string]`（必要）。 預覽版本`api-version=2015-02-28-Preview`。 請參閱[搜尋服務版本設定](http://msdn.microsoft.com/library/azure/dn864560.aspx)詳細資料] 與 [替代的版本。

**要求標頭**

下列清單說明必要與選用要求的標頭。

- `Content-Type`︰ 必要。 將此值設`application/json`
- `api-key`︰ 必要。 `api-key`會用於驗證您的搜尋服務要求。 是字串的值，為您的服務的唯一。 **更新索引**要求必須包含`api-key`頁首設為您的管理員金鑰 （而不是查詢鍵）。

您也必須服務名稱，以建構要求 URL。 您可以取得服務名稱和`api-key`從您的服務儀表板中 Azure 入口網站。 請參閱[建立 Azure 搜尋服務入口網站中](search-create-service-portal.md)的 [頁面導覽協助。

**邀請本文語法**

更新現有的索引，本文必須包括原始的結構描述定義，以及您要新增，新的欄位，以及修改計分設定檔，suggesters 及 CORS 選項]，如果有的話。 如果您不修改計分的設定檔和 CORS 選項，您必須包含從建立索引的原始圖片。 在 [一般用更新的最佳模式是擷取索引定義，以取得、 修改，，然後放入更新。

用來建立索引的結構描述語法會重製以下方便時觀看。 如需詳細資訊，請參閱[建立索引](#CreateIndex)。

    {
      "name": (optional) "name_of_index",
      "fields": [
        {
          "name": "name_of_field",
          "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
          "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
          "filterable": true (default) | false,
          "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
          "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
          "key": true | false (default, only Edm.String fields can be keys),
          "retrievable": true (default) | false, 
          "analyzer": "name of the analyzer used for search and indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
          "searchAnalyzer": "name of the search analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
          "indexAnalyzer": "name of the indexing analyzer" (only if 'searchAnalyzer' is set and 'analyzer' is not set)
        }
      ],
      "suggesters": [
        {
          "name": "name of suggester",
          "searchMode": "analyzingInfixMatching" (other modes may be added in the future),
          "sourceFields": ["field1", "field2", ...]
        }
      ],
      "scoringProfiles": [
        {
          "name": "name of scoring profile",
          "text": (optional, only applies to searchable fields) {
            "weights": {
              "searchable_field_name": relative_weight_value (positive numbers),
              ...
            }
          },
          "functions": (optional) [
            {
              "type": "magnitude | freshness | distance | tag",
              "boost": # (positive number used as multiplier for raw score != 1),
              "fieldName": "...",
              "interpolation": "constant | linear (default) | quadratic | logarithmic",
              "magnitude": {
                "boostingRangeStart": #,
                "boostingRangeEnd": #,
                "constantBoostBeyondRange": true | false (default)
              },
              "freshness": {
                "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
              },
              "distance": {
                "referencePointParameter": "...", (parameter to be passed in queries to use as reference location, see "scoringParameter" for syntax details)
                "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
              },
              "tag": {
                "tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field, see "scoringParameter" for syntax details)
              }
            }
          ],
          "functionAggregation": (optional, applies only when functions are specified)
            "sum (default) | average | minimum | maximum | firstMatching"
        }
      ],
      "analyzers":(optional)[ ... ],
      "charFilters":(optional)[ ... ],
      "tokenizers":(optional)[ ... ],
      "tokenFilters":(optional)[ ... ],
      "defaultScoringProfile": (optional) "...",
      "corsOptions": (optional) {
        "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
        "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
      }
    }


**回應**

成功的要求: 「 204 沒有內容]。

依預設回應內容會是空的。 不過，如果`Prefer`要求標頭設定為 [ `return=representation`，回應內容會包含索引定義更新 JSON。 成功狀態碼將會在此情況下，「 200 [確定]。

**更新索引定義與自訂分析器**

一旦定義分析、 tokenizer、 token 篩選或 char 篩選後，您無法進行修改。 新的文件可以新增至現有的索引，只有當`allowIndexDowntime`旗標設定為 [索引更新邀請中，則為 true: 

`PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true`

請注意，這項作業，將會放置至少幾秒鐘，導致您編製索引和失敗的查詢要求您離線的索引。 索引的效能和寫入可用性可以是數分鐘後更新索引，效能或更長的時間很大的索引。

<a name="ListIndexes"></a>
## <a name="list-indexes"></a>清單的索引

**清單索引**作業傳回您 Azure 搜尋服務中目前索引的清單。

    GET https://[service name].search.windows.net/indexes?api-version=[api-version]
    api-key: [admin key]

**要求**

HTTPS 時需要所有服務要求。 您可使用取得方法建構**清單索引**要求。

`api-version=[string]`（必要）。 預覽版本`api-version=2015-02-28-Preview`。 請參閱[搜尋服務版本設定](http://msdn.microsoft.com/library/azure/dn864560.aspx)詳細資料] 與 [替代的版本。

**要求標頭**

下列清單說明必要與選用要求的標頭。

- `api-key`︰ 必要。 `api-key`會用於驗證您的搜尋服務要求。 是字串的值，為您的服務的唯一。 **清單索引**要求必須包含`api-key`設定管理員金鑰 （而不是查詢鍵）。

您也必須服務名稱，以建構要求 URL。 您可以取得服務名稱和`api-key`從您的服務儀表板中 Azure 入口網站。 請參閱[建立 Azure 搜尋服務入口網站中](search-create-service-portal.md)的 [頁面導覽協助。

**邀請內文**

無。

**回應**

狀態碼︰ 200 確定會傳回成功的回應。

以下是範例回應內容︰

    {
      "value": [
        {
          "name": "Books",
          "fields": [
            {"name": "ISBN", ...},
            ...
          ]
        },
        {
          "name": "Games",
          ...
        },
        ...
      ]
    }

請注意，您也可以篩選下您感興趣屬性的回應。 例如，若要只索引名稱清單，請使用 OData`$select`查詢選項︰

    GET /indexes?api-version=2015-02-28-Preview&$select=name

在此情況下，從上述範例回應會出現，如下所示︰

    {
      "value": [
        {"name": "Books"},
        {"name": "Games"},
        ...
      ]
    }

這是很有用的技巧，若要儲存的頻寬，如果您有許多索引，在您的搜尋服務。

<a name="GetIndex"></a>
## <a name="get-index"></a>取得索引

**取得索引**作業取得 Azure 搜尋索引定義。

    GET https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]

**要求**

需要服務要求的 HTTPS。 您可使用取得方法建構**取得索引**要求。

[索引名稱] 中要求 URI 指定要傳回索引集合中的索引。

`api-version=[string]`（必要）。 預覽版本`api-version=2015-02-28-Preview`。 請參閱[搜尋服務版本設定](http://msdn.microsoft.com/library/azure/dn864560.aspx)詳細資料] 與 [替代的版本。

**要求標頭**

下列清單說明必要與選用要求的標頭。

- `api-key`:`api-key`會用於驗證您的搜尋服務要求。 是字串的值，為您的服務的唯一。 **取得索引**要求必須包含`api-key`設定管理員金鑰 （而不是查詢鍵）。

您也必須服務名稱，以建構要求 URL。 您可以取得服務名稱和`api-key`從您的服務儀表板中 Azure 入口網站。 請參閱[建立 Azure 搜尋服務入口網站中](search-create-service-portal.md)的 [頁面導覽協助。

**邀請內文**

無。

**回應**

狀態碼︰ 200 確定會傳回成功的回應。

請參閱範例 JSON 中[建立及更新索引](#CreateUpdateIndexExample)的回應裝載的範例。

<a name="DeleteIndex"></a>
## <a name="delete-index"></a>刪除索引

**刪除索引**作業會移除您 Azure 搜尋服務索引和相關聯的文件。 從 Azure] 入口網站中的服務儀表板或 API，您可以取得索引名稱。 如需詳細資訊，請參閱[清單索引](#ListIndexes)。

    DELETE https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]

**要求**

需要服務要求的 HTTPS。 您可使用 [刪除] 方法建構**刪除索引**要求。

[索引名稱] 中要求 URI 指定的索引，若要刪除索引集合。

`api-version=[string]`（必要）。 預覽版本`api-version=2015-02-28-Preview`。 請參閱[搜尋服務版本設定](http://msdn.microsoft.com/library/azure/dn864560.aspx)詳細資料] 與 [替代的版本。

**要求標頭**

下列清單說明必要與選用要求的標頭。

- `api-key`︰ 必要。 `api-key`會用於驗證您的搜尋服務要求。 它是字串值，唯一您服務的 URL。 **刪除索引**要求必須包含`api-key`頁首設為您的管理員金鑰 （而不是查詢鍵）。

您也必須服務名稱，以建構要求 URL。 您可以取得服務名稱和`api-key`從您的服務儀表板中 Azure 入口網站。 請參閱[建立 Azure 搜尋服務入口網站中](search-create-service-portal.md)的 [頁面導覽協助。

**邀請內文**

無。

**回應**

狀態碼︰ 204 沒有內容會傳回成功的回應。

<a name="GetIndexStats"></a>
## <a name="get-index-statistics"></a>取得索引統計資料

**取得索引統計資料**作業會傳回從 Azure 搜尋文件計數目前的索引，以及儲存空間的使用方式。

    GET https://[service name].search.windows.net/indexes/[index name]/stats?api-version=[api-version]
    api-key: [admin key]

> [AZURE.NOTE] 文件計數和儲存空間大小的統計資料收集每隔幾分鐘，不在即時。 因此，此 API 所傳回的統計資料可能不會反映的變更因最近編製索引作業。

**要求**

需要的所有服務要求 HTTPS。 您可使用取得方法建構**取得索引統計資料**要求。

[索引名稱] 中要求 URI 會告訴傳回指定索引的索引統計資料服務。

`api-version=[string]`（必要）。 預覽版本`api-version=2015-02-28-Preview`。 請參閱[搜尋服務版本設定](http://msdn.microsoft.com/library/azure/dn864560.aspx)詳細資料] 與 [替代的版本。


**要求標頭**

下列清單說明必要與選用要求的標頭。

- `api-key`:`api-key`會用於驗證您的搜尋服務要求。 是字串的值，為您的服務的唯一。 **取得索引統計資料**要求必須包含`api-key`設定管理員金鑰 （而不是查詢鍵）。

您也必須服務名稱，以建構要求 URL。 您可以取得服務名稱和`api-key`從您的服務儀表板中 Azure 入口網站。 請參閱[建立 Azure 搜尋服務入口網站中](search-create-service-portal.md)的 [頁面導覽協助。

**邀請內文**

無。

**回應**

狀態碼︰ 200 確定會傳回成功的回應。

回應內容是以下列格式︰

    {
      "documentCount": number,
      "storageSize": number (size of the index in bytes)
    }

<a name="TestAnalyzer"></a>
## <a name="test-analyzer"></a>測試分析器

**分析 API**顯示分析如何文字分成權杖。

    POST https://[service name].search.windows.net/indexes/[index name]/analyze?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**要求**

需要的所有服務要求 HTTPS。 您可使用 POST 方法建構**分析 API**邀請。

`api-version=[string]`（必要）。 預覽版本`api-version=2015-02-28-Preview`。 請參閱[搜尋服務版本設定](http://msdn.microsoft.com/library/azure/dn864560.aspx)詳細資料] 與 [替代的版本。


**要求標頭**

下列清單說明必要與選用要求的標頭。

- `api-key`:`api-key`會用於驗證您的搜尋服務要求。 是字串的值，為您的服務的唯一。 **分析 API**邀請必須包含`api-key`設定管理員金鑰 （而不是查詢鍵）。

您也必須索引名稱與服務名稱，以建構要求 URL。 您可以取得服務名稱和`api-key`從您的服務儀表板中 Azure 入口網站。 請參閱[建立 Azure 搜尋服務入口網站中](search-create-service-portal.md)的 [頁面導覽協助。

**邀請內文**

    {
      "text": "Text to analyze",
      "analyzer": "analyzer_name"
    }

或

    {
      "text": "Text to analyze",
      "tokenizer": "tokenizer_name",
      "tokenFilters": (optional) [ "token_filter_name" ],
      "charFilters": (optional) [ "char_filter_name" ]
    }

`analyzer_name`， `tokenizer_name`，`token_filter_name`和`char_filter_name`必須有效的預先定義或自訂分析器、 tokenizers、 token 篩選和 char 篩選的索引的名稱。 若要瞭解關於詞彙分析的程序的詳細資訊請參閱[Azure 搜尋中的分析](https://aka.ms/azsanalysis)。

**回應**

狀態碼︰ 200 確定會傳回成功的回應。

回應內容是以下列格式︰

    {
      "tokens": [
        {
          "token": string (token),
          "startOffset": number (index of the first character of the token),
          "endOffset": number (index of the last character of the token),
          "position": number (position of the token in the input text)
        },
        ...
      ]
    }

**分析 API 範例**

**要求**

    {
      "text": "Text to analyze",
      "analyzer": "standard"
    }

**回應**

    {
      "tokens": [
        {
          "token": "text",
          "startOffset": 0,
          "endOffset": 4,
          "position": 0
        },
        {
          "token": "to",
          "startOffset": 5,
          "endOffset": 7,
          "position": 1
        },
        {
          "token": "analyze",
          "startOffset": 8,
          "endOffset": 15,
          "position": 2
        }
      ]
    }

________________________________________
<a name="DocOps"></a>
## <a name="document-operations"></a>文件作業

在 Azure 搜尋索引會儲存在雲端，然後填入使用 JSON 您上傳至服務的文件。 您上傳的所有文件組成搜尋資料的中華。 文件包含其中一些 token 化成搜尋字詞為其上傳的欄位。 `/docs` Azure 搜尋 API 的 URL 區段代表索引中的文件集合。 執行的集合，例如 [上傳的所有作業，合併、 刪除或查詢文件記錄都將單一索引的內容中，因此 Url 的這些作業會先`/indexes/[index name]/docs`指定的索引名稱。

應用程式碼必須產生 JSON 文件上傳至 Azure 搜尋，或您可以使用 [[重新](https://msdn.microsoft.com/library/dn946891.aspx)載入資料來源時 Azure SQL 資料庫或 DocumentDB 的文件。 一般而言，您所提供的單一資料集要填入的索引。

您應該計劃有一份文件的每個您想要搜尋的項目。 電影租賃應用程式可能會有影片每一份文件、 店面應用程式可能會有 SKU 每一份文件、 線上教學軟體應用程式可能會有課程每一份文件、 研究公司可能會在其存放庫中，有一份文件的每個學術紙張等等。

文件包含一個或多個欄位。 欄位是由 Azure 搜尋 token 化的搜尋字詞]，將，以及非 token 化的文字或可用於篩選] 或 [計分的設定檔的非文字值。 名稱、 資料類型及支援的每個欄位的搜尋功能取決於索引結構描述。 每個索引結構描述中的欄位必須指定為識別碼，而且每個文件必須擁有唯一識別文件索引中的 [識別碼] 欄位的值。 所有其他的文件欄位是選擇性的如果未指定的左側會預設為 null 值。 請注意，null 值不會在搜尋索引中的空間。

您可以上傳文件之前，您必須已經建立索引服務。 第一個步驟的詳細資料，請參閱[建立索引](#CreateIndex)。

<a name="AddOrUpdateDocuments"></a>
## <a name="add-update-or-delete-documents"></a>新增、 更新或刪除文件

您可以上傳，使用 HTTP 文章指定索引的合併列印]、 [合併列印或上傳或 [刪除文件。 大量更新，批次最多 （1000年文件每一批次中的） 或每個批次約 16 MB 的文件的建議。

    POST https://[service name].search.windows.net/indexes/[index name]/docs/index?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**要求**

HTTPS 時需要所有服務要求。 您可以上傳，使用 HTTP 文章指定索引的合併列印]、 [合併列印或上傳或 [刪除文件。

要求 URI 包含 [索引名稱] 中，指定要張貼文件的索引。 您可以只將文件張貼至一個索引一次。

`api-version=[string]`（必要）。 預覽版本`api-version=2015-02-28-Preview`。 請參閱[搜尋服務版本設定](http://msdn.microsoft.com/library/azure/dn864560.aspx)詳細資料] 與 [替代的版本。

**要求標頭**

下列清單說明必要與選用要求的標頭。

- `Content-Type`︰ 必要。 將此值設`application/json`
- `api-key`︰ 必要。 `api-key`會用於驗證您的搜尋服務要求。 是字串的值，為您的服務的唯一。 **新增文件**要求必須包含`api-key`頁首設為您的管理員金鑰 （而不是查詢鍵）。

您也必須服務名稱，以建構要求 URL。 您可以取得服務名稱和`api-key`從您的服務儀表板中 Azure 入口網站。 請參閱[建立 Azure 搜尋服務入口網站中](search-create-service-portal.md)的 [頁面導覽協助。

**邀請內文**

邀請內文包含能編製索引的一個或多個文件。 文件是以唯一識別。 每個文件是與動作相關聯︰ 上傳、 合併、 mergeOrUpload 或刪除。 上傳要求必須包含文件資料作為一組索引鍵/值組。

    {
      "value": [
        {
          "@search.action": "upload (default) | merge | mergeOrUpload | delete",
          "key_field_name": "unique_key_of_document", (key/value pair for key field from index schema)
          "field_name": field_value (key/value pairs matching index schema)
            ...
        },
        ...
      ]
    }

> [AZURE.NOTE] 文件索引鍵只可以包含字母、 數字、 虛線 (「-」)，底線 (「 _ 」) 和等號 （「 = 」）。 如需詳細資訊，請參閱[命名規則](https://msdn.microsoft.com/library/azure/dn857353.aspx)。

**文件動作**

- `upload`︰ 如果是新增及更新/取代已經存在插入文件位置上傳動作是類似 「 了 」。 注意︰ 所有欄位會都取代更新的大小寫。
- `merge`︰ 合併指定的欄位，以更新現有的文件。 如果文件不存在，將會失敗合併列印。 您指定合併列印中的任何欄位，將會取代現有的欄位，在文件中。 這包含類型的欄位`Collection(Edm.String)`。 例如，如果文件中包含的欄位值的 「 標籤 」`["budget"]`和執行合併值`["economy", "pool"]`「 標籤 」 欄位的最後一個值為 「 標籤 」， `["economy", "pool"]`。 它會**不**會`["budget", "economy", "pool"]`。
- `mergeOrUpload`︰ 類似`merge`如果索引中已存在文件的指定索引鍵。 如果文件不存在類似`upload`新的文件。
- `delete`︰ 刪除索引中移除指定的文件。 請注意，任何欄位您指定在`delete`會忽略以外的索引鍵欄位的作業。 如果您想要移除文件中的個別功能變數時，使用`merge`改為並直接將欄位設定明確至`null`。

**回應**

狀態碼 200 （確定） 會傳回成功的回應，這表示已成功索引的所有項目。 這會由`status`屬性設定為 true 的所有項目，以及`statusCode`201 （適用於新上傳文件） 或 （適用於合併或刪除文件） 200 設定屬性︰

    {
      "value": [
        {
          "key": "unique_key_of_new_document",
          "status": true,
          "errorMessage": null,
          "statusCode": 201
        },
        {
          "key": "unique_key_of_merged_document",
          "status": true,
          "errorMessage": null,
          "statusCode": 200
        },
        {
          "key": "unique_key_of_deleted_document",
          "status": true,
          "errorMessage": null,
          "statusCode": 200
        }
      ]
    }  

不成功索引至少一個項目時，會傳回狀態碼 207 （多狀態）。 有未索引的項目`status`欄位設為 false。 `errorMessage`和`statusCode`屬性會指出編製索引發生錯誤的原因︰

    {
      "value": [
        {
          "key": "unique_key_of_document_1",
          "status": false,
          "errorMessage": "The search service is too busy to process this document. Please try again later.",
          "statusCode": 503
        },
        {
          "key": "unique_key_of_document_2",
          "status": false,
          "errorMessage": "Document not found.",
          "statusCode": 404
        },
        {
          "key": "unique_key_of_document_3",
          "status": false,
          "errorMessage": "Index is temporarily unavailable because it was updated with the 'allowIndexDowntime' flag set to 'true'. Please try again later.",
          "statusCode": 422
        }
      ]
    }  

下表說明可以在回應中傳回的各種每個文件狀態碼。 注意︰ 部分指出要求本身的問題，而其他人表示暫時錯誤狀況。 第二的延遲後請重新嘗試。

<table style="font-size:12">
    <tr>
        <th>狀態碼</th>
        <th>意義</th>
        <th>重</th>
        <th>備忘稿</th>
    </tr>
    <tr>
        <td>200</td>
        <td>文件已成功修改或刪除。</td>
        <td>n/a</td>
        <td>刪除作業是<a href="https://en.wikipedia.org/wiki/Idempotence">冪</a>。 也就是說，即使在索引中的文件索引鍵不存在，嘗試刪除作業使用該金鑰會導致 200 狀態碼。</td>
    </tr>
    <tr>
        <td>201</td>
        <td>已成功建立文件。</td>
        <td>n/a</td>
        <td></td>
    </tr>
    <tr>
        <td>400</td>
        <td>防止正編製索引的文件時發生錯誤。</td>
        <td>無</td>
        <td>在回應中的錯誤訊息會指出錯誤的文件。</td>
    </tr>
    <tr>
        <td>404</td>
        <td>無法合併文件，因為索引中不存在的指定索引鍵。</td>
        <td>無</td>
        <td>不會無法上傳發生此錯誤，因為他們建立新的文件，而不是為刪除因為它們是<a href="https://en.wikipedia.org/wiki/Idempotence">冪</a>上。</td>
    </tr>
    <tr>
        <td>409</td>
        <td>嘗試編製索引的文件時，已偵測到版本衝突的使用者。</td>
        <td>[是]</td>
        <td>當您嘗試超過一次同時索引同一份文件時，也可能會發生。</td>
    </tr>
    <tr>
        <td>422</td>
        <td>因為它更新設定 'allowIndexDowntime' 旗標為 「 true 」，則索引是暫時無法使用。</td>
        <td>[是]</td>
        <td></td>
    </tr>
    <tr>
        <td>503</td>
        <td>暫時無法使用，可能是因為大量負載您搜尋服務。</td>
        <td>[是]</td>
        <td>您的程式碼應該前在此情況下等待或您的風險延長服務無法使用。</td>
    </tr>
</table> 

**注意**︰ 如果您的用戶端程式碼經常遇到 207 回應，原因可能是系統負載。 您可以藉由確認`statusCode`503 屬性。 如果是這樣，我們建議***節流編製索引的要求***。 否則，如果流量編製索引作業無法處理並減少，系統可能會啟動拒絕所有有 503 錯誤的要求。

狀態碼 429 表示您已超過您配額的每個索引的文件數。 您必須建立新的索引，或升級為較高的容量限制。

**範例︰**

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
          "@search.action": "merge",
          "hotelId": "3",
          "baseRate": 279.99,
          "description": "Surprisingly expensive",
          "lastRenovationDate": null
        },
        {
          "@search.action": "delete",
          "hotelId": "4"
        }
      ]
    }
________________________________________
<a name="SearchDocs"></a>
## <a name="search-documents"></a>搜尋文件

**搜尋**作業會發出取得或文章的要求，並指定選取相符的文件提供之準則的參數。

    GET https://[service name].search.windows.net/indexes/[index name]/docs?[query parameters]
    api-key: [admin or query key]

    POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin or query key]

**而不是取得使用文章**

當您使用 HTTP GET 撥打**搜尋**API 時，必須注意的要求 URL 的長度不能超過 8 KB。 足以通常是大部分的應用程式。 然而，部分應用程式會產生很大的查詢或 OData 篩選運算式。 這些應用程式，使用 HTTP 文章是較佳的選擇因為其允許較大的篩選和比取得的查詢。 字詞或查詢中的子句數文章，就限制的因素，而非原始的查詢由於文章的要求大小上限約 16 MB 的大小。

> [AZURE.NOTE] 即使文章要求大小限制是很大，搜尋查詢和篩選運算式無法很複雜。 有關搜尋查詢和篩選複雜度限制的詳細資訊，請參閱[Lucene 查詢語法](https://msdn.microsoft.com/library/mt589323.aspx)和[OData 運算式語法](https://msdn.microsoft.com/library/dn798921.aspx)。

**要求**

需要服務要求的 HTTPS。 您可使用取得或文章方法建構**搜尋**要求。

要求 URI 指定查詢中，符合參數的所有文件的索引。 參數會指定查詢字串若是 GET 要求，並在邀請中若是文章本文要求。

最佳作法是建立 GET 要求時，請記得要[進行 URL 編碼](https://msdn.microsoft.com/library/system.uri.escapedatastring.aspx)的特定查詢參數直接呼叫 REST API 時。 對於**搜尋**作業，包括︰

- `$filter`
- `facet`
- `highlightPreTag`
- `highlightPostTag`
- `search`
- `moreLikeThis`

在上述查詢參數，建議您只 URL 編碼。 如果您不小心進行 URL 編碼的整個查詢字串 (之後的所有？)，要求將會中斷。

此外，URL 編碼只有必要時，呼叫直接使用 [取得 REST API。 呼叫**搜尋**使用文章，或使用[.NET 用戶端文件庫](https://msdn.microsoft.com/library/dn951165.aspx)，其控點的 URL 編碼方式時，沒有 URL 編碼方式是必要的。

<a name="SearchQueryParameters"></a>
**查詢參數**

**搜尋**可接受多個參數提供查詢準則，也指定搜尋行為。 您提供呼叫透過張貼**搜尋**時，在邀請內文中呼叫**搜尋**透過取得，並為 JSON 內容時，這些 URL 中的參數查詢字串。 某些參數的語法是取得與文章之間稍有不同。 這些差異都標示為適用如下︰

`search=[string]`（選擇性）-要搜尋的文字。 所有`searchable`欄位會搜尋根據預設，除非`searchFields`指定。 搜尋時`searchable`本身的搜尋文字方塊的欄位，token 化，讓多個字詞，可以加上空格區隔 (例如︰ `search=hello world`)。 若要比對任何字詞，使用`*`（這可用於布林值的篩選查詢）。 省略此參數具有相同的效果將它設定為`*`。 在 [搜尋語法的詳細資訊，請參閱[簡單查詢語法](https://msdn.microsoft.com/library/dn798920.aspx)。

  - **附註**︰ 結果有時會意外查詢整個`searchable`欄位。 Tokenizer 包含邏輯來處理一般單引號、 逗號等數字的英文文字的情況。例如，`search=123,456`將符合單一字詞 123,456，而不是個別的字詞 123 和 456，由於英文字使用逗號作為千位分隔符號。 因此，建議使用區隔字詞中的空白區域，而不是標點符號`search`參數。

`searchMode=any|all`(選用，預設值為`any`)-是否才能計算符合文件，則必須符合下列任一或所有搜尋字詞。

`searchFields=[string]`（選擇性）-逗號分隔] 欄位名稱，若要指定文字搜尋清單。 目標欄位必須標示為`searchable`。

`queryType=simple|full`(選擇性的預設值為`simple`)-時設定為 「 簡單 」 的搜尋文字項目都會解譯使用簡單的查詢語言，例如讓符號 +、 * 和 「 」。 查詢會評估所有可搜尋的欄位 (或欄位中指定`searchFields`) 預設的每個文件中。 當查詢類型設定為`full`搜尋文字項目都會解譯使用 Lucene 查詢語言可讓功能變數和加權搜尋。 在 [搜尋語法的詳細資訊，請參閱[簡單查詢語法](https://msdn.microsoft.com/library/dn798920.aspx)和[Lucene 查詢語法](https://msdn.microsoft.com/library/mt589323.aspx)。 
 
> [AZURE.NOTE] 在查詢語言而採用 $filter 提供類似的功能不受支援 Lucene 中搜尋的範圍。

`moreLikeThis=[key]`（選用）**重要︰**此功能只適用於`2015-02-28-Preview`。 這個選項，不能包含文字搜尋參數查詢中`search=[string]`。 `moreLikeThis`參數會找出類似文件索引鍵所指定的文件的文件。 搜尋要求時所做的`moreLikeThis`，根據頻率和不成熟發展來源文件中的字詞的所產生的搜尋字詞清單。 這些字詞用來進行邀請。 根據預設，所有的內容`searchable`欄位會被視為，除非`searchFields`用來限制搜尋的欄位。  

`$skip=#`（選擇性）-略過; 的搜尋結果的數字不能大於 100000。 如果您需要順序掃描文件，但無法使用`$skip`因為這項限制，請考慮使用`$orderby`完全排序索引鍵和`$filter`於某範圍內的查詢改為。

> [AZURE.NOTE] 這個參數呼叫時使用文章的**搜尋**，名為`skip`而不是`$skip`。

`$top=#`（選擇性）-來擷取搜尋結果的數目。 這可以用搭配`$skip`實作搜尋結果的用戶端分頁。

> [AZURE.NOTE] 這個參數呼叫時使用文章的**搜尋**，名為`top`而不是`$top`。

`$count=true|false`(選用，預設值為`false`)-指定是否要擷取的結果的總數。 這是符合所有文件的計數`search`和`$filter`參數，並忽略`$top`和`$skip`。 將此值設為`true`可能影響效能。 請注意，傳回的計數近似值。

> [AZURE.NOTE] 這個參數呼叫時使用文章的**搜尋**，名為`count`而不是`$count`。

`$orderby=[string]`（選擇性）-逗點分隔運算式的結果排序的清單。 每個運算式可以是欄位名稱，或是呼叫`geo.distance()`函數。 每個運算式可以後面`asc`來表示遞增，和`desc`指出 [遞減]。 預設值遞增順序。 以符合分數的文件，就會中斷連結。 如果沒有`$orderby`指定，預設的排序順序遞減文件符合分數。 沒有限制的 32 子句`$orderby`。

> [AZURE.NOTE] 這個參數呼叫時使用文章的**搜尋**，名為`orderby`而不是`$orderby`。

`$select=[string]`（選擇性）-逗號分隔] 欄位來擷取清單。 如果未指定，標示為擷取結構描述中的所有欄位都都包含在內。 若要將此參數，您可以也明確要求所有欄位`*`。

> [AZURE.NOTE] 這個參數呼叫時使用文章的**搜尋**，名為`select`而不是`$select`。

`facet=[string]`（零或更多）-多面向所要的欄位。 您也可以選擇的字串可能包含來自訂以逗號分隔 faceting 參數`name:value`配對。 有效的參數是︰

- `count`(最多面向字詞; 的預設值為 10)。 沒有最大值，但是較高的值會造成對應的效能產生負面影響，尤其是如果 [多面向] 欄位包含大量的唯一的字詞。
  - 例如︰`facet=category,count:5`在多面向結果中取得五大類別。  
  - **注意**︰ 如果`count`參數小於唯一字詞的數字，結果可能不正確。 這是因為 faceting 查詢分佈擊碎的方式。 增加`count`通常會增加，但在的效能成本的字詞計數精確度。
- `sort`(其中一項`count`排序項目個數*遞減*`-count`排序項目個數*遞增*`value`排序*遞增*的值，或`-value`排序*遞減*值)
  - 例如︰`facet=category,count:3,sort:count`會以遞減順序的每個城市名稱的文件的多面向結果中的前三個類別。 比方說，如果頂端的三個類別的預算與 Motel，可惜，和預算 5 點擊、 Motel 6，而可惜具有 4，然後值區就會在順序 Motel、 預算、 可惜。
  - 例如︰`facet=rating,sort:-value`產生所有可能的評等，以遞減順序值的值區。 例如，如果評等是從 1 至 5，值區會訂購 5、 4、 3、 2、 1，不管資料多少文件比對每個評等。
- `values`(直立線符號分隔的數字或`Edm.DateTimeOffset`值指定動態的一組多面向項目值)
  - 例如︰`facet=baseRate,values:10|20`產生三種情況︰ 一個用於基底的工資率 0，但不是包括最多 10 個 10 但不是包括 20，一個 20 或更新版本。
  - 例如︰`facet=lastRenovationDate,values:2010-02-01T00:00:00Z`產生兩者︰ 一個用於旅館 renovated 年 2 月 2010年之前和旅館一個 renovated 年 2 月 1st 2010年或更新版本。
- `interval`(大於 0 的數字的整數間隔或`minute`， `hour`， `day`， `week`， `month`， `quarter`，`year`日期時間值)
  - 例如︰`facet=baseRate,interval:100`會產生值區根據基底工資率範圍的大小 100。 例如，如果 $60 和 $600 之間的所有的基準工資率，會有值 0-100，100 200、 200 300、 300 400、 400 500，而 500 600 區。
  - 例如︰`facet=lastRenovationDate,interval:year`旅館已 renovated 時每年產生一個連結。
- `timeoffset`([+-] hh: mm，[+-] hhmm，或 [+-] hh)`timeoffset`為選用步驟。 僅限聯合它與`interval`選項，並套用至類型的欄位時，才`Edm.DateTimeOffset`。 此值指定帳戶 UTC 位移設定時間範圍。
  - 例如︰ `facet=lastRenovationDate,interval:day,timeoffset:-01:00` 01:00:00 UTC （午夜目標時區） 在所使用的開始日期邊界
- **附註**︰`count`和`sort`可以結合在相同的一環規格中，但無法與結合`interval`或`values`，及`interval`和`values`無法結合在一起。
- **注意**︰ 如果根據 UTC 時間計算日期時間間隔 facet`timeoffset`未指定。 例如︰ 的`facet=lastRenovationDate,interval:day`、 邊界 00:00:00 UTC 從開始的日期。 

> [AZURE.NOTE] 這個參數呼叫時使用文章的**搜尋**，名為`facets`而不是`facet`。 此外，您指定的字串，其中每個字串是個別的多面向運算式以 JSON 陣列。

`$filter=[string]`（選擇性）-在標準 OData 語法的結構化的搜尋運算式。 Azure 搜尋支援的 OData 運算式文法子集的詳細資訊，請參閱[OData 運算式語法](#ODataExpressionSyntax)。

> [AZURE.NOTE] 這個參數呼叫時使用文章的**搜尋**，名為`filter`而不是`$filter`。

`highlight=[string]`（選擇性）-一組用於叫用逗點分隔的欄位名稱的醒目提示。 僅限`searchable`欄位可用於結果醒目提示。

`highlightPreTag=[string]`(選用，預設值為`<em>`)-字串預先規劃叫用醒目提示的標籤。 必須以設定`highlightPostTag`。

> [AZURE.NOTE] 當呼叫**搜尋**使用取得，保留在 URL 中的字元必須百分比-編碼 （例如，而不是 # %23）。

`highlightPostTag=[string]`(選用，預設值為`</em>`)-字串標籤附加到按下醒目提示。 必須以設定`highlightPreTag`。

> [AZURE.NOTE] 當呼叫**搜尋**使用取得，保留在 URL 中的字元必須百分比-編碼 （例如，而不是 # %23）。

`scoringProfile=[string]`（選擇性）-評估計分設定檔名稱符合分數才能排序結果符合文件。

`scoringParameter=[string]`（零或更多）-指出定義計分函數中的每個參數的值 (例如， `referencePointParameter`) 使用格式`name-value1,value2,...`。

- 例如，如果計分的設定檔定義函數與稱為 「 mylocation 」 參數查詢字串選項會`&scoringParameter=mylocation--122.2,44.8`。 第一個破折號名稱與分隔值] 清單中，第二個虛線時的第一個值 （在此範例中經度） 的一部分。
- 計分參數例如標記促進的最多可以包含逗號，您可以逸出任何這類使用單引號括住清單中的值。 如果本身的值包含單引號括住，您可以加倍逸出它們。
  - 例如，如果您有促進稱為 「 mytag 」 參數的標籤，且您想要標記上拓展值"Hello，O'Brien"和"Smith"，查詢字串的選項就是`&scoringParameter=mytag-'Hello, O''Brien',Smith`。 請注意，報價只需包含逗號分隔的值。

> [AZURE.NOTE] 這個參數呼叫時使用文章的**搜尋**，名為`scoringParameters`而不是`scoringParameter`。 以 JSON 陣列的每個字串位置是不同的字串您指定的同時，`name-values`配對。

`minimumCoverage`(選用，預設值為 100)-介於 0 到 100，指出必須覆蓋順序的報告為成功查詢搜尋查詢的索引的百分比。 根據預設，整個索引必須可供或`Search`會傳回 HTTP 狀態碼 503。 如果您設定`minimumCoverage`和`Search`順利完成，它會傳回 HTTP 200，包含`@search.coverage`指出索引查詢中包含的百分比在回應中的值。

> [AZURE.NOTE] 設定此參數的值小於 100 可以用來確保搜尋可用性，即使的服務與只有一個複本。 不過，並非所有相符的文件一定會出現在搜尋結果中。 如果搜尋回收的考量優先應用程式於的顯示狀態，然後最好離開`minimumCoverage`在其預設值為 100。

`api-version=[string]`（必要）。 預覽版本`api-version=2015-02-28-Preview`。 請參閱[搜尋服務版本設定](http://msdn.microsoft.com/library/azure/dn864560.aspx)詳細資料] 與 [替代的版本。

注意︰ 這項作業，對於`api-version`指定查詢中參數的 URL，無論您是否呼叫**搜尋**取得的文章。

**要求標頭**

下列清單說明必要與選用要求的標頭。

- `api-key`:`api-key`會用於驗證您的搜尋服務要求。 它是字串值，唯一您服務的 URL。 **搜尋**要求可以指定管理員鍵 」 或 「 查詢按鍵`api-key`。

您也必須服務名稱，以建構要求 URL。 您可以取得服務名稱和`api-key`從您的服務儀表板中 Azure 入口網站。 請參閱[建立 Azure 搜尋服務入口網站中](search-create-service-portal.md)的 [頁面導覽協助。

**邀請內文**

取得︰ 無。

文章︰

    {
      "count": true | false (default),
      "facets": [ "facet_expression_1", "facet_expression_2", ... ],
      "filter": "odata_filter_expression",
      "highlight": "highlight_field_1, highlight_field_2, ...",
      "highlightPreTag": "pre_tag",
      "highlightPostTag": "post_tag",
      "minimumCoverage": # (% of index that must be covered to declare query successful; default 100),
      "moreLikeThis": "document_key" (mutually exclusive with "search" parameter),
      "orderby": "orderby_expression",
      "scoringParameters": [ "scoring_parameter_1", "scoring_parameter_2", ... ],
      "scoringProfile": "scoring_profile_name",
      "search": "simple_query_expression",
      "searchFields": "field_name_1, field_name_2, ...",
      "searchMode": "any" (default) | "all",
      "select": "field_name_1, field_name_2, ...",
      "skip": # (default 0),
      "top": #
    }

**部分搜尋回應的接續註**

有時 Azure 搜尋無法在單一搜尋回應中傳回所有要求的結果。 就會發生此不同的原因，例如查詢時要求太多的文件沒有指定`$top`指定的值或`$top`太大。 Azure 搜尋會包含在這種情況下，`@odata.nextLink`回應本文中，註釋，同時`@search.nextPageParameters`是否文章要求。 您可以使用這些註解的值來制定以取得搜尋回應的下一個組件的另一個搜尋要求。 這稱為原始的搜尋要求，***接續***和註釋通常稱為***接續權杖***。 請參閱[下面的範例](#SearchResponse)，這些註解的語法的詳細資訊與顯示回應內容中。 

為什麼 Azure 搜尋可能會傳回接續權杖的原因如下實作特定和有所變更。 強大的用戶端應該一律可以處理其中比預期更少的文件，而接續權杖是包含繼續擷取文件的情況。 也請注意，您必須使用相同的 HTTP 方法為原始邀請才能繼續。 例如，如果您已傳送 GET 要求，您傳送的任何接續要求也必須使用取得 （以及同樣文章）。

<a name="SearchResponse"></a>
**回應**

狀態碼︰ 200 確定會傳回成功的回應。

    {
      "@odata.count": # (if $count=true was provided in the query),
      "@search.coverage": # (if minimumCoverage was provided in the query),
      "@search.facets": { (if faceting was specified in the query)
        "facet_field": [
          {
            "value": facet_entry_value (for non-range facets),
            "from": facet_entry_value (for range facets),
            "to": facet_entry_value (for range facets),
            "count": number_of_documents
          }
        ],
        ...
      },
      "@search.nextPageParameters": { (request body to fetch the next page of results if not all results could be returned in this response and Search was called with POST)
        "count": ... (value from request body if present),
        "facets": ... (value from request body if present),
        "filter": ... (value from request body if present),
        "highlight": ... (value from request body if present),
        "highlightPreTag": ... (value from request body if present),
        "highlightPostTag": ... (value from request body if present),
        "minimumCoverage": ... (value from request body if present),
        "moreLikeThis": ... (value from request body if present),
        "orderby": ... (value from request body if present),
        "scoringParameters": ... (value from request body if present),
        "scoringProfile": ... (value from request body if present),
        "search": ... (value from request body if present),
        "searchFields": ... (value from request body if present),
        "searchMode": ... (value from request body if present),
        "select": ... (value from request body if present),
        "skip": ... (page size plus value from request body if present),
        "top": ... (value from request body if present minus page size),
      },
      "value": [
        {
          "@search.score": document_score (if a text query was provided),
          "@search.highlights": {
            field_name: [ subset of text, ... ],
            ...
          },
          key_field_name: document_key,
          field_name: field_value (retrievable fields or specified projection),
          ...
        },
        ...
      ],
      "@odata.nextLink": (URL to fetch the next page of results if not all results could be returned in this response; Applies to both GET and POST)
    }

**範例︰**

您可以在 [ [OData 運算式語法 Azure 搜尋](https://msdn.microsoft.com/library/azure/dn798921.aspx)頁面上，找到其他範例。

1)  搜尋索引以遞減方式排序的日期。


    取得 /indexes/hotels/docs？ 搜尋 = * & $orderby = lastRenovationDate 遞減 & api 版本 = 2015年 02-28 預覽

    文章 /indexes/hotels/docs/search？ api 版本 = 2015年 02-28 預覽 {「 搜尋 」: 「 * 「，」 排序方式]: 「 lastRenovationDate 遞減 」}

2)  在多面向的搜尋，搜尋索引，並可擷取 facet 類別、 評等、 標籤，以及 baseRate 在特定範圍中的項目︰


    取得 /indexes/hotels/docs？ 搜尋 = 測試與多面向 = 類別與多面向 = 評等與多面向 = 標記與多面向 = baseRate，值︰ 80 | 150 | 220 與 api 版本 = 2015年 02-28 預覽

    文章 /indexes/hotels/docs/search？ api 版本 = 2015年 02-28 預覽 {「 搜尋 」: 「 測試 「，」 facet 」: ["類別"、"評等 」、 「 標籤 」，「 baseRate，值︰ 80 | 150 | 220 」]}

3)  使用者在後，使用 [篩選]，縮小前一個多面向查詢結果 3 和類別 「 Motel 」 的評等︰


    取得 /indexes/hotels/docs？ 搜尋 = 測試與多面向 = 標記與多面向 = baseRate，值︰ 80 | 150 | 220 與 $filter = 評等 eq 3 」 及 「 類別 eq 'Motel 」 及 「 api 版本 = 2015年 02-28 預覽

    文章 /indexes/hotels/docs/search？ api 版本 = 2015年 02-28 預覽 {「 搜尋 」: 「 測試 「，」 facet 」: [「 標籤 」，「 baseRate，值︰ 80 | 150 | 220 」]，「 篩選器 」: 「 評等 eq 3 和類別 eq 'Motel' 」}

4) 在多面向的搜尋設定查詢傳回的唯一字詞的上限。 預設值為 10，但您可以增加或減少此值，使用`count`上的參數`facet`屬性︰


    取得 /indexes/hotels/docs？ 搜尋 = 測試與多面向 = 縣/市]、 [計數︰ 5 與 api 版本 = 2015年 02-28 預覽

    文章 /indexes/hotels/docs/search？ api 版本 = 2015年 02-28 預覽 {「 搜尋 」: 「 測試 「，」 facet 」: [「 城市，計數︰ 5"]}

5)  搜尋特定欄位中的索引例如，語言特定欄位︰


    取得 /indexes/hotels/docs？ 搜尋 = hôtel 與 searchFields = description_fr & api 版本 = 2015年 02-28 預覽

    文章 /indexes/hotels/docs/search？ api 版本 = 2015年 02-28 預覽 {「 搜尋 」: 「 hôtel 「，」 searchFields 」: 「 description_fr"}

6) 在多個欄位中搜尋索引。 例如，您可以儲存及查詢可搜尋欄位中所有在相同的索引的多種語言。  如果英文和法文描述共同存在於同一份文件，您可以返回任一或所有查詢結果中。


    取得 /indexes/hotels/docs？ 搜尋 = 旅館 & searchFields = 描述、 description_fr 與 api 版本 = 2015年 02-28 預覽

    文章 /indexes/hotels/docs/search？ api 版本 = 2015年 02-28 預覽 {「 搜尋 」: 「 旅館 」、 「 searchFields 」: 「 描述，description_fr 」}

請注意，您可以一次只能查詢一個索引。 除非您打算查詢一次，不要建立每種語言的多個的索引。

7)  分頁-取得的項目第 1 頁 （頁面大小為 10）︰


    取得 /indexes/hotels/docs？ 搜尋 = * & $skip = 0 與 $top = 10 & api 版本 = 2015年 02-28 預覽

    文章 /indexes/hotels/docs/search？ api 版本 = 2015年 02-28 預覽 {「 搜尋 」: 「 * 「，」 略過]: 0，「 上方 」: 10}

8)  分頁-取得的項目第 2 頁 （頁面大小為 10）︰


    取得 /indexes/hotels/docs？ 搜尋 = * & $skip = 10 & $top = 10 & api 版本 = 2015年 02-28 預覽

    文章 /indexes/hotels/docs/search？ api 版本 = 2015年 02-28 預覽 {「 搜尋 」: 「 * 「，」 略過]: 10，「 上方 」: 10}

9)  擷取特定的一組欄位︰


    取得 /indexes/hotels/docs？ 搜尋 = * & $select = hotelName、 描述與 api 版本 = 2015年 02-28 預覽

    文章 /indexes/hotels/docs/search？ api 版本 = 2015年 02-28 預覽 {「 搜尋 」: 「 * 」、 「 選取 」: 「 hotelName，描述 」}

10)  擷取相符的特定的篩選運算式的文件︰


    取得 /indexes/hotels/docs？ $filter = (baseRate ge 60 和 baseRate lt 300) 或 hotelName eq 美觀保持 ' 與 api 版本 = 2015年 02-28 預覽

    文章 /indexes/hotels/docs/search？ api 版本 = 2015年 02-28 預覽 {「 篩選器 」: 」 (baseRate ge 60 和 baseRate lt 300) 或 hotelName eq '美觀保持 」]}

11) 搜尋編製索引及傳回片段與點擊醒目提示


    取得 /indexes/hotels/docs？ 搜尋 = 項目，並醒目提示 = 描述與 api 版本 = 2015年 02-28 預覽

    文章 /indexes/hotels/docs/search？ api 版本 = 2015年 02-28 預覽 {「 搜尋 」: 」 訊息 「，」 醒目提示 「: 「 描述 」}

12) 搜尋索引，並傳回排序從接近參照之間的距離的位置的文件


    取得 /indexes/hotels/docs？ 搜尋 = 項目 & $orderby=geo.distance (位置，geography'POINT(-122.12315 47.88121) 」) & api 版本 = 2015年 02-28 預覽

    文章 /indexes/hotels/docs/search？ api 版本 = 2015年 02-28 預覽 {「 搜尋 」: 「 項目]，[排序方式]: 「 geo.distance (位置，geography'POINT(-122.12315 47.88121) 」) 「}

13) 具有兩個距離計分函數搜尋索引假設計分的設定檔，稱為 「 地理 」、 一個定義參數稱為 「 currentLocation 」，而另一個定義稱為 「 lastLocation 」 的參數


    取得 /indexes/hotels/docs？ 搜尋 = 項目 & scoringProfile = 地理 & scoringParameter = currentLocation-122.123,44.77233 與 scoringParameter = lastLocation-121.499,44.2113 與 api 版本 = 2015年 02-28 預覽

    文章 /indexes/hotels/docs/search？ api 版本 = 2015年 02-28 預覽 {「 搜尋 」: 」 訊息 「，」 scoringProfile 」: 「 地理 「，」 scoringParameters 」: [」 currentLocation-122.123,44.77233 「，」 lastLocation-121.499,44.2113 」]}

14) 使用[簡單的查詢語法](https://msdn.microsoft.com/library/dn798920.aspx)索引中找到文件。 此查詢會傳回的旅館可搜尋] 欄位包含字詞 「 家裡 」 和 「 位置 」 但不是 「 motel 」 的位置︰


    取得 /indexes/hotels/docs？ 搜尋 = 家裡 + 位置-motel 與 searchMode = 所有 & api 版本 = 2015年 02-28 預覽

    文章 /indexes/hotels/docs/search？ api 版本 = 2015年 02-28 預覽 {「 搜尋 」: 「 家裡 + 位置-motel 「，」 searchMode 」: 「 所有 」}

請注意使用`searchMode=all`上方。 包含此參數會覆寫預設的`searchMode=any`，確保的`-motel`表示 「 AND NOT 」，而不是 」 或不 」。 不含`searchMode=all`您收到 「 或不 」 這會展開，而不是限制搜尋結果，這可能是違反直覺部分使用者。

15) 使用[lucene 查詢語法](https://msdn.microsoft.com/library/mt589323.aspx)索引中找到文件。 此查詢會傳回旅館 [類別] 欄位包含字詞 「 預算 」 的位置和所有可搜尋] 欄位包含 「 最近 renovated 」 片語。 包含字詞 「 最近 renovated 」 的文件被等級當做字詞增量值 (3)

    取得 /indexes/hotels/docs?search = 類別︰ 預算與\"最近 renovated\"^3 與 searchMode = 所有 & api 版本 = 2015年 02-28 預覽 & querytype = 完整

    文章 /indexes/hotels/docs/search?api-version = 2015年 02-28 預覽 {「 搜尋 」: 「 類別︰ 預算與\"最近 renovated\"^3"、"queryType 」: 「 完整 「，」 searchMode 」: 「 所有 」}

<a name="LookupAPI"></a>
## <a name="lookup-document"></a>查閱文件

**查閱文件**] 作業會從 Azure 搜尋擷取文件。 當使用者在特定的搜尋結果，而您想要尋找特定的文件的相關詳細資料，這是很有用。

    GET https://[service name].search.windows.net/indexes/[index name]/docs/[key]?[query parameters]
    api-key: [admin or query key]

**要求**

需要服務要求的 HTTPS。 **查閱文件**要求建構方式如下。

    GET /indexes/[index name]/docs/key?[query parameters]

或者，您可以使用傳統 OData 語法鍵查閱︰

    GET /indexes('[index name]')/docs('[key]')?[query parameters]

要求 URI 包含 [索引名稱] 和 [重要]，指定要從哪一個索引擷取的文件。 您只可以一次取得一份文件。 使用**搜尋**以取得單一邀請中的多份文件。

**查詢參數**

`$select=[string]`（選擇性）-逗號分隔] 欄位來擷取清單。 如果未指定或設定為`*`，標示為擷取結構描述中的所有欄位會都包含在預測。

`api-version=[string]`（必要）。 預覽版本`api-version=2015-02-28-Preview`。 請參閱[搜尋服務版本設定](http://msdn.microsoft.com/library/azure/dn864560.aspx)詳細資料] 與 [替代的版本。

注意︰ 這項作業，對於`api-version`指定查詢參數。

**要求標頭**

下列清單說明必要與選用要求的標頭。

- `api-key`:`api-key`會用於驗證您的搜尋服務要求。 它是字串值，唯一您服務的 URL。 **查閱文件**要求可以指定管理員鍵 」 或 「 查詢按鍵`api-key`。

您也必須服務名稱，以建構要求 URL。 您可以取得服務名稱和`api-key`從您的服務儀表板中 Azure 入口網站。 請參閱[建立 Azure 搜尋服務入口網站中](search-create-service-portal.md)的 [頁面導覽協助。

**邀請內文**

無。

**回應**

狀態碼︰ 200 確定會傳回成功的回應。

    {
      field_name: field_value (fields matching the default or specified projection)
    }

**範例**

查閱具有 [2] 的文件

    GET /indexes/hotels/docs/2?api-version=2015-02-28-Preview

查閱文件含有鍵 '3' 使用 OData 語法︰

    GET /indexes('hotels')/docs('3')?api-version=2015-02-28-Preview

<a name="CountDocs"></a>
## <a name="count-documents"></a>計算文件

**計算文件**作業擷取的搜尋索引中的文件數。 `$count`語法是 OData 通訊協定的一部分。

    GET https://[service name].search.windows.net/indexes/[index name]/docs/$count?api-version=[api-version]
    Accept: text/plain
    api-key: [admin or query key]

**要求**

需要服務要求的 HTTPS。 您可使用取得方法建構**計算文件**要求。

[索引名稱] 中要求 URI 會告訴傳回的所有項目計數指定索引的文件集合中的服務。

`api-version=[string]`（必要）。 預覽版本`api-version=2015-02-28-Preview`。 請參閱[搜尋服務版本設定](http://msdn.microsoft.com/library/azure/dn864560.aspx)詳細資料] 與 [替代的版本。

**要求標頭**

下列清單說明必要與選用要求的標頭。

- `Accept`︰ 此值必須設定為 [ `text/plain`。
- `api-key`:`api-key`會用於驗證您的搜尋服務要求。 它是字串值，唯一您服務的 URL。 **計算文件**要求可以指定管理員鍵 」 或 「 查詢按鍵`api-key`。

您也必須服務名稱，以建構要求 URL。 您可以取得服務名稱和`api-key`從您的服務儀表板中 Azure 入口網站。 請參閱[建立 Azure 搜尋服務入口網站中](search-create-service-portal.md)的 [頁面導覽協助。

**邀請內文**

無。

**回應**

狀態碼︰ 200 確定會傳回成功的回應。

回應內文包含計算值以純文字格式的整數。

<a name="Suggestions"></a>
## <a name="suggestions"></a>建議

[**建議**] 作業會擷取部分搜尋輸入為基礎的建議。 通常是在搜尋方塊中提供預先輸入的建議，為使用者輸入搜尋字詞。

建議要求瞄準建議目標文件，因此多份候選文件符合輸入相同的搜尋時，可能會重複建議的文字。 您可以使用`$select`擷取 （包括文件索引鍵） 其他文件欄位，好讓您得知自己的文件是每一個建議的來源。

取得或文章要求會發出**建議**作業。

    GET https://[service name].search.windows.net/indexes/[index name]/docs/suggest?[query parameters]
    api-key: [admin or query key]

    POST https://[service name].search.windows.net/indexes/[index name]/docs/suggest?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin or query key]

**而不是取得使用文章**

當您使用 HTTP GET 撥打**建議**API 時，必須注意的要求 URL 的長度不能超過 8 KB。 足以通常是大部分的應用程式。 然而，部分應用程式會產生很大的查詢，尤其是 OData 篩選運算式。 這些應用程式，使用 HTTP 文章是較佳的選擇因為其允許比取得較大的篩選。 文章，在篩選中的子句數目就限制的因素，而非原始的篩選字串由於文章的要求大小上限約 16 MB 的大小。

> [AZURE.NOTE] 即使文章要求大小限制是很大，無法複雜的篩選條件運算式。 如需篩選複雜度限制相關資訊，請參閱[OData 運算式語法](https://msdn.microsoft.com/library/dn798921.aspx)。

**要求**

需要服務要求的 HTTPS。 您可使用取得或文章方法建構**建議**要求。

要求 URI 指定要查詢的索引的名稱。 若是 GET 要求的查詢字串中指定參數，例如部分輸入的搜尋字詞，並在邀請中若是文章本文要求。

最佳作法是建立 GET 要求時，請記得要[進行 URL 編碼](https://msdn.microsoft.com/library/system.uri.escapedatastring.aspx)的特定查詢參數直接呼叫 REST API 時。 **建議**作業，包括︰

- `$filter`
- `highlightPreTag`
- `highlightPostTag`
- `search`

在上述查詢參數，建議您只 URL 編碼。 如果您不小心進行 URL 編碼的整個查詢字串 (之後的所有？)，要求將會中斷。

此外，URL 編碼只有必要時，呼叫直接使用 [取得 REST API。 呼叫**建議**使用文章，或使用[.NET 用戶端文件庫](https://msdn.microsoft.com/library/dn951165.aspx)，其控點的 URL 編碼方式時，沒有 URL 編碼方式是必要的。

**查詢參數**

**建議**接受提供查詢準則，並也指定搜尋行為的多個參數。 您提供呼叫**建議**透過文章時，在邀請內文中呼叫**建議**透過取得，並為 JSON 內容時，這些 URL 中的參數查詢字串。 某些參數的語法是取得與文章之間稍有不同。 這些差異都標示為適用如下︰

`search=[string]`-使用建議的查詢搜尋文字。 必須至少 1 個字元，而且不能超過 100 個字元。

`highlightPreTag=[string]`（選擇性）-字串標記的搜尋點擊預先規劃。 必須以設定`highlightPostTag`。

> [AZURE.NOTE] 當呼叫**建議**使用取得，保留在 URL 中的字元必須是百分比編碼 （例如，而不是 # %23）。

`highlightPostTag=[string]`（選擇性）-字串標記的搜尋點擊例子。 必須以設定`highlightPreTag`。

> [AZURE.NOTE] 當呼叫**建議**使用取得，保留在 URL 中的字元必須是百分比編碼 （例如，而不是 # %23）。

`suggesterName=[string]`-指定 suggester 名稱`suggesters`屬於索引定義的集合。 A`suggester`決定建議的查詢規定掃描的欄位。 如需詳細資訊，請參閱[Suggesters](#Suggesters) 。

`fuzzy=[boolean]`(選用，預設 = false)-當設為 true，則此 API 會找到建議即使有替代或遺漏的字元，在搜尋文字]。 雖然這會提供更好的經驗，在某些情況下帶來的效能成本模糊建議搜尋速度變慢，以及使用更多資源。

`searchFields=[string]`（選擇性）-逗號分隔] 欄位名稱與指定的搜尋文字搜尋的清單。 您必須啟用目標欄位的建議。

`$top=#`(選用，預設 = 5) 的建議來擷取的數目。 必須是介於 1 到 100 的數字。

> [AZURE.NOTE] 呼叫時使用文章的**建議**，為此參數`top`而不是`$top`。

`$filter=[string]`（選擇性）-視為建議的文件的篩選運算式。

> [AZURE.NOTE] 呼叫時使用文章的**建議**，為此參數`filter`而不是`$filter`。

`$orderby=[string]`（選擇性）-逗點分隔運算式的結果排序的清單。 每個運算式可以是欄位名稱，或是呼叫`geo.distance()`函數。 每個運算式可以後面`asc`來表示遞增，和`desc`指出 [遞減]。 預設值遞增順序。 沒有限制的 32 子句`$orderby`。

> [AZURE.NOTE] 呼叫時使用文章的**建議**，為此參數`orderby`而不是`$orderby`。

`$select=[string]`（選擇性）-逗號分隔] 欄位來擷取清單。 如果未指定，則會傳回只文件索引鍵和建議的文字。 您可以藉由設定為此參數明確要求所有欄位`*`。

> [AZURE.NOTE] 呼叫時使用文章的**建議**，為此參數`select`而不是`$select`。

`minimumCoverage`(選用，預設值為 80)-介於 0 到 100，指出必須覆蓋順序的報告為成功查詢建議查詢的索引的百分比。 根據預設，至少索引的 80%必須可供或`Suggest`會傳回 HTTP 狀態碼 503。 如果您設定`minimumCoverage`和`Suggest`順利完成，它會傳回 HTTP 200，包含`@search.coverage`指出索引查詢中包含的百分比在回應中的值。

> [AZURE.NOTE] 設定此參數的值小於 100 可以用來確保搜尋可用性，即使的服務與只有一個複本。 不過，並非所有相符的建議一定會出現在結果中。 如果回收的考量優先應用程式於的顯示狀態，然後最好不降低`minimumCoverage`80 的預設值] 下方。

`api-version=[string]`（必要）。 預覽版本`api-version=2015-02-28-Preview`。 請參閱[搜尋服務版本設定](http://msdn.microsoft.com/library/azure/dn864560.aspx)詳細資料] 與 [替代的版本。

注意︰ 這項作業，對於`api-version`指定查詢中參數的 URL，無論您是否呼叫**建議**取得的文章。

**要求標頭**

下列清單說明必要與選用要求的標頭

- `api-key`:`api-key`會用於驗證您的搜尋服務要求。 它是字串值，唯一您服務的 URL。 管理員鍵 」 或 「 為查詢機碼，**建議**要求可以指定`api-key`。

您也必須服務名稱，以建構要求 URL。 您可以取得服務名稱和`api-key`從您的服務儀表板中 Azure 入口網站。 請參閱[建立 Azure 搜尋服務入口網站中](search-create-service-portal.md)的 [頁面導覽協助。

**邀請內文**

取得︰ 無。

文章︰

    {
      "filter": "odata_filter_expression",
      "fuzzy": true | false (default),
      "highlightPreTag": "pre_tag",
      "highlightPostTag": "post_tag",
      "minimumCoverage": # (% of index that must be covered to declare query successful; default 80),
      "orderby": "orderby_expression",
      "search": "partial_search_input",
      "searchFields": "field_name_1, field_name_2, ...",
      "select": "field_name_1, field_name_2, ...",
      "suggesterName": "suggester_name",
      "top": # (default 5)
    }

**回應**

狀態碼︰ 200 確定會傳回成功的回應。

    {
      "@search.coverage": # (if minimumCoverage was provided in the query),
      "value": [
        {
          "@search.text": "...",
          "<key field>": "..."
        },
        ...
      ]
    }

如果用來擷取欄位預測選項可以包含在陣列的每個項目︰

    {
      "@search.coverage": # (if minimumCoverage was provided in the query),
      "value": [
        {
          "@search.text": "...",
          "<key field>": "..."
          <other projected data fields>
        },
        ...
      ]
    }

**範例**

擷取 5 部分搜尋輸入哪裡 'lux 」 的建議

    GET /indexes/hotels/docs/suggest?search=lux&$top=5&suggesterName=sg&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/suggest?api-version=2015-02-28-Preview
    {
      "search": "lux",
      "top": 5,
      "suggesterName": "sg"
    }
