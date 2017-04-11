<properties
   pageTitle="StorSimple 虛擬陣列系統需求"
   description="進一步瞭解的軟體和網路 StorSimple 虛擬陣列的需求"
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
   ms.workload="NA"
   ms.date="10/17/2016"
   ms.author="alkohli"/>

# <a name="storsimple-virtual-array-system-requirements"></a>StorSimple 虛擬陣列系統需求

## <a name="overview"></a>概觀

本文將說明 Microsoft Azure StorSimple 虛擬陣列 （也稱為 StorSimple 內部部署虛擬裝置或 StorSimple 虛擬裝置），以及存取陣列的儲存空間用戶端的重要的系統需求。 我們建議您先仔細檢閱資訊部署 StorSimple 系統，然後參考視部署及後續作業期間之前。

系統需求，包括︰

-   **儲存用戶端的軟體需求**-說明支援的虛擬主機平台、 網頁瀏覽器、 iSCSI 啟動器、 SMB 用戶端、 最小的虛擬裝置需求，以及這些作業系統任何額外的需求。

-   **網路 StorSimple 裝置需求**-提供必須開啟，在您的防火牆，以允許 iSCSI、 雲端，或管理流量的連接埠的相關資訊。

StorSimple 系統需求資訊發佈本文只適用於 StorSimple 虛擬陣列。

- 8000 數列裝置，請移至[StorSimple 8000 數列裝置的系統需求](storsimple-system-requirements.md)。
 
- 7000 數列裝置，請移至[StorSimple 5000 7000 數列裝置的系統需求](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements)。


## <a name="software-requirements"></a>軟體需求

軟體需求包含上支援的網頁瀏覽器、 中小企業版本、 虛擬主機平台和最小的虛擬裝置需求的資訊。

### <a name="supported-virtualization-platforms"></a>支援的虛擬化平台


| **Hypervisor** | **版本**                          |
|----------------|--------------------------------------|
| Hyper-v        | Windows Server 2008 R2 SP1 及更新版本 |
| VMware ESXi    | 5.5 及更新版本                        |

### <a name="virtual-device-requirements"></a>虛擬裝置需求

| **元件**                                | **要求**            |
|----------------------------------------------|----------------------------|
| 最小的數字的虛擬處理器 （核心） | 4                          |
| 最小的記憶體 (RAM)                         | 8 GB                       |
| 磁碟空間<sup>1</sup>                       | OS 磁碟-80 GB <br></br>資料磁碟-500 GB，以 8 TB|
| 最小的數字的網路介面       | 1                          |
| 最小的網際網路頻寬<sup>2</sup>       | 5 Mbps                     |

<sup>1</sup> -精簡佈建後

<sup>2</sup> -網路需求可能會有所不同每日的資料變更率。 例如，如果裝置必須一天內備份 10 GB 或更多的變更，然後透過 5 Mbps 連線每日備份可能需要 4.25 小時 （如果無法壓縮或 deduplicated 資料）。

### <a name="supported-web-browsers"></a>支援的網頁瀏覽器

| **元件**     | **版本** | **其他的需求筆記** |
|-------------------|-----------------|-----------------------------------|
| Microsoft Edge    | 最新版本  |                                   |
| Internet Explorer | 最新版本  | 使用 Internet Explorer 11 測試  |
| Google Chrome     | 最新版本  | 使用 Chrome 46 測試             |

### <a name="supported-storage-clients"></a>支援的儲存空間的用戶端 

存取 StorSimple 虛擬陣列 （設定為 iSCSI 伺服器） iSCSI 啟動器是下列軟體需求。

| **支援的作業系統** | **所需的版本** | **其他的需求筆記** |
| --------------------------- | ---------------- | ------------- |
| Windows Server              | 2008R2 SP1，2012 2012R2 |StorSimple 可以建立狗熊能夠和完整能夠區。 無法建立部分能夠的區。 StorSimple iSCSI 區只支援的︰ <ul><li>在 Windows 基本磁碟上的簡單區。</li><li>Windows NTFS 來設定格式的音量。</li>|


下列軟體需求是用戶端存取 StorSimple 虛擬陣列 （設為在檔案伺服器）。

| **SMB 版本** |
|-------------|
| SMB 2.x     |
| SMB 3.0     |
| SMB 3.02    |

> [AZURE.IMPORTANT] 複製或儲存檔案到 StorSimple 虛擬陣列檔案伺服器; 保護以 Windows 加密檔案系統 (EFS)這會導致不受支援的設定。 

## <a name="networking-requirements"></a>網路需求 

