<properties
    pageTitle="網站復原如何運作？ |Microsoft Azure"
    description="本文提供網站復原架構的概觀"
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
    ms.topic="get-started-article"
    ms.date="10/05/2016"
    ms.author="raynew"/>

# <a name="how-does-azure-site-recovery-work"></a>Azure 網站復原如何運作？

閱讀本文以瞭解 Azure 網站復原服務的基礎架構，讓它的元件運作。 

在底端的本文中，或在[Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上張貼任何註解或問題。


## <a name="overview"></a>概觀

組織必須決定如何應用程式、 負載，以及資料保持執行及使用期間計劃和計畫停機時間和越快越復原正常運作狀況 BCDR 策略。 您 BCDR 策略應該安全及修復，保留商務資料，並確保當發生損毀時，仍然持續可用的工作量。 

網站復原是 Azure 服務可提供您 BCDR 策略來協調實體伺服器的內部部署和雲端 (Azure) 或次要資料中心的虛擬機器複寫。 中斷問題發生時在您的主要位置，您無法透過，讓應用程式可用的工作負載的第二個位置。 會傳回標準作業時，您會回到您的主要位置失敗。 如需深入瞭解[什麼是網站復原？](site-recovery-overview.md)

## <a name="site-recovery-in-the-azure-portal"></a>Azure 入口網站中的網站復原

Azure 有兩種不同的[部署模型](../resource-manager-deployment-model.md)建立及使用的資源︰ Azure 資源管理員模型與傳統的服務管理模型。 Azure 也會有兩個入口網站 – 支援傳統部署模型， [Azure 傳統入口網站](https://manage.windowsazure.com/)和[Azure 入口網站](https://portal.azure.com)的兩種部署模型支援。

在 [傳統] 入口網站和 Azure 入口網站中使用網站復原。 Azure 傳統入口網站中您可以使用傳統的服務管理模型支援網站復原。 Azure 入口網站中，您可以支援傳統的模型或資源模型部署。 [閱讀更多](site-recovery-overview.md#site-recovery-in-the-azure-portal)部署 Azure 入口網站的相關資訊。

傳統和 Azure 入口網站的部署適用於本文中的資訊。 適用之處，則會註明的差異。

## <a name="deployment-scenarios"></a>部署案例

協調複寫案例的數字，可以部署網站修復︰

- **複製 VMware 虛擬機器**︰ Azure 或次要資料中心，您可以複製內部部署 VMware 虛擬機器。
- - **複寫的實體機器**︰ 您可以複製執行 Windows 或 Linux Azure 或次要資料中心的實體機器。 複寫的實體機器的程序是幾乎一樣複寫 VMware Vm 的程序
- **複製 （不含 VMM) 的 HYPER-V Vm**︰ 您可以複製不受 VMM Azure 的 HYPER-V Vm。
- **在系統管理中心 VMM 雲朵的複寫 HYPER-V Vm 管理**︰ 您可以複製內部部署 HYPER-V 虛擬機器 Azure 或次要資料中心，VMM 雲朵 HYPER-V 主機伺服器上執行。 您可以複製使用標準 HYPER-V 複本，或使用舊複寫。
- **移轉 Vm**︰ 您可以使用網站[移轉 Azure IaaS Vm](site-recovery-migrate-azure-to-azure.md)之間的區域，或要[移轉 AWS Windows 執行個體](site-recovery-migrate-aws-to-azure.md)復原至 Azure IaaS Vm。 目前只移轉支援這表示您可以透過下列 Vm 失敗，但您無法回復它們。

網站復原可以複寫這些 Vm 與實體伺服器上執行大部分的應用程式。 您可以取得完整的摘要中支援的應用程式的[哪些負載可以 Azure 網站復原保護？](site-recovery-workload.md)


## <a name="replicate-to-azure-vmware-virtual-machines-or-physical-windowslinux-servers"></a>複製至 Azure: VMware 虛擬機器或實體的 Windows/Linux 伺服器

有數種方式複寫 VMware Vm 網站復原。

- **使用 [Azure 入口網站**-當您部署網站復原傳統服務管理員存放裝置或給資源管理員，您可以容錯移轉 Vm Azure 入口網站中。 複製 VMware Vm Azure 入口網站中將數字的優點，包括複製到 [傳統] 或 [資源管理員存放 Azure 中的功能。 [進一步瞭解](site-recovery-vmware-to-azure.md)。
- **使用 [傳統] 入口網站**-您可以使用增強的體驗傳統入口網站中部署網站復原。 這適用於所有新的部署，在 [傳統] 入口網站中。 在此部署您可以只容錯 Vm 傳統 Azure 中的儲存空間，而不要到資源管理員儲存空間。 [進一步瞭解](site-recovery-vmware-to-azure-classic.md)。 還有設定 VMware 複寫傳統入口網站中的[舊版的體驗](site-recovery-vmware-to-azure-classic-legacy.md)。 這不應使用新的部署。  如果您已經已部署使用舊體驗[瞭解移轉](site-recovery-vmware-to-azure-classic-legacy.md#migrate-to-the-enhanced-deployment)至增強型部署。



部署網站復原複寫 VMware Vm/實體伺服器 Azure 入口網站或 Azure 傳統入口網站 （增強） 中的架構需求類似，有幾項差異︰

- 如果您部署在您可以複製到 [資源管理員為基礎的儲存空間，並用於資源管理員網路連線後移轉後的 Azure Vm Azure 入口網站。
- 當 Azure 入口網站中部署兩個 LRS 和支援 GRS 儲存空間。 在 [傳統] 入口網站中 GRS 資格。
- 部署程序是簡體與 Azure 入口網站中更容易使用。


以下是您需要︰

- **Azure 帳戶**︰ 您必須以 Microsoft Azure 帳戶。
- **Azure 儲存空間**︰ 您必須 Azure 儲存體帳戶儲存複製的資料。 您可以使用傳統的帳戶或資源管理員儲存帳戶。 當您部署 Azure 入口網站中，LRS 或 GRS 就會帳戶。 複製的資料會儲存在 Azure 儲存空間，容錯移轉發生時 Azure Vm 會開始。 
- **Azure 網路**︰ 您必須建立在錯誤後移轉時，將會連線到的 Azure Vm Azure 虛擬網路。 Azure 入口網站中，則可在傳統的服務管理員模型或資源管理員模型中建立的網路。
- **內部部署設定伺服器**︰ 您必須設定伺服器和其他網站復原元件執行內部部署 Windows Server 2012 R2 電腦。 如果您複製 VMware Vm 應該是高度可用 VMware VM。 如果您想要複製實體伺服器電腦可以實體。 在電腦上，將會安裝這些網站復原元件︰
    - **設定伺服器**︰ 協調您內部部署環境和 Azure 之間的通訊和管理資料複製及復原。
    - **程序伺服器**︰ 做為複寫閘道器。 它從受保護的來源機器接收複寫資料、 將它調整快取、 壓縮，及加密，並將資料傳送至 Azure 儲存體。 也會處理推入安裝行動服務受保護的電腦，並執行 VMware Vm 的自動探索。 當您部署規模擴大時，您可以新增其他專用的另一個程序伺服器處理增加對於複寫流量。
    - **主要目標伺服器**︰ 在 [從 Azure 回復時處理複寫資料。 
- **VMware Vm 或複製的實體伺服器**︰ 每個您想要複製到 Azure 的電腦必須安裝行動服務元件。 這項服務擷取資料寫入在電腦上的，並將它們轉寄程序伺服器。 這個元件可以手動安裝或來推入和安裝自動程序伺服器當您啟用複寫機器。
- **vSPhere 主機/vCenter 伺服器**︰ 您必須執行 VMware Vm 的一或多個 vSphere host （主機） 伺服器。 我們建議您部署 vCenter 伺服器管理這些主機。
- **回復**︰ 以下是您需要︰
    - **不支援的實體-實體回復**︰ 這表示，如果您無法透過 Azure 實體伺服器，並要回失敗，您必須失敗回到 VMware VM。 您無法失敗回到實體伺服器。 您需要回復至 Azure VM，如果您沒有部署 VMware VM 設定伺服器您必須設定為 VMware VM 個別的主要目標伺服器。 此需要，因為主要目標伺服器間的互動，並將附加至 VMware vm 還原磁碟 VMware 儲存空間。
    - - **Azure 中的暫時程序伺服器**︰ 如果您想要從 Azure 失敗後移轉後，您必須設定設為程序伺服器，Azure VM 處理從 Azure 複寫。 回復完成之後，您可以刪除這個 VM。
    - **VPN 連線**︰ 回復您需要的 VPN 連線 （或 Azure ExpressRoute） 設定從 Azure 網路至內部部署網站。
    - **內部部署的另一個主要目標伺服器**︰ 內部部署的主要目標伺服器處理回復。 主要目標伺服器管理伺服器上的預設會安裝，但如果您失敗回較大對於流量您應該個別內部部署主要目標將伺服器設定為此用途。

**一般的結構**

![增強型](./media/site-recovery-components/arch-enhanced.png)

**部署元件**

![增強型](./media/site-recovery-components/arch-enhanced2.png)

**回復**

![增強型的回復](./media/site-recovery-components/enhanced-failback.png)


- [瞭解更多](site-recovery-vmware-to-azure.md#azure-prerequisites)有關 Azure 入口網站的部署的需求。
- [瞭解更多](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)有關傳統入口網站中的增強型的部署需求。
- [瞭解更多](site-recovery-failback-azure-to-vmware.md)有關回復 Auzre 入口網站中。
- [瞭解更多](site-recovery-failback-azure-to-vmware-clas- [Learn more](site-recovery-failback-azure-to-vmware-classic.md) about failback in the Auzre portal.sic.md)有關回復傳統入口網站中。

## <a name="replicate-to-azure-hyper-v-vms-not-managed-by-vmm"></a>複製至 Azure︰ 不受 VMM HYPER-V Vm

您可以複製 HYPER-V Vm 不由管理的系統管理中心 VMM Azure 網站復原，如下所示︰

- **使用 [Azure 入口網站**-當您部署網站復原或到資源管理員傳統的儲存空間，您可以容錯移轉 Vm Azure 入口網站中。 [進一步瞭解](site-recovery-hyper-v-site-to-azure.md)。
- **使用 [傳統] 入口網站**-您可以在 [傳統] 入口網站中部署網站復原。 在此部署您可以只容錯 Vm 傳統 Azure 中的儲存空間，而不要到資源管理員儲存空間。 [進一步瞭解](site-recovery-hyper-v-site-to-azure-classic.md)。

這兩種部署架構很類似，但︰

- 如果您部署在您可以複製到 [資源管理員儲存空間，並用於資源管理員網路連線後移轉後的 Azure Vm Azure 入口網站。
- 部署程序是簡體與 Azure 入口網站中更容易使用。

以下是您需要︰

- **Azure 帳戶**︰ 您必須以 Microsoft Azure 帳戶。
- **Azure 儲存空間**︰ 您必須 Azure 儲存體帳戶儲存複製的資料。 Azure 入口網站中，您可以使用傳統的帳戶或資源管理員儲存帳戶。 在 [傳統] 入口網站中，您可以使用僅會以傳統的帳戶。 複製的資料儲存在 Azure 儲存空間，發生錯誤後移轉時，系統會建立 Azure Vm。
- **Azure 網路**︰ 您必須在建立時即後移轉後，將會連線到的 Azure Vm Azure 網路。 
- **Hyper-v host （主機)**: 您必須一或多個 Windows Server 2012 R2 HYPER-V 主機伺服器。 在網站復原部署期間會安裝 Azure 網站修復提供者與 Microsoft Azure 修復服務代理程式 」 主機上。
- **HYPER-V Vm**︰ 您必須 HYPER-V 主機伺服器上的一個或多個 Vm。 Azure 網站修復提供者和 Azure 修復服務代理程式網站復原部署期間 HYPER-V 主機上。 協調提供者，並透過網際網路協調網站復原服務複寫。 透過 HTTPS 443 代理程式控點的資料複製資料。 從提供者] 和 [代理程式的通訊內容的安全性與加密。 也加密複寫 Azure 儲存體中的資料。

**一般的結構**

![Azure HYPER-V 網站](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)


- [瞭解更多](site-recovery-hyper-v-site-to-azure.md#azure-prerequisites)有關 Azure 入口網站的部署的需求。
- [瞭解更多](site-recovery-hyper-v-site-to-azure-classic.md#azure-prerequisites)有關傳統入口網站的部署的需求。



## <a name="replicate-to-azure-hyper-v-vms-managed-by-vmm"></a>複製至 Azure: HYPER-V Vm 由 VMM 管理

您可以將 HYPER-V Vm 中 VMM 雲朵複寫到網站復原 Azure，如下所示︰

- **使用 [Azure 入口網站**-當您部署網站復原或到資源管理員傳統的儲存空間，您可以容錯移轉 Vm Azure 入口網站中。 [進一步瞭解](site-recovery-vmm-to-azure.md)。
- **使用 [傳統] 入口網站**-您可以在 [傳統] 入口網站中部署網站復原。 在此部署您可以只容錯 Vm 傳統 Azure 中的儲存空間，而不要到資源管理員儲存空間。 [進一步瞭解](site-recovery-vmm-to-azure-classic.md)。

這兩種部署架構很類似，但︰

- 如果您部署在您可以複製到 [資源管理員為基礎的儲存空間，並用於資源管理員網路連線後移轉後的 Azure Vm Azure 入口網站。
- 部署程序是簡體與 Azure 入口網站中更容易使用。


以下是您需要︰

- **Azure 帳戶**︰ 您必須以 Microsoft Azure 帳戶。
- **Azure 儲存空間**︰ 您必須 Azure 儲存體帳戶儲存複製的資料。 Azure 入口網站中，您可以使用傳統的帳戶或資源管理員儲存帳戶。 在 [傳統] 入口網站中，您可以使用僅會以傳統的帳戶。 複製的資料儲存在 Azure 儲存空間，發生錯誤後移轉時，系統會建立 Azure Vm。
- **Azure 網路**︰ 您必須設定網路對應，以便在建立時即後移轉後 Azure Vm 連線到適當的網路。 
- **VMM 伺服器**︰ 您會需要在系統管理中心 2012 R2 上執行的一或多個內部部署 VMM 伺服器，設定一或多個私人雲朵。 如果您部署 Azure 中入口網站必須邏輯和 VM 網路設定，讓您可以設定網路對應。 在 [傳統] 入口網站中此為選用步驟。  VM 網路應該邏輯網路雲端與相關聯的連結。
- **Hyper-v host （主機)**: 您必須 VMM 雲端中的一或多個 Windows Server 2012 R2 HYPER-V 主機伺服器。
- **HYPER-V Vm**︰ 您必須 HYPER-V 主機伺服器上的一個或多個 Vm。

**一般的結構**

![Azure VMM](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)

- [瞭解更多](site-recovery-vmm-to-azure.md#azure-requirements)有關 Azure 入口網站的部署的需求。
- [瞭解更多](site-recovery-vmm-to-azure-classic.md#before-you-start)有關傳統入口網站的部署的需求。




## <a name="replicate-to-a-secondary-site-vmware-virtual-machines-or-physical-servers"></a>複製至次要網站︰ VMware 虛擬機器或實體伺服器 

複製下載 InMage 搜索的 Azure 網站復原訂閱所含的次要網站 VMware Vm 或實體的伺服器。 從 Azure 入口網站，或從 Azure 傳統入口網站可以下載它。 

您設定 （設定程序、 主要目標），每個網站的元件伺服器，並在您想要複製的電腦上安裝整合代理程式。 初始複寫之後上每一部電腦的代理程式會傳送至程序伺服器 delta 複寫的變更。 程序伺服器最佳化資料，並將其移到主目標伺服器中的次要網站上。 設定伺服器管理複寫程序。

以下是您需要︰

**Azure 帳戶**︰ 您部署使用 InMage 搜索這種情況。 若要取得它，您需要 Azure 的訂閱。 建立網站復原保存庫之後您下載 InMage 搜索，並安裝最新的更新，可以部署設定。
**程序伺服器 （主要網站）**︰ 設定程序伺服器元件主要網站中處理快取、 壓縮和資料最佳化。 也會處理推入安裝在電腦上執行您想要保護的整合代理程式。 
**VMware ESX/ESXi 和 vCenter server （主要網站）**︰ 如果您保護 VMware Vm 必須 VMware EXS/ESXi hypervisor 和 （選擇性） 若要管理 hypervisors VMware vCenter 伺服器。
- **Vm/實體伺服器 （主要網站）**︰ 您想要保護會的 VMware Vm 或 Windows/Linux 實體的伺服器需要安裝整合代理程式。 整合代理程式也會在做為主要目標伺服器電腦上安裝。 代理程式做為通訊提供者之間的所有元件。 
- - **設定伺服器 （次要網站）**︰ 設定伺服器是安裝時，第一個元件和次要網站管理]，設定，並監控您的部署，請使用 [管理網站或 vContinuum 主控台上安裝。 部署中有一個設定伺服器，您必須在執行 Windows Server 2012 R2 電腦上安裝。
- **vContinuum 伺服器 （次要網站）**︰ 安裝在同一個位置 （次要網站） 中設定伺服器。 管理及監視您受保護的環境提供主控台。 在預設安裝 vContinuum 伺服器的第一個主版目標伺服器，而且已經安裝整合代理程式。
- **主要目標伺服器 （次要網站）**︰ 主要目標伺服器保留複製的資料。 它從程序伺服器接收資料、 建立複本電腦中的次要網站，並保留資料保留點。 您需要的主要目標伺服器的數目取決於您要保護的電腦數目。 如果您想要回復至主站台必須在主要目標伺服器太。 

**一般的結構**

![若要 VMware VMware](./media/site-recovery-components/vmware-to-vmware.png)


## <a name="replicate-to-a-secondary-site-hyper-v-vms-managed-by-vmm"></a>複製至次要網站︰ 由 VMM 管理 HYPER-V Vm


您可以複製 HYPER-V Vm 所管理的系統管理中心 VMM 到網站復原的次要資料中心，如下所示︰

- **使用 [Azure 入口網站**-當您部署網站復原 Azure 入口網站中。 [進一步瞭解](site-recovery-hyper-v-site-to-azure.md)。
- **使用 [傳統] 入口網站**-您可以在 [傳統] 入口網站中部署網站復原。 [進一步瞭解](site-recovery-hyper-v-site-to-azure-classic.md)。

這兩種部署架構很類似，但︰

- 如果您部署在您必須設定網路對應 Azure 入口網站。 此為選用步驟在 [傳統] 入口網站中。
- 部署程序是簡體與 Azure 入口網站中更容易使用。
- - 如果您部署 Azure 中會傳統入口網站[儲存對應](site-recovery-storage-mapping.md)。

以下是您需要︰

- **Azure 帳戶**︰ 您必須以 Microsoft Azure 帳戶。
- **VMM 伺服器**︰ 我們建議您在主要網站 VMM 伺服器並在次要網站，每個包含至少一個 VMM 私人雲端中的項目。 伺服器應該至少執行系統管理中心的最新的更新，2012 SP1，並連線到網際網路。 雲朵應有設定 HYPER-V 功能設定檔。 您會在 VMM 伺服器上安裝 Azure 網站修復提供者。 協調提供者，並透過網際網路協調網站復原服務複寫。 提供者與 Azure 之間的通訊內容的安全性與加密。
- **HYPER-V 伺服器**︰ HYPER-V 主機伺服器應該位於主要和次要 VMM 雲朵。 Host （主機） 伺服器應該至少執行最新的更新的 Windows Server 2012 安裝，而且連線至網際網路。 資料伺服器間複寫主要和次要 HYPER-V 主機 LAN 或要有 vpn 才能使用 Kerberos] 或 [憑證驗證。  
- **受保護的電腦**︰ 來源 HYPER-V 主機伺服器必須至少有一個您要保護的 VM。

**一般的結構**

![內部部署至內部部署](./media/site-recovery-components/arch-onprem-onprem.png)


- [瞭解更多](site-recovery-vmm-to-vmm.md#azure-prerequisites)有關在 Azure 入口網站的部署需求。
- - [瞭解更多](site-recovery-vmm-to-vmm-classic.md#before-you-start)有關在 Azure 傳統入口網站的部署需求。




## <a name="replicate-to-a-secondary-site-with-san-replication-hyper-v-vms-managed-by-vmm"></a>複製至舊複寫次要網站︰ 由 VMM 管理 HYPER-V Vm

您可以複製 HYPER-V Vm VMM 雲朵舊複寫使用 Azure 傳統入口網站的次要網站中管理。 新的 Azure 入口網站目前不支援這種情況。 

在這個案例中網站復原部署期間您會在 VMM 伺服器上安裝 Azure 網站修復提供者。 協調提供者，並透過網際網路協調網站復原服務複寫。 使用同步舊複寫主要和次要儲存陣列之間複製資料。

以下是您需要︰

**Azure 帳戶**︰ 您必須 Azure 的訂閱
- **舊陣列**︰[支援舊陣列](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx)受主要 VMM 伺服器。 舊共用次要網站中的另一個舊陣列網路基礎結構。
- **VMM 伺服器**︰ 我們建議您在主要網站 VMM 伺服器並在次要網站，每個包含至少一個 VMM 私人雲端中的項目。 伺服器應該至少執行系統管理中心的最新的更新，2012 SP1，並連線到網際網路。 雲朵應有設定 HYPER-V 功能設定檔。
- **HYPER-V 伺服器**︰ 位於主要和次要 VMM 雲朵 HYPER-V 主機伺服器。 Host （主機） 伺服器應該至少執行最新的更新的 Windows Server 2012 安裝，而且連線至網際網路。
- **受保護的電腦**︰ 來源 HYPER-V 主機伺服器必須至少有一個您要保護的 VM。

**舊複寫架構**

![舊複寫](./media/site-recovery-components/arch-onprem-onprem-san.png)

[瞭解更多](site-recovery-vmm-san.md#before-you-start)有關部署需求。
### <a name="on-premises"></a>內部部署



## <a name="hyper-v-protection-lifecycle"></a>HYPER-V 保護生命週期

此工作流程會顯示保護與複寫，透過 HYPER-V 虛擬機器失敗的程序。 

1. **啟用保護**︰ 設定網站復原保存庫、 設定 VMM 雲端或 HYPER-V 網站複寫設定和啟動 Vm 保護。 稱為 [**啟用保護**的工作啟動時，可以監視**工作**] 索引標籤中。 工作會檢查電腦符合先決條件，然後再叫用為您設定的設定 Azure 複寫的[CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx)方法。 [**啟用保護**的工作也叫用[StartReplication](https://msdn.microsoft.com/library/hh850303.aspx)方法初始化完整的 VM 複製。
2. **初始複寫**︰ 取得虛擬機器快照集且虛擬硬碟複製的一個接一個] 鍵，直到其所有複製 Azure 或次要資料中心。 若要完成所需的時間，取決於虛擬記憶體大小、 網路頻寬和初始複寫方法。 如果正在進行中的初始複寫時，會發生磁碟變更 HYPER-V 複本複寫追蹤器會追蹤這些變更為 HYPER-V 複寫記錄 (.hrl) 磁碟的相同資料夾中。 每個磁碟具有相關聯的.hrl 檔案，將會傳送至次要儲存空間。 請注意的快照和記錄檔在初始複寫正在進行中時，會使用磁碟資源。 完成初始複寫 VM 快照刪除並 delta 磁碟變更記錄檔中的同步處理合併。
3. **Finalize 保護**︰ 初始複寫完成**Finalize 保護**工作後設定網路和其他後複寫設定讓虛擬機器受到保護。 如果您複製至 Azure 您可能需要調整虛擬機器的設定，使其位於準備好進行容錯移轉。 此時，您可以執行測試容錯移轉檢查所有項目會如預期般運作。
4. **複寫**︰ 初始複寫之後 delta 同步處理開始，根據複寫設定的。 
    - **複寫失敗**︰ 如果差異複寫失敗，請完整的複寫就會大幅降低頻寬] 或 [時間]，然後重新同步處理，就會發生。 範例如果.hrl 檔案達到 50%的磁碟大小然後 VM 會被標示為 [重新同步處理。 重新同步化最小化計算總和檢查來源和目標的虛擬機器和傳送只 delta 所傳送的資料的量。 重新同步化完成後會繼續差異複寫。 根據預設，重新同步處理會排程外 office 小時，自動執行，但您可以手動同步處理虛擬機器。
    - **複寫錯誤**︰ 複寫發生錯誤時沒有內建重試。 如果無法修復的錯誤驗證或授權錯誤，例如或複本電腦位於不正確的狀態，將會嘗試沒有重試]。 如果是可修復的錯誤，例如網路錯誤或低磁碟空間/記憶體，然後重試發生增加重試之間的時間間隔 (1、 2、 4、 8、 10，然後每隔 30 分鐘)。
4. **預定/意外的錯誤後移轉**︰ 如有需要您可以執行計劃或意外的錯誤後移轉。 如果您執行的計劃的容錯移轉然後來源 Vm 關機]，以，確保遺失任何資料。 建立複本 Vm 之後它們被放狀態等待認可中。 您需要確認即可完成容錯移轉，除非您正在使用舊複寫在這種情況認可自動。 主要的網站和使用後會發生回復。 如果您已複製到 Azure 反向複寫會自動執行。 否則您開始進行反向複寫以手動方式。
 

![工作流程](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## <a name="next-steps"></a>後續步驟

[準備進行部署](site-recovery-best-practices.md)
