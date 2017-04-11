<properties
   pageTitle="Azure AD Connect 同步處理︰ 排程器 |Microsoft Azure"
   description="本主題說明 Azure AD Connect 同步處理的內建的排程器功能。"
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
   ms.date="08/04/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-sync-scheduler"></a>Azure AD Connect 同步處理︰ 排程器
本主題說明中 Azure AD Connect 同步處理的內建排程器 （又名 同步處理引擎）。

建立 1.1.105.0 （發行年 2 月 2016年） 引進這項功能。

## <a name="overview"></a>概觀
Azure AD Connect 同步處理會同步處理發生內部部署目錄使用的排程器中的變更。 有兩個排程器處理程序，一個用於密碼同步處理，另一個物件/屬性同步處理及維護工作。 本主題將涵蓋後者。

在舊版中的物件和屬性排程器外部同步引擎，要觸發在同步處理程序使用 Windows 工作排程器或個別的 Windows 服務。 同步處理至 1.1 版本內建功能排程器引擎，並且讓某些自訂動作。 新的預設同步處理頻率為 30 分鐘。

排程器負責兩項工作︰

- **同步處理循環**。 若要匯入、 同步及匯出變更程序。
- **維護工作**。 更新索引鍵和重設密碼和裝置註冊服務 (DRS) 的憑證。 清除舊的項目作業記錄檔中。

一律執行本身排程器，但可以將它設定為只執行下列一或無這些工作。 例如，如果您需要有自己的同步處理循環圖程序，您可以停用這項工作排程器中的，但仍可執行維護工作。

## <a name="scheduler-configuration"></a>排程器設定
若要查看您目前設定的設定，請移至 PowerShell 和執行`Get-ADSyncScheduler`。 隨即會顯示您應該像這樣︰

![GetSyncScheduler](./media/active-directory-aadconnectsync-feature-scheduler/getsynccyclesettings.png)

如果您看到**的同步處理命令或指令程式會無法使用**這個指令程式中執行時，就不會載入 PowerShell 模組。 如果您執行 Azure AD Connect 網域控制站或更高的 PowerShell 限制層級比預設設定伺服器，則會發生此問題。 如果您看到此錯誤，然後執行`Import-Module ADSync`若要使用 cmdlet。

- **AllowedSyncCycleInterval**。 最常 Azure AD 可讓發生同步處理。 您無法超過此同步處理，並仍受支援。
- **CurrentlyEffectiveSyncCycleInterval**。 作用中的目前排程。 會有 CustomizedSyncInterval 相同的值 (如果設定) 如果不是更頻繁 AllowedSyncInterval。 如果您變更 CustomizedSyncCycleInterval，這會在下次同步處理循環之後生效。
- **CustomizedSyncCycleInterval**。 如果您想執行任何其他頻率與預設值，30 分鐘排程器，會設定此設定。 在圖片上排程器具有已設為改為執行每小時。 如果您設定此值低於 AllowedSyncInterval，後者會使用。
- **NextSyncCyclePolicyType**。 Delta 或縮寫。 定義如果下次執行應該只程序 delta 變更，或是在下次執行應該執行完整匯入及同步處理，其中也會重新處理任何新增或變更的規則。
- **NextSyncCycleStartTimeInUTC**。 下一次排程器就會開始下次同步處理週期。
- **PurgeRunHistoryInterval**。 作業記錄要保留的時間。 您可以在同步處理服務管理員檢閱這些。 預設為要保留這些 7 天。
- **SyncCycleEnabled**。 指出排程器是否正在執行匯入、 同步處理，並匯出程序，其操作的一部分。
- **MaintenanceEnabled**。 如果啟用進行的維修作業程序，則會顯示。 它會更新憑證/索引鍵，並清除作業登入。
- **IsStagingModeEnabled**。 顯示已啟用[臨時模式](active-directory-aadconnectsync-operations.md#staging-mode)。

您可以變更這些設定與部分`Set-ADSyncScheduler`。 您可以修改下列參數︰

- CustomizedSyncCycleInterval
- NextSyncCyclePolicyType
- PurgeRunHistoryInterval
- SyncCycleEnabled
- MaintenanceEnabled

排程器設定會儲存於 Azure AD。 如果您有暫存的伺服器時，主要伺服器上的任何變更都會影響暫存 （但 IsStagingModeEnabled) 伺服器。

### <a name="customizedsynccycleinterval"></a>CustomizedSyncCycleInterval
語法︰`Set-ADSyncScheduler -CustomizedSyncCycleInterval d.HH:mm:ss`  
d 天，HH-小時、 公釐-分鐘，ss-（秒）

範例︰`Set-ADSyncScheduler -CustomizedSyncCycleInterval 03:00:00`  
將變更執行每 3 小時排程器。

範例︰`Set-ADSyncScheduler -CustomizedSyncCycleInterval 1.0:0:0`  
將變更執行每日排程器。

## <a name="start-the-scheduler"></a>開始排程器
排程器預設會執行每 30 分鐘。 在某些情況下，您可能會想執行同步處理週期之間已排程的循環或您需要執行其他類型。

**Delta 同步處理循環圖**  
Delta 同步處理循環圖包含下列步驟︰

