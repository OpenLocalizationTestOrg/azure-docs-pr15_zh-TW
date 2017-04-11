<properties
    pageTitle="準備網站復原部署 |Microsoft Azure"
    description="本文將說明如何準備好部署複寫 Azure 網站復原。"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor="tysonn"/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="10/05/2016"
    ms.author="raynew"/>

# <a name="prepare-for-azure-site-recovery-deployment"></a>準備 Azure 網站復原部署

閱讀本文高層級的概觀 Azure 網站復原服務支援的每一個試驗案例的部署需求。 讀取每個案例的一般需求之後，您可以連結至每個部署的特定的部署詳細資料。

讀取後本文會底部的文章，或在[Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上張貼任何註解或問題。

## <a name="overview"></a>概觀

組織必須決定如何應用程式、 負載，以及資料保持執行及使用期間計劃和計畫停機時間和越快越復原正常運作狀況 BCDR 策略。 您 BCDR 策略應該安全及修復，保留商務資料，並確保當發生損毀時，仍然持續可用的工作量。 

網站復原是 Azure 服務可提供您 BCDR 策略來協調實體伺服器的內部部署和雲端 (Azure) 或次要資料中心的虛擬機器複寫。 中斷問題發生時在您的主要位置，您無法透過，讓應用程式可用的工作負載的第二個位置。 會傳回標準作業時，您會回到您的主要位置失敗。 如需深入瞭解[什麼是網站復原？](site-recovery-overview.md)

## <a name="select-your-deployment-model"></a>選取您的部署模型

Azure 有兩種不同的[部署模型](../resource-manager-deployment-model.md)建立及使用的資源︰ Azure 資源管理員模型與傳統的服務管理模型。 Azure 也會有兩個入口網站 – 支援傳統部署模型， [Azure 傳統入口網站](https://manage.windowsazure.com/)和[Azure 入口網站](https://ms.portal.azure.com/)的兩種部署模型支援。

在 [傳統] 入口網站和 Azure 入口網站中使用網站復原。 Azure 傳統入口網站中您可以使用傳統的服務管理模型支援網站復原。 Azure 入口網站中，您可以支援傳統的模型或資源管理員部署。 [閱讀更多](site-recovery-overview.md#site-recovery-in-the-azure-portal)部署 Azure 入口網站的相關資訊。

當您選擇部署模型記事的︰

- 我們建議您儘可能使用[Azure 入口網站](https://portal.azure.com)與資源管理員模型。
- 網站復原提供較簡單，更直覺化的快速入門體驗 Azure 入口網站。
- 使用 Azure 入口網站，您可以複製機器傳統和資源管理員 Azure 中的儲存空間。 此外，在 Azure 入口網站，您可以使用 LRS 或 GRS 儲存帳戶。
- Azure 入口網站會將網站復原和備份服務合併為單一的修復服務保存庫中，使您可以設定，並從單一位置管理 BCDR 服務。
- 現在，佈建與雲端解決方案提供者 (CSP) 程式 Azure 訂閱的使用者能管理 Azure 入口網站中的網站修復作業。
- 複製 VMware Vm 或實體機器至 Azure Azure 入口網站提供許多新功能，包括支援進階版儲存空間及功能複寫排除特定磁碟。


## <a name="select-your-deployment-scenario"></a>選取您的部署案例

**部署** | **詳細資料** | **Azure 入口網站** | **傳統的入口網站** | **PowerShell**
---|---|---|---|---
**若要 Azure VMware Vm** | 複製 VMware Vm 至 Azure 儲存體 | Azure 中入口網站 Vm 可能無法透過傳統或資源管理員儲存空間<br/><br/> [Azure 入口網站](site-recovery-vmware-to-azure.md)中的部署提供精簡的部署體驗和數字的功能優點。 | Azure 傳統入口網站中可以[增強型的模型](site-recovery-vmware-to-azure-classic.md)部署，並無法透過傳統 Azure 儲存空間。<br/><br/> 此外，也不應該用於新的部署舊版模型。 |  PowerShell 目前不支援]。
**若要 Azure HYPER-V Vm** | HYPER-V Vm 至 Azure 儲存體。 在系統管理中心 VMM 雲朵或不 VMM 管理 HYPER-V 主機可 Vm。 | Azure 中入口網站 Vm 可能無法透過傳統或資源管理員儲存空間。<br/><br/> Azure 入口網站提供精簡的部署體驗。 [瞭解更多](site-recovery-vmm-to-azure.md)有關複製 HYPER-V Vm VMM 雲朵中。 [瞭解更多](site-recovery-hyper-v-site-to-azure.md)有關複製 （不含 VMM) HYPER-V Vm。| 在 [傳統] Azure 入口網站中您可能會失敗 Vm 透過傳統 Azure 儲存體 | PowerShell 部署功能受支援。
**Azure 實體伺服器** | 複製實體 Windows/Linux 伺服器至 Azure 儲存體 | Azure 中入口網站的伺服器可能無法透過傳統或資源管理員儲存空間。<br/><br/> [Azure 入口網站](site-recovery-vmware-to-azure.md)中的部署提供精簡的部署體驗和數字的功能優點。 | Azure 傳統入口網站中可以[增強型的模型](site-recovery-vmware-to-azure-classic.md)部署，並無法透過傳統 Azure 儲存空間。<br/><br/> 此外，也不應該用於新的部署舊版模型。 | PowerShell 部署目前不支援]。
**次要網站 VMware Vm/實體伺服器* | 次要網站複寫 VMware Vm 或實體的 Windows/Linux 伺服器。 [進一步瞭解並下載](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf)InMage 搜索使用者輔助線。 | Azure 入口網站中無法使用 | 在 [傳統] 入口網站中會從網站復原保存庫下載 InMage 搜索。 | 目前不支援 PowerShell 部署
**HYPER-V Vm 次要網站** | 在 VMM 雲朵 HYPER-V Vm 複製至第二個的雲端 | [Azure 入口網站](site-recovery-vmm-to-vmm.md)中您可以只使用 HYPER-V 複本的次要網站複寫 HYPER-V Vm VMM 雲朵中。 舊目前不支援]。 | Azure 傳統入口網站中您可以複製中 VMM 雲朵 HYPER-V Vm 次要網站使用[HYPER-V 複本](site-recovery-vmm-to-vmm-classic.md)或[舊複寫](site-recovery-vmm-san.md) | 支援的 PowerShell 部署



