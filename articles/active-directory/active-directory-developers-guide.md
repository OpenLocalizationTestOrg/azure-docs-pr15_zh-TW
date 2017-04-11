<properties
   pageTitle="Azure Active Directory 開發人員指南 |Microsoft Azure"
   description="本文提供 Azure Active Directory 開發人員導向的資源指南全面涵蓋所有內容。"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/24/2016"
   ms.author="mbaldwin"/>


# <a name="azure-active-directory-developers-guide"></a>Azure Active Directory 開發人員指南

## <a name="overview"></a>概觀
為身分識別管理服務 (IDMaaS) 平台 Azure Active Directory (AD) 會提供開發身分識別管理整合應用程式的有效方式。 下列文章提供實作與 Azure AD 重要功能的概觀。 建議您讀取順序，或如果您準備好深入跳至[快速入門](#getting-started)。


1. [Azure AD 整合的好處](./develop/active-directory-how-to-integrate.md)︰ 探索為什麼 Azure AD 整合提供安全登入和授權的最佳解決方案。

1. [Azure AD 驗證案例](active-directory-authentication-scenarios.md)︰ 利用簡化 Azure AD 提供登入您的應用程式中的驗證。

1. [整合應用程式與 Azure AD](active-directory-integrating-applications.md)︰ 了解如何新增、 更新及移除應用程式從 Azure AD，以及整合式應用程式的商標指導方針。

1. [Azure AD 圖形 API](active-directory-graph-api.md)︰ 使用 Azure AD 圖形 API 以程式設計方式存取 Azure AD，透過 REST API 端點。 Azure AD 圖形 API 也是透過[Microsoft Graph](https://graph.microsoft.io/)。 Microsoft Graph 會提供可讓您存取多個 Microsoft 雲端服務的 Api，透過單一 REST API 端點，並以單一存取權杖整合的 API。

1. [Azure AD 驗證文件庫](active-directory-authentication-libraries.md)︰ 輕鬆地驗證使用者，以取得.net，JavaScript] 使用 Azure AD 驗證文件庫的存取權杖目標 C、 Android 及其他功能。


## <a name="getting-started"></a>快速入門

這些教學課程專為多個平台打造，可協助您快速開始開發與 Azure Active Directory。 成必要條件，您必須[取得 Azure Active Directory 租用戶](active-directory-howto-tenant.md)。

### <a name="mobile-and-pc-application-quick-start-guides"></a>行動電話及電腦應用程式快速入門指南

|[![iOS](./media/active-directory-developers-guide/ios.png)](active-directory-devquickstarts-ios.md)|[![Android](./media/active-directory-developers-guide/android.png)](active-directory-devquickstarts-android.md)|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-dotnet.md)|[![Windows 標準](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsstore.md)|[![Xamarin](./media/active-directory-developers-guide/xamarin.png)](active-directory-devquickstarts-xamarin.md)|[![Cordova](./media/active-directory-developers-guide/cordova.png)](active-directory-devquickstarts-cordova.md)|[![OAuth 2.0](./media/active-directory-developers-guide/oauth-2.png)](active-directory-protocols-oauth-code.md)
|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
|[iOS](active-directory-devquickstarts-ios.md)|[Android](active-directory-devquickstarts-android.md)|[.NET](active-directory-devquickstarts-dotnet.md)|[Windows 標準](active-directory-devquickstarts-windowsstore.md)|[Xamarin](active-directory-devquickstarts-xamarin.md)|[Cordova](active-directory-devquickstarts-cordova.md)|[直接與 OAuth 2.0 整合](active-directory-protocols-oauth-code.md)|

### <a name="web-application-quick-start-guides"></a>Web 應用程式快速入門指南

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapp-dotnet.md)|[![Java](./media/active-directory-developers-guide/java.png)](active-directory-devquickstarts-webapp-java.md)|[![AngularJS](./media/active-directory-developers-guide/angularjs.png)](active-directory-devquickstarts-angular.md)|[![Javascript](./media/active-directory-developers-guide/javascript.png)](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi)|[![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-openidconnect-nodejs.md) | [![OpenID 連線](./media/active-directory-developers-guide/openid-connect.png)](active-directory-protocols-openid-connect-code.md)
|:--:|:--:|:--:|:--:|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapp-dotnet.md)|[Java](active-directory-devquickstarts-webapp-java.md)|[AngularJS](active-directory-devquickstarts-angular.md)|[Javascript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi)|[Node.js](active-directory-devquickstarts-openidconnect-nodejs.md)|[直接與 OpenID 整合連線](active-directory-protocols-openid-connect-code.md)|

