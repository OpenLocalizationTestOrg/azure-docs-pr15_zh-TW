<properties
   pageTitle="StorSimple 裝置上安裝更新 3 |Microsoft Azure"
   description="說明如何 StorSimple 8000 數列裝置上安裝 StorSimple 8000 數列更新 3。"
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
   ms.date="10/05/2016"
   ms.author="alkohli" />

# <a name="install-update-3-on-your-storsimple-device"></a>StorSimple 裝置上安裝更新 3

## <a name="overview"></a>概觀

本教學課程說明如何更新 3 在裝置上安裝 StorSimple 透過 Azure 傳統入口網站執行在舊版的軟體和使用 hotfix 方法。 閘道器已在 StorSimple 裝置的資料 0 以外的網路介面，而您嘗試從更新前 1 軟體版本更新時，會使用 hotfix 方法。

更新 3 包含裝置軟體 」、 「 LSI 驅動程式 」 和 「 韌體 Storport，並且太空更新。 如果更新更新 2 或較舊的版本，您也必須套用 iSCSI，WMI，並在某些情況下，磁碟韌體更新。 裝置軟體、 WMI、 iSCSI、 LSI 驅動程式、 太空和 Storport 修正非破壞的更新，並可套用透過 Azure 傳統入口網站。 磁碟韌體破壞的更新並更新只能套用透過 Windows PowerShell 介面的裝置。 

> [AZURE.IMPORTANT]

> - 手動和自動預先檢查一組完成安裝]，以決定龐大硬體狀態和網路連線的裝置狀況之前。 只有當您將更新套用從 Azure 傳統入口網站執行這些測試檢查。
> - 我們建議您安裝的軟體和驅動程式的更新，透過 Azure 傳統入口網站。 如果測試更新閘道器核取失敗入口網站中，您只應該移到的裝置 （若要安裝的更新） Windows PowerShell 介面。 您要更新的版本而定，更新可能需要安裝的 1.5 2.5 小時。 透過 Windows PowerShell 介面的裝置，必須安裝進行的維修作業模式更新。 破壞更新進行的維修作業模式更新時，這些就會導致您的裝置的向下時間。
> - 如果執行選擇性 StorSimple 快照管理員，請確定的您升級您的快照管理員版本更新 2 之前更新裝置。

[AZURE.INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-3-via-the-azure-classic-portal"></a>安裝更新 3 透過 Azure 傳統入口網站

執行下列步驟，以[更新 3](storsimple-update3-release-notes.md)更新您的裝置。


> [AZURE.NOTE]
如果您要套用更新 2 或更新版本 （包括更新 2.1)，Microsoft 無法擷取其他裝置的診斷資訊。 如此一來，我們的作業小組會識別有問題的裝置，我們就更有效地配備從裝置收集的資訊和診斷問題。 接受更新 2 或更新版本，您可以讓我們提供此積極支援。

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

12. 確認您的裝置正在執行**StorSimple 8000 數列更新 3 (6.3.9600.17759)**。 **上次更新日期**應該也會將修改。 

    如果您要更新從更新 2 之前的版本，您也會看到有可用的維護模式更新 （此訊息可能會繼續以顯示最多 24 小時後安裝的更新）。

    維護模式更新是裝置的破壞的更新，導致裝置停機時間，並僅透過您的 Windows PowerShell 介面會套用。 在某些情況下當您執行的更新 1.2 磁碟韌體可能已經是最新狀態，在您不需要安裝任何進行的維修作業模式更新這種情況。

    如果您是從更新 2 更新或更新版本，您的裝置現在應該可以最新版本。 您可以略過其餘的步驟。

