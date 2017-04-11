<properties
    pageTitle="Azure 網站復原使用舊的次要網站複製 HYPER-V Vm VMM 雲端 |Microsoft Azure"
    description="本文將說明如何使用舊複寫 Azure 網站復原的兩個網站之間複製 HYPER-V 虛擬機器。"
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
    ms.date="07/06/2016"
    ms.author="raynew"/>

# <a name="replicate-hyper-v-vms-in-a-vmm-cloud-to-a-secondary-site-with-azure-site-recovery-using-san"></a>Azure 網站復原使用舊的次要網站複製 HYPER-V Vm VMM 雲端

在本文中，您將學習部署網站復原協調和自動化舊複寫和 HYPER-V 虛擬機器中第二個 VMM 網站的系統管理中心 VMM 雲朵容錯移轉的方式。

讀取後本文會底部的本文中，或在[Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上張貼任何註解或問題。 


## <a name="overview"></a>概觀

組織需要的業務連續性和損壞復原 (BCDR) 策略，決定如何應用程式、 負載，以及資料保持執行及使用期間計劃和計畫停機時間和越快越復原正常運作狀況。 您 BCDR 策略中心] 的周圍使商務資料安全及修復，與負載不斷地使用發生損毀的解決方案。

網站復原是 Azure 服務可提供您 BCDR 策略來協調實體伺服器的內部部署和雲端 (Azure) 或次要資料中心的虛擬機器複寫。 時中斷會出現在您的主要位置，您無法透過，讓應用程式可用的工作負載的次要網站。 會傳回標準作業時，您會回到您的主要位置失敗。 網站復原可以使用案例的數字，而且可以保護的工作負載的數字。 如需深入瞭解[什麼是 Azure 網站復原？](site-recovery-overview.md)

本文包含以下設定的相關指示的 HYPER-V Vm 複寫從一個 VMM 網站到另一個使用舊複寫。 其包含架構的概觀，及部署先決條件與指示。 您會探索並分類在 VMM 中佈建 Lun，舊儲存空間配置 HYPER-V 叢集的儲存空間。 完成測試容錯移轉，以確保一切運作正常。


## <a name="why-replicate-with-san"></a>為什麼複製舊嗎？

以下是這種情況下會提供︰

- 提供自動化網站復原企業調整複寫解決方案。
- 利用舊複寫功能提供企業儲存合作夥伴跨兩個光纖通道及 iSCSI 儲存空間。 請參閱我們的[舊儲存合作夥伴](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx)。
- 使用現有保護重要的應用程式部署 HYPER-V 叢集舊基礎結構。
- 來賓叢集提供支援。
- 跨不同階層的應用程式同步處理的複寫低 RTO 和 RPO 及非同步的複寫高彈性，根據儲存陣列功能，可確保複寫一致性。  
- 整合 VMM 提供 VMM 主控台中的舊管理並 SMI-S VMM 中探索現有的儲存空間。  

## <a name="architecture"></a>架構

這種情況下會用來保護您的工作量備份 HYPER-V 虛擬機器從內部部署 VMM 網站到另一個使用舊複寫。

![舊架構](./media/site-recovery-vmm-san/architecture.png)

在這個案例中的元件包括︰

- **內部部署的虛擬機器**，您的內部部署 HYPER-V 伺服器管理 VMM 私人雲朵中包含您要保護的虛擬機器。
- **內部部署 VMM 伺服器**，您可以執行您想要保護的主要網站及次要網站上的一個或多個 VMM 伺服器。
- **舊儲存空間**，主要的網站，一個次要網站上的舊陣列
-  **Azure 網站復原保存庫**— 保存庫協調及協調資料的複本、 錯誤移轉及復原您的內部部署網站之間。
- **Azure 網站修復提供者**，在每個 VMM 伺服器上已安裝的提供者。

## <a name="before-you-start"></a>在您開始之前

請確定您有下列先決條件位置︰

