<properties
   pageTitle="Azure AD Connect︰ 自動升級 |Microsoft Azure"
   description="本主題說明 Azure AD Connect 同步處理的內建自動升級功能。"
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/24/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-automatic-upgrade"></a>Azure AD Connect︰ 自動升級
建立 1.1.105.0 （發行年 2 月 2016年） 引進這項功能。

## <a name="overview"></a>概觀
確保您 Azure AD Connect 安裝永遠最新狀態就是這麼簡單使用**自動升級**功能。 此功能的快速安裝的預設會啟用並 DirSync 升級。 釋放新版本後，會自動升級您的安裝。

依下列項目預設會啟用自動升級︰

- 快速設定安裝及 DirSync 升級。
- 使用 SQL Express LocalDB，這是快速設定一律使用。 使用 SQL Express DirSync 也使用 LocalDB。
- AD 帳戶是快速設定] 和 [DirSync 所建立的預設 MSOL_ 帳戶。
- 斷必須小於 100000 物件。

使用 PowerShell 指令程式可檢視目前狀態的自動升級`Get-ADSyncAutoUpgrade`。 有下列狀態︰

狀態 | 註解
---- | ----
啟用 | 自動升級為啟用。
暫停 | 由系統僅限設定。 系統已不再收到自動升級資格。
停用 | 自動升級會停用。

您可以變更**已啟用**和**停用**與之間`Set-ADSyncAutoUpgrade`。 只有系統應該設定的狀態**已擱置 」 下方**。

自動升級使用的 Azure AD 連線狀況版升級的基礎結構。 搭配使用的自動升級，請確定您有 Url 中開啟您的 proxy 伺服器**Azure AD 連線**運作情況如詳加說明[Office 365 Url 與 IP 位址範圍](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)。

如果在伺服器上執行**同步處理服務管理員**使用者介面，然後升級暫停，直到關閉在 ui。

## <a name="troubleshooting"></a>疑難排解
如果您連線的安裝就不會升級本身如預期般，然後依照下列步驟，找出哪些可能的錯誤。

首先，您應該不會自動升級到嘗試新版的第一天。 如果無法立即升級您的安裝，不要擔心，嘗試升級前，有刻意的隨機。

如果您認為有不正確，然後第一次執行`Get-ADSyncAutoUpgrade`確定已啟用自動升級。

接著，請確定在您的 proxy 或防火牆，您已經開啟所需的 Url。 自動更新使用的 Azure AD 連線狀況[概觀](#overview)所述。 如果您使用 proxy，請確定狀況已設定為使用[proxy 伺服器](active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)。 Azure AD[狀況連線](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)測試。

驗證 Azure ad 連線，則查詢其他事件日誌的時間。 啟動 [事件檢視器]，並查看**應用程式**的事件記錄檔。 新增來源**Azure AD 連線升級**的事件記錄檔篩選器和事件識別碼範圍**300 399**。  
![自動升級的事件記錄檔篩選](./media/active-directory-aadconnect-feature-automatic-upgrade/eventlogfilter.png)  

您現在可以看到其他自動升級的狀態相關聯的事件日誌。  
![自動升級的事件記錄檔篩選](./media/active-directory-aadconnect-feature-automatic-upgrade/eventlogresult.png)  

結果程式碼具有前置詞與狀態的概觀。

結果碼前置詞 | 描述
--- | ---
成功 | 安裝已順利升級。
UpgradeAborted | 暫時停止升級。 它會重試一次，預期的成功更新版本。
UpgradeNotSupported | 系統會有封鎖從自動升級系統的設定。 它會重試若要查看狀態會變更，並預期系統必須手動升級。

以下是最常見的訊息，您找到的清單。 不會列出所有項目，但結果，郵件應該清除有什麼問題。

結果訊息 | 描述
--- | ---
**UpgradeAborted** |
UpgradeAbortedCouldNotSetUpgradeMarker | 無法寫入登錄機碼。
UpgradeAbortedInsufficientDatabasePermissions | 內建的系統管理員群組沒有資料庫的權限。 手動升級至最新版 Azure AD Connect 來解決這個問題。
UpgradeAbortedInsufficientDiskSpace | 不需要光碟空間不足，無法支援升級。
UpgradeAbortedSecurityGroupsNotPresent | 可能不會尋找並解決同步處理引擎使用的所有安全性群組。
UpgradeAbortedServiceCanNotBeStarted | **Microsoft Azure AD Sync** NT 服務無法啟動。
UpgradeAbortedServiceCanNotBeStopped | 若要停止，NT 服務**Microsoft Azure AD Sync**失敗。
UpgradeAbortedServiceIsNotRunning | **Microsoft Azure AD Sync** NT 服務不在執行中。
UpgradeAbortedSyncCycleDisabled | 已停用[排程器](active-directory-aadconnectsync-feature-scheduler.md)中的 [SyncCycle] 選項。
UpgradeAbortedSyncExeInUse | [同步處理服務管理員使用者介面](active-directory-aadconnectsync-service-manager-ui.md)是在伺服器上開啟。
UpgradeAbortedSyncOrConfigurationInProgress | 執行安裝精靈或外部排程器排程同步處理。
**UpgradeNotSupported** |
UpgradeNotSupportedCustomizedSyncRules | 您已設定新增您自己的自訂規則。
UpgradeNotSupportedDeviceWritebackEnabled | 您已啟用[裝置回寫](active-directory-aadconnect-feature-device-writeback.md)功能。
UpgradeNotSupportedGroupWritebackEnabled | 您已啟用[群組回寫](active-directory-aadconnect-feature-preview.md#group-writeback)功能。
UpgradeNotSupportedInvalidPersistedState | 安裝無法快速設定] 或 [DirSync 升級。
UpgradeNotSupportedMetaverseSizeExceeeded | 您可以有多個 100000 物件斷。
UpgradeNotSupportedMultiForestSetup | 您連線到多個樹系。 快速設定只會連線到一個樹系。
UpgradeNotSupportedNonLocalDbInstall | 您不使用 SQL Server Express LocalDB 資料庫。
UpgradeNotSupportedNonMsolAccount | [AD 連接器帳戶](active-directory-aadconnect-accounts-permissions.md#active-directory-account)不是預設 MSOL_ 帳戶再。
UpgradeNotSupportedStagingModeEnabled | 伺服器設定為在[臨時模式](active-directory-aadconnectsync-operations.md#staging-mode)。
UpgradeNotSupportedUserWritebackEnabled | 您已啟用[使用者回寫](active-directory-aadconnect-feature-preview.md#user-writeback)功能。

## <a name="next-steps"></a>後續步驟
進一步瞭解[整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)。
