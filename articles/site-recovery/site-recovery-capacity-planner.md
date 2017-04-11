<properties
    pageTitle="規劃容量保護虛擬機器和 Azure 網站修復的實體伺服器 |Microsoft Azure"
    description="Azure 網站復原協調複寫、 錯誤移轉及復原的虛擬機器和實體位於內部部署 Azure 或第二個內部部署網站的伺服器。" 
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
    ms.date="07/12/2016" 
    ms.author="raynew"/>

# <a name="plan-capacity-for-protecting-virtual-machines-and-physical-servers-in-azure-site-recovery"></a>規劃容量保護虛擬機器和 Azure 網站修復的實體伺服器

Azure 網站復原容量規劃工具可協助您找出您容量需求，保護 HYPER-V Vm VMware Vm 與 Windows/Linux Azure 網站復原的實體伺服器。


## <a name="overview"></a>概觀

使用網站復原容量規劃分析您的來源環境和工作負載，並算出頻寬需求，您必須在您的來源位置的伺服器資源與您需要在您的目標位置的資源 （虛擬機器及儲存空間等）。 

您可以執行這個工具，有幾種模式︰

- **快速規劃**︰ 執行此模式下，以取得網路和伺服器計帳工具根據 Vm、 磁碟、 儲存及變更率平均數目。
- **規劃的詳細資料**︰ 在此模式下執行工具，並提供每個工作量 VM 層級的詳細資料。 分析 VM 相容性，並取得網路和伺服器的預測。

## <a name="before-you-start"></a>在您開始之前

之前執行工具︰

