<properties
    pageTitle="管理您的第一個 API Azure API 管理 |Microsoft Azure"
    description="瞭解如何建立 Api，加入的作業，並開始使用 API 管理。"
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

# <a name="manage-your-first-api-in-azure-api-management"></a>管理您的第一個 API Azure API 管理

## <a name="overview"></a>概觀

本指南為您示範如何快速著手使用 Azure API 管理，使您第一次的 API 呼叫。

## <a name="concepts"></a>什麼是 Azure API 管理？

您可以使用 Azure API 管理任何的後端並啟動完備的 API 程式，以它。

常見的案例包括︰

* **保護行動基礎結構**來控制出入存取 API 金鑰與禁止 DOS 攻擊使用節流設定，或使用進階的安全性原則，例如 JWT token 驗證。
* **啟用 ISV 合作夥伴 ecosystems**提供快速合作夥伴登入，透過開發人員入口網站和建置 API 外觀從內部實作不是少數合作夥伴消耗可提高。
* 所有**執行內部的 API 程式**提供集中的位置，為組織進行通訊的可用性和 Api，控制出入根據組織的帳戶，存取最新變更的相關根據 API 閘道器與後端之間的安全通道。


系統組成的下列元件︰

* **API 閘道**器端點的︰
  * 接受 API 通話，並將其傳送至您 backends。
  * 驗證 API 金鑰、 JWT 權杖、 憑證，以及其他認證。
  * 強制執行使用量配額和評分限制。
  * 轉換您的 API，而不需程式碼修改即時。
  * 快取設定位置的後端回應。
  * 記錄呼叫用於分析的中繼資料。

* **Publisher 入口網站**是設定您的 API 程式的系統管理介面。 用來︰
    * 定義或匯入 API 結構描述。
    * 將封裝 Api 產品。
    * 設定原則，例如配額或轉換 Api 上。
    * 從分析中取得深入資訊。
    * 管理使用者。

* **開發人員入口網站**做為主要 web 目前狀態適用於開發人員，讓他們可以︰
    * 閱讀 API 文件。
    * 嘗試透過互動式主控台 API。
    * 建立帳戶，並取得 API 金鑰訂閱。
    * Access 分析在自己的使用方式。


## <a name="create-service-instance"></a>建立 API 管理執行個體

>[AZURE.NOTE] 若要完成此教學課程中，您需要 Azure 帳戶。 如果您沒有帳戶，您可以建立在幾分鐘的免費的帳戶。 如需詳細資訊，請參閱[Azure 免費試用版][]。

使用 API 管理的第一步是建立服務執行個體。 登入[Azure 傳統入口網站][]，然後按一下 [**新增]**，**應用程式服務**、 **API 管理**，**建立**。

![API 管理新的執行個體][api-management-create-instance-menu]

**URL**，指定要使用的服務 url 的唯一的子網域名稱。

選擇您要**訂閱**與**地區**服務執行個體。 後進行選取項目，請按一下 [**下一步**] 按鈕。

![新的 API 管理服務][api-management-create-instance-step1]

輸入**Contoso ltd** **組織名稱**，並在 [**系統管理員電子郵件**] 欄位中輸入您的電子郵件地址。

>[AZURE.NOTE] 此電子郵件地址用於從 API 管理系統的通知。 如需詳細資訊，請參閱[如何設定通知和 Azure API 管理中的電子郵件範本][]。

![新的 API 管理服務][api-management-create-instance-step2]

API 管理服務執行個體是否會出現在三層︰ 開發人員、 標準和進階版。 根據預設，建立新的 API 管理服務執行個體在開發人員層。 選取標準或進階版層**進階的設定**] 核取方塊，選取下列畫面上所要的層。

