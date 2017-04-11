<properties
  pageTitle="Azure 和 Linux VM 儲存 |Microsoft Azure"
  description="說明 Linux 虛擬機器 Azure 標準，進階版的儲存空間。"
  services="virtual-machines-linux"
  documentationCenter="virtual-machines-linux"
  authors="vlivech"
  manager="timlt"
  editor=""/>

<tags
  ms.service="virtual-machines-linux"
  ms.devlang="NA"
  ms.topic="article"
  ms.tgt_pltfrm="vm-linux"
  ms.workload="infrastructure"
  ms.date="10/04/2016"
  ms.author="v-livech"/>

# <a name="azure-and-linux-vm-storage"></a>Azure 及 Linux VM 存放區

Azure 儲存體是雲端儲存空間方案的新式依賴持續性、 可用性和延展性以符合其客戶的需求的應用程式。  除了讓大型的應用程式，以支援新的情況，Azure 儲存體也提供儲存基礎的 Azure 虛擬機器。

## <a name="azure-storage-standard-and-premium"></a>Azure 儲存空間︰ 標準和進階版

您可以在標準的儲存空間磁碟或進階版儲存空間的磁碟建置 azure VM。  使用入口網站，選擇您 VM 時必須切換基本概念螢幕檢視標準和付費磁碟上的下拉式清單。  以下螢幕擷取畫面會醒目提示的 [切換] 功能表。  當切換 SSD，只會顯示已啟用的 Vm，進階版儲存為所有由 SSD 磁碟機。  切換至 HDD，標準存放啟用的 Vm 備份的旋轉磁碟機就會顯示，以及 Vm 由 SSD 所支援的進階版儲存空間。

  ![screen1](../virtual-machines/media/virtual-machines-linux-azure-vm-storage-overview/screen1.png)

建立從 VM 時`azure-cli`選擇透過虛擬記憶體大小時，您可以選擇標準和進階版之間`-z`或`--vm-size`cli 標幟。

### <a name="create-a-vm-with-standard-storage-vm-on-the-cli"></a>使用標準的儲存空間 VM cli 上建立 VM

Cli 標幟`-z`選擇 Standard_A1 與 A1 的標準的儲存空間基礎 Linux VM。

```bash
azure vm quick-create -g rbg \
exampleVMname \
-l westus \
-y Linux \
-Q Debian \
-u exampleAdminUser \
-M ~/.ssh/id_rsa.pub
-z Standard_A1
```

### <a name="create-a-vm-with-premium-storage-on-the-cli"></a>建立進階版儲存在 cli VM

Cli 標幟`-z`選擇 Standard_DS1 與 DS1 正在進階版儲存基礎 Linux VM。

```bash
azure vm quick-create -g rbg \
exampleVMname \
-l westus \
-y Linux \
-Q Debian \
-u exampleAdminUser \
-M ~/.ssh/id_rsa.pub
-z Standard_DS1
```

## <a name="standard-storage"></a>標準的儲存空間

Azure 標準儲存為預設類型的儲存空間。  標準的儲存空間會在成本仍然能夠效能。  

## <a name="premium-storage"></a>進階版儲存空間

Azure 進階版儲存提供執行 O-大量工作負載的虛擬機器高效能、 低延遲磁碟支援。 使用進階版儲存空間的虛擬機器 (VM) 磁碟上實心狀態磁碟機 (SSDs) 儲存資料。 您可以將您的應用程式 VM 磁碟移轉至 Azure 進階版儲存體才能利用速度和這些磁碟的效能。

進階儲存功能︰

- 進階版儲存空間的磁碟︰ Azure 進階版存放支援 VM 磁碟可以附加到 DS、 DSv2 或 GS 數列 Azure Vm。

- 進階頁面 Blob︰ 進階版存放支援 Azure 頁面 Blob，這用來保留常設磁碟的 Azure 虛擬機器 (Vm)。

- 付費本機多餘的儲存空間︰ 進階儲存帳戶只支援本機多餘儲存空間 (LRS) 作為複寫選項以及保留三份資料一個區域中。

- [進階版儲存空間](../storage/storage-premium-storage.md)

## <a name="premium-storage-supported-vms"></a>進階儲存支援 Vm

進階版儲存裝置可支援 DS 系列，DSv2 系列，GS 系列]，然後 Fs 數列 Azure 虛擬機器 (Vm)。 您可以使用標準及進階儲存空間的磁碟與 Vm 的受支援的進階儲存空間。 但您無法使用進階版儲存空間的磁碟 VM 系列，也就是不相容的進階版儲存空間。

以下是 Linux 散佈我們驗證與進階版儲存空間。

| 通訊群組 | 版本                 | 支援的核心    |
|--------------|-------------------------|---------------------|
| Ubuntu       | 12.04                   | 3.2.0-75.110+       |
| Ubuntu       | 14.04                   | 3.13.0-44.73+       |
| Debian       | 7.x、 8.x                | 3.16.7-ckt4-1+      |
| SLES         | SLES 12                 | 3.12.36-38.1+       |
| SLES         | SLES 11 SP4             | 3.0.101-0.63.1+     |
| CoreOS       | 584.0.0+                | 3.18.4+             |
| Centos       | 6.5 6.6、 6.7、 7.0、 7.1 | 3.10.0-229.1.2.el7+ |
| RHEL         | 6.8 +、 7.2 +              |                     |


