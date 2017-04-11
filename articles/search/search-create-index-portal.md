<properties
    pageTitle="建立使用 Azure 入口網站 Azure 搜尋索引 |Microsoft Azure |裝載的雲端搜尋服務"
    description="建立索引使用 Azure 入口網站。"
    services="search"
    manager="jhubbard"
    authors="ashmaka"
    documentationCenter=""/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="create-an-azure-search-index-using-the-azure-portal"></a>建立使用 Azure 入口網站 Azure 搜尋索引
> [AZURE.SELECTOR]
- [概觀](search-what-is-an-index.md)
- [入口網站](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [其餘](search-create-index-rest-api.md)

本文會引導您完成建立 Azure 搜尋[索引](search-what-is-an-index.md)使用 Azure 入口網站的程序。

追蹤本指南和之前建立索引，您應該已經[建立 Azure 搜尋服務](search-create-service-portal.md)。


## <a name="i-go-to-your-azure-search-blade"></a>我。 移至您的 Azure 搜尋刀
1. [Azure 入口網站](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)的左側功能表中，按一下 「 所有資源 」 上
2. 選取您 Azure 搜尋服務

## <a name="ii-add-and-name-your-index"></a>II。 新增及命名索引
1. 按一下 [新增索引 」] 按鈕
2. 命名您的 Azure 搜尋索引。 我們正在建立旅館本指南中搜尋索引，因為我們已命名索引 」 旅館 」。
  * 索引名稱必須以字母開頭且包含 [大小寫字母、 數字或連字號 (「-」)。
  * 與您的服務名稱，您所選擇的索引名稱也會端點 URL 的一部分，您會傳送您 HTTP 要求 Azure 搜尋 api
3. 按一下以開啟新的刀 「 欄位 」 項目

![](./media/search-create-index-portal/add-index.png)


## <a name="iii-create-and-define-the-fields-of-your-index"></a>III。 建立及定義您索引的欄位
1. 選取 「 欄位 」 項目時，新的刀將會開啟表單以輸入您的索引的定義。
2. 新增欄位至您使用的表單的索引。

  * *索引鍵*的欄位類型 Edm.String 對是必要的每個 Azure 搜尋索引。 使用 [欄位名稱 「 識別碼 」 的預設會建立此索引鍵欄位。 我們已變更其為 「 hotelId 」 在我們索引。
  * 您索引的結構描述的某些屬性，才可以一次設定，而且無法在未來更新。 因此，任何需要變更欄位類型，例如重新編製索引的結構描述更新不是目前可能之後初始設定。
  * 我們謹慎選擇根據我們如何認為，將會使用應用程式中的每個欄位的屬性值。 設計您的索引，為每個欄位必須被指派[適當的內容](https://msdn.microsoft.com/library/azure/dn798941.aspx)時，請記住您搜尋的使用者體驗與企業需求。 這些內容控制項的搜尋功能 （篩選、 faceting、 排序、 全文檢索等） 套用到哪一個欄位。 例如，很可能旅館搜尋的人員會感興趣的關鍵字 「 描述 」 欄位、 相符的項目，所以我們啟用該欄位的全文檢索搜尋 「 Searchable] 屬性設定。
    * 您也可以按一下頂端的刀 「 分析] 索引標籤上，以設定[語言分析器](https://msdn.microsoft.com/en-us/library/azure/dn879793.aspx)的每一個欄位。 您可以看到下方我們有中選取的欄位法文分析器我們用於法文文字的索引。

3. 按一下**[確定]**以確認您的欄位定義 「 欄位 」 刀上
4. 在 [儲存並建立您定義的索引的 「 新增索引 」 刀上，按一下**[確定]** 。

在以下的螢幕擷取畫面，您可以查看如何我們已命名並為我們的 「 旅館 」 索引定義欄位。

![](./media/search-create-index-portal/field-definitions.png)

![](./media/search-create-index-portal/set-analyzer.png)

## <a name="next"></a>下一步
建立之後 Azure 搜尋索引，您就可以開始上傳[到索引內容](search-what-is-data-import.md)之後，您可以開始搜尋您的資料。
