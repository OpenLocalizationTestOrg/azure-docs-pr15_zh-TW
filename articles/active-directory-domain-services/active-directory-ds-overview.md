<properties
    pageTitle="Azure Active Directory 網域服務概觀 |Microsoft Azure"
    description="Azure Active Directory 網域服務的概觀"
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
    ms.date="10/07/2016"
    ms.author="maheshu"/>

# <a name="azure-ad-domain-services"></a>Azure AD 網域服務

## <a name="overview"></a>概觀
Azure 基礎結構服務可讓您部署大範圍的計算方式敏捷式的解決方案。 使用 Azure 虛擬機器中，您可以幾乎立即部署，您只使用分鐘付款。 您可以使用支援 Windows、 Linux、 SQL Server、 Oracle、 IBM、 SAP，BizTalk，部署任何工作量、 在幾乎任何作業系統上的任何語言。 這些福利可讓您將舊版的應用程式部署內部部署移轉到 Azure，若要儲存在費用。

Azure 移轉內部部署的應用程式的重點會處理這些應用程式的身分識別的需求。 目錄注意應用程式可能會依賴 LDAP 供讀取或寫入存取權的公司目錄，或 Windows 整合式驗證 （Kerberos 驗證或 NTLM 驗證），以驗證使用者是仰賴。 在 Windows Server 上執行的線條的商務 (LOB) 應用程式通常會加入網域在電腦上，部署，讓他們可以安全地使用群組原則來管理。 雲端 ' 增益 shift' 內部部署應用程式，這些的公司的身分識別為基礎的相依性需要解決。

系統管理員通常開啟下列解決方案來滿足身分識別需求部署 Azure 在其應用程式的其中一項︰

- 部署網站-VPN 連線之間 Azure 基礎結構服務和公司內部部署的目錄中執行的工作量。
- 設定使用 Azure 虛擬機器複本網域控制站延伸公司 AD 網域/樹系基礎結構。
- 部署 Azure 使用網域控制站部署為 Azure 虛擬機器中的獨立網域。

這些方法都遭受高成本與管理費用。 系統管理員，才能部署網域控制站使用 Azure 虛擬機器。 此外，需要管理、 保護、 更新、 監控、 備份，及疑難排解這些虛擬機器。 VPN 連線到內部部署目錄依賴會使弱點暫時網路故障或中斷 Azure 中部署的工作量。 這些網路中斷依次產生較低的執行時間，降低的可靠性這些應用程式。

我們設計 Azure AD 網域服務，提供更容易替代方案。


## <a name="introducing-azure-ad-domain-services"></a>介紹 Azure AD 網域服務
Azure AD 網域服務會提供受管理的網域服務，例如完全相容於 Windows Server Active Directory 群組原則，LDAP，Kerberos/NTLM 驗證網域加入。 您可以使用這些網域服務，而不需要使用您的部署、 管理及更新雲端中的網域。 Azure AD 網域服務整合了您現有的 Azure AD 租用戶，因此很可能會使用其公司的認證登入的使用者。 此外，您可以使用現有的群組和使用者帳戶安全存取資源，以確保多 ' 增益和-移位 」 的內部部署資源 Azure 基礎結構服務。

Azure AD 網域服務功能完美無論您 Azure AD 租用戶是否雲端專用或與您內部部署的 Active Directory 同步處理。

### <a name="azure-ad-domain-services-for-cloud-only-organizations"></a>Azure AD 網域服務雲端專用的組織
雲端專用 Azure AD 租用戶 （通常稱為 「 受管理的租用戶 」），並沒有任何所需的內部部署的身分識別。 換句話說，使用者帳戶、 密碼和群組的成員資格是所有原生雲端-也就是建立和管理 Azure AD 中。 請考慮一下 Contoso 是雲端專用 Azure AD 租用戶。 下圖所示，Contoso 的系統管理員設定虛擬網路中 Azure 基礎結構服務。 在這個 Azure 虛擬機器中的虛擬網路中部署應用程式與伺服器工作量。 由於 Contoso 是雲端專用的租用戶，所有的使用者身分識別，其認證，然後建立並 Azure AD 中管理群組成員資格。

![Azure AD 網域服務概觀](./media/active-directory-domain-services-overview/aadds-overview.png)

