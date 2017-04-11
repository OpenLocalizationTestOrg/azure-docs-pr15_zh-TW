<properties
   pageTitle="如何建立的應用程式，可以登入任何 Azure Active Directory 使用者 |Microsoft Azure"
   description="Step by step 建置應用程式的指示可以從任何 Azure Active Directory 租用戶，也稱為多租用戶的應用程式在使用者登入。"
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
   ms.date="10/11/2016"
   ms.author="skwan;bryanla"/>

# <a name="how-to-sign-in-any-azure-active-directory-ad-user-using-the-multi-tenant-application-pattern"></a>如何登入任何使用多租用戶應用程式模式的 Azure Active Directory (AD) 使用者
如果您的軟體提供許多組織服務應用程式，您可以設定您的應用程式接受登增益集的任何 Azure AD 租用戶。  Azure AD 中，稱為 [讓您的應用程式的多租用戶。  在任何 Azure AD 租用戶中的使用者無法登入同意其帳戶使用您的應用程式之後，應用程式。  

如果您有自己的帳戶系統，或支援其他類型的其他雲端提供者入現有的應用程式時，從任何租用戶加入 Azure AD 登入是登的非常簡單，註冊您的應用程式中透過 OAuth2、 OpenID 連線，或 SAML，程式碼新增登與登入 Microsoft] 按鈕放在您的應用程式。 按一下下方若要進一步瞭解商標應用程式] 按鈕。

[![登入] 按鈕][AAD-登入]][AAD-App-Branding]


本文假設您已經熟悉建立 Azure AD 租用戶單一應用程式。  如果您不是，不對備份到[開發人員指南首頁][AAD-Dev-Guide]並嘗試以下其中一個我們快速開始 ！

有四個簡單的步驟，將您的應用程式轉換成 Azure AD 租用戶多應用程式︰

1.  更新您的應用程式註冊為多租用戶
2.  更新您的程式碼將要求傳送給 /common 端點 
3.  更新您的程式碼來處理多個發行者值
4.  了解使用者和管理員同意並變更適當的程式碼

讓我們來看看每一個步驟的詳細資料。 您可以也直接跳到[多租用戶範例這份清單][AAD-Samples-MT]。

## <a name="update-registration-to-be-multi-tenant"></a>更新為多租用戶的註冊
根據預設，web 應用程式/API 註冊 Azure AD 中的是單一租用戶。  您可以讓您的註冊多租用戶[Azure 傳統入口網站]中，尋找您的應用程式註冊的 [設定] 頁面上的 「 應用程式是多租用戶 」 切換[AZURE-classic-portal]並將其設定為 [是]。

注意︰ 應用程式進行多租用戶之前，Azure AD 需要應用程式識別碼 URI 全域唯一的應用程式。 應用程式識別碼 URI 是一種方式將應用程式識別通訊協定訊息中。  單一租用戶應用程式，則該租用戶中是唯一的應用程式識別碼 URI 滿足所需。  多租用戶應用程式，它必須是全域唯一讓 Azure AD 可以所有租用戶中尋找應用程式。  要求應用程式識別碼 URI 有符合 Azure AD 租用戶驗證的網域的主機名稱強制執行全域的唯一性。  比方說，如果您的租用戶的名稱為 contoso.onmicrosoft.com 然後有效想為應用程式識別碼 URI `https://contoso.onmicrosoft.com/myapp`。  如果您的租用戶已驗證的網域的`contoso.com`，也就是有效的應用程式識別碼 URI `https://contoso.com/myapp`。  如果應用程式識別碼 URI 不遵循此模式，就無法將應用程式設定成多租用戶。

原生的用戶端登錄是預設的多租用戶。  您不需要採取任何動作，讓原生用戶端應用程式註冊多租用戶。

## <a name="update-your-code-to-send-requests-to-common"></a>更新您的程式碼將要求傳送給 /common
在單一租用戶應用程式中，登入要求會傳送到的租用戶的登入結束點。   例如，contoso.onmicrosoft.com 的端點會︰

    https://login.microsoftonline.com/contoso.onmicrosoft.com

傳送到租用戶的端點要求可以登入使用者 （或來賓），該租用戶中的應用程式的租用戶中。  多租用戶應用程式中，應用程式不知道預付使用者時，哪些租用戶，您無法傳送邀請給租用戶的結束點。  不過，邀請會傳送至端點 multiplexes 過所有 Azure AD 租用戶︰

    https://login.microsoftonline.com/common

