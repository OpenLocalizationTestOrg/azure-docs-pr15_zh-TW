<properties
   pageTitle="StorSimple 裝置上安裝更新 2 |Microsoft Azure"
   description="說明如何 StorSimple 8000 數列裝置上安裝 StorSimple 8000 數列更新 2。"
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
   ms.date="09/21/2016"
   ms.author="alkohli" />

# <a name="install-update-2-on-your-storsimple-device"></a>StorSimple 裝置上安裝更新 2

## <a name="overview"></a>概觀

本教學課程說明如何透過 Azure 傳統入口網站執行在舊版的軟體 StorSimple 裝置上安裝更新 2。 教學課程也說明更新閘道設定 StorSimple 裝置的資料 0 以外的網路介面上，並想要更新從更新前 1 軟體版本時所需的步驟。

更新 2 包含裝置軟體更新、 LSI 驅動程式的更新及磁碟韌體更新。 裝置軟體 LSI 更新非破壞的更新，並可套用透過 Azure 傳統入口網站。 磁碟韌體破壞的更新並更新只能套用透過 Windows PowerShell 介面的裝置。

> [AZURE.IMPORTANT]

> -  您可能不會出現更新 2 立即由於階段的首展的更新。 掃描幾天一次更新為此更新才可使用推出。
> - 手動和自動預先檢查一組完成安裝]，以決定而言硬體狀態和網路連線的裝置狀況之前。 只有當您將更新套用從 Azure 傳統入口網站執行這些測試檢查。
> - 我們建議您安裝的軟體和驅動程式的更新，透過 Azure 傳統入口網站。 如果測試更新閘道器核取失敗入口網站中，您只應該移到的裝置 （若要安裝的更新） Windows PowerShell 介面。 更新可能需要 4-7 小時，才能安裝 （包括 Windows 更新）。 透過 Windows PowerShell 的介面裝置，必須安裝進行的維修作業模式更新。 破壞更新進行的維修作業模式更新時，這些就會導致您的裝置的向下時間。
> - 如果執行選擇性 StorSimple 快照管理員，請確定的您升級您的快照管理員版本更新 2 之前更新裝置。

[AZURE.INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-2-via-the-azure-classic-portal"></a>安裝更新 2 透過 Azure 傳統入口網站

執行下列步驟，以[更新 2](storsimple-update2-release-notes.md)更新您的裝置。


> [AZURE.NOTE]
更新 2 讓 Microsoft 來擷取其他裝置的診斷資訊。 如此一來，我們的作業小組會識別有問題的裝置，我們就更有效地配備從裝置收集的資訊和診斷問題。 接受更新 2，讓我們提供此主動式支援。

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

12. 確認您的裝置正在執行**StorSimple 8000 數列更新 2 (6.3.9600.17673)**。 應該也修改**上次更新日期**。 您也會看到有可用的維護模式更新 （此訊息可能會繼續以顯示最多 24 小時後安裝的更新）。

    維護模式更新是裝置的破壞的更新，導致裝置停機時間，並僅透過您的 Windows PowerShell 介面會套用。 在某些情況下當您執行的更新 1.2 磁碟韌體可能已經是最新狀態，在您不需要安裝任何進行的維修作業模式更新這種情況。

13. 搜尋並下載 KB3121899，安裝磁碟韌體更新 （其他更新應該已經安裝現在） 使用[下載快速修正程式](#to-download-hotfixes)中所列的步驟，以下載進行的維修作業模式更新。

13. 依照[安裝並確認進行的維修作業模式快速修正程式](#to-install-and-verify-maintenance-mode-hotfixes)安裝進行的維修作業模式更新中所列的步驟。


## <a name="install-update-2-as-a-hotfix"></a>為重要修正程式安裝更新 2

如果您沒有閘道器檢查嘗試安裝的更新，透過 Azure 傳統入口網站時，請使用此程序。 您必須將閘道指派給非資料 0 網路介面，並在您的裝置執行軟體版本之前更新 1，就會失敗核取。

軟體版本可使用 hotfix 方法升級為更新 0.1、 更新 0.2，並更新 0.3、 更新 1、 更新 1.1 （英文） 及更新 1.2。 Hotfix 方法包含下列三個步驟︰

- 下載快速修正程式從 Microsoft Update 類別目錄。
- 安裝並確認一般模式快速修正程式。
- 安裝並確認進行的維修作業模式 hotfix。

若要安裝更新 2 為重要修正程式，您必須下載並安裝下列動作︰

| 順序  | KB        | 描述                    | 更新類型  |
|--------|-----------|-------------------------|------------- |
| 1      | KB3121901 | 軟體更新         |  一般     |
| 2      | KB3121900 | LSI 驅動程式              |  一般     |
| 3      | KB3080728 | Storport 修正 </br> Windows Server 2012 R2 |  一般     |
| 4      | KB3090322 | 太空修正 </br> Windows Server 2012 R2 |  一般     |
| 5      | KB3121899 | 磁碟韌體           | 進行的維修作業  |


> [AZURE.IMPORTANT]
>
> - 如果您的裝置執行版本 (GA) 版本，請連絡[Microsoft 支援服務](storsimple-contact-microsoft-support.md)為協助您進行更新。
> - 此程序必須執行一次套用更新 2。 若要套用後續的更新，您可以使用 Azure 傳統入口網站。
> - 每次安裝可以需要關於 20 分鐘才能完成。 總安裝時接近 2 的時數。
> - 之前使用此程序，若要套用的更新，請確定兩個裝置控制站的線上，而且所有硬體元件都健全。

執行下列步驟，以套用此更新為重要修正程式。

[AZURE.INCLUDE [storsimple-install-update2-hotfix](../../includes/storsimple-install-update2-hotfix.md)]

[AZURE.INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]



## <a name="next-steps"></a>後續步驟

進一步瞭解[更新 2 版本](storsimple-update2-release-notes.md)。
