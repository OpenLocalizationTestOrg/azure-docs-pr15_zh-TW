<properties
    pageTitle="使用重新導向中 Azure RemoteApp |Microsoft Azure"
    description="瞭解如何設定和使用重新導向中 RemoteApp"
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

# <a name="using-redirection-in-azure-remoteapp"></a>Azure RemoteApp 中使用重新導向

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

裝置重新導向可讓使用者互動遠端應用程式使用附加至其本機電腦、 手機或平板電腦的裝置。 例如，如果您有提供透過 Azure RemoteApp Skype，使用者需要使用 Skype 個人電腦上安裝的相機鏡頭。 這也適用於印表機與喇叭、 部監視器，範圍的 USB 連接的周邊。

遠端桌面通訊協定 (RDP)，並 RemoteFX 提供重新導向，則會運用 RemoteApp。

## <a name="what-redirection-is-enabled-by-default"></a>依預設會啟用哪些重新導向？
當您使用 RemoteApp 時，預設會啟用下列重新導向。 括號括住的資訊會顯示 RDP 設定。

- （**這台電腦上播放**） 的本機電腦上播放音效。 (audiomode:i:0)
- 擷取從本機電腦音訊並傳送到遠端電腦 （**此電腦的記錄**）。 (audiocapturemode:i:1)
- 列印到本機印表機 (redirectprinters:i:1)
- COM 連接埠 (redirectcomports:i:1)
- 智慧卡裝置 (redirectsmartcards:i:1)
- 剪貼簿 （複製並貼上的能力） (redirectclipboard:i:1)
- 清除 [輸入字型平滑法 (允許字型︰ i:1)
- 重新導向所有支援的隨插即用的裝置。 (devicestoredirect:s: *)

## <a name="what-other-redirection-is-available"></a>有哪些重新導向？
兩個重新導向] 選項預設為停用︰

- 磁碟機重新導向 （磁碟機對應）︰ 本機電腦的磁碟機成為遠端工作階段中的對應磁碟機。 這個選項可讓您儲存或開啟的檔案從本機磁碟機當您在遠端工作階段。
- USB 重新導向︰ 您可以使用 USB 裝置連接到您的本機電腦的遠端工作階段。

## <a name="change-your-redirection-settings-in-remoteapp"></a>在變更您重新導向設定 RemoteApp
您可以使用 SDK 的 Microsoft Azure PowerShell 來變更集合的裝置重新導向設定。 在您安裝新的 PowerShell 和 SDK 之後，請先設定來管理您的訂閱，[以及安裝與設定 PowerShell 的 Azure](../powershell-install-configure.md)所述。

若要設定自訂 RDP 屬性，然後使用類似下列命令︰

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:*"

（請注意，*條*會用來作為分隔符號區之間個別屬性）。

若要取得清單哪些自訂 RDP 屬性的設定，請執行下列指令程式。 請注意，只自訂屬性會顯示為輸出結果並不是預設內容︰  

    Get-AzureRemoteAppCollection -CollectionName <collection name>

當您設定自訂屬性必須指定所有的自訂屬性每次;否則此設定會還原為停用。   

### <a name="common-examples"></a>常見的範例
若要啟用磁碟機重新導向使用下列 cmdlet:  

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*”

使用這個 cmdlet 來啟用 USB] 和 [磁碟機重新導向︰

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:*"

若要停用 [剪貼簿共用使用這個 cmdlet:  

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "redirectclipboard:i:0”

> [AZURE.IMPORTANT] 請務必以完全登出集合中的所有使用者 （並不只是將其中斷連線） 測試變更之前。 若要確保使用者會完全登出，移至在 Azure 入口網站集合中的 [**工作階段**] 索引標籤，然後登出會中斷連線，或登入任何使用者。 有時可能需要幾秒鐘本機磁碟機，若要顯示在檔案總管中的工作階段。

## <a name="change-usb-redirection-settings-on-your-windows-client"></a>變更您的 Windows 用戶端上 USB 重新導向設定

如果您要連線到 RemoteApp 的電腦上使用 USB 重新導向，有 2 需要發生的動作。 1-您的系統管理員必須使用 PowerShell 的 Azure 啟用集合層級的 USB 重新導向。 2-在每個您要使用 USB 重新導向的裝置，必須先啟用的群組原則，讓它。 此步驟會需要完成的每個使用者想要使用 USB 重新導向。

> [AZURE.NOTE] Windows 的電腦，才支援使用 Azure RemoteApp USB 重新導向。

### <a name="enable-usb-redirection-for-the-remoteapp-collection"></a>啟用 RemoteApp 集合的 USB 重新導向
若要啟用集合層級的 USB 重新導向使用下列 cmdlet:

    Set-AzureRemoteAppCollection -CollectionName <collection_name> -CustomRdpProperty "nusbdevicestoredirect:s:*"

### <a name="enable-usb-redirection-for-the-client-computer"></a>啟用用戶端電腦的 USB 重新的導向

若要在您的電腦上設定 USB 重新導向設定︰

1. 開啟 [本機群組原則編輯器 (GPEDIT。MSC)。 （從命令列執行 gpedit.msc）。
2. 開啟 [**電腦設定 \ 原則系統遠端桌面服務 \ 遠端桌面連線 Client\RemoteFX USB 裝置重新導向**]。
3. 按兩下 [**此電腦的其他支援 RemoteFX USB 裝置允許 RDP 重新導向**]。
4. 選取 [**啟用**]，然後按一下**系統管理員和使用者 RemoteFX USB 重新導向的存取權限**。
5. 開啟命令提示字元具有系統管理權限，並執行下列命令︰

        gpupdate /force
6. 重新啟動電腦。

您也可以使用 [群組原則管理工具來建立和套用您網域中的所有電腦的 USB 重新導向原則︰

1. 請網域系統管理員的身分登入網域控制站。
2. 開啟群組原則管理主控台。 (按一下**開始 > 系統管理工具] > 群組原則管理**。)
3. 瀏覽至網域或您要建立的原則的單位。
4. **預設網域原則**，以滑鼠右鍵按一下，然後按一下 [**編輯**。
5. 開啟 [**電腦設定 \ 原則系統遠端桌面服務 \ 遠端桌面連線 Client\RemoteFX USB 裝置重新導向**]。
6. 按兩下 [**此電腦的其他支援 RemoteFX USB 裝置允許 RDP 重新導向**]。
7. 選取 [**啟用**]，然後按一下**系統管理員和使用者 RemoteFX USB 重新導向的存取權限**。
8. 按一下**[確定]**。  
