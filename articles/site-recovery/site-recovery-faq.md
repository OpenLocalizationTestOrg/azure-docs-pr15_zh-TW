<properties
    pageTitle="Azure 網站復原︰ 常見問題集 |Microsoft Azure"
    description="本文將討論關於 Azure 網站修復熱門問題。"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="cfreeman"
    editor=""/>

<tags
    ms.service="get-started-article"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="10/10/2016"
    ms.author="raynew"/>


# <a name="azure-site-recovery-frequently-asked-questions-faq"></a>Azure 網站復原︰ 常見問題集 (faq)

本文包含常見問題 Azure 網站復原。 如果您有問題讀取這份文件後， [Azure 復原服務論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)上張貼。


## <a name="general"></a>一般

### <a name="what-does-site-recovery-do"></a>網站復原做什麼？

網站復原佔您業務連續性和損壞復原 (BCDR) 策略，來協調和自動化複寫從內部部署虛擬機器和實體伺服器 Azure 或次要資料中心。 [進一步瞭解](site-recovery-overview.md)。


### <a name="what-can-site-recovery-protect"></a>什麼可以網站復原保護？

- **HYPER-V 虛擬機器**︰ 網站復原可以保護 HYPER-V VM 上執行任何工作負載。
- **實體伺服器**︰ 網站復原可以保護執行 Windows 或 Linux 的實體伺服器。
- **VMware 虛擬機器**︰ 網站復原可以保護 VMware VM 在執行任何工作負載。

### <a name="does-site-recovery-support-the-azure-resource-manager-model"></a>網站復原是否支援 Azure 資源管理員模型？

除了網站中的 [復原 Azure 傳統入口網站，網站復原會提供的支援資源管理員的 Azure 入口網站。 大部分的部署案例網站復原 Azure 中的入口網站提供精簡的部署體驗，您可以複製 Vm 和實體伺服器到傳統的儲存空間或資源管理員儲存空間。 以下是支援的部署︰

- [複寫 VMware Vm 或實體伺服器 Azure Azure 入口網站中](site-recovery-vmware-to-azure.md)
- [Azure 中 VMM 雲朵 HYPER-V Vm 複寫 Azure 入口網站中](site-recovery-vmm-to-azure.md)
- [Azure 入口網站中，複製到 Azure 的 HYPER-V Vm （不含 VMM)](site-recovery-hyper-v-site-to-azure.md)
- [將在 VMM 雲朵 HYPER-V Vm 複寫到 Azure 入口網站中的次要網站](site-recovery-vmm-to-vmm.md)


### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>我需要什麼 HYPER-V 中協調網站復原試驗？

HYPER-V 主機伺服器所需取決於部署案例。 請查看 HYPER-V 必要條件中︰

