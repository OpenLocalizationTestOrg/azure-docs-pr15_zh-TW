<properties
   pageTitle="StorSimple 裝置上安裝更新 2.2 |Microsoft Azure"
   description="說明如何 StorSimple 8000 數列裝置上安裝 StorSimple 8000 數列更新 2.2。"
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
   ms.date="08/02/2016"
   ms.author="alkohli" />

# <a name="install-update-22-on-your-storsimple-device"></a>StorSimple 裝置上安裝更新 2.2

## <a name="overview"></a>概觀

本教學課程說明如何更新 2.2 在裝置上安裝 StorSimple 透過 Azure 傳統入口網站執行在舊版的軟體和使用 hotfix 方法。 閘道器已在 StorSimple 裝置的資料 0 以外的網路介面，而您嘗試從更新前 1 軟體版本更新時，會使用 hotfix 方法。

更新 2.2 包含裝置軟體、 WMI，以及 iSCSI 更新。 如果從 2.1 版的更新，只裝置軟體更新必須套用。 如果更新從更新前 2 版本，您也必須套用 LSI 驅動程式、 太空、 Storport，以及磁碟韌體更新。 裝置軟體、 WMI、 iSCSI、 LSI 驅動程式、 太空和 Storport 修正非破壞的更新，並可套用透過 Azure 傳統入口網站。 磁碟韌體破壞的更新並更新只能套用透過 Windows PowerShell 介面的裝置。 

> [AZURE.IMPORTANT]

> - 手動和自動預先檢查一組完成安裝]，以決定龐大硬體狀態和網路連線的裝置狀況之前。 只有當您將更新套用從 Azure 傳統入口網站執行這些測試檢查。
> - 我們建議您安裝的軟體和驅動程式的更新，透過 Azure 傳統入口網站。 如果測試更新閘道器核取失敗入口網站中，您只應該移到的裝置 （若要安裝的更新） Windows PowerShell 介面。 您要更新的版本而定，更新可能需要安裝的 1.5 2.5 小時。 透過 Windows PowerShell 介面的裝置，必須安裝進行的維修作業模式更新。 破壞更新進行的維修作業模式更新時，這些就會導致您的裝置的向下時間。
> - 如果執行選擇性 StorSimple 快照管理員，請確定的升級您的快照管理員版本成更新 2.2 之前更新裝置。

[AZURE.INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-22-via-the-azure-classic-portal"></a>安裝更新 2.2 透過 Azure 傳統入口網站

執行下列步驟，以[更新 2.2](storsimple-update21-release-notes.md)更新您的裝置。


> [AZURE.NOTE]
如果您要套用更新 2 或更新版本 （包括更新 2.1)，Microsoft 無法擷取其他裝置的診斷資訊。 如此一來，我們的作業小組會識別有問題的裝置，我們就更有效地配備從裝置收集的資訊和診斷問題。 接受更新 2 或更新版本，您可以讓我們提供此積極支援。

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

12. 確認您的裝置正在執行**StorSimple 8000 數列更新 2.2 (6.3.9600.17708)**。 **上次更新日期**應該也會將修改。 

    如果您要更新從更新 2 之前的版本，您也會看到有可用的維護模式更新 （此訊息可能會繼續以顯示最多 24 小時後安裝的更新）。

    維護模式更新是裝置的破壞的更新，導致裝置停機時間，並僅透過您的 Windows PowerShell 介面會套用。 在某些情況下當您執行的更新 1.2 磁碟韌體可能已經是最新狀態，在您不需要安裝任何進行的維修作業模式更新這種情況。

    如果您要更新從更新 2，現在應該更新您的裝置。 您可以略過其餘的步驟。

