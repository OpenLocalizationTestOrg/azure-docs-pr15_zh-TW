<properties
    pageTitle="Azure 搜尋 Lucene 查詢範例 |Microsoft Azure 搜尋"
    description="Lucene 模糊搜尋、 鄰近範圍搜尋、 字詞促進、 規則運算式搜尋和萬用字元搜尋查詢語法。"
    services="search"
    documentationCenter=""
    authors="LiamCa"
    manager="pablocas"
    editor=""
    tags="Lucene query analyzer syntax"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="liamca"
/>

# <a name="lucene-query-syntax-examples-for-building-queries-in-azure-search"></a>建置 Azure 搜尋中的查詢 Lucene 查詢語法範例

當建構 Azure 搜尋查詢，您可以使用預設[簡單查詢語法](https://msdn.microsoft.com/library/azure/dn798920.aspx)或替代[Lucene 查詢剖析器 Azure 搜尋中](https://msdn.microsoft.com/library/azure/mt589323.aspx)。 Lucene 查詢剖析器支援更複雜的查詢建構，例如欄位範圍查詢、 模糊搜尋、 鄰近範圍搜尋、 字詞促進，及 reqular 運算式搜尋。

本文中，您可以逐步執行顯示 Lucene 查詢語法和結果並排的範例。 範例執行預先載入的搜尋索引中[JSFiddle](https://jsfiddle.net/)，線上的程式碼編輯器測試指令碼和 HTML。 

以滑鼠右鍵按一下另一個瀏覽器視窗中開啟 JSFiddle 查詢範例 Url。

> [AZURE.NOTE] 下列範例會運用包含的工作可根據[城市紐約 OpenData](https://nycopendata.socrata.com/)計劃所提供的資料集的搜尋索引。 此資料不應該考慮目前或完成。 索引是由 Microsoft 提供沙箱服務。 您不需要 Azure 訂閱或 Azure 搜尋，請嘗試這些查詢。

## <a name="viewing-the-examples-in-this-article"></a>在 [這份文件中檢視範例

所有的本文中的範例指定 Lucene 查詢剖析器，透過**queryType**搜尋參數。 使用時 Lucene 查詢剖析器，從您的程式碼，您會在每次要求來指定**queryType** 。  有效的值包括**簡單**|**完整**的**簡單**為預設值，並選取 [**完整**Lucene 查詢剖析器。 指定查詢參數的詳細資料，請參閱[搜尋文件 (Azure 搜尋服務 REST API)](https://msdn.microsoft.com/library/azure/dn798927.aspx) 。

**範例 1** --以滑鼠右鍵按一下下列查詢，在新的瀏覽器頁面的載入 JSFiddle 並執行查詢中開啟的程式碼片段︰
- [& queryType = 完整與搜尋 = *](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*)

此查詢會傳回文件從工作索引 （載入沙箱服務）

在新瀏覽器視窗中，您會看到 JavaScript 來源與 HTML 輸出並排顯示。 指令碼參照由範例 Url，本文中所提供的查詢。 例如，在**範例 1**，基礎的查詢是，如下所示︰

    http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*

通知查詢使用的預先設定的 Azure 搜尋索引，稱為 nycjobs。 **SearchFields**參數將搜尋限制只商務標題] 欄位。 **QueryType**會設定為 [**完整**，它會 Azure 搜尋這個查詢中使用 Lucene 查詢剖析器。

### <a name="fielded-query-operation"></a>擁有查詢作業

您可以藉由指定定義擁有查詢作業，位置欄位的一個單字，以及搜尋字詞也是一個單字或片語時，您也可以使用布林值運算子**fieldname:searchterm**建築修改本文中的範例。 部分範例包括下列各項︰

- business_title:(senior NOT junior)
- 狀態: (「 紐約 」 和 「 新紐澤西 」)

請務必將放在引號中的多個字串，如果您想要評估為單一項目，在此情況下，在 [位置] 欄位中的兩個不同城市搜尋兩個字串。 此外，請確定您看到的不大寫的運算子和 and。

指定**fieldname:searchterm**的欄位必須是可搜尋的欄位。 在欄位定義索引屬性的使用方式的詳細資訊，請參閱[建立索引 (Azure 搜尋服務 REST API)](https://msdn.microsoft.com/library/azure/dn798941.aspx) 。

## <a name="fuzzy-search"></a>模糊搜尋

模糊搜尋找到相符的項目有類似的建築的條款。 每個[Lucene 文件](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)，模糊搜尋根據[Damerau Levenshtein 距離](https://en.wikipedia.org/wiki/Damerau%e2%80%93Levenshtein_distance)。

若要執行的模糊搜尋，請使用 [波狀符號 「 ~ 」 符號的單字與選用的參數，介於 0 和 2，指定編輯距離的結尾。 例如，「 藍色 ~ 」 或 「 藍色 ~ 1"會傳回藍色，則藍色，且黏附。

**範例 2** ，以滑鼠右鍵按一下下列查詢程式碼片段來嘗試一下。 此查詢會搜尋的地方，但不資深字詞資深企業標題︰

- [& queryType = 完整與搜尋 = business_title:senior 不資深](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:senior+NOT+junior)

## <a name="proximity-search"></a>鄰近範圍搜尋

鄰近搜尋用來尋找字詞的會互相文件中。 插入波狀符號 「 ~ 」 片語結尾處的符號後面的建立鄰近範圍邊界的字數。 例如，「 旅館機場 」 ~ 5 會在文件中找到的字詞旅館和機場內的彼此的 5 個字。

**範例 3** ，以滑鼠右鍵按一下下列查詢程式碼片段。 此查詢會搜尋工作 （位置將其拼字錯誤） 字詞關聯︰

- [& queryType = 完整與搜尋 = business_title:asosiate ~](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:asosiate~)

**範例 4** ，以滑鼠右鍵按一下查詢。 搜尋 「 資深分析師 」 開頭的工作，均有一個以上的 word:

- [& queryType = 完整與搜尋 = business_title: 「 資深分析師 」 ~ 1](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~1)

**範例 5** -再試一次移除之間字詞 「 資深分析師 」 文字。

- [& queryType = 完整與搜尋 = business_title: 「 資深分析師 」 ~ 0](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~0)

## <a name="term-boosting"></a>字詞 Boosting

字詞促進指的是排名包含 boosted 的字詞，相對於文件不包含字詞的較高的文件。 這與不同計分的某些欄位，而不是特定的字詞，拓展計分的設定檔的設定檔。 下列範例中，可協助描述差異。

請考慮計分的設定檔，提升符合的特定欄位中，例如 musicstoreindex 範例中的**內容類型**。 字詞促進無法用來進一步拓展特定搜尋字詞高於其他人。 例如，「 岩石 ^2 電子 」 會拓展高於其他可搜尋的欄位，在索引中的 [**內容類型**] 欄位中包含搜尋字詞的文件。 此外，包含字詞 「 岩石 」 的文件會比其他搜尋字詞 「 電子 」 當做字詞增量值 (2) 更高的排名。

拓展字詞，請使用插入號，「 ^ 」，與增量因子變異數 （數值） 結尾的搜尋的字詞的符號。 較高的增量因素、 更多相關字詞會相對於其他搜尋字詞。 根據預設，增量因子變異數為 1。 雖然增量因子變異數必須是正數，但它可以是小於 1 （例如，0.2）。

**範例 6** ，以滑鼠右鍵按一下查詢。 搜尋字詞 「 電腦分析師 「 我們看到沒有結果與文字電腦和分析師，但尚頂端的結果分析師工作的工作。

- [& queryType = 完整與搜尋 = business_title:computer 分析師](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

**範例 7** -再試一次，此時間促進結果與字詞電腦字詞分析師透過如果這兩個文字不存在。

- [& queryType = 完整與搜尋 = business_title:computer ^2 分析師](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

## <a name="regular-expression"></a>規則運算式

規則運算式搜尋找到符合根據內容之間正斜線 「 / 」，為[RegExp 類別](http://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html)中的記錄。

**範例 8** ，以滑鼠右鍵按一下查詢。 搜尋字詞資深或基礎使用的工作。

- `&queryType=full&$select=business_title&search=business_title:/(Sen|Jun)ior/`

在頁面上，此範例中的 URL 會不正確呈現。 因應措施，複製下列的 URL，然後貼到瀏覽器 URL 位址︰    `http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26queryType=full%26$select=business_title%26search=business_title:/(Sen|Jun)ior/)`


## <a name="wildcard-search"></a>萬用字元搜尋

您可以使用通常可辨識的語法的倍數 (\*) 或單一 (?) 字元萬用字元搜尋。 請注意 Lucene 查詢剖析器支援使用單一字詞，與不片語這些符號。

**範例 9** ，以滑鼠右鍵按一下查詢。 搜尋包含 「 程式 」，其中會包含商務標題的程式設計程式設計的條款與前置字元的工作。

- [& queryType = 完整 & $select = business_title 與搜尋 = business_title:prog*](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26queryType=full%26$select=business_title%26search=business_title:prog*)

您無法使用 * 或嗎？ 符號搜尋的第一個字元。


## <a name="next-steps"></a>後續步驟

請嘗試在您的程式碼中指定 Lucene 查詢剖析器。 下列連結會說明如何設定搜尋查詢.NET 和 REST API 兩者均適用。 連結使用預設簡單的語法，因此您將需要套用複習所學內容從指定**queryType**這篇文章。

- [使用.NET SDK 您 Azure 搜尋索引的查詢](search-query-dotnet.md)
- [您使用 REST API 的 Azure 搜尋索引的查詢](search-query-rest-api.md)


 