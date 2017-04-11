<properties
    pageTitle="Azure Active Directory 網域服務︰ 部署案例 |Microsoft Azure"
    description="Azure AD 網域服務的部署案例"
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
    ms.date="09/21/2016"
    ms.author="maheshu"/>


# <a name="deployment-scenarios-and-use-cases"></a>部署案例和使用案例
在此區段中，我們看看幾個案例與受益 Azure Active Directory (AD) 網域服務的使用案例。

## <a name="secure-easy-administration-of-azure-virtual-machines"></a>保護，輕鬆地管理 Azure 虛擬機器
您可以使用 Azure Active Directory 網域服務管理 Azure 虛擬機器精簡的方式。 Azure 虛擬機器可以加入受管理的網域，因此讓您可以使用您的公司 AD 認證登入。 這種方法可協助避免認證管理相關問題，例如保持在每一個 Azure 虛擬機器上的本機系統管理員帳戶。

加入受管理的網域的伺服器虛擬機器也能管理和使用群組原則保護。 您可以用於 Azure 虛擬機器中的所需的安全性比較基準，而且地鎖定根據公司安全性指導方針的。 例如，您可以使用的群組原則管理功能來限制可以啟動這些虛擬機器上的應用程式類型。

![精簡的系統管理的 Azure 虛擬機器](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

當伺服器和其他基礎結構達到結束的生活，Contoso 會移動目前裝載於內部部署至雲端的許多應用程式。 目前 IT 標準規定裝載公司的應用程式的伺服器，必須是網域及受管理的使用群組原則。 Contoso IT 管理員會優先網域加入虛擬機器中 Azure 部署輕鬆地管理。 如此一來，系統管理員和使用者可以使用認證登入其公司。 同時，可以設定機器遵守使用群組原則的所需的安全性比較基準。 Contoso 不想對部署、 監控和管理 Azure 安全性 Azure 虛擬機器中的網域控制站。 因此，Azure AD 網域服務會是此使用案例非常適合。

**部署備忘稿**

請考慮此部署案例的下列重點︰

- Azure AD 網域服務所提供的受管理的網域依預設提供單一二維結構 OU （組織單位）。 網域的所有電腦都位於單一一般 OU。 不過，您可以選擇建立自訂 Ou。

- Azure AD 網域服務中的表單的內建 GPO 每個使用者和電腦支援簡單的群組原則容器。 您無法目標 OU/部門 GP、 執行 WMI 篩選，或建立自訂 Gpo。

- Azure AD 網域服務支援基底 AD 電腦物件結構描述。 您無法延伸電腦物件的結構描述。


## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-bind-authentication-to-azure-infrastructure-services"></a>增益 shift 使用 LDAP 繫結驗證 Azure 基礎結構服務的內部部署應用程式

![LDAP 連結](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Contoso 有許多幾年前從 ISV 所購買的內部部署應用程式。 應用程式目前 ISV 所進行的維修作業模式中，而要求應用程式的變更是極為昂貴 contoso。 這個應用程式有網頁的 frontend 收集使用者認證使用 web 表單，然後執行的 LDAP 繫結到公司的 Active Directory 驗證使用者。 Contoso 想要移轉 Azure 基礎結構服務此應用程式。 會應用程式運作時，而不需要任何變更。 此外，使用者才能使用其現有的公司認證進行驗證，而不必重新訓練使用者做法。 換句話說，使用者應明顯的應用程式執行的位置，移轉應變為透明，它們。

**部署備忘稿**

請考慮此部署案例的下列重點︰

- 請確定應用程式不需要修改寫的目錄。 不支援 Azure AD 網域服務所提供的受管理網域的 LDAP 寫入權限。

- 您無法變更密碼直接對受管理的網域。 使用者可以變更他們的密碼請使用 Azure AD 自助密碼變更機制或針對內部部署目錄。 這些變更會是自動同步處理，並可在管理網域]。


## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-read-to-access-the-directory-to-azure-infrastructure-services"></a>增益 shift 存取 Azure 基礎結構服務目錄閱讀使用 LDAP 的內部部署應用程式
Contoso 具有所開發幾乎在十年前的內部部署線條商務 (LOB) 應用程式。 這個應用程式目錄注意，而設計為與 Windows Server AD 搭配使用。 應用程式會使用 Active Directory 讀取資訊/屬性使用者 LDAP （輕量型目錄存取通訊協定）。 應用程式不修改屬性或否則寫入的目錄。 Contoso 想要將此應用程式 Azure 基礎結構服務來移轉淘汰過時內部部署硬體目前主控這個應用程式。 無法使用現代目錄 Api，例如其餘型 Azure AD 圖形 API 重寫應用程式。 因此，增益 shift 選項需，可以將應用程式移轉到執行在雲端，而不修改程式碼或重新寫入的應用程式。

**部署備忘稿**

請考慮此部署案例的下列重點︰

- 請確定應用程式不需要修改寫的目錄。 不支援 Azure AD 網域服務所提供的受管理網域的 LDAP 寫入權限。

- 請確定應用程式不需要自訂/延伸 Active Directory 結構描述。 Azure AD 網域服務中不支援架構延伸。


## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure-infrastructure-services"></a>移轉 Azure 基礎結構服務的內部部署服務或協助程式應用程式
部分應用程式包含多個層級，其中一個層需要執行經過驗證的來電至] 等資料庫層的後端層的位置。 作用中的目錄服務帳戶通常用於這些使用案例。 您可以增益 shift Azure 基礎結構服務，使用這些應用程式的身分識別需求 Azure AD 網域服務這類應用程式。 您可以選擇要使用的從您的內部部署目錄同步處理到 Azure AD 同一個服務帳戶。 或者，您可以先建立自訂的 OU，然後 OU，部署這類應用程式中建立不同的服務帳戶。

