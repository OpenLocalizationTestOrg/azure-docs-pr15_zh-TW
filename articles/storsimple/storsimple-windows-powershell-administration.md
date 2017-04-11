<properties 
   pageTitle="PowerShell StorSimple 裝置管理 |Microsoft Azure"
   description="瞭解如何使用 StorSimple 版的 Windows PowerShell 來管理 StorSimple 裝置。"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/18/2016"
   ms.author="alkohli@microsoft.com" />

# <a name="use-windows-powershell-for-storsimple-to-administer-your-device"></a>使用 Windows PowerShell 的 StorSimple 來管理您的裝置

## <a name="overview"></a>概觀

Windows PowerShell 的 StorSimple 提供可用來管理您的 Microsoft Azure StorSimple 裝置的命令列介面。 顧名思義，則內建於受限制的 runspace Windows PowerShell 型命令列介面。 從命令列中的使用者的觀點來看，限制的 runspace 會顯示為受限制的版本的 Windows PowerShell。 維持的 Windows PowerShell 的基本功能，此介面會有其他都能管理您的 Microsoft Azure StorSimple 裝置的專用的 cmdlet。 

本文說明 StorSimple 功能，包括您如何連線到此介面，Windows PowerShell，並包含連結的逐步程序或使用此介面，您可以執行的工作流程。 工作流程包含如何註冊您的裝置，您的裝置上設定的網路介面，安裝更新需要進行的維修作業模式中，變更裝置陳述式，而您可能會遇到任何問題進行疑難排解的裝置。

閱讀本文之後，您將可以︰

- 連線至您的 StorSimple 使用 Windows PowerShell 的 StorSimple 裝置。

- 管理您的 StorSimple 使用 Windows PowerShell 的 StorSimple 裝置。

- 在 Windows PowerShell 中取得 StorSimple 說明。

>[AZURE.NOTE]   