## <a name="check-what-you-need-for-deployment"></a>檢查您需要以供部署

### <a name="replicate-to-azure"></a>複製到 Azure

**要求** | **詳細資料** 
---|---
**Azure 帳戶** | 您必須以[Microsoft Azure](http://azure.microsoft.com/)帳戶。<br/><br/> 您可以開始使用[免費的試用版](https://azure.microsoft.com/pricing/free-trial/)。 [瞭解更多](https://azure.microsoft.com/pricing/details/site-recovery/)有關網站復原價格。
**Azure 儲存體** | 複製的資料儲存在 Azure 儲存空間，發生錯誤後移轉時，系統會建立 Azure Vm。 若要將複寫到 Azure，您必須[Azure 儲存體帳戶](../storage/storage-introduction.md)。<br/><br/>如果您在 [傳統] 入口網站中部署網站復原您需要一個或多個[標準 GRS 儲存帳戶](..storage/storage-redundancy.md#geo-redundant-storage)。<br/><br/> 如果您部署 Azure 入口網站中，您可以使用 GRS 或 LRS 儲存空間。<br/><br/> 如果您複製 VMware Vm 或在 Azure 入口網站的進階版的儲存位置的實體伺服器支援。 請注意，是否您是進階版儲存帳戶您將需要儲存擷取內部部署資料的變更進行中的複製記錄的標準儲存帳戶。 [進階版儲存空間](../storage/storage-premium-storage.md)，通常是需要一致 IO 高效能與主機 IO 大量工作量低延遲的虛擬機器。<br/><br/> 如果您想要儲存複製的資料使用進階版帳戶，您將需要儲存擷取內部部署資料的變更進行中的複製記錄的標準儲存帳戶。
**Azure 網路** | 若要複寫 Azure，您必須在建立時即後移轉後，將會連線到的 Azure Vm Azure 網路。<br/><br/> 如果您部署傳統入口網站中，您會使用傳統的網路。 如果您部署 Azure 入口網站中，您可以使用傳統或資源管理員網路。<br/><br/> 網路必須在同一個地區為保存庫。
**網路對應 (Azure VMM)** | 如果您從 VMM 複製至 Azure，可確保[網路對應](site-recovery-network-mapping.md)的 Azure Vm 均已連線到正確的網路後移轉後。<br/><br/> 若要設定的網路對應必須設定 VM 網路 VMM 入口網站中。
**內部部署** | **VMware Vm**︰ 您必須在內部部署電腦執行的網站復原元件、 VMware vSphere 主機/vCenter 伺服器，以及您要複製的 Vm。 [閱讀更多](site-recovery-vmware-to-azure.md#configuration-server-prerequisites)。<br/><br/> **實體伺服器**︰ 如果您複製實體伺服器需要執行網站復原元件，以及您要複製的實體伺服器內部部署電腦。 [閱讀更多](site-recovery-vmware-to-azure.md#configuration-server-prerequisites)。 如果您想要[回復](site-recovery-failback-azure-to-vmware.md)Azure 失敗之後必須進行 VMware 基礎結構。<br/><br/> **HYPER-V Vm**︰ 如果您想要複製的 VMM 雲朵 HYPER-V Vm 必須 VMM 伺服器，以及 HYPER-V 主機的 Vm 您想要保護的所在。 [閱讀更多](site-recovery-vmm-to-azure.md#on-premises-prerequisites)。<br/><br/> 如果您想要複製不 VMM HYPER-V Vm 必須 Vm 位於 HYPER-V 主機。 [閱讀更多](site-recovery-hyper-v-site-to-azure.md#on-premises-prerequisites)。
**受保護的電腦** | 受保護會複製到 Azure 的電腦必須遵守[Azure 先決條件](#azure-virtual-machine-requirements)如下所述。


### <a name="replicate-to-a-secondary-site"></a>複製至次要網站

**要求** | **詳細資料** 
---|---
**Azure 帳戶** | 您必須以[Microsoft Azure](http://azure.microsoft.com/)帳戶。<br/><br/> 您可以開始使用[免費的試用版](https://azure.microsoft.com/pricing/free-trial/)。 [瞭解更多](https://azure.microsoft.com/pricing/details/site-recovery/)有關網站復原價格。
**內部部署** | **VMware Vm**︰ 主網站中所需的程序伺服器快取、 壓縮和加密複寫資料傳送到次要網站之前。 在 [次要網站會安裝可用於管理及監視部署和母片的目標伺服器設定伺服器。 我們也建議更容易管理 vContinuum 伺服器。 此外，您需要一個或多個 VMware vSphere 主辦城市] 和 [選擇性 vCenter 伺服器。 [了解更多](site-recovery-vmware-to-vmware.md)<br/><br/> **在 [VMM 雲朵 HYPER-V Vm**︰ 您需要設定 VMM 伺服器，而您想要複製之 Vm HYPER-V 主機。 [閱讀更多](site-recovery-vmm-to-vmm.md#on-premises-prerequisites)。
**網路對應 (VMM VMM)** | 如果您從 VMM 複製到 VMM，[網路對應](site-recovery-network-mapping.md)可確保複本 Vm 到適當的網路連線後移轉後，並以最佳方式置於 HYPER-V 主機。 若要設定的網路對應必須設定 VM 網路 VMM 伺服器上。
**儲存對應** | 如果您複製從 VMM VMM 您可以選擇性地設定[儲存對應](site-recovery-storage-mapping.md)至指定複寫 Vm 儲存目標。 儲存對應可以設定 HYPER-V 複本和舊複寫。<br/><br/> 儲存對應目前不支援在 Azure 入口網站。


## <a name="verify-url-access"></a>請確認 URL 存取

請確認這些 Url 存取的伺服器。

**URL** | **若要 VMM VMM** | **Azure VMM** | **Hyper-v 至 Azure** | **若要 Azure VMware**
---|---|---|---|---
*。 accesscontrol.windows.net | 允許 | 允許 | 允許 | 允許
*。 backup.windowsazure.com | 不需要 | 允許 | 允許 | 允許
*。 hypervrecoverymanager.windowsazure.com | 允許 | 允許 | 允許 | 允許
*。 store.core.windows.net | 允許 | 允許 | 允許 | 允許
*。 blob.core.windows.net | 不需要 | 允許 | 允許 | 允許
https://www.msftncsi.com/ncsi.txt | 允許 | 允許 | 允許 | 允許
https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi | 不需要 | 不需要 | 不需要 | 允許

## <a name="azure-virtual-machine-requirements"></a>Azure 虛擬機器需求

您可以部署網站復原複製虛擬機器及執行任何 Azure 所支援的作業系統的實體伺服器。 這包含大部分的 Windows 和 Linux 版本。 您需要，請確定的內部部署 Azure 需求符合您想要保護的虛擬機器。


**功能** | **需求** | **詳細資料**
---|---|---
HYPER-V 主機 | 執行 Windows Server 2012 R2 | 如果不支援的作業系統，將會失敗的先決條件核取
VMware hypervisor  | 支援的作業系統 | [檢查需求](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)
來賓作業系統 | HYPER-V Azure 複寫︰ 網站復原支援所有[Azure 所支援](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)的作業系統。 <br/><br/> VMware 和實體伺服器複寫︰ 檢查 Windows 和 Linux[的先決條件](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) | 不支援的必要條件檢查將會失敗。
來賓作業系統架構 | 64 位元 | 如果某些不受支援，將會失敗的先決條件核取
作業系統磁碟大小 |  1023 GB | 如果某些不受支援，將會失敗的先決條件核取
作業系統磁碟計數 | 1 | 不支援的必要條件檢查將會失敗。
資料磁碟計數 | 16 或更少 （最大值為所建立的虛擬機器大小的函數。 16 = XL) | 如果某些不受支援，將會失敗的先決條件核取
資料磁碟 VHD 大小 | 最多到 1023 GB | 如果某些不受支援，將會失敗的先決條件核取
網路介面卡 | 支援多重介面卡 |
靜態 IP 位址 | 支援 | 如果主要的虛擬機器使用的您可以指定將會建立在 Azure 虛擬機器的靜態 IP 位址的靜態 IP 位址。 請注意，不支援 hyper-v 上執行 linux 虛擬機器的靜態 IP 位址。
iSCSI 磁碟 | 不支援 | 如果某些不受支援，將會失敗的先決條件核取
共用的 VHD | 不支援 | 如果某些不受支援，將會失敗的先決條件核取
FC 磁碟 | 不支援 | 如果某些不受支援，將會失敗的先決條件核取
硬碟格式| VHD <br/><br/> VHDX | 雖然 Azure 目前不支援 VHDX，網站復原自動轉換 VHDX VHD 無法透過 Azure 時。 當您無法回內部部署虛擬機器繼續使用 VHDX 格式。
Bitlocker | 不支援 | 保護虛擬機器前，必須先停用 Bitlocker。
虛擬機器名稱| 介於 1 到 63 的字元。 限制為字母、 數字和連字號。 應該的開始和結束以字母或數字 | 更新網站修復虛擬機器屬性中的值
虛擬機器類型 | <p>產生 1</p> <p>產生 2-Windows</p> |  產生 2 基本磁碟包括 1 或 2 的資料區磁碟格式為 VHDX 即低於 300 GB 功能受支援的作業系統磁碟類型的虛擬機器。 不支援 Linux 產生 2 虛擬機器。 [閱讀更多的資訊](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)



## <a name="optimizing-your-deployment"></a>最佳化您的部署

使用下列秘訣，可協助您最佳化及調整您的部署。

- **作業系統大量大小**︰ 當您要複製到 Azure 虛擬機器作業系統音量必須小於 1 TB。 如果您有多個區超過此您可以手動其移動至另一個磁碟開始部署之前。
- **資料磁碟大小**︰ 如果您複製至 Azure 您可以讓為 32 個資料磁碟上虛擬機器，各有 1 TB 的最大值。 有效，您可以複製及容錯 ~ 32 TB 虛擬機器。
- **復原計劃限制**︰ 網站復原可以不按比例縮放數以千計的虛擬機器。 修復計劃專為應用程式應該無法透過放在一起，我們數量機器 50 復原計劃中的模式。
- **Azure 服務限制**︰ 每次 Azure 訂閱隨附一組的預設限制核心，在雲端服務等。我們建議您執行測試容錯移轉以驗證您的訂閱中的資源的可用性。 您可以修改透過 Azure 支援這些限制。
- **容量計劃**︰ 瞭解網站復原[容量計劃](site-recovery-capacity-planner.md)。
- **複寫頻寬**︰ 如果您是複製頻寬簡短附註︰
    - **ExpressRoute**︰ 網站復原搭配例如 Riverbed 的 Azure ExpressRoute 和 WAN 最佳化。 [閱讀更多](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx)關於 ExpressRoute 資訊。
    - **複寫流量**︰ 網站復原用途執行使用只資料區塊] 和 [不整個 VHD 智慧初始複寫。 在進行中的複寫複寫只會變更。
    - **網路流量**︰ 您可以控制複寫的使用原則，根據目的地 IP 位址和連接埠設定[Windows QoS](https://technet.microsoft.com/library/hh967468.aspx)網路流量。  此外，如果您複製使用 Azure 備份代理程式的 Azure 網站復原您可以設定節流代理程式。 [閱讀更多](https://support.microsoft.com/kb/3056159)。
- **RTO**︰ 測量復原時間目標 (RTO) 您可以預期的網站復原我們建議您執行測試容錯移轉，並完成作業所需的檢視來分析多少網站復原工作時間。 如果您無法透過 Azure，最佳 RTO 建議您將所有手動動作自動化整合 Azure 自動化及修復方案。
- **RPO**︰ 網站復原支援接近同步復原點目標 (RPO)，當您複製到 Azure。 這是假設您的資料中心和 Azure 之間有足夠的頻寬。


##<a name="service-urls"></a>服務 Url
請確認這些 Url 存取從伺服器


**Url** | **若要 VMM VMM** | **Azure VMM** | **Azure HYPER-V 網站** | **若要 Azure VMware**
---|---|---|---|---
 \*。 accesscontrol.windows.net | 必要的存取權  | 必要的存取權  | 必要的存取權  | 必要的存取權
 \*。 backup.windowsazure.com |  | 必要的存取權  | 必要的存取權  | 必要的存取權
 \*。 hypervrecoverymanager.windowsazure.com | 必要的存取權  | 必要的存取權  | 必要的存取權  | 必要的存取權
 \*。 store.core.windows.net | 必要的存取權  | 必要的存取權  | 必要的存取權  | 必要的存取權
 \*。 blob.core.windows.net |  | 必要的存取權  | 必要的存取權  | 必要的存取權
 https://www.msftncsi.com/ncsi.txt | 必要的存取權  | 必要的存取權  | 必要的存取權  | 必要的存取權
 https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi | | | | 必要的存取權


## <a name="next-steps"></a>後續步驟

學習並比較一般部署需求之後，您可以閱讀詳細的必要條件，並開始部署每個案例。

- [Azure 複寫 VMware 虛擬機器](site-recovery-vmware-to-azure-classic.md)
- [Azure 複寫實體伺服器](site-recovery-vmware-to-azure-classic.md)
- [Azure 複寫 VMM 雲朵 HYPER-V 伺服器](site-recovery-vmm-to-azure.md)
- [Azure 複寫 HYPER-V 虛擬機器 （不含 VMM)](site-recovery-hyper-v-site-to-azure.md)
- [複製 HYPER-V Vm 次要網站](site-recovery-vmm-to-vmm.md)
- [舊 HYPER-V Vm 複製至次要網站](site-recovery-vmm-san.md)
- [複製 HYPER-V Vm 與單一 VMM 伺服器](site-recovery-single-vmm.md)
