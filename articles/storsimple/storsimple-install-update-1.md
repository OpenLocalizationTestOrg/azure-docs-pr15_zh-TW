<properties
   pageTitle="StorSimple 裝置上安裝更新 1.2 |Microsoft Azure"
   description="說明如何 StorSimple 8000 數列裝置上安裝 StorSimple 8000 數列更新 1.2。"
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
   ms.date="08/22/2016"
   ms.author="alkohli" />

# <a name="install-update-12-on-your-storsimple-device"></a>StorSimple 裝置上安裝更新 1.2

## <a name="overview"></a>概觀

本教學課程說明如何執行軟體版本之前更新 1 StorSimple 裝置上安裝更新 1.2。 教學課程也說明更新閘道設定 StorSimple 裝置的資料 0 以外的網路介面上時所需的額外步驟。

更新 1.2 包含裝置軟體更新與 LSI 驅動程式更新磁碟韌體更新。 軟體和 LSI 驅動程式更新非破壞的更新，並可套用透過 Azure 傳統入口網站。 磁碟韌體破壞的更新並更新只能套用透過 Windows PowerShell 介面的裝置。

您的裝置而定的是哪個版本執行，您可以決定是否更新 1.2 就會套用。 您可以瀏覽至您的裝置**儀表板**的 [**快速**] 區段，檢查您的裝置的軟體版本。

</br>

| 如果執行軟體版本...   | 入口網站中會發生什麼情況？                              |
|---------------------------------|--------------------------------------------------------------|
| 發行-GA                    | 如果您執行的發行版本 (GA)，不會套用此更新。 請[連絡 Microsoft 支援服務](storsimple-contact-microsoft-support.md)以更新您的裝置。|
| 更新 0.1                      | 入口網站適用於更新 1.2。                                |
| 更新 0.2                      | 入口網站適用於更新 1.2。                                |
| 更新 0.3                      | 入口網站適用於更新 1.2。                                |
| 更新 1                        | 將無法使用此更新。                           |
| 更新 1.1 （英文）                      | 將無法使用此更新。                           |

</br>

> [AZURE.IMPORTANT]

> -  您可能不會出現更新 1.2 立即由於階段的首展的更新。 掃描幾天一次更新為此更新才可使用推出。
> - 此更新包含一組手動和自動測試的檢查，以判定而言硬體狀態和網路連線裝置健康情況。 只有當您將更新套用從 Azure 傳統入口網站執行這些測試檢查。
> - 我們建議您安裝的軟體和驅動程式的更新，透過 Azure 傳統入口網站。 如果測試更新閘道器核取失敗入口網站中，您只應該移到的裝置 （若要安裝的更新） Windows PowerShell 介面。 更新可能需要 5 到 10 小時，才能安裝 （包括 Windows 更新）。 透過 Windows PowerShell 的介面裝置，必須安裝進行的維修作業模式更新。 破壞更新進行的維修作業模式更新時，這些就會導致您的裝置的向下時間。

[AZURE.INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-12-via-the-azure-classic-portal"></a>安裝更新 1.2 透過 Azure 傳統入口網站

執行下列步驟，以[更新 1.2](storsimple-update1-release-notes.md)更新您的裝置。 只有當您在裝置上的資料 0 網路介面上設定的閘道器，請使用此程序。

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

12. 確認您的裝置正在執行**StorSimple 8000 數列更新 1.2 (6.3.9600.17584)**。 應該也修改**上次更新日期**。 您也會看到有可用的維護模式更新 （此訊息可能會繼續以顯示最多 24 小時後安裝的更新）。

    維護模式更新是裝置的破壞的更新，導致裝置停機時間，並僅透過您的 Windows PowerShell 介面會套用。

    ![進行的維修作業] 頁面](./media/storsimple-install-update-1/InstallUpdate12_10M.png "進行的維修作業] 頁面")

