<properties
    pageTitle="複寫內部部署 VMware 虛擬機器或實體伺服器次要網站 |Microsoft Azure"
    description="使用這份文件至 Azure 網站復原次要網站複製 VMware Vm 或 Windows/Linux 實體伺服器。"
    services="site-recovery"
    documentationCenter=""
    authors="nsoneji"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="nisoneji"/>


# <a name="replicate-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>將內部部署 VMware 虛擬機器或實體伺服器複寫到次要網站


## <a name="overview"></a>概觀

Azure 網站修復 InMage 搜索提供內部部署 VMware 網站之間的即時複寫。 InMage 搜索會包含在 Azure 網站復原服務訂閱。


## <a name="prerequisites"></a>必要條件

**Azure 帳戶**︰ 您必須以[Microsoft Azure](https://azure.microsoft.com/)帳戶。 您可以開始使用[免費的試用版](https://azure.microsoft.com/pricing/free-trial/)。 [瞭解更多](https://azure.microsoft.com/pricing/details/site-recovery/)有關網站復原價格。


## <a name="step-1-create-a-vault"></a>步驟 1︰ 建立保存庫
1. [Azure 入口網站](https://portal.azure.com)登入。
2. 按一下 [新增] > 管理 > 備份及網站修復 (OMS)。 或者，您可以按一下 [瀏覽 > 復原服務保存庫 > 新增。
3. 在 [**名稱]**中，指定識別保存庫好記的名稱。 如果您有多個訂閱，請選取其中之一。
4. 在 [**資源] 群組**中建立新的資源群組，或選取現有。 指定要完成所需的欄位 Azure 區域。 
5.  在 [**位置**] 中，選取保存庫的地理區域。 若要查看支援的區域，請參閱[Azure 網站復原價格](https://azure.microsoft.com/pricing/details/site-recovery/)。
5. 如果您想要快速存取儀表板從保存庫按一下 [固定至儀表板，，然後按一下 [建立。
6. 新的保存庫會出現在儀表板上 > 所有資源，並在主要修復服務保存庫刀。

## <a name="step-2-configure-the-vault-and-download-inmage-scout-components"></a>步驟 2︰ 設定保存庫，並下載 InMage 搜索元件
7. 修復服務中保存庫刀選取您保存庫，然後按一下 [設定]。
8. 在 [**設定** > **快速入門**按一下**網站復原**> 步驟 1︰**準備基礎結構** > **保護目標**。
9. **保護目標**] 中選取復原網站，並選取 [是]，與 VMware vSphere Hypervisor。 然後按一下 [確定]。
10. 在**搜索設定**] 中，按一下 [下載至下載 InMage 搜索 8.0.1 GA 軟體和登錄機碼。 針對您所有的所需的元件的 [安裝] 檔案處於下載的.zip 檔案。


## <a name="step-3-install-component-updates"></a>步驟 3︰ 安裝元件更新

閱讀有關最新的[更新](#updates)。 您會以下列順序安裝在伺服器上的更新檔案︰

1. 如果有的話，接收伺服器
2. 設定伺服器
3. 程序伺服器
3. 主要目標伺服器
4. vContinuum 伺服器
5. 來源伺服器 （Windows 和 Linux 伺服器）

請安裝的更新，如下所示︰

1. 下載此[更新](https://aka.ms/asr-scout-update4).zip 檔案。 此.zip 檔案包含下列檔案︰

    - RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz
    - CX_Windows_8.0.4.0_GA_Update_4_8725865_14Sep16.exe
    - UA_Windows_8.0.4.0_GA_Update_4_9035261_27Sep16.exe
    - UA_RHEL6 64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
    - vCon_Windows_8.0.4.0_GA_Update_4_8921562_16Sep16.exe
    - RHEL5、 OL5、 OL6、 SUSE 10、 SUSE 11 UA update4 位元︰ UA_<Linux OS>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz 
    
2. 解壓縮.zip 檔案。<br>
3. **接收伺服器**︰ 複製**RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz**接收伺服器，然後將它解壓縮。 在 [解壓縮] 資料夾中，**執行/安裝**。<br>
4. **設定伺服器/程序伺服器**︰ 複製**CX_Windows_8.0.4.0_GA_Update_4_8725865_14Sep16.exe**設定及程序伺服器。 按兩下以執行。<br>
5. **在 Windows 版主要目標伺服器**︰ 若要更新的整合代理程式，請將**UA_Windows_8.0.4.0_GA_Update_4_9035261_27Sep16.exe**複製到主要目標伺服器。 按兩下以執行。 請注意，整合代理程式也適用於來源伺服器。 您應該安裝來源伺服器上也，在這份清單，稍後所述。<br>
7. **VContinuum 伺服器**︰ 複製**vCon_Windows_8.0.4.0_GA_Update_4_8921562_16Sep16.exe**到 vContinuum 伺服器。  請確定您已關閉 vContinuum 精靈。 按兩下以執行檔案。<br>
8. **Linux 主要目標伺服器**︰ 若要更新的整合代理程式，請複製**UA_RHEL6 64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz**到主要目標伺服器，擷取它。 在 [解壓縮] 資料夾中，**執行/安裝**。<br>
9. **在 Windows 版來源伺服器**︰ 若要更新的整合代理程式，請複製**UA_Windows_8.0.4.0_GA_Update_4_9035261_27Sep16.exe**來源伺服器。 按兩下以執行。<br>
10. **Linux 來源伺服器**︰ 若要更新的整合代理程式，請複製對應 UA 檔案版本 Linux 伺服器並擷取它。 在 [解壓縮] 資料夾中，**執行/安裝**。  範例︰ RHEL 6.7 64 位元伺服器] 中，複製**UA_RHEL6 64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz**至伺服器並擷取它。 在 [解壓縮] 資料夾中，**執行/安裝**。

## <a name="step-4-set-up-replication"></a>步驟 4︰ 設定複寫
1. 設定複寫之間來源和目標 VMware 網站。
2. 指導方針，使用產品中的下載 InMage 搜索文件。 或者，您可以存取文件如下︰

    - [版本資訊](https://aka.ms/asr-scout-release-notes)
    - [相容性矩陣圖](https://aka.ms/asr-scout-cm)
    - [使用者指南](https://aka.ms/asr-scout-user-guide)
    - [接收使用者指南](https://aka.ms/asr-scout-rx-user-guide)
    - [快速安裝指南](https://aka.ms/asr-scout-quick-install-guide)


## <a name="updates"></a>更新

### <a name="azure-site-recovery-scout-801-update-4"></a>Azure 網站復原搜索 8.0.1 更新 4
搜索更新 4 是積存更新。 有 update1 直到 update3 下列新錯誤修正和加強功能的所有修正。

**新支援的平台** 

- 已新增的 vCenter/vSphere 6.0 與 6.1 6.2 支援
- 已新增下列 Linux 作業系統的支援
    - 紅色的角色企業 Linux 7.0 與 7.1 7.2 (RHEL) 
    - CentOS 7.0 與 7.1 7.2
    - 紅色的角色企業 Linux (RHEL) 6.8
    - CentOS 6.8

>[AZURE.NOTE]
>
> RHEL CentOS 7 64 位元**InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release.tar.gz**使用底數搜索 GA 封裝**InMage_Scout_Standard_8.0.1 GA.zip**封裝。 [Step1](site-recovery-vmware-to-vmware.md#Step 1: Create a vault)所述，請從入口網站下載搜索 GA 套件。

**錯誤修正和增強功能** 

- 改良的關機處理下列 Linux Os 和複製防止不需要重新同步處理問題。
    - 紅色的角色企業 Linux (RHEL) 6.x
    - Oracle Linux (OL) 6.x
- 對於 Linux，完成僅至本機使用者現在限制整合代理程式安裝目錄中的權限存取的資料夾。
- 在 Windows 上問題逾時重度發行常見分散式的一致性書籤上載入分散式應用程式，例如 SQL 和共用點叢集。
- 新增的記錄的相關的修正 CX 基底安裝程式中。
- VMware vCLI 6.0 下載連結會新增至 Windows 母片目標基底安裝程式。
- 錯誤移轉及 DR 切入期間新增更多檢查和變更網路設定的記錄。
- 有時候保留資訊不會以 CX 報告。  
- 實體叢集，當來源大量縮小發生失敗大量 vContinuum 精靈重新調整大小的作業。
- 叢集保護發生錯誤 」 無法找到磁碟簽章 」 叢集磁碟時 PRDM 磁碟。
- cxps 傳輸伺服器損毀，因為範圍外的例外狀況。 
- 伺服器名稱及 IP 欄現在已可調整 vContinuum 精靈] 的推入 [安裝] 頁面的。
- 接收 API 增強功能
    - 提供五個最晚可用常見一致性點 （只保證標記）。
    - 受保護的所有裝置都提供容量和可用空間詳細資料。
    - 在來源伺服器上提供搜索驅動程式狀態。 
    
>[AZURE.NOTE] 
>
>- 現在**InMage_Scout_Standard_8.0.1_GA.zip**基底套件已更新 CX 基底安裝程式**InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe**及 Windows 母片目標基底安裝程式**InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**。 為所有新安裝使用新 CX 及 Windows 母片目標 GA 位元。
>- 更新 4 直接套用上 8.0.1 GA.
>- 它們套用系統上後，無法復原設定伺服器和接收更新。

### <a name="azure-site-recovery-scout-801-update-3"></a>Azure 網站復原搜索 8.0.1 更新 3
更新 3 包含下列錯誤修正和加強功能︰

- [設定伺服器] 和 [接收無法在他們的 proxy 背後 register 網站復原保存庫。
- 復原點目標 (RPO) 不符合需求的時數不是狀況報表中快速更新。
- 設定伺服器未同步處理與接收時 ESX 硬體詳細資料] 或 [網路詳細資料可以包含任何 utf-8 字元。
- Windows Server 2008 R2 網域控制站無法復原後開機。
- 離線同步處理無法如預期般運作。
- 後虛擬機器 (VM) 移轉後，複寫組刪除卡在 [CX UI 如很長的時間，而且使用者無法完成回復或繼續作業。
- 整體快照，以協助降低應用程式已經最佳化由一致性工作完成之作業的中斷連線 SQL 用戶端等。
- 一致性工具 (VACP.exe) 的效能已經過改良來減少記憶體使用量所需的 Windows 上建立快速查詢。
- 推入密碼大於 16 個字元時，安裝服務當機。
- vContinuum 不檢查，而認證變更時，新 vCenter 認證的提示。
- Linux，主要目標快取管理員 (cachemgr) 無法從程序伺服器，藉此複寫組節流下載檔案。
- 實體容錯移轉叢集 (MSCS) 磁碟順序不相同時上所有的節點，複寫未設定叢集磁碟區部分。
<br/>請注意，叢集需要 reprotected 利用此 [修正]。  
- SMTP 功能無法運作正常之後接收從搜索 7.1 升級為搜索 8.0.1。
- 已追蹤花了完成時間的復原作業的記錄中新增更多的統計資料。
- 已新增支援 Linux 作業系統來源伺服器上的︰
    - 紅色角色企業 Linux (RHEL) 6 更新 7
    - CentOS 6 更新 7
- CX 和接收 UI 可以現在會顯示組，它會進入點陣圖模式的通知。
- 在接收中已新增以下的安全性修正方式︰

**問題描述**|**實作程序**
---|---
透過參數竄改略過的授權|限制的存取不適用的使用者。
跨網站要求偽造|實作頁面權杖概念，會產生隨機的每一頁。 <br/>使用此，您會看到︰ <li> 有只能有單一登入執行個體相同的使用者。</li><li>頁面重新整理無法運作，它會重新導向至儀表板。</li>
惡意檔案上傳|限制特定的延伸至的檔案。 允許的副檔名是︰ 7z，aiff，asf，avi，bmp csv，文件、 docx、 fla、 flv、 gif、 gz、 gzip、 jpeg、 jpg、 記錄，mid、 mov、 mp3、 mp4、 mpc、 mpeg、 mpg，ods，odt、 pdf、 png、 ppt、 pptx、 pxd、 qt、 ram、 rar、 rm、 rmi、 rmvb、 rtf、 sdc、 sitd、 swf、 sxc、 sxw、 tar、 tgz、 tif、 tiff、 txt、 vsd、 wav、 wma、 wmv、 xls、 xlsx、 xml、 和 zip。
常設跨網站指令碼 | 新增輸入的驗證。


>[AZURE.NOTE]
>
>-  所有網站復原更新都是累計。 更新 3 有更新 1 和 2，更新的所有的修正。 更新 3 直接套用上 8.0.1 GA.
>-  它們套用系統上後，無法復原設定伺服器和接收更新。

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Azure 網站復原搜索 8.0.1 更新 2 （要更新 03 年 12 月 15）

修正在更新 2 包括︰

- **設定伺服器**︰ 無法使用 31 天免費測量功能如預期般時設定伺服器註冊網站修復問題的修正。
- **整合代理程式**︰ 修正問題，而導致上未安裝主要目標伺服器時從版本 8.0 至 8.0.1 升級更新更新 1 中。


### <a name="azure-site-recovery-scout-801-update-1"></a>Azure 網站復原搜索 8.0.1 更新 1

更新 1，包含下列錯誤修正和新的功能︰

- 每個伺服器執行個體的免費保護的 31 天。 這可讓您測試的功能，或設定的概念。
    - 在伺服器上，包括錯誤移轉及回復，所有作業都免費提供 31 天，從伺服器網站復原搜索第一次受保護的時間。
    - 開始建立編號 32nd 天，從每個受保護的伺服器會是標準的執行個體工資率支付薪資 Azure 網站復原保護客戶所擁有的網站。
    - 在任何時候受保護的伺服器目前支付的數目有的 Azure 網站復原保存庫的 [儀表板] 頁面上。
- 支援 vSphere 命令列介面 (vCLI) 新增 5.5 更新 2。
- 新增來源伺服器上 Linux 作業系統的支援︰
    - RHEL 6 更新 6
    - RHEL 5 更新 11
    - CentOS 6 更新 6
    - CentOS 5 更新 11
- 若要控制下列問題修正錯誤︰
    - 保存庫註冊失敗設定伺服器或接收伺服器。
    - 如預期般叢集的虛擬機器 reprotected 時恢復時，不會出現叢集磁碟區。
    - 主要目標伺服器裝載在內部部署生產虛擬機器不同的 ESXi 伺服器上時，就會失敗回復。
    - 升級到 8.0.1，會影響保護和作業時，會變更設定檔案權限。
    - 重新同步化閥值並不執行，其會造成不一致的複寫行為。
    - 設定伺服器介面，RPO 設定會無法正確顯示。 壓縮的資料值不正確的方式顯示的壓縮的值。
    -  移除作業並不會刪除如預期般在 vContinuum 精靈中，與複寫無法刪除設定伺服器介面。
    -  在 vContinuum 精靈中，磁碟時，自動未選取 [磁碟] 檢視中按一下期間保護 MSCS 虛擬機器的**詳細資料**。
    - 實體至虛擬 (P2V) 案例中，在所需的 HP 服務，例如 CIMnotify 和 CqMgHost，不移至虛擬機器修復手動。 結果其他啟動時。
    - 主要目標伺服器上有多個 26 磁碟時，就會失敗 Linux 虛擬機器保護。

## <a name="next-steps"></a>後續步驟

張貼[Azure 修復服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)有任何問題。
