<properties 
   pageTitle="變更 StorSimple 裝置模式 |Microsoft Azure"
   description="說明 StorSimple 裝置模式，並說明如何使用 Windows PowerShell 的 StorSimple 變更裝置模式。"
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
   ms.date="06/17/2016"
   ms.author="alkohli" />

# <a name="change-the-device-mode-on-your-storsimple-device"></a>變更裝置模式 StorSimple 裝置上

本文提供各種 StorSimple 裝置可進行操作的模式的簡短描述。 StorSimple 裝置可以函數中三種模式︰ 標準、 維護和修復。 

閱讀本文之後，您就會知道︰

- 哪些 StorSimple 裝置模式
- 如何找出哪一種模式 StorSimple 裝置位於
- 如何變更從標準要進行的維修作業模式，*反之亦然。*


透過 Windows PowerShell 介面 StorSimple 裝置的只能用於到上述管理工作。

## <a name="about-storsimple-device-modes"></a>關於 StorSimple 裝置模式

StorSimple 裝置能夠在標準模式]、 [維護] 或 [復原模式中作業。 以下簡要描述每一個模式。

### <a name="normal-mode"></a>標準模式

這被定義為完全設定 StorSimple 裝置的相關操作標準模式。 根據預設，您的裝置應該在 [標準模式。

### <a name="maintenance-mode"></a>維護模式

有時候 StorSimple 裝置可能需要放入維護模式。 此模式下，可讓您在裝置上維護及安裝破壞的更新，例如相關磁碟韌體。

您可以置於系統進行的維修作業模式僅透過 Windows PowerShell 的 StorSimple。 在此模式下暫停的所有 I/O 要求。 也會停止等服務的靜態隨機存取記憶體 （開機項目識別碼） 或叢集服務。 當您輸入或結束這個模式時，會重新啟動控制站。 當您離開維護模式時，所有服務會繼續，而且應該健全。 這可能需要幾分鐘的時間。

>[AZURE.NOTE]正常運作的裝置上僅支援**進行的維修作業模式。不支援在其中一項或兩個控制器無法正常在裝置上。**
</br>

### <a name="recovery-mode"></a>復原] 模式

復原模式可以描述為 「 使用網路支援 Windows 安全模式 」。 復原模式下執行 Microsoft 技術支援小組，並讓他們執行診斷系統上。 復原] 模式的主要目的是擷取系統記錄。

如果您的系統進入復原] 模式時，您應該連絡 Microsoft 支援服務的下一個步驟。 如需詳細資訊，請移至[連絡 Microsoft 支援人員](storsimple-contact-microsoft-support.md)。

>[AZURE.NOTE] **您無法將裝置放在 [復原] 模式。如果裝置是問題，修復模式會嘗試取得 Microsoft 支援人員可以檢查其狀態的裝置。**

## <a name="determine-storsimple-device-mode"></a>決定 StorSimple 裝置模式

#### <a name="to-determine-the-current-device-mode"></a>若要決定目前的裝置模式

1. [使用 PuTTY 連線至裝置循序主控台](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console)中的步驟進行登入裝置的循序主控台。

2. 查看橫幅循序主控台] 功能表中訊息的裝置。 此訊息明確指出裝置是否在進行的維修作業] 或 [復原] 模式中。 如果郵件沒有任何相關系統模式的特定資訊，請使用的裝置是以標準模式。

## <a name="change-the-storsimple-device-mode"></a>變更 StorSimple 裝置模式 

您可以執行維護工作，或安裝進行的維修作業模式更新，以將 StorSimple 裝置放入維護模式 （在 [標準] 模式）。 執行下列程序，請輸入或結束進行的維修作業模式。

> [AZURE.IMPORTANT] 之前，請先進行的維修作業模式，請確認兩個裝置控制站健全存取 Azure 傳統入口網站中的 [**進行的維修作業**] 頁面上的**硬體狀態**。 如果一或兩個控制站不是健全的請連絡 Microsoft 支援服務的下一個步驟。 如需詳細資訊，請移至[連絡 Microsoft 支援人員](storsimple-contact-microsoft-support.md)。

#### <a name="to-enter-maintenance-mode"></a>若要輸入維護模式

1. [使用 PuTTY 連線至裝置循序主控台](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console)中的步驟進行登入裝置的循序主控台。

2. 在循序主控台] 功能表中，選擇 [**登入的完整功能**的選項 1，]。 出現提示時，提供**裝置系統管理員的密碼**。 預設的密碼︰ `Password1`。

3. 在命令提示字元中，輸入 

    `Enter-HcsMaintenanceMode`

4. 您會看到通知您進行的維修作業模式會中斷所有 I/O 要求與伺服器的連線 Azure 傳統入口網站，且系統會提示您確認您的警告訊息。 輸入**Y**輸入維護模式。

5. 兩個控制站會重新啟動。 重新啟動完成後，會出現另一封郵件，指出裝置中進行的維修作業模式。


#### <a name="to-exit-maintenance-mode"></a>若要結束維護模式

1. 登入裝置的循序主控台。 驗證從您的裝置已進行的維修作業模式中橫幅訊息。

2. 在命令提示字元中，輸入︰

    `Exit-HcsMaintenanceMode`

3. 會出現警告訊息和確認訊息。 輸入**Y**結束進行的維修作業模式。

4. 兩個控制站會重新啟動。 重新啟動完成後，會出現另一封郵件，指出裝置以標準模式。


## <a name="next-steps"></a>後續步驟

瞭解如何將 StorSimple 裝置上的 [[套用標準模式] 和 [維護模式更新](storsimple-update-device.md)。

