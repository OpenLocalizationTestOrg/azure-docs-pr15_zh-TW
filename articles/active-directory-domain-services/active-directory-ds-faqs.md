<properties
    pageTitle="常見問題集-Azure Active Directory 網域服務 |Microsoft Azure"
    description="Azure Active Directory 網域服務相關的常見問題集"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="maheshu"/>

# <a name="azure-active-directory-domain-services-frequently-asked-questions-faqs"></a>Azure Active Directory 網域服務︰ 常見問題集 (Faq)

此頁面將回答有關 Azure Active Directory 網域服務的常見問題集的問題。 保留檢查回更新。

### <a name="troubleshooting-guide"></a>疑難排解指南
請參閱設定或管理 Azure AD 網域服務時所發生的一般問題的解決方案我們[疑難排解指南](active-directory-ds-troubleshooting.md)。


### <a name="configuration"></a>設定

#### <a name="can-i-create-multiple-domains-for-a-single-azure-ad-directory"></a>我是否能建立多個網域的單一 Azure AD 目錄？
[否]。 您只可以建立單一網域服務的單一 Azure AD 網域服務 Azure AD 目錄。  

#### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>我可以啟用 Azure 資源管理員虛擬網路中的 Azure AD 網域服務？
[否]。 Azure AD 網域服務可以只在傳統的 Azure 虛擬網路。 您可以使用為使用您受管理的網域，在 [資源管理員虛擬網路對等的虛擬網路資源管理員虛擬網路連線傳統的虛擬網路。

#### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>讓 Azure AD 網域服務中多個虛擬網路可用內 [我的訂閱？
本身的服務並不會直接支援這種情況。 Azure AD 網域服務中會有一個虛擬網路，一次。 不過，您可以設定公開 Azure AD 網域其他虛擬網路服務的多個虛擬網路之間的連線。 本文將說明如何[在 Azure 虛擬網路連線](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)。

#### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>我可以啟用使用 PowerShell 的 Azure AD 網域服務？
PowerShell/自動化部署 Azure AD 網域服務目前無法使用。

#### <a name="is-azure-ad-domain-services-available-in-the-new-azure-portal"></a>Azure AD 網域服務中有新的 Azure 入口網站？
[否]。 只在[Azure 傳統入口網站](https://manage.windowsazure.com)，可以設定 azure AD 網域服務。 我們會在未來延伸支援[Azure 入口網站](https://portal.azure.com)。

#### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>可以新增網域至 Azure AD 網域服務受管理的網域嗎？
[否]。 Azure AD 網域服務所提供的網域已受管理的網域。 您不需要佈建、 設定或管理此網域的網域控制站管理活動所提供的服務 microsoft。 因此，您無法新增其他網域控制站 （讀寫或唯讀） 的受管理的網域。

### <a name="administration-and-operations"></a>管理及作業

#### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>我可以連線至網域控制站的使用遠端桌面版的受管理的網域嗎？
[否]。 您沒有連線至受管理的網域透過遠端桌面網域控制站的權限。 「 AAD DC 管理員 」 群組的成員可以管理使用 AD 系統管理工具，例如 Active Directory 系統管理中心 (ADAC) 或 AD PowerShell 受管理的網域。 安裝這些工具在加入受管理的網域的 Windows server 上使用 [遠端伺服器管理工具] 功能。

#### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>我已啟用 Azure AD 網域服務。 此網域的網域加入機器使用哪些使用者帳戶？
您已新增至系統管理群組 （例如，「 AAD DC 系統管理員 」） 的使用者可以網域加入機器。 此外，在這個群組中的使用者授權讓遠端桌面存取加入網域的電腦。

#### <a name="can-i-wield-domain-administrator-privileges-for-the-domain-provided-by-azure-ad-domain-services"></a>我可以使用提供的 Azure AD 網域服務中該網域的網域系統管理員權限？
[否]。 您無法取得系統管理權限的受管理的網域。 「 網域管理員 」 和 「 企業系統管理員] 權限都無法使用您網域中使用。 現有的網域系統管理員或您 Azure AD 目錄內的企業系統管理員群組會也不授與網域/企業系統管理員權限的網域上。

#### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-domains-provided-by-azure-ad-domain-services"></a>可以修改 Azure AD 網域服務所提供的網域上使用 LDAP 或其他 AD 系統管理工具的群組成員資格嗎？
[否]。 無法上傳 Azure AD 網域服務的網域修改群組成員資格。 相同適用於使用者屬性。 Azure AD 或從您內部部署的網域，您可能會不過變更群組的成員資格或使用者屬性。 這些變更會自動同步處理至 Azure AD 網域服務。

#### <a name="can-i-extend-the-schema-of-the-domain-provided-by-azure-ad-domain-services"></a>可以擴充的結構描述 Azure AD 網域服務所提供的網域嗎？
[否]。 結構描述是由 Microsoft 管理，受管理的網域。 Azure AD 網域服務不支援架構延伸。

#### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>我是否可以修改或新增的受管理的網域的 DNS 記錄？
[是]。 屬於 AAD DC 管理員 」 群組的使用者權限 」 DNS 系統管理員]，修改受管理的網域的 DNS 記錄。 這些使用者可以使用 [DNS 管理員] 主控台執行 Windows Server 加入受管理的網域，電腦上管理 DNS。 若要使用 [DNS 管理員] 主控台，安裝 [DNS 伺服器工具]，這是 「 遠端伺服器管理工具 「 選擇性功能，在伺服器上的一部分。 TechNet 上的[管理、 監控及疑難排解 DNS](https://technet.microsoft.com/library/cc753579.aspx)公用程式的詳細資訊。


### <a name="billing-and-availability"></a>計費與可用性

#### <a name="is-azure-ad-domain-services-a-paid-service"></a>是 Azure AD 網域服務付費的服務？
[是]。 如需詳細資訊，請參閱[價格頁面](https://azure.microsoft.com/pricing/details/active-directory-ds/)。

#### <a name="is-there-a-free-trial-for-the-service"></a>有服務的免費試用版嗎？
這項服務會包含在 Azure 的免費試用版。 您可以註冊[免費的一個月的 Azure 試用版](https://azure.microsoft.com/pricing/free-trial/)。

#### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems"></a>可以取得 Azure AD 網域服務一部分的企業版行動性套件 (EMS)？
#### <a name="do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>我需要使用 Azure AD 網域服務 Azure AD 進階版嗎？
[否]。 Azure AD 網域服務 pay-as-you-go Azure 服務，而不是 EMS 的一部分。 Azure AD 網域服務可供所有版本 Azure AD （免費、 基本、 及，付費） 及付款地，根據使用方式。

#### <a name="what-azure-regions-is-the-service-available-in"></a>哪些 Azure 區域為提供服務？
請參閱[Azure 服務，依地區](https://azure.microsoft.com/regions/#services/)頁面以查看其中 Azure AD 網域服務是可用的 Azure 區域的清單。
