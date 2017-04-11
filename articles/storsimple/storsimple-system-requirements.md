<properties
   pageTitle="StorSimple 系統需求 |Microsoft Azure"
   description="說明軟體、 網路連線和可用性需求及 Microsoft Azure StorSimple 解決方案的最佳作法。"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/31/2016"
   ms.author="alkohli"/>

# <a name="storsimple-software-high-availability-and-networking-requirements"></a>StorSimple 軟體、 高可用性和網路需求

## <a name="overview"></a>概觀

歡迎使用 Microsoft Azure StorSimple。 本文將說明重要的系統需求與 StorSimple 裝置及的存取裝置的儲存空間用戶端的最佳作法。 我們建議您先仔細檢閱資訊部署 StorSimple 系統，然後參考視部署及後續作業期間之前。

系統需求，包括︰

- **儲存空間的用戶端的軟體需求**-說明支援的作業系統，以及這些作業系統任何額外的需求。
- **網路 StorSimple 裝置需求**-提供必須開啟，在您的防火牆，以允許 iSCSI、 雲端，或管理流量的連接埠的相關資訊。
- **可用性需求 StorSimple** -說明可用性需求及 StorSimple 裝置和主機電腦的最佳作法。 


## <a name="software-requirements-for-storage-clients"></a>儲存空間的用戶端的軟體需求

下列軟體需求的儲存空間的用戶端存取 StorSimple 裝置。

