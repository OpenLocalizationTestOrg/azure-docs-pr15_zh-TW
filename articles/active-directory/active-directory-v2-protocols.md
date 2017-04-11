<properties
    pageTitle="Azure AD v2.0 通訊協定 |Microsoft Azure"
    description="Azure AD v2.0 端點所支援的通訊協定輔助線。"
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
    ms.date="09/16/2016"
    ms.author="dastrock"/>

# <a name="v20-protocols---oauth-20--openid-connect"></a>v2.0 通訊協定-OAuth 2.0 與 OpenID 連線

Azure AD 可以使用 v2.0 端點身分識別為-的-服務業界標準通訊協定，OpenID 連線與 OAuth 2.0。  符合標準服務時，可以是下列通訊協定的任何兩個實作細微的差異。  如果您選擇要直接傳送給撰寫程式碼及處理 HTTP 要求或使用 3rd 廠商開啟來源文件庫，而不是使用其中一個我們開啟來源文件庫，就會很有用的資訊。
<!-- TODO: Need link to libraries above -->

> [AZURE.NOTE]
    並非所有的 Azure Active Directory 案例與功能支援 v2.0 結束點。  若要判斷是否您應該使用 v2.0 端點，請閱讀有關[v2.0 限制](active-directory-v2-limitations.md)。

## <a name="the-basics"></a>基本概念
幾乎所有的 OAuth 與 OpenID 連線書寫共有四個廠商 exchange 中︰

![OAuth 2.0 角色](../media/active-directory-v2-flows/protocols_roles.png)

- **授權伺服器**是 v2.0 結束點。  它負責確保使用者的身分識別、 授與及撤銷存取資源，以及發出權杖。  也稱為是身分識別提供者-安全地處理對使用者的資訊、 其存取權，以及信任之間的關係廠商 flow 中的任何項目。
- **資源擁有者**通常是 [使用者]。  這是擁有資料，並有可允許存取該資料或資源的協力廠商 power 的合作對象。
- **OAuth 用戶端**是您的應用程式，可由其應用程式識別碼。  通常合作對象的使用者進行互動，並將其從授權伺服器要求權杖。  用戶端必須擁有者的資源授與存取資源的權限。
- **資源伺服器**是資料的資源的所在位置。  它信任安全地驗證和授權 OAuth 用戶端授權伺服器，並使用承載者 access_tokens，以確保可以授與存取資源。


## <a name="app-registration"></a>應用程式註冊
需要使用 v2.0 端點每個應用程式之前，可以互動使用 OAuth 或 OpenID 連線在[apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)註冊。  應用程式註冊程序會收集與將的幾個數值指派給您的應用程式︰

- **應用程式識別碼**可唯一識別您的應用程式
- **重新導向 URI**或可以用來將回應您的應用程式的**套件識別碼**
- 其他一些情況特定值。

如需詳細資訊，請進一步瞭解如何[註冊應用程式](active-directory-v2-app-registration.md)。

## <a name="endpoints"></a>結束點
一旦註冊，應用程式與通訊 Azure AD 藉由傳送至 v2.0 端點的要求︰

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

位置`{tenant}`可採取的其中一個四個不同的值︰

| 值 | 描述 |
| ----------------------- | ------------------------------- |
| `common` | 從 Azure Active Directory 登入應用程式，可讓使用者同時使用個人 Microsoft 帳戶與工作/學校帳戶。 |
| `organizations` | 僅允許使用者使用工作/學校帳戶登入應用程式的 Azure Active directory。 |
| `consumers` | 僅允許使用者使用個人 Microsoft 帳戶 (MSA) 登入應用程式。 |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490`或`contoso.onmicrosoft.com` | 僅允許使用者使用工作/學校帳戶登入應用程式中的特定的 Azure Active Directory 租用戶中。  可以使用 [Azure AD 租用戶的易記的網域名稱或租用戶的 guid 識別碼。  |

更多有關如何使用這些端點的詳細資訊，請選擇以下的特定應用程式類型。

## <a name="tokens"></a>權杖
V2.0 實作 OAuth 2.0 和 OpenID 連線進行大量使用承載者權杖，包括表示為 JWTs 承載者權杖。 承載者權杖是輕量型安全性權杖受保護的資源 」 承載者] 存取。 因此，「 承載者 」 是可以展示權杖任何方。 如果所需的步驟不會移至安全的 token 傳輸和儲存空間，必須先派驗證與接收承載者權杖，Azure AD，雖然是攔截及非預期的廠商所使用。 雖然某些安全性權杖有使用時，防止未經授權的合作對象的內建機制，承載者權杖沒有這個機制，且必須安全通道，例如傳輸層安全性 (HTTPS) 中傳輸。 如果承載者權杖會以清除傳輸，男人增益集攻擊可讓惡意派取得權杖，並用於未經授權存取受保護的資源。 儲存或快取承載者權杖，以供日後使用時，就會套用相同的安全性原則。 確保您的應用程式來傳送，並將承載者權杖儲存安全的方式。 更多承載者權杖上的安全性考量，請參閱[RFC 6750 步驟 5](http://tools.ietf.org/html/rfc6750)。

使用[v2.0 端點 token 參照](active-directory-v2-tokens.md)中進一步的詳細資料的不同類型的權杖用於 v2.0 結束點。

## <a name="protocols"></a>通訊協定

如果您準備好要查看一些範例要求，開始使用下列其中一項下方教學課程。  每個會對應到特定驗證案例。  如果您需要協助您判斷哪您的右流程，查看[您可以建立使用 v2.0 應用程式類型](active-directory-v2-flows.md)。

- [建立行動和與 OAuth 2.0 的原生應用程式](active-directory-v2-protocols-oauth-code.md)
- [建立 Web 應用程式開啟識別碼連線](active-directory-v2-protocols-oidc.md)
- [建立單一頁面相關應用程式與 OAuth 2.0 隱含流程](active-directory-v2-protocols-implicit.md)
- [建立協助或伺服器端處理程序和 OAuth 2.0 用戶端憑證流程](active-directory-v2-protocols-oauth-client-creds.md)
- 權杖參與 Web API 與 OAuth 2.0 開啟代表的流程 （即將推出）

<!-- - Get tokens using a username & password with the OAuth 2.0 Resource Owner Password Credentials Flow (coming soon) --> 
