<properties
    pageTitle="Azure Active Directory B2C︰ 限制和限制 |Microsoft Azure"
    description="清單限制和 Azure Active Directory B2C 與限制"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-limitations-and-restrictions"></a>Azure Active Directory B2C︰ 限制和限制

有數種功能和 Azure Active Directory (Azure AD) B2C 的並不支援的功能。 許多這些已知的問題和限制將會收件者，但您應該知道這些如果您要建立使用 Azure AD B2C 的消費者具應用程式。

## <a name="issues-during-the-creation-of-azure-ad-b2c-tenants"></a>Azure AD B2C 租用戶建立期間的問題

如果您在[建立 Azure AD B2C 租用戶](active-directory-b2c-get-started.md)時遇到問題，請參閱[建立 Azure AD 租用戶或 Azure AD B2C 租用戶-問題和解決方案](active-directory-b2c-support-create-directory.md)的指引。

請注意，那里已知問題，當您刪除現有的 B2C 租用戶，並重新建立相同的網域名稱。 您必須使用不同的網域名稱建立 B2C 租用戶。

## <a name="note-about-b2c-tenant-quotas"></a>請注意關於 B2C 租用戶配額

根據預設，B2C 租用戶中的使用者數目會限制為 50000 的使用者。 如果您需要提高 B2C 租用戶的配額，您應該連絡支援人員。

## <a name="branding-issues-on-verification-email"></a>驗證電子郵件的商標問題

預設驗證電子郵件包含 Microsoft 商標。 我們會在未來移除。 現在，您可以使用[公司商標功能](../active-directory/active-directory-add-company-branding.md)移除。

## <a name="restrictions-on-applications"></a>應用程式的限制

Azure AD B2C 目前不支援下列類型的應用程式。 支援的類型的應用程式的說明，請參閱[Azure AD B2C︰ 應用程式類型](active-directory-b2c-apps.md)。

### <a name="single-page-applications-javascript"></a>單一頁面應用程式 (JavaScript)

許多新的應用程式有單一頁面應用程式] 選項前端主要 JavaScript 所撰寫，而且經常使用的選項架構，例如 AngularJS、 Ember.js、 Durandal 等等。尚未提供 Azure AD B2C 此流程。

### <a name="daemons--server-side-applications"></a>協助 / 伺服器端應用程式

