<properties 
    pageTitle="API 管理重要概念" 
    description="深入了解 Api、 產品、 角色、 群組和其他 API 管理重要概念。" 
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
    ms.topic="hero-article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

#<a name="what-is-api-management"></a>什麼是 API 管理？

API 管理協助組織發佈外部 Api、 合作夥伴和內部開發人員若要解除鎖定可能會其資料和服務。 企業所有位置想要將其作業延伸為數位平台、 建立新的頻道，尋找新客戶以及開車與現有的更深入的互動。 API 管理提供核心能力，以確保成功 API 程式通過開發人員互動、 商務獲得深入見解、 分析、 安全性和保護。

觀看以下影片，以概略 Azure API 管理，並瞭解如何使用 API 管理許多功能新增至您的 API，包括存取控制、 速率限制、 監控、 事件記錄和回應快取，以在您的組件最小的工作。

> [AZURE.VIDEO azure-api-management-overview]

若要使用 API 管理，管理員可建立 Api。 每個 API 包含一或多個作業]，以及每個 API 可以新增至一或多個產品。 若要使用的 API，開發人員訂閱包含該 API，產品，然後他們可以呼叫 API 的作業，需任何作用中的使用原則。

本主題提供 API 管理重要概念的概觀。

>[AZURE.NOTE] 如需詳細資訊，請參閱[雲端 API 管理︰ 另外 Power 的 Api](http://j.mp/ms-apim-whitepaper) PDF 白皮書。 本簡介白皮書的 API 管理 CITO 參考資料，包括︰ 
>
> - 常見的 API 需求與挑戰
>     - 耦合 Api 及呈現外貌
>     - 讓開發和執行
>     - 保護存取
>     - 分析及指標
> - 取得控制項並充分使用 API 管理平台
> - 使用雲端與內部部署解決方案
> - Azure API 管理

## <a name="apis"> </a>Api 及作業

Api 是 API 管理服務執行個體的基礎。 每個 API 表示一組的開發人員提供的作業。 每個 API 包含參照後端服務實作 API，並其作業對應至實作後端服務的作業。 API 管理作業可高度設定，並且控制 URL 對應、 查詢和路徑參數、 要求與回應內容與作業回應快取。 工資率限制、 配額和 IP 限制原則也可以執行 API 或個別作業層級。

如需詳細資訊，請參閱[如何建立 Api][] ，以及[如何新增 api 的作業][]。


## <a name="products"></a>產品

產品的開發人員如何呈現 Api。 產品 API 管理中的有一或多個 Api，並使用標題、 描述及使用規定。 產品可**開啟**] 或 [**受保護**。 受保護的產品必須訂閱他們可供使用前，同時開啟產品可供沒有訂閱。 產品可供使用的開發人員時，可以發行。 一旦發行，就可以檢視 （和受保護的產品若是訂閱） 開發人員。 訂閱 「 核准 」 設定的產品層級，以及可以需要管理員核准或會自動核准。

群組用來管理產品的開發人員的可見性。 產品可見度授與群組]，然後開發人員可以檢視及訂閱之產品的皆可看到其所屬的群組。 

如需詳細資訊，請參閱[如何建立及發佈產品][]和以下的影片。

> [AZURE.VIDEO using-products]

## <a name="groups"></a>群組

群組用來管理產品的開發人員的可見性。 API 管理有下列不變系統群組。

-   **系統管理員**-Azure 訂閱系統管理員是此群組的成員。 系統管理員管理 API 管理服務的執行個體，建立 Api、 作業及開發人員所使用的產品。
-   **開發人員**-使用者可分為此群組的已驗證的開發人員入口網站。 開發人員可使用您的 Api 建置應用程式的客戶。 開發人員授與存取開發人員入口網站，並建立呼叫 API 的作業的應用程式。
-   **來賓的身分**驗證開發人員入口網站的使用者，例如造訪 API 管理執行個體的開發人員入口網站的預期客戶屬於此群組。 他們可以授與特定的唯讀存取，例如檢視 Api，但不是撥打電話給的功能。

