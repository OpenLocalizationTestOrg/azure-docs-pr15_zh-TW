
<properties 
    pageTitle="如何會 Azure RemoteApp 儲存使用者資料與設定？ |Microsoft Azure"
    description="瞭解如何 Azure RemoteApp 儲存使用者資料使用的使用者設定檔磁碟。"
    services="remoteapp"
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />

# <a name="how-does-azure-remoteapp-save-user-data-and-settings"></a>如何會 Azure RemoteApp 儲存使用者資料與設定？

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

Azure RemoteApp 使用者身分識別和自訂儲存跨裝置與工作階段。 這個使用者資料會儲存於每位使用者每個集合磁碟，稱為 [使用者設定檔光碟 (UPD)。 磁碟遵循使用者，並確保使用者擁有一致的體驗，無論何處登入。 儲存 

使用者設定檔磁碟是完全透明的使用者，使用者將文件儲存至自己的文件] 資料夾 （在上看起來本機磁碟機），並變更為主要其應用程式設定。 同時，從任何裝置連線至 Azure RemoteApp 保存所有個人的設定。 所有使用者看到有其資料在相同位置。

每個 UPD 有 50 GB 的持續的儲存空間，並包含兩個使用者資料和應用程式設定。 

請參閱使用者的設定檔資料的詳細資料。

