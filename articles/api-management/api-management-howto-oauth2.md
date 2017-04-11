<properties 
    pageTitle="如何授權開發人員使用 OAuth 2.0 Azure API 管理的帳戶" 
    description="瞭解如何使用 OAuth 2.0 API 管理使用者的授權。" 
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

# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>如何授權開發人員使用 OAuth 2.0 Azure API 管理的帳戶

許多 Api 支援[OAuth 2.0](http://oauth.net/2/)安全 API，並確保只有有效的使用者才有存取權，，及他們只能存取他們權的資源。 若要使用 Azure API 的互動式開發人員與管理主控台此類 Api，服務可讓您設定以使用您的 OAuth 2.0 啟用的 API 服務執行個體。

## <a name="prerequisites"></a>先決條件

本指南將示範如何設定 API 管理服務執行個體 OAuth 2.0 授權用於開發人員帳戶，但未顯示您如何設定的 OAuth 2.0 提供者。 每個 OAuth 2.0 提供者設定會不同，，雖然步驟很類似，並用於設定 OAuth 2.0 API 管理的服務執行個體中的資訊必要項目都是一樣的。 本主題會顯示為 OAuth 2.0 提供者使用 Azure Active Directory 的範例。

>[AZURE.NOTE] 如需設定 OAuth 2.0 使用 Azure Active Directory 的詳細資訊，請參閱[WebApp-GraphAPI-DotNet][]範例。

## <a name="step1"> </a>API 管理中設定 OAuth 2.0 授權伺服器

若要開始，按一下 [**管理**您的 API 管理服務 Azure 傳統入口網站中。 會帶您到 API 管理 publisher 入口網站。

![Publisher 入口網站][api-management-management-console]

>[AZURE.NOTE] 如果您尚未建立 API 管理服務執行個體，請參閱[建立 API 管理服務執行個體][]，以[開始使用 Azure API 管理][]教學課程中。

按一下 [**安全性**]，請從左側的 [ **API 管理**] 功能表，按一下**OAuth 2.0**，，再按一下 [**新增授權伺服器**。

![OAuth 2.0][api-management-oauth2]

按一下 [**新增授權伺服器**之後, 會顯示新的授權伺服器表單。

![新的伺服器][api-management-oauth2-server-1]

在 [**名稱**與**描述**] 欄位中輸入的名稱，以及選擇性的描述。 

>[AZURE.NOTE] 這些欄位用來識別 OAuth 2.0 授權伺服器，在目前的 API 管理服務執行個體，其值不會從 OAuth 2.0 伺服器。

輸入的**用戶端註冊頁面的 URL**。 此頁面是讓使用者可以建立及管理自己的帳戶，並根據使用的 OAuth 2.0 提供者而異。 **用戶端註冊頁面的 URL**指向使用者可以用來建立及設定自己的帳戶支援使用者管理帳戶的 OAuth 2.0 提供者的頁面。 有些公司不設定或即使 OAuth 2.0 提供者提供支援，使用這項功能。 如果您 OAuth 2.0 的提供者未與任何管理使用者設定的帳戶，請輸入您公司的 URL，或是如以下的版面配置區 URL 例如`https://placeholder.contoso.com`。

在表單的下一個區段包含的**授權程式碼授與類型**、**授權端點 URL**及**授權要求方法**設定。

![新的伺服器][api-management-oauth2-server-2]

核取您要的類型，指定**授權程式碼授與類型**。 依預設指定**授權程式碼**。

請輸入**授權端點 URL**。 適用於 Azure Active Directory，此 URL 會類似下列的 URL，其中`<client_id>`取代識別您的應用程式的 OAuth 2.0 伺服器的用戶端識別碼。

    https://login.windows.net/<client_id>/oauth2/authorize

**授權要求方法**會指定授權邀請傳送至 OAuth 2.0 伺服器的方式。 依預設會選取**取得**。

下一節是指定的**權杖端點 URL**、**用戶端驗證方法**，**存取權杖傳送方法**，和**預設範圍**。

![新的伺服器][api-management-oauth2-server-3]

Azure Active Directory OAuth 2.0 伺服器，在**權杖端點 URL**會有下列的格式，其中`<APPID>`具有的格式`yourapp.onmicrosoft.com`。

    https://login.windows.net/<APPID>/oauth2/token

**用戶端驗證方法**的預設設定**基本**，而**存取權杖傳送方法**是**授權標頭**。 本節的表單，以及**預設範圍**設定這些值。

[**用戶端憑證**] 區段包含**用戶端識別碼**和**用戶端 [私人**]，其中 OAuth 2.0 伺服器的建立及設定程序時將會取得。 一旦指定**的用戶端識別碼**和**用戶端密碼**，則會產生**redirect_uri**的**驗證碼**。 此 URI 用來設定回覆 URL 中 OAuth 2.0 伺服器的設定。

![新的伺服器][api-management-oauth2-server-4]

[**資源擁有者密碼認證**] 區段**授權程式碼授與類型**設定為**資源擁有者密碼**，如果用來指定那些認證。否則您可以保留為空白。

![新的伺服器][api-management-oauth2-server-5]

一旦完成表單，請按一下 [儲存 API 管理 OAuth 2.0 授權伺服器設定的 [**儲存**]。 儲存伺服器設定，您可以設定 Api 使用此設定，如下圖所示下, 一節。

## <a name="step2"></a>設定的 API 使用 OAuth 2.0 使用者的授權

按一下 [ **Api**從左側的 [ **API 管理**] 功能表，按一下所要的 API 的名稱、 按一下 [**安全性**]，然後**OAuth 2.0**的核取方塊。

![使用者的授權][api-management-user-authorization]

從下拉式清單中，選取您要的**授權伺服器**，然後按一下 [**儲存**]。

![使用者的授權][api-management-user-authorization-save]

## <a name="step3"></a>開發人員入口網站中測試 OAuth 2.0 使用者的授權

一旦您已經設定 OAuth 2.0 授權伺服器，設定您的 API 使用該伺服器，您可以移至開發人員入口網站，然後呼叫 API 來進行測試。  按一下 [**開發人員入口網站**中最右邊的功能表。

![開發人員入口網站][api-management-developer-portal-menu]

按一下頂端的功能表中的**Api** ，然後選取 [**回應 API**。

![盡可能降低回音 API][api-management-apis-echo-api]

>[AZURE.NOTE] 如果您只有一個 API 設定，或顯示您的帳戶，然後按一下 [Api 讓您直接前往該 API 的作業。

選取**取得資源**操作，按一下**開啟主控台**]，然後從下拉式清單中選取 [**授權程式碼**。

![開啟主控台][api-management-open-console]

選取**授權程式碼**之後，快顯視窗會顯示與 OAuth 2.0 提供者的登入表單。 在此範例中 Azure Active Directory 提供登入表單。

>[AZURE.NOTE] 如果您有停用的快顯系統會提示您啟用這些瀏覽器。 啟用這些之後，請選取**授權程式碼**及登入表單隨即出現。

![登入][api-management-oauth2-signin]

您有登入之後，會以填入**要求標頭**`Authorization : Bearer`授權要求的標題。

![要求標頭的憑證][api-management-request-header-token]

此時，可以設定所要的值為其餘的參數，並提交要求。 

## <a name="next-steps"></a>後續步驟

如需有關如何使用 OAuth 2.0 與 API 管理的詳細資訊，請參閱以下影片和伴隨的[文章](api-management-howto-protect-backend-with-aad.md)。

> [AZURE.VIDEO protecting-web-api-backend-with-azure-active-directory-and-api-management]

[api-management-management-console]: ./media/api-management-howto-oauth2/api-management-management-console.png
[api-management-oauth2]: ./media/api-management-howto-oauth2/api-management-oauth2.png
[api-management-user-authorization]: ./media/api-management-howto-oauth2/api-management-user-authorization.png
[api-management-user-authorization-save]: ./media/api-management-howto-oauth2/api-management-user-authorization-save.png
[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-developer-portal-menu]: ./media/api-management-howto-oauth2/api-management-developer-portal-menu.png
[api-management-open-console]: ./media/api-management-howto-oauth2/api-management-open-console.png
[api-management-oauth2-server-1]: ./media/api-management-howto-oauth2/api-management-oauth2-server-1.png
[api-management-oauth2-server-2]: ./media/api-management-howto-oauth2/api-management-oauth2-server-2.png
[api-management-oauth2-server-3]: ./media/api-management-howto-oauth2/api-management-oauth2-server-3.png
[api-management-oauth2-server-4]: ./media/api-management-howto-oauth2/api-management-oauth2-server-4.png
[api-management-oauth2-server-5]: ./media/api-management-howto-oauth2/api-management-oauth2-server-5.png
[api-management-apis-echo-api]: ./media/api-management-howto-oauth2/api-management-apis-echo-api.png


[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[開始使用 Azure API 管理]: api-management-get-started.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[建立 API 管理服務執行個體]: api-management-get-started.md#create-service-instance

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