## <a name="file-storage"></a>檔案儲存空間

Azure 檔案儲存空間提供使用標準 SMB 通訊協定雲端中的檔案共用。 Azure 檔案，您可以移轉 Azure 檔案伺服器所依賴的企業應用程式。 Azure 中執行的應用程式從 Azure 虛擬機器執行 Linux，可以輕鬆地裝載檔案共用。 然後使用最新版本的檔案儲存空間，您可以也裝載檔案共用從內部部署應用程式的支援中小企業 3.0。  因為檔案共用 SMB 共用，您可以透過標準檔案系統 Api 存取。

檔案儲存為內建 Blob、 表格和佇列中的儲存空間，為相同的技術，所以檔案儲存空間提供可用性、 持續性、 延展性和 Azure 儲存平台內建的地理重複。 如需檔案儲存空間效能目標與限制的詳細資訊，請參閱 Azure 儲存體延展性和效能目標。

- [如何使用 Linux Azure 檔案儲存空間](../storage/storage-how-to-use-files-linux.md)

## <a name="hot-storage"></a>作用儲存空間

Azure 作用儲存層最適合儲存經常存取的資料。  作用儲存空間是 blob 儲存的預設儲存空間類型。

## <a name="cool-storage"></a>製作出酷炫的儲存空間

Azure 製作出酷炫的儲存空間層最適合儲存的資料，且不常存取久。 範例使用情況下，製作出酷炫的儲存空間包含備份、 媒體內容、 科學資料、 規範和保存的資料。 一般而言，少存取的任何資料是完美候選製作出酷炫的儲存空間。

|                             | 作用儲存層      | 製作出酷炫的儲存空間層     |
|:----------------------------|:---------------------:|:---------------------:|
| 顯示狀態                | 99.9%                 | 99%                   |
| 顯示狀態 （RA GRS 讀取） | 99.99%                | 99.9%                 |
| 使用費用               | 較高的儲存空間成本  | 降低儲存空間的成本   |
|                             | 較低的存取權          | 較高的存取權         |
|                             | 和交易成本 | 和交易成本 |


## <a name="redundancy"></a>重複

您的 Microsoft Azure 儲存體帳戶中的資料永遠複寫永久性且高的顯示狀態，會議 Azure 儲存體 SLA 甚至面對暫時性硬體失敗。

當您建立的儲存空間帳戶時，您必須選取下列複寫選項的其中一個︰

- 在本機上多餘的儲存空間 (LRS)
- 區域多餘的儲存空間 (ZRS)
- 地理多餘的儲存空間 (GRS)
- 讀取權限地理多餘的儲存空間 (RA GRS)

### <a name="locally-redundant-storage"></a>在本機上多餘的儲存空間

在本機上多餘的儲存空間 (LRS) 會將複製您用來建立您儲存的帳戶的區域內的資料。 最大化持續性，對您儲存的帳戶中的資料每個要求複寫三次。 下列三個複本每個位於另一個錯誤網域和升級的網域。  要求已成功傳回一次寫入所有三個的複本。

### <a name="zone-redundant-storage"></a>區域多餘的儲存空間

區域多餘的儲存空間 (ZRS) 會將您的資料複製跨三設備內的一個區域或跨兩個區域，提供更高的持續性比 LRS。 如果您儲存的帳戶有啟用 ZRS，則為長期失敗 」 功能，即使您的資料。

### <a name="geo-redundant-storage"></a>地理多餘的儲存空間

地理多餘的儲存空間 (GRS) 會將您的資料複製到下的數百個主要區域離英哩的第二個區域。 如果您儲存的帳戶有啟用 GRS，您的資料是長期即使完成的地區資料或損毀的主要區域是無法復原。

### <a name="read-access-geo-redundant-storage"></a>讀取權限地理多餘的儲存空間

讀取權限地理多餘的儲存空間 (RA GRS) 會將儲存帳戶的可用性最大化提供唯讀存取次要的位置，除了複寫跨 GRS 所提供的兩個區域中的資料。 主要區域中，無法使用的資料，您的應用程式可以讀取資料，從第二個區域。

針對 [深層不動產到 Azure 儲存複本，請參閱︰

- [Azure 儲存複寫](../storage/storage-redundancy.md)

## <a name="scalability"></a>延展性

Azure 儲存體是人連線擴充，您可以儲存，以及處理程序數百 tb 支援大型資料的資料的所需的科學、 財務分析和媒體應用程式。 或者您可以儲存少量 small business 網站所需的資料。 落在您的需求，無論您是付款僅適用於已儲存的資料。 Azure 儲存體目前儲存數十 trillions 的唯一的客戶物件，以及平均處理數百萬秒的要求。

標準儲存帳戶︰ 標準儲存帳戶有 20000 IOPS 最大的總要求工資率。 在所有的虛擬機器磁碟標準儲存帳戶總 IOPS 不可超過此限制。

進階版儲存的帳戶︰ 進階儲存帳戶有 50 Gbps 最大的總處理量工資率。 所有的 VM 磁碟上的總處理能力不可超過此限制。

## <a name="availability"></a>顯示狀態

我們保證的至少 99.99%（99.9%的酷炫的存取層) 的時間，我們會成功處理要求的讀取權限地理多餘的儲存空間 (RA GRS) 帳戶，請從讀取資料的第二個區域上重試失敗的嘗試讀取的主要區域中的資料。

