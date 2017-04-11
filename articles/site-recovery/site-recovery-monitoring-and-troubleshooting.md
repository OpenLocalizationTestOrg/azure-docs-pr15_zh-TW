<properties
    pageTitle="監控和疑難排解虛擬機器及實體伺服器保護 |Microsoft Auzre" 
    description="Azure 網站復原協調複寫、 錯誤移轉及復原的虛擬機器位於 Azure 或次要資料中心的內部部署伺服器上。 監控和疑難排解保護 VMM 或 HYPER-V 網站使用這份文件。" 
    services="site-recovery" 
    documentationCenter="" 
    authors="anbacker" 
    manager="mkjain" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery" 
    ms.date="10/13/2016"    
    ms.author="rajanaki"/>
    
# <a name="monitor-and-troubleshoot-protection-for-virtual-machines-and-physical-servers"></a>監控和疑難排解虛擬機器及實體伺服器的保護

此監控和疑難排解指南，可讓您瞭解追蹤複寫健康狀況與疑難排解 Azure 網站復原技巧。

## <a name="understanding-the-components"></a>了解元件

### <a name="vmwarephysical-site-deployment-for-replication-between-on-premises-and-azure"></a>內部部署和 Azure 之間複寫 VMware/實體網站部署。
若要設定 DR 之間內部部署 VMware/實體機器。設定伺服器、 母片目標及程序伺服器需要設定。 同時來源伺服器的保護 Azure 網站復原將會安裝行動服務。 張貼內部部署資料來源伺服器失敗移轉至 Azure 客戶的需求，以設定程序中的伺服器 Azure 和主要目標後 server 內部部署保護回到重建內部部署來源伺服器。 

![VMware/實體網站部署內部部署與 Azure 之間的複寫](media/site-recovery-monitoring-and-troubleshooting/image18.png)

### <a name="vmm-site-deployment-for-replication-between-on-premises-site"></a>內部部署網站之間的複寫 VMM 網站的部署。

屬於 DR 設定之間兩個內部部署的位置。Azure 網站修復提供者必須下載並安裝 VMM 伺服器上。 提供者需要網際網路連線，以確保觸發從 Azure 入口網站的所有作業取得都翻譯至內部部署作業等啟用保護]，關閉主虛擬機器容錯移轉等的一部分。

![內部部署網站之間的複寫 VMM 網站的部署](media/site-recovery-monitoring-and-troubleshooting/image1.png)

### <a name="vmm-site-deployment-for-replication-between-on-premises--azure"></a>內部部署與 Azure 之間的複寫 VMM 網站部署。

設定 DR 內部部署與 Azure; 之間的一部分Azure 網站修復提供者必須下載並安裝在 VMM 伺服器，以及 Azure 復原服務代理程式的需要每個 HYPER-V 主機上安裝。 如需詳細資訊，請參閱[Azure 保護瞭解網站](./site-recovery-understanding-site-to-azure-protection.md)。

![內部部署與 Azure 之間的複寫 VMM 網站的部署](media/site-recovery-monitoring-and-troubleshooting/image2.png)

### <a name="hyper-v-site-deployment-for-replication-between-on-premises--azure"></a>內部部署與 Azure 之間的複寫 HYPER-V 網站的部署

這是同 VMM 部署 – 本身 HYPER-V 主機上安裝的提供者與代理程式的差異。 如需詳細資訊，請參閱[Azure 保護瞭解網站](./site-recovery-understanding-site-to-azure-protection.md)。

## <a name="monitor-configuration-protection-and-recovery-operations"></a>監視設定、 保護及修復作業

每一項作業 ASR 中的取得稽核和追蹤 「 工作 」 索引標籤下方。 若任何的設定、 保護或復原錯誤瀏覽至 [工作] 索引標籤，並查看是否有任何失敗。

![監視設定、 保護及修復作業](media/site-recovery-monitoring-and-troubleshooting/image3.png)

當您找到失敗的工作檢視] 底下時，選取 [工作]，然後按一下該工作的錯誤詳細資料。

![監視設定、 保護及修復作業](media/site-recovery-monitoring-and-troubleshooting/image4.png)

