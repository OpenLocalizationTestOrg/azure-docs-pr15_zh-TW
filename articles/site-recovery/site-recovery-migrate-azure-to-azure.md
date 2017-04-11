<properties
    pageTitle="從一個 Azure 區域的 Azure IaaS 虛擬機器移轉到另一個網站復原 |Microsoft Azure"
    description="從一個 Azure 區域的 Azure IaaS 虛擬機器移轉到另一個使用 Azure 網站復原。"
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
    ms.topic="article"
    ms.date="08/21/2016"
    ms.author="raynew"/>

#  <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>移轉 Azure 網站復原 Azure 區域間 Azure IaaS 虛擬機器

## <a name="overview"></a>概觀

歡迎使用 Azure 網站復原 ！ 如果您想要移轉 Azure Vm 之間 Azure 區域，請使用這份文件。 在開始之前，請注意:

- Azure 有兩種不同的部署模型建立及使用的資源︰ Azure 資源管理員] 及 [傳統。 Azure 也會有兩個入口網站 – 支援傳統部署模型，Azure 傳統入口網站和支援兩種部署模型 Azure 入口網站。 無論您設定網站復原，在 [資源管理員或傳統移轉的基本步驟都相同。 不過 Azure 入口網站的相關指示 UI 和本文中的螢幕擷取畫面。
- **目前您可以只移轉從一個區域到另一個。您可以移到另一個從一個 Azure 區域 Vm 失敗，但您無法回復其一次。**
- 移轉中的指示進行這份文件會根據將實體機器複寫到 Azure 的指示進行。 [複製 VMware Vm 或實體 Azure 伺服器](site-recovery-vmware-to-azure.md)，其中說明如何複寫 Azure 入口網站中的實體伺服器其包含連結的步驟。
- 如果您正在設定網站修復，在 [傳統] 入口網站中，請遵循[本文](site-recovery-vmware-to-azure-classic.md)中的詳細的指示。 **您應該無法再使用**[舊版的文件](site-recovery-vmware-to-azure-classic-legacy.md)中的指示進行。

在底端的本文中，或在[Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上張貼任何註解或問題。


## <a name="prerequisites"></a>必要條件

以下是您需要此部署︰

- **設定伺服器**︰ 執行 Windows Server 2012 R2 做為設定伺服器內部部署 VM。 太此 VM 上安裝其他網站復原元件 （包括的程序伺服器及主要目標伺服器）。 閱讀更多，在[案例架構](site-recovery-vmware-to-azure.md#scenario-architecture)及[設定伺服器的先決條件](site-recovery-vmware-to-azure.md#configuration-server-prerequisites)。
- **IaaS 虛擬機器**︰ 您想要移轉的 Vm。 您可以將這些 Vm 移轉，並將它們當作實體機器。

## <a name="deployment-steps"></a>部署步驟

本節說明新的 Azure 入口網站的部署步驟。 如果您需要網站復原的部署步驟在 [傳統] 入口網站中，請參閱[本文](site-recovery-vmware-to-azure-classic.md)。

1. [建立保存庫](site-recovery-vmware-to-azure.md#create-a-recovery-services-vault)。
2. [部署設定伺服器](site-recovery-vmware-to-azure.md#step-2-set-up-the-source-environment)。
3. 您已部署設定伺服器之後，請核取 [可以通訊與您想要移轉的 Vm。
4. [設定複寫設定](site-recovery-vmware-to-azure.md#step-4-set-up-replication-settings)。 建立複寫原則，並指派到設定伺服器。
5. [安裝行動服務](site-recovery-vmware-to-azure.md#step-6-replication-application)。 每個您想要保護的 VM 必須安裝行動服務。 此服務傳送資料至程序伺服器。 行動服務可以手動安裝或推入和啟用保護 vm 時自動安裝程序伺服器。 應該設定防火牆規則，在您想要移轉的 Vm 允許推入安裝此服務。
6. [啟用複寫](site-recovery-vmware-to-azure.md#enable-replication)。 啟用您要移轉的 Vm 複寫。 您可以找出您想要移轉至 Azure 使用私人 IP 位址的虛擬機器 IaaS 虛擬機器。 Azure 中尋找 [虛擬機器儀表板上的這個地址。 當您啟用複寫時，您設定的電腦類型的 Vm 為實體機器。
7. [執行意外的容錯移轉](site-recovery-failover.md#run-an-unplanned-failover)。 初始複寫完成之後，您可以從一個 Azure 區域執行意外的容錯移轉到另一個。 或者，您可以建立一個修復計劃，並執行意外的容錯移轉，移轉區域之間的多個虛擬機器。 [瞭解更多](site-recovery-create-recovery-plans.md)有關修復計劃。

## <a name="next-steps"></a>後續步驟

進一步瞭解在其他複寫案例[什麼是 Azure 網站復原？](site-recovery-overview.md)
