<properties
    pageTitle="可用性設定指導方針 |Microsoft Azure"
    description="深入了解部署可用性集 Azure 基礎結構服務中的重要的設計及實作方針。"
    documentationCenter=""
    services="virtual-machines-windows"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="availability-sets-guidelines"></a>顯示狀態設定為指導方針

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)] 

本文著重於瞭解所需的規劃步驟，以確保您的應用程式仍會保留在計畫之內事件時可存取的可用性組。

## <a name="implementation-guidelines-for-availability-sets"></a>實作可用性組指導方針

決策︰

- 多少可用性設定您需要不同的角色和層級的應用程式基礎結構中？

工作︰

- 在您需要每個應用程式層定義 Vm 數目。
- 判斷您是否需要調整錯誤的數目，或更新網域用於您的應用程式。
- 定義您的命名慣例，以及使用的可用性集 Vm 位於它們。 VM 只能放在一個可用的設定。 

## <a name="availability-sets"></a>可用性集

在 Azure 虛擬機器 (Vm) 可以放在名為顯示狀態設定為邏輯群組。 當您在可用性組中建立 Vm 時，Azure 的平台會分配的基礎結構這些 Vm 的位置。 有應該 Azure 平台或基礎的硬體預定進行的維修作業事件/基礎結構錯誤的顯示狀態集使用可確保至少有一個 VM 保持執行。

最佳作法不是應該位於單一 VM 的應用程式。 包含單一 VM 可用性集無法取得計畫之內事件 Azure 的平台內的任何保護。 [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines)需要兩個或多個 Vm 設為允許 Vm 跨基礎結構的分佈的可用性。

若要更新的網域和故障網域資料中分為 Azure 中的基礎結構。 這些網域是由什麼主機共用一般更新循環圖或共用類似實際基礎結構例如和網路所定義。 Azure 自動將您的 Vm 跨網域設定，若要維持可用性和容錯可用性。 根據您的應用程式] 及 [可用性組中的 Vm 數的大小，您可以調整您想要使用的網域數目。 您可以閱讀更多關於[管理可用性及的更新和故障網域用途](virtual-machines-windows-manage-availability.md)。

當設計您的應用程式基礎結構時，您也應該規劃您使用的應用程式層。 群組 Vm 相同的目的中可用的設定，例如設定為您執行 IIS 的前端 Vm 可用性。 建立個別的顯示狀態設定為您的後端 Vm 執行 SQL Server。 目標是要確保您的應用程式的每個元件受到可用性設定，並至少一次執行個體永遠保持執行。

負載平衡器可以利用前面同時可用性設定工作，並確保流量永遠可以路由至執行中執行個體的每一個應用程式層級。 如果沒有負載平衡器，您 Vm 可能會繼續執行整個計劃和尚未計劃進行的維修作業事件，但您的使用者可能無法解決方法，如果無法使用的主要 VM。


## <a name="next-steps"></a>後續步驟
[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)] 