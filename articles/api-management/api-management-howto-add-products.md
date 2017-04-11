<properties 
    pageTitle="如何建立及發佈 Azure API 管理產品" 
    description="瞭解如何建立及發佈 Azure API 管理產品。" 
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

# <a name="how-to-create-and-publish-a-product-in-azure-api-management"></a>如何建立及發佈 Azure API 管理產品

Azure API 管理] 中的產品包含一或多個 Api，以及使用量配額和使用規定。 一旦發行產品，開發人員可以產品訂閱，並開始使用產品的 Api。 主題提供建立產品、 API，以及發佈到適用於開發人員指南。

## <a name="create-product"></a>建立產品

新增及設定以在 publisher 入口網站的 API 作業。 若要存取 publisher 入口網站，按一下 [**管理**您的 API 管理服務 Azure 傳統入口網站中。

![Publisher 入口網站][api-management-management-console]

>如果您尚未建立 API 管理服務執行個體，請參閱[建立 API 管理服務執行個體][]，以[開始使用 Azure API 管理][]教學課程中。

按一下以顯示 [**產品**] 頁面的左側功能表中的**產品**，然後按一下 [**新增產品**]。

![產品][api-management-products]

![新的產品][api-management-add-new-product]

在 [**名稱**] 欄位和 [**描述**] 欄位中的產品描述輸入產品的描述性名稱。

**開啟**] 或 [**受保護**，則可以是 API 管理中的產品。 受保護的產品必須訂閱他們可供使用前，同時開啟產品可供沒有訂閱。 核取 [建立受保護的產品需要訂閱的 [**需要訂閱**。 這是預設的設定。

如果您想要檢閱並接受或拒絕這項產品的訂閱嘗試以系統管理員，核取**訂閱核准**。 如果未核取方塊，請嘗試訂閱會自動核准。 如需有關訂閱的詳細資訊，請參閱[檢視訂閱的產品][]。

若要允許訂閱多次產品的開發人員帳戶，請核取 [**允許多個訂閱**的核取方塊。 如果未核取此方塊，每個開發人員帳戶可以訂閱只一次產品。

![不受限制的多個訂閱][api-management-unlimited-multiple-subscriptions]

若要限制同時的多個訂閱的計數，請**同時訂閱的數量限制**] 核取方塊，輸入的訂閱上限。 在下列範例中，同時訂閱限於四每個開發人員帳戶。

![四個多個訂閱][api-management-four-multiple-subscriptions]

一旦設定所有新的產品選項，請按一下 [建立新的產品的 [**儲存**]。

![產品][api-management-products-page]

>依預設新產品解除發佈，而只看到 [**管理員**] 群組。

若要設定產品，請按一下 [**產品**] 索引標籤的產品名稱。

## <a name="add-apis"></a>新增至產品的 Api

[**產品**] 頁面包含四個連結的設定︰**摘要**、**設定**、**可見度**和**訂閱者**。 [**摘要**] 索引標籤是您可以在其中新增 Api 及發佈或解除發佈產品。

![摘要][api-management-new-product-summary]

發佈您的產品之前，您需要新增一或多個 Api。 若要這麼做，請按一下 [**新增 API 產品**。

![新增 Api][api-management-add-apis-to-product]

選取所要的 Api，然後按一下 [**儲存**]。

## <a name="add-description"></a>產品中加入描述性資訊

[**設定**] 索引標籤可讓您提供的產品，其目的，它提供的存取權，Api 等其他有用的資訊的詳細的資訊。 在開發人員呼叫 API，可以撰寫純文字或 HTML 標記被目標內容。

![產品設定][api-management-product-settings]

選取 [**需要訂閱**建立受保護的產品需要用的訂閱或清除核取方塊，以建立可訂閱不開啟產品。

如果您想要手動核准所有產品的訂閱要求，請選取 [**需要核准訂閱**]。 依預設會自動授予所有產品的訂閱。

若要允許訂閱多次產品的開發人員帳戶，請核取 [**允許多個訂閱**的核取方塊，您也可以選擇指定限制。 如果未核取此方塊，每個開發人員帳戶可以訂閱只一次產品。

您也可以填寫描述使用條款的訂閱者必須先接受才能使用這項產品的產品**使用條款的**欄位。

## <a name="publish-product"></a>發佈產品

可呼叫產品中的 Api 之前，必須發行產品。 [**摘要**] 索引標籤的產品按一下 [**發佈**]，然後按一下**[是]，將它發佈**以確認。 若要將之前發佈的產品設為私人，按一下 [**解除發佈**]。

![發佈產品][api-management-publish-product]

## <a name="make-visible"></a>顯示開發人員產品

[**可見度**] 索引標籤可讓您選擇的角色無法看到 [開發人員入口網站上的 [產品及產品訂閱。

![產品可見性][api-management-product-visiblity]

啟用或停用產品的可見性適用於開發人員] 群組中，核取或取消核取群組旁邊的核取方塊，然後再按一下 [**儲存**。

>如需詳細資訊，請參閱[如何建立及使用管理開發人員帳戶 Azure API 管理群組][]。

## <a name="view-subscribers"></a>檢視產品的訂閱

[**訂閱**] 索引標籤會列出的開發人員已經訂閱產品。 開發人員的名稱上按一下 [可以檢視的詳細資料和每個開發人員的設定。 在此範例中沒有開發人員已經尚未訂閱產品。

![開發人員][api-management-developer-list]

## <a name="next-steps"></a>接下來的步驟

新增所需的 Api 並產品發佈之後，開發人員可以產品訂閱，並開始呼叫的 Api。 教學課程示範這些項目，以及進階的產品設定，請參閱[如何建立及設定 Azure API 管理的進階的產品][]。

如需使用產品的詳細資訊，請參閱以下的影片。

> [AZURE.VIDEO using-products]

[Create a product]: #create-product
[Add APIs to a product]: #add-apis
[Add descriptive information to a product]: #add-description
[Publish a product]: #publish-product
[Make a product visible to developers]: #make-visible
[檢視訂閱的產品]: #view-subscribers
[Next steps]: #next-steps

[api-management-management-console]: ./media/api-management-howto-add-products/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-add-products/api-management-add-product.png
[api-management-add-new-product]: ./media/api-management-howto-add-products/api-management-add-new-product.png
[api-management-unlimited-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-unlimited-multiple-subscriptions.png
[api-management-four-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-four-multiple-subscriptions.png
[api-management-products-page]: ./media/api-management-howto-add-products/api-management-products-page.png
[api-management-new-product-summary]: ./media/api-management-howto-add-products/api-management-new-product-summary.png
[api-management-add-apis-to-product]: ./media/api-management-howto-add-products/api-management-add-apis-to-product.png
[api-management-product-settings]: ./media/api-management-howto-add-products/api-management-product-settings.png
[api-management-publish-product]: ./media/api-management-howto-add-products/api-management-publish-product.png
[api-management-product-visiblity]: ./media/api-management-howto-add-products/api-management-product-visibility.png
[api-management-developer-list]: ./media/api-management-howto-add-products/api-management-developer-list.png



[api-management-products]: ./media/api-management-howto-add-products/api-management-products.png
[api-management-]: ./media/api-management-howto-add-products/
[api-management-]: ./media/api-management-howto-add-products/


[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[開始使用 Azure API 管理]: api-management-get-started.md
[建立 API 管理服務執行個體]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps
[如何建立及管理 Azure API 管理中的 [開發人員帳戶使用群組]: api-management-howto-create-groups.md
[如何建立及設定 Azure API 管理的進階的產品設定]: api-management-howto-product-with-rules.md 