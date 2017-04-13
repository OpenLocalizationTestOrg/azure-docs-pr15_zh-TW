<properties
    pageTitle="Azure 資源管理員移轉 Azure 服務管理社群工具"
    description="這篇文章分類是提供的社群，協助進行移轉 IaaS 資源的從 Azure 服務管理 Azure 資源管理員堆疊的工具。"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="singhkay"/>

# <a name="community-tools-for-azure-service-management-to-azure-resource-manager-migration"></a>Azure 資源管理員移轉 Azure 服務管理社群工具

這篇文章分類是提供的社群，協助進行移轉 IaaS 資源的從 Azure 服務管理 Azure 資源管理員堆疊的工具。

>[AZURE.NOTE]Microsoft 技術支援正式不支援這些工具。 因此他們是開啟 Github 上取得資料來源，而且我們感到滿意接受 PRs 修正或其他案例。 若要報告問題，請使用 Github 問題功能。
>
> 使用這些工具移轉，將停機時間導致傳統虛擬機器的。 如果您正在尋找的支援平台移轉，請瀏覽 
>
>- [支援的平台移轉至 Azure 資源管理員堆疊傳統 IaaS 資源](./virtual-machines-windows-migration-classic-resource-manager.md)
>- [在平台上的技術深度剖析支援從傳統的移轉至 Azure 資源管理員](./virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
>- [從傳統至 Azure 資源管理員使用 PowerShell 的 Azure 移轉 IaaS 資源](./virtual-machines-windows-ps-migration-classic-resource-manager.md)

## <a name="asm2arm"></a>ASM2ARM

這是您**單一**虛擬機器 (VM) 從升級 Azure 服務管理堆疊 Azure 資源管理員堆疊的 PowerShell 指令碼模組。 

[連結至工具文件](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/asm2arm)

## <a name="migaz"></a>migAz

migAz 是一組完整的 Azure 服務管理 IaaS 資源移轉 Azure 資源管理員 IaaS 資源的其他選項。 移轉發生同一份訂閱或訂閱類型之間的不同訂閱 (例如︰ CSP 訂閱)。

[連結至工具文件](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/migaz)