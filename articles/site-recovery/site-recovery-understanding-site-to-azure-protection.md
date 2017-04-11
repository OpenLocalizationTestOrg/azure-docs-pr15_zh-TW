<properties
    pageTitle="Azure 網站復原 HYPER-V 複寫 |Microsoft Azure"
    description="若要瞭解技術的概念，協助您順利安裝、 設定及管理 Azure 網站復原使用這份文件。"
    services="site-recovery"
    documentationCenter=""
    authors="Rajani-Janaki-Ram"
    manager="mkjain"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="09/12/2016"
    ms.author="rajanaki"/>  


# <a name="hyper-v-replication-with-azure-site-recovery"></a>Azure 網站復原 HYPER-V 複寫

本文將說明技術的概念，可協助您成功設定且使用 Azure 網站復原管理 HYPER-V 網站或 Azure 保護系統管理中心的虛擬機器管理員 (VMM) 網站。

## <a name="setting-up-the-source-environment-for-replication-between-on-premises-and-azure"></a>設定內部部署和 Azure 之間複製來源環境

設定損毀修復內部部署和 Azure 之間的一部分，請務必上下載及安裝 Azure 網站修復提供者 VMM 伺服器。 每個 HYPER-V 主機上安裝 Azure 復原服務代理程式。

![內部部署和 Azure 之間複寫 VMM 網站部署](media/site-recovery-understanding-site-to-azure-protection/image00.png)

設定 HYPER-V 管理基礎結構中的來源環境就像設定 VMM 受管理的基礎結構中的來源環境。 唯一的差別本身 HYPER-V 主機上已安裝的提供者及代理程式。 VMM 環境 VMM 伺服器上已安裝的提供者，並安裝代理程式 （若 Azure 複寫） HYPER-V 主機上。

## <a name="workflows"></a>工作流程

### <a name="enable-protection"></a>啟用保護
您從 Azure 入口網站或內部部署保護虛擬機器之後，名為 [**啟用保護**網站復原工作開始。 您可以監視，在 [**工作**] 索引標籤下。

![工作清單中的 [啟用保護] 工作](media/site-recovery-understanding-site-to-azure-protection/image001.PNG)

[**啟用保護**的工作會檢查必要條件之前叫用[CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx)方法。 這個方法使用輸入期間保護設定，以建立複寫 Azure。

[**啟用保護**的工作從開始，初始複寫來自內部部署叫用[StartReplication](https://msdn.microsoft.com/library/hh850303.aspx)方法。 這個方法會傳送至 Azure 虛擬機器的虛擬磁碟。

![[啟用保護] 工作的詳細資料](media/site-recovery-understanding-site-to-azure-protection/IMAGE002.PNG)

### <a name="finalize-protection-on-the-virtual-machine"></a>完成虛擬機器上的保護
觸發初始複寫時，就會採取[HYPER-V VM 快照集](https://technet.microsoft.com/library/dd560637.aspx)。 虛擬硬碟是逐一處理，直到所有的磁碟上傳至 Azure。 通常會時，若要完成，根據磁碟大小和頻寬。 若要最佳化您的網路使用，請參閱[如何管理內部部署至 Azure 保護的網路頻寬使用量](https://support.microsoft.com/kb/3056159)。

初始複寫完成後，請**在虛擬機器上的 Finalize 保護**工作設定的網路和後複寫設定。 進行初始複寫時︰

- 追蹤磁碟的所有變更。 
- 額外儲存空間耗用的快照，並 HYPER-V 複本記錄 (HRL) 檔案。

初始複寫完成時，會刪除 HYPER-V VM 快照。 刪除產生初始複寫到上層磁碟之後合併的資料變更。

![在工作清單中的 「 完成程序的虛擬機器上的 [保護] 工作](media/site-recovery-understanding-site-to-azure-protection/image03.png)

### <a name="delta-replication"></a>Delta 複寫
HYPER-V 複本複寫追蹤器，也就是 HYPER-V 複本複寫引擎，追蹤變更虛擬硬碟為 HYPER-V 複本記錄 (*.hrl) 檔案的一部分。 HRL 檔案位於相同的相關聯的磁碟的目錄。

設定複寫的每個磁碟有相關聯的 HRL 檔案。 初始複寫完成後，此記錄會傳送至客戶的儲存空間的帳戶。 傳送至 Azure 記錄時，相同的目錄中的另一個記錄檔中追蹤的主要變更。

初始複寫或期間差異複寫中,，您可以監視 VM 複寫狀況在 VM] 檢視中，[針對虛擬機器監控複寫運作情況](./site-recovery-monitoring-and-troubleshooting.md#monitor-replication-health-for-virtual-machine)所述。  

### <a name="resynchronization"></a>重新同步處理
Delta 複寫失敗和完整的初始複寫是大幅降低網路頻寬或時間時重新同步處理已標示虛擬機器。 例如，當 HRL 檔案大小編碼 50%的磁碟總大小，虛擬機器已標示重新同步處理。 重新同步化最小化網路上傳送運算的來源和目標的虛擬機器磁碟的總和檢查並傳送只差異的資料的量。

重新同步化] 完成之後，標準 delta 複寫應該繼續。 如果發生網路中斷或另一個資料，您可以恢復重新同步處理。

根據預設，自動排程重新同步處理設定為外部工作時間發生。 如果虛擬機器需要重新同步處理以手動方式，請選取的虛擬機器從入口網站，然後按一下 [**重新同步處理**。

![手動重新同步處理](media/site-recovery-understanding-site-to-azure-protection/image04.png)

重新同步化使用固定區塊區塊演算法來源和目標檔案位置分成固定區塊。 產生的每個區塊總和檢查與比判斷哪一個區塊，從來源需要可套用至目標。

### <a name="retry-logic"></a>重試邏輯
沒有內建的重試邏輯複寫錯誤。 此邏輯可分為兩種類別︰

| 類別                  | 案例                                    |
|---------------------------|----------------------------------------------|
| 無法復原的錯誤     | 嘗試沒有重試]。 虛擬機器複寫狀態是 [**要徑**，，需要系統管理員操作。 範例包括︰ <ul><li>中斷的 VHD 鏈</li><li>無效的狀態為複本虛擬機器</li><li>網路驗證錯誤</li><li>授權錯誤</li><li>找不到，如果是獨立 HYPER-V 伺服器的虛擬機器</li></ul>|
| 可修復的錯誤         | 重試發生指數返回-關閉增加重試間隔的第一次嘗試 （1、 2、 4、 8 10 分鐘的時間） 開始使用每個複寫間隔內。 如果持續發生錯誤，請再試一次每 30 分鐘。 範例包括︰ <ul><li>網路錯誤</li><li>磁碟空間不足</li><li>記憶體條件</li></ul>|

## <a name="hyper-v-virtual-machine-protection-and-recovery-life-cycle"></a>HYPER-V 虛擬機器保護與復原生命週期

![HYPER-V 虛擬機器保護與復原生命週期](media/site-recovery-understanding-site-to-azure-protection/image05.png)

## <a name="other-references"></a>其他參考

- [監控和疑難排解 VMware，VMM，HYPER-V，及實體網站的保護](./site-recovery-monitoring-and-troubleshooting.md)
- [不讓其與 Microsoft 支援](./site-recovery-monitoring-and-troubleshooting.md#reaching-out-for-microsoft-support)
- [Azure 網站復原的常見錯誤和其解決方案](./site-recovery-monitoring-and-troubleshooting.md#common-asr-errors-and-their-resolutions)
