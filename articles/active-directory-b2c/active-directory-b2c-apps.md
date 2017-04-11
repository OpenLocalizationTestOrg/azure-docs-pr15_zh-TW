<properties
    pageTitle="Azure AD B2C |Microsoft Azure"
    description="您可以在 Azure Active Directory B2C 中建立的應用程式類型。"
    services="active-directory-b2c"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="07/22/2016"
    ms.author="dastrock"/>

# <a name="azure-active-directory-b2c-types-of-applications"></a>Azure Active Directory B2C︰ 類型的應用程式

Azure Active Directory (Azure AD) B2C 支援各種不同的現代化的應用程式架構驗證。 全部這些根據業界標準通訊協定[OAuth 2.0](active-directory-b2c-reference-protocols.md)或[OpenID 連線](active-directory-b2c-reference-protocols.md)。 這份文件簡要描述您可以建立的應用程式的類型，獨立的語言或平台想。 也可協助您瞭解之前[開始建立應用程式](active-directory-b2c-overview.md#getting-started)的高層級的案例。

## <a name="the-basics"></a>基本概念
使用 Azure AD B2C 每個應用程式必須註冊[B2C 目錄](active-directory-b2c-get-started.md)透過[Azure 入口網站](https://portal.azure.com/)中。 應用程式註冊程序會收集並將的幾個數值指派給您的應用程式︰

- **應用程式識別碼**可唯一識別您的應用程式。
- **重新導向 URI** ，可以用來指示回到您的應用程式的回應。
- 其他任何特定案例的值。 如需詳細資訊，請進一步瞭解如何[註冊應用程式](active-directory-b2c-app-registration.md)。

註冊應用程式之後，通訊與 Azure AD 藉由傳送至 Azure AD v2.0 端點的要求︰

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

每個要求傳送給 Azure AD B2C 指定**原則**。 原則控制 Azure AD 的行為。 您也可以使用這些端點，建立一組可高度自訂使用者經驗。 常見的原則包含註冊、 登入，然後編輯設定檔原則。 如果您不熟悉原則，您應該瞭解 Azure AD B2C[可延伸原則架構](active-directory-b2c-reference-policies.md)再繼續進行。

每個應用程式互動 v2.0 端點所示類似的高層級模式︰

1. 應用程式會引導您執行[原則](active-directory-b2c-reference-policies.md)v2.0 端點使用者。
2. 使用者完成根據原則定義的原則。
4. 應用程式會將某種安全性 token 接收 v2.0 結束點。
5. 應用程式使用的安全性 token 存取受保護的資訊或受保護的資源。
6. 資源伺服器驗證安全性 token，以驗證您可以授與存取權。
7. 應用程式會定期重新整理的安全性 token。

<!-- TODO: Need a page for libraries to link to -->
這些步驟可能不同稍微根據您建立的應用程式的類型。 開啟來源文件庫可以解決您的詳細資料。

## <a name="web-apps"></a>Web 應用程式
如需 web 應用程式 （包括.NET、 PHP、 Java]、 [注音標示、 Python 和 Node.js） 伺服器上裝載及透過瀏覽器存取，Azure AD B2C 支援[OpenID 連線](active-directory-b2c-reference-protocols.md)所有的使用者體驗。 這包含登入，註冊，以及管理設定檔。 Azure AD B2C 實作 OpenID 連線，您的 web 應用程式會發出驗證要求 Azure ad 啟動這些使用者體驗。 要求的結果是`id_token`。 此安全性 token 代表使用者的身分識別。 同時也會提供形式的宣告之使用者的相關的資訊︰

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

深入瞭解的權杖和可用的宣告，應用程式的[B2C 權杖參照](active-directory-b2c-reference-tokens.md)的類型。

在 web 應用程式中，每次執行[原則](active-directory-b2c-reference-policies.md)會採用這些高層級的步驟︰

![Web 應用程式區隔線圖像](./media/active-directory-b2c-apps/webapp.png)

驗證`id_token`使用簽署公開金鑰接收從 Azure AD 足以驗證的使用者身分識別。 這也會將工作階段 cookie，可以用來識別要求後續的頁面上的使用者。

若要查看此案例中的，請嘗試我們的[快速入門] 區段](active-directory-b2c-overview.md#getting-started)的 web 應用程式登入程式碼範例。

除了促進簡單登入，網頁伺服器應用程式可能也需要存取後端 web 服務。 在此情況下，web 應用程式可以執行稍有不同的[OpenID 連線流程](active-directory-b2c-reference-oidc.md)使用授權碼來擷取權杖，並重新整理權杖。 這種情況下是下[Web Api] 區段](#web-apis)中所述。

<!--, and in our [WebApp-WebAPI Getting started topic](active-directory-b2c-devquickstarts-web-api-dotnet.md).-->

## <a name="web-apis"></a>Web Api
您可以使用 Azure AD B2C 至安全的 web 服務，例如您的應用程式 RESTful 網路 API。 Web Api 可以使用 OAuth 2.0 安全其資料，以驗證使用權杖傳入 HTTP 要求。 來電者之間的網路 API 例子 HTTP 要求授權標頭中的權杖︰

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

網路 API 使用權杖驗證 API 呼叫者的身分識別，並從宣告編碼權杖中擷取來電者之間的相關資訊。 深入瞭解的權杖和可用的宣告，應用程式的[Azure AD B2C 權杖參照](active-directory-b2c-reference-tokens.md)的類型。

> [AZURE.NOTE]
    Azure AD B2C 目前支援網頁以自己的已知的用戶端存取的 Api。 例如，您完整的應用程式可能會包含 iOS 應用程式、 Android 應用程式] 及後端網路 API。 此結構完全受支援。 允許合作夥伴用戶端，例如另一個 iOS 應用程式，來存取相同 API 目前不支援的網頁。 所有的元件完成應用程式必須共用單一應用程式識別碼

網路 API 可以從多種類型的用戶端，包括 web 應用程式、 桌面與行動應用程式、 單一頁面應用程式、 伺服器端協助和其他網站的 Api 接收權杖。 以下是通話網路 API 的 web 應用程式的完整流程範例︰

![Web 應用程式網路 API 區隔線圖像](./media/active-directory-b2c-apps/webapi.png)

若要進一步瞭解授權代碼、 重新整理權杖，並取得權杖的步驟，瞭解[OAuth 2.0 通訊協定](active-directory-b2c-reference-oauth-code.md)。

若要瞭解如何使用 Azure AD B2C 保護的網路 API，查看我們的[快速入門] 區段](active-directory-b2c-overview.md#getting-started)中的 API 教學課程 web。

## <a name="mobile-and-native-apps"></a>行動電話及原生應用程式
在行動裝置及桌面應用程式，例如的裝置已安裝的應用程式通常需要存取後端服務或 web Api 代表使用者。 您可以使用 Azure AD B2C 與[OAuth 2.0 授權的程式碼流程](active-directory-b2c-reference-oauth-code.md)原生應用程式與安全地呼叫後端服務至的檔案，以新增自訂的身分識別管理體驗。  

在此流程，應用程式執行[原則](active-directory-b2c-reference-policies.md)，並會接收`authorization_code`從 Azure AD 使用者後原則。 `authorization_code`代表呼叫代表目前登入的使用者的後端服務應用程式的權限。 然後 exchange 應用程式，可以`authorization_code`的背景中`id_token`和`refresh_token`。  應用程式可以使用`id_token`驗證在 HTTP 邀請中的後端網路 API。 也可以使用`refresh_token`取得新`id_token`較舊的項目時到期。

> [AZURE.NOTE]
    Azure AD B2C 目前支援用來存取應用程式自己的後端 web 服務的權杖。 例如，您完整的應用程式可能會包含 iOS 應用程式、 Android 應用程式] 及後端網路 API。 此結構完全受支援。 目前不支援允許使用 OAuth 2.0 存取權杖存取合作夥伴網路 API iOS 應用程式。 所有的元件完成應用程式必須共用單一應用程式識別碼

