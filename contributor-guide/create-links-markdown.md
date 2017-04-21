<properties
   pageTitle="建立 markdown 文章中的連結" description="說明如何撰寫程式碼中 markdown 交互連結。" metaKeywords="" services="" solutions="" documentationCenter="" authors="tysonn" videoId="" scriptId="" manager="carolz" />

<tags ms.service="contributor-guide" ms.devlang="" ms.topic="article" ms.tgt_pltfrm="" ms.workload="" ms.date="02/03/2015" ms.author="tysonn" />

# <a name="linking-guidance-for-azure-technical-content"></a>連結 Azure 技術內容的指導方針

### <a name="links-from-one-acom-article-to-another"></a>從一個 ACOM 文章的連結到另一個

若要從 ACOM 技術文件中建立的直接連結到另一個 ACOM 技術文件中，使用下列連結語法︰  

- 在同一個服務目錄中的另一篇文章的連結服務目錄中的文章︰

  `[link text](article-name.md)`

- 本文連結從服務子目錄的根目錄中的文章︰

  `[link text](../article-name.md)`

- 在根目錄連結服務子目錄中的文章︰ 

  `[link text](./service-directory/article-name.md)`

- 服務子目錄連結到另一個服務子目錄在文章中的文章︰

  `[link text](../service-directory/article-name.md)`
 

## <a name="links-to-anchors"></a>錨點的連結

您沒有建立錨點-它們會自動產生，發佈 H2 的所有標題的時間。 唯一就必須執行的是建立 H2 節的連結。

- 若要連結至相同文件中的標題︰

  `[link](#the-text-of-the-H2-section-separated-by-hyphens)`  
  `[Create cache](#create-cache)`

- 若要連結至相同的目錄中的另一個文件中的錨點︰

  `[link text](article-name.md#anchor-name)`
  `[Configure your profile](media-services-create-account.md#configure-your-profile)`

- 若要連結至另一個服務目錄中的錨點︰

  `[link text](../service-directory/article-name.md#anchor-name)`
  `[Configure your profile](../service-directory/media-services-create-account.md#configure-your-profile)`

自動在您的文件自動產生的錨點的連結以建立連結的程序的其中一個方法是[MarkdownAnchorLinkGenerator-工具，以產生錨點連結 ACOM 以適當的格式](https://github.com/Azure/Azure-CSI-Content-Tools/tree/master/Tools/ACOMMarkdownAnchorLinkGenerator)。

## <a name="links-from-includes"></a>包含的連結

因為包含檔案位於另一個目錄，您必須使用再相對路徑，如下所示。 若要從包含檔案連結的文章，請使用此格式︰

    [link text](../articles/service-folder/article-name.md)
    
進一步瞭解如何使用[自訂 markdown 副檔名準則](custom-markdown-extensions.md#includes)中的包含檔案。

## <a name="links-in-selectors"></a>選擇器中的連結

如果您有選取器，請在包含內嵌時，您可以使用這類的連結︰ 

    > [AZURE。選取器清單 (Dropdown1 |Dropdown2)]     -  [(Text1 |Example1)](../articles/service-folder/article-name1.md)
    - [(Text1 |Example2)](../articles/service-folder/article-name2.md)
    - [(Text2 |Example3)](../articles/service-folder/article-name3.md)
    - [(Text2 |Example4)](../articles/service-folder/article-name4.md)


## <a name="reference-style-links"></a>參照樣式連結

您可以使用 [參考資料樣式連結，讓您來源的內容更容易閱讀。 參考資料樣式連結取代內嵌連結語法簡化的語法，可讓您將完整的 Url 移到文件的結尾。 以下是任何火球範例︰

內嵌的文字︰

    I get 10 times more traffic from [Google][1] than from [Yahoo][2] or [MSN][3].

連結的文件結尾的參考資料︰

    <!--Reference links in article-->
    [1]: http://google.com/
    [2]: http://search.yahoo.com/  
    [3]: http://search.msn.com/

請確定您冒號之前連結包含空格。 當您連結至其他技術文件中，如果您忘了包含空格時，已發佈的文件中的連結將會無法進行運算。 

## <a name="link-to-acom-pages-that-are-not-part-of-the-technical-documentation-set"></a>連結至 ACOM 網頁技術文件組的一部分

若要連結的頁面上 ACOM （例如價格的頁面、 SLA 頁面或其他項目不是文件的文件），請使用絕對 URL，但省略地區設定。 以下的目標是在 GitHub 和呈現的網站上的連結工作︰

    [link text](http://azure.microsoft.com/pricing/details/virtual-machines/)


## <a name="link-to-msdn-or-technet"></a>連結至 MSDN 或 TechNet

當您要連結至 MSDN 或 TechNet 時，請使用完整的連結的主題，並移除 en-us-我們語言地區設定的連結。 

### <a name="use-friendly-link-text-for-all-links"></a>使用易記的連結文字的所有連結

您在連結中包含的文字應該易記-換句話說，他們應該是標準的英文字或連結至頁面的標題。 不要使用 [按一下這裡]。 它不正確的 SEO，而不足以描述目標。

**修正︰**

- `For more information, see the [contributor guide index](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-index.md).`

- `For more details, see the [SET TRANSACTION ISOLATION LEVEL](https://msdn.microsoft.com/library/ms173763.aspx) reference.`

**不正確︰**

- `For more details, see [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).`

- `For more information, click [here](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-index.md).`


## <a name="fwlinks"></a>FWLinks

避免在 azure.microsoft.com 內容的 FWLinks （我們系統重新導向）。 他們應該使用只能作為的最後一個步驟，當您需要建立頁面的 URL，您還不知道的連結。 在幾乎所有實際需要。 ACOM，您定義檔案名稱，讓您可以瞭解它將會提前。 尚未發佈的文件庫主題，您可以建立使用主題 GUID，好讓您能夠使用 Fw 的連結。

如果您必須使用 Fw 網頁上，包括 P 參數，使其永久重新導向︰

    http://go.microsoft.com/fwlink/p/?LinkId=389595

當您將目標 URL 貼到 Fw 工具時，請記得要移除的地區設定，如果您的目標連結 ACOM，或 MSDN 或 TechNet 文件庫。

## <a name="remember-the-azure-library-chrome"></a>請記住 Azure 文件庫 chrome ！
如果您想要連結到[此節點](https://msdn.microsoft.com/library/azure)之下的 Azure 文件庫主題，請記住，若要指定 Azure 中的連結 （/azure/） chrome。 Azure chrome 共用 ACOM 導覽選項，並只顯示 Azure 內容的 MSDN 文件庫。 正確範圍的連結看起來像這樣︰

    http://msdn.microsoft.com/library/azure/dd163896.aspx

否則，頁面將會呈現在標準的 MSDN 檢視中，以顯示整個 MSDN 樹狀目錄。

### <a name="contributors-guide-links"></a>參與者的指南的連結

- [概觀文章](./../README.md)
- [指導文件的索引](./contributor-guide-index.md)

<!--image references-->
[1]: ./media/create-tables-markdown/table-markdown.png
[2]: ./media/create-tables-markdown/break-tables.png
