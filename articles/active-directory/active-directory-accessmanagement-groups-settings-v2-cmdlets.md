<properties
    pageTitle="Azure AD 中的群組管理的 azure Active Directory PowerShell 預覽 cmdlet |Microsoft Azure"
    description="本頁面提供的 PowerShell，協助您管理 Azure Active Directory 中的群組的範例"
    keywords="Azure AD，Azure Active Directory PowerShell 群組，群組管理"
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/29/2016"
    ms.author="curtand"/>

# <a name="azure-active-directory-preview-cmdlets-for-group-management"></a>Azure Active Directory 預覽 cmdlet 群組管理

> [AZURE.SELECTOR]
- [Azure 入口網站](active-directory-groups-create-azure-portal.md)
- [Azure 傳統入口網站](active-directory-accessmanagement-manage-groups.md)
- [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)

下列文件會提供您如何使用 PowerShell 來管理您的 Azure Active Directory (Azure AD) 群組的範例。  它也會提供如何使用 PowerShell 的 Azure AD 預覽模組取得設定的資訊。 首先，您必須[下載 PowerShell 的 Azure AD 模組](http://go.microsoft.com/fwlink/p/?LinkId=828627)。

## <a name="installing-the-azure-ad-powershell-module"></a>安裝 PowerShell 的 Azure AD 模組

若要安裝 AzureAD PowerShell 預覽模組，請使用下列命令︰

    PS C:\Windows\system32> install-module azureadpreview

若要確認已安裝的預覽模組，使用下列命令︰

    PS C:\Windows\system32> get-module azureadpreview

    ModuleType Version    Name                                ExportedCommands
    ---------- -------    ----                                ----------------
    Binary     1.1.146.0  azureadpreview                      {Add-AzureADAdministrati...}

現在您可以開始在模組中使用 cmdlet。 AzureAD 預覽模組中的指令程式的完整描述，請參閱[線上參考文件](https://msdn.microsoft.com/library/azure/mt757216.aspx)。

## <a name="connecting-to-the-directory"></a>連線至目錄
管理群組使用 PowerShell 的 Azure AD 預覽 cmdlet 前，您必須在您想要管理目錄連線您 PowerShell 工作階段。 若要這麼做，請使用下列命令︰

    PS C:\Windows\system32> Connect-AzureAD -Force

指令程式會提示您輸入您想要用來存取您的目錄的認證。 在此範例中，我們使用karen@drumkit.onmicrosoft.com存取示範目錄。 Cmdlet 會傳回以顯示工作階段已成功連線到您的目錄確認︰

    Account                       Environment Tenant
    -------                       ----------- ------
    Karen@drumkit.onmicrosoft.com AzureCloud  85b5ff1e-0402-400c-9e3c-0f…

現在您可以開始使用 AzureAD 預覽 cmdlet 來管理您的目錄中的群組。

## <a name="retrieving-groups"></a>擷取群組
若要從您的目錄中擷取現有的群組中，您可以使用取得 AzureADGroups 指令程式。 若要擷取的目錄中的所有群組，請使用 cmdlet 參數︰

    PS C:\Windows\system32> get-azureadgroup

Cmdlet 會傳回連線目錄中的所有群組。

您可以使用-objectID 參數以擷取您指定的群組 objectID 特定群組︰

    PS C:\Windows\system32> get-azureadgroup -ObjectId e29bae11-4ac0-450c-bc37-6dae8f3da61b

Cmdlet 現在會傳回其 objectID 符合您所輸入參數值的群組︰

    DeletionTimeStamp            :
    ObjectId                     : e29bae11-4ac0-450c-bc37-6dae8f3da61b
    ObjectType                   : Group
    Description                  :
    DirSyncEnabled               :
    DisplayName                  : Pacific NW Support
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 9bb4139b-60a1-434a-8c0d-7c1f8eee2df9
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

您可以搜尋特定群組使用-篩選參數。 這個參數會採用 ODATA 篩選子句，然後傳回符合篩選，如下列範例所示的所有群組︰

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

請注意，AzureAD PowerShell cmdlet 實作 OData 查詢標準，詳細資訊，請參閱[以下](https://msdn.microsoft.com/library/gg309461.aspx#BKMK_filter)。

## <a name="creating-groups"></a>建立群組
若要在目錄中建立新的群組，請使用新增 AzureADGroup 指令程式。 這個指令程式會建立名為 「 行銷 」 的新安全性群組︰

    PS C:\Windows\system32> New-AzureADGroup -Description "Marketing" -DisplayName "Marketing" -MailEnabled $false -SecurityEnabled $true -MailNickName "Marketing"

## <a name="updating-groups"></a>更新群組
若要更新現有的群組，請使用設定 AzureADGroup 指令程式。 在此範例中，我們要變更顯示名稱的屬性群組 」 Intune 管理員 」。 首先，我們正在尋找的群組，使用取得 AzureADGroup 指令程式，並使用的 DisplayName 屬性篩選︰

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

接下來，[描述] 屬性變更為新的值 」 Intune 裝置管理員 」:

    PS C:\Windows\system32> Set-AzureADGroup -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -Description "Intune Device Administrators"

現在我們看到如果再次找到] 群組中，[描述] 屬性會更新以反映新的值︰

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Device Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

## <a name="deleting-groups"></a>刪除群組
若要從您的目錄中刪除群組，請使用移除 AzureADGroup cmdlet，如下所示︰

    PS C:\Windows\system32> Remove-AzureADGroup -ObjectId b11ca53e-07cc-455d-9a89-1fe3ab24566b

## <a name="managing-members-of-groups"></a>管理群組的成員
如果您要新增成員至群組，請使用 [新增 AzureADGroupMember 指令程式。 這個命令加入我們在上一個範例中使用的 Intune 管理員群組的成員︰

    PS C:\Windows\system32> Add-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

-ObjectId 參數是我們要新增成員，群組的 ObjectID 而-RefObjectId ObjectID 我們想要新增成員至群組的使用者。

若要取得現有群組的成員，請使用取得 AzureADGroupMember cmdlet，範例如下︰

    PS C:\Windows\system32> Get-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                        72cd4bbd-2594-40a2-935c-016f3cfeeeea User
                        8120cc36-64b4-4080-a9e8-23aa98e8b34f User

若要移除我們先前已新增至群組的成員，請使用移除 AzureADGroupMember cmdlet，如此處所示︰

    PS C:\Windows\system32> Remove-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -MemberId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

若要驗證的使用者群組 membership(s)，使用 [選取 AzureADGroupIdsUserIsMemberOf 指令程式。 這個指令程式需要為其參數的使用者要檢查的群組成員資格，ObjectId 要檢查的成員資格的群組清單。 群組清單必須提供在表單中輸入 「 Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck 」 複數變數的因此我們先必須建立變數與類型︰

    PS C:\Windows\system32> $g = new-object Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck

接下來，我們會提供 groupIds 回屬性 「 GroupIds 」 這個複數變數的值︰

    PS C:\Windows\system32> $g.GroupIds = "b11ca53e-07cc-455d-9a89-1fe3ab24566b", "31f1ff6c-d48c-4f8a-b2e1-abca7fd399df"

現在，我們要檢查 ObjectID 72cd4bbd-2594-40a2-935c-016f3cfeeeea 針對 $g 中的群組與使用者的群組成員資格，如果我們應該使用︰

    PS C:\Windows\system32> Select-AzureADGroupIdsUserIsMemberOf -ObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea -GroupIdsForMembershipCheck $g

    OdataMetadata                                                                                               Value
    -------------                                                                                               -----
    https://graph.windows.net/85b5ff1e-0402-400c-9e3c-0f9e965325d1/$metadata#Collection(Edm.String)             {31f1ff6c-d48c-4f8a-b2e1-abca7fd399df}


傳回的值為此使用者是成員的群組清單。 您也可以套用此方法來檢查指定的群組]，使用 [選取 AzureADGroupIdsContactIsMemberOf、 選取 AzureADGroupIdsGroupIsMemberOf 或選取 AzureADGroupIdsServicePrincipalIsMemberOf 清單的連絡人、 群組或服務原則成員資格

## <a name="managing-owners-of-groups"></a>管理群組的擁有者
若要新增至群組的擁有者，請使用新增 AzureADGroupOwner 指令程式︰

    PS C:\Windows\system32> Add-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

-ObjectId 參數是我們要新增擁有者，群組的 ObjectID 且-RefObjectId 是我們想要新增群組的擁有者為使用者 ObjectID。

若要擷取群組的擁有者，請使用取得 AzureADGroupOwner:

    PS C:\Windows\system32> Get-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

Cmdlet 會傳回指定群組擁有者的清單︰

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                        e831b3fd-77c9-49c7-9fca-de43e109ef67 User

如果您想要從群組移除擁有者，請使用 [移除 AzureADGroupOwner:

    PS C:\Windows\system32> remove-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -OwnerId e831b3fd-77c9-49c7-9fca-de43e109ef67

## <a name="next-steps"></a>後續步驟

您可以在[Azure Active Directory Cmdlet](http://go.microsoft.com/fwlink/p/?LinkId=808260)，以找到更多的 Azure Active Directory PowerShell 文件。

* [管理資源的存取權與 Azure Active Directory 群組](active-directory-manage-groups.md)

* [整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)