### <a name="web-api-quick-start-guides"></a>網路 API 快速入門指南

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapi-dotnet.md)|[![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-webapi-nodejs.md)
|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapi-dotnet.md)|[Node.js](active-directory-devquickstarts-webapi-nodejs.md)

### <a name="querying-the-directory-quickstart-guide"></a>查詢目錄快速入門指南

| [![.NET](./media/active-directory-developers-guide/graph.png)](active-directory-graph-api-quickstart.md)|
|:--:|
|[圖形 API](active-directory-graph-api-quickstart.md)|

## <a name="how-tos"></a>操作說明

下列文章說明如何使用 Azure Active Directory 執行特定工作︰

- [取得 Azure AD 租用戶](active-directory-howto-tenant.md)
- [登入使用多租用戶應用程式模式任何 Azure AD 使用者](active-directory-devhowto-multi-tenant-overview.md)
- 啟用在[Android](active-directory-sso-android.md)和[iOS](active-directory-sso-ios.md)裝置上使用 ADAL，跨應用程式 SSO
- [讓您的應用程式認證 AppSource Azure AD](active-directory-devhowto-appsource-certified.md)
- [Azure AD 應用程式庫中的應用程式] 清單](active-directory-app-gallery-listing.md)
- [針對 Office 365 連線到銷售者儀表板提交 web 應用程式](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)
- [了解 Azure Active Directory 應用程式資訊清單](active-directory-application-manifest.md)
- [瞭解在用戶端應用程式中的 [登入和應用程式] 擷取按鈕的商標指導方針](active-directory-branding-guidelines.md)
- [預覽︰ 如何建立應用程式的登入同時個人和公司或學校帳戶的使用者](active-directory-appmodel-v2-overview.md)
- [預覽︰ 如何建立應用程式的登入與登入客戶](../active-directory-b2c/active-directory-b2c-overview.md)
- [預覽︰ Azure AD 中設定權杖存留時間](active-directory-configurable-token-lifetimes.md)使用 PowerShell。 在 [透過 Azure AD 圖形 API 設定，請參閱[原則作業](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations)與[原則實體](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity)如需詳細資訊。

## <a name="reference"></a>參照

這些文章的其餘和驗證的文件庫的 Api、 通訊協定、 錯誤、 程式碼範例，以及結束點提供 foundation 參照。  

###  <a name="support"></a>支援
- [標籤問題](http://stackoverflow.com/questions/tagged/azure-active-directory)︰ 尋找 Azure Active Directory 方案堆疊溢位藉由搜尋的標籤[azure active 目錄](http://stackoverflow.com/questions/tagged/azure-active-directory)及[adal](http://stackoverflow.com/questions/tagged/adal)。
- 請參閱[Azure AD 開發人員詞彙](active-directory-dev-glossary.md)的一些常用的字詞相關應用程式開發和整合的定義。

### <a name="code"></a>程式碼

- [Azure Active Directory 開啟來源文件庫](http://github.com/AzureAD)︰ 尋找文件庫的來源最簡單的方法是使用我們的[文件庫清單](active-directory-authentication-libraries.md)。

- [Azure Active Directory 範例](https://github.com/azure-samples?query=active-directory)︰ 瀏覽範本清單的最簡單方法是使用[的程式碼範例索引](active-directory-code-samples.md)。

- 使用[最新的主要版本](https://docs.microsoft.com/active-directory/adal/microsoft.identitymodel.clients.activedirectory)和[舊的主要版本](https://docs.microsoft.com/active-directory/adal/v2/microsoft.identitymodel.clients.activedirectory) [Active Directory 驗證文件庫 (ADAL).net](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet)參考文件。

### <a name="graph-api"></a>圖形 API

- [圖形 API 參考](https://msdn.microsoft.com/library/azure/hh974476.aspx)︰ Azure Active Directory 圖表 API 的其餘部分參考。 [檢視互動式圖表 API 參考體驗](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)。

- [圖形 API 權限範圍](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)︰ OAuth 2.0 權限範圍是用來控制存取權，應用程式目錄的租用戶中的資料。

### <a name="authentication-and-authorization-protocols"></a>驗證與授權的通訊協定

- [Azure AD 中登入鍵變換](active-directory-signing-key-rollover.md)︰ 深入了解 Azure AD 簽署金鑰變換頻率，以及如何更新為最常見的應用程式案例的索引鍵。

- [OAuth 2.0 通訊協定︰ 使用授權的程式碼授與](active-directory-protocols-oauth-code.md)︰ 您可以使用 OAuth 2.0 通訊協定的授權的程式碼授與授權存取 Web 應用程式，而且您的 Azure Active Directory 中的網頁 Api 租用戶。

- [OAuth 2.0 通訊協定︰ 了解隱含授與](active-directory-dev-understanding-oauth2-implicit-grant.md)︰ 深入了解隱含授權授與]，以及是否適合您的應用程式。

- [OAuth 2.0 通訊協定︰ 服務電話使用用戶端憑證服務](active-directory-protocols-oauth-service-to-service.md): OAuth 2.0 用戶端憑證授與允許 web 服務 （機密用戶端） 使用自己的憑證驗證呼叫其他的 web 服務時，而不是模擬使用者。 在此案例中，通常的用戶端是中間層 web 服務、 精靈服務或網站。

- [OpenID 連線 1.0 通訊協定︰ 登入及驗證](active-directory-protocols-openid-connect-code.md): OpenID 連線 1.0 通訊協定延伸 OAuth 2.0 作為驗證通訊協定。 用戶端應用程式可以接收 id_token 管理登入程序，或擴大接收 id_token 」 和 「 授權代碼授權代碼流程。

- [SAML 2.0 通訊協定參照](active-directory-saml-protocol-reference.md)︰ SAML 2.0 通訊協定讓應用程式，為使用者提供單一登入體驗。

- [1.2 WS 同盟通訊協定](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html)︰ Azure Active Directory 支援 WS 同盟 1.2 依照 Web 服務同盟版本 1.2 規格。 如需有關同盟中繼資料文件的詳細資訊，請參閱[同盟中繼資料](active-directory-federation-metadata.md)。

- [支援的權杖和宣告類型](active-directory-token-and-claims.md)︰ 您可以使用本指南，瞭解並評估 SAML 2.0 和 JSON 網頁權杖 (JWT) 權杖中的宣告。

## <a name="videos"></a>視訊

### <a name="build"></a>建立

這些概觀簡報，在開發應用程式使用 Azure Active Directory 功能直接在工程團隊的喇叭。 簡報包含基本的主題，包括 IDMaaS、 驗證、 識別同盟和單一登入。

- [Microsoft 身分識別︰ 狀態聯集和未來的方向](https://azure.microsoft.com/documentation/videos/build-2016-microsoft-identity-state-of-the-union-and-future-direction/)
- [為新的應用程式服務的 azure Active Directory︰ 身分識別管理](https://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications/)
- [開發與 Azure Active Directory 的現代化的 web 應用程式](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory/)
- [開發與 Azure Active Directory 新式原生應用程式](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory/)

### <a name="azure-friday"></a>Azure 星期五
[Azure 星期五](https://azure.microsoft.com/documentation/videos/azure-friday/)是週期性星期五 1:1 視訊系列專門用來讓您的簡短 （10-15 分鐘） 面談與專家的各種不同的 Azure 主題。  若要查看所有的 Azure Active Directory 影片，在頁面上使用服務篩選功能。

- [Azure 身分識別 101](https://azure.microsoft.com/documentation/videos/azure-identity-basics/)
- [Azure 身分識別 102](https://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)
- [Azure 身分識別出 103](https://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)

## <a name="social"></a>社交

- [Active Directory 小組部落格](http://blogs.technet.com/b/ad/)︰ 世界的 Azure Active Directory 取得的最新資訊。

- [Azure Active Directory 圖表小組部落格](http://blogs.msdn.com/b/aadgraphteam)︰ 特定圖形 api 的 Azure Active Directory 資訊。

- [雲端身分識別](http://www.cloudidentity.net)︰ 想法的身分識別管理的服務，從主要 Azure Active Directory PM。  

- [Twitter 上的 azure Active Directory](https://twitter.com/azuread): Azure Active Directory 中 140 個字元或更少的相關公告。

## <a name="windows-server-on-premises-development"></a>Windows Server 內部開發
如需使用 Windows Server 和 Active Directory Federation Services (ADFS) 開發的指引，請參閱︰

- [適用於開發人員 AD FS 案例](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/overview/ad-fs-scenarios-for-developers)︰ 提供 AD FS 元件和運作方式，使用支援的驗證授權案例的詳細資料的概觀。
- [AD FS 逐步解說](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/ad-fs-development)︰ 逐步說明文章，實作相關的驗證授權流提供逐步指示的清單。