13. 搜尋並下載 KB3121899，安裝磁碟韌體更新 （其他更新應該已經安裝現在） 使用[下載快速修正程式](#to-download-hotfixes)中所列的步驟，以下載進行的維修作業模式更新。

13. 依照[安裝並確認進行的維修作業模式快速修正程式](#to-install-and-verify-maintenance-mode-hotfixes)安裝進行的維修作業模式更新中所列的步驟。 

  

## <a name="install-update-3-as-a-hotfix"></a>為重要修正程式安裝更新 3

如果您沒有閘道器檢查嘗試安裝的更新，透過 Azure 傳統入口網站時，請使用此程序。 核取失敗，您必須將閘道指派給非資料 0 網路介面，並在您的裝置執行之前更新 1 軟體版本。

可以使用 hotfix 方法升級軟體版本是︰

- 更新 0.1 0.2、 0.3
- 更新 1、 1.1 （英文）、 1.2
- 更新 2、 2.1 2.2 

> [AZURE.IMPORTANT]
>
> - 如果您的裝置執行版本 (GA) 版本，請連絡[Microsoft 支援服務](storsimple-contact-microsoft-support.md)，協助您更新。

Hotfix 方法包含下列三個步驟︰

1.  下載快速修正程式從 Microsoft Update 類別目錄。

2.  安裝並確認一般模式快速修正程式。

3.  安裝並確認進行的維修作業模式 hotfix （只有在從更新前 2 軟體更新）。


#### <a name="download-updates-for-your-device"></a>下載更新您的裝置

**如果您的裝置執行更新 2.1 或 2.2**，您必須下載並安裝下列並依指定順序︰

| 順序  | KB        | 描述                    | 更新類型  | 安裝時間 |
|--------|-----------|-------------------------|------------- |-------------|
| 1。      | KB3186843 | 軟體更新與 #42;  |  一般 <br></br>非破壞     | ~ 45 分鐘 |
| 2。      | KB3186859 | LSI 驅動程式 」 和 「 韌體             |  一般 <br></br>非破壞      | ~ 20 分鐘 |
| 3。      | KB3121261 | Storport 和太空修正 </br> Windows Server 2012 R2 |  一般 <br></br>非破壞      | ~ 20 分鐘 |

& #42; 包含兩個的二進位檔案*附註，軟體更新︰ 裝置軟體更新做為開頭`all-hcsmdssoftwareupdate`和做為開頭 Cis 和 Mds 代理程式`all-cismdsagentupdatebundle`。Cis 和 Mds 代理程式之前，必須先安裝裝置軟體更新。您也必須重新啟動透過作用中的控制器`Restart-HcsController`cmdlet 後套用 Cis 和 Mds 代理程式的更新 （和前套用剩餘更新）。* 


**您的裝置是否正在執行更新 0.1、 0.2、 0.3、 1.0、 1.1 （英文）、 1.2 或 2.0**，您必須下載並安裝下列軟體、 LSI 驅動程式和韌體更新 （上述表格中顯示），除了在給定的程序︰

| 順序  | KB        | 描述                    | 更新類型  | 安裝時間 |
|--------|-----------|-------------------------|------------- |-------------|
| 4。      | KB3146621 | iSCSI 套件 | 一般 <br></br>非破壞  | ~ 20 分鐘 |
| 5。      | KB3103616 | WMI 套件 |  一般 <br></br>非破壞      | ~ 12 分鐘 |


<br></br>

**如果您的裝置執行的 0.2，0.3，1.0、 1.1 （英文） 和 1.2 版**，您可能也需要安裝磁碟韌體更新上方顯示前一個資料表中的所有更新。 您可以驗證您是否需要磁碟韌體更新，藉由執行`Get-HcsFirmwareVersion`指令程式。 如果您執行的韌體版本︰ `XMGG`， `XGEG`， `KZ50`， `F6C2`， `VR08`，然後您不需要安裝的更新。


| 順序  | KB        | 描述                    | 更新類型  | 安裝時間 |
|--------|-----------|-------------------------|------------- |-------------|
| 6。      | KB3121899 | 磁碟韌體              |  進行的維修作業 <br></br>破壞      | ~ 30 分鐘 |
 
<br></br>

> [AZURE.IMPORTANT]
>
> - 此程序必須執行一次套用更新 3。 若要套用後續的更新，您可以使用 Azure 傳統入口網站。
> - 從更新 2.2 更新，總安裝時間靠近 1.1 小時。
> - 然後再使用此程序來套用更新，請確認裝置控制器 online 及所有的硬體為健全元件。

執行下列步驟，以下載並安裝。

[AZURE.INCLUDE [storsimple-install-update3-hotfix](../../includes/storsimple-install-update3-hotfix.md)]

[AZURE.INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>後續步驟

進一步瞭解[更新 3 發行](storsimple-update3-release-notes.md)。
