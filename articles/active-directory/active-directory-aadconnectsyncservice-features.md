<properties
    pageTitle="Azure AD Connect 同步處理服務功能和設定 |Microsoft Azure"
    description="說明服務側功能 Azure AD Connect 同步處理服務。"
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
    ms.date="08/22/2016"
    ms.author="andkjell;markvi"/>

# <a name="azure-ad-connect-sync-service-features"></a>Azure AD Connect 同步處理服務功能

Azure AD Connect 的同步處理功能有兩個元件︰

- 將內部部署的元件命名**Azure AD Connect 同步處理**，也稱為 [**同步處理引擎**。
- 服務位於 Azure AD 也稱為**Azure AD Connect 同步處理服務**

本主題說明下列**Azure AD Connect 同步處理服務**之功能的運作方式，並設定其使用 Windows PowerShell。

[Azure Active Directory 模組的 Windows PowerShell](http://aka.ms/aadposh)的這些設定。 下載並安裝分別從 Azure AD Connect。 在本主題中的指令程式所採用的[2016 年 3 月發行 （組建 9031.1）](http://social.technet.microsoft.com/wiki/contents/articles/28552.microsoft-azure-active-directory-powershell-module-version-release-history.aspx#Version_9031_1)。 如果您沒有在本主題中的指令程式，或不會產生相同的結果，請確認您執行最新版本。

若要查看 Azure AD 目錄中的設定，請執行`Get-MsolDirSyncFeatures`。  
![取得 MsolDirSyncFeatures 結果](./media/active-directory-aadconnectsyncservice-features/getmsoldirsyncfeatures.png)

這些設定許多只能由 Azure AD Connect 會變更。

可以設定下列設定`Set-MsolDirSyncFeature`:

DirSyncFeature | 註解
--- | ---
[DuplicateProxyAddressResiliency<br/>DuplicateUPNResiliency](#duplicate-attribute-resiliency) | 可讓被隔離時重複的另一個物件，而非匯出期間失敗整個物件屬性。
[EnableSoftMatchOnUpn](#userprincipalname-soft-match) | 可讓物件聯結 userPrincipalName 除了主要的 SMTP 位址。
[SynchronizeUpnForManagedUsers](#synchronize-userprincipalname-updates) | 允許同步處理引擎更新 userPrincipalName 屬性管理/授權 （非聯盟） 的使用者。

您已啟用功能之後，就無法停用一次。

>[AZURE.NOTE] 2016 年 8 月 24，*重複屬性恢復*功能預設新 Azure AD 目錄。 此功能也會導入並啟用這個日期之前所建立的目錄。 取得這項功能已啟用您的目錄時，您會收到電子郵件通知。

下列設定由 Azure AD Connect 設定，並無法修改`Set-MsolDirSyncFeature`:

DirSyncFeature | 註解
--- | ---
DeviceWriteback | [Azure AD Connect︰ 啟用裝置回寫](active-directory-aadconnect-feature-device-writeback.md)
DirectoryExtensions | [Azure AD Connect 同步處理︰ 目錄副檔名](active-directory-aadconnectsync-feature-directory-extensions.md)
PasswordSync | [Azure AD Connect 同步處理的實作密碼同步處理](active-directory-aadconnectsync-implement-password-synchronization.md)
UnifiedGroupWriteback | [預覽︰ 群組回寫](active-directory-aadconnect-feature-preview.md#group-writeback)
UserWriteback | 目前不支援。

## <a name="duplicate-attribute-resiliency"></a>重複屬性恢復功能
而不是進行佈建失敗物件 upn 重複，請使用/proxyAddresses，重複的屬性 「 隔離 」，並指派暫存的值。 當已解決衝突時，請暫時 UPN 會自動變更為正確的值。 可以針對 UPN 及 proxyAddress 分別啟用此行為。 如需詳細資訊，請參閱[身分識別同步處理和重複屬性恢復](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md)。

## <a name="userprincipalname-soft-match"></a>UserPrincipalName 柔相符項目
啟用此功能時，柔符合的項目已啟用 UPN 除了[主要的 SMTP 位址](https://support.microsoft.com/kb/2641663)，永遠啟用。 柔符合的項目用來與現有的雲端使用者中 Azure AD 與內部部署使用者。

如果您要符合的項目內部部署與現有帳戶在雲端建立 AD 帳戶您不使用 Exchange Online，然後這項功能很有用。 在此案例中，您通常不需要將 SMTP 屬性設定為在雲端的理由。

此功能是在預設新建立的 Azure AD 目錄。 您可以看到是否啟用此功能是為您執行︰  
```
Get-MsolDirSyncFeatures -Feature EnableSoftMatchOnUpn
```

如果這項功能未啟用 Azure AD 目錄，然後您可以啟用此執行︰  
```
Set-MsolDirSyncFeature -Feature EnableSoftMatchOnUpn -Enable $true
```

## <a name="synchronize-userprincipalname-updates"></a>同步處理 userPrincipalName 更新
以往 UserPrincipalName 屬性使用來自內部部署的同步處理服務的更新已封鎖，除非兩上述情況皆成立時︰

- 使用者管理 （非聯盟）。
- 使用者尚未指派的授權。

如需詳細資訊，請參閱[在 Office 365、 Azure 或 Intune 的使用者名稱不相符的內部部署 UPN 或其他登入 ID](https://support.microsoft.com/kb/2523192)。

啟用此功能可讓同步處理引擎是內部部署的變更，並使用密碼同步處理更新 userPrincipalName。 如果您使用同盟，不支援此功能。

此功能是在預設新建立的 Azure AD 目錄。 您可以看到是否啟用此功能是為您執行︰  
```
Get-MsolDirSyncFeatures -Feature SynchronizeUpnForManagedUsers
```

如果這項功能未啟用 Azure AD 目錄，然後您可以啟用此執行︰  
```
Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $true
```

啟用此功能之後, 現有 userPrincipalName 值仍為-是。 UserPrincipalName 屬性內部部署的下一個修訂，標準 delta 在同步處理的使用者就會更新 UPN。  

## <a name="see-also"></a>另請參閱

- [Azure AD Connect 同步處理](active-directory-aadconnectsync-whatis.md)
- [整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)。
