<properties
    pageTitle="Windows Vm 規劃維修 |Microsoft Azure"
    description="了解哪些 Azure 預定進行的維修作業，而該如何影響您在 Azure 中執行的 Windows 虛擬機器"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="drewm"
    manager="timlt"
    editor=""
    tags="azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/26/2016"
    ms.author="drewm"/>

# <a name="planned-maintenance-for-virtual-machines-in-azure"></a>預定進行的維護中 Azure 虛擬機器


了解哪些 Azure 預定進行的維護是，以及如何將會影響您的 Windows 虛擬機器中的可用性。 本文也適用於[Linux 虛擬機器](virtual-machines-linux-planned-maintenance.md)。 

本文提供有關 Azure 預定進行的維護程序背景。 如果您想要重新啟動您 VM 的原因進行疑難排解，您可以[閱讀此部落格文章詳細檢視 VM 重新啟動記錄](https://azure.microsoft.com/blog/viewing-vm-reboot-logs/)。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## <a name="why-azure-performs-planned-maintenance"></a>為什麼 Azure 執行預定進行的維修作業

Microsoft Azure 定期執行更新，以改善可靠性、 效能及安全性主機基礎結構的虛擬機器的全球。 更新許多會執行而不會以您的虛擬機器或雲端服務，包括記憶體保留更新任何影響。

不過，某些更新需要重新啟動電腦以您的虛擬機器套用基礎結構的必要的更新。 虛擬機器都關閉，我們修補基礎結構]，然後重新啟動虛擬機器鍵。

請注意，是會影響您的虛擬機器中的可用性進行的維修作業的兩種︰ 計劃和尚未計劃。 本頁面說明如何 Microsoft Azure 執行預定進行的維修作業。 如需有關尚未計劃進行的維修作業的詳細資訊，請參閱[瞭解計畫與計畫進行的維修作業](virtual-machines-windows-manage-availability.md)。

[AZURE.INCLUDE [virtual-machines-common-planned-maintenance](../../includes/virtual-machines-common-planned-maintenance.md)]