![使用 WIA 服務帳戶](./media/active-directory-domain-services-scenarios/wia-service-account.png)

Contoso 有自訂軟體保存庫應用程式，包括前端網頁、 SQL server，以及後端 FTP 伺服器。 Windows 整合式驗證服務帳戶用來驗證的 web 前端 FTP 伺服器。 Web 前端已設定為執行為服務帳戶。 後端伺服器設定為授權的服務帳戶的前端網頁的存取權。 Contoso 慣用不需要部署網域控制站虛擬機器中，將此應用程式 Azure 基礎結構服務雲端。 Contoso IT 系統管理員可以部署前端網頁與 SQL server Azure 虛擬機器 FTP 伺服器裝載的伺服器。 然後，這些機器連接至 Azure AD 網域服務受管理的網域。 然後，他們可以使用他們的內部部署目錄中相同的服務帳戶進行應用程式的驗證。 此服務帳戶同步處理至 Azure AD 網域服務受管理的網域，可供使用。

**部署備忘稿**

請考慮此部署案例的下列重點︰

- 請確定應用程式會使用驗證的使用者名稱與密碼。 Azure AD 網域服務不支援基礎的憑證/智慧卡驗證。

- 您無法變更密碼直接對受管理的網域。 使用者可以變更他們的密碼請使用 Azure AD 自助密碼變更機制或針對內部部署目錄。 這些變更會是自動同步處理，並可在管理網域]。


## <a name="azure-remoteapp"></a>Azure RemoteApp
Azure RemoteApp 可讓 Contoso 的系統管理員，以建立網域的集合。 此功能可讓遠端應用程式由 Azure RemoteApp 網域的電腦上執行，以及存取其他使用 Windows 整合式驗證的資源。 Contoso 可以使用 Azure AD 網域服務，提供受管理的網域使用 Azure RemoteApp 網域的集合。

![Azure RemoteApp](./media/active-directory-domain-services-scenarios/azure-remoteapp.png)

如需此部署案例的詳細資訊，請參閱遠端桌面服務部落格文章標題[增益 shift Azure RemoteApp Azure AD 網域服務與您負載](http://blogs.msdn.com/b/rds/archive/2016/01/19/lift-and-shift-your-workloads-with-azure-remoteapp-and-azure-ad-domain-services.aspx)。