>[AZURE.NOTE] 開發人員層適用於開發、 測試和試驗 API 程式可用性並不成問題。 在 [一般] 及 [進階版的階層，您可以調整保留的單位統計處理更多的流量。 標準和進階版層提供最處理 power 和效能的 API 管理服務。 您可以使用任何層完成本教學課程。 如需有關 API 管理層級的詳細資訊，請參閱[API 管理價格][]。

按一下 [建立您的服務執行個體] 核取方塊。

![新的 API 管理服務][api-management-instance-created]

服務執行個體建立後下, 一步是建立或匯入 API。

## <a name="create-api"></a>匯入的 API

API 包含一組可以叫用戶端應用程式的作業。 代理到現有的 web 服務時，是 API 的作業。

您可以建立 Api （和可以新增作業） 以手動方式，或可以匯入。 在本教學課程中，我們會匯入範例計算機 web 服務由 Microsoft 所提供，並裝載於 Azure 的 API。

>[AZURE.NOTE] 建立 API 和手動新增作業，請參閱[如何建立 Api](api-management-howto-create-apis.md) ，以及[如何新增 api 的作業](api-management-howto-add-operations.md)。

Api 的存取透過 Azure 傳統入口網站的 publisher 入口網站的設定。 若要達到 publisher 入口網站，按一下 [**管理**您的 API 管理服務 Azure 傳統入口網站中。

![Publisher 入口網站][api-management-management-console]

若要匯入 API 計算機，請按一下**Api**從左側的 [ **API 管理**] 功能表，然後按一下**匯入的 API**。

![匯入 API] 按鈕][api-management-import-api]

若要設定計算機 API 下列步驟執行︰

1. 按一下 [**從 URL****規格文件 URL** ] 文字方塊中，輸入**http://calcapi.cloudapp.net/calcapi.json** ，按一下 [ **Swagger**選項] 按鈕。
2. 輸入**網址 API 後置字元**的文字] 方塊中的**計算**。
3. 在 [**產品 （選用）** ] 方塊中按一下，然後選擇 [**入門**。
4. 按一下 [匯入 API 的 [**儲存**]。

![新增新的 API][api-management-import-new-api]

>[AZURE.NOTE] **API 管理**目前支援匯入 1.2 和 2.0 版本 Swagger 文件。 請確認，即使[Swagger 2.0 規格](http://swagger.io/specification)宣告`host`， `basePath`，及`schemes`屬性是選擇性的您**必須**在 Swagger 2.0 文件包含這些屬性。否則，不會匯入。 

一旦匯入 API，API 的 [摘要] 頁面會顯示在 publisher 入口網站。

![API 摘要][api-management-imported-api-summary]

Api > 一節中有多個索引標籤。 [**摘要**] 索引標籤會顯示基本的指標以及 API 的相關資訊。 [[設定](api-management-howto-create-apis.md#configure-api-settings)] 索引標籤用來檢視和編輯 API 的設定。 [[作業](api-management-howto-add-operations.md)] 索引標籤用來管理的 API 作業。 [**安全性**] 索引標籤可用來設定後端伺服器的閘道器驗證使用基本驗證或[相互憑證驗證]](api-management-howto-mutual-certificates.md)，並設定[使用者的授權，使用 OAuth 2.0](api-management-howto-oauth2.md)。  [**問題**] 索引標籤用來檢視報告的開發人員使用的您的 Api 的問題。 [**產品**] 索引標籤用來設定包含此 API 的產品。

根據預設，每個 API 管理執行個體提供兩個範例產品︰

-   **入門**
-   **無限制**

在本教學課程，基本計算機 API 時所加入入門產品 API 已匯入。

撥打 API，才能開發人員必須先訂閱產品，以便讓存取。 開發人員可以在開發人員入口網站，產品訂閱或系統管理員可以訂閱開發人員 publisher 入口網站中的產品。 由於您建立的 API 管理執行個體中的上一個步驟在教學課程中，您已經訂閱至每個產品根據預設，您是系統管理員。

## <a name="call-operation"></a>呼叫作業，從開發人員入口網站

作業可直接從 [開發人員] 入口網站提供方便的方式來檢視並測試 API 的作業。 在此教學課程的步驟，您會呼叫基本的計算機 API**新增兩個整數**作業。 從功能表頂端按一下 [**開發人員入口網站**右上方 publisher 入口網站。

![開發人員入口網站][api-management-developer-portal-menu]

從上方的功能表中，按一下 [ **Api** ，然後按一下 [**基本計算機**，以查看可用的作業。

![開發人員入口網站][api-management-developer-portal-calc-api]

請注意範例描述，以及 API 及作業，提供文件，會使用這項作業的開發已匯入的參數。 手動新增作業時，也可以新增這些說明。

若要撥打電話**新增兩個整數**作業，請按一下 [**試試看**。

![試試看][api-management-developer-portal-calc-api-console]

您可以輸入參數的一些值或保留預設值]，然後再按一下 [**傳送**。

![HTTP Get][api-management-invoke-get]

呼叫作業之後，**回應狀態**、**回應標頭**，以及任何**回應內容**，也會顯示開發人員入口網站。

![回應][api-management-invoke-get-response]

## <a name="view-analytics"></a>檢視狀況分析

若要檢視的基本計算機的分析，切換回 publisher 入口網站**管理**從功能表選取頂端的開發人員入口網站。

![管理][api-management-manage-menu]

Publisher 入口網站的預設檢視是**儀表板**提供您的 API 管理執行個體的概觀。

![儀表板][api-management-dashboard]

將滑鼠停留在**基本計算機**若要查看特定的 API 使用矩陣在指定的期間內的圖表。

>[AZURE.NOTE] 如果您沒有看到任何行在圖表上，切換回開發人員入口網站與一些打電話到 API、 等候幾分鐘的時間，然後回到儀表板。

按一下 [**檢視詳細資料**，來檢視 API，包括 [放大顯示的指標摘要的頁面。

![分析][api-management-mouse-over]

![摘要][api-management-api-summary-metrics]

詳細的指標和報表中，按一下 [**分析**從左側的 [ **API 管理**] 功能表。

![概觀][api-management-analytics-overview]

[**分析**] 區段中有下列四個索引標籤︰

-   **概覽**提供整體的使用狀況和健康指標，以及上方的開發人員、 主要產品、 上方的 Api 及上方的作業。
-   **使用**提供深入了解 API 通話與頻寬，包括地理方式呈現。
-   **健康**狀態碼，著重於快取成功率，回應時間和 API，與服務的回應時間。
-   **活動**提供報表的向下切入，開發人員、 產品、 API，以及作業特定的活動。

## <a name="next-steps"></a>接下來的步驟

- 瞭解如何[保護您的 API 使用工資率限制](api-management-howto-product-with-rules.md)。

[Azure 免費試用版]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=api_management_hero_a

[Create an API Management instance]: #create-service-instance
[Create an API]: #create-api
[Add an operation]: #add-operation
[Add the new API to a product]: #add-api-to-product
[Subscribe to the product that contains the API]: #subscribe
[Call an operation from the Developer Portal]: #call-operation
[View analytics]: #view-analytics
[Next steps]: #next-steps


[How to manage developer accounts in Azure API Management]: api-management-howto-create-or-invite-developers.md
[Configure API settings]: api-management-howto-create-apis.md#configure-api-settings
[如何設定 Azure API 管理通知和電子郵件範本]: api-management-howto-configure-notifications.md
[Responses]: api-management-howto-add-operations.md#responses
[How create and publish a product]: api-management-howto-add-products.md
[API 管理價格]: http://azure.microsoft.com/pricing/details/api-management/

[Azure 傳統入口網站]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-get-started/api-management-management-console.png
[api-management-create-instance-menu]: ./media/api-management-get-started/api-management-create-instance-menu.png
[api-management-create-instance-step1]: ./media/api-management-get-started/api-management-create-instance-step1.png
[api-management-create-instance-step2]: ./media/api-management-get-started/api-management-create-instance-step2.png
[api-management-instance-created]: ./media/api-management-get-started/api-management-instance-created.png
[api-management-import-api]: ./media/api-management-get-started/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-get-started/api-management-import-new-api.png
[api-management-imported-api-summary]: ./media/api-management-get-started/api-management-imported-api-summary.png
[api-management-calc-operations]: ./media/api-management-get-started/api-management-calc-operations.png
[api-management-list-products]: ./media/api-management-get-started/api-management-list-products.png
[api-management-add-api-to-product]: ./media/api-management-get-started/api-management-add-api-to-product.png
[api-management-add-myechoapi-to-product]: ./media/api-management-get-started/api-management-add-myechoapi-to-product.png
[api-management-api-added-to-product]: ./media/api-management-get-started/api-management-api-added-to-product.png
[api-management-developers]: ./media/api-management-get-started/api-management-developers.png
[api-management-add-subscription]: ./media/api-management-get-started/api-management-add-subscription.png
[api-management-add-subscription-window]: ./media/api-management-get-started/api-management-add-subscription-window.png
[api-management-subscription-added]: ./media/api-management-get-started/api-management-subscription-added.png
[api-management-developer-portal-menu]: ./media/api-management-get-started/api-management-developer-portal-menu.png
[api-management-developer-portal-calc-api]: ./media/api-management-get-started/api-management-developer-portal-calc-api.png
[api-management-developer-portal-calc-api-console]: ./media/api-management-get-started/api-management-developer-portal-calc-api-console.png
[api-management-invoke-get]: ./media/api-management-get-started/api-management-invoke-get.png
[api-management-invoke-get-response]: ./media/api-management-get-started/api-management-invoke-get-response.png
[api-management-manage-menu]: ./media/api-management-get-started/api-management-manage-menu.png
[api-management-dashboard]: ./media/api-management-get-started/api-management-dashboard.png

[api-management-add-response]: ./media/api-management-get-started/api-management-add-response.png
[api-management-add-response-window]: ./media/api-management-get-started/api-management-add-response-window.png
[api-management-developer-key]: ./media/api-management-get-started/api-management-developer-key.png
[api-management-mouse-over]: ./media/api-management-get-started/api-management-mouse-over.png
[api-management-api-summary-metrics]: ./media/api-management-get-started/api-management-api-summary-metrics.png
[api-management-analytics-overview]: ./media/api-management-get-started/api-management-analytics-overview.png
[api-management-analytics-usage]: ./media/api-management-get-started/api-management-analytics-usage.png
[api-management-]: ./media/api-management-get-started/api-management-.png
[api-management-]: ./media/api-management-get-started/api-management-.png