Contoso IT 系統管理員可以啟用其 Azure AD 租用戶的 Azure AD 網域服務，並選擇 [若要在這個虛擬網路中使用網域服務。 此後，Azure AD 網域服務佈建新的受管理的網域，並可在虛擬網路。 所有的使用者帳戶，群組成員資格和 Contoso Azure AD 租用戶中的使用者認證，也會使用這個新建立的網域。 此功能可讓使用者在組織中以登入至遠端網域的電腦，透過遠端桌面連線時，使用其公司認證-例如網域。 系統管理員可以提供存取中使用現有群組的成員資格的網域的資源。 部署虛擬網路上的虛擬機器中的應用程式可以使用功能，例如網域加入、 LDAP 讀取、 LDAP 繫結、 NTLM 和 Kerberos 驗證，以及群組原則。

已佈建 Azure AD 網域服務的受管理網域的幾個明顯方面如下所示︰

- Contoso IT 系統管理員不需要管理、 更新，或監視此網域或任何網域控制站，此受管理的網域。
- 有不需要管理 AD 複寫此網域。 使用者帳戶與群組成員資格，從 Contoso Azure AD 租用戶的認證，可自動內此受管理的網域。
- Azure AD 網域服務、 Contoso 的受管理的網域是因為 IT 系統管理員沒有此網域的網域管理員或企業系統管理員權限。


### <a name="azure-ad-domain-services-for-hybrid-organizations"></a>Azure AD 網域服務的混合式組織
混合式 IT 基礎架構的組織使用的雲端資源與內部部署資源。 這類組織進行同步處理至其 Azure AD 租用戶的身分識別資訊從他們的內部部署目錄。 當混合式組織想要更移轉至雲端，特別是舊版目錄注意應用程式，其內部部署應用程式的 Azure AD 網域服務會有幫助它們。

Litware 公司已部署[Azure AD Connect](../active-directory/active-directory-aadconnect.md)，同步處理至其 Azure AD 租用戶的身分識別資訊從他們的內部部署目錄。 同步處理的身分識別資訊包括使用者帳戶、 認證雜湊驗證 （密碼同步處理） 和群組的成員資格。

> [AZURE.NOTE] **密碼同步處理對是必要的混合式組織使用 Azure AD 網域服務**。 這是因為 Azure AD 網域服務，提供受管理的網域中需要使用者的憑證驗證這些使用者透過 NTLM 或 Kerberos 驗證方法。

![Azure AD 網域服務 Litware Corporation](./media/active-directory-domain-services-overview/aadds-overview-synced-tenant.png)

如上圖會顯示如何混合式 IT 基礎結構，例如 Litware 公司的組織使用 Azure AD 網域服務。 Azure 基礎結構服務中的虛擬網路中部署 Litware 的應用程式，並要求網域服務的伺服器工作量。 Litware 的 IT 系統管理員可以啟用其 Azure AD 租用戶的 Azure AD 網域服務，並選擇 [在這個虛擬網路中提供的受管理的網域。 Litware 為組織中與混合式部署的 IT 基礎結構，使用者帳戶、 群組和認證會同步處理至其 Azure AD 租用戶從他們的內部部署目錄。 此功能可讓使用者登入網域使用其公司認證集，例如，當網域透過遠端桌面連線遠端電腦加入。 系統管理員可以提供存取中使用現有群組的成員資格的網域的資源。 部署虛擬網路上的虛擬機器中的應用程式可以使用功能，例如網域加入、 LDAP 讀取、 LDAP 繫結、 NTLM 和 Kerberos 驗證，以及群組原則。

已佈建 Azure AD 網域服務的受管理網域的幾個明顯方面如下所示︰

- 受管理的網域是獨立的網域。 沒有副檔名為 Litware 的內部部署的網域。
- Litware 的 IT 系統管理員不需要管理，修補程式，或監視網域控制站此受管理的網域。
- 有不需要管理 AD 複寫到這個網域。 Azure ad 透過 Azure AD Connect 會同步處理使用者帳戶與群組成員資格，Litware 的內部部署目錄中的認證。 這些使用者帳戶，群組成員資格，與認證，可自動內的受管理的網域。
- Azure AD 網域服務、 Litware 的受管理的網域是因為 IT 系統管理員沒有此網域的網域管理員或企業系統管理員權限。


## <a name="benefits"></a>優點
Azure AD 網域服務與您享受下列優點︰

-   **簡單**– 您可以滿足身分識別的需求虛擬機器部署簡單按幾下 Azure 基礎結構服務。 您不需要部署及管理的身分識別基礎結構中 Azure 或設定連線到您的內部部署識別基礎結構。

-   Azure AD 租用戶深層整合**整合**– Azure AD 網域服務。 您現在可以使用 Azure AD caters 現代應用程式與傳統目錄注意應用程式的需求的整合雲端企業目錄。

-   **相容**– Azure AD 網域服務內建的基礎經過驗證的企業成績的 Windows Server Active Directory。 因此，您的應用程式可以依賴更大的 Windows Server Active Directory 功能與相容性]。 Windows Server AD 中可用的並非所有功能，都可目前 Azure AD 網域服務中。 不過，可用的功能會與您在內部部署基礎結構依賴相對應的 Windows Server AD 功能相容。 LDAP、 Kerberos、 NTLM、 群組原則，以及網域加入功能構成成熟提供的測試並精簡透過各種不同的 Windows Server 版本。

-   **效益**– 使用 Azure AD 網域服務中，您可以避免基礎架構與管理負擔與管理的身分識別基礎結構支援傳統目錄注意應用程式相關聯。 您可以將 Azure 基礎結構服務這些應用程式，然後受益更大的存款在費用。
