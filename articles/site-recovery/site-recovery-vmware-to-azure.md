<properties
    pageTitle="複寫 VMware 虛擬機器和實體伺服器 Azure 網站復原 Azure Azure 入口網站中 |Microsoft Azure"
    description="說明如何部署 Azure 網站復原協調複寫、 錯誤移轉及復原的內部部署 VMware 虛擬機器和 Windows/Linux 實體伺服器 Azure 使用 Azure 入口網站"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/12/2016"
    ms.author="raynew"/>

# <a name="replicate-vmware-virtual-machines-and-physical-machines-to-azure-with-azure-site-recovery-using-the-azure-portal"></a>使用 [Azure 入口網站的 Azure 網站復原 Azure 複寫 VMware 虛擬機器和實體機器

> [AZURE.SELECTOR]
- [Azure 入口網站](site-recovery-vmware-to-azure.md)
- [Azure 傳統](site-recovery-vmware-to-azure-classic.md)
- [Azure 傳統 （舊版）](site-recovery-vmware-to-azure-classic-legacy.md)

歡迎使用 Azure 網站復原 ！ 如果您想要將內部部署 VMware 虛擬機器或 Windows/Linux 實體伺服器複寫到 Azure Azure 入口網站中使用 Azure 網站修復，請使用這份文件。

