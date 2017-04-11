<properties
    pageTitle="V2.0 結束點類型 |Microsoft Azure"
    description="應用程式和 Azure AD v2.0 端點所支援的案例的類型。"
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

# <a name="types-of-apps-for-the-v20-endpoint"></a>V2.0 端點的應用程式的類型
V2.0 端點支援各種不同的現代化的應用程式架構，這些都根據業界標準通訊協定[OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow)及/或[OpenID 連線](active-directory-v2-protocols.md#openid-connect-sign-in-flow)驗證。  此文件簡要描述您可以建立的應用程式的類型，獨立的語言或平台想。  它會協助您瞭解之前您[直接在程式碼](active-directory-appmodel-v2-overview.md#getting-started)的高層級的案例。

> [AZURE.NOTE]
    並非所有的 Azure Active Directory 案例與功能支援 v2.0 結束點。  若要判斷是否您應該使用 v2.0 端點，請閱讀有關[v2.0 限制](active-directory-v2-limitations.md)。

## <a name="the-basics"></a>基本概念
使用 v2.0 端點每個應用程式，則必須在[apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)註冊。  應用程式註冊程序會收集與將的幾個數值指派給您的應用程式︰

- **應用程式識別碼**可唯一識別您的應用程式
- **重新導向 URI** ，可以用來將回應您的應用程式
- 其他一些情況特定值。  如需更多詳細資料，請進一步瞭解如何[註冊應用程式](active-directory-v2-app-registration.md)。

一旦註冊，應用程式與通訊 Azure AD 藉由傳送要求給 Azure Active Directory v2.0 端點。  我們提供開啟來源架構與這些要求的細節會處理的文件庫，或您可以實作驗證邏輯自己製作這些端點的要求︰

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```
<!-- TODO: Need a page for libraries to link to -->

## <a name="web-apps"></a>Web 應用程式
Web 應用程式 （.NET、 PHP、 Java]、 [注音標示、 Python、 節點等），可以透過瀏覽器存取，您可以執行使用者登入使用[OpenID 連線](active-directory-v2-protocols.md#openid-connect-sign-in-flow)。  在 OpenID 連線的 web 應用程式會收到`id_token`，安全性 token 驗證使用者的身分識別，並且提供在表單中的 [宣告使用者的相關資訊︰

```
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

您可以瞭解所有類型的權杖和[v2.0 權杖參照](active-directory-v2-tokens.md)中的應用程式可用的宣告。

在網頁伺服器應用程式，登入驗證流程會採用這些高層級的步驟︰

![Web 應用程式區隔線圖像](../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

使用來自 v2.0 端點簽署公開金鑰 id_token 驗證足以確保使用者的身分識別和設定工作階段 cookie，可以用來識別要求後續的頁面上的使用者。

若要查看的實際操作這種情況下，嘗試其中一個 web 應用程式登入的程式碼範例，我們[快速入門](active-directory-appmodel-v2-overview.md#getting-started)] 區段中。

簡單登入，除了網頁伺服器應用程式可能也需要存取如 REST API 的一些其他 web 服務。  在此情況下的網頁伺服器應用程式可以加入合併 OpenID 連線與 OAuth 2.0 的流程，使用[OAuth 2.0 授權程式碼流程](active-directory-v2-protocols.md#oauth2-authorization-code-flow)。 我們[開始 WebApp WebAPI 主題](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md)下方討論這種情況。

## <a name="web-apis"></a>Web Api
若要保護 web 服務，例如您的應用程式 RESTful Web API，您可以使用 v2.0 端點。  而不是 id_tokens 及工作階段 cookie Web Api 使用 OAuth 2.0 access_tokens 保護其資料，以及驗證傳入的邀請。  來電者之間的 Web API 例子 access_token HTTP 要求授權標頭中︰

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Web API 使用 access_token 驗證 API 呼叫者的身分識別，並從宣告編碼 access_token 中擷取來電者之間的相關資訊。  您可以瞭解所有類型的權杖和[v2.0 權杖參照](active-directory-v2-tokens.md)中的應用程式可用的宣告。

Web API 可以功能可讓使用者選擇-在/退出的特定功能或資料公開權限，否則會以[範圍](active-directory-v2-scopes.md)。  取得範圍的權限之呼叫應用程式，使用者必須期間流程同意的範圍。  要求使用者權限] 與 [在 Web API 收到的所有 access_tokens 錄製這些權限會負責 v2.0 結束點。  所有網頁 API 需要擔心驗證在每個呼叫收到 access_tokens，而執行適當的授權，檢查項目。

Web API 可以收到所有類型的應用程式，包括網頁伺服器應用程式、 桌面和行動應用程式、 單一頁面應用程式、 伺服器端協助，以及其他 Web Api access_tokens。  Web api 驗證高層級的流程如下所示︰

![網路 API 區隔線圖像](../media/active-directory-v2-flows/convergence_scenarios_webapi.png)

若要進一步瞭解如何 authorization_codes、 refresh_tokens，並開始 access_tokens 的詳細的步驟，瞭解[OAuth 2.0 通訊協定](active-directory-v2-protocols-oauth-code.md)。

若要瞭解如何保護 web api 與 OAuth2 access_tokens，請查看我們的[快速入門] 區段](active-directory-appmodel-v2-overview.md#getting-started)中的網頁 api 程式碼範例。


## <a name="mobile-and-native-apps"></a>行動電話及原生應用程式
在裝置上，例如行動與桌面應用程式已安裝的應用程式通常需要存取後端服務或 Web Api，儲存資料，並執行各種不同的函數，代表使用者。  這些應用程式可以新增後端服務使用[OAuth 2.0 授權程式碼流程](active-directory-v2-protocols-oauth-code.md)登入和授權。  

在此流程，應用程式會 authorization_code 接收來自 v2.0 端點在使用者登入，代表呼叫代表目前登入使用者的後端服務應用程式的權限。  應用程式然後交換 OAuth 2.0 access_token 和 refresh_token 背景中的 authoriztion_code。  應用程式可以使用 access_token 在 HTTP 要求，驗證 Web Api，而且可以使用舊的到期時收到新 access_tokens refresh_token。

![原生應用程式區隔線圖像](../media/active-directory-v2-flows/convergence_scenarios_native.png)

## <a name="single-page-apps-javascript"></a>單一頁面應用程式 (javascript)
許多新的應用程式有單一頁面應用程式] 選項前端 javascript 主要撰寫並經常使用架構例如 AngularJS、 Ember.js、 Durandal 等等。 Azure AD v2.0 端點支援使用[OAuth 2.0 隱含流程](active-directory-v2-protocols-implicit.md)這些應用程式。

在此流程，應用程式會收到權杖從 v2.0 直接，而不執行任何後端伺服器交換授權端點。  這個選項可讓所有驗證邏輯和工作階段處理才能都置於完全 javascript 的用戶端，而不執行額外的頁面重新導向。

![隱含流程區隔線圖像](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

若要查看的實際操作這種情況下，嘗試其中一個我們[快速入門](active-directory-appmodel-v2-overview.md#getting-started)] 區段中的單一頁面應用程式碼範例。

### <a name="daemonsserver-side-apps"></a>協助/伺服器端應用程式
包含長時間執行的程序，或您沒有使用者的目前狀態的運作的應用程式也需要存取安全的資源，例如網頁 Api 的方式。  這些應用程式，可以驗證並開始使用應用程式的身分識別 （而非使用者委派身分識別） 使用 OAuth 2.0 用戶端的權杖認證流程。

在此流程，應用程式，請取得以直接互動的權杖`/token`結束點︰

![精靈應用程式區隔線圖像](../media/active-directory-v2-flows/convergence_scenarios_daemon.png)

若要建立的精靈應用程式，請參閱我們[快速入門](active-directory-appmodel-v2-overview.md#getting-started)] 區段中的用戶端認證 documeenation 或參照[此.NET 範例應用程式](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)。

## <a name="current-limitations"></a>目前的限制
這些類型的應用程式目前不支援 v2.0 端點，但在藍圖上。  其他限制規定 v2.0 端點[v2.0 限制本文](active-directory-v2-limitations.md)所述。

### <a name="chained-web-apis-on-behalf-of"></a>鏈結的 web Api （代表的）
許多架構包含必須打電話給另一種下游 Web API，同時 v2.0 端點保護 Web API。  這種情況下會有 Web API 後端，依序呼叫 Microsoft 線上服務，例如 Office 365 或圖形 API 的原生用戶端的常見。

可以使用 OAuth 2.0 Jwt 承載者認證授與，又稱為 「 [On-Behalf-Of 流程](active-directory-v2-protocols.md#oauth2-on-behalf-of-flow)的支援此鏈結的 Web API 案例。  不過，在代理人的流程目前尚未 v2.0 端點。  若要查看此流程如何運作，通常可以 Azure AD 服務，請參閱[在 GitHub 代表的程式碼範例](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet)。