下表列出需要以開啟您的防火牆，以允許 iSCSI、 SMB、 雲端，或管理流量的連接埠。 在這個資料表中，*在*或*輸入*指的是從傳入的用戶端要求存取您的裝置的方向。 *或*輸出**參照 StorSimple 裝置的限制，超出部署傳送於外部資料的方向︰ 例如，輸出至網際網路。

| **<sup>1</sup>的連接埠 [否]。** | **或縮小** | **連接埠範圍** | **所需**              | **備忘稿**                                                                                                            |
|--------------------------|---------------|----------------|---------------------------|----------------------------------------------------------------------------------------------------------------------|
| TCP 80 (HTTP)            | 取出           | WAN            | 無                        | 輸出的連接埠用於存取網際網路擷取的更新。 <br></br>輸出網頁 proxy 是使用者可設定。 |
| TCP 443 (HTTPS)          | 取出           | WAN            | [是]                       | 輸出的連接埠用來存取雲端中的資料。 <br></br>輸出網頁 proxy 是使用者可設定。 |
| UDP 53 (DNS)             | 取出           | WAN            | 在某些情況下。請參閱備忘稿。 | 只有當您使用的以網際網路為基礎的 DNS 伺服器，則需要此連接埠。 <br></br> **注意**︰ 如果部署檔案伺服器，建議使用本機 DNS 伺服器。|
| UDP 123 (NTP)            | 取出           | WAN            | 在某些情況下。請參閱備忘稿。 | 只有當您使用的以網際網路為基礎的 NTP 伺服器，則需要此連接埠。<br></br> **附註︰**如果部署檔案伺服器，我們建議您 Active Directory 網域控制站與同步處理的時間。  |
| TCP 80 (HTTP)           | 在            | 區域網路            | [是]                       | 這是本機 UI 本機管理 StorSimple 裝置上的連入連接埠。 <br></br> **附註**︰ 透過 HTTP 存取本機 UI 會自動重新導向至 HTTPS。|
| TCP 443 (HTTPS)          | 在            | 區域網路            | [是]                       | 這是本機 UI 本機管理 StorSimple 裝置上的連入連接埠。|
| TCP 3260 (iSCSI)         | 在            | 區域網路            | 無                        | 此連接埠用來透過 iSCSI 存取資料。|

<sup>1</sup>需要開啟公用網際網路上沒有連入連接埠。

> [AZURE.IMPORTANT] 確保防火牆不會無法修改，或解密 StorSimple 裝置和 Azure 之間任何 SSL 流量。


### <a name="url-patterns-for-firewall-rules"></a>防火牆規則的 URL 模式 

網路管理員通常可以設定進階的防火牆規則，根據 URL 模式，若要篩選的輸入與輸出流量。 虛擬陣列和 StorSimple 管理員服務而定其他的 Microsoft 應用程式，例如 Azure 服務匯流排、 Azure Active Directory 存取控制、 儲存帳戶和 Microsoft Update 伺服器。 這些應用程式相關聯的 URL 模式可用來設定防火牆規則。 請務必瞭解這些應用程式相關聯的 URL 模式，可以變更。 接著就必須監控和更新防火牆規則，為您 StorSimple，並在需要時的網路系統管理員。 

我們建議您設定外寄流量，根據 StorSimple 在大部分情況下從寬固定的 IP 位址的防火牆規則。 不過，您可以使用下列資訊將會建立安全的環境時所需的進階的防火牆規則。

> [AZURE.NOTE] 
> 
> - 裝置 （來源） IPs 應該一律設為所有的雲端啟用網路介面。 
> - 目的地 Ip 應該會設定為[Azure 資料中心的 IP 範圍](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653)。


| URL 模式                                                      | 元件/功能                                           |
|------------------------------------------------------------------|---------------------------------------------------------------|
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`   | StorSimple 管理員服務<br>存取控制服務<br>Azure 服務匯流排|
|`http://*.backup.windowsazure.com`|裝置註冊|
|`http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*`|憑證撤銷 |
| `https://*.core.windows.net/*`<br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` | Azure 儲存體帳戶和監視 |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com`| Microsoft Update 伺服器<br>                        |
| `http://*.deploy.akamaitechnologies.com`                         |Akamai CDN |
| `https://*.partners.extranet.microsoft.com/*`                    | 支援套件                                                  |
| `http://*.data.microsoft.com `                   | 在 Windows 中，遙測服務，請參閱 「[客戶經驗和診斷遙測的更新](https://support.microsoft.com/en-us/kb/3068708)                                                  |

## <a name="next-step"></a>下一步

-   [準備要部署 StorSimple 虛擬陣列入口網站](storsimple-ova-deploy1-portal-prep.md)