>[AZURE.NOTE] 若要停用 UPD 需要嗎？ 您可以執行的興趣取出 Pavithra 的部落格文章，[停用使用者設定檔中的磁碟 (UPDs) Azure RemoteApp](https://blogs.technet.microsoft.com/enterprisemobility/2015/11/11/disable-user-profile-disks-upds-in-azure-remoteapp/)，如需詳細資訊。


## <a name="how-can-an-admin-get-to-the-data"></a>系統管理員如何存取資料？

如果您需要存取您的使用者 （適用於損毀修復，或如果使用者離開公司） 的資料，請連絡 Azure 支援，並提供訂閱資訊的收集與使用者身分識別。 Azure RemoteApp 小組會提供您 VHD URL。 下載該 VHD，並擷取任何文件或您需要的檔案。 請注意，VHD 50 GB，因此會花費位元下載它。


## <a name="is-the-data-backed-up"></a>為備份的資料？

是的我們會儲存每個地理位置的使用者資料的備份。 資料是唯讀的並可以存取相同的方式時，一般的資料可 （連絡人 Azure RemoteApp 取得），如果主要的資料中心已關閉。 資料會複製到備份位置即時，我們不保留不同版本的複本。 因此，在資料損毀，我們無法將它還原至先前已知的建議版本，但向下主要資料中心時，您將能夠取得從其他位置的使用者資料。

## <a name="how-do-users-see-the-upd-on-the-server-side"></a>如何使用者看到 UPD 伺服器端？

每個使用者會有自己的目錄會對應到其 UPD 伺服器上︰ c:\Users\username。

## <a name="whats-the-best-way-to-use-outlook-and-upd"></a>什麼是使用 Outlook 和 UPD 的最好方法？

Azure RemoteApp 儲存 Outlook 狀態 （信箱，Pst） 工作階段之間。 若要啟用此問題，我們需要儲存在使用者設定檔資料 PST (c:\users\<使用者名稱 >)。 這是預設位置的資料，因此，只要您不會變更位置，資料就會保存工作階段之間。

我們也建議您在 Outlook 中使用 「 快取 」 模式，並使用 「 伺服器/線上 」 模式，來搜尋。

請參閱[本文](remoteapp-outlook.md)的使用 Outlook 和 Azure RemoteApp 的詳細資訊。

## <a name="what-about-redirection"></a>重新導向呢？
您可以設定讓使用者存取本機裝置設定[重新導向](remoteapp-redirection.md)Azure RemoteApp。 本機裝置能夠存取 UPD 上的資料。

## <a name="can-i-use-my-upd-as-a-network-share"></a>可以使用我 UPD 為網路共用嗎？
[否]。 UPDs 無法作為網路共用。 使用者已連線至 Azure RemoteApp 時，只提供給使用者 UPD。

## <a name="if-i-delete-a-user-from-a-collection-is-their-upd-deleted"></a>從集合刪除使用者，如果是其 UPD 刪除嗎？

否，當您刪除使用者，我們不會自動刪除 UPD-不過，我們將資料儲存鍵，直到您刪除集合。 90 天後刪除集合中，我們會刪除所有 UPDs。 

如果您需要從集合刪除 UPD，請連絡 Azure RemoteApp-我們可以刪除 UPD 我們端。

## <a name="can-i-access-my-users-upds-either-current-or-deleted-users"></a>可以存取我的使用者 UPDs （目前或已刪除的使用者） 嗎？

是的若您連絡[Azure RemoteApp](mailto:remoteappforum@microsoft.com)，我們可以將您設定來存取資料的 url。 您必須瞭解的 10 個小時的任何資料或檔案從下載 UPD access 到期之前。

## <a name="are-upds-available-offline"></a>是離線 UPDs？

現在我們不超過 10 個小時 access 視窗上述 UPDs，以提供離線存取。 這表示，我們目前沒有可讓您有權存取的時間的方式完成更複雜的工作，例如 UPDs 上執行的防毒軟體或存取資料的稽核足夠。

## <a name="do-registry-key-settings-persist"></a>保存登錄機碼設定？
是的 HKEY_Current_User 寫入的任何項目是 UPD。

## <a name="can-i-disable-upds-for-a-collection"></a>停用 UPDs 集合？

是的您可以要求以停用訂閱 UPDs Azure RemoteApp，但您無法在執行該自己。 這表示 UPDs 會停用訂閱中的所有集合。

您可能會想要停用 UPDs 在下列情況下︰ 

- 您需要完成存取及控制使用者資料 (如稽核並檢閱之用，例如財務機構)。
- 您必須 3rd 派對使用者設定檔管理解決方案內部部署，並且想要繼續使用您網域的 Azure RemoteApp 部署中。 這需要載入金色影像的設定檔代理程式。 
- 您不需要任何本機資料存放區，或您的雲端或檔案共用的所有資料，並想要的控制項儲存在本機使用 Azure RemoteApp 的資料。

如需詳細資訊，請參閱[停用使用者設定檔中的磁碟 (UPDs) Azure RemoteApp](https://blogs.technet.microsoft.com/enterprisemobility/2015/11/11/disable-user-profile-disks-upds-in-azure-remoteapp/) 。

## <a name="can-i-restrict-users-from-saving-data-to-the-system-drive"></a>我可以限制使用者將資料儲存至系統磁碟機？

是的但您必須在設定的範本圖像中建立的集合。 封鎖存取系統磁碟機中使用下列步驟︰

1. 執行**gpedit.msc**範本圖像上。
2. 瀏覽至**使用者設定 > 系統管理範本 > Windows 元件 > 總管**。
3. 選取下列選項︰
    - **隱藏我的電腦中這些指定磁碟機**
    - **從我的電腦避免磁碟機的存取**

## <a name="can-i-seed-upds-i-want-to-put-some-data-in-the-upd-thats-available-the-first-time-the-user-signs-in"></a>我可以植 UPDs 嗎？ 我要將部分資料放在 UPD 提供的第一次使用者登入。

是的當您建立的範本圖像時，您可以新增資訊至預設的設定檔。 這項資訊然後加入 UPD。

## <a name="can-i-change-the-size-of-the-upd-depending-on-how-much-data-i-want-to-store"></a>我可以變更 [根據我想要儲存的資料量 UPD 的大小嗎？

否，所有 UPDs 都有 50 GB 的儲存空間。 如果您想要儲存不同的資料量，請嘗試下列動作︰

1. 停用 UPDs 集合。
2. 設定使用者存取檔案共用。
3. 使用 「 啟動指令碼載入檔案共用。 如需啟動 Azure RemoteApp 指令碼的詳細資訊，請參閱下列內容。
4. 若要儲存檔案的所有資料的直接使用者共用。


## <a name="how-do-i-run-a-startup-script-in-azure-remoteapp"></a>如何在 Azure RemoteApp 執行啟動指令碼？

如果您想要執行啟動指令碼，開始建立您要使用的範本圖像中的已排程的工作。 (此*之前*執行 sysprep。) 

![建立系統的工作](./media/remoteapp-upd/upd1.png)

![建立系統當使用者登入時所執行的工作](./media/remoteapp-upd/upd2.png)

在 [**一般**] 索引標籤，請務必若要變更的**使用者帳戶**在 [安全性]，「 BUILTIN\Users 」。

![變更群組的使用者帳戶](./media/remoteapp-upd/upd4.png)

排程的任務便會啟動啟動指令碼、 使用使用者的認證。 排程來執行任務每次使用者登入。

![設定任務的觸發程序為 「 在登入 」](./media/remoteapp-upd/upd3.png)

您也可以使用[群組原則的啟動指令碼](https://technet.microsoft.com/library/cc779329%28v=ws.10%29.aspx)。 

## <a name="what-about-placing-a-startup-script-in-the-start-menu-would-that-work"></a>應該做什麼放在 [開始] 功能表中的啟動指令碼？ 想要的工作嗎？

換句話說，可以建立執行 config 視窗指令碼.bat 檔案並將它儲存到 [c:\ProgramData\Microsoft\Windows\Start 使用者名稱 \appdata\roaming\microsoft\windows\start Menu\Programs\StartUp] 資料夾中，和嗎然後有使用者啟動的 RemoteApp 工作階段時執行該指令碼？

否的不是支援 Azure RemoteApp，使用 RDSH，也不支援啟動指令碼中的 [開始] 功能表。

## <a name="can-i-use-mstscexe-the-remote-desktop-program-to-configure-logon-scripts"></a>可以使用 mstsc.exe （遠端桌面程式） 來設定登入指令碼嗎？

沒有，不支援 Azure RemoteApp。

## <a name="can-i-store-data-on-the-vm-locally"></a>我是否能將資料儲存 VM 本機上？

不會儲存在上以外的其他 VM UPD 中的資料將會遺失。 有高機會使用者不會在同一個 VM 下一步他們登入 Azure RemoteApp 的時間。 讓使用者將無法登入相同 VM，而且資料將會遺失，我們不會維持使用者 VM 保存。 此外，當我們更新集合時，便會現有 Vm 取代一組新 Vm-代表任何 VM 本身上儲存的資料都會遺失。 建議您將資料儲存在 UPD，例如檔案伺服器內 VNET，或使用雲端儲存空間系統 DropBox 等雲端 Azure 檔案共用儲存空間。

## <a name="how-do-i-mount-an-azure-file-share-on-a-vm-using-powershell"></a>如何裝載在 VM，使用 PowerShell 的 Azure 檔案共用？

您可以使用網路 PSDrive cmdlet 來裝載磁碟機，，如下所示︰

    New-PSDrive -Name <drive-name> -PSProvider FileSystem -Root \\<storage-account-name>.file.core.windows.net\<share-name> -Credential :<storage-account-name>


您也可以執行下列動作，以儲存您的認證︰

    cmdkey /add:<storage-account-name>.file.core.windows.net /user:<storage-account-name> /pass:<storage-account-key>


可讓您略過-認證中的參數新增 PSDrive 指令程式。
