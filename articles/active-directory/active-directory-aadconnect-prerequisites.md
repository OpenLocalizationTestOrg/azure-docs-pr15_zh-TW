<properties
   pageTitle="Azure AD Connect︰ 先決條件與硬體 |Microsoft Azure"
   description="本主題說明的必要條件和 Azure AD Connect 的硬體需求"
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="10/12/2016"
   ms.author="billmath"/>

# <a name="prerequisites-for-azure-ad-connect"></a>必要條件 Azure AD 連線
本主題說明的必要條件和 Azure AD Connect 的硬體需求。

## <a name="before-you-install-azure-ad-connect"></a>安裝 Azure AD Connect 之前
Azure AD Connect 安裝之前，有幾個您需要的項目。

### <a name="azure-ad"></a>Azure AD
- Azure 訂閱或[Azure 試用訂閱](https://azure.microsoft.com/pricing/free-trial/)。 這只是需要存取 Azure 入口網站而不使用 Azure AD Connect。 如果您使用 PowerShell 或 Office 365 您不需要使用 Azure AD Connect Azure 訂閱。 如果您有 Office 365 授權，您也可以使用 Office 365 入口網站。 與付費的 Office 365 授權您也可以取得 Azure 入口網站將從 Office 365 入口網站。
    - 您也可以使用 Azure AD 預覽功能在[Azure 入口網站](https://portal.azure.com)。 此入口網站時，不需要 Azure 的授權。
- [新增及驗證網域](active-directory-add-domain.md)您打算在 Azure AD 中使用。 例如，如果您打算使用 contoso.com 為您的使用者，然後確定已驗證網域，然後您使用的不只 contoso.onmicrosoft.com 預設網域。
- Azure AD 租用戶允許預設 50 k 物件。 當您驗證您的網域時，限制會增加 300 k 物件。 如果您需要更多物件中 Azure AD 必須先開啟支援案例以增加更進一步限制。 如果您需要多個 500 k 物件您需要的授權，例如 Office 365、 Azure AD 基本、 Azure AD 進階版或企業版行動性套件。

### <a name="prepare-your-on-premises-data"></a>準備您的內部部署資料
- 檢閱[選擇性同步處理功能，您可以在 Azure AD 啟動](active-directory-aadconnectsyncservice-features.md)和評估您應該啟用的功能。

### <a name="on-premises-servers-and-environment"></a>內部部署伺服器和環境
- AD 結構描述版本和森林功能層級必須是 Windows Server 2003 或更新版本。 網域控制站，只要符合結構描述和森林層級的需求，可以執行任何版本。
- 如果您打算使用**密碼回寫**功能的網域控制站必須 （使用最新的預存程序） 的 Windows Server 2008 或更新版本。 如果您的 Dc 是 2008 (前 R2)，那麼您也必須套用[hotfix KB2386717](http://support.microsoft.com/kb/2386717)。
- Azure AD 所使用的網域控制站必須是可寫入。 不支援使用 RODC （唯讀網域控制站） 和 Azure AD Connect 不符合任何寫入重新導向。
- Azure AD Connect 無法在小型企業版伺服器或 Windows Server 基本版安裝。 伺服器必須使用標準或更佳的 Windows Server。
- Azure AD Connect 伺服器，必須具備完整 GUI 安裝。 不支援在核心伺服器上安裝。
- Azure AD Connect 必須安裝在 Windows Server 2008 或更新版本。 此伺服器可能在網域控制站或成員伺服器如果使用快速設定。 如果您使用自訂設定，也可以是獨立伺服器，並不需要加入網域。
- 如果您在 Windows Server 2008 安裝 Azure AD Connect，確認 Windows Update 套用最新的快速修正程式。 若要開始與未更新的伺服器無法安裝。
- 如果您打算使用**密碼同步處理**功能，Azure AD Connect 伺服器必須於 Windows Server 2008 R2 SP1 或更新版本。
- Azure AD Connect 伺服器必須[.NET Framework 4.5.1](#component-prerequisites)或更新版本及[Microsoft PowerShell 3.0](#component-prerequisites)安裝或更新版本。
- 安裝 AD FS 或 Web 應用程式 Proxy 伺服器部署 Active Directory Federation Services 時，必須是 Windows Server 2012 R2 或更新版本。 [Windows 遠端管理](#windows-remote-management)必須啟用這些遠端安裝的伺服器上。
- 部署 Active Directory Federation Services 時，您會需要[SSL 憑證](#ssl-certificate-requirements)。
- 部署 Active Directory Federation Services 時，您需要設定[名稱解析](#name-resolution-for-federation-servers)。
- Azure AD Connect 需要 SQL Server 資料庫儲存身分識別的資料。 根據預設會安裝 SQL Server 2012 Express LocalDB （精簡版本的 SQL Server Express） 和服務的服務帳戶會建立本機電腦上。 SQL Server Express 有可讓您管理大約 100000 物件的 10 GB 大小限制。 如果您需要管理目錄物件調高音量，必須先指向不同的安裝 SQL server 安裝精靈。
- 如果您使用不同的 SQL Server，然後套用這些需求︰
    - Azure AD Connect 支援 SQL Server 2008 （含 SP4) 從 SQL Server 2014 的 Microsoft SQL Server 的所有的類別。 Microsoft Azure SQL 資料庫做為**不支援**的資料庫。
    - 您必須使用不區分大小寫的 SQL 定序。 這些用來識別\_CI_ 在他們的名稱。 則**不支援**使用區分大小寫編碼選擇，以識別\_CS_ 在他們的名稱。
    - 您只能有一個同步處理引擎，每個資料庫執行個體。 這是與 FIM/MIM 同步處理、 DirSync 或 Azure AD Sync 共用資料庫執行個體的 [**不受支援**。

### <a name="accounts"></a>帳戶
- 您想要與整合 Azure AD 目錄的 Azure AD 全域管理員帳戶。 這必須是**學校或組織帳戶**，而且無法以**Microsoft 帳戶**。
- 如果您使用快速設定，或從 DirSync 升級，您必須適用於您的本機 Active Directory 擁有企業系統管理員帳戶。
- [在 Active Directory 中的帳戶](./connect/active-directory-aadconnect-accounts-permissions.md)如果您使用的自訂設定安裝路徑。

### <a name="azure-ad-connect-server-configuration"></a>Azure AD Connect 伺服器設定
- 如果您的全域管理員有啟用 MFA，URL **https://secure.aadcdn.microsoftonline-p.com**必須在信任的網站清單中。 系統會提示您如果系統提示您輸入 MFA 挑戰之前，就不會新增它新增到信任的網站清單。 您可以使用 Internet Explorer，將其新增至信任的網站。

### <a name="connectivity"></a>連線
- Azure AD Connect 伺服器需要 DNS 解析內部網路和網際網路兩者均適用。 您必須能夠同時您內部部署的 Active Directory 以及 Azure AD 結束點名稱解析 DNS 伺服器。
- 如果在您的內部網路有防火牆，必須先開啟 Azure AD Connect 伺服器與您的網域控制站之間的連接埠，請參閱[Azure AD 連線連接埠](active-directory-aadconnect-ports.md)，如需詳細資訊。
- 如果必須開啟您 proxy 或防火牆限制的可存取的 Url，然後將記錄在[Office 365 Url 與 IP 位址範圍](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)的 Url。
    - 如果您使用的 Microsoft 雲端德國或 Microsoft Azure 政府雲端，請參閱[Azure AD Connect 同步處理服務執行個體考量](active-directory-aadconnect-instances.md)的 Url。
- Azure AD Connect 是使用 TLS 1.0 Azure AD 與通訊的預設值。 您可以變更此 TLS 1.2 來[啟用 TLS 1.2 版 Azure AD Connect](#enable-tls-12-for-azure-ad-connect)中的步驟進行。
- 如果您使用連外的 proxy 連線至網際網路，必須安裝精靈和 Azure AD Connect 同步處理，都能連線至網際網路和 Azure AD 加入**C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config**檔案中的下列設定。 輸入文字時，必須底部的檔案。 在這個程式碼， &lt;PROXYADRESS&gt;代表實際的 proxy IP 位址或主機名稱。

```
    <system.net>
        <defaultProxy>
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

- 如果您的 proxy 伺服器需要驗證，然後[服務帳戶](./connect/active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-accounts)必須放在網域]，並指定[自訂的服務帳戶](./connect/active-directory-aadconnect-get-started-custom.md#install-required-components)必須使用自訂的設定安裝路徑。 您也需要 machine.config 其他變更。 使用此 machine.config 安裝精靈中的變更和同步處理引擎回應驗證要求 proxy 伺服器。 在所有安裝精靈會使用頁面、 排除 [**設定**] 頁面，登入使用者的認證。 結尾的 [安裝] 精靈的 [**設定**] 頁面上的內容切換至 [[服務帳戶](./connect/active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-accounts)由您所建立的。 Machine.config 區段應該如下所示。

```
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

如需[預設 proxy 項目的](https://msdn.microsoft.com/library/kd3cf2ex.aspx)相關資訊，請參閱 msdn 文章。

如果您有連線問題，請參閱[疑難排解連線問題](active-directory-aadconnect-troubleshoot-connectivity.md)。

### <a name="other"></a>其他
- 可省略︰ 測試使用者帳戶以確認同步處理。

## <a name="component-prerequisites"></a>元件的先決條件

### <a name="powershell-and-net-framework"></a>PowerShell 和.Net Framework
Azure AD Connect 取決於 Microsoft PowerShell 和.NET Framework 4.5.1。 您需要此版本或更新版本伺服器上安裝。 根據您的 Windows Server 版本，請執行下列動作︰

- Windows Server 2012R2
  - 預設會安裝 Microsoft PowerShell，不不需要任何動作。
  - .NET framework 4.5.1 與後續發行版本可能會提供透過 Windows Update。 請確定您已經安裝最新的更新到 [控制台] 中的 Windows Server。
- Windows Server 2008R2 和 Windows Server 2012
  - 最新版本的 Microsoft PowerShell 適用於**Windows 管理架構 4.0**，可在[Microsoft 下載中心](http://www.microsoft.com/downloads)上使用。
  - .NET framework 4.5.1 及更新版本，可在[Microsoft 下載中心](http://www.microsoft.com/downloads)上。
- Windows Server 2008
  - 使用**Windows 管理 Framework 3.0**，可在[Microsoft 下載中心](http://www.microsoft.com/downloads)上使用 PowerShell 的最新的受支援的版本。
 - .NET framework 4.5.1 及更新版本，可在[Microsoft 下載中心](http://www.microsoft.com/downloads)上。

### <a name="enable-tls-12-for-azure-ad-connect"></a>啟用 TLS 1.2 的 Azure AD Connect
Azure AD Connect 使用 TLS 1.0 依預設為加密同步引擎伺服器和 Azure AD 之間的通訊。 您可以變更此設定預設的伺服器上使用 TLS 1.2.Net 應用程式。 [Microsoft 安全性提示 2960358](https://technet.microsoft.com/security/advisory/2960358)中找 TLS 1.2 的詳細資訊。

1. 無法在 Windows Server 2008 啟用 TLS 1.2。 您需要 Windows Server 2008R2 或更新版本。 請確定您有安裝適用於您的作業系統的.Net 4.5.1 hotfix，請參閱[Microsoft 安全性提示 2960358](https://technet.microsoft.com/security/advisory/2960358)。 您可能需要這或已安裝在伺服器上的新版本。
2. 如果您使用 Windows Server 2008R2，然後確認已啟用 TLS 1.2。 在 Windows Server 2012 伺服器及更新版本，應該已啟用 TLS 1.2。
```
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
```
3. 對於所有的作業系統，設定下列登錄機碼並重新啟動伺服器。
```
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
"SchUseStrongCrypto"=dword:00000001
```
4. 如果您也想要啟用 TLS 1.2 同步處理引擎伺服器與遠端的 SQL Server 之間，然後確定您有必要的[TLS 1.2 支援 Microsoft SQL Server](https://support.microsoft.com/kb/3135244)安裝的版本。

## <a name="prerequisites-for-federation-installation-and-configuration"></a>同盟安裝與設定的先決條件

### <a name="windows-remote-management"></a>Windows 遠端管理
使用時 Azure AD Connect 部署 Active Directory Federation Services 或 Web 應用程式 Proxy，核取下列需求，以確保連線，然後將成功設定︰

- 如果目標伺服器加入網域，請確定已啟用 [Windows 遠端管理
    - 在提高權限 PSH 命令] 視窗中，使用命令`Enable-PSRemoting –force`
- 如果目標伺服器是非網域連結的 WAP 電腦，有幾個額外的需求
    - 在目標電腦 （WAP 電腦）︰
         - 確保 winrm (Windows 遠端管理 / Ws-management) 透過服務嵌入式管理單元執行服務
         - 在提高權限 PSH 命令] 視窗中，使用命令`Enable-PSRemoting –force`
    - 在電腦執行精靈 （如果目標電腦非網域聯結或不受信任的網域）︰
        - 在提高權限 PSH 命令] 視窗中，使用 [] 命令`Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
        - 在 [伺服器管理員︰
             - 新增至機器集區 DMZ WAP host (伺服器管理員]-> [管理...]-> [新增伺服器使用 [DNS] 索引標籤)
             - 伺服器管理員所有伺服器] 索引標籤︰ WAP 伺服器上按一下滑鼠右鍵並選擇 [管理存新檔...]，輸入本機 （非網域） 認證 WAP 機器
             - 若要驗證遠端 PSH 中的連線，伺服器管理員所有伺服器] 索引標籤︰ 以滑鼠右鍵按一下 WAP 伺服器，然後選擇 [Windows PowerShell。  遠端 PSH 工作階段應該開啟以確保可以建立遠端 PowerShell 工作階段。

### <a name="ssl-certificate-requirements"></a>SSL 憑證需求
**重要事項︰**強烈建議您 AD FS 伺服器陣列的所有節點，以及所有 Web 應用程式 proxy 伺服器上使用相同的 SSL 憑證。

- 憑證必須 X509 憑證。
- 您可以使用自我簽署的憑證實驗室環境中的同盟伺服器上。 不過，為生產環境中，我們建議您從公用 CA 取得憑證。
    - 如果使用的不是公開信任的憑證，請確定每個 Web 應用程式 Proxy 伺服器上安裝的憑證是本機伺服器上，在所有的同盟伺服器上受信任
- 身分識別的憑證必須符合同盟服務名稱 (例如，sts.contoso.com)。
    - 身分識別為 [主旨替代 （舊） 的副檔名類型 dNSName 或者的主體名稱如果沒有舊的項目，指定為一般名稱。  
    - 多個舊項目中可以存在憑證，提供一個符合同盟服務名稱。
    - 如果您打算使用地點加入，其他的舊就需要值**enterpriseregistration。** 後面再加上貴組織的使用者主要名稱 (UPN) 尾碼，例如**enterpriseregistration.contoso.com**。
- 不支援根據 CryptoAPI 下一個產生 (CNG) 鍵和金鑰儲存提供者的憑證。 這表示您必須使用根據 CSP （加密服務提供者） 和不 KSP （金鑰儲存提供者） 的憑證。
- 支援萬用字元憑證。

### <a name="name-resolution-for-federation-servers"></a>同盟伺服器的名稱解析
- 設定 AD FS 同盟服務名稱 (例如 sts.contoso.com) 網路 （內部 DNS 伺服器） 與外部網路 (公用 DNS 透過您的網域註冊機構) 的 DNS 記錄。 針對內部網路的 DNS 記錄確定您使用的記錄和不 CNAME 記錄。 這是必要的 windows 驗證從您網域的連接電腦正確運作。
- 如果您部署一個以上的 AD FS server 或 Web 應用程式 Proxy 伺服器，請確定您已設定您的負載平衡器與 AD FS 同盟服務名稱 (例如 sts.contoso.com) 的 DNS 記錄指向負載平衡器。
- Windows 整合式驗證合作，以在您的內部網路中使用 Internet Explorer 的瀏覽器應用程式，請確定 AD FS 同盟服務名稱 (例如 sts.contoso.com) 會新增至 IE 的內部網路區域。 這是透過群組原則控制及部署到所有加入網域電腦。

## <a name="azure-ad-connect-supporting-components"></a>支援元件 azure AD Connect
以下是安裝 Azure AD Connect 伺服器安裝 Azure AD Connect 的元件的清單。 這是清單基本 Express 安裝。  如果您選擇要使用不同的 SQL Server 安裝同步處理服務] 頁面上，然後 SQL Express LocalDB 沒有安裝至本機。

- Azure AD Connect 健康狀況
- Microsoft Online Services 登入小幫手 IT 專業人員 （但不依存安裝）
- Microsoft SQL Server 2012 命令列公用程式
- Microsoft SQL Server 2012 快速 LocalDB
- Microsoft SQL Server 2012 原生用戶端
- Microsoft Visual c + + 2013年轉散發套件

## <a name="hardware-requirements-for-azure-ad-connect"></a>Azure AD Connect 的硬體需求
下表顯示 Azure AD Connect 同步處理電腦的最低需求。

| Active Directory 中的物件數目 | CPU | 記憶體 | 硬碟大小 |
| ------------------------------------- | --- | ------ | --------------- |
| 不超過 10000 | 1.6 GHz | 4 GB | 70 GB |
| 10000 – 50000 | 1.6 GHz | 4 GB | 70 GB |
| 50000-100000 | 1.6 GHz | 16 GB | 100 GB |
| 100000 或多個物件完整版的 SQL Server 資格|  |  |  |
| 100000 – 300000 | 1.6 GHz | 32 GB | 300 GB |
| 300000 – 600000 | 1.6 GHz | 32 GB | 450 GB |
| 多個 600000 | 1.6 GHz | 32 GB | 500 GB |

AD FS 或 Web 應用程式伺服器電腦的最低需求如下︰

- CPU︰ 雙核心 1.6 GHz 或更高版本
- 記憶體︰ 2 GB 或更高版本
- Azure VM: A2 設定或更新版本

## <a name="next-steps"></a>後續步驟
進一步瞭解[整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)。
