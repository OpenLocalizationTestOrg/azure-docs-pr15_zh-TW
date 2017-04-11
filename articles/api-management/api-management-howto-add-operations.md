<properties 
    pageTitle="如何新增作業至 Azure API 管理的 API |Microsoft Azure" 
    description="瞭解如何新增作業至 Azure API 管理的 API。" 
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

# <a name="how-to-add-operations-to-an-api-in-azure-api-management"></a>如何新增作業至 Azure API 管理的 API

可供 API 管理的 API 之前，必須新增作業。 本指南會顯示如何新增及設定 API 管理不同類型的 api 的作業。

## <a name="add-operation"></a>新增作業

新增及設定以在 publisher 入口網站的 API 作業。 若要存取 publisher 入口網站，按一下 [**管理**您的 API 管理服務 Azure 傳統入口網站中。

![Publisher 入口網站][api-management-management-console]

>如果您尚未建立 API 管理服務執行個體，請參閱[建立 API 管理服務執行個體][]，以[開始使用 Azure API 管理][]教學課程中。

選取您要的 API publisher 入口網站中，然後選取 [**作業**] 索引標籤。 

![作業][api-management-operations]

按一下 [**新增作業**新增新作業]。 將會顯示**新的作業**，預設會選取 [**簽名**] 索引標籤。

![新增作業][api-management-add-operation]

指定**HTTP 動作**下拉式清單中選擇。

![HTTP 方法][api-management-http-method]

<a name="url-template"></a>

定義 URL 範本中包含一或多個 URL 路徑區段和零個或多個查詢字串參數的 URL 片段。 URL 範本，在基底 url 的 api，識別單一 HTTP 作業。 它可能包含一個或多個具名變數大括號來識別的組件。 這些變數的組件稱為範本參數，且動態指派 API 管理平台處理要求時，從該要求的 URL 擷取的值。

![URL 範本][api-management-url-template]

<a name="rewrite-url-template"></a>

如有需要，請指定**改寫 URL 範本**。 這個選項可讓您可以使用標準的 URL 範本時呼叫透過已轉換的 URL，根據好範本後端處理前端傳入邀請。 好的範本中應從 URL 範本的範本參數。 下列範例顯示編碼方式路徑區段在上述範例中的 web 服務可提供為發佈透過使用 URL 範本的 API 管理平台 API 中查詢參數的方式內容類型。

![URL 範本重新寫入][api-management-url-template-rewrite]

來電者的作業會使用格式`/customers?customerid=ALFKI`，這會對應至`/Customers('ALFKI')`後端服務會叫用。


**顯示**名稱和**描述**提供作業的描述，用於開發人員入口網站中使用此 API 開發人員提供的說明文件。

![描述][api-management-description]

作業描述可指定為純文字] 或 [**描述**] 文字方塊中的 HTML。

## <a name="operation-caching"></a>操作快取

回應快取降低觀察得到的 API 消費者降低頻寬使用量與減少負載 HTTP web 服務實作 API 的延遲。 

若要快速輕鬆地啟用作業的快取，請選取 [**快取**] 索引標籤，核取 [**啟用**] 核取方塊。

![快取][api-management-caching-tab]

**工期**指定時間期間持續快取中的作業回應。 預設值為 3600 秒或 1 小時。

