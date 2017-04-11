
<properties
    pageTitle="Azure 網站復原︰ 複製 HYPER-V 虛擬機器單一 VMM 伺服器上 |Microsoft Azure"
    description="本文將說明如何複寫 HYPER-V 虛擬機器，當您只需要在單一 VMM 伺服器。"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="backup-recovery"
    ms.date="08/24/2016"
    ms.author="raynew"/>

#  <a name="replicate-hyper-v-virtual-machines-on-a-single-vmm-server"></a>複製單一 VMM 伺服器上 HYPER-V 虛擬機器

閱讀本文以瞭解如何複製 HYPER-V 虛擬機器位於 VMM 雲端 HYPER-V host （主機） 伺服器上，當您只需要在單一 VMM 伺服器部署中。

Azure 有兩種不同的[部署模型](../resource-manager-deployment-model.md)建立及使用的資源︰ Azure 資源管理員] 及 [傳統。 Azure 也會有兩個入口網站 – 支援傳統部署模型，Azure 傳統入口網站和支援兩種部署模型 Azure 入口網站。 本文包含複寫 Azure 入口網站中所設定相關的指示。


如果您有任何問題，閱讀本文之後，張貼 Disqus 註解下方或[Azure 修復服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上的本文中。

## <a name="overview"></a>概觀

如果您想要複寫 HYPER-V Vm 位於 HYPER-V 主機 VMM 中，您只需要在單一 VMM 伺服器，您可以[複製到 Azure](site-recovery-vmm-to-azure.md)，或是雲朵單一 VMM 伺服器上。

我們建議您複製至 Azure，因為錯誤移轉及復原不順暢複寫之間雲朵時，所需的手動步驟數字。 如果您想要複寫使用 VMM 伺服器，您可以執行下列動作︰

- 複製與單一獨立 VMM 伺服器
- 複製與單一延伸 Windows 叢集部署 VMM 伺服器


## <a name="replicate-across-sites-with-a-single-standalone-vmm-server"></a>跨網站與單一獨立 VMM 伺服器複寫

![獨立虛擬 VMM 伺服器](./media/site-recovery-single-vmm/single-vmm-standalone.png)

在這個案例中，您將單一 VMM 伺服器部署虛擬機器中的主要的網站，為並將此 VM 複寫到使用網站復原和 HYPER-V 複本的次要網站。

1. 設定 HYPER-V VM 上 VMM。 建議您安裝 SQL Server 執行個體 VMM 相同 VM 上用來節省時間。 如果您想要使用的 SQL Server，並中斷遠端執行個體發生，您要復原的執行個體，您可以復原 VMM 之前。
2. 請確定 VMM 伺服器具有兩個以上的雲朵設定。 一個雲端包含 Vm 您要複製，以及其他雲端做為次要的位置。 包含您要保護的 Vm 雲端應該有︰

    - 一或多個 VMM 主機群組包含每個主機] 群組中的一或多個 HYPER-V host （主機） 伺服器。
    - 每個 HYPER-V 主機伺服器上至少有一個 HYPER-V 虛擬機器。

3. 建立修復服務保存庫、 產生和下載保存庫登錄機碼，並在保存庫註冊 VMM 伺服器。 在註冊時，請 VMM 伺服器上安裝 Azure 網站修復提供者。
4. 設定一或多個雲朵上 VMM VM，並新增這些雲朵 HYPER-V 主機。
3. 設定雲朵保護設定。 您可以指定單一 VMM 伺服器的名稱為來源及目標位置。 若要設定的網路對應，您會對應 VM 網路上的 [您想要保護，請至 VM 網路複寫雲端 Vm 的雲端。
4. 啟用初始複寫 Vm 您想要在網路上保護，因為這兩個雲朵位於相同的伺服器上。
4. 在 HYPER-V 管理員主控台，包含 VMM VM HYPER-V 主機上啟用 HYPER-V 複本，然後啟用複寫 VM 上。 請確定您沒有將 VMM VM 新增至任何雲朵受到網站復原。 如此一來，可確保網站復原不覆寫 HYPER-V 複本的設定。
5. 如果您想要建立修復計劃，您可以指定來源和目標的相同 VMM 伺服器。

請依照建立保存庫、 登錄伺服器，並設定保護[本文](site-recovery-vmm-to-vmm.md)中的指示進行。

### <a name="what-to-do-in-an-outage"></a>處理方式在中斷

如果完整的資料，就會發生，您需要的次要網站，請執行下列動作︰

1.  在 HYPER-V 管理員主控台次要網站中，執行意外的容錯移轉失敗 VMM VM 從主要次要網站上。
2.  請確認 VMM VM 和使用中的次要網站。
3.  修復服務保存庫中執行容錯工作負載 Vm 從主要次要雲朵意外的容錯移轉。 若要完成的 Vm 意外的容錯移轉，認可容錯移轉或視需要選取不同的復原點。
4.  意外的容錯移轉完成後，使用者可以存取的次要網站中的工作量資源。

當主站台運作正常一次時，執行下列動作︰

1.  在 HYPER-V 管理員主控台，啟用反向複寫 VMM VM，若要啟動從次要改為主要複寫它。
2.  在 HYPER-V 管理員主控台中，執行計劃的容錯移轉回復 VMM VM 主要的網站。 確認容錯移轉完成。 然後啟用反向複寫開始從主要的 VMM 複寫到第二個。
3.  修復服務保存庫中啟用反向複寫 Vm，若要啟動從次要改為主要複寫他們的工作量。
4.  修復服務保存庫中執行失敗返回至主網站的工作量 Vm 計劃容錯移轉。 確認容錯移轉完成。 然後啟用反向複寫開始從主要的工作量 Vm 複寫到第二個。



## <a name="replicate-across-sites-with-a-single-vmm-server-in-a-stretched-cluster"></a>跨網站與單一 VMM 伺服器延伸叢集複寫

![群組直虛擬 VMM 伺服器](./media/site-recovery-single-vmm/single-vmm-cluster.png)

複製到次要網站 VM 為部署獨立 VMM 伺服器，而不是您可以提供 VMM 高度為 Windows 容錯移轉叢集 VM 部署它。 這會提供工作量可靠度及保護硬體失敗。 若要部署網站復原 VMM VM 應該部署伸展叢集跨不同地理位置的網站。 若要執行這項操作︰

1. 安裝 VMM 虛擬機器中 Windows 容錯移轉叢集，並選取要在安裝期間執行成高度可用的伺服器的選項。
2. 應該使用 SQL Server AlwaysOn 可用性群組複寫 VMM 所使用的 SQL Server 執行個體，如此就會在次要網站資料庫的複本。
3. 請依照建立保存庫、 登錄伺服器，並設定保護[本文](site-recovery-vmm-to-vmm.md)中的指示進行。 您需要註冊叢集修復服務保存庫中的每個 VMM 伺服器。 若要這麼做，您可以安裝在作用中的節點的提供者，並註冊 VMM 伺服器。 然後您會在其他節點上安裝的提供者。

### <a name="what-to-do-in-an-outage"></a>處理方式在中斷

中斷後，VMM 伺服器和其對應的 SQL Server 資料庫，移轉，以及從次要網站存取。


## <a name="next-steps"></a>後續步驟

[瞭解更多](site-recovery-vmm-to-vmm.md)有關詳細網站復原的部署 VMM VMM 複寫。
