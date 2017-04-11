<properties
    pageTitle="Azure Azure 網站復原複寫 VMware 虛擬機器和實體伺服器 |Microsoft Azure"
    description="本文將說明如何部署 Azure 網站復原協調複寫、 錯誤移轉及復原的內部部署 VMware 虛擬機器和 Azure Windows/Linux 實體伺服器。"
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

# <a name="replicate-vmware-virtual-machines-and-physical-servers-to-azure-with-azure-site-recovery"></a>Azure Azure 網站復原複寫 VMware 虛擬機器和實體伺服器

> [AZURE.SELECTOR]
- [Azure 入口網站](site-recovery-vmware-to-azure.md)
- [傳統的入口網站](site-recovery-vmware-to-azure-classic.md)
- [傳統入口網站 （舊版）](site-recovery-vmware-to-azure-classic-legacy.md)


Azure 網站復原服務佔業務連續性和損壞復原 (BCDR) 策略來協調複寫、 錯誤移轉及復原的虛擬機器和實體伺服器。 Azure，或第二個內部部署資料中心，可將複寫機器。 概述讀[什麼是 Azure 網站復原？](site-recovery-overview.md)。

## <a name="overview"></a>概觀

本文將說明如何︰

- **複製 VMware Azure 虛擬機器**— 部署網站復原協調複寫、 錯誤移轉及復原的內部部署 VMware 虛擬機器至 Azure 儲存體。
- **複製實體伺服器 Azure**— 部署 Azure 網站復原複寫、 錯誤移轉及復原的內部部署實體 Windows 和 Linux 伺服器 Azure 進行共同作業。

>[AZURE.NOTE] 本文將說明如何將複寫到 Azure。 如果您想要複製到第二個資料中心的 VMware Vm 或 Windows/Linux 實體伺服器，請遵循[本文](site-recovery-vmware-to-vmware.md)中的指示進行。

