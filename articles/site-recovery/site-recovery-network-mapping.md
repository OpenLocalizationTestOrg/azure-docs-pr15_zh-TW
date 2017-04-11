<properties
    pageTitle="準備使用 Azure 網站修復 VMM HYPER-V 虛擬機器保護的網路對應 |Microsoft Azure"
    description="設定 HYPER-V 虛擬機器複寫從內部部署資料中心 Azure 或次要網站的網路對應。"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="10/05/2016"
    ms.author="raynew"/>


# <a name="prepare-network-mapping-for-hyper-v-virtual-machine-protection-with-vmm-in-azure-site-recovery"></a>準備使用 Azure 網站修復 VMM HYPER-V 虛擬機器保護的網路對應

Azure 網站復原佔業務連續性和損壞復原 (BCDR) 策略來協調複寫、 錯誤移轉及復原的虛擬機器和實體伺服器。

本文將說明能協助您以最佳方式設定網路設定，當您用來做 HYPER-V 虛擬機器網站復原位於 VMM 雲朵兩個內部部署資料中心，或是之間的內部部署資料中心和 Azure 的網路對應。 請注意，如果您複製 HYPER-V Vm 不 VMM 雲端，或複製 VMware Vm 或實體的伺服器，本文不相關。

在底端的本文中，或在[Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上張貼任何註解或問題。


## <a name="overview"></a>概觀

Azure 網站復原部署複寫 HYPER-V 虛擬機器 Azure 或次要資料中心，使用 HYPER-V 複本或舊複寫時，會使用網路對應。

- **複製 HYPER-V 虛擬機器中兩個之間 VMM 雲朵內部部署資料中心**，網路來源 VMM 伺服器上的 VM 網路與執行下列動作目標 VMM 伺服器上的 VM 網路之間的對應地圖︰

    - **連線虛擬機器後移轉後**，可確保虛擬機器連線到適當的網路時後移轉後。 複本虛擬機器將目標網路] 已對應至來源網路的連線。
    - **Host （主機） 伺服器上的位置複本虛擬機器**，以最佳方式將 HYPER-V 主機伺服器上的 [複本的虛擬機器。 將可存取對應的 VM 網路主機上放置複本虛擬機器。
    - **沒有網路對應**，如果您沒有設定網路對應，不會複製的虛擬機器到任何 VM 網路連線後移轉後。

- **複製 HYPER-V 虛擬機器中的內部部署 VMM 雲端至 Azure**— 網路來源 VMM 伺服器上的 VM 網路之間的對應地圖及目標 Azure 網路中，執行下列動作︰
    - **連線後移轉後的虛擬機器**— 同一個網路上的容錯移轉可以連線至彼此，不管修復您的方案資料它們是在所有電腦。
    - **網路閘道器**，如果網路閘道器設定目標 Azure 網路，虛擬機器可以連線至其他內部部署的虛擬機器。
    - **沒有網路對應**，如果您沒有設定網路對應，只虛擬機器中相同的復原計劃的容錯移轉能夠彼此失敗之後透過連線至 Azure。


## <a name="network-mapping-example"></a>網路對應範例

您可以設定網路對應之間 VM 網路兩個 VMM 伺服器或單一 VMM 伺服器如果兩個網站受相同的伺服器上。 當對應已正確設定，並已啟用複寫時，虛擬機器主要位置會連線到網路，與目標位置及其複本會連線到其對應的網路。

如果網路有已設定正確 VMM，當您選取目標 VM 網路網路對應時，使用來源 VM 網路 VMM 來源雲朵隨即出現，以及用於保護目標雲朵上可用的目標 VM 網路。

以下是範例說明此機制。 讓我們來看組織中與紐約 」 和 「 芝加哥的兩個位置。

**位置** | **VMM 伺服器** | **VM 網路** | **若要對應**
---|---|---|---
紐約 | VMM 紐約| VMNetwork1 紐約 | 對應到 VMNetwork1 芝加哥
 |  | VMNetwork2 紐約 | 未對應
芝加哥 | VMM 芝加哥| VMNetwork1 芝加哥 | 對應到 VMNetwork1 紐約
 | | VMNetwork2 芝加哥 | 未對應

使用此範例中︰

- 針對任何已連線至 VMNetwork1 紐約的虛擬機器建立複本虛擬機器時，它會連線到 VMNetwork1 芝加哥。
- VMNetwork2 紐約或 VMNetwork2 芝加哥建立複本虛擬機器時，它不會連線到任何網路。

以下是 VMM 雲朵我們的範例組織和邏輯雲朵相關聯的網路中的設定方式。

### <a name="cloud-protection-settings"></a>雲端保護設定

**受保護的雲端** | **保護雲端** | **邏輯網狀 （紐約）**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>NA</p><p></p> | <p>LogicalNetwork1 紐約</p><p>LogicalNetwork1 芝加哥</p>
SilverCloud2 | <p>NA</p><p></p> | <p>LogicalNetwork1 紐約</p><p>LogicalNetwork1 芝加哥</p>

### <a name="logical-and-vm-network-settings"></a>邏輯和 VM 網路的設定

**位置** | **邏輯網狀** | **相關聯的 VM 網路**
---|---|---
紐約 | LogicalNetwork1 紐約 | VMNetwork1 紐約
芝加哥 | LogicalNetwork1 芝加哥 | VMNetwork1 芝加哥
 | LogicalNetwork2Chicago | VMNetwork2 芝加哥

### <a name="target-networks"></a>目標網路

根據這些設定，請選取目標 VM 網路時下, 表顯示將可供選擇。

**選取** | **受保護的雲端** | **保護雲端** | **目標網路可用**
---|---|---|---
VMNetwork1 芝加哥 | SilverCloud1 | SilverCloud2 | 使用
 | GoldCloud1 | GoldCloud2 | 使用
VMNetwork2 芝加哥 | SilverCloud1 | SilverCloud2 | 無法使用
 | GoldCloud1 | GoldCloud2 | 使用



## <a name="multiple-subnets"></a>多個子網路

如果目標網路都有多個子網路且這些子網路的其中一個子網路來源虛擬機器位於相同的名稱，然後複本虛擬機器會連線到目標該子後移轉後。 如果不有任何名稱相符的目標子網路，請在虛擬機器會連線到網路中的第一個子網路。


### <a name="failback"></a>回復

若要查看時會發生若是回復 （反向複寫），假設 VMNetwork1 紐約對應至 VMNetwork1 芝加哥，使用下列設定。


**虛擬機器** | **連線到 VM 網路**
---|---
VM1 | VMNetwork1 網路
VM2 （VM1 的複本） | VMNetwork1 芝加哥

使用這些設定，讓我們來檢閱發生的情況幾個可能的案例。

**案例** | **結果**
---|---
網路內容的 VM 2 後移轉後的任何變更。 | VM 1 仍會保留來源網路連線。
網路內容的 VM 2 後移轉後會變更，並已中斷連線。 | VM 1 已中斷連線。
網路內容的 VM 2 後移轉後會變更，並已連線至 VMNetwork2 芝加哥。 | 如果未對應 VMNetwork2 芝加哥，將會中斷 VM-1。
網路對應的 VMNetwork1 芝加哥即會變更。 | 將現在對應至 VMNetwork1 芝加哥網路連線 VM-1。


## <a name="next-steps"></a>後續步驟

現在，您需要進一步了解網路對應，[開始使用網站復原部署](site-recovery-best-practices.md)。
