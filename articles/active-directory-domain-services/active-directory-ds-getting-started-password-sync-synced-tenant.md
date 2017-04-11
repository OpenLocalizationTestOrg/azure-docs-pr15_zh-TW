<properties
    pageTitle="Azure AD 網域服務︰ 啟用密碼同步處理 |Microsoft Azure"
    description="快速入門 Azure Active Directory 網域服務"
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
    ms.topic="get-started-article"
    ms.date="09/20/2016"
    ms.author="maheshu"/>

# <a name="enable-password-synchronization-to-azure-ad-domain-services"></a>啟用密碼同步處理至 Azure AD 網域服務
上述的基本工作，您可以啟用 Azure AD 租用戶的 Azure AD 網域服務。 下一步是啟用同步處理的密碼，Azure AD 網域服務。 認證同步處理設定時，使用者可以為受管理的網域使用其公司的認證登入。

相關的步驟有不同根據您的組織是否有雲端專用的 Azure AD 租用戶，或設為與使用 Azure AD Connect 您內部部署目錄同步處理。

<br>

> [AZURE.SELECTOR]
- [雲端專用 Azure AD 租用戶](active-directory-ds-getting-started-password-sync.md)
- [同步處理 Azure AD 租用戶](active-directory-ds-getting-started-password-sync-synced-tenant.md)

<br>


## <a name="task-5-enable-password-synchronization-to-aad-domain-services-for-a-synced-azure-ad-tenant"></a>任務 5︰ 啟用密碼同步處理至 AAD 網域服務的同步處理的 Azure AD 租用戶
A 同步處理 Azure AD 租用戶設定與使用 Azure AD Connect 貴組織的內部部署目錄同步處理。 Azure AD Connect 不同步處理 NTLM 及 Kerberos 認證預設 Azure AD 雜湊。 若要使用 Azure AD 網域服務，您需要設定同步處理 NTLM 和 Kerberos 驗證所需的認證雜湊 Azure AD Connect。 以下步驟啟用同步處理至您的 Azure AD 租用戶必要的認證雜湊。


### <a name="install-or-update-azure-ad-connect"></a>安裝或更新 Azure AD Connect
Azure AD Connect 的最新建議的版本的電腦上安裝網域連接。 如果您有現有的執行個體 Azure AD Connect 過程中，您需要更新以使用最新版 Azure AD Connect。 若要避免已知的問題/錯誤的可能已經修復，請確定您使用最新版 Azure AD Connect。

**[下載 Azure AD 連線](http://www.microsoft.com/download/details.aspx?id=47594)**

建議的版本︰ **1.1.281.0** -2016 年 9 月 7 日上發佈。

  > [AZURE.WARNING] 您必須安裝最新建議 Azure AD Connect，若要同步處理至您的 Azure AD 租用戶 （NTLM 和 Kerberos 驗證必填） 的舊密碼認證發行。 此功能不適用於 Azure AD Connect 或舊版 DirSync 工具的舊版本。

Azure AD Connect 的安裝指示可使用下列 「 文件-[快速入門 Azure AD Connect](../active-directory/active-directory-aadconnect.md)


### <a name="enable-synchronization-of-ntlm-and-kerberos-credential-hashes-to-azure-ad"></a>啟用同步處理至 Azure AD NTLM 和 Kerberos 認證雜湊
在每個 AD 樹系，若要強制完整密碼同步處理，執行下列 PowerShell 指令碼，並啟用同步處理到您的 Azure AD 租用戶的所有內部部署使用者的認證雜湊。 這個指令碼啟用 NTLM/Kerberos 驗證，無法同步處理至您的 Azure AD 租用戶所需的認證雜湊。

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"  
$azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"  
Import-Module adsync  
$c = Get-ADSyncConnector -Name $adConnector  
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1  
$c.GlobalParameters.Remove($p.Name)  
$c.GlobalParameters.Add($p)  
$c = Add-ADSyncConnector -Connector $c  
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false   
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true  
```

根據您的目錄的大小 (數字的使用者、 群組等等)，Azure AD 認證雜湊同步處理的時間。 密碼會出現可用在 Azure AD 網域服務管理網域的認證雜湊 Azure ad 同步處理後，引進了。


<br>

## <a name="related-content"></a>相關的內容

- [啟用密碼同步處理 AAD 網域服務的雲端專用 Azure AD 目錄](active-directory-ds-getting-started-password-sync.md)

- [管理 Azure AD 網域服務管理的網域](active-directory-ds-admin-guide-administer-domain.md)

- [加入 Azure AD 網域服務受管理網域的 Windows 虛擬機器](active-directory-ds-admin-guide-join-windows-vm.md)

- [紅色角色企業 Linux 虛擬機器加入 Azure AD 網域服務受管理網域](active-directory-ds-admin-guide-join-rhel-linux-vm.md)
