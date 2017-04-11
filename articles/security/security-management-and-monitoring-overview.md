<properties
   pageTitle="Azure 安全性管理及監視概觀 |Microsoft Azure"
   description=" Azure 提供協助管理和監視 Azure 雲端服務和虛擬機器中的安全性機制。  本文提供這些核心安全性功能及服務的概觀。 "
   services="security"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="terrylan"/>

# <a name="azure-security-management-and-monitoring-overview"></a>Azure 安全性管理及監視概觀

Azure 提供協助管理和監視 Azure 雲端服務和虛擬機器中的安全性機制。 本文提供這些核心安全性功能及服務的概觀。 讓每個詳細資料，您可以瞭解更多的文章提供的連結。

Microsoft 雲端服務的安全性是合作關係和您與 Microsoft 之間共用的責任。 共用的責任表示 Microsoft 負責 Microsoft Azure 和其資料的實體安全性中心 （透過使用鎖定的徽章項目門與守衛保護，例如安全性保護）。 此外，Azure 提供強式層的軟體符合安全性、 隱私權和法規遵循的需求滿足客戶的雲端安全性層級。

您擁有您的資料與身分識別、 保護這些、 您的內部部署資源的安全性和安全性的雲端元件的您可以控制的責任。 Microsoft 會提供您的安全性控制項與功能可協助您保護您的資料與應用程式。 您負責安全性根據雲端服務的類型而定。

下表摘要列出餘額的 Microsoft 和客戶的責任。

![共用的責任][1]

安全性管理深入，請參閱[Azure 中的安全性管理](azure-security-management.md)。

以下是本文涵蓋的核心功能︰

- 角色型存取控制
- 反惡意程式碼
- 多重因素驗證
- ExpressRoute
- 虛擬網路閘道器
- 有權限的身分識別管理
- 身分識別保護
- 資訊安全中心

## <a name="role-based-access-control"></a>角色型存取控制

角色型存取控制 (RBAC) 提供精密存取管理 Azure 資源。 使用 RBAC，您可以授與人員執行其工作所需的存取量。  RBAC 也可協助您確保人員離開組織時它們會遺失存取雲端中的資源。

深入瞭解︰

