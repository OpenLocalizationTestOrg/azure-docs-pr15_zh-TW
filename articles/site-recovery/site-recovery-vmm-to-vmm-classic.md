<properties
    pageTitle="在第二個 VMM 網站複製 HYPER-V 虛擬機器中 VMM 雲朵 |Microsoft Azure"
    description="本文將說明如何 HYPER-V Vm VMM 雲朵中複製至第二個 VMM 網站 Azure 網站復原。"
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

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site"></a>複製 HYPER-V 虛擬機器中 VMM 雲朵次要 VMM 網站

> [AZURE.SELECTOR]
- [Azure 入口網站](site-recovery-vmm-to-vmm.md)
- [傳統的入口網站](site-recovery-vmm-to-vmm-classic.md)
- [PowerShell-資源管理員](site-recovery-vmm-to-vmm-powershell-resource-manager.md)

Azure 網站復原服務佔業務連續性和損壞復原 (BCDR) 策略來協調複寫、 錯誤移轉及復原的虛擬機器和實體伺服器。 Azure，或第二個內部部署資料中心，可將複寫機器。 概述讀[什麼是 Azure 網站復原？](site-recovery-overview.md)

## <a name="overview"></a>概觀

本文將說明如何複寫 HYPER-V 虛擬機器 HYPER-V 主機在伺服器上，在第二個 VMM 網站 Azure 網站復原 VMM 雲朵管理的檔案。

本文包含的必要條件，為您示範如何設定網站復原保存庫、 安裝 Azure 網站修復提供者上來源和目標 VMM 伺服器、 在保存庫註冊伺服器、 設定 VMM 雲朵保護設定，然後啟用 HYPER-V Vm 的保護。 藉由測試以確保一切運作正常容錯移轉完成。