- Delta 匯入所有的連接器
- Delta 同步處理所有的連接器
- 匯出所有的連接器

可能是您已在緊急變更其必須進行同步處理立即這是您需要手動執行 [循環圖的原因。 若要手動執行循環圖，然後執行 PowerShell `Start-ADSyncSyncCycle -PolicyType Delta`。

**完整的同步處理循環圖**  
如果您已經做了一個下列設定變更，您需要 （又名執行完整的同步處理的循環圖 初始）︰

- 新增更多的物件或要匯入來源目錄中的屬性
- 變更同步處理規則
- 變更[篩選](active-directory-aadconnectsync-configure-filtering.md)，應該包括在內的物件數目不同讓

如果您已經做了一個這些變更，您需要執行完整的同步處理的循環圖，因此同步處理引擎有機會在 reconsolidate 連接器空格。 完整的同步處理的循環圖包含下列步驟︰

- 完整的匯入所有的連接器
- 完整的同步處理所有的連接器
- 匯出所有的連接器

若要初始化完整的同步處理的循環圖，請執行`Start-ADSyncSyncCycle -PolicyType Initial`從 PowerShell 提示。 這會啟動完整的同步處理的循環圖。

## <a name="stop-the-scheduler"></a>停止排程器
如果排程器目前正在同步處理循環您可能需要停止。 例如，如果您啟動安裝精靈，且您收到此錯誤︰

![SyncCycleRunningError](./media/active-directory-aadconnectsync-feature-scheduler/synccyclerunningerror.png)

執行同步處理的循環圖時，您無法變更設定。 您可能會等到排程器已完成此程序，但您也可以停止，因此您可以立即進行變更。 停止目前的循環圖有害並不會與下一個執行處理仍無法處理的任何變更。

1. 首先請告訴若要停止使用 PowerShell cmdlet 其目前的循環排程器`Stop-ADSyncSyncCycle`。
2. 停止排程器不會停止目前的連接器，從目前的工作。 若要強制連接器，以停止，請採取下列動作︰ ![StopAConnector](./media/active-directory-aadconnectsync-feature-scheduler/stopaconnector.png)
    - 從 [開始] 功能表開始**同步處理服務**。 移至**連接線**、 醒目提示的連接器**執行**的狀態並從動作中選取 [**停止**。

排程器仍在使用中，在下一個商機上重新開始。

## <a name="custom-scheduler"></a>自訂的排程器
本節說明 cmdlet 只用於建立[1.1.130.0](active-directory-aadconnect-version-history.md#111300)及更新版本。

如果內建的排程器不符合您的需求，您就可以排程使用 PowerShell 的連接器。

### <a name="invoke-adsyncrunprofile"></a>呼叫 ADSyncRunProfile
您可以開始設定檔的連接器，以這種方式︰

```
Invoke-ADSyncRunProfile -ConnectorName "name of connector" -RunProfileName "name of profile"
```

若要使用的[連接器名稱](active-directory-aadconnectsync-service-manager-ui-connectors.md)及[執行設定檔名稱](active-directory-aadconnectsync-service-manager-ui-connectors.md#configure-run-profiles)名稱可以[同步處理服務管理員使用者介面](active-directory-aadconnectsync-service-manager-ui.md)中找到。

![呼叫執行的設定檔](./media/active-directory-aadconnectsync-feature-scheduler/invokerunprofile.png)  

`Invoke-ADSyncRunProfile` Cmdlet 同步，也就是它不會傳回控制項直到連接器已經完成作業成功或發生錯誤。

當您排定的連接器時，建議您以下列順序對其進行排程︰

1. (完整/Delta)從內部部署目錄，例如 Active Directory 匯入
2. (完整/Delta)從 Azure AD 匯入
3. (完整/Delta)從內部部署目錄，例如 Active Directory 同步處理
4. (完整/Delta)從 Azure AD 同步處理
5. 匯出至 Azure AD
6. 匯出至內部部署目錄，例如 Active Directory

如果您查看內建的排程器，這是連接線會執行的順序。

### <a name="get-adsyncconnectorrunstatus"></a>取得 ADSyncConnectorRunStatus
您也可以監視同步處理引擎，若要查看是否忙碌或閒置。 如果同步處理引擎閒置而不執行的連接器，這個指令程式會傳回空的結果。 如果執行連接器時，它會傳回的連接器名稱。

```
Get-ADSyncConnectorRunStatus
```

![連接器執行狀態](./media/active-directory-aadconnectsync-feature-scheduler/getconnectorrunstatus.png)  
在上方的圖片，第一行是從同步處理引擎空閒狀態。 從 Azure AD 連接器執行時，第二行。

## <a name="scheduler-and-installation-wizard"></a>安裝和排程器精靈
如果您啟動安裝精靈，然後排程器會暫時停止。 這是因為假設您將會變更設定，而且不能將這些套用如果同步處理引擎正在執行。 因此，不要讓安裝精靈開啟因為它會停止同步處理引擎從執行同步處理的任何動作。

## <a name="next-steps"></a>後續步驟
進一步瞭解[Azure AD Connect 同步處理](active-directory-aadconnectsync-whatis.md)設定。

進一步瞭解[整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)。