包含較長時間執行程序，或您沒有使用者的目前狀態的運作的應用程式也需要存取安全的資源，例如網頁 Api 的方式。 這些應用程式可以驗證，並使用應用程式的身分識別 （而非委派的取用者的身分識別） [OAuth 2.0 用戶端認證流程](active-directory-b2c-reference-protocols.md#oauth2-client-credentials-grant-flow)中取得權杖。 此流程尚未提供 Azure AD B2C，因此現在，應用程式後，可以存取權杖只互動式消費者登入流程發生。

### <a name="standalone-web-apis"></a>獨立 Web Api

Azure AD B2C 中，在有[建立受保護的使用 OAuth 2.0 權杖 Web API](active-directory-b2c-apps.md#web-apis)的能力。 不過，該網站的 API 只能接收權杖從用戶端共用相同的應用程式識別碼。 不支援建立網頁的 API，從數種不同的用戶端存取。

### <a name="web-api-chains-on-behalf-of"></a>網頁 API 鏈結 （代表的）

許多架構包含所要打電話給另一種下游 Web API，同時受到 Azure AD B2C Web API。 這種情況下會有 Web API 後端，依序呼叫 Microsoft 線上服務，例如 Azure AD 圖形 API 的原生用戶端的常見。

這個鏈結的 Web API 案例中可支援使用 OAuth 2.0 Jwt 承載者認證授與]，否則會以開啟代表的流程。 不過，在代理人的流程目前尚未 Azure AD B2C 中。

## <a name="restriction-on-libraries-and-sdks"></a>在文件庫和 Sdk 的限制

Microsoft 支援文件庫可 Azure AD B2C 就會十分受到限制這一次。 我們已根據.NET web 應用程式和服務，以及 NodeJS web 應用程式與服務的支援。  我們也有稱為可以搭配 Windows 及其他.NET 應用程式中的 Azure AD B2C 的 MSAL 預覽.NET 用戶端程式庫。

我們目前沒有任何其他語言或平台，包括 iOS 和 Android 支援的文件庫。  如果您想要建立上述所列以外的其他平台上，我們建議使用開啟來源 sdk，您可以參照我們[OAuth 2.0 與 OpenID 連線通訊協定參照](active-directory-b2c-reference-protocols.md)視。  Azure AD B2C 實作 OAuth 與 OpenID 連線，讓它可以整合使用一般 OAuth 或 OpenID 連線文件庫。

我們 iOS 和 Android 的快速入門教學課程使用，我們已測試開啟來源文件庫與 Azure AD B2C 的相容性。  我們的快速入門教學課程都提供我們[快速入門](active-directory-b2c-overview.md#getting-started)] 區段中。

## <a name="restriction-on-protocols"></a>在 [通訊協定的限制

Azure AD B2C 支援 OpenID 連線與 OAuth 2.0。 不過，在實作並非所有的功能和每個通訊協定的功能。 若要深入了解支援的通訊協定功能 Azure AD B2C，閱讀我們的[OpenID 連線與 OAuth 2.0 通訊協定參照](active-directory-b2c-reference-protocols.md)中的範圍。 無法使用 SAML 和 WS Fed 通訊協定支援。

## <a name="restriction-on-tokens"></a>權杖限制

Azure AD B2C 所發行的權杖許多實作 JSON Web 權杖，或 JWTs。 不過，並非所有 JWTs （稱為 「 宣告 」） 中所包含的資訊都時很應該或遺失。 部分範例包括"sub"和"preferred_username 」 宣告。  值、 格式或宣告變更一段時間的意義，為您現有的原則的權杖會受影響，-您可以使用其生產應用程式中的值。  值變更時，我們會提供您機會，在針對各個的原則設定這些變更。  若要進一步瞭解目前發出 Azure AD B2C 服務的權杖，閱讀我們的[權杖參照](active-directory-b2c-reference-tokens.md)。

## <a name="restriction-on-nested-groups"></a>巢狀群組的限制

巢狀的群組的成員資格不支援的 Azure AD B2C 租用戶。 我們不打算將這項功能。

## <a name="restriction-on-differential-query-feature-on-azure-ad-graph-api"></a>Azure AD 圖形 API 上的差異查詢功能的限制

[Azure AD 圖形 API 上的 [差異的查詢] 功能](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-differential-query)不支援的 Azure AD B2C 租用戶。 這是我們長期指南。

## <a name="issues-with-user-management-on-the-azure-classic-portal"></a>使用者管理 Azure 傳統入口網站上的問題

B2C 功能就可以存取 Azure 入口網站上。 不過，您可以使用 Azure 傳統入口網站，若要存取其他租用戶的功能，包括管理使用者。 目前有幾個 Azure 傳統入口網站上的使用者管理 （[**使用者**] 索引標籤） 的已知問題︰

- 本機帳戶使用者 （亦即人員的電子郵件地址和密碼，或使用者名稱和密碼註冊消費者），不會對應到的登入識別項 （電子郵件地址或使用者名稱） 在註冊時所使用的**使用者名稱**] 欄位。 這是因為 Azure 傳統入口網站上顯示的欄位確實使用者主要名稱 (UPN)，這不是在 B2C 案例中。 若要檢視的本機帳戶登入識別項，請在[圖形總管]](https://graphexplorer.cloudapp.net/)中尋找使用者物件。 您會找到相同的問題與社交帳戶使用者 （亦即消費者的 Facebook、 Google + 等註冊），但在此情況下，沒有登入識別項的說話。

    ![本機帳戶-UPN](./media/active-directory-b2c-limitations/limitations-user-mgmt.png)

- 本機帳戶使用者，您將無法編輯的任何欄位，並在 [**設定檔**] 索引標籤上儲存變更。

## <a name="issues-with-admin-initiated-password-reset-on-the-azure-classic-portal"></a>管理員啟動密碼重設 Azure 傳統入口網站上的問題

如果您重設密碼的本機帳戶消費者用，在 Azure 傳統入口網站 （在 [**使用者**] 索引標籤上**重設密碼**命令），該消費者將無法變更在下一個登入，他們的密碼，如果您使用標誌或登入原則，並會鎖定您的應用程式。 解決方法使用[Azure AD 圖形 API](active-directory-b2c-devquickstarts-graph-dotnet.md)消費者的密碼 （不含密碼到期） 或使用登入原則，而不是登或登入原則。

## <a name="issues-with-creating-a-custom-attribute"></a>建立自訂屬性的問題

[加入 Azure 入口網站上的自訂屬性](active-directory-b2c-reference-custom-attr.md)不會立即建立 B2C 租用戶。 您必須使用的自訂屬性中至少一個您用的原則，您 B2C 租用戶中建立並才可透過圖形 API。

## <a name="issues-with-verifying-a-domain-on-the-azure-classic-portal"></a>驗證網域 Azure 傳統入口網站上的問題

目前您無法驗證成功上[Azure 傳統入口網站](https://manage.windowsazure.com/)的網域。

## <a name="issues-with-sign-in-with-mfa-policy-on-safari-browsers"></a>MFA 原則在 Safari 瀏覽器中登入問題

要求間歇性上 （錯誤的要求） HTTP 400 錯誤的 Safari 瀏覽器登入原則 （已開啟的 MFA) 會失敗。 這是因為 Safari 的低 cookie 大小限制。 有數種此問題的因應措施︰

- 使用 「 註冊或登入原則 」，而不是 「 登入原則]。
- 減少**應用程式宣告**原則中所要求的數目。