Azure AD 時收到要求的 /common 結束點，並登入的使用者，且因此探索使用者是來自哪一個租用戶。  常見的端點搭配所有 Azure AD 支援的驗證通訊協定 /: OpenID 連線、 OAuth 2.0、 SAML 2.0 和 WS 同盟。

登入應用程式，然後回應包含代表使用者的權杖。  權杖中的發行者] 值會告訴應用程式使用者是從何種租用戶。  當回應傳回 /common 端點，請在權杖簽發者值會對應到使用者的租用戶。  請務必注意 /common 端點不租用戶，而不是將發行者，只要工。  使用 /common，以驗證權杖應用程式中的邏輯需要更新為考慮這。 

如先前所述，多租用戶應用程式應該也會提供給使用者，追蹤商標指導方針 Azure AD 應用程式一致的登入體驗。 按一下下方若要進一步瞭解商標應用程式] 按鈕。

[![登入] 按鈕][AAD-登入]][AAD-App-Branding]

讓我們來看一下 /common 使用端點和您的程式碼中實作更多詳細資料。

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>更新您的程式碼來處理多個發行者值
Web 應用程式與網路 Api 收發及驗證的權杖從 Azure AD。  

> [AZURE.NOTE] 原生的用戶端應用程式要求，並從 Azure AD 接收權杖，他們可以執行，傳送給 Api，已驗證的位置。  原生應用程式不會驗證的權杖，而且必須將其視為不透明。

讓我們來看它從 Azure AD 在應用程式如何驗證權杖接收。  單一租用戶應用程式通常會採取端點值類似︰

    https://login.microsoftonline.com/contoso.onmicrosoft.com

並使用它來建構中繼資料 URL （在此例 OpenID 連線），例如︰

    https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration

若要下載兩重要部分資訊會用於驗證的權杖︰ 租用戶的簽章，索引鍵和發行者] 值。  每個 Azure AD 租用戶具有表單的唯一的發行者] 值︰

    https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/

其中 GUID 值是租用戶的租用戶識別碼的安全重新命名的版本。  如果您按一下上方的中繼資料連結`contoso.onmicrosoft.com`，您可以看到文件中的此發行者] 值。

時的單一租用戶應用程式驗證的權杖，它會檢查簽章的簽章的金鑰從中繼資料文件中，針對權杖，並可確保權杖中的發行者] 值相符的中繼資料文件中找不到。

自 /common 端點不會對應到租用戶，並檢查簽發者中值的中繼資料/一般有樣板的 URL，而非實際的值時，無法使用發行者︰

    https://sts.windows.net/{tenantid}/

因此，多租用戶應用程式時，無法只要相符的中繼資料中的發行者] 值驗證權杖`issuer`權杖中的值。  根據租用戶的發行者] 值的識別碼部分，必須決定哪些發行者值是有效的哪些不是邏輯多租用戶應用程式。  

例如，如果多租用戶應用程式只允許登入，從特定的租用戶已註冊的服務，然後它必須核取發行者] 值或`tid`宣告權杖，請確定該租用戶處於他們的訂閱清單中的值。  如果多租用戶應用程式只能處理個人，而且沒有任何 access 根據做出決策租用戶，然後將其可以略過] 發行者值完全。

