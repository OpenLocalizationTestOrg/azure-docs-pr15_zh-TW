<properties 
    pageTitle="如何建立 Azure API 管理的 Api" 
    description="瞭解如何建立及設定 Azure API 管理的 Api。" 
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

# <a name="how-to-create-apis-in-azure-api-management"></a>如何建立 Azure API 管理的 Api

API 管理的 API 代表一組可叫用戶端應用程式的作業。 在 publisher 入口網站中建立新的 Api，然後新增您要的作業。 後新增作業，API 會新增至產品並發佈頁面。 一旦發行 API，它可以訂閱並使用 [開發人員。

本指南顯示程序中的第一步︰ 如何建立及設定新的 API API 管理。 新增作業及發佈產品的詳細資訊，請參閱[如何新增作業 API，][]以及[如何建立及發佈產品][]。

## <a name="create-new-api"></a>建立新的 API

建立並設定在 publisher 入口網站的 Api。 若要存取 publisher 入口網站，按一下 [**管理**您的 API 管理服務 Azure 傳統入口網站中。

![Publisher 入口網站][api-management-management-console]

>如果您尚未建立 API 管理服務執行個體，請參閱[建立 API 管理服務執行個體][]，以[開始使用 Azure API 管理][]教學課程中。

按一下**Api**從左側的 [ **API 管理**] 功能表，然後按一下 [**新增 API**。

![建立 API][api-management-create-api]

若要設定新的 API 使用 [**新增新的 API** ] 視窗。

![新增新的 API][api-management-add-new-api]

下列欄位用來設定新的 API。

-   **Web API 名稱**提供 API 的唯一且具描述性名稱。 它會顯示在 [開發人員和 publisher 的入口網站。
-   **Web 服務 URL**參照實作 API HTTP 服務。 API 管理轉寄要求至此地址。
-   **網址 API 後置字元**會附加至基底 API 管理服務的 URL。 基底的 URL 是常見的所有 Api 裝載的 API 管理服務執行個體。 API 管理區別 Api 由其後置字元，並因此後置字元必須是唯一的每個指定的發行者的 API。
-   **網址 API 配置**決定哪些通訊協定，可以用來存取 API。 根據預設，被指定 HTTPs。
-   您也可以新增新的 API 到的產品，請按一下**產品 （可省略）**下拉式清單，並選擇 [產品。 可以多次重複此步驟將 API 新增至多個產品。

一旦設定所要的值，請按一下 [**儲存**]。 新的 API 建立之後，[摘要] 頁面的 API 會顯示在 publisher 入口網站。

![API 摘要][api-management-api-summary]

## <a name="configure-api-settings"></a>設定 API 設定

您可以使用 [**設定**] 索引標籤，確認及編輯 API 的設定。 **Web API 名稱**、 **Web 服務的 URL**和**網址 API 後置字元**是一開始時設定 API 建立，而且可以修改以下。 **描述**提供選擇性的描述，然後**網址 API 配置**決定哪些通訊協定，可以用來存取 API。

![API 設定][api-management-api-settings]

若要設定實作 API 的後端服務的閘道器驗證，請選取 [**安全性**] 索引標籤。 **使用的認證**] 下拉式清單可用來設定**HTTP 基本**或**用戶端憑證**驗證。 若要使用 HTTP 基本驗證，只要輸入您要的認證。 使用用戶端憑證驗證資訊，請參閱[如何保護後端使用用戶端憑證驗證 Azure API 管理的服務][]。

[**安全性**] 索引標籤也可以用於設定使用 OAuth 2.0**使用者的授權**。 如需詳細資訊，請參閱[如何授權開發人員使用 OAuth 2.0 Azure API 管理的帳戶][]。

![基本驗證的設定][api-management-api-settings-credentials]

按一下 [**儲存**] 儲存您對 API 設定進行任何變更。

## <a name="next-steps"></a>接下來的步驟

API 會建立並設定的設定，若要新增作業至 API 的下一個步驟是，新增產品，API，並將其發佈，使其位於適用於開發人員。 如需詳細資訊，請參閱下列文章。

-   [如何新增作業至 API][]
-   [如何建立及發佈產品][]





[api-management-create-api]: ./media/api-management-howto-create-apis/api-management-create-api.png
[api-management-management-console]: ./media/api-management-howto-create-apis/api-management-management-console.png
[api-management-add-new-api]: ./media/api-management-howto-create-apis/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-create-apis/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-create-apis/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-create-apis/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-create-apis/api-management-echo-operations.png

[What is an API?]: #what-is-api
[Create a new API]: #create-new-api
[Configure API settings]: #configure-api-settings
[Configure API operations]: #configure-api-operations
[Next steps]: #next-steps

[如何新增作業至 API]: api-management-howto-add-operations.md
[如何建立及發佈產品]: api-management-howto-add-products.md

[開始使用 Azure API 管理]: api-management-get-started.md
[建立 API 管理服務執行個體]: api-management-get-started.md#create-service-instance
[如何保護後端服務使用的用戶端憑證驗證 Azure API 管理]: api-management-howto-mutual-certificates.md
[如何授權開發人員使用 OAuth 2.0 Azure API 管理的帳戶]: api-management-howto-oauth2.md