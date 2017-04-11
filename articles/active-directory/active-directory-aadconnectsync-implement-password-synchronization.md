<properties
    pageTitle="實作 Azure AD Connect 同步處理的密碼同步處理 |Microsoft Azure"
    description="提供有關密碼同步處理如何運作，以及如何啟用此資訊。"
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
    ms.date="08/24/2016"
    ms.author="markusvi;andkjell"/>


# <a name="implementing-password-synchronization-with-azure-ad-connect-sync"></a>Azure AD Connect 同步處理的實作密碼同步處理
本主題提供您要同步處理您的使用者密碼從內部部署 Active Directory (AD) 到雲端 Azure Active Directory (Azure AD) 所需的資訊。

## <a name="what-is-password-synchronization"></a>什麼是密碼同步處理
機率您已封鎖來自因為忘記的密碼完成工作相關的不同您要記住的密碼。 更多的密碼，您需要請記住，忘了一個較高的機率。 問題和密碼重設和其他密碼相關問題的相關的通話要求大部分的支援資源。

密碼同步處理是同步處理到雲端 Azure Active Directory (Azure AD) 的使用者密碼從內部部署 Active Directory 的功能。
此功能可讓您登入 （例如 Office 365、 Microsoft Intune、 CRM Online 和 Azure AD 網域服務） 的 Azure Active Directory 服務使用您用於登入您的內部部署 Active Directory 相同的密碼。

![什麼是 Azure AD Connect](./media/active-directory-aadconnectsync-implement-password-synchronization/arch1.png)

密碼同步處理來減少需要維護只要您的使用者的密碼的數目，可協助您︰

- 提高生產力的使用者
- 減少您技術服務的成本  

此外，如果您選取要使用[**AD FS 同盟**](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect)，您可以選擇性地啟用密碼同步處理當成備份以防 AD FS 基礎結構失敗。

密碼同步處理是實作 Azure AD Connect 同步處理的目錄同步處理功能的延伸。 若要使用密碼同步處理您的環境中，您需要︰

- 安裝 Azure AD 連線  
- 設定目錄同步處理您的內部部署之間 AD 與您的 Azure Active Directory
- 啟用密碼同步處理

如需詳細資訊，請參閱[整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)

