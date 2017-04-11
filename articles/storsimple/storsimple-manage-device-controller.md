<properties
   pageTitle="管理 StorSimple 裝置控制站 |Microsoft Azure"
   description="瞭解如何停止、 重新啟動、 關閉或重設您的 StorSimple 裝置控制站。"
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/11/2016"
   ms.author="alkohli" />

# <a name="manage-your-storsimple-device-controllers"></a>管理您的 StorSimple 裝置控制站

## <a name="overview"></a>概觀

本教學課程說明不同 StorSimple 裝置控制站執行的作業。 StorSimple 裝置中的控制站是多餘 （對） 控制站的主動被動設定。 在指定的時間，只有一個控制站已啟動，並會處理所有的磁碟和網路作業。 其他控制器為被動式模式。 如果無法使用中的控制站，被動式控制器會變成作用中自動。

本教學課程中包含使用來管理裝置控制站的逐步指示:

- StorSimple 管理員服務中的 [**進行的維修作業**] 頁面的 [**控制器**] 區段
- StorSimple 的 Windows PowerShell。

我們建議您管理裝置控制器透過 StorSimple 管理員服務。 如果只能 StorSimple 使用 Windows PowerShell 來執行巨集指令，教學課程可讓您的筆記。

閱讀本教學課程之後，您將可以︰

- 重新啟動或關閉 StorSimple 裝置控制器
- 關閉 StorSimple 裝置
- 重設為工廠預設值的 StorSimple 裝置


## <a name="restart-or-shut-down-a-single-controller"></a>重新啟動或關閉單一控制器

控制站重新啟動或關閉不需要做為標準系統作業的一部分。 只有在的失敗的裝置的硬體元件需要替代的情況下有單一裝置控制站的關機作業。 使用過多的記憶體使用量或將控制站會影響效能的情況下也可能需要控制器重新啟動。 您可能也需要控制器重新成功控制器替代方案，如果您想要啟用並測試取代的控制器。

重新啟動裝置，則無法連線啟動器，假設被動式控制器可用的影響。 如果被動式控制器無法使用或已關閉，然後重新啟動的作用中的控制站可能會導致停機時間及服務中斷。

> [AZURE.IMPORTANT]

> - **執行控制站應該永遠不會移除，因為這會導致影響重複性和停機時間的影響的風險。**

