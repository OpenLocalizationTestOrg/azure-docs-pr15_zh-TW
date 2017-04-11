<properties
    pageTitle="Azure API 管理常見問題集 |Microsoft Azure"
    description="瞭解一般問題、 模式和 Azure API 管理的最佳作法的答案。"
    services="api-management"
    documentationCenter=""
    authors="miaojiang"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="mijiang"/>

# <a name="azure-api-management-faqs"></a>Azure API 管理常見問題集

取得 Azure API 管理中的一般問題、 圖樣及最佳作法的答案。

## <a name="frequently-asked-questions"></a>常見問題集

-   [如何可以我 Microsoft Azure API 管理小組中詢問問題？](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question)
-   [預覽功能時意義為何？](#what-does-it-mean-when-a-feature-is-in-preview)
-   [我要如何保護的 API 管理閘道器與我的後端服務之間的連線？](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
-   [如何將我的 API 管理服務執行個體複製到新的執行個體？](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance)
-   [我可以以程式設計方式管理我的 API 管理執行個體？](#can-i-manage-my-api-management-instance-programmatically)
-   [如何新增至管理員群組的使用者？](#how-do-i-add-a-user-to-the-administrators-group)
-   [為何我想要新增原則編輯器中無法使用的原則？](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
-   [如何使用 API 管理的 API 版本設定？](#how-do-i-use-api-versioning-in-api-management)
-   [如何設定在單一 API 的多個環境？](#how-do-i-set-up-multiple-environments-in-a-single-api)
-   [可以使用 SOAP API 管理嗎？](#can-i-use-soap-with-api-management)
-   [是的 API 管理閘道器 IP 位址常數嗎？我可以將其中使用防火牆規則？](#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules)
-   [可以使用 AD FS 安全性設定的 OAuth 2.0 授權伺服器？](#can-i-configure-an-oauth-20-authorization-server-with-adfs-security)
-   [部署到多個地理位置中將 API 管理使用在哪些路由的方式？](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
-   [可以使用 Azure 資源管理員範本建立 API 管理服務執行個體嗎？](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
-   [我可以使用後端自我簽署的 SSL 憑證嗎？](#can-i-use-a-self-signed-ssl-certificate-for-a-back-end)
-   [為什麼當我嘗試複製給存放庫取得驗證失敗？](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
-   [是否能使用的 Azure ExpressRoute API 管理？](#does-api-management-work-with-azure-expressroute)
-   [我可以移動 API 管理服務從一個訂閱到另一個嗎？](#can-i-move-an-api-management-service-from-one-subscription-to-another)


### <a name="how-can-i-ask-the-microsoft-azure-api-management-team-a-question"></a>如何可以我 Microsoft Azure API 管理小組中詢問問題？

您可以使用這些選項的其中一個與我們連絡︰

-   我們[API 管理 MSDN 論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt)中張貼您的問題。
-   傳送電子郵件<apimgmt@microsoft.com>。
-   傳送給我們功能邀請中的[Azure 意見反應](https://feedback.azure.com/forums/248703-api-management)。

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>預覽功能時意義為何？

預覽功能時，這表示我們正在積極搜尋意見反應功能為您的使用方式。 在預覽中的功能功能上完成，但有可能是，我們要中斷，變更客戶意見反應來回應。 我們建議您不要依賴生產環境中的預覽中的功能。 如果您有任何意見反應預覽功能時，請讓我們知道透過 [連絡人] 選項的其中一個[如何可以我發問 Microsoft Azure API 管理小組？](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question)。

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>我要如何保護的 API 管理閘道器與我的後端服務之間的連線？

您有幾個選項的安全性的 API 管理閘道器與後端服務之間的連線。 您可以︰

-   使用 HTTP 基本驗證。 如需詳細資訊，請參閱[設定 API 設定](api-management-howto-create-apis.md#configure-api-settings)。
- 使用 SSL 相互驗證[如何保護使用 Azure API 管理中的用戶端憑證驗證的後端服務](api-management-howto-mutual-certificates.md)所述。
- 使用 IP 家您後端的服務。 如果您有標準或進階版的層 API 管理執行個體，閘道器的 IP 位址保持不變。 您可以設定您 whitelist 允許此 IP 位址。 您可以在 Azure 入口網站中的儀表板上取得您的 API 管理執行個體的 IP 位址。
- Azure 虛擬網路連線 API 管理執行個體。 如需詳細資訊，請參閱[如何設定在 Azure API 管理 VPN 連線](api-management-howto-setup-vpn.md)。

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>如何將我的 API 管理服務執行個體複製到新的執行個體？

如果您想要複製到新的執行個體的 API 管理執行個體，您會有幾個選項。 您可以︰

-   使用備份與還原 API 管理中的函數。 如需詳細資訊，請參閱[如何實作損毀修復使用服務備份和還原 Azure API 管理](api-management-howto-disaster-recovery-backup-restore.md)。
-   建立您自己的備份及還原使用[API 管理 REST API](https://msdn.microsoft.com/library/azure/dn776326.aspx)的功能。 您可以使用 REST API 來儲存並還原項目從您想要的服務執行個體。
-   使用給，下載服務設定，然後上傳至新的執行個體。 如需詳細資訊，請參閱[如何儲存及設定使用給您 API 管理服務設定](api-management-configuration-repository-git.md)。

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>我可以以程式設計方式管理我的 API 管理執行個體？

是的您可以使用以程式設計方式管理 API 管理︰

-   [API 管理 REST API](https://msdn.microsoft.com/library/azure/dn776326.aspx)。
-   [Microsoft Azure ApiManagement 服務管理文件庫 SDK](http://aka.ms/apimsdk)。
-   [服務部署](https://msdn.microsoft.com/library/mt619282.aspx)和[服務管理](https://msdn.microsoft.com/library/mt613507.aspx)PowerShell cmdlet。

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>如何新增至管理員群組的使用者？

以下說明如何新增使用者至管理員群組︰

1. [Azure 入口網站](https://portal.azure.com)登入。
2. 移至含有您想要更新的 API 管理執行個體的 [資源] 群組。
3. 在 API 管理**Api 管理參與者**角色指派給使用者。

現在，新加入的參與者可以使用 Azure PowerShell [cmdlet](https://msdn.microsoft.com/library/mt613507.aspx)。 以下是如何以系統管理員身分登入︰

1. 使用`Login-AzureRmAccount`cmdlet 來登入。
2. 將內容設定為 [已使用的服務訂閱`Set-AzureRmContext -SubscriptionID <subscriptionGUID>`。
3. 使用取得單一登入 URL `Get-AzureRmApiManagementSsoToken -ResourceGroupName <rgName> -Name <serviceName>`。
4. 使用 URL 存取管理入口網站。


### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>為何我想要新增原則編輯器中無法使用的原則？

如果您想要新增的原則灰色或原則編輯器] 中會加上陰影，請確定您是在原則的正確的範圍。 每個原則陳述式被專為您在特定範圍及原則區段中使用。 若要檢閱的原則節和原則的範圍，請參閱原則使用情況] 區段中[API 管理原則](https://msdn.microsoft.com/library/azure/dn894080.aspx)。


### <a name="how-do-i-use-api-versioning-in-api-management"></a>如何使用 API 管理的 API 版本設定？

您有要用於 API 管理的 API 版本設定幾個選項︰

-   在 API 管理] 中，您可以設定來代表不同版本的 Api。 例如，您可能必須兩個不同的 Api、 MyAPIv1 和 MyAPIv2。 開發人員可以選擇開發人員想要使用的版本。
-   您也可以設定您的 API，並不包含版本區段中，例如 https://my.api 服務 url。 然後，設定每一項作業[改寫 URL](https://msdn.microsoft.com/library/azure/dn894083.aspx#RewriteURL)範本上的版本區段。 例如，您可以讓作業 /resource] 和 [稱為 /v1/Resource[改寫 URL](api-management-howto-add-operations.md#rewrite-url-template)範本的[URL 範本](api-management-howto-add-operations.md#url-template)。 您可以變更版本的區段值，分別為每一項作業。
-   如果您想要保留 「 預設 」 版本區段中的 API 服務的 URL，在所選的作業，設定原則，若要變更的後端要求路徑中使用的[後端服務設定](https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBackendService)原則。

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>如何設定在單一 API 的多個環境？

若要設定多個環境，測試環境，生產環境中，在單一 API，您有兩個選項。 您可以︰

-   Host （主機) 不同的 Api 上相同的租用戶。
-   裝載相同的 Api 上不同的租用戶。

### <a name="can-i-use-soap-with-api-management"></a>可以使用 SOAP API 管理嗎？

[SOAP 通過](http://blogs.msdn.microsoft.com/apimanagement/2016/10/13/soap-pass-through/)現已支援。 系統管理員可以匯入的 SOAP 服務，並 Azure API 管理會建立 SOAP 前端。 開發人員入口網站文件、 測試主控台、 原則和分析] 會提供 SOAP 服務。

### <a name="is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules"></a>是的 API 管理閘道器 IP 位址常數嗎？ 我可以將其中使用防火牆規則？

在 [一般] 及 [進階版的層級，API 管理租用戶的公用 IP 位址 (VIP) 是靜態的租用戶，有一些例外狀況的存留時間。 在下列情況下 IP 位址變更︰

-   刪除及重新建立的服務。
-   服務訂閱 （例如，適用於 nonpayment) 暫停，而然後恢復。
-   新增或移除 Azure 虛擬網路 （只在進階版層可以使用虛擬網路）。

多區域部署地區的地址變更如果地區是空出，然後恢復 （只在進階版層可以使用多區域部署）。

為多區域部署設定的進階版層租用戶指派一個每個地區的公用 IP 位址。

Azure 入口網站中的 [租用戶] 頁面上，您可以取得 IP 位址 （或在多區域部署地址）。

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>可以使用 AD FS 安全性設定的 OAuth 2.0 授權伺服器？

若要瞭解如何使用 Active Directory Federation Services (AD FS) 的安全性設定的 OAuth 2.0 授權伺服器，請參閱[使用 ADFS，API 管理](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/)。

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>部署到多個地理位置中將 API 管理使用在哪些路由的方式？

API 管理部署到多個地理位置中，使用[效能流量路由方法](../traffic-manager/traffic-manager-routing-methods.md#performance-traffic-routing-method)。 內送的流量會路由至最接近的 API 閘道器。 如果一個區域離線，內送流量自動將路由到下一個最接近的閘道器。 進一步瞭解路由[流量管理員](../traffic-manager/traffic-manager-routing-methods.md)路由方法的方式。

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>可以使用 Azure 資源管理員範本建立 API 管理服務執行個體嗎？

[是]。 請參閱[Azure API 管理服務](http://aka.ms/apimtemplate)快速入門範本。

### <a name="can-i-use-a-self-signed-ssl-certificate-for-a-back-end"></a>我可以使用後端自我簽署的 SSL 憑證嗎？

[是]。 若要使用的後端的自我簽署的安全通訊端層 (SSL) 憑證的方法如下︰

1. 使用 API 管理建立[後端](https://msdn.microsoft.com/library/azure/dn935030.aspx)實體。
2. **SkipCertificateChainValidation**屬性設**為 true**。
3. 如果您不再想要允許自我簽署的憑證，請刪除後端的實體，或將**skipCertificateChainValidation**屬性設定為**false**。

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>為什麼當我嘗試複製給存放庫取得驗證失敗？

如果您使用給認證管理員，或如果您正在使用 Visual Studio 複製給存放庫，您可能會遇到 Windows 認證] 對話方塊的已知問題。 ] 對話方塊中限制密碼長度 127 字元，並將其截斷 Microsoft 產生的密碼。 我們正在處理縮短密碼。 現在，請使用給艦隊複製您就可以給存放庫。

### <a name="does-api-management-work-with-azure-expressroute"></a>是否能使用的 Azure ExpressRoute API 管理？

[是]。 管理 API 搭配 Azure ExpressRoute。

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>我可以移動 API 管理服務從一個訂閱到另一個嗎？

[是]。 若要瞭解作法，請參閱[移動到新的資源群組或訂閱的資源](../resource-group-move-resources.md)。