| 支援的作業系統 | 所需的版本 | 其他的需求筆記 |
| --------------------------- | ---------------- | ------------- |
| Windows Server              | 2008R2 SP1，2012 2012R2 |適用於僅下列 Windows 磁碟類型支援 StorSimple iSCSI 區︰<ul><li>基本磁碟上的簡單音量</li><li>動態磁碟上的簡單及左右對稱音量</li></ul>如果您使用的 StorSimple iSCSI 大量，支援 Windows Server 2012 型佈建和 ODX 功能。<br><br>StorSimple 可以建立狗熊能夠和完整能夠區。 無法建立部分能夠的區。<br><br>重新格式化狗熊能夠的大量花費很長的時間。 我們建議您刪除音量並建立一個新，而非重新格式化。 不過，如果仍想要格式化大量︰<ul><li>執行之前重新格式化下列命令，以避免空間回收延遲︰ <br>`fsutil behavior set disabledeletenotify 1`</br></li><li>格式設定完成之後，請重新啟用空間回收使用下列命令︰<br>`fsutil behavior set disabledeletenotify 0`</br></li><li>到您的 Windows Server 電腦所述[KB 2878635](https://support.microsoft.com/kb/2870270)套用 Windows Server 2012。</li></ul></li></ul></ul> 如果您正在設定 StorSimple 快照管理員或 StorSimple 介面卡的 SharePoint，請移至[選用元件的軟體需求](#software-requirements-for-optional-components)。|
| VMWare ESX | 5.5 和 6.0 | 為 iSCSI 用戶端支援 VMWare vSphere。 使用 VMware vSphere StorSimple 裝置上支援 VAAI 封鎖功能。
| Linux RHEL/CentOS | 5、 6 和 7 | 支援 Linux iSCSI 用戶端與開啟 iSCSI 啟動器版本 5、 6 和 7。 |
| Linux | SUSE Linux 11 | |
 > [AZURE.NOTE] IBM AIX 目前不支援使用 StorSimple。

## <a name="software-requirements-for-optional-components"></a>選用元件的軟體需求

選用的 StorSimple 元件 （StorSimple 快照管理員和 sharepoint StorSimple 介面卡） 是下列軟體需求。

| 元件 | 主機平台 | 其他的需求筆記 |
| --------------------------- | ---------------- | ------------- |
| StorSimple 快照管理員 | Windows Server 2008R2 SP1 2012 2012R2 | 備份/還原的左右對稱的動態磁碟，以及任何應用程式一致的備份需要使用的 Windows Server 上 StorSimple 快照管理員。<br> 僅在 Windows Server 2008 R2 SP1 （64 位元）、 Windows 2012 R2 和 Windows Server 2012 支援 StorSimple 快照管理員。<ul><li>如果您使用視窗 Server 2012，您必須先安裝.NET 3.5 – 4.5，才能安裝 StorSimple 快照管理員。</li><li>如果您使用的 Windows Server 2008 R2 SP1，您必須先安裝 Windows 管理 Framework 3.0，才能安裝 StorSimple 快照管理員。</li></ul> |
| Sharepoint StorSimple 介面卡 | Windows Server 2008R2 SP1 2012 2012R2 |<ul><li>在 SharePoint 2010 與 SharePoint 2013 只支援 sharepoint 的 StorSimple 卡。</li><li>RBS 需要 SQL Server 企業版，2008 R2 或 2012年版本。</li></ul>|

## <a name="networking-requirements-for-your-storsimple-device"></a>網路 StorSimple 裝置的需求

StorSimple 裝置是鎖定的裝置。 不過，需要以開啟您的防火牆，允許 iSCSI、 雲端，並管理流量的連接埠。 下表列出要在您的防火牆中開啟的連接埠。 在這個資料表中，*在*或*輸入*指的是從傳入的用戶端要求存取您的裝置的方向。 *或*輸出**參照 StorSimple 裝置的限制，超出部署傳送於外部資料的方向︰ 例如，輸出至網際網路。

| <sup>1，2</sup>的連接埠 [否]。 | 或縮小 | 連接埠範圍 | 所需 | 備忘稿 |
|------------------------|-----------|------------|----------|-------|
|TCP 80 (HTTP)<sup>3</sup>|  取出 |  WAN | 無 |<ul><li>輸出的連接埠用於存取網際網路擷取的更新。</li><li>輸出網頁 proxy 是使用者可設定。</li><li>若要允許系統更新，此也必須開啟連接埠固定 IPs 控制器。</li></ul> |
|TCP 443 (HTTPS)<sup>3</sup>| 取出 | WAN | [是] |<ul><li>輸出的連接埠用來存取雲端中的資料。</li><li>輸出網頁 proxy 是使用者可設定。</li><li>若要允許系統更新，此也必須開啟連接埠固定 IPs 控制器。</li><li>此連接埠也可用於控制站回收。</li></ul>|
|UDP 53 (DNS) | 取出 | WAN | 在某些情況下。請參閱備忘稿。 |只有當您使用的以網際網路為基礎的 DNS 伺服器，則需要此連接埠。 |
| UDP 123 (NTP) | 取出 | WAN | 在某些情況下。請參閱備忘稿。 |只有當您使用的以網際網路為基礎的 NTP 伺服器，則需要此連接埠。 |
| TCP 9354 | 取出 | WAN | [是] |StorSimple 裝置使用輸出的連接埠與 StorSimple 管理員服務進行通訊。 |
| 3260 (iSCSI) | 在 | 區域網路 | 無 | 此連接埠用來透過 iSCSI 存取資料。|
| 5985 | 在 | 區域網路 | 無 | 通訊與 StorSimple 裝置使用 StorSimple 快照管理員所輸入的連接埠。<br>當您從遠端連接到 Windows PowerShell 的 StorSimple 透過 HTTP，也會使用這個連接埠。 |
| 5986 | 在 | 區域網路 | 無 | 當您從遠端透過 Windows powershell 的 StorSimple HTTPS 時，會使用此連接埠。 |

<sup>1</sup>需要開啟公用網際網路上沒有連入連接埠。

<sup>2</sup>如果多個連接埠執行的閘道器設定，請輸出路由的流量順序會決定根據[連接埠路由](#routing-metric)，所述的連接埠路由順序。

<sup>3</sup>固定 IPs StorSimple 裝置上的控制站必須舉例來說，並且可以連線至網際網路。 固定的 IP 位址用於提供服務的更新裝置。 如果裝置控制器無法固定 IPs 透過網際網路連線，您將無法更新 StorSimple 裝置。

> [AZURE.IMPORTANT] 確保防火牆不會無法修改，或解密 StorSimple 裝置和 Azure 之間任何 SSL 流量。

### <a name="url-patterns-for-firewall-rules"></a>防火牆規則的 URL 模式

網路管理員通常可以設定進階的防火牆規則，根據篩選輸入的 URL 模式和外寄流量。 StorSimple 裝置和 StorSimple 管理員服務而定其他的 Microsoft 應用程式，例如 Azure 服務匯流排、 Azure Active Directory 存取控制、 儲存帳戶和 Microsoft Update 伺服器。 這些應用程式相關聯的 URL 模式可用來設定防火牆規則。 請務必瞭解這些應用程式相關聯的 URL 模式，可以變更。 接著就必須監控和更新防火牆規則，為您 StorSimple，並在需要時的網路系統管理員。

我們建議您設定外寄流量，根據 StorSimple 在大部分情況下從寬固定的 IP 位址的防火牆規則。 不過，您可以使用下列資訊將會建立安全的環境時所需的進階的防火牆規則。

> [AZURE.NOTE] 裝置 （來源） IPs 應該一律設為所有的網路介面。 目的地 Ip 應該會設定為[Azure 資料中心的 IP 範圍](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653)。

#### <a name="url-patterns-for-azure-portal"></a>Azure 入口網站的 URL 模式
| URL 圖樣                                                      | 元件/功能                                           | 裝置 IPs                           |
|------------------------------------------------------------------|---------------------------------------------------------------|-----------------------------------------|
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`   | StorSimple 管理員服務<br>存取控制服務<br>Azure 服務匯流排| 啟用雲端的網路介面        |
|`https://*.backup.windowsazure.com`|裝置註冊| 僅限資料 0|
|`http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*`|憑證撤銷 |啟用雲端的網路介面 |
| `https://*.core.windows.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` | Azure 儲存體帳戶和監視 | 啟用雲端的網路介面        |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com`| Microsoft Update 伺服器<br>                             | 僅限固定 IPs 控制器               |
| `http://*.deploy.akamaitechnologies.com`                         |Akamai CDN |僅限固定 IPs 控制器   |
| `https://*.partners.extranet.microsoft.com/*`                    | 支援套件                                                  | 啟用雲端的網路介面        |

#### <a name="url-patterns-for-azure-government-portal"></a>Azure 政府版入口網站的 URL 模式
| URL 模式                                                      | 元件/功能                                           | 裝置 IPs                           |
|------------------------------------------------------------------|---------------------------------------------------------------|-----------------------------------------|
| `https://*.storsimple.windowsazure.us/*`<br>`https://*.accesscontrol.usgovcloudapi.net/*`<br>`https://*.servicebus.usgovcloudapi.net/*`   | StorSimple 管理員服務<br>存取控制服務<br>Azure 服務匯流排| 啟用雲端的網路介面        |
|`https://*.backup.windowsazure.us`|裝置註冊| 僅限資料 0|
|`http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*`|憑證撤銷 |啟用雲端的網路介面 |
| `https://*.core.usgovcloudapi.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` | Azure 儲存體帳戶和監視 | 啟用雲端的網路介面        |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com`| Microsoft Update 伺服器<br>                             | 僅限固定 IPs 控制器               |
| `http://*.deploy.akamaitechnologies.com`                         |Akamai CDN |僅限固定 IPs 控制器   |
| `https://*.partners.extranet.microsoft.com/*`                    | 支援套件                                                  | 啟用雲端的網路介面        |

### <a name="routing-metric"></a>路由公制

路由公制是介面與資料傳送到指定網路的閘道器相關聯。 路由公制用於路由通訊協定計算最佳的路徑，以指定的目的，如果其學習多重路徑有相同的目的地。 較低路由公制，較高的喜好設定。

在 StorSimple 內容，如果多個網路介面及閘道器設定為頻道流量路由指標會進入決定會取得使用介面相對順序播放。 使用者無法變更路由指標。 但是您可以使用`Get-HcsRoutingTable`cmdlet 來列印 StorSimple 裝置上路由資料表 （且指標）。 更多[疑難排解 StorSimple 部署](storsimple-troubleshoot-deployment.md)中的取得 HcsRoutingTable cmdlet 的詳細資訊。

路由公制演算法會有所不同 StorSimple 裝置上所執行的軟體版本。

**更新 1 之前的版本**

這包含軟體更新 1 GA，0.1，例如之前的版本 0.2，或 0.3 版本。 根據路由指標的順序如下所示︰

   *最後一個設定 10 GbE 網路介面 > 其他 10 GbE 網路介面 > 最後一個設定 1 GbE 網路介面 > 其他 1 GbE 網路介面*


**啟動更新 1 及更新 2 之前的版本**

這包含 1、 1.1 （英文） 或 1.2 等軟體版本。 根據路由指標的順序會決定，如下所示︰

   *資料 0 > 最後一個設定 10 GbE 網路介面 > 其他 10 GbE 網路介面 > 最後一個設定 1 GbE 網路介面 > 其他 1 GbE 網路介面*

   更新 1 中所做的資料 0 路由公制最低。因此，所有雲端的流量會導向到資料 0。 記下這如果 StorSimple 裝置上有一個以上的雲端啟用網路介面。


**從更新 2 開始的版本**

更新 2 有多個網路相關的改良功能，而且路由指標已變更。 可以如下所示解釋行為。

- 網路介面有已指派的一組預先定義的值。   

- 請考慮值分派給不同的網路介面雲端-啟用或停用雲端的但設定閘道器所示的範例資料表。 請注意指派以下的值只範例值。


  	| 網路介面 | 雲端啟用 | 雲端停用的閘道器 |
  	|-----|---------------|---------------------------|
  	| 資料 0  | 1            | -                        |
  	| 1 的資料  | 2            | 20                       |
  	| 2 的資料  | 3            | 30                       |
  	| 3 的資料  | 4            | 40                       |
  	| 資料 4  | 5            | 50                       |
  	| 5 的資料  | 6            | 60                       |


- 會透過網路介面路由雲端流量的順序就是︰

    *資料 0 > 資料 1 > 日期 2 > 資料 3 > 資料 4 > 5 的資料*

    這可以透過下列範例說明。

    請考慮 StorSimple 裝置以搭配兩個已啟用雲端的網路介面資料 0 和資料 5。 資料 1 到 4 資料是雲端停用，但是已設定的閘道器。 將此裝置路由流量的順序就會是︰

    *資料 0 (1) > 資料 5 (6) > 1 (20) 資料 > 資料 2 (30) > 資料 3 (40) > 資料 4 (50)*

    *位置括號括住的數字會指出個別路由指標。*

    如果資料 0 失敗，請雲端流量會傳送至資料 5。 閘道器設定在所有其他的網路上失敗資料 0 與資料 5 一樣，雲端流量就會移到資料 1。


- 如果啟用雲端的網路介面失敗，則重試 3 次，以連線至介面 30 秒延遲。 如果所有的重試失敗下, 一步可使用雲端啟用介面由路由表路由流量。 如果所有雲端啟用的網路都介面失敗，然後裝置將無法透過控制站 （在本例中沒有重新開機）。

- 啟用 iSCSI 的網路介面 VIP 失敗時，會有 2 秒延遲重試 3 次。 這種行為具有仍會維持舊版本的相同。 如果所有 iSCSI 網路介面都失敗，控制器容錯移轉會發生 （旁都重新啟動電腦）。


- VIP 失敗時，提醒也會引發 StorSimple 裝置上。 如需詳細資訊，請前往[通知的快速參考](storsimple-manage-alerts.md)。

- 重試，而言 iSCSI 會優先於雲端。

    請考慮下列範例︰ StorSimple 裝置有啟用的兩個網路介面、 資料 0 和 1 的資料。 資料 0 且雲端啟用而資料 1 是兩個雲端 iSCSI 啟用。 此裝置上的任何其他網路介面不啟用雲端或 iSCSI。

    如果資料 1 失敗，指定的最後一個 iSCSI 網路介面，這會產生控制器容錯移轉資料 1 其他控制器上。


### <a name="networking-best-practices"></a>網路的最佳作法

除了上面之網路的需求，StorSimple 解決方案的最佳效能，請遵循下列最佳作法︰

- 確保您 StorSimple 裝置有專用的 40 Mbps 頻寬 （或更多） 隨時可用。 不應該共用此頻寬 （或應該使用 QoS 原則保證配置） 和其他應用程式。

- 請確定網路連線到網際網路可用在所有的時間。 月或不可靠的裝置，包括不，沒有網際網路連線至網際網路連線會導致不受支援的設定。

- 以具有專屬 iSCSI 和雲端存取您的裝置上的網路介面隔離 iSCSI 和雲端流量。 如需詳細資訊，請參閱如何 StorSimple 裝置上的 [[修改網路介面](storsimple-modify-device-config.md#modify-network-interfaces)。

- 為您的網路介面，請勿使用連結彙總控制通訊協定 (LACP) 設定。 這是不受支援的設定。


## <a name="high-availability-requirements-for-storsimple"></a>可用性 StorSimple 需求

硬體平台隨附 StorSimple 解決方案有提供的基礎資料中心的可用性、 容錯儲存基礎結構的可用性和可靠性功能。 然而，有需求，您應該可協助確保 StorSimple 方案的可用性遵守的最佳作法。 部署 StorSimple 之前，請仔細檢閱下列需求及 StorSimple 裝置和連線的主機電腦的最佳作法。

如需有關監視及維護 StorSimple 裝置的硬體元件的詳細資訊，請移至[使用 StorSimple 管理員服務監控硬體元件和狀態](storsimple-monitor-hardware-status.md)和[StorSimple 硬體元件取代](storsimple-hardware-component-replacement.md)。

### <a name="high-availability-requirements-and-procedures-for-your-storsimple-device"></a>高可用性需求與 StorSimple 裝置的程序

檢閱下列資訊，請仔細以確保 StorSimple 裝置的可用性。

#### <a name="pcms"></a>PCMs

StorSimple 裝置包含重複、 熱切換 power 及製冷模組 (PCMs)。 每個 PCM 有足夠的容量提供適用於整個底座服務。 若要確保可用性，必須安裝兩個 PCMs。

- 如果電源失敗提供可用的不同 power 來源連接您 PCMs。
- 如果 PCM 失敗，請立即要求替代方案。
- 只有當您有替代並準備好進行安裝時，請移除失敗的 PCM。
- 請勿將兩個 PCMs 同時。 PCM 模組包含備用模組。 移除其中一個 PCMs 會導致不電池保護]，關閉並不會儲存裝置狀態。 如需電池的詳細資訊，請移至[維持備用模組](storsimple-battery-replacement.md#maintain-the-backup-battery-module)。

#### <a name="controller-modules"></a>控制站模組

StorSimple 裝置包括重複、 熱切換控制站的模組。 控制站模組操作主動/被動的方式。 在任何時候一個控制器模組已啟動，並提供服務的其他控制器模組被動式時。 被動控制器模組已開啟，並會變成操作，如果作用中的控制器模組失敗或移除。 每個控制器模組有足夠的容量提供適用於整個底座服務。 若要確保高可用性必須安裝這兩個控制器模組。

- 請確定兩個控制器模組已安裝在所有的時間。

- 如果控制器模組失敗，請立即要求替代方案。

- 只有當您有替代並準備好進行安裝時，請移除失敗的控制器模組。 移除模組的長的時間會影響氣流，因此的系統。

- 請確定您的網路連線至這兩個控制器模組相同，且連線的網路介面有相同的網路設定。

- 如果控制器模組失敗，或需要更換，請務必控制器模組中作用中狀態之前取代失敗的控制器模組。 若要確認控制站作用中，移至[識別您的裝置上使用中控制器](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device)。

- 不同時移除這兩個控制器模組。 如果正在進行控制站容錯移轉，不關閉備用控制器模組或移除底座。

- 後控制器移轉後，請等候至少五分鐘，才能移除任一控制站的模組。

#### <a name="network-interfaces"></a>網路介面

StorSimple 裝置控制器模組每個有四個 1gb 與兩個 10 Gb 乙太網路介面。

- 請確定您的網路連線至這兩個控制器模組相同，且網路介面的控制器模組介面均已連線至有相同的網路設定。

- 可能的話，請跨不同的選項，以確保網路裝置失敗的服務可用性部署網路連線。

- （含 IPs 指派) 拔除只有或最後一個的剩餘 iSCSI 啟用介面，第一次停用介面，然後拔除纜線。 如果介面拔除第一次，它會導致容錯被動式控制站的作用中控制站。 如果被動式控制器也有拔除及其對應介面，兩個控制站會在一個控制器上坐重新多次。

- 連線至網路至少兩個資料介面，從每個控制器模組。

- 如果您已啟用兩個 10 GbE 介面部署上不同的選項。

- 可能的話，請使用 [在伺服器上 MPIO 以確保伺服器的可容許的連結、 網路或介面失敗。

如需有關網路您的裝置可用性及效能，前往[您 StorSimple 8100 裝置上安裝](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device)或[安裝 StorSimple 從 8600 裝置](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device)。

#### <a name="ssds-and-hdds"></a>SSDs 和 HDDs

StorSimple 裝置包含實心狀態磁碟 (SSDs)，然後硬碟機 (HDDs) 使用受到保護的鏡像空格。 使用左右對稱的空格中，可確保裝置可容許的一個或多個 SSDs HDDs 失敗。

- 請確定已安裝所有 SSD 和 HDD 模組。

- 如果 SSD 或 HDD 失敗，請立即要求替代方案。

- 如果 SSD HDD 失敗或需要取代，請確定您移除只 SSD 或 HDD 需要取代。

- 請勿移除多個 SSD 或 HDD 從任何系統時間。
2 或更多磁碟 （HDD、 SSD） 特定類型的失敗或簡短時間範圍內的連續失敗可能會導致系統故障和潛在資料遺失。 如果這種情況，[請連絡 Microsoft 支援服務](storsimple-contact-microsoft-support.md)取得協助。

- 取代時, 監視**硬體狀態**在**進行的維修作業**] 頁面上 SSDs 和 HDDs 磁碟機。 綠色核取狀態表示磁碟健全或 [確定]，而指向紅色驚嘆號指示失敗 SSD 或 HDD。

- 我們建議您設定雲端快照所有您要保護若系統失敗的區。

#### <a name="ebod-enclosure"></a>EBOD 圍繞符號

StorSimple 裝置模型從 8600 包含除了主要的圍繞符號的延伸 Bunch 的磁碟 (EBOD) 圍繞符號。 EBOD 包含 EBOD 控制站和硬碟機 (HDDs) 使用受到保護的鏡像空格。 使用左右對稱的空格中，可確保裝置可容許的一或多個 HDDs 失敗。 EBOD 圍繞符號已連線至主要的圍繞符號透過多餘 SA 纜線。

- 請確定兩個 EBOD 圍繞符號控制器模組 SA 纜線，及所有的硬碟已安裝在所有的時間。

- 如果 EBOD 圍繞符號控制器模組失敗，請立即要求替代方案。

- 如果 EBOD 圍繞符號控制器模組失敗，請確定取代失敗的模組之前，先，是作用中的其他控制器模組。 若要確認控制站作用中，移至[識別您的裝置上使用中控制器](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device)。

- 以 EBOD 控制器模組形式取代期間持續監視 StorSimple 管理員服務元件的狀態存取**進行的維修作業** > **硬體狀態**。

- 如果 SA 纜線失敗，或需要的取代 （Microsoft 支援服務必須做出這類判斷包含），請確定您移除只需要取代 SA 纜線。

- 不同時兩個 SA 纜線從系統移除任何時間。

### <a name="high-availability-recommendations-for-your-host-computers"></a>Host （主機） 電腦的可用性建議

請仔細檢閱這些最佳做法，以確保主機連線到您的 StorSimple 裝置的可用性。

- 使用[兩個節點檔案伺服器叢集組態]設定 StorSimple[1]。 移除單一的失敗，以及建置主機位於相同的點，將整個方案有空高度。

- 使用提供的持續可用 (CA) 共用 Windows Server 2012 (中小企業 3.0) 高可用性期間的儲存空間控制站的容錯移轉。 使用 Windows Server 2012 中設定檔案伺服器叢集和持續可用的共用資料夾的詳細資訊，請參考此[影片示範](http://channel9.msdn.com/Events/IT-Camps/IT-Camps-On-Demand-Windows-Server-2012/DEMO-Continuously-Available-File-Shares)。

## <a name="next-steps"></a>後續步驟

- [深入了解 StorSimple 系統限制](storsimple-limits.md)。
- [瞭解如何部署 StorSimple 方案](storsimple-deployment-walkthrough-u2.md)。

<!--Reference links-->
[1]: https://technet.microsoft.com/library/cc731844(v=WS.10).aspx