> - 下列程序僅適用於 StorSimple 實體裝置。 啟動、 停止，並重新啟動虛擬裝置的相關資訊，請參閱[使用虛擬裝置](storsimple-virtual-device-u2.md#work-with-the-storsimple-virtual-device)。

您可以重新啟動或關閉單一裝置控制器使用 StorSimple StorSimple 管理員服務或 Windows PowerShell 的 Azure 傳統的入口網站

若要從 Azure 傳統入口網站管理您的裝置控制站，請執行下列步驟。

#### <a name="to-restart-or-shut-down-a-controller-in-classic-portal"></a>若要重新啟動或關閉 [傳統入口網站中的控制站

1. 瀏覽至**裝置 > 進行的維修作業**。

1. 移至 [**硬體狀態**，並確認您的裝置上的兩個控制站的狀態是 [**狀況良好**。

    ![確認 StorSimple 裝置控制站是健全](./media/storsimple-manage-device-controller/IC766017.png)

1. 從 [**進行的維修作業**] 頁面的底部，按一下 [**管理控制器**]。

    ![管理 StorSimple 裝置控制站](./media/storsimple-manage-device-controller/IC766018.png)</br>

    >[AZURE.NOTE] 如果您無法看到**管理控制站**，您需要安裝的更新。 如需詳細資訊，請參閱[更新您的 StorSimple 裝置](storsimple-update-device.md)。

1. 在 [**變更控制器設定**] 對話方塊中，執行下列動作︰
    1. 從 [**選取控制器**] 下拉式清單中，選取您想要管理的控制站。 控制站 0 與 1 控制器就的選項。 這些控制站也會識別為作用中或被動式中。

        >[AZURE.NOTE] 如果是無法管理控制器無法使用或已關閉，並不會出現在下拉式清單中。

    2. 從 [**選取動作**] 下拉式清單中，選擇 [**重新啟動控制器**或**關閉 [控制器**。

        ![重新啟動 StorSimple 裝置被動式控制器](./media/storsimple-manage-device-controller/IC766020.png)
    3. 按一下 [檢查] 圖示 ![檢查圖示](./media/storsimple-manage-device-controller/IC740895.png).

這會重新啟動或關閉控制器。 下表摘要列出根據您所做**變更控制器設定**] 對話方塊中選取項目會有什麼狀況的詳細資料。  


|選取範圍 #|如果您選擇...|這會改變。|
|---|---|---|
|1。|重新啟動被動式控制器。|重新啟動控制站，才會顯示工作和工作已順利建立後會通知您。 這會啟動控制器重新啟動。 您可以監視重新啟動程序來存取**服務 > 儀表板 > 檢視作業的記錄檔**，然後以您的服務特定的參數篩選。|
|2。|重新啟動的作用中的控制站。|您會看到下列警告: 「 您重新啟動的作用中的控制站，如果裝置將無法透過被動式控制器。 您要繼續嗎？ 」 </br>如果您選擇要繼續進行這項作業，會用來重新啟動被動式控制站的相同的下一個步驟 （請參閱選取項目 1）。|
|3。|關閉被動式控制器。|您會看到下列訊息: 「 關機完畢之後，您會需要推播開啟控制器上的 [power] 按鈕。 確定您要關閉此控制站？ 」 </br>如果您選擇要繼續進行這項作業，會用來重新啟動被動式控制站的相同的下一個步驟 （請參閱選取項目 1）。|
|4。|關閉使用中的控制器。|您會看到下列訊息: 「 關機完畢之後，您會需要推播開啟控制器上的 [power] 按鈕。 確定您要關閉此控制站？ 」 </br>如果您選擇要繼續進行這項作業，會用來重新啟動被動式控制站的相同的下一個步驟 （請參閱選取項目 1）。|


#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>若要重新啟動或關閉 Windows PowerShell 的控制站的 StorSimple
執行下列步驟，以關閉或重新啟動單一控制器 StorSimple 裝置從 Azure 傳統入口網站上。


1. Access 使用循序主控台或 telnet 工作階段從遠端電腦的裝置。 連線到控制器 0 或控制器 1[使用 PuTTY 連線至裝置循序主控台](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console)中的步驟進行。

1. 在循序主控台] 功能表中，選擇 [**登入的完整功能**的選項 1，]。

1. 橫幅在郵件中，記下您已連線到 （控制器 0 或控制器 1） 的控制站及是否為作用中] 或 [被動 （備用） 控制器。
    - 若要關閉單一控制器，出現提示時，請輸入︰

        `Stop-HcsController`

        這會關閉您已連線到控制器。 如果您停止使用中的控制站，然後將其將無法透過被動式控制器在關閉之前。
    - 若要重新啟動控制站，出現提示時，請輸入︰

        `Restart-HcsController`

        這會重新啟動您已連線到控制器。 如果您重新啟動的作用中的控制站，它將無法透過被動式控制器重新啟動之前。


## <a name="shut-down-a-storsimple-device"></a>關閉 StorSimple 裝置

本節說明如何關閉計算或失敗的 StorSimple 裝置的遠端電腦。 裝置已關閉後兩個裝置控制站關機。 裝置在實體上移] 或採取登出服務時，就會完成裝置關閉。

> [AZURE.IMPORTANT] 您關閉該裝置前，請檢查裝置元件的狀況。 瀏覽至**裝置 > 進行的維修作業 > 硬體狀態**，並確認所有元件的 LED 狀態為綠色。 僅以良好的裝置必須綠色的狀態。 如果您的裝置已被關機]，以取代將元件，您會看到失敗 （紅色） 或降級 （黃色） 各元件的狀態。

#### <a name="to-shut-down-a-storsimple-device"></a>若要關閉 StorSimple 裝置

1. 若要找出並關閉您的裝置上的被動式控制站使用[重新啟動或關閉控制站](#restart-or-shut-down-a-single-controller)的程序。 您可以針對 StorSimple Azure 傳統入口網站中，或在 Windows PowerShell 中執行這項作業。
2. 重複上述的步驟，以關閉 [作用中的控制器。
3. 您現在需要查看裝置的後平面。 兩個控制器完全關閉後，在這兩個控制站的狀態 Led 應該閃爍紅色。 如果您需要將裝置完全關閉這次，翻轉 Power 和製冷模組 (PCMs) 上的 power 切換至 [關閉] 位置。 這應該關閉裝置。


<!--#### To shut down a StorSimple device in Windows PowerShell for StorSimple

1. Connect to the serial console of the StorSimple device by following the steps in [Use PuTTY to connect to the device serial console](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-serial-console).

1. In the serial console menu, verify from the banner message that the controller you are connected to is the passive controller. If you are connected to the active controller, disconnect from this controller and connect to the other controller.

1. In the serial console menu, choose option 1, **log in with full access**.

1. At the prompt, type:

    `Stop-HCSController`

    This should shut down the current controller. To verify whether the shutdown has finished, check the back of the device. The controller status LED should be solid red.

1. Repeat steps 1 through 4 to connect to the active controller and then shut it down.

1. After both the controllers are completely shut down, the status LEDs on both should be blinking red. If you need to turn off the device completely at this time, flip the power switches on both Power and Cooling Modules (PCMs) to the OFF position.-->

## <a name="reset-the-device-to-factory-default-settings"></a>重設為原廠預設設定的裝置

> [AZURE.IMPORTANT] 如果您要重設為原廠預設設定的裝置，請連絡 Microsoft 支援服務。 只有在 Microsoft 支援服務搭配應如下所述的程序。

此程序說明如何重設您的 Microsoft Azure StorSimple 裝置的 StorSimple 使用 Windows PowerShell 的工廠預設設定。
重設裝置移除所有的資料與設定預設整個叢集。

執行下列步驟，以重設您的 Microsoft Azure StorSimple 裝置廠預設設定︰

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>若要重設為預設設定在 Windows PowerShell 中 StorSimple 的裝置

1. 透過其循序主控台存取裝置。 檢查以確保您已連線到作用中的控制器橫幅訊息。

1. 在循序主控台] 功能表中，選擇 [**登入的完整功能**的選項 1，]。

1. 出現提示時，輸入下列命令以重設整個叢集，移除所有的資料、 中繼資料，以及控制站設定︰

    `Reset-HcsFactoryDefault`

    若要改為重設單一控制器，請使用 [[重設 HcsFactoryDefault](http://technet.microsoft.com/library/dn688132.aspx) cmdlet 與`-scope`參數。)

    系統會重新啟動多次。 重設已順利完成時，您會收到通知。 根據系統模型，可能需要 45 到 60 分鐘的時間和 60 90 分鐘才能完成此程序從 8600 8100 的裝置。

    > [AZURE.TIP]

    > - 如果您使用的更新 1.2 或更舊版本使用`–SkipFirmwareVersionCheck`參數略過檢查韌體版本 (否則，您會看到韌體不相符的錯誤︰ 工廠重設無法繼續，因為韌體版本不相符。 ).

    > - 工廠重設程序可能失敗的 StorSimple 裝置，在政府版入口網站執行更新 1 或 1.1 （英文），且已執行成功的一個或兩控制器取代 （與取代控制站出貨更新前 1 軟體）。 發生這種情況時工廠重設圖像已驗證的 SHA1 檔案不存在的控制器上更新前 1 軟體的目前狀態。 如果您看到這個工廠重設失敗，請連絡 Microsoft 支援，以協助您接下來的步驟。 取代控制站出貨從工廠更新 1 或更新版本的軟體，不會出現此問題。


## <a name="questions-and-answers-about-managing-device-controllers"></a>問與答管理裝置控制站

在此區段中，我們有合併彙算的常見問題集一些關於管理 StorSimple 裝置控制站。

**問:。** 如果在裝置上的兩個控制站健全，則，會發生什麼情況上和我重新啟動或關閉 [作用中的控制器？

**答︰** 如果您的裝置上的兩個控制站是健全及關閉，系統會提示您確認。 您可以選擇︰

- **重新啟動作用中的控制站**– 您就會收到重新啟動作用中的控制器會導致無法透過被動式控制站的裝置。 控制站會重新啟動。

- **關閉使用中的控制器**– 您將會收到通知，關閉 [作用中的控制器會導致停機時間。 您也必須推控制器上開啟裝置上的 [power] 按鈕。

**問:。** 如果在裝置上的被動式控制站無法使用或已關閉，我要重新啟動或關閉 [作用中的控制站，會發生什麼情況？

**答︰** 如果您的裝置上的被動式控制站無法使用或已關閉，並選擇︰

- **重新啟動作用中的控制站**– 您將會收到通知，請繼續作業時，會導致服務]，暫時停止運作，而且系統會提示您確認。

- **關閉使用中的控制器**– 您將會收到通知，繼續作業會導致停機時間和，您需要在裝置上開啟的一或兩個控制站推 power] 按鈕。 系統會提示您確認。

**問:。** 當沒有控制器重新啟動或關閉無法進度嗎？

**答︰** 如果重新啟動或關閉 [控制器可能會失敗︰

- 裝置更新正在進行。

- 控制站重新啟動已在進行中。

- 控制站關機正在進行。

**問:。** 您如何可以找出如果重新啟動或關閉控制器？

**答︰** 您可以檢查在進行的維修作業] 頁面上的控制器狀態。 控制狀態會指出控制器是否已重新啟動或關閉。 此外，如果控制器已重新啟動或關閉 [提醒] 頁面將包含參考警示。 控制站重新啟動和關閉作業也會記錄在作業的記錄。 如需作業記錄的詳細資訊，請移至[檢視作業的記錄檔](storsimple-service-dashboard.md#view-the-operations-logs)。

**問:。** 有任何影響到當做控制器容錯移轉 I/o 嗎？

**答︰** TCP 之間的連線啟動器和使用中文控制器當做控制器容錯移轉，將會重設，但被動式控制器假設作業時就會重新建立。 這項作業期間可能會暫時 （小於 30 秒） 暫停 I/O 啟動器和裝置之間的活動。

**問:。** 如何傳回我的控制器服務關閉並移除後？

**答︰** 若要返回服務控制器，您必須將它插入底座[取代控制器模組 StorSimple 裝置上的](storsimple-controller-replacement.md)所述。

## <a name="next-steps"></a>後續步驟

- 如果您無法使用此教學課程中，[連絡 Microsoft 支援服務](storsimple-contact-microsoft-support.md)中所列的程序來解決您 StorSimple 裝置控制站發生任何問題。

- 若要進一步瞭解如何使用 StorSimple 管理員服務，請參閱[管理您的 StorSimple 裝置 StorSimple 管理員服務](storsimple-manager-service-administration.md)。
