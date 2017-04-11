<properties
    pageTitle="設定自動註冊 Windows 與 Azure Active Directory 網域裝置 |Microsoft Azure"
    description="設定您網域的 Windows 裝置註冊 Azure Active Directory 自動與火車。"
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="markvi"/>



# <a name="set-up-automatic-registration-of-windows-domain-joined-devices-with-azure-active-directory"></a>設定自動註冊的 Windows Azure Active Directory 網域的裝置

若要使用[Azure Active Directory 裝置型條件存取](active-directory-conditional-access.md)，您的 Windows 網域的電腦必須註冊與 Azure Active Directory (Azure AD)。 本文中，您可以瞭解為什麼您需要設定的 Windows Azure AD 您組織中使用網域的裝置註冊。

Azure AD 中使用條件的 access 提供下列優點︰

-   Azure AD 透過公司或學校帳戶的應用程式中的增強型單一登入 (SSO) 體驗
-   在裝置上企業漫遊的設定
-   商務用存取 Windows 市集
-   更強大的驗證，好方便您使用登入 Windows Hello

> [AZURE.NOTE] Windows 10 年 11 月更新提供 Azure AD，加強的使用者經驗的部分，但 Windows 10 週年紀念日更新完全支援的裝置型條件存取。 如需有關條件的存取權的詳細資訊，請參閱[Azure Active Directory 裝置型條件存取](active-directory-conditional-access.md)。 如需在工作場所和使用者如何註冊 Windows 10 裝置 Azure AD 與 Windows 10 裝置的詳細資訊，請參閱[企業版的 Windows 10︰ 工作使用裝置](active-directory-azureadjoin-windows10-devices-overview.md)。

您可以註冊某些舊版的 Windows，包括以下版本︰

-   Windows 8.1
-   Windows 7

如果您使用的桌面為 Windows Server 的電腦，您可以註冊這些平台︰

- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2


## <a name="prerequisites"></a>必要條件

自動註冊的網域的裝置，透過 Azure AD 主需求是最新版的 Azure Active Directory 連線 (Azure AD Connect)。

根據您部署 Azure AD Connect 的方式，您使用的是快速或自訂安裝或就地升級下列先決條件可能會有已自動設定︰

-   **服務中的連接點內部部署 Active Directory**。 Azure AD 租用戶資訊的電腦的探索的註冊 Azure AD。

-   **Active Directory Federation Services (AD FS) 發行轉換規則**。 電腦驗證上註冊 （適用於同盟設定）。

如果您組織中的某些裝置不是在 Windows 10 網域的裝置，請確定您執行下列工作︰

- Azure AD 中設定原則，讓使用者可以註冊裝置
- 設定多重因素驗證 AD FS 中有效替代方案的整合 Windows 驗證 (IWA)



## <a name="set-a-service-connection-point-for-discovery-of-the-azure-ad-tenant"></a>設定 Azure AD 租用戶的探索服務連接點

命名內容磁碟分割的位置，連接電腦網域樹系的設定檔中，必須有服務連接點物件。 服務連接點會保留探索資訊 Azure AD 租用戶的電腦登錄的位置。 在多樹系 Active Directory 設定中，所有的樹系的網域的電腦必須存在於服務連接點。

在下列位置 Active Directory 中設定服務連接點︰

    CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Configuration Naming Context]

> [AZURE.NOTE] 使用 Active Directory 網域名稱*example.com*，設定命名內容樹系是 CN = 設定，亦即 = 範例中，亦即 = com。

您可以使用下列 Windows PowerShell 指令碼的核取 Azure AD 租用戶物件及探索值。 （與您的設定命名內容取代範例中的設定命名內容）。

    $scp = New-Object System.DirectoryServices.DirectoryEntry;

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=example,DC=com";

    $scp.Keywords;

`$scp.Keywords`輸出顯示 Azure AD 租用戶資訊︰

    azureADName:microsoft.com

    azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47

如果不存在的服務連接點，則會建立 Azure AD Connect 伺服器上執行下列 PowerShell 指令碼︰

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;


> [AZURE.NOTE]當您執行`$aadAdminCred = Get-Credential`，使用格式*user@example.com***取得認證**] 對話方塊中的使用者名稱。  
> 當您執行初始化 ADSyncDomainJoinedComputerSync cmdlet 時，將 [*連接器帳戶名稱*] 取代 Active Directory 連接器帳戶中所使用的網域帳戶。  
> 指令程式會使用 Active Directory PowerShell 模組，請在網域控制站仰賴 Active Directory Web 服務。 在 Windows Server 2008 R2 及更新版本中的網域控制站支援 active Directory Web 服務。 在 Windows Server 2008 或舊版中的網域控制站，用透過 PowerShell 徵 API，來建立服務連接點，然後指派的關鍵字值。

## <a name="create-ad-fs-rules-for-instant-device-registration-in-federated-organizations"></a>建立同盟的組織中的 [立即裝置註冊 AD FS 規則

在同盟 Azure AD 設定裝置使用 AD FS （或內部部署同盟伺服器上） Azure AD 驗證。 然後，他們註冊 Azure Active Directory 裝置註冊服務 （Azure AD 裝置註冊服務）。

> [AZURE.NOTE] 在非聯盟設定中，使用者的密碼雜湊已同步處理至 Azure AD，以及 Windows 10 和 Windows Server 2016 的網域的電腦驗證 Azure AD 裝置註冊服務。 使用認證，使用者將內部部署電腦帳戶，將使用者驗證，這轉送至 Azure AD 透過 Azure AD Connect。 非 Windows 10 和 Windows Server 2016 電腦在非聯盟設定，您必須設定裝置為基礎的憑證授權單位，您組織中的選項。 如需詳細資訊，請參閱本文中的非 Windows 10 電腦的區段下載 Windows 安裝程式套件。

在 Windows 10 和 Windows Server 2016 的電腦上，Azure AD Connect 關聯的裝置物件中 Azure AD 內部部署電腦帳戶物件。 下列宣告必須存在期間驗證 Azure AD 裝置註冊服務]，以完成註冊，然後建立裝置物件︰

- http://schemas.microsoft.com/ws/2012/01/accounttype 包含 DJ 值，用來識別主要驗證網域的電腦。
- http://schemas.microsoft.com/identity/claims/onpremobjectguid 包含內部部署電腦帳戶的**objectguid 資訊**屬性的值。
- http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid 包含電腦的主要安全性識別碼信用，其會對應到內部部署電腦帳戶的**objectSid**屬性值。
- http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid 包含 Azure AD 使用信任從 AD FS 或從內部安全性 Token 服務 (STS) 發出的 token 值。 這是很重要的多樹系 Active Directory 設定。 在此組態中，可能會加入以外的連線到在 AD FS Azure AD 或內部部署 STS 樹系電腦。 AD fs 中，使用值 http://<*網域名稱*>/adfs/services/信任/，<*網域名稱*> 中 Azure AD 是經過驗證的網域名稱的位置。

若要建立這些規則以手動方式，AD FS 中使用下列 PowerShell 指令碼已連線至您的伺服器的工作階段。 Azure AD 中取代已驗證的組織的網域名稱的第一行。

> [AZURE.NOTE] 如果您不是您的內部部署同盟伺服器使用 AD FS，請遵循供應商的指示建立議題這些宣告的規則。

    $validatedDomain = "example.com"      # Replace example.com with your organization's validated domain name in Azure AD

    $rule1 = '@RuleName = "Issue object GUID"

        c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&

        c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]

        => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), query = ";objectguid;{0}", param = c2.Value);'

    $rule2 = '@RuleName = "Issue account type for domain-joined computers"

      c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]

      => issue(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "DJ");'

> [AZURE.NOTE] 如果您的環境的內部部署是單一樹系，請使用 [僅限前三個規則。 如果您的電腦位於不同的樹系個 Azure AD 與同步處理，或如果您使用同步處理設定中的替代名稱，您也必須包含剩餘的規則。

    $rule3 = '@RuleName = "Pass through primary SID"

      c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&

      c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]

      => issue(claim = c2);'

    $rule4 = '@RuleName = "Issue AccountType with the value User when it’s not a computer account"

      NOT EXISTS([Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"])

      => add(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "User");'

    $rule5 = '@RuleName = "Capture UPN when AccountType is User and issue the IssuerID"

      c1:[Type == "http://schemas.xmlsoap.org/claims/UPN"] &&

      c2:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "User"]

      => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c1.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));'

    $rule6 = '@RuleName = "Update issuer for DJ computer auth"

      c1:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"]

      => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = "http://'+$validatedDomain+'/adfs/services/trust/");'

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3 + $rule4 + $rule5 + $rule6

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString

> [AZURE.NOTE] 在 Windows 10 和 Windows Server 2016 網域的電腦進行驗證，請使用主控 AD FS 作用中 Ws-trust 端點 IWA。 請確定端點是作用中。 如果您使用的 Web 應用程式 Proxy，請確定此端點已發佈透過 proxy。 在 adfs/服務/信任/13/windowstransport 則端點。 若要檢查是否為作用中 AD FS 管理主控台前往**服務** > **結束點**。 如果您沒有使用 AD FS 您內部部署的同盟伺服器，請遵循供應商的指示，請務必對應的端點作用中。



## <a name="set-up-ad-fs-for-authentication-of-device-registration"></a>設定的裝置註冊驗證 AD FS

請確定 IWA 有效的替代方案裝置註冊 AD FS 中的多因素驗證設定。 若要這麼做，您需要有發行轉換經過驗證方法的規則。

