<properties
    pageTitle="複製 （不含 VMM) HYPER-V 虛擬機器至 Azure Azure 入口網站中使用 Azure 網站復原 |Microsoft Azure"
    description="說明如何部署協調複寫、 錯誤移轉及復原的未受 VMM Azure 使用 Azure 入口網站的內部部署 HYPER-V Vm Azure 網站復原"
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
    ms.date="09/19/2016"
    ms.author="raynew"/>


# <a name="replicate-hyper-v-virtual-machines-without-vmm-to-azure-using-azure-site-recovery-with-the-azure-portal"></a>Azure Azure 入口網站中使用 Azure 網站復原複寫 HYPER-V 虛擬機器 （不含 VMM)

> [AZURE.SELECTOR]
- [Azure 入口網站](site-recovery-hyper-v-site-to-azure.md)
- [Azure 傳統](site-recovery-hyper-v-site-to-azure-classic.md)
- [PowerShell-資源管理員](site-recovery-deploy-with-powershell-resource-manager.md)



歡迎使用 Azure 網站復原 ！ 如果您想要複製內部部署 HYPER-V 虛擬這篇文章的電腦上的**未**受管理的系統管理中心的虛擬機器管理員 (VMM) 雲朵至 Azure 中使用。 本文將說明如何設定複寫 Azure 入口網站中使用 Azure 網站復原。

> [AZURE.NOTE] Azure 有兩種不同的[部署模型](../resource-manager-deployment-model.md)建立及使用的資源︰ Azure 資源管理員] 及 [傳統。 Azure 也會有兩個入口網站 – 支援傳統部署模型，Azure 傳統入口網站和支援兩種部署模型 Azure 入口網站。

> Azure 網站復原支援復原及 HYPER-V Azure 虛擬機器移轉。 本文中的步驟，套用相同時設定複製到 Azure 損毀修復或移轉至 Azure Vm

Azure 網站復原 Azure 入口網站中提供的新功能︰

- Azure 中入口網站、 Azure 備份和 Azure 網站修復服務會合併為單一的修復服務保存庫，好讓您可以設定和管理業務連續性和損毀修復 (BCDR) 從單一位置。 整合儀表板可讓您監控和管理您的內部部署網站與 Azure 公用雲端作業。
- 現在，佈建與雲端解決方案提供者 (CSP) 程式 Azure 訂閱的使用者能管理 Azure 入口網站中的網站修復作業。
- 網站復原 Azure 入口網站中的可以複寫機器資源管理員儲存帳戶。 在錯誤後移轉，網站復原會建立資源管理員型 Vm Azure 中。
- 網站復原仍會繼續支援複寫傳統的儲存空間帳戶及 Vm 使用傳統的部署模型的容錯移轉。


讀取後這份文件會在 Disqus 註解] 區段底部張貼您的意見反應。 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上提出技術的問題。


## <a name="overview"></a>概觀


組織必須決定如何應用程式、 負載，以及資料保持執行及使用期間計劃和計畫停機時間和越快越復原正常運作狀況 BCDR 策略。 BCDR 策略將安全及修復，保留商務資料，並確保的負載時，可持續發生損毀。

網站復原是 Azure 服務可提供您 BCDR 策略來協調實體伺服器的內部部署和雲端 (Azure) 或次要資料中心的虛擬機器複寫。 中斷問題發生時在您的主要位置，您可能無法透過，讓應用程式可用的工作負載的第二個位置。 您可能會失敗回到您的主要位置傳回到正常運作。 如需深入瞭解[什麼是 Azure 網站復原？](site-recovery-overview.md)

本文提供您要複製內部部署 HYPER-V 虛擬的所有資訊的都電腦上的****在系統管理中心的虛擬都機器管理員 (VMM) 雲朵 Azure 來管理。 其包含架構的概觀，規劃的詳細資訊，以及設定內部部署伺服器、 Azure、 複寫原則，以及容量規劃部署步驟。 您已設定基礎結構之後，可以啟用您要保護的電腦上的複寫並執行測試容錯移轉驗證設定。 您可以首次執行計劃的容錯移轉，也將您的 Vm 移轉至 Azure，，然後完成移轉。

## <a name="business-advantages"></a>商務優點

- 提供離線 (Azure) 容錯移轉商務工作量及 HYPER-V 虛擬機器上執行應用程式。
- 簡單的設定和管理複寫、 容錯移轉及修復程序提供單一修復服務主控台。
- 可讓您輕鬆地執行從您的內部部署基礎結構的容錯移轉至 Azure 及錯誤後回復 （還原） 從 Azure 至內部部署網站。
- 讓多層應用程式負載一起容錯，您可以使用多部電腦，設定修復計劃。

## <a name="scenario-architecture"></a>案例架構

以下是分析藍本元件︰

