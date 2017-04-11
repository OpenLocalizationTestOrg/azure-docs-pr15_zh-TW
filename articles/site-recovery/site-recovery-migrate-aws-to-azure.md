<properties
    pageTitle="將從 Amazon Web 服務的 Windows 虛擬機器移轉到網站復原 Azure |Microsoft Azure"
    description="本文將說明如何移轉 Windows Azure 使用 Azure 網站修復至執行中 Amazon Web 服務 (AWA) 的虛擬機器。"
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
    ms.date="08/22/2016"
    ms.author="raynew"/>

#  <a name="migrate-windows-virtual-machines-in-amazon-web-services-aws-to-azure-with-azure-site-recovery"></a>將 Windows 虛擬機器中 Amazon Web 服務 (AWS) 移轉到 Azure Azure 網站復原

## <a name="overview"></a>概觀

歡迎使用 Azure 網站復原。 您可以使用這份文件，將 Windows Azure 網站復原中執行中 AWS 的執行個體。 在開始之前，請注意:

- Azure 有兩種不同的部署模型建立及使用的資源︰ Azure 資源管理員] 及 [傳統。 Azure 也會有兩個入口網站 – 支援傳統部署模型，Azure 傳統入口網站和支援兩種部署模型 Azure 入口網站。 無論您設定網站復原，在 [資源管理員或傳統移轉的基本步驟都相同。不過 Azure 入口網站的相關指示 UI 和本文中的螢幕擷取畫面。
- **目前您可以只移轉 AWS 到 Azure。您可以透過 Azure，以從 AWS Vm 失敗，但您無法回復其一次。沒有進行中的複寫。**
- 移轉中的指示進行這份文件會根據將實體機器複寫到 Azure 的指示進行。 [複製 VMware Vm 或實體 Azure 伺服器](site-recovery-vmware-to-azure.md)，其中說明如何複寫 Azure 入口網站中的實體伺服器其包含連結的步驟。
- 如果您正在設定網站修復，在 [傳統] 入口網站中，請遵循[本文](site-recovery-vmware-to-azure-classic.md)中的詳細的指示。 **您應該無法再使用**[舊版的文件](site-recovery-vmware-to-azure-classic-legacy.md)中的指示進行。

在底端的本文中，或在[Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上張貼任何註解或問題


## <a name="prerequisites"></a>必要條件

以下是您需要此部署

- **設定伺服器**︰ 執行 Windows Server 2012 R2 做為設定伺服器內部部署 VM。 太此 VM 上安裝其他網站復原元件 （包括的程序伺服器及主要目標伺服器）。 閱讀更多，在[案例架構](site-recovery-vmware-to-azure.md#scenario-architecture)及[設定伺服器的先決條件](site-recovery-vmware-to-azure.md#configuration-server-prerequisites)。
- **EC2 VM 執行個體**︰ 您想要移轉的執行 Windows 的執行個體。

## <a name="deployment-steps"></a>部署步驟

本節說明新的 Azure 入口網站的部署步驟。 如果您需要網站復原的部署步驟在 [傳統] 入口網站中，請參閱[本文](site-recovery-vmware-to-azure-classic.md)。

1. [建立保存庫](site-recovery-vmware-to-azure.md#create-a-recovery-services-vault)。
2. [部署設定伺服器](site-recovery-vmware-to-azure.md#step-2-set-up-the-source-environment)。
3. 您已部署設定伺服器之後，可以通訊驗證您要移轉的 vm。
4. [設定複寫設定](site-recovery-vmware-to-azure.md#step-4-set-up-replication-settings)。 建立複寫原則，並指派到設定伺服器。
5. [安裝行動服務](site-recovery-vmware-to-azure.md#step-6-replication-application)。 每個您想要保護的 VM 必須安裝行動服務。 此服務傳送資料至程序伺服器。 行動服務可以手動安裝或推入和啟用保護 vm 時自動安裝程序伺服器。 允許此服務推入安裝應該設定防火牆規則，在您想要移轉的 EC2 執行個體。 EC2 執行個體的安全性群組應有下列規則︰

    ![防火牆規則](./media/site-recovery-migrate-aws-to-azure/migrate-firewall.png)

6. [啟用複寫](site-recovery-vmware-to-azure.md#enable-replication)。 啟用您要移轉的 Vm 複寫。 您可以找出使用 [私用的 IP 位址，就可以從 EC2 主控台 EC2 執行個體。
7. [執行意外的容錯移轉](site-recovery-failover.md#run-an-unplanned-failover)。 初始複寫完成之後，您可以執行意外的容錯移轉從 AWS 至 Azure 的每個 VM。 或者，您可以建立一個修復計劃，並執行意外的容錯移轉，將多個虛擬機器從 AWS 移轉至 Azure。 [瞭解更多](site-recovery-create-recovery-plans.md)有關修復計劃。

## <a name="next-steps"></a>後續步驟

進一步瞭解在其他複寫案例[什麼是 Azure 網站復原？](site-recovery-overview.md)