在底端的本文中，或在[Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上張貼任何註解或問題。

## <a name="enhanced-deployment"></a>增強的部署

本文包含以下包含的增強型部署傳統 Azure 入口網站中的指示。 我們建議您可以使用此版本的所有新的部署。 如果您已使用舊版舊版部署我們建議您移轉至新的版本。 閱讀[更多](site-recovery-vmware-to-azure-classic-legacy.md##migrate-to-the-enhanced-deployment)關於移轉。

增強的部署是主要更新。 以下是我們所做的改良功能的摘要︰

- **沒有基礎結構中 Azure Vm**︰ 資料會複寫直接到 Azure 儲存體帳戶。 此外複寫和容錯移轉有不需要以舊版的部署中，我們需要設定任何基礎結構 Vm ([母片的目標伺服器中的 [設定伺服器）。  
- **整合安裝**︰ 單一安裝提供簡單設定能與延展性內部部署元件。
- **安全的部署**︰ 所有流量已都加密並透過 HTTPS 443 傳送複寫管理通訊。
- **復原點**︰ 當機和應用程式一致的復原支援 Windows 和 Linux 環境中，與支援這兩個單一 VM 和多重 VM 一致的設定。
- **測試容錯移轉**︰ 非破壞測試容錯移轉至 Azure，而不影響實際執行或暫停複寫的支援。
- **意外的容錯移轉**︰ 意外的容錯移轉至 Azure 的增強型 Vm 自動前請先關閉容錯移轉支援。
- **回復**︰ 整合式的回復複寫只 delta 變更回內部部署網站。
- **vSphere 6.0**︰ 有限支援 VMware Vsphere 6.0 部署。


## <a name="how-does-site-recovery-help-protect-virtual-machines-and-physical-servers"></a>網站復原如何協助保護虛擬機器和實體伺服器？


- VMware 系統管理員可以設定離保護 Azure 商務工作負載和 VMware 虛擬機器上執行應用程式。 伺服器管理員可以複寫 Azure 實體內部部署 Windows 和 Linux 伺服器。
- Azure 網站修復主控台提供簡單的設定和管理複寫、 容錯移轉及修復程序的單一位置。
- 如果您要複製 VMware 虛擬機器所管理的 vCenter 伺服器，網站復原才能自動探索這些 Vm。 如果是 ESXi 主機機器網站復原會發現 Vm 主機上。
- Azure 及回復 （還原） 從 Azure VMware VM 伺服器內部部署網站中，從您的內部部署基礎結構執行簡單的容錯移轉。
- 設定群組在一起的層多台電腦上的應用程式工作負載的修復計劃。 您可以容錯這些方案中，並在網站復原提供多重 VM 一致性而執行相同的工作負載的電腦可以同時復原一致的資料點。


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

分析藍本元件︰

- **內部部署管理伺服器**︰ 管理伺服器執行網站復原元件︰
    - **設定伺服器**︰ 協調通訊和管理資料複寫及修復程序。
    - **程序伺服器**︰ 做為複寫閘道器。 它接收資料從受保護的來源機器、 最佳化，使用快取、 壓縮和加密並會複寫資料傳送至 Azure 儲存體。 同時也控點的推入安裝行動服務受保護的電腦，並執行 VMware Vm 的自動探索。
    - **主要目標伺服器**︰ 在 [從 Azure 回復時處理複寫資料。
    您也可以部署作為程序伺服器，以調整您的部署中管理伺服器。
- **行動服務**︰ 這個元件部署在您想要複製到 Azure 每一台電腦 （VMware VM 或實體伺服器）。 它會擷取資料寫入在電腦上的，並轉寄給程序伺服器。
- **Azure**︰ 您不需要建立處理複寫和容錯移轉任何 Azure Vm。 網站復原服務處理資料管理並直接到 Azure 儲存體中複製資料。 複製的 Azure Vm 會開始自動只容錯移轉至 Azure 發生時。 不過，如果您想要的內部部署網站無法從 Azure 您必須設定為程序伺服器 Azure VM。


圖形會顯示這些元件互動的方式。

![架構](./media/site-recovery-vmware-to-azure/v2a-architecture-henry.png)

**圖 1: VMware/實體至 Azure**（由於 Robalino 所建立）


## <a name="capacity-planning"></a>容量計劃

當您正規劃容量，以下的必須考量下列事項︰

- **來源環境**— 容量規劃或 VMware 基礎架構與來源電腦需求。
- **管理伺服器**︰ 規劃網站復原元件執行的內部部署管理伺服器。
- **從來源至目標的網路頻寬**-複寫來源和 Azure 之間所需的網路頻寬規劃

### <a name="source-environment-considerations"></a>來源環境的考量因素

- **每日最大值變更工資率**，受保護的電腦可以只使用一個程序伺服器，而且單一程序伺服器可以處理最多 2 TB 的每日的資料變更。 因此 2 TB 是最大的每日資料變更受保護的電腦支援的工資率。
- **最大值處理量**，複製的電腦可以屬於 Azure 中的一個儲存帳戶。 標準儲存帳戶可以處理 20000 秒的要求，最大值，我們建議您 20000 保留 IOPS 來源電腦上的數目。 範例如果您有 5 磁碟與每個磁碟的來源機器產生 120 IOPS （8 K 大小） 來源上的然後就會在每一磁碟 IOPS 限制的 500 Azure。 數字的儲存空間帳戶所需 = 總來源 IOPs/20000。


### <a name="management-server-considerations"></a>管理伺服器考量事項

管理伺服器執行處理資料最佳化、 複製及管理網站復原元件。 它應會處理過所有受保護的電腦上執行的工作負載的每日變更工資率容量，而且有足夠的頻寬持續至 Azure 儲存體中複製資料。 特別是︰

- 程序伺服器接收複寫資料從受保護的電腦，並將它調整快取、 壓縮和加密傳送 Azure 之前，先使用。 管理伺服器應該有足夠的資源來執行這些工作。
- 程序伺服器使用基礎的磁碟快取。 我們建議您 600 GB 或更多處理網路瓶頸或資料時所儲存的資料變更為不同的快取磁碟。 在部署您可以設定快取任何至少 5 gb 的可用儲存空間的磁碟機上但 600 GB 的最小的建議。
- 最佳作法是我們建議您在相同的網路和 LAN 區段上找到管理伺服器，為您想要保護的電腦。 位於不同的網路，但是您想要保護的電腦應該已以便 L3 網路可見性。

下表摘要列出大小建議管理伺服器。

**管理伺服器 CPU** | **記憶體** | **快取磁碟大小** | **資料變更率** | **受保護的電腦**
--- | --- | --- | --- | ---
8 vCPUs (通訊端 2 * 4 個核心@2.5 GHz) | 16 GB | 300 GB | 500 GB 或更少 | 部署管理伺服器使用這些設定，以複製小於 100 的電腦。
12 vCPUs (2 通訊端 * 6 核心@2.5 GHz) | 18 GB | 600 GB | 500 GB，以 1 TB | 部署 100 150 電腦之間複製的這些設定與管理伺服器。
16 vCPUs (2 通訊端 * 8 個核心@2.5 GHz) | 32 GB | 1 TB | 若要 2 TB 的 1 TB | 部署 150 200 電腦之間複製的這些設定與管理伺服器。
部署另一個處理程序伺服器 | | | > 2 TB | 如果您複製超過 200 封機器，或是每日的資料變更工資率超過 2 TB，請部署其他程序伺服器。

位置︰

- 每個來源機器設定使用 3 個磁碟 100 gb。
- 我們使用這樣的儲存空間的 10 個 K 轉速 8 SA 磁碟機 RAID 10 的快取磁碟度量單位。

### <a name="network-bandwidth-from-source-to-target"></a>從來源至目標的網路頻寬
請確定您計算的頻寬，則需要初始複寫和差異複寫使用[容量規劃工具](site-recovery-capacity-planner.md)

#### <a name="throttling-bandwidth-used-for-replication"></a>節流頻寬用於複寫

複製到 Azure VMware 流量移到特定的程序伺服器。 您可以節流的頻寬可供網站復原複寫該伺服器上，如下所示︰

1. 開啟 Microsoft Azure 備份 MMC 嵌入式管理單元主管理伺服器上，或執行其他的管理伺服器上提供的程序伺服器。 根據預設捷徑的 Microsoft Azure 備份會建立在桌面上，或您可以找到中︰ C:\Program Files\Microsoft Azure 復原服務 Agent\bin\wabadmin。
2. 在嵌入式管理單元中按一下 [**變更內容**]。

    ![節流頻寬](./media/site-recovery-vmware-to-azure-classic/throttle1.png)

3. 在 [ **Throttling** ] 索引標籤上指定的頻寬，可以用於網站復原複寫及適用的排程。

    ![節流頻寬](./media/site-recovery-vmware-to-azure-classic/throttle2.png)

或者您也可以設定節流使用 PowerShell。 以下是範例︰

    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth (512*1024) -NonWorkHourBandwidth (2048*1024)

#### <a name="maximizing-bandwidth-usage"></a>最大化頻寬使用量
若要增加複寫供 Azure 網站復原的頻寬，您必須變更登錄機碼。

下列鍵控制每個的執行緒複寫磁碟複寫時所使用的數字

    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM

 在 「 overprovisioned 」 的網路中，下列登錄機碼需要變更其預設值。 我們支援 32 的最大值。  


[瞭解更多](site-recovery-capacity-planner.md)有關規劃詳細的容量。

### <a name="additional-process-servers"></a>其他處理程序伺服器

如果您要保護超過 200 封機器或每日變更率較大的 2 TB 您可以新增額外的伺服器處理負載。 若要不按比例縮放查看您可以︰

- 增加管理伺服器的數目。 例如，您可以保護與兩個管理伺服器最 400 機器。
- 新增額外的程序伺服器，並使用這些處理而不是 （或另外） 的流量管理伺服器。

下表描述的情況︰

- 若要將其做為僅限設定伺服器設定原始管理伺服器。
- 您設定的其他處理程序伺服器。
- 您設定以使用其他的程序伺服器受保護的虛擬機器。
- 每一個受保護的來源電腦會設定為 100 GB 的三個磁碟。

**原始的管理伺服器**<br/><br/>（設定伺服器） | **其他處理程序伺服器**| **快取磁碟大小** | **資料變更率** | **受保護的電腦**
--- | --- | --- | --- | ---
8 vCPUs (通訊端 2 * 4 個核心@2.5 GHz)，16 GB 的記憶體 | 4 vCPUs (2 通訊端 * 2 核心@2.5 GHz)，8 GB 的記憶體 | 300 GB | 250 GB 或更少 | 您可以複製 85 或更少的電腦。
8 vCPUs (通訊端 2 * 4 個核心@2.5 GHz)，16 GB 的記憶體 | 8 vCPUs (通訊端 2 * 4 個核心@2.5 GHz)，12 GB 的記憶體 | 600 GB | 250 GB，以 1 TB | 您可以複製 85 150 電腦之間。
12 vCPUs (2 通訊端 * 6 核心@2.5 GHz)，18 GB 的記憶體 | 12 vCPUs (2 通訊端 * 6 核心@2.5 GHz) 24 GB 的記憶體 | 1 TB | 若要 2 TB 的 1 TB | 您可以複製 150 225 電腦之間。


您可以在其中調整您的伺服器的方式將設定而定的小數位數您喜好設定，或擴充模型。  您不按比例縮放部署幾個高階管理及程序伺服器或擴充部署更多的伺服器，以較少的資源。 例如︰ 如果您要保護 220 機器也可以執行下列其中一項︰

- 設定與 12vCPU、 18 GB 的記憶體 12vCPU，24 GB 的記憶體，與其他處理程序伺服器的原始管理伺服器並設定受保護的電腦，若要使用其他的程序僅限伺服器。
- 或者您也可以設定兩個管理伺服器 （2 x 8vCPU，16 GB 的 RAM） 與兩個額外的程序伺服器 (x 8vCPU 1) 」 和 「 4vCPU x 1 處理 135 + 85 (220) 的電腦，並且設定受保護的電腦，若要使用其他的程序僅限伺服器。


若要設定其他程序伺服器，[請遵循以下指示](#deploy-additional-process-servers)。

## <a name="before-you-start-deployment"></a>部署開始之前

資料表摘要部署這種情況的先決條件。


### <a name="azure-prerequisites"></a>Azure 的先決條件

**必要條件** | **詳細資料**
--- | ---
**Azure 帳戶**| 您必須以[Microsoft Azure](https://azure.microsoft.com/)帳戶。 您可以開始使用[免費的試用版](https://azure.microsoft.com/pricing/free-trial/)。 [瞭解更多](https://azure.microsoft.com/pricing/details/site-recovery/)有關網站復原價格。
**Azure 儲存體** | 您必須 Azure 儲存體帳戶儲存複製的資料。 複製的資料會儲存在 Azure 儲存空間，容錯移轉發生時 Azure Vm 會開始。 <br/><br/>您需要[標準地理多餘的儲存的帳戶](../storage/storage-redundancy.md#geo-redundant-storage)。 帳戶必須是網站修復服務，為相同的區域，並會與同一份訂閱相關聯。 請注意進階版儲存帳戶複寫目前不支援和不應使用。<br/><br/>我們不支援建立跨資源群組使用[新的 Azure 入口網站](../storage/storage-create-storage-account.md)的儲存空間帳戶的移動。[閱讀相關資訊](../storage/storage-introduction.md)Azure 儲存空間。<br/><br/>
**Azure 網路** | 您必須容錯移轉發生時，將會連線到的 Azure Vm Azure 虛擬網路。 Azure 虛擬網路必須是網站復原保存庫為相同的區域。<br/><br/>請注意，失敗 Azure 失敗之後，返回您會需要 VPN 連線 （或 Azure ExpressRoute） 設定從 Azure 網路至內部部署網站。


### <a name="on-premises-prerequisites"></a>內部部署的先決條件

**必要條件** | **詳細資料**
--- | ---
**管理伺服器** | 您需要的虛擬機器或實體的伺服器上執行的內部部署 Windows 2012 R2 伺服器。 此管理伺服器上安裝所有的內部部署網站復原元件<br/><br/> 我們建議您部署高度可用 VMware VM 伺服器。 從 Azure 至內部部署網站回復為一律會以 VMware Vm，不論是否您失敗 Vm 或實體伺服器上。 如果您沒有將管理伺服器設定為 VMware VM 必須設定不同的主版目標伺服器為 VMware VM 接收回復流量。<br/><br/>伺服器不應該網域控制站。<br/><br/>伺服器應有的靜態 IP 位址。<br/><br/>伺服器主機名稱應 15 個字元小於或等於。<br/><br/>應該僅英文作業系統地區設定。<br/><br/>管理伺服器需要存取網際網路。<br/><br/>您需要從伺服器的輸出存取，如下所示︰ 暫時存取 HTTP 80 期間設定 （若要下載 MySQL） 的網站復原元件。進行中的輸出存取權 HTTPS 443 複寫管理;進行中的輸出存取權 HTTPS 9443 複寫流量 （可以修改這個連接埠）<br/><br/> 請確定這些 Url 就可以從存取管理伺服器︰ <br/>- \*。 hypervrecoverymanager.windowsazure.com<br/>- \*。 accesscontrol.windows.net<br/>- \*。 backup.windowsazure.com<br/>- \*。 blob.core.windows.net<br/>- \*。 store.core.windows.net<br/>-https://www.msftncsi.com/ncsi.txt<br/>- [https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi]( https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi " https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi")<br/><br/>如果您在伺服器上有 IP 位址的防火牆規則，請核取規則，允許 Azure 通訊。 您必須允許[Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)和 HTTPS (443) 連接埠。 您也需要白色清單的 IP 位址範圍 Azure 區域，您的訂閱，與西美國。 URL [https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi](https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi " https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi")適用於下載 MySQL。
**VMware vCenter/ESXi host （主機）**︰ | 您需要一個或多個 vMware vSphere ESX/ESXi hypervisors 管理您的 VMware 虛擬機器執行 ESX ESXi 版本 6.0、 5.5 或 5.1 最新的更新。<br/><br/> 我們建議您部署管理您的 ESXi 主機 VMware vCenter 伺服器。 它應該執行 vCenter 版本 6.0 或 5.5 最新的更新。<br/><br/>請注意，網站復原不支援新 vCenter vSphere 6.0 功能，例如交互 vCenter vMotion 虛擬磁碟區與儲存 DRS。 網站復原支援僅限於也是版本 5.5 提供的功能。
**受保護的電腦**︰ | **AZURE**<br/><br/>您想要保護的電腦應符合建立 Azure Vm [Azure 的先決條件](site-recovery-best-practices.md#azure-virtual-machine-requirements)。<br><br/>如果您要連線至 Azure Vm 後移轉後會需要啟用遠端桌面連線在本機的防火牆上。<br/><br/>在受保護的電腦上的個別磁碟容量應該不超過 1023 GB。 VM 可以有最多 64 磁碟 (因此最多 64 TB)。 如果您有大於 1 TB 的磁碟請考慮使用資料庫複寫，例如 SQL Server 永遠或 Oracle 資料保護。<br/><br/>最小 2 GB 的可用空間安裝元件安裝磁碟機上。<br/><br/>共用磁碟來賓叢集不受支援。 如果您有直的部署，請考慮使用資料庫複寫，例如 SQL Server 永遠或 Oracle 資料保護。<br/><br/>整合可延伸韌體介面 (UEFI) / 可延伸韌體 Interface(EFI) 開機不受支援。<br/><br/>電腦名稱應介於 1 到 63 個字元 （字母、 數字和連字號）。 名稱必須字母或數字的開頭和結尾字母或數字。 受到保護的電腦之後，您可以修改 Azure 的名稱。<br/><br/>**VMware Vm**<br/><br>您需要安裝 VMware vSphere PowerCLI 6.0。 管理伺服器 （設定伺服器）。<br/><br/>您想要保護的 VMware Vm 應有 VMware 工具安裝及執行。<br/><br/>如果來源 VM NIC 小組 Azure 失敗之後會將其轉換成單一 NIC。<br/><br/>如果受保護的 Vm iSCSI 磁碟然後網站復原轉換受保護的 VM iSCSI 磁碟 VHD 檔案 VM 無法透過 Azure 時。 如果可以連線 Azure VM iSCSI 目標然後會連線到 iSCSI 目標並基本上看到兩個磁碟 – VHD 磁碟上 Azure VM 及來源 iSCSI 磁碟。 在此情況下必須中斷連線會出現在失敗，透過 Azure VM iSCSI 目標。<br/><br/>[瞭解更多](#vmware-permissions-for-vcenter-access)有關所需的網站復原 VMware 使用者權限。<br/><br/> **WINDOWS 伺服器電腦 （在 VMware VM 或實體伺服器）**<br/><br/>伺服器應該執行支援的 64 位元作業系統︰ Windows Server 2012 R2、 Windows Server 2012 或 Windows Server 2008 R2，至少 SP1。<br/><br/>作業系統應該 C:\ 磁碟機上安裝和 OS 磁碟應該 Windows 基本磁碟 （OS 不應該安裝 Windows 動態磁碟）。<br/><br/>您必須有.NET Framework 3.5.1 安裝 Windows Server 2008 R2 機器。<br/><br/>您必須提供系統管理員帳戶 （必須是在 Windows 電腦上的本機管理員） 的推入安裝在 Windows 伺服器上行動服務。 如果所提供的帳戶必須先停用遠端使用者存取控制本機電腦上的非網域帳戶。 [進一步瞭解](#install-the-mobility-service-with-push-installation)。<br/><br/>網站復原支援 Vm RDM 磁碟。  期間回復網站復原會重複使用 RDM 磁碟，如果使用原始的來源 VM 和 RDM 磁碟。 如果他們無法使用，期間回復上的網站復原會建立新的 VMDK 檔案，每個磁碟。<br/><br/>**LINUX 電腦**<br/><br/>您必須是受支援的 64 位元作業系統︰ 紅色角色企業 Linux 6.7;Centos 6.5、 6.6,6.7;Oracle 企業 Linux 6.4，執行 「 紅色角色相容核心 」 或 「 永不折損企業核心第 3 版 (UEK3)，SUSE Linux Enterprise Server 11 SP3 6.5。<br/><br/>在受保護的電腦上的 /etc/hosts 檔案必須包含本機主機名稱對應到所有的網路介面卡與相關聯的 IP 位址的項目。 <br/><br/>如果您要連線至執行後使用安全命令介面的用戶端 (ssh) 的移轉後的 Linux Azure 虛擬機器，確定受保護的電腦上的安全命令介面服務，設定為自動開始系統開機，及防火牆規則，允許 ssh 連線。<br/><br/>只使用下列的儲存空間 Linux 電腦啟用保護︰ 檔案系統 （EXT3 ETX4、 ReiserFS、 XFS）;多重路徑軟體裝置 （多重路徑） 的對應程式）。大量管理員: (LVM2)。 不支援使用 HP CCISS 控制器儲存空間的實體伺服器。 只有 SUSE Linux Enterprise Server 11 SP3 支援 ReiserFS 檔案系統。<br/><br/>網站復原支援 Vm RDM 磁碟。  期間的 Linux 回復，網站復原不重複使用 RDM 磁碟。 請改為它會建立新的 VMDK 檔案，每個對應 RDM 磁碟。

僅適用於 Linux VM-請確保您設定的 VMware 中 VM 設定參數 disk.enableUUID=true 設定。 如果此列不存在，請將它加入。 這被需要 VMDK 提供一致的 UUID，使其裝載正確。 另請注意，這項設定，不回復會導致完整下載即使 VM 上 prem.可用 新增此設定可確保差異的變更只會傳送回期間回復。

## <a name="step-1-create-a-vault"></a>步驟 1︰ 建立保存庫

1. [管理入口網站](https://manage.windowsazure.com/)登入。
2. 展開**資料服務** > **修復服務**，按一下 [**網站復原保存庫**。
3. 按一下 [**建立新** > **快速建立**。
4. 在 [**名稱**] 中，輸入好記的名稱來識別保存庫。
5. 在 [**地區**中，選取 [保存庫的地理區域。 若要檢查支援的地區請參閱[Azure 網站復原定價詳細資料](https://azure.microsoft.com/pricing/details/site-recovery/)中的地理可用性
6. 按一下 [**建立保存庫**]。
    ![新的保存庫](./media/site-recovery-vmware-to-azure-classic/quick-start-create-vault.png)

請確認已成功建立保存庫狀態列。 保存庫會列為**作用中**主要的 [**復原服務**] 頁面上。

## <a name="step-2-set-up-an-azure-network"></a>步驟 2︰ 設定 Azure 網路

設定 Azure 的網路，以便 Azure Vm 會連線到網路後移轉後，並讓內部部署網站回復可以在如預期般運作。

1. Azure 入口網站中 >**建立虛擬網路**指定網路名稱。 IP 位址範圍及子網路名稱。
2. 您必須要有 vpn 才能/ExpressRoute 新增至網路，如果您需要執行回復。 VPN/ExpressRoute 可新增至 [網路，甚至後移轉後。

[閱讀更多](../virtual-network/virtual-networks-overview.md)關於 Azure 網路資訊。

> [AZURE.NOTE] 用於部署網站復原的網路不支援[的網路移轉](../resource-group-move-resources.md)跨資源群組內的相同的訂閱或訂閱。

## <a name="step-3-install-the-vmware-components"></a>步驟 3︰ 安裝 VMware 元件

如果您想要複製 VMware 虛擬機器會在 [管理伺服器上安裝下列 VMware 元件︰

1. [下載](https://developercenter.vmware.com/tool/vsphere_powercli/6.0)並安裝 VMware vSphere PowerCLI 6.0。
2. 重新啟動伺服器。


## <a name="step-4-download-a-vault-registration-key"></a>步驟 4︰ 下載保存庫登錄機碼

1. 從管理伺服器，請在 [Azure 中開啟網站修復主控台。 在 [**復原服務**] 頁面按一下 [保存庫，以開啟 [快速入門] 頁面。 也可以隨時使用圖示開啟快速入門。

    ![快速入門] 圖示](./media/site-recovery-vmware-to-azure-classic/quick-start-icon.png)

2. 在 [**快速入門**] 頁面上按一下 [**準備目標資源** > **下載登錄機碼**。 自動產生的登錄檔案。 5 天產生後就有效。


## <a name="step-5-install-the-management-server"></a>步驟 5︰ 安裝管理伺服器
> [AZURE.TIP] 請確定這些 Url 就可以從存取管理伺服器︰
>
- *。 hypervrecoverymanager.windowsazure.com
- *。 accesscontrol.windows.net
- *。 backup.windowsazure.com
- *。 blob.core.windows.net
- *。 store.core.windows.net
- https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi
- https://www.msftncsi.com/ncsi.txt




[AZURE.VIDEO enhanced-vmware-to-azure-setup-registration]

1. 在 [**快速入門**] 頁面上將整合的安裝檔案下載到伺服器。

2. 執行安裝檔網站復原整合安裝精靈中啟動安裝程式。

3.  在**開始之前**選取**安裝設定伺服器及程序伺服器**。

    ![在您開始之前](./media/site-recovery-vmware-to-azure-classic/combined-wiz1.png)
4. 在**第三方軟體授權**按一下 [**我接受**下載並安裝 MySQL]。 

    ![第三個 = 廠商的軟體](./media/site-recovery-vmware-to-azure-classic/combined-wiz105.PNG)

5. 在**登錄**中瀏覽並選取您從保存庫下載的登錄機碼。

    ![註冊](./media/site-recovery-vmware-to-azure-classic/combined-wiz3.png)

6. **網際網路設定**中指定設定伺服器上執行的提供者連線至 Azure 網站復原網際網路的方式。

    - 如果您想要與目前在電腦設定 proxy 連線選取**現有的 proxy 設定的連線**。
    - 如果您想要直接連接的提供者選取**直接不 proxy 的連線**。
    - 如果現有 proxy 需要驗證]，或您想要使用自訂的 proxy 提供者連線，請選取 [**使用自訂的 proxy 設定的連線**]。
        - 如果您是使用自訂 proxy 必須先指定的地址、 連接埠和認證
        - 如果您使用 proxy，您應該已經允許下列 Url:
            - *。 hypervrecoverymanager.windowsazure.com;    
            - *。 accesscontrol.windows.net; 
            - *。 backup.windowsazure.com; 
            - *。 blob.core.windows.net; 
            - *。 store.core.windows.net
            

    ![防火牆](./media/site-recovery-vmware-to-azure-classic/combined-wiz4.png)

7. 在**核取 [先決條件**設定會執行檢查，確定可以執行安裝。 

    
    ![必要條件](./media/site-recovery-vmware-to-azure-classic/combined-wiz5.png)

     如果相關的**核取 [全域時間同步處理**，會出現警告確認的系統時鐘 （**日期和時間**設定） 上的時間時區相同。

    ![TimeSyncIssue](./media/site-recovery-vmware-to-azure-classic/time-sync-issue.png)

8. **MySQL 設定**中建立認證登入將會安裝的 MySQL 伺服器執行個體。

    ![MySQL](./media/site-recovery-vmware-to-azure-classic/combined-wiz6.png)

9. 在**環境的詳細資料**選取是否要複製 VMware Vm。 如果您是，安裝程式會檢查已安裝 PowerCLI 6.0。

    ![MySQL](./media/site-recovery-vmware-to-azure-classic/combined-wiz7.png)

10. 在**安裝位置**選取您要安裝的二進位檔案及儲存快取的位置。 您可以選取具有至少 5 GB 的可用儲存空間的磁碟機，但我們建議您至少 600 GB 的可用空間的快取磁碟機。

    ![安裝位置](./media/site-recovery-vmware-to-azure-classic/combined-wiz8.png)

11. 在**網路選取項目**指定的接聽 （網路介面卡和 SSL 連接埠） 設定伺服器會傳送及接收複寫資料。 您可以修改預設連接埠 (9443)。 此連接埠，除了網頁伺服器的協調複寫作業要使用連接埠 443。 不應使用 443 接收複寫流量。


    ![網路選取項目](./media/site-recovery-vmware-to-azure-classic/combined-wiz9.png)



12.  **摘要**中檢閱資訊，然後按一下 [**安裝**]。 安裝完成後，就會產生複雜密碼。 您必須將其當您啟用複寫時，請將其複製並將它維持在安全的位置。

    ![摘要](./media/site-recovery-vmware-to-azure-classic/combined-wiz10.png)



13.  **摘要**中檢閱資訊。

    ![摘要](./media/site-recovery-vmware-to-azure-classic/combined-wiz10.png)

>[AZURE.WARNING]Microsoft Azure 復原服務專員通話的 proxy 必須安裝程式。
>安裝完成後啟動應用程式名稱為 「 Microsoft Azure 復原服務命令介面 」，從 Windows [開始] 功能表。 在開啟的 [命令] 視窗中執行下列命令以設定 proxy 伺服器設定的集合。
>
    $pwd = ConvertTo-SecureString -String ProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumb – ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine



### <a name="run-setup-from-the-command-line"></a>從命令列執行安裝程式

您也可以執行整合的精靈從命令列中，如下所示︰

    UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

位置︰

- / ServerMode︰ 強制。 指定安裝是否應該安裝的設定和程序伺服器或僅限伺服器程序 （用來安裝其他程序伺服器）。 輸入的值︰ CS PS.
- InstallDrive︰ 強制。 指定元件的安裝位置的資料夾。
- / MySQLCredFilePath。 強制性。 指定 MySQL 伺服器認證的故事所在之檔案的路徑。 取得要建立檔案的範本。
- / VaultCredFilePath。 強制性。 保存庫認證檔案的位置
- / EnvType。 強制性。 安裝類型。 值︰ VMware NonVMware
- / PSIP 和 /CSIP。 強制性。 在程序伺服器和設定伺服器 IP 位址。
- / PassphraseFilePath。 強制性。 複雜密碼檔案的位置。
- / ByPassProxy。 選用。 指定管理伺服器連線至 Azure 不 proxy。
- / ProxySettingsFilePath。 選用。 指定自訂 proxy (預設 proxy 伺服器需要驗證] 上,)，或是自訂的 proxy 設定




## <a name="step-6-set-up-credentials-for-the-vcenter-server"></a>步驟 6︰ 設定 vCenter 伺服器的認證

> [AZURE.VIDEO enhanced-vmware-to-azure-discovery]

程序伺服器才能自動探索 vCenter 伺服器管理的 VMware Vm。 自動探索網站復原需要帳戶與可以存取 vCenter 伺服器的認證。 這不是如果您複製實體僅限伺服器。

執行下列步驟，如下所示︰

1. 上 vCenter 伺服器建立 vCenter 層級具有[必要的權限](#vmware-permissions-for-vcenter-access)的角色 (**Azure_Site_Recovery**)。
2. **Azure_Site_Recovery**角色指派給 vCenter 使用者。

    >[AZURE.NOTE] 有 「 唯讀 」 角色的使用者帳戶 vCenter 可以執行容錯移轉，而不關閉受保護的來源機器。 如果您想要關閉這些機器必須 Azure_Site_Recovery 角色。 請注意，如果您只有移轉 Vm 從 VMware 至 Azure，不需要回復然後唯讀角色足夠。

3. 若要新增的帳戶，請開啟**cspsconfigtool**。 在 [安裝位置] \home\svsystems\bin 資料夾中是可做為快速鍵在桌面和位置。
2. 在 [**管理帳戶**] 索引標籤中，按一下 [**新增帳戶**。

    ![新增帳戶](./media/site-recovery-vmware-to-azure-classic/credentials1.png)

3. **帳戶詳細資料**中新增可存取 vCenter 伺服器的認證。 請注意，可能需要超過 15 分鐘入口網站中所顯示的帳戶名稱。 若要立即更新，請按一下 [重新整理**設定伺服器**] 索引標籤上。

    ![詳細資料](./media/site-recovery-vmware-to-azure-classic/credentials2.png)

## <a name="step-7-add-vcenter-servers-and-esxi-hosts"></a>步驟 7︰ 新增 vCenter 伺服器及 ESXi 主機

如果您複製 VMware Vm 您需要新增 vCenter 伺服器 （或 ESXi 主機）。

1. 在**伺服器**上 > **設定伺服器**] 索引標籤上，選取 [設定伺服器 >**新增 vCenter 伺服器**。

    ![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter1.png)

2. 新增 vCenter 伺服器或 ESXi 主機詳細資料，您指定要存取 vCenter 伺服器在先前的步驟，將用來探索 VMware Vm vCenter 伺服器所管理的程序伺服器帳戶的名稱。 請注意，vCenter 伺服器或 ESXi 主機應該位於相同的網路安裝程序 server 的伺服器。

    >[AZURE.NOTE] 如果您是要新增 vCenter 伺服器或 ESXi 主機 vCenter 或 host （主機） 伺服器上沒有系統管理員權限的帳戶，然後確定 vCenter 或 ESXi 帳戶有啟用這些權限︰ 資料中心、 資料存放區、 資料夾、 Jost、 網路、 資源、 虛擬機器，vSphere 分散對齊] 切換。 另外 vCenter 伺服器需要的儲存空間檢視權限。

    ![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter2.png)

3. 探索完成後 vCenter 伺服器會列在 [**設定伺服器**] 索引標籤。

    ![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter3.png)


## <a name="step-8-create-a-protection-group"></a>步驟 8︰ 建立保護群組

> [AZURE.VIDEO enhanced-vmware-to-azure-protection]


保護群組是邏輯群組的虛擬機器或您想要保護使用相同的保護設定的實體伺服器。 您在 [保護] 群組中，以套用保護設定，這些設定會套用至所有您新增到群組的虛擬機器/實體機器。

1. 開啟**受保護的項目** > **保護] 群組**，並按一下 [新增保護群組。

    ![建立 [保護] 群組](./media/site-recovery-vmware-to-azure-classic/protection-groups1.png)

2. 在 [**指定保護群組設定**] 頁面上指定群組名稱，並在 [**從**選取您要建立群組設定伺服器。 **目標**是 Azure。

    ![保護群組設定](./media/site-recovery-vmware-to-azure-classic/protection-groups2.png)

3. 在 [**指定複寫設定**] 頁面上設定複寫用於群組中的所有電腦的。

    ![保護群組複寫](./media/site-recovery-vmware-to-azure-classic/protection-groups3.png)

    - **多重 VM 一致性**︰ 如果您開啟此 [保護] 群組中的電腦上建立共用的應用程式一致復原點。 所有的 [保護] 群組中的電腦執行的相同的工作負載時，最相關這項設定。 所有的電腦將會還原至相同的資料點。 這是提供您複製 VMware Vm 或 Windows/Linux 實體伺服器。
    - **RPO 閥值**︰ 設定 RPO。 連續資料保護複寫超過設定的 RPO 臨界值時，將會產生提醒。
    - **復原點保留**︰ 指定 [保留] 視窗。 受保護的電腦可以復原此視窗中的任何位置。
    - **應用程式一致的快照頻率**︰ 指定會建立包含應用程式一致的快照的復原點的頻率。

當您按一下核取記號保護群組將會建立您所指定的名稱。 第二個 [保護] 群組名稱的建立 in addition < 保護] 群組的名稱回復)。 如果您無法在回內部部署網站 Azure 失敗之後，會使用此 [保護] 群組。 這些是建立 [**受保護的項目**] 頁面上，您可以監視保護群組。

## <a name="step-9-install-the-mobility-service"></a>步驟 9︰ 安裝行動服務

啟用保護虛擬機器及實體伺服器的第一個步驟是安裝行動服務。 您可以透過以下兩種方法︰

- 自動推入，並從程序伺服器的每一部電腦上安裝的服務。 請注意，當您新增至 [保護] 群組的電腦，並已經執行適當行動服務推入安裝的版本就不會發生。
- 使用您的企業推入方法，例如 WSUS] 或 [系統管理中心設定管理員的服務，自動安裝。 請確定您已進行之前請先將管理伺服器設定。
- 手動安裝在每個您想要保護的電腦上。 設確定您已設定管理伺服器進行之前請先。


### <a name="install-the-mobility-service-with-push-installation"></a>推入安裝行動服務

當您新增至 [保護] 群組的機器行動服務自動推入，每一部電腦上安裝程序伺服器。


#### <a name="prepare-for-automatic-push-on-windows-machines"></a>在 Windows 電腦上的自動推入準備

以下說明如何讓行動服務自動安裝程序 server 準備 Windows 的電腦。

1.  建立可用於程序伺服器存取電腦的帳戶。 帳戶應該具有管理員權限 （本機或的網域）。 請注意的推入安裝行動服務只會使用這些認證。

    >[AZURE.NOTE] 如果您不使用的網域帳戶必須停用遠端使用者存取本機電腦上的控制項。 若要這麼做下 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System, register 新增 DWORD 項目 LocalAccountTokenFilterPolicy 值為 1 底下。 若要新增登錄項目從 CLI 開啟] 命令，或使用 PowerShell 輸入**`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**。

2.  在 Windows 防火牆的電腦上您想要保護，請選取 [**允許應用程式或功能通過防火牆**啟用**檔案及印表機共用**] 和 [ **Windows 管理儀器**。 屬於網域的電腦中，您可以設定防火牆原則 gpo。

    ![防火牆設定](./media/site-recovery-vmware-to-azure-classic/mobility1.png)

2. 新增您建立的帳戶︰

    - 開啟**cspsconfigtool**。 在 [安裝位置] \home\svsystems\bin 資料夾中是可做為快速鍵在桌面和位置。
    - 在 [**管理帳戶**] 索引標籤中，按一下 [**新增帳戶**。
    - 新增您建立的帳戶。 新增帳戶後，您需要提供的認證，當您新增至 [保護] 群組的電腦。


#### <a name="prepare-for-automatic-push-on-linux-servers"></a>準備 Linux 伺服器上的自動推入

1.  請確定您想要保護的 Linux 電腦支援[內部部署的先決條件](#on-premises-prerequisites)所述。 確保您想要保護的電腦之間沒有網路連線能力，並管理伺服器的執行程序伺服器。

2.  建立可用於程序伺服器存取電腦的帳戶。 帳戶應該根使用者來源 Linux 伺服器上。 請注意的推入安裝行動服務只會使用這些認證。

    - 開啟**cspsconfigtool**。 在 [安裝位置] \home\svsystems\bin 資料夾中是可做為快速鍵在桌面和位置。
    - 在 [**管理帳戶**] 索引標籤中，按一下 [**新增帳戶**。
    - 新增您建立的帳戶。 新增帳戶後，您需要提供的認證，當您新增至 [保護] 群組的電腦。

3.  核取 /etc/hosts 伺服器上的檔案來源 Linux 包含將本機的主機名稱對應到所有的網路介面卡與相關聯的 IP 位址的項目。
4.  在您想要保護的電腦上安裝最新的 openssh openssh 伺服器、 openssl 套件。
5.  請確定 SSH 連接埠 22 上是啟用並執行。
6.  啟用 SFTP 子系統及密碼驗證 sshd_config 檔案中的，如下所示︰

    - 為根登入。
    - 在檔案 /etc/ssh/sshd_config 檔案中，尋找開頭 PasswordAuthentication 的線條。
    - 取消行註解，然後從**沒有**的值變更為 [**是]**。
    - 尋找開頭**子系統**的線條，然後取消行註解]。

        ![Linux](./media/site-recovery-vmware-to-azure-classic/mobility2.png)


### <a name="install-the-mobility-service-manually"></a>手動安裝行動服務

安裝程式可在 C:\Program Files (x86) \Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository。

來源作業系統 | 行動服務安裝檔案
--- | ---
Windows 伺服器 （僅限 64 位元） | Microsoft ASR_UA_9。*.0.0_Windows_* release.exe
CentOS 6.4，6.5，6.6 （僅限 64 位元） | Microsoft ASR_UA_9。*.0.0_RHEL6-64_*release.tar.gz
SUSE Linux Enterprise Server 11 SP3 （僅限 64 位元） | Microsoft ASR_UA_9。*.0.0_SLES11-SP3-64_*release.tar.gz
Oracle 企業 Linux 6.4，6.5 （僅限 64 位元） | Microsoft ASR_UA_9。*.0.0_OL6-64_*release.tar.gz


#### <a name="install-manually-on-a-windows-server"></a>手動安裝在 Windows server 上


1. 下載並執行相關的安裝程式。
2. 在**您開始之前，**選取 [**行動服務**]。

    ![行動服務](./media/site-recovery-vmware-to-azure-classic/mobility3.png)

3. **設定伺服器詳細資料**中指定管理伺服器，當您安裝的管理伺服器元件時所產生的複雜密碼的 IP 位址。 您可以藉由執行擷取複雜密碼︰ ** <SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe-n**管理伺服器上。

    ![行動服務](./media/site-recovery-vmware-to-azure-classic/mobility6.png)

4. 在**安裝位置**保留預設位置，然後按 [**下一步**開始安裝。
5. **安裝過程**中監控安裝，然後如果出現提示，請重新啟動電腦。

您也可以從命令列執行安裝︰

UnifiedAgent.exe [/ 角色 < 代理程式/MasterTarget >] [/ InstallLocation <Installation Directory>] [/ CSIP <IP address of CS to be registered with>] [/ PassphraseFilePath <Passphrase file path>] [/ LogFilePath <Log File Path>]

位置︰

- / 角色︰ 強制。 指定是否要安裝行動服務。
- / InstallLocation︰ 強制。 指定安裝該服務的位置。
- / PassphraseFilePath︰ 強制。 指定設定伺服器複雜密碼。
- / LogFilePath︰ 強制。 指定記錄設定檔位置

#### <a name="uninstall-mobility-service-manually"></a>手動解除安裝行動服務

使用 [新增移除程式從 [控制台]，或使用命令列，可以解除安裝行動服務。

若要解除安裝行動服務命令列] 命令

    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1}

#### <a name="modify-the-ip-address-of-the-management-server"></a>修改管理伺服器的 IP 位址

執行精靈之後您可以修改管理伺服器的 IP 位址，如下所示︰

1. 開啟檔案 hostconfig.exe （位於 「 桌面 」）。
2. 在 [**全域**] 索引標籤上，您可以變更管理伺服器的 IP 位址。

    >[AZURE.NOTE] 您應該只會變更管理伺服器的 IP 位址。 管理伺服器通訊的連接埠號碼 443，而使用 HTTPS 應保持啟用。 不應修改複雜密碼。

    ![管理伺服器 IP 位址](./media/site-recovery-vmware-to-azure-classic/host-config.png)

#### <a name="install-manually-on-a-linux-server"></a>手動安裝 Linux 伺服器上︰

1. 複製到您想要保護 Linux 電腦上方資料表為基礎的適當 tar 封存。
2. 開啟命令介面程式，並執行擷取壓縮的 tar 封存的本機路徑︰`tar -xvzf Microsoft-ASR_UA_8.5.0.0*`
3. Passphrase.txt 檔案的目錄中建立本機解壓縮 tar 封存的內容。 若要執行此複製複雜密碼 C:\ProgramData\Microsoft Azure 網站 Recovery\private\connection.passphrase 在管理伺服器上，並將其儲存 passphrase.txt 中執行*`echo <passphrase> >passphrase.txt`*於命令介面。
4. 輸入以安裝行動服務*`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*。
5. 指定管理伺服器的內部 IP 位址，並確定已選取 [連接埠 443。

**您也可以從命令列安裝**︰

1. 複製 C:\Program Files (x86) \InMage Systems\private\connection 管理伺服器上的複雜密碼，然後將其儲存為 「 passphrase.txt 」 中管理伺服器上。 然後，請執行這些命令。 在此範例中的管理伺服器 IP 位址 104.40.75.37 並 HTTPS 連接埠應該 443:

若要在實際執行伺服器上安裝︰

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

在 [主要目標伺服器上安裝︰


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt


## <a name="step-10-enable-protection-for-a-machine"></a>步驟 10︰ 啟用機器的保護

若要啟用保護您的保護群組新增虛擬機器和實體伺服器。 在開始之前，請注意下列如果您保護 VMware 虛擬機器︰

- VMware Vm 發現每 15 分鐘，可能需要超過 15 分鐘才會顯示在網站復原入口網站後探索。
- 虛擬機器 （例如 VMware 工具安裝） 的環境變更可能也需要更新網站修復超過 15 分鐘。
- 您可以檢查在**最後一個連絡人在**欄位中的 VMware Vm 發現上次 vCenter 伺服器/ESXi 主機**設定伺服器**] 索引標籤上。
- 如果您已經建立 [保護] 群組，並將 vCenter 伺服器或 ESXi 主機新增之後，可能需要超過 15 分鐘重新整理 Azure 網站復原入口網站，以及**新增電腦，保護群組**] 對話方塊中列出的虛擬機器。
- 如果您想要繼續立即加入不會排程探索等候的 [保護] 群組中的電腦，醒目提示設定伺服器 （但不要按一下），按一下 [**重新整理**] 按鈕。

此外請注意︰

- 我們建議您設計保護群組，讓他們反映您的工作量。 例如加入執行相同的群組特定應用程式的電腦。
- 當您新增的電腦，保護群組時，程序伺服器自動推入，並安裝行動服務，如果其尚未安裝。 請注意，您必須準備上一個步驟所述的推入機制。


新增電腦，保護群組︰

1. 按一下 [**受保護的項目** > **保護群組** > **機器**> 加入電腦。 \As 的最佳作法
2. **選取虛擬機器**中如果您保護 VMware 虛擬機器中，選取 [管理您的虛擬機器 （或 EXSi 主機正在執行），vCenter 伺服器，，然後選取 [機器。

    ![啟用保護](./media/site-recovery-vmware-to-azure-classic/enable-protection2.png)

3.  **選取虛擬機器**中如果您要保護的實際的伺服器，在**新增的實體機器**精靈提供的 IP 位址和好記的名稱。 然後選取 [作業系統系列]。

    ![啟用保護](./media/site-recovery-vmware-to-azure-classic/enable-protection1.png)

4. 在**指定目標資源**選取儲存您的帳戶使用複寫的選擇是否要設定適用於所有工作負載均。 請注意，目前不支援進階版儲存的帳戶。

    >[AZURE.NOTE] 1.我們不支援建立跨資源群組使用[新的 Azure 入口網站](../storage/storage-create-storage-account.md)的儲存空間帳戶的移動。                           2。 儲存帳戶的部署網站復原不支援[的儲存空間帳戶移轉](../resource-group-move-resources.md)跨資源群組內的相同的訂閱或訂閱。

    ![啟用保護](./media/site-recovery-vmware-to-azure-classic/enable-protection3.png)

5. **指定帳戶**中選取的帳戶您[設定](#install-the-mobility-service-with-push-installation)自動安裝行動服務使用。

    ![啟用保護](./media/site-recovery-vmware-to-azure-classic/enable-protection4.png)

6. 按一下 [核取記號，完成新增至 [保護] 群組的電腦，並啟動初始複寫每一部電腦]。

    >[AZURE.NOTE] 如果您已準備推入安裝行動服務自動安裝沒有，它們是新增至 [保護] 群組的電腦上。 服務安裝後保護工作啟動，並會失敗。 失敗之後必須手動重新啟動每個的電腦已安裝的行動服務。 重新啟動之後保護工作開始一次，並會初始複寫。

您可以監視在 [**工作**] 頁面上的狀態。

![啟用保護](./media/site-recovery-vmware-to-azure-classic/enable-protection5.png)

此外，受保護狀態監視**受保護的項目**中 > <protection group name> > **虛擬機器**。 初始複寫完成，並同步處理資料之後，電腦狀態變更為**受保護**。

![啟用保護](./media/site-recovery-vmware-to-azure-classic/enable-protection6.png)


## <a name="step-11-set-protected-machine-properties"></a>步驟 11︰ 設定受保護電腦屬性

1. 電腦有**受保護**狀態之後，您可以設定其容錯移轉內容。 在保護] 群組詳細資料選取電腦並開啟 [**設定**] 索引標籤。
2. 網站復原自動建議 Azure vm 的內容，並偵測到內部部署網路設定。

    ![設定虛擬機器屬性](./media/site-recovery-vmware-to-azure-classic/vm-properties1.png)

3. 您可以修改這些設定︰

    -  **Azure VM 名稱**︰ 這是 Azure 中的電腦將給予後移轉後的名稱。 名稱必須遵守 Azure 需求。

    -  **Azure 虛擬記憶體大小**︰ 網路介面卡的數字的取決於您指定為目標虛擬機器的大小。 [閱讀更多](../virtual-machines/virtual-machines-linux-sizes.md/#size-tables)關於大小和介面卡。 請注意︰

        - 修改虛擬機器的大小，然後儲存的設定，當您下次開啟 [**設定**] 索引標籤時，會變更網路介面卡的號碼。 網路介面卡的目標虛擬機器中的數字是來源虛擬機器上的網路介面卡數] 及 [支援的虛擬機器選擇大小的網路介面卡的最大的數字的最小值。
            - 如果來源電腦上的網路介面卡的數字小於或等於的可容許的目標電腦大小的介面卡的數字，然後目標會有數相同的介面卡做為來源。
            - 如果來源虛擬機器介面卡數目超過允許的目標大小然後目標大小的最大值將用於的數字。
            - 例如，如果來源電腦有兩個網路介面卡，目標電腦大小支援四個目標電腦會有兩個介面卡。 如果來源電腦有兩個介面卡，但支援的目標大小僅支援其中一個目標電腦會有只有一個介面卡。
        - 如果虛擬機器具有多個網路介面卡應該所有介面卡連線到相同的 Azure 網路。
    - **Azure 網路**︰ 您必須指定 Azure Vm 會連線到後移轉後的 Azure 網路。 如果您不指定一個 Azure Vm 不會連線至網路。 此外，您需要指定 Azure 的網路，如果您想要回復 Azure 從內部部署網站。 回復需要 VPN 連線 Azure 網路與內部部署網路之間。
    - **Azure IP 位址子網路**︰ 選取每個網路介面卡 Azure VM 應該將連線的子網路。 請注意︰
        - 如果來源電腦的網路介面卡設定為使用靜態 IP 位址，您可以 Azure vm 指定的靜態 IP 位址。 如果您沒有提供的靜態 IP 位址，然後將配置任何可用的 IP 位址。 如果指定目標 IP 位址，但已由另一個 VM Azure 中使用容錯移轉將會失敗。 如果來源電腦的網路介面卡設定為使用 DHCP 您將會設定為 Azure 有 DHCP。

## <a name="step-12-create-a-recovery-plan-and-run-a-failover"></a>步驟 12︰ 建立一個修復計劃，並執行容錯移轉

> [AZURE.VIDEO enhanced-vmware-to-azure-failover]

您可以執行的單一電腦、 容錯移轉，或在多個執行相同的工作，或執行相同的工作負載的虛擬機器失敗。 在多部電腦失敗，同時將其新增至 [復原計劃。

### <a name="create-a-recovery-plan"></a>建立一個修復計劃

1. 在 [**復原方案**] 頁面上按一下 [**新增復原計劃**並新增復原計劃。 指定計劃的詳細資料，並選取目標**Azure** 。

    ![設定復原計劃](./media/site-recovery-vmware-to-azure-classic/recovery-plan1.png)

2. 在**選取的虛擬機器**中選取 [保護] 群組，然後選取機器 [若要新增至復原計劃] 群組中。

    ![加入虛擬機器](./media/site-recovery-vmware-to-azure-classic/recovery-plan2.png)

您可以自訂計劃以建立群組及序列中復原計劃機器容錯移轉的順序。 您也可以新增指令碼，並提示輸入手動的動作。 手動或使用[Azure 自動化 Runbooks](site-recovery-runbook-automation.md)，可以建立指令碼。 [瞭解更多](site-recovery-create-recovery-plans.md)關於自訂修復計劃的資訊。

## <a name="run-a-failover"></a>執行容錯移轉

在您執行容錯移轉之前請注意︰


- 請確定管理伺服器正在執行，而且-否則容錯移轉將會失敗。
- 如果您所執行的意外的容錯移轉附註︰

    - 如果可以的話您應該關閉主要電腦才能執行意外的容錯移轉。 如此一來，可確保您沒有安裝來源與複本的電腦執行一次。 如果您複製 VMware Vm 然後當您執行意外的容錯移轉您可以指定網站復原，應該都讓關閉來源機器最佳效果。 根據主站台的狀態，這可能或可能無法運作。 如果您複製實體伺服器網站復原不提供此選項。
    - 當您執行意外的容錯移轉時，開始意外的容錯移轉之後，就不會傳送任何資料 delta 會停止資料複寫從主要的電腦。

- 如果您要連線到複本中 Azure 虛擬機器後移轉後，啟用遠端桌面連線來源電腦上執行容錯移轉，並允許透過防火牆 RDP 連線之前。 您也需要允許 RDP 後移轉後的 Azure 虛擬機器公用結束點。 請遵循下列[最佳作法](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)，以確保 RDP 運作後移轉後。

>[AZURE.NOTE] 若要取得最佳效能，當您進行 Azure 容錯移轉時，請確定您受保護的電腦中安裝 Azure 代理程式。 在 [快速啟動藉此，也能協助診斷若有任何問題。 Linux 代理程式可找到[以下](https://github.com/Azure/WALinuxAgent)和 Windows 代理程式可以找到[以下](http://go.microsoft.com/fwlink/?LinkID=394789)

### <a name="run-a-test-failover"></a>執行測試容錯移轉

執行模擬您容錯移轉及修復程序隔離的網路不會影響您生產環境中測試容錯移轉與一般複寫如往常般。 測試容錯移轉起始來源上與您有幾種方式可以執行︰

- **沒有指定 Azure 的網路**︰ 如果您執行測試容錯移轉沒有網路測試只要檢查虛擬機器啟動，並在 Azure 中正確顯示。 虛擬機器無法連線到 Azure 網路後移轉後。
- **指定 Azure 的網路**︰ 此類型的容錯移轉檢查整個複寫環境提供最預期的 Azure 虛擬機器均已連線至指定的網路。


1. 在 [**復原方案]**頁面上選取 [計劃，按一下**測試容錯移轉**。

    ![加入虛擬機器](./media/site-recovery-vmware-to-azure-classic/test-failover1.png)

2. 在**確認測試容錯移轉**中選取 [**無**以表示您不想要測試容錯移轉時，使用 Azure 的網路，或選取要將連接測試 Vm 後移轉後的網路。 按一下 [核取記號，以開始容錯移轉]。

    ![加入虛擬機器](./media/site-recovery-vmware-to-azure-classic/test-failover2.png)

3. 監視**工作**] 索引標籤上的容錯移轉進度。

    ![加入虛擬機器](./media/site-recovery-vmware-to-azure-classic/test-failover3.png)

4. 容錯移轉完成後，您也應該能夠看到複本 Azure 電腦會出現在 Azure 入口網站 >**虛擬機器**。 如果您想要啟動 Azure vm RDP 連線必須開啟連接埠 3389 VM 端點。

5. 之後您已完成，當容錯移轉達到完成測試階段，按一下 [完成測試] 以完成。 在筆記中錄製並儲存聯測試容錯移轉任何觀察值。

6. 按一下 [自動清理測試環境的 [**測試容錯移轉已完成**]。 這是後測試容錯移轉會顯示**完成**狀態。 刪除任何項目或 Vm 自動建立在測試容錯移轉。 請注意，是否測試容錯移轉持續時間超過兩週才能完成完成來強制。



### <a name="run-an-unplanned-failover"></a>執行意外的容錯移轉

意外的容錯移轉從 Azure 啟動時，可以執行，即使主要網站無法使用。


1. 在 [**復原方案**] 頁面選取方案並按一下**容錯移轉** > **意外的容錯移轉**。

    ![加入虛擬機器](./media/site-recovery-vmware-to-azure-classic/unplanned-failover1.png)

2. 如果您複製 VMware 虛擬機器您可以選取，試著關閉內部部署 Vm。 這是最佳效能，容錯移轉會繼續投入是否成功或不。 如果它不成功錯誤詳細資料會出現在 [**工作**] 索引標籤上 >**非預期的容錯移轉工作**。

    ![加入虛擬機器](./media/site-recovery-vmware-to-azure-classic/unplanned-failover2.png)

    >[AZURE.NOTE] 此選項無法使用，如果您複製實體伺服器。 您需要嘗試，這些手動關閉如果可以的話。

3. 在 [**確認容錯移轉**驗證的容錯移轉方向 （Azure)，然後選取您想要使用的容錯移轉的復原點。 如果您設定複寫屬性時，啟用多重 VM 就可以復原的最新的應用程式或損毀一致的復原點。 您也可以選取 [**自訂復原點**復原至之前的時間。 按一下 [核取記號，以開始容錯移轉]。

    ![加入虛擬機器](./media/site-recovery-vmware-to-azure-classic/unplanned-failover3.png)

3. 等待意外的容錯移轉工作完成。 您可以監視**工作**] 索引標籤上的容錯移轉進度。 請注意，即使意外的容錯移轉過程中發生錯誤修復計劃執行，直到已完成。 您也應該能夠看到複本 Azure 虛擬機器中顯示 Azure 入口網站中的電腦。

### <a name="connect-to-replicated-azure-virtual-machines-after-failover"></a>連線至複寫後移轉後的 Azure 虛擬機器

以連線至複製中 Azure 虛擬機器之後容錯移轉，以下是您需要,︰

1. 應在主要的電腦上啟用遠端桌面連線。
2. 若要允許 RDP 主要的電腦上 Windows 防火牆。
3. 後移轉後必須將 RDP 新增至公用 Azure 虛擬機器中的端點。

[閱讀更多](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)關於此設定。


## <a name="deploy-additional-process-servers"></a>部署其他程序伺服器

如果您不按比例縮放的出您的部署超過 200 來源機器或您所有的每日變換速度超過 2 TB，您將需要額外的程序伺服器處理流量。 若要設定其他程序伺服器檢查中[其他程序伺服器](#additional-process-servers)的需求，並依照指示進行以下設定程序伺服器。 設定伺服器後，您可以設定來源機器使用它。

### <a name="set-up-an-additional-process-server"></a>設定其他程序伺服器

您設定的其他處理程序伺服器，如下所示︰

- 執行整合的精靈將管理伺服器設定為 [僅限伺服器程序。
- 如果您想要管理資料使用的新程序伺服器的複寫必須移轉受保護的電腦執行此動作。

### <a name="install-the-process-server"></a>安裝程序伺服器

1. 在 [快速入門] 頁面上下載網站復原元件安裝的整合的安裝檔案。 執行安裝程式。
2. 在**開始之前**選取**擴充部署新增額外的程序伺服器**。

    ![新增程序伺服器](./media/site-recovery-vmware-to-azure-classic/add-ps1.png)

3. 當您所做的相同方式完成精靈您[設定](#step-5-install-the-management-server)的第一個管理伺服器。

4. **設定伺服器詳細資料**中指定您要在其上安裝設定伺服器，以及複雜密碼的原始管理伺服器 IP 位址。 執行的原始管理伺服器上**<SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe-n**以取得複雜密碼。

    ![新增程序伺服器](./media/site-recovery-vmware-to-azure-classic/add-ps2.png)

### <a name="migrate-machines-to-use-the-new-process-server"></a>移轉機器使用新的程序伺服器

1. 開啟 [**設定伺服器** > **伺服器**> 原始管理伺服器的名稱 >**伺服器詳細資料**。

    ![更新程序伺服器](./media/site-recovery-vmware-to-azure-classic/update-process-server1.png)

2. [**程序伺服器**] 清單中按一下 [**變更程序 Server**您想要修改的伺服器旁邊。

    ![更新程序伺服器](./media/site-recovery-vmware-to-azure-classic/update-process-server2.png)

3. **變更程序**server > **目標程序伺服器**選取新的管理伺服器，然後選取 [新的程序伺服器會處理虛擬機器。 按一下 [資訊] 圖示，以取得伺服器的相關資訊。 具有所需的每個所選的虛擬機器複製到新的程序伺服器的平均空間會顯示以協助您進行載入決策。 按一下 [核取記號，以開始複製到新的程序伺服器]。

    ![更新程序伺服器](./media/site-recovery-vmware-to-azure-classic/update-process-server3.png)




## <a name="vmware-permissions-for-vcenter-access"></a>VMware vCenter 存取權限

程序伺服器才能自動探索 Vm vCenter 伺服器上。 若要執行自動探索必須定義在 vCenter 層級，以允許網站復原存取 vCenter 伺服器的角色 (Azure_Site_Recovery)。 請注意，如果您只需要將 VMware 機器移轉到 Azure 並不需要從 Azure 回復，您可以定義足夠的唯讀角色。 您將權限設定中所述[步驟 6︰ 設定 vCenter 伺服器的認證](#step-6-set-up-credentials-for-the-vcenter-server)下表摘要列出的角色的權限。

**角色** | **詳細資料** | **權限**
--- | --- | ---
Azure_Site_Recovery 角色 | VMware VM 探索 |指派 v 中心伺服器這些權的限︰<br/><br/>資料存放區]-> [配置空間、 瀏覽資料存放區、 低層級檔案作業。、 移除檔案、 更新虛擬機器檔案<br/><br/>網路]-> [網路指派<br/><br/>資源]-> [資源資料庫，可關閉虛擬機器中的移轉，將可虛擬機器上指派虛擬機器<br/><br/>工作]-> [建立工作，更新工作<br/><br/>虛擬機器]-> [設定<br/><br/>虛擬機器]-> [互動]-> [回應問題裝置連線。、 設定 CD 媒體、 設定磁碟媒體]，關閉 Power 電力上、 VMware 工具安裝<br/><br/>虛擬機器]-> [清單]-> [建立]，[Register 取消註冊<br/><br/>虛擬機器]-> [提供]-> [儲存格內允許虛擬機器下載，允許虛擬機器檔案上傳<br/><br/>虛擬機器]-> [快照]-> [移除快照集
vCenter 使用者角色 | VMware VM 探索/容錯移轉不關閉來源 VM | 指派 v 中心伺服器這些權的限︰<br/><br/>資料中心物件 –> 傳播至子物件角色 = 唯讀狀態 <br/><br/>使用者指派資料中心層級，因此所在的資料中心的 [存取所有的物件。  如果您想要限制存取，**沒有存取**角色指派**傳播至子系**物件的子物件 （ESX 主機、 datastores、 Vm 和網路）。
vCenter 使用者角色 | 錯誤移轉及回復 | 指派 v 中心伺服器這些權的限︰<br/><br/>資料中心物件 – 傳播至子物件角色 = Azure_Site_Recovery<br/><br/>使用者指派資料中心層級，因此所在的資料中心的 [存取所有的物件。  如果您想要限制存取，**沒有存取**角色指派與**傳播至子物件**子物件 （ESX 主機、 datastores、 Vm 和網路）。  



## <a name="third-party-software-notices-and-information"></a>第三方軟體聲明與資訊

請不要翻譯或當地語系化

軟體和韌體執行中的 Microsoft 產品或服務為基礎，或整合下方所列的專案中的資料 （共同，「 協力廠商程式碼 」）。  Microsoft 是不原始作者的協力廠商程式碼。  原始的著作權聲明及授權，在 Microsoft 收到這類協力廠商程式碼中，設定左起第四個下方。

在 A] 區段中的資訊關於從下方所列的專案的協力廠商程式碼元件。 這類的資訊和授權可供參考。  此協力廠商程式碼會被 relicensed 您 microsoft 下 Microsoft 產品或服務的 Microsoft 軟體授權條款。  

區段 B 中的資訊關於協力廠商程式碼元件，會在您就可以使用 microsoft 原始授權條款。

可以在[Microsoft 下載中心](http://go.microsoft.com/fwlink/?LinkId=529428)找到完整的檔案。 Microsoft 保留不明文授予之，所有的權限，也就是說，是否 estoppel 或。

## <a name="next-steps"></a>後續步驟

[深入瞭解回復](site-recovery-failback-azure-to-vmware-classic.md)Azure 中執行的機器上顯示您失敗回到您的內部部署環境。
