<properties
    pageTitle="查詢您的 Azure 搜尋索引 |Microsoft Azure |裝載的雲端搜尋服務"
    description="建立 Azure 搜尋中的搜尋查詢，以及使用搜尋參數篩選和排序搜尋結果。"
    services="search"
    manager="jhubbard"
    documentationCenter=""
    authors="ashmaka"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="query-your-azure-search-index"></a>查詢您的 Azure 搜尋索引
> [AZURE.SELECTOR]
- [概觀](search-query-overview.md)
- [入口網站](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [其餘](search-query-rest-api.md)

送出 Azure 搜尋搜尋要求，當有可以同時應用程式的搜尋方塊中輸入的實際單字指定參數的數字。 這些查詢參數可讓您獲得全文檢索搜尋體驗的一些更深入的控制項。

以下是簡要說明 Azure 搜尋查詢參數的常見用法的清單。 查詢參數和其行為的完整的範圍，請參閱詳細資料頁的[REST API](https://msdn.microsoft.com/library/azure/dn798927.aspx)和[.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.searchparameters_properties.aspx)。

## <a name="types-of-queries"></a>查詢類型

Azure 搜尋提供許多選項來建立極功能強大的查詢。 查詢會使用兩個主要類型是`search`和`filter`。 A`search`查詢的所有_可搜尋_的欄位，在索引中，搜尋一或多個字詞，並以您的預期搜尋引擎，例如 Google 或 Bing 搭配使用的方式運作。 A`filter`查詢所有評估的布林運算式_可以篩選_索引中的欄位。 不同於`search`查詢`filter`查詢符合確切的欄位，這表示有區分大小寫字串欄位的內容。

您可以使用搜尋及篩選一併或分別。 如果您在一起使用它們，篩選會先套用至整個索引，並篩選的結果，然後執行搜尋。 篩選，因此可以是很有用的技巧，以改善查詢效能，因為他們減少搜尋查詢需要處理的文件的設定。

篩選運算式的語法是[OData 篩選語言](https://msdn.microsoft.com/library/azure/dn798921.aspx)的子集合。 搜尋查詢，您可以使用[簡化的語法](https://msdn.microsoft.com/library/azure/dn798920.aspx)或[Lucene 查詢語法](https://msdn.microsoft.com/library/azure/mt589323.aspx)這如下所示。

### <a name="simple-query-syntax"></a>簡單查詢語法
[簡單查詢語法](https://msdn.microsoft.com/library/azure/dn798920.aspx)是 Azure 搜尋中使用的預設查詢語言。 簡單查詢語法支援或不包括 AND、 一般搜尋運算子、 片語、 後置字元，及優先順序運算子。

### <a name="lucene-query-syntax"></a>Lucene 查詢語法
[Lucene 查詢語法](https://msdn.microsoft.com/library/azure/mt589323.aspx)可讓您使用所開發[Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)廣泛採用和生動查詢語言。

使用此查詢語法可讓您輕鬆地完成下列功能︰[欄位範圍查詢](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_fields)、[模糊搜尋](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_fuzzy)、[鄰近範圍搜尋](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_proximity)、[字詞促進](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_termboost)、[規則運算式搜尋](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_regex)、[萬用字元搜尋](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_wildcard)、[語法的基本概念](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_syntax)，以及[查詢使用布林值運算子](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_boolean)。



## <a name="ordering-results"></a>排序結果
接收時搜尋查詢的結果，您可以要求 Azure 搜尋可排序的特定欄位值的結果。 根據預設，Azure 搜尋排序依據的每個文件的搜尋分數，這衍生自[TF IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)排名搜尋結果。

如果您想要回到您的結果排序依據值以外的搜尋分數，您可以使用 Azure 搜尋`orderby`搜尋參數。 您可以指定的值`orderby`參數的欄位名稱及的來電至[`geo.distance()`函數](https://msdn.microsoft.com/library/azure/dn798921.aspx)休士頓值。 每個運算式可以後面`asc`表示結果的要求以遞增順序，並`desc`表示結果的要求以遞減順序。 預設順序遞增順序排列次序。

## <a name="paging"></a>分頁
Azure 搜尋可讓您更容易實作搜尋結果的分頁。 使用`top`和`skip`參數，您可以順利發行搜尋要求，可讓您更容易管理、 排序輕鬆啟用 [建議的搜尋 UI 作法的子集合中收到的搜尋結果總計的設定。 接收時這些縮小的結果，您也可以在搜尋結果的總設定接收文件的計數。

您可以進一步瞭解分頁，請參閱[如何頁面搜尋 Azure 搜尋結果](search-pagination-page-layout.md)的搜尋結果。


## <a name="hit-highlighting"></a>按下醒目提示
Azure 搜尋強調完全符合搜尋查詢的搜尋結果的部分更容易使用`highlight`， `highlightPreTag`，及`highlightPostTag`參數。 您可以指定的_搜尋_欄位應該比對的文字強調以及指定要附加到 [開始] 和 [符合的文字結尾的 Azure 搜尋的確切的字串標籤傳回。
