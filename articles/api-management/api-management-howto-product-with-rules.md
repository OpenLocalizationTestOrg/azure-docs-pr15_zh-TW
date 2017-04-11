<properties
    pageTitle="保護您的 Azure API 管理的 API |Microsoft Azure"
    description="瞭解如何保護您的 API 使用配額和節流 （工資率限制） 原則。"
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

# <a name="protect-your-api-with-rate-limits-using-azure-api-management"></a>保護您的 API 使用 Azure API 管理的工資率限制

本指南示範如何輕鬆新增保護您的後端 API Azure API 管理設定工資率限制和配額原則。

在本教學課程中，您將建立可讓開發人員打電話最多 10 個每分鐘，上限為 200 通話，每週至您的 API 使用[每個訂閱的限制通話工資率](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate)] 和 [[設定每個訂閱的使用量配額](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota)原則 」 免費試用版 」 API 產品。 然後，您會發佈 API，並測試工資率限制原則。

更多進階節流情況下使用的[工資率限制-依-鍵](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey)和[配額的金鑰](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey)原則，請參閱[節流 Azure API 管理的進階的要求](api-management-sample-flexible-throttling.md)。

## <a name="create-product"></a>來建立產品

在此步驟中，您將建立不需要訂閱核准免費試用版產品。

>[AZURE.NOTE] 如果您已經有產品設定，並且想要用於此教學課程中，您可以跳到[設定來電工資率限制和配額的原則][]，並從該處使用您的產品取代免費試用版產品依照本教學課程。

若要開始，按一下 [**管理**中 Azure 傳統 API 管理服務。 會帶您到 API 管理 publisher 入口網站。

![Publisher 入口網站][api-management-management-console]

>如果您尚未建立 API 管理服務執行個體，請參閱[建立 API 管理服務執行個體][]，[管理您的第一個 API Azure API 管理][]教學課程中。

按一下以顯示 [**產品**頁面左側的**API 管理**] 功能表中的 [**產品**]。

![新增產品][api-management-add-product]

按一下 [**新增產品**]，以顯示 [**新增新的產品**] 對話方塊。

![新增新的產品][api-management-new-product-window]

在 [**標題**] 方塊中，輸入**免費試用版**。

在 [**描述**] 方塊中，輸入下列文字︰ **訂閱者能夠執行 10 通話/分鐘上限為 200 通話/週之後存取。**

產品 API 管理會受到保護，或開啟。 受保護的產品必須訂閱可供使用前。 開啟產品可供沒有訂閱。 確保**需要訂閱**已選取 [建立受保護的產品需要訂閱。 這是預設的設定。

如果您想要檢閱並接受或拒絕訂閱嘗試這項產品的系統管理員，請選取 [**訂閱核准**]。 如果未選取核取方塊，訂閱嘗試將會自動核准。 在此範例中，訂閱會自動核准，讓未選取方塊。

若要允許訂閱多次新產品的開發人員帳戶，請選取 [**允許同時的多個訂閱**的核取方塊。 本教學課程中不使用多個同時訂閱，因此不要選取。

輸入所有的值後，按一下 [**儲存**]，建立產品。

![新增產品][api-management-product-added]

根據預設，新的產品會在 [**管理員**] 群組中的使用者。 我們會新增 [**開發人員**] 群組。 按一下 [**免費試用版**，，然後按一下 [**可見度**] 索引標籤。

>API 管理群組用來管理產品的開發人員的可見性。 產品可見度授與群組]，然後開發人員可以檢視及訂閱之產品的皆可看到其所屬的群組。 如需詳細資訊，請參閱[如何建立及使用 Azure API 管理群組][]。

![新增 [開發人員] 群組][api-management-add-developers-group]

選取 [**開發人員**] 核取方塊，然後再按一下 [**儲存**]。

## <a name="add-api"></a>新增產品的 API

在此步驟的教學課程中，我們會將盡可能降低回音 API 新增至新的免費試用版產品。

>每個 API 管理服務執行個體隨附預先設定，以盡可能降低回音 API，可嘗試使用和瞭解 API 管理。 如需詳細資訊，請參閱[管理您的第一個 API Azure API 管理][]。

從**API 管理**左側功能表上，按一下 [**產品**，然後按一下 [**免費試用版**來設定產品。

![設定產品][api-management-configure-product]

按一下 [**新增 API 產品**]。

![新增產品 API][api-management-add-api]

選取**回音 API**，然後再按一下 [**儲存**]。

![新增回應 API][api-management-add-echo-api]

## <a name="policies"></a>設定通話工資率限制和配額原則

原則編輯器] 中設定工資率限制和配額。 按一下左側的 [ **API 管理**] 功能表下的 [**原則**]。 在 [**產品**] 清單中，按一下 [**免費試用版**。

![產品原則][api-management-product-policy]

按一下 [**新增原則**要匯入的原則範本開始建立工資率限制和配額原則]。

![新增原則][api-management-add-policy]

若要插入原則，將游標放到原則範本的 [**輸入**或**輸出**區段上。 工資率限制和配額原則輸入的原則，因此，將游標放置於輸入的項目。

![原則編輯器][api-management-policy-editor-inbound]

我們要新增此教學課程中的兩個原則會[限制每個訂閱的通話工資率](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate)] 和 [[設定每個訂閱的使用量配額](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota)原則。

![原則陳述式][api-management-limit-policies]

將游標放在**輸入**的原則項目後，請按一下 [**限制每個訂閱的通話工資率**插入其原則範本旁邊的箭號。

    <rate-limit calls="number" renewal-period="seconds">
    <api name="name" calls="number">
    <operation name="name" calls="number" />
    </api>
    </rate-limit>

**每個訂閱的限制通話工資率**可以使用產品層級，而且也可以用 API 及個別作業名稱層級。 在本教學課程中，使用只產品層級原則請刪除**api**及**作業**的項目從**速度限制**的項目，只讓外部**速度限制**項目會保留，如下列範例所示。

    <rate-limit calls="number" renewal-period="seconds">
    </rate-limit>

免費試用版的產品中, 最大可允許通話工資率每分鐘 10 通話，因此為**通話**屬性的值和**60** **續約期間**屬性中輸入**10** 。

    <rate-limit calls="10" renewal-period="60">
    </rate-limit>

若要設定的**設定每個訂閱的使用量配額**的原則，請正下方新增的**速度限制**內的項目**輸入**的項目，請將游標的位置，，然後按一下 [**設定每個訂閱的使用量配額**左側的箭號。

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    <api name="name" calls="number" bandwidth="kilobytes">
    <operation name="name" calls="number" bandwidth="kilobytes" />
    </api>
    </quota>

由於此原則也要在產品層級，請刪除**api**及**作業**名稱項目，如下列範例所示。

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    </quota>

配額可以為基礎的每個間隔、 頻寬，或兩者的呼叫數目。 在本教學課程中，我們不會節流根據的頻寬，請刪除**頻寬**屬性。

    <quota calls="number" renewal-period="seconds">
    </quota>

在免費試用版的產品，配額會為每週 200 來電。 指定**200**做為**通話**屬性的值，然後指定**604800**做為**續約期間**屬性的值。

    <quota calls="200" renewal-period="604800">
    </quota>

>指定原則間隔秒數。 若要計算每週的間隔，您可以一天 (24) 中的秒數 (60) 分鐘數 (60) 小時的分鐘數字的時數的數字乘以的天數 (7): 7 *24* 60 * 60 = 604800。

當您完成設定原則時，它應該符合下列範例。

    <policies>
        <inbound>
            <rate-limit calls="10" renewal-period="60">
            </rate-limit>
            <quota calls="200" renewal-period="604800">
            </quota>
            <base />

    </inbound>
    <outbound>

        <base />

        </outbound>
    </policies>

想要的原則設定之後，請按一下 [**儲存**]。

![儲存原則][api-management-policy-save]

## <a name="publish-product"></a>發行產品

現在，Api 會新增並設定原則、 產品必須發行，這樣就可以使用由開發人員。 從**API 管理**左側功能表上，按一下 [**產品**，然後按一下 [**免費試用版**來設定產品。

![設定產品][api-management-configure-product]

按一下 [**發佈**]，，然後按一下 [**是，將它發佈**以確認。

![發佈產品][api-management-publish-product]

## <a name="subscribe-account"></a>訂閱產品的開發人員帳戶

現在，已發行產品，則訂閱並使用 [開發人員使用。

>每個產品自動訂閱 API 管理執行個體的管理員。 在此教學課程的步驟中，我們會訂閱其中一項免費試用版產品的非系統管理員開發人員帳戶。 如果您開發人員的帳戶是系統管理員角色的一部分，然後您可以遵循以及此步驟中，即使您已經訂閱。

按一下左邊的**API 管理**功能表的**使用者**，然後按一下 [開發人員帳戶的名稱。 在此範例中，我們使用**雯 Gragg**開發人員帳戶。

![設定 [開發人員][api-management-configure-developer]

按一下 [**新增訂閱**。

![新增訂閱][api-management-add-subscription-menu]

選取**免費試用版**]，然後再按一下 [**訂閱**]。

![新增訂閱][api-management-add-subscription]

>[AZURE.NOTE] 在本教學課程，同時的多個訂閱不會啟用免費試用版產品。 如果您想要會提示您名稱的訂閱，如下列範例所示。

![新增訂閱][api-management-add-subscription-multiple]

按一下 [**訂閱**]，產品就會出現在使用者的**訂閱**清單中。

![新增的訂閱][api-management-subscription-added]

## <a name="test-rate-limit"></a>呼叫作業，並測試速度限制

現在設定和發佈的免費試用版的產品，我們可以撥打某些操作並測試工資率限制原則。
切換至開發人員入口網站的右上方功能表中按一下 [**開發人員入口網站**。

![開發人員入口網站][api-management-developer-portal-menu]

在頂端的功能表中，按一下 [ **Api** ，然後按一下 [**回應 API**。

![開發人員入口網站][api-management-developer-portal-api-menu]

按一下 [**取得的資源**，然後按一下 [**試試看**。

![開啟主控台][api-management-open-console]

保留預設參數值，然後選取 [免費試用版產品您訂閱的金鑰。

![訂閱索引鍵][api-management-select-key]

>[AZURE.NOTE] 如果您有多個訂閱，請務必選取 [索引鍵的**免費試用版**]，否則無法作用中的上一個步驟中設定的原則。

按一下 [**傳送**]，然後檢視 [回應。 請注意**回應狀態** **200 確定**。

![作業結果][api-management-http-get-results]

大於 10 分鐘呼叫的工資率限制原則速度，按一下 [**傳送**]。 超過工資率限制原則之後，會傳回**429 太多要求**的狀態為回應。

![作業結果][api-management-http-get-429]

前重試會成功**回應內容**會指出剩餘的間隔。

當每分鐘 10 通話的工資率限制原則作用中時，會失敗的後續通話，直到 60 秒經過從第一個產品之前超過工資率限制的 10 個成功呼叫。 在此範例中，其餘的間隔是 54 的秒數。

## <a name="next-steps"></a>接下來的步驟

-   觀看以下影片設定工資率限制和配額的示範。

> [AZURE.VIDEO rate-limits-and-quotas]


[api-management-management-console]: ./media/api-management-howto-product-with-rules/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-product-with-rules/api-management-add-product.png
[api-management-new-product-window]: ./media/api-management-howto-product-with-rules/api-management-new-product-window.png
[api-management-product-added]: ./media/api-management-howto-product-with-rules/api-management-product-added.png
[api-management-add-policy]: ./media/api-management-howto-product-with-rules/api-management-add-policy.png
[api-management-policy-editor-inbound]: ./media/api-management-howto-product-with-rules/api-management-policy-editor-inbound.png
[api-management-limit-policies]: ./media/api-management-howto-product-with-rules/api-management-limit-policies.png
[api-management-policy-save]: ./media/api-management-howto-product-with-rules/api-management-policy-save.png
[api-management-configure-product]: ./media/api-management-howto-product-with-rules/api-management-configure-product.png
[api-management-add-api]: ./media/api-management-howto-product-with-rules/api-management-add-api.png
[api-management-add-echo-api]: ./media/api-management-howto-product-with-rules/api-management-add-echo-api.png
[api-management-developer-portal-menu]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-menu.png
[api-management-publish-product]: ./media/api-management-howto-product-with-rules/api-management-publish-product.png
[api-management-configure-developer]: ./media/api-management-howto-product-with-rules/api-management-configure-developer.png
[api-management-add-subscription-menu]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-menu.png
[api-management-add-subscription]: ./media/api-management-howto-product-with-rules/api-management-add-subscription.png
[api-management-developer-portal-api-menu]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-api-menu.png
[api-management-open-console]: ./media/api-management-howto-product-with-rules/api-management-open-console.png
[api-management-http-get]: ./media/api-management-howto-product-with-rules/api-management-http-get.png
[api-management-http-get-results]: ./media/api-management-howto-product-with-rules/api-management-http-get-results.png
[api-management-http-get-429]: ./media/api-management-howto-product-with-rules/api-management-http-get-429.png
[api-management-product-policy]: ./media/api-management-howto-product-with-rules/api-management-product-policy.png
[api-management-add-developers-group]: ./media/api-management-howto-product-with-rules/api-management-add-developers-group.png
[api-management-select-key]: ./media/api-management-howto-product-with-rules/api-management-select-key.png
[api-management-subscription-added]: ./media/api-management-howto-product-with-rules/api-management-subscription-added.png
[api-management-add-subscription-multiple]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-multiple.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[管理您的第一個 API Azure API 管理]: api-management-get-started.md
[如何建立和使用 Azure API 管理中的群組]: api-management-howto-create-groups.md
[View subscribers to a product]: api-management-howto-add-products.md#view-subscribers
[Get started with Azure API Management]: api-management-get-started.md
[建立 API 管理服務執行個體]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps

[Create a product]: #create-product
[設定來電工資率限制和配額原則]: #policies
[Add an API to the product]: #add-api
[Publish the product]: #publish-product
[Subscribe a developer account to the product]: #subscribe-account
[Call an operation and test the rate limit]: #test-rate-limit

[Limit call rate]: https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate
[Set usage quota]: https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota
