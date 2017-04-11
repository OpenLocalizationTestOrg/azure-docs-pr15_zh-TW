<properties
    pageTitle="Azure ad 連線網域的裝置的 Windows 10 體驗 |Microsoft Azure"
    description="說明系統管理員可以列印文件的設定，請啟用裝置網域加入至企業網路的群組原則。"
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""
    tags="azure-classic-portal"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="connect-domain-joined-devices-to-azure-ad-for-windows-10-experiences"></a>加入網域的裝置連接到 Windows 10 體驗 Azure AD

網域聯結是傳統方式組織已連線的前 15 年和其他項目的工作的裝置。 已啟用使用者登入他們的裝置使用 Windows Server Active Directory (Active Directory) 工作或學校帳戶，並允許完整管理這些裝置的 IT。 組織通常是使用影像的方法來提供給使用者的裝置與通常使用系統管理中心設定管理員 (SCCM) 或群組原則加以管理。

在 Windows 10 中的網域加入裝置連接到 Azure Active Directory (Azure AD) 之後，會提供下列好處︰

- 單一登入 (SSO) Azure AD 資源從任何位置
- 存取 Windows 市集企業使用公司或學校帳戶 （不需要 Microsoft 帳戶）
- 企業相容漫遊的使用者設定在裝置上使用公司或學校帳戶 （不需要 Microsoft 帳戶）
- 強式驗證，好方便您用於登入 Microsoft 密碼和 Windows Hello 的公司或學校帳戶
- 若要限制存取只遵守組織裝置的群組原則設定的裝置的能力

## <a name="prerequisites"></a>必要條件

網域加入繼續很有用。 不過，若要取得 Azure AD SSO 優點，以公司或學校帳戶漫遊設定與公司或學校帳戶及存取的 Windows 市集，您將需要下列動作︰

- Azure AD 訂閱
- 若要將內部部署目錄延伸到 Azure AD azure AD Connect
- Azure ad 連線網域的裝置已設定的原則
- 在 Windows 10 建立的裝置 （建立 10551 或更新版本）

若要啟用 Microsoft 密碼工時與 Windows Hello，您將也需要下列動作︰