1. 在 AD FS 管理主控台中，前往 [ **AD FS** > **信任關係** > **依賴廠商信任**。

2. Microsoft Office 365 身分識別的平台依賴廠商信任物件，以滑鼠右鍵按一下，然後選取 [**編輯宣告規則**。

3.  在 [**發佈轉換規則**] 索引標籤上選取 [**新增規則**]。

4.  在 [**宣告規則**範本] 清單中，選取 [**傳送宣告使用自訂規則**]。

5.  選取 [**下一步**]。

6.  在 [**宣告規則名稱**] 方塊中，輸入**驗證方法宣告規則**。

7.  在 [**宣告規則**] 方塊中，輸入此命令︰

    `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"]
    => issue(claim = c);`.

8. 在您同盟伺服器上，輸入這個 PowerShell 命令︰

    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

<*RPObjectName*> 是 Azure AD 依賴廠商信任物件的信賴廠商物件名稱。 這個物件通常稱為 「 *Microsoft Office 365 身分識別的平台*。



## <a name="deployment-and-rollout"></a>部署及首展

當您網域的電腦符合必要條件時，請他們的年齡可以註冊 Azure AD。

Azure AD 重新啟動裝置，或當使用者登入 Windows 時自動註冊 Windows 10 週年紀念日更新與 Windows Server 2016 網域的電腦。 加入網域的新電腦登錄 Azure AD 時重新啟動裝置之後網域聯結運算。

> [AZURE.NOTE] 設定 [範圍] 群組原則] 物件時，才會自動以 Azure AD 註冊 Windows 10 網域的電腦。

若要控制自動註冊 Windows 10 和 Windows Server 2016 網域的電腦的範圍，您可以使用群組原則物件。 若要執行非 Windows 10 網域的電腦自動註冊，您可以將 Windows Installer 套件部署您選取的電腦。

> [AZURE.NOTE] 首展控制項群組原則也會觸發註冊 Windows 8.1 網域的電腦。 您可以使用原則的註冊 Windows 8.1 網域的電腦。 或者，如果您的 Windows 版本，包括 Windows 7 或 Windows Server 版本，您可以使用 Windows Installer 套件註冊您所有的非 Windows 10 和 Windows Server 2016 電腦。

### <a name="create-a-group-policy-object-to-control-the-rollout-of-automatic-registration"></a>建立群組原則物件來控制自動註冊的首展

若要控制自動註冊的網域的電腦，使用 Azure AD 的範圍，您可以在您要註冊的電腦部署**註冊網域的電腦，以裝置**群組原則。 例如，您可以部署原則組織單位或安全性群組。

若要設定原則，請執行下列步驟︰

1. 開啟伺服器管理員]，然後移至 [**工具** > **群組原則管理**。

2. 前往 [網域] 節點對應到您要啟動的 Windows 10 或 Windows Server 2016 電腦自動登錄的網域。

3. 以滑鼠右鍵按一下**群組原則物件**]，然後選取 [**新增]**。

4. 輸入您的群組原則物件的名稱。 例如，*自動註冊 Azure ad*。 選取**[確定]**。

4. 以滑鼠右鍵按一下新的群組原則物件，然後按一下**編輯**。

5. 移至 [**電腦設定** > **原則** > **系統管理範本** > **Windows 元件** > **裝置註冊**。 **加入裝置為電腦的註冊網域**，請以滑鼠右鍵按一下，然後選取 [**編輯**]。

    > [AZURE.NOTE] 此群組原則範本已從舊版的群組原則管理主控台重新命名。 如果您使用的舊版主控台，請移至 [**電腦設定** > **原則** > **系統管理範本** > **Windows 元件** > **地點加入** > **自動地點加入用戶端電腦**。

6. 選取 [**啟用**]，然後選取 [**套用]**。

7. 選取**[確定]**。

8. 將群組原則物件連結到您所選擇的位置。 例如，您可以將其連結至特定的組織單位。 您也可以將其連結至特定的安全性群組的自動註冊 Azure AD 的電腦。 若要設定網域的 Windows 10 和 Windows Server 2016 中所有電腦貴組織的此原則，連結到網域的群組原則物件。

### <a name="download-windows-installer-packages-for-non-windows-10-computers"></a>下載非 Windows 10 的電腦上的 Windows Installer 套件  

若要註冊網域的電腦執行 Windows 8.1、 Windows 7、 Windows Server 2012 R2、 Windows Server 2012 或 Windows Server 2008 R2，您可以下載及安裝這些 Windows Installer 套件 (.msi) 檔案︰

- [x64](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x64.msi)
- [x86](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x86.msi)

使用軟體發佈系統等系統管理中心設定管理員部署套件。 封裝支援*安靜*的參數標準無訊息的安裝選項。 系統管理中心設定管理員 2016年提供額外的福利，從舊的版本，例如追蹤完成的註冊的能力。 如需詳細資訊，請參閱[系統管理中心 2016年](https://www.microsoft.com/en-us/cloud-platform/system-center)。

安裝程式會在使用者的內容中執行系統上建立排程的任務。 當使用者登入 Windows 時，會觸發工作。 任務火車註冊 Azure AD 使用使用者的認證，透過 IWA 驗證之後的裝置。 若要查看排程的任務，請移至**Microsoft** > **地點加入**，然後移至工作排程器程式庫。



## <a name="next-steps"></a>後續步驟

- [Azure Active Directory 條件的存取](active-directory-conditional-access.md)
