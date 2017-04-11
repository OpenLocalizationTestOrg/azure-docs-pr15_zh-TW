<properties
    pageTitle="Azure 網站復原 （傳統） 的 Azure 複寫 VMware 虛擬機器和實體伺服器 |Microsoft Azure" 
    description="說明如何將內部部署 Vm 複寫和 Azure Windows/Linux 實體伺服器使用 Azure 網站復原舊版部署在傳統入口網站中。" 
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
    ms.date="09/29/2016"
    ms.author="raynew"/>

# <a name="replicate-vmware-virtual-machines-and-physical-servers-to-azure-with-azure-site-recovery-using-the-classic-portal-legacy"></a>將 VMware 虛擬機器和實體伺服器複寫到 Azure Azure 網站復原使用傳統的入口網站 （舊版）

> [AZURE.SELECTOR]
- [Azure 入口網站](site-recovery-vmware-to-azure.md)
- [傳統的入口網站](site-recovery-vmware-to-azure-classic.md)
- [傳統入口網站 （舊版）](site-recovery-vmware-to-azure-classic-legacy.md)


歡迎使用 Azure 網站復原 ！ 本文將說明將內部部署 VMware 虛擬機器或 Windows/Linux 實體伺服器複寫到 Azure [傳統] 入口網站中使用 Azure 網站復原舊版部署。

## <a name="overview"></a>概觀

組織必須決定如何應用程式、 負載，以及資料保持執行及使用期間計劃和計畫停機時間和越快越復原正常運作狀況 BCDR 策略。 您 BCDR 策略應該安全及修復，保留商務資料，並確保當發生損毀時，仍然持續可用的工作量。

網站復原是 Azure 服務可提供您 BCDR 策略來協調實體伺服器的內部部署和雲端 (Azure) 或次要資料中心的虛擬機器複寫。 中斷問題發生時在您的主要位置，您無法透過，讓應用程式可用的工作負載的第二個位置。 會傳回標準作業時，您會回到您的主要位置失敗。 如需深入瞭解[什麼是 Azure 網站復原？](site-recovery-overview.md)


>[AZURE.WARNING] 本文包含**舊版的指示進行**。 不要使用新的部署。 不過，[請遵循以下指示](site-recovery-vmware-to-azure.md)Azure 入口網站，或[使用這些指示](site-recovery-vmware-to-azure-classic.md)設定增強型的部署在傳統入口網站中部署網站復原。 如果您已經已部署使用本文所述的方法，我們建議您移轉傳統入口網站中的增強型部署。


## <a name="migrate-to-the-enhanced-deployment"></a>移轉至增強型部署

本節內容才有已經已部署網站復原使用本文中的指示。

若要移轉您現有的部署，您需要︰

1. 部署在您的內部部署網站的新網站復原元件。
2. 設定認證的 VMware Vm 的自動探索伺服器上的新設定。
3. 探索 VMware 伺服器的新設定伺服器。
3. 建立新的 [保護] 群組的新設定伺服器。


開始之前︰

- 我們建議您設定移轉進行的維修作業] 視窗。
- 您有舊版的部署期間所建立的現有保護群組時，才可以使用時，才能**移轉機器**選項。
- 您已完成的移轉步驟後，可以需要 15 分鐘或更長的時間，重新整理認證，並可探索並重新整理虛擬機器，以便您可以將它們新增至 [保護] 群組。 您可以手動更新，而不是等候。 

移轉，如下所示︰

