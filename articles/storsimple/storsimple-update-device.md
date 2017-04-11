<properties
   pageTitle="更新您的 StorSimple 裝置 |Microsoft Azure"
   description="說明如何安裝一般和維護模式更新，並快速修正程式使用 StorSimple 更新功能。"
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="06/28/2016"
   ms.author="v-sharos" />

# <a name="update-your-storsimple-8000-series-device"></a>更新您的 StorSimple 8000 數列裝置

## <a name="overview"></a>概觀

StorSimple 更新功能可讓您輕鬆地保持為最新資訊 StorSimple 裝置。 根據更新類型，您可以將更新套用至裝置，透過 Azure 傳統入口網站，或是透過 Windows PowerShell 介面。 本教學課程說明更新類型，以及如何安裝每個。

您可以套用兩種類型的裝置的更新︰ 

- 一般 （或 [標準模式） 更新
- 維護模式更新

您可以安裝透過 Azure 傳統入口網站或 Windows PowerShell; 定期更新不過，您必須使用 Windows PowerShell 來安裝進行的維修作業模式更新。 

每個更新類型如下所述分別。

### <a name="regular-updates"></a>一般更新

一般更新是非破壞裝置是在 [標準模式時可以安裝的更新。 透過 Microsoft 更新網站會套用這些更新，每個裝置控制站。 

> [AZURE.IMPORTANT] 控制站容錯移轉可能更新程序期間會發生。 不過，但不會影響系統的可用性] 或 [作業。

- 如何安裝定期更新透過 Azure 傳統入口網站的詳細資訊，請參閱 [使用透過 Azure 傳統 portal(#install-regular-updates-via-the-azure-classic-portal) 安裝定期更新。

- 您也可以用 StorSimple 安裝 Windows PowerShell 透過定期更新。 如需詳細資訊，請參閱[安裝 Windows PowerShell 的 StorSimple 透過定期更新](#install-regular-updates-via-windows-powershell-for-storsimple)。

### <a name="maintenance-mode-updates"></a>維護模式更新

維護模式更新是破壞的更新，例如磁碟韌體升級。 更新要求放到維護模式的裝置。 如需詳細資訊，請參閱[步驟 2︰ 輸入維護模式](#step2)。 您無法使用 Azure 傳統入口網站，才能安裝進行的維修作業模式更新。 不過，您必須使用 Windows PowerShell 的 StorSimple。 

如需如何安裝進行的維修作業模式更新的詳細資訊，請參閱[透過 StorSimple 的 Windows PowerShell 安裝進行的維修作業模式更新](#install-maintenance-mode-updates-via-windows-powershell-for-storsimple)。

> [AZURE.IMPORTANT] 維護模式更新必須分別套用至每個控制站。 

## <a name="install-regular-updates-via-the-azure-classic-portal"></a>安裝透過 Azure 傳統入口網站的一般更新

若要將更新套用至 StorSimple 裝置，您可以使用 [Azure 傳統入口網站。

[AZURE.INCLUDE [storsimple-install-updates-manually](../../includes/storsimple-install-updates-manually.md)]

## <a name="install-regular-updates-via-windows-powershell-for-storsimple"></a>安裝 StorSimple 透過 Windows PowerShell 的一般更新

或者，您可以使用 Windows PowerShell 的 StorSimple 套用一般 （標準模式） 更新。

> [AZURE.IMPORTANT] 雖然您可以安裝 Windows PowerShell 用 StorSimple 定期更新，我們強烈建議您安裝透過 Azure 傳統入口網站的定期更新。 更新 1 開始，測試執行檢查之前從入口網站安裝更新。 這些測試檢查會先取得失敗，並確保更順暢。 

[AZURE.INCLUDE [storsimple-install-regular-updates-powershell](../../includes/storsimple-install-regular-updates-powershell.md)]

## <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>安裝 StorSimple 透過 Windows PowerShell 進行的維修作業模式更新

您可以使用 Windows PowerShell 的 StorSimple 來進行的維修作業模式更新套用至您的 StorSimple 裝置。 在此模式下暫停的所有 I/O 要求。 也會停止等服務的靜態隨機存取記憶體 （開機項目識別碼） 或叢集服務。 兩個控制站會重新開機，當您輸入或結束這個模式。 當您結束這個模式時，所有服務會繼續，而且應該健全。 （這可能需要幾分鐘的時間）。

如果您要套用進行的維修作業模式更新時，您會收到通知，透過 Azure 傳統入口網站，必須安裝更新。 使用 Windows PowerShell 的 StorSimple 安裝更新的相關指示中會包含此通知。 更新您的裝置之後，使用相同的程序來變更為標準模式的裝置。 如需逐步指示，請參閱[步驟 4︰ 結束維護模式](#step4)。

> [AZURE.IMPORTANT] 
> 
> - 之前，請先進行的維修作業模式，請確認兩個裝置控制站健全檢查 Azure 傳統入口網站中的 [**進行的維修作業**] 頁面上的**硬體狀態**。 如果沒有健全控制器，請連絡 Microsoft 支援服務的下一個步驟。 如需詳細資訊，請移至連絡 Microsoft 支援人員。 
> - 當您在進行的維修作業模式時，您需要其他控制器上一個控制器上，然後在第一次套用更新。

### <a name="step-1-connect-to-the-serial-console-a-namestep1"></a>步驟 1︰ 連線至循序主控台<a name="step1">

首先，請使用應用程式，例如 PuTTY 存取循序主控台。 下列程序說明如何使用 PuTTY 連線至循序主控台。

[AZURE.INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="step-2-enter-maintenance-mode-a-namestep2"></a>步驟 2︰ 輸入維護模式<a name="step2">

連接到主控台之後，判斷是否有安裝，然後輸入進行的維修作業模式]，安裝的更新。

[AZURE.INCLUDE [storsimple-enter-maintenance-mode](../../includes/storsimple-enter-maintenance-mode.md)]

### <a name="step-3-install-your-updates-a-namestep3"></a>步驟 3︰ 安裝更新<a name="step3">

接下來，請安裝更新。

[AZURE.INCLUDE [storsimple-install-maintenance-mode-updates](../../includes/storsimple-install-maintenance-mode-updates.md)]
 
### <a name="step-4-exit-maintenance-mode-a-namestep4"></a>步驟 4︰ 結束維護模式<a name="step4">

最後，結束維護模式。

[AZURE.INCLUDE [storsimple-exit-maintenance-mode](../../includes/storsimple-exit-maintenance-mode.md)]

## <a name="install-hotfixes-via-windows-powershell-for-storsimple"></a>安裝 StorSimple 快速修正程式透過 Windows PowerShell

不同於 Microsoft Azure StorSimple 的更新，快速修正程式已安裝共用資料夾中。 以的更新，有兩種類型的快速修正程式︰ 

- 一般快速修正程式 
- 維護模式快速修正程式  

下列程序說明如何使用 Windows PowerShell 的 StorSimple 安裝一般和維護模式快速修正程式。

[AZURE.INCLUDE [storsimple-install-regular-hotfixes](../../includes/storsimple-install-regular-hotfixes.md)]

[AZURE.INCLUDE [storsimple-install-maintenance-mode-hotfixes](../../includes/storsimple-install-maintenance-mode-hotfixes.md)]

## <a name="what-happens-to-updates-if-you-perform-a-factory-reset-of-the-device"></a>會發生什麼情況更新如果您執行的裝置工廠重設？

如果裝置重設為原廠設定，所有的更新都會遺失。 工廠重設裝置註冊並設定之後，您必須手動安裝 StorSimple 透過 Azure 傳統入口網站及/或 Windows PowerShell 的更新。 如需有關工廠重設，請參閱[重設為原廠預設設定的裝置](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings)。

## <a name="next-steps"></a>後續步驟

- 深入瞭解如何[使用 Windows PowerShell 來管理您的 StorSimple 裝置 StorSimple 的](storsimple-windows-powershell-administration.md)。
- 瞭解更多關於[使用 StorSimple 管理員服務來管理您的 StorSimple 裝置](storsimple-manager-service-administration.md)。
