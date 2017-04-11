<properties
    pageTitle="v2.0 端點限制及限制 |Microsoft Azure"
    description="清單限制和 Azure AD v2.0 端點的限制。"
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="dastrock"/>

# <a name="should-i-use-the-v20-endpoint"></a>我應該使用 v2.0 端點？

時建立的 Azure Active Directory 與整合應用程式，您必須決定 v2.0 端點和驗證通訊協定是否符合您的需求。  原始的 Azure AD 端點仍完全支援，而且在某些方面，是比 v2.0 更多豐富功能。  不過，v2.0 端點[嚴重優點](active-directory-v2-compare.md)引誘您使用新的程式設計模型的開發人員。

在目前，我們使用 v2.0 端點的建議，如下所示︰

- 如果您想要在應用程式中支援個人 Microsoft 帳戶，您應該使用 v2.0 端點。  但是請務必瞭解本文中所列，尤其是專屬於工作與學校帳戶的限制。
- 如果您的應用程式只需要支援的公司與學校帳戶，您應該使用[原始的 Azure AD 結束點](active-directory-developers-guide.md)。

一段時間，v2.0 端點會增大以排除列於此處，限制，以便只需要使用 v2.0 結束點。  同時，本文會協助您判斷 v2.0 端點是否適合您。  我們會持續一段時間，以反映目前狀態的 v2.0 端點，請回到重新評估您針對 v2.0 功能的需求，因此請查看更新這份文件。

如果您有未使用 v2.0 端點的 Azure AD 與現有的應用程式，但也有不需要從頭開始。  在未來，我們會提供您啟用 v2.0 端點用於現有 Azure AD 應用程式的方式。

## <a name="restrictions-on-apps"></a>應用程式的限制
V2.0 端點目前不支援下列類型的應用程式。  支援的類型的應用程式的說明，請參閱[本文](active-directory-v2-flows.md)。