13. 搜尋並下載 KB3121899，安裝磁碟韌體更新 （其他更新應該已經安裝現在） 使用[下載快速修正程式](#to-download-hotfixes)中所列的步驟，以下載進行的維修作業模式更新。

13. 依照[安裝並確認進行的維修作業模式快速修正程式](#to-install-and-verify-maintenance-mode-hotfixes)安裝進行的維修作業模式更新中所列的步驟。 

  

## <a name="install-update-22-as-a-hotfix"></a>安裝更新 2.2 為重要修正程式

如果您沒有閘道器檢查嘗試安裝的更新，透過 Azure 傳統入口網站時，請使用此程序。 核取失敗，您必須將閘道指派給非資料 0 網路介面，並在您的裝置執行之前更新 1 軟體版本。

可以使用 hotfix 方法升級軟體版本是︰

- 更新 0.1 0.2、 0.3
- 更新 1、 1.1 （英文）、 1.2
- 更新 2 2.1 

> [AZURE.IMPORTANT]
>
> - 如果您的裝置執行版本 (GA) 版本，請連絡[Microsoft 支援服務](storsimple-contact-microsoft-support.md)，協助您更新。

Hotfix 方法包含下列三個步驟︰

- 下載快速修正程式從 Microsoft Update 類別目錄。
- 安裝並確認一般模式快速修正程式。
- 安裝並確認進行的維修作業模式 hotfix （只有在從更新前 2 軟體更新）。

#### <a name="download-updates-for-a-device-running-update-21-software"></a>下載裝置執行更新 2.1 軟體的相關更新

**如果您的裝置正在執行更新 2.1**，您必須下載只裝置軟體更新 KB3179904。 只安裝做為 「 所有 hcsmdssoftwareudpate 」 開頭的二進位檔案。 無法安裝 Cis 和 MDS 代理程式更新做為開頭`all-cismdsagentupdatebundle`。 要這麼做會造成錯誤。 這是非破壞更新不會中斷 IO 與裝置不會有任何停機時間。


#### <a name="download-updates-for-a-device-running-update-2-software"></a>下載裝置執行更新 2 軟體的相關更新

**如果您的裝置正在執行更新 2**，您必須下載並安裝下列並依指定順序︰

| 順序  | KB        | 描述                    | 更新類型  | 安裝時間 |
|--------|-----------|-------------------------|------------- |-------------|
| 1。      | KB3179904 | 軟體更新與 #42;  |  一般 <br></br>非破壞     | ~ 45 分鐘 |
| 2。      | KB3146621 | iSCSI 套件 | 一般 <br></br>非破壞  | ~ 20 分鐘 |
| 3。      | KB3103616 | WMI 套件 |  一般 <br></br>非破壞      | ~ 12 分鐘 |


 & #42; 包含兩個的二進位檔案*附註，軟體更新︰ 裝置軟體更新做為開頭`all-hcsmdssoftwareupdate`和做為開頭 Cis 和 Mds 代理程式`all-cismdsagentupdatebundle`。Cis 和 Mds 代理程式之前，必須先安裝裝置軟體更新。您也必須重新啟動透過作用中的控制器`Restart-HcsController`cmdlet 後套用 Cis 和 MDS 代理程式的更新 （和前套用剩餘更新）。* 

#### <a name="download-updates-for-a-device-running-pre-update-2-software"></a>下載裝置執行更新前 2 軟體的相關更新

**如果您的裝置執行的版本 0.2，0.3，1.0 1.1 （英文）**，您必須下載並安裝 LSI 驅動程式 」 和 「 韌體更新除了軟體、 iSCSI 和 WMI 更新。 如果您執行的更新 1.2 或 2，已經安裝此更新。 
 
| 順序  | KB        | 描述                    | 更新類型  | 安裝時間 |
|--------|-----------|-------------------------|------------- |-------------|
| 4。      | KB3121900 | LSI 驅動程式 」 和 「 韌體             |  一般 <br></br>非破壞      | ~ 20 分鐘 |


<br></br>
**如果您的裝置執行的 0.2，0.3，1.0、 1.1 （英文） 和 1.2 版**，您必須下載並安裝太空和 Storport 修正。 如果您執行的更新 2 這些便已安裝的。

| 順序  | KB        | 描述                    | 更新類型  | 安裝時間 |
|--------|-----------|-------------------------|------------- |-------------|
| 5。      | KB3090322 | 太空修正 </br> Windows Server 2012 R2 |  一般 <br></br>非破壞      | ~ 20 分鐘 |
| 6。      | KB3080728 | Storport 修正 </br> Windows Server 2012 R2 |  一般 <br></br>非破壞      | ~ 20 分鐘 |



<br></br>
您可能也需要安裝磁碟韌體更新。 您可以驗證您是否需要磁碟韌體更新，藉由執行`Get-HcsFirmwareVersion`指令程式。 如果您執行的韌體版本︰ `XMGG`， `XGEG`， `KZ50`， `F6C2`， `VR08`，然後您不需要安裝的更新。


| 順序  | KB        | 描述                    | 更新類型  | 安裝時間 |
|--------|-----------|-------------------------|------------- |-------------|
| 7。      | KB3121899 | 磁碟韌體              |  進行的維修作業 <br></br>破壞      | ~ 30 分鐘 |
 
<br></br>

> [AZURE.IMPORTANT]
>
> - 此程序必須執行一次套用更新 2.2。 若要套用後續的更新，您可以使用 Azure 傳統入口網站。
> - 如果更新更新 2，總安裝時關閉 1.5 小時。
> - 然後再使用此程序來套用更新，請確認裝置控制器 online 及所有的硬體為健全元件。

執行下列步驟，以下載並安裝。

[AZURE.INCLUDE [storsimple-install-update21-hotfix](../../includes/storsimple-install-update21-hotfix.md)]

[AZURE.INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>後續步驟

進一步瞭解[更新 2.1 發行](storsimple-update21-release-notes.md)。
