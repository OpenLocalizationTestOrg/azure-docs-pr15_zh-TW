<properties 
    pageTitle="如何在 Azure 搜尋實作多面向導覽 |Microsoft Azure |搜尋導覽類別" 
    description="新增多面向導覽的整合與 Azure 搜尋、 Microsoft Azure 的雲端裝載於搜尋服務應用程式。" 
    services="search" 
    documentationCenter="" 
    authors="HeidiSteen" 
    manager="mblythe" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="10/17/2016" 
    ms.author="heidist"/>

#<a name="how-to-implement-faceted-navigation-in-azure-search"></a>如何在 Azure 搜尋實作多面向導覽

多面向導覽會提供自我引導向下切入功能，在搜尋應用程式中的篩選機制。 時可能不熟悉字詞 「 多面向導覽 」，幾乎是假設您已使用前一。 下列範例所示，多面向導覽只是用來篩選結果的類別。

## <a name="what-it-looks-like"></a>看起來像是

 ![][1]
  
Facet 可協助您找到您會需要的內容，同時確保就不會收到零結果。 開發人員]，為 facet 可讓您瀏覽您的搜尋中華的最有用的搜尋準則的方式公開。 線上零售應用程式中，多面向導覽通常內建在商標、 部門 （兒童鞋）、 大小、 價格、 常用性，並評等。 

實作多面向導覽會透過搜尋技術，可以很複雜。 Azure 搜尋多面向導覽內建在查詢時，使用先前指定在您的結構描述中使用屬性的欄位。 在您的應用程式建立的查詢，查詢必須傳送*多面向查詢參數*] 才會收到的文件結果集的可用多面向篩選值。 實際修剪文件結果設定、 應用程式必須套用`$filter`運算式。

應用程式開發而言撰寫建構查詢的程式碼會構成大部分的工作。 您想要從多面向導覽的應用程式行為許多提供的服務，包括設定範圍並取得多面向結果計數的內建支援。 服務也包含直覺式的預設值，可協助您避免龐大瀏覽結構。 

本文包含下列各節︰

