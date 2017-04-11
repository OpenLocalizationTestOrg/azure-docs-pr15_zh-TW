<properties
    pageTitle="Azure Active Directory 網域服務︰ 疑難排解指南 |Microsoft Azure"
    description="Azure AD 網域服務疑難排解指南"
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

# <a name="azure-ad-domain-services---troubleshooting-guide"></a>Azure AD 網域服務-疑難排解指南
本文提供設定或管理 Azure Active Directory (AD) 網域服務時，可能會遇到的問題的疑難排解提示。


## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>您無法啟用 Azure AD 目錄的 Azure AD 網域服務
本節可協助您解決錯誤，當您嘗試啟用 Azure AD 網域服務為您的目錄，失敗或取得切換回 [已停用 」。

挑選的疑難排解步驟會對應至發生的錯誤訊息。

|**錯誤訊息**|**解決方法**|
|---|:---|
|*名稱 contoso100.com 已在這個網路上的使用中。指定不是使用中的名稱。*|[在虛擬網路的網域名稱衝突](active-directory-ds-troubleshooting.md#domain-name-conflict)
|*無法在此 Azure AD 租用戶中啟用網域服務。服務沒有足夠的權限至應用程式稱為 「 Azure AD 網域服務同步處理]。刪除應用程式稱為 「 Azure AD 網域服務同步處理]，然後嘗試啟用 Azure AD 租用戶的網域服務。*|[網域服務沒有足夠的權限 Azure AD 網域服務同步處理應用程式](active-directory-ds-troubleshooting.md#inadequate-permissions)|
|*無法在此 Azure AD 租用戶中啟用網域服務。Azure AD 租用戶的網域服務應用程式沒有必要的權限，以啟用網域服務。刪除應用程式識別碼 d87dcbc6-a371-462e-88e3-28ad15ec4e64 的應用程式，然後嘗試啟用 Azure AD 租用戶的網域服務。*|[網域服務應用程式未妥善設定您的租用戶中](active-directory-ds-troubleshooting.md#invalid-configuration)|
|*無法在此 Azure AD 租用戶中啟用網域服務。Microsoft Azure AD 應用程式會在您 Azure AD 租用戶中停用。啟用與應用程式識別碼 00000002-0000-0000-c000-000000000000 的應用程式，然後嘗試啟用 Azure AD 租用戶的網域服務。*|[Azure AD 租用戶中停用的 Microsoft Graph 應用程式](active-directory-ds-troubleshooting.md#microsoft-graph-disabled)|


### <a name="domain-name-conflict"></a>網域名稱衝突
**錯誤訊息︰**

*名稱 contoso100.com 已在這個網路上的使用中。指定不是使用中的名稱。*

**修復︰**

請確定您沒有可用的虛擬網路上相同的網域名稱與現有的網域。 例如，假設您有稱為 'contoso.com' 已經使用所選的虛擬網路上的網域。 之後，您嘗試啟用 Azure AD 網域服務受管理的網域相同的網域名稱 (也就是 「 contoso.com 」) 的虛擬網路上。 嘗試啟用 Azure AD 網域服務時，就會發生錯誤。

此錯誤是因為名稱衝突的虛擬網路上的網域名稱。 在此情況下，您必須使用不同的名稱，設定讓您 Azure AD 網域服務受管理的網域。 或者，您可以取消佈建現有的網域，並前往啟用 Azure AD 網域服務。


### <a name="inadequate-permissions"></a>不適當的權限
**錯誤訊息︰**

*無法在此 Azure AD 租用戶中啟用網域服務。服務沒有足夠的權限至應用程式稱為 「 Azure AD 網域服務同步處理]。刪除應用程式稱為 「 Azure AD 網域服務同步處理]，然後嘗試啟用 Azure AD 租用戶的網域服務。*

**修復︰**

請檢查是否有名稱 「 Azure AD 網域服務同步處理' Azure AD 目錄中的應用程式。 如果此應用程式，將其刪除，並重新啟用 Azure AD 網域服務。

如果應用程式，請執行下列步驟以檢查存在的應用程式，並將它刪除:

  1. 瀏覽至**Azure 傳統入口網站**([https://manage.windowsazure.com](https://manage.windowsazure.com))。
  2. 選取在左窗格的 [ **Active Directory**節點。
  3. 選取您要啟用 Azure AD 網域服務 Azure AD 租用戶 （目錄）。
  4. 瀏覽至 [**應用程式**] 索引標籤。
  5. 在下拉式清單中選取 [**我的公司所擁有的應用程式**] 選項。
  6. 檢查有稱為**Azure AD 網域服務同步處理**應用程式。 如果應用程式，請繼續將其刪除。
  7. 當您刪除應用程式時，請嘗試再次啟用 Azure AD 網域服務。


### <a name="invalid-configuration"></a>不正確的設定
**錯誤訊息︰**

*無法在此 Azure AD 租用戶中啟用網域服務。Azure AD 租用戶的網域服務應用程式沒有必要的權限，以啟用網域服務。刪除應用程式識別碼 d87dcbc6-a371-462e-88e3-28ad15ec4e64 的應用程式，然後嘗試啟用 Azure AD 租用戶的網域服務。*

**修復︰**

若要查看您是否有名稱 「 AzureActiveDirectoryDomainControllerServices 」 （含 d87dcbc6-a371-462e-88e3-28ad15ec4e64 的應用程式識別碼） 的應用程式 Azure AD 目錄中的核取。 如果此應用程式，必須先將其刪除，然後重新啟用 Azure AD 網域服務。

您可以使用下列 PowerShell 指令碼來尋找應用程式，並將其刪除。

> [AZURE.NOTE] 此指令碼使用**Azure AD PowerShell 版本 2**指令程式。 如需所有可用的 cmdlet 和下載模組的完整清單，請閱讀[AzureAD PowerShell 參考文件](https://msdn.microsoft.com/library/azure/mt757189.aspx)。

```
$InformationPreference = "Continue"
$WarningPreference = "Continue"

$aadDsSp = Get-AzureADServicePrincipal -Filter "AppId eq 'd87dcbc6-a371-462e-88e3-28ad15ec4e64'" -ErrorAction Ignore
if ($aadDsSp -ne $null)
{
    Write-Information "Found Azure AD Domain Services application. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $aadDsSp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services application."
}

$identifierUri = "https://sync.aaddc.activedirectory.windowsazure.com"
$appFilter = "IdentifierUris eq '" + $identifierUri + "'"
$app = Get-AzureADApplication -Filter $appFilter
if ($app -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync application. Deleting it ..."
    Remove-AzureADApplication -ObjectId $app.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync application."
}

$spFilter = "ServicePrincipalNames eq '" + $identifierUri + "'"
$sp = Get-AzureADServicePrincipal -Filter $spFilter
if ($sp -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync service principal. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $sp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync service principal."
}
```
<br>

### <a name="microsoft-graph-disabled"></a>停用的 Microsoft Graph
**錯誤訊息︰**

無法在此 Azure AD 租用戶中啟用網域服務。 Microsoft Azure AD 應用程式會在您 Azure AD 租用戶中停用。 啟用與應用程式識別碼 00000002-0000-0000-c000-000000000000 的應用程式，然後嘗試啟用 Azure AD 租用戶的網域服務。

**修復︰**

如果您已停用與識別項 00000002-0000-0000-c000-000000000000 應用程式，請參閱檢查。 這個應用程式是 Microsoft Azure AD 應用程式，並提供您 Azure AD 租用戶圖形 API 的存取權。 Azure AD 網域服務需要此應用程式來啟用同步處理您的 Azure AD 租用戶，您受管理的網域。

若要解決此錯誤，啟用這個應用程式，然後再次嘗試啟用 Azure AD 租用戶的網域服務。


## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>使用者將無法登入 Azure AD 網域服務管理網域
如果您 Azure AD 租用戶中的一或多位使用者無法登入新建立受管理的網域，請執行下列疑難排解步驟︰

- **登入使用 UPN 格式︰**嘗試登入使用 UPN 格式 (例如， 'joeuser@contoso.com') ，而不是 SAMAccountName 格式 (「 CONTOSO\joeuser 」)。 其 UPN 前置詞是過長，或是另一位使用者的受管理的網域上相同，SAMAccountName，可能會自動產生的使用者。 Azure AD 租用戶中是唯一的保證 UPN 格式。

> [AZURE.NOTE] 我們建議使用 UPN 格式登入 Azure AD 網域服務管理網域。

- 請確定您已[啟用密碼同步處理](active-directory-ds-getting-started-password-sync.md)根據的快速入門指南 》 中所述的步驟。

- **外部帳戶︰**確定受影響的使用者帳戶不是 Azure AD 租用戶中的外部帳戶。 外部帳戶的範例包括 Microsoft 帳戶 (例如，'joe@live.com')或外部使用者帳戶 Azure AD 目錄。 Azure AD 網域服務沒有這類使用者帳戶的認證，因為這些使用者無法登入的受管理的網域。

- **同步處理的帳戶︰**如果受影響的使用者帳戶從內部部署目錄同步處理，確認︰
    - 您可以部署或更新為[最新建議 Azure AD Connect 發行](active-directory-ds-getting-started-password-sync.md#install-or-update-azure-ad-connect)。

    - 您已設定 Azure AD Connect 執行[完整的同步處理](active-directory-ds-getting-started-password-sync.md)。

    - 根據您的目錄的大小，它可能需要使用者帳戶的一段時間，並認證雜湊能夠 Azure AD 網域服務中使用。 請確定您段前驗證 （根據您的目錄-幾個小時一天的時間，或兩個大目錄的大小） 等待。

    - 如果問題持續發生，確認上述步驟之後，請嘗試重新啟動 Microsoft Azure AD Sync 服務。 從電腦同步處理，啟動 [命令提示字元中，執行下列命令︰
      1. 網路停駐點 」 Microsoft Azure AD Sync 」
      2. 網路開始 「 Microsoft Azure AD Sync 」

- **雲端專用的帳戶**︰ 如果受影響的使用者帳戶是雲端專用的使用者帳戶，請確定 [使用者已變更他們的密碼之後您啟用 Azure AD 網域服務。 此步驟會導致產生的 Azure AD 網域服務所需的雜湊認證。


## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>從 Azure AD 租用戶中移除的使用者不會從您受管理的網域移除
Azure AD 可以讓您避免意外刪除的使用者物件。 當您從 Azure AD 租用戶刪除使用者帳戶時，請對應使用者物件會移至資源回收筒中。 當此刪除作業同步處理至您受管理的網域時，就會導致對應的使用者帳戶，若要標記為已停用。 此功能可協助您復原或更新版本取消刪除的使用者帳戶。

若要移除的使用者帳戶完全受管理的網域，刪除的使用者將會永久從 Azure AD 租用戶。 使用與移除 MsolUser PowerShell cmdlet '-RemoveFromRecycleBin 」 選項，此[MSDN 文章](https://msdn.microsoft.com/library/azure/dn194132.aspx)中所述。


## <a name="contact-us"></a>與我們連絡
連絡 Azure Active Directory 網域服務產品小組 [共用的意見反應或支援] (使用中-目錄-ds-連絡人-us.md)。
