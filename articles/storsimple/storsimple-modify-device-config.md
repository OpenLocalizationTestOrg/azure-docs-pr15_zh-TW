<properties 
   pageTitle="修改 StorSimple 裝置設定 |Microsoft Azure" 
   description="說明如何重新設定已經部署 StorSimple 裝置使用 StorSimple 管理員服務。" 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="09/29/2016"
   ms.author="v-sharos"/>

# <a name="use-the-storsimple-manager-service-to-modify-your-storsimple-device-configuration"></a>若要修改 StorSimple 裝置設定使用 StorSimple 管理員服務

## <a name="overview"></a>概觀 

Azure 傳統的入口網站**設定**] 頁面包含您可以重新設定由 StorSimple 管理員服務管理 StorSimple 裝置的所有裝置參數。 本教學課程會說明如何使用 [**設定**] 頁面執行下列裝置層級工作︰

- 修改裝置設定 
- 修改時間設定 
- 修改 DNS 設定 
- 修改網路介面
- 更換或重新指派 IPs

## <a name="modify-device-settings"></a>修改裝置設定

裝置設定包含裝置和裝置說明的易記的名稱。

StorSimple 裝置連接到 StorSimple 管理員服務會指派預設的名稱。 預設名稱通常會反映出序列值的裝置。 例如，預設裝置名稱 15 個字元，例如從 8600 SHX0991003G44HT，如下︰

- **從 8600** – 表示裝置模型。
- **SHX** – 指出製造網站。
- **0991003** -指出特定產品。
- **G44HT**-最後 5 位數遞增建立唯一的序列值。 這可能不連續的設定。

若要變更裝置名稱，然後將其指派好記的唯一名稱，您的選擇，您可以使用 Azure 傳統入口網站。 好記的名稱最多可以包含的任何字元，並可 64 個字元的最大值。

您也可以指定裝置的說明。 裝置描述通常可協助找出擁有者和裝置的實際位置。 [描述] 欄位必須包含少於 256 個字元。
 
## <a name="modify-time-settings"></a>修改時間設定

您的裝置必須同步處理時間，以驗證您的雲端儲存服務提供者。 從下拉式清單中，選取您的時區，並指定兩個網路時間通訊協定 (NTP) 伺服器。 主要 NTP 伺服器需要，指定當您使用 Windows PowerShell 的 StorSimple 來設定您的裝置。 您可以指定預設 Windows Server **time.windows.com**您 NTP 伺服器。 您可以檢視主要 NTP 伺服器設定，透過 Azure 傳統入口網站，但您必須使用 Windows PowerShell 介面，來變更。

將副 NTP 伺服器設定為選用步驟。 若要設定次要 NTP 伺服器，您可以使用 [傳統] 入口網站。 

設定時 NTP 伺服器，請確定您的網路，讓 NTP 流量，若要從您的資料中心傳遞至網際網路。 指定時公用 NTP 伺服器，您必須確定您的網路防火牆及其他安全性裝置設定為允許 NTP 傳輸流量與外部網路。 如果不允許的雙向 NTP 傳輸，則必須使用內部 NTP 伺服器 （Windows 網域控制站會提供此函數）。 如果您的裝置無法同步處理的時間，它可能無法進行通訊的雲端儲存空間提供者。