> [AZURE.NOTE] Azure 有兩種不同的[部署模型](../resource-manager-deployment-model.md)建立及使用的資源︰ Azure 資源管理員 (ARM)] 和 [傳統。 Azure 也會有兩個入口網站 – 支援傳統部署模型，Azure 傳統入口網站和支援兩種部署模型 Azure 入口網站。

Azure 入口網站中的網站復原提供許多新的功能︰

- Azure 備份和 Azure 網站復原服務會合併到單一的修復服務保存庫，讓您可以設定及管理業務連續性和損毀修復 (BCDR) 從單一位置。 整合的儀表板中您可以監視並管理您的內部部署網站與 Azure 公用雲端作業。
- 現在，佈建與雲端解決方案提供者 (CSP) 程式 Azure 訂閱的使用者能管理 Azure 入口網站中的網站修復作業。
- 網站復原 Azure 入口網站中的可以複寫機器 ARM 儲存帳戶。 在錯誤後移轉，網站復原會建立 arm Vm Azure 中。
- 網站復原仍會繼續支援複寫傳統的儲存空間的帳戶。 在錯誤後移轉，網站復原建立 Vm 使用傳統的模型。

讀取後這份文件張貼底部 Disqus 註解中的任何註解。 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上提出技術的問題。

## <a name="overview"></a>概觀

組織必須決定如何應用程式、 負載，以及資料保持執行及使用期間計劃和計畫停機時間和越快越復原正常運作狀況 BCDR 策略。 您 BCDR 策略應該安全及修復，保留商務資料，並確保當發生損毀時，仍然持續可用的工作量。

網站復原是 Azure 服務可提供您 BCDR 策略來協調實體伺服器的內部部署和雲端 (Azure) 或次要資料中心的虛擬機器複寫。 中斷問題發生時在您的主要位置，您無法透過，讓應用程式可用的工作負載的第二個位置。 會傳回標準作業時，您會回到您的主要位置失敗。 如需深入瞭解[什麼是 Azure 網站復原？](site-recovery-overview.md)

本文提供您要複製的所有資訊內部部署 VMware Vm 和 Azure Windows/Linux 實體伺服器。 其包含架構的概觀，規劃的詳細資訊，以及設定 Azure、 內部部署伺服器、 複寫設定與容量規劃部署步驟。 您已設定基礎結構之後，您可以啟用複製您要保護，請核取該容錯移轉運作的電腦上。

## <a name="business-advantages"></a>商務優點

- 網站復原提供離線保護商務工作量及 VMware Vm 和實體伺服器上執行應用程式。
- 修復服務入口網站提供設定、 管理及監視複寫、 錯誤移轉及復原的單一位置。
- 網站復原才能自動探索 VMware Vm 新增至 vSphere 主機。
- 您可以輕鬆地執行容錯移轉從您的內部部署基礎結構 Azure 及回復 （還原） 從 Azure VMware VM 伺服器在您的內部部署網站。
- 您可以啟用多重 VM，並讓應用程式工作負載層跨多個機器複寫同時建立複寫群組。 當對方無法透過複寫群組中的所有電腦會都有當機一致和應用程式一致的復原點。 容錯移轉，您可以復原計劃中收集的多部電腦，以便多層應用程式負載一起容錯。

## <a name="supported-operating-systems"></a>支援的作業系統

### <a name="windows64-bit-only"></a>Windows （僅限 64 位元）
- Windows Server 2008 R2 SP1 +
- Windows Server 2012
- Windows Server 2012 R2

### <a name="linux-64-bit-only"></a>Linux （僅限 64 位元）
- 紅色的角色企業 Linux 6.7，7.1、 7.2
- CentOS 6.5，6.6 6.7、 7.0、 7.1、 7.2
- Oracle 企業 Linux 6.4，6.5 執行 「 紅色角色相容核心 」 或 「 永不折損企業核心第 3 版 (UEK3)
- SUSE Linux Enterprise Server 11 SP3

## <a name="scenario-architecture"></a>案例架構

以下是分析藍本元件︰

- **設定伺服器**︰ 內部部署電腦的協調通訊和管理資料複寫及修復程序。 您會在這台電腦上執行單一安裝檔案來安裝設定伺服器和其他元件︰
    - **程序伺服器**︰ 做為複寫閘道器。 它從受保護的來源機器接收複寫資料、 將其最佳化的快取、 壓縮和加密，並將其傳送至 Azure 儲存體。 也會處理推入安裝行動服務受保護的電腦，並執行 VMware Vm 的自動探索。 預設的程序伺服器是設定伺服器上安裝。 您可以部署至您的部署不按比例縮放的其他獨立程序伺服器。
    - **主要目標伺服器**︰ 在 [從 Azure 回復時處理複寫資料。

- **行動服務**︰ 這個元件部署在您想要複製到 Azure 每個電腦 （VMware VM 或實體伺服器）。 它會擷取資料寫入在電腦上的，並轉寄給程序伺服器。
- **Azure**︰ 您不需要建立處理複寫和容錯移轉至 Azure 任何 Azure Vm。  您需要 Azure 訂閱，將複製的資料和 Azure 虛擬網路，Azure Vm 後移轉後，會連線到網路 Azure 儲存體帳戶。 儲存帳戶和網路必須修復服務保存庫為相同的區域。
- **回復**︰ 當您準備好要從 Azure 至您的內部部署網站失敗後移轉後時，會建立所需為暫時程序伺服器 Azure VM。 回復完成後，您可以刪除它。 回復，您也必須要有 vpn 才能 （或 Azure ExpressRoute） 連線內部部署網站與您的 Azure Vm 位於 Azure 網路之間。 如果經常回復流量您可能也需要設定專用主要目標伺服器電腦內部部署。 更亮流量的預設設定伺服器上執行的主要目標伺服器可用。


圖形會顯示這些元件互動的方式。

![架構](./media/site-recovery-vmware-to-azure/v2a-architecture-henry.png)

**圖 1: VMware/實體至 Azure**

## <a name="azure-prerequisites"></a>Azure 的先決條件

以下是您需要 Azure 中部署這種情況。

**必要條件** | **詳細資料**
--- | ---
**Azure 帳戶**| 您必須以[Microsoft Azure](http://azure.microsoft.com/)帳戶。 您可以開始使用[免費的試用版](https://azure.microsoft.com/pricing/free-trial/)。 [瞭解更多](https://azure.microsoft.com/pricing/details/site-recovery/)有關網站復原價格。
**Azure 儲存體** | 複製的資料儲存在 Azure 儲存空間，發生錯誤後移轉時，系統會建立 Azure Vm。 <br/><br/>若要儲存資料，您必須修復服務保存庫為相同的區域中的標準或付費的儲存空間帳戶。<br/><br/>您可以使用 LRS 或 GRS 儲存帳戶。 我們建議您 GRS，讓資料是同時，如果地區的資料，或如果無法復原的主要區域。 [進一步瞭解](../storage/storage-redundancy.md)。<br/><br/> [進階版儲存空間](../storage/storage-premium-storage.md)，通常是需要一致 IO 高效能與主機 IO 大量工作量低延遲的虛擬機器。<br/><br/> 如果您想要儲存複製的資料使用進階版帳戶，您將需要儲存擷取內部部署資料的變更進行中的複製記錄的標準儲存帳戶。<br/><br/> 請注意 Azure 入口網站中建立的儲存空間帳戶無法跨越資源群組。 也在中央印度和南美洲印度進階版儲存帳戶保護目前不支援。<br/><br/> [閱讀相關資訊](../storage/storage-introduction.md)Azure 儲存空間。
**Azure 網路** | 您必須容錯移轉發生時，將會連線到的 Azure Vm Azure 虛擬網路。 Azure 虛擬網路必須在同一個地區為修復服務保存庫。
**從 Azure 回復** | 您必須暫時設定為 Azure VM 的程序伺服器。 當您準備好回復，並將其刪除失敗回完成後，您可以建立這。<br/><br/> 失敗回您需要 VPN 連線 （或 Azure ExpressRoute） 從 Azure 網路至內部部署網站。

## <a name="configuration-server--scale-out-process-prerequisites"></a>設定伺服器 / 擴充程序的先決條件

您將會設定為 [設定伺服器內部部署電腦 / 擴充程序伺服器。

**必要條件** | **詳細資料**
--- | ---
**設定伺服器**| 您必須在內部部署的實體或執行 Windows Server 2012 R2 的虛擬機器。 這台電腦上安裝所有的內部部署網站復原元件。<br/><br/>VMware VM 複寫我們建議您部署高度可用 VMware VM 伺服器。 如果您複製實體機器電腦可以是實體伺服器。<br/><br/> 從 Azure 至內部部署網站回復永遠是 VMware Vm 無論是否移轉 Vm 或實體的伺服器。 如果您沒有為您要設定不同的主版目標伺服器為 VMware VM 接收回復流量 VMware VM 部署設定伺服器。<br/><br/>如果伺服器 VMware VM，網路介面卡類型應該 VMXNET3。 如果您使用不同類型的網路介面卡必須安裝 vSphere 5.5 伺服器上的 [ [VMware 更新](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1)。<br/><br/>伺服器應有的靜態 IP 位址。<br/><br/>伺服器不應該網域控制站。<br/><br/>伺服器主機名稱應 15 個字元小於或等於。<br/><br/>應該僅英文作業系統。<br/><br/> 您需要安裝 VMware vSphere PowerCLI 6.0。 設定在伺服器上。<br/><br/>設定伺服器需要存取網際網路。 輸出存取，必須具備，如下所示︰<br/><br/>暫時存取 HTTP 80 期間設定 （若要下載 MySQL） 的網站復原元件<br/><br/>進行中的輸出存取權 HTTPS 443 複寫管理<br/><br/>進行中的輸出存取權 HTTPS 9443 複寫流量 （可以修改這個連接埠）<br/><br/>伺服器也需要存取下列 Url，讓它可以連線至 Azure: *。 hypervrecoverymanager.windowsazure.com; *。accesscontrol.windows.net;*。 backup.windowsazure.com; *。blob.core.windows.net;*。 store.core.windows.net<br/><br/>如果您在伺服器上有 IP 位址的防火牆規則，請核取規則，允許 Azure 通訊。 您必須允許[Azure 資料中心 IP 範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (443) 通訊協定。<br/><br/>允許的 IP 位址範圍 Azure 區域，您的訂閱，與西美國。<br/><br/>允許 MySQL 下載這個 URL:.http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi


## <a name="vmware-vcentervsphere-host-prerequisites"></a>VMware vCenter/vSphere 主機先決條件

**必要條件** | **詳細資料**
--- | ---
**vSphere**| 您需要一個或多個 VMware vSphere hypervisors。<br/><br/>Hypervisors 應該執行 vSphere 版本 6.0、 5.5 或 5.1 最新的更新。<br/><br/>我們建議您 vSphere 主辦城市] 和 [vCenter 伺服器都位於相同的網路程序伺服器 （這是在其中設定伺服器所處的網路除非您已設定專用的程序伺服器）。
**vCenter** | 我們建議您部署管理您的 vSphere 主機 VMware vCenter 伺服器。 它應該執行 vCenter 版本 6.0 或 5.5 最新的更新。<br/><br/>請注意，網站復原不支援新 vCenter vSphere 6.0 功能，例如交互 vCenter vMotion 虛擬磁碟區與儲存 DRS。 網站復原支援僅限於也是版本 5.5 提供的功能。


## <a name="protected-machine-prerequisites"></a>受保護的電腦的先決條件

**必要條件** | **詳細資料**
--- | ---
**內部部署 (VMware Vm)** | 您想要保護的 VMware Vm 應有 VMware 工具安裝及執行。<br/><br/> 您想要保護的電腦應符合建立 Azure Vm [Azure 的先決條件](site-recovery-best-practices.md#azure-virtual-machine-requirements)。<br/><br/>在受保護的電腦上的個別磁碟容量應該不超過 1023 GB。 VM 可以有最多 64 磁碟 (因此最多 64 TB)。 <br/><br/>最小 2 GB 的可用空間安裝元件安裝磁碟機上。<br/><br/>不支援加密磁碟 Vm 的保護。<br/><br/>共用磁碟來賓叢集不受支援。<br/><br/>**連接埠 20004**應該開啟 「 受保護的虛擬機器本機的防火牆，如果您想要啟用**應用程式的一致性**。<br/><br/>整合可延伸韌體介面 (UEFI) 機器 / 可延伸韌體 Interface(EFI) 開機不受支援。<br/><br/>電腦名稱應介於 1 到 63 個字元 （字母、 數字和連字號）。 名稱必須字母或數字的開頭和結尾字母或數字。 啟用機器複寫後，您可以修改 Azure 的名稱。<br/><br/>如果來源 VM NIC 小組 Azure 失敗之後會將其轉換成單一 NIC。<br/><br/>如果受保護的虛擬機器 iSCSI 磁碟然後網站復原轉換受保護的 VM iSCSI 磁碟 VHD 檔案 VM 無法透過 Azure 時。 如果可以連線 Azure VM iSCSI 目標然後會連線到其並基本上看到兩個磁碟 – VHD 磁碟上 Azure VM 及來源 iSCSI 磁碟。 在此情況下必須中斷連線隨後出現於 Azure VM iSCSI 目標。
**在 Windows 電腦 (實體或 VMware)** | 支援的 64 位元作業系統應該執行機器︰ Windows Server 2012 R2、 Windows Server 2012 或 Windows Server 2008 R2，至少 SP1。<br/><br/> 作業系統應該安裝 C:\ 磁碟機上。 OS 磁碟應該 Windows 基本磁碟而非動態。 您可以動態資料磁碟。<br/><br/>網站復原支援 Vm RDM 磁碟。 期間回復，網站復原會重複使用 RDM 磁碟，如果使用原始的來源 VM 和 RDM 磁碟。 如果他們無法使用，期間回復上的網站復原會建立新的 VMDK 檔案，每個磁碟。
**Linux 電腦**（phyical 或 VMware）|  您需要支援的 64 位元作業系統︰ 紅色角色企業 Linux 6.7,7.1,7.2;Centos 6.5、 6.6,6.7,7.0,7.1,7.2;Oracle 企業 Linux 6.4，執行 「 紅色角色相容核心 」 或 「 永不折損企業核心第 3 版 (UEK3)，SUSE Linux Enterprise Server 11 SP3 6.5。<br/><br/>在受保護的電腦上的 /etc/hosts 檔案必須包含本機主機名稱對應到所有的網路介面卡與相關聯的 IP 位址的項目。<br/><br/>如果您要連線至執行後使用安全命令介面的用戶端 (ssh) 的移轉後的 Linux Azure 虛擬機器，確定受保護的電腦上的安全命令介面服務，設定為自動開始系統開機，及防火牆規則，允許 ssh 連線。<br/><br/>主機名稱、 連接點、 裝置名稱，並 Linux 系統路徑和檔案名稱 （例如/等 /; /usr） 應該是英文只。<br/><br/>只使用下列的儲存空間 Linux 電腦啟用保護︰ 檔案系統 （EXT3 ETX4、 ReiserFS、 XFS）;多重路徑軟體裝置 （多重路徑） 的對應程式）。大量管理員: (LVM2)。 不支援使用 HP CCISS 控制器儲存空間的實體伺服器。 只有 SUSE Linux Enterprise Server 11 SP3 支援 ReiserFS 檔案系統。<br/><br/>網站復原支援 Vm RDM 磁碟。  期間的 Linux 回復，網站復原不重複使用 RDM 磁碟。 請改為它會建立新的 VMDK 檔案，每個對應 RDM 磁碟。<br/><br/>確保您在設定的參數中 VMware VM 設定 disk.enableUUID=true 設定。 如果不存在，請建立項目。 需要 VMDK 提供一致的 UUID，使其裝載正確。 新增此設定也可確保在回復，並不完整的複寫期間只 delta 變更並回到內部部署中傳輸。
**行動服務** |  **Windows**︰ 您會自動發送到 Vm 執行必須要讓系統管理員帳戶 （在 Windows 電腦上的本機系統管理員），以便程序伺服器可以執行的推入安裝 Windows 的 [行動服務。<br/><br/>**Linux**︰ 若要自動推入 Vm 執行的 Linux 必須先建立一個帳戶，可讓程序 server 推入安裝的 [行動服務。<br/><br/> 依預設會複寫在電腦上的所有磁碟。 若要[排除複寫從光碟](#exclude-disks-from-replication)，行動服務必須先安裝以手動方式在電腦上啟用複寫。<br/>

## <a name="prepare-for-deployment"></a>準備進行部署

準備部署，您需要︰

1. 在其中 Azure Vm 時，會位於它們開始後移轉後的[Azure 網路的設定](#set-up-an-azure-network)。 此外，回復為必須設定 VPN 連線 （或 Azure ExpressRoute） 從 Azure 網路至您的內部部署網站。
2. [設定 Azure 儲存體帳戶](#set-up-an-azure-storage-account)複製的資料。
3. [準備帳戶](#prepare-an-account-for-automatic-discovery)vCenter 伺服器或 vSphere 裝載，好讓網站復原可以自動偵測 VMware Vm 新增。
4. [準備設定伺服器](#prepare-the-configuration-server)]，以確保其可以存取所需的 Url，並安裝 vSphere PowerCLI 6.0。


### <a name="set-up-an-azure-network"></a>設定 Azure 的網路

- 網路應該位於您會在其中部署修復服務保存庫相同 Azure 區域。
- 根據您想要用於無法透過 Azure Vm 資源模型，您可以設定[ARM 模式](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)] 或 [[標準模式](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)中的 Azure 網路設定。
- 以您的內部部署 VMware 網站無法從 Azure 您需要 VPN 連線 （或 Azure ExpressRoute 連線） 從 Azure 中複製的 Azure Vm 位於，內部部署網路設定伺服器所在的網路。
- [深入瞭解如何](../vpn-gateway/vpn-gateway-site-to-site-create.md)支援部署模型 VPN 網站的連線，以及如何[設定連線](../vpn-gateway/vpn-gateway-site-to-site-create.md#create-your-virtual-network)。
- 或者您可以設定[Azure ExpressRoute](../expressroute/expressroute-introduction.md)。 [深入](../expressroute/expressroute-howto-vnet-portal-classic.md)瞭解設定 Azure ExpressRoute 網路。

> [AZURE.NOTE] 用於部署網站復原的網路不支援[的網路移轉](../resource-group-move-resources.md)跨資源群組內的相同的訂閱或訂閱。

### <a name="set-up-an-azure-storage-account"></a>設定 Azure 儲存體帳戶

- 您必須為標準或進階版 Azure 儲存體帳戶保留資料複製到 Azure。 帳戶必須在同一個地區為修復服務保存庫。 根據您想要用於無法透過 Azure Vm 資源模型，您可以設定[ARM 模式](../storage/storage-create-storage-account.md)] 或 [[標準模式](../storage/storage-create-storage-account-classic-portal.md)中的帳戶設定。
- 如果您使用的複寫的資料建立標準儲存擷取進行中的變更內部部署資料的複寫記錄其他帳戶所需的進階版帳戶。  

> [AZURE.NOTE] 儲存帳戶的部署網站復原不支援[的儲存空間帳戶移轉](../resource-group-move-resources.md)跨資源群組內的相同的訂閱或訂閱。

### <a name="prepare-an-account-for-automatic-discovery"></a>準備自動探索的帳戶

網站修復程序伺服器才能自動探索 VMware Vm，vSphere 主機或管理主機 vCenter 伺服器上。 若要執行自動探索網站復原認證的可以存取 VMware 伺服器。 這不是如果您複製實體的電腦。

1. 使用自動探索的專用的帳戶建立 vCenter 層級具有[必要的權限](#vmware-account-permissions)的角色 (例如 Azure_Site_Recovery)。
2. VSphere 主機] 或 [vCenter 伺服器上建立新的使用者與角色指派給使用者。 稍後您會讓知道這些認證，讓它可以執行自動探索網站復原。

    >[AZURE.NOTE] VCenter 使用者帳戶與唯讀角色可以執行容錯移轉，但無法關閉受保護的來源機器。 如果您想要關閉這些機器必須[Azure_Site_Recovery](#vmware-account-permissions)角色。 如果您只有移轉 Vm 從 VMware 至 Azure，不需要回復唯讀角色是足夠的。

### <a name="prepare-the-configuration-server"></a>準備設定伺服器

1.  請確定您使用的設定伺服器的電腦符合[的先決條件](#configuration-server-prerequisites)。 特別確定電腦已連線至網際網路，利用這些設定︰

    - 允許存取這些 Url: *。 hypervrecoverymanager.windowsazure.com; *。accesscontrol.windows.net;*。 backup.windowsazure.com; *。blob.core.windows.net;*。 store.core.windows.net
    - 允許[http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi](http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi)下載 MySQL 存取。
    - 允許[Azure 資料中心的 IP 範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653)及 HTTPS (443) 通訊協定 Azure 防火牆通訊。

2.  下載並安裝[VMware vSphere PowerCLI 6.0](https://developercenter.vmware.com/tool/vsphere_powercli/6.0)設定伺服器上。 （目前 PowerCLI 其他版本不支援，包括 R 發行版本 6.0。）


## <a name="create-a-recovery-services-vault"></a>建立修復服務保存庫

1. [Azure 入口網站](https://portal.azure.com)登入。
2. 按一下 [**新** > **管理** > **備份及網站修復 (OMS)**。 或者您可以按一下 [**瀏覽** > **復原服務保存庫** > **新增**。

    ![新的保存庫](./media/site-recovery-vmware-to-azure/new-vault3.png)

3. 在 [**名稱]**中，指定識別保存庫好記的名稱。 如果您有多個訂閱，請選取其中之一。
4. [建立新的資源群組](../resource-group-template-deploy-portal.md)或選取現有的項目。 指定 Azure 的區域。 電腦會複製到此區域。 請注意，Azure 儲存和網路用於網站復原必須在同一個區域中。 若要檢查支援的地區請參閱[Azure 網站復原定價詳細資料](https://azure.microsoft.com/pricing/details/site-recovery/)中的地理可用性
4. 如果您想要從儀表板的 [快速存取保存庫按一下 [**固定至儀表板**，然後按一下 [**建立**。

    ![新的保存庫](./media/site-recovery-vmware-to-azure/new-vault-settings.png)

新的保存庫會出現在**儀表板**上 > **所有資源**，然後在 [主要**修復服務保存庫**刀。

## <a name="getting-started"></a>快速入門

網站復原提供設計用來協助您快速入門體驗和盡快執行。 它會檢查必要條件，並會引導您完成您需要取得部署網站復原的步驟。

您可以選取類型的電腦您想要複製，和您要複製到的位置。 您設定的基礎結構，包括內部部署伺服器、 Azure 設定、 複寫原則，以及容量計劃。 您的基礎結構中位置後您啟用複寫 Vm 和實體伺服器。 然後，您可以執行錯誤後移轉的特定的電腦，或建立失敗的多部電腦上的修復計劃。

選擇您想要部署網站復原的如何開始快速入門。 快速入門流程稍微改變，根據您複製的需求。


## <a name="step-1-choose-your-protection-goals"></a>步驟 1︰ 選擇 [保護定目標

選取您要複製及您要複製到的位置。

1. **修復服務保存庫**刀中選取您保存庫，然後按一下 [**設定]**。
2. 在 [**設定** > **快速入門**按一下**網站復原** > **步驟 1︰ 準備基礎結構** > **保護目標**。

    ![選擇目標](./media/site-recovery-vmware-to-azure/choose-goals.png)

3. **保護目標**] 中選取**至 Azure**，然後選取**[是，使用 VMware vSphere Hypervisor**。 然後按一下**[確定]**。

    ![選擇目標](./media/site-recovery-vmware-to-azure/choose-goals2.png)


## <a name="step-2-set-up-the-source-environment"></a>步驟 2︰ 設定來源環境

設定伺服器設定，並登錄修復服務保存庫。 如果您複製 VMware Vm 指定您要用於自動探索 VMware 帳戶。

1. 按一下 [**步驟 1︰ 準備基礎結構** > **來源**。 **準備來源**中如果您沒有設定伺服器按一下**+ 設定伺服器**新增。

    ![設定來源](./media/site-recovery-vmware-to-azure/set-source1.png)

2. 在**新增伺服器**刀核取該**設定伺服器**會出現在**輸入的伺服器**。
3. 設定伺服器設定之前請先確認[的先決條件](#configuration-server-prerequisites)。 在特定的核取電腦可以存取所需的 Url。
4.  下載網站修復整合安裝程式安裝檔案。
5.  下載保存庫登錄機碼。 執行整合設定時，您會需要此。 5 天後產生，是有效的金鑰。

    ![設定來源](./media/site-recovery-vmware-to-azure/set-source2.png)

6.  在您使用的設定伺服器電腦上執行整合安裝來安裝設定伺服器、 程序伺服器，以及主目標伺服器。


### <a name="run-site-recovery-unified-setup"></a>執行的網站復原整合設定

1.  執行整合安裝的 [安裝] 檔案。
2.  在**開始之前**選取**安裝設定伺服器及程序伺服器**。

    ![在您開始之前](./media/site-recovery-vmware-to-azure/combined-wiz1.png)

3. 在**第三方軟體授權**按一下 [**我接受**下載並安裝 MySQL]。

    ![第三個 = 廠商的軟體](./media/site-recovery-vmware-to-azure/combined-wiz105.PNG)

4. 在**登錄**中瀏覽並選取您從保存庫下載的登錄機碼。

    ![註冊](./media/site-recovery-vmware-to-azure/combined-wiz3.png)

5. **網際網路設定**中指定設定伺服器上執行的提供者連線至 Azure 網站復原網際網路的方式。

    - 如果您想要與目前在電腦設定 proxy 連線選取**現有的 proxy 設定的連線**。
    - 如果您想要直接連接的提供者選取**直接不 proxy 的連線**。
    - 如果現有 proxy 需要驗證]，或您想要使用自訂的 proxy 提供者連線，請選取 [**使用自訂的 proxy 設定的連線**]。
        - 如果您是使用自訂 proxy 必須先指定的地址、 連接埠和認證
        - 如果您使用的 proxy，您應該已經允許[先決條件](#configuration-server-prerequisites)所述的 Url。

    ![防火牆](./media/site-recovery-vmware-to-azure/combined-wiz4.png)

6. 在**核取 [先決條件**設定會執行檢查，確定可以執行安裝。 如果相關的**核取 [全域時間同步處理**，會出現警告確認的系統時鐘 （**日期和時間**設定） 上的時間時區相同。

    ![必要條件](./media/site-recovery-vmware-to-azure/combined-wiz5.png)

7. **MySQL 設定**中建立認證登入將會安裝的 MySQL 伺服器執行個體。

    ![MySQL](./media/site-recovery-vmware-to-azure/combined-wiz6.png)

8. 在**環境的詳細資料**選取是否要複製 VMware Vm。 如果您是，安裝程式會檢查已安裝 PowerCLI 6.0。

    ![MySQL](./media/site-recovery-vmware-to-azure/combined-wiz7.png)

9. 在**安裝位置**選取您要安裝的二進位檔案及儲存快取的位置。 您可以選取具有至少 5 GB 的可用儲存空間的磁碟機，但我們建議您至少 600 GB 的可用空間的快取磁碟機。

    ![安裝位置](./media/site-recovery-vmware-to-azure/combined-wiz8.png)

10. 在**網路選取項目**指定的接聽 （網路介面卡和 SSL 連接埠） 設定伺服器會傳送及接收複寫資料。 您可以修改預設連接埠 (9443)。 此連接埠，除了網頁伺服器的協調複寫作業要使用連接埠 443。 不應使用 443 接收複寫流量。


    ![網路選取項目](./media/site-recovery-vmware-to-azure/combined-wiz9.png)



11.  **摘要**中檢閱資訊，然後按一下 [**安裝**]。 安裝完成後，就會產生複雜密碼。 您必須將其當您啟用複寫時，請將其複製並將它維持在安全的位置。

    ![摘要](./media/site-recovery-vmware-to-azure/combined-wiz10.png)

12.  註冊完成伺服器後會顯示在**設定** > 保存庫中的**伺服器**刀。



#### <a name="run-setup-from-the-command-line"></a>從命令列執行安裝程式

您可以將從命令列設定伺服器設定︰

    UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

參數︰

- / ServerMode︰ 強制。 指定是否應該安裝 [設定] 和 [程序的伺服器，或僅限伺服器程序。 輸入的值︰ CS PS.
- InstallLocation︰ 強制。 [安裝元件] 資料夾。
- / MySQLCredsFilePath。 強制性。 檔案路徑 MySQL 伺服器認證儲存。 檔案必須此格式︰
    - [MySQLCredentials]
    - MySQLRootPassword = 」<Password>」
    - MySQLUserPassword = 」<Password>」
- / VaultCredsFilePath。 強制性。 保存庫認證檔案的位置
- / EnvType。 強制性。 安裝類型。 值︰ VMware NonVMware
- / PSIP 和 /CSIP。 強制性。 在程序伺服器和設定伺服器 IP 位址。
- / PassphraseFilePath。 強制性。 複雜密碼檔案的位置。
- / BypassProxy。 選用。 指定設定伺服器連線至 Azure 不 proxy。
- / ProxySettingsFilePath。 選用。 （預設 proxy 需要驗證] 或自訂 proxy） 的 proxy 設定。 檔案必須此格式︰
    - [ProxySettings]
    - ProxyAuthentication = [是/否]
    - Proxy IP = 」 IP 位址 > 」
    - ProxyPort = 」<Port>」
    - ProxyUserName = 」<User Name>」
    - ProxyPassword = 」<Password>」
- DataTransferSecurePort。 選用。 若要用來複製資料的連接埠號碼。
- SkipSpaceCheck。 選用。 略過空格快取的檢查。
- AcceptThirdpartyEULA。 強制性。 旗標表示接受的協力廠商使用者授權合約。
- ShowThirdpartyEULA。 強制性。 顯示第三方使用者授權合約。 如果作為輸入會忽略所有其他參數。

### <a name="add-the-vmware-account-used-for-automatic-discovery"></a>新增 VMware 所用的帳戶的自動探索

 當您準備好部署時，您應該已[建立 VMware 帳戶](#prepare-an-account-for-automatic-discovery)網站復原可以用來自動探索。 新增此帳戶如下所示︰

1. 開啟**CSPSConfigtool.exe**。 在 [安裝位置] \home\svsystems\bin 資料夾中是可做為快速鍵在桌面和位置。
2. 按一下 [**管理帳戶** > **新增帳戶**。

    ![新增帳戶](./media/site-recovery-vmware-to-azure/credentials1.png)

3. **帳戶詳細資料**中新增的帳戶，將會用於自動探索。 請注意，可能需要 15 分鐘以上的帳戶名稱，在要顯示在入口網站。 若要立即更新，請按一下 [**設定伺服器**> 伺服器名稱 >**重新整理伺服器**。

    ![詳細資料](./media/site-recovery-vmware-to-azure/credentials2.png)

### <a name="connect-to-vsphere-hosts-and-vcenter-servers"></a>連線至 vSphere 主辦城市] 和 [vCenter 伺服器

如果您複製 VMware Vm 連線至 vSphere 主辦城市] 和 [vCenter 伺服器。

1. 確認設定伺服器 vSphere 主辦城市] 和 [vCenter 伺服器的網路存取。
2. 按一下 [**準備基礎結構** > **來源**。 在**準備來源**選取設定伺服器]，然後按一下**+ vCenter**以新增 vSphere 主機] 或 [vCenter 伺服器。
3. 在**新增 vCenter**指定 vSphere 主機] 或 [vCenter 伺服器的易記的名稱，然後指定的 IP 位址或伺服器的 FQDN。 將連接埠 443 為，除非您 VMware 伺服器設定為接聽要求在不同的連接埠。 然後選取 [將用來連線到 VMware 伺服器的帳戶。 按一下**[確定]**。

    ![VMware](./media/site-recovery-vmware-to-azure/vmware-server.png)

    >[AZURE.NOTE] 如果您要新增 vCenter 伺服器或 vSphere 主機 vCenter 或 host （主機） 伺服器上沒有系統管理員權限的帳戶，請確認您的帳戶有啟用這些權限︰ 資料中心、 資料存放區、 資料夾、 主機、 網路、 資源、 虛擬機器，vSphere 分散對齊] 切換。 另外 vCenter 伺服器需要的儲存空間檢視權限。

網站復原連線至您指定的設定探索 Vm VMware 伺服器。

## <a name="step-3-set-up-the-target-environment"></a>步驟 3︰ 設定目標環境

確認您有複寫和 Azure 的網路的 Azure Vm 會將連線後移轉後的儲存空間帳戶。

1.  按一下 [**準備基礎結構** > **目標**並選取您想要使用的 Azure 訂閱。
2.  指定您想要使用的 Vm 後移轉後的部署模型。
3.  網站復原檢查您有一個或多個相容 Azure 儲存體帳戶與網路。

    ![目標](./media/site-recovery-vmware-to-azure/gs-target.png)

4.  如果您還沒有建立儲存帳戶，而且您想要建立一個使用 ARM 按一下**+ 儲存帳戶**執行的內嵌。  在**建立儲存帳戶**刀指定帳戶名稱、 類型、 訂閱及位置。 帳戶應該修復服務保存庫為相同的區域。

    ![儲存空間](./media/site-recovery-vmware-to-azure/gs-createstorage.png)

    請注意︰

    - 如果您想要建立使用傳統模式儲存帳戶必須執行的 Azure 入口網站中。 [深入瞭解](../storage/storage-create-storage-account-classic-portal.md)
    - 如果您使用的是進階版儲存帳戶複製資料，您必須設定標準的額外儲存空間帳戶至存放區複寫內部部署資料記錄的擷取進行中的變更。

    > [AZURE.NOTE] 目前不支援在中央印度和南美洲印度進階版儲存帳戶的保護。

4.  選取 Azure 的網路。 如果您還沒有建立網路，且您想要執行這項作業使用 ARM 按一下**+ 網路**執行該內嵌。 在**建立虛擬網路**刀指定網路名稱、 地址範圍、 子網路詳細資料、 訂閱及位置。 網路應該位於修復服務保存庫相同的位置。

    ![網路](./media/site-recovery-vmware-to-azure/gs-createnetwork.png)

    如果您想要建立使用傳統模式網路必須執行的 Azure 入口網站中。 [進一步瞭解](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)。

## <a name="step-4-set-up-replication-settings"></a>步驟 4︰ 設定複寫設定

1. 若要建立新的複寫原則按一下**準備基礎結構** > **複寫設定** > **+ 建立並建立關聯**。
2. **建立和相關的原則**中指定的原則名稱。
3. 在**RPO 閥值**︰ 指定 RPO 限制。 連續複寫超過此限制時，將會產生提醒。
5. 在 [**復原點保留**，指定時多久 [保留] 視窗會針對每個復原點。 受保護的電腦可以復原視窗中的任何位置。 24 小時保留為支援複寫進階版儲存到電腦。
6. 在 [**應用程式一致的快照頻率**、 指定時間間隔 （以分鐘為單位） 將會建立包含應用程式一致的快照的復原點。
7. 當您建立複寫原則時，預設相符的原則會自動建立回復的。 範例如果複寫原則是**代表原則**，則回復原則會**錯誤後原則代表回復**。 啟動回復之前，無法使用此原則。  
8. 按一下**[確定**]，建立原則。

    ![複寫原則](./media/site-recovery-vmware-to-azure/gs-replication2.png)

9. 當您建立新原則時，會自動與相關聯設定伺服器。 按一下**[確定]**。

    ![複寫原則](./media/site-recovery-vmware-to-azure/gs-replication3.png)


## <a name="step-5-capacity-planning"></a>步驟 5︰ 容量計劃

您已經有您的基本設定您的基礎結構可以考慮容量計劃，並找出您是否需要額外的資源。

復原網站提供可協助您為您的來源環境、 網站復原元件、 網路和儲存空間配置正確的資源容量規劃。 您可以執行規劃，以快速模式估算根據 Vm、 磁碟和儲存空間，平均數目，或會在其中輸入數字的工作量層級的詳細模式中。 您開始之前，您必須︰

- 收集您複寫環境，包括 Vm Vm 中硬碟，每個磁碟的儲存空間的相關資訊。
- 估計每日的變更 （變換） 率，您必須複製資料。 您可以使用[vSphere 容量規劃應用裝置](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance)可協助您執行此動作。

1.  按一下 [若要下載的工具，然後執行 [**下載**]。 [已讀取，請參閱](site-recovery-capacity-planner.md)工具隨附。
2.  完成後選取**[是]** **您已完成的容量規劃？**

    ![容量計劃](./media/site-recovery-vmware-to-azure/gs-capacity-planning.png)

下表擷取的點數可協助您處理這種情況下規劃的容量。


**元件** | **詳細資料**
--- | --- | ---
**複寫** | **每日最大值變更工資率**，受保護的電腦可以只使用一個程序伺服器，而且單一程序伺服器可以處理日變更評分最多 2 TB。 因此 2 TB 是最大的每日資料變更受保護的電腦支援的工資率。<br/><br/> **最大值處理量**，複製的電腦可以屬於 Azure 中的一個儲存帳戶。 標準儲存帳戶可以處理 20000 秒的要求，最大值，我們建議您 20000 保留 IOPS 來源電腦上的數目。 範例如果您有 5 磁碟與每個磁碟的來源機器產生 120 IOPS （8 K 大小） 來源上的然後就會在每一磁碟 IOPS 限制的 500 Azure。 數字的儲存空間帳戶所需 = 總來源 IOPs/20000。
**設定伺服器** | 設定伺服器應該能處理過所有受保護的電腦上執行的工作負載的每日變更工資率容量，而且必須足夠的頻寬持續至 Azure 儲存體中複製資料。<br/><br/> 最佳作法是我們建議您在相同的網路和 LAN 區段上找到設定伺服器，為您想要保護的電腦。 位於不同的網路，但是您想要保護的電腦應該已以便 L3 網路可見性。<br/><br/> 下表摘要列出設定伺服器的大小建議。
**程序伺服器** | 設定伺服器上的預設會安裝的第一個程序伺服器。 您可以部署至您的環境不按比例縮放的其他處理程序伺服器。 請注意︰<br/><br/> 程序伺服器接收複寫資料從受保護的電腦，並將它調整快取、 壓縮和加密傳送 Azure 之前，先使用。 程序伺服器電腦應具備足夠的資源來執行這些工作。<br/><br/> 程序伺服器使用基礎的磁碟快取。 我們建議您 600 GB 或更多處理網路瓶頸或資料時所儲存的資料變更為不同的快取磁碟。

### <a name="size-recommendations-for-the-configuration-server"></a>設定伺服器的大小建議

**CPU** | **記憶體** | **快取磁碟大小** | **資料變更率** | **受保護的電腦**
--- | --- | --- | --- | ---
8 vCPUs (通訊端 2 * 4 個核心@2.5 GHz) | 16 GB | 300 GB | 500 GB 或更少 | 複寫小於 100 的電腦。
12 vCPUs (2 通訊端 * 6 核心@2.5 GHz) | 18 GB | 600 GB | 500 GB，以 1 TB | 複寫 100 150 電腦之間。
16 vCPUs (2 通訊端 * 8 個核心@2.5 GHz) | 32 GB | 1 TB | 若要 2 TB 的 1 TB | 複寫 150 200 電腦之間。
部署另一個處理程序伺服器 | | | > 2 TB | 如果您複製超過 200 封機器，或是每日的資料變更工資率超過 2 TB，請部署其他程序伺服器。

位置︰

- 每個來源機器設定使用 3 個磁碟 100 gb。
- 我們使用這樣的儲存空間的 10 個 K 轉速 8 SA 磁碟機 RAID 10 的快取磁碟度量單位。

### <a name="size-recommendations-for-the-process-server"></a>大小建議程序伺服器

如果您要保護超過 200 封機器或每日變更率大於 2 TB，您可以新增額外的程序伺服器處理複寫下載。 若要不按比例縮放查看您可以︰

- 增加設定伺服器的數目。 例如，您可以保護進位到 400 機器與兩個設定伺服器。
- 新增額外的程序伺服器及處理而不是 （或另外） 的流量使用這些設定伺服器。

下表描述的情況︰

- 您不打算使用程序伺服器的設定伺服器。
- 您已設定的其他處理程序伺服器。
- 您已設定為使用其他的程序的伺服器受保護的虛擬機器。
- 每一個受保護的來源電腦會設定為 100 GB 的三個磁碟。

**設定伺服器** | **其他處理程序伺服器**| **快取磁碟大小** | **資料變更率** | **受保護的電腦**
--- | --- | --- | --- | ---
8 vCPUs (通訊端 2 * 4 個核心@2.5 GHz)，16 GB 的記憶體 | 4 vCPUs (2 通訊端 * 2 核心@2.5 GHz)，8 GB 的記憶體 | 300 GB | 250 GB 或更少 | 複寫 85 或更少的電腦。
8 vCPUs (通訊端 2 * 4 個核心@2.5 GHz)，16 GB 的記憶體 | 8 vCPUs (通訊端 2 * 4 個核心@2.5 GHz)，12 GB 的記憶體 | 600 GB | 250 GB，以 1 TB | 鍵入 [85 150 電腦之間複製。
12 vCPUs (2 通訊端 * 6 核心@2.5 GHz)，18 GB 的記憶體 | 12 vCPUs (2 通訊端 * 6 核心@2.5 GHz) 24 GB 的記憶體 | 1 TB | 若要 2 TB 的 1 TB | 複寫 150 225 電腦之間。


您可以在其中調整您的伺服器的方式將設定而定的小數位數您喜好設定，或擴充模型。  您不按比例縮放部署幾個高階設定及程序伺服器或擴充部署更多的伺服器，以較少的資源。 例如︰ 如果您要保護 220 機器也可以執行下列其中一項︰

- 含 12vCPU、 18 GB 的記憶體、 12vCPU，24 GB 的記憶體，與其他處理程序伺服器設定伺服器設定，並設定受保護的電腦，若要使用其他的程序僅限伺服器。
- 或者您可以設定兩個設定伺服器 （2 x 8vCPU，16 GB 的 RAM） 與兩個額外的程序伺服器 (x 8vCPU 1) 」 和 「 4vCPU x 處理 135 + 85 (220) 機器 1，設定受保護的電腦，若要使用其他的程序僅限伺服器。

若要設定其他程序伺服器，[請遵循以下指示](#deploy-additional-process-servers)。

### <a name="network-bandwidth-considerations"></a>網路頻寬考量

若要計算複寫 （初始複寫然後 delta） 的所需的頻寬，您可以使用容量規劃工具。 若要控制的頻寬複寫適用於您有幾個選項︰

- **節流頻寬**︰ 複製到 Azure 的 VMware 流量經過特定的程序伺服器。 您可以節流頻寬執行程序伺服器電腦上。
- **影響頻寬**︰ 您可能會影響頻寬用於複寫使用幾個登錄機碼︰
    - **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\UploadThreadsPerVM**登錄值指定的執行緒所使用的資料傳輸 （初始或 delta 複寫） 磁碟的數目。 較高的值會增加用於複寫的網路頻寬。
    - **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DownloadThreadsPerVM**指定執行緒回復期間應使用的資料傳輸的數目。

#### <a name="throttle-bandwidth"></a>節流頻寬

1. Microsoft Azure 備份 MMC 嵌入式管理單元做為程序伺服器電腦上開啟。 根據預設 Microsoft Azure 備份的快速鍵是在桌面上或 C:\Program Files\Microsoft Azure 復原服務 Agent\bin\wabadmin。
2. 在嵌入式管理單元中按一下 [**變更內容**]。

    ![節流頻寬](./media/site-recovery-vmware-to-azure/throttle1.png)

3. **Throttling** ] 索引標籤上選取 [**啟用節流備份作業的網際網路頻寬使用量**，設定工作的限制和非工作時間。 有效範圍是從 512 Kbps 102 Mbps 秒。

    ![節流頻寬](./media/site-recovery-vmware-to-azure/throttle2.png)

您也可以使用[設定 OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) cmdlet 設定節流設定。 以下是範例︰

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**設定 OBMachineSetting NoThrottle**表示沒有節流必要。


#### <a name="influence-network-bandwidth"></a>影響網路頻寬

1. 在登錄中瀏覽至**HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**。
    - 若要影響複製的磁碟上的頻寬流量，修改值**UploadThreadsPerVM**，或建立索引鍵，如果不存在。
    - 若要影響 azure 回復流量的頻寬，修改**DownloadThreadsPerVM**的值。
2. 預設值為 4。 在 「 overprovisioned 」 的網路中，下列登錄機碼應該變更預設值。 最大值為 32。 監控流量最佳化的值。

## <a name="step-6-replicate-applications"></a>步驟 6︰ 複製應用程式

請確定您想要複製的電腦準備行動服務的安裝，然後啟用複寫。

### <a name="install-the-mobility-service"></a>安裝行動服務

啟用保護虛擬機器及實體伺服器的第一個步驟是安裝行動服務。 您可以有幾種方法︰

- **程序 server 推入**︰ 當您啟用在電腦上的試驗時，推入和從程序伺服器安裝行動服務元件。 請注意，是否機器已執行的向上 todate 版本的元件，就不會發生推入安裝。
- **企業推入**︰ 自動安裝元件使用企業推入處理程序，例如 WSUS 或系統管理中心設定管理員或[Azure 自動化和 Desired 狀態設定](./site-recovery-automate-mobility-service-install.md)。 設定設定伺服器，才能執行這項操作。
- **手動安裝**︰ 手動安裝元件，每個您想要複製的電腦上。 設定設定伺服器，才能執行這項操作。


#### <a name="prepare-for-automatic-push-on-windows-machines"></a>在 Windows 電腦上的自動推入準備

以下說明如何讓行動服務自動安裝程序 server 準備 Windows 的電腦。

1.  建立可用於程序伺服器存取電腦的帳戶。 帳戶應有系統管理員權限 （本機或的網域），並僅用於推入安裝。

    >[AZURE.NOTE] 如果您不使用的網域帳戶必須停用遠端使用者存取本機電腦上的控制項。 若要這麼做，在下 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System register 新增 DWORD 項目 LocalAccountTokenFilterPolicy 值為 1。 若要新增的登錄項目 CLI 類型**`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**。

2.  在 Windows 防火牆的電腦上您要保護，請選取 [**允許應用程式或功能通過防火牆**。 啟用**檔案及印表機共用**和**Windows 管理儀器**。 屬於網域的電腦中，您可以設定防火牆設定一個 gpo。

    ![防火牆設定](./media/site-recovery-vmware-to-azure/mobility1.png)

2. 新增您建立的帳戶︰

    - 開啟**cspsconfigtool**。 在 [安裝位置] \home\svsystems\bin 資料夾中是可做為快速鍵在桌面和位置。
    - 在 [**管理帳戶**] 索引標籤中，按一下 [**新增帳戶**。
    - 新增您建立的帳戶。 新增帳戶後必須提供的認證，當您啟用複寫機器。


#### <a name="prepare-for-automatic-push-on-linux-servers"></a>準備 Linux 伺服器上的自動推入

1.  請確定您想要保護的 Linux 電腦功能受支援，[受保護的電腦的先決條件](#protected-machine-prerequisites)所述。 請確定有網路 Linux 電腦與程序伺服器之間的連線。

2.  建立可用於程序伺服器存取電腦的帳戶。 帳戶應為根使用者來源 Linux 伺服器上的，只能用於推入安裝。

    - 開啟**cspsconfigtool**。 在 [安裝位置] \home\svsystems\bin 資料夾中是可做為快速鍵在桌面和位置。
    - 在 [**管理帳戶**] 索引標籤中，按一下 [**新增帳戶**。
    - 新增您建立的帳戶。 新增帳戶後必須提供的認證，當您啟用複寫機器。

3.  核取 /etc/hosts 伺服器上的檔案來源 Linux 包含將本機的主機名稱對應到所有的網路介面卡與相關聯的 IP 位址的項目。
4.  在您想要複製的電腦上安裝最新的 openssh openssh 伺服器、 openssl 套件。
5.  請確定 SSH 連接埠 22 上是啟用並執行。
6.  啟用 SFTP 子系統及密碼驗證 sshd_config 檔案中的，如下所示︰

    - 為根登入。
    - 在檔案 /etc/ssh/sshd_config 檔案中，尋找開頭**PasswordAuthentication**的線條。
    - 取消行註解，然後從**沒有**的值變更為 [**是]**。
    - 尋找開頭**子系統**的線條，然後取消行註解]。

        ![Linux](./media/site-recovery-vmware-to-azure/mobility2.png)


### <a name="install-the-mobility-service-manually"></a>手動安裝行動服務

安裝程式，可在**C:\Program Files (x86) \Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**設定伺服器上。

來源作業系統 | 行動服務安裝檔案
--- | ---
Windows 伺服器 （僅限 64 位元） | Microsoft ASR_UA_9。*.0.0_Windows_* release.exe
CentOS 6.4，6.5，6.6 （僅限 64 位元） | Microsoft ASR_UA_9。*.0.0_RHEL6-64_*release.tar.gz
SUSE Linux Enterprise Server 11 SP3 （僅限 64 位元） | Microsoft ASR_UA_9。*.0.0_SLES11-SP3-64_*release.tar.gz
Oracle 企業 Linux 6.4，6.5 （僅限 64 位元） | Microsoft ASR_UA_9。*.0.0_OL6-64_*release.tar.gz


#### <a name="install-mobility-service-on-a-windows-server"></a>在 Windows Server 上安裝行動服務


1. 下載並執行相關的安裝程式。
2. 在**您開始之前，**選取 [**行動服務**]。

    ![行動服務](./media/site-recovery-vmware-to-azure/mobility3.png)

3. **設定伺服器詳細資料**中指定設定伺服器，當您執行整合安裝程式時所產生的複雜密碼的 IP 位址。 您可以藉由執行擷取複雜密碼︰ ** <SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe – v**設定伺服器上。

    ![行動服務](./media/site-recovery-vmware-to-azure/mobility6.png)

4. 在**安裝位置**保留的預設設定，請按 [**下一步**開始安裝。
5. **安裝過程**中監控安裝，然後如果出現提示，請重新啟動電腦。 安裝服務之後，可以需要約 15 分鐘，在入口網站更新的狀態。

#### <a name="install-mobility-service-on-a-windows-server-using-the-command-prompt"></a>使用命令提示字元中的 Windows 伺服器上安裝行動服務

1. 複製您想要保護的伺服器上的本機資料夾 (假設 c:\temp.) 安裝程式。 在**[安裝位置] \home\svsystems\pushinstallsvc\repository**設定伺服器上找安裝程式。 封裝的 Windows 作業系統會有一個 Microsoft ASR_UA_9.3.0.0_Windows_GA_17thAug2016_release.exe 類似的名稱
2. **重新命名**MobilitySvcInstaller.exe 此檔案
3. 執行下列命令以擷取 MSI 安裝程式 </br>

        C:\> cd C:\Temp
        C:\Temp> MobilitySvcInstaller.exe /q /xC:\Temp\Extracted
        C:\Temp> cd Extracted
        C:\Temp\Extracted> UnifiedAgent.exe /Role "Agent" /CSEndpoint "IP Address of Configuration Server" /PassphraseFilePath <Full path to the passphrase file>

#####<a name="full-command-line-syntax"></a>完整命令列的語法

    UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Installation Directory>] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>] [/LogFilePath <Log File Path>]<br/>

**參數**

- **/Role:**強制性。 指定是否要安裝行動服務。 輸入值代理程式 |MasterTarget
- **/InstallLocation:**強制性。 指定安裝該服務的位置。
- **/PassphraseFilePath:**強制性。 設定伺服器複雜密碼。
- **/LogFilePath:**強制性。 安裝記錄檔應該會建立的位置。



#### <a name="uninstall-mobility-service-manually"></a>手動解除安裝行動服務

使用 [新增移除程式從 [控制台]，或使用命令列，可以解除安裝行動服務。

若要解除安裝行動服務命令列] 命令

    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1}


#### <a name="install-mobility-service-on-a-linux-server-using-command-line"></a>使用命令列 Linux 伺服器上安裝行動服務

1. 複製到您想要複製 Linux 電腦上方資料表為基礎的適當 tar 封存。
2. 開啟命令介面程式，並執行擷取壓縮的 tar 封存的本機路徑︰`tar -xvzf Microsoft-ASR_UA_8.5.0.0*`
3. Passphrase.txt 檔案的目錄中建立本機解壓縮 tar 封存的內容。 若要執行此複製複雜密碼 C:\ProgramData\Microsoft Azure 網站 Recovery\private\connection.passphrase 設定在伺服器上，並將其儲存 passphrase.txt 中執行*`echo <passphrase> >passphrase.txt`*於命令介面。
4. 執行安裝行動服務*`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*。
5. 指定設定伺服器的內部 IP 位址，並確定已選取 [連接埠 443。 安裝服務之後，可以需要約 15 分鐘，在入口網站更新的狀態。

**您也可以從命令列安裝**︰

1. 複製設定伺服器上的 C:\Program Files (x86) \InMage Systems\private\connection 複雜密碼，然後將其儲存為 「 passphrase.txt 」 中設定伺服器上。 然後，請執行這些命令。 在此範例中的設定伺服器 IP 位址 104.40.75.37 並 HTTPS 連接埠應該 443:

若要在實際執行伺服器上安裝︰

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

在 [主要目標伺服器上安裝︰


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt


### <a name="enable-replication"></a>啟用複寫

#### <a name="before-you-start"></a>在您開始之前

如果您複製 VMware 虛擬機器請注意下列事項︰

- VMware Vm 發現每 15 分鐘，可能需要 15 分鐘或更長的時間，才會顯示在入口網站後探索。 同樣地探索可能需要 15 分鐘以上當新增新的 vCenter 伺服器或 vSphere 主機。
- 15 分鐘或更多] 以更新在入口網站，也可能需要環境 （例如 VMware 工具安裝） 虛擬機器上做的變更。
- 您可以檢查發現上次 VMware Vm**最後一個連絡人在**欄位中的 [**設定伺服器**刀 vCenter 伺服器/vSphere 主機。
- 若要新增不會排程探索等候複寫的電腦，醒目提示設定伺服器 （但不要按一下），按一下 [**重新整理**] 按鈕。
- 當您啟用複寫，如果電腦自動準備程序伺服器上安裝行動服務。

#### <a name="exclude-disks-from-replication"></a>從複寫排除磁碟

當您啟用複寫時，依預設在電腦上的所有磁碟都複寫。 您可以從複寫排除磁碟。 例如您可能不想複製具有磁碟暫存的資料或具有每次重新整理一台電腦的資料，或應用程式重新啟動 （例如 pagefile.sys 或 SQL Server tempdb）。 如果您想要排除的磁碟注意︰

- 您只可以排除的磁碟已經安裝行動服務。 您需要[手動安裝行動服務](#install-the-mobility-service-manually)因為之後已啟用複寫使用推入機制只安裝行動服務。
- 只有基本磁碟可以排除複寫。 您無法排除 OS 或動態磁碟。
- 啟動複寫之後無法新增或移除複寫的磁碟。 如果您想要新增或排除的磁碟，必須先停用保護電腦，然後重新開啟它。
- 如果您要排除具有運作的應用程式所需的磁碟，Azure 失敗之後必須建立手動 Azure 中，可以執行複寫應用程式。 或者您可以整合 Azure 自動化成復原計劃在電腦上的容錯移轉建立磁碟。
- Azure 中手動建立的磁碟會回失敗。 例如，如果您無法超過三個磁碟，建立兩個直接在 Azure 中時，所有五個會回失敗 」。 您無法排除從回復手動建立的磁碟。

**立即重新啟用複寫，如下所示**︰

1. 按一下 [**步驟 2︰ 複製應用程式** > **來源**。 啟用複寫第一次後按一下**+ 複寫**中啟用其他電腦的複製保存庫。
2. 在**來源**刀 >**來源**選取 [設定伺服器。
3. 在**電腦類型**選取**虛擬機器**或**實體的電腦**。
4. 在**vCenter vSphere Hypervisor**選取 vCenter 伺服器管理 vSphere host （主機），或選取主應用程式。 此設定不相關如果您複製實體機器。
5. 選取的程序伺服器。 如果您還沒有建立任何其他處理程序伺服器這將可設定伺服器的名稱。 然後按一下**[確定]**。

    ![啟用複寫](./media/site-recovery-vmware-to-azure/enable-replication2.png)

6. 在**目標**選取保存庫訂閱，並在**文章容錯部署模型**選取您想要使用 Azure 中後移轉後的模型 （傳統或資源管理）。
7. 選取您要用於複製資料的 Azure 儲存體帳戶。 請注意︰

    - 您可以選取的進階版或標準儲存帳戶。 如果您選取您要指定進行中的複寫記錄其他標準儲存帳戶的進階版帳戶。 帳戶必須在同一個地區為修復服務保存庫。
    - 如果您想要使用不同的儲存的帳戶可能會讓您可以[建立一個](#set-up-an-azure-storage-account)。 若要建立儲存體帳戶使用 ARM 模型按一下 [**建立新**。 如果您想要建立使用傳統模式儲存帳戶必須執行該[Azure 入口網站中](../storage/storage-create-storage-account-classic-portal.md)。

8. 選取 [Azure 網路和子網路時它們開始後移轉後，Azure Vm 會將連線。 網路必須在同一個地區為修復服務保存庫。 選取 [**目前的選取電腦的設定**]，以將網路設定套用至所有電腦您選取 [保護]。 選取 [**更新版本設定**]，以選取 Azure 網路每部電腦]。 如果您沒有網路必須[建立一個](#set-up-an-azure-network)。 若要建立使用 ARM 模型網路按一下 [**建立新**。如果您想要建立使用傳統模式網路必須執行該[Azure 入口網站中](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)。 如果有的話，請選取 [子網路]。 然後按一下**[確定]**。

    ![啟用複寫](./media/site-recovery-vmware-to-azure/enable-replication3.png)

9. **虛擬機器**中 > **選取虛擬機器**按一下，然後選取您想要複製的每一部電腦。 您可以只選取可以啟用複寫的電腦。 然後按一下**[確定]**。

    ![啟用複寫](./media/site-recovery-vmware-to-azure/enable-replication5.png)

10. 在 [**內容** > **設定屬性**] 下，選取會自動使用所要的程序伺服器的帳戶安裝行動服務在電腦上。 依預設會複製所有磁碟。 按一下 [**所有磁碟**，然後清除您不想要複寫任何的磁碟。 然後按一下**[確定]**。 您可以稍後設定其他屬性。

    ![啟用複寫](./media/site-recovery-vmware-to-azure/enable-replication6.png)

11. **複製設定**中 > **複寫設定**可讓您確認已選取正確的複寫原則。 您可以修改複寫原則設定中**設定** > **複寫原則**> 原則名稱 >**編輯設定**。 您套用原則的變更會套用至複製和新的電腦。

12. 如果您想要收集機器複寫] 群組中，將資料，並指定群組的名稱，請啟用**多重 VM 一致性**。 然後按一下**[確定]**。 請注意︰

    - 在複寫機器分成複寫，當他們無法透過共用當機一致和應用程式一致的復原點。
    - 我們建議您，您收集 Vm 和實體伺服器放在一起，讓他們反映您的工作量。 啟用多 VM 一致性會影響工作負載的效能，而只會機器執行相同的工作量，且您需要的一致性。

    ![啟用複寫](./media/site-recovery-vmware-to-azure/enable-replication7.png)

13. 按一下 [**啟用複寫**。 您可以追蹤進度**設定**中**啟用保護**工作的 > **工作** > **網站復原工作**。 **完成保護**工作執行電腦之後，就可以容錯移轉。

> [AZURE.NOTE] 如果電腦已啟用保護時，將會安裝行動服務元件的推入安裝的準備。 元件之後被安裝在電腦保護工作開始與失敗。 失敗之後，您需要手動重新啟動每一部電腦。 重新啟動之後保護工作開始一次，並會初始複寫。

### <a name="view-and-manage-vm-properties"></a>檢視及管理 VM 屬性

我們建議您確認來源電腦的內容。 請記住 Azure VM 名稱應符合[Azure 虛擬機器](site-recovery-best-practices.md#azure-virtual-machine-requirements)需求。

1. 按一下 [**設定** > **複寫項目**> 並選取電腦。 **基本版**刀顯示機器設定與狀態的相關資訊。

2. 在 [**內容**中，您可以檢視 vm 複寫和容錯移轉資訊。

    ![啟用複寫](./media/site-recovery-vmware-to-azure/test-failover2.png)

3. 在**計算和網路** > **計算屬性**您可以指定 Azure 虛擬記憶體名稱與目標的大小。 修改以符合 Azure 的需求，如果您要的名稱。
您也可以檢視，並新增目標網路的子網路，與將 Azure vm 指定的 IP 位址的相關資訊。 請注意下列事項︰

    - 您可以設定的目標 IP 位址。 如果您沒有在電腦上提供的地址，失敗會使用 DHCP。 如果您將無法在錯誤後移轉的地址，就無法運作的容錯移轉。 相同的目標 IP 位址可用於測試容錯移轉如果地址被用於測試容錯移轉網路。
    - 您指定為目標虛擬機器，，如下所示的大小取決於網路介面卡數︰

        - 如果來源電腦上的網路介面卡的數字小於或等於的可容許的目標電腦大小的介面卡的數字，然後目標會有數相同的介面卡做為來源。
        - 如果來源虛擬機器介面卡數目超過允許的目標大小然後目標大小的最大值將用於的數字。
        - 例如，如果來源電腦有兩個網路介面卡，目標電腦大小支援四個目標電腦會有兩個介面卡。 如果來源電腦有兩個介面卡，但支援的目標大小僅支援其中一個目標電腦會有只有一個介面卡。     
    - 如果 VM 有多個網路介面卡，將所有連線到相同的網路。

    ![啟用複寫](./media/site-recovery-vmware-to-azure/test-failover4.png)

4. 在 [**磁碟**就可以看見的作業系統和資料磁碟 VM 複寫。


## <a name="step-7-test-the-deployment"></a>步驟 7︰ 測試部署

若要測試部署中，您可以執行測試容錯移轉單一虛擬機器或包含一或多個虛擬機器復原計劃。


### <a name="prepare-for-failover"></a>準備容錯移轉

- 若要執行測試容錯移轉我們建議您從 Azure 生產網路 （Azure 中建立新的網路時，這是預設行為），建立新的 Azure 網路，具有隔離。 [瞭解更多](site-recovery-failover.md#run-a-test-failover)有關執行測試容錯移轉。
- 為獲得最佳效能，您無法透過 Azure，請在 [受保護的電腦上安裝 Azure 代理程式。 它可讓您更快速開機，並可協助進行疑難排解。 安裝[Linux](https://github.com/Azure/WALinuxAgent)或[Windows](http://go.microsoft.com/fwlink/?LinkID=394789)代理程式。
- 若要完全測試您的部署，您需要基礎結構，以複製電腦如預期般運作。 如果您想要測試 Active Directory 和 DNS，可以建立 dns 網域控制站的虛擬機器並將此複寫到 Azure 使用 Azure 網站復原。 閱讀更多在[Active Directory 測試容錯移轉考量](site-recovery-active-directory.md#considerations-for-test-failover)。
- 請確定您正在設定伺服器。 否則容錯移轉將會失敗。
- 如果您已從複寫排除磁碟您可能需要這些磁碟中手動建立 Azure 後移轉後，讓應用程式的執行如預期般。
- 如果您想要執行，而不是測試容錯移轉意外的容錯移轉請注意下列動作︰

    - 如果可以的話您應該關閉主要電腦才能執行意外的容錯移轉。 如此一來，可確保您沒有安裝來源與複本的電腦執行一次。 如果您複製 VMware Vm 您可以指定網站復原，應該都讓關閉來源機器最佳效果。 根據主站台的狀態，這可能或可能無法運作。 如果您複製實體伺服器網站復原不提供此選項。
    - 當您執行意外的容錯移轉時，開始意外的容錯移轉之後，就不會傳送任何資料 delta 會停止資料複寫從主要的電腦。 此外如果您執行復原計劃意外的容錯移轉之前會執行完成，即使發生錯誤。

### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>連線至 Azure Vm 後移轉後的準備

如果您要連線至 Azure Vm 使用 RDP 後移轉後，請確定您執行下列動作︰

**容錯移轉之前的內部部署電腦上**︰

- 在網際網路上存取啟用 RDP，確保 TCP 和 UDP 規則會新增為**公開**，並確保 RDP 可在**Windows 防火牆** -> **允許應用程式和功能**的所有設定檔。
- 存取網站-連線啟用 RDP 在電腦上，並確保 RDP 是在**Windows 防火牆** -> **允許應用程式和功能**的**網域**與**私人**網路。
- 在內部部署電腦上安裝[Azure VM 代理程式](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。
- [手動安裝行動服務](#install-the-mobility-service-manually)在電腦上，而不是使用程序伺服器自動推入服務。 這是因為電腦啟用複寫後，只會發生的推入安裝。
- 確定作業系統的舊原則會設定為 OnlineAll。 [深入瞭解]( https://support.microsoft.com/kb/3031135)
- 執行容錯移轉前，請關閉 IPSec 服務。

**在 Azure VM 後移轉後**︰

- 加入公用端點 RDP 通訊協定 （連接埠 3389），並指定登入認證。
- 請確定您沒有任何網域原則，無法連線至虛擬機器使用公用的地址。
- 請嘗試連線。 如果您無法連線確認 VM 正在執行。 如需其他疑難排解秘訣請閱讀[本文](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)。


如果您想要存取後使用安全命令介面的用戶端 (ssh) 的移轉後執行 Linux Azure VM，請執行下列動作︰

**容錯移轉之前的內部部署電腦上**︰

- 請確定 Azure VM 上的安全命令介面服務會設定為自動啟動，在系統開機。
- 核取防火牆規則，讓它的 SSH 連線。

**在 Azure VM 後移轉後**︰

- 網路安全性] 群組上的規則失敗 VM 和 Azure 子網路連線到的必須允許連入連線 SSH 連接埠。
- 若要允許 SSH 連接埠 （TCP 連接埠 22 預設） 上的連入連線，應該建立公用結束點。
- 如果 VM 存取透過 VPN 連線 （Express 路由或網站 VPN） 用戶端可以使用透過 SSH 直接連接 VM。

**在 Azure Windows/Linux VM 後移轉後**︰

如果您有網路安全性群組虛擬機器或所屬至電腦的子網路相關聯，請確定網路的安全性群組皆已允許 HTTP/HTTPS 輸出規則。 請確定的虛擬機器之網路的 DNS 快速超過無法正確設定。 還有哪些容錯移轉可能逾時錯誤-'PreFailoverWorkflow 工作 WaitForScriptExecutionTask 逾時 」。 若要了解的詳細資料，請參閱上的[監控和疑難排解指南](site-recovery-monitoring-and-troubleshooting.md#recovery)中的 [復原] 區段。

## <a name="run-a-test-failover"></a>執行測試容錯移轉

1. 在一部電腦，在 [**設定**失敗 > **複製項目**] 下，按一下 VM > **+ 測試容錯移轉**圖示。

    ![測試容錯移轉](./media/site-recovery-vmware-to-azure/test-failover1.png)

2. 透過復原計劃，在 [**設定**失敗 > **復原方案**，以滑鼠右鍵按一下方案 >**測試容錯移轉**。 若要建立復原計劃[依照這些指示進行](site-recovery-create-recovery-plans.md)。

3. 在**測試容錯移轉**選取 Azure 的 Azure Vm 將連線之後發生錯誤後移轉的網路。
4. 按一下**[確定]**以開始容錯移轉]。 您可以追蹤進度，即可開啟其內容，VM 或**測試容錯移轉**工作保存庫名稱 >**設定** > **工作** > **網站復原工作**。
5. 當容錯移轉達到**完成測試**狀態時，執行下列動作︰

    1. 檢視複本虛擬機器中的 Azure 入口網站。 驗證成功啟動虛擬機器。
    2. 如果您是從您的內部部署網路設定 access 的虛擬機器可以啟動遠端桌面連線到虛擬機器。
    3. 按一下 [**完成測試**] 來完成它。

        ![測試容錯移轉](./media/site-recovery-vmware-to-azure/test-failover6.png)


    4. 按一下 [**備忘稿**，錄製並儲存聯測試容錯移轉任何觀察值]。
    5. 按一下 [自動清理測試環境的 [**測試容錯移轉已完成**]。 這是後測試容錯移轉會顯示**完成**狀態。
    6.  此時會刪除任何項目或 Vm 測試容錯移轉時自動建立的網站復原。 無法刪除您建立的測試容錯移轉任何其他項目。

    > [AZURE.NOTE] 如果測試容錯移轉持續時間超過兩週才能完成它是由強制中完成。


6. 容錯移轉完成後，您也應該能夠看到複本 Azure 電腦會出現在 Azure 入口網站 >**虛擬機器**。 請確定 VM 適當的大小，其具有連接到適當的網路，且正在執行。
7. 如果您[準備好連線後移轉後的](#prepare-to-connect-to-azure-vms-after-failover)您可以連線至 Azure VM。

## <a name="monitor-your-deployment"></a>監控您的部署

以下是如何監視設定的設定、 狀態及狀況網站復原部署︰

1. 按一下 [保存庫名稱，即可存取**基本資訊**儀表板上。 在此儀表板，您可以網站復原工作、 複寫狀態、 修復計劃、 伺服器狀況和事件。  您可以自訂顯示的並排顯示和給您，包括其他網站復原和備份保存庫的狀態最有用的版面配置的基礎。<br>
![基本版](./media/site-recovery-vmware-to-azure/essentials.png)

2. [**健康狀況**] 方塊中，您可以監視網站伺服器 （VMM] 或 [設定伺服器） 所遇到的問題，然後在過去 24 小時網站復原引發的事件。
3. 您可以管理並監控複寫中**複製的項目**，**修復方案**，然後**網站復原工作**磚。 您可以向下切入**設定**中的工作 -> **工作** -> **網站復原工作**。


## <a name="deploy-additional-process-servers"></a>部署其他程序伺服器

如果您有擴充超過 200 來源機器或總計的每日變換率超過 2 TB 部署，您需要處理流量的其他處理程序伺服器。

核取[大小建議的程序伺服器](#size-recommendations-for-the-process-server)，然後依照這些指示設定程序伺服器。 設定伺服器後，您會移轉來源機器使用它。

### <a name="install-an-additional-process-server"></a>安裝的其他處理程序伺服器

1. 在 [**設定** > **網站復原伺服器**按一下 [設定伺服器 >**程序伺服器**。

    ![新增程序伺服器](./media/site-recovery-vmware-to-azure/migrate-ps1.png)

2. 在 [**伺服器類型**的 [**程序 server （內部部署）**。

    ![新增程序伺服器](./media/site-recovery-vmware-to-azure/migrate-ps2.png)

3. 下載網站修復整合安裝檔案，並執行安裝程序 server，並登錄保存庫。
4. 在**開始之前**選取**擴充部署新增額外的程序伺服器**。
5. 當您所做的相同方式完成精靈您[設定](#step-2-set-up-the-source-environment)的設定伺服器。

    ![新增程序伺服器](./media/site-recovery-vmware-to-azure/add-ps1.png)

6. **設定伺服器詳細資料**中指定設定伺服器，以及複雜密碼的 IP 位址。 若要取得執行複雜密碼**<SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe-n**設定伺服器上。

    ![新增程序伺服器](./media/site-recovery-vmware-to-azure/add-ps2.png)

### <a name="migrate-machines-to-use-the-new-process-server"></a>移轉機器使用新的程序伺服器

1. 在 [**設定** > **網站復原伺服器**按一下 [設定伺服器]，然後再展開 [**處理程序伺服器**。

    ![更新程序伺服器](./media/site-recovery-vmware-to-azure/migrate-ps2.png)

2. 以滑鼠右鍵按一下目前使用中的程序伺服器，然後按一下 [**切換**。

    ![更新程序伺服器](./media/site-recovery-vmware-to-azure/migrate-ps3.png)

3. 在**選取目標程序伺服器**，請選取您想要使用]，然後選取 [新的程序伺服器會處理虛擬機器新的程序伺服器。 按一下 [資訊] 圖示，以取得伺服器的相關資訊。 若要協助您進行載入的決策，會顯示具有所需的每個所選的虛擬機器複製到新的程序伺服器的平均空間。 按一下 [核取記號，以開始複製到新的程序伺服器]。

## <a name="vmware-account-permissions"></a>VMware 帳戶權限

程序伺服器才能自動探索 Vm vCenter 伺服器上。 若要執行自動探索必須以[定義角色 (Azure_Site_Recovery)](#prepare-an-account-for-automatic-discovery)以允許網站復原存取 VMware 伺服器。 請注意，如果您只需要將 VMware 機器移轉到 Azure 並不需要從 Azure 回復，您可以定義足夠的唯讀角色。 下表摘要列出的所需之角色的權限。

**角色** | **詳細資料** | **權限**
--- | --- | ---
Azure_Site_Recovery 角色 | VMware VM 探索 |指派 v 中心伺服器這些權的限︰<br/><br/>資料存放區]-> [配置空間、 瀏覽資料存放區、 低層級檔案作業。、 移除檔案、 更新虛擬機器檔案<br/><br/>網路]-> [網路指派<br/><br/>資源]-> [資源資料庫，可關閉虛擬機器中的移轉，將可虛擬機器上指派虛擬機器<br/><br/>工作]-> [建立工作，更新工作<br/><br/>虛擬機器]-> [設定<br/><br/>虛擬機器]-> [互動]-> [回應問題裝置連線。、 設定 CD 媒體、 設定磁碟媒體]，關閉 Power 電力上、 VMware 工具安裝<br/><br/>虛擬機器]-> [清單]-> [建立]，[Register 取消註冊<br/><br/>虛擬機器]-> [提供]-> [儲存格內允許虛擬機器下載，允許虛擬機器檔案上傳<br/><br/>虛擬機器]-> [快照]-> [移除快照集
vCenter 使用者角色 | VMware VM 探索/容錯移轉不關閉來源 VM | 指派 v 中心伺服器這些權的限︰<br/><br/>資料中心物件 –> 傳播至子物件角色 = 唯讀狀態 <br/><br/>使用者指派資料中心層級，因此所在的資料中心的 [存取所有的物件。  如果您想要限制存取，**沒有存取**角色指派**傳播至子系**物件的子物件 （vSphere 主機、 datastores、 Vm 和網路）。
vCenter 使用者角色 | 錯誤移轉及回復 | 指派 v 中心伺服器這些權的限︰<br/><br/>資料中心物件 – 傳播至子物件角色 = Azure_Site_Recovery<br/><br/>使用者指派資料中心層級，因此所在的資料中心的 [存取所有的物件。  如果您想要限制存取，**沒有存取**角色指派與**傳播至子物件**子物件 （vSphere 主機、 datastores、 Vm 和網路）。  
## <a name="next-steps"></a>後續步驟

- [瞭解更多](site-recovery-failover.md)有關不同類型的容錯移轉。
- [深入瞭解回復](site-recovery-failback-azure-to-vmware.md)Azure 中執行的機器上顯示您失敗回到您的內部部署環境。

## <a name="third-party-software-notices-and-information"></a>第三方軟體聲明與資訊

請不要翻譯或當地語系化

軟體和韌體執行中的 Microsoft 產品或服務為基礎，或整合下方所列的專案中的資料 （共同，「 協力廠商程式碼 」）。  Microsoft 是不原始作者的協力廠商程式碼。  原始的著作權聲明及授權，在 Microsoft 收到這類協力廠商程式碼中，設定左起第四個下方。

在 A] 區段中的資訊關於從下方所列的專案的協力廠商程式碼元件。 這類的資訊和授權可供參考。  此協力廠商程式碼會被 relicensed 您 microsoft 下 Microsoft 產品或服務的 Microsoft 軟體授權條款。  

區段 B 中的資訊關於協力廠商程式碼元件，會在您就可以使用 microsoft 原始授權條款。

可以在[Microsoft 下載中心](http://go.microsoft.com/fwlink/?LinkId=529428)找到完整的檔案。 Microsoft 保留不明文授予之，所有的權限，也就是說，是否 estoppel 或。
