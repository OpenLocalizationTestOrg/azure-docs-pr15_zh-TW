<properties
    pageTitle="Azure AD 同盟相容性清單"
    description="此頁面包含非 Microsoft 身分識別提供者可以用來實作單一登入。"
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/12/2016"
    ms.author="billmath"/>

# <a name="azure-ad-federation-compatibility-list"></a>Azure AD 同盟相容性清單
Azure Active Directory 提供單一登入和 Office 365 及其他 Microsoft 線上服務的混合式部署和雲端專用實作增強應用程式存取的安全性，而不需要任何非 Microsoft 解決方案。 Azure Active Directory 整合 office 365，大部分的 Microsoft 線上服務，例如目錄服務、 驗證及授權。 Azure Active Directory 還提供單一登入數以千計的 SaaS 應用程式與內部部署 web 應用程式。 請參閱支援的 SaaS 應用程式的 Azure Active Directory 應用程式組件庫。

有投資在非 Microsoft federation 解決方案的公司，本主題包含使用從 「 Azure Active Directory federation 相容性清單 」 下方的非 Microsoft 身分識別提供者設定單一登入 Windows Server Active Directory 使用者使用 Microsoft Online services 的指引。 


![](./media/active-directory-aadconnect-federation-compatibility/oxford2.jpg)   
[Oxford 電腦群組](http://oxfordcomputergroup.com/)，第三方，代表 Microsoft 測試這些單一登入體驗非 Microsoft 身分識別提供者，針對一組通用的使用情況下使用 Azure Active Directory。

如何取得您列於此處的協力廠商身分識別提供者的詳細資訊，請連絡 Oxford 電腦 Group [idp@oxfordcomputergroup.com](mailto:idp@oxfordcomputergroup.com)。

>[AZURE.IMPORTANT] Oxford 電腦群組測試僅限同盟功能這些單一登入的案例。 Oxford 電腦群組無法執行同步處理、 雙因素驗證等元件，這些單一登入的案例的測試。

>使用的登入以替代識別碼 UPN 也不測試此程式中。



- [Azure Active Directory](#azure-active-directory)
- [最佳 IDM 虛擬身分識別伺服器 Federation Services](#optimal-idm-virtual-identity-server-federation-services) 
- [PingFederate 6.11](#pingfederate-611) 
- [PingFederate 7.2](#pingfederate-72) 
- [PingFederate 8.x](#pingfederate-8x)
- [Centrify](#centrify) 
- [IBM 役同盟識別管理員 6.2.2](#ibm-tivoli-federated-identity-manager-622) 
- [SecureAuth IdP 7.2.0](#secureauth-idp-720) 
- [CA SiteMinder 12.52](#ca-siteminder-1252-sp1-cumulative-release-4) 
- [RadiantOne CFS 3.0](#radiantone-cfs-30) 
- [Okta](#okta) 
- [OneLogin](#onelogin) 
- [NetIQ 存取管理員 4.0.1](#netiq-access-manager-401) 
- [使用 x 11.6 x 存取原則管理員大 IP 版本 11.3 大 IP](#big-ip-with-access-policy-manager-big-ip-ver-113x-116x) 
- [VMware 工作區入口網站 2.1 版](#vmware-workspace-portal-version-21) 
- [登入並移 5.3](#signampgo-53) 
- [IceWall 同盟 3.0 版](#icewall-federation-version-30) 
- [CA 安全雲端](#ca-secure-cloud) 
- [Dell 一個身分識別雲端存取管理員 v7.1](#dell-one-identity-cloud-access-manager-v71) 
- [4.5 AuthAnvil 單一登](#authavil-single-sign-on-45) 

>[AZURE.IMPORTANT] 這些都是協力廠商的產品，因為 Microsoft 並不提供支援的部署、 設定、 疑難排解、 最佳作法，等的問題有關這些身分識別提供者。 如需支援與問題有關這些身分識別提供者，請直接連絡支援的協力廠商。

>與 Microsoft 雲端服務使用 WS 同盟和只 Ws-trust 通訊協定的互通性測試這些協力廠商身分識別提供者。 測試未包含使用 SAML 通訊協定。

## <a name="azure-active-directory"></a>Azure Active Directory 
Azure Active Directory 可以驗證使用者同盟的與您內部部署 Active Directory 或不使用密碼同步處理內部部署同盟伺服器。 

以下是此登入體驗的案例支援矩陣︰ 


| 用戶端 |支援  |例外狀況|
| --------- | --------- |--------- |
| 網頁用戶端，例如 Exchange Web Access 和 SharePoint Online | 支援 |無|
| 豐富型用戶端應用程式，例如 Lync、 CRM 的 Office 訂閱 |  支援 |無|
| Outlook 和 ActiveSync 等電子郵件豐富型用戶端 |  支援 |無|
|使用 Office 2016 例如 ADAL 的新式應用程式| 支援|無|

如需使用 AD FS 的 Azure Active Directory 的相關資訊，請參閱[Active Directory Federation Services (ADFS)](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)

使用密碼同步處理的 Azure Active Directory 的相關資訊，請參閱[Azure AD Connect](active-directory-aadconnect.md)。


## <a name="optimal-idm-virtual-identity-server-federation-services"></a>最佳 IDM 虛擬身分識別伺服器 Federation Services 
最佳 IDM 虛擬身分識別伺服器 Federation Services 可以驗證客戶的內部部署 Active 目錄中的使用者。

以下是在此案例支援矩陣此單一登入體驗︰


| 用戶端 |支援  |例外狀況|
| --------- | --------- |--------- |
| 網頁用戶端，例如 Exchange Web Access 和 SharePoint Online | 支援 |無|
| 豐富型用戶端應用程式，例如 Lync 的 Office 訂閱、 CRM |  支援 |整合式的 Windows 驗證|
| Outlook 和 ActiveSync 等電子郵件豐富型用戶端 |  支援 |如需有關用戶端存取原則，請參閱[限制存取 Office 365 服務為基礎的用戶端的位置。](https://technet.microsoft.com/library/hh526961.aspx)|



## <a name="pingfederate-611"></a>PingFederate 6.11 

PingFederate 6.11] 實作提供單一登入和屬性 exchange 架構的常用的 WS 同盟身分識別標準。

以下是此單一登入體驗的案例支援矩陣︰


| 用戶端 |支援  |例外狀況|
| --------- | --------- |--------- |
| 網頁用戶端，例如 Exchange Web Access 和 SharePoint Online | 支援 |無|
| 豐富型用戶端應用程式，例如 Lync 的 Office 訂閱、 CRM |  支援 |無 （舊版必須升級至 6.11]|
| Outlook 和 ActiveSync 等電子郵件豐富型用戶端 |  支援 |無|

如需如何設定此 PingFederate 指示給您的 Active Directory 使用者提供的單一登入體驗 STS 下載 pdf[以下。](http://go.microsoft.com/fwlink/?LinkID=266321)

## <a name="pingfederate-72"></a>PingFederate 7.2 
PingFederate 7.2 實作常用的同盟/WS Ws-trust 身分識別標準提供單一登入和屬性 exchange 架構。

以下是此單一登入體驗的案例支援矩陣︰


| 用戶端 |支援  |例外狀況|
| --------- | --------- |--------- |
| 網頁用戶端，例如 Exchange Web Access 和 SharePoint Online | 支援 |無|
| 豐富型用戶端應用程式，例如 Lync、 CRM 的 Office 訂閱 |  支援 |無|
| Outlook 和 ActiveSync 等電子郵件豐富型用戶端 |  支援 |無|

若要提供給 Active Directory 使用者的單一登入體驗如何設定此 STS PingFederate 指示，請參閱[以下。](http://documentation.pingidentity.com/display/PF72/PingFederate+7.2)

## <a name="pingfederate-8x"></a>PingFederate 8.x 
PingFederate 8.x 實作常用的同盟/WS Ws-trust 身分識別標準提供單一登入和屬性 exchange 架構。

以下是此單一登入體驗的案例支援矩陣︰


| 用戶端 |支援  |例外狀況|
| --------- | --------- |--------- |
| 網頁用戶端，例如 Exchange Web Access 和 SharePoint Online | 支援 |無|
| 豐富型用戶端應用程式，例如 Lync、 CRM 的 Office 訂閱 |  支援 |無|
| Outlook 和 ActiveSync 等電子郵件豐富型用戶端 |  支援 |無|

若要提供給 Active Directory 使用者的單一登入體驗如何設定此 STS PingFederate 指示，請參閱[以下。](http://documentation.pingidentity.com/display/PFS/SSO+to+Office+365+Introduction)

## <a name="centrify"></a>Centrify 
Centrify 有助於提供 Office 365 的同盟單一登入體驗，而不需要的主機服務是內部部署同盟伺服器。

以下是此單一登入體驗的案例支援矩陣︰


| 用戶端 |支援  |例外狀況|
| --------- | --------- |--------- |
| 網頁用戶端，例如 Exchange Web Access 和 SharePoint Online | 支援 |無|
| 豐富型用戶端應用程式，例如 Lync、 CRM 的 Office 訂閱 |  支援 |無|
| Outlook 和 ActiveSync 等電子郵件豐富型用戶端 |  支援 |不支援的用戶端存取控制 

如需有關 Centrify 的詳細資訊，請參閱[以下。](http://www.centrify.com/cloud/apps/single-sign-on-for-office-365.asp)|

## <a name="ibm-tivoli-federated-identity-manager-622"></a>IBM 役同盟識別管理員 6.2.2 
IBM 役同盟身分識別管理員 6.2.2 與 IBM 安全性存取管理員的 Microsoft 應用程式 1.4 實作常用的同盟/WS Ws-trust 身分識別標準提供單一登入和屬性 exchange 架構。

以下是此單一登入體驗的案例支援矩陣︰ 

| 用戶端 |支援  |例外狀況|
| --------- | --------- |--------- |
| 網頁用戶端，例如 Exchange Web Access 和 SharePoint Online | 支援 |無|
| 豐富型用戶端應用程式，例如 Lync、 CRM 的 Office 訂閱 |  支援 |無|
| Outlook 和 ActiveSync 等電子郵件豐富型用戶端 |  支援 |無|

如需關於 IBM 役同盟身分識別管理人員，請參閱[IBM 安全性存取管理員的 Microsoft 應用程式。](http://www-01.ibm.com/support/docview.wss?uid=swg24029517)

## <a name="secureauth-idp-720"></a>SecureAuth IdP 7.2.0 
SecureAuth IdP 7.2.0 實作常用的同盟/WS Ws-trust 身分識別標準提供單一登入體驗和屬性 exchange 架構。

以下是此單一登入體驗的案例支援矩陣︰ 

| 用戶端 |支援  |例外狀況|
| --------- | --------- |--------- |
| 網頁用戶端，例如 Exchange Web Access 和 SharePoint Online | 支援 |無|
| 豐富型用戶端應用程式，例如 Lync、 CRM 的 Office 訂閱 |  支援 |無|
| Outlook 和 ActiveSync 等電子郵件豐富型用戶端 |  支援 |無|

如需有關 SecureAuth 的詳細資訊，請參閱[SecureAuth IdP](http://go.microsoft.com/?linkid=9845293)。

## <a name="ca-siteminder-1252-sp1-cumulative-release-4"></a>CA SiteMinder 12.52 SP1 累計發行 4
CA SiteMinder 同盟 12.52 實作常用的同盟/WS Ws-trust 身分識別標準提供單一登入和屬性 exchange 架構。

以下是此單一登入體驗的案例支援矩陣︰ 

| 用戶端 |支援  |例外狀況|
| --------- | --------- |--------- |
| 網頁用戶端，例如 Exchange Web Access 和 SharePoint Online | 支援 |無|
| 豐富型用戶端應用程式，例如 Lync、 CRM 的 Office 訂閱 |  支援 |無|
| Outlook 和 ActiveSync 等電子郵件豐富型用戶端 |  支援 |無|

如需有關 CA SiteMinder 的詳細資訊，請參閱[CA SiteMinder 同盟。](http://www.ca.com/us/products/ca-single-sign-on.html) 

## <a name="radiantone-cfs-30"></a>RadiantOne CFS 3.0 
RadiantOne 雲端同盟服務 (CFS) 3.0 實作常用的同盟/WS Ws-trust 身分識別標準提供單一登入和屬性 exchange 架構。

以下是此單一登入體驗的案例支援矩陣︰ 

| 用戶端 |支援  |例外狀況|
| --------- | --------- |--------- |
| 網頁用戶端，例如 Exchange Web Access 和 SharePoint Online | 支援 |無|
| 豐富型用戶端應用程式，例如 Lync、 CRM 的 Office 訂閱 |  支援 |整合式的 Windows 驗證|
| Outlook 和 ActiveSync 等電子郵件豐富型用戶端 |  支援 |無|

如需有關 RadiantOne CFS 的詳細資訊，請參閱[RadiantOne CFS。](http://www.radiantlogic.com/products/radiantone-cfs/)


## <a name="okta"></a>Okta 
Okta 實作常用的同盟/WS Ws-trust 身分識別標準提供單一登入和屬性 exchange 架構。

以下是此單一登入體驗的案例支援矩陣︰ 


| 用戶端 |支援  |例外狀況|
| --------- | --------- |--------- |
| 網頁用戶端，例如 Exchange Web Access 和 SharePoint Online | 支援 |整合式的 Windows 驗證需要額外的網頁伺服器及 Okta 應用程式的設定。|
| 豐富型用戶端應用程式，例如 Lync、 CRM 的 Office 訂閱 |  支援 |整合式的 Windows 驗證|
| Outlook 和 ActiveSync 等電子郵件豐富型用戶端 |  支援 |無|

如需有關 Okta 的詳細資訊，請參閱[Okta。](https://www.okta.com/)
 
## <a name="onelogin"></a>OneLogin 
為在 2014年測試 OneLogin 實作常用的同盟/WS Ws-trust 身分識別標準提供單一登入和屬性 exchange 架構。

以下是此單一登入體驗的案例支援矩陣︰ 

| 用戶端 |支援  |例外狀況|
| --------- | --------- |--------- |
| 網頁用戶端，例如 Exchange Web Access 和 SharePoint Online | 支援 |整合式的 Windows 驗證|
| 豐富型用戶端應用程式，例如 Lync、 CRM 的 Office 訂閱 |  支援 |整合式的 Windows 驗證|
| Outlook 和 ActiveSync 等電子郵件豐富型用戶端 |  支援 |無|

如需有關 OneLogin 的詳細資訊，請參閱[OneLogin。](https://www.onelogin.com/)

## <a name="netiq-access-manager-401"></a>NetIQ 存取管理員 4.0.1 
NetIQ 存取管理員 4.0.1 實作常用的同盟/WS Ws-trust 身分識別標準提供單一登入和屬性 exchange 架構。

以下是此單一登入體驗的案例支援矩陣︰

| 用戶端 |支援  |例外狀況|
| --------- | --------- |--------- |
| 網頁用戶端，例如 Exchange Web Access 和 SharePoint Online | 支援 |* Kerberos 合約支援|
| 豐富型用戶端應用程式，例如 Lync、 CRM 的 Office 訂閱 |  支援 |整合式的 Windows 驗證功能不受支援|
| Outlook 和 ActiveSync 等電子郵件豐富型用戶端 |  支援 |無|

* NetIQ 支援 Kerberos 驗證，透過 Kerberos 合約的設定。  如需此設定的協助，請連絡 NetIQ 或檢視安裝指南。 如需關於 NetIQ 存取管理員，請參閱[NetIQ 存取管理員。](https://www.netiq.com/documentation/netiqaccessmanager4/identityserverhelp/data/b12iqp0m.html)

## <a name="big-ip-with-access-policy-manager-big-ip-ver-113x--116x"></a>大 IP 與存取原則管理員大 IP 版本 x 11.6 x 11.3 
使用 Access 原則管理員，(APM) 大 IP 大 IP 版本 11.3 x – 11.6 x 實作提供單一登入體驗和屬性 exchange 架構的常用的 SAML 身分識別標準。

以下是此單一登入體驗的案例支援矩陣︰ 

| 用戶端 |支援  |例外狀況|
| --------- | --------- |--------- |
| 網頁用戶端，例如 Exchange Web Access 和 SharePoint Online | 支援 |無|
| 豐富型用戶端應用程式，例如 Lync、 CRM 的 Office 訂閱 |  不支援 |不支援|
| Outlook 和 ActiveSync 等電子郵件豐富型用戶端 |  支援 |無|

如需關於大 IP 存取原則管理員，請參閱[大 IP 存取原則管理員。](https://f5.com/products/modules/access-policy-manager) 

如需如何設定此大 IP 存取原則管理員指示 Active Directory 使用者，提供單一登入體驗 STS 下載 pdf[以下。](http://www.f5.com/pdf/deployment-guides/microsoft-office-365-idp-dg.pdf)

## <a name="vmware--workspace-portal-version-21"></a>VMware 工作區入口網站 2.1 版 
VMware 工作區入口網站 2.1 版實作常用的同盟/WS Ws-trust 身分識別標準提供單一登入和屬性 exchange 架構。

以下是此單一登入體驗的案例支援矩陣︰

| 用戶端 |支援  |例外狀況|
| --------- | --------- |--------- |
| 網頁用戶端，例如 Exchange Web Access 和 SharePoint Online | 支援 |整合式的 Windows 驗證功能不受支援|
| 豐富型用戶端應用程式，例如 Lync、 CRM 的 Office 訂閱 | 支援 |整合式的 Windows 驗證功能不受支援|
| Outlook 和 ActiveSync 等電子郵件豐富型用戶端 |  支援 |無|

如需有關 VMware 工作區入口網站 2.1 版的詳細資訊，請下載 pdf[以下。](http://pubs.vmware.com/workspace-portal-21/topic/com.vmware.ICbase/PDF/workspace-portal-21-resource.pdf)

## <a name="signgo-53"></a>登入並移 5.3 
登入並移 5.3 實作常用的同盟/WS Ws-trust 身分識別提供單一登入和屬性 exchange 架構標準]。

以下是此單一登入體驗的案例支援矩陣︰

| 用戶端 |支援  |例外狀況|
| --------- | --------- |--------- |
| 網頁用戶端，例如 Exchange Web Access 和 SharePoint Online | 支援 |支援 Kerberos 合約 |
| 豐富型用戶端應用程式，例如 Lync、 CRM 的 Office 訂閱 | 支援 |無|
| Outlook 和 ActiveSync 等電子郵件豐富型用戶端 |  支援 |無|


符號與執行 5.3 支援 Kerberos 驗證，透過 Kerberos 合約的設定。  如需此設定的協助，請連絡 Ilex 或檢視設定指南[以下。](http://www.ilex-international.com/docs/sign&go_wsfederation_en.pdf)


## <a name="icewall-federation-version-30"></a>IceWall 同盟 3.0 版 
IceWall 同盟版本 3.0 實作常用的同盟/WS Ws-trust 身分識別標準提供單一登入和屬性 exchange 架構。

以下是此單一登入體驗的案例支援矩陣︰

| 用戶端 |支援  |例外狀況|
| --------- | --------- |--------- |
| 網頁用戶端，例如 Exchange Web Access 和 SharePoint Online | 支援 |整合式的 Windows 驗證功能不受支援|
| 豐富型用戶端應用程式，例如 Lync、 CRM 的 Office 訂閱 | 支援 |不支援整合的 Windows 驗證|
| Outlook 和 ActiveSync 等電子郵件豐富型用戶端 |  支援 |無|

如需有關 IceWall 同盟的詳細資訊，請參閱[以下](http://h50146.www5.hp.com/products/software/security/icewall/eng/federation/)和[以下。](http://h50146.www5.hp.com/products/software/security/icewall/federation/office365.html)

## <a name="ca-secure-cloud"></a>CA 安全雲端 

CA 安全雲端實作常用的同盟/WS Ws-trust 身分識別標準提供單一登入和屬性 exchange 架構。

以下是此單一登入體驗的案例支援矩陣︰

| 用戶端 |支援  |例外狀況|
| --------- | --------- |--------- |
| 網頁用戶端，例如 Exchange Web Access 和 SharePoint Online | 支援 |整合式的 Windows 驗證功能不受支援|
| 豐富型用戶端應用程式，例如 Lync、 CRM 的 Office 訂閱 | 支援 |整合式的 Windows 驗證功能不受支援|
| Outlook 和 ActiveSync 等電子郵件豐富型用戶端 |  支援 |無|

如需有關 CA 安全雲端的詳細資訊，請參閱[CA 安全雲端。](http://www.ca.com/us/products/security-as-a-service.aspx)

## <a name="dell-one-identity-cloud-access-manager-v71"></a>Dell 一個身分識別雲端存取管理員 v7.1 
Dell 一個身分識別雲端存取管理員實作常用的同盟/WS Ws-trust 身分識別標準提供單一登入和屬性 exchange 架構。

以下是此單一登入體驗的案例支援矩陣︰

| 用戶端 |支援  |例外狀況|
| --------- | --------- |--------- |
| 網頁用戶端，例如 Exchange Web Access 和 SharePoint Online | 支援 |無|
| 豐富型用戶端應用程式，例如 Lync、 CRM 的 Office 訂閱 |  支援 |無|
| Outlook 和 ActiveSync 等電子郵件豐富型用戶端 |  支援 |無|

如需有關 Dell 一個身分識別雲端存取管理員，請參閱[Dell 一個身分識別雲端存取管理員。](http://software.dell.com/products/cloud-access-manager)

 若要提供給您的 Office 365 使用者的單一登入體驗如何設定此 STS 指示，請參閱[設定 Office 365 的使用者。](http://documents.software.dell.com/dell-one-identity-cloud-access-manager/7.1/how-to-configure-microsoft-office-365) 

## <a name="authanvil-single-sign-on-45"></a>4.5 AuthAnvil 單一登 
AuthAnvil 單一登入 4.5 實作常用的同盟/WS Ws-trust 身分識別標準提供單一登入和屬性 exchange 架構。

以下是此單一登入體驗的案例支援矩陣︰

| 用戶端 |支援  |例外狀況|
| --------- | --------- |--------- |
| 網頁用戶端，例如 Exchange Web Access 和 SharePoint Online | 支援 |不支援整合的 Windows 驗證|
| 豐富型用戶端應用程式，例如 Lync、 CRM 的 Office 訂閱 | 支援 |不支援整合的 Windows 驗證|
| Outlook 和 ActiveSync 等電子郵件豐富型用戶端 |  支援 |無|


如需詳細資訊，請參閱[AuthAnvil 單一登入。](https://help.scorpionsoft.com/entries/26538603-How-can-I-Configure-Single-Sign-On-for-Office-365-)