>- Windows PowerShell 的 StorSimple cmdlet 可讓您循序主控台或遠端透過 Windows PowerShell 遠端管理 StorSimple 裝置。 如需有關每一則可在此介面中的個別 cmdlet 的詳細資訊，請前往[StorSimple 的 Windows PowerShell cmdlet 參考](https://technet.microsoft.com/library/dn688168.aspx)。

>- Azure PowerShell StorSimple cmdlet 是 cmdlet 的不同，讓您可以自動化 StorSimple 服務層級和從命令列的移轉工作的集合。 StorSimple 的 Azure PowerShell cmdlet 的相關詳細資訊，請移至[Azure StorSimple cmdlet 參考](https://msdn.microsoft.com/library/azure/dn920427.aspx)。

您可以存取 Windows PowerShell 的 StorSimple 使用下列方法之一︰

- [連線至 StorSimple 裝置循序主控台](#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
- [若要使用 Windows PowerShell 的 StorSimple 遠端連線](#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)
    

## <a name="connect-to-windows-powershell-for-storsimple-via-the-device-serial-console"></a>連線到 Windows PowerShell StorSimple 透過裝置循序主控台

您可以[下載 PuTTY](http://www.putty.org/)或類似終端機連線到 Windows PowerShell 用 StorSimple 模擬軟體。 您需要設定存取 Microsoft Azure StorSimple 裝置，以便於 PuTTY。 下列主題包含有關如何設定 PuTTy 並連接至裝置的詳細的步驟。 同時也說明循序主控台中各種不同的功能表選項。

### <a name="putty-settings"></a>PuTTY 設定

請確定您使用下列 PuTTY 設定，從循序主控台連線到 Windows PowerShell 介面。

#### <a name="to-configure-putty"></a>若要設定 PuTTY

1. 在 PuTTY**重新設定**] 對話方塊中，在 [**類別**] 窗格中，選取 [**鍵盤**]。

2. 請確定已選取下列選項 （當您開始新的工作階段時，這些是預設設定）。 

  	|鍵盤項目|選取|
  	|---|---|
  	|退格鍵|控制項-嗎？ (127)|
  	|[常用] 和 [結束索引鍵|標準|
  	|功能鍵和數字鍵台|ESC [n ~|
  	|方向鍵初始狀態|標準模式|
  	|數字鍵台的初始狀態|標準模式|
  	|啟用額外的鍵盤功能|控制 Alt 是不同的 AltGr|

    ![支援 Putty 設定](./media/storsimple-windows-powershell-administration/IC740877.png)

3. 按一下 [**套用**]。

4. 在 [**類別**] 窗格中，選取 [**翻譯**]。

5. 在 [**遠端字元集**] 清單方塊中，選取**utf-8**。

6. **處理的線條繪製字元**] 下選取 [**使用 Unicode 線條繪圖程式碼點**]。 下圖顯示正確的 PuTTY 選取項目。

    ![UTF-8 Putty 設定](./media/storsimple-windows-powershell-administration/IC740878.png)

7. 按一下 [**套用**]。


您現在可以使用 PuTTY 裝置循序主控台連線，請執行下列步驟。

[AZURE.INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]


### <a name="about-the-serial-console"></a>關於循序主控台

當您存取透過循序主控台，橫幅郵件 StorSimple 裝置的介面呈現 Windows PowerShell 時，後面接著功能表選項。 

橫幅郵件包含基本 StorSimple 裝置資訊，例如模型、 名稱、 已安裝的版本和您要存取控制站的狀態。 下圖顯示橫幅訊息範例。

![序列橫幅訊息](./media/storsimple-windows-powershell-administration/IC741098.png)

>[AZURE.IMPORTANT] 若要識別控制站您已連線到作用中] 或 [被動是否，您可以使用橫幅訊息。

下圖顯示循序主控台] 功能表中可用的各種 runspace 選項。

![註冊您的裝置 2](./media/storsimple-windows-powershell-administration/IC740906.png)

您可以選擇從下列設定︰

1. **登入的完整功能**這個選項可讓您 （具有適當認證） 連線到本機控制站的**SSAdminConsole** runspace。 （本機控制站是您目前正在存取透過 StorSimple 裝置的循序主控台控制器）。這個選項，也可以用於允許 Microsoft 支援人員存取不受限制的 runspace （支援工作階段） 任何可能的裝置問題進行疑難排解。 您使用的選項 1 登入之後，您可以讓 Microsoft 支援工程師存取不受限制的 runspace 執行特定指令程式。 如需詳細資訊，請參閱[啟動支援工作階段](storsimple-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple)。

2. **登入等控制站的完整存取**這個選項，但您可以連線 （具有適當認證） 等控制器上的**SSAdminConsole** runspace 是選項 1，相同。 因為 StorSimple 裝置是主動被動設定中的兩個控制站的可用性裝置，對指的是透過循序主控台存取您的裝置中的其他控制站）。
類似的選項 1，這個選項，也可允許存取不受限制的 runspace 等控制器上的 Microsoft 支援服務。

3. **連線的有限存取權**這個選項會用來存取有限模式中的 Windows PowerShell 介面。 您是不會提示您存取認證。 這個選項會連線到限制的 runspace 比較 1 和 2 的選項。  部分工作，可透過選項 1 的 **無法*在此 runspace 執行是︰

    - 重設為原廠設定
    - 變更密碼
    - 啟用或停用支援存取
    - 套用更新
    - 安裝快速修正程式 
                                                

    >[AZURE.NOTE] **如果您忘了裝置系統管理員的密碼，無法連線到選項 1 或 2，這是慣用的選項。**

4. **變更語言**這個選項可讓您變更顯示語言的 Windows PowerShell 介面上。 支援的語言是英文、 日文、 俄文、 法文、 南部韓文、 西班牙文、 義大利文、 德文、 中文和巴西葡萄牙文。


## <a name="connect-remotely-to-storsimple-using-windows-powershell-for-storsimple"></a>若要使用 Windows PowerShell 的 StorSimple StorSimple 遠端連線

您可以使用 Windows PowerShell 遠端連線到您 StorSimple 裝置。 當您連線以下列方式時，將不會看到的功能表。 （只有當您在裝置上使用循序主控台連線，您看到的功能表。 連線遠端讓您直接前往等同的 「 選項 1 – 完整存取 「 循序主控台。）使用 Windows PowerShell 遠端您連線至特定的 runspace。 您也可以指定的顯示語言。 

您使用循序主控台] 功能表中的 [**變更語言**] 選項設定語言無關的顯示語言。 遠端 PowerShell 自動挑選您連線時若未指定的地區設定的裝置。

>[AZURE.NOTE] 如果您正在使用 Microsoft Azure 虛擬主辦城市] 和 [StorSimple 虛擬裝置，您可以使用 Windows PowerShell 遠端處理和虛擬主機連線到虛擬裝置。 如果您已設定要儲存的 Windows PowerShell 工作階段資訊 」 主機上的共用位置，您應該注意所有人 」 本金包含已驗證的使用者。 因此，如果您有共用] 設定為允許所有人的存取，您不指定認證連線，將會用於未經授權的匿名本金，而且您會看到錯誤。 若要修正這個問題，請在共用主控您必須啟用來賓帳戶，然後來賓帳戶完整存取權授與共用]，或您必須指定有效的認證，以及 Windows PowerShell 指令程式。

您可以透過 Windows PowerShell 遠端使用 HTTP 或 HTTPS。 下列教學課程中使用的相關指示︰

- [使用從遠端 HTTP 連線](storsimple-remote-connect.md#connect-through-http)
- [使用從遠端 HTTPS 連線](storsimple-remote-connect.md#connect-through-https)

## <a name="connection-security-considerations"></a>連線的安全性考量

當您決定如何連線到 Windows PowerShell StorSimple 時，請考慮下列動作︰

- 直接連接到裝置循序主控台安全，但無法透過網路參數連線至循序主控台。 請務必謹慎安全性風險透過網路參數連線至裝置循序時。

- 透過 HTTP 工作階段連線，可能會提供更高的安全性比透過網路連線透過循序主控台。 雖然這不是最安全的方法，則可在受信任的網路上。

- 連線到 HTTPS 工作階段是最安全和建議的選項。


## <a name="administer-your-storsimple-device-using-windows-powershell-for-storsimple"></a>管理您的 StorSimple 使用 Windows PowerShell 的 StorSimple 裝置
下表顯示 StorSimple 裝置的 Windows PowerShell 介面內的所有管理的一般工作及複雜的工作流程可以執行的摘要。 如需有關每個工作流程的詳細資訊，請按一下 [在資料表中適當的項目]。

#### <a name="windows-powershell-for-storsimple-workflows"></a>StorSimple 工作流程的 Windows PowerShell

|如果您想要執行此動作...|使用此程序。|
|---|---|
|註冊您的裝置|[設定和註冊 StorSimple 使用 Windows PowerShell 的裝置](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |
|設定網頁 proxy</br>檢視 web proxy 設定|[設定網頁 proxy StorSimple 裝置](storsimple-configure-web-proxy.md)|
|修改您的裝置上的資料 0 網路介面設定|[修改資料 0 的網路介面 StorSimple 裝置](storsimple-modify-data-0.md)|
|停止控制器 </br> 重新啟動或關閉控制器 </br> 關閉裝置</br>重設為原廠預設設定的裝置|[管理裝置控制站](storsimple-manage-device-controller.md)|
|安裝進行的維修作業模式更新及快速修正程式|[更新您的裝置](storsimple-update-device.md)|
|輸入維護模式 </br>結束進行的維修作業模式|[StorSimple 裝置模式](storsimple-device-modes.md)|
|建立支援套件</br>解密並編輯支援套件|[建立及管理支援套件](storsimple-create-manage-support-package.md)|
|啟動支援工作階段</br>|[在 Windows PowerShell 中啟動 StorSimple 支援工作階段](/storsimple-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple)
 

## <a name="get-help-in-windows-powershell-for-storsimple"></a>在 Windows PowerShell 中 StorSimple 取得說明

StorSimple 的 Windows PowerShell 指令程式說明可供使用。 線上、 最新版本，此說明也會出現，您可以用來更新您的系統說明。

這個介面中取得說明是類似於 Windows PowerShell，大部分的說明相關的 cmdlet 都將能夠運作。 您可以找到說明適用於 Windows PowerShell 線上 TechNet Library 中︰[使用 Windows PowerShell 撰寫指令碼](http://go.microsoft.com/fwlink/?LinkID=108518)。

以下是說明的這個 Windows PowerShell 介面，包括如何更新說明類型的簡短描述。

#### <a name="to-get-help-for-a-cmdlet"></a>若要取得指令程式的說明

- 若要取得所有指令程式或函數的說明，請使用下列命令︰`Get-Help <cmdlet-name>`

- 若要取得線上說明適用於任何指令程式，使用 [與前一個 cmdlet`-Online`參數︰`Get-Help <cmdlet-name> -Online`

- 您可以使用完整的說明，請`–Full`參數，如需範例，使用`–Examples`參數。

#### <a name="to-update-help"></a>若要更新的說明

您可以輕鬆更新的 Windows PowerShell 介面中的說明。 執行下列步驟，以更新您的系統的說明。

#### <a name="to-update-cmdlet-help"></a>若要更新指令程式說明

1. 啟動 Windows PowerShell**以管理員身分執行**] 選項。

1. 在命令提示字元中，輸入︰ `Update-Help`

1. 將會安裝更新的說明檔案。

1. 已安裝的說明檔案後，請輸入︰ `Get-Help Get-Command`。 這會顯示的 cmdlet 針對提供說明的清單。


>[AZURE.NOTE] 若要取得所有可用的 cmdlet 的清單中 runspace，登入的相對應的功能表選項，並執行`Get-Command`指令程式。

## <a name="next-steps"></a>後續步驟
如果遇到任何問題與 StorSimple 裝置執行其中一項上述的工作流程時，請參閱[疑難排解 StorSimple 部署工具](storsimple-troubleshoot-deployment.md#tools-for-troubleshooting-storsimple-deployments)。

