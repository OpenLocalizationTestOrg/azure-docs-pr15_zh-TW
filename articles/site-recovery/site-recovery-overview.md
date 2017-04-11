<properties
    pageTitle="什麼是網站復原？ |Microsoft Azure"
    description="提供以下概觀 Azure 網站修復服務，然後摘要列出部署案例。"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="cfreeman"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="10/13/2016"
    ms.author="raynew"/>

#  <a name="what-is-site-recovery"></a>什麼是網站復原？

歡迎使用 Azure 網站復原 ！ 本文提供網站復原服務，以及如何將其佔對貴企業的快速概觀。

貴組織需要的業務連續性和損毀時，保留應用程式、 負載，以及資料安全且可供計劃和計畫停機時間的復原 (BCDR) 策略與越快越復原正常運作狀況。 網站復原是 Azure 服務可提供這項策略。

網站復原協調工作量執行內部部署的實體伺服器，以及虛擬機器的複寫。 您可以複製伺服器和 Vm 從主要的資料中心至雲端 (Azure)，或到第二個資料中心。 中斷問題發生時主要網站中，您無法透過保留存取和使用可用的應用程式與工作負載的次要網站。 會傳回標準作業時，您會回到您的主要位置失敗。

## <a name="site-recovery-in-the-azure-portal"></a>Azure 入口網站中的網站復原

Azure 有兩種不同的[部署模型](../resource-manager-deployment-model.md)建立及使用的資源。 Azure 資源管理員模型和傳統的服務管理模型。 Azure 也會有兩個入口網站 – 支援傳統部署模型， [Azure 傳統入口網站](https://manage.windowsazure.com/)和[Azure 入口網站](https://portal.azure.com)的傳統和資源管理員模型都支援。

- 在 [傳統] 入口網站和 Azure 入口網站中使用網站復原。
- 在 Azure 傳統入口網站，您可以使用傳統的服務管理模型支援網站復原。
- 在 Azure 入口網站，您可以支援傳統的模型或資源管理員部署。 

傳統和 Azure 入口網站的部署適用於本文中的資訊。 適用之處，則會註明的差異。


## <a name="why-deploy-site-recovery"></a>為什麼部署網站復原嗎？

以下是網站復原可以您的商務用︰

- **簡化 BCDR**，您可以複製、 錯誤移轉及復原 Azure 入口網站中的單一位置中的多個工作負載的控點。 網站復原協調複寫和容錯移轉，但不會攔截您應用程式的資料或有任何資訊。
- **提供非常有彈性的試驗**，使用網站復原，您可以複製支援的 HYPER-V Vm VMware Vm 與 Windows/Linux 實體伺服器上執行的工作量。
- **執行簡單複寫測試**— 網站復原提供支援損毀復原切入，而不會影響生產環境中測試容錯移轉。
- **無法透過並復原**，您可以使用的非預期的損毀的最小的資料遺失 （根據複寫頻率） 執行計劃的容錯移轉的零資料遺失，與預期中斷或意外的錯誤後移轉。 後移轉後，您可以回復您主要的網站。 網站復原提供修復計劃，包括指令碼和 Azure 自動化活頁簿，讓您可以自訂錯誤移轉及復原多層應用程式。
- **排除次要資料中心**，Azure，而不是次要網站，您可以複製的工作量。 如此的成本及維護次要資料中心的複雜性。 使用提供的所有恢復，複製的資料會儲存於 Azure 儲存。 Vm 會在發生錯誤後移轉時，建立與複寫的資料。
- **與現有的 BCDR 技術整合**，網站復原整合了其他 BCDR 功能。 例如，您可以使用網站復原保護 SQL Server 後端的公司工作負載，包括原生支援的 SQL Server AlwaysOn，來管理可用性群組的容錯移轉。

## <a name="what-can-i-replicate"></a>我可以做什麼？

以下是您可以複製摘要使用網站復原。

![內部部署至內部部署](./media/site-recovery-overview/asr-overview-graphic.png)

**複寫** | **若要複製** 
---|---
在內部部署 VMware Vm 上執行的工作負載 | [Azure](site-recovery-vmware-to-azure-classic.md)<br/><br/> [次要網站](site-recovery-vmware-to-vmware.md)
在內部部署 HYPER-V Vm 上執行的工作負載管理 VMM 雲朵中  | [Azure](site-recovery-vmm-to-azure.md)<br/><br/> [次要網站](site-recovery-vmm-to-vmm.md) 
在內部部署 HYPER-V Vm 上執行的工作量管理 VMM 雲朵舊儲存使用中|  [次要網站](site-recovery-vmm-san.md)
在內部部署 HYPER-V Vm，而不 VMM 上執行的工作負載 | [Azure](site-recovery-hyper-v-site-to-azure.md)
在內部部署實體 Windows/Linux 伺服器上執行的工作負載 | [Azure](site-recovery-vmware-to-azure-classic.md)<br/><br/> [次要網站](site-recovery-vmware-to-vmware.md)


## <a name="what-workloads-can-i-protect"></a>可以保護哪些負載？

讓工作量及應用程式繼續執行一致的方式，中斷問題發生時，網站復原可讓應用程式注意 BCDR。 網站復原提供︰

- **應用程式一致的快照**，機器複寫的單一或多層應用程式中使用應用程式一致的快照。 除了擷取磁碟的資料，應用程式一致的快照集擷取擷取記憶體中的所有資料與程序中的所有交易]。
- **附近同步複寫**— 網站復原提供複寫 frequency 不是低 30 秒 HYPER-V 及連續複寫 VMware。
- **彈性修復計劃**，您可以建立和自訂修復計劃與外部的指令碼，手動的動作。 Azure 自動化 runbooks 整合可讓您要還原按一下整個應用程式堆疊。
- **與 SQL Server AlwaysOn 整合**，您可以管理網站復原修復計劃中的可用性群組的容錯移轉。
- **自動化文件庫**，豐富的 Azure 自動化文件庫提供生產準備、 應用程式，可以下載和與網站復原整合指令碼。
- **簡單的網路管理**— 網站復原和 Azure 中的進階的網路管理可簡化應用程式的網路需求，包括預約 IP 位址、 設定負載平衡器，以及整合 Azure 流量管理員的有效網路 switchovers。


## <a name="next-steps"></a>後續步驟

- 閱讀更多在[哪些負載可以網站復原保護？](site-recovery-workload.md)
- 進一步瞭解網站復原架構[網站復原如何運作？](site-recovery-components.md)
 
