<properties
    pageTitle="計分 （Azure 搜尋 REST API 版本 2015年-02-28-預覽版本） 的設定檔 |Microsoft Azure |Azure 搜尋預覽 API"
    description="Azure 搜尋是裝載的雲端搜尋服務的支援調整排名的結果，根據使用者定義計分的設定檔。"
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.author="heidist"
    ms.date="08/29/2016" />

# <a name="scoring-profiles-azure-search-rest-api-version-2015-02-28-preview"></a>計分的設定檔 （Azure 搜尋 REST API 版本 2015年-02-28-預覽版本）

> [AZURE.NOTE] 本文將說明在[2015年 02-28 預覽](search-api-2015-02-28-preview.md)計分的設定檔。 目前沒有任何之間的差異`2015-02-28`記載於[MSDN](http://msdn.microsoft.com/library/azure/mt183328.aspx)上的版本和`2015-02-28-Preview`版本本文，但我們提供這份文件繼續才能跨整個 API 提供文件。

## <a name="overview"></a>概觀

計分指的是搜尋分數每個項目的傳回搜尋結果中的計算。 成績是目前的搜尋作業的內容中的項目相關的指標。 高分數、 更多相關項目。 在搜尋結果中，項目是為由高到低，根據針對每個項目計算出的搜尋成績等級。

Azure 搜尋會使用預設計分來計算初始的分數，但您可以自訂計算透過計分的設定檔。 計分的設定檔讓您進一步控制的項目排名搜尋結果中。 例如，您可能要拓展根據營收可能的項目、 升階較新的項目，或可能是拓展已經太長存貨中的項目。

計分的設定檔是索引定義，組成的欄位、 函數和參數。

若要讓您概略瞭解計分的設定檔的外觀，下列範例會顯示簡單設定檔名為 「 地理 」。 此項目提升中有搜尋字詞的項目`hotelName`欄位。 它也會使用`distance`函數，而非目前位置的十個公里內的項目。 如果其他人搜尋字詞 '事 」，而和 '事' 旅館名稱的一部分，包括旅館與 '事 」 的文件會出現在搜尋結果。

    "scoringProfiles": [
      {
        "name": "geo",
        "text": {
          "weights": { "hotelName": 5 }
        },
        "functions": [
          {
            "type": "distance",
            "boost": 5,
            "fieldName": "location",
            "interpolation": "logarithmic",
            "distance": {
              "referencePointParameter": "currentLocation",
              "boostingDistance": 10
            }
          }
        ]
      }
    ]

若要使用這個計分的設定檔，編寫您查詢的查詢字串中指定的設定檔。 在下面的查詢中，注意 [查詢參數，`scoringProfile=geo`在邀請中。

    GET /indexes/hotels/docs?search=inn&scoringProfile=geo&scoringParameter=currentLocation--122.123,44.77233&api-version=2015-02-28-Preview

此查詢搜尋字詞 '事 」，並傳遞中目前的位置。 請注意此查詢包含其他參數，例如`scoringParameter`。 查詢參數說明[搜尋文件 (Azure 搜尋 API)](search-api-2015-02-28-preview.md#SearchDocs)。

按一下 [檢閱計分的設定檔的詳細的範例的[範例](#example)。

## <a name="what-is-default-scoring"></a>什麼預設計分？

計分，計算次序排序的結果集中的每個項目的搜尋分數。 在搜尋結果集中的每個項目是指派搜尋分數，然後排名最高到最低。 更高分數的項目會傳回應用程式。 根據預設，會傳回上方 50，但是您可以使用`$top`參數傳回較小或更大的項目數 （進位到單一回應 1000)。

根據預設，搜尋分數是根據計算統計資料] 與 [查詢屬性。 Azure 搜尋尋找包含查詢字串中的搜尋字詞的文件 (部分或所有，根據`searchMode`)，有助於文件若包含搜尋字詞的執行個體數目。 搜尋分數升字詞很少跨資料中華，但一般的文件中的更高。 為電腦的相關性這種方法的基礎稱為 TF IDF 或 （字詞頻率反函數值文件頻率）。

假設有任何自訂排序，然後排名結果由搜尋分數之前傳給呼叫的應用程式。 如果`$top`未指定，50 有最高分數會傳回的搜尋的項目。

整個結果集，也可以執行搜尋成績值。 例如，您可能必須分數 10 個項目 1.2，1.0 的分數 20 個項目和 0.5 的分數 20 個項目。 如果多個點擊有相同的搜尋分數的順序相同計分的項目未定義，並不穩定。 執行查詢，而且您可能會看到的項目 shift 位置。 提供相同的成績的兩個項目，不的保證有一個會先出現。

## <a name="when-to-use-custom-scoring"></a>使用自訂計分的時機

當排名行為預設不會移到目前為止足夠的會議您企業的目標時，您應該建立一或多個計分的設定檔。 例如，您可能決定搜尋相關性應優先新增的項目。 同樣地，您可能必須包含獲利率的欄位或一些其他欄位，指出潛在營收。 提高對貴企業的優點點擊可以決定要使用計分的設定檔中的重要因素。

透過計分設定檔，也實作相關性型順序。 請考慮您已使用過可讓您依價格、 日期、 評等或相關性排序搜尋結果頁面。 Azure 搜尋計分的設定檔磁碟機 「 關聯 」 選項。 定義的相關資訊是由您在商務目標]，然後輸入您想要進行的搜尋體驗的預測有控制。

<a name="example"></a>
## <a name="example"></a>範例

如上所述，自訂計分實作透過計分索引結構描述中定義的設定檔。

此範例顯示與兩個計分的設定檔索引的結構描述 (`boostGenre`， `newAndHighlyRated`)。 包含 [設定檔作為查詢參數會使用來分數結果集的設定檔此索引的任何查詢。

[這個範例](search-get-started-scoring-profiles.md)。

    {
      "name": "musicstoreindex",
      "fields": [
        { "name": "key", "type": "Edm.String", "key": true },
        { "name": "albumTitle", "type": "Edm.String" },
        { "name": "albumUrl", "type": "Edm.String", "filterable": false },
        { "name": "genre", "type": "Edm.String" },
        { "name": "genreDescription", "type": "Edm.String", "filterable": false },
        { "name": "artistName", "type": "Edm.String" },
        { "name": "orderableOnline", "type": "Edm.Boolean" },
        { "name": "rating", "type": "Edm.Int32" },
        { "name": "tags", "type": "Collection(Edm.String)" },
        { "name": "price", "type": "Edm.Double", "filterable": false },
        { "name": "margin", "type": "Edm.Int32", "retrievable": false },
        { "name": "inventory", "type": "Edm.Int32" },
        { "name": "lastUpdated", "type": "Edm.DateTimeOffset" }
      ],
      "scoringProfiles": [
        {
          "name": "boostGenre",
          "text": {
            "weights": {
              "albumTitle": 1.5,
              "genre": 5,
              "artistName": 2
            }
          }
        },
        {
          "name": "newAndHighlyRated",
          "functions": [
            {
              "type": "freshness",
              "fieldName": "lastUpdated",
              "boost": 10,
              "interpolation": "quadratic",
              "freshness": {
                "boostingDuration": "P365D"
              }
            },
            {
              "type": "magnitude",
              "fieldName": "rating",
              "boost": 10,
              "interpolation": "linear",
              "magnitude": {
                "boostingRangeStart": 1,
                "boostingRangeEnd": 5,
                "constantBoostBeyondRange": false
              }
            }
          ]
        }
      ],
      "suggesters": [
        {
          "name": "sg",
          "searchMode": "analyzingInfixMatching",
          "sourceFields": ["albumTitle", "artistName"]
        }
      ]
    }


## <a name="workflow"></a>工作流程

若要實作自訂計分的行為，新增計分的設定檔，以定義索引的結構描述。 您可以有多個計分的設定檔中的索引，但是您只能在任何指定查詢中次指定一個設定檔。

開始使用本主題提供的[範本](#bkmk_template)。

提供的名稱。 計分的設定檔是選擇性的但如果您將其中一個，名稱。 請務必遵循欄位的命名慣例 （字母，以避免特殊字元，保留的字）。 如需詳細資訊，請參閱[命名規則](http://msdn.microsoft.com/library/azure/dn857353.aspx)。

本文計分的設定檔的建構從加權的欄位與函數。

### <a name="weights"></a>粗細 ###

`weights`計分的設定檔的屬性會指定相對粗細指派至] 欄位的名稱 / 值組。 在此[範例](#example)中，albumTitle、 類型和 artistName 欄位是提高為 1.5 來計算、 5 和 2，分別。 為什麼要為內容類型提高這麼多高於其他？ 如果是較為同類型的資料進行搜尋 (在此情況下使用 「 類型 」 中`musicstoreindex`)，您可能需要較大的變異數的相對粗細。 例如，在`musicstoreindex`，'岩石 」 會顯示為兩個內容類型與中相同措詞內容類型描述。 如果您想超過內容類型描述的內容類型，[內容類型] 欄位會需要多高相對粗細。

### <a name="functions"></a>函數 ###

其他計算所需的特定內容時，會使用函數。 有效的函數類型`freshness`， `magnitude`，`distance`和`tag`。 每個函數有其專屬的參數。

  - `freshness`當您想要拓展應該使用是如何新增或更舊的項目。 此函數只可以使用日期時間] 欄位 (`Edm.DataTimeOffset`)。 請注意`boostingDuration`屬性搭配新鮮度函數。
  - `magnitude`當您想要拓展根據如何高或低的數值是，應該使用。 此函數呼叫的情況包括促進利率、 最高價格、 最低的價格，或下載的計數。 如果您想要的反模式 （例如，若要提升較低價格項目超過較高價格的項目），您可以反轉高到低的範圍。 指定範圍價格 $ 100 美元 $1，您可以設定`boostingRangeStart`100 和`boostingRangeEnd`提高較低價格的項目 1。 此函數只使用點兩下] 和 [整數] 欄位。
  - `distance`您想要拓展鄰近範圍或地理位置時，應該使用。 本函數僅能使用`Edm.GeographyPoint`欄位。
  - `tag`您想要拓展依標籤之間的文件及搜尋查詢時，應該使用。 本函數僅能使用`Edm.String`和`Collection(Edm.String)`欄位。
  
#### <a name="rules-for-using-functions"></a>使用函數的規則 ####

  - 函數類型 （新鮮度、 大小、 距離、 標記） 必須小寫。
  - 函數不能包含 null 值或空白值。 明確地說，如果您要包含欄位名稱，您必須將它設定為項目。
  - 函數只能套用至可以篩選的欄位。 如需有關可篩選的欄位，請參閱[建立索引](search-api-2015-02-28-preview.md#CreateIndex)。
  - 函數只能套用至定義索引的欄位集合中的欄位。

定義索引之後，請上傳的索引結構描述，後面接著文件建立索引。 如需這些作業的指示，請參閱[建立索引](search-api-2015-02-28-preview.md#CreateIndex)及[新增或更新文件](search-api-2015-02-28-preview.md#AddOrUpdateDocuments)。 索引建立後，您應該可以搭配您的搜尋資料功能計分設定檔。

<a name="bkmk_template"></a>
## <a name="template"></a>範本
本節說明的語法和計分設定檔的範本。 請參閱中的屬性描述的下一節的[索引屬性參考](#bkmk_indexref)。

    ...
    "scoringProfiles": [
      {
        "name": "name of scoring profile",
        "text": (optional, only applies to searchable fields) {
          "weights": {
            "searchable_field_name": relative_weight_value (positive #'s),
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
            }

            // (- or -)

            "freshness": {
              "boostingDuration": "..." (value representing timespan over which boosting occurs)
            }

            // (- or -)

            "distance": {
              "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)
              "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
            }

            // (- or -)

            "tag": {
              "tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field)
            }
          }
        ],
        "functionAggregation": (optional, applies only when functions are specified)
            "sum (default) | average | minimum | maximum | firstMatching"
      }
    ],
    "defaultScoringProfile": (optional) "...",
    ...

<a name="bkmk_indexref"></a>
## <a name="scoring-profile-property-reference"></a>計分的設定檔屬性參考

**附註**計分函數只能套用至可以篩選的欄位。

| 屬性 | 描述 |
|----------|-------------|
| `name`   | 所需。 這是計分的設定檔的名稱。 看來欄位的相同的命名慣例。 它必須以字母開頭，不能包含點，冒號或@符號，而且無法啟動包含字詞 「 azureSearch 」 （區分大小寫）。 |
| `text` | 包含線寬] 屬性。 |
| `weights` | 選用。 指定欄位名稱] 和 [相對粗細名稱 / 值組。 相對粗細必須是正整數或浮點數字。 您可以指定欄位名稱沒有相對應的寬度。 線寬] 用來指出相對於另一個欄位的重要性。 |
| `functions` | 選用。 請注意計分函數只套用到可篩選的欄位。 |
| `type` | 必須具備計分函數。 指出要使用的函數的類型。 有效的值包括`magnitude`， `freshness`，`distance`和`tag`。 您可以包含一個以上的函數，每個計分的設定檔中。 函數名稱必須小寫。 |
| `boost` | 必須具備計分函數。 正數的數字，做為加成原始的成績。 不能等於 1。 |
| `fieldName` | 必須具備計分函數。 計分函數只能套用至欄位的索引的欄位集合的組件與了。 此外，每一種函數類型介紹額外的限制 （最近使用的日期時間欄位，含整數或雙欄位的大小、 位置欄位的距離] 和 [字串或字串集合欄位的標籤）。 您只可以指定每個函數定義單一欄位。 例如，強度按兩次中使用相同的設定檔，您必須包含兩個定義強度，其中一個的每一個欄位。 |
| `interpolation` | 必須具備計分函數。 定義斜率的分數促進增加從範圍的開頭為範圍的結尾。 有效的值包括`linear`（預設）、 `constant`， `quadratic`，及`logarithmic`。 如需詳細資訊，請參閱[設定補](#bkmk_interpolation)。 |
| `magnitude` | 範圍計分函數用來變更排名根據數字欄位的值的範圍。 一些最常見的使用狀況範例，這是︰<ul><li>[星級評等︰ 變更分數的 「 星星評等] 欄位中的值。 當兩個項目相關時，將第一次顯示較高的評等項目。</li><li>邊界︰ 相關兩份文件時，零售商可能會想要拓展文件的第一次有較高的邊界。</li><li>按一下 [計數︰ 追蹤的應用程式，請按一下透過產品或頁面的動作，您可以使用強度增量也許會變得最流量的項目。</li><li>下載計數︰ 追蹤下載]，您拓展強度函數可讓項目的應用程式有最下載。</li></ul> |
| `magnitude:boostingRangeStart` | 設定開始的值強度計分的範圍。 值必須是整數或浮點數字。 1 到 4 的星級評等，這是 1。 邊界超過 50%，這是 50。 |
| `magnitude:boostingRangeEnd` | 設定結束的值強度計分的範圍。 值必須是整數或浮點數字。 1 到 4 的星級評等，這是 4。 |
| `magnitude:constantBoostBeyondRange` | 有效的值是 true 或 false （預設）。 若要設定時，則為 true，完整增量仍會套用至文件含有高於範圍上方結尾的目標欄位的值。 若為 false，此函數的增量將不會套用至文件的目標欄位的範圍內的值。 |
| `freshness` | 計分函數的有效期限用來變更排名分數 DateTimeOffset 欄位中的數值為根據的項目。 例如，較新的日期的項目可以排名高於較舊的項目。 （請注意，也有可能次序的項目，例如行事曆事件，未來的日期，例如項目往簡報可以會後面的項目高於進一步在未來）。在目前的服務版本中，將目前的時間修正一個範圍的結尾。 將另一端是根據過去的時間`boostingDuration`。 若要提高在未來的時間範圍使用負`boostingDuration`。 提高變更從最大值與最小範圍由插補速率套用至計分的設定檔 （請參閱下圖）。 若要反向套用 boosting 因素，選擇 [小於 1 增量 factor。 |
| `freshness:boostingDuration` | 設定後的過期期間會停止特定的文件。 [設定 boostingDuration]，請參閱 [#bkmk_boostdur] 下的一節的語法及範例。 |
| `distance` | 使用評分函數將會影響的距離如何為基礎的文件的成績關閉或最相對於參照的地理位置。 提供做為查詢中參數的一部分，參照的位置 (使用`scoringParameter`查詢參數) 為 lon，lat 引數。 |
| `distance:referencePointParameter` | 在查詢中用來作為參照位置傳遞參數。 scoringParameter 是查詢參數。 查詢參數的說明，請參閱[搜尋文件](search-api-2015-02-28-preview.md#SearchDocs)。 |
| `distance:boostingDistance` | 數字，指出公里 boosting 範圍的結束處的參考位置中的距離。 |
| `tag` | 計分函數的標籤用來影響根據標記文件和搜尋查詢中的文件的成績。 將提高文件，與搜尋查詢的標籤。 搜尋查詢的標籤，提供為每個搜尋邀請中的評分參數 (使用`scoringParameter`查詢參數)。 |
| `tag:tagsParameter` | 在查詢中，指定特定要求的標籤傳遞參數。 `scoringParameter`是查詢參數。 查詢參數的說明，請參閱[搜尋文件](search-api-2015-02-28-preview.md#SearchDocs)。 |
| `functionAggregation` | 選用。 適用於只指定函數是。 包含有效的值︰ `sum` （預設）、 `average`， `minimum`， `maximum`，及`firstMatching`。 搜尋分數會從多個變數，包括多個函數計算單一值。 這個屬性會指出所有函數等級如何組合成單一的彙總增量然後套用基本的文件的成績。 基本分數為基礎的文件] 和 [搜尋查詢計算 tf idf 值。 |
| `defaultScoringProfile` | 執行時搜尋要求時，如果有不指定任何計分的設定檔，則預設計分為使用 (tf-idf 只)。 預設的計分設定檔名稱可以在此設定，導致 Azure 搜尋指定特定的設定檔中搜尋要求時使用的設定檔。 |

<a name="bkmk_interpolation"></a>
## <a name="set-interpolations"></a>設定補

插補可讓您定義斜率的分數促進增加從範圍的開頭為範圍的結尾。 您可以使用下列補︰

  - `Linear`︰ 的最大] 和 [最小值的範圍內的項目，套用到項目增量會持續隨時間完成。 線性是預設插補計分的設定檔。
  - `Constant`︰ 的開始及結束範圍內的項目，常數增量也許會套用至排名的結果。
  - `Quadratic`︰ 在 comparison 若要線性插補含有持續降低增量，二次方一開始會減少較小的速度，然後為其接近範圍結束時，它將會減少更高的間隔。 標記計分函數中不允許此插補選項。
  - `Logarithmic`︰ 在 comparison 若要線性插補含有持續降低增量，對數一開始會減少較高的速度，然後將其接近範圍結束時，它時，會較小的間隔。 標記計分函數中不允許此插補選項。

<a name="Figure1"></a>
 ![][1]

<a name="bkmk_boostdur"></a>
## <a name="set-boostingduration"></a>設定 boostingDuration

`boostingDuration`是的有效期限函數的屬性。 設定到期日使用期間之後的特定的文件會停止。 例如，提高 10 天促銷期間內的產品線或品牌，您想要指定這些文件的 10 天期間為 「 P10D 」。 若要提高指定下週即將到來的事件或者 「-P7D 」。

`boostingDuration`必須格式化為 XSD 「 dayTimeDuration 」 的值 （限制 ISO 8601 持續時間值的子集）。 這個模式︰ `[-]P[nD][T[nH][nM][nS]]`。

下表提供幾個範例。

| 持續時間 | boostingDuration |
|----------|------------------|
| 1 天 | 「 P1D 」 |
| 2 天 12 小時 | 「 P2DT12H 」 |
| 15 分鐘 | 「 PT15M 」 |
| 30 天、 5 小時，10 分鐘的時間，並 6.334 秒 | 「 P30DT5H10M6.334S 」 |

如需更多範例，請參閱[XML 結構描述︰ 資料類型 （W3.org 網站）](http://www.w3.org/TR/xmlschema11-2/)。

**另請參閱**
MSDN 上的[Azure 搜尋服務 REST API](http://msdn.microsoft.com/library/azure/dn798935.aspx) <br/>
MSDN 上的 [[建立索引 （Azure 搜尋 API）](http://msdn.microsoft.com/library/azure/dn798941.aspx)<br/>
MSDN 上的[新增的搜尋索引的計分設定檔](http://msdn.microsoft.com/library/azure/dn798928.aspx)<br/>

<!--Image references-->
[1]: ./media/search-api-scoring-profiles-2015-02-28-Preview/scoring_interpolations.png