您可以在本文結尾的[相關內容](#related-content)] 區段中找到多租用戶範例，簽發者驗證會停用啟用登入任何 Azure AD 租用戶。

現在讓我們來看看多租用戶應用程式登入的使用者的使用者體驗。

## <a name="understanding-user-and-admin-consent"></a>了解使用者與管理員同意
若要登入應用程式中 Azure AD 使用者，應用程式必須表示在使用者的租用戶。  這個選項可讓組織執行的動作包括唯一的原則套用時的使用者，使其租用戶登入應用程式。  單一租用戶應用程式的此登錄很簡單。當您註冊[Azure 傳統入口網站]中的應用程式，有一個[AZURE-classic-portal]。

多租用戶應用程式，應用程式的初始登錄放在使用 [開發人員 Azure AD 租用戶中。  當使用者從不同的租用戶的應用程式第一次登入時，系統會詢問 Azure AD 即可同意要求應用程式的權限。  如果他們同意，然後呼叫*服務主要*的應用程式的表示法會建立在使用者的租用戶，，並可以繼續登入。 委派也會建立的記錄至應用程式的使用者同意目錄中。 請參閱[應用程式與服務主要物件][AAD-App-SP-Objects]的應用程式的應用程式和 ServicePrincipal 物件，以及如何彼此彼此的詳細資訊。

![同意單層應用程式][Consent-Single-Tier] 

要求應用程式的權限會影響此同意體驗。  Azure AD 支援兩種類型的權限，僅供應用程式和委派︰

- 委派的權限授與可作為使用者登入子集的項目使用者可以執行的應用程式。  例如，您可以授與應用程式讀取登入使用者的行事曆的委派權限。
- 僅供應用程式的權限是授與直接應用程式的身分識別。  例如，您可以應用程式僅供讀取使用者的租用戶，清單的權限授與應用程式和會執行這項操作，無論人登入應用程式。

由一般的使用者，可以部分的權限同意至，而有些則需要租用戶系統管理員的同意。 

### <a name="admin-consent"></a>管理員同意
僅應用程式的權限永遠需要租用戶系統管理員的同意。  如果您的應用程式要求僅應用程式的權限的標準使用者嘗試登入應用程式，您的應用程式就會收到下列錯誤訊息︰ 使用者無法同意。

特定的委派權限，也需要租用戶系統管理員的同意。  例如，可寫入 Azure AD 以登入的使用者需要租用戶系統管理員的同意。  僅應用程式的權限，例如如果一般使用者嘗試登入應用程式要求需要系統管理員同意的委派權限的應用程式會收到錯誤。  需要權限管理同意由發佈資源的開發人員，並可資源的文件中。  Azure AD 圖形 API 及 Microsoft Graph API 可用的權限的說明主題連結是在本文的[相關內容](#related-content)] 區段中。

如果您的應用程式使用需要管理員同意的權限，必須先在您的應用程式，例如按鈕或管理員可以啟動巨集指令的位置的連結有筆勢。  此動作是主要 OAuth2/OpenID 連線的授權要求，但也包含，傳送給您的應用程式要求`prompt=admin_consent`查詢字串參數。  一旦管理員具有同意服務主要會建立客戶的租用戶中，不需要後續的登入要求`prompt=admin_consent`參數。   因為系統管理員決定要求的權限可接受，會提示不您租用戶中的任何其他使用者同意從插入點。

`prompt=admin_consent`參數也可讓應用程式要求不需要管理員同意，但要授與位置租用戶管理員 」 註冊 」 體驗的權限的應用程式一次，並沒有其他系統會提示使用者同意從該點上。

如果應用程式需要系統管理員同意，而且管理員身分登入應用程式，但`prompt=admin_consent`不會傳送參數，管理員都能成功同意應用程式，但它們會只同意其使用者帳戶。  一般的使用者將仍無法登入，並同意應用程式。  這是如果您要授與租用戶系統管理員以探索之前允許其他使用者存取您的應用程式的能力。

租用戶系統管理員可以停用使用者同意應用程式的能力。  如果這項功能已停用，管理員同意所需的應用程式設定租用戶中。  如果您想要使用一般使用者同意停用來測試您的應用程式，您可以設定參數中找到 Azure AD 租用戶組態] 區段中的[Azure 傳統入口網站][AZURE-classic-portal]。

> [AZURE.NOTE] 部分應用程式，想要的一般使用者能夠同意一開始，而應用程式可以稍後涉及管理員] 和 [邀請的權限要求管理員同意體驗。  有不進行此單一應用程式註冊 Azure AD 今天。  即將 Azure AD v2 結束點可讓而不是在執行階段，要求權限的應用程式次註冊，讓這種情況。  如需詳細資訊，請參閱[Azure AD 應用程式模型 v2 開發人員指南][AAD-V2-Dev-Guide]。

### <a name="consent-and-multi-tier-applications"></a>同意和多層應用程式
您的應用程式可能會有多個層級，分別表示自己登錄中 Azure AD。  例如，通話 web API 的原生應用程式或 web 應用程式的通話網路 API。  在這兩種情況下，用戶端 （原生應用程式或 web 應用程式） 會要求撥打資源 (web API) 的權限。  用戶端可以順利同意到客戶租用戶，必須已經存在的要求權限的所有資源客戶的租用戶中。  如果這種情況不符合，Azure AD 會傳回錯誤，必須先新增資源。

如果邏輯應用程式中包含的兩個或多個應用程式進行之註冊，例如另一個用戶端和資源，這可能是問題。  如何讓資源到客戶租用戶的第一個？  Azure AD 說明這種情況下，讓用戶端和資源同意在單一步驟中，使用者看到要求用戶端和資源在同意書頁面上的權限的總和的位置。  若要啟用此行為，資源的應用程式登錄必須包含於用戶端應用程式識別碼為`knownClientApplications`在其應用程式資訊清單。  例如︰

    knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]

可以更新此屬性，透過資源[應用程式的資訊清單][AAD-App-Manifest]，及多層的原生用戶端，呼叫本文結尾的[相關內容](#related-content)] 區段中的網路 API 範例所示。 下圖會提供以下概觀同意多層應用程式︰

![同意多層已知的用戶端應用程式][Consent-Multi-Tier-Known-Client] 

在不同的租用戶中登錄應用程式的各層情況類似的大小寫。  例如，請考慮建立打電話給 Office 365 Exchange Online API 的原生的用戶端應用程式的大小寫。  若要開發的原生應用程式，及客戶的租用戶中執行原生應用程式的更新版本，Exchange Online 服務主要必須要有。  在此情況下客戶必須購買服務主體會在其租用戶中建立的 [Exchange Online。  若是 Microsoft 以外組織內建的 API，必須提供的客戶同意到客戶租用戶，例如網頁的磁碟機使用本文所述的機制同意其應用程式的 api 的開發人員。  服務主要租用戶中建立後，原生應用程式可以取得 API 的權杖。

下圖會提供以下概觀同意在不同的租用戶中登錄多層應用程式︰

![同意多層多方應用程式][Consent-Multi-Tier-Multi-Party] 

### <a name="revoking-consent"></a>撤銷同意
使用者與系統管理員可以撤銷同意隨時應用程式︰

- 使用者移除它們從其[存取面板應用程式]撤銷個別的應用程式存取[AAD-Access-Panel]清單。
- 系統管理員撤銷存取應用程式從 Azure AD 使用 Azure AD 管理章節[Azure 傳統入口網站]中移除它們[AZURE-classic-portal]。

如果系統管理員 consents 租用戶中的所有使用者的應用程式，使用者就無法個別撤銷存取權。  只系統管理員可以撤銷存取權，並僅適用於整個應用程式。

### <a name="consent-and-protocol-support"></a>同意與通訊協定支援
Azure AD OAuth，OpenID 連線，透過支援同意 WS 同盟和 SAML 通訊協定。  不支援 SAML 和 WS 同盟通訊協定`prompt=admin_consent`參數，讓管理員同意，才可以透過 OAuth 與 OpenID 連線。

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>多租用戶應用程式和快取存取權杖
多租用戶應用程式，也可以取得撥打受到 Azure AD Api 的存取權杖。  常見的錯誤時多租用戶應用程式中使用 Active Directory 驗證文件庫 (ADAL) 一開始要求使用 /common，為使用者的權杖獲得回應，，然後要求的後續的權杖，也使用 /common 的相同的使用者。  由於從 Azure AD 回應不會從承租人，/快取的常見，ADAL 為的租用戶的 token。 取得使用者存取權杖 /common 後續通話遺漏快取項目，並再次登入會提示使用者。  若要避免遺失快取，請確定已登入使用者的後續通話對租用戶的結束點。

## <a name="related-content"></a>相關的內容

- [多租用戶應用程式範例][AAD-Samples-MT]
- [應用程式的商標指導方針][AAD-App-Branding]
- [Azure AD 開發人員指南][AAD-Dev-Guide]
- [應用程式與服務主要物件][AAD-App-SP-Objects]
- [整合應用程式與 Azure Active Directory][AAD-Integrating-Apps]
- [同意架構的概觀][AAD-Consent-Overview]
- [Microsoft Graph API 權限範圍][MSFT-Graph-AAD]
- [Azure AD 圖形 API 權限範圍][AAD-Graph-Perm-Scopes]

請若要提供意見反應，並協助我們精簡和圖案內容，使用 Disqus 註解] 區段下方。

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]: ./active-directory-branding-guidelines.md
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Consent-Overview]: ./active-directory-integrating-applications.md#overview-of-the-consent-framework
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Overview]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-graph-api/
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Samples-MT]: https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multitenant
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-classic-portal]: https://manage.windowsazure.com
[MSFT-Graph-AAD]: https://graph.microsoft.io/en-us/docs/authorization/permission_scopes

<!--Image references-->
[AAD-Sign-In]: ./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png
[Consent-Single-Tier]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-single-tier.png
[Consent-Multi-Tier-Known-Client]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-known-clients.png
[Consent-Multi-Tier-Multi-Party]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-multi-party.png

<!--Reference style links -->
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]: ./active-directory-token-and-claims.md
[AAD-V2-Dev-Guide]: ./active-directory-appmodel-v2-overview.md
[AZURE-classic-portal]: https://manage.windowsazure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[O365-Perm-Ref]: https://msdn.microsoft.com/en-us/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Code-Grant-Flow]: https://msdn.microsoft.com/library/azure/dn645542.aspx
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3 
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: http://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-ID-Token]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken














