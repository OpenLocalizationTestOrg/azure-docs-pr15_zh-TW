<properties
    pageTitle="Azure Active Directory B2C |Microsoft Azure"
    description="如何建立應用程式直接使用支援的 Azure Active Directory B2C 通訊協定。"
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
    ms.topic="article"
    ms.date="07/22/2016"
    ms.author="dastrock"/>

# <a name="azure-ad-b2c-authentication-protocols"></a>Azure AD B2C︰ 驗證通訊協定

Azure Active Directory (Azure AD) B2C 身分識別為您的應用程式的服務提供支援兩個業界標準通訊協定︰ OpenID 連線與 OAuth 2.0。 服務是標準相容，但這些通訊協定的任何兩個實作有細微的差異。  本指南中的資訊會對您有幫助您撰寫程式碼，直接傳送給及處理 HTTP 要求，而不是使用開啟來源文件庫。 我們建議您之前，您就能深入探索每個特定的通訊協定的詳細資訊，請閱讀此頁面。 但如果您已經熟悉 Azure AD B2C，您可以前往直接[通訊協定參考指南](#protocols)。

<!-- TODO: Need link to libraries above -->

## <a name="the-basics"></a>基本概念
需要註冊[Azure 入口網站](https://portal.azure.com)中 B2C 目錄中每個應用程式，使用 Azure AD B2C。 應用程式註冊程序會收集並將的幾個數值指派給您的應用程式︰

- **應用程式識別碼**可唯一識別您的應用程式。
- **重新導向 URI**或**套件識別碼**，可以用來將回應您的應用程式中。
- 其他一些情況特定值。 如需更多]，進一步瞭解[如何註冊您的應用程式](active-directory-b2c-app-registration.md)。

註冊您的應用程式之後，通訊與 Azure AD 藉由傳送至 v2.0 端點的要求︰

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

幾乎所有的 OAuth 和 OpenID 連線書寫 exchange 中包含四個廠商個︰

![OAuth 2.0 角色](./media/active-directory-b2c-reference-protocols/protocols_roles.png)

- Azure AD v2.0 端點**授權伺服器**。 安全地處理相關的使用者資訊與存取權的任何項目。 也會處理之間的流程廠商的信任關係。 它負責驗證使用者的身分識別、 授與及撤銷存取資源，以及發出權杖。 也稱為是身分識別提供者。
- **資源擁有者**通常是 [使用者]。 派對擁有資料，且電源允許存取該資料或資源的第三方。
- **OAuth 用戶端**是您的應用程式。 它會識別其應用程式識別碼]。 通常是使用者互動的合作對象。 它也會要求從授權伺服器的權杖。 資源擁有者必須授與存取資源的用戶端權限。
- **資源伺服器**是資料的資源的所在位置。 信任授權伺服器安全地驗證和授權 OAuth 用戶端。 它也使用承載者存取權杖，以確保可以授與存取資源。

## <a name="policies"></a>原則
可以 Azure AD B2C 原則是最重要的功能的服務。 Azure AD B2C 延伸標準的 OAuth 2.0 和 OpenID 連線通訊協定，藉原則。 可讓 Azure AD B2C 執行更多簡單的驗證和授權。 原則完整描述消費者身分識別體驗，包括註冊，登入並編輯設定檔。 原則可定義管理的 ui 上。 他們可以使用特殊的查詢參數在 HTTP 驗證邀請中執行。 原則不 OAuth 2.0 和 OpenID 連線的標準功能，因此您應該花了解這些時間。 如需詳細資訊，請參閱[Azure AD B2C 原則參考指南](active-directory-b2c-reference-policies.md)。

## <a name="tokens"></a>權杖
Azure AD B2C 實作 OAuth 2.0 和 OpenID 連線可讓您大量使用承載者權杖，包括表示為 JSON web 權杖 (JWTs) 承載者權杖。 承載者權杖是輕量型安全性權杖受保護的資源 」 承載者] 存取。 承載者是可以呈現權杖任何方。 Azure AD 它可以接收承載者權杖之前，必須先驗證合作對象。 但如果所需的步驟不會移至安全的 token 傳輸和儲存空間，是攔截及非預期的廠商所使用。

某些安全性權杖內建的機制使未授權的使用者無法使用，但是承載者權杖沒有這個機制。 他們必須在安全通道，例如傳輸層安全性 (HTTPS) 傳輸。 如果承載者權杖傳輸到外部安全通道，惡意派可用來攔截攻擊取得權杖，並使用未經授權存取受保護的資源。 儲存或以供日後使用快取承載者權杖時，就會套用相同的安全性原則。 確保您的應用程式來傳送，並將承載者權杖儲存安全的方式。

其他承載者權杖的安全性考量，請參閱[RFC 6750 步驟 5](http://tools.ietf.org/html/rfc6750)。

進一步的權杖 Azure AD B2C 中使用不同類型的詳細資訊，可[Azure AD token 參照](active-directory-b2c-reference-tokens.md)中。

## <a name="protocols"></a>通訊協定

當您準備好要檢閱一些範例要求時，您可以開始的其中一個動作的教學課程。 每個會對應到特定驗證案例。 如果您需要的協助決定哪些流程是最適合您時，查看[使用 Azure AD B2C 可以建立的應用程式類型](active-directory-b2c-apps.md)。

- [使用 OAuth 2.0 建置行動與原生應用程式](active-directory-b2c-reference-oauth-code.md)
- [建立 web 應用程式使用 OpenID 連線](active-directory-b2c-reference-oidc.md)
