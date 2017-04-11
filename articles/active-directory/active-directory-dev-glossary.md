<properties
   pageTitle="Azure Active Directory 開發人員詞彙 |Microsoft Azure"
   description="常用的 Azure Active Directory 開發人員概念及功能的字詞的清單。"
   services="active-directory"
   documentationCenter=""
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/31/2016"
   ms.author="bryanla"/>

# <a name="azure-active-directory-developer-glossary"></a>Azure Active Directory 開發人員詞彙
本文包含定義的一些核心 Azure Active Directory (AD) 開發人員概念，瞭解 Azure AD 開發應用程式時，會很有幫助。

## <a name="access-token"></a>存取權杖
使用[用戶端應用程式](#client-application)才能存取[受保護的伺服器資源](#resource-server)和[授權伺服器](#authorization-server)，發出[安全性 token](#security-token)類型。 通常在表單中的[JSON Web Token (JWT)][JWT]，權杖意由[資源擁有者](#resource-owner)，要求層級的存取權授與用戶端的授權。 權杖包含所有適用[宣告](#claim)關於主旨，讓使用表單的認證以存取指定的資源時用戶端應用程式。 這也不需要的資源擁有者，公開至用戶端的認證。

存取權杖有時也稱為 「 使用者 + 應用程式 」 或 「 應用程式僅供 」，根據表示的認證。 例如，當用戶端應用程式使用:

- 使用者[授與 「 授權程式碼 」 授權](#authorization-grant)，驗證第一次為資源擁有人，委派存取資源用戶端的授權。 取得存取權杖時，用戶端驗證之後。 權杖可以有時候稱為更明確地說 「 使用者 + 應用程式 」 權杖，為它代表兩個使用者的授權的用戶端應用程式，與應用程式。
- [授與 「 用戶端認證 」 授權](#authorization-grant)，用戶端提供對於驗證、 運作資源擁有者的驗證或授權，不讓權杖可以有時候稱為 「 應用程式專用 」 的權杖。

請參閱[Azure AD 權杖參照][AAD-Tokens-Claims]如需詳細資訊。

## <a name="application-manifest"></a>應用程式資訊清單  
[Azure 傳統入口網站]提供的功能[AZURE-classic-portal]，而產生的應用程式的身分識別設定，用來做為更新其相關聯的[應用程式]的機制 JSON 表示[AAD-Graph-App-Entity]和[ServicePrincipal] [AAD-Graph-Sp-Entity]項目。 請參閱[瞭解 Azure Active Directory 應用程式顯示][AAD-App-Manifest]如需詳細資訊。

## <a name="application-object"></a>應用程式物件  
當您註冊/更新[Azure 傳統入口網站]中的應用程式[AZURE-classic-portal]，入口網站建立或更新的應用程式物件和對應的[服務主體物件](#service-principal-object)該租用戶。 應用程式的物件*定義*應用程式的身分識別的設定全域 （所有租用戶，有權存取的位置），提供從其對應的服務主要的物件是*衍生*使用本機在執行階段 （在特定的租用戶） 的範本。

請參閱[應用程式與服務主要物件][AAD-App-SP-Objects]如需詳細資訊。

## <a name="application-registration"></a>應用程式註冊  
若要允許應用程式以整合及代理人身分識別與管理存取 Azure AD 函數，必須先註冊與 Azure AD[租用戶](#tenant)。 當您與 Azure AD 註冊您的應用程式時，您提供身分識別設定您的應用程式，讓它與 Azure AD 整合，以及使用功能，例如︰

- 強大的單一登入使用 Azure AD 身分識別管理和[OpenID 連線]的管理[OpenIDConnect]通訊協定實作
- 仲介的存取權的[用戶端應用程式](#client-application)，透過 Azure AD OAuth 2.0[授權伺服器](#authorization-server)實作的 [[受保護的資源](#resource-server)
- [同意架構](#consent)管理用戶端存取受保護的資源，根據資源擁有授權。

請參閱[拷貝應用程式與 Azure Active Directory] [AAD-Integrating-Apps]如需詳細資訊。

## <a name="authentication"></a>驗證
挑戰派正確的認證，提供基礎來建立用於身分識別和存取控制安全性原則的動作。 例如，期間[OAuth2 授權授與](#authorization-grant)驗證的合作對象填寫[資源擁有者](#resource-owner)或[用戶端應用程式](#client-application)，根據使用授與的角色。

## <a name="authorization"></a>授權
授與進行操作經過驗證的安全性主體權限的動作。 Azure AD 程式設計模型中有兩個主要的使用案例︰

- [授與 OAuth2 授權](#authorization-grant)的流程期間︰ 當[資源擁有者](#resource-owner)授與[用戶端應用程式](#client-application)的授權，讓 [用戶端存取資源擁有者的資源。
- 在用戶端資源存取︰ 實作[資源伺服器](#resource-server)時，使用[宣告](#claim)值展示[存取權杖](#access-token)，讓存取控制的決策根據這些中。

## <a name="authorization-code"></a>授權程式碼
簡短生活將會變 」 token 」 所提供給[用戶端應用程式](#client-application)[授權端點](#authorization-endpoint)，屬於 「 授權程式碼 」 流程，其中四個 OAuth2[授與授權](#authorization-grant)。 程式碼會傳回驗證的[資源擁有者](#resource-owner)，指出資源擁有者具有委派授權，以存取要求的資源來回應用戶端應用程式。 流程的一部分，程式碼是稍後弄丟[存取權杖](#access-token)。

## <a name="authorization-endpoint"></a>授權端點
實作[授權伺服器](#authorization-server)，才能提供[授權授與](#authorization-grant)OAuth2 授權期間用來與[資源的擁有者](#resource-owner)互動的結束點的其中一個授與流程。 使用授權授與流量，根據提供實際授與而有所不同，包括[授權程式碼](#authorization-code)或[安全性 token](#security-token)。

請參閱 OAuth2 規格的[授權授與類型][OAuth2-AuthZ-Grant-Types]和[授權端點][OAuth2-AuthZ-Endpoint]節，以及[OpenIDConnect 規格][OpenIDConnect-AuthZ-Endpoint]如需詳細資訊。

## <a name="authorization-grant"></a>授權授與
代表[資源擁有者的](#resource-owner)[授權](#authorization)，以存取其受保護的資源，授與[用戶端應用程式](#client-application)的認證。 用戶端應用程式可以使用[四種授與類型所定義的 OAuth2 授權架構]的其中一個[OAuth2-AuthZ-Grant-Types]以取得授與，根據用戶端類型需求: 「 授權的程式碼授與 」、 「 用戶端憑證授與 」、 「 隱含授與]，及 「 資源擁有者密碼認證授與 「。 傳回用戶端認證為[存取權杖](#access-token)或以[授權程式碼](#authorization-code)（交換稍後存取權杖），根據使用的授權授與的類型。

## <a name="authorization-server"></a>授權伺服器
所定義的[OAuth2 授權架構][OAuth2-Role-Def]，[用戶端](#client-application)權杖之後順利驗證[資源擁有者](#resource-owner)，並取得其授權的負責核發存取的伺服器。 [用戶端應用程式](#client-application)進行互動在執行階段透過其[授權](#authorization-endpoint)的授權伺服器， [token](#token-endpoint)端點，根據 OAuth2 的定義[授權授與](#authorization-grant)。

Azure AD 應用程式整合，若是 Azure AD 實作 Azure AD 應用程式與 Microsoft 服務的 Api，例如[Microsoft Graph Api]的授權伺服器角色[Microsoft-Graph]。

## <a name="claim"></a>宣告
[安全性權杖](#security-token)包含宣告，提供判斷提示關於實體 （如[用戶端應用程式](#client-application)或[資源擁有者](#resource-owner)） 至另一個實體 （例如[資源伺服器](#resource-server)）。 宣告是轉送有關 token 主旨 （例如，安全性原則[授權伺服器](#authorization-server)已驗證） 的名稱/值組。 宣告中指定的權杖會取決於數個變數，包括權杖，用來驗證主旨、 應用程式設定等認證的類型的類型。

請參閱[Azure AD token 參考][AAD-Tokens-Claims]如需詳細資訊。

## <a name="client-application"></a>用戶端應用程式  
所定義的[OAuth2 授權架構][OAuth2-Role-Def]，可讓您的應用程式受保護的資源要求[資源擁有者](#resource-owner)。 「 用戶端 」 一詞不表示特定的硬體實作特徵 （例如，是否應用程式執行伺服器、 桌面或其他裝置上）。  

用戶端應用程式要求[授權](#authorization)的資源擁有者，若要參與[OAuth2 授權授與](#authorization-grant)的流程，並可能資源擁有者的代表存取 Api/資料。 [定義兩種類型的用戶端]OAuth2 授權架構[OAuth2-Client-Types]「 機密 」 和 「 公用 」、 根據維護其認證的機密的用戶端的能力。 應用程式可以實作[網頁用戶端 （機密）](#web-client)執行於網頁伺服器，安裝在裝置或[（公開）] 的 [使用者代理程式用戶端](#user-agent-based-client)裝置的瀏覽器中執行[原生用戶端 （公開）]](#native-client) 。

## <a name="consent"></a>同意
授與[用戶端應用程式](#client-application)，來存取受保護的資源，代表資源擁有者的特定[權限](#permissions)的授權[資源擁有者](#resource-owner)的程序。 根據用戶端要求的權限，系統管理員或使用者會要求您同意分別允許其組織/個別資料的存取權。 請注意，在[多租用戶](#multi-tenant-application)案例中，應用程式的[主要的服務](#service-principal-object)，也會 consenting 使用者的租用戶中記錄。

## <a name="id-token"></a>識別碼權杖
[OpenID 連線][OpenIDConnect-ID-Token]提供的[授權伺服器的](#authorization-server)[授權端點](#authorization-endpoint)，其中包含[宣告](#claim)相關的使用者[資源擁有者](#resource-owner)驗證[安全性 token](#security-token) 。 存取權杖，例如 ID 權杖也會以表示數位簽章的[JSON Web Token (JWT)][JWT]。 不同於存取權杖，識別碼權杖的宣告不會用於存取資源相關的目的與特別存取控制。

請參閱[Azure AD token 參考][AAD-Tokens-Claims]如需詳細資訊。

## <a name="multi-tenant-application"></a>多租用戶應用程式
[用戶端應用程式](#client-application)，可讓的課程登入並[同意](#consent)使用者佈建在任何 Azure AD[租用戶](#tenant)，包括租用戶，而不在哪裡註冊的用戶端。 相反地，應用程式註冊為單一租用戶，只會讓登增益集佈建在為相同的租用戶中的使用者帳戶註冊應用程式的位置。 [原生的用戶端](#native-client)應用程式會根據預設，多租用戶，而[網頁用戶端](#web-client)應用程式能夠選取單一或多個租用戶。

請參閱[如何登入使用多租用戶應用程式模式任何 Azure AD 使用者][AAD-Multi-Tenant-Overview]如需詳細資訊。

## <a name="native-client"></a>原生的用戶端
[用戶端應用程式](#client-application)的裝置上已安裝原本的類型。 由於在裝置上執行所有的程式碼，它會被視為 「 公用 」 的用戶端，因為其無法儲存認證私下/保密。 請參閱[OAuth2 用戶端類型與設定檔][OAuth2-Client-Types]如需詳細資訊。

## <a name="permissions"></a>權限
[用戶端應用程式](#client-application)會取得宣告權限要求存取[資源伺服器](#resource-server)。 有兩種類型︰

- 「 委派 」 權限]，要求下[以範圍為基礎](#scopes)的存取委派的登入[資源擁有](#resource-owner)的授權，會在執行階段的資源顯示為[「 scp 」 宣告](#claim)在用戶端[存取權杖](#access-token)。
- 在執行階段資源呈現 」 應用程式 」 權限，要求在用戶端應用程式的認證身分識別的[角色型](#roles)存取，請為 [在用戶端存取權杖中的 [[宣告角色]](#claim) 。

他們也運用在[同意](#consent)過程中，讓系統管理員或資源擁有者有機會授與/拒絕用戶端存取他們的租用戶中的資源。

在 「 應用程式] 上設定權限要求 / [Azure 傳統入口網站]中 「 設定 」] 索引標籤[AZURE-classic-portal]，在 [權限給其他應用程式]，以選取所要 [委派的權限] 和 [應用程式權限] （後者需要中的全域管理員角色的成員資格） 底下。 [公用用戶端](#client-application)無法維持認證，因為它只能要求委派的權限，而[機密的用戶端](#client-application)可邀請兩個委派和應用程式權限的能力。 用戶端[應用程式物件](#application-object)在其[requiredResourceAccess 屬性]中儲存的宣告的權限[AAD-Graph-App-Entity]。

## <a name="resource-owner"></a>資源擁有者
所定義的[OAuth2 授權架構][OAuth2-Role-Def]，可以授與存取權給受保護的資源的實體。 人員資源擁有者時，它被指使用者。 例如，當[用戶端應用程式](#client-application)想要透過[Microsoft Graph API]存取使用者信箱[Microsoft-Graph]，需要資源擁有者的信箱的權限。

## <a name="resource-server"></a>資源伺服器
所定義的[OAuth2 授權架構][OAuth2-Role-Def]，伺服器主機受保護的資源，可接受和回應受呈現[存取權杖](#access-token)的[用戶端應用程式](#client-application)中的資源要求。 也稱為受保護的資源伺服器或資源應用程式。

資源伺服器會公開 Api，並會強制執行存取其受保護的資源，透過[範圍](#scopes)和[角色](#roles)，使用 OAuth 2.0 授權架構。 範例包括 Azure AD 圖形 API 提供存取 Azure AD 租用戶資料，以及 Office 365 Api 提供存取資料，例如郵件和行事曆。 這些都是也可透過[Microsoft Graph API]存取[Microsoft-Graph]。  

就像用戶端應用程式中，資源應用程式的身分識別設定會建立透過[註冊](#application-registration)Azure AD 租用戶，提供應用程式和服務的主體物件中。 某些 Microsoft 提供的 Api，例如 Azure AD 圖形 API，有預先註冊服務主體提供時提供所有租用戶中。

## <a name="roles"></a>角色
[範圍](#scopes)，例如角色提供來控制存取受保護的資源的[資源伺服器](#resource-server)的方式。 有兩種類型: 「 使用者 」 角色會執行時的 「 應用程式 」 角色實作相同的[用戶端應用程式](#client-application)需要存取要求的資源，存取權的使用者/群組的角色型存取控制。

角色是資源定義的字串 (例如 「 費用核准者]，「 唯讀 」、 「 Directory.ReadWrite.All 」) [Azure 傳統入口網站]中受管理[AZURE-classic-portal]透過資源的[應用程式資訊清單](#application-manifest)，及儲存的資源的[appRoles 屬性][AAD-Graph-Sp-Entity]。 Azure 傳統入口網站也會使用 「 使用者 」 角色指派使用者及設定用戶端[應用程式權限](#permissions)存取 」 應用程式 」 角色。

Azure AD 圖形 API 公開的應用程式角色的詳細說明，請參閱[圖形 API 權限範圍][AAD-Graph-Perm-Scopes]。 如需逐步實作範例，請參閱[角色存取控制在雲端應用程式中使用 Azure AD][Duyshant-Role-Blog]。

## <a name="scopes"></a>範圍
[角色](#roles)，例如範圍會提供來控制存取受保護的資源的[資源伺服器](#resource-server)的方式。 範圍用來實作[以範圍為基礎][OAuth2-Access-Token-Scopes]存取控制項，具有委派的存取資源的擁有者的[用戶端應用程式](#client-application)。

範圍是資源定義的字串 （例如 「 Mail.Read 」、 「 Directory.ReadWrite.All 」） 中[Azure 傳統入口網站]管理[AZURE-classic-portal]透過資源的[應用程式資訊清單](#application-manifest)，並儲存於資源的[oauth2Permissions 屬性][AAD-Graph-Sp-Entity]。 Azure 傳統入口網站也可用於設定用戶端應用程式的[委派權限](#permissions)來存取範圍。

最佳作法命名慣例，是使用 「 resource.operation.constraint 」 格式。 Azure AD 圖形 API 公開範圍的詳細說明，請參閱[圖形 API 權限範圍][AAD-Graph-Perm-Scopes]。 如 Office 365 服務所公開的範圍，請參閱[Office 365 API 權限參考][O365-Perm-Ref]。

## <a name="security-token"></a>安全性權杖
已簽署的文件，其中包含宣告，例如 OAuth2 權杖或 SAML 2.0 判斷提示。 對 OAuth2[授權授與](#authorization-grant)[存取權杖](#access-token)(OAuth2)，然後[權杖識別碼](OpenID Connect)類型的安全性權杖，這兩種實作[JSON Web Token (JWT)]為[JWT]。

## <a name="service-principal-object"></a>服務主體物件
當您註冊/更新[Azure 傳統入口網站]中的應用程式[AZURE-classic-portal]，入口網站建立或更新的相對應的服務主體的物件和[應用程式物件](#application-object)該租用戶。 應用程式的物件*定義*應用程式的身分識別設定全域 （在所有租用戶位置相關聯的應用程式已授與存取），而是從其對應的服務主要的物件是*衍生*用於本機在執行階段 （在特定的租用戶） 的範本。

請參閱[應用程式與服務主要物件][AAD-App-SP-Objects]如需詳細資訊。

## <a name="sign-in"></a>登入
[用戶端應用程式](#client-application)而起始使用者驗證及擷取相關的狀態，以取得[安全性 token](#security-token)和範圍設定的狀態的應用程式工作階段的程序。 包含的使用者設定檔資訊，例如成品及資訊衍生自 token 宣告狀態。

應用程式的登入函數通常用來實作單一登入 (SSO)。 它可能也會前面加上 「 註冊 」 的函數，為使用者取得的存取權 （在第一個登入） 應用程式的項目點。 註冊函數用來收集資料，並保留特定使用者的其他狀態，可能會要求[使用者同意](#consent)。

## <a name="sign-out"></a>教具借出
[用戶端應用程式](#client-application)工作階段期間[登入](#sign-in)與相關聯的程序取消驗證的使用者，中斷使用者狀態

## <a name="tenant"></a>租用戶
Azure AD 目錄的執行個體被指 Azure AD 租用戶。 它提供各種不同的功能，包括︰

- 登錄服務整合式應用程式
- 驗證的使用者帳戶和已註冊的應用程式
- 支援各種不同的通訊協定包括 OAuth2 和 SAML，包括[授權端點](#authorization-endpoint)、 [token 端點](#token-endpoint)和使用[多租用戶應用程式](#multi-tenant-application)的 「 一般 」 結束點所需的其餘部分結束點。

租用戶也會與 Azure AD 相關聯或期間佈建的訂閱，身分識別與存取管理功能提供訂閱的 Office 365 訂閱。 瞭解[如何上手的 Azure Active Directory 租用戶][AAD-How-To-Tenant]的各種方式的詳細資料您可以存取的租用戶。 查看[如何 Azure 訂閱相關聯的 Azure Active Directory] [AAD-How-Subscriptions-Assoc]的訂閱和 Azure AD 租用戶之間的關係的詳細資訊。

## <a name="token-endpoint"></a>權杖端點
下列其中一項[授權伺服器](#authorization-server)支援 OAuth2[授權授與](#authorization-grant)實作結束點。 授與，根據其可用來擷取[存取權杖](#access-token)（和相關的 「 重新整理 」 token）[用戶端](#client-application)，或[識別碼權杖](#ID-token)搭配[OpenID 連線]時[OpenIDConnect]通訊協定。

## <a name="user-agent-based-client"></a>使用者代理程式為基礎的用戶端
[用戶端應用程式](#client-application)下載的程式碼的網頁伺服器，並執行內使用者代理程式 （例如，網頁瀏覽器中），例如單一頁面應用程式] 選項的類型。 由於在裝置上執行所有的程式碼，它會被視為 「 公用 」 的用戶端，因為其無法儲存認證私下/保密。 請參閱[OAuth2 用戶端類型與設定檔][OAuth2-Client-Types]如需詳細資訊。

## <a name="user-principal"></a>使用者主體
服務主體物件用來代表應用程式執行個體的方式很類似，使用者主體物件是安全性的其他類型原則，這代表使用者。 Azure AD 圖形[使用者實體][AAD-Graph-User-Entity]定義的結構描述使用者物件，包括使用者的相關內容，例如名字和姓氏、 使用者主要名稱、 目錄角色的成員資格等等。這會提供建立使用者主體在執行階段 Azure AD 的使用者身分識別設定。 使用者主體用來代表已驗證的使用者的單一登入，錄製[同意](#consent)委派、 進行存取控制的決策等。

## <a name="web-client"></a>網頁用戶端
[用戶端應用程式](#client-application)的網頁伺服器，並可執行所有的程式碼，成為安全地將其認證儲存在伺服器上的 「 機密 」 的用戶端類型。 請參閱[OAuth2 用戶端類型與設定檔][OAuth2-Client-Types]如需詳細資訊。

## <a name="next-steps"></a>後續步驟
[Azure AD 開發人員指南][AAD-Dev-Guide]是使用於所有 Azure AD 開發入口網站相關主題，包括的[應用程式整合]概觀[AAD-How-To-Integrate]和[Azure AD 驗證與支援的驗證案例]的基本概念[AAD-Auth-Scenarios]。

請若要提供意見反應，並協助我們精簡和圖案內容，使用下列 Disqus 註解] 區段。

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-Subscriptions-Assoc]: ./active-directory-how-subscriptions-associated-directory.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-How-To-Tenant]: active-directory-howto-tenant.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Multi-Tenant-Overview]: active-directory-devhowto-multi-tenant-overview.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]: ./active-directory-token-and-claims.md
[AZURE-classic-portal]: https://manage.windowsazure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[Microsoft-Graph]: https://graph.microsoft.io
[O365-Perm-Ref]: https://msdn.microsoft.com/en-us/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Endpoint]: https://tools.ietf.org/html/rfc6749#section-3.1
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: http://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-AuthZ-Endpoint]: http://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint
[OpenIDConnect-ID-Token]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken
