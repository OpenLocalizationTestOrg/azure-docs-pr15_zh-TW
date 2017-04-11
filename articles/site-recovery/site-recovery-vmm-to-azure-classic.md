<properties
    pageTitle="Azure 複寫 HYPER-V 虛擬機器中 VMM 雲朵 |Microsoft Azure"
    description="本文將說明如何複寫 HYPER-V 虛擬機器位於 [系統管理中心 VMM 雲朵至 Azure HYPER-V 主機上。"
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
    ms.topic="hero-article"
    ms.date="05/06/2016"
    ms.author="raynew"/>

#  <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure"></a>Azure 複寫 HYPER-V 虛擬機器中 VMM 雲朵

> [AZURE.SELECTOR]
- [Azure 入口網站](site-recovery-vmm-to-azure.md)
- [PowerShell-資源管理員](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [傳統的入口網站](site-recovery-vmm-to-azure-classic.md)
- [PowerShell-傳統](site-recovery-deploy-with-powershell.md)



Azure 網站復原服務佔業務連續性和損壞復原 (BCDR) 策略來協調複寫、 錯誤移轉及復原的虛擬機器和實體伺服器。 Azure，或第二個內部部署資料中心，可將複寫機器。 概述讀[什麼是 Azure 網站復原？](site-recovery-overview.md)。

## <a name="overview"></a>概觀

本文將說明如何部署網站復原複寫 HYPER-V 虛擬機器中 VMM 私人雲朵至 Azure HYPER-V host （主機） 伺服器上。

本文包含此案例的必要條件，並顯示如何設定網站復原保存庫、 取得來源 VMM 伺服器上安裝了 Azure 網站復原提供者，註冊此伺服器保存庫、 新增 Azure 儲存體帳戶、 HYPER-V host （主機） 伺服器上安裝 Azure 修復服務代理程式，設定會套用至所有受保護的虛擬機器 VMM 雲朵保護設定然後啟用這些虛擬機器中的保護。 藉由測試以確保一切運作正常容錯移轉完成。

在底端的本文中，或在[Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上張貼任何註解或問題。

## <a name="architecture"></a>架構

![架構](./media/site-recovery-vmm-to-azure-classic/topology.png)

- Azure 網站修復提供者上 VMM 安裝網站復原部署時，在網站復原保存庫中註冊 VMM 伺服器。 提供者會與網站復原處理複寫協調流程通訊。
- 在網站復原部署 HYPER-V host （主機） 伺服器上安裝 Azure 修復服務代理程式。 處理資料複製到 Azure 儲存體。


## <a name="azure-prerequisites"></a>Azure 的先決條件

以下是您所需的 Azure 中。

**必要條件** | **詳細資料**
--- | ---
**Azure 帳戶**| 您必須以[Microsoft Azure](https://azure.microsoft.com/)帳戶。 您可以開始使用[免費的試用版](https://azure.microsoft.com/pricing/free-trial/)。 [瞭解更多](https://azure.microsoft.com/pricing/details/site-recovery/)有關網站復原價格。
**Azure 儲存體** | 您必須 Azure 儲存體帳戶儲存複製的資料。 複製的資料會儲存在 Azure 儲存空間，容錯移轉發生時 Azure Vm 會開始。 <br/><br/>您需要[標準地理多餘的儲存的帳戶](../storage/storage-redundancy.md#geo-redundant-storage)。 帳戶必須為網站修復服務，相同的區域，並會與同一份訂閱相關聯。 請注意進階版儲存帳戶複寫目前不支援和不應使用。<br/><br/>[閱讀相關資訊](../storage/storage-introduction.md)Azure 儲存空間。
**Azure 網路** | 您必須容錯移轉發生時，將會連線到的 Azure Vm Azure 虛擬網路。 Azure 虛擬網路必須是網站復原保存庫為相同的區域。

## <a name="on-premises-prerequisites"></a>內部部署的先決條件

以下是您的需要內部部署。

**必要條件** | **詳細資料**
--- | ---
**VMM** | 您必須至少有一個 VMM 伺服器部署為獨立的實體或虛擬伺服器，或虛擬叢集。 <br/><br/>VMM 伺服器應該執行系統管理中心 2012 R2 使用最新的積存性更新。<br/><br/>您必須至少有一個雲端 VMM 伺服器上設定。<br/><br/>您要保護的來源雲端必須包含一個或多個 VMM 主機群組。<br/><br/>進一步瞭解如何設定在 VMM 雲朵[逐步解說︰ 使用系統管理中心 2012 SP1 VMM 建立私人的雲朵](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx)Keith Mayer 部落格上。
**Hyper-v** | 您需要一個或多個 HYPER-V 主機伺服器或叢集 VMM 雲端中。 Host （主機） 伺服器應有及一或多個 Vm。 <br/><br/>HYPER-V 伺服器上必須執行至少**Windows Server 2012 R2** HYPER-V 角色或**Microsoft HYPER-V Server 2012 R2** ，安裝最新的更新。<br/><br/>包含您要保護的 Vm 任何 HYPER-V 伺服器必須位於 VMM 雲端。<br/><br/>如果您執行的 HYPER-V 叢集記事中的叢集代理人不會自動建立如果您有靜態 IP 位址基礎叢集。 您必須以手動方式設定叢集代理人。 劉 Finn 部落格文章中[進一步瞭解](https://www.petri.com/use-hyper-v-replica-broker-prepare-host-clusters)。
**受保護的電腦** | 您想要保護的 Vm 應該遵守[Azure 需求](site-recovery-best-practices.md#azure-virtual-machine-requirements)。


## <a name="network-mapping-prerequisites"></a>網路對應的先決條件
當您在保護 Azure 網路對應地圖來源 VMM 伺服器上的 VM 網路之間的虛擬機器及目標 Azure 的網路啟用下列動作︰

- 在相同的網路上的容錯移轉可以連線至彼此，不管修復您的方案資料它們是在所有電腦。
- 如果網路閘道器的目標 Azure 網路上的設定，虛擬機器可以連線至其他內部部署虛擬機器中。
- 如果您沒有設定網路對應只虛擬機器的容錯相同的復原計劃都能連線至其他 Azure 失敗之後。

如果您想要部署網路對應必須下列動作︰

- 您想要保護來源 VMM 伺服器上的虛擬機器應該連線到 VM 網路。 該網路應該邏輯網路雲端與相關聯的連結。
- 要複製的虛擬機器可以連線後移轉後 Azure 網路。 在容錯移轉時，會選取這個網路。 網路應該 Azure 網站復原訂閱為相同的區域。

準備網路對應，如下所示︰

1. [閱讀有關](site-recovery-network-mapping.md)網路對應需求。
2. 準備 VMM VM 網路︰

    - [設定邏輯的網路](https://technet.microsoft.com/library/jj721568.aspx)。
    - [設定 VM 網路](https://technet.microsoft.com/library/jj721575.aspx)。


## <a name="step-1-create-a-site-recovery-vault"></a>步驟 1︰ 建立網站復原保存庫

1. 從您要註冊 VMM 伺服器登入[管理入口網站](https://portal.azure.com)。
2. 按一下 [**資料服務** > **修復服務** > **網站復原保存庫**。
3. 按一下 [**建立新** > **快速建立**。
4. 在 [**名稱**] 中，輸入好記的名稱來識別保存庫。
5. 在 [**地區**中，選取 [保存庫的地理區域。 若要檢查支援的區域，請參閱[Azure 網站復原定價詳細資料](https://azure.microsoft.com/pricing/details/site-recovery/)中的地理可用性。
6. 按一下 [**建立保存庫**]。

    ![新的保存庫](./media/site-recovery-vmm-to-azure-classic/create-vault.png)

請確認已成功建立保存庫狀態列。 保存庫會列為**作用中**主要的 [復原服務] 頁面上。

## <a name="step-2-generate-a-vault-registration-key"></a>步驟 2︰ 產生保存庫登錄機碼

產生保存庫登錄機碼。 下載 Azure 網站修復提供者，並將其安裝於 VMM 伺服器之後，您會使用此按鍵在保存庫註冊 VMM 伺服器。

1. 在 [**復原服務**] 頁面上，按一下 [保存庫，以開啟 [快速入門] 頁面。 也可以隨時使用圖示開啟快速入門。

    ![快速入門] 圖示](./media/site-recovery-vmm-to-azure-classic/qs-icon.png)

2. 在下拉式清單中，選取**內部部署 VMM 網站與 Microsoft Azure 之間**。
3. 在 [**準備 VMM 伺服器**，請按一下 [**產生登錄機碼**檔案]。 索引鍵的檔案會自動產生且有效產生後的 5 天。 如果您不從 VMM 伺服器，必須先將此檔案複製到伺服器存取 Azure 入口網站。

    ![登錄機碼](./media/site-recovery-vmm-to-azure-classic/register-key.png)

## <a name="step-3-install-the-azure-site-recovery-provider"></a>步驟 3︰ 安裝 Azure 網站修復提供者

1. 在 [**快速啟動** > **準備 VMM 伺服器**，按一下 [**下載 Microsoft Azure 網站修復提供者 VMM 伺服器上的安裝**，以取得最新版本的提供者安裝檔案。
2. 執行此來源 VMM 伺服器上的檔案。

    >[AZURE.NOTE] 如果 VMM 配置叢集，且您要安裝的提供者，第一次就會將其安裝在作用中的節點，並完成安裝在保存庫註冊 VMM 伺服器。 再安裝在其他節點的提供者。 請注意，是否您要升級的提供者必須先將所有節點升級，因為他們應該所有執行相同的提供者版本。

3. 安裝程式會在 prerequirements 檢查，並要求停止 VMM 服務開始提供者設定的權限。 自動將重新啟動 VMM 服務，設定完成。 如果您要安裝在 VMM 叢集系統會提示您停止叢集角色。

4. 在 [ **Microsoft Update**您可以選擇加入集更新。 使用此設定啟用會根據您的 Microsoft Update 原則安裝更新的提供者。

    ![Microsoft Update](./media/site-recovery-vmm-to-azure-classic/updates.png)


5.  提供者的安裝位置設定為 [ ** <SystemDrive>\Program Files\Microsoft 系統管理中心 2012 R2\Virtual 機器 Manager\bin**。 按一下 [**安裝**]。

    ![InstallLocation](./media/site-recovery-vmm-to-azure-classic/install-location.png)

6. 已安裝的提供者之後按一下 [註冊伺服器保存庫中的 [**註冊**]。

    ![InstallComplete](./media/site-recovery-vmm-to-azure-classic/install-complete.png)

9. 在 [**保存庫名稱**，確認伺服器註冊保存庫的名稱。 按一下 [*下一步*]。

    ![伺服器註冊](./media/site-recovery-vmm-to-azure-classic/vaultcred.PNG)

7. 在 [**網際網路連線**指定 VMM 伺服器上執行的提供者連線至網際網路的方式。 選取 [**現有的 proxy 設定連線**，使用 [在伺服器上設定的預設網際網路連線設定。

    ![網際網路設定](./media/site-recovery-vmm-to-azure-classic/proxydetails.PNG)

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

    ![Lastpage](./media/site-recovery-vmm-to-azure-classic/provider13.PNG)

註冊後，從 VMM 伺服器的中繼資料擷取是 Azure 網站復原。 伺服器會顯示在保存庫中的 [**伺服器**] 頁面上的 [ **VMM 伺服器**] 索引標籤。

### <a name="command-line-installation"></a>命令列安裝

Azure 網站修復提供者也可以使用下列命令列會安裝。 這個方法可以用於伺服器核心的 Windows Server 2012 R2 上安裝的提供者。

1. 下載資料夾的提供者安裝檔案及登錄機碼。 例如︰ C:\ASR。
2. 停止系統管理中心虛擬機器管理員服務
3. 提高權限的命令提示字元中擷取提供者安裝程式，使用這些命令︰

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

4. 請安裝提供者，如下所示︰

        C:\ASR> setupdr.exe /i

5. 請註冊提供者，如下所示︰

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>       

參數在哪裡，如下所示︰

 - **/Credentials** ︰ 強制參數會指定登錄鍵檔案所在的位置  
 - **/FriendlyName** ︰ 強制參數會出現在 Azure 網站復原入口網站中的 HYPER-V 主機伺服器名稱。
 - **/EncryptionEnabled** ︰ 若要指定您是否要加密 （位於其餘加密） 中 Azure 虛擬機器的選擇性參數。 檔案名稱應有**.pfx**副檔名。
 - **/proxyAddress** ︰ 選用的參數會指定 proxy 伺服器的位址。
 - **/proxyport** ︰ 選用的參數會指定 proxy 伺服器的連接埠。
 - **/proxyUsername** ︰ 指定 proxy 使用者名稱的選擇性參數。
 - **/proxyPassword** ︰ 指定 proxy 密碼的選擇性參數。  


## <a name="step-4-create-an-azure-storage-account"></a>步驟 4︰ 建立 Azure 儲存體帳戶

1. 如果您沒有 Azure 儲存體帳戶，請按一下**新增 Azure 儲存體帳戶**]，以建立帳戶。
2. 啟用地理複寫建立帳戶。 它必須 Azure 網站修復服務，為相同的區域，並會與同一份訂閱相關聯。

    ![儲存帳戶](./media/site-recovery-vmm-to-azure-classic/storage.png)

> [AZURE.NOTE] 儲存帳戶的部署網站復原不支援[的儲存空間帳戶移轉](../resource-group-move-resources.md)跨資源群組內的相同的訂閱或訂閱。

## <a name="step-5-install-the-azure-recovery-services-agent"></a>步驟 5︰ 安裝 Azure 復原服務代理程式

VMM 雲端中的每個 HYPER-V 主機伺服器上安裝 Azure 修復服務代理程式。

1. 按一下 [**快速入門** > **下載 Azure 網站復原服務代理程式]，然後安裝主機上的**取得最新版本的代理程式安裝檔案。

    ![安裝復原服務代理程式](./media/site-recovery-vmm-to-azure-classic/install-agent.png)

2. 執行安裝檔案，每個 HYPER-V 主機伺服器上。
3. 在 [**先決條件檢查**] 頁面中，按一下 [**下一步**]。 將會自動安裝任何缺少的必要條件。

    ![先決條件復原服務代理程式](./media/site-recovery-vmm-to-azure-classic/agent-prereqs.png)

4. 在 [**安裝設定**] 頁面中，指定您要安裝代理程式，然後選取 [快取位置安裝備份的中繼資料的位置。 然後按一下 [**安裝**]。
5. 安裝完成後按一下 [**關閉**精靈。

    ![註冊火星代理程式](./media/site-recovery-vmm-to-azure-classic/agent-register.png)

### <a name="command-line-installation"></a>命令列安裝

您也可以從命令列使用這個命令安裝 Microsoft Azure 修復服務代理程式︰

    marsagentinstaller.exe /q /nu

## <a name="step-6-configure-cloud-protection-settings"></a>步驟 6︰ 設定雲端保護設定

註冊 VMM 伺服器之後，您可以設定雲端保護。 當您安裝的提供者 VMM 伺服器上的所有雲朵就會出現在保存庫中的 [<b>受保護的項目</b>] 索引標籤時，您可以啟用**保存庫的同步處理雲端資料**的選項。

![已發佈的雲端](./media/site-recovery-vmm-to-azure-classic/clouds-list.png)

1. 在 [快速入門] 頁面上，按一下 [**設定 VMM 雲朵的保護**。
2. 在 [**受保護的項目**] 索引標籤中，按一下的雲端您想要設定，然後移至 [**設定**] 索引標籤。
3. 在**目標**選取**Azure**。
4. 在**儲存帳戶**中選取所用的複寫 Azure 儲存體帳戶。
5. 若要**關閉**，設定**加密儲存資料**。 這項設定，指定要加密內部部署網站和 Azure 之間複製的資料。
6. 在 [**複製頻率**保留預設設定。 此值指定頻率應該同步資料來源與目標位置。
7. 以**保留復原點**保留預設設定。 預設值為零，主要的虛擬機器只最新復原點儲存在複本 host （主機） 伺服器上。
8. 在**應用程式一致的快照的頻率**，保留預設設定。 此值會指定要多久建立快速查詢的方式。 快照，以確保應用程式都一致時，會使用大量陰影複製服務 (VSS)。  如果您設定值，請確定小於您設定的其他修復點的數目。
9. 在 [**複寫開始時間**] 時，指定初始資料複寫到 Azure 應該開始。 將使用 HYPER-V 主機伺服器上的時區。 我們建議您安排在離峰初始複寫。

    ![雲端複寫設定](./media/site-recovery-vmm-to-azure-classic/cloud-settings.png)

您儲存設定後工作會建立，並且可以監視**工作**] 索引標籤。 VMM 來源雲端中的所有 HYPER-V 主機伺服器會都設定為複寫。

儲存後，就可以在 [**設定**] 索引標籤上修改雲端設定。 若要修改的目標位置 」 或 「 目標儲存帳戶必須移除雲端設定，然後再重新設定雲端。 請注意，是否您要變更的儲存空間帳戶會只套用變更修改儲存帳戶之後，可以使用的保護的虛擬機器。 現有的虛擬機器不會移轉至新的儲存空間帳戶。

## <a name="step-7-configure-network-mapping"></a>步驟 7︰ 設定網路對應
在您開始之前的網路對應確認來源 VMM 伺服器上的虛擬機器連線到 VM 網路。 此外，請建立一個或多個 Azure 虛擬網路。 請注意，多個 VM 網路對應到單一 Azure 網路。

1. 在 [快速入門] 頁面上，按一下 [**對應網路**。
2. 在 [**網路**] 索引標籤上的**來源位置**選取來源 VMM 伺服器。 在**目標位置**選取 Azure。
3. 在**來源**網路顯示 VM 網路 VMM 伺服器相關聯的清單。 在**目標**網路中會顯示 Azure 與訂閱相關聯的網路。
4. 選取來源 VM 網路，然後按一下 [**地圖**]。
5. 在**選取目標網路**頁面上，選取您想要使用的 Azure 網路的目標。
6. 按一下完成對應程序的核取記號。

    ![雲端複寫設定](./media/site-recovery-vmm-to-azure-classic/map-networks.png)

您儲存設定後工作開始追蹤對應程序，並可在 [工作] 索引標籤上監看。 任何現有的複本虛擬機器對應至來源 VM 網路會連線至目標 Azure 網路。 新來源 VM 網路連線的虛擬機器將複寫之後對應 Azure 網路連線。 如果您修改現有的對應與新的網路，複本虛擬機器會使用新的設定連線。

請注意，如果目標網路有多個子網路，而且這些子網路的其中一個有相同的來源虛擬機器有位置，然後複本虛擬機器會連線到目標該子後移轉後的子網路名稱。 如果不有任何名稱相符的目標子網路，請在虛擬機器會連線到網路中的第一個子網路。

> [AZURE.NOTE] 用於部署網站復原的網路不支援[的網路移轉](../resource-group-move-resources.md)跨資源群組內的相同的訂閱或訂閱。

## <a name="step-8-enable-protection-for-virtual-machines"></a>步驟 8︰ 啟用保護虛擬機器

伺服器、 雲朵和網路設定正確之後，您可以啟用虛擬機器雲端中的保護。 請注意下列事項︰

- 虛擬機器必須符合[Azure 需求](site-recovery-best-practices.md#azure-virtual-machine-requirements)。
- 若要啟用保護的作業系統和作業系統磁碟屬性必須設定虛擬機器。 當您建立虛擬機器 VMM 使用虛擬機器範本中時，您可以設定的屬性。 您也可以設定的虛擬機器屬性的 [**一般**] 及 [**硬體設定**] 索引標籤上的現有的虛擬機器這些屬性。 如果您未設定這些屬性 VMM 中您可以設定這些 Azure 網站復原入口網站中。

    ![建立虛擬機器](./media/site-recovery-vmm-to-azure-classic/enable-new.png)

    ![修改虛擬機器屬性](./media/site-recovery-vmm-to-azure-classic/enable-existing.png)


1. 若要啟用保護]，在雲端的虛擬機器所在的**虛擬機器**] 索引標籤上按一下 [**啟用保護** > **新增虛擬機器**。
2. 從雲端中的虛擬機器清單中，選取您要保護的項目。

    ![啟用虛擬機器保護](./media/site-recovery-vmm-to-azure-classic/select-vm.png)

    追蹤進度的**工作**] 索引標籤，包括初始複寫中的 [**啟用保護**] 動作。 **完成保護**工作執行之後就有一個虛擬機器可供容錯移轉。 啟用保護時，且複寫虛擬機器之後，您就可以在 Azure 中檢視。


    ![虛擬機器保護的工作](./media/site-recovery-vmm-to-azure-classic/vm-jobs.png)

3. 驗證的虛擬機器屬性，然後視需要修改。

    ![驗證虛擬機器](./media/site-recovery-vmm-to-azure-classic/vm-properties.png)


4. 在虛擬機器屬性中的 [**設定**] 索引標籤上可以修改下列網路屬性。





- **目標虛擬機器上的網路介面卡的數字**的網路介面卡數取決於您指定為目標虛擬機器的大小。 核取[虛擬機器大小規格](../virtual-machines/virtual-machines-linux-sizes.md#size-tables)支援虛擬機器大小的介面卡的數目。 修改虛擬機器的大小，然後儲存的設定，當您下次開啟**設定**] 頁面將會變更網路介面卡的號碼。 目標虛擬機器網路介面卡的數字是來源虛擬機器上的網路介面卡的最小的數字和網路介面卡支援的選擇，如下所示的虛擬機器大小上限︰

    - 如果來源電腦上的網路介面卡的數字小於或等於的可容許的目標電腦大小的介面卡的數字，然後目標會有數相同的介面卡做為來源。
    - 如果來源虛擬機器介面卡數目超過允許的目標大小然後目標大小的最大值將用於的數字。
    - 例如，如果來源電腦有兩個網路介面卡的目標電腦大小支援四，目標電腦會有兩個介面卡。 如果來源電腦有兩個介面卡，但支援的目標大小僅支援其中一個目標電腦會有只有一個介面卡。    

- 取決於來源虛擬機器的網路的網路對應**網路目標虛擬機器的**虛擬機器連線至網路。 如果來源虛擬機器有一個以上的網路介面卡來源網路對應到目標上不同的網路，您會需要選擇其中一個目標網路。
- **每個網路介面卡的子網路**-每個網路介面卡您可以選取要子網路失敗透過虛擬機器想連線至。
- **目標 IP 位址**-如果來源虛擬機器的網路介面卡已設定為使用靜態 IP 位址，然後您可以為目標虛擬機器中提供的 IP 位址。 使用此功能後移轉後保留來源虛擬機器的 IP 位址。 如果未提供 IP 位址是然後任何可用的 IP 位址指定網路介面卡容錯移轉的時間。 如果目標 IP 位址指定，但已由 Azure 中執行的其他虛擬機器容錯移轉將會失敗。  

    ![修改網路屬性](./media/site-recovery-vmm-to-azure-classic/multi-nic.png)

>[AZURE.NOTE] 不支援 Linux 虛擬機器靜態 IP 位址。

## <a name="test-the-deployment"></a>測試部署

若要測試您的部署，您可以執行測試容錯移轉單一虛擬機器，或建立包含多個虛擬機器、 復原計劃，並執行測試容錯移轉計劃。  

測試容錯移轉模擬您隔離的網路中的錯誤移轉及復原機制。 請注意︰

- 如果您想要在使用遠端桌面後移轉後的 Azure 虛擬機器連線時，啟用遠端桌面連線虛擬機器上執行測試容錯移轉之前。
- 後移轉後您會在使用遠端桌面 Azure 虛擬機器連線使用的公用 IP 位址。 如果您想要執行此動作，請確定您沒有任何網域原則，無法連線至虛擬機器使用公用的地址。

>[AZURE.NOTE] 若要取得最佳效能，當您進行 Azure 容錯移轉時，請確定您受保護的電腦中安裝 Azure 代理程式。 在 [快速啟動藉此，也能協助診斷若有任何問題。 Linux 代理程式可找到[以下](https://github.com/Azure/WALinuxAgent)和 Windows 代理程式可以找到[以下](http://go.microsoft.com/fwlink/?LinkID=394789)

### <a name="create-a-recovery-plan"></a>建立一個修復計劃

1. 在 [**復原方案**] 索引標籤上新增新的方案。 指定的名稱， **VMM**在**來源類型**]，並在**來源**目標的來源 VMM 伺服器會 Azure。

    ![建立復原計劃](./media/site-recovery-vmm-to-azure-classic/recovery-plan1.png)

2. 在 [**選取虛擬機器**頁面上，選取 [若要新增至復原計劃的虛擬機器]。 這些虛擬機器會新增至復原計劃預設群組，群組 1。 測試 100 的虛擬機器單一復原計劃中的最大值。

- 如果您要驗證的虛擬機器屬性之前將其新增至的方案，請按一下它在雲端的 [內容] 頁面上的虛擬機器所在。 您也可以設定的虛擬機器屬性 VMM 主控台中檢視。
- 所有的虛擬機器所顯示的已啟用的保護。 清單中包含兩個已啟用的保護與初始複寫完成後，那些已啟用保護的初始複寫等待的虛擬機器。 僅虛擬機器完成初始複寫可以容錯復原計劃的一部分。

    ![建立復原計劃](./media/site-recovery-vmm-to-azure-classic/select-rp.png)

復原計劃建立之後, 會出現在 [**復原方案**] 索引標籤。 您也可以新增[Azure 自動化 runbooks](site-recovery-runbook-automation.md)復原計劃來自動化容錯移轉期間的動作。

### <a name="run-a-test-failover"></a>執行測試容錯移轉

有兩種方式執行 Azure 測試容錯移轉。

- **測試容錯移轉不 Azure 的網路**，這種類型的測試容錯移轉檢查的虛擬機器出現於正確 Azure 中。 虛擬機器無法連線到任何 Azure 的網路後移轉後。
- **測試 Azure 網路的容錯移轉**，將此類型的容錯移轉檢查整份複寫環境提供最如預期般且無法透過虛擬機器連線至指定的目標 Azure 網路。 子網路處理測試容錯移轉的測試虛擬機器子網路將會知道根據複本虛擬機器的子網路。 當複本虛擬機器的子網路根據來源虛擬機器的子網路，這是一般複寫不同。

如果您想要執行測試容錯移轉不指定 Azure 目標網路啟用保護 Azure 虛擬機器您不需要準備任何項目。 若要執行測試容錯移轉與目標 Azure 網路，您必須從 Azure 生產網路 （預設行為 Azure 中建立新的網路時） 建立新的 Azure 網路，具有隔離。 看看如何[執行測試容錯移轉](site-recovery-failover.md#run-a-test-failover)如需詳細資訊。


您也需要設定基礎結構，以複製虛擬機器如預期般運作。 例如虛擬機器的網域及 DNS 可以複製到 Azure 使用 Azure 網站修復，且無法建立使用測試容錯移轉測試網路中。 查看[測試適用於 active directory 的容錯移轉考量](site-recovery-active-directory.md#considerations-for-test-failover)的更多詳細資料] 區段。

若要執行測試容錯移轉執行下列動作︰

1. 在 [**復原方案**] 索引標籤上選取方案然後按一下 [**測試容錯移轉**。
2. 在 [**確認測試容錯移轉**頁面上選取 [**無**或特定 Azure 網路。  請注意，是否您選取 [無測試容錯移轉會檢查虛擬機器正確複寫到 Azure 但不是會檢查您複製的網路設定。

    ![沒有網路](./media/site-recovery-vmm-to-azure-classic/test-no-network.png)

3. 如果啟用雲端的資料加密，則在**加密金鑰**選取當您開啟啟用雲端的資料加密的選項，在 VMM 伺服器上，提供者的安裝期間所發出的憑證。
4. 在 [**工作**] 索引標籤上，您可以追蹤容錯移轉進度。 您也應該能夠看到在入口網站中 Azure 虛擬機器測試複本。 如果您是從您的內部部署網路設定 access 的虛擬機器可以啟動遠端桌面連線到虛擬機器。
5. 當容錯移轉達到**完成測試**階段時，請按一下 [**完成測試**測試容錯移轉完成]。 您可以追蹤容錯移轉進度和狀態，並執行任何動作所需的 [**工作**] 索引標籤下的向上切入。
6. 後移轉後，您就可以請參閱在入口網站中 Azure 虛擬機器測試複本。 如果您是從您的內部部署網路設定 access 的虛擬機器可以啟動遠端桌面連線到虛擬機器。 執行下列動作︰

    1. 請確認虛擬機器順利啟動。
    2. 如果您想要在使用遠端桌面後移轉後的 Azure 虛擬機器連線時，啟用遠端桌面連線虛擬機器上執行測試容錯移轉之前。 您也需要新增 RDP 端點虛擬機器上。 您可以利用[Azure 自動化 Runbooks](site-recovery-runbook-automation.md) ，若要執行這項作業。
    3. 容錯移轉，如果您使用的公用 IP 位址連線到在使用遠端桌面，Azure 虛擬機器確定後您不需要任何網域原則，無法連線至虛擬機器使用公用的地址。

7.  測試是完成後，執行下列動作︰
    - 按一下 [**測試容錯移轉完成**。 清理自動關閉電源，並刪除測試虛擬機器測試環境。
    - 按一下 [**備忘稿**，錄製並儲存聯測試容錯移轉任何觀察值]。

>

## <a name="next-steps"></a>後續步驟

深入了解[設定修復計劃](site-recovery-create-recovery-plans.md)和[容錯移轉](site-recovery-failover.md)。
