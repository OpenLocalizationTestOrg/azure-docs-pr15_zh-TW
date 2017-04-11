<properties
   pageTitle="如何取得適用於 Azure Active Directory 認證 AppSource |Microsoft Azure"
   description="如何取得您的應用程式的 Azure Active Directory 認證 AppSource 的詳細資料。"
   services="active-directory"
   documentationCenter=""
   authors="skwan"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/28/2016"
   ms.author="skwan;bryanla"/>

#<a name="how-to-get-appsource-certified-for-azure-active-directory-ad"></a>如何取得 AppSource 認證的 Azure Active Directory (AD) 

若要接收 Azure ad AppSource 憑證，您的應用程式必須圖樣實作多租用戶登與 Azure AD 使用 OpenID 連線、 OAuth 2.0 SAML 2.0 的通訊協定。 

如果您不熟悉 Azure AD 登入或多個租用戶應用程式開發︰

1. 首先請閱讀有關[瀏覽器中的 Azure AD 的驗證案例的 Web 應用程式案例][AAD-Auth-Scenarios-Browser-To-WebApp]。  
2. 接下來，請參閱 Azure AD [web 應用程式快速入門指南][AAD-QuickStart-Web-Apps]，其中將示範如何實作登入，並且包含小幫手] 程式碼範例。 

    > [AZURE.TIP] 請試試我們將協助您快速上手與 Azure Active Directory 中只需幾分鐘的新[開發人員入口網站](https://identity.microsoft.com/Docs/Web)的預覽 ！  開發人員入口網站會引導您執行登錄應用程式，並將 Azure AD 整合至您的程式碼的程序。  完成後，您會有一個簡單的應用程式，可以驗證您的租用戶和後端，可接受權杖，並執行驗證的使用者。

3. 若要瞭解如何實作與 Azure AD 登入模式多租用戶，請查看[如何登入任何使用多租用戶應用程式模式的 Azure Active Directory (AD) 使用者][AAD-Howto-Multitenant-Overview]

## <a name="related-content"></a>相關的內容
如需有關建置應用程式的支援 Azure AD 登入]，或取得說明及支援]，請參閱[Azure AD 開發人員指南][AAD-Dev-Guide]。

請若要提供意見反應，並協助我們精簡和圖案內容，使用下列本文 Disqus 註解] 區段。

<!--Reference style links -->
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]: ./active-directory-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Howto-Multitenant-Overview]: ./active-directory-devhowto-multi-tenant-overview.md
[AAD-QuickStart-Web-Apps]: ./active-directory-developers-guide.md#web-application-quick-start-guides


<!--Image references-->










