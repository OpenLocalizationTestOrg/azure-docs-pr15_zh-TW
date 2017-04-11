<properties
   pageTitle="Azure 搜尋中的多種語言中建立的文件索引 |Microsoft Azure |裝載的雲端搜尋服務"
   description=" Azure 搜尋支援 56 的語言，運用語言分析器從 microsoft Lucene 和自然語言處理技術。"
   services="search"
   documentationCenter=""
   authors="yahnoosh"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="na"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="07/14/2016"
   ms.author="jlembicz"/>

# <a name="create-an-index-for-documents-in-multiple-languages-in-azure-search"></a>Azure 搜尋中的多種語言中建立文件的索引
> [AZURE.SELECTOR]
- [入口網站](search-language-support.md)
- [其餘](https://msdn.microsoft.com/library/azure/dn879793.aspx)
- [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.analyzername.aspx)

舉凡的語言分析器是這麼簡單索引定義中的搜尋欄位上設定屬性。 現在您可以執行此步驟，在入口網站。

以下是可讓使用者定義的索引結構描述的 Azure 搜尋的 Azure 入口網站刀的螢幕擷取畫面。 從這個刀，使用者可以建立的所有欄位，並針對每個設定分析器屬性。

> [AZURE.IMPORTANT] 您可以只設定語言分析器期間欄位定義為中，建立新的索引，從頭時，或將新欄位新增至現有的索引。 請確定您完全指定所有的屬性，包括分析，同時建立欄位。 您將無法編輯的屬性，或變更分析類型，當您儲存變更。

## <a name="define-a-new-field-definition"></a>定義新的欄位定義

1. 登入[Azure 入口網站](https://portal.azure.com)，並開啟您的搜尋服務的服務刀。
2. 若要開始新的索引，服務儀表板頂端的命令列中按一下 [**新增索引**，或開啟您要新增至現有的索引的新欄位上設定分析器現有的索引。
3. 欄位刀出現時，可讓您定義的結構描述的索引選項包括 [分析] 索引標籤用於選擇語言分析。
4. 在欄位中，首先欄位定義提供的名稱，選擇資料類型，及設定屬性標示欄位為完整的文字可搜尋，在搜尋結果中，使用多面向導覽結構，可以擷取可排序，等等。 
5. 再繼續進行下一個欄位中，開啟 [**分析**] 索引標籤。 

   
![][1]
*若要選取分析程式，請按一下 [欄位刀 [分析] 索引標籤*

## <a name="choose-an-analyzer"></a>選擇分析

6. 捲動以找出您要定義欄位。 
7. 如果您還沒有標示為 [可搜尋的欄位，請立即將其標示為**Searchable**按一下核取方塊。
8. 按一下 [分析] 區域，以顯示可用分析器的清單。
9. 選擇您想要使用的分析]。

![][2]
*選取其中一個支援分析器的每一個欄位*

根據預設，所有可搜尋的欄位會使用[標準 Lucene 分析器](http://lucene.apache.org/core/4_10_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html)這是語言診斷。 若要檢視支援分析器的完整清單，請參閱[Azure 搜尋中的語言支援](https://msdn.microsoft.com/library/azure/dn879793.aspx)。

選取語言分析後的欄位，將會用於每個索引及搜尋要求，該欄位的。 當針對多個欄位︰ 使用不同的分析器發出查詢時，查詢會獨立處理右分析器的每一個欄位。

多的網頁和行動應用程式服務使用者各地使用不同語言。 若要建立的每種語言支援的欄位來定義如下案例的索引可能是。

![][3]
*索引定義與描述] 欄位中的每種語言支援*

如果知道發行查詢代理程式的語言，可以搜尋要求範圍限定使用**searchFields**查詢參數的特定欄位中。 下列查詢會發出只針對中增添風格的說明︰

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=description_pl&api-version=2015-02-28`

您可以查詢從入口網站，您索引貼上如上所示類似在查詢中使用**搜尋檔案總管]** 。 使用服務刀的命令列中搜尋檔案總管]。 如需詳細資訊，請參閱[查詢您的 Azure 搜尋索引，在入口網站](search-explorer.md)。

有時發行查詢代理程式的語言不知道，這種情況下查詢可以發送給所有欄位同時。 如有需要您可以使用[計分設定檔](https://msdn.microsoft.com/library/azure/dn798928.aspx)定義結果特定語言的喜好設定。 在下面的範例中，以英文描述中找到相符的項目會被計分更高相對於在法文和造型相符的項目︰

    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2015-02-28`

如果您是.NET 開發人員，請注意，您可以設定語言分析器使用[Azure 搜尋.NET SDK](http://www.nuget.org/packages/Microsoft.Azure.Search)。 最新版本包含 Microsoft 語言分析器支援。

<!-- Image References -->
[1]: ./media/search-language-support/AnalyzerTab.png
[2]: ./media/search-language-support/SelectAnalyzer.png
[3]: ./media/search-language-support/IndexDefinition.png