我們保證的至少 99.9%（99%的酷炫的存取層) 的時間，我們會成功處理要求讀取資料的本機多餘儲存空間 (LRS)] 區域多餘儲存空間 (ZRS)，以及地理多餘儲存空間 (GRS) 帳戶。

我們保證的至少 99.9%（99%的酷炫的存取層) 的時間，我們會成功處理要求資料寫入本機多餘儲存空間 (LRS)、 區域多餘儲存空間 (ZRS)，及地理多餘儲存空間 (GRS) 帳戶以及讀取權限地理多餘的儲存空間 (RA GRS) 帳戶。

- [Azure SLA 儲存空間](https://azure.microsoft.com/support/legal/sla/storage/v1_1/)


## <a name="regions"></a>區域

Azure 推出 30 世界各地的區域，而具有本項方案 4 的其他區域。 地理擴充是 Azure 優先順序，因為它可以讓我們的客戶達成較高的效能及它支援其需求和喜好設定相關資料的位置。  若要啟動的 azures 最新區域是在德國。

Microsoft Cloud 德國提供差異的選項已使用的 Microsoft 雲端服務歐洲，提高的機會創新中建立及經濟成長高度規定合作夥伴和客戶德國、 歐盟 (EU) 和歐洲自由貿易關聯 (EFTA)。

Magdeburg 和 Frankfurt，這些新資料中心的客戶資料所控制的資料信任、 T 系統國際、 獨立的德文公司及德國 Telekom 的管理。 Microsoft 的商業雲端服務，這些資料中心的德文資料處理法規遵循，並為客戶的方式及位置處理資料的其他選項。


- [Azure 區域圖](https://azure.microsoft.com/regions/)

## <a name="security"></a>安全性

Azure 儲存體提供完整的安全性功能的共同啟用安全的應用程式。 可以使用角色型存取控制和 Azure Active Directory 受到保護的儲存空間帳戶本身。 資料可以使用用戶端加密、 HTTPS 或中小企業 3.0 受到保護的應用程式和 Azure 之間傳送之用。 資料可以設定自動加密寫入 Azure 儲存體時使用儲存空間服務加密 (SSE)。 虛擬機器所使用的作業系統與資料磁碟可以設定為使用 Azure 磁碟加密加密。 Azure 儲存體中的資料物件的委派存取權授與使用共用的 Access 簽章。

### <a name="management-plane-security"></a>管理平面安全性

管理平面包含用來管理您儲存的帳戶的資源。 在此區段中，我們將討論 Azure 資源管理員部署模型，以及如何使用角色型存取控制 (RBAC) 來控制存取您儲存的帳戶。 我們也會與管理您儲存帳戶金鑰，以及如何重新產生它們。

### <a name="data-plane-security"></a>資料平面安全性

在此區段中，我們會查看允許存取實際資料物件，在您儲存的帳戶，例如二進位大型物件、 檔案、 佇列和表格中使用共用的 Access 簽章和儲存存取原則。 我們會說明服務層級 SA 和帳戶層級 SA。 我們也會看到如何限制存取特定的 IP 位址 （或 IP 位址範圍），限制為 HTTPS，使用的通訊協定以及撤銷共用 Access 簽章，而不需等待為過期。

## <a name="encryption-in-transit"></a>在傳輸時會加密

本節將討論如何保護資料，當您傳送進或移出 Azure 儲存體。 我們將討論建議使用 HTTPS 及中小企業 3.0 用於 Azure 檔案共用加密。 我們也會在用戶端加密，可讓您之前在用戶端應用程式中，將儲存空間傳送加密的資料，並解密資料傳輸不儲存後的外觀。

## <a name="encryption-at-rest"></a>在其餘的加密

我們會討論儲存服務加密 (SSE)，及如何啟用它儲存帳戶，導致您的封鎖 blob，頁面 blob 和附加 blob 寫入 Azure 儲存體時自動加密。 我們也會看看如何使用 Azure 磁碟加密，並瀏覽基本差異和與 SSE 與用戶端加密的磁碟加密的情況。 我們會簡短地看 FIPS 規範的美國政府電腦。

- [Azure 儲存空間的安全性指南](../storage/storage-security-guide.md)

## <a name="cost-savings"></a>節省的成本

- [儲存空間的成本](https://azure.microsoft.com/pricing/details/storage/)

- [儲存空間成本計算機](https://azure.microsoft.com/pricing/calculator/?service=storage)

## <a name="storage-limits"></a>儲存限制

- [服務的儲存限制](../azure-subscription-service-limits.md#storage-limits)
