<properties
    pageTitle="新增快取以改善效能 Azure API 管理 |Microsoft Azure"
    description="瞭解如何改善延遲，請頻寬使用量，以及 web 服務載入 API 管理服務通話。"
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
    ms.topic="get-started-article"
    ms.date="10/25/2016"
    ms.author="sdanie"/>

# <a name="add-caching-to-improve-performance-in-azure-api-management"></a>新增快取以改善效能 Azure API 管理

回應快取的您可以設定 API 管理中的作業。 回應快取，可以大幅降低 API 延遲，頻寬使用量並 web 服務載入，不會經常變更的資料。

本指南為您示範如何新增回應您的 API 的快取及設定的範例回音 API 作業原則。 您就可以從開發人員入口網站，以驗證中巨集指令的快取呼叫作業。

>[AZURE.NOTE] 使用原則運算式的索引鍵的快取項目上的資訊，請參閱[Azure API 管理中的自訂快取](api-management-sample-cache-by-key.md)。

## <a name="prerequisites"></a>必要條件

本指南中的步驟前，您必須擁有 API 管理服務執行個體的 api，產品設定。 如果您尚未建立 API 管理服務執行個體，請參閱[建立 API 管理服務執行個體][]，以[開始使用 Azure API 管理][]教學課程中。

## <a name="configure-caching"></a>設定快取的作業

在此步驟中，您將會檢閱**取得資源 （快取）**作業的範例回音 API 的快取設定。

>[AZURE.NOTE] 每個 API 管理服務執行個體可以自行以盡可能降低回音 API，可嘗試使用和瞭解 API 管理。 如需詳細資訊，請參閱[開始使用 Azure API 管理][]。

若要開始，按一下 [**管理**您的 API 管理服務 Azure 傳統入口網站中。 會帶您到 API 管理 publisher 入口網站。

![Publisher 入口網站][api-management-management-console]

從**API 管理**左側功能表上，按一下 [ **Api** ，然後按一下 [**回應 API**。

![盡可能降低回音 API][api-management-echo-api]

按一下 [**作業**] 索引標籤，然後按一下 [**作業**] 清單**取得資源 （快取）**作業。

![盡可能降低回音 API 作業][api-management-echo-api-operations]

按一下 [**快取**] 索引標籤檢視此項作業的快取設定。

![快取] 索引標籤][api-management-caching-tab]

若要啟用作業的快取，請選取 [**啟用**] 核取方塊。 在此範例中，啟用快取。

每個作業回應做為索引鍵，而根據**查詢字串參數分**和**分，以標題**欄位中的值。 如果您想要快取查詢字串參數或標題的多個回覆，您可以在這兩個欄位中將這些設定。

**工期**指定到期間隔的快取的回應。 在此範例中，間隔是**3600**秒數，相當於 1 小時。

在此範例中，使用快取設定，**取得資源 （快取）**作業的第一個要求會傳回後端服務回應。 此回應會快取，依指定的標題和查詢字串參數索引。 後續的來電至] 作業，以符合參數，會有快取的回應傳回，直到快取持續時間間隔已過期。

## <a name="caching-policies"></a>檢閱快取的原則

您可以在此步驟中，檢閱的範例回音 API**取得資源 （快取）**作業的快取設定。

快取設定設定時的 [**快取**] 索引標籤上的作業，快取原則會新增的作業。 這些原則可以檢視及編輯原則編輯器] 中。

從**API 管理**左側功能表上，按一下**原則**，然後選取**回音 API / 取得資源 （快取）**從 [**作業**] 下拉式清單。

![原則範圍作業][api-management-operation-dropdown]

此原則編輯器] 中顯示這項作業的原則。

![API 管理原則編輯器][api-management-policy-editor]