> [AZURE.NOTE] 如需 Active Directory 網域服務所設定的 FIPS 和密碼同步處理的詳細資訊，請參閱[密碼同步處理和 FIPS](#password-synchronization-and-fips)。

## <a name="how-password-synchronization-works"></a>密碼同步處理如何運作
Active Directory 網域服務會儲存在表單中的實際使用者的密碼雜湊值表示密碼。 雜湊值是單向數學函數 (「*雜湊演算法*」) 的結果。 沒有轉換為純文字版本密碼的單向函數的結果的方法。 您無法登入您的內部部署網路使用密碼雜湊。

若要同步處理您的密碼，Azure AD Connect 同步處理從內部部署的 Active Directory 中擷取您的密碼雜湊。 額外的安全性處理會套用至密碼雜湊之前 Azure Active Directory 驗證服務同步處理。 密碼會同步處理，每個使用者的方式，並依時間順序。

實際資料密碼同步處理程序的流程很類似的同步處理的使用者資料，例如顯示名稱或電子郵件地址。 不過，密碼同步處理超過其他屬性的標準目錄同步處理視窗。 密碼同步處理程序執行每 2 分鐘。 您無法修改此程序的頻率。 當您同步處理密碼時，它會覆寫現有的雲端密碼。

第一次，啟用密碼同步處理功能，它所執行的範圍內的所有使用者的密碼的初始同步處理。 您無法明確定義您想要同步處理的使用者密碼的子集合。

當您變更了內部部署的密碼時，更新的密碼同步處理時，通常在幾分鐘。
密碼同步處理功能自動重試失敗的同步處理嘗試。 如果在嘗試同步處理密碼時，發生錯誤，錯誤是身分登入您的事件檢視器中。

密碼同步處理目前登入的使用者造成任何影響。
當您登入雲端服務，就會發生同步處理的密碼變更立即不影響您目前的雲端服務工作階段。 不過，當雲端服務要求您再次進行驗證，您需要提供您的新密碼。

> [AZURE.NOTE] 在 Active Directory 中的物件類型使用者，才支援密碼同步處理。 不支援需要物件類型。

### <a name="how-password-synchronization-works-with-azure-ad-domain-services"></a>Azure AD 網域服務與密碼同步處理的運作方式
您也可以使用密碼同步處理功能來同步處理您的內部部署密碼[Azure AD 網域服務](../active-directory-domain-services/active-directory-ds-overview.md)。 這種情況可讓您在雲端中的使用者驗證方法可在您的內部部署中的所有方法 Azure AD 網域服務 AD。 這種情況的體驗方式類似於內部部署環境中使用 Active Directory 移轉工具 (ADMT)。

### <a name="security-considerations"></a>安全性考量
同步處理的密碼，您的密碼的純文字版本不會公開密碼同步處理功能，Azure AD，或任何相關聯的服務。

此外，沒有在內部部署的 Active Directory 需求以反轉的加密格式儲存的密碼。 用於傳輸之間內部部署的 Active Directory 密碼雜湊摘要 AD 與 Azure Active Directory。 密碼雜湊的摘要不會用來存取您的內部部署環境中的資源。

### <a name="password-policy-considerations"></a>密碼原則考量
有兩種類型的受啟用密碼同步處理的密碼原則︰

1. 密碼複雜度原則
2. 密碼過期原則

**密碼複雜度原則**  
當您啟用密碼同步處理時，在您的內部部署 Active Directory 中的密碼複雜度原則會覆寫複雜度原則，在已同步處理的使用者的雲端。 您可以使用所有有效的密碼，您的內部部署 Active directory 存取 Azure AD 服務。

> [AZURE.NOTE] 建立直接在雲端中的使用者的密碼是密碼原則主旨，仍在雲端所定義。

**密碼過期原則**  
如果使用者位於密碼同步處理的範圍，就會的雲端帳戶密碼設定為 「*永不過期*」。
您可以繼續使用同步處理的密碼已到期內部部署環境中的雲端服務登入。 雲端密碼會更新的下次當您變更內部部署環境中的密碼。

### <a name="overwriting-synchronized-passwords"></a>覆寫同步處理的密碼
系統管理員可以手動重設您使用 Windows PowerShell 的密碼。

在此情況下，新密碼覆寫您已同步處理的密碼，並定義在雲端中的所有密碼原則會都套用至新的密碼。

如果您變更您內部部署的密碼，新密碼同步處理至雲端，並覆寫手動更新的密碼。

## <a name="enabling-password-synchronization"></a>啟用密碼同步處理
自動啟用密碼同步處理，當您安裝 Azure AD Connect 使用**快速設定**。 如需詳細資訊，請參閱[快速入門 Azure AD Connect 使用快速設定](./connect/active-directory-aadconnect-get-started-express.md)。

如果您使用自訂設定，當您安裝 Azure AD Connect 時，您會啟用在使用者登入頁面上的密碼同步處理。 如需詳細資訊，請參閱[Azure AD Connect 自訂安裝](./connect/active-directory-aadconnect-get-started-custom.md)。

![啟用密碼同步處理](./media/active-directory-aadconnectsync-implement-password-synchronization/usersignin.png)

### <a name="password-synchronization-and-fips"></a>密碼同步處理和 FIPS
如果您的伺服器已鎖定根據美國聯邦資訊處理標準 (FIPS)，然後 MD5 已停用。

**若要啟用 MD5 密碼同步處理，請執行下列步驟︰**

1. 移至**%programfiles%\Azure AD Sync\Bin**。
2. 開啟**miiserver.exe.config**。
3. 移至 [**設定/runtime**節點 （結尾的檔案）。
4. 新增下列節點︰`<enforceFIPSPolicy enabled="false"/>`
5. 儲存變更。

參照的此剪取是如何看起來就像︰

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

安全性及 FIPS 相關資訊，請參閱[AAD 密碼同步處理、 加密和 FIPS 法規遵循](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/)

## <a name="troubleshooting-password-synchronization"></a>疑難排解密碼同步處理
如果預期，不會同步處理的密碼，它可以是使用者子集或所有使用者。

- 如果您有個別的物件發生問題，請參閱[疑難排解單一物件的沒有同步處理的密碼](#troubleshoot-one-object-that-is-not-synchronizing-passwords)。
- 如果您有問題沒有密碼同步處理的位置，請參閱[疑難排解問題沒有密碼同步處理的位置](#troubleshoot-issues-where-no-passwords-are-synchronized)。

### <a name="troubleshoot-one-object-that-is-not-synchronizing-passwords"></a>疑難排解沒有同步處理密碼的單一物件
檢閱物件的狀態，可以輕鬆地疑難排解密碼同步處理問題。

開始在**Active Directory 使用者和電腦**。 找出的使用者，並確認**使用者必須變更密碼在下次登入時**為未選取。
![Active Directory 生產力的密碼](./media/active-directory-aadconnectsync-implement-password-synchronization/adprodpassword.png)  
如果已選取，然後要求使用者登入，並變更密碼。 Azure ad 未同步處理臨時密碼。

如果看起來正確 Active Directory 中下, 一步就是追蹤使用者在同步處理引擎中。 依照使用者從內部部署的 Active Directory Azure ad，您可以看到的物件是否為描述性的錯誤。

1. 啟動**[同步處理服務管理員](active-directory-aadconnectsync-service-manager-ui.md)**]。
2. 按一下 [**連接器**]。
3. 選取使用者位於**Active Directory 連接器**。
4. 選取 [**搜尋連接器空間**]。
5. 找出您要尋找的使用者。
6. 選取 [**歷程**] 索引標籤，然後確認至少有一個同步處理規則會顯示為**True**的**密碼同步處理**]。 在預設設定，同步處理規則的名稱是**中從 AD-使用者 AccountEnabled**。  
    ![使用者的歷程資訊](./media/active-directory-aadconnectsync-implement-password-synchronization/cspasswordsync.png)  
7. 接著[遵循使用者](active-directory-aadconnectsync-service-manager-ui-connectors.md#follow-an-object-and-its-data-through-the-system)透過 Azure AD 連接器空間 metaverse。 連接器空間物件應有輸出規則設**為 True**的**密碼同步處理**。 在 [預設設定，同步處理規則的名稱會是**外出 AAD-使用者加入**。  
    ![使用者連接器空間屬性](./media/active-directory-aadconnectsync-implement-password-synchronization/cspasswordsync2.png)  
8. 若要查看密碼同步處理的詳細資料，物件的週，按一下 [**記錄...**]。  
    ![物件記錄詳細資料](./media/active-directory-aadconnectsync-implement-password-synchronization/csobjectlog.png)  

[狀態] 欄可能會有下列的值︰

狀態 | 描述
---- | -----
成功 | 已成功地同步處理的密碼。
FilteredByTarget | 密碼設定為**使用者必須變更密碼在下次登入時**。 密碼不同步處理。
NoTargetConnection | 物件沒有斷或 Azure AD 連接器空間。
SourceConnectorNotPresent | 在內部部署的 Active Directory 連接器空間中找到的物件。
TargetNotExportedToDirectory | Azure AD 連接器空間中的物件已不尚未匯出。
MigratedCheckDetailsForMoreInfo | 日誌項目建立 1.0.9125.0 之前所建立，並且會顯示在舊版的狀態。

### <a name="troubleshoot-issues-where-no-passwords-are-synchronized"></a>疑難排解的問題沒有密碼同步處理的位置
開始執行 [[取得的密碼同步處理設定](#get-the-status-of-password-sync-settings)] 區段中的指令碼。 它會讓您概略瞭解密碼同步處理設定。  
![PowerShell 指令碼的輸出從密碼同步處理設定](./media/active-directory-aadconnectsync-implement-password-synchronization/psverifyconfig.png)  
如果 Azure AD 中未啟用的功能，或者不會啟用同步處理頻道狀態，然後執行連線安裝精靈。 選取**自訂同步處理選項**，然後取消選取密碼同步處理]。 這項變更暫時停用的功能。 接著再次執行精靈，然後重新啟用密碼同步處理。 執行指令碼] 以確認設定正確無誤。

如果指令碼] 顯示的沒有任何活動訊號，然後執行[觸發程序完整的同步處理所有密碼](#trigger-a-full-sync-of-all-passwords)的指令碼。 這個指令碼也可以用於其他案例中，設定正確，但未同步處理的密碼。

#### <a name="get-the-status-of-password-sync-settings"></a>取得密碼同步處理設定的狀態

```
Import-Module ADSync
$connectors = Get-ADSyncConnector
$aadConnectors = $connectors | Where-Object {$_.SubType -eq "Windows Azure Active Directory (Microsoft)"}
$adConnectors = $connectors | Where-Object {$_.ConnectorTypeName -eq "AD"}
if ($aadConnectors -ne $null -and $adConnectors -ne $null)
{
    if ($aadConnectors.Count -eq 1)
    {
        $features = Get-ADSyncAADCompanyFeature -ConnectorName $aadConnectors[0].Name
        Write-Host
        Write-Host "Password sync feature enabled in your Azure AD directory: "  $features.PasswordHashSync
        foreach ($adConnector in $adConnectors)
        {
            Write-Host
            Write-Host "Password sync channel status BEGIN ------------------------------------------------------- "
            Write-Host
            Get-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector.Name
            Write-Host
            $pingEvents =
                Get-EventLog -LogName "Application" -Source "Directory Synchronization" -InstanceId 654  -After (Get-Date).AddHours(-3) |
                    Where-Object { $_.Message.ToUpperInvariant().Contains($adConnector.Identifier.ToString("D").ToUpperInvariant()) } |
                    Sort-Object { $_.Time } -Descending
            if ($pingEvents -ne $null)
            {
                Write-Host "Latest heart beat event (within last 3 hours). Time " $pingEvents[0].TimeWritten
            }
            else
            {
                Write-Warning "No ping event found within last 3 hours."
            }
            Write-Host
            Write-Host "Password sync channel status END ------------------------------------------------------- "
            Write-Host
        }
    }
    else
    {
        Write-Warning "More than one Azure AD Connectors found. Please update the script to use the appropriate Connector."
    }
}
Write-Host
if ($aadConnectors -eq $null)
{
    Write-Warning "No Azure AD Connector was found."
}
if ($adConnectors -eq $null)
{
    Write-Warning "No AD DS Connector was found."
}
Write-Host
```

#### <a name="trigger-a-full-sync-of-all-passwords"></a>觸發程序完整的同步處理所有密碼
您可以觸發完整的同步處理所有的密碼，請使用下列指令碼︰

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
$aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
Import-Module adsync
$c = Get-ADSyncConnector -Name $adConnector
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1
$c.GlobalParameters.Remove($p.Name)
$c.GlobalParameters.Add($p)
$c = Add-ADSyncConnector -Connector $c
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true
```

## <a name="next-steps"></a>後續步驟

* [Azure AD 連線同步處理︰ 自訂同步處理選項](active-directory-aadconnectsync-whatis.md)
* [整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)
