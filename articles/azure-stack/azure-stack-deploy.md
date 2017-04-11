<properties
    pageTitle="部署 Azure 堆疊 POC 之前 |Microsoft Azure"
    description="檢視 Azure 堆疊 POC （服務系統管理員） 的環境及硬體需求。"
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/12/2016"
    ms.author="erikje"/>

# <a name="azure-stack-deployment-prerequisites"></a>Azure 堆疊部署先決條件

部署 Azure 堆疊 POC （[的概念](azure-stack-poc.md)） 之前，請確定您的電腦符合下列需求。
POC 技術預覽 2 部署需求是所技術預覽 1 所需的相同。 因此，您可以使用相同的硬體所使用的上一個單一方塊預覽。

## <a name="hardware"></a>硬體

| 元件 | 最小值  | 建議 |
|---|---|---|
| 磁碟機︰ 作業系統 | 200 GB 的可用系統分割 （SSD 或 HDD） 的最小值 1 OS 磁碟 | 200 GB 的可用系統分割 （SSD 或 HDD） 的最小值 1 OS 磁碟 |
| 磁碟機︰ 一般 Azure 堆疊 POC 資料 | 4 的磁碟。 每個磁碟提供的容量 （SSD 或 HDD） 140 GB 的最小值。 將使用所有可用的磁碟。 | 4 的磁碟。 每個磁碟提供的容量 （SSD 或 HDD） 250 GB 的最小值。 將使用所有可用的磁碟。|
| 計算︰ CPU | 雙重端︰ 12 實體核心 （總計）  | 雙重端︰ 16 實體核心 （總計） |
| 計算︰ 記憶體 | 96 GB RAM  | 128 GB RAM |
| 計算︰ BIOS | HYPER-V 啟用 （含造板條支援）  | HYPER-V 啟用 （含造板條支援） |
| 網路︰ NIC | Windows Server 2012 R2 憑證所需的 NIC;沒有所需的特殊的功能 | Windows Server 2012 R2 憑證所需的 NIC;沒有所需的特殊的功能 |
| HW 標誌憑證 | [Windows Server 2012 R2 的認證](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |[Windows Server 2012 R2 的認證](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0)|

**資料磁碟機︰**所有資料磁碟機都必須屬於相同類型 （所有 SA 或所有 SATA） 與容量。 如果使用 SA 磁碟機，必須附加磁碟機，透過單一路徑 （沒有 MPIO，多重路徑支援提供）。

**HBA 設定選項**
 
- （建議選項）簡單 HBA
- 「 通過 」 模式中，必須設定 RAID HBA-卡
- RAID HBA – 磁碟應該設為單一磁碟、 raid-0

**支援的匯流排和媒體輸入組合**

-   SATA HDD

-   SA HDD

-   RAID HDD

-   RAID SSD (如果媒體類型為 [未指定/未知\*)

-   SATA SSD + SATA HDD

-   SA SSD + SA HDD

\*傳遞的功能不 RAID 控制器無法辨識的媒體類型。 這類控制站會將 HDD 和 SSD，標示為 [未指定。 在此情況下，SSD 會作為持續儲存空間，而非快取裝置。 因此，您可以部署 Microsoft Azure 堆疊 POC 這些 SSDs 上。

**範例 Hba**: LSI 9207 8i、 LSI 9300 8i 或 LSI-9265-8i 傳遞模式

範例 OEM 設定可供使用。

## <a name="operating-system"></a>作業系統

| | **需求**  |
|---|---|
| **作業系統版本** | Windows Server 2012 R2 或更新版本。 作業系統版本不是要徑之前部署開始，當您將開機主機到 VHD 隨附於 Azure 堆疊安裝 zip 中。 將圖像做整合 OS 和所有必要的更新。 不使用任何鍵啟動 POC 中使用任何 Windows Server 執行個體。|

## <a name="deployment-requirements-check-tool"></a>部署需求檢查工具

安裝後的作業系統，您可以使用[Azure 堆疊技術預覽 2 部署檢查](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b)以確認您的硬體符合所有的需求。



## <a name="microsoft-azure-active-directory-accounts"></a>Microsoft Azure Active Directory 帳戶

Microsoft Azure 堆疊 POC 部署必須連接到 Azure。 因此，您必須在執行部署 PowerShell 指令碼之前，先準備 Microsoft Azure Active Directory 帳戶。 此帳戶會將全域管理員變成 Azure Active Directory 租用戶。 它會用於佈建和委派應用程式與服務主體與 Azure Active Directory 和圖形 API 互動的所有 Azure 堆疊服務。 也會 （由您稍後可以變更） 的預設提供者訂閱的擁有者使用。 您可以使用此帳戶來登入您的 Azure 堆疊系統管理入口網站。

1. 建立一個以上的 Azure Active Directory 的目錄系統管理員的 Azure AD 帳戶。 如果您已經有一個，您可以使用的。 否則，您可以建立一個免費[http://azure.microsoft.com/en-us/pricing/free-trial/](http://azure.microsoft.com/pricing/free-trial/) （在 china （中國），請造訪<http://go.microsoft.com/fwlink/?LinkID=717821>改為。）

    使用這些認證儲存的[執行 PowerShell 部署指令碼](azure-stack-run-powershell-script.md#run-the-powershell-deployment-script)的步驟 6。 此*服務系統管理員*帳戶可以設定及管理資源雲朵、 使用者帳戶、 租用戶計劃、 配額、 和價格。 在入口網站，他們可以建立網站雲朵虛擬機器私人雲朵、 建立計劃，及管理使用者的訂閱。

2. [建立](azure-stack-add-new-user-aad.md)至少一個帳戶，好讓您可以登入 Azure 堆疊 POC 為租用戶。

  	| **Azure Active Directory 帳戶**  | **受支援？** |
  	|---|---| 
  	| 有效公用 Azure 訂閱的公司或學校帳戶  | [是] |
  	| 有效公用 Azure 訂閱的 Microsoft 帳戶  | 無 |
  	| 有效的 china （中國） Azure 訂閱的公司或學校帳戶  | [是] |
  	| 有效的美國政府 Azure 訂閱的公司或學校帳戶  | [是] |


## <a name="network"></a>網路

### <a name="switch"></a>切換

一個可用的連接埠的切換參數 POC 電腦上。  

Azure 堆疊 POC 電腦支援連線到切換 access 連接埠或主幹連接埠。 沒有特別的功能，必須在切換。 如果您使用的主幹連接埠，或如果您需要設定虛擬區域網路識別碼，您必須提供為部署參數的虛擬區域網路識別碼。 您可以看到[部署參數的清單](azure-stack-run-powershell-script.md)中的範例。

### <a name="subnet"></a>子網路

請下列子網路連線 POC 電腦︰
- 192.168.200.0/24
- 192.168.100.0/27
- 192.168.101.0/26
- 192.168.102.0/24
- 192.168.103.0/25
- 192.168.104.0/25

在 Microsoft Azure 堆疊 POC 環境中的內部網路的保留這些子網路。

### <a name="ipv4ipv6"></a>IPv4/IPv6

僅限 IPv4 功能受支援。 您無法建立 IPv6 網路。

### <a name="dhcp"></a>DHCP

請確定在 NIC 連線到網路上沒有 DHCP 伺服器。 如果 DHCP 無法使用，您必須先準備除了主機所使用的其他靜態 IPv4 網路。 您必須提供的 IP 位址和閘道做為部署參數。 您可以看到[部署參數的清單](azure-stack-run-powershell-script.md)中的範例。

### <a name="internet-access"></a>網際網路存取

Azure 堆疊需要存取網際網路，直接或透過透明 proxy。 Azure 堆疊不支援網頁啟用存取網際網路 proxy 的設定。 主機 IP 和新的 IP 指派給 MAS BGPNAT01 （DHCP 或靜態 IP） 必須能夠存取網際網路。 連接埠 80 和 443 所使用的 graph.windows.net 與 login.windows.net 網域。

### <a name="telemetry"></a>遙測

支援遙測資料流，連接埠 443 (HTTPS) 必須是您的網路中開啟。 用戶端端點是 https://vortex-win.data.microsoft.com。


## <a name="next-steps"></a>後續步驟

[下載 Azure 堆疊 POC 部署套件](https://azure.microsoft.com/overview/azure-stack/try/?v=try)

[部署 Azure 堆疊 POC](azure-stack-run-powershell-script.md)