![原生應用程式區隔線圖像](./media/active-directory-b2c-apps/native.png)

## <a name="current-limitations"></a>目前的限制
Azure AD B2C 目前不支援下列類型的應用程式，但它們位於 roadmapy。 其他限制和 Azure AD B2C 相關的限制所述[的限制與限制](active-directory-b2c-limitations.md)。

### <a name="single-page-apps-javascript"></a>單一頁面應用程式 (JavaScript)
許多新的應用程式有單一頁面應用程式前端主要以 JavaScript 撰寫。 經常使用的架構，例如 AngularJS、 Ember.js 或 Durandal。 推出 Azure AD 服務使用 OAuth 2.0 隱含流程支援這些應用程式。 不過，此流程尚未提供 Azure AD B2C。

### <a name="daemonsserver-side-apps"></a>協助/伺服器端應用程式
包含較長時間執行程序，或您沒有使用者的目前狀態的運作的應用程式也需要存取安全的資源，例如網頁的 Api 的方式。 這些應用程式，可以驗證並取得權杖使用應用程式的身分識別 （而不是使用者委派身分識別） 及使用 OAuth 2.0 用戶端認證流程。

Azure AD B2C 目前不支援此流程。 只有在發生互動式使用者流程後，這些應用程式可以取得權杖。

### <a name="web-api-chains-on-behalf-of-flow"></a>網路 API 鏈結 （代表的流程）
許多架構包含所要撥打另一個下游網路 API，同時可以在其中受到 Azure AD B2C API 的 web。 這種情況下會有 Web API 後端的原生用戶端的常見。 這會呼叫 Microsoft 線上服務，例如 Azure AD 圖形 API。

這個鏈結的網路 API 案例中可支援使用 OAuth 2.0 JWT 承載者認證授與，也就是代表的流程。  不過，代表的流程目前尚未 Azure AD B2C 中。