這項作業的原則定義包含檢閱上一個步驟中使用 [**快取**] 索引標籤定義快取設定原則。

    <policies>
        <inbound>
            <base />
            <cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
                <vary-by-header>Accept</vary-by-header>
                <vary-by-header>Accept-Charset</vary-by-header>
            </cache-lookup>
            <rewrite-uri template="/resource" />
        </inbound>
        <outbound>
            <base />
            <cache-store caching-mode="cache-on" duration="3600" />
        </outbound>
    </policies>

>[AZURE.NOTE] 作業，反之亦然**快取**] 索引標籤上，將會反映在原則編輯器] 中的快取原則所做的變更。

## <a name="test-operation"></a>呼叫作業，並測試快取

若要查看的實際操作快取，我們可以從開發人員入口網站呼叫作業。 按一下 [**開發人員入口網站**中最右邊的功能表。

![開發人員入口網站][api-management-developer-portal-menu]

在頂端的功能表中，按一下 [ **Api** ，然後選取 [**回應 API**。

![盡可能降低回音 API][api-management-apis-echo-api]

>如果您只有一個 API 設定，或顯示您的帳戶，然後按一下 [Api 讓您直接前往該 API 的作業。

選取 [**取得資源 （快取）**作業，，然後按一下 [**開啟主控台**。

![開啟主控台][api-management-open-console]

主控台可讓您叫用直接從開發人員入口網站的作業。

![主控台][api-management-console]

將**參數 1**和**參數 2**的預設值。

從**訂閱鍵**下拉式清單中選取所需的索引鍵。 如果您的帳戶有只有一個訂閱，則會已選取。

在 [**要求頁首**的 [文字] 方塊中輸入**sampleheader:value1** 。

按一下 [ **HTTP 取得**，並記下回應標頭。

在 [**要求標題**文字] 方塊中輸入**sampleheader:value2** ，然後按一下 [ **HTTP Get**。

請注意，仍**sampleheader**價值**value1**在回應中。 請嘗試一些不同的值，並記下 [快取的回應，從第一個通話會傳回。

將**參數 2** ] 欄位中，輸入**25** ，然後按一下 [ **HTTP Get**。

請注意，現在**sampleheader**在回應中的值**value2**。 因為作業結果會依查詢字串索引，未傳回先前快取的回應。

## <a name="next-steps"></a>接下來的步驟

-   如需快取原則的詳細資訊，請參閱[API 管理原則參照][]中的 [[快取原則][]。
-   使用原則運算式的索引鍵的快取項目上的資訊，請參閱[Azure API 管理中的自訂快取](api-management-sample-cache-by-key.md)。

[api-management-management-console]: ./media/api-management-howto-cache/api-management-management-console.png
[api-management-echo-api]: ./media/api-management-howto-cache/api-management-echo-api.png
[api-management-echo-api-operations]: ./media/api-management-howto-cache/api-management-echo-api-operations.png
[api-management-caching-tab]: ./media/api-management-howto-cache/api-management-caching-tab.png
[api-management-operation-dropdown]: ./media/api-management-howto-cache/api-management-operation-dropdown.png
[api-management-policy-editor]: ./media/api-management-howto-cache/api-management-policy-editor.png
[api-management-developer-portal-menu]: ./media/api-management-howto-cache/api-management-developer-portal-menu.png
[api-management-apis-echo-api]: ./media/api-management-howto-cache/api-management-apis-echo-api.png
[api-management-open-console]: ./media/api-management-howto-cache/api-management-open-console.png
[api-management-console]: ./media/api-management-howto-cache/api-management-console.png


[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[開始使用 Azure API 管理]: api-management-get-started.md

[API 管理原則參照]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[快取原則]: https://msdn.microsoft.com/library/azure/dn894086.aspx

[建立 API 管理服務執行個體]: api-management-get-started.md#create-service-instance

[Configure an operation for caching]: #configure-caching
[Review the caching policies]: #caching-policies
[Call an operation and test the caching]: #test-operation
[Next steps]: #next-steps