若要查看公用 NTP 伺服器的清單，請移至[NTP 伺服器網頁](http://support.ntp.org/bin/view/Servers/WebHome)。 

### <a name="what-happens-if-the-device-is-deployed-in-a-different-time-zone"></a>在不同的時區部署裝置時，會發生什麼情況？

如果使用的裝置已部署在不同的時區，就會變更裝置的時區。 假設您所有的備份原則使用裝置的時區，備份原則會自動調整根據的新的時區。 需要沒有使用者互動。

## <a name="modify-dns-settings"></a>修改 DNS 設定

當您的裝置嘗試在雲端儲存服務提供者通訊時，會使用 DNS 伺服器。 高可用性，您必須在初始裝置部署設定主要和次要 DNS 伺服器。 若要重新設定主要 DNS 伺服器，您將需要 StorSimple 裝置上使用 Windows PowerShell 介面。

若要修改次要 DNS 伺服器，您可以使用 [Azure 傳統入口網站。



## <a name="modify-network-interfaces"></a>修改網路介面

您的裝置有六個裝置網路介面，其中四個 1 GbE，其中兩個 10 GbE。 這些介面都被標示為 0 – 資料 5 的資料。 資料 0，資料 1、 資料 4 和 5 的資料會是 1 GbE，而資料 2 和 3 的資料是 10 GbE 網路介面。

設定**網路介面設定**為每個要使用的介面。 若要確保可用性，我們建議您有兩個以上 iSCSI 介面和兩個已啟用雲端的介面，您的裝置上。 我們建議您，但不是需要停用未使用的介面。

當您設定的任何網路介面時，您必須設定虛擬 IP (VIP)。

0 的資料是依預設啟用雲端。 在設定資料 0，您也必須設定兩個固定的 IP 位址，每個控制站的其中一個。 這些固定的 IP 位址可直接存取裝置控制器，當您安裝的更新，在裝置上，或當您存取來疑難排解控制器時非常有用。

StorSimple 8000 數列更新 1 中，設定路由公制的資料 0 到最低。因此，如果您的裝置正在執行 StorSimple 8000 數列更新 1，將會瀏覽資料 0 路由所有雲端流量。 如果您有一個以上的雲端啟用網路介面 StorSimple 裝置上，則請記下這。

>[AZURE.NOTE] 控制站固定的 IP 位址用於提供服務的更新裝置。 舉例來說，並且可以連線到網際網路，因此，必須先固定的 IPs。

每個網路介面，會顯示下列參數︰

- **速度**– 不是使用者設定的參數。 資料 0，資料 1、 資料 4 和 5 的資料永遠是 1 GbE，而資料 2 和 3 的資料是 10 GbE 介面。

     >[AZURE.NOTE] 速度和雙面列印一律自動-交涉。 不支援談框架。
 
- **介面狀態**-介面可以啟用或停用。 如果啟用，裝置會嘗試使用介面。 我們建議您連線至網路和使用這些介面會啟用。 停用任何不使用的介面。

- **介面類型**– 此參數，可讓您隔離 iSCSI 流量從雲端儲存空間流量。 這個參數可以是下列其中一項︰

    - **啟用雲端**– 啟用時，裝置會使用這個介面進行通訊的雲端。
    - **啟用 iSCSI** – 啟用時，裝置會使用這個介面與 iSCSI 主機通訊。

    我們建議您隔離 iSCSI 流量從雲端儲存空間流量。 另請注意是否您的主機位於相同的子網路，為您的裝置，您不需要指派閘道器。不過，如果您的主機位於不同的子網路您的裝置，您必須將閘道指派。

- **IP 位址**– 可 IPv4 或 IPv6 或兩者。 IPv4 和 IPv6 位址系列支援的裝置網路介面。 使用時 IPv4，指定 32 位元的 IP 位址 （*選擇*） 中點小數點。 使用 IPv6，僅提供 4 位數字首，是 128 位元的地址會自動產生的前置詞為根據您的裝置網路介面。

- **子網路**– 此參照的子網路遮罩，而且已透過 Windows PowerShell 介面。

- **閘道器**– 這是預設閘道器嘗試與不在相同的 IP 位址空間 （子網路） 的節點通訊時應使用這個介面。 預設閘道器必須在相同的地址空間 （子網路） 的介面 IP 位址，取決於子網路遮罩。

- **固定的 IP 位址**– 此欄位時，可只設定資料 0 介面。 更新或疑難排解裝置等作業，您可能需要將直接連接到裝置控制器。 固定的 IP 位址可用來存取您的裝置上的被動式控制站作用和。

您可以重新設定控制站 0 和控制器 1，透過 Azure 傳統入口網站。

>[AZURE.NOTE] 
>
>- 若要確保正常運作，請確認的介面速度與雙面列印，在每個裝置介面連線到切換。 切換介面與應該是交涉或 Gb 乙太網路設定 (1000 Mbps)，是全雙面列印。 作業系統速度變慢的速度，或在半形雙面列印的介面會導致效能問題。
>
>- 若要減少干擾及停機時間，我們建議您啟用 portfast 每個裝置的 iSCSI 網路介面會連線到切換連接埠。 這樣就能確定網路連線可以來快速建立事件容錯移轉。
 
## <a name="swap-or-reassign-ips"></a>更換或重新指派 IPs

目前，如果控制器上的任何網路介面指派為使用中 VIP 時 （藉由同一個裝置或網路中的另一個裝置），控制站就無法透過。 因此，您必須遵循的適當的程序，如果您交換 Vip 裝置網路介面，因為您將會建立重複的 IP 情況。

執行下列步驟以交換或重新指派的網路介面 Vip:

#### <a name="to-reassign-ips"></a>若要重新指派 IPs

1. 清除這兩個介面的 IP 位址。

2. 清除 IP 位址之後，請將新的 IP 位址指派給個別介面。

## <a name="next-steps"></a>後續步驟

- 瞭解如何[設定 MPIO StorSimple 裝置](storsimple-configure-mpio-windows-server.md)。

- 瞭解如何[使用 StorSimple 管理員服務，來管理您的 StorSimple 裝置](storsimple-manager-service-administration.md)。
     