##### <a name="standalone-web-apis"></a>獨立 Web Api
與 v2.0 端點，您可以建立[的受到保護的使用 OAuth 2.0 Web API](active-directory-v2-flows.md#web-apis)。  不過，該網站的 API 只能收到權杖從應用程式共用相同的應用程式識別碼。  不支援建立網頁的 API，從不同的應用程式識別碼的用戶端存取。  用戶端無法要求，或取得您的網路 API 的權限。

若要查看如何建立接受從用戶端的相同的應用程式識別碼的權杖 Web API，請參閱 v2.0 端點 Web API 範例，在 [[開始使用](active-directory-appmodel-v2-overview.md#getting-started)。

##### <a name="web-api-on-behalf-of-flow"></a>Web API 代表的流程
許多架構包含必須打電話給另一種下游 Web API，同時 v2.0 端點保護 Web API。  這種情況下是常見的 Web API 後端，依序呼叫 Microsoft 線上服務或其他內建的自訂網路 API 支援 Azure AD 的原生用戶端的項目。

可以使用 OAuth 2.0 Jwt 承載者認證授與，又稱為 「 On-Behalf-Of 流程支援這種情況。  不過，在代理人的流程是目前不支援 v2.0 端點。  若要查看此流程如何運作，通常可以 Azure AD 服務，請參閱[在 GitHub 代表的程式碼範例](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet)。

## <a name="restrictions-on-app-registrations"></a>在應用程式登錄] 上的限制
在目前，要從中整合與 v2.0 端點的所有應用程式必須在[apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)建立新的應用程式註冊。  任何現有的 Azure AD 或 Microsoft 帳戶的應用程式不會在相容於 v2.0 端點，也不會在新的應用程式註冊入口網站以外的任何入口網站中登錄應用程式。  我們計畫提供啟用為 v2.0 的應用程式的使用現有的應用程式。 此時，沒有移轉路徑 v2.0 端點應用程式。

同樣地，在新的應用程式註冊入口網站中登錄應用程式無法對原始 Azure AD 驗證結束點。  可用，不過，來建立應用程式註冊入口網站中的應用程式順利與 Microsoft 帳戶驗證端點整合， `https://login.live.com`。

此外，在[apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)所建立的應用程式進行之註冊有下列變更︰

- 不支援 [**首頁**] 屬性，也就是**登入 URL** 。  首頁，而這些應用程式不會顯示 [Office MyApps] 面板中。
- 只有兩個應用程式密碼允許每個應用程式識別碼這一次。
- 僅供檢視及管理單一開發人員帳戶的應用程式註冊。  它不能多個開發人員之間共用。
- 有數種限制 redirect_uri 允許的格式。  請參閱下的一節，如需詳細資訊。

## <a name="restrictions-on-redirect-uris"></a>重新導向 Uri 的限制
新的應用程式註冊入口網站中登錄應用程式僅限於目前有限的 redirect_uri 值。  配置都必須開始的 web 應用程式與服務 redirect_uri `https`，及所有 redirect_uri 值必須都共用單一 DNS 網域。  例如，無法註冊具有 redirect_uris 的 web 應用程式︰

`https://login-east.contoso.com`  
`https://login-west.contoso.com`

註冊系統會比較 redirect_uri 您要新增的 DNS 名稱與現有 redirect_uri 的整個 DNS 名稱。 若要新增的 DNS 名稱要求將會失敗，如果符合下列條件其中一項︰  

- 如果新 redirect_uri 的整個 DNS 名稱不相符的現有 redirect_uri DNS 名稱
- 如果沒有現有 redirect_uri 的子網域新增 redirect_uri 的整個 DNS 名稱。

例如，如果應用程式目前有 redirect_uri:

`https://login.contoso.com`

然後就可以加入︰

`https://login.contoso.com/new`

其符合您的 DNS 名稱，或︰

`https://new.login.contoso.com`

這是 DNS 的子網域 login.contoso.com。  如果您想要有應用程式的登入 east.contoso.com，並且登入-west.contoso.com 為 redirect_uris，則您必須新增下列 redirect_uris 順序︰

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

可以新增後兩種，因為這些子網域的第一個 redirect_uri，contoso.com。 這項限制將會移除即將來臨的版本。

若要瞭解如何註冊新的應用程式註冊入口網站的應用程式，請參閱[本文](active-directory-v2-app-registration.md)。

## <a name="restrictions-on-services--apis"></a>服務和 Api 的限制
V2.0 端點目前支援的登入註冊新應用程式註冊的入口網站，它提供任何應用程式落在[支援的驗證流程](active-directory-v2-flows.md)清單。  不過，這些應用程式只能取得非常有限的資源的 OAuth 2.0 存取權杖。  V2.0 端點只會發出的 access_tokens:

- 要求權杖應用程式。  應用程式可以取得 access_token 本身，如果數種不同的元件或層級由邏輯的應用程式。  若要查看此案例中的，請查看我們的[快速入門](active-directory-appmodel-v2-overview.md#getting-started)教學課程。
- Outlook 郵件、 行事曆及連絡人 REST Api，均位於 https://outlook.office.com。  若要瞭解如何撰寫存取這些 Api 的應用程式，請參閱這些[Office 快速入門](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)教學課程。
- Microsoft Graph Api。  若要深入瞭解 Microsoft Graph 和可用的所有資料，請造訪[https://graph.microsoft.io](https://graph.microsoft.io)。

這次支援的其他服務。  更多 Microsoft 線上服務就會在未來，新增以及支援您自己的自訂內建的 Web Api 和服務。

## <a name="restrictions-on-libraries--sdks"></a>文件庫與 Sdk 的限制
文件庫支援 v2.0 端點是相當有限這一次。  如果您想要使用 v2.0 端點生產應用程式中，您會有下列選項︰

- 如果您要建立 web 應用程式，您就可以執行登入並驗證的權杖，安全地使用我們推出的伺服器端介軟體。  ASP.NET 和我們 NodeJS 密碼外掛程式包括 OWIN 開啟的識別碼連線介軟體。  我們[快速入門](active-directory-appmodel-v2-overview.md#getting-started)] 區段中，也可使用我們介軟體程式碼範例。
- 其他平台和原生和行動應用程式，您也可以整合與 v2.0 端點，直接傳送與接收您的應用程式碼中的通訊協定訊息。  OpenID 連線和 OAuth 通訊協定[已明確記錄](active-directory-v2-protocols.md)可協助您執行這類整合 v2.0。
- 最後，您可以使用 [開啟來源開啟識別碼連線及 OAuth 文件庫與 v2.0 端點整合。  V2.0 通訊協定應該相容於沒有重大變更多個開啟的來源的通訊協定文件庫。  這種文件庫範圍的每個語言與平台，並[開啟識別碼連線](http://openid.net/connect/)與[OAuth 2.0](http://oauth.net/2/)網站維護熱門實作清單。 更多詳細資料，及開啟來源用戶端文件庫和測試與 v2.0 端點的範例的清單，請參閱[Azure Active Directory (AD) v2.0 及驗證的文件庫](active-directory-v2-libraries.md)。

我們也有只但.NET [Microsoft 驗證文件庫 (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)初始預覽。  您可以嘗試此文件庫中.NET 用戶端和伺服器應用程式，但為預覽文件庫 GA 品質還不會提供支援歡迎使用。

## <a name="restrictions-on-protocols"></a>通訊協定的限制
開啟識別碼連線與 OAuth 2.0，只支援 v2.0 結束點。  不過，並非所有的功能和每個通訊協定的功能已納入 v2.0 端點，包括︰

- OpenID 連線`end_session_endpoint`，可讓應用程式，結束與 v2.0 端點的使用者的工作階段。
- 由 v2.0 端點 id_tokens 只包含使用者的成對識別碼。  這表示兩個不同的應用程式將會收到不同的識別碼相同的使用者。  請注意，藉由查詢 Microsoft Graph`/me`端點，您將能夠取得 correlatable ID 可透過應用程式的使用者。
- 不包含發行 v2.0 端點 id_tokens`email`宣告次，即使您取得從使用者的權限，來檢視他們的電子郵件。
- OpenID 連線的使用者資訊結束點。 使用者資訊端點並未實作 v2.0 端點上這一次。  不過，您可能會收到此結束點的所有使用者設定檔資料都，可以從 Microsoft Graph`/me`結束點。
- 角色與群組宣告。  此時，v2.0 端點不支援發行的角色或群組宣告 id_tokens 中。

若要進一步瞭解 v2.0 端點中支援的通訊協定功能的範圍，閱讀我們[OpenID 連線與 OAuth 2.0 通訊協定參照](active-directory-v2-protocols.md)。

## <a name="restrictions-for-work--school-accounts"></a>公司與學校帳戶的限制
有特定尚未 v2.0 端點不支援的 Microsoft 組織/商務使用者的一些功能。

##### <a name="device-based-conditional-access-native-and-mobile-apps-and-the-microsoft-graph"></a>裝置型條件存取原生和行動應用程式，與 Microsoft Graph
V2.0 端點還不支援的裝置驗證行動與原生應用程式，例如原生應用程式在 iOS 或 Android 上執行。  這可能封鎖來自特定組織呼叫 Microsoft Graph 原生應用程式。  當系統管理員設定裝置條件存取原則應用程式上時，就需要裝置驗證。  V2.0 端點，裝置型條件存取最可能的案例是系統管理員在 Microsoft Graph，例如 Outlook API 資源設定原則。  如果系統管理員設定此原則原生應用程式要求 Microsoft graph 的權杖，要求將最後會失敗，因為不支援的裝置驗證。  不過，裝置為基礎的原則設定時，會支援要求 Microsoft graph，權杖的 web 應用程式。  在 web 上執行應用程式案例裝置驗證透過使用者的網頁瀏覽器。

開發人員]，為您最有可能沒有控制項時得到 Microsoft Graph 資源，或甚至注意發生時，會設定原則。  如果您要建立公司與學校使用者的應用程式，您應該使用[原始的 Azure AD 結束點](active-directory-developers-guide.md)，直到 v2.0 端點支援裝置驗證。  如需有關裝置型條件存取的詳細資訊，請查看[本文](active-directory-conditional-access.md#device-based-conditional-access)。

##### <a name="windows-integrated-authentication-for-federated-tenants"></a>Windows 同盟租用戶整合式的驗證
如果您已在 Windows 應用程式中使用 ADAL （以及原始 Azure AD 結束點），您可能會取得善用所謂的 SAML 判斷提示授與。  此授與可讓使用者同盟 Azure AD 租用戶，而不需輸入其認證自動驗證他們的內部部署 Active Directory 執行個體。  SAML 判斷提示授與目前不支援 v2.0 端點。