- [建立方式](#howtobuildit)
- [建立簡報圖層](#presentationlayer)
- [建立索引](#buildindex)
- [檢查資料品質](#checkdata)
- [建立查詢](#buildquery)
- [如何控制多面向導覽的秘訣](#tips)
- [多面向導覽根據範圍的值](#rangefacets)
- [根據 GeoPoints 多面向導覽](#geofacets)
- [試試看](#tryitout)

##<a name="why-use-it"></a>為什麼要使用其
最有效搜尋應用程式有多個互動模型以外的搜尋方塊。 多面向導覽] 是提供方便的替代選項，以手動輸入複雜搜尋運算式搜尋，請指向替代的項目。

##<a name="know-the-fundamentals"></a>瞭解基本概念

如果您是新搜尋開發的多面向導覽的最佳方式就是它顯示自我引導搜尋的可能性。 它是一種向下切入搜尋體驗，根據預先定義的篩選，用來快速縮小搜尋結果，透過點按一下動作下。 

**互動模型**

多面向導覽的搜尋體驗是反覆運算，所以讓我們先瞭解其為一系列角色的使用者動作來回應的查詢。

起始點會提供多面向導覽，通常是放在位於周邊視覺範圍的應用程式頁面。 多面向導覽，通常會樹狀結構，含核取方塊，為每個值或可點選的文字。 

1.  傳送至 Azure 搜尋查詢指定一個或多個多面向查詢參數透過多面向導覽結構。 查詢執行個體，可能會包含`facet=Rating`，可能與`:values`或`:sort`，進一步縮小簡報的選項。
2.  在 [圖層簡報呈現提供多面向導覽，使用指定的要求 facet 搜尋頁面。
3.  指定包含評等的多面向導覽結構，使用者按一下 「 4 」 表示只 4 或更新版本的評等的產品應該會顯示。 
4.  在回應時，應用程式傳送包含的查詢`$filter=Rating ge 4` 
5.  在 [圖層簡報更新的頁面，顯示精簡的結果集，包含符合新的準則僅這些項目 (在此情況下，產品評等 4 和最)。

多面向查詢參數，但請勿使用查詢輸入混淆。 不會做選取準則的查詢。 相反地，將多面向查詢參數做為輸入恢復在回應中瀏覽結構。 為您提供每個多面向查詢參數，Azure 搜尋會評估多少文件是在每個多面向值部分的結果。

請注意`$filter`在步驟 4。 這是多面向導覽的重點。 雖然獨立 API 中 facet 和篩選，您必須同時進行您想要的體驗。 

**設計模式**

應用程式碼中的模式就是使用多面向查詢參數傳回多面向導覽結構，以及多面向結果，以及處理在 $filter 運算式。 考慮的`$filter`為實際調整後的程式碼的搜尋結果的運算式傳回至簡報的圖層。 指定色彩多面向，按一下 [色彩紅色透過實作`$filter`運算式，可選擇色彩的紅色這些項目。 

**Azure 搜尋查詢的基本概念**

在 Azure 搜尋，透過 （每個的說明，請參閱[搜尋文件](http://msdn.microsoft.com/library/azure/dn798927.aspx)） 的一或多個查詢參數指定要求。 查詢參數沒有必要，但您必須至少一個有效查詢的順序。

透過一或兩個運算式達成精確度，通常為可篩選出無關點擊，了解︰

- **搜尋 =**<br/>
這個參數值構成搜尋運算式。 可能是單一的一段文字或包含多個字詞與運算子的複雜搜尋運算式。 在伺服器上，搜尋運算式用於全文檢索，查詢相符的字詞，將結果傳回 rank 順序索引中的搜尋欄位中。 如果您設定`search`null 」、 「 查詢執行是透過整個索引 (也就是`search=*`)。 在此例中其他元素的查詢，例如`$filter`或計分的設定檔，會傳回影響哪些文件的主要因素`($filter`) 以何種順序 (`scoringProfile`或`$orderb`y)。

- **$filter =**<br/>
篩選是強大的機制，限制特定的文件屬性的數值為根據的搜尋結果的大小。 A`$filter`評估第一次，後面接著產生的可用的值和對應的計算每個值的 faceting 邏輯

複雜搜尋運算式將會減少查詢的效能。 可能的話，請利用建構完善的篩選運算式增加精確度及改善查詢效能。

若要進一步瞭解如何篩選會將更精確，比較複雜搜尋運算式為包含的篩選運算式︰

- `GET /indexes/hotel/docs?search=lodging budget +Seattle –motel +parking`

- `GET /indexes/hotel/docs?search=lodging&$filter=City eq ‘Seattle’ and Parking and Type ne ‘motel’`

雖然這兩個查詢是有效的第二個是如果您正在尋找的非屋與停車西雅圖上司。 第一個查詢依賴特定文字被提及或尚未提及的字串變數，例如名稱、 描述及任何其他包含可搜尋資料的欄位。 第二個查詢的結構化資料的精確符合看起來，可能會更精確。

在包含多面向導覽的應用程式，您會要請務必在多面向導覽結構的每個使用者動作伴隨縮小搜尋結果，透過篩選運算式。

<a name="howtobuildit"></a>
##<a name="how-to-build-it"></a>建立方式

建置邀請，但必須使用預先定義的項目，在您的結構描述中的應用程式碼中實作 Azure 搜尋中的多面向導覽。

預先定義的索引是在搜尋中`Facetable [true|false]`設定啟用或停用多面向導覽結構中的使用所選欄位的索引屬性。 不含`"Facetable" = true`，功能變數無法用於多面向導覽。

在查詢時，應用程式碼會建立包含要求`facet=[string]`，要求參數提供多面向所要的欄位。 查詢可以有多個 facet，例如`&facet=color&facet=category&facet=rating`，每個以連字號 (&) 字元。

應用程式碼也必須建構`$filter`來處理多面向導覽中的按一下事件的運算式。 A`$filter`減少搜尋結果中，使用多面向值做為篩選準則。

Azure 搜尋會傳回搜尋結果中，每個使用者，以及多面向導覽的更新輸入搜尋。 在 Azure 搜尋多面向導覽單層次建構，為多面向的值，而計算的結果數目找到的每一個。

簡報中的圖層程式碼提供的使用者體驗。 它應該列出多面向導覽，例如標籤、 值、 核取方塊及計數的組成部分。 Azure 搜尋 REST API 平台診斷，所以請使用任何語言與您想要的平台。 重要的是包含支援累加重新整理]，更新 UI 狀態，選取每個額外的多面向時的使用者介面項目。 

在下列區段中，我們深入瞭解如何建立每一個組件，在 [圖層簡報開始著手。

<a name="presentationlayer"></a>
##<a name="build-the-presentation-layer"></a>建立簡報圖層

從簡報圖層的工作可協助您發掘的需求，可能會遺漏，並了解哪些功能是基本搜尋體驗。

多面向導覽畫面中，以您網頁或應用程式的頁面會顯示多面向導覽結構、 偵測到使用者輸入在頁面上，並插入變更的項目。 

Web 應用程式，AJAX 常用，展示層因為其可讓您重新整理累加的變更。 您也可以使用 ASP.NET MVC 或任何其他視覺效果的平台，可以透過 HTTP 連線到 Azure 搜尋服務。 在本文- **AdventureWorks 目錄**– 參照的範例應用程式的問題是 ASP.NET MVC 應用程式。

下列範例中，將**index.cshtml**檔案的範例應用程式中，建立您的搜尋結果頁面中顯示多面向導覽的動態 HTML 結構。 在範例中，多面向導覽內建在 [搜尋結果] 頁面，並顯示之後使用者已送出搜尋字詞。

通知的每個多面向都有標籤 （[色彩]，[類別] 價格），將繫結至多面向欄位 （色彩、 類別名稱，listPrice） 和`.count`參數，用來傳回的項目數找到多面向結果。

  ![][2]
 

> [AZURE.TIP] 設計時搜尋結果頁面，請記得要新增為清除 facet 機制。 如果您使用的核取方塊，使用者可以輕鬆地 intuit 如何清除篩選。 針對其他版面配置，您可能需要的階層連結圖樣或另一個創意的方法。 例如，AdventureWorks 目錄範例應用程式中，您可以按一下標題，AdventureWorks 目錄，重設 [搜尋] 頁面。

<a name="buildindex"></a>
##<a name="build-the-index"></a>建立索引

Faceting 已啟用索引，透過這個索引屬性中的欄位基礎︰ `"Facetable": true`。  
所有可能無法使用多面向導覽中的欄位類型都會`Facetable`預設。 這類欄位類型包含`Edm.String`， `Edm.DateTimeOffset`，所有數值欄位類型 (基本上，所有的欄位類型是以外的 facetable `Edm.GeographyPoint`，其中多面向導覽中就不能使用)。 

建立索引時, 多面向導覽的最佳作法是明確的欄位，永遠不能當做多面向關閉 faceting。  特別是單一值，例如 ID 或產品名稱的字串欄位設定為`"Facetable": false`以避免意外 （和無效） 使用多面向導覽中的。

以下是 AdventureWorks 目錄範例應用程式 （修剪的某些屬性，以減少整體大小） 的結構描述︰

 ![][3]
 
請注意，`Facetable`不應作為 facet，例如 ID 或名稱的字串欄位已關閉。 開啟 faceting 關閉，您不需要保持索引的大小，可協助，通常可改善效能。

> [AZURE.TIP] 最佳作法是包含完整的每個欄位的索引屬性。 雖然`Facetable`預設為開啟執行幾乎所有欄位，刻意設定的每個屬性可協助您思考每個結構描述決策的含意。 

<a name="checkdata"></a>
##<a name="check-for-data-quality"></a>檢查資料品質 

當開發任何資料中心的應用程式，準備資料通常是其中一項工作的較大的組件。 搜尋應用程式也不例外。 資料的品質有直接關係到多面向導覽結構是否具體化如您預期，以及其效果能夠協助您建構減少結果的篩選設定。

在 Azure 搜尋，搜尋中華的正確從填入索引的文件。 文件提供來計算 facet 的值。 如果您想要的品牌或價格的一環，每份文件包含值*BrandName*和*ProductPrice*的有效、 一致和生產力作為篩選選項。

以下列出所要刪除的幾個提醒︰

- 您想要依多面向每個欄位，請先考慮是否有包含自我引導搜尋適合做為篩選的值。 值應該要簡短、 描述性的文字，且充分特殊提供清除選擇其他選項。
- 拼字錯誤或幾乎相符的值。 如果您多面向上色彩和欄位值納入橘色與 Ornage （拼字錯誤），請根據 [色彩] 欄位多面向想選擇兩者。
- 混合大小寫的文字可以也會造成破壞，多面向導覽中，橘色與橘色顯示為兩個不同的值。 
- 單一和複數版相同的值會導致每個個別的一環。

您可以想見，注意準備資料是不可或缺的有效多面向導覽的一部分。

<a name="buildquery"></a>
##<a name="build-the-query"></a>建立查詢

您撰寫的建立查詢的程式碼應該指定所有的部分有效查詢，包括搜尋運算式，facet、 篩選、 分數設定檔 – 公式要求的任何項目。 在此區段中，我們會探索 facet 會配合查詢，和如何篩選搭配使用的是 facet 提供精簡的結果集。

範例，通常會開始的好地方。 下列範例中，[ **CatalogSearch.cs**的檔案，將建置建立多面向導覽根據色彩、 類別和價格的要求。 

請注意，facet 是整數，此範例應用程式中。 AdventureWorks 目錄] 中的搜尋體驗被專為多面向導覽和篩選。 這是出現在頁面中的多面向導覽的顯眼的位置。 範例應用程式為搜尋方法 （如建構範例應用程式中） 上的內容，包括 URI 參數 facet （色彩、 類別、 價格）。

  ![][4]
 
多面向查詢參數設為 [功能變數，根據的資料類型，可以進一步參數化以逗號分隔的清單，包括`count:<integer>`， `sort:<>`， `intervals:<integer>`，及`values:<list>`。 設定 [範圍時數字資料，支援值清單。 如需使用方式的詳細資訊，請參閱[搜尋文件 (Azure 搜尋 API)](http://msdn.microsoft.com/library/azure/dn798927.aspx) 。

Facet，以及撰寫您的應用程式要求也應該建立篩選，以縮小候選多面向值選取範圍為基礎的文件的設定。 多面向導覽的 safetystockllevel 存放區中，提供問題線索喜歡 」 何種色彩、 製造商和自行車類型可提供 」，同時篩選的回答問題，例如 「 的確切自行車會變成紅色上, 顯示的山脈自行車此價格範圍]。

當使用者按下 「 紅色 」 表示應該會顯示紅色的產品時，包括應用程式傳送的下一個查詢`$filter=Color eq ‘Red’`。

## <a name="best-practices-for-faceted-navigation"></a>多面向導覽的最佳作法

下列清單摘要列出一些最佳作法。

- **精確度**<br/>
使用 [篩選]。 如果您是仰賴單獨因只搜尋運算式可能造成傳回任何其欄位中沒有精準多面向值的文件。 

- **目標欄位**<br/>
在多面向向下切入，您通常要僅包含的所有可搜尋的欄位不任何一處有多面向值在特定的 （多面向） 欄位中，文件。 新增篩選導向只能在 [多面向相符的值] 欄位中搜尋服務來強調目標欄位。

- **索引效率**<br/>
如果您的應用程式中使用多面向導覽獨佔 （也就是沒有搜尋方塊中），而且可以標示為欄位`searchable=false`，`facetable=true`來產生更精簡的索引。 此外，編製索引發生只在整個多面向的值，word 符號或編製索引元件組件的多個字值。

- **效能**<br/>
篩選縮小搜尋候選文件的設定，並將其從排名排除。 如果您有大量的文件時，使用 [向下的 [謹慎選擇多面向切入通常讓您大幅較佳的效能。


<a name="tips"></a> 
##<a name="tips-on-how-to-control-faceted-navigation"></a>如何控制多面向導覽的秘訣

以下是秘訣具的工作表上的特定問題的指引。

**在多面向導覽中新增的每一個欄位的標籤**

在 [HTML] 或 [表單 (**index.cshtml**範例應用程式中)，通常被定義標籤。 沒有任何 API 的 Azure 搜尋多面向導覽標籤或任何其他種類的中繼資料。

**定義欄位可為多面向**

索引的結構描述決定哪些欄位可做為多面向的回收。 假設欄位 facetable，查詢會指定多面向所要的欄位。 依據您是 faceting] 欄位會提供] 標籤底下顯示的值。 

從索引中擷取出現在每個標籤] 下的值。 比方說，如果多面向欄位是*色彩*，適用於其他篩選的值會 （紅色、 黑色，等等） 該欄位的值。

針對數值和日期時間值僅，您可以明確設定值多面向欄位 (例如， `facet=Rating,values:1|2|3|4|5`)。 允許值清單以簡化多面向結果將連續的範圍 （根據數值或時間週期任一範圍） 分隔這些欄位類型。 

**修剪多面向結果**

多面向結果為尋找符合多面向字詞的搜尋結果中的文件。 在下列範例中，在搜尋結果中的*雲端運算*，254 的項目也有*內部規格*做為內容類型。 項目不一定會互斥。 如果項目符合準則的兩個篩選，請將其計算每個項目。 這可能是何時上的 faceting`Collection(Edm.String)`欄位，通常用來實作標記文件。

        Search term: "cloud computing"
        Content type
           Internal specification (254)
           Video (10) 

一般而言，如果您發現多面向結果會持續太大，我們建議您新增多個篩選，在舊版的區段中，若要授與您的應用程式更多選項來縮小搜尋範圍，所述。

**限制多面向導覽中的項目**

導覽樹狀目錄中每個多面向] 欄位中，有 10 個值的預設限制。 此預設適合用於瀏覽結構因為它會將值清單要管理的大小。 若要覆寫預設值，可以指派計算值。

- `&facet=city,count:5`指定多面向結果傳回只在頂端排名結果中找到的第一次 5 城市。 指定 32 相符的項目，「 機場 」 的搜尋字詞，如果查詢指定`&facet=city,count:5`，只在前五個唯一城市最多搜尋結果中的文件包含多面向結果中。

請注意多面向結果與搜尋結果之間的差異。 搜尋結果的符合查詢的所有文件。 多面向結果為每個多面向值相符的項目。 在範例中，搜尋結果中會包含在多面向分類清單 (在本例中為 5) 中的縣/市名稱。 他或她清除 facet，或選擇其他 facet 除了縣/市時就會顯示給使用者的多面向導覽透過篩選出的結果。 

> [AZURE.NOTE] 討論`count`一個以上的類型，則可以混亂。 下表提供簡短摘要字詞 Azure 搜尋 API、 程式碼範例和文件中的使用方式。 

- `@colorFacet.count`<br/>
在簡報的程式碼，您應該會看到計數參數上多面向，用來顯示多面向結果的數字。 在多面向結果中，count 會指出符合多面向字詞或範圍的文件數。

- `&facet=City,count:12`<br/>
在多面向查詢中，您可以設定計算值。  預設值為 10，但您可以將其高或較低。 設定`count:12`取得頂端 12 符合多面向結果中的文件計數。

- "`@odata.count`"<br/>
在查詢回應時，此值會指出搜尋結果中的相符項目數目。 平均大於結合，因為符合搜尋字詞的項目存在的所有多面向結果的總和，但有多面向值相符的項目。


**多面向導覽中的層級** 

如上所述，沒有直接支援的巢狀階層中的 facet。 預設工作，多面向導覽只支援一個層級的篩選。 不過，會有因應措施。 您可以編碼中的階層式多面向結構`Collection(Edm.String)`有一個項目指向每個階層。 實作此因應措施是本文中的範圍之內，但您可以閱讀[範例 OData](http://msdn.microsoft.com/library/ff478141.aspx)集合。 

**驗證欄位**

如果您建立的動態根據不受信任的使用者輸入的 facet 清單，您應該驗證多面向欄位的名稱不正確，或當建置 Url 使用逸出名稱`Uri.EscapeDataString()`中.NET 或在您的平台的選項。

**在多面向結果中的計算**

多面向查詢至篩選時，您可能會想要保留多面向陳述式 (例如， `facet=Rating&$filter=Rating ge 4`)。 技術上而言，多面向 = 不需要評等，但保留其傳回的評等的多面向值的計算，4 及更新版本。 例如，如果使用者按一下 「 4 」 的查詢包含大於或等於 「 4 」 的篩選，計算會傳回 4 的每個評等和設定。  

**多面向計數 sharding 影響**

在某些情況下，您可能會發現多面向計數不符合結果集 （請參閱[Azure 搜尋 （論壇文章） 中的多面向導覽](https://social.msdn.microsoft.com/Forums/azure/06461173-ea26-4e6a-9545-fbbd7ee61c8f/faceting-on-azure-search?forum=azuresearch)）。

多面向計數可能是因為 sharding 架構不正確的。 每個的搜尋索引中有多個擊碎，和每個報表前 n 個 facet 依文件數，然後就會合併到單一結果。 如果某些擊碎有許多相符的值，而其他人有小於，您可能會發現部分多面向值遺失或下-計算結果。

雖然這種行為可能會變更，也可以隨時，如果今天發生此問題，您可以以避免手動 inflating 計數︰<number>強制執行完整的報告，從每個晶怪很大的數字。 如果計算的值︰ 大於或等於] 欄位中的唯一值的數目，您一定正確的結果。 不過，當文件計數很高，有效能產生負面影響，因此使用這個選項謹慎。

<a name="rangefacets"></a>
##<a name="facet-navigation-based-on-a-range-values"></a>範圍數值為根據的多面向導覽

Faceting 範圍是常見的搜尋應用程式需求。 支援數值資料] 和 [日期時間值的範圍。 您可以閱讀更多有關[搜尋文件 (Azure 搜尋 API)](http://msdn.microsoft.com/library/azure/dn798927.aspx)中的每一種方法。

Azure 搜尋會提供兩種方法來計算某個範圍，以簡化範圍建築。 Azure 搜尋這兩種方法，會建立適當提供您所輸入的範圍。 例如，如果您指定為 10 的範圍值 | 20 | 30，就會自動建立範圍中的 0-10，10-20、 20-30 個。 範例應用程式中移除任何是空的時間間隔。 

**方法 1︰ 使用時間間隔參數**<br/>
若要設定價格 facet $10 幅度地，您可以指定︰`&facet=price,interval:10`


**方法 2︰ 使用值清單**<br/>
數字資料，您可以使用值清單。  請考慮 listPrice，呈現，如下所示的多面向範圍︰

  ![][5]

使用值清單**CatalogSearch.cs**檔案中指定的範圍︰

    facet=listPrice,values:10|25|100|500|1000|2500

每個範圍內建使用 0 作為起點，為端點，清單中的值，然後再加以剪裁建立不連續的時間間隔的上一個範圍。 Azure 搜尋會這樣做為多面向導覽的一部分。 您沒有為組織結構每個間隔撰寫程式碼。

### <a name="build-a-filter-for-facet-ranges"></a>建立多面向範圍的篩選 ###

若要篩選以使用者選取範圍文件，您可以使用`"ge"`和`"lt"`篩選中定義的範圍結束點的兩個部分運算式的運算子。 例如，如果使用者選擇範圍 10-25，就會篩選`$filter=listPrice ge 10 and listPrice lt 25`。

在範例應用程式中的篩選運算式會使用**priceFrom**和**priceTo**參數設定結束點。 **BuildFilter**方法**CatalogSearch.cs**中的包含可讓您的文件範圍中的篩選運算式。

  ![][6]

<a name="geofacets"></a> 
##<a name="filtered-navigation-based-on-geopoints"></a>根據 GeoPoints 的篩選的功能

它的常見若要查看篩選的協助您選擇儲存、 餐廳或根據您目前位置到其鄰近範圍的目的地。 當此類型的篩選可能看起來多面向導覽時，時實際上只篩選。 我們所提，以下的特別尋找特定的設計問題的實作建議的人。

有兩種休士頓函數 Azure 搜尋、 **geo.distance**和**geo.intersects**。

- **Geo.distance**函數傳回公里中的兩個點之間的距離，一個功能變數與一個是常數傳遞做為篩選的一部分。 

- **Geo.intersects**函數會傳回指定多邊形，其中點是欄位，而多邊形已指定為常數座標傳遞屬於篩選清單中指定的點時，則為 true。

您可以找到篩選範例[OData 運算式](http://msdn.microsoft.com/library/azure/dn798921.aspx)語法 （Azure 搜尋）。

<a name="tryitout"></a>
##<a name="try-it-out"></a>試試看

Codeplex 上 azure 搜尋 Adventure Works 示範包含參考本文中的範例。 當您使用的搜尋結果，請觀看變更查詢建築的 URL。 這個應用程式的問題 uri 附加 facet，當您選取每個項目。

1.  範例應用程式設定成使用您的服務 URL 及 api 金鑰。 

    請注意 Program.cs CatalogIndexer 專案檔案中所定義的結構描述。 它會指定色彩、 listPrice、 大小、 寬度、 類別名稱，以及 modelName facetable 欄位。  只是其中幾項這些 （色彩、 listPrice、 類別名稱） 的實際實作多面向導覽中。

3.  執行應用程式。 

    首先，只是 [搜尋] 方塊會顯示。 您可以按一下 [立即上手所有結果，[搜尋] 按鈕，或輸入搜尋字詞。

    ![][7]
 
4.  輸入搜尋字詞，例如 safetystockllevel，然後按一下 [**搜尋**]。 快速執行查詢。
 
    多面向導覽結構，也會傳回搜尋結果。  在 [URL，facet 色彩、 類別和價格為 null。 在 [搜尋結果] 頁面多面向導覽結構包含計算每個多面向結果。

     ![][8]
 
5.  按一下 [色彩、 類別和價格範圍]。 Facet null 初始的搜尋，但為其對值進行時，搜尋結果會修剪的不再符合的項目。 請注意，URI 挑選查詢所做的變更。

    ![][9]
 
6.  若要讓您可以嘗試不同的查詢行為，請清除 [多面向查詢，請按一下 [ **AdventureWorks 目錄**頂端的頁面]。

    ![][10]
 
<a name="nextstep"></a>
##<a name="next-step"></a>下一步

若要測試您的知識，您可以新增*modelName*多面向欄位。 索引已設定為此多面向，因此沒有變更索引。 但您將需要修改模型，包括新的一環，並將 [多面向] 欄位新增至查詢建構函式的 HTML。

如需深入多面向導覽的設計原則的詳細資訊，我們建議下列連結︰

- [搜尋多面向設計](http://www.uie.com/articles/faceted_search/)
- [設計模式︰ 多面向導覽](http://alistapart.com/article/design-patterns-faceted-navigation)

您也可以觀看[Azure 搜尋深度剖析](http://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410)。 在 45:25，有示範如何實作 facet。

<!--Anchors-->
[How to build it]: #howtobuildit
[Build the presentation layer]: #presentationlayer
[Build the index]: #buildindex
[Check for data quality]: #checkdata
[Build the query]: #buildquery
[Tips on how to control faceted navigation]: #tips
[Faceted navigation based on range values]: #rangefacets
[Faceted navigation based on GeoPoints]: #geofacets
[Try it out]: #tryitout

<!--Image references-->
[1]: ./media/search-faceted-navigation/Facet-1-slide.PNG
[2]: ./media/search-faceted-navigation/Facet-2-CSHTML.PNG
[3]: ./media/search-faceted-navigation/Facet-3-schema.PNG
[4]: ./media/search-faceted-navigation/Facet-4-SearchMethod.PNG
[5]: ./media/search-faceted-navigation/Facet-5-Prices.PNG
[6]: ./media/search-faceted-navigation/Facet-6-buildfilter.PNG
[7]: ./media/search-faceted-navigation/Facet-7-appstart.png
[8]: ./media/search-faceted-navigation/Facet-8-appbike.png
[9]: ./media/search-faceted-navigation/Facet-9-appbikefaceted.png
[10]: ./media/search-faceted-navigation/Facet-10-appTitle.png

<!--Link references-->
[Designing for Faceted Search]: http://www.uie.com/articles/faceted_search/
[Design Patterns: Faceted Navigation]: http://alistapart.com/article/design-patterns-faceted-navigation
[Create your first application]: search-create-first-solution.md
[OData expression syntax (Azure Search)]: http://msdn.microsoft.com/library/azure/dn798921.aspx
[Azure Search Adventure Works Demo]: https://azuresearchadventureworksdemo.codeplex.com/
[http://www.odata.org/documentation/odata-version-2-0/overview/]: http://www.odata.org/documentation/odata-version-2-0/overview/ 
[Faceting on Azure Search forum post]: ../faceting-on-azure-search.md?forum=azuresearch
[Search Documents (Azure Search API)]: http://msdn.microsoft.com/library/azure/dn798927.aspx

 