快取索引鍵用來區分回覆，讓對應至每個不同的快取索引鍵的回應取得自己個別快取的值。 您也可以輸入特定的查詢字串參數及/或 HTTP 標頭，以供分別運算快取**查詢字串參數分**和**分以標題**文字方塊中的關鍵值。 當都不指定，完整的要求 URL 和快取產生金鑰中使用下列 HTTP 標頭值︰**接受**] 和 [**接受字元集**。

>如需快取及快取原則的詳細資訊，請參閱[Azure API 管理中的快取作業結果的方式][]。


## <a name="request-parameters"></a>要求參數

在 [參數] 索引標籤上管理作業參數。 在 [**簽名**] 索引標籤上的**URL 範本**中指定的參數會自動新增，並可以變更僅編輯 URL 範本。 您可以手動輸入其他參數。

若要新增新的查詢參數，請按一下 [**新增查詢參數**，並輸入下列資訊︰

-   **名稱**-參數名稱。
-   **描述**-（選擇性） 參數的簡短描述。
-   **類型**-參數類型，選擇下拉式清單中向下。
-   **值**-可以指派給此參數的值。 其中一個值可以標示為預設值 （選擇性）。
-   **所需**-檢查] 核取方塊，讓參數強制。 

![要求參數][api-management-request-parameters]

## <a name="request-body"></a>要求本文

如果允許作業 （例如放入、 文章），而且需要本文，您可能會提供在所有支援的表示格式 (例如 json，XML) 中的範例。 

>邀請本文只會用於文件，並不會驗證。

若要輸入邀請本文，請切換至 [**本文**] 索引標籤。

按一下 [**新增表示**，輸入所要的內容類型的名稱 (例如應用程式/json)，在下拉式清單中選取它，您要的邀請本文範例中選取的格式貼上到 [文字] 方塊。 

![邀請內文][api-management-request-body]

在其他表示法，您也可以指定在 [**描述**] 文字方塊中，選擇性的文字描述。

## <a name="responses"></a>回覆

最好的所有作業可能會產生的狀態碼提供的回應的範例。 每個狀態碼可能會有一個以上的回應本文範例，其中每個支援的內容類型。 

若要新增回應，按一下 [**新增**]，然後開始輸入您要的狀態碼。 在這個範例是狀態碼**200 確定**。 後的程式碼會顯示在下拉式清單中，選取它，並會建立回應程式碼，並將其新增至您的作業。

![回應程式碼][api-management-response-code]

按一下 [**新增表示**，輸入所要的內容類型的名稱 (例如應用程式/json)，然後將其選取下拉式清單中向下。

![本文內容類型][api-management-response-body-content-type]

回應本文範例中所選取的格式貼上到 [文字] 方塊。 

![回應內容][api-management-response-body]

如有需要，新增到 [**描述**] 文字方塊中的選擇性描述。

作業設定之後，請按一下 [**儲存**]。


## <a name="next-steps"></a>接下來的步驟

一旦作業會新增至 API 下, 一步是與產品的 API，並將其發佈以便開發人員可以撥打其作業。

-   [如何建立及發佈產品][]

[api-management-management-console]: ./media/api-management-howto-add-operations/api-management-management-console.png
[api-management-operations]: ./media/api-management-howto-add-operations/api-management-operations.png
[api-management-add-operation]: ./media/api-management-howto-add-operations/api-management-add-operation.png
[api-management-http-method]: ./media/api-management-howto-add-operations/api-management-http-method.png
[api-management-url-template]: ./media/api-management-howto-add-operations/api-management-url-template.png
[api-management-url-template-rewrite]: ./media/api-management-howto-add-operations/api-management-url-template-rewrite.png
[api-management-description]: ./media/api-management-howto-add-operations/api-management-description.png
[api-management-caching-tab]: ./media/api-management-howto-add-operations/api-management-caching-tab.png
[api-management-request-parameters]: ./media/api-management-howto-add-operations/api-management-request-parameters.png
[api-management-request-body]: ./media/api-management-howto-add-operations/api-management-request-body.png
[api-management-response-code]: ./media/api-management-howto-add-operations/api-management-response-code.png
[api-management-response-body-content-type]: ./media/api-management-howto-add-operations/api-management-response-body-content-type.png
[api-management-response-body]: ./media/api-management-howto-add-operations/api-management-response-body.png


[api-management-contoso-api]: ./media/api-management-howto-add-operations/api-management-contoso-api.png

[api-management-add-new-api]: ./media/api-management-howto-add-operations/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-add-operations/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-add-operations/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-add-operations/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-add-operations/api-management-echo-operations.png

[Add an operation]: #add-operation
[Operation caching]: #operation-caching
[Request parameters]: #request-parameters
[Request body]: #request-body
[Responses]: #responses
[Next steps]: #next-steps

[開始使用 Azure API 管理]: api-management-get-started.md
[建立 API 管理服務執行個體]: api-management-get-started.md#create-service-instance

[How to add operations to an API]: api-management-howto-add-operations.md
[如何建立及發佈產品]: api-management-howto-add-products.md
[Azure API 管理中的快取作業結果的方式]: api-management-howto-cache.md