錯誤詳細資料可協助您識別可能的原因及問題的建議。

![監視設定、 保護及修復作業](media/site-recovery-monitoring-and-troubleshooting/image5.png)

上例中那里似乎是這是因為其失敗保護設定進行中的另一個作業。 請確定您解決的問題，依照建議 – 該處之後按一下 RESART 重新進行的作業。

![監視設定、 保護及修復作業](media/site-recovery-monitoring-and-troubleshooting/image6.png)

重新啟動選項不適用於所有作業 – 那些沒有重新啟動選項，瀏覽回物件，然後再一次取消復原作業。 每項工作，可以在任何點的同時進行中使用 [取消] 按鈕取消。

![監視設定、 保護及修復作業](media/site-recovery-monitoring-and-troubleshooting/image7.png)

## <a name="monitor-replication-health-for-virtual-machine"></a>監控虛擬機器複寫健全狀況

中央與遠端監視透過 Azure 入口網站的每個受保護的實體 ASR 提供者。 選取 [VMM 雲朵] 或 [保護群組，請瀏覽該處之後受保護的項目。 VMM 雲朵] 索引標籤僅適用於 VMM 基礎部署，而其他所有案例都有受保護的項目，保護群組] 索引標籤下。

![監控複寫狀況的虛擬機器](media/site-recovery-monitoring-and-troubleshooting/image8.png)