- 公開金鑰基礎結構 (PKI) 的使用者憑證。
- 系統管理中心設定管理員版本 1509 Technical Preview 版。 如需詳細資訊，請參閱[Microsoft 系統管理中心設定管理員 Technical Preview](https://technet.microsoft.com/library/dn965439.aspx#BKMK_TP3Update)和[系統管理中心設定 Manager 團隊部落格](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx)。 這被需要部署使用者憑證根據 Microsoft 密碼索引鍵。

PKI 部署需求，或者，您可以執行下列動作︰

- 有一些網域控制站與 Windows Server 2016 Active Directory 網域服務。

若要啟用條件的存取，您可以建立群組原則設定網域的任何其他部署裝置存取。 若要管理存取控制根據法規遵循的裝置，您將需要下列項目︰

- 系統管理中心設定管理員版 1509 Technical Preview 版密碼案例

## <a name="deployment-instructions"></a>部署指示



### <a name="step-1-deploy-azure-active-directory-connect"></a>步驟 1︰ 部署 Azure Active Directory 連線

Azure AD Connect 會讓您在雲端中的裝置物件為提供電腦內部部署。 若要部署 Azure AD Connect，請參閱[整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md#install-azure-ad-connect)文件中的 [安裝 Azure AD 連線]。

 - 如果您按照在[自訂安裝 Azure AD Connect](./connect/active-directory-aadconnect-get-started-custom.md) （不 Express 安裝），然後遵循稍後的此步驟的程序**服務連線指向內部部署的 Active Directory 中建立**、。
 - 如果您有同盟的設定與 Azure AD 之前安裝 Azure AD Connect （例如，如果您已經部署 Active Directory Federation Services (AD FS) 之前），然後遵循**設定 AD FS 宣告規則**稍後的程序，這個步驟。

#### <a name="create-a-service-connection-point-in-on-premises-active-directory"></a>在內部部署的 Active Directory 中建立服務連接點

加入網域的裝置會使用服務連接點來探索 Azure AD 租用戶時自動註冊 Azure 裝置註冊服務的資訊。

Azure AD Connect 在伺服器上，執行下列 PowerShell 命令︰

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;


執行 cmdlet $aadAdminCred 時 = 取得認證，使用格式*user@example.com*的認證，以取得認證快顯視窗出現時輸入使用者名稱。

執行 cmdlet 初始化 ADSyncDomainJoinedComputerSync...，會用來作為 Active Directory 連接器帳戶的網域帳戶取代 [*連接器帳戶名稱*]。

#### <a name="configure-ad-fs-claim-rules"></a>設定 AD FS 宣告規則
設定 AD FS 宣告規則藉由使用電腦來使用 Kerberos/NTLM 透過 AD FS 驗證啟用瞬間完成的效能註冊 Azure 裝置註冊服務的電腦。 此步驟中，不 Azure AD 會取得電腦的延遲的方式 （愛爾蘭 Azure AD Connect 同步處理的時間）。

>[AZURE.NOTE]
如果您沒有安裝 AD FS 同盟伺服器內部部署為，依照指示的廠商建立宣告規則。

AD FS server （或工作階段連線到伺服器，AD FS），請執行下列 PowerShell 命令︰

      <#----------------------------------------------------------------------
     |   Modify the Azure AD Relying Party to include the claims needed
     |   for DomainJoin++. The rules include:
     |   -ObjectGuid
     |   -AccountType
     |   -ObjectSid
     +---------------------------------------------------------------------#>

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules

    $rule1 = '@RuleName = "Issue object GUID"
          c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&
          c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
          => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), query = ";objectguid;{0}", param = c2.Value);'

    $rule2 = '@RuleName = "Issue account type for domain joined computers"
          c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
          => issue(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "DJ");'

    $rule3 = '@RuleName = "Pass through primary SID"
          c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&
          c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
          => issue(claim = c2);'

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString

>[AZURE.NOTE]
Windows 10 電腦會使用 Windows 整合式驗證使用 AD FS 所託管的作用中 Ws-trust 端點驗證。 確認已啟用此端點。 如果您使用的 Web 驗證 Proxy，也請確定此端點已發佈透過 proxy。 您可以檢查 adfs/服務/信任/13/windowstransport。 它會顯示為 AD FS 管理主控台下**服務**中啟用 > **結束點**。


### <a name="step-2-configure-automatic-device-registration-via-group-policy-in-active-directory"></a>步驟 2︰ 在 Active Directory 中設定自動裝置註冊透過群組原則

您可以使用 Active Directory 中的群組原則設定自動註冊 Azure AD 您在 Windows 10 網域的裝置。

> [AZURE.NOTE]
> 如何設定自動裝置註冊的最新指示，請參閱[如何設定 Windows 網域的自動註冊加入與 Azure Active Directory 的裝置](active-directory-conditional-access-automatic-device-registration-setup.md)。
>
> 在 Windows 10 中重新命名此群組原則範本。 如果您執行的從 Windows 10 電腦群組原則工具，原則會顯示為︰ <br>
> **裝置註冊加入網域的電腦**<br>
> 原則會在下列位置︰<br>
> ***電腦設定/原則/Windows 系統管理範本/元件/裝置註冊***


## <a name="additional-information"></a>其他資訊
* [企業版的 Windows 10︰ 裝置用於工作的方法](active-directory-azureadjoin-windows10-devices-overview.md)
* [延伸至 Windows 10 裝置，透過 Azure [Active Directory 加入雲端功能](active-directory-azureadjoin-user-upgrade.md)
* [瞭解有關使用案例的 Azure AD 加入](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [加入網域的裝置連接到 Windows 10 體驗 Azure AD](active-directory-azureadjoin-devices-group-policy.md)
* [設定 Azure AD 加入](active-directory-azureadjoin-setup.md)
