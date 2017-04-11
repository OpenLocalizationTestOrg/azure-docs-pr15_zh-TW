<properties 
   pageTitle="關於 ExpressRoute 虛擬網路閘道器 |Microsoft Azure"
   description="瞭解有關虛擬網路閘道器 ExpressRoute 的。"
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager, azure-service-management"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/03/2016"
   ms.author="cherylmc" />

# <a name="about-virtual-network-gateways-for-expressroute"></a>關於虛擬網路閘道器的 ExpressRoute


虛擬網路閘道器用來傳送 Azure 虛擬網路之間的網路流量和內部部署的位置。 當您設定 ExpressRoute 連線時，您必須建立並設定虛擬網路閘道器與虛擬網路閘道器連線。

當您建立一個虛擬網路閘道時，您可以指定數個設定。 必要的設定值的指定是否閘道器會使用 ExpressRoute 或網站-VPN 流量。 資源管理員部署模型中的設定，則 「-GatewayType 」。

當網路流量傳送專用的私人連線時，您會使用閘道器類型 'ExpressRoute'。 這也稱為 ExpressRoute 閘道器。 當網路流量公用網際網路上傳送加密時，您會使用閘道器類型 'Vpn 」。 這被指 VPN 閘道器。 為網站網站為網站點及 VNet-VNet 連線所有使用 VPN 閘道器。 

每個虛擬網路可能只有一個虛擬網路閘道器，每個閘道器類型。 例如，您可以讓虛擬網路使用閘道器之-GatewayType Vpn，並使用-GatewayType ExpressRoute。 本文著重於 ExpressRoute 虛擬網路閘道器。

## <a name="gwsku"></a>閘道器 Sku

[AZURE.INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

如果您想要升級您的閘道器至功能更強大的閘道器 SKU，在大部分情況下您可以使用 「 調整 AzureRmVirtualNetworkGateway' PowerShell 指令程式。 標準和 HighPerformance Sku 的升級的運作。 不過，若要升級至 UltraPerformance SKU，您必須重新建立閘道器。

###  <a name="aggthroughput"></a>閘道器 SKU 估計彙總處理量


下表顯示的閘道器類型及估計的彙總處理量。 此表格適用於資源管理員] 及 [傳統部署模型。

[AZURE.INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)] 


## <a name="resources"></a>REST Api 和 PowerShell cmdlet

如需其他技術資源與特定的語法需求使用 REST Api 及 PowerShell cmdlet 虛擬網路閘道器設定時，請參閱下列頁面︰

|**傳統** | **資源管理員**|
|-----|----|
|[PowerShell](https://msdn.microsoft.com/library/mt270335.aspx)|[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx)|
|[REST API](https://msdn.microsoft.com/library/jj154113.aspx)|[REST API](https://msdn.microsoft.com/library/mt163859.aspx)|


## <a name="next-steps"></a>後續步驟

如需可用的連線設定，請參閱[ExpressRoute 概觀](expressroute-introduction.md)。 







 