除了這些系統群組中，管理員可以建立自訂群組或[運用相關聯的 Azure Active Directory 租用戶中的外部群組](api-management-howto-aad.md#how-to-add-an-external-azure-active-directory-group)。 自訂及外部群組可同時在 [開發人員可見度與存取權授予 API 產品的系統群組。 比方說，您可以建立適用於開發人員與特定的合作夥伴組織相關的自訂群組，讓他們存取 api 從包含相關的 Api 產品。 使用者可以是群組的一個以上的成員。

如需詳細資訊，請參閱[建立及使用群組的方式][]。

## <a name="developers"></a>開發人員

開發人員代表 API 管理服務執行個體中的使用者帳戶。 開發人員可以建立或受邀加入由系統管理員，或他們可以從[開發人員入口網站][]登入。 每個開發人員是一或多個群組的成員，並且可訂閱可見度授與這些群組的產品。

當開發人員訂閱產品授權產品的主要和次要鍵。 撥打電話至產品的 Api 時，會使用這個索引鍵。

如需詳細資訊，請參閱[如何建立或邀請開發人員][]，以及[如何建立與開發人員群組之間的關聯][]。

## <a name="policies"></a>原則

原則是允許透過設定 API 的行為變更 publisher 的 API 管理強大的功能。 原則是依序要求上執行的陳述式集合 API 的回應。 常用的陳述式包括 JSON 和通話速率限制，來限制的來電開發人員]，從 XML 格式轉換，可以使用許多其他原則。

除非原則指定，否則，就可以為屬性值或文字值中任何 API 管理原則，使用原則運算式。 某些原則，例如[控制項流程](https://msdn.microsoft.com/library/azure/dn894085.aspx#choose)和[變數設定](https://msdn.microsoft.com/library/azure/dn894085.aspx#set-variable)原則根據原則運算式。 如需詳細資訊，請參閱[進階原則](https://msdn.microsoft.com/library/azure/dn894085.aspx#AdvancedPolicies)，[原則運算式](https://msdn.microsoft.com/library/azure/dn910913.aspx)，及觀看以下影片。

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

如 API 管理原則的完整清單，請參閱[原則參照][]。 如需有關使用及設定原則的詳細資訊，請參閱[API 管理原則][]。 建立產品率限制和配額原則的教學課程，請參閱[如何建立及設定進階的產品][]。 示範，請參閱以下的影片。

> [AZURE.VIDEO rate-limits-and-quotas]

## <a name="developer-portal"></a>開發人員入口網站

開發人員入口網站是開發人員可以進一步瞭解您的 Api、 檢視和通話作業，和訂閱產品的位置。 預期客戶可以造訪的開發人員入口網站檢視 Api 及作業]，並登入。 開發人員入口網站的 URL 位於 Azure 傳統入口網站的 API 管理的服務執行個體中的儀表板上。

您可以新增自訂的內容，自訂樣式，並新增您的品牌自訂您的開發人員入口網站的外觀與風格。

## <a name="api-management-and-the-api-economy"></a>API 管理及 API 經濟

若要瞭解更多關於 API 管理的資訊，請觀看下列從 Microsoft Ignite 2015 會議簡報。

> [AZURE.VIDEO microsoft-ignite-2015-azure-api-management-and-the-api-economy]

[APIs and operations]: #apis
[Products]: #products
[Groups]: #groups
[Developers]: #developers
[Policies]: #policies
[開發人員入口網站]: #developer-portal

[如何建立 Api]: api-management-howto-create-apis.md
[如何新增作業至 API]: api-management-howto-add-operations.md
[如何建立及發佈產品]: api-management-howto-add-products.md
[如何建立及使用群組]: api-management-howto-create-groups.md
[如何建立群組與開發人員之間的關聯]: api-management-howto-create-groups.md#associate-group-developer
[如何建立及設定進階的產品]: api-management-howto-product-with-rules.md
[如何建立或邀請開發人員]: api-management-howto-create-or-invite-developers.md
[原則參考]: api-management-policy-reference.md
[API 管理原則]: api-management-howto-policies.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance



 
