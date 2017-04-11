<properties 
    pageTitle="您要如何重新導向中 Azure RemoteApp 的 USB 裝置？ |Microsoft Azure" 
    description="瞭解如何使用 USB 裝置中 Azure RemoteApp 重新導向。" 
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



# <a name="how-do-you-redirect-usb-devices-in-azure-remoteapp"></a>您要如何重新導向中 Azure RemoteApp 的 USB 裝置？

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

裝置重新導向可讓使用者使用的 USB 裝置連接到電腦或平板電腦應用程式中 Azure RemoteApp。 例如，如果您共用的 Skype 透過 Azure RemoteApp，使用者需要能夠使用其裝置相機。

在您進入下之前，請確定您閱讀[Azure RemoteApp 中的使用重新導向](remoteapp-redirection.md)的 USB 重新導向資訊。 不過建議 nusbdevicestoredirect:s: * 不適用於 USB 網路攝影機，並可能不適用於某些 USB 印表機或 USB multifunctional 裝置。 設計及基於安全性理由，Azure RemoteApp 管理員重新導向裝置類別 GUID 或裝置執行個體識別碼之前啟用您的使用者可以使用的裝置。

雖然這份文件說話關於網頁相機重新導向，您可以使用類似的方法來重新導向 USB 印表機和不會重新導向的其他 USB multifunctional 裝置**nusbdevicestoredirect:s:***] 命令。

## <a name="redirection-options-for-usb-devices"></a>重新導向 USB 裝置選項
Azure RemoteApp 可用於將 USB 裝置為可用的重新導向的遠端桌面服務非常類似機制。 基礎技術可讓您選擇正確的重新導向方法提供的裝置，以取得的最高層級與 RemoteFX USB 裝置使用重新導向**usbdevicestoredirect:s:** ] 命令。 有四個項目，此命令︰

| 處理順序 | 參數           | 描述                                                                                                                |
|------------------|---------------------|----------------------------------------------------------------------------------------------------------------------------|
| 1                | *                   | 選取 [不收取的高層級的重新導向的所有裝置]。 附註︰ 根據設計，* 無法運作的 USB 網路攝影機。  |
|                  | {裝置類別 GUID} | 選取符合指定的裝置安裝類別的所有裝置。                                                           |
|                  | USB\InstanceID      | 選取 [指定給指定的執行個體識別碼 USB 裝置                                                                  |
| 2                | -USB\Instance 識別碼    | 移除指定的裝置的重新導向設定。                                                                 |

## <a name="redirecting-a-usb-device-by-using-the-device-class-guid"></a>將 USB 裝置使用的裝置類別 GUID 重新導向
有兩種方式來尋找裝置類別 GUID，可用來重新導向。 

第一個選項是使用[System-Defined 裝置設定類別可用樣式](https://msdn.microsoft.com/library/windows/hardware/ff553426.aspx)。 選擇最符合連接至本機電腦的裝置的類別。 數位相機這可能是影像裝置類別或視訊擷取裝置類別。 您需要執行一些實驗尋找正確的類別，可以使用本機連接 USB 裝置 （在本例網路攝影機） GUID 的裝置類別。

更好的方式或第二個選項，請遵循下列步驟來尋找特定裝置類別 GUID 是︰

1. 開啟 [裝置管理員、 找出裝置會被重新導向，以滑鼠右鍵按一下，然後再開啟屬性。
![開啟 [裝置管理員](./media/remoteapp-usbredir/ra-devicemanager.png)
2. 在 [**詳細資料**] 索引標籤上選擇 [**類別 Guid**] 屬性。 它會出現的值是裝置的類別 GUID 類型。
![數位相機內容](./media/remoteapp-usbredir/ra-classguid.png)
3. 若要重新導向符合其裝置使用類別 Guid 值。

例如︰

        Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "nusbdevicestoredirect:s:<Class Guid value>"

您可以結合多個裝置重新導向相同的指令程式。 例如︰ 若要重新導向本機的儲存空間及 USB 網路攝影機，指令程式看起來像這樣︰

        Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:<Class Guid value>"

當您設定裝置重新導向類別 GUID 會重新導向符合該類別 GUID 指定集合中的所有裝置。 例如，如果有多部電腦區域網路上有相同的 USB 網路攝影機，請您可以執行若要重新導向所有的網路攝影機的單一指令程式。

## <a name="redirecting-a-usb-device-by-using-the-device-instance-id"></a>重新導向使用的裝置執行個體識別碼的 USB 裝置

如果您想要進一步精密的控制，並想要控制每個裝置的重新導向，您可以使用**USB\InstanceID**重新導向參數。

這個方法最困難的部分會尋找 USB 裝置執行個體識別碼。 您必須在電腦和特定的 USB 裝置的存取權。 然後遵循下列步驟︰

1. 啟用裝置重新導向遠端桌面工作階段中所述[如何使用我的裝置與資源遠端桌面工作階段？](http://windows.microsoft.com/en-us/windows7/How-can-I-use-my-devices-and-resources-in-a-Remote-Desktop-session)
2. 開啟遠端桌面連線，然後按一下 [**顯示選項**。
3. 按一下 [儲存目前的連線設定為 RDP 檔案的 [**另存新檔**]。  
    ![將設定儲存為 RDP 檔案](./media/remoteapp-usbredir/ra-saveasrdp.png)
4. 選擇 [檔案名稱和位置，例如 「 MyConnection.rdp 」 和 「 這 PC\Documents 」，然後儲存檔案。
5. 開啟文字編輯器 MyConnection.rdp 檔案並找到您要重新導向的裝置的執行個體識別碼。

現在的執行個體識別碼在中使用下列 cmdlet:

    Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "nusbdevicestoredirect:s: USB\<Device InstanceID value>"



### <a name="help-us-help-you"></a>協助我們協助您 
您知道，除了評等這份文件] 下方，向下進行註解，您可以進行變更本身的文件嗎？ 遺漏的內容？ 有錯誤？ 我是否撰寫的項目只混亂？ 向上捲動，然後按一下 [若要變更的 [**編輯 GitHub 上**-那些以供檢閱，傳送給我們，然後，我們在登入，就會看到您的變更和這裡改良項目。