- [若要 Azure 複寫 HYPER-V Vm （不含 VMM)](site-recovery-hyper-v-site-to-azure.md#before-you-start)
- [若要 Azure 複寫 HYPER-V Vm （含 VMM)](site-recovery-vmm-to-azure.md#before-you-start)
- [將 HYPER-V Vm 複寫到第二個資料中心](site-recovery-vmm-to-vmm.md#before-you-start)

- 如果您複製到瞭解[支援 HYPER-V Vm 的來賓作業系統](https://technet.microsoft.com/library/mt126277.aspx)的次要資料中心。
- 如果您複製至 Azure，網站復原支援所有來賓作業系統所[支援的 Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)。

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>在用戶端的作業系統上執行 HYPER-V 時可以保護 Vm 嗎？

否，Vm 必須位於 HYPER-V 主機伺服器支援的 Windows 伺服器電腦上執行。 如果您要保護的用戶端電腦無法複製為實體機器[Azure](site-recovery-vmware-to-azure.md)或[次要資料中心](site-recovery-vmware-to-vmware.md)。


### <a name="what-workloads-can-i-protect-with-site-recovery"></a>網站復原可以保護哪些負載？

您可以使用網站復原保護大部分支援的 VM 或實體的伺服器上執行的工作量。 網站復原的應用程式注意複寫，提供支援，讓應用程式可以儲存為智慧狀態。 它與 Microsoft 應用程式，例如 SharePoint、 Exchange、 Dynamics、 SQL Server 和 Active Directory 整合，並與前置廠商，包括 Oracle、 SAP、 IBM 及紅色角色密切合作。 [深入](site-recovery-workload.md)瞭解工作負載保護功能。


### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>是否需要位於 VMM 雲朵 HYPER-V 主機？

如果您想要複製到第二個資料中心，然後 HYPER-V Vm 必須上 HYPER-V 主控伺服器位於 VMM 雲端。 如果您想要複製到 Azure，您可以在包含或不含 VMM 雲朵 HYPER-V 主機伺服器上複寫 Vm。 [閱讀更多](site-recovery-hyper-v-site-to-azure.md)。

### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>如果只有一個 VMM 伺服器可以部署網站復原與 VMM 嗎？

[是]。 您可以將 Vm 複寫至 Azure VMM 雲端中的 HYPER-V 伺服器或您可以複製之間 VMM 雲朵相同的伺服器上。 內部部署至內部部署的試驗，我們建議您在主要和次要網站中有 VMM 伺服器。  [了解更多](site-recovery-single-vmm.md)

### <a name="what-physical-servers-can-i-protect"></a>可以保護哪些實體伺服器？

您可以複製 Azure 或次要網站執行 Windows 和 Linux 的實體伺服器。 [深入瞭解如何](site-recovery-vmware-to-azure.md#protected-machine-prerequisites)作業系統需求。  無論您複製實體伺服器，Azure 或次要網站，就會套用相同的需求。

注意是否您的內部部署伺服器當機，會以 Vm Azure 中執行實體伺服器。 目前不支援回復到內部部署的實體伺服器，但您可能會失敗回到虛擬機器 HYPER-V 或 VMware 上執行。


### <a name="what-vmware-vms-can-i-protect"></a>可以保護哪些 VMware Vm？

若要保護 VMware Vm 必須 vSphere hypervisor，並執行 VMware 工具的虛擬機器。 我們也建議您有管理 hypervisors VMware vCenter 伺服器。 [瞭解更多](site-recovery-vmware-to-azure.md#protected-machine-prerequisites)有關複寫 VMware 伺服器和 Vm Azure 或次要網站的確切需求。

### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>管理我分公司網站復原損毀修復可以？

[是]。 當您使用網站復原您分公司協調複寫和容錯移轉時，您會收到的整合協調流程和檢視所有分支 office 負載在一個中央位置。 輕鬆地，您可以執行容錯移轉，並不造訪分支管理所有分支總部，從損毀復原。

## <a name="security"></a>安全性

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>傳送至網站復原服務複寫資料？

否，網站復原不 intercept 複製的資料，且沒有任何虛擬機器或實體伺服器上執行的哪些相關資訊。
內部部署 HYPER-V 主機、 VMware hypervisors 或實體的伺服器和 Azure 儲存空間或您的次要網站之間交換複製資料。 網站復原有沒有 intercept 該資料的能力。 協調複寫和容錯移轉所需的中繼資料會傳送至網站復原服務。

網站復原是 ISO 27001:2013，27018，HIPAA、 DPA 認證，和在 SOC2 及 FedRAMP JAB 評量。


### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>基於規範，甚至是我們內部部署的中繼資料必須保持在相同的地理區域中。 網站復原可以幫助我們嗎？

[是]。 當您建立網站復原保存庫中時，我們確保所有的中繼資料，我們需要啟用並協調複寫，容錯移轉仍會保留在該區域內的地理邊界。

### <a name="does-site-recovery-encrypt-replication"></a>網站復原是否加密複寫？

虛擬機器及實體伺服器，內部部署網站加密-傳輸中之間複寫支援。 虛擬機器及複寫到 Azure 的實體伺服器，在傳輸時會加密和加密靜止 （在 Azure) 都支援。


## <a name="replication"></a>複寫


### <a name="are-there-any-prerequisites-for-replicating-virtual-machines-to-azure"></a>有任何的必要條件在複寫到 Azure 虛擬機器嗎？

您想要複製到 Azure 虛擬機器應該遵守[Azure 需求](site-recovery-best-practices.md#virtual-machines)。

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>我是否可以將 HYPER-V 產生 2 虛擬機器複寫到 Azure？

[是]。 網站復原會從產生 2 轉換產生 1 在容錯移轉。 在回復電腦轉換回代 2。 [閱讀更多](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)。

### <a name="if-i-replicate-to-azure-how-do-i-pay-for-azure-vms"></a>如果複製至 Azure 如何我支付 Azure Vm？

在一般複寫，資料複寫到地理多餘的 Azure 儲存且不需支付任何 Azure IaaS 虛擬機器費用提供嚴重的優點。 當您執行容錯移轉至 Azure 時，網站復原自動建立 Azure IaaS 虛擬機器，並之後，您會付費您 Azure 中使用計算資源的。


### <a name="is-there-an-sdk-i-can-use-to-automate-the-asr-workflow"></a>有的 SDK 可用來自動化 ASR 工作流程嗎？

[是]。 您可以自動使用 Rest API、 PowerShell 或 Azure SDK 網站復原工作流程。 部署網站復原使用 PowerShell 目前支援案例︰

- [PowerShell 的 Azure 傳統複寫 HYPER-V Vm Vmm 雲朵中](site-recovery-deploy-with-powershell.md)
- [複製中 Vmm 雲朵 HYPER-V Vm Azure PowerShell 資源管理員](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [將不 VMM HYPER-V Vm 複寫到 PowerShell 的 Azure 傳統](site-recovery-hyper-v-site-to-azure-classic.md)
- [複製不 VMM HYPER-V Vm Azure PowerShell 資源管理員](site-recovery-deploy-with-powershell-resource-manager.md)


### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-do-i-need"></a>如果我複寫到 Azure 哪一類的儲存空間帳戶需要嗎？

- **Azure 傳統入口網站**︰ 如果您部署網站復原 Azure 傳統入口網站中，您必須以[標準地理多餘的儲存帳戶](../storage/storage-redundancy.md#geo-redundant-storage)。 目前不支援進階版儲存空間。 帳戶必須是網站復原保存庫為相同的區域。

- **Azure 入口網站**︰ 如果您部署網站復原 Azure 入口網站中，您必須 LRS 或 GRS 儲存帳戶。 我們建議您 GRS，讓資料是同時，如果地區的資料，或如果無法復原的主要區域。 帳戶必須在同一個地區為修復服務保存庫。 您複製 VMware Vm 或實體的伺服器時，才支援進階版儲存空間。

### <a name="how-often-can-i-replicate-data"></a>頻率可以複製資料？

- **超 v**可將複寫 HYPER-V Vm 5 分鐘或 15 分鐘每 30 秒。 如果您已設定好舊複寫複寫是同步。
- **VMware 和實體伺服器︰**以下複寫頻率不相關的。 複寫是連續。

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>我可以延伸複寫從現有的復原網站另一個第三個網站？

延伸或鏈結複寫不受支援。 要求[意見反應](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication)中的功能。


### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>我如何離線複製至 Azure 複製的第一次？

這不受支援。 要求[意見反應](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)中的功能。


### <a name="can-i-exclude-specific-disks-from-replication"></a>我可以從複寫排除特定磁碟嗎？

當您準備要 Azure，使用 [Azure 入口網站的 [[複製 VMware Vm 和實體伺服器](site-recovery-vmware-to-azure.md#exclude-disks-from-replication)時，會支援此項目。


### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>我可以複製動態磁碟的虛擬機器嗎？

動態磁碟支援 HYPER-V 虛擬機器廖。 他們也支援在複寫到 Azure VMware Vm 和實體機器時。 作業系統磁碟必須是基本磁碟。

### <a name="can-i-add-a-new-machine-to-an-existing-replication-group"></a>可以對現有複寫群組新增新的電腦嗎？

將新的電腦新增至現有複寫群組功能受支援。 若要這麼做，請選取複寫群組 （從 「 複寫項目 」 刀） 和選取以滑鼠右鍵按一下快顯功能表上的複寫群組並選取適當的選項。

![新增至複寫群組](./media/site-recovery-faq/add-server-replication-group.png)

### <a name="can-i-throttle-bandwidth-allotted-for-hyper-v-replication-traffic"></a>我可以節流分配給 HYPER-V 複寫流量的頻寬嗎？

[是]。 您可以閱讀更多關於節流頻寬部署文章中︰

- [容量複寫 VMware Vm 和實體伺服器計劃](site-recovery-vmware-to-azure.md#step-5-capacity-planning)
- [容量複寫中 VMM 雲朵 HYPER-V Vm 計劃](site-recovery-vmm-to-azure.md#step-5-capacity-planning)
- [容量複寫不 VMM HYPER-V Vm 計劃](site-recovery-hyper-v-site-to-azure.md#step-5-capacity-planning)

## <a name="failover"></a>容錯移轉


### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-virtual-machines-after-failover"></a>如果我正在無法透過 Azure，如何存取 Azure 虛擬機器後移轉後？

您可以在安全的網際網路連線、 透過網站-VPN，或透過 Azure ExpressRoute 存取 Azure Vm。 您必須準備項目，才能將連線的數字。 延伸閱讀中︰

- [Azure Vm 後移轉後 VMware Vm 或實體伺服器的連接](hsite-recovery-vmware-to-azure.md#step-7-test-the-deployment)
- [連線至 Azure Vm 後移轉後的 HYPER-V Vm VMM 雲朵中](site-recovery-vmm-to-azure.md#step-7-test-your-deployment)
- [連線至 Azure Vm 後移轉後的不 VMM HYPER-V Vm](site-recovery-hyper-v-site-to-azure.md#step-7-test-the-deployment)


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>我無法透過 Azure 如何 Azure 確知我的資料是否有彈性？

Azure 被專為彈性。 如果網站復原已經是的工程容錯移轉至第二個 Azure 資料中心，根據 Azure SLA 的設計。 如果這種情況，我們請確定您的中繼資料，並保存庫保留在您選擇的您保存庫相同地理區域內。  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>如果我正在兩個資料中心之間複寫如果我的主要資料中心遭遇未預期的資料，會發生什麼情況？

您可以從次要網站意外的容錯移轉觸發程序。 網站復原不需要執行容錯移轉的主要網站的連線。

### <a name="is-failover-automatic"></a>是容錯移轉自動？

容錯移轉無法自動。 啟動錯誤後的移轉單一按一下，在入口網站，或者您可以使用[網站復原 PowerShell](https://msdn.microsoft.com/library/dn850420.aspx)觸發容錯移轉。 回失敗是網站復原入口網站中的簡單的動作。

若要自動化您無法使用內部部署 Orchestrator 或 Operations Manager 偵測虛擬機器失敗，，然後觸發使用 SDK 容錯移轉。

- [閱讀更多](site-recovery-create-recovery-plans.md)修復計劃的相關資訊。
- [閱讀更多](site-recovery-failover.md)關於容錯移轉資訊。
- [閱讀更多](site-recovery-failback-azure-to-vmware.md)有關失敗回復 VMware Vm 和實體伺服器


## <a name="service-providers"></a>服務提供者


### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>我的服務提供者。 運作網站復原與共用了專用的基礎結構模型？

是的網站復原支援兩種專用和共用基礎結構模型。

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>服務提供者是我的租用戶共用網站復原服務的身分識別？

[否]。 租用戶身分識別會保持匿名。 您的租用戶不需要網站復原入口網站的存取。 只有服務提供者管理員互動入口網站。


### <a name="will-tenant-application-data-ever-go-to-azure"></a>將租用戶應用程式的資料時移至 Azure 嗎？

當複製服務提供者所擁有的網站之間，應用程式的資料永遠不會撥給 Azure。 加密傳輸中，並直接之間的服務提供者網站複製的資料。

如果您複製至 Azure，應用程式的資料傳送 Azure 儲存空間，但無法復原網站服務。 資料加密的傳輸中，並保持加密 Azure 中。


### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>我的租用戶是否會收到任何 Azure 服務的帳單？

[否]。 Azure 的帳單的關聯性是直接使用的服務提供者。 服務提供者會負責適用於其承租人產生特定的帳單。

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>如果我正在複寫到 Azure，我們需要隨時執行 Azure 虛擬機器？

否，資料會複寫至您的訂閱中的 Azure 儲存體帳戶。 當您執行的測試容錯移轉 （DR 向下切入） 或實際的容錯移轉時，網站復原自動建立虛擬機器中您的訂閱。

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>複製至 Azure 時確保租用戶層級隔離？

[是]。

### <a name="what-platforms-do-you-currently-support"></a>哪些平台您目前支援？

我們支援 Azure 套件，雲端平台系統，而系統管理中心 （2012年及更新版本） 的部署。 [瞭解更多](https://technet.microsoft.com/library/dn850370.aspx)有關 Azure 套件及網站修復整合。

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>您是否支援單一 Azure 隨身出版單一 VMM 伺服器部署？

是的您可以將 HYPER-V 虛擬機器複寫到 Azure 或服務提供者的網站之間複製。  請注意，是否您要複製服務提供者的網站之間，Azure runbook 整合無法使用。


## <a name="next-steps"></a>後續步驟

- 閱讀[網站修復概觀](site-recovery-overview.md)
- 瞭解[網站復原架構](site-recovery-components.md)  