- [上 RBAC active Directory 小組部落格](http://i1.blogs.technet.com/b/ad/archive/2015/10/12/azure-rbac-is-ga.aspx)
- [Azure 角色型存取控制](../active-directory/role-based-access-control-configure.md)

## <a name="antimalware"></a>反惡意程式碼

您可以使用 Azure 從主要的安全性樣式，例如 Microsoft、 Symantec、 趨勢微、 McAfee 及 Kaspersky 反惡意程式碼軟體，協助防止惡意檔案、 廣告軟體及其他潛在威脅的虛擬機器。

Microsoft 反惡意程式碼會提供您安裝 PaaS 角色] 及 [虛擬機器反惡意程式碼代理程式的能力。 根據系統管理中心端點保護，此功能讓經過驗證的內部部署安全性技術雲端。

我們也提供趨勢上的[[深層安全性](http://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/)深度整合™ 和[SecureCloud](http://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/)™ Azure 平台的產品。 DeepSecurity 防毒解決方案，而 SecureCloud 是加密的解決方案。 將部署 DeepSecurity Vm 使用副檔名模型內。 使用 PowerShell 與入口網站使用者介面，您可以選擇要使用的正在旋轉完畢之後，新 Vm 或已部署的現有 Vm DeepSecurity。

Azure 也支援 Symantec 結束點保護 （年 9 月）。 透過入口網站整合，客戶能夠以指定要使用年 9 月內 VM。 年 9 月可以安裝在新 VM 透過 Azure 入口網站，或可以安裝在現有的 VM 使用 PowerShell。

深入瞭解︰

- [Azure 虛擬機器上的部署反惡意程式碼解決方案](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
- [Microsoft 反惡意程式碼 Azure 雲端服務和虛擬機器](../security/azure-security-antimalware.md)
- [如何安裝和設定趨勢微深層安全性為 Windows VM 的服務](../virtual-machines/virtual-machines-windows-classic-install-trend.md)
- [如何安裝和設定 Windows VM Symantec 端點保護](../virtual-machines/virtual-machines-windows-classic-install-symantec.md)
- [新的反惡意程式碼選項，來保護 Azure 虛擬機器 – McAfee 端點保護](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>多重因素驗證

Azure 多重因素驗證 (MFA) 是安全性的需要使用一個以上的驗證方法，並將關鍵的第二個層級新增到使用者登增益集和交易的驗證方法。 MFA 可協助保護資料和會議的簡單登入程序的使用者需求時的應用程式存取。 它提供強式驗證透過一系列的驗證選項 — 電話、 文字訊息或行動應用程式通知或驗證碼和 3rd 廠商誓言權杖。

深入瞭解︰

- [多重因素驗證](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
- [什麼是 Azure 多重因素驗證？](../multi-factor-authentication/multi-factor-authentication.md)
- [Azure 多重因素驗證的運作方式](../multi-factor-authentication/multi-factor-authentication-how-it-works.md)

## <a name="expressroute"></a>ExpressRoute

Microsoft Azure ExpressRoute 可讓您透過一個連線提供者所提供的專用私人連線時，將您的內部部署網路延伸至 Microsoft 雲端。 有 ExpressRoute，您可以建立連線至 Microsoft 雲端服務，例如 Microsoft Azure、 Office 365 和 CRM Online。 連線可以從任何-任何 (IP VPN) 網路、 點對點乙太網路或虛擬連線提供者所提供的代管設備在跨-連線。 ExpressRoute 連線，請勿繼續進行公用網際網路上。 這個選項可讓 ExpressRoute 連線至網際網路上提供更多的可靠性、 更快的速度，較低延遲，以及較高的安全性比一般的連線。

深入瞭解︰

- [ExpressRoute 技術概觀](../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>虛擬網路閘道器

VPN 閘道，也稱為 Azure 虛擬網路閘道器用來傳送虛擬網路與內部部署位置之間的網路流量。 他們也用來傳送之間 Azure (VNet-VNet) 內的多個虛擬網路流量。  VPN 閘道器提供 Azure 與您的基礎結構安全跨內部部署連線。

深入瞭解︰

- [關於 VPN 閘道器](../vpn-gateway/vpn-gateway-about-vpngateways.md)
- [Azure 網路安全性概觀](security-network-overview.md)

## <a name="privileged-identity-management"></a>有權限的身分識別管理

有時候，使用者需要執行 Azure 資源或其他 SaaS 應用程式中的權限作業。 這通常表示的組織必須為其授與永久權限的存取的 Azure Active Directory (Azure AD)。 這是持續增加安全性風險的雲端裝載的資源，因為組織無法完全監控這些使用者擁有其存取權的執行。
此外，如果受到擁有存取權的使用者帳戶，該一個違反可能會影響您的整體雲端安全性。 Azure AD 權限的身分識別管理協助解決這類風險降低揭露時間的權限，並增加完善使用方式。  

有權限的身分識別管理提出的暫存的管理員角色或 」 在 「 系統管理員存取權，也就是需要完成啟動程序的角色指派之使用者的概念。 啟動程序變更使用者的工作分派至角色中 Azure AD 非使用中為 [作用中，針對指定的時間期間，例如 8 小時。

深入瞭解︰

- [Azure AD 權限的身分識別管理](../active-directory/active-directory-privileged-identity-management-configure.md)
- [Azure AD 有權限的身分識別管理快速入門](../active-directory/active-directory-privileged-identity-management-getting-started.md)

## <a name="identity-protection"></a>身分識別保護

Azure Active Directory (AD) 的身分識別保護提供可疑的登入活動和可能的弱點協助保護您的企業的合併的檢視。 身分識別保護偵測到可疑的活動，以使用者與權限 （管理員） 的身分識別，根據訊號惡意攻擊，例如遺漏認證，然後登增益集從熟悉的位置感染裝置。

藉由提供通知和建議的補救，身分識別保護 」 可協助降低風險即時。 它會計算使用者風險的嚴重性，以及設定自動協助保護應用程式從未來威脅存取的風險原則。

深入瞭解︰

- [Azure Active Directory 身分識別保護](../active-directory/active-directory-identityprotection.md)
- [頻道 9: Azure AD 與身分識別放映︰ 身分識別保護預覽](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>資訊安全中心

Azure 資訊安全中心 」 可協助您避免偵測到，與回應威脅，，並提供您增加完善，並控制，您 Azure 資源的安全性。 整合式的安全性的監控和原則管理提供您 Azure 訂閱，有助於偵測威脅，否則請忽略和相關事務的安全性解決方案的主要生態系統。

資訊安全中心 」 可協助您最佳化，並監控由您 Azure 資源的安全性︰

- 讓您定義中每個訂閱原則 Azure 訂閱資源根據貴公司的安全性需求及應用程式類型或資料的敏感度。
- 監控您的 Azure 虛擬機器、 網路連線和應用程式的狀態。
- 提供優先順序的安全性提醒，包括整合式的合作夥伴解決方案，以及您要快速調查的資訊和如何修補攻擊的建議的通知的清單。

深入瞭解︰

- [Azure 安全性中心簡介](../security-center/security-center-intro.md)

<!--Image references-->
[1]: ./media/security-management-and-monitoring-overview/shared-responsibility.png