該處後選取 [在個別的雲端或 [保護] 群組底下受保護的實體。 一旦您選取受保護的實體所有允許作業會顯示在下方窗格中。

![監控複寫狀況的虛擬機器](media/site-recovery-monitoring-and-troubleshooting/image9.png)

如上所示的大小寫虛擬機器狀況很重要 – 您可以按一下 [錯誤詳細資料] 按鈕，以查看錯誤底部。 根據 「 可能的原因 」 和 「 建議 「 提及解決問題。

![監控複寫狀況的虛擬機器](media/site-recovery-monitoring-and-troubleshooting/image10.png)

![監控複寫狀況的虛擬機器](media/site-recovery-monitoring-and-troubleshooting/image11.png)

注意︰ 如果有任何作用中的作業進行中或失敗然後瀏覽至 [工作] 檢視如先前所述，若要檢視工作的特定錯誤。

## <a name="troubleshoot-on-premises-hyper-v-issues"></a>內部部署 HYPER-V 問題進行疑難排解

連線到內部部署 HYPER-V 管理員主控台、 選取的虛擬機器以及查看複寫健康狀況。

![內部部署 HYPER-V 問題進行疑難排解](media/site-recovery-monitoring-and-troubleshooting/image12.png)

在此情況下*複寫狀況*被指定為要徑 – 查看詳細資料*檢視複寫健康狀況*。

![內部部署 HYPER-V 問題進行疑難排解](media/site-recovery-monitoring-and-troubleshooting/image13.png)

位置複寫已暫停虛擬機器的情況下，請以滑鼠右鍵按一下選取的*複寫*->*繼續複寫*
![疑難排解內部部署 HYPER-V 問題](media/site-recovery-monitoring-and-troubleshooting/image19.png)

虛擬機器移轉新 HYPER-V 主機 （在叢集或單獨電腦），其中已透過 ASR，以防複寫虛擬機器就不會受到影響。 請確定新 HYPER-V 主機符合所有每個-必要條件，並使用 ASR 設定。

### <a name="event-log"></a>事件記錄檔

| 事件來源                | 詳細資料                                                                                                                                                                                           |
|-------------------------  |:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------    |
| **應用程式與服務記錄檔/Microsoft/VirtualMachineManager/伺服器/管理員**（VMM 伺服器）   |  這會提供有用的許多不同的 VMM 問題的疑難排解的記錄。 |
| **應用程式與服務記錄檔/MicrosoftAzureRecoveryServices/複寫**（HYPER-V 主機）   | 這會提供有用的多個 Microsoft Azure 修復服務代理問題的疑難排解的記錄。 <br/> ![事件來源 HYPER-V 主機](media/site-recovery-monitoring-and-troubleshooting/eventviewer03.png) |
| **應用程式及服務記錄檔/Microsoft Azure 網站復原/提供者/操作**（HYPER-V 主機）   | 這會提供有用的多個 Microsoft Azure 網站復原服務問題的疑難排解的記錄。 <br/> ![事件來源 HYPER-V 主機](media/site-recovery-monitoring-and-troubleshooting/eventviewer02.png) |
| **應用程式與服務記錄檔/Microsoft/Windows/超-V-VMM/管理員**（HYPER-V 主機） | 這會提供有用的許多 HYPER-V 虛擬機器管理問題的疑難排解的記錄。 <br/> ![事件來源 HYPER-V 主機](media/site-recovery-monitoring-and-troubleshooting/eventviewer01.png) |


### <a name="hyper-v-replication-logging-options"></a>HYPER-V 複寫記錄選項

HYPER-V 複本的相關的所有事件都登入超 V VMM\\管理員記錄位於**應用程式及服務記錄檔\\Microsoft\\Windows**。 此外，但 vmm V 超還是可以啟用分析的記錄。 若要啟用此記錄，請先分析與偵錯記錄在 [事件檢視器中檢視。 開啟事件檢視器]，然後在**檢視] 功能表**中，按一下 [**顯示分析與偵錯記錄**。

![內部部署 HYPER-V 問題進行疑難排解](media/site-recovery-monitoring-and-troubleshooting/image14.png)

分析的記錄會下但 vmm V 超還是顯示

![內部部署 HYPER-V 問題進行疑難排解](media/site-recovery-monitoring-and-troubleshooting/image15.png)

在 [**動作**] 窗格中，按一下 [**啟用記錄**。 一旦啟用，它會出現在**效能監視器**時位於 [事件追蹤工作階段**收集的資料集。**

![內部部署 HYPER-V 問題進行疑難排解](media/site-recovery-monitoring-and-troubleshooting/image16.png)

若要檢視收集的資訊，請先，停用登入，請停止追蹤工作階段，然後儲存記錄檔和事件檢視器中重新開啟或將其轉換為所要使用其他工具。



## <a name="reaching-out-for-microsoft-support"></a>不讓其與 Microsoft 支援

### <a name="log-collection"></a>記錄檔集合

如需 VMM 網站保護，請參閱[ASR 記錄集合使用支援診斷平台 (SDP) 工具](http://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx)收集必要的記錄。

HYPER-V 網站保護的下載[工具](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab)，並以收集記錄 HYPER-V 主機上執行。

VMware/實體情況下，請參閱[Azure 網站復原記錄集合 VMware 及實體網站保護](http://social.technet.microsoft.com/wiki/contents/articles/30677.azure-site-recovery-log-collection-for-vmware-and-physical-site-protection.aspx)收集必要的記錄。

工具會收集本機下隨機命名子資料夾下**%LocalAppData%\ElevatedDiagnostics**記錄

![範例顯示 HYPER-V 網站保護的步驟。](media/site-recovery-monitoring-and-troubleshooting/animate01.gif)

### <a name="opening-a-support-ticket"></a>開啟支援票證

若要提高支援票證 asr，連絡 Azure 支援使用<http://aka.ms/getazuresupport> URL

## <a name="kb-articles"></a>知識庫文件

-   [若要保留受保護的移轉或移轉到 Azure 虛擬機器的磁碟機字母的方式](http://support.microsoft.com/kb/3031135)
-   [如何管理內部部署至 Azure 保護的網路頻寬使用量](https://support.microsoft.com/kb/3056159)
-   [當您嘗試啟動虛擬機器保護 ASR: 「 無法找到叢集資源 」 錯誤](http://support.microsoft.com/kb/3010979)
-   [了解與疑難排解 HYPER-V 複本指南](http://www.microsoft.com/en-in/download/details.aspx?id=29016) 

## <a name="common-asr-errors-and-their-resolutions"></a>常見的 ASR 錯誤和其解決方案

以下是您可以按下的常見錯誤和其解決方案。 每個錯誤會在另一個 WIKI 頁面中說明。

### <a name="general"></a>一般
-   <span style="color:green;">新增</span>[工作失敗，錯誤 」 作業正在進行中]。錯誤 505，514，532](http://social.technet.microsoft.com/wiki/contents/articles/32190.azure-site-recovery-jobs-failing-with-error-an-operation-is-in-progress-error-505-514-532.aspx)
-   <span style="color:green;">新增</span>[工作失敗，錯誤 」 伺服器未連線到網際網路 」。錯誤 25018](http://social.technet.microsoft.com/wiki/contents/articles/32192.azure-site-recovery-jobs-failing-with-error-server-isn-t-connected-to-the-internet-error-25018.aspx)

### <a name="setup"></a>設定
-   [無法註冊 VMM 伺服器發生內部錯誤。請參閱在網站復原入口網站，如需詳細資訊工作檢視錯誤。執行安裝程式以註冊伺服器。](http://social.technet.microsoft.com/wiki/contents/articles/25570.the-vmm-server-cannot-be-registered-due-to-an-internal-error-please-refer-to-the-jobs-view-in-the-site-recovery-portal-for-more-details-on-the-error-run-setup-again-to-register-the-server.aspx)
-   [無法連線 HYPER-V 復原管理員保存庫。驗證的 proxy 設定，或稍後再試。](http://social.technet.microsoft.com/wiki/contents/articles/25571.a-connection-cant-be-established-to-the-hyper-v-recovery-manager-vault-verify-the-proxy-settings-or-try-again-later.aspx)

### <a name="configuration"></a>設定
-   [無法建立保護] 群組︰ 擷取伺服器的清單時，發生錯誤。](http://blogs.technet.com/b/somaning/archive/2015/08/12/unable-to-create-the-protection-group-in-azure-site-recovery-portal.aspx)
-   [HYPER-V 主機叢集包含至少一個靜態網路介面卡，或者沒有連線的介面卡會設定為使用 DHCP。](http://social.technet.microsoft.com/wiki/contents/articles/25498.hyper-v-host-cluster-contains-at-least-one-static-network-adapter-or-no-connected-adapters-are-configured-to-use-dhcp.aspx)
-   [VMM 沒有權限，才能完成動作](http://social.technet.microsoft.com/wiki/contents/articles/31110.vmm-does-not-have-permissions-to-complete-an-action.aspx)
-   [無法選取時設定保護訂閱中的儲存空間帳戶](http://social.technet.microsoft.com/wiki/contents/articles/32027.can-t-select-the-storage-account-within-the-subscription-while-configuring-protection.aspx)

### <a name="protection"></a>保護
- <span style="color:green;">新增</span>使用錯誤 」 保護無法設定虛擬機器 」[啟用保護失敗。錯誤 60007，40003](http://social.technet.microsoft.com/wiki/contents/articles/32194.azure-site-recovery-enable-protection-failing-with-error-protection-couldn-t-be-configured-for-the-virtual-machine-error-60007-40003.aspx)
- <span style="color:green;">新增</span>使用錯誤 」 保護無法啟用虛擬機器。 「[啟用保護失敗錯誤 70094](http://social.technet.microsoft.com/wiki/contents/articles/32195.azure-site-recovery-enable-protection-failing-with-error-protection-couldn-t-be-enabled-for-the-virtual-machine-error-70094.aspx)
- <span style="color:green;">新增</span>使用類型 Live 移動即將[即時移轉錯誤 23848-虛擬機器。可能會中斷的虛擬機器復原保護狀態。](http://social.technet.microsoft.com/wiki/contents/articles/32021.live-migration-error-23848-the-virtual-machine-is-going-to-be-moved-using-type-live-this-could-break-the-recovery-protection-status-of-the-virtual-machine.aspx) 
- [啟用失敗，因為主機電腦上未安裝代理程式的保護](http://social.technet.microsoft.com/wiki/contents/articles/31105.enable-protection-failed-since-agent-not-installed-on-host-machine.aspx)
- [找不到適合主機複本虛擬機器-因為低計算資源](http://social.technet.microsoft.com/wiki/contents/articles/25501.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-low-compute-resources.aspx)
- [適合主機複本虛擬機器找不到-因為沒有連接的邏輯網路](http://social.technet.microsoft.com/wiki/contents/articles/25502.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-no-logical-network-attached.aspx)
- [無法連線至複本主機機器-無法連線](http://social.technet.microsoft.com/wiki/contents/articles/31106.cannot-connect-to-the-replica-host-machine-connection-could-not-be-established.aspx)


### <a name="recovery"></a>復原
- VMM 無法完成主機作業-
    -   [無法透過虛擬機器選取的復原點︰ 一般拒絕存取錯誤。](http://social.technet.microsoft.com/wiki/contents/articles/25504.fail-over-to-the-selected-recovery-point-for-virtual-machine-general-access-denied-error.aspx)
    -   [HYPER-V 無法無法透過虛擬機器選取的復原點︰ 作業中止嘗試較新的復原點。(0x80004004)](http://social.technet.microsoft.com/wiki/contents/articles/25503.hyper-v-failed-to-fail-over-to-the-selected-recovery-point-for-virtual-machine-operation-aborted-try-a-more-recent-recovery-point-0x80004004.aspx)
    -   無法建立 (0x00002EFD) 與伺服器的連線。
        -   [HYPER-V 無法啟用反向複寫虛擬機器](http://social.technet.microsoft.com/wiki/contents/articles/25505.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-reverse-replication-for-virtual-machine.aspx)
        -   [HYPER-V 無法啟用複寫虛擬機器虛擬機器](http://social.technet.microsoft.com/wiki/contents/articles/25506.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-replication-for-virtual-machine-virtual-machine.aspx)
    -   [無法認可容錯移轉虛擬機器](http://social.technet.microsoft.com/wiki/contents/articles/25508.could-not-commit-failover-for-virtual-machine.aspx)
-   [復原計劃包含不是準備好進行計劃的容錯移轉虛擬機器](http://social.technet.microsoft.com/wiki/contents/articles/25509.the-recovery-plan-contains-virtual-machines-which-are-not-ready-for-planned-failover.aspx)
-   [虛擬機器尚未準備好計劃的容錯移轉](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)
-   [虛擬機器並未執行，且不在關機](http://social.technet.microsoft.com/wiki/contents/articles/25510.virtual-machine-is-not-running-and-is-not-powered-off.aspx)
-   [辦公室頻作業發生在虛擬機器和認可容錯移轉失敗](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)
-   測試容錯移轉
    -   [無法起始容錯移轉，因為正在進行測試容錯移轉](http://social.technet.microsoft.com/wiki/contents/articles/31111.failover-could-not-be-initiated-since-test-failover-is-in-progress.aspx)
-   <span style="color:green;">新增</span> 容錯移轉逾時與 'PreFailoverWorkflow 任務 WaitForScriptExecutionTaskTimeout' 因為網路安全性群組相關聯的虛擬機器或所屬至電腦的子網路上的設定。 如需詳細資料，請參閱[「 PreFailoverWorkflow 工作 WaitForScriptExecutionTaskTimeout 」](https://aka.ms/troubleshoot-nsg-issue-azure-site-recovery) 。


### <a name="configuration-server-process-server-master-target"></a>設定伺服器、 程序伺服器主要目標
設定伺服器 (CS)，程序 (PS)，母片 Targer （細明體）
-   [要在其 PS/CS 存放為 VM ESXi 主機而失敗死亡紫色畫面。](http://social.technet.microsoft.com/wiki/contents/articles/31107.vmware-esxi-host-experiences-a-purple-screen-of-death.aspx)

### <a name="remote-desktop-troubleshooting-after-failover"></a>遠端桌面後移轉後的疑難排解
-   許多客戶面臨透過 Azure 中 VM 連線到失敗問題。 [將 VM 使用 RDP 疑難排解文件](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)

#### <a name="adding-a-public-ip-on-a-resource-manager-virtual-machine"></a>資源管理員虛擬機器中新增的公用 IP
如果入口網站中的 [**連線**] 按鈕呈現灰色，且您未連線到 Azure 透過快速傳送或網站-VPN 連線，您需要建立及指定您 VM 的公用 IP 位址，您才能使用 RDP/SSH。 請遵循下列步驟來新增公用 IP 虛擬機器的網路介面上。  

![新增網路介面上的公用 IP 無法透過虛擬機器](media/site-recovery-monitoring-and-troubleshooting/createpublicip.gif)