- **HYPER-V 主機] 或 [叢集**︰ 內部部署 HYPER-V 主機伺服器或叢集。 執行您想要保護的 Vm HYPER-V 主機網站復原部署期間收集到邏輯 HYPER-V 網站。
- **Azure 網站修復提供者並修復服務代理程式**︰ 在部署您 Azure 網站修復提供者與 Microsoft Azure 修復服務代理程式 HYPER-V 主機在伺服器上安裝。 透過 HTTPS 443 複寫協調流程，Azure 網站復原進行通訊的提供者。 HYPER-V 主機伺服器上的代理程式 Azure 儲存體，透過 HTTPS 443 根據預設，複製資料。
- **Azure**︰ 您必須 Azure 訂閱、 複製的儲存資料，Azure 儲存體帳戶和 Azure 虛擬網路以便 Azure Vm 後移轉後連線至網路。

![HYPER-V 網站架構](./media/site-recovery-hyper-v-site-to-azure/architecture.png)



## <a name="azure-prerequisites"></a>Azure 的先決條件

以下是您需要 Azure 中部署這種情況。

**必要條件** | **詳細資料**
--- | ---
**Azure 帳戶**| 您必須以[Microsoft Azure](http://azure.microsoft.com/)帳戶。 您可以開始使用[免費的試用版](https://azure.microsoft.com/pricing/free-trial/)。 [瞭解更多](https://azure.microsoft.com/pricing/details/site-recovery/)有關網站復原價格。
**Azure 儲存體** | 您必須是標準的儲存空間帳戶。 您可以使用 LRS 或 GRS 儲存帳戶。 我們建議您 GRS，讓資料是同時，如果地區的資料，或如果無法復原的主要區域。 [進一步瞭解](../storage/storage-redundancy.md)。 帳戶必須在同一個地區為修復服務保存庫。<br/><br/> 不支援的進階版儲存空間。<br/><br/> 複製的資料儲存在 Azure 儲存空間，發生錯誤後移轉時，系統會建立 Azure Vm。<br/><br/> [閱讀相關資訊](../storage/storage-introduction.md)Azure 儲存空間。
**Azure 網路** | 您必須容錯移轉發生時，將會連線到的 Azure Vm Azure 虛擬網路。 Azure 虛擬網路必須在同一個地區為修復服務保存庫。

## <a name="on-premises-prerequisites"></a>內部部署的先決條件

以下是您的需要內部部署。

**必要條件** | **詳細資料**
--- | ---
**Hyper-v** | 一或多內部部署的最新的更新並啟用 HYPER-V 角色或**Microsoft HYPER-V Server 2012 R2**執行**Windows Server 2012 R2**的伺服器。<br/><br/>HYPER-V 伺服器應該包含一或多個虛擬機器。<br/><br/>HYPER-V 伺服器應該連接至網際網路，直接或透過 proxy。<br/><br/>HYPER-V 伺服器應有[KB2961977](https://support.microsoft.com/en-us/kb/2961977 "KB2961977")安裝中所提及的修正。
**提供者和代理程式** | Azure 網站復原部署時，您就會安裝 Azure 網站修復提供者。 提供者安裝也會執行您想要保護的虛擬機器每個 HYPER-V 伺服器上安裝 Azure 修復服務代理程式。 在網站復原保存庫中的所有 HYPER-V 伺服器都應有的提供者及代理程式的相同版本。<br/><br/>若要透過網際網路連線到 Azure 網站復原必須提供者。 可傳送流量，直接或透過 proxy。 請注意，根據 HTTPS proxy 不支援。 Proxy 伺服器應該允許存取︰ <br/><br/> *。 hypervrecoverymanager.windowsazure.com <br/> <br/> *。 accesscontrol.windows.net <br/><br/> *。 backup.windowsazure.com <br/> <br/> *。 blog.core.windows.net <br/><br/> *store.core.windows.net <br/><br/> https://www.msftncsi.com/ncsi.txt<br/><br/>如果您在伺服器上有 IP 位址的防火牆規則，請核取規則，允許 Azure 通訊。 您必須允許[Azure 資料中心 IP 範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (443) 連接埠。<br/><br/>允許的 IP 位址範圍 Azure 區域，您的訂閱，與西美國。

## <a name="protected-machine-prerequisites"></a>受保護的電腦的先決條件


**必要條件** | **詳細資料**
--- | ---
**受保護的 Vm** | 您無法透過 VM 之前必須確認的名稱，將指定的 Azure vm 遵守[Azure 的先決條件](site-recovery-best-practices.md#azure-virtual-machine-requirements)。 您已啟用 vm 試驗之後，您可以修改名稱。<br/><br/> 在受保護的電腦上的個別磁碟容量應該不超過 1023 GB。 VM 可以有到 16 的磁碟 (因此最 16 TB)。<br/><br/> 共用磁碟來賓叢集不受支援。<br/><br/> 如果來源 VM NIC 小組 Azure 失敗之後會將其轉換成單一 NIC。<br/><br/>保護 Vm 執行的靜態 IP 位址 Linux 不支援。

## <a name="prepare-for-deployment"></a>準備進行部署

準備部署，您需要︰

1. 在其中 Azure Vm 時，會位於建立後移轉後的[Azure 網路的設定](#set-up-an-azure-network)。
2. [設定 Azure 儲存體帳戶](#set-up-an-azure-storage-account)複製的資料。
3. [準備 HYPER-V 主機](#prepare-the-hyper-v-hosts)，以確保他們可以存取所需的 Url。

### <a name="set-up-an-azure-network"></a>設定 Azure 的網路

設定 Azure 的網路。 您需要此 Azure Vm 建立之後容錯移轉連線至網路。

- 網路應該位於您將所修復服務保存庫部署為相同的區域。
- 根據您想要用於無法透過 Azure Vm 資源模型，您可以設定[資源管理員模式](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)] 或 [[標準模式](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)中的 Azure 網路設定。
- 我們建議您開始之前，您將網路設定。 如果您不需要進行此動作網站復原部署期間。

> [AZURE.NOTE] 用於部署網站復原的網路不支援[的網路移轉](../resource-group-move-resources.md)跨資源群組內的相同的訂閱或訂閱。

### <a name="set-up-an-azure-storage-account"></a>設定 Azure 儲存體帳戶

- 您必須保留資料複製到 Azure 標準 Azure 儲存體帳戶。
- 根據您想要用於無法透過 Azure Vm 資源模型，您會在[資源管理員模式](../storage/storage-create-storage-account.md)] 或 [[標準模式](../storage/storage-create-storage-account-classic-portal.md)中設定的帳戶。
- 我們建議您開始之前設定儲存帳戶。 如果您不需要進行此動作網站復原部署期間。 帳戶必須為修復服務保存庫相同的區域。

> [AZURE.NOTE] 儲存帳戶的部署網站復原不支援[的儲存空間帳戶移轉](../resource-group-move-resources.md)跨資源群組內的相同的訂閱或訂閱。

### <a name="prepare-the-hyper-v-hosts"></a>準備 HYPER-V 主機

- 請確定 HYPER-V 主機遵守[的先決條件](#on-premises-prerequisites)。

### <a name="create-a-recovery-services-vault"></a>建立修復服務保存庫

1. [Azure 入口網站](https://portal.azure.com)登入。
2. 按一下 [**新** > **管理** > **備份及網站修復 (OMS)**。 或者您可以按一下 [**瀏覽** > **修復服務**保存庫 >**新增**。

    ![新的保存庫](./media/site-recovery-hyper-v-site-to-azure/new-vault3.png)

3. 在 [**名稱]**中，指定識別保存庫好記的名稱。 如果您有多個訂閱，請選取其中之一。
4. [建立新的資源群組](../resource-group-template-deploy-portal.md)或選取現有的圖示，然後指定 Azure 的區域。 電腦會複製到此區域。 若要檢查支援的地區請參閱[Azure 網站復原定價詳細資料](https://azure.microsoft.com/pricing/details/site-recovery/)中的地理可用性
4. 如果您想要從儀表板的 [快速存取保存庫按一下 [**固定至儀表板**，然後按一下 [**建立保存庫**。

    ![新的保存庫](./media/site-recovery-hyper-v-site-to-azure/new-vault-settings.png)

新的保存庫會出現在**儀表板**上 > **所有資源**，然後在 [主要**修復服務保存庫**刀。

## <a name="getting-started"></a>快速入門

網站復原提供快速入門體驗，可協助您以最快速部署。 開始使用檢查先決條件並引導您瞭解部署步驟順序正確的網站復原。

在 [開始使用您選取類型的電腦您想要複製，和您要複製到的位置。 您將內部部署伺服器、 Azure 儲存體帳戶及網路設定。 您建立複寫原則，並執行容量計劃。 您已設定您的基礎結構之後，您會啟用 Vm 複寫。 您可以執行錯誤後移轉特定的電腦，或建立失敗的多部電腦上的修復計劃。

選擇您想要部署網站復原的如何開始快速入門。 快速入門流程稍微改變，根據您複製的需求。



## <a name="step-1-choose-your-protection-goals"></a>步驟 1︰ 選擇 [保護定目標

選取您要複製及您要複製到的位置。

1. **修復服務保存庫**刀中選取您保存庫，然後按一下 [**設定]**。
2. 在 [**設定** > **快速入門**按一下**網站復原** > **步驟 1︰ 準備基礎結構** > **保護目標**。

    ![選擇目標](./media/site-recovery-hyper-v-site-to-azure/choose-goals.png)

3. **保護目標**] 中選取**至 Azure**，並選取**[是，使用 HYPER-V**。 選取 [**否]**確認您不使用 VMM。 然後按一下**[確定]**。

    ![選擇目標](./media/site-recovery-hyper-v-site-to-azure/choose-goals2.png)


## <a name="step-2-set-up-the-source-environment"></a>步驟 2︰ 設定來源環境

設定 HYPER-V 網站、 Azure 網站修復提供者和 Azure 修復服務代理程式安裝在 HYPER-V 主機和註冊主機保存庫中。


1. 按一下 [**步驟 2︰ 準備基礎結構** > **來源**。 若要新增新 HYPER-V 網站為容器 HYPER-V 主機或叢集，按一下**[+ HYPER-V 網站**。

    ![設定來源](./media/site-recovery-hyper-v-site-to-azure/set-source1.png)

2. 在**建立 HYPER-V 網站**刀指定網站的名稱。 然後按一下**[確定]**。 選取您剛剛建立的網站。

    ![設定來源](./media/site-recovery-hyper-v-site-to-azure/set-source2.png)

3. 按一下**+ HYPER-V 伺服器**以新增至網站的伺服器。
4. **新增伺服器**中 > **伺服器類型**檢查**HYPER-V 伺服器**會顯示。 請確定您想要新增 HYPER-V 伺服器符合[先決條件](#on-premises-prerequisites)能夠存取指定的 Url。
4. 下載 Azure 網站修復提供者安裝檔案。 您會執行此安裝 [每個 HYPER-V 主機上的 [提供者，並修復服務代理程式的檔案。
5. 下載登錄機碼。 當您執行安裝程式時，您將需要此。 5 天後產生，是有效的金鑰。

    ![設定來源](./media/site-recovery-hyper-v-site-to-azure/set-source3.png)

6. 執行提供者您新增至 HYPER-V 網站的每個主機上的 [安裝] 檔案。 如果您在 HYPER-V 叢集安裝，請在每個叢集節點上執行安裝程式。 安裝並註冊每個 HYPER-V 叢集節點可確保虛擬機器持續受到保護，即使他們移轉節點。

### <a name="install-the-provider-and-agent"></a>安裝提供者與代理程式

1. 執行提供者設定檔。
2. 在 [ **Microsoft Update**您可以選擇在更新以便會根據您的 Microsoft Update 原則的安裝提供者的更新。
3. 在**安裝**接受或修改預設提供者的安裝位置，然後按一下 [**安裝**]。
4. 在**保存庫設定**] 頁面上，按一下 [以選取您所下載的保存庫檔案的 [**瀏覽**]。 指定 Azure 網站復原訂閱、 保存庫名稱，以及 HYPER-V 伺服器所屬的 HYPER-V 網站。

    ![伺服器註冊](./media/site-recovery-hyper-v-site-to-azure/provider3.png)

5. **Proxy 設定**中指定將會安裝在伺服器的提供者連線至 Azure 網站復原網際網路的方式。

- 如果您想要直接連接的提供者選取**直接不 proxy 的連線**。
- 如果您想要與目前在伺服器設定 proxy 連線選取**現有的 proxy 設定的連線**。
- 如果您現有的 proxy 需要驗證]，或您想要使用**自訂的 proxy 設定連線**提供者連線選取自訂的 proxy。
- 如果您是使用自訂 proxy 必須先指定的地址、 連接埠和認證
- 如果您使用 proxy 產生確定[先決條件](#on-premises-prerequisites)所述的 Url 允許瀏覽。

    ![網際網路](./media/site-recovery-hyper-v-site-to-azure/provider7.PNG)

6.安裝完成後按一下 [**註冊**] 以註冊保存庫中的伺服器。

![安裝位置](./media/site-recovery-hyper-v-site-to-azure/provider2.png)

7.註冊中繼資料之後 HYPER-V 從伺服器擷取 Azure 網站復原伺服器上也會顯示**設定** > **網站復原基礎結構** > **HYPER-V 主機**刀。


### <a name="command-line-installation"></a>命令列安裝

Azure 網站修復提供者和代理程式也可以安裝使用下列命令列。 這個方法可以用於伺服器核心的 Windows Server 2012 R2 上安裝的提供者。

1. 下載資料夾的提供者安裝檔案及登錄機碼。 例如 C:\ASR。
2. 提高權限的命令提示字元中，執行下列命令來擷取的提供者安裝程式︰

            C:\Windows\System32> CD C:\ASR
            C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
3. 執行此命令來安裝元件︰

            C:\ASR> setupdr.exe /i

4. 然後執行下列命令登錄保存庫中的伺服器︰ CD C:\Program Files\Microsoft Azure 網站復原 Provider\
            C:\Program Files\Microsoft Azure 網站修復提供者\>DRConfigurator.exe /r /Friendlyname <friendly name of the server> /認證<path of the credentials file>

<br/>
位置︰

- **/Credentials** ︰ 強制參數會指定登錄鍵檔案所在的位置  
- **/FriendlyName** ︰ 強制參數會出現在 Azure 網站復原入口網站中的 HYPER-V 主機伺服器名稱。
- **/proxyAddress** ︰ 選用的參數會指定 proxy 伺服器的位址。
- **/proxyport** ︰ 選用的參數會指定 proxy 伺服器的連接埠。
- **/proxyUsername** ︰ 選用的參數會指定 Proxy 使用者名稱 （如果 proxy 需要驗證）。
- **/proxyPassword** ︰ 選用的參數會指定驗證的 proxy 伺服器 （如果 proxy 需要驗證） 的密碼。


## <a name="step-3-set-up-the-target-environment"></a>步驟 3︰ 設定目標環境

指定要用於複寫和 Azure 網路的 Azure Vm 會將連線後移轉後的 Azure 儲存體帳戶。

1.  按一下 [**準備基礎結構** > **目標**並選取您想要使用的 Azure 訂閱。
2.  指定您想要使用的 Vm 後移轉後的部署模型。
3.  網站復原檢查您有一個或多個相容 Azure 儲存體帳戶與網路。

    ![儲存空間](./media/site-recovery-hyper-v-site-to-azure/select-target.png)

4.  如果您還沒有建立儲存帳戶，而且您想要建立一個使用資源管理員的 [ **+ 的儲存空間**執行該文字間的帳戶。 在**建立儲存帳戶**刀指定帳戶名稱、 類型、 訂閱及位置。 帳戶應該位於修復服務保存庫相同的位置。

    ![儲存空間](./media/site-recovery-hyper-v-site-to-azure/gs-createstorage.png)

    如果您想要建立使用傳統模式儲存帳戶必須執行該[Azure 入口網站中](../storage/storage-create-storage-account-classic-portal.md)。

5.  如果您還沒有建立 Azure 的網路，且您想要建立一個使用資源管理員按一下**+ 網路**執行該內嵌。 在**建立虛擬網路**刀指定網路名稱、 地址範圍、 子網路詳細資料、 訂閱及位置。 網路應該位於修復服務保存庫相同的位置。

    ![網路](./media/site-recovery-hyper-v-site-to-azure/gs-createnetwork.png)

    如果您想要建立使用傳統模式網路必須執行該[Azure 入口網站中](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)。


## <a name="step-4-set-up-replication-settings"></a>步驟 4︰ 設定複寫設定

1. 若要建立新的複寫原則按一下**準備基礎結構** > **複寫設定** > **+ 建立和關聯**。

    ![網路](./media/site-recovery-hyper-v-site-to-azure/gs-replication.png)

2. **建立和相關的原則**中指定的原則名稱。
3. 在**複製頻率**指定您想要在初始複寫 （每隔 30 秒，5 到 15 分鐘） 之後複寫 delta 資料的頻率。
4. 在 [**復原點保留**，指定時多久 [保留] 視窗會針對每個復原點。 受保護的電腦可以復原視窗中的任何位置。
6. 在**應用程式一致的快照頻率**指定頻率 （1-12 小時） 包含應用程式一致的快照的復原點是建立。 HYPER-V 使用兩種類型的快照集-的標準的快照，可提供完整的虛擬機器的累加快照集和應用程式一致的快照的時間點資料的快照應用程式虛擬機器內。 應用程式一致的快照，以確保應用程式都一致時，會使用大量陰影複製服務 (VSS)。 請注意，是否您啟用應用程式一致的快照，它會影響來源虛擬機器上執行應用程式的效能。 請確定您設定的值小於您設定的其他修復點的數目。
3. 在**初始複寫開始時間**中，指定何時開始初始複寫。 您的網際網路頻寬，因此您可能要排定外您忙碌時間發生複寫。 然後按一下**[確定]**。

    ![複寫原則](./media/site-recovery-hyper-v-site-to-azure/gs-replication2.png)

當您建立新原則時，會自動與相關聯 HYPER-V 網站。 按一下**[確定]**。 您可以與建立關聯 HYPER-V 網站 （及的 Vm）**設定**] 中的多個複寫原則 > **複寫**> 原則名稱 >**關聯 HYPER-V 網站**。

## <a name="step-5-capacity-planning"></a>步驟 5︰ 容量計劃

您已經有您的基本設定您的基礎結構可以考慮容量計劃，並找出您是否需要額外的資源。

復原網站提供可協助您為您的來源環境、 網站復原元件、 網路和儲存空間配置正確的資源容量規劃。 您可以執行規劃，以快速模式估算根據 Vm、 磁碟和儲存空間，平均數目，或會在其中輸入數字的工作量層級的詳細模式中。 您開始之前，您必須︰

- 收集您複寫環境，包括 Vm Vm 中硬碟，每個磁碟的儲存空間的相關資訊。
- 估計每日的變更 （變換） 率，您必須複製資料。 您可以使用[HYPER-V 複本的容量規劃](https://www.microsoft.com/download/details.aspx?id=39057)，以協助您執行此動作。

1.  按一下 [若要下載的工具，然後執行 [**下載**]。 [已讀取，請參閱](site-recovery-capacity-planner.md)工具隨附。
2.  完成後選取**[是]** ，在**您已執行容量規劃**嗎？

    ![容量計劃](./media/site-recovery-hyper-v-site-to-azure/gs-capacity-planning.png)

### <a name="network-bandwidth-considerations"></a>網路頻寬考量

若要計算複寫 （初始複寫然後 delta） 的所需的頻寬，您可以使用容量規劃工具。 若要控制的頻寬複寫適用於您有幾個選項︰

- **節流頻寬**︰ HYPER-V 流量，複製到 Azure 經歷的特定 HYPER-V 主機。 您可以節流頻寬主機伺服器上。
- **調整頻寬**︰ 您可能會影響用於使用幾個登錄機碼複寫的頻寬。

#### <a name="throttle-bandwidth"></a>節流頻寬

1. 開啟 Microsoft Azure 備份 MMC 嵌入式管理單元 HYPER-V 主機伺服器上。 根據預設 Microsoft Azure 備份的快速鍵是在桌面上或 C:\Program Files\Microsoft Azure 復原服務 Agent\bin\wabadmin。
2. 在嵌入式管理單元中按一下 [**變更內容**]。
3. **Throttling** ] 索引標籤上選取 [**啟用節流備份作業的網際網路頻寬使用量**，設定工作的限制和非工作時間。 有效範圍是從 512 Kbps 102 Mbps 秒。

    ![節流頻寬](./media/site-recovery-hyper-v-site-to-azure/throttle2.png)

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

## <a name="step-6-enable-replication"></a>步驟 6︰ 啟用複寫

現在，如下所示啟用複寫︰

1. 按一下 [**步驟 2︰ 複製應用程式** > **來源**。 啟用複寫第一次後按一下**+ 複寫**中啟用其他電腦的複製保存庫。

    ![啟用複寫](./media/site-recovery-hyper-v-site-to-azure/enable-replication.png)

2. 在**來源**刀 > 選取 HYPER-V 網站。 然後按一下**[確定]**。
3. 在**目標**選取保存庫訂閱，與您想要使用 Azure （傳統或資源管理） 中後移轉後的容錯移轉模型。
4. 選取您想要使用的儲存空間帳戶。 如果您想要使用不同的儲存的帳戶可能會讓您可以[建立一個](#set-up-an-azure-storage-account)。 若要建立儲存體帳戶使用資源管理員模型按一下 [**建立新**。 如果您想要建立使用傳統模式儲存帳戶必須執行該[Azure 入口網站中](../storage/storage-create-storage-account-classic-portal.md)。 然後按一下**[確定]**。
5.  選取 [Azure 網路和子網路時它們開始後移轉後，Azure Vm 會將連線。 選取 [**目前的選取電腦的設定**]，以將網路設定套用至所有電腦您選取 [保護]。 選取 [**更新版本設定**]，以選取 Azure 網路每部電腦]。 如果您想要使用不同的網路會有您可以[建立一個](#set-up-an-azure-network)。 若要建立網路使用的資源管理員模型按一下 [**建立新**。如果您想要建立使用傳統模式網路必須執行該[Azure 入口網站中](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)。 如果有的話，請選取 [子網路]。 然後按一下**[確定]**。

    ![啟用複寫](./media/site-recovery-hyper-v-site-to-azure/enable-replication11.png)

6. **虛擬機器**中 > **選取虛擬機器**按一下，然後選取您想要複製的每一部電腦。 您可以只選取可以啟用複寫的電腦。 然後按一下**[確定]**。

    ![啟用複寫](./media/site-recovery-hyper-v-site-to-azure/enable-replication5.png)

11. 在 [**內容** > **設定屬性**] 下，選取的 Vm，和 OS 磁碟選取的作業系統。 驗證 Azure VM 名稱 （目標名稱） 遵守[Azure 虛擬機器需求](site-recovery-best-practices.md#azure-virtual-machine-requirements)，如果您需要加以修改。 然後按一下**[確定]**。 您可以稍後設定其他屬性。

    ![啟用複寫](./media/site-recovery-hyper-v-site-to-azure/enable-replication6.png)

12. **複製設定**中 > **複寫設定**] 下，選取您想要套用的 [受保護的 Vm 複寫原則。 然後按一下**[確定]**。 您可以修改**設定**的複寫原則 > **複寫原則**> 原則名稱 >**編輯設定**。 已複製的電腦和新的電腦都將使用您套用的變更。

    ![啟用複寫](./media/site-recovery-hyper-v-site-to-azure/enable-replication7.png)

您可以追蹤進度**設定**中**啟用保護**工作的 > **工作** > **網站復原工作**。 **完成保護**工作執行電腦之後，就可以容錯移轉。

### <a name="view-and-manage-vm-properties"></a>檢視及管理 VM 屬性

我們建議您確認來源電腦的內容。

1. 按一下 [**設定** > **受保護的項目** > **複製項目**> 並選取電腦。

    ![啟用複寫](./media/site-recovery-hyper-v-site-to-azure/test-failover1.png)

2. 在 [**內容**中，您可以檢視 vm 複寫和容錯移轉資訊。

    ![啟用複寫](./media/site-recovery-hyper-v-site-to-azure/test-failover2.png)

3. 在**計算和網路** > **計算屬性**您可以指定 Azure 虛擬記憶體名稱與目標的大小。 修改以符合 Azure 的需求，如果您要的名稱。 您也可以檢視及修改目標網路、 子網路，以及將 Azure vm 指定的 IP 位址相關資訊。 請注意下列事項︰

    - 您可以設定的目標 IP 位址。 如果您沒有在電腦上提供的地址，失敗會使用 DHCP。 如果您設定的位址，無法在錯誤後移轉，將會失敗容錯移轉。 相同的目標 IP 位址可用於測試容錯移轉如果地址被用於測試容錯移轉網路。
    - 您指定為目標虛擬機器，，如下所示的大小取決於網路介面卡數︰

        - 如果來源電腦上的網路介面卡的數字小於或等於的可容許的目標電腦大小的介面卡的數字，然後目標會有數相同的介面卡做為來源。
        - 如果來源虛擬機器介面卡數目超過允許的目標大小然後目標大小的最大值將用於的數字。
        - 例如，如果來源電腦有兩個網路介面卡，目標電腦大小支援四個目標電腦會有兩個介面卡。 如果來源電腦有兩個介面卡，但支援的目標大小僅支援其中一個目標電腦會有只有一個介面卡。     
        - 如果 VM 有多個網路介面卡，將所有連線到相同的網路。

    ![啟用複寫](./media/site-recovery-hyper-v-site-to-azure/test-failover4.png)

5.  在 [**磁碟**就可以看見的作業系統和資料磁碟 VM 複寫。


## <a name="step-7-test-the-deployment"></a>步驟 7︰ 測試部署

若要測試部署中，您可以執行測試容錯移轉單一虛擬機器或包含一或多個虛擬機器復原計劃。


### <a name="prepare-for-test-failover"></a>準備進行測試容錯移轉

- 若要執行測試容錯移轉我們建議您從 Azure 生產網路 （Azure 中建立新的網路時，這是預設行為），建立新的 Azure 網路，具有隔離。 [瞭解更多](site-recovery-failover.md#run-a-test-failover)有關執行測試容錯移轉。
- 為獲得最佳效能，您無法透過 Azure，請在 [受保護的電腦上安裝 Azure 代理程式。 它可讓您更快速開機，並可協助進行疑難排解。 安裝[Linux](https://github.com/Azure/WALinuxAgent)或[Windows](http://go.microsoft.com/fwlink/?LinkID=394789)代理程式。
- 若要完全測試您的部署，您需要基礎結構，以複製電腦如預期般運作。 如果您想要測試 Active Directory 和 DNS，可以建立 dns 網域控制站的虛擬機器並將此複寫到 Azure 使用 Azure 網站復原。 閱讀更多在[Active Directory 測試容錯移轉考量](site-recovery-active-directory.md#considerations-for-test-failover)。
- 如果您想要執行，而不是測試容錯移轉意外的容錯移轉請注意下列動作︰

    - 如果可以的話您應該關閉主要電腦才能執行意外的容錯移轉。 如此一來，可確保您沒有安裝來源與複本的電腦執行一次。
    - 當您執行意外的容錯移轉時，開始意外的容錯移轉之後，就不會傳送任何資料 delta 會停止資料複寫從主要的電腦。 此外如果您執行復原計劃意外的容錯移轉之前會執行完成，即使發生錯誤。

### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>連線至 Azure Vm 後移轉後的準備

如果您要連線至 Azure Vm 使用 RDP 後移轉後，請確定您執行下列動作︰

**容錯移轉之前的內部部署電腦上**︰

- 在網際網路上存取啟用 RDP，確保 TCP 和 UDP 規則會新增為**公開**，並確保 RDP 可在**Windows 防火牆** -> **允許應用程式和功能**的所有設定檔。
- 存取網站-連線啟用 RDP 在電腦上，並確保 RDP 是在**Windows 防火牆** -> **允許應用程式和功能**的**網域**與**私人**網路。
- 在內部部署電腦上安裝[Azure VM 代理程式](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。
- 確定作業系統的舊原則會設定為 OnlineAll。 [深入瞭解]( https://support.microsoft.com/kb/3031135)
- 執行容錯移轉前，請關閉 IPSec 服務。

**在 Azure VM 後移轉後**︰

- 將允許 RDP Azure VM 相關聯的 NIC 中的公用 IP 位址。
- 請確定您沒有任何網域原則，無法連線至虛擬機器使用公用的地址。
- 請嘗試連線。 如果您無法連線確認 VM 正在執行。 如需其他疑難排解秘訣請閱讀[本文](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)。

如果您想要存取後使用安全命令介面的用戶端 (ssh) 的移轉後執行 Linux Azure VM，請執行下列動作︰

**容錯移轉之前的內部部署電腦上**︰

- 請確定 Azure VM 上的安全命令介面服務會設定為自動啟動，在系統開機。
- 核取防火牆規則，讓它的 SSH 連線。

**在 Azure VM 後移轉後**︰

- 網路安全性] 群組上的規則失敗 VM 和 Azure 子網路連線到的必須允許連入連線 SSH 連接埠。
- 若要允許 SSH 連接埠 （TCP 連接埠 22 預設） 上的連入連線，應該建立公用結束點。
- 如果 VM 存取透過 VPN 連線 （Express 路由或網站-VPN） 用戶端可以使用透過 SSH 直接連接 VM。

### <a name="run-a-test-failover"></a>執行測試容錯移轉

若要執行測試容錯移轉執行下列動作︰

1. 在 [**設定**] 中的單一 VM 失敗 > **複製項目**]，按一下 VM > **+ 測試容錯移轉**。

    ![測試容錯移轉](./media/site-recovery-hyper-v-site-to-azure/run-failover1.png)

2. 透過復原計劃，在 [**設定**失敗 > **復原方案**，以滑鼠右鍵按一下方案 >**測試容錯移轉**。 若要建立復原計劃[依照這些指示進行](site-recovery-create-recovery-plans.md)。

3. 在**測試容錯移轉**選取 Azure 的 Azure Vm 將連線之後發生錯誤後移轉的網路。

    ![測試容錯移轉](./media/site-recovery-hyper-v-site-to-azure/run-failover2.png)

4. 按一下**[確定]**以開始容錯移轉]。 您可以按一下以開啟其屬性，VM 上] 或 [**測試容錯移轉**工作**設定**中追蹤進度 > **網站復原工作**。
5. 當容錯移轉達到**完成測試**階段時，執行下列動作︰
    1. 檢視複本虛擬機器中的 Azure 入口網站。 驗證成功啟動虛擬機器。
    2. 如果您是從您的內部部署網路設定 access 的虛擬機器可以啟動遠端桌面連線到虛擬機器。
    3. 按一下 [完成，**完成測試**]。
    4. 按一下 [**備忘稿**，錄製並儲存聯測試容錯移轉任何觀察值]。
    5. 按一下 [**測試容錯移轉完成**。 清理自動關閉電源，並刪除測試虛擬機器測試環境。
    6. 此時會刪除任何項目或 Vm 測試容錯移轉時自動建立的網站復原。 無法刪除您建立的測試容錯移轉任何其他項目。

    > [AZURE.NOTE] 如果測試容錯移轉持續時間超過兩週才能完成它是由強制中完成。

6. 容錯移轉完成後，您也應該能夠看到複本 Azure 電腦會出現在 Azure 入口網站 >**虛擬機器**。 請確定 VM 適當的大小，其具有連接到適當的網路，且正在執行。
7. 如果您[準備好連線後移轉後的](#prepare-to-connect-to-Azure-VMs-after-failover)您可以連線至 Azure VM。


## <a name="failover"></a>容錯移轉

初始複寫完成您的電腦之後，您可以在需要時叫用容錯移轉。 網站復原支援各種類型的容錯移轉-測試容錯移轉預定容錯移轉與意外的容錯移轉。
[瞭解更多](site-recovery-failover.md)有關不同類型的容錯移轉以及何時及如何執行每個詳細的說明。

> [AZURE.NOTE] 如果您想要將虛擬機器移轉到 Azure，我們強烈建議您在將虛擬機器移轉到 Azure 使用[計劃的容錯移轉作業](site-recovery-failover.md#run-a-planned-failover-primary-to-secondary)。 一旦使用測試容錯移轉 Azure 中驗證移轉應用程式時，使用[完成](#Complete-migration-of-your-virtual-machines-to-Azure)的移轉所述的步驟完成您的虛擬機器移轉。 您不需要執行 [認可] 或 [刪除。 完成移轉完成移轉中移除保護虛擬機器與停駐點的電腦的 Azure 網站復原計費。


###<a name="run-an-unplanned-failover"></a>執行意外的容錯移轉

此應該主要網站時無法存取由於非預期的事件，例如電源中斷或病毒攻擊選擇。 此程序說明如何執行 「 意外的容錯移轉' 復原計劃。 或者您可以執行單一虛擬機器容錯的移轉虛擬機器] 索引標籤上。 在開始之前，請確定您想要透過失敗的所有虛擬機器已都完成初始複寫。

1. 選取 [**復原方案 > recoveryplan_name**。
2. 在 [復原計劃刀中，按一下 [**意外的容錯移轉**。
3. 在 [**意外的容錯移轉**] 頁面中，選擇 [來源及目標位置]。
4. 選取 [**關閉虛擬機器並同步處理的最新資料**，指定網站復原應該嘗試關閉 [受保護的虛擬機器並同步處理資料，使最新版的資料會移轉。
5. 後移轉後，虛擬機器位於狀態等待認可中。  按一下 [**認可**]，以確認容錯移轉。

[深入瞭解](site-recovery-failover.md#run-an-unplanned-failover)

## <a name="complete-migration-of-your-virtual-machines-to-azure"></a>完成移轉至 Azure 虛擬機器


>[AZURE.NOTE] 下列步驟僅適用於您要移轉至 Azure 的虛擬機器

1. 以 [提及[以下](site-recovery-failover.md)執行計劃的容錯移轉
2. 在 [**設定 > 複製項目**，請以滑鼠右鍵按一下虛擬機器，然後選取 [**完成移轉**

    ![completemigration](./media/site-recovery-hyper-v-site-to-azure/migrate.png)

2. 按一下**[確定**] 完成移轉。 您可以追蹤進度，以開啟其內容，VM 上按一下或使用中的完成的移轉工作**設定 > 網站復原工作**。


## <a name="monitor-your-deployment"></a>監控您的部署

以下是如何監視設定的設定、 狀態及狀況網站復原部署︰

1. 按一下 [保存庫名稱，即可存取**基本資訊**儀表板上。 在此儀表板，您可以網站復原工作、 複寫狀態、 修復計劃、 伺服器狀況和事件。  您可以自訂顯示的並排顯示和給您，包括其他網站復原和備份保存庫的狀態最有用的版面配置的基礎。

    ![基本版](./media/site-recovery-hyper-v-site-to-azure/essentials.png)

2. [**健康狀況**] 方塊中，您可以監視網站伺服器所遇到的問題，然後在過去 24 小時網站復原引發的事件。
3. 您可以管理並監控複寫中**複製的項目**，**修復方案**，然後**網站復原工作**磚。 您可以深入瞭解**設定**中的工作 -> **工作** -> **網站復原工作**。
