<properties 
    pageTitle="如何使用 Azure API 管理中的 Azure Active Directory 的開發人員帳戶的授權" 
    description="瞭解如何使用 Azure Active Directory API 管理使用者的授權。" 
    services="api-management" 
    documentationCenter="API Management" 
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

# <a name="how-to-authorize-developer-accounts-using-azure-active-directory-in-azure-api-management"></a>如何使用 Azure API 管理中的 Azure Active Directory 的開發人員帳戶的授權


## <a name="overview"></a>概觀
本指南為您示範如何啟用一或多個 Azure Active 目錄中的所有使用者的存取權開發人員入口網站。 本指南也會顯示您如何管理的 Azure Active Directory 使用者群組加入外部包含的 Azure Active Directory 使用者的群組。

>若要完成本指南中的步驟，首先必須 Azure Active Directory 中建立的應用程式。

## <a name="how-to-authorize-developer-accounts-using-azure-active-directory"></a>如何使用 Azure Active Directory 的開發人員帳戶的授權

若要開始，按一下 [**管理**您的 API 管理服務 Azure 傳統入口網站中。 會帶您到 API 管理 publisher 入口網站。

![Publisher 入口網站][api-management-management-console]

>如果您尚未建立 API 管理服務執行個體，請參閱[建立 API 管理服務執行個體][]，以[開始使用 Azure API 管理][]教學課程中。

按一下 [**安全性**]，請從左側的 [ **API 管理**] 功能表，然後按一下 [**外部的身分識別**。

![外部的身分識別][api-management-security-external-identities]

按一下 [ **Azure Active Directory**]。 記下**重新導向的 URL** ] 與 [切換到您的 Azure Active Directory Azure 傳統入口網站中。

![外部的身分識別][api-management-security-aad-new]

按一下 [建立新的 Azure Active Directory 應用程式中，[**新增**] 按鈕，然後選擇 [**新增組織內部開發的應用程式**。

![新增新的 Azure Active Directory 應用程式][api-management-new-aad-application-menu]

輸入應用程式的名稱、 選取**Web 應用程式及/或 Web API**，然後按一下 [下一步] 按鈕。

![新的 Azure Active Directory 應用程式][api-management-new-aad-application-1]

**登入 URL**，輸入您的開發人員入口網站登入 URL。 在此範例中，**登入 URL**是`https://aad03.portal.current.int-azure-api.net/signin`。 

**應用程式識別碼 URL**中，輸入 Azure Active Directory 中的預設網域或自訂網域，然後附加唯一的字串。 在此範例中**https://contoso5api.onmicrosoft.com**的預設網域使用**/api**所指定的尾碼。

![新的 Azure Active Directory 應用程式屬性][api-management-new-aad-application-2]

按一下 [儲存並建立新的應用程式，並切換至 [**設定**] 索引標籤來設定新的應用程式的 [檢查] 按鈕。

![建立新的 Azure Active Directory 應用程式][api-management-new-aad-app-created]

如果要使用此應用程式將多個 Azure Active 目錄，請按一下**[是]** **應用程式是多租用戶**。 預設值為 [**否**]。

![應用程式是多租用戶][api-management-aad-app-multi-tenant]

在 publisher 入口網站的**外部的身分識別**] 索引標籤的**Azure Active Directory**節中複製的 [**重新導向的 URL** ，然後貼到 [**回覆 URL** ] 文字方塊。 

![回覆 URL][api-management-aad-reply-url]

向下捲動至底部的 [設定] 索引標籤、 選取**應用程式權限**] 下拉式清單，以及檢查**讀取目錄資料**。

![應用程式權限][api-management-aad-app-permissions]

選取 [**代理人的權限**] 下拉式清單，然後核取 [**啟用登入並閱讀使用者的設定檔**]。

![委派的權限][api-management-aad-delegated-permissions]

>如需有關應用程式和委派的權限的詳細資訊，請參閱[圖形 API 的存取][]。

將**用戶端識別碼**複製到剪貼簿。

![用戶端識別碼][api-management-aad-app-client-id]

切換回 publisher 入口網站並貼上複製的 Azure Active Directory 應用程式設定的**用戶端識別碼**。

![用戶端識別碼][api-management-client-id]

切換回 Azure Active Directory 設定，並按一下 [**選取期間**下拉式**鍵**] 區段中並指定時間間隔。 在此範例會使用**1 年**。

![索引鍵][api-management-aad-key-before-save]

按一下 [儲存設定並顯示 [索引鍵的 [**儲存**]。 將金鑰複製到剪貼簿。

>記下此鍵。 當您關閉 Azure Active Directory 的 [設定] 視窗時，不能再顯示索引鍵。

![索引鍵][api-management-aad-key-after-save]

切換回 publisher 入口網站，並貼到 [**用戶端 [私人**] 文字方塊中的 [索引鍵。

![用戶端密碼][api-management-client-secret]

**允許租用戶**指定哪一個目錄 API 管理服務執行個體的 api 的存取權。 指定您要授與存取權的 Azure Active Directory 執行個體的網域。 您可以使用換行、 空格或逗號來分隔多個網域。

![允許的租用戶][api-management-client-allowed-tenants]

在 [**允許租用戶**] 區段中，可以指定多個網域。 任何使用者從原始應用程式在哪裡註冊的網域不同的網域，可以登入前，不同的網域的全域管理員必須授與存取目錄資料的應用程式的權限。 若要授與權限，全域管理員必須登入應用程式，並按一下 [**接受**]。 在下列範例`miaoaad.onmicrosoft.com`已新增至**允許的租用戶**和全域管理員從該網域的系統管理員第一次登入。

![權限][api-management-permissions-form]

>如果登入以全域管理員權限授與之前嘗試非全域管理員，登入失敗及錯誤畫面會顯示。

一旦指定所需的設定，請按一下 [**儲存**]。

![儲存][api-management-client-allowed-tenants-save]

後會儲存變更，指定的 Azure Active Directory 中的使用者可以登入開發人員入口網站中[使用的是 Azure Active Directory 帳戶開發人員入口網站登入][]的步驟進行。

## <a name="how-to-add-an-external-azure-active-directory-group"></a>如何新增外部 Azure Active Directory 群組

啟用後的 Azure Active Directory 中的使用者存取，您可以將 Azure Active Directory 群組新增到 API 管理] 以更輕鬆地管理和所需的產品的開發人員] 群組中的關聯。

> 才能設定外部的 Azure Active Directory 群組，請 Azure Active Directory 必須先設定 [身分識別] 索引標籤中的前一節中的程序。 

外部的 Azure Active Directory 群組會新增您要授予存取權給群組的產品中的 [**可見度**] 索引標籤。 按一下 [**產品**]，並按一下所需的產品名稱。

![設定產品][api-management-configure-product]

切換至 [**可見度**] 索引標籤，然後按一下 [**新增群組]，從 Azure Active Directory**。

![新增群組][api-management-add-groups]

從下拉式清單中，選取**Azure Active Directory 租用戶**，然後**群組**以新增文字] 方塊中輸入所要的群組的名稱。

![選取群組][api-management-select-group]

此群組的名稱找不到 [**群組**] 清單中您的 Azure Active Directory，如下列範例所示。

![Azure Active Directory 群組清單][api-management-aad-groups-list]

按一下 [**新增**]，以驗證的群組名稱，並新增群組]。 在此範例中**Contoso 5 開發人員**會新增外部的群組。 

![新增群組][api-management-aad-group-added]

按一下 [儲存新的群組選取 [**儲存**]。

一旦 Azure Azure Active Directory 群組已設定從一個產品，就可用於簽其他產品 API 管理服務執行個體中的 [**可見度**] 索引標籤。

若要檢閱並設定外部群組的內容，已新增後，按一下 [**群組**] 索引標籤群組的名稱。

![管理群組][api-management-groups]

從這裡開始，您可以編輯**名稱**和群組的**描述**。

![編輯群組][api-management-edit-group]

使用者設定的 Azure Active Directory 可以登入的開發人員入口網站及檢視和訂閱的有可見度遵循下一節的指示的所有群組。

## <a name="how-to-log-in-to-the-developer-portal-using-an-azure-active-directory-account"></a>如何登入使用 Azure Active Directory 帳戶開發人員入口網站

若要登入使用各節中所設定的 Azure Active Directory 帳戶開發人員入口網站，開啟新的瀏覽器視窗，使用**登入 URL**從 Active Directory 應用程式設定，然後按一下**Azure Active Directory**。

![開發人員入口網站][api-management-dev-portal-signin]

在您 Azure Active Directory 中，輸入一位使用者的認證，然後按一下 [**登入**]。

![登入][api-management-aad-signin]

必要的任何其他資訊時，可能會提示您註冊表單。 完成註冊表單，然後按一下 [**註冊**]。

![註冊][api-management-complete-registration]

您的使用者現在登入您的 API 管理服務執行個體的開發人員入口網站。

![完成註冊][api-management-registration-complete]



[api-management-management-console]: ./media/api-management-howto-aad/api-management-management-console.png
[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-security-external-identities.png
[api-management-security-aad-new]: ./media/api-management-howto-aad/api-management-security-aad-new.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-aad/api-management-new-aad-application-menu.png
[api-management-new-aad-application-1]: ./media/api-management-howto-aad/api-management-new-aad-application-1.png
[api-management-new-aad-application-2]: ./media/api-management-howto-aad/api-management-new-aad-application-2.png
[api-management-new-aad-app-created]: ./media/api-management-howto-aad/api-management-new-aad-app-created.png
[api-management-aad-app-permissions]: ./media/api-management-howto-aad/api-management-aad-app-permissions.png
[api-management-aad-app-client-id]: ./media/api-management-howto-aad/api-management-aad-app-client-id.png
[api-management-client-id]: ./media/api-management-howto-aad/api-management-client-id.png
[api-management-aad-key-before-save]: ./media/api-management-howto-aad/api-management-aad-key-before-save.png
[api-management-aad-key-after-save]: ./media/api-management-howto-aad/api-management-aad-key-after-save.png
[api-management-client-secret]: ./media/api-management-howto-aad/api-management-client-secret.png
[api-management-client-allowed-tenants]: ./media/api-management-howto-aad/api-management-client-allowed-tenants.png
[api-management-client-allowed-tenants-save]: ./media/api-management-howto-aad/api-management-client-allowed-tenants-save.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-aad/api-management-aad-delegated-permissions.png
[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-permissions-form]: ./media/api-management-howto-aad/api-management-permissions-form.png
[api-management-configure-product]: ./media/api-management-howto-aad/api-management-configure-product.png
[api-management-add-groups]: ./media/api-management-howto-aad/api-management-add-groups.png
[api-management-select-group]: ./media/api-management-howto-aad/api-management-select-group.png
[api-management-aad-groups-list]: ./media/api-management-howto-aad/api-management-aad-groups-list.png
[api-management-aad-group-added]: ./media/api-management-howto-aad/api-management-aad-group-added.png
[api-management-groups]: ./media/api-management-howto-aad/api-management-groups.png
[api-management-edit-group]: ./media/api-management-howto-aad/api-management-edit-group.png

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
[存取該圖表 API]: http://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[開發人員入口網站使用的是 Azure Active Directory 帳戶登入]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account

