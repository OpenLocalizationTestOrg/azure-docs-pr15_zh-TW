<properties
    pageTitle="第二個 VMM 網站 Azure 入口網站複製 HYPER-V 虛擬機器中 VMM 雲朵 |Microsoft Azure"
    description="說明如何部署 Azure 網站復原協調複寫、 錯誤移轉及復原 HYPER-V Vm VMM 雲朵中的次要 VMM 網站 Azure 入口網站。"
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
    ms.date="08/23/2016"
    ms.author="raynew"/>

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site-using-the-azure-portal"></a>複製 HYPER-V 虛擬機器中 VMM 雲朵次要 VMM 網站 Azure 入口網站

> [AZURE.SELECTOR]
- [Azure 入口網站](site-recovery-vmm-to-vmm.md)
- [傳統的入口網站](site-recovery-vmm-to-vmm-classic.md)
- [PowerShell-資源管理員](site-recovery-vmm-to-vmm-powershell-resource-manager.md)

歡迎使用 Azure 網站復原 ！ 如果您想要複製內部部署 HYPER-V 虛擬機器這份文件管理系統管理中心的虛擬機器管理員 (VMM) 雲朵次要網站中使用。 本文將說明如何設定複寫 Azure 入口網站中使用 Azure 網站復原。

> [AZURE.NOTE] Azure 有兩種不同的[部署模型](../resource-manager-deployment-model.md)建立及使用的資源︰ Azure 資源管理員] 及 [傳統。 Azure 也會有兩個入口網站 – 支援傳統部署模型，Azure 傳統入口網站和支援兩種部署模型 Azure 入口網站。


Azure 入口網站中的 azure 網站復原提供幾項新功能︰

- Azure 中入口網站、 Azure 備份和 Azure 網站修復服務會組合成單一修復服務保存庫，以便您可以設定及管理業務連續性和損毀修復 (BCDR) 從單一位置。 整合儀表板，可讓您監控和管理您的內部部署網站與 Azure 公用雲端作業。
- 現在，佈建與雲端解決方案提供者 (CSP) 程式 Azure 訂閱的使用者能管理 Azure 入口網站中的網站修復作業。


閱讀本文之後, 張貼底部 Disqus 註解中的任何註解。 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上提出技術的問題。


## <a name="overview"></a>概觀

組織必須決定如何應用程式、 負載，以及資料保持執行及使用期間計劃和計畫停機時間和越快越復原正常運作狀況 BCDR 策略。 您 BCDR 策略應該安全及修復，保留商務資料，並確保當發生損毀時，仍然持續可用的工作量。

網站復原是 Azure 服務可提供您 BCDR 策略來協調實體伺服器的內部部署和雲端 (Azure) 或次要資料中心的虛擬機器複寫。 中斷問題發生時在您的主要位置，您無法透過，讓應用程式可用的工作負載的第二個位置。 會傳回標準作業時，您會回到您的主要位置失敗。 如需深入瞭解[什麼是 Azure 網站復原？](site-recovery-overview.md)

本文提供您要複製的所有資訊內部部署 HYPER-V Vm VMM 雲朵次要 VMM 網站中。 其包含架構的概觀，規劃的詳細資訊，以及設定內部部署伺服器複寫設定與容量規劃部署步驟。 您已設定基礎結構之後，您可以啟用複製您要保護，請核取該容錯移轉運作的電腦上。

## <a name="business-advantages"></a>商務優點

- 網站復原提供保護商務工作量及複製它們的次要 HYPER-V 伺服器 HYPER-V Vm 上執行應用程式。
- 修復服務入口網站提供設定、 管理及監視複寫、 錯誤移轉及復原的單一位置。
- 您可以執行輕鬆地執行容錯移轉您的內部部署的主要基礎結構的次要網站，並回復 （還原） 從主要的次要網站。
- 讓多層應用程式負載失敗在一起，您可以設定修復計劃與多部電腦。

## <a name="scenario-architecture"></a>案例架構

以下是分析藍本元件︰

- **主要網站**︰ 在主要的網站中，有一或多個 HYPER-V 主機伺服器執行來源 Vm 您想要複製。 下列主要主機伺服器位於 VMM 私人的雲端。
- **次要網站**︰ 在次要網站中，有執行目標 Vm 複製主要 Vm 的一或多個 HYPER-V host （主機） 伺服器。 這些主機伺服器位於 VMM 私人的雲端。 雲端可以在主要伺服器 （如果您只需要在單一 VMM 伺服器） 或次要 VMM 伺服器上。
- **提供者**︰ 網站復原部署時 VMM 伺服器上安裝 Azure 網站修復提供者，並在修復服務保存庫註冊那些伺服器。 透過 HTTPS 443 複寫協調流程，與網站復原通訊 VMM 伺服器上執行的提供者。 資料複寫發生之間主要和次要 HYPER-V host （主機） 伺服器。 複製的資料在內部部署網站與網路保持不變，並不會傳送給 Azure。 進一步瞭解[隱私權](#privacy-information-for-site-recovery)。

![E2E 拓撲](./media/site-recovery-vmm-to-vmm/architecture.png)

### <a name="data-privacy-overview"></a>資料隱私權概觀

下表摘要列出在這個案例中儲存資料的方式︰
****
巨集指令 | **詳細資料** | **收集的資料** | **使用** | **所需**
--- | --- | --- | --- | ---
**註冊** | 您在修復服務保存庫註冊 VMM 伺服器。 如果您稍後想要移除登錄伺服器，您可以執行刪除從 Azure 入口網站的伺服器資訊。 | 註冊 VMM 伺服器後網站復原收集，處理程序，，以及傳輸 VMM 伺服器和偵測到的網站復原 VMM 雲朵名稱的中繼資料。 | 資料用來識別與適當的 VMM 伺服器通訊和設定適當 VMM 雲朵的設定。 | 需要這項功能。 如果您不想要將此資訊傳送給網站復原您不應使用網站復原服務。
**啟用複寫** | Azure 網站修復提供者 VMM 伺服器上已安裝，而是導管與網站復原服務進行通訊。 提供者是裝載於 VMM 程序的動態連結程式庫 (DLL)。 提供者安裝之後，「 資料中心復原 」 功能會啟用 VMM 管理主控台] 中。 新的或現有 Vm 可以啟用此功能啟動 VM 保護。 | 設定此屬性，提供者會傳送的名稱和 ID VM 網站復原。  複寫被啟用的 Windows Server 2012 或 Windows Server 2012 R2 HYPER-V 複本。 虛擬機器資料會複製到另一個 （通常位於不同的 「 復原 」 資料中心） HYPER-V 主機。 | 網站復原填入 VM 資訊 Azure 入口網站中的使用中繼資料。 | 此功能服務不可或缺的一部分，而不關閉。 如果您不想要傳送此資訊，不讓網站復原 Vm 保護。 請注意，透過 HTTPS 傳送至網站復原提供者所傳送的所有資料。
**復原計劃** | 修復計劃可協助您建立的復原資料中心的協調流程方案。 您可以定義應該復原網站啟動 Vm 或群組的虛擬機器的順序。 您也可以指定所要執行，或任何手動所要採取，一次的每個 VM 復原的動作的自動化指令碼。 容錯移轉通常會觸發層級復原計劃一致的復原。 | 網站復原收集、 處理程序和傳輸復原計劃，包括虛擬機器中繼資料及中繼資料的任何自動化指令碼和手動動作備忘稿的中繼資料。 | 中繼資料用來建立 Azure 入口網站中的復原計劃。 | 此功能服務不可或缺的一部分，而不關閉。 如果您不想要將此資訊傳送給網站復原，沒有建立修復計劃。
**網路對應** | 地圖從主要的資料中心復原資料中心的網路資訊。 當 Vm 復原復原網站上時，網路對應可協助建立網路連線。 | 網站復原收集、 處理程序和傳輸邏輯網路的每個網站 （「 主要 」 和 「 資料中心 」） 的中繼資料。 | 中繼資料用來填入網路的設定，讓您可以將對應的網路資訊。 | 此功能服務不可或缺的一部分，而不關閉。 如果您不想要將此資訊傳送給網站復原，不要使用網路對應。
**容錯移轉 （計劃/未計劃/測試）** | 容錯移轉到另一個 VMM 管理資料中心 Vm 失敗。 Azure 入口網站中手動觸發容錯移轉動作。 | VMM 伺服器上的提供者網站復原容錯移轉事件的通知，並執行 HYPER-V 主機上的容錯移轉動作，透過 VMM 介面。 實際的容錯移轉 VM 的是另一個和藉由 Windows Server 2012 或 Windows Server 2012 R2 HYPER-V 複本處理 HYPER-V 主機。 容錯移轉完成之後，復原資料中心 VMM 伺服器上的提供者成功將資訊傳送到網站復原。 | 網站復原使用填入 Azure 入口網站中的容錯移轉動作資訊的狀態傳送的資訊。 | 此功能服務不可或缺的一部分，而不關閉。 如果您不想要將此資訊傳送給網站復原，不要使用容錯移轉。


## <a name="azure-prerequisites"></a>Azure 的先決條件

以下是您需要什麼 Azure 中部署這種情況︰

**必要條件** | **詳細資料**
--- | ---
**Azure**| 您必須以[Microsoft Azure](http://azure.microsoft.com/)帳戶。 您可以開始使用[免費的試用版](https://azure.microsoft.com/pricing/free-trial/)。 [瞭解更多](https://azure.microsoft.com/pricing/details/site-recovery/)有關網站復原價格。


## <a name="on-premises-prerequisites"></a>內部部署的先決條件

以下是您需要在內部部署的主要和次要網站中部署這種情況︰

**必要條件** | **詳細資料**
--- | ---
**VMM** | 我們建議您部署主站台 VMM 伺服器及 VMM 伺服器中的次要網站。<br/><br/> 您也可以[複製之間雲朵單一 VMM 伺服器上](site-recovery-single-vmm.md)。 若要執行此動作必須至少為兩個雲朵 VMM 伺服器上設定。<br/><br/> VMM 伺服器應該至少執行系統管理中心 2012 SP1 最新的更新。<br/><br/> 每個 VMM 伺服器必須在一或多個雲朵設定和所有雲朵必須設定 HYPER-V 容量設定檔。 <br/><br/>雲朵必須包含一個或多個 VMM 主機群組。<br/><br/>深入瞭解設定中[設定 VMM 雲端布料的轉印圖樣](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)VMM 雲朵和[逐步解說︰ 使用系統管理中心 2012 SP1 VMM 建立私人的雲朵](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx)。<br/><br/> VMM 伺服器需要存取網際網路。
**Hyper-v** | 必須至少執行 HYPER-V 伺服器 HYPER-V 角色與 Windows Server 2012，安裝最新的更新。<br/><br/> HYPER-V 伺服器應該包含一或多個 Vm。<br/><br/>  HYPER-V 主機伺服器應該位於主要和次要 VMM 雲朵主機群組。<br/><br/> 如果您 HYPER-V 集中在執行 Windows Server 2012 R2 您應該安裝[更新 2961977](https://support.microsoft.com/kb/2961977)<br/><br/> 如果您執行的 HYPER-V 集中在 Windows Server 2012，請注意如果您有靜態 IP 位址基礎叢集該叢集代理人不會自動建立。 您必須以手動方式設定叢集代理人。 [閱讀更多](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx)。
**提供者** | 在網站復原部署時，請在 VMM 伺服器上安裝 Azure 網站修復提供者。 透過協調複寫 HTTPS 443，網站復原進行通訊的提供者。 資料複寫發生之間的主要和次要 HYPER-V 伺服器 LAN 或 VPN 連線。<br/><br/> VMM 伺服器上執行的提供者需要存取這些 Url: *。 hypervrecoverymanager.windowsazure.com; *。accesscontrol.windows.net;*。 backup.windowsazure.com; *。blob.core.windows.net;*。 store.core.windows.net。<br/><br/> 此外允許[Azure 資料中心的 IP 範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653)從 VMM 伺服器防火牆通訊，並且允許 HTTPS (443) 通訊協定。

## <a name="prepare-for-deployment"></a>準備進行部署

準備要的部署︰

1. 網站復原部署[準備 VMM 伺服器](#prepare-the-vmm-server)。
2. [準備網路對應](#prepare-for-network-mapping)。 設定網路，好讓您可以設定網路對應。

### <a name="prepare-the-vmm-server"></a>準備 VMM 伺服器

請確定 VMM 伺服器符合[先決條件](#on-premises-prerequisites)可以存取所列的 Url。


### <a name="prepare-for-network-mapping"></a>準備網路對應

在 [主要和次要 VMM 伺服器上 VMM VM 網路之間的網路對應地圖︰

- 以最佳方式置於複本 Vm 次要 HYPER-V 主機後移轉後。
- 將複本 Vm 連線到適當的 VM 網路。
- 如果您沒有設定 Vm 無法連線至網路後移轉後的網路對應複本。
- 如果您想要設定的網路對應網站復原期間部署，以下是您的需要︰

    - 請確定來源 HYPER-V 主機伺服器上的 Vm 連線到 VMM VM 網路。 該網路應該邏輯網路雲端與相關聯的連結。
    - 請確認您用來復原次要雲端對應 VM 網路設定。 VM 網路應該邏輯網路與次要雲端相關聯的連結。

- [瞭解更多](site-recovery-network-mapping.md)有關網路對應的運作方式。

## <a name="prepare-for-deployment-with-a-single-vmm-server"></a>準備使用單一 VMM 伺服器的部署

如果您只需要在單一 VMM 伺服器您可以複製 Vm HYPER-V 主機 VMM 雲端[Azure](site-recovery-vmm-to-azure.md)或次要 VMM 雲端中。 我們建議您的第一個選項之間雲朵複寫不順暢，因為，但如果您需要執行此動作以下是您需要︰

1. **設定 HYPER-V VM 上 VMM**。 當執行此動作我們建議您可 VMM 相同 VM 上所使用的 SQL Server 執行個體。 只有一個 VM 具有建立為節省時間。 如果您想要使用的 SQL Server，並中斷遠端執行個體，就會發生，您需要先您可以復原 VMM 復原該執行個體。
2. **確保 VMM 伺服器具有兩個以上的雲朵設定**。 一個雲端 Vm 包含您想要複製及其他雲端做為次要的位置。 包含您要保護的 Vm 雲端必須符合[的先決條件](#on-premises-prerequisites)。
3. 設定網站復原本文所述。 建立並在保存庫註冊 VMM 伺服器、 設定複寫原則，並啟用複寫。 您應該指定初始複寫到網路發生地點。
4. 設定網路對應時您會對應到 VM 網路上的第二個雲端的 VM 網路上的 [主要的雲端。
5. 在 HYPER-V 管理員主控台，包含 VMM VM HYPER-V 主機上啟用 HYPER-V 複本，然後啟用複寫 VM 上。 請確定您沒有將 VMM 虛擬機器新增至網站復原，以確保網站復原不覆寫 HYPER-V 複本設定受到保護的雲朵。
6. 如果您建立的容錯移轉修復計劃您可以使用相同的 VMM 伺服器的來源及目標。
7. 您無法透過，並復原，如下所示︰

    - 手動容錯 VMM VM 至 HYPER-V 管理員使用計劃的容錯移轉的次要網站。
    - 透過 Vm 失敗。
    - 主要 VMM VM 復原之後，登入 Azure 入口網站]-> [之後修復服務地窖及執行意外的容錯移轉 Vm 的次要網站至主要的網站。
    - 意外的容錯移轉完成之後，所有資源可以都存取從主要的網站一次。


### <a name="create-a-recovery-services-vault"></a>建立修復服務保存庫

1. [Azure 入口網站](https://portal.azure.com)登入。
2. 按一下 [**新** > **管理** > **修復服務**。 或者您可以按一下 [**瀏覽** > **修復服務**保存庫 >**新增**。

    ![新的保存庫](./media/site-recovery-vmm-to-vmm/new-vault3.png)

3. 在 [**名稱]**中，指定識別保存庫好記的名稱。 如果您有多個訂閱，請選取其中之一。
4. [建立新的資源群組](../resource-group-template-deploy-portal.md)或選取現有的圖示，然後指定 Azure 的區域。 電腦會複製到此區域。 若要檢查支援的地區請參閱[Azure 網站復原定價詳細資料](https://azure.microsoft.com/pricing/details/site-recovery/)中的地理可用性
4. 如果您想要從儀表板的 [快速存取保存庫按一下 [**固定至儀表板** > **建立保存庫**。

    ![新的保存庫](./media/site-recovery-vmm-to-vmm/new-vault-settings.png)

新的保存庫會出現在**儀表板**上 > **所有資源**，然後在 [主要**修復服務保存庫**刀。




## <a name="getting-started"></a>快速入門

網站復原提供快速入門體驗，可協助您以最快速部署。 開始使用檢查先決條件並引導您瞭解部署步驟順序正確的網站復原。

在 [開始使用您選取類型的電腦您想要複製，和您要複製到的位置。 您將內部部署伺服器設定，建立複寫原則，並執行容量計劃。 您已設定您的基礎結構之後，您會啟用 Vm 複寫。 您可以執行錯誤後移轉特定的電腦，或建立失敗的多部電腦上的修復計劃。

選擇您想要部署網站復原的如何開始快速入門。 快速入門流程稍微改變，根據您複製的需求。

## <a name="step-1-choose-your-protection-goal"></a>步驟 1︰ 選擇您保護的目標

選取您要複製及您要複製到的位置。

1. **修復服務保存庫**刀中選取您保存庫，然後按一下 [**設定]**。
2. 在 [**設定** > **快速入門**按一下**網站復原** > **步驟 1︰ 準備基礎結構** > **保護目標**。
3. **保護目標**] 中選取**復原網站**，並選取**[是，使用 HYPER-V**。
4. 選取 [ **]** ，表示您用來管理 HYPER-V 主機]，並選取**[是]** ，如果您有次要 VMM 伺服器 VMM。 如果您部署之間的單一 VMM 伺服器上的雲朵複寫您按一下 [**否]**。 然後按一下**[確定]**。

    ![選擇目標](./media/site-recovery-vmm-to-vmm/choose-goals.png)


## <a name="step-2-set-up-the-source-environment"></a>步驟 2︰ 設定來源環境

安裝 Azure 網站修復提供者 VMM 在伺服器上，並在保存庫註冊伺服器。

1. 按一下 [**步驟 2︰ 準備基礎結構** > **來源**。

    ![設定來源](./media/site-recovery-vmm-to-vmm/goals-source.png)

2. 按一下 [**準備來源**中的 [ **+ VMM**以新增 VMM 伺服器]。

    ![設定來源](./media/site-recovery-vmm-to-vmm/set-source1.png)

2. 在**系統管理中心 VMM 伺服器**會出現在**伺服器類型**] 及 [的**新增伺服器**刀核取 VMM 伺服器符合的[必要條件和 URL 需求](#on-premises-prerequisites)。
4. 下載 Azure 網站修復提供者安裝檔案。
5. 下載登錄機碼。 您需要此當您執行安裝程式。 5 天後產生，是有效的金鑰。

    ![設定來源](./media/site-recovery-vmm-to-vmm/set-source3.png)

6. VMM 伺服器上安裝 Azure 網站修復提供者。

> [AZURE.NOTE] 您不需要明確 HYPER-V host （主機） 伺服器上安裝任何項目。


### <a name="set-up-the-azure-site-recovery-provider"></a>設定 Azure 網站修復提供者

1. 執行提供者每個 VMM 伺服器上的 [安裝] 檔案。 如果 VMM 配置叢集，且您要安裝的提供者，第一次就會將其安裝在作用中的節點，並完成安裝在保存庫註冊 VMM 伺服器。 再安裝在其他節點的提供者。 叢集節點應該執行同一版本的提供者。
2. 安裝程式執行幾個 prerequirement 檢查，並要求停止 VMM 服務的權限。 自動將重新啟動 VMM 服務，設定完成。 如果您要安裝在 VMM 叢集系統會提示您停止叢集角色。

2.  在 [ **Microsoft Update**您可以選擇在更新以便會根據您的 Microsoft Update 原則的安裝提供者的更新。
3. 在**安裝**接受或修改預設提供者的安裝位置，然後按一下 [**安裝**]。

    ![安裝位置](./media/site-recovery-vmm-to-vmm/provider-location.png)

3. 安裝完成後請按一下 [註冊伺服器保存庫中的 [**註冊**]。

    ![安裝位置](./media/site-recovery-vmm-to-vmm/provider-register.png)

9. 在 [**保存庫名稱**，確認伺服器註冊保存庫的名稱。 按一下 [*下一步*]。

    ![伺服器註冊](./media/site-recovery-vmm-to-vmm-classic/vaultcred.PNG)

7. 在 [**網際網路連線**指定 VMM 伺服器上執行的提供者連線至網際網路的方式。 選取 [**現有的 proxy 設定連線**，使用 [在伺服器上設定的預設網際網路連線設定。

    ![網際網路設定](./media/site-recovery-vmm-to-vmm-classic/proxydetails.PNG)

    - 如果您想要使用自訂 proxy，您應該將其設定之前安裝提供者。 當您設定自訂的 proxy 設定會執行檢查 proxy 連線測試。
    - 如果您使用自訂 proxy]，或您預設的 proxy 需要的驗證您需要輸入 proxy 詳細資料，包括 proxy 位址和連接埠。
    - 下列 url 應該是從 VMM 伺服器和 hyper-v 主機存取
        - *。 hypervrecoverymanager.windowsazure.com
        - *。 accesscontrol.windows.net
        - *。 backup.windowsazure.com
        - *。 blob.core.windows.net
        - *。 store.core.windows.net
    - 允許[Azure 資料中心 IP 範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (443) 通訊協定所述的 IP 位址。 您必須您打算使用 Azure 區域的白色清單 IP 範圍和西美國。
    - 如果您使用的自訂 proxy 會使用指定的 proxy 認證自動建立 VMM RunAs 帳戶 (DRAProxyAccount)。 設定 proxy 伺服器，以便能順利驗證此帳戶。 可以 VMM 主控台中修改 VMM RunAs 帳戶設定。 若要這麼做，開啟 [**設定**工作區、 展開 [**安全性]**、**執行方式帳戶**]，然後修改密碼 DRAProxyAccount 的。 必須重新啟動 VMM 服務，讓此設定才會生效。


8. 在**登錄機碼**中，選取您從 Azure 網站復原下載並複製到 VMM 伺服器的金鑰。


10.  您在中 VMM 雲朵 HYPER-V Vm 複寫到 Azure 時，才會使用加密設定。 如果您複製至次要網站不會使用它。

11.  在 [**伺服器名稱**，指定識別保存庫中的 VMM 伺服器好記的名稱。 在叢集設定中指定 VMM 叢集角色名稱。
12.  在**同步處理雲端中繼資料**選取是否想要保存庫與同步處理所有的雲朵 VMM 伺服器上的中繼資料。 此動作只需要進行一次在每個伺服器上。 如果您不想要同步處理所有的雲朵，您可以離開取消核取此設定，並同步處理個別在雲端中的屬性 VMM 主控台中每個雲端。

13.  按一下 [**下一步**完成程序。 註冊後，從 VMM 伺服器的中繼資料擷取是 Azure 網站復原。 伺服器會顯示在保存庫中的 [**伺服器**] 頁面上的 [ **VMM 伺服器**] 索引標籤。

    ![伺服器](./media/site-recovery-vmm-to-vmm-classic/provider13.PNG)

11. 伺服器用於**來源**中的網站復原主控台後 > **準備來源**選取 VMM 伺服器，然後選取 [HYPER-V 主機位於的雲端。 然後按一下**[確定]**。

#### <a name="command-line-installation"></a>命令列安裝

從命令列可安裝 Azure 網站修復提供者。 這個方法可以用於伺服器核心的 Windows Server 2012 R2 上安裝的提供者。

1. 下載資料夾的提供者安裝檔案及登錄機碼。 例如 C:\ASR。
2. 停止系統管理中心虛擬機器管理員服務。
3. 提高權限的命令提示字元中，執行下列命令來擷取的提供者安裝程式︰

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

4. 執行此命令來安裝提供者︰

        C:\ASR> setupdr.exe /i

5. 然後，請執行這些登錄伺服器保存庫中的命令︰

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>     

參數位置如下︰

 - **/Credentials**︰ 強制參數會指定登錄鍵檔案所在的位置  
 - **/FriendlyName**︰ 強制參數會出現在 Azure 網站復原入口網站中的 HYPER-V 主機伺服器名稱。
 - **/EncryptionEnabled**︰ 您只能使用從 VMM 在複寫到 Azure 時的選擇性參數。
 - **/proxyAddress**︰ 選用的參數會指定 proxy 伺服器的位址。
 - **/proxyport**︰ 選用的參數會指定 proxy 伺服器的連接埠。
 - **/proxyUsername**︰ 選用的參數會指定 Proxy 使用者名稱 （如果 proxy 需要驗證）。
 - **/proxyPassword**︰ 選用的參數會指定驗證的 proxy 伺服器 （如果 proxy 需要驗證） 的密碼。  

## <a name="step-3-set-up-the-target-environment"></a>步驟 3︰ 設定目標環境

選取目標 VMM 伺服器和雲端。

1. 按一下 [**準備基礎結構** > **目標**並選取您想要使用目標 VMM 伺服器。
2.  在伺服器上會與網站修復同步處理的雲朵隨即出現。 選取目標雲端。

    ![目標](./media/site-recovery-vmm-to-vmm/target-vmm.png)

## <a name="step-4-set-up-replication-settings"></a>步驟 4︰ 設定複寫設定

1. 若要建立新的複寫原則按一下**準備基礎結構** > **複寫設定** > **+ 建立和關聯**。

    ![網路](./media/site-recovery-vmm-to-vmm/gs-replication.png)

2. **建立和相關的原則**中指定的原則名稱。 來源及目標類型應該**HYPER-V**。
3. 在 [ **HYPER-V 主機版本**選取主機上執行的作業系統。

    > [AZURE.NOTE] VMM 雲端最多可以包含 HYPER-V 主機執行 Windows Server 的不同 （支援） 的版本，但複寫原則會套用的主機執行相同的作業系統。 如果您有一個以上的作業系統版本執行主機再建立另一個試驗原則。

4. 在 [**驗證類型**和**驗證連接埠**指定流量驗證之間的主要及復原 HYPER-V 主機伺服器的方式。 除非您已使用 Kerberos 環境，請選取 [**憑證**]。 Azure 網站復原將會自動設定 HTTPS 驗證的憑證。 您不需要手動執行任何動作。 根據預設，會在 HYPER-V host （主機） 伺服器上的 [Windows 防火牆開啟連接埠 8083 和 8084 （適用於憑證）。 如果您選取 [ **Kerberos]**，Kerberos 票證會用於主機伺服器相互驗證。 請注意，此設定只有相關的 Windows Server 2012 R2 上執行的 HYPER-V host （主機） 伺服器。
3. 在**複製頻率**指定您想要在初始複寫 （每隔 30 秒，5 到 15 分鐘） 之後複寫 delta 資料的頻率。
4. 在 [**復原點保留**，指定時多久 [保留] 視窗會針對每個復原點。 受保護的電腦可以復原視窗中的任何位置。
6. 在**應用程式一致的快照頻率**指定頻率 （1-12 小時） 包含應用程式一致的快照的復原點是建立。 HYPER-V 使用兩種類型的快照集-的標準的快照，可提供完整的虛擬機器的累加快照集和應用程式一致的快照的時間點資料的快照應用程式虛擬機器內。 應用程式一致的快照，以確保應用程式都一致時，會使用大量陰影複製服務 (VSS)。 請注意，是否您啟用應用程式一致的快照，它會影響來源虛擬機器上執行應用程式的效能。 請確定您設定的值小於您設定的其他修復點的數目。
7. 在**資料傳輸壓縮**指定是否要壓縮的複寫的資料傳輸。
8. 選取 [**刪除複本 VM** ] 以指定應刪除複本虛擬機器，是否您停用來源 VM 保護。 如果您啟用這項設定，當您停用來源 VM 就會從網站修復主控台移除保護，請 VMM 網站復原設定會從 VMM 主控台，並刪除複本。
3. 在**初始複寫方法**如果您複製到網路指定是否要啟動初始複寫或排程時間。 若要儲存網路頻寬，您可能要排定外您忙碌時間。 然後按一下**[確定]**。

    ![複寫原則](./media/site-recovery-vmm-to-vmm/gs-replication2.png)

6. 當您建立新原則時，會自動與相關聯 VMM 雲端。 **複寫原則**中按一下**[確定]**。 您可以建立其他 VMM 雲朵 （和很 Vm） 關聯與**設定**此複寫原則 > **複寫**> 原則名稱 >**關聯 VMM 雲端**。

    ![複寫原則](./media/site-recovery-vmm-to-vmm/policy-associate.png)

### <a name="prepare-for-offline-initial-replication"></a>準備離線初始複寫

您可以執行離線複寫複製的初始的資料。 您可以準備這，如下所示︰

- 在來源伺服器上，您會指定的資料匯出採取的路徑位置。 將 「 完全控制指派 VMM 服務匯出路徑 NTFS 和共用權限。 在目標伺服器上，您會指定要從中匯入資料就會發生的路徑位置。 指派此匯入路徑上相同的權限。
- 如果共用的匯入或匯出路徑，指派 VMM 服務帳戶的共用所在的遠端電腦上的管理員、 進階使用者，列印運算子或伺服器運算子群組成員資格。
- 如果您使用任何的執行方式帳戶主機，新增匯入及匯出路徑，指派讀取和寫入權限 VMM 執行為帳戶。
- 由於迴路設定不支援 HYPER-V 匯入及匯出共用應該不到用來做為 HYPER-V 主機伺服器，任何電腦上。
- 在 Active Directory 中每個 HYPER-V 上包含您想要保護、 啟用及設定的虛擬機器主機伺服器限制委派信任遠端電腦匯入及匯出路徑位於，，如下所示︰
    1. 在網域控制站，開啟 [ **Active Directory 使用者和電腦**。
    2. 按一下 [主控台樹狀目錄中的 [**網域名稱** > **電腦**。
    3. 以滑鼠右鍵按一下 HYPER-V 主機伺服器名稱 >**屬性**。
    4. 在 [**委派**] 索引標籤上按一下 [**信任這部電腦委派指定的服務**。
    5. 按一下 [**使用任何驗證通訊協定**。
    6. 按一下 [**新增** > **使用者和電腦**。
    7. 輸入主控匯出路徑的電腦名稱 > **[確定]**。從可用的服務清單中，按住 CTRL 鍵並按一下**cifs** > **[確定]**。 對主控的匯入路徑的電腦的名稱。 重複為所需的額外 HYPER-V host （主機） 伺服器。


### <a name="configure-network-mapping"></a>設定網路對應

設定網路來源及目標網路之間的對應。

- 未[讀取](#prepare-for-network-mapping)的哪些網路對應的快速概觀。 如果在加法[閱讀](site-recovery-network-mapping.md)更深入的說明。
- 請確認 VMM 伺服器上的虛擬機器連線到 VM 網路。

設定對應，如下所示︰

1. **設定** > **網站復原基礎結構** > **網路對應** > **網路對應**按一下**+ 網路對應**。

    ![網路對應](./media/site-recovery-vmm-to-azure/network-mapping1.png)

2. 在 [**新增網路對應**] 索引標籤上，選取 [來源和目標 VMM 伺服器。 會擷取 VMM 伺服器相關聯的 VM 網路。
3. 在 [**來源網路**中，選取您想要使用的主要 VMM 伺服器相關聯的 VM 網路清單中的網路。
6. 在**目標網路**選取您想要使用次要 VMM 伺服器上的網路。 然後按一下**[確定]**。

    ![網路對應](./media/site-recovery-vmm-to-vmm/network-mapping2.png)

以下是您的網路對應開始時，會發生什麼情況︰

- 任何現有的複本虛擬機器對應至來源 VM 網路將目標 VM 網路連線。
- 新的虛擬機器連接至來源 VM 網路將連接到目標對應網路，複寫之後。
- 如果您修改現有的對應與新的網路，複本虛擬機器會使用新的設定連線。
- 如果目標網路都有多個子網路且這些子網路的其中一個子網路來源虛擬機器位於相同的名稱，然後複本虛擬機器會連線到目標該子後移轉後。 如果不有任何名稱相符的目標子網路，請在虛擬機器會連線到網路中的第一個子網路。

### <a name="configure-storage-mapping"></a>設定儲存對應
依預設當您要複製到目標 HYPER-V 主機伺服器，虛擬機器來源 HYPER-V host （主機） 伺服器上複製的資料會儲存於所表示的目標 HYPER-V 主機 HYPER-V 管理員中的預設位置。 更進一步控制儲存複製的資料的詳細資訊，您可以設定儲存對應<br/><br/> 若要設定儲存對應，您需要設定儲存分類來源及目標 VMM 伺服器，部署之前。 目前不支援儲存對應到新的 Azure 入口網站。 不過，就可以透過 Powershell 啟用。[進一步瞭解](site-recovery-vmm-to-vmm-powershell-resource-manager.md#step-6-configure-storage-mapping)。

## <a name="step-5-capacity-planning"></a>步驟 5︰ 容量計劃

您已經有您的基本設定您的基礎結構可以考慮容量計劃，並找出您是否需要額外的資源。

網站復原提供 Excel 型容量規劃，以協助您為您的來源環境、 網站復原元件、 網路和儲存空間配置正確的資源。 您可以執行規劃，以快速模式估算根據 Vm、 磁碟和儲存空間，平均數目，或會在其中輸入數字的工作量層級的詳細模式中。 您開始之前，您必須︰

- 收集您複寫環境，包括 Vm Vm 中硬碟，每個磁碟的儲存空間的相關資訊。
- 估計每日的變更 （變換） 率，您必須複製的 delta 資料。 您可以使用[HYPER-V 複本的容量規劃](https://www.microsoft.com/download/details.aspx?id=39057)，以協助您執行此動作。

1.  按一下 [若要下載的工具，然後執行 [**下載**]。 [已讀取，請參閱](site-recovery-capacity-planner.md)工具隨附。
2.  完成後選取**[是]** ，在**您已執行容量規劃**嗎？

    ![容量計劃](./media/site-recovery-vmm-to-azure/gs-capacity-planning.png)

### <a name="control-bandwidth"></a>控制頻寬

您所收集到使用容量規劃 HYPER-V 複本的即時 delta 複寫資訊之後，Excel 型容量規劃工具可協助您計算複寫 （初始和 delta） 的所需的頻寬。 若要控制用於複寫的頻寬，您可以設定使用群組原則] 或 [Windows PowerShell 的 NetQos 原則。 有幾種方式執行這項操作︰

**PowerShell** | **詳細資料**
--- | ---
**新增是-NetQosPolicy 」 QoS 至目的地子網路 」** | 節流從 HYPER-V 主機執行 Windows Server 2012 R2 次要子網路流量。 如果您的主要和次要子網路有不同，請使用。
**新增是-NetQosPolicy 」 QoS 目的地連接埠 」** | 節流流量的目的地連接埠執行 Windows Server 2012 R2 HYPER-V 主機。
**新 NetQosPolicy 」 從 VMM 流速流量 」** | 節流 vmms.exe 流量。 這會節流 HYPER-V 流量和 Live 移轉流量。 您可以符合 IP 通訊協定和縮小的連接埠。

您可以使用頻寬寬度] 設定，或限制每次要位元流量。 如果您使用的您需要執行此動作在所有叢集節點叢集。 如需詳細資訊，請參閱︰


- [節流設定 HYPER-V 複本](http://www.thomasmaurer.ch/2013/12/throttling-hyper-v-replica-traffic/)流量邁可 Maurer 部落格
- 其他資訊[新增 NetQosPolicy cmdlet](https://technet.microsoft.com/library/hh967468.aspx)的詳細資訊。


## <a name="step-6-enable-replication"></a>步驟 6︰ 啟用複寫

現在，如下所示啟用複寫︰

1. 按一下 [**步驟 2︰ 複製應用程式** > **來源**。 啟用複寫第一次後，您可以按一下**+ 複寫**中啟用其他電腦的複製保存庫。

    ![啟用複寫](./media/site-recovery-vmm-to-vmm/enable-replication1.png)

2. 在**來源**刀 > 選取 VMM 伺服器與您想要複寫 HYPER-V 主機位於的雲端。 然後按一下**[確定]**。

    ![啟用複寫](./media/site-recovery-vmm-to-vmm/enable-replication2.png)

3. 在**目標**刀確認次要 VMM 伺服器，並在雲端。
4. **虛擬機器**中選取您要保護從清單的 Vm。

    ![啟用虛擬機器保護](./media/site-recovery-vmm-to-vmm/enable-replication5.png)

您可以追蹤進度的設定] 中的 [**啟用保護**] 動作 >**工作** > **網站復原工作**。 **完成保護**工作執行之後就有一個虛擬機器可供容錯移轉。


>[AZURE.NOTE] 您也可以啟用虛擬機器 VMM 主控台中的保護。 按一下 [虛擬機器屬性中的工具列上的 [**啟用保護**> **Azure 網站修復**] 索引標籤。

啟用複寫後您可以檢視屬性**設定**中 vm > **複製項目**> vm 名稱。 在**基本資訊**儀表板上，您可以看到 VM 和其狀態複寫原則的相關資訊。 按一下 [更多詳細資料的 [**屬性**]。

### <a name="onboard-existing-virtual-machines"></a>內建現有的虛擬機器

如果您有現有的虛擬機器中 VMM 複寫 HYPER-V 複本可以內建 Azure 網站復原保護，如下所示的地方︰

1. 確定 [HYPER-V 伺服器裝載的現有 VM 位於主要的雲端裝載複本虛擬機器 HYPER-V 伺服器位於次要雲端]。
2. 請確定複寫原則設定為主要 VMM 雲端。
2. 啟用複寫主要的虛擬機器。 Azure 網站復原和 VMM 可確保偵測到相同的複本主機和虛擬機器，和 Azure 網站修復會重複使用及重新建立複寫使用指定的設定。


## <a name="step-7-test-your-deployment"></a>步驟 7︰ 測試您的部署

若要測試您的部署中，您可以執行測試容錯移轉單一虛擬機器，或建立包含一或多個虛擬機器復原計劃。

### <a name="prepare-for-failover"></a>準備容錯移轉

- 若要完全測試您的部署必須基礎結構，以複製電腦如預期般運作。 如果您想要測試 Active Directory 和 DNS，可以建立 dns 網域控制站的虛擬機器並將此複寫到 Azure 使用 Azure 網站復原。 閱讀更多在[Active Directory 測試容錯移轉考量](site-recovery-active-directory.md#considerations-for-test-failover)。
- 本文中的指示說明如何與不網路執行測試容錯移轉。 這個選項會測試 VM 失敗，但不會測試 vm 網路設定。 [深入](site-recovery-failover.md#run-a-test-failover)瞭解其他選項。
- 如果您想要執行，而不是測試容錯移轉意外的容錯移轉請注意下列動作︰

    - 如果可以的話您應該關閉主要電腦才能執行意外的容錯移轉。 如此一來，可確保您沒有安裝來源與複本的電腦執行一次。
    - 當您執行意外的容錯移轉時，開始意外的容錯移轉之後，就不會傳送任何資料 delta 會停止資料複寫從主要的電腦。 此外如果您執行復原計劃意外的容錯移轉之前會執行完成，即使發生錯誤。




### <a name="run-a-test-failover"></a>執行測試容錯移轉

1. 在 [**設定**] 中的單一 VM 失敗 > **複製項目**]，按一下 VM > **+ 測試容錯移轉**。

    ![測試容錯移轉](./media/site-recovery-vmm-to-vmm/test-failover.png)

2. 透過復原計劃，在 [**設定**失敗 > **復原方案**，以滑鼠右鍵按一下方案 >**測試容錯移轉**。 若要建立復原計劃[依照這些指示進行](site-recovery-create-recovery-plans.md)。
2. 在 [**測試容錯移轉**，請選取 [**無**]。 使用此選項您測試如預期般 VM 失敗，但複寫的 VM 無法連線至網路。

    ![測試容錯移轉](./media/site-recovery-vmm-to-vmm/test-failover1.png)

3. 按一下**[確定]**以開始容錯移轉]。 您可以按一下以開啟其內容，VM 上] 或 [**測試容錯移轉**工作**設定**中追蹤進度 > **工作** > **網站復原工作**。
4. 當容錯移轉工作達到**完成測試**階段時，執行下列動作︰

    -  在第二個 VMM 雲端檢視 VM 的複本。
    -  按一下 [**完成，測試**，測試容錯移轉完成]。
    -  按一下 [**備忘稿**，錄製並儲存聯測試容錯移轉任何觀察值]。

5. 測試虛擬機器將會建立主複本虛擬機器存在於主機上。 它會新增至複本虛擬機器位於相同的雲端。
6. 在成功確認該 Vm 開始後按一下 [**測試容錯移轉完成**。 在此階段期間測試容錯移轉自動建立的網站復原的任何項目會被刪除。  

    > [AZURE.NOTE] 如果測試容錯移轉持續時間超過兩週才能完成它是由強制中完成。



### <a name="update-dns-with-the-replica-vm-ip-address"></a>更新 DNS 複本 VM IP 位址

後移轉後的複本 VM 可能沒有與主要的虛擬機器相同的 IP 位址。

- 虛擬機器時都會更新他們使用 DNS 伺服器開始後。
- 您也可以手動更新 DNS，如下所示︰

#### <a name="retrieve-the-ip-address"></a>擷取的 IP 位址

執行此範例指令碼來擷取的 IP 位址。

        $vm = Get-SCVirtualMachine -Name <VM_NAME>
        $na = $vm[0].VirtualNetworkAdapters>
        $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
        $ip.address  

#### <a name="update-dns"></a>更新 DNS

執行此範例指令碼來更新 DNS，指定您使用的上一個範例指令碼所擷取的 IP 位址。

        string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord

## <a name="next-steps"></a>後續步驟

在您的部署之後設定，並執行，[進一步](site-recovery-failover.md)瞭解不同類型的容錯移轉。
