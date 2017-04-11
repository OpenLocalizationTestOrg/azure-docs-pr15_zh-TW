<properties
    pageTitle="Azure Azure 入口網站中使用網站復原複寫 HYPER-V 虛擬機器中 VMM 雲朵 |Microsoft Azure"
    description="說明如何部署 Azure 網站復原協調複寫、 錯誤移轉及復原的 HYPER-V Vm 中 VMM 雲朵至 Azure 使用 Azure 入口網站"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor="tysonn"/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/16/2016"
    ms.author="raynew"/>

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-azure-site-recovery-with-the-azure-portal--microsoft-azure"></a>Azure Azure 入口網站中使用 Azure 網站復原複寫 HYPER-V 虛擬機器中 VMM 雲朵 |Microsoft Azure

> [AZURE.SELECTOR]
- [Azure 入口網站](site-recovery-vmm-to-azure.md)
- [Azure 傳統](site-recovery-vmm-to-azure-classic.md)
- [PowerShell 資源管理員](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [PowerShell 傳統](site-recovery-deploy-with-powershell.md)

歡迎使用 Azure 網站復原 ！ 如果您想要複製內部部署 HYPER-V 虛擬機器這份文件管理在系統管理中心的虛擬機器管理員 (VMM) 雲朵至 Azure Azure 入口網站中使用 Azure 網站復原的使用。

> [AZURE.NOTE]Azure 有兩種不同的[部署模型](../resource-manager-deployment-model
> )建立及使用的資源︰ Azure 資源管理員] 及 [傳統。 Azure 也會有兩個入口網站 – 支援傳統部署模型，Azure 傳統入口網站和支援兩種部署模型 Azure 入口網站。


Azure 入口網站中的 azure 網站復原提供幾項新功能︰

- Azure 入口網站中 Azure 備份和 Azure 網站復原服務會合併到單一修復服務保存庫，讓您可以設定及管理業務連續性和損毀修復 (BCDR) 從單一位置。 整合儀表板，可讓您監控和管理您的內部部署網站與 Azure 公用雲端作業。
- 現在，佈建與雲端解決方案提供者 (CSP) 程式 Azure 訂閱的使用者能管理 Azure 入口網站中的網站修復作業。
- 網站復原 Azure 入口網站中的可以複寫機器 Azure 資源管理員儲存帳戶。 在錯誤後移轉，網站復原會建立資源管理員型 Vm Azure 中。
- 網站復原仍會繼續支援複寫傳統的儲存空間的帳戶。 在錯誤後移轉，網站復原建立 Vm 使用傳統的模型。


閱讀本文之後, 張貼底部 Disqus 註解中的任何註解。 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上提出技術的問題。

## <a name="overview"></a>概觀

組織必須決定如何應用程式、 負載，以及資料保持執行及使用期間計劃和計畫停機時間和越快越復原正常運作狀況 BCDR 策略。 您 BCDR 策略應該安全及修復，保留商務資料，並確保當發生損毀時，仍然持續可用的工作量。

網站復原是 Azure 服務可提供您 BCDR 策略來協調複寫實體伺服器的內部部署和雲端 (Azure)，或第二個資料中心的虛擬機器。 中斷問題發生時在您的主要位置，您無法透過，讓應用程式可用的工作負載的第二個位置。 會傳回標準作業時，您會回到您的主要位置失敗。 如需深入瞭解[什麼是 Azure 網站復原？](site-recovery-overview.md)

本文提供您要複製的所有資訊內部部署 HYPER-V Vm VMM 雲朵至 Azure 中。 其包含架構的概觀，規劃的詳細資訊，以及設定 Azure、 內部部署伺服器、 複寫設定與容量規劃部署步驟。 您已設定基礎結構之後，您可以啟用複製您要保護，請核取該容錯移轉運作的電腦上。


## <a name="business-advantages"></a>商務優點

- 網站復原提供離線保護商務工作量及 HYPER-V Vm 上執行應用程式。
- 修復服務入口網站提供設定、 管理及監視複寫、 錯誤移轉及復原的單一位置。
- 您可以輕鬆地執行容錯移轉從您的內部部署基礎結構 Azure 及回復 （還原） 從 Azure 到 HYPER-V 主機伺服器在您的內部部署網站。
- 讓多層應用程式負載失敗在一起，您可以設定修復計劃與多部電腦。


## <a name="scenario-architecture"></a>案例架構


以下是分析藍本元件︰

- **VMM 伺服器**︰ 使用一或多個雲朵內部部署 VMM 伺服器。
- **HYPER-V 主機] 或 [叢集**︰ HYPER-V 主機伺服器或叢集 VMM 雲朵中的管理。
- **Azure 網站修復提供者並修復服務代理程式**︰ 在部署您 Azure 網站修復提供者上安裝 VMM 伺服器，以及在 HYPER-V 主機伺服器上的 Microsoft Azure 修復服務代理程式。 透過 HTTPS 443 複寫協調流程，與網站復原通訊 VMM 伺服器上的提供者。 HYPER-V 主機伺服器上的代理程式 Azure 儲存體，透過 HTTPS 443 根據預設，複製資料。
- **Azure**︰ 您必須 Azure 訂閱、 複製的儲存資料，Azure 儲存體帳戶和 Azure 虛擬網路以便 Azure Vm 後移轉後連線至網路。

![E2A 拓撲](./media/site-recovery-vmm-to-azure/architecture.png)


## <a name="azure-prerequisites"></a>Azure 的先決條件

以下是您需要什麼 Azure 中部署這種情況。

**必要條件** | **詳細資料**
--- | ---
**Azure 帳戶**| 您必須以[Microsoft Azure](http://azure.microsoft.com/)帳戶。 您可以開始使用[免費的試用版](https://azure.microsoft.com/pricing/free-trial/)。 [瞭解更多](https://azure.microsoft.com/pricing/details/site-recovery/)有關網站復原價格。
**Azure 儲存體** | 您需要標準 Azure 儲存體帳戶儲存複製的資料。 您可以使用 LRS 或 GRS 儲存帳戶。 我們建議您 GRS，讓資料是同時，如果地區的資料，或如果無法復原的主要區域。 [進一步瞭解](../storage/storage-redundancy.md)。 帳戶必須在同一個地區為修復服務保存庫。<br/><br/>不支援的進階版儲存空間。<br/><br/> 複製的資料儲存在 Azure 儲存空間，發生錯誤後移轉時，系統會建立 Azure Vm。 <br/><br/> [閱讀相關資訊](../storage/storage-introduction.md)Azure 儲存空間。
**Azure 網路** | 您需要 Azure Vm 在發生錯誤後移轉時，要連線至 Azure 虛擬網路。 網路必須在同一個地區為修復服務保存庫。

## <a name="on-premises-prerequisites"></a>內部部署的先決條件

以下是您的需要內部部署

**必要條件** | **詳細資料**
--- | ---
**VMM**| 在系統管理中心 2012 R2 上執行的一或多個 VMM 伺服器。 每個 VMM 伺服器應有一個或多個雲朵設定。 雲端應該包含︰<br/><br/> 一或多個 VMM 主機群組。<br/><br/> 一或多個 HYPER-V 主機伺服器或叢集的每個主機] 群組中。<br/><br/>[深入](http://www.server-log.com/blog/2011/8/26/vmm-2012-and-the-clouds.html)瞭解設定 VMM 雲朵。
**Hyper-v** | HYPER-V 主機伺服器必須至少執行**Windows Server 2012 R2** HYPER-V 角色或**Microsoft HYPER-V Server 2012 R2** ，安裝最新的更新。<br/><br/> HYPER-V 伺服器應該包含一或多個 Vm。<br/><br/> HYPER-V 主機伺服器或包含您想要複製的 Vm 叢集必須 VMM 雲端管理。<br/><br/>HYPER-V 伺服器應該連接至網際網路，直接或透過 proxy。<br/><br/>HYPER-V 伺服器應該安裝的文章[2961977](https://support.microsoft.com/kb/2961977)中所提及的修正。<br/><br/>Azure 資料複寫 HYPER-V 主機伺服器需要存取網際網路。
**提供者和代理程式** | Azure 網站復原部署期間您將 VMM 伺服器，並修復服務上的代理程式 HYPER-V 主機上安裝 Azure 網站修復提供者。 提供者和代理程式需要連線到 Azure，直接在網際網路上，或透過 proxy。 請注意，不支援的 HTTPS 為基礎的 proxy。 Proxy 伺服器上的 VMM 伺服器及 HYPER-V 主機應該允許存取︰ <br/><br/> *。 hypervrecoverymanager.windowsazure.com <br/> <br/> *。 accesscontrol.windows.net <br/><br/> *。 backup.windowsazure.com <br/> <br/> *。 blob.core.windows.net <br/><br/> *。 store.core.windows.net<br/><br/>如果您有 VMM 伺服器上的 IP 位址的防火牆規則，請核取規則，允許 Azure 通訊。 您必須允許[Azure 資料中心 IP 範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653)及 HTTPS (443) 連接埠。<br/><br/>允許的 IP 位址範圍 Azure 區域，您的訂閱，與西美國。<br/><br/>此外。 proxy 伺服器上 VMM 伺服器需要存取 https://www.msftncsi.com/ncsi.txt


## <a name="protected-machine-prerequisites"></a>受保護的電腦的先決條件


**必要條件** | **詳細資料**
--- | ---
**受保護的 Vm** | 您無法透過 VM 之前，請確定名稱指派給 Azure VM 遵守[Azure 的先決條件](site-recovery-best-practices.md#azure-virtual-machine-requirements)。 您已啟用 vm 試驗之後，您可以修改名稱。 <br/><br/> 在受保護的電腦上的個別磁碟容量應該不超過 1023 GB。 VM 可以有到 16 的磁碟 (因此最 16 TB)。<br/><br/> 共用磁碟來賓叢集不受支援。<br/><br/> 整合可延伸韌體介面 (UEFI) / 可延伸韌體 Interface(EFI) 開機不受支援。<br/><br/> 如果來源 VM NIC 小組 Azure 失敗之後會將其轉換成單一 NIC。<br/><br/>保護 Vm 執行的靜態 IP 位址 Linux 不支援。

## <a name="prepare-for-deployment"></a>準備進行部署

準備要的部署︰

1. 在其中 Azure Vm 都位於後移轉後的[Azure 網路的設定](#set-up-an-azure-network)。
2. [設定 Azure 儲存體帳戶](#set-up-an-azure-storage-account)複製的資料。
4. 網站復原部署[準備 VMM 伺服器](#prepare-the-vmm-server)。
5. [準備網路對應](#prepare-for-network-mapping)。 設定網路，好讓您可以設定網站復原部署期間的網路對應。

### <a name="set-up-an-azure-network"></a>設定 Azure 的網路

Azure Vm 建立容錯移轉會將連線到其之後，您會需要 Azure 的網路。

- 網路應該位於您所部署修復服務保存庫的項目為相同的區域。
- 根據您想要用於無法透過 Azure Vm 資源模型，您可以設定[資源管理員模式](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)] 或 [[標準模式](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)中的 Azure 網路設定。
- 我們建議您開始之前，您將網路設定。 如果沒有的話，您需要進行此動作網站復原部署期間。

> [AZURE.NOTE] 用於部署網站復原的網路不支援[的網路移轉](../resource-group-move-resources.md)跨資源群組內的相同的訂閱或訂閱。


### <a name="set-up-an-azure-storage-account"></a>設定 Azure 儲存體帳戶

- 您必須保留資料複製到 Azure 標準 Azure 儲存體帳戶。 帳戶必須在同一個地區為修復服務保存庫。
- 您想要用於無法透過 Azure Vm 資源模型，根據您設定[資源管理員模式](../storage/storage-create-storage-account.md)] 或 [[標準模式](../storage/storage-create-storage-account-classic-portal.md)中的帳戶。
- 我們建議您開始之前設定的帳戶。 如果沒有的話，您需要進行此動作網站復原部署期間。

> [AZURE.NOTE] 儲存帳戶的部署網站復原不支援[的儲存空間帳戶移轉](../resource-group-move-resources.md)跨資源群組內的相同的訂閱或訂閱。

### <a name="prepare-the-vmm-server"></a>準備 VMM 伺服器

- 請確定 VMM 伺服器遵守[的先決條件](#on-premises-prerequisites)。
- 網站復原部署期間，您可以指定 VMM 伺服器上的所有雲朵都應用於 Azure 入口網站。 如果您只想要顯示在入口網站中的特定雲朵，您可以啟用 VMM 管理主控台中的雲端上該設定。


### <a name="prepare-for-network-mapping"></a>準備網路對應

您必須設定網站復原部署期間的網路對應。 網路的來源 VMM VM 網路之間的對應地圖，以啟用下列 Azure 網路為目標︰

- 在相同的網路上失敗的電腦可以連線至彼此中,，即使他們不無法透過以相同的方式，或在相同的復原計劃。
- 如果網路閘道器設定目標 Azure 網路，Azure 虛擬機器可以連線至內部部署虛擬機器中。
- 若要設定網路對應以下是您需要準備︰

    - 請確定來源 HYPER-V 主機伺服器上的 Vm 連線到 VMM VM 網路。 該網路應該邏輯網路雲端與相關聯的連結。
    - 描述如何在[上述](#set-up-an-azure-network)Azure 網路

- [瞭解更多](site-recovery-network-mapping.md)有關網路對應的運作方式。


## <a name="create-a-recovery-services-vault"></a>建立修復服務保存庫

1. [Azure 入口網站](https://portal.azure.com)登入。
2. 按一下 [**新** > **管理** > **修復服務**。 或者您可以按一下 [**瀏覽** > **修復服務**保存庫 >**新增**。

    ![新的保存庫](./media/site-recovery-vmm-to-azure/new-vault3.png)

3. 在 [**名稱**] 中，指定識別保存庫好記的名稱。 如果您有多個訂閱，請選取其中之一。
4. [建立資源群組](../resource-group-template-deploy-portal.md)]，或選取現有的項目。 指定 Azure 的區域。 電腦會複製到此區域。 若要檢查支援的地區請參閱[Azure 網站復原定價詳細資料](https://azure.microsoft.com/pricing/details/site-recovery/)中的地理可用性
4. 若要從儀表板的 [快速存取保存庫，請按一下 [**固定至儀表板** > **建立保存庫**。

    ![新的保存庫](./media/site-recovery-vmm-to-azure/new-vault-settings.png)

新的保存庫會出現在 [**儀表板**上 > **所有資源**，然後在 [主要**修復服務保存庫**刀。

## <a name="getting-started"></a>快速入門

網站復原提供快速入門體驗，可協助您以最快速部署。 開始使用檢查先決條件並引導您瞭解部署步驟順序正確的網站復原。

在 [開始使用您選取類型的電腦您想要複製，和您要複製到的位置。 您將內部部署伺服器、 Azure 儲存體帳戶及網路設定。 您建立複寫原則，並執行容量計劃。 您已設定您的基礎結構之後，您會啟用 Vm 複寫。 您可以執行錯誤後移轉特定的電腦，或建立失敗的多部電腦上的修復計劃。

選擇您想要部署網站復原的如何開始快速入門。 快速入門流程稍微改變，根據您複製的需求。



## <a name="step-1-choose-your-protection-goals"></a>步驟 1︰ 選擇 [保護定目標

選取您要複製及您要複製到的位置。

1. 在**修復服務保存庫**刀中，選取您保存庫，按一下 [**設定]**。
2. 在 [**快速入門**中，按一下 [**網站復原** > **步驟 1︰ 準備基礎結構** > **保護目標**。

    ![選擇目標](./media/site-recovery-vmm-to-azure/choose-goals.png)

3. **保護目標**] 中選取**至 Azure**，並選取**[是，使用 HYPER-V**。 選取**[是]**以確認您使用 VMM 管理 HYPER-V 主辦城市] 和 [復原網站。 然後按一下**[確定]**。

    ![選擇目標](./media/site-recovery-vmm-to-azure/choose-goals2.png)



## <a name="step-2-set-up-the-source-environment"></a>步驟 2︰ 設定來源環境

Azure 網站修復提供者的伺服器上安裝 VMM，並登錄伺服器保存庫中。 Azure 修復服務上安裝代理程式 HYPER-V 主機。

1. 按一下 [**步驟 2︰ 準備基礎結構** > **來源**。

    ![設定來源](./media/site-recovery-vmm-to-azure/set-source1.png)

2. 按一下 [**準備來源**中的 [ **+ VMM**以新增 VMM 伺服器]。

    ![設定來源](./media/site-recovery-vmm-to-azure/set-source2.png)

3. 在**系統管理中心 VMM 伺服器**會出現在**伺服器類型**] 及 [的**新增伺服器**刀核取 VMM 伺服器符合的[必要條件和 URL 需求](#on-premises-prerequisites)。
4. 下載 Azure 網站修復提供者安裝檔案。
5. 下載登錄機碼。 您需要此當您執行安裝程式。 索引鍵的五個天後產生，是有效的。

    ![設定來源](./media/site-recovery-vmm-to-azure/set-source3.png)

6. VMM 伺服器上安裝 Azure 網站修復提供者。


### <a name="set-up-the-azure-site-recovery-provider"></a>設定 Azure 網站修復提供者

1.  執行提供者設定檔。
2. 在 [ **Microsoft Update**您可以選擇在更新以便會根據您的 Microsoft Update 原則的安裝提供者的更新。
3. 在 [**安裝**]，接受或修改預設提供者的安裝位置，然後按一下 [**安裝**]。

    ![安裝位置](./media/site-recovery-vmm-to-azure/provider2.png)

4. 安裝完成時請按一下 [註冊 VMM 伺服器保存庫中的 [**註冊**]。
5. 在**保存庫設定**] 頁面上，按一下 [以選取保存庫金鑰檔案的 [**瀏覽**]。 指定 Azure 網站復原訂閱和保存庫名稱。

    ![伺服器註冊](./media/site-recovery-vmm-to-azure/provider10.PNG)

6. 在 [**網際網路連線**，指定 VMM 伺服器上執行的提供者連線至網站復原網際網路的方式。

    - 如果您想要直接連接的提供者選取**直接連線到 Azure 網站復原不 proxy**。
    - 如果您現有的 proxy 需要驗證]，或您想要使用自訂的 proxy 選取 [**連線至 Azure 網站復原使用 proxy 伺服器**。
    - 如果您使用的自訂 proxy，指定的地址、 連接埠和認證。
    - 如果您使用的 proxy，您應該已經允許[先決條件](#on-premises-prerequisites)所述的 Url。
    - 如果您使用的自訂 proxy 會使用指定的 proxy 認證自動建立 VMM RunAs 帳戶 (DRAProxyAccount)。 設定 proxy 伺服器，以便能順利驗證此帳戶。 可以 VMM 主控台中修改 VMM RunAs 帳戶設定。 在 [**設定**] 中，展開 [**安全性** > **執行方式帳戶**，然後修改的 DRAProxyAccount 的密碼。 必須重新啟動 VMM 服務，讓此設定才會生效。

    ![網際網路](./media/site-recovery-vmm-to-azure/provider13.PNG)

7. 接受或修改的 SSL 憑證來加密的資料會自動產生的位置。 如果您啟用雲端受 Azure Azure 網站復原入口網站中的資料加密，會使用這個憑證。 保留這個憑證安全。 當您執行容錯移轉至 Azure 您需要將解密，如果啟用資料加密。


8. 在 [**伺服器名稱**，指定識別保存庫中的 VMM 伺服器好記的名稱。 在 [叢集設定中，指定 VMM 叢集角色名稱。
9. 如果您想要保存庫與同步處理所有的雲朵 VMM 伺服器上的中繼資料，請啟用**同步處理雲端中繼資料**。 此動作只需要進行一次在每個伺服器上。 如果您不想要同步處理所有的雲朵，您可以離開取消核取此設定，並同步處理個別在雲端中的屬性 VMM 主控台中每個雲端。 按一下 [**註冊**] 以完成程序。

    ![伺服器註冊](./media/site-recovery-vmm-to-azure/provider16.PNG)

10. 註冊開始。 伺服器註冊完成之後，會顯示在**設定** > 保存庫中的**伺服器**刀。


#### <a name="command-line-installation-for-the-azure-site-recovery-provider"></a>Azure 網站復原提供者的命令列安裝

從命令列可安裝 Azure 網站修復提供者。 這個方法可以用於伺服器核心的 Windows Server 2012 R2 上安裝的提供者。

1. 下載資料夾的提供者安裝檔案及登錄機碼。 例如，C:\ASR。
2. 提高權限的命令提示字元中，執行下列命令來擷取的提供者安裝程式︰

            C:\Windows\System32> CD C:\ASR
            C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
3. 執行此命令來安裝元件︰

            C:\ASR> setupdr.exe /i

4. 然後，請執行這些登錄伺服器保存庫中的命令︰

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>       

位置︰

- **/Credentials**︰ 強制指定登錄鍵檔案所在的參數。  
- **/FriendlyName**︰ 強制參數會出現在 Azure 網站復原入口網站中的 HYPER-V 主機伺服器名稱。
- - **/EncryptionEnabled**︰ 當您要複製 HYPER-V Vm VMM 中的選擇性參數雲朵至 Azure。 指定您是否要加密 Azure 中 （位於其餘加密） 的虛擬機器。 請確定檔案的名稱**.pfx**副檔名。 加密 」 預設為關閉。
- **/proxyAddress**︰ 選用的參數會指定 proxy 伺服器的位址。
- **/proxyport**︰ 選用的參數會指定 proxy 伺服器的連接埠。
- **/proxyUsername**︰ 選用的參數會指定 proxy 使用者名稱 （如果 proxy 需要驗證）。
- **/proxyPassword**︰ 選用的參數會指定要驗證的 proxy 伺服器 （如果 proxy 需要驗證） 的密碼。


### <a name="install-the-azure-recovery-services-agent-on-hyper-v-hosts"></a>在 [HYPER-V 主機上安裝 Azure 修復服務代理程式

1. 您已將提供者設定之後，必須先將安裝檔案下載 Azure 修復服務代理程式。 VMM 雲端中的每個 HYPER-V 伺服器上執行安裝程式。

    ![HYPER-V 網站](./media/site-recovery-vmm-to-azure/hyperv-agent1.png)

2. 在**先決條件檢查**] 頁面上，按一下 [**下一步**]。 將會自動安裝任何缺少的必要條件。

    ![先決條件復原服務代理程式](./media/site-recovery-vmm-to-azure/hyperv-agent2.png)

3. 在 [**安裝設定**] 頁面接受或修改安裝位置，以及快取位置。 您可以在具有至少 5 GB 的可用儲存空間的磁碟機上設定快取，但我們建議 600 GB 或更多的可用空間的快取磁碟機。 然後按一下 [**安裝**]。
4. 安裝完成後，按一下 [**關閉**完成]。

    ![註冊火星代理程式](./media/site-recovery-vmm-to-azure/hyperv-agent3.png)

#### <a name="command-line-installation-for-azure-site-recovery-services-agent"></a>Azure 網站修復服務代理程式的命令列安裝

您可以安裝 Microsoft Azure 修復服務代理從命令列使用下列命令︰

     marsagentinstaller.exe /q /nu

#### <a name="set-up-internet-proxy-access-to-site-recovery-from-hyper-v-hosts"></a>設定 HYPER-V 主機網站復原存取網際網路 proxy

HYPER-V 主機上執行修復服務代理程式所需 VM 複寫 Azure 網際網路存取。 如果您透過 proxy 存取網際網路，將其設定，如下所示︰

1. 開啟 Microsoft Azure 備份 MMC 嵌入式管理單元 HYPER-V 主機上。 根據預設 Microsoft Azure 備份的快速鍵是在桌面上或 C:\Program Files\Microsoft Azure 復原服務 Agent\bin\wabadmin。
2. 在嵌入式管理單元中按一下 [**變更內容**]。
3. 在 [ **Proxy 設定**] 索引標籤中，指定 proxy 伺服器資訊。

    ![註冊火星代理程式](./media/site-recovery-vmm-to-azure/mars-proxy.png)

4. 請確定代理程式可連線[的先決條件](#on-premises-prerequisites)所述的 Url。


## <a name="step-3-set-up-the-target-environment"></a>步驟 3︰ 設定目標環境

指定要用於複寫和 Azure 網路的 Azure Vm 會將連線後移轉後的 Azure 儲存體帳戶。

1.  按一下 [**準備基礎結構** > **目標**並選取您想要使用的 Azure 訂閱。
2.  指定您想要使用的 Vm 後移轉後的部署模型。
3.  網站復原檢查您有一個或多個相容 Azure 儲存體帳戶與網路。

    ![儲存空間](./media/site-recovery-vmm-to-azure/compatible-storage.png)

4.  如果您還沒有建立儲存帳戶，而且您想要建立一個使用資源管理員] 中，按一下 [ **+ 儲存帳戶**執行的內嵌。  在**建立儲存帳戶**刀指定帳戶名稱、 類型、 訂閱及位置。 帳戶應該位於修復服務保存庫相同的位置。

    ![儲存空間](./media/site-recovery-vmm-to-azure/gs-createstorage.png)

    請注意︰

    - 如果您想要建立使用傳統模式的儲存空間帳戶，您的 Azure 入口網站中。 [深入瞭解](../storage/storage-create-storage-account-classic-portal.md)
    - 如果您使用進階版儲存帳戶複製資料，您需要設定儲存擷取進行中的變更內部部署資料的複寫記錄其他標準儲存帳戶。

4.  如果您還沒有建立 Azure 的網路，且您想要建立一個使用資源管理員按一下**+ 網路**執行該內嵌。 在**建立虛擬網路**刀指定網路名稱、 地址範圍、 子網路詳細資料、 訂閱及位置。 網路應該位於修復服務保存庫相同的位置。

    ![網路](./media/site-recovery-vmm-to-azure/gs-createnetwork.png)

    如果您想要建立使用傳統模式網路必須執行的 Azure 入口網站中。 [進一步瞭解](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)。

### <a name="configure-network-mapping"></a>設定網路對應

- [閱讀](#prepare-for-network-mapping)並哪些網路對應的快速概觀。 [閱讀本文](site-recovery-network-mapping.md)更深入的說明。
- 請確認 VMM 伺服器上的虛擬機器連線到 VM 網路，且您已建立至少有一個 Azure 虛擬網路。 多個 VM 網路可以對應到單一 Azure 網路。

設定對應，如下所示︰

1. 在 [**設定** > **網站復原基礎結構** > **網路對應** > **對應網路**，按一下 [ **+ 網路對應**] 圖示。

    ![網路對應](./media/site-recovery-vmm-to-azure/network-mapping1.png)

2. 在**新增網路對應**選取來源 VMM 伺服器，以及**Azure**做為目標。
3. 驗證後移轉後的訂閱和部署模型。
4. 在 [**來源網路**中，選取的來源內部部署 VM 網路您想要對應從 VMM 伺服器相關聯的清單。
5. 在**目標網路**中，選取 [Azure 網路的複本中 Azure Vm 時，會位於建立]。 然後按一下**[確定]**。

    ![網路對應](./media/site-recovery-vmm-to-azure/network-mapping2.png)

以下是您的網路對應開始時，會發生什麼情況︰

- 在來源 VM 網路上的現有 Vm 連線至目標網路對應開始時。 新的 Vm 連線至來源 VM 網路連線至對應的 Azure 網路複寫的時機。
- 如果您修改現有的網路對應時，會使用新的設定連線複本虛擬機器。
- 如果目標網路都有多個子網路且這些子網路的其中一個子網路來源虛擬機器位於相同的名稱，然後複本虛擬機器會連線到目標該子後移轉後。
- 如果不有任何名稱相符的目標子網路，請在虛擬機器會連線到網路中的第一個子網路。



## <a name="step-4-set-up-replication-settings"></a>步驟 4︰ 設定複寫設定


1. 若要建立新的複寫原則，請按一下 [**準備基礎結構** > **複寫設定** > **+ 建立和關聯**。

    ![網路](./media/site-recovery-vmm-to-azure/gs-replication.png)

2. 在 [**建立和相關的原則**，指定原則名稱。
3. 在**複製頻率**，指定您想要在初始複寫 （每隔 30 秒，5 到 15 分鐘） 之後複寫 delta 資料的頻率。
4. 在 [**復原點保留**，指定時多久 [保留] 視窗會針對每個復原點。 受保護的電腦可以復原視窗中的任何位置。
6. 在 [**應用程式一致的快照頻率**，指定 [頻率 （1-12 小時） 包含應用程式一致的快照的復原點會建立。 HYPER-V 使用兩種類型的快照集-的標準的快照，可提供完整的虛擬機器的累加快照集和應用程式一致的快照的時間點資料的快照應用程式虛擬機器內。 應用程式一致的快照，以確保應用程式都一致時，會使用大量陰影複製服務 (VSS)。 請注意，是否您啟用應用程式一致的快照，它會影響來源虛擬機器上執行應用程式的效能。 請確定您設定的值小於您設定的其他修復點的數目。
3. 在**初始複寫開始時間**] 中，指定何時開始初始複寫。 您的網際網路頻寬，因此您可能要排定外您忙碌時間發生複寫。
5. 在**儲存於 Azure 的加密資料**，指定是否要加密 Azure 儲存體中的其他資料。 然後按一下**[確定]**。

    ![複寫原則](./media/site-recovery-vmm-to-azure/gs-replication2.png)

6. 當您建立新原則時，會自動與相關聯 VMM 雲端。 按一下**[確定]**。 您可以建立其他 VMM 雲朵 （和很 Vm） 關聯與**設定**此複寫原則 > **複寫**> 原則名稱 >**關聯 VMM 雲端**。

    ![複寫原則](./media/site-recovery-vmm-to-azure/policy-associate.png)

## <a name="step-5-capacity-planning"></a>步驟 5︰ 容量計劃

您已經有您的基本設定您的基礎結構可以考慮容量計劃，並找出您是否需要額外的資源。

復原網站提供可協助您為您的來源環境、 網站復原元件、 網路和儲存空間配置正確的資源容量規劃。 您可以執行規劃，以快速模式估算根據 Vm、 磁碟和儲存空間，平均數目，或會在其中輸入數字的工作量層級的詳細模式中。 您開始之前，您必須︰

- 收集您複寫環境，包括 Vm Vm 中硬碟，每個磁碟的儲存空間的相關資訊。
- 估計每日的變更 （變換） 率，您必須複製資料。 您可以使用[HYPER-V 複本的容量規劃](https://www.microsoft.com/download/details.aspx?id=39057)，以協助您執行此動作。

1.  按一下 [若要下載的工具，然後執行 [**下載**]。 [已讀取，請參閱](site-recovery-capacity-planner.md)工具隨附。
2.  完成後選取**[是]** ，在**您已執行容量規劃**嗎？

    ![容量計劃](./media/site-recovery-vmm-to-azure/gs-capacity-planning.png)

### <a name="network-bandwidth-considerations"></a>網路頻寬考量

若要計算複寫 （初始複寫然後 delta） 的所需的頻寬，您可以使用容量規劃工具。 若要控制的頻寬複寫適用於您有幾個選項︰

- **節流頻寬**︰ HYPER-V 複寫次要網站流量透過特定 HYPER-V 主機。 您可以節流頻寬主機伺服器上。
- **調整頻寬**︰ 您可能會影響用於使用幾個登錄機碼複寫的頻寬。

#### <a name="throttle-bandwidth"></a>節流頻寬

1. 開啟 Microsoft Azure 備份 MMC 嵌入式管理單元 HYPER-V 主機伺服器上。 根據預設 Microsoft Azure 備份的快速鍵是在桌面上或 C:\Program Files\Microsoft Azure 復原服務 Agent\bin\wabadmin。
2. 在嵌入式管理單元中按一下 [**變更內容**]。
3. **Throttling** ] 索引標籤上選取 [**啟用節流備份作業的網際網路頻寬使用量**，設定工作的限制和非工作時間。 有效範圍是從 512 Kbps 102 Mbps 秒。

    ![節流頻寬](./media/site-recovery-vmm-to-azure/throttle2.png)

您也可以使用[設定 OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) cmdlet 設定節流設定。 以下是範例︰

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**設定 OBMachineSetting NoThrottle**表示沒有節流必要。


#### <a name="influence-network-bandwidth"></a>影響網路頻寬

**UploadThreadsPerVM**登錄值控制執行緒所使用的資料傳輸 （初始或 delta 複寫） 磁碟的數目。 較高的值會增加用於複寫的網路頻寬。 **DownloadThreadsPerVM**登錄值指定執行緒回復期間應使用的資料傳輸的數目。

1. 在登錄中瀏覽至**HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**。

    - 修改值**UploadThreadsPerVM** （或如果不存在，建立索引鍵） 用於磁碟複寫控制執行緒。
    - 修改值**DownloadThreadsPerVM** （或如果不存在，建立索引鍵） 用於從 Azure 回復流量控制執行緒。
2. 預設值為 4。 在 「 overprovisioned 」 的網路中，下列登錄機碼應該變更預設值。 最大值為 32。 監控流量最佳化的值。

## <a name="step-6-enable-replication"></a>步驟 6︰ 啟用複寫

現在，如下所示啟用複寫︰

1. 按一下 [**步驟 2︰ 複製應用程式** > **來源**。 啟用複寫第一次後您按一下**+ 複寫**中啟用其他電腦的複製保存庫。

    ![啟用複寫](./media/site-recovery-vmm-to-azure/enable-replication1.png)

2. 在**來源**刀 > 選取 VMM 伺服器與 HYPER-V 主機位於的雲端。 然後按一下**[確定]**。

    ![啟用複寫](./media/site-recovery-vmm-to-azure/enable-replication-source.png)

3. 在**目標**選取訂閱、 文章容錯部署模型，以及您所使用的複製資料的儲存空間帳戶。

    ![啟用複寫](./media/site-recovery-vmm-to-azure/enable-replication-target.png)

4. 選取您想要使用的儲存空間帳戶。 如果您想要使用不同的儲存的帳戶可能會讓您可以[建立一個](#set-up-an-azure-storage-account)。 若要建立儲存體帳戶使用資源管理員模型按一下 [**建立新**。 如果您想要建立使用傳統模式的儲存空間帳戶，您的[Azure 入口網站中](../storage/storage-create-storage-account-classic-portal.md)。 然後按一下**[確定]**。
5. 選取 [Azure 網路和子網路時它們開始後移轉後，Azure Vm 會將連線。 選取 [**目前的選取電腦的設定**]，以將網路設定套用至所有電腦您選取 [保護]。 選取 [**更新版本設定**]，以選取 Azure 網路每部電腦]。 如果您想要使用不同的網路會有您可以[建立一個](#set-up-an-azure-network)。 若要建立網路使用的資源管理員模型按一下 [**建立新**。如果您想要建立使用傳統模式網路必須執行該[Azure 入口網站中](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)。 如果有的話，請選取 [子網路]。 然後按一下**[確定]**。
6. **虛擬機器**中 > **選取虛擬機器**按一下，然後選取您想要複製的每一部電腦。 您可以只選取可以啟用複寫的電腦。 然後按一下**[確定]**。

    ![啟用複寫](./media/site-recovery-vmm-to-azure/enable-replication5.png)

5. 在 [**內容** > **設定屬性**] 下，選取的 Vm，和 OS 磁碟選取的作業系統。 然後按一下**[確定]**。 您可以稍後設定其他屬性。

    ![啟用複寫](./media/site-recovery-vmm-to-azure/enable-replication6.png)


12. **複製設定**中 > **複寫設定**] 下，選取您想要套用的 [受保護的 Vm 複寫原則。 然後按一下**[確定]**。 您可以修改**設定**的複寫原則 > **複寫原則**> 原則名稱 >**編輯設定**。 已複製的電腦和新電腦會使用您套用的變更。

    ![啟用複寫](./media/site-recovery-vmm-to-azure/enable-replication7.png)

您可以追蹤進度**設定**中**啟用保護**工作的 > **工作** > **網站復原工作**。 **完成保護**工作執行電腦之後，就可以容錯移轉。

### <a name="view-and-manage-vm-properties"></a>檢視及管理 VM 屬性

我們建議您確認來源電腦的內容。 請記住 Azure VM 名稱應符合[Azure 虛擬機器](site-recovery-best-practices.md#azure-virtual-machine-requirements)需求。

1. 按一下 [**設定** > **受保護的項目** > **複製項目**> 並選取電腦以查看詳細資料。

    ![啟用複寫](./media/site-recovery-vmm-to-azure/vm-essentials.png)

2. 在 [**內容**中，您可以檢視 vm 複寫和容錯移轉資訊。

    ![啟用複寫](./media/site-recovery-vmm-to-azure/test-failover2.png)

3. 在**計算和網路** > **計算屬性**您可以指定 Azure 虛擬記憶體名稱與目標的大小。 修改以符合[Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements)的需求，如果您要的名稱。 您也可以檢視及修改目標網路、 子網路和 Azure vm 指派的 IP 位址相關資訊。 請注意下列事項︰

    - 您可以設定的目標 IP 位址。 如果您沒有在電腦上提供的地址，失敗會使用 DHCP。 如果您設定的位址，無法在錯誤後移轉，將會失敗容錯移轉。 相同的目標 IP 位址可用於測試容錯移轉如果地址被用於測試容錯移轉網路。
    - 您指定為目標虛擬機器，，如下所示的大小取決於網路介面卡數︰

        - 如果來源電腦上的網路介面卡的數字小於或等於的可容許的目標電腦大小的介面卡的數字，然後目標會有數相同的介面卡做為來源。
        - 如果來源虛擬機器介面卡數目超過允許的目標大小的目標大小的最大的數字會使用。
        - 例如，如果來源電腦有兩個網路介面卡，目標電腦大小支援四個目標電腦會有兩個介面卡。 如果來源電腦有兩個介面卡，但支援的目標大小僅支援其中一個目標電腦會有只有一個介面卡。     
        - 如果 VM 有多個網路介面卡，將所有連線到相同的網路。

    ![啟用複寫](./media/site-recovery-vmm-to-azure/test-failover4.png)

5.  在 [**磁碟**就可以看見的作業系統和資料磁碟 VM 複寫。



## <a name="step-7-test-your-deployment"></a>步驟 7︰ 測試您的部署

若要測試部署中，您可以執行測試容錯移轉單一虛擬機器或包含一或多個虛擬機器復原計劃。


### <a name="prepare-for-failover"></a>準備容錯移轉

- 若要執行測試容錯移轉我們建議您從 Azure 生產網路 （Azure 中建立新的網路時，這是預設行為），建立新的 Azure 網路，具有隔離。 [瞭解更多](site-recovery-failover.md#run-a-test-failover)有關執行測試容錯移轉。
- 為獲得最佳效能，您無法透過 Azure，請在 [受保護的電腦上安裝 Azure 代理程式。 它可讓您更快速開機，並可協助進行疑難排解。 安裝[Linux](https://github.com/Azure/WALinuxAgent)或[Windows](http://go.microsoft.com/fwlink/?LinkID=394789)代理程式。
- 若要完全測試您的部署必須基礎結構，以複製電腦如預期般運作。 如果您想要測試 Active Directory 和 DNS，可以建立 dns 網域控制站的虛擬機器並將此複寫到 Azure 使用 Azure 網站復原。 閱讀更多在[Active Directory 測試容錯移轉考量](site-recovery-active-directory.md#considerations-for-test-failover)。
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

- 加入公用端點 RDP 通訊協定 （連接埠 3389），並指定登入認證。
- 請確定您沒有任何網域原則，無法連線至虛擬機器使用公用的地址。
- 請嘗試連線。 如果您無法連線，請確認 VM 正在執行。 如需其他疑難排解秘訣請閱讀[本文](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)。

如果您想要存取後使用安全命令介面的用戶端 (ssh) 的移轉後執行 Linux Azure VM，請執行下列動作︰

**容錯移轉之前的內部部署電腦上**︰

- 請確定 Azure VM 上的安全命令介面服務會設定為自動啟動，在系統開機。
- 核取防火牆規則，讓它的 SSH 連線。

**在 Azure VM 後移轉後**︰

- 網路安全性] 群組上的規則失敗 VM 和 Azure 子網路連線到的必須允許連入連線 SSH 連接埠。
- 若要允許 SSH 連接埠 （TCP 連接埠 22 預設） 上的連入連線，應該建立公用結束點。
- 如果 VM 存取透過 VPN 連線 （Express 路由或網站 VPN） 用戶端可以使用透過 SSH 直接連接 VM。


### <a name="run-a-test-failover"></a>執行測試容錯移轉

若要執行測試容錯移轉執行下列動作︰

1. 在 [**設定**] 中的單一 VM 失敗 > **複製項目**]，按一下 VM > **+ 測試容錯移轉**。
2. 透過復原計劃，在 [**設定**失敗 > **復原方案**，以滑鼠右鍵按一下方案 >**測試容錯移轉**。 若要建立復原計劃[依照這些指示進行](site-recovery-create-recovery-plans.md)。

3. 在**測試容錯移轉**選取 Azure 的 Azure Vm 連線之後發生錯誤後移轉的網路。
4. 按一下**[確定]**以開始容錯移轉]。 您可以按一下以開啟其內容，VM 上] 或 [**測試容錯移轉**工作**設定**中追蹤進度 > **網站復原工作**。
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


## <a name="monitor-your-deployment"></a>監控您的部署

以下是如何監視設定的設定、 狀態及狀況網站復原部署︰

1. 按一下 [保存庫名稱，即可存取**基本資訊**儀表板上。 在此儀表板，您可以網站復原工作、 複寫狀態、 修復計劃、 伺服器狀況和事件。  您可以自訂顯示的並排顯示和給您，包括其他網站復原和備份保存庫的狀態最有用的版面配置的基礎。

    ![基本版](./media/site-recovery-vmm-to-azure/essentials.png)

2. [**健康狀況**] 方塊中，您可以監視網站伺服器 （VMM] 或 [設定伺服器） 所遇到的問題，然後在過去 24 小時網站復原引發的事件。
3. 您可以管理並監控複寫中**複製的項目**，**修復方案**，然後**網站復原工作**磚。 您可以深入瞭解**設定**中的工作 -> **工作** -> **網站復原工作**。


## <a name="next-steps"></a>後續步驟

在您的部署之後設定，並執行，[進一步](site-recovery-failover.md)瞭解不同類型的容錯移轉。
