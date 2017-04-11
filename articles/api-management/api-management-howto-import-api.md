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
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-import-the-definition-of-an-api-with-operations-in-azure-api-management"></a>如何匯入作業 Azure API 管理的 API 的定義

在 API 管理] 中，您可以建立新的 Api，手動新增的作業或 API 可以匯入以及一個步驟中的作業。

Api 和其作業可以匯入使用下列格式。

-   WADL
-   Swagger

本指南會顯示如何建立新的 API，匯入其單步驟中的作業。 手動建立 API，並新增作業的資訊，請參閱[如何建立 Api][] ，以及[如何新增 api 的作業][]。

## <a name="import-api"></a>匯入的 API

建立並設定在 publisher 入口網站的 Api。 若要存取 publisher 入口網站，按一下 [**管理**您的 API 管理服務 Azure 傳統入口網站中。 如果您尚未建立 API 管理服務執行個體，請參閱[建立 API 管理服務執行個體][]，以[開始使用 Azure API 管理][]教學課程中。

![Publisher 入口網站][api-management-management-console]

按一下**Api**從左側的 [ **API 管理**] 功能表，然後按一下 [**匯入 API**。

![匯入 API][api-management-import-apis]

[**匯入 API** ] 視窗中有三個索引標籤，並對應到提供 API 規格的三種方式。

-   **從剪貼簿]**可讓您將 API 規格貼到 [指定的文字] 方塊。
-   **從檔案**可讓您瀏覽並選取含有 API 規格的檔案。
-   **從 URL**可讓您提供的 API 規格的 URL。

![匯入 API 格式][api-management-import-api-clipboard]

之後提供 API 規格，使用 [選項] 按鈕右邊的表示規格格式。 支援下列格式。

-   WADL
-   Swagger

接下來，輸入**網址 API 後置字元**。 這會附加至基底 API 管理服務的 URL。 基底的 URL 是常見的所有 Api 裝載於 API 管理服務的每個執行個體。 API 管理區別 Api 由其後置字元，並因此後置字元必須是唯一的每個 API 的特定的 API 管理服務執行個體。

一旦輸入所有的值，請按一下 [建立 API 及相關聯的作業的 [**儲存**]。 

>[AZURE.NOTE] 匯入基本計算機 API Swagger 格式的教學課程，請參閱[管理您的第一個 API Azure API 管理](api-management-get-started.md)。

## <a name="export-api"></a>匯出 API

除了匯入新的 Api，您可以將您的 api 定義匯出從 publisher 入口網站。 若要這麼做，請按一下 [**匯出 API**從您的**API**的**摘要] 索引標籤**。

![匯出 API][api-management-export-api]

可以使用 WADL 或 Swagger 匯出 Api。 選取所要的格式，按一下 [**儲存**]，選擇要儲存檔案的位置。

![匯出 API 格式][api-management-export-api-format]

## <a name="next-steps"></a>接下來的步驟

後 API 會建立並匯入作業，您可以檢視並設定任何其他設定]，新增至產品、 的 API，並將它發佈，使其位於適用於開發人員。 如需詳細資訊，請參閱下列輔助線。

-   [如何設定 API 設定][]
-   [如何建立及發佈產品][]




[api-management-management-console]: ./media/api-management-howto-import-api/api-management-management-console.png
[api-management-import-apis]: ./media/api-management-howto-import-api/api-management-api-import-apis.png
[api-management-import-api-clipboard]: ./media/api-management-howto-import-api/api-management-import-api-wizard.png
[api-management-export-api]: ./media/api-management-howto-import-api/api-management-export-api.png
[api-management-export-api-format]: ./media/api-management-howto-import-api/api-management-export-api-format.png

[Import an API]: #import-api
[Export an API]: #export-api
[Configure API settings]: #configure-api-settings
[Next steps]: #next-steps

[開始使用 Azure API 管理]: api-management-get-started.md
[建立 API 管理服務執行個體]: api-management-get-started.md#create-service-instance

[如何新增作業至 API]: api-management-howto-add-operations.md
[如何建立及發佈產品]: api-management-howto-add-products.md
[如何建立 Api]: api-management-howto-create-apis.md
[如何設定 API 設定]: api-management-howto-create-apis.md#configure-api-settings
