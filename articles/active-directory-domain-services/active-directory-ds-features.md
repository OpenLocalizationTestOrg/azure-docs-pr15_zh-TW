<properties
    pageTitle="Azure Active Directory 網域服務︰ 功能 |Microsoft Azure"
    description="Azure Active Directory 網域服務的功能"
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

## <a name="features"></a>功能
下列功能可在受管理的 Azure AD 網域服務的網域。

- **簡單的部署體驗︰**您可以啟用使用按幾下滑鼠 Azure AD 租用戶的 Azure AD 網域服務。 無論您 Azure AD 租用戶雲端租用戶是否與內部部署目錄同步處理，可以快速地提供您受管理的網域。

- **網域加入支援︰**您可以輕鬆地在您受管理的網域有 Azure 虛擬網路的網域加入電腦。 在 Windows 用戶端和伺服器作業系統運作完美對網域服務 Azure AD 網域服務的網域加入體驗。 您也可以使用自動的網域加入工具針對這類網域。

- **Azure AD 目錄每一個網域執行個體︰**您可以建立單一 Active Directory 網域的每個 Azure AD 目錄。

- **網域建立自訂的名稱︰**您可以建立自訂的名稱 (例如，「 contoso100.com 」) 的網域使用 Azure AD 網域服務。 您可以使用 [驗證或未驗證的網域名稱。 您也可以使用內建的網域後置字元建立網域的選擇性地 (也就是 「 *。 onmicrosoft.com 」) 所提供的 Azure AD 目錄。

- **與 Azure AD 整合︰**您不需要設定或管理 Azure AD 網域服務複寫。 使用者帳戶與群組成員資格，從 Azure AD 目錄的使用者認證 （密碼），可自動 Azure AD 網域服務中。 新的使用者、 群組或變更屬性從 Azure AD 租用戶或您的內部部署目錄會自動同步處理至 Azure AD 網域服務。

- **NTLM 和 Kerberos 驗證︰**支援 NTLM 和 Kerberos 驗證，您可以部署依賴 Windows 整合式驗證應用程式。

- **使用您公司的認證/密碼︰**Azure AD 網域服務與搭配使用 Azure AD 租用戶的使用者的密碼。 使用者可以使用其公司認證網域加入機器、 互動的方式，或透過遠端桌面，登入及受管理的網域驗證。

- **LDAP 繫結與 LDAP 閱讀支援︰**您可以使用 LDAP 連結，以驗證網域服務的 Azure AD 網域服務中的使用者所依賴的應用程式。 此外，從目錄使用查詢使用者/電腦屬性的 LDAP 讀取的作業應用程式，也可以使用 Azure AD 網域服務。

- **安全 LDAP (LDAPS):**您可以透過安全 LDAP (LDAPS) 啟用目錄的存取權。 使用預設的虛擬網路中安全的 LDAP 存取。 不過，您可以透過網際網路也可以選擇啟用安全的 LDAP 存取。

- **群組原則︰**您可以使用單一內建 GPO 每個使用者和電腦的使用者帳戶和網域的電腦強制執行規範與容器所需的安全性原則。

- **管理 DNS:**「 AAD DC 管理員 」 群組的成員可以管理使用熟悉的 DNS 管理工具，例如 [DNS 管理 MMC 嵌入式管理單元受管理網域的 DNS。

- **建立自訂的組織單位 (Ou):**「 AAD DC 管理員 」 群組的成員可以建立自訂 Ou 在管理網域]。 這些使用者，讓他們可以新增或移除服務帳戶、 電腦、 群組內這些自訂 Ou 等自訂 ou，取得完整的管理權限。

- **在多個 Azure 區域內的可用︰**請參閱知道 Azure 區域 Azure AD 網域服務提供 [[依地區 Azure 服務](https://azure.microsoft.com/regions/#services/)] 頁面。

- **可用性︰**Azure AD 網域服務可提供您網域的可用性。 此功能可提供較高的服務執行時間和恢復失敗的保證。 內建的健康監視優惠自動化從失敗補救旋轉設定新的執行個體，若要取代失敗，並提供您網域的連續的服務。

- **使用熟悉的管理工具︰**您可以使用熟悉的 Windows Server Active Directory 管理工具，例如 Active Directory 管理中心或 Active Directory PowerShell 來管理受管理的網域。
