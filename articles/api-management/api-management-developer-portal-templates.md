<properties 
    pageTitle="自訂使用範本 Azure API 管理開發人員入口網站的方式 |Microsoft Azure" 
    description="瞭解如何自訂使用範本 Azure API 管理開發人員入口網站。" 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>


# <a name="how-to-customize-the-azure-api-management-developer-portal-using-templates"></a>自訂使用範本 Azure API 管理開發人員入口網站的方式

Azure API 管理提供數種的自訂功能，讓系統管理員，若要[自訂的開發人員入口網站的外觀與風格](api-management-customize-portal.md)，以及自訂使用的範本設定自己的頁面內容的一組開發人員入口網站頁面的內容。 您使用[DotLiquid](http://dotliquidmarkup.org/)語法，以及提供的本地化的字串資源、 圖示及頁面控制項，有更多的彈性設定頁面的內容，根據您的需要使用這些範本。

## <a name="developer-portal-templates-overview"></a>開發人員入口網站範本概觀

在開發人員入口網站中，可由系統管理員的 API 管理服務執行個體管理開發人員入口網站範本。 若要管理開發人員範本，請瀏覽至您的 API 管理服務執行個體，Azure 傳統入口網站中，然後按一下 [**瀏覽**。

![開發人員入口網站][api-management-browse]

如果您已在 publisher 入口網站中，您可以存取開發人員入口網站，按一下 [**開發人員入口網站**。

![開發人員入口網站] 功能表][api-management-developer-portal-menu]

若要存取開發人員入口網站範本，請按一下 [自訂] 圖示可顯示 [自訂] 功能表中，然後按一下 [**範本**]。

![開發人員入口網站範本][api-management-customize-menu]

[範本] 清單會顯示範本涵蓋不同的頁面，在開發人員入口網站中的數種類別。 每個範本會不同，不過來編輯及發佈變更的步驟是一樣的。 若要編輯範本，請按一下範本的名稱。

![開發人員入口網站範本][api-management-templates-menu]

按一下範本會帶您到該範本可自訂的開發人員入口網站頁面。 在此範例中的 [**產品] 清單**會顯示範本。 **產品清單**範本控制所指出的紅色矩形的畫面區域。 

![產品清單範本][api-management-developer-portal-templates-overview]

某些範本，例如 「**使用者設定檔**範本，自訂同一個頁面的不同部分。 

![使用者設定檔範本][api-management-user-profile-templates]

編輯器] 中的每個開發人員入口網站範本有顯示在頁面底部的兩個部分。 左側顯示的範本，[編輯] 窗格和右側顯示的資料模型的範本。 

編輯窗格範本包含控制項的外觀和行為的對應的頁面，在開發人員入口網站中的標記。 範本中的標記使用[DotLiquid](http://dotliquidmarkup.org/)的語法。 一個常用 DotLiquid 編輯器是[DotLiquid 設計工具](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers)。 在編輯範本所做的任何變更會顯示在即時在瀏覽器中，但不是會顯示您的客戶直到您[儲存](#to-save-a-template)並[發佈](#to-publish-a-template)範本。

![範本標記][api-management-template]

[**範本資料**] 窗格可供使用特定的範本中的實體提供輔助線的資料模型。 會顯示目前顯示在開發人員入口網站中的即時資料提供本指南。 您可以展開 [範本] 窗格，按一下右上角的 [**範本資料**] 窗格的矩形。

![範本資料模型][api-management-template-data]

在上一個範例中有兩個產品顯示在開發人員入口網站中，擷取的資料顯示在 [**範本資料**] 窗格中，如下列範例所示。

    {
        "Paging": {
            "Page": 1,
            "PageSize": 10,
            "TotalItemCount": 2,
            "ShowAll": false,
            "PageCount": 1
        },
        "Filtering": {
            "Pattern": null,
            "Placeholder": "Search products"
        },
        "Products": [
            {
                "Id": "56ec64c380ed850042060001",
                "Title": "Starter",
                "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",
                "Terms": "",
                "ProductState": 1,
                "AllowMultipleSubscriptions": false,
                "MultipleSubscriptionsCount": 1
            },
            {
                "Id": "56ec64c380ed850042060002",
                "Title": "Unlimited",
                "Description": "Subscribers have completely unlimited access to the API. Administrator approval is required.",
                "Terms": null,
                "ProductState": 1,
                "AllowMultipleSubscriptions": false,
                "MultipleSubscriptionsCount": 1
            }
        ]
    }

**產品清單**範本中的標記程序，逐一顯示資訊及連結至每個個別產品的產品的集合，提供所要的輸出資料。 請注意`<search-control>`和`<page-control>`標記檢視] 中的項目。 這些控制項顯示的搜尋和分頁頁面上的控制項。 `ProductsStrings|PageTitleProducts`為本地化的字串參照包含`h2`頁面的頁首文字。 字串的清單資源、 頁面控制項及圖示適用於開發人員入口網站範本中使用，請參閱[API 管理開發人員入口網站範本參考](https://msdn.microsoft.com/library/azure/mt697540.aspx)。

    <search-control></search-control>
    <div class="row">
        <div class="col-md-9">
            <h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>
        </div>
    </div>
    <div class="row">
        <div class="col-md-12">
        {% if products.size > 0 %}
        <ul class="list-unstyled">
        {% for product in products %}
            <li>
                <h3><a href="/products/{{product.id}}">{{product.title}}</a></h3>
                {{product.description}}
            </li>   
        {% endfor %}
        </ul>
        <paging-control></paging-control>
        {% else %}
        {% localized "CommonResources|NoItemsToDisplay" %}
        {% endif %}
        </div>
    </div>

## <a name="to-save-a-template"></a>若要儲存範本

若要儲存為範本，按一下 [儲存] 樣板編輯器。

![儲存範本][api-management-save-template]

儲存的變更即時在開發人員入口網站後才會輸出。

## <a name="to-publish-a-template"></a>發佈範本

個別或一起儲存的範本可發佈。 若要發佈的個別的範本，請按一下 [發佈範本編輯器] 中。

![發佈範本][api-management-publish-template]

按一下 [ **]**進行確認，然後製作的範本即時開發人員入口網站上。

![確認發佈][api-management-publish-template-confirm]

若要發佈所有目前解除發佈的範本版本，[在 [範本] 清單中的 [**發佈**]。 解除發佈的範本會指定星號追蹤的範本名稱。 在此範例中，要在發佈的**產品清單**與**產品**的範本。

![發佈範本][api-management-publish-templates]

按一下 [**發佈] 自訂**確認]。

![確認發佈][api-management-publish-customizations]

新發佈的範本可立即在開發人員入口網站。

## <a name="to-revert-a-template-to-the-previous-version"></a>若要還原舊版的範本

若要還原先前已發佈版本的範本，請按一下 [還原為範本編輯器] 中。

![還原為範本][api-management-revert-template]

按一下 [ **]**以確認。

![確認][api-management-revert-template-confirm]

還原操作完成後，範本先前已發佈的版本是即時在開發人員入口網站。

## <a name="to-restore-a-template-to-the-default-version"></a>若要還原為預設版本的範本

還原至其預設版本的範本是執行的程序。 首先您必須還原範本，並還原的版本然後必須發行。

若要還原為預設版本單一範本按一下範本編輯器中的還原。

![還原為範本][api-management-reset-template]

按一下 [ **]**以確認。

![確認][api-management-reset-template-confirm]

若要將所有範本都還原為預設版本中，按一下 [**都還原預設範本中**的樣板清單。

![還原範本][api-management-restore-templates]

還原的範本必須然後發佈個別或同時全部送出以[發佈範本](#to-publish-a-template)中的步驟進行。

## <a name="developer-portal-templates-reference"></a>開發人員入口網站範本參考

用於開發人員入口網站範本與字串資源、 圖示、 網頁控制項的參考資訊，請參閱[API 管理開發人員入口網站範本參考](https://msdn.microsoft.com/library/azure/mt697540.aspx)。

## <a name="watch-a-video-overview"></a>觀看視訊的概觀

觀看以下影片，瞭解如何使用範本的開發人員入口網站的 API 及作業網頁新增討論區] 與 [評等。

> [AZURE.VIDEO adding-developer-portal-functionality-using-templates-in-azure-api-management]


[api-management-customize-menu]: ./media/api-management-developer-portal-templates/api-management-customize-menu.png
[api-management-templates-menu]: ./media/api-management-developer-portal-templates/api-management-templates-menu.png
[api-management-developer-portal-templates-overview]: ./media/api-management-developer-portal-templates/api-management-developer-portal-templates-overview.png
[api-management-template]: ./media/api-management-developer-portal-templates/api-management-template.png
[api-management-template-data]: ./media/api-management-developer-portal-templates/api-management-template-data.png
[api-management-developer-portal-menu]: ./media/api-management-developer-portal-templates/api-management-developer-portal-menu.png
[api-management-browse]: ./media/api-management-developer-portal-templates/api-management-browse.png
[api-management-user-profile-templates]: ./media/api-management-developer-portal-templates/api-management-user-profile-templates.png
[api-management-save-template]: ./media/api-management-developer-portal-templates/api-management-save-template.png
[api-management-publish-template]: ./media/api-management-developer-portal-templates/api-management-publish-template.png
[api-management-publish-template-confirm]: ./media/api-management-developer-portal-templates/api-management-publish-template-confirm.png
[api-management-publish-templates]: ./media/api-management-developer-portal-templates/api-management-publish-templates.png
[api-management-publish-customizations]: ./media/api-management-developer-portal-templates/api-management-publish-customizations.png
[api-management-revert-template]: ./media/api-management-developer-portal-templates/api-management-revert-template.png
[api-management-revert-template-confirm]: ./media/api-management-developer-portal-templates/api-management-revert-template-confirm.png
[api-management-reset-template]: ./media/api-management-developer-portal-templates/api-management-reset-template.png
[api-management-reset-template-confirm]: ./media/api-management-developer-portal-templates/api-management-reset-template-confirm.png
[api-management-restore-templates]: ./media/api-management-developer-portal-templates/api-management-restore-templates.png