**必要條件** | **詳細資料** 
--- | ---
**Azure**| 您必須以[Microsoft Azure](https://azure.microsoft.com/)帳戶。 您可以開始使用[免費的試用版](https://azure.microsoft.com/pricing/free-trial/)。 [瞭解更多](https://azure.microsoft.com/pricing/details/site-recovery/)有關網站復原價格。 
**VMM** | 您必須至少有一個 VMM 伺服器部署為獨立的實體或虛擬伺服器，或虛擬叢集。 <br/><br/>VMM 伺服器應該執行系統管理中心 2012 R2 使用最新的積存性更新。<br/><br/>您必須至少有一個您要保護的主要 VMM 伺服器上設定的雲端並設定您想要使用的保護與復原次要 VMM 伺服器上的一個雲端<br/><br/>您要保護的來源雲端必須包含一個或多個 VMM 主機群組。<br/><br/>所有 VMM 雲朵都必須設定 HYPER-V 容量設定檔。<br/><br/>深入瞭解設定中[設定 VMM 雲端布料的轉印圖樣](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)VMM 雲朵和[逐步解說︰ 使用系統管理中心 2012 SP1 VMM 建立私人的雲朵](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx)。
**Hyper-v** | 您需要一個或多個 HYPER-V 叢集主要和次要網站，與一或多個 Vm 來源 HYPER-V 叢集上。 在主要和次要位置 VMM 主機群組應有一個或多個 HYPER-V 叢集每個群組中。<br/><br/>Host （主機） 和目標 HYPER-V 伺服器必須至少執行 HYPER-V 角色與 Windows Server 2012，安裝最新的更新。<br/><br/>包含您要保護的 Vm 任何 HYPER-V 伺服器必須位於 VMM 雲端。<br/><br/>如果您執行的 HYPER-V 叢集記事中的叢集代理人不會自動建立如果您有靜態 IP 位址基礎叢集。 您必須以手動方式設定叢集代理人。 劉 Finn 部落格文章中[進一步瞭解](https://www.petri.com/use-hyper-v-replica-broker-prepare-host-clusters)。
**舊儲存空間** | 您可以使用舊複寫複寫來賓直 iSCSI 光纖頻道儲存空間，或使用共用虛擬硬碟 (vhdx) 的虛擬機器。<br/><br/>您必須設定的兩個舊陣列、 一個主要的網站，一個次要。<br/><br/>網路基礎結構應該陣列之間設定。 對等和複寫應進行設定。 複寫授權應該根據儲存陣列需求的設定。<br/><br/>網路應該會設定 HYPER-V 主機伺服器和儲存陣列之間以便主機可以與儲存 Lun 通訊使用 ISCSI 或光纖。<br/><br/> 請檢查[支援儲存陣列](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx)的清單。<br/><br/>應該安裝 SMI S 提供者，儲存陣列製造商提供的和舊陣列應該管理提供者。 設定根據其文件的提供者。<br/><br/>確定 VMM 伺服器可以存取的 IP 位址或 FQDN 在網路上的伺服器陣列的 SMI S 提供者。<br/><br/>每一個舊陣列應有才能使用這個部署中的一或多個儲存資料庫。主要網站 VMM 伺服器需要管理主要陣列及次要 VMM 伺服器會管理次要陣列。<br/><br/>主要網站 VMM 伺服器應該主要的陣列，並管理次要 VMM 伺服器應該管理次要陣列。
**網路對應** | 您可以設定網路對應，確保後移轉後，以最佳方式次要 HYPER-V host （主機） 伺服器上放置複寫的虛擬機器，他們可以連線到適當的 VM 網路。 如果您沒有設定 Vm 無法連線至網路後移轉後的網路對應複本。<br/><br/>若要設定網路對應部署期間確定來源 HYPER-V 主機伺服器上的虛擬機器連線到 VMM VM 網路。 該網路應該連結至雲端。 與相關聯邏輯網狀 < 巴西 /<br/>目標雲端您用來復原次要 VMM 伺服器上的應該對應 VM 網路設定，而且在應連結至對應的邏輯網路與目標雲端相關聯。<br/><br/>[瞭解更多](site-recovery-network-mapping.md)有關網路對應。


## <a name="step-1-prepare-the-vmm-infrastructure"></a>步驟 1︰ 準備 VMM 基礎結構

若要準備 VMM 基礎結構您需要︰

1. 確保 VMM 雲朵設定
2. 整合和分類舊 VMM 中的儲存空間
3. 建立 Lun 及配置存放區
4. 建立複寫群組
5. 設定 VM 網路

### <a name="ensure-vmm-clouds-are-set-up"></a>確保 VMM 雲朵設定

網站復原協調虛擬機器位於 VMM 雲朵 HYPER-V 主機伺服器上的保護。 您需要確保這些雲朵設定正確網站復原部署之前。 如需深入瞭解[建立私人的雲朵](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx)Keith Mayer 部落格上。

### <a name="integrate-and-classify-san-storage-in-vmm"></a>整合和分類舊 VMM 中的儲存空間

新增分類細 VMM 主控台中檢視︰

1. **布料的轉印圖樣**工作區中按一下 [**儲存**]。 按一下 [**常用]** > **新增資源** > **存放裝置**] 來啟動 [新增儲存空間裝置精靈。
2. 在**選取的儲存空間提供者類型**] 頁面上，選取**舊及 NAS 裝置發現和 SMI S 提供者管理**。

    ![提供者類型](./media/site-recovery-vmm-san/provider-type.png)

3. 在 [**指定通訊協定和儲存 SMI S 提供者的地址**] 頁面上選取**SMI S CIMXML**並指定設定連線提供者。
4. 在**提供者的 IP 位址或 FQDN**和**TCP/IP 連接埠**，指定設定連線提供者。 您可以只使用 SSL 連線的 SMI S CIMXML。

    ![提供者連線](./media/site-recovery-vmm-san/connect-settings.png)

5. 在**執行為帳戶**指定的 VMM 執行為帳戶，可以存取提供者，或建立新的帳戶。
6. 在**收集的資訊**] 頁面中，VMM 會自動嘗試探索及匯入儲存裝置的資訊。 若要重試探索，按一下 [**掃描的提供者**。 如果探索程序順利完成，發現的儲存陣列、 儲存集區製造商、 型號及容量會列於頁面。

    ![探索儲存空間](./media/site-recovery-vmm-san/discover.png)

7. 在 [**選取放置管理] 下，並指派分類的儲存集區**，請選取 [VMM 會管理，並為其分派分類的儲存集區]。 將匯入 LUN 資訊，從儲存集區。 建立 Lun 根據您要保護，其容量需求及所需的一起複寫您需求的應用程式。

    ![分類儲存空間](./media/site-recovery-vmm-san/classify.png)

### <a name="create-luns-and-allocate-storage"></a>建立 Lun 及配置存放區

1. 舊儲存整合 VMM 之後就會建立 （提供） 邏輯單位 (Lun):

    - [如何選取 VMM 中建立邏輯單位的方法](https://technet.microsoft.com/library/gg610624.aspx)
    - [如何佈建儲存 VMM 中的邏輯單位](https://technet.microsoft.com/library/gg696973.aspx)

    >[AZURE.NOTE] 已啟用複寫機器後您不應為其新增 Vhd lun 不位於網站復原複寫群組。 如果您執行的動作，就不會偵測到網站復原。

2. 然後配置 HYPER-V 主機叢集儲存容量，以便 VMM 可以部署能夠儲存的虛擬機器資料︰

    - 配置叢集存放區之前必須配置叢集所在 VMM 主機群組。 請參閱[如何分配主機群組的儲存空間邏輯單位](https://technet.microsoft.com/library/gg610686.aspx)，以及[如何配置主機群組儲存集區](https://technet.microsoft.com/library/gg610635.aspx)。</a>.
    - 然後配置叢集[如何設定 HYPER-V 主機叢集 VMM 中的儲存空間](https://technet.microsoft.com/library/gg610692.aspx)中所述的儲存空間容量。</a>.

### <a name="create-replication-groups"></a>建立複寫群組

建立包含所有需要一起複寫的 Lun 複寫群組。

1. VMM 主控台中開啟儲存的陣列屬性，**複寫群組**] 索引標籤，然後按一下 [**新增**]。
2. 然後建立複寫群組。

    ![舊複寫群組](./media/site-recovery-vmm-san/rep-group.png)

### <a name="set-up-networks"></a>設定網路

如果您想要設定網路對應執行下列動作︰

1. [閱讀有關](site-recovery-network-mapping.md)網路對應。
2. 準備 VMM VM 網路︰

    - [設定邏輯的網路](https://technet.microsoft.com/library/jj721568.aspx)。
    - [設定 VM 網路](https://technet.microsoft.com/library/jj721575.aspx)。

## <a name="step-2-create-a-vault"></a>步驟 2︰ 建立保存庫


1. 從您要註冊 VMM 伺服器登入[管理入口網站](https://portal.azure.com)。

2. 展開**資料服務** > **修復服務**，按一下 [**網站復原保存庫**。

3. 按一下 [**建立新** > **快速建立**。

4. 在 [**名稱**] 中，輸入好記的名稱來識別保存庫。

5. 在**區域**中選取保存庫的地理區域。 若要檢查支援的區域，請參閱[Azure 網站復原定價詳細資料](https://azure.microsoft.com/pricing/details/site-recovery/)中的地理可用性。

6. 按一下 [**建立保存庫**]。

    ![新的保存庫](./media/site-recovery-vmm-san/create-vault.png)

請確認已成功建立保存庫狀態列。 保存庫會列為**作用中**主要的 [**復原服務**] 頁面上。


### <a name="register-the-vmm-servers"></a>註冊 VMM 伺服器

1. 開啟 [快速入門] 頁面的 [**復原服務**] 頁面。 也可以隨時使用圖示開啟快速入門。

    ![快速入門] 圖示](./media/site-recovery-vmm-san/quick-start-icon.png)

2. 在下拉式清單中，選取 [**之間 HYPER-V 內部部署陣列複寫的網站**]。

    ![登錄機碼](./media/site-recovery-vmm-san/select-san.png)


3. 在 [**準備 VMM 伺服器**] 下載最新版 Azure 網站修復提供者安裝檔案。
4. 執行此來源 VMM 伺服器上的檔案。 如果 VMM 配置叢集，且您要安裝的提供者，第一次就會將其安裝在作用中的節點，並完成安裝在保存庫註冊 VMM 伺服器。 再安裝在其他節點的提供者。 請注意，是否您要升級的提供者必須先將所有節點升級，因為他們應該所有執行相同的提供者版本。
5. 安裝程式會停止 VMM 服務開始提供者設定的幾個**測試需求核取**及要求權限。 自動將重新啟動 VMM 服務，設定完成。 如果您要安裝在 VMM 叢集系統會提示您停止叢集角色。
6. 在 [ **Microsoft Update**您可以選擇加入集更新。 使用此設定啟用會根據您的 Microsoft Update 原則安裝更新的提供者。

    ![Microsoft Update](./media/site-recovery-vmm-san/ms-update.png)

7. 若要設定的安裝位置**<SystemDrive>\Program Files\Microsoft 系統管理中心 2012 R2\Virtual 機器 Manager\bin**。 按一下 [安裝] 按鈕，即可開始安裝的提供者。

    ![InstallLocation](./media/site-recovery-vmm-san/install-location.png)

8. 已安裝的提供者之後按一下 [註冊] 按鈕，在 [保存庫中註冊伺服器。

    ![InstallComplete](./media/site-recovery-vmm-san/install-complete.png)

9. 在 [**網際網路連線**指定 VMM 伺服器上執行的提供者連線至網際網路的方式。 選取 [**使用預設系統 proxy 設定**使用伺服器上設定的預設網際網路連線設定]。

    ![網際網路設定](./media/site-recovery-vmm-san/proxy-details.png)

    - 如果您想要使用自訂 proxy，您應該將其設定之前安裝提供者。 當您設定自訂的 proxy 設定會執行檢查 proxy 連線測試。
    - 如果您使用自訂 proxy]，或您預設的 proxy 需要的驗證您需要輸入 proxy 詳細資料，包括 proxy 位址和連接埠。
    - 下列 url 應該是從 VMM 伺服器和 hyper-v 主機存取
        - *。 hypervrecoverymanager.windowsazure.com
        - *。 accesscontrol.windows.net
        - *。 backup.windowsazure.com
        - *。 blob.core.windows.net
        - *。 store.core.windows.net
    - 允許[Azure 資料中心 IP 範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (443) 通訊協定所述的 IP 位址。 您必須您打算使用 Azure 區域的白色清單 IP 範圍和西美國。
    - 如果您使用的自訂 proxy 會使用指定的 proxy 認證自動建立 VMM RunAs 帳戶 (DRAProxyAccount)。 設定 proxy 伺服器，以便能順利驗證此帳戶。 可以 VMM 主控台中修改 VMM RunAs 帳戶設定。 若要這麼做，開啟 [設定工作區展開 [安全性]，為帳戶執行，然後按一下然後修改 DRAProxyAccount 密碼。 必須重新啟動 VMM 服務，讓此設定才會生效。

10. 在**登錄機碼**中，選取您從 Azure 網站復原下載並複製到 VMM 伺服器。
11. 在 [**保存庫名稱**，確認伺服器註冊保存庫的名稱。 

    ![伺服器註冊](./media/site-recovery-vmm-san/vault-creds.png)

12. 加密設定只適用於 VMM Azure 案例中，如果您是 VMM VMM 僅限的使用者，然後您可以略過這個畫面。

    ![伺服器註冊](./media/site-recovery-vmm-san/encrypt.png)

13. 在 [**伺服器名稱**，指定識別保存庫中的 VMM 伺服器好記的名稱。 在叢集設定中指定 VMM 叢集角色名稱。
14. 在**初始雲端中繼資料同步處理**指定好記的名稱伺服器，會出現在保存庫，並選取您想要保存庫與同步處理所有的雲朵 VMM 伺服器上的中繼資料。 此動作只需要進行一次在每個伺服器上。 如果您不想要同步處理所有的雲朵，您可以離開取消核取此設定，並同步處理個別在雲端中的屬性 VMM 主控台中每個雲端。

    ![伺服器註冊](./media/site-recovery-vmm-san/friendly-name.png)

15. 按一下 [**下一步**完成程序。 註冊後，從 VMM 伺服器的中繼資料擷取是 Azure 網站復原。 伺服器會顯示在保存庫中的 [**伺服器**] 頁面上的 [ *VMM 伺服器*] 索引標籤。

### <a name="command-line-installation"></a>命令列安裝

Azure 網站修復提供者也可以使用下列命令列會安裝。 這個方法可以用於伺服器核心的 Windows Server 2012 R2 上安裝的提供者。

1. 下載提供者安裝檔案和資料夾的登錄機碼說出 C:\ASR
2. 停止系統管理中心虛擬機器管理員服務
3. 擷取的提供者安裝程式具有**管理員**權限的命令提示字元中執行下列動作︰

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

4. 安裝提供者來執行下列動作︰

        C:\ASR> setupdr.exe /i

5. 註冊提供者來執行下列動作︰

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>         

參數位置如下︰

 - **/Credentials** ︰ 強制參數會指定登錄鍵檔案所在的位置  
 - **/FriendlyName** ︰ 強制參數會出現在 Azure 網站復原入口網站中的 HYPER-V 主機伺服器名稱。
 - **/EncryptionEnabled** ︰ 您需要使用只能在 VMM Azure 案例中，如果您需要在靜止中 Azure 虛擬機器加密的選擇性參數。 請確定您提供副檔名為**.pfx**檔的名稱。
 - **/proxyAddress** ︰ 選用的參數會指定 proxy 伺服器的位址。
 - **/proxyport** ︰ 選用的參數會指定 proxy 伺服器的連接埠。
 - **/proxyUsername** ︰ 選用的參數會指定 Proxy 使用者名稱 （如果 proxy 需要驗證）。
 - **/proxyPassword** ︰ 選用的參數會指定驗證的 proxy 伺服器 （如果 proxy 需要驗證） 的密碼。


## <a name="step-3-map-storage-arrays-and-pools"></a>步驟 3︰ 對應儲存陣列和集區

對應陣列來指定哪些次要儲存集區接收主要資料庫複寫資料。 因為當您啟用複寫群組保護時，會使用對應資訊設定雲端保護之前，您應該將對應儲存空間。

開始之前雲朵出現的核取保存庫中。 選取要同步處理所有的雲朵，當您安裝提供者，或選取要同步處理雲端中的屬性 VMM 主控台中的 [**一般**] 索引標籤上的特定雲端，則會偵測到雲朵。 然後對應儲存陣列，如下所示︰

1. 按一下 [**資源**] > **伺服器儲存空間** > **地圖來源和目標陣列**。
    ![伺服器註冊](./media/site-recovery-vmm-san/storage-map.png)
2. 選取主要的網站上儲存陣列和其對應到第二個網站上的儲存空間陣列。
3.  對應陣列中的來源及目標儲存集區。 若要這麼做，請**儲存集區**中選取對應的來源及目標儲存集區。

    ![伺服器註冊](./media/site-recovery-vmm-san/storage-map-pool.png)

## <a name="step-4-configure-cloud-protection-settings"></a>步驟 4︰ 設定雲端保護設定

登錄 VMM 伺服器之後，您可以設定雲端保護。 當您安裝的提供者 VMM 伺服器上的所有雲朵就會出現在保存庫中的 [<b>受保護的項目</b>] 索引標籤時，您可以啟用**保存庫的同步處理雲端資料**的選項。

![已發佈的雲端](./media/site-recovery-vmm-san/clouds-list.png)

1. 在 [快速入門] 頁面上，按一下 [**設定 VMM 雲朵的保護**。
2. 在 [**受保護的項目**] 索引標籤上選取您想要設定，然後移至 [**設定**] 索引標籤的雲端。 請注意︰
3. 在**目標**，選取 [ **VMM**]。
4. 在**目標位置**，選取 [現場 VMM 伺服器管理的雲端您想要使用的 [復原]。
5. 在**目標雲端**，選取您想要使用的來源雲端中的虛擬機器容錯移轉的目標雲端。 請注意︰
    - 我們建議您選取符合您要保護的虛擬機器復原需求目標雲端。
    - 雲端只能屬於單一雲端組，做為主要或目標雲端。
6. Azure 網站復原驗證雲朵有存取舊複寫簡訊儲存空間，儲存陣列 peered。 參與陣列同儕會顯示。
7. 如果驗證成功，在**複寫類型**，請選取**舊**。

您儲存設定後工作會建立，並且可以監視**工作**] 索引標籤。 在 [**設定**] 索引標籤上，就可以修改雲端設定。 如果您想要修改目標位置或目標雲端必須移除雲端設定，並重新設定雲端。

## <a name="step-5-enable-network-mapping"></a>步驟 5︰ 啟用網路對應

1. 在 [快速入門] 頁面上，按一下 [**對應網路**。
2. 選取來源 VMM 伺服器要對應網路，然後按一下 [對應網路目標 VMM 伺服器。 來源網路和其關聯的目標網路的清單會顯示。 空白值會顯示目前未對應的網路。 按一下 [若要檢視的每一個網路的子網路來源和目標的網路名稱旁邊的 [資訊] 圖示。
3. 在 [**來源上的網路**中，選取網路，然後按一下 [**地圖**。 服務偵測到目標伺服器上的 VM 網路，並顯示。

    ![舊架構](./media/site-recovery-vmm-san/network-map1.png)

4. 在隨即出現的對話方塊中，選取一個 VM 網路目標 VMM 伺服器。

    ![舊架構](./media/site-recovery-vmm-san/network-map2.png)

5. 當您選取目標網路時，會顯示使用來源網路受保護的雲朵。 用來保護的雲朵與相關聯的可用的目標網路也會顯示。 我們建議您選取目標網路可供您使用的保護的所有雲朵。
6.  按一下完成對應程序的核取記號。 工作會開始追蹤對應進度。 您可以檢視其**工作**] 索引標籤。


## <a name="step-6-enable-replication-for-replication-groups"></a>步驟 6︰ 啟用複寫複寫群組

您可以啟用的虛擬機器保護之前必須啟用複寫儲存複寫群組。

1. 在 Azure 網站復原入口網站中，在 [屬性] 頁面的主要雲端中開啟 [**虛擬機器**] 索引標籤。 按一下 [**新增複寫群組**。
2. 選取一或更多雲端，與相關聯的 VMM 複寫群組驗證來源和目標陣列，與指定的頻率。

這項作業完成後，Azure 網站修復，與 VMM SMI S 提供者佈建目標網站儲存空間 Lun，並啟用儲存複寫。 如果已複製複寫群組，Azure 網站復原重複使用現有的複寫關聯性，並更新 Azure 網站復原中的資訊。

## <a name="step-7-enable-protection-for-virtual-machines"></a>步驟 7︰ 啟用保護虛擬機器

群組正在複寫後的儲存空間，啟用保護虛擬機器在 VMM 主控台中使用下列方法之一︰

- **新的虛擬機器**，當您建立新的虛擬機器您啟用 Azure 網站復原保護並關聯複寫群組的虛擬機器中 VMM 主控台。
使用此選項 VMM 會使用 「 智慧定位，以最佳方式將複寫群組 Lun 上的 [虛擬機器儲存空間。 Azure 網站復原協調陰影虛擬機器的次要網站上，建立並配置以便複本虛擬機器開始後移轉後的容量。
- **現有的虛擬機器**— 虛擬機器已經部署 VMM 中，您可以啟用 Azure 網站復原保護]，並執行儲存移轉到複寫群組。 完成之後，VMM Azure 網站復原偵測到新的虛擬機器，並啟動 Azure 網站修復管理的保護。 陰影虛擬機器，建立第二個網站上，並配置以便複本虛擬機器開始後移轉後的容量。

    ![啟用保護](./media/site-recovery-vmm-san/enable-protect.png)

虛擬機器會啟用保護後顯示 Azure 網站復原主控台中檢視。 您可以檢視虛擬機器內容、 追蹤狀態，及透過包含多個虛擬機器複寫群組失敗。 請注意，在舊複寫複寫群組相關聯的所有虛擬機器必須都容錯放在一起。 這是因為發生錯誤後移轉儲存層第一次。 請務必妥善群組複寫群組與位置只虛擬機器放在一起。

>[AZURE.NOTE] 已啟用複寫機器後您不應為其新增 Vhd lun 不位於網站復原複寫群組。 如果您執行的動作，就不會偵測到網站復原。

您可以追蹤進度的**工作**] 索引標籤，包括初始複寫中的 [啟用保護] 動作。 完成保護工作執行之後就有一個虛擬機器可供容錯移轉。

![虛擬機器保護的工作](./media/site-recovery-vmm-san/job-props.png)

## <a name="step-8-test-the-deployment"></a>步驟 8︰ 測試部署

測試您的部署，請確定虛擬機器和資料如預期般超過失敗。 執行此動作會建立復原規劃選取複寫群組。計劃，然後執行測試容錯移轉。

1. 在 [**復原方案**] 索引標籤上按一下 [**建立復原規劃**。
2. 指定的復原計劃及來源和目標 VMM 伺服器的名稱。 來源伺服器必須啟用錯誤移轉及復原的虛擬機器。 選取 [檢視設定舊複寫的雲朵**舊**]。
3.
    ![建立復原計劃](./media/site-recovery-vmm-san/r-plan.png)

4. 在 [**選取虛擬機器**中，選取 [複寫群組]。 將選取的複寫群組相關聯的所有虛擬機器，並將其新增至復原計劃。 這些虛擬機器會新增至復原計劃預設群組，群組 1。 如有必要，您可以新增更多的群組。 請注意，在複寫虛擬機器就會開始根據的復原計劃群組的順序。

    ![加入虛擬機器](./media/site-recovery-vmm-san/r-plan-vm.png)
5. 復原之後計劃一建立之後，它會出現在 [**復原方案**] 索引標籤上的清單。
6. 在 [**復原方案**] 索引標籤上選取方案然後按一下 [**測試容錯移轉**。
7. 在**確認測試容錯移轉**頁面上，選取 [**無**]。 請注意，使用此選項複本的虛擬機器上啟用失敗無法連線至網路。 這會測試虛擬機器如預期般失敗，但不會測試複寫網路環境。 看看如何[執行測試容錯移轉](site-recovery-failover.md#run-a-test-failover)如需有關如何使用不同的網路選項的詳細資訊。


    ![選取測試網路](./media/site-recovery-vmm-san/test-fail1.png)

8. 測試虛擬機器將會建立主複本虛擬機器存在於主機上。 不會新增到複本虛擬機器位於的雲端。
9. 複寫之後複本虛擬機器會有不同的主索引的虛擬機器的 IP 位址的 IP 位址。 如果您從 DHCP 發出地址然後會自動更新。 如果您不使用 DHCP，而您想要確定是相同的地址必須執行幾個指令碼。
10. 執行此範例指令碼來擷取的 IP 位址。

        $vm = Get-SCVirtualMachine -Name <VM_NAME>
        $na = $vm[0].VirtualNetworkAdapters>
        $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
        $ip.address  

11. 執行此範例指令碼來更新 DNS，指定您使用的上一個範例指令碼所擷取的 IP 位址。

        [string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord

## <a name="next-steps"></a>後續步驟

之後，您已執行測試容錯移轉檢查您的環境未能正常運作，[瞭解](site-recovery-failover.md)不同的類型錯誤後移轉。