1. 收集您的環境，包括 Vm，每個 VM，每個磁碟的儲存空間的磁碟的相關資訊。
2. 找出您複製的資料的每日變更 （變換） 率。 若要執行這項操作︰

    - 如果您複製 HYPER-V Vm，請下載[HYPER-V 容量規劃工具](https://www.microsoft.com/download/details.aspx?id=39057)以取得變更工資率。 [瞭解更多](site-recovery-capacity-planning-for-hyper-v-replication.md)有關這項工具。 我們建議您執行這項工具超過一週擷取平均值。
    - 如果您複製 VMware 虛擬機器，可用於[vSphere 容量規劃應用裝置](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance)找出變換工資率。
    - 如果您複製您必須以手動方式估計的實體伺服器。

## <a name="run-the-quick-planner"></a>執行快速規劃
1.  下載並開啟 [ [Azure 網站復原容量規劃](http://aka.ms/asr-capacity-planner-excel)] 工具。 您需要執行巨集以啟用編輯功能，並啟用內容出現提示時，選取。 
2.  在 [**選取規劃類型**選取**快速規劃**從清單方塊。

    ![快速入門](./media/site-recovery-capacity-planner/getting-started.png)

3.  **容量規劃**工作表中輸入所需的資訊。 在下面的螢幕擷取畫面的紅色圈起來的所有欄位] 中，您必須填滿。

    - 在 [**選取您的案例**中，選擇 [ **HYPER-V Azure**或**VMware/實體至 Azure**]。
    - 在 [**每日資料平均變更工資率 （%）**的資訊將您收集使用[HYPER-V 容量規劃工具](site-recovery-capacity-planning-for-hyper-v-replication.md)或[vSphere 容量規劃應用裝置](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance)。  
    - **壓縮**僅適用於在複寫到 Azure VMware Vm 或實體的伺服器時，所提供的壓縮。 我們估計 30 百分比] 或 [更多，但您可以修改所需的設定。 Azure 壓縮複寫 HYPER-V Vm 中，您可以使用協力廠商應用裝置 Riverbed 等。 
    -  在 [**保留輸入**指定多久保留複本。 如果您複製 VMware 或實體伺服器天內輸入的值。 如果您複製 HYPER-V 小時中指定的時間。
    -  在**應該完成初始複寫批次的虛擬機器中的時數**，並在**每個初始複寫批次的虛擬機器中的數字**輸入來計算初始複寫需求的設定。  部署網站復原時，應該上傳整個初始資料集。 

    ![輸入](./media/site-recovery-capacity-planner/inputs.png)

2.  您已將它放在來源環境的值後，顯示的輸出包含︰

    - **Delta 試驗所需的頻寬**（秒 MB）。 Delta 複寫的網路頻寬計算平均的每日資料變更工資率。
    - **頻寬所需的初始複寫**（秒 MB）。 初始複寫的網路頻寬計算置於初始複寫值。 
    - **儲存要求 （Gb)**是必要的總 Azure 儲存空間。
    - **在標準的儲存空間帳戶上的總 IOPS**會根據標準的總儲存空間帳戶 8 K IOPS 單位大小計算。  數字的計算方式根據所有來源 Vm 快速規劃磁碟及每日的資料變更工資率。 變更數字的計算方式根據 Vm 對應至標準 Azure Vm 和資料的總數詳細規劃這些 Vm 率。 
    - **數字的標準儲存帳戶**提供標準儲存帳戶所需的保護 Vm 的總數。 請注意，標準儲存帳戶可透過標準的儲存區中的所有 Vm 保留多達 20000 IOPS 最多 500 IOPS 支援每個磁碟。 
    - **Blob 磁碟數目必要**時，可讓會建立在 Azure 儲存空間的磁碟數目。
    - **數字的進階版儲存的帳戶所需**提供進階版儲存的帳戶所需的保護 Vm 總數。 請注意來源與高 IOPS （大於 20000） VM 需要進階版儲存帳戶。 進階版儲存帳戶可保留最多 80000 IOPS。
    - **進階版存放區上的總 IOPS**會根據 256 K IOPS 單位大小總進階版儲存帳戶計算。  數字的計算方式根據所有來源 Vm 快速規劃磁碟及每日的資料變更工資率。 變更數字的計算方式根據對應到進階版 Azure VM （DS 與 GS 數列） 和資料的 Vm 總數詳細規劃這些 Vm 率。 
    - **所需的設定伺服器的數字**顯示多少設定伺服器所需的部署 (1)
    - **所需的其他處理程序伺服器的數字**會顯示其他處理程序伺服器是否需要除了程序伺服器上設定伺服器設定預設的。
    - **100%來源上的額外儲存空間**會顯示在來源位置時是否需要額外儲存空間。
            
    ![輸出](./media/site-recovery-capacity-planner/output.png)
 
## <a name="run-the-detailed-planner"></a>執行詳細的規劃


1.  下載並開啟 [ [Azure 網站復原容量規劃](http://aka.ms/asr-capacity-planner-excel)] 工具。 您需要執行巨集以啟用編輯功能，並啟用內容出現提示時，選取。 
2.  在 [**選取規劃類型**選取**詳細規劃**從清單方塊。

    ![快速入門](./media/site-recovery-capacity-planner/getting-started-2.png)

3.  **工作負載限定**工作表中輸入所需的資訊。 您必須在 [所有標記] 欄位中填滿。

    - 在 [**處理器核心**指定核心的總數來源伺服器上。
    - 在**mb 的記憶體配置**指定來源伺服器的 RAM 大小。 
    - **數字的 Nic**來源伺服器上指定網路介面卡的數目。 
    -  在**總儲存空間 （在 GB)**指定 VM 儲存空間的總大小。 如果來源伺服器有 3 個磁碟 500 gb 每個，那麼總儲存空間大小的範例是 1500 GB。
    -  在**磁碟數目**指定來源伺服器的磁碟的總數。
    -  在**磁碟容量使用**指定的平均使用率。
    -  在 [**每日變更工資率 （%）**指定每日的資料變更來源伺服器的工資率。
    -  **對應 Azure**大小請輸入您要對應的 Azure 虛擬記憶體大小。 如果您不想要手動進行按一下**計算 IaaS Vm**。 請注意，是否您輸入的手動設定，然後按一下 [計算 IaaS Vm 您手動設定可能會覆寫因為計算程序會自動識別最符合上 Azure 虛擬記憶體大小。

    ![工作負載的完整名稱](./media/site-recovery-capacity-planner/workload-qualification.png)

4.  如果您按一下以下**計算 IaaS Vm**是功能︰

    - 驗證強制輸入。
    - 計算 IOPS，並會建議最佳 Azure VM aize 有資格申請 Azure 複寫的相符的每個 Vm。 如果無法會發出錯誤偵測的 Azure VM 適當的大小。 範例的磁碟錯誤附加中 65 是否問題自最高大小 Azure VM 為 64。
    - 建議您可以使用 Azure VM 的儲存空間帳戶。
    - 計算總數標準儲存帳戶和進階版儲存帳戶所需的工作量。 在右側，檢視 Azure 儲存體類型和儲存帳戶，可以用於來源伺服器上向下捲動
    - 完成並排序表格根據所需的儲存空間 （標準或付費） 指派類型 VM，和磁碟數目的其餘部分。 針對符合需求的備份到欄 A Azure (是 VM 限定？) 的所有 Vm 顯示 [是]。 如果無法備份 VM 至會顯示錯誤 Azure。

欄 AA AE 輸出，並提供每個 VM 的資訊。

![工作負載的完整名稱](./media/site-recovery-capacity-planner/workload-qualification-2.png)


### <a name="example"></a>範例
作為範例，顯示在表格中的值的六個 Vm 的工具和最佳的 Azure VM 相符項目] 和 Azure 儲存體需求。

![工作負載的完整名稱](./media/site-recovery-capacity-planner/workload-qualification-3.png)

- 在範例的輸出請注意下列各項︰
    
    - 第一欄是用於 Vm 磁碟與變換驗證資料行。
    - 兩個標準儲存帳戶和一個進階版儲存帳戶所需的五個 Vm 中。 
    -  因為一或多個磁碟已超過 1 TB VM3 不合格保護。
    -  可以使用的第一個標準儲存帳戶 VM1 和 VM2
    -  VM4 可以使用的第二個標準儲存帳戶。
    -  VM5 和 VM6 需要進階版儲存帳戶，可以同時使用單一帳戶。

    >[AZURE.NOTE]  在標準及付費存放區上的 IOPS 計算 VM 層級，而非磁碟層級。 標準的虛擬機器可以處理多達 500 IOPS 每個磁碟。 如果磁碟 IOPS 大於 500 您需要進階版儲存空間。 但是如果 IOPS 磁碟的多個 500，但總 VM 磁碟 IOPS 都支援標準 Azure VM 限制範圍內 （虛擬記憶體大小，磁碟數目，數字的介面卡，CPU、 記憶體） 然後規劃挑選標準 VM 但 DS 或 GS 數列。 必須手動更新適當 DS 或 GS 數列 VM 對應 Azure 大小儲存格。

5. 所有的詳細資訊之後，請按一下 [**送出資料至規劃工具**]，以開啟**容量規劃**負載會醒目提示顯示是否已有資格申請保護或不。


### <a name="submit-data-in-the-capacity-planner"></a>送出容量規劃中的資料

1.  當您開啟**容量規劃**工作表時，會填入根據您指定的設定。 Word 中的**基礎輸入來源**儲存格，以顯示所輸入的**工作量限定**工作表，會出現 「 工作量 「。 
2.  如果您想要變更您要修改的**工作量限定**工作表，然後再按一下 [送出資料至規劃工具。  

    ![容量規劃](./media/site-recovery-capacity-planner/capacity-planner.png)