在底端的本文中，或在[Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上張貼任何註解或問題。

## <a name="architecture"></a>架構

下圖顯示不同的通訊通道和 Azure 網站復原用來協調流程和複寫的連接埠

![E2E 拓撲](./media/site-recovery-vmm-to-vmm-classic/e2e-topology.png)

## <a name="before-you-start"></a>在您開始之前

請確定您有下列先決條件位置︰

**必要條件** | **詳細資料**
--- | ---
**Azure**| 您必須以[Microsoft Azure](https://azure.microsoft.com/)帳戶。 您可以開始使用[免費的試用版](https://azure.microsoft.com/pricing/free-trial/)。 [瞭解更多](https://azure.microsoft.com/pricing/details/site-recovery/)有關網站復原價格。
**VMM** | 您必須至少有一個 VMM 伺服器。<br/><br/>VMM 伺服器應該至少執行系統管理中心 2012 SP1 累計最新的更新。<br/><br/>如果您想要保護的單一 VMM 伺服器設定，必須至少為兩個雲朵伺服器上設定。<br/><br/>如果您想要部署與兩個 VMM 伺服器的保護，每個伺服器必須至少有一個雲端您想要保護，請在主要 VMM 伺服器上設定並設定您想要使用的保護與復原次要 VMM 伺服器上的一個雲端<br/><br/>所有 VMM 雲朵都必須設定 HYPER-V 功能設定檔。<br/><br/>您要保護的來源雲端必須包含一個或多個 VMM 主機群組。<br/><br/>進一步瞭解如何設定在 VMM 雲朵[逐步解說︰ 使用系統管理中心 2012 SP1 VMM 建立私人的雲朵](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx)Keith Mayer 部落格上。
**Hyper-v** | 您需要在主要和次要 VMM 主機群組的一或多個 HYPER-V 主機伺服器和每個 HYPER-V 主機伺服器上的一個或多個虛擬機器。<br/><br/>Host （主機） 和目標 HYPER-V 伺服器必須至少執行 HYPER-V 角色與 Windows Server 2012，安裝最新的更新。<br/><br/>包含您要保護的 Vm 任何 HYPER-V 伺服器必須位於 VMM 雲端。<br/><br/>如果您執行的叢集 HYPER-V，請注意如果您有靜態 IP 位址基礎叢集該叢集代理人不會自動建立。 您必須以手動方式設定叢集代理人。 劉 Finn 部落格文章中[進一步瞭解](https://www.petri.com/use-hyper-v-replica-broker-prepare-host-clusters)。
**網路對應** | 您可以設定網路對應，確保後移轉後，以最佳方式次要 HYPER-V host （主機） 伺服器上放置複寫的虛擬機器，他們可以連線到適當的 VM 網路。 如果您沒有設定網路對應，複本 Vm 不到任何網路連線後移轉後。<br/><br/>若要設定網路對應部署時，請確定來源 HYPER-V 主機伺服器上的虛擬機器連線到 VMM VM 網路。 該網路應該連結至雲端。 與相關聯邏輯網狀 < 巴西 /<br/>目標雲端您用來復原次要 VMM 伺服器上的應該對應 VM 網路設定，而且在應連結至對應的邏輯網路與目標雲端相關聯。<br/><br/>[瞭解更多](site-recovery-network-mapping.md)有關網路對應。
**儲存對應** | 依預設當您要複製到目標 HYPER-V 主機伺服器，虛擬機器來源 HYPER-V host （主機） 伺服器上複製的資料會儲存於所表示的目標 HYPER-V 主機 HYPER-V 管理員中的預設位置。 更進一步控制儲存複製的資料的詳細資訊，您可以設定儲存對應<br/><br/> 若要設定儲存對應，您需要設定儲存分類來源及目標 VMM 伺服器，部署之前。 [進一步瞭解](site-recovery-storage-mapping.md)。


## <a name="step-1-create-a-site-recovery-vault"></a>步驟 1︰ 建立網站復原保存庫

1. 從您要註冊 VMM 伺服器登入[管理入口網站](https://portal.azure.com)。

2. 展開**資料服務** > **修復服務**，按一下 [**網站復原保存庫**。

3. 按一下 [**建立新** > **快速建立**。

4. 在 [**名稱**] 中，輸入好記的名稱來識別保存庫。

5. 在**區域**中選取保存庫的地理區域。 若要檢查支援的區域，請參閱[Azure 網站復原定價詳細資料](http://go.microsoft.com/fwlink/?LinkId=389880)中的地理可用性。

6. 按一下 [**建立保存庫**]。

    ![建立保存庫](./media/site-recovery-vmm-to-vmm-classic/create-vault.png)

核取狀態列中，建立保存庫。 保存庫會列為**作用中**主要的 [復原服務] 頁面上。

## <a name="step-2-generate-a-vault-registration-key"></a>步驟 2︰ 產生保存庫登錄機碼

產生保存庫登錄機碼。 下載 Azure 網站修復提供者，並將其安裝於 VMM 伺服器之後，您會使用此按鍵在保存庫註冊 VMM 伺服器。

1. 在 [**復原服務**] 頁面上，按一下 [保存庫，以開啟 [快速入門] 頁面。 也可以隨時使用圖示開啟快速入門。

    ![快速入門] 圖示](./media/site-recovery-vmm-to-vmm-classic/quick-start-icon.png)

2. 在下拉式清單中，選取**兩個內部部署 VMM 網站之間**。
3. 在**準備 VMM 伺服器**，按一下 [**產生註冊金鑰檔案**。 索引鍵的檔案會自動產生且有效產生後的 5 天。 如果您不從 VMM 伺服器，必須先將此檔案複製到伺服器存取 Azure 入口網站。

    ![登錄機碼](./media/site-recovery-vmm-to-vmm-classic/register-key.png)

## <a name="step-3-install-the-azure-site-recovery-provider"></a>步驟 3︰ 安裝 Azure 網站修復提供者

4. 按一下 [**快速入門**] 頁面的 [在 [**準備 VMM 伺服器**] 的 [**下載 Microsoft Azure 網站修復提供者 VMM 伺服器上的安裝**，以取得最新版本的提供者安裝檔案]。

2. 執行此來源 VMM 伺服器上的檔案。

    >[AZURE.NOTE] 如果 VMM 配置叢集，且您要安裝的提供者，第一次就會將其安裝在作用中的節點，並完成安裝在保存庫註冊 VMM 伺服器。 再安裝在其他節點的提供者。 請注意，是否您要升級的提供者需要將所有節點升級，因為他們應該所有執行相同的提供者版本。

3. 安裝程式會停止 VMM 服務開始提供者設定的幾個**測試需求核取**及要求權限。 自動將重新啟動 VMM 服務，設定完成。 如果您要安裝在 VMM 叢集系統會提示您停止叢集角色。

4. 在 [ **Microsoft Update**您可以選擇加入集更新。 使用此設定啟用會根據您的 Microsoft Update 原則安裝更新的提供者。

    ![Microsoft Update](./media/site-recovery-vmm-to-vmm-classic/ms-update.png)

5. 若要設定的安裝位置**<SystemDrive>\Program Files\Microsoft 系統管理中心 2012 R2\Virtual 機器 Manager\bin**。 按一下 [安裝] 按鈕，即可開始安裝的提供者。

    ![InstallLocation](./media/site-recovery-vmm-to-vmm-classic/install-location.png)

6. 已安裝的提供者之後按一下 [註冊伺服器保存庫中的 [**註冊**]。

    ![InstallComplete](./media/site-recovery-vmm-to-vmm-classic/install-complete.png)
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

13.  按一下 [**下一步**完成程序。 註冊後，從 VMM 伺服器的中繼資料擷取是 Azure 網站復原。 伺服器會顯示在**VMM 伺服器** > 保存庫中的**伺服器**。

    ![伺服器](./media/site-recovery-vmm-to-vmm-classic/provider13.PNG)

### <a name="command-line-installation"></a>命令列安裝

Azure 網站修復提供者也可以從命令列安裝。 這個方法可以用於伺服器核心的 Windows Server 2012 R2 上安裝的提供者。

1. 下載資料夾的提供者安裝檔案及登錄機碼。 例如 C:\ASR。
2. 停止系統管理中心虛擬機器管理員服務
3. 擷取的提供者安裝程式具有**管理員**權限的命令提示字元中執行下列命令︰

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

4. 安裝提供者來執行︰

        C:\ASR> setupdr.exe /i

5. 註冊提供者來執行︰

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>     

參數位置如下︰

 - **/Credentials**︰ 強制參數會指定登錄鍵檔案所在的位置  
 - **/FriendlyName**︰ 強制參數會出現在 Azure 網站復原入口網站中的 HYPER-V 主機伺服器名稱。
 - **/EncryptionEnabled**︰ 您需要使用只能在 VMM Azure 案例中，如果您需要在靜止中 Azure 虛擬機器加密的選擇性參數。 請確定您提供副檔名為**.pfx**檔的名稱。
 - **/proxyAddress**︰ 選用的參數會指定 proxy 伺服器的位址。
 - **/proxyport**︰ 選用的參數會指定 proxy 伺服器的連接埠。
 - **/proxyUsername**︰ 選用的參數會指定 Proxy 使用者名稱 （如果 proxy 需要驗證）。
 - **/proxyPassword**︰ 選用的參數會指定驗證的 proxy 伺服器 （如果 proxy 需要驗證） 的密碼。  

## <a name="step-4-configure-cloud-protection-settings"></a>步驟 4︰ 設定雲端保護設定

登錄 VMM 伺服器之後，您可以設定雲端保護。 如果您安裝的提供者時，啟用**保存庫的同步處理雲端資料**的選項，因此所有雲朵 VMM 伺服器上會都出現在保存庫中的 [**受保護的項目**] 索引標籤中。 如果您沒有您可以與同步處理特定的雲端 Azure 網站復原 VMM 主控台中的 [雲端屬性] 頁面的 [**一般**] 索引標籤中。

![已發佈的雲端](./media/site-recovery-vmm-to-vmm-classic/clouds-list.png)

1. 在 [快速入門] 頁面上，按一下 [**設定 VMM 雲朵的保護**。
2. 在**VMM 雲朵**] 索引標籤中，選取您想要設定，然後移至 [**設定**] 索引標籤的雲端。
3. 在**目標**，選取 [ **VMM**]。
4. 在**目標位置**，選取 [現場 VMM 伺服器管理的雲端您想要使用的 [復原]。
4. 在**目標雲端**，選取您想要使用的來源雲端中的虛擬機器容錯移轉的目標雲端。 請注意︰

    - 我們建議您選取符合您要保護的虛擬機器復原需求目標雲端。
    - 雲端只能屬於單一雲端組，做為主要或目標雲端。

5. 在 [**複製頻率**] 指定頻率應該同步處理 5he 來源及目標位置之間的資料。 請注意，此設定只有相關時 HYPER-V 主機正在執行 Windows Server 2012 R2。 對於其他伺服器使用五分鐘的預設設定。
6. 在**其他復原點**，指定您是否要建立其他的復原點。預設零值會指出主要的虛擬機器只最新復原點儲存在複本 host （主機） 伺服器上。 請注意啟用多個復原點需要額外儲存空間儲存在每個復原的快照。 根據預設，會建立復原點每小時，使每個復原點包含資料的小時價值。 您指派為虛擬機器 VMM 主控台中檢視的復原點值不應該小於 Azure 網站復原主控台中檢視您指定的值。
7. 在**應用程式一致的快照的頻率**，指定要多久建立應用程式一致的快照。 HYPER-V 使用兩種類型的快照集-的標準的快照，可提供完整的虛擬機器的累加快照集和應用程式一致的快照的時間點資料的快照應用程式虛擬機器內。 應用程式一致的快照，以確保應用程式都一致時，會使用大量陰影複製服務 (VSS)。 請注意，是否您啟用應用程式一致的快照，它會影響來源虛擬機器上執行應用程式的效能。 請確定您設定的值小於您設定的其他修復點的數目。

    ![保護設定](./media/site-recovery-vmm-to-vmm-classic/cloud-settings.png)

8. 在**資料傳輸壓縮**，指定是否要壓縮的複寫的資料傳輸。
9. 在 [**驗證**]，指定流量驗證之間的主要及復原 HYPER-V 主機伺服器的方式。 除非您已使用 Kerberos 環境設定，請選取 [HTTPS]。 Azure 網站復原將會自動設定 HTTPS 驗證的憑證。 不不需要任何手動設定。 如果您選取 [Kerberos]，Kerberos 票證會用於主機伺服器相互驗證。 根據預設，會在 HYPER-V host （主機） 伺服器上的 [Windows 防火牆開啟連接埠 8083 和 8084 （適用於憑證）。 請注意，此設定只有相關的 Windows Server 2012 R2 上執行的 HYPER-V host （主機） 伺服器。
10. 在**連接埠**，修改的來源和目標主機電腦接聽複寫流量的連接埠號碼。 例如，您可能會修改設定，如果您想要套用品質服務 (QoS) 網路頻寬節流複寫流量。 核取的任何其他應用程式不使用連接埠和它已開啟的防火牆設定。
11. **複寫的方法**，指定的資料來源至目標位置初始複寫處理的方式，一般複寫開始之前︰

    - **在網路上**，將資料複製到網路就跟大量的資源。 我們建議您使用這個選項，如果雲端包含較小虛擬硬碟的虛擬機器，而且主要網站至次要網站連線到與寬頻寬的連線。 您可以指定複製應該立即啟動，或選取的時間。 如果您使用網路複寫，我們建議您將其在離峰進行排程。
    - **離線**，此方法可讓您指定將使用外部媒體播放會執行初始複寫。 如果您想要避免降低網路效能或遠端地理位置，非常有用。 若要使用這個方法中，您可以指定匯出位置來源雲端和目標雲端的匯入位置上。 當您啟用保護虛擬機器時，虛擬硬碟會複製到指定的匯出的位置。 您可以將其傳送至目標網站上，並將其複製到匯入的位置。 系統會將匯入的資訊複製到複本虛擬機器。

12. 選取 [**刪除複本虛擬機器**以指定應刪除複本虛擬機器，是否您停止保護虛擬機器選取 [**刪除的虛擬機器保護**選項虛擬機器] 索引標籤上的雲端屬性]。 啟用，此設定當您停用保護虛擬機器從中 Azure 網站復原，、 在 VMM 主控台中，移除虛擬機器網站復原設定，並且會刪除複本。

    ![保護設定](./media/site-recovery-vmm-to-vmm-classic/cloud-settings-replica.png)

您儲存設定後工作會建立，並且可以監視**工作**] 索引標籤。 VMM 來源雲端中的所有 HYPER-V 主機伺服器會都設定為複寫。 在 [**設定**] 索引標籤上，就可以修改雲端設定。 如果您想要修改目標位置或目標雲端必須移除雲端設定，並重新設定雲端。

### <a name="prepare-for-offline-initial-replication"></a>準備離線初始複寫

您必須執行下列動作，以準備離線初始複寫︰

- 在來源伺服器上，您會指定的資料匯出採取的路徑位置。 將 「 完全控制指派 VMM 服務匯出路徑 NTFS 和共用權限。 在目標伺服器上，您會指定要從中匯入資料就會發生的路徑位置。 指派此匯入路徑上相同的權限。
- 如果共用的匯入或匯出路徑，指派 VMM 服務帳戶的共用所在的遠端電腦上的管理員、 進階使用者，列印運算子或伺服器運算子群組成員資格。
- 如果您使用任何的執行方式帳戶主機，新增匯入及匯出路徑，指派讀取和寫入權限 VMM 執行為帳戶。
- 由於迴路設定不支援 HYPER-V 匯入及匯出共用應該不到用來做為 HYPER-V 主機伺服器，任何電腦上。
- 在 Active Directory 中每個 HYPER-V 上包含您想要保護、 啟用及設定的虛擬機器主機伺服器限制委派信任遠端電腦匯入及匯出路徑位於，，如下所示︰
    1. 在網域控制站，開啟 [ **Active Directory 使用者和電腦**。
    2. 按一下 [主控台樹狀目錄中的 [**網域名稱** > **電腦**。
    3. 以滑鼠右鍵按一下 HYPER-V 主機伺服器名稱 >**屬性**。
    4. 在 [**委派**] 索引標籤上按一下 T**rust 這部電腦委派指定的服務**。
    5. 按一下 [**使用任何驗證通訊協定**。
    6. 按一下 [**新增** > **使用者和電腦**。
    7. 輸入主控匯出路徑的電腦名稱 > **[確定]**。從可用的服務清單中，按住 CTRL 鍵並按一下**cifs** > **[確定]**。 對主控的匯入路徑的電腦的名稱。 重複為所需的額外 HYPER-V host （主機） 伺服器。

## <a name="step-5-configure-network-mapping"></a>步驟 5︰ 設定網路對應
1. 在 [快速入門] 頁面上，按一下 [**對應網路**。
2. 選取來源 VMM 伺服器要對應網路，然後按一下 [對應網路目標 VMM 伺服器。 來源網路和其關聯的目標網路的清單會顯示。 空白值會顯示目前未對應的網路。
3. 在**網路上來源**選取網路 > **地圖**。 服務偵測到目標伺服器上的 VM 網路，並顯示。 按一下 [若要檢視的每一個網路的子網路來源和目標的網路名稱旁邊的 [資訊] 圖示。

    ![設定網路對應](./media/site-recovery-vmm-to-vmm-classic/network-mapping1.png)

4. 在對話方塊中選取其中一個 VM 網路目標 VMM 伺服器。

    ![選取目標網路](./media/site-recovery-vmm-to-vmm-classic/network-mapping2.png)

5. 當您選取目標網路時，會顯示使用來源網路受保護的雲朵。 用來保護的雲朵與相關聯的可用的目標網路也會顯示。 我們建議您選取目標網路可供您使用的保護的所有雲朵。 或者，您也可以移至 VMM 伺服器並修改雲端內容新增至您要選擇 vm 網路對應的邏輯網路。
6. 按一下完成對應程序的核取記號。 工作會開始追蹤對應進度。 您可以檢視其**工作**] 索引標籤。


## <a name="step-6-configure-storage-mapping"></a>步驟 6︰ 設定儲存對應
依預設當您要複製到目標 HYPER-V 主機伺服器，虛擬機器來源 HYPER-V host （主機） 伺服器上複製的資料會儲存於所表示的目標 HYPER-V 主機 HYPER-V 管理員中的預設位置。 更進一步控制儲存複製的資料的詳細資訊，您可以設定儲存對應，如下所示︰



1. 在來源及目標 VMM 伺服器上定義儲存分類。 [進一步瞭解](https://technet.microsoft.com/library/gg610685.aspx)。 分類必須在來源及目標雲朵 HYPER-V host （主機） 伺服器。 分類不需要擁有相同的儲存空間。 例如，您可以對應包含 SMB 共用可以包含 CSVs 目標分類來源分類。
2. 分類準備就緒後，您可以建立對應。 若要這樣做，請在 [**快速入門**] 頁面上 >**對應儲存空間**。
3. 按一下 [**儲存**] 索引標籤 >**對應儲存分類**。
4. 在 [**對應儲存分類**] 索引標籤上選取分類來源及目標 VMM 伺服器。 儲存您的設定。

    ![選取目標網路](./media/site-recovery-vmm-to-vmm-classic/storage-mapping.png)


## <a name="step-7-enable-virtual-machine-protection"></a>步驟 7︰ 啟用虛擬機器保護
伺服器、 雲朵和網路設定正確之後，您可以啟用虛擬機器雲端中的保護。

1. 在虛擬機器位於的雲端的**虛擬機器**] 索引標籤上，按一下 [**啟用保護** > **新增虛擬機器**。
2. 從雲端中的虛擬機器清單中，選取您要保護的項目。

    ![啟用虛擬機器保護](./media/site-recovery-vmm-to-vmm-classic/enable-protection.png)

3. 追蹤進度的**工作**] 索引標籤，包括初始複寫中的 [啟用保護] 動作。 完成保護工作執行之後就有一個虛擬機器可供容錯移轉。 啟用保護時，且複寫虛擬機器之後，您就可以在 Azure 中檢視。

    ![虛擬機器保護的工作](./media/site-recovery-vmm-to-vmm-classic/vm-jobs.png)

>[AZURE.NOTE] 您也可以啟用虛擬機器 VMM 主控台中的保護。 按一下 [在虛擬機器屬性中的 [ **Azure 網站復原**] 索引標籤的工具列上的 [**啟用保護**]。

### <a name="on-board-existing-virtual-machines"></a>板現有的虛擬機器

如果您有現有的虛擬機器中 VMM 複寫 HYPER-V 複本會需要至內建 Azure 網站復原保護，如下所示︰

1. 確認您有主要和次要雲朵。 確定 [管理現有的虛擬機器的 HYPER-V 伺服器位於主要雲端 HYPER-V 伺服器複本虛擬機器位於次要雲端]。 請確定您已設定雲朵保護設定。 設定應符合目前設定 HYPER-V 複本。 否則虛擬機器複寫可能無法如預期般運作。
2. 然後啟用主要的虛擬機器中的保護。 Azure 網站復原和 VMM 可確保偵測到相同的複本主機和虛擬機器，和 Azure 網站修復會重複使用及重新建立複寫使用雲端設定時設定的設定。


## <a name="test-your-deployment"></a>測試您的部署

若要測試您的部署，您可以執行測試容錯移轉單一虛擬機器，或建立包含多個虛擬機器復原計劃，並執行測試容錯移轉計劃。  測試容錯移轉模擬您隔離的網路中的錯誤移轉及復原機制。

### <a name="create-a-recovery-plan"></a>建立一個修復計劃

1. 在 [**復原方案**] 索引標籤上按一下 [**建立復原規劃**。
2. 指定的復原計劃及來源和目標 VMM 伺服器的名稱。 來源伺服器必須啟用錯誤移轉及復原的虛擬機器。 選取 [檢視設定 HYPER-V 複寫的雲朵**HYPER-V** ]。

    ![建立復原計劃](./media/site-recovery-vmm-to-vmm-classic/recovery-plan1.png)

3. 在 [**選取虛擬機器**中，選取 [複寫群組]。 將選取的複寫群組相關聯的所有虛擬機器，並將其新增至復原計劃。 這些虛擬機器會新增至復原計劃預設群組，群組 1。 如有必要，您可以新增更多的群組。 請注意，在複寫虛擬機器會根據復原計劃群組的順序的啟動。

    ![加入虛擬機器](./media/site-recovery-vmm-to-vmm-classic/recovery-plan2.png)

復原之後計劃一建立之後，它會出現在 [**復原方案**] 索引標籤上的清單。

###<a name="run-a-test-failover"></a>執行測試容錯移轉

1. 在 [**復原方案**] 索引標籤上選取方案然後按一下 [**測試容錯移轉**。
2. 在**確認測試容錯移轉**頁面上，選取 [**無**]。 請注意，使用此選項複本的虛擬機器上啟用失敗無法連線至網路。 這會測試虛擬機器如預期般失敗，但不會測試複寫網路環境。 看看如何[執行測試容錯移轉](site-recovery-failover.md#run-a-test-failover)如需有關如何使用不同的網路選項的詳細資訊。
3. 測試虛擬機器將會建立主複本虛擬機器存在於主機上。 它會新增至複本虛擬機器位於相同的雲端。

### <a name="run-a-recovery-plan"></a>執行復原計劃
複寫之後複本虛擬機器可能沒有主要的虛擬機器的 IP 位址相同的 IP 位址。 虛擬機器時都會更新他們使用 DNS 伺服器開始後。 您也可以新增指令碼來更新 DNS 伺服器，以確保及時的更新。

#### <a name="script-to-retrieve-the-ip-address"></a>若要擷取的 IP 位址的指令碼
執行此範例指令碼來擷取的 IP 位址。

        $vm = Get-SCVirtualMachine -Name <VM_NAME>
        $na = $vm[0].VirtualNetworkAdapters>
        $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
        $ip.address  

#### <a name="script-to-update-dns"></a>若要更新 DNS 的指令碼
執行此範例指令碼來更新 DNS，指定您使用的上一個範例指令碼所擷取的 IP 位址。

        string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord



## <a name="privacy-information-for-site-recovery"></a>網站復原隱私權資訊

本節提供 Microsoft Azure 網站復原服務 （「 服務 」） 的額外的隱私權的資訊。 若要檢視之 Microsoft Azure 服務的隱私權聲明，請參閱[Microsoft Azure 隱私權聲明](http://go.microsoft.com/fwlink/?LinkId=324899)

**功能︰ 註冊**

- **功能**︰ 使用服務註冊伺服器，以便可以保護虛擬機器
- **收集的資訊**︰ 註冊這項服務會收集之後，處理，並管理憑證從傳輸資訊 VMM 伺服器指定提供損毀修復 VMM 伺服器上使用服務的 VMM 伺服器名稱，並虛擬機器雲朵的名稱。
- **資訊的用途**︰
    - 管理憑證，這用來協助識別與驗證註冊的 VMM 伺服器服務的存取。 服務使用的憑證的公開金鑰部分安全註冊的 VMM 伺服器可以存取的權杖。 伺服器需要使用此 token 加存取服務的功能。
    - VMM 伺服器的名稱，VMM 的伺服器名稱是必要找出並與雲朵位於適當 VMM 伺服器進行通訊。
    - 雲端 VMM 伺服器名稱，雲端名稱時，需要使用服務雲端配對/unpairing 功能如下所述。 當您決定要配對復原資料中心裡的另一個雲端您雲端從主要的資料中心時，會出現從復原資料中心的所有雲朵的名稱。

- **選擇**︰ 這項資訊會服務註冊程序中不可或缺的一部分，因為其可協助您和服務找出您要為識別正確登錄且的 VMM 伺服器提供 Azure 網站復原保護，以及 VMM 伺服器。 如果您不想要傳送此資訊服務，不會使用這項服務。 如果您註冊您的伺服器，然後稍後想要移除其註冊時，您可以執行刪除 VMM 伺服器資訊，從服務入口網站 （這是 Azure 入口網站）。

**功能︰ 啟用 Azure 網站復原保護**

- **功能**︰ Azure 網站復原提供者 VMM 伺服器上安裝是導管與服務進行通訊。 提供者是裝載於 VMM 程序的動態連結程式庫 (DLL)。 提供者安裝之後，「 資料中心復原 」 功能會啟用 VMM 管理主控台] 中。 任何新的或現有的虛擬機器中的雲端可以啟用稱為 「 資料中心復原] 來協助保護虛擬機器的屬性。 一旦設定此屬性，提供者，則會傳送的名字和 ID 虛擬機器的服務。 Windows Server 2012 或 Windows Server 2012 R2 HYPER-V 複寫技術為啟用虛擬保護。 虛擬機器資料會複製到另一個 （通常位於不同的 「 復原 」 資料中心） HYPER-V 主機。

- **收集的資訊**︰ 服務會收集、 處理程序和傳輸虛擬機器，其中包含名稱、 識別碼、 虛擬網路和雲端名稱的中繼資料它所屬。

- **資訊的用途**︰ 服務使用上述資訊填入您的服務入口網站上的虛擬機器資訊。

- **選項**︰ 此服務不可或缺的一部分，而不關閉。 如果您不希望此服務傳送的資訊，不讓任何虛擬機器 Azure 網站復原保護。 請注意，透過 HTTPS 傳送服務提供者所傳送的所有資料。

**功能︰ 修復計劃**

- **功能**︰ 此功能可協助您建立的 「 復原 」 資料中心的協調流程方案。 您可以定義應該復原網站啟動虛擬機器或群組的虛擬機器的順序。 您也可以指定所要執行，或任何手動所要採取，在每個虛擬機器復原時的動作的自動化指令碼。 復原計劃一致的復原層級通常觸發容錯移轉 （下一節所述）。

- **收集的資訊**︰ 服務會收集、 處理程序和傳輸復原計劃，包括虛擬機器中繼資料及中繼資料的任何自動化指令碼和手動動作備忘稿的中繼資料。

- **資訊的用途**︰ 上述的中繼資料用來建立您的服務入口網站中復原計劃。

- **選項**︰ 此服務不可或缺的一部分，而不關閉。 如果您不希望此服務傳送的資訊，沒有建立復原方案在此服務。

**功能︰ 網路對應**

- **功能**︰ 此功能可讓您從主要的資料中心復原資料中心的網路資訊對應。 當虛擬機器復原復原網站上時，此對應可以協助建立網路連線。

- **收集的資訊**︰ 網路對應功能的一部分，服務會收集、 處理程序和傳輸邏輯網路的每個網站 （「 主要 」 和 「 資料中心 」） 的中繼資料。

- **資訊的用途**︰ 服務使用中繼資料填入您的服務入口網站，您可以將對應的網路資訊。

- **選項**︰ 此服務不可或缺的一部分，而不關閉。 如果您不希望此服務傳送的資訊，不要使用網路對應功能。

**功能︰ 容錯移轉-計劃的計畫測試**

- **功能**︰ 這項功能有助於從一個 VMM 受管理的資料中心的虛擬機器容錯移轉到另一個 VMM 受管理的資料中心。 容錯移轉動作觸發其服務入口網站上的使用者。 容錯移轉的可能原因包括意外的事件 (例如若是自然 disaster0; 計劃的事件 （例如資料中心負載平衡）; 測試容錯移轉 （例如復原計劃排練）。

VMM 伺服器上的提供者會收到該服務中的事件，並透過 VMM 介面 HYPER-V 主機上執行的容錯移轉動作。 實際的容錯移轉到另一個 （通常以不同的 「 復原 」 資料中心執行） HYPER-V 主機的虛擬機器會處理的 Windows Server 2012 或 Windows Server 2012 R2 HYPER-V 複寫技術。 容錯移轉完成後，「 復原 」 資料中心的 VMM 伺服器上安裝的提供者成功將資訊傳送到服務。

- **收集的資訊**︰ 服務使用上述資訊填入您的服務入口網站上的容錯移轉動作資訊的狀態。

- **資訊的用途**: 「 服務使用上述資訊，如下所示︰

    - 管理憑證，這用來協助識別與驗證註冊的 VMM 伺服器服務的存取。 服務使用的憑證的公開金鑰部分安全註冊的 VMM 伺服器可以存取的權杖。 伺服器需要使用此 token 加存取服務的功能。
    - VMM 伺服器的名稱，VMM 的伺服器名稱是必要找出並與雲朵位於適當 VMM 伺服器進行通訊。
    - 雲端 VMM 伺服器名稱，雲端名稱時，需要使用服務雲端配對/unpairing 功能如下所述。 當您決定要配對復原資料中心裡的另一個雲端您雲端從主要的資料中心時，會出現從復原資料中心的所有雲朵的名稱。

- **選項**︰ 此服務不可或缺的一部分，而不關閉。 如果您不希望此服務傳送的資訊，不要使用這項服務。

## <a name="next-steps"></a>後續步驟

之後，您已執行測試容錯移轉檢查您的環境未能正常運作，[瞭解](site-recovery-failover.md)不同的類型錯誤後移轉。