13. 搜尋並下載 KB3063416，安裝磁碟韌體更新 （其他更新應該已經安裝現在） 使用[下載快速修正程式]( #to-download-hotfixes)中所列的步驟，以下載進行的維修作業模式更新。

13. 依照[安裝並確認進行的維修作業模式快速修正程式](#to-install-and-verify-maintenance-mode-hotfixes)安裝進行的維修作業模式更新中所列的步驟。

14. 在 Azure 傳統的入口網站中，瀏覽至 [**進行的維修作業**] 頁面，然後在頁面底部，按一下 [**掃描的更新**，檢查有任何 Windows 更新，然後按一下 [**安裝更新**]。 完成後之後的所有更新已成功安裝。



## <a name="install-update-12-on-a-device-that-has-a-gateway-configured-for-a-non-data-0-network-interface"></a>具有設定為非資料 0 網路介面閘道器的裝置上安裝更新 1.2

如果您嘗試安裝透過 Azure 傳統入口網站更新時失敗閘道器核取，您應該使用此程序。 您必須將閘道指派給非資料 0 網路介面，並在您的裝置執行軟體版本之前更新 1，就會失敗核取。 如果您的裝置沒有非資料 0 網路介面的閘道器，您可以更新您的裝置，直接從 Azure 傳統入口網站。 請參閱[安裝更新 1.2 透過 Azure 傳統入口網站](#install-update-1.2-via-the-azure-classic-portal)。

可以使用這個方法升級軟體版本是 0.1 會更新，更新 0.2 及更新 0.3。


> [AZURE.IMPORTANT]
>
> - 如果您的裝置執行版本 (GA) 版本，請連絡[Microsoft 支援服務](storsimple-contact-microsoft-support.md)為協助您進行更新。
> - 此程序必須執行一次套用更新 1.2。 若要套用後續的更新，您可以使用 Azure 傳統入口網站。

如果您的裝置正在執行更新前 1 軟體，有閘道器設定網路介面資料 0 以外，您可以套用更新 1.2 下列兩種方式︰

- **選項 1**︰ 下載此更新並將其套用使用`Start-HcsHotfix`從裝置的 Windows PowerShell 介面指令程式。 這是建議的方法。 **請勿使用這個方法若要套用更新 1.2，如果您的裝置執行更新 1.0 或更新 1.1 （英文）。**

- **選項 2**︰ 移除閘道器設定，並直接從 Azure 傳統入口網站安裝更新。


下列各節提供每個的詳細的指示。

## <a name="option-1-use-windows-powershell-for-storsimple-to-apply-update-12-as-a-hotfix"></a>若要套用為重要修正程式的更新 1.2 StorSimple 的選項 1︰ 使用 Windows PowerShell

如果您執行的更新 0.1 0.2 0.3，如果您的閘道器檢查失敗時，嘗試從 Azure 傳統入口網站安裝更新，您應該使用此程序。 如果您正在執行版本 (GA) 軟體，請更新您的裝置的[Microsoft 支援服務](storsimple-contact-microsoft-support.md)。

若要安裝更新 1.2 為重要修正程式，您必須下載並安裝下列動作︰

| 順序  | KB        | 描述             | 更新類型  |
|--------|-----------|-------------------------|------------- |
| 1      | KB3063418 | 軟體更新         |  一般     |
| 2      | KB3043005 | LSI SA 控制器更新 |  一般     |
| 3      | KB3063416 | 磁碟韌體           | 進行的維修作業  |

再使用此程序套用更新，請確認︰

- 兩個裝置控制站在線上。

執行下列步驟，以套用更新 1.2。 **更新可能需要大約 2 的時間才能完成 （30 分鐘軟體，30 分鐘的時間驅動程式，如磁碟韌體 45 分鐘）。**

[AZURE.INCLUDE [storsimple-install-update-option1](../../includes/storsimple-install-update-option1.md)]


## <a name="option-2-use-the-azure-classic-portal-to-apply-update-12-after-removing-the-gateway-configuration"></a>選項 2︰ 使用 Azure 傳統入口網站，若要移除的閘道器組態後套用更新 1.2

此程序僅適用於 StorSimple 裝置執行中的軟體之前版本更新 1 有閘道器設定資料 0 以外的網路介面上。 您必須取消之前套用更新閘道器設定。

更新可能需要幾個小時，才能完成。 如果您的主機位於不同的子網路，請移除 iSCSI 介面上的閘道器設定可能會導致停機時間。 我們建議您設定以減少停機時間的資料 0 iSCSI 流量。

執行下列步驟，以停用網路介面閘道器，然後再套用更新。

[AZURE.INCLUDE [storsimple-install-update-option2](../../includes/storsimple-install-update-option2.md)]

[AZURE.INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]


## <a name="next-steps"></a>後續步驟

進一步瞭解[更新 1.2 發行](storsimple-update1-release-notes.md)。