1. 閱讀有關[增強的部署，在 [傳統] 入口網站中](site-recovery-vmware-to-azure-classic.md#enhanced-deployment)。 檢閱增強型的[架構](site-recovery-vmware-to-azure-classic.md#scenario-architecture)，並[的先決條件](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)。
2. 從您要複製的電腦，解除安裝行動服務。 將電腦上安裝新版的服務，當您將其新增至新的 [保護] 群組。
3. 下載[保存庫登錄機碼](site-recovery-vmware-to-azure-classic.md#step-4-download-a-vault-registration-key)並安裝設定伺服器、 程序伺服器，以及主目標伺服器元件的 [[執行整合的安裝精靈](site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server)。 進一步瞭解[規劃容量](site-recovery-vmware-to-azure-classic.md#capacity-planning)。
4. [設定認證](site-recovery-vmware-to-azure-classic.md#step-6-set-up-credentials-for-the-vcenter-server)該網站復原可用來存取自動探索 VMware Vm VMware 伺服器。 瞭解[必要的權限](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access)。
5. 新增[vCenter 伺服器或 vSphere 主機](site-recovery-vmware-to-azure-classic.md#step-7-add-vcenter-servers-and-esxi-hosts)。 需要 15 分鐘，如需更多的伺服器會出現在 [網站復原入口網站。
6. 建立[新的 [保護] 群組](site-recovery-vmware-to-azure-classic.md#step-8-create-a-protection-group)。 可能需要重新整理虛擬機器發現，而顯示入口網站最 15 分鐘。 如果您不想要等到您可以醒目提示管理伺服器名稱 （但不要按一下） >**重新整理**。
7. 在 [新的 [保護] 群組底下，按一下 [**移轉機器**]。

    ![新增帳戶](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration1.png)

8. **選取機器**中選取您要移轉，從 [保護] 群組和您想要移轉的電腦。

    ![新增帳戶](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration2.png)

9. **設定目標設定**中指定您是否要使用相同的設定，所有的電腦，然後選取 [處理程序伺服器和 Azure 儲存體帳戶。 如果您沒有在另一個程序伺服器這將成為設定伺服器伺服器的 IP 位址。


    ![新增帳戶](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration3.png)

    > [AZURE.NOTE] 儲存帳戶的部署網站復原不支援[的儲存空間帳戶移轉](../resource-group-move-resources.md)跨資源群組內的相同的訂閱或訂閱。

10. 在**指定的帳戶**，請選取您建立的程序伺服器存取發送行動服務的新版本的電腦的帳戶。

    ![新增帳戶](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration4.png)

11. 網站復原會將您複製的資料移轉到您所提供的 Azure 儲存體帳戶。 或者您可以重複使用在舊版的部署中所使用的儲存空間帳戶。
12. 完成工作後虛擬機器會自動同步處理。 同步處理完成之後，您可以刪除虛擬機器從舊版保護群組。
13. 所有的電腦有移轉之後，您可以刪除舊版保護] 群組。
14. 請記得要同步處理完畢之後，指定電腦，並 Azure 網路設定的容錯移轉內容。
15. 如果您有現有的修復計劃，您可以將其移轉的增強型部署與**移轉規劃復原**選項。 您應該只執行此動作之後已移轉所有受保護的電腦。 

    ![新增帳戶](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration5.png)

>[AZURE.NOTE] 完成移轉後繼續[增強的文章](site-recovery-vmware-to-azure-classic.md)。 舊版本文的其餘部分將不再相關，您不需要遵循的 it * * 中所述的步驟。




## <a name="what-do-i-need"></a>我需要什麼？

此圖顯示部署元件。

![新的保存庫](./media/site-recovery-vmware-to-azure-classic-legacy/architecture.png)

以下是您需要︰

**元件** | **部署** | **詳細資料**
--- | --- | ---
**設定伺服器** | Azure 標準 A3 中的虛擬機器為網站復原同一份訂閱。 | 設定伺服器協調受保護的電腦、 程序伺服器和 Azure 中的主要目標伺服器之間的通訊。 它會設定複寫和 Azure 中的座標復原容錯移轉發生時所示。
**主要目標伺服器** | Azure 虛擬機器，以在 Windows Server 2012 R2 圖庫的圖像 （保護 Windows 電腦） 上或 Linux 伺服器為基礎的任一的 Windows server 根據 OpenLogic CentOS 6.6 圖庫的圖像 （保護 Linux 電腦）。<br/><br/> 三個調整大小選項可 – 標準 A4、 標準 D14 和標準 DS4。<br/><br/> 伺服器連線到相同的 Azure 網路設定伺服器。<br/><br/> 您在網站復原入口網站設定 | 接收，並保留複寫從受保護電腦使用附加的 Vhd blob 儲存體 Azure 儲存體帳戶中建立的資料。<br/><br/> 選取 [標準 DS4 特別設定要求一致高效能與使用進階版儲存帳戶低延遲的工作負載的保護。
**程序伺服器** | 執行 Windows Server 2012 R2 的內部部署虛擬或實體伺服器<br/><br/> 我們建議您放在相同的網路和 LAN 區段上為您想要保護的電腦，但它可以在不同的網路上執行，只要受保護的電腦有其 L3 網路可見度。<br/><br/> 您進行設定，並在網站復原入口網站設定伺服器註冊。 | 受保護的電腦複寫資料傳送至內部部署程序伺服器。 有收到磁碟快取快取複製資料。 在資料上執行動作的數字。<br/><br/> 提升資料快取、 壓縮，以及加密，然後再傳送以哪主要目標伺服器。<br/><br/> 處理推入安裝行動服務。<br/><br/> 它會執行 VMware 虛擬機器自動探索。
**內部部署電腦** | 內部部署 VMware 虛擬機器或執行 Windows 或 Linux 的實體伺服器。 | 您設定的套用一或多個機器複寫設定。 您可以在個別的電腦或失敗通常一起收集成復原計劃的多部電腦。 
**行動服務** | 每個虛擬機器或您想要保護的實際伺服器上安裝<br/><br/> 可以手動安裝或推入，當您啟用複寫機器自動安裝程序伺服器。 | 初始複寫 （重新同步處理） 時，行動服務會傳送資料至程序伺服器。 電腦處於受保護狀態 （之後重新同步處理完成） 之後行動服務擷取寫入磁碟記憶體內，並將其傳送給程序。 Windows 伺服器 Applicationconsistency 的方法是使用 vss。
**Azure 網站復原保存庫** | 您建立網站復原保存庫 Azure 的訂閱，而且註冊保存庫中的伺服器。 | 保存庫協調及協調資料複製、 錯誤移轉及復原您的內部部署網站和 Azure 之間。
**複寫機制** | **移至網際網路**，通訊與複製資料從受保護內部部署伺服器至 Azure 使用 SSL/TLS 安全通道透過網際網路。 這是預設的選項。<br/><br/> **VPN/ExpressRoute**— 通訊和複製資料之間內部部署伺服器和 Azure 透過 VPN 連線。 必須設定網站-VPN 或內部部署網站和 Azure 網路之間的 ExpressRoute 連線。<br/><br/> 您可以選取您要在網站復原部署期間所做的方式。 設定而不會影響的現有機器複寫之後，您無法變更機制。 | 這兩個選項要求您開啟受保護的電腦上的任何輸入的網路連接埠。 所有的網路通訊是從內部部署網站啟動時。 

## <a name="capacity-planning"></a>容量計劃

您將需要考慮的主要區域︰

- **來源環境**— VMware 基礎結構、 來源電腦設定與需求。
- **元件伺服器**— 程序伺服器設定伺服器與主要目標伺服器 

### <a name="considerations-for-the-source-environment"></a>來源環境的考量

- **磁碟最大值**，可附加至虛擬機器磁碟的目前大小上限為 1 TB。 因此，可將複寫來源磁碟的最大也是限制為 1 TB。
- **每個來源最大值**，在單一來源台電腦的最大功能 31 TB （31 磁碟） 與主要目標伺服器佈建 D14 執行個體。 
- **每個主要的目標伺服器的來源數目**，來源的多部電腦可以保護與單一主要目標伺服器。 不過，在單一來源台機器無法因為 Azure blob 儲存體上為磁碟複製時，會建立鏡像磁碟的大小 VHD 跨多個主要的目標伺服器，保護，而資料磁碟的形式附加至主版目標伺服器。  
- **每日最大值變更每個來源的工資率**，，有三個需要考慮的每個來源的建議的變更率時考慮的因素。 為基礎的目標考量兩個 IOPS 所需的每個作業來源上目標磁碟機。 這是因為舊資料讀取和寫入的新資料會發生這種情形的目標磁碟機。 
    - **每日變更程序伺服器支援工資率**，來源電腦無法橫跨多個程序伺服器。 在單一程序伺服器可支援 1 TB 的每日變更率。 因此 1 TB 是最大的每日資料變更來源機器支援工資率。 
    - **最大值處理量支援目標磁碟**，每個來源磁碟的最大值變換不能超過 144 GB/日 （含 8 K 寫入大小）。 各種撰寫大小，請參閱處理量和 IOPs 目標的主要目標節中的資料表。 必須由兩個分割這個數字，因為每個來源 IOP 會產生 2 IOPS 目標磁碟上。 設定目標進階版儲存的帳戶時，請閱讀[Azure 延展性和效能的目標](../storage/storage-scalability-targets.md#scalability-targets-for-premium-storage-accounts)。
    - **最大值處理量的儲存空間帳戶支援**，來源無法橫跨多個儲存的帳戶。 儲存帳戶需要秒 20000 要求的最大值和每個來源 IOP 產生 2 IOPS 在主要目標伺服器中指定時，建議您保留數 IOPS 跨來源 10000。 設定進階版的儲存空間帳戶來源時，請閱讀[Azure 延展性和效能的目標](../storage/storage-scalability-targets.md#scalability-targets-for-premium-storage-accounts)。

### <a name="considerations-for-component-servers"></a>元件伺服器的考量

表 1 摘要的設定和主要目標伺服器的虛擬機器大小。

**元件** | **部署 Azure 執行個體** | **核心** | **記憶體** | **最大值磁碟** | **磁碟大小**
--- | --- | --- | --- | --- | ---
設定伺服器 | 標準 A3 | 4 | 7 GB | 8 | 1023 GB
主要目標伺服器 | 標準 A4 | 8 | 14 GB | 16 | 1023 GB
 | 標準 D14 | 16 | 112 GB | 32 | 1023 GB
 | 標準 DS4 | 8 | 28 GB | 16 | 1023 GB

**資料表 1**

#### <a name="process-server-considerations"></a>處理程序伺服器考量

通常程序伺服器的大小取決於每日變更率過所有受保護的工作量。


- 您需要執行工作，例如內嵌壓縮及加密足夠的計算。
- 程序伺服器使用基礎的磁碟快取。 請確定建議的快取空間和磁碟處理量有幫助網路瓶頸或資料時所儲存的資料變更。 
- 確定足夠的頻寬，以便程序伺服器可以上傳的資料提供連續資料保護主要目標伺服器。 

資料表 2 提供的程序伺服器指導方針的摘要。

**資料變更率** | **CPU** | **記憶體** | **快取磁碟大小**| **快取磁碟處理能力** | **頻寬輸入/出口**
--- | --- | --- | --- | --- | ---
< 300 GB | 4 vCPUs (2 通訊端 * 2 核心@2.5 GHz) | 4 GB | 600 GB | 每秒的 7 到 10 MB | 30 Mbps/21 Mbps
若要 600 300 GB | 8 vCPUs (通訊端 2 * 4 個核心@2.5 GHz) | 6 GB | 600 GB | 每秒 11 到 15 MB | 60 Mbps/42 Mbps
600 GB，以 1 TB | 12 vCPUs (2 通訊端 * 6 核心@2.5 GHz) | 8 GB | 600 GB | 每秒的 16 到 20 MB | 100 Mbps/70 Mbps
> 1 TB | 部署另一個處理程序伺服器 | | | | 

**資料表 2**

位置︰ 

- 輸入是下載頻寬 （內部網路之間來源及程序伺服器）。
- 出口是上傳頻寬 （網際網路之間的程序伺服器及主要目標伺服器）。 出口數字假設平均 30%程序伺服器壓縮。
- 快取磁碟不同 OS 磁碟的最小 128 GB 建議，為所有的程序伺服器。
- 快取磁碟傳輸量下列的儲存空間所用的效能︰ 使用 RAID 10 設定 10 K 轉速 8 SA 磁碟機。

#### <a name="configuration-server-considerations"></a>設定伺服器考量事項

每個設定伺服器可支援 3-4 區最多 100 個來源機器。 如果您的部署大於我們建議您部署另一個設定伺服器。 預設的虛擬機器屬性設定的伺服器，請參閱資料表 1。 

#### <a name="master-target-server-and-storage-account-considerations"></a>主要目標伺服器與儲存體帳戶的考量事項

每個主要的目標伺服器儲存空間包含 OS 磁碟、 保留音量及資料磁碟。 保留磁碟機維護磁碟變更的筆記本期間的定義網站復原入口網站中的 [保留] 視窗。  如需主要目標伺服器的虛擬機器屬性，請參閱資料表 1。 表 3 顯示 A4 的磁碟的使用方式。

**執行個體** | **OS 磁碟** | **保留** | **資料磁碟**
--- | --- | --- | ---
 | | **保留** | **資料磁碟**
標準 A4 | 1 磁碟 (1 * 1023 GB) | 1 磁碟 (1 * 1023 GB) | 15 磁碟 (15 * 1023 GB)
標準 D14 |  1 磁碟 (1 * 1023 GB) | 1 磁碟 (1 * 1023 GB) | 31 磁碟 (15 * 1023 GB)
標準 DS4 |  1 磁碟 (1 * 1023 GB) | 1 磁碟 (1 * 1023 GB) | 15 磁碟 (15 * 1023 GB)

**資料表 3**

規劃主目標伺服器的容量而定︰

- Azure 儲存體效能與限制
    - 最大的數字的高度標準層 vm 利用磁碟，關於 40 (每個磁碟 20000/500 IOPS) 單一儲存帳戶。 閱讀關於[延展性目標的標準存放 sccounts](../storage/storage-scalability-targets.md#scalability-targets-for-standard-storage-accounts)和[進階版儲存 sccounts](../storage/storage-scalability-targets.md#scalability-targets-for-premium-storage-accounts)。
-   每日變更工資率 
-   保留大量儲存空間。

請注意︰

- 一個來源無法橫跨多個儲存的帳戶。 適用於移至 [設定保護時，選取儲存帳戶資料磁碟。 在 OS 和通常會移至 [自動部署的儲存帳戶的保留磁碟。
- 所需的儲存空間保留音量取決於每日變更工資率] 和 [保留的天數。 保留儲存每個主要的目標伺服器需求 = 每日來源變換總計 * 保留天的數字。 
- 每個主要的目標伺服器具有只有一個保留音量。 在附加至主版目標伺服器的磁碟共用保留音量。 例如︰
    - 如果來源電腦與 5 的磁碟，而且每個磁碟產生來源上的 120 IOPS （8 K 大小），這轉換成 240 IOPS 每個磁碟 （每個來源 IO 目標磁碟上的 2 作業）。 240 IOPS 是每 500 磁碟 IOPS 限制 Azure 中。
    - 在保留音量，這會變成 120 * 5 = 600 IOPS，這可能會變得多管口。 在此案例中，好的策略會保留大量新增更多磁碟和橫跨，將其做為 RAID 條紋設定。 這會改善效能，因為 IOPS 分散到多個磁碟機。 磁碟機，都會新增至保留大量的數目會如下所示︰
        - 從來源環境總 IOPS / 500
        - 從來源環境 （未壓縮） 每天變換總計 / 287 GB。 287 GB 是受支援的每日的目標磁碟的最大處理量。 此計量會因寫入大小，使用 factor 為 8 K，因為這種情況下 8 K 是三個假設寫入大小。 例如，如果寫入大小的 4 K 然後處理量會 287/2。 如果寫入大小為 16 K 然後處理量會 287 * 2。
- 數字的儲存空間帳戶所需 = 總來源 IOPs/10000。


## <a name="before-you-start"></a>在您開始之前

**元件** | **需求** | **詳細資料**
--- | --- | --- 
**Azure 帳戶** | 您必須以[Microsoft Azure](https://azure.microsoft.com/)帳戶。 您可以開始使用[免費的試用版](https://azure.microsoft.com/pricing/free-trial/)。
**Azure 儲存體** | 您需要 Azure 儲存體帳戶儲存複製的資料<br/><br/> [帳戶應該是[標準地理多餘的儲存空間帳戶](../storage/storage-redundancy.md#geo-redundant-storage)或[進階版儲存帳戶](../storage/storage-premium-storage.md)。<br/><br/> 它必須 Azure 網站修復服務，為相同的區域，並會與同一份訂閱相關聯。 我們不支援建立跨資源群組使用[新的 Azure 入口網站](../storage/storage-create-storage-account.md)的儲存空間帳戶的移動。<br/><br/> 若要進一步瞭解[Microsoft Azure 儲存體簡介](../storage/storage-introduction.md)
**Azure 虛擬網路** | 您必須在設定伺服器和主要目標伺服器會部署 Azure 虛擬網路。 應在相同的訂閱及區域為 Azure 網站復原保存庫。 如果您想要透過 ExpressRoute 或 VPN 連線複寫資料 Azure 虛擬網路必須連接到內部部署網路透過 ExpressRoute 連線或網站-VPN。
**Azure 資源** | 請確定您有足夠 Azure 的資源來部署所有元件。 閱讀更多的[Azure 訂閱限制](../azure-subscription-service-limits.md)。
**Azure 虛擬機器** | [Azure 先決條件](site-recovery-best-practices.md)應符合您想要保護的虛擬機器。<br/><br/> **磁碟計數**，可支援 31 磁碟的最大值的單一受保護的伺服器<br/><br/> **磁碟大小**，個別磁碟容量不能超過 1023 GB<br/><br/> **叢集**，叢集伺服器不支援<br/><br/> **開機**，整合可延伸韌體介面 (UEFI) / 可延伸韌體 Interface(EFI) 開機不受支援<br/><br/> **區**— Bitlocker 加密的區不支援<br/><br/> **伺服器名稱**，名稱應該介於 1 到 63 個字元 （字母、 數字和連字號）。 名稱必須字母或數字的開頭和結尾字母或數字。 受到保護的電腦之後，您可以修改 Azure 的名稱。
**設定伺服器** | 根據 Azure 網站修復 Windows Server 2012 R2 庫圖像的標準 A3 虛擬機器將會建立在您的訂閱設定伺服器。 它會建立新的雲端服務中的第一個執行個體。 如果您選取 [設定伺服器的連線類型為 [公用網際網路雲端服務將會建立保留的公用 IP 位址。<br/><br/> 安裝路徑應在英文的字元。
**主要目標伺服器** | Azure 虛擬機器，標準 A4、 D14 或 DS4。<br/><br/> 安裝路徑應在英文的字元。 例如路徑應**/usr/local/ASR**執行 Linux 主要目標伺服器。
**程序伺服器** | 您可以部署程序伺服器上的實體或執行 Windows Server 2012 R2 最新的更新。 C︰ 在安裝 /。<br/><br/> 我們建議您在相同的網路和子網路上放置伺服器為您想要保護的電腦。<br/><br/> VMware vSphere CLI 5.5.0 伺服器上安裝程序。 VMware vSphere CLI 元件，才能程序在伺服器上，找出 vCenter 伺服器所管理的虛擬機器或 ESXi 主機上執行的虛擬機器。<br/><br/> 安裝路徑應在英文的字元。<br/><br/> 參照檔案系統不支援。
**VMware** | 管理您的 VMware vSphere hypervisors VMware vCenter 伺服器。 它應該執行 vCenter 版本 5.1 或 5.5 最新的更新。<br/><br/> 想要保護的一或多個 vSphere hypervisors 包含 VMware 虛擬機器。 Hypervisor 應該執行 ESX ESXi 版本 5.1 或 5.5 最新的更新。<br/><br/> VMware 虛擬機器應有 VMware 工具安裝及執行。 
**在 Windows 電腦** | 受保護的實際伺服器或執行 Windows VMware 虛擬機器有一些需求。<br/><br/> A 支援 64 位元作業系統︰ **Windows Server 2012 R2**、 **Windows Server 2012**，或**Windows Server 2008 R2，至少 SP1**。<br/><br/> 主機名稱、 連接點，裝置名稱、 Windows 系統路徑 (例如︰ c:\windows\tmp) 應只會以英文。<br/><br/> 作業系統應該安裝 C:\ 磁碟機上。<br/><br/> 支援只有基本磁碟。 不支援動態磁碟。<br/><br/> 在受保護的電腦上的防火牆規則應該讓他們達到 Azure.p 的設定和母片目標伺服器 ><p>您必須提供系統管理員帳戶 （必須是在 Windows 電腦上的本機管理員） 推入安裝在 Windows 伺服器上行動服務。 如果所提供的帳戶必須先停用遠端使用者存取控制本機電腦上的非網域帳戶。 若要執行這會新增值為 1 底下 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System LocalAccountTokenFilterPolicy DWORD 登錄項目。 若要新增的登錄項目從 CLI 開啟 cmd 或 powershell，並輸入**`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**。 [深入](https://msdn.microsoft.com/library/aa826699.aspx)瞭解存取控制。<br/><br/> 後移轉後，如果您要連線到 Windows 中使用遠端桌面 Azure 虛擬機器確認遠端桌面已啟用的內部部署電腦。 如果您不透過 VPN 連線，防火牆規則應該在網際網路上允許遠端桌面連線。
**Linux 電腦** | 支援的 64 位元的作業系統︰ **Centos 6.4，6.5、 6.6**;**Oracle 企業 Linux 6.4，6.5 執行 「 紅色角色相容核心 」 或 「 永不折損企業核心第 3 版 (UEK3)**， **SUSE Linux Enterprise Server 11 SP3**。<br/><br/> 在受保護的電腦上的防火牆規則應該讓連線設定和 Azure 中的主要目標伺服器上。<br/><br/> 在受保護的電腦上的 /etc/hosts 檔案必須包含本機主機名稱對應到所有的 Nic 相關聯的 IP 位址的項目 <br/><br/> 如果您要連線至執行後使用安全命令介面的用戶端 (ssh) 的移轉後的 Linux Azure 虛擬機器，確定受保護的電腦上的安全命令介面服務，設定為自動開始系統開機，及防火牆規則，允許 ssh 連線。<br/><br/> 主機名稱、 連接點、 裝置名稱，並 Linux 系統路徑和檔案名稱 （例如/等 /; /usr） 應該是英文只。<br/><br/> 您可以使用下列的儲存空間的內部部署電腦啟用保護:-<br>檔案系統︰ EXT3、 ETX4、 ReiserFS XFS<br>多重路徑軟體裝置對應工具 （多重路徑）<br>大量管理員︰ LVM2<br>不支援使用 HP CCISS 控制器儲存空間的實體伺服器。
**第三方** | 在這個案例中部分部署元件取決於第三方軟體正常運作。 如的完整清單，請參閱[第三方軟體聲明與資訊](#third-party)


### <a name="network-connectivity"></a>網路連線

設定您的內部部署網站和 Azure 虛擬網路的部署基礎結構元件 （[母片的目標伺服器中的 [設定伺服器） 的間的網路連線時，您會有兩個選項。 您必須決定使用才能部署設定伺服器的網路連線選項。 您必須選擇一次的部署這項設定。 無法更新版本變更。

**網際網路︰**通訊與複寫的內部部署伺服器 （[受保護的電腦中的 [處理程序 server） 和 Azure 基礎結構元件伺服器 （[母片的目標伺服器中的 [設定伺服器） 之間的資料，這時安全 SSL/TLS 連線，從內部部署至公用的端點上設定和母片的目標伺服器。 （唯一的例外是程序伺服器和 TCP 連接埠 9080 加密的主要目標伺服器之間的連線。 只會控制複寫設定的複寫通訊協定和相關的資訊上交換此連線。）

![部署圖表網際網路](./media/site-recovery-vmware-to-azure-classic-legacy/internet-deployment.png)

**VPN**︰ 透過 VPN 連線內部網路和已部署設定伺服器及主要目標伺服器 Azure 虛擬網路之間的通訊與內部部署伺服器 （[受保護的電腦中的 [處理程序 server） 和 Azure 基礎結構元件伺服器 （[母片的目標伺服器中的 [設定伺服器） 之間的資料複寫發生。 請確定您的內部部署網路連線 Azure 虛擬網路，ExpressRoute 連線或網站-VPN 連線。

![部署圖表 VPN](./media/site-recovery-vmware-to-azure-classic-legacy/vpn-deployment.png)


## <a name="step-1-create-a-vault"></a>步驟 1︰ 建立保存庫

1. [管理入口網站](https://portal.azure.com)登入。


2. 展開**資料服務** > **修復服務**，按一下 [**網站復原保存庫**。


3. 按一下 [**建立新** > **快速建立**。

4. 在 [**名稱**] 中，輸入好記的名稱來識別保存庫。

5. 在 [**地區**中，選取 [保存庫的地理區域。 若要檢查支援的地區請參閱[Azure 網站復原定價詳細資料](https://azure.microsoft.com/pricing/details/site-recovery/)中的地理可用性

6. 按一下 [**建立保存庫**]。

    ![新的保存庫](./media/site-recovery-vmware-to-azure-classic-legacy/quick-start-create-vault.png)

請確認已成功建立保存庫狀態列。 保存庫會列為**作用中**主要的 [**復原服務**] 頁面上。

## <a name="step-2-deploy-a-configuration-server"></a>步驟 2︰ 部署設定伺服器

### <a name="configure-server-settings"></a>設定伺服器設定

1. 在 [**復原服務**] 頁面上，按一下 [保存庫，以開啟 [快速入門] 頁面。 也可以隨時使用圖示開啟快速入門。

    ![快速入門] 圖示](./media/site-recovery-vmware-to-azure-classic-legacy/quick-start-icon.png)

2. 在下拉式清單中，選取**Azure VMware/實體伺服器與內部部署網站之間**。
3. 在 [**準備 Target(Azure) 資源****部署設定伺服器**。

    ![部署設定伺服器](./media/site-recovery-vmware-to-azure-classic-legacy/deploy-cs2.png)

4. 在**新的設定伺服器詳細資料**指定︰

    - [設定伺服器] 和 [認證以連線至該名稱。
    - 在網路連線類型] 下拉式選取**公用網際網路**或**VPN**。 請注意，您無法修改此設定後會套用。
    - 選取要在其伺服器應該位於 Azure 網路。 如果您是要有 vpn 才能讓確定 Azure 網路連線到您的內部部署網路如預期般。 
    - 指定內部 IP 位址和子網路，會指派至伺服器。 請注意任何子網路中的前四個 IP 位址保留內部 Azure 的使用方式。 使用任何其他可用的 IP 位址。
    
    ![部署設定伺服器](./media/site-recovery-vmware-to-azure-classic-legacy/cs-details.png)

5. 當您按一下**[確定]**以 Azure 網站修復 Windows Server 2012 R2 庫圖像的標準 A3 虛擬機器就會建立在您的訂閱設定伺服器。 它會建立新的雲端服務中的第一個執行個體。 如果您選擇要透過網際網路連線的雲端服務會建立與保留的公用 IP 位址。 您可以監視**工作**] 索引標籤中的進度。

    ![監控進度](./media/site-recovery-vmware-to-azure-classic-legacy/monitor-cs.png)

6.  如果您透過網際網路連線之後設定伺服器, 部署的記事的公用 IP 位址指派給它在入口網站中 Azure**虛擬機器**頁面上。 然後，**結束點**上的筆記] 索引標籤的公用 HTTPS 連接埠對應到私人連接埠 443。 當您註冊的主要目標及程序伺服器與設定伺服器需要稍後這項資訊。 使用這些端點部署設定伺服器︰

    - HTTPS︰ 公用連接埠用來協調在網際網路上的元件伺服器和 Azure 之間的通訊。 私人連接埠 443 用來透過 VPN 協調元件伺服器和 Azure 之間的通訊。
    - 自訂︰ 公用連接埠是回復工具通訊透過網際網路使用。 透過 VPN，私人連接埠 9443 用於回復工具通訊。
    - PowerShell︰ 私人連接埠 5986
    - 遠端桌面︰ 私人連接埠 3389
    
    ![VM 端點](./media/site-recovery-vmware-to-azure-classic-legacy/vm-endpoints.png)

    >[AZURE.WARNING] 不會刪除，或變更任何設定伺服器部署期間所建立的端點的公用或私用的連接埠號碼。

設定伺服器會自動建立 Azure 雲端服務中保留的 IP 位址的部署。 若要確保設定伺服器雲端服務的 IP 位址相同整個重新啟動的虛擬機器 （包括設定伺服器） 需要保留的位址雲端服務。 設定伺服器解除，或它會保留保留時，會以手動方式未保留要保留的公用 IP 位址。 有 20 保留的公用 IP 位址每個訂閱的預設限制。 [瞭解更多](../virtual-network/virtual-networks-reserved-private-ip.md)有關保留的 IP 位址。 

### <a name="register-the-configuration-server-in-the-vault"></a>註冊保存庫中的 [設定伺服器

1. 在 [**快速入門**] 頁面上，按一下 [**準備目標資源** > **下載登錄機碼**。 自動產生的索引鍵的檔案。 5 天產生後就有效。 將其複製到設定伺服器。
2. **虛擬機器**中選取 [設定伺服器從虛擬機器清單。 開啟 [**儀表板**] 索引標籤，然後按一下 [**連線**]。 **開啟**所下載的 RDP 檔案以登入使用遠端桌面設定伺服器。 如果您使用 VPN，用於從內部部署網站遠端桌面連線的內部的 IP 位址 （部署設定伺服器時，您所指定的地址）。 當您第一次登入 Azure 網站復原設定伺服器設定精靈會自動執行。

    ![註冊](./media/site-recovery-vmware-to-azure-classic-legacy/splash.png)

3. **第三方軟體安裝**] 中按一下 [**我接受**下載並安裝 MySQL]。

    ![MySQL 安裝](./media/site-recovery-vmware-to-azure-classic-legacy/sql-eula.png)

4. **MySQL 伺服器詳細資料**中建立認證登入 MySQL 伺服器執行個體。

    ![MySQL 認證](./media/site-recovery-vmware-to-azure-classic-legacy/sql-password.png)

5. 在 [**網際網路設定**中指定設定伺服器連線到網際網路的方式。 請注意︰

    - 如果您想要使用自訂 proxy，您應該將其設定之前安裝提供者。
    - 當您按一下 [**下一步**檢查 proxy 連線執行測試。
    - 如果您使用自訂 proxy]，或您預設的 proxy 需要的驗證您需要輸入 proxy 詳細資料，包括的地址、 連接埠和認證。
    - 下列 Url 應該可透過 proxy 存取︰
        - *。 hypervrecoverymanager.windowsazure.com
        - *。 accesscontrol.windows.net
        - *。 backup.windowsazure.com
        - *。 blob.core.windows.net
        - *。 store.core.windows.net
    - 如果您有 IP 位址基礎防火牆規則可確保規則會設定為允許來自設定伺服器通訊[Azure 資料中心 IP 範圍](https://msdn.microsoft.com/library/azure/dn175718.aspx)和 HTTPS (443) 通訊協定所述的 IP 位址。 您必須您打算使用，Azure 區域的白色清單 IP 範圍和西美國。

    ![Proxy 註冊](./media/site-recovery-vmware-to-azure-classic-legacy/register-proxy.png)

6. **提供者錯誤訊息已進行本地化設定**中指定您想要的語言顯示錯誤訊息。

    ![錯誤訊息註冊](./media/site-recovery-vmware-to-azure-classic-legacy/register-locale.png)

7. 在**Azure 網站復原註冊**瀏覽並選取檔案複製到伺服器。

    ![檔案註冊](./media/site-recovery-vmware-to-azure-classic-legacy/register-vault.png)

8. 在精靈的 [完成] 頁面上選取一個選項︰

    - 選取 [**啟動帳戶管理] 對話方塊**，指定應該開啟 [管理帳戶] 對話方塊，當您完成精靈]。
    - 選取 [設定伺服器上新增桌面捷徑，讓您可以隨時開啟 [**管理帳戶**] 對話方塊，而不需要重新執行精靈**建立 Cspsconfigtool 桌面圖示**。

    ![完成註冊](./media/site-recovery-vmware-to-azure-classic-legacy/register-final.png)

9. 按一下 [**完成**] 以完成精靈。 系統會產生複雜密碼。 將它複製到 [安全的位置。 您必須驗證，並設定伺服器中註冊程序與主要目標伺服器。 它也用來確保頻道設定伺服器通訊的完整性。 您可以重新產生複雜密碼，但接著您將需要重新註冊主要目標，並使用新的複雜密碼伺服器 [處理程序。

    ![複雜密碼](./media/site-recovery-vmware-to-azure-classic-legacy/passphrase.png)

註冊後設定伺服器會列出保存庫中的 [**設定伺服器**] 頁面。

### <a name="set-up-and-manage-accounts"></a>設定及管理帳戶

在部署網站復原會要求認證，執行下列動作︰

- ThatSite 復原可以自動探索 Vm vCenter 伺服器或 vSphere 主機 VMware 帳戶。 
- 當您新增電腦的 [保護]，讓網站復原可以在其上安裝行動服務。

註冊後設定伺服器之後，您可以開啟新增與管理適用於這些動作的帳戶的 [**管理帳戶**] 對話方塊。 有數種方式進行這項操作︰

- 開啟您選擇建立對話方塊的 [設定伺服器 (cspsconfigtool) 設定的最後一頁上的快速鍵。
- 開啟對話方塊完成的設定伺服器設定。

1. 在 [**管理帳戶**的 [**新增帳戶**。 您也可以修改並刪除現有的帳戶。

    ![管理帳戶](./media/site-recovery-vmware-to-azure-classic-legacy/manage-account.png)

2. **帳戶詳細資料**中指定要使用的帳戶名稱 Azure 和認證 （網域/使用者名稱）。 

    ![管理帳戶](./media/site-recovery-vmware-to-azure-classic-legacy/account-details.png)

### <a name="connect-to-the-configuration-server"></a>連線到設定伺服器 

有兩種方法可以連線到設定伺服器︰

- 透過 VPN 網站的網站或 ExpressRoute 連線
- 透過網際網路 

請注意︰

- 連線至網際網路使用虛擬機器中的端點搭配公用伺服器的虛擬 IP 位址。
- VPN 連線端點私人連接埠與使用內部伺服器的 IP 位址。
- 決定是否要連線 （表單控制項和複寫資料） 的一次性決策從內部部署伺服器的各種元件伺服器 （[母片的目標伺服器中的 [設定伺服器） 執行 Azure 中透過 VPN 連線或網際網路。 您無法變更此設定之後。 如果您必須重新部署此案例，並重新將您的電腦。  


## <a name="step-3-deploy-the-master-target-server"></a>步驟 3︰ 部署主要目標伺服器

1. 按一下 [**準備 Target(Azure) 資源** > **部署主要目標伺服器**。
2. 指定主要目標伺服器詳細資料與認證。 將部署設定伺服器相同的 Azure 網路伺服器。 當您按一下完成 Azure 虛擬機器將會建立 Windows 或 Linux 庫圖像。

    ![目標伺服器設定](./media/site-recovery-vmware-to-azure-classic-legacy/target-details.png)

請注意任何子網路中的前四個 IP 位址保留內部 Azure 的使用方式。 指定任何其他可用的 IP 位址。

>[AZURE.NOTE] 設定才能主控 I/O 大量負載使用[進階版儲存帳戶](../storage/storage-premium-storage.md)需要一致 I/O 高效能與低延遲的工作負載的保護時，請選取 [標準 DS4]。


3. 在 Windows 母片 VM 會建立包含這些端點的目標伺服器。 請注意，將公開端點，建立只有當您透過網際網路連線。

    - 若要透過網際網路傳送複寫資料程序伺服器使用自訂︰ 公用連接埠。 私人連接埠 9443 程序伺服器用於複寫資料傳送到主要目標伺服器，透過 VPN。
    - 若要透過網際網路傳送中繼資料程序伺服器使用 Custom1︰ 公用連接埠。 程序伺服器使用私人連接埠 9080 透過 VPN，傳送給主要目標伺服器的中繼資料。
    - PowerShell︰ 私人連接埠 5986
    - 遠端桌面︰ 私人連接埠 3389

4. 主要目標伺服器 VM 會建立包含這些端點 Linux。 請注意，只有當您透過網際網路連線，建立公用結束點。

    - 若要透過網際網路傳送複寫資料程序 server 使用自訂︰ 公用連接埠。 私人連接埠 9443 程序伺服器用於複寫資料傳送到主要目標伺服器，透過 VPN。
    - Custom1︰ 公用連接埠用於程序伺服器透過網際網路傳送中繼資料。 程序 server 會使用私人連接埠 9080 透過 VPN，傳送給主要目標伺服器的中繼資料
    - SSH︰ 私人連接埠 22

    >[AZURE.WARNING] 不會刪除，或變更的任何端點的主要目標伺服器部署期間所建立的公用或私用的連接埠號碼。

5. 在**虛擬機器**虛擬機器開始等待。

    - 如果是在 Windows server 記事下遠端桌面的詳細資料。
    - 如果是 Linux 伺服器，且您透過 VPN 連線，請注意虛擬機器內部 IP 位址。 如果您透過網際網路連線，請注意的公用 IP 位址。

6.  登入伺服器，才能完成安裝，並登錄設定伺服器。 
7.  如果您執行的 Windows:

    1. 啟動遠端桌面連線到虛擬機器。 第一次登入指令碼會 PowerShell 視窗中執行。 不要將其關閉。 完成後主機代理程式組態工具會自動開啟登錄伺服器。
    2. **Host （主機） 代理程式設定**中指定設定伺服器和連接埠 443 的內部 IP 位址。 即使因為虛擬機器附加至相同的 Azure 網路設定伺服器時，您不在透過 VPN 連線，您可以使用的內部的地址和私人連接埠 443。 [離開**使用 HTTPS**啟用。 您先前所述設定伺服器中輸入密碼。 按一下**[確定**]，以註冊伺服器。 您可以略過的 NAT 選項。 他們不使用。
    3. 如果您的預估的保留磁碟機需求超過 1 TB，您就可以設定保留音量 （:），以虛擬磁碟和[儲存空間](http://blogs.technet.com/b/askpfeplat/archive/2013/10/21/storage-spaces-how-to-configure-storage-tiers-with-windows-server-2012-r2.aspx)
    
    ![Windows 主要目標伺服器](./media/site-recovery-vmware-to-azure-classic-legacy/target-register.png)

8. 如果您執行的 Linux:
    1. 請確定您已經安裝最新 Linux 整合服務 （清單） 安裝主要目標伺服器。 您可以找到清單的最新版本，以及指示安裝方式[以下](https://www.microsoft.com/download/details.aspx?id=46842)。 清單安裝後，請重新啟動電腦。
    2. 按一下 [**準備目標 (Azure) 資源**的 [**下載並安裝其他軟體 （僅適用於 Linux 主要目標伺服器）**]。 複製下載的 tar 檔案的 sftp 用戶端的虛擬機器。 或者，您便可以登入已部署的 linux 主要目標伺服器上，並使用*wget http://go.microsoft.com/fwlink/?LinkID=529757&clcid=0x409*下載檔案。
    2. 登入伺服器使用安全命令介面的用戶端。 如果您已透過 VPN 連線至 Azure 網路使用的內部 IP 位址。 否則使用外部的 IP 位址] 和 [SSH 公用結束點。
    3. 將檔案從 gzipped 安裝程式解壓縮執行︰ **tar – xvzf Microsoft-ASR_UA_8.4.0.0_RHEL6-64***
    ![Linux 主要目標伺服器](./media/site-recovery-vmware-to-azure-classic-legacy/linux-tar.png)
    4. 請確定您在目錄解壓縮 tar 檔案的內容。
    5. 使用 [] 命令的本機檔案複製設定伺服器複雜密碼**回音* `<passphrase>` * > passphrase.txt**
    6. 執行命令 」**sudo。 / 安裝-t 兩個-主控-R MasterTarget-d /usr/local/ASR-i* `<Configuration server internal IP address>` * -p 443-s y-c https-P passphrase.txt** 」。

    ![註冊目標伺服器](./media/site-recovery-vmware-to-azure-classic-legacy/linux-mt-install.png)

9. 等待幾分鐘的時間 (10-15)，並在頁面上核取 [註冊**伺服器**中的清單列出的主要目標伺服器 > **設定伺服器****伺服器詳細資料**] 索引標籤。 如果您執行的 Linux 和其未註冊主機設定工具從重新執行 /usr/local/ASR/Vx/bin/hostconfigcli。 必須設定為根執行 chmod 的存取權限。

    ![驗證目標伺服器](./media/site-recovery-vmware-to-azure-classic-legacy/target-server-list.png)

>[AZURE.NOTE] 可能需要最註冊列在入口網站的主要目標伺服器完成後 15 分鐘。 若要立即更新，請按一下 [在**伺服器設定**] 頁面上的 [**更新**]。

## <a name="step-4-deploy-the-on-premises-process-server"></a>步驟 4︰ 部署內部部署的程序伺服器

在您開始之前我們建議您在處理程序伺服器上設定的靜態 IP 位址，讓它具有保證常設整個重新啟動。

1. 按一下 [快速入門 >**安裝程序 Server 內部部署** > **下載並安裝程序伺服器**。

    ![安裝程序伺服器](./media/site-recovery-vmware-to-azure-classic-legacy/ps-deploy.png)

2.  複製下載的 zip 檔案至要安裝程序 server 的伺服器。 Zip 檔案包含兩個安裝檔案︰

    - Microsoft-ASR_CX_TP_8.4.0.0_Windows*
    - Microsoft-ASR_CX_8.4.0.0_Windows*

3. 解壓縮封存，並將安裝檔案複製到伺服器上的位置。
4. 執行**Microsoft-ASR_CX_TP_8.4.0.0_Windows*** 安裝檔案，然後依照指示進行。 這會安裝所需的部署協力廠商元件。
5. 再執行**Microsoft-ASR_CX_8.4.0.0_Windows***。
6. 在 [**伺服器] 模式**的頁面上選取 [**程序伺服器**。
7. 在 [**環境的詳細資料**頁面上執行下列動作︰


    - 如果您想要保護 VMware 虛擬機器中按一下**[是]**
    - 如果您只想要保護的實際的伺服器，因此不需要 VMware vCLI 程序伺服器上安裝。 按一下 [**否]** ，然後繼續。

8. 安裝 VMware vCLI 時，請注意下列事項︰

    - **僅限 VMware vSphere CLI 5.5.0 功能受支援**。 與其他版本或更新 vSphere CLI 無法程序伺服器。
    - 下載 vSphere CLI 5.5.0 從[以下。](https://my.vmware.com/web/vmware/details?downloadGroup=VCLI550&productId=352)
    - 如果您有安裝 vSphere CLI 只在您開始安裝程序伺服器，並安裝程式無法偵測到它之前，請等候達五分鐘，才再次嘗試安裝。 這樣的所有環境變數所需的 vSphere CLI 偵測都初始化正確。

9.  在**NIC 程序伺服器的選取範圍**中選取 [程序伺服器應該使用的網路介面卡]。

    ![選取介面卡](./media/site-recovery-vmware-to-azure-classic-legacy/ps-nic.png)

10. 在 [**設定伺服器詳細資料**︰

    - IP 位址和連接埠] 如果您透過 VPN 連線指定設定伺服器和連接埠 443 的內部 IP 位址。 否則指定虛擬的公用 IP 位址和對應公用 HTTP 端點。
    - 在 [設定伺服器的複雜密碼中輸入。
    - 如果您想要停用驗證，當您使用自動推入安裝服務時，請清除 [**驗證行動服務軟體簽章**。 簽章驗證必須從程序伺服器的網際網路連線。
    - 按一下 [**下一步**]。

    ![註冊的伺服器設定](./media/site-recovery-vmware-to-azure-classic-legacy/ps-cs.png)


11. **選取 [安裝磁碟機**中選取 [快取磁碟機]。 程序伺服器需要至少 600 GB 的可用空間的快取磁碟機。 然後按一下 [**安裝**]。 

    ![註冊的伺服器設定](./media/site-recovery-vmware-to-azure-classic-legacy/ps-cache.png)

12. 請注意，您可能需要重新啟動伺服器以完成安裝。 在 [**設定伺服器** > 程序伺服器，會出現，而成功註冊保存庫中，核取**伺服器詳細資料**。

>[AZURE.NOTE]可能需要最註冊程序伺服器出現在 [設定伺服器底下所列的完成後 15 分鐘。 若要立即更新，以重新整理設定伺服器按一下底部的 [設定伺服器] 頁面的 [重新整理] 按鈕
 
![驗證程序伺服器](./media/site-recovery-vmware-to-azure-classic-legacy/ps-register.png)

如果您未停用行動服務的簽章驗證，當您註冊程序伺服器，您可以執行更新版本，如下所示︰

1. 登入程序伺服器以系統管理員身分，並開啟檔案 C:\pushinstallsvc\pushinstaller.conf 以進行編輯。 在區段底下**[PushInstaller.transport]**新增這一行︰ **SignatureVerificationChecks = 「 0 」**。 儲存並關閉檔案。
2. 重新啟動 InMage PushInstall 服務。


## <a name="step-5-update-site-recovery-components"></a>步驟 5︰ 更新網站復原元件

隨時更新網站復原元件。 當有可用的更新您應該以下列順序安裝︰

1. 設定伺服器
2. 程序伺服器
3. 主要目標伺服器
4. 回復工具 (vContinuum)

### <a name="obtain-and-install-the-updates"></a>取得並安裝更新


1. 從網站復原**儀表板**，您可以取得設定程序，與主要目標伺服器的更新。 Linux 安裝，請從 gzipped 安裝程式擷取檔案，然後執行命令 」 sudo。 / 安裝 」 以安裝更新。
2. [下載](http://go.microsoft.com/fwlink/?LinkID=533813)最新回復 tool(vContinuum) 更新。
3. 如果您正在執行虛擬機器或已安裝的行動服務的實體伺服器，您可以取得更新的服務，如下所示︰

    - **選項 1**︰ 下載更新︰
        - [Windows 伺服器 （僅限 64 位元）](http://download.microsoft.com/download/8/4/8/8487F25A-E7D9-4810-99E4-6C18DF13A6D3/Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe)
        - [CentOS 6.4,6.5,6.6 （僅限 64 位元）](http://download.microsoft.com/download/7/E/D/7ED50614-1FE1-41F8-B4D2-25D73F623E9B/Microsoft-ASR_UA_8.4.0.0_RHEL6-64_GA_28Jul2015_release.tar.gz)
        - [Oracle 企業 Linux 6.4,6.5 （僅限 64 位元）](http://download.microsoft.com/download/5/2/6/526AFE4B-7280-4DC6-B10B-BA3FD18B8091/Microsoft-ASR_UA_8.4.0.0_OL6-64_GA_28Jul2015_release.tar.gz)
        - [SUSE Linux Enterprise Server SP3 （僅限 64 位元）](http://download.microsoft.com/download/B/4/2/B4229162-C25C-4DB2-AD40-D0AE90F92305/Microsoft-ASR_UA_8.4.0.0_SLES11-SP3-64_GA_28Jul2015_release.tar.gz)
        - 更新程序伺服器後更新的版本的行動服務便會出現在 C:\pushinstallsvc\repository 伺服器上的資料夾程序。
    - **選項 2**︰ 如果您有安裝行動服務較舊版本的電腦，您可以從 [管理] 入口網站，自動升級在電腦上的行動服務。

        1. 確定已更新程序伺服器。
        2. 請確定的受保護的電腦符合[先決條件](#install-the-mobility-service-automatically)用於自動推入行動服務，以便更新預期般運作。
        2. 選取 [保護] 群組，醒目提示 [受保護的電腦，按一下 [**更新行動服務**。 此按鈕才有較新版本的行動服務，才能使用。 

            ![選取 [vCenter 伺服器](./media/site-recovery-vmware-to-azure-classic-legacy/update-mobility.png)

在 [選取帳戶中指定要用來更新行動服務保護伺服器的系統管理員帳戶。 按一下 [確定]，並等待 [觸發的工作完成。


## <a name="step-6-add-vcenter-servers-or-vsphere-hosts"></a>步驟 6︰ 新增 vCenter 伺服器或 vSphere 主機

1. 按一下 [**伺服器** > **設定伺服器**> 設定伺服器 > 若要新增的 vCenter 伺服器或 vSphere 主機**新增 vCenter 伺服器**。

    ![選取 [vCenter 伺服器](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter.png)

2. 指定伺服器或 host （主機） 的詳細資訊，然後選取 [將用來探索它的程序伺服器。

    - 如果 vCenter 伺服器不在執行預設連接埠 443 指定執行 vCenter 伺服器的連接埠號碼。
    - 程序伺服器必須 vCenter 伺服器/vSphere 主機相同的網路上，而且應有 VMware vSphere CLI 5.5.0 安裝。

    ![vCenter 伺服器設定](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter4.png)


3. 探索完成後，vCenter 伺服器會列在 [設定伺服器詳細資料。

    ![vCenter 伺服器設定](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter2.png)

4. 如果您要新增的伺服器或主機使用非系統管理員帳戶，請確定帳戶擁有下列權限︰

    - vCenter 帳戶應具備資料中心、 資料存放區、 資料夾、 主機、 網路、 資源、 儲存空間檢視、 虛擬機器和啟用的 vSphere 分散式切換權限。
    - vSphere 主機帳戶應有資料中心、 資料存放區、 資料夾、 主機、 網路、 資源、 虛擬機器和啟用的 vSphere 分散式切換權限



## <a name="step-7-create-a-protection-group"></a>步驟 7︰ 建立保護群組

1. 開啟**受保護的項目** > **保護群組** > **建立 [保護] 群組**。

    ![建立 [保護] 群組](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg1.png)

2. 在 [**指定保護群組設定**] 頁面上指定群組的名稱，然後選取您要建立群組設定伺服器。

    ![保護群組設定](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg2.png)

3. 在 [**指定複寫設定**] 頁面上設定複寫用於群組中的所有電腦的。

    ![保護群組複寫](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg3.png)

4. 設定︰
    - **多重 VM 一致性**︰ 如果您開啟此 [保護] 群組中的電腦上建立共用的應用程式一致復原點。 所有的 [保護] 群組中的電腦執行的相同的工作負載時，最相關這項設定。 所有的電腦將會還原至相同的資料點。 僅適用於 Windows 伺服器。
    - **RPO 閥值**︰ 連續資料保護複寫 RPO 超過設定的 RPO 臨界值時，將會產生警示。
    - **復原點保留**︰ 指定 [保留] 視窗。 受保護的電腦可以復原此視窗中的任何位置。
    - **應用程式一致的快照頻率**︰ 指定會建立包含應用程式一致的快照的復原點的頻率。

這些是建立 [**受保護的項目**] 頁面上，您可以監視 [保護] 群組。

## <a name="step-8-set-up-machines-you-want-to-protect"></a>步驟 8︰ 設定您想要保護的電腦

您需要安裝行動服務虛擬機器和您想要保護的實際伺服器上。 您可以透過以下兩種方法︰

- 自動推入，並從程序伺服器的每一部電腦上安裝的服務。
- 手動安裝的服務。 

### <a name="install-the-mobility-service-automatically"></a>自動安裝行動服務

當您新增至 [保護] 群組的機器行動服務自動推入，每一部電腦上安裝程序伺服器。 

**自動推入在 Windows 伺服器上安裝行動服務︰** 

1. 安裝最新的更新程序伺服器中所述[步驟 5︰ 安裝最新的更新](#step-5-install-latest-updates)，並確定程序伺服器是使用。 
2. 請確定在來源電腦及程序伺服器間的網路連線，而且來源電腦是從程序伺服器存取。  
3. 設定為允許**檔案及印表機共用**] 與 [ **Windows 管理儀器**Windows 防火牆。 [Windows 防火牆設定] 底下選取 [允許應用程式或功能通過防火牆] 的選項，然後選取的應用程式下, 圖所示。 屬於網域的電腦中，您可以設定防火牆原則 gpo。

    ![防火牆設定](./media/site-recovery-vmware-to-azure-classic-legacy/push-firewall.png)

4. 若要執行推入安裝所用的帳戶必須是您想要保護的電腦上的 [管理員] 群組中。 只會使用這些認證的推入安裝行動服務，您會向他們提供當您新增至 [保護] 群組的電腦。
5. 如果所提供的帳戶不是您必須停用遠端使用者存取控制本機電腦上的網域帳戶。 若要執行這會新增值為 1 底下 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System LocalAccountTokenFilterPolicy DWORD 登錄項目。 若要新增的登錄項目從 CLI 開啟 cmd 或 powershell，並輸入**`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**。 

**自動推入安裝行動服務 Linux 伺服器上︰**

1. 安裝最新的更新程序伺服器中所述[步驟 5︰ 安裝最新的更新](#step-5-install-latest-updates)，並確定程序伺服器是使用。
2. 請確定在來源電腦及程序伺服器間的網路連線，而且來源電腦是從程序伺服器存取。  
3. 請確定您的帳戶是根使用者來源 Linux 伺服器上。
4. 確定 /etc/hosts 伺服器上的檔案來源 Linux 包含本機主機名稱對應到所有的 Nic 相關聯的 IP 位址的項目。
5. 在您想要保護的電腦上安裝最新的 openssh openssh 伺服器、 openssl 套件。
6. 請確定 SSH 連接埠 22 上是啟用並執行。 
7. 啟用 SFTP 子系統及密碼驗證 sshd_config 檔案中的，如下所示︰ 

    - a） 作為根方式登入。
    - b） 在檔案 /etc/ssh/sshd_config 檔案中，尋找開頭**PasswordAuthentication**的線條。
    - c） 取消註解線條，然後變更 「 否 」 [是] 中的值。

        ![Linux 行動性](./media/site-recovery-vmware-to-azure-classic-legacy/linux-push.png)

    - d） 尋找開頭子系統的線條，然後取消行註解]。
    
        ![Linux 推入行動性](./media/site-recovery-vmware-to-azure-classic-legacy/linux-push2.png)    

8. 確定來源電腦 Linux variant 支援。 
 
### <a name="install-the-mobility-service-manually"></a>手動安裝行動服務

安裝行動服務使用軟體套件是 C:\pushinstallsvc\repository 的程序伺服器上。 登入程序伺服器，並將適當的安裝套件複製到根據下表的來源機器:-

| 來源作業系統                               | 程序伺服器上的行動服務套件                                                            |
|---------------------------------------------------    |------------------------------------------------------------------------------------------------------ |
| Windows 伺服器 （僅限 64 位元）                          | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe`         |
| CentOS 6.4，6.5，6.6 （僅限 64 位元）                    | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_RHEL6-64_GA_28Jul2015_release.tar.gz`     |
| SUSE Linux Enterprise Server 11 SP3 （僅限 64 位元）     | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_SLES11-SP3-64_GA_28Jul2015_release.tar.gz`|
| Oracle 企業 Linux 6.4，6.5 （僅限 64 位元）        | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_OL6-64_GA_28Jul2015_release.tar.gz`       |


**安裝行動服務以手動方式在 Windows server 上的**，執行下列動作︰

1. **Microsoft ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe**套件複製來源電腦上方表格中所列的程序伺服器目錄路徑。
2. 安裝行動服務來源電腦上執行的可執行檔。
3. 依照安裝程式的指示。
4. 選取**行動服務**的角色，然後按一下 [**下一步**]。
    
    ![安裝行動服務](./media/site-recovery-vmware-to-azure-classic-legacy/ms-install.png)

5. 離開安裝目錄作為預設安裝路徑，然後按一下 [**安裝**]。
6. **Host （主機） 代理程式設定**中指定的 IP 位址和 HTTPS 的設定伺服器的連接埠。

    - 如果您透過網際網路連線指定虛擬的公用 IP 位址和公用 HTTPS 端點連接埠。
    - 如果您透過 VPN 連線指定的內部 IP 位址和連接埠 443。 核取 [離開**使用 HTTPS** 。

    ![安裝行動服務](./media/site-recovery-vmware-to-azure-classic-legacy/ms-install2.png)

7. 指定設定伺服器複雜密碼，然後按一下**[確定**] 以設定伺服器中註冊行動服務]。

**若要從命令列執行︰**

1. CX 複雜密碼複製至伺服器上的檔案 」 C:\connection.passphrase 」，並執行此命令。 在我們的範例 CX i 104.40.75.37 和 HTTPS 連接埠是 62519:

    `C:\Microsoft-ASR_UA_8.2.0.0_Windows_PREVIEW_20Mar2015_Release.exe" -ip 104.40.75.37 -port 62519 -mode UA /LOG="C:\stdout.txt" /DIR="C:\Program Files (x86)\Microsoft Azure Site Recovery" /VERYSILENT  /SUPPRESSMSGBOXES /norestart  -usesysvolumes  /CommunicationMode https /PassphrasePath "C:\connection.passphrase"`

**安裝行動服務 Linux 伺服器上以手動方式**︰

1. 複製上述表格，從來源電腦程序伺服器為基礎的適當 tar 封存。
2. 開啟命令介面程式，並執行擷取壓縮的 tar 封存到本機的路徑`tar -xvzf Microsoft-ASR_UA_8.2.0.0*`
3. 建立 passphrase.txt 檔案解壓縮 tar 封存的內容所輸入的本機目錄中*`echo <passphrase> >passphrase.txt`*從命令介面。
4. 輸入以安裝行動服務*`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*。
5. 指定的 IP 位址和連接埠︰

    - 如果您透過網際網路連線到設定伺服器指定設定伺服器虛擬公用 IP 位址和中的公用 HTTPS 端點`<IP address>`和`<port>`。
    - 如果您連線透過 VPN 連線指定的內部 IP 位址和 443。

**若要從命令列執行**︰

1. 複製 CX 複雜密碼到伺服器上的檔案 」 passphrase.txt 」，並執行此命令。 在我們的範例 CX i 104.40.75.37 和 HTTPS 連接埠是 62519:

若要在實際執行伺服器上安裝︰

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 62519 -s y -c https -P passphrase.txt
 
若要在目標伺服器上安裝︰


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 62519 -s y -c https -P passphrase.txt

>[AZURE.NOTE] 當您新增已執行的適當版本的行動服務然後推入安裝的電腦，保護群組會略過。


## <a name="step-9-enable-protection"></a>步驟 9︰ 啟用保護

若要啟用保護您的保護群組新增虛擬機器和實體伺服器。 在開始之前，請注意:

- 虛擬機器發現每 15 分鐘，而且必須花 15 分鐘的時間才會顯示在 Azure 網站復原之後探索。
- 環境 （例如 VMware 工具安裝） 虛擬機器上做的變更也可以充分 15 分鐘，若要在網站復原更新。
- 在 [**伺服器設定**] 頁面上的 vCenter 伺服器/ESXi host （主機） 您可以檢查上次發現**最後一個連絡人在**欄位中。
- 如果您已經建立 [保護] 群組，並新增 vCenter 伺服器或 ESXi 主機之後，花 15 分鐘，重新整理 Azure 網站復原入口網站，以及**新增電腦，保護群組**] 對話方塊中列出的虛擬機器。
- 如果您想要繼續立即加入不會排程探索等候的 [保護] 群組中的電腦，醒目提示設定伺服器 （但不要按一下），按一下 [**重新整理**] 按鈕。
- 當您新增虛擬機器或實體的電腦，保護群組時，程序伺服器自動推入和 it 尚未安裝如果來源伺服器上安裝行動服務。
- 搭配使用自動推入機制請確定您已設定受保護的電腦上一個步驟所述。

新增機器如下所示︰

1. 按一下 [**受保護的項目** > **保護群組** > **機器** > **加入電腦**。 最佳作法是我們建議您保護群組應鏡像您的工作量，讓您新增電腦執行相同的群組特定應用程式。
2. **選取虛擬機器**中如果您要保護的實際的伺服器，在**新增的實體機器**精靈提供的 IP 位址和好記的名稱。 然後選取 [作業系統系列]。

    ![新增 V 中心伺服器](./media/site-recovery-vmware-to-azure-classic-legacy/physical-protect.png)

3. **選取虛擬機器**中如果您保護 VMware 虛擬機器中，選取 [管理您的虛擬機器 （或 EXSi 主機正在執行），vCenter 伺服器，，然後選取 [機器。

    ![新增 V 中心伺服器](./media/site-recovery-vmware-to-azure-classic-legacy/select-vms.png) 
4. 在 [**指定目標資源**選取主要目標伺服器及複寫的使用，並選擇是否要設定適用於所有工作負載的儲存空間。 選取[進階版儲存帳戶](../storage/storage-premium-storage.md)時設定才能主控 IO 大量負載需要一致 IO 高效能與低延遲工作負載的保護。 如果您想要使用的工作量磁碟的進階版儲存的帳戶，您需要使用母片的目標 DS 數列。 您無法使用母片目標非 DS 數列的進階版儲存空間的磁碟。

    >[AZURE.NOTE] 我們不支援建立跨資源群組使用[新的 Azure 入口網站](../storage/storage-create-storage-account.md)的儲存空間帳戶的移動。

    ![vCenter 伺服器](./media/site-recovery-vmware-to-azure-classic-legacy/machine-resources.png)

5. **指定帳戶**中選取您想要在受保護的電腦上安裝行動服務使用的帳戶。 帳戶認證所需的自動安裝行動服務。 如果您無法選取帳戶，請務必您設定一個步驟 2 中所述。 請注意 Azure 無法存取此帳戶。 在 Windows 版伺服器帳戶必須在來源伺服器的系統管理員權限。 為 Linux 帳戶必須是根。

    ![Linux 認證](./media/site-recovery-vmware-to-azure-classic-legacy/mobility-account.png)

6. 按一下 [核取記號，完成新增至 [保護] 群組的電腦，並啟動初始複寫每一部電腦]。 您可以監視在 [**工作**] 頁面上的狀態。

    ![新增 V 中心伺服器](./media/site-recovery-vmware-to-azure-classic-legacy/pg-jobs2.png)

7. 此外您可以監視保護狀態，按一下 [**受保護的項目**> 保護群組名稱 >**虛擬機器**。 初始複寫完成與電腦同步處理資料之後，他們會顯示**受保護**狀態。

    ![虛擬機器中的工作](./media/site-recovery-vmware-to-azure-classic-legacy/pg-jobs.png)


### <a name="set-protected-machine-properties"></a>設定受保護電腦屬性

1. 電腦有**受保護**狀態之後，您可以設定其容錯移轉內容。 在保護] 群組詳細資料選取電腦並開啟 [**設定**] 索引標籤。
2. 您可以修改容錯移轉後的 Azure 虛擬機器大小指定給 Azure 中電腦的名稱。 您也可以選取要電腦會連線後移轉後的 Azure 網路。

    > [AZURE.NOTE] 用於部署網站復原的網路不支援[的網路移轉](../resource-group-move-resources.md)跨資源群組內的相同的訂閱或訂閱。

    ![設定虛擬機器屬性](./media/site-recovery-vmware-to-azure-classic-legacy/vm-props.png)

請注意︰

- Azure 電腦的名稱必須遵守 Azure 需求。
- 依預設 Azure 中複製的虛擬機器未連線到 Azure 的網路。 如果您想要複製通訊的虛擬機器確認這些設定相同的 Azure 網路。
- 如果您調整音量 VMware 虛擬機器或實體的伺服器上它會進入嚴重狀態。 如果您需要修改大小，請執行下列動作︰

    - a） 變更大小設定。
    - b） 在 [**虛擬機器**] 索引標籤中，選取的虛擬機器，按一下 [**移除**]。
    - c） 中**移除的虛擬機器**中，選取 [**停用保護 （使用復原切入和大量調整大小）**] 選項。 這個選項會停用，但會保留 Azure 中的復原點。

        ![設定虛擬機器屬性](./media/site-recovery-vmware-to-azure-classic-legacy/remove-vm.png)

    - d） 重新啟用虛擬機器保護。 當您重新啟用保護調整大量的資料會傳送至 Azure。

    

## <a name="step-10-run-a-failover"></a>步驟 10︰ 執行容錯移轉

目前您僅可以執行意外的錯誤後移轉受保護的 VMware 虛擬機器及實體伺服器。 請注意下列事項︰



- 啟動容錯移轉前，請確定設定及主要目標伺服器執行且運作正常。 否則容錯移轉將會失敗。
- 來源機器不關閉意外的容錯移轉的一部分。 執行意外的容錯移轉停止資料複製為受保護的伺服器。 您需要刪除 [保護] 群組中的電腦，然後才能開始保護電腦，意外的容錯移轉完成後再次新增。
- 如果您想要透過失敗，而不會遺失任何資料，請確定主站台虛擬機器已關閉之前啟動容錯移轉。

1. 在**復原方案**] 頁面上，並新增復原計劃。 指定計劃的詳細資料，並選取目標**Azure** 。

    ![設定復原計劃](./media/site-recovery-vmware-to-azure-classic-legacy/rplan1.png)

2. 在**選取的虛擬機器**中選取 [保護] 群組，然後選取機器 [若要新增至復原計劃] 群組中。 [閱讀更多](site-recovery-create-recovery-plans.md)修復計劃的相關資訊。

    ![加入虛擬機器](./media/site-recovery-vmware-to-azure-classic-legacy/rplan2.png)

3. 如果需要您可以自訂建立群組和順序排序的計劃中的電腦中復原計劃會移轉。 您也可以新增手動動作和指令碼的提示。 使用[Azure 自動化 Runbooks](site-recovery-runbook-automation.md)，可以新增至 Azure 復原時的指令碼。

4. 在 [**復原方案]**頁面上選取 [計劃，按一下**意外的容錯移轉**。
5. 在 [**確認容錯移轉**驗證容錯移轉方向 (至 Azure)，然後選取 [復原指向無法透過。
6. 等到完成，然後確認容錯移轉，如預期般運作，並複製的虛擬機器順利啟動 Azure 中的容錯移轉工作。




## <a name="step-11-fail-back-failed-over-machines-from-azure"></a>步驟 11︰ 無法透過 azure 機器回失敗

[進一步瞭解](site-recovery-failback-azure-to-vmware-classic-legacy.md)如何在執行的機器上讓您失敗 Azure 返回您內部部署環境。


## <a name="manage-your-process-servers"></a>管理您的程序伺服器

程序伺服器複寫資料至 Azure 中的主要目標伺服器與探索新 VMware 虛擬機器新增至 vCenter 伺服器。 在下列情況下可能會想要變更您的部署中的程序伺服器︰

- 如果目前的程序伺服器當機
- 如果無法接受為您的組織層級，讓您的復原點目標 (RPO)。

如果需要您可以將部分或所有的內部部署 VMware 虛擬機器和實體伺服器複寫移到不同的程序伺服器。 例如︰

- **失敗**，如果處理程序伺服器當機或無法使用您可以移動受保護的電腦複製到另一個處理程序伺服器。 中繼資料的來源電腦及複本電腦會移至新的程序伺服器，資料會重新同步處理。 新的程序伺服器會自動連線到 vCenter 伺服器，才能執行自動探索。 您可以監視網站復原儀表板上的程序伺服器的狀態。
- **負載平衡調整 RPO**，改善的負載平衡，您可以在網站復原入口網站中，選取不同的程序的伺服器，然後移到它的一個或多個機器複寫手動負載平衡。 在此情況下中繼資料的所選的來源與複本電腦會移至新的程序伺服器。 原始的程序伺服器會保持聯繫 vCenter 伺服器。 

### <a name="monitor-the-process-server"></a>監視程序伺服器

如果程序伺服器是在網站復原儀表板所顯示的狀態警告的要徑狀態。 您可以按一下狀態]，開啟 [事件] 索引標籤，然後 [向下切入工作] 索引標籤上的特定工作。 

### <a name="modify-the-process-server-used-for-replication"></a>修改使用複寫的程序伺服器

1. 開啟**伺服器** > **設定伺服器**> 設定伺服器 >**伺服器詳細資料**。
2. 按一下 [**處理程序伺服器** > **變更程序伺服器**您想要修改的伺服器旁邊。

    ![變更程序伺服器 1](./media/site-recovery-vmware-to-azure-classic-legacy/change-ps1.png)

3. **變更程序**server > **目標程序伺服器**選取您想要使用]，然後選取 [您想要複製到新的伺服器虛擬機器新伺服器。 按一下 [可用空間和使用的記憶體的詳細資料的伺服器名稱] 旁的 [資訊] 圖示。 將需要每個所選的虛擬機器複製到新的程序伺服器的平均空間會顯示以協助您進行載入決策。

    ![變更程序伺服器 2](./media/site-recovery-vmware-to-azure-classic-legacy/change-ps2.png)

4. 按一下 [核取記號，若要開始複製到新的程序伺服器]。 請注意，是否要徑程序伺服器中移除所有的虛擬機器時應該不再儀表板中顯示要徑警告。


## <a name="third-party-software-notices-and-information"></a>第三方軟體聲明與資訊

請不要翻譯或當地語系化

軟體和韌體執行中的 Microsoft 產品或服務為基礎，或整合下方所列的專案中的資料 （共同，「 協力廠商程式碼 」）。  Microsoft 是不原始作者的協力廠商程式碼。  原始的著作權聲明及授權，在 Microsoft 收到這類協力廠商程式碼中，設定左起第四個下方。

在 A] 區段中的資訊關於從下方所列的專案的協力廠商程式碼元件。 這類的資訊和授權可供參考。  此協力廠商程式碼會被 relicensed 您 microsoft 下 Microsoft 產品或服務的 Microsoft 軟體授權條款。  

區段 B 中的資訊關於協力廠商程式碼元件，會在您就可以使用 microsoft 原始授權條款。

可以在[Microsoft 下載中心](http://go.microsoft.com/fwlink/?LinkId=529428)找到完整的檔案。 Microsoft 保留不明文授予之，所有的權限，也就是說，是否 estoppel 或。
