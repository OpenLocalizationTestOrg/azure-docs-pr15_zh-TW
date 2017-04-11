<properties
   pageTitle="實作高度可用的混合式網路架構 |Microsoft Azure"
   description="如何實作橫跨 Azure 虛擬網路和連線與 VPN 閘道器容錯移轉 ExpressRoute 內部網路的安全網站間的網路架構。"
   services="guidance,virtual-network,vpn-gateway,expressroute"
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="telmos"/>

# <a name="implementing-a-highly-available-hybrid-network-architecture"></a>實作高度可用的混合式網路架構

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

本文將說明內部部署網路使用連線到虛擬網路上 Azure ExpressRoute，以網站為網站虛擬私人網路 (VPN) 的容錯移轉連線的最佳作法。 流量流動內部網路和 ExpressRoute 連線透過 Azure 虛擬網路 (VNet)。  在 ExpressRoute 電路的連線中斷時，將會透過 IPSec VPN 通道路由流量。

> [AZURE.NOTE] Azure 有兩種不同的部署模型︰[資源管理員][resource-manager-overview]和傳統。 這個藍圖使用資源管理員，Microsoft 建議新的部署。

此結構的一般的使用案例包括︰

- 混合式應用程式之間的內部部署網路和 Azure 分佈工作負載的位置。

- 執行需要高性的大型、 重要的工作負載的應用程式。

- 大型備份與還原設備必須開儲存的資料。

- 處理大型資料的工作量。

- 使用 Azure 損毀修復網站。

請注意，是否無法使用 ExpressRoute 電路，VPN 路由將只處理私人對等的連線。 公用對等及對等連接的 Microsoft 會透過網際網路傳遞。

## <a name="architecture-diagram"></a>架構圖表

>[AZURE.NOTE] [Azure VPN 閘道器服務][azure-vpn-gateway]實作兩種類型的虛擬網路閘道器。VPN 虛擬網路閘道器及 ExpressRoute 虛擬網路閘道器。 在此文件，的字詞*VPN 閘道*參照的片語*VPN 虛擬網路閘道器*時的 Azure 服務及*ExpressRoute 虛擬網路閘道器*用來分別參照 VPN 及 ExpressRoute 實作閘道器。

下圖會醒目提示在這種架構的重要元件︰

> Visio 文件包含此架構圖是可從[Microsoft 下載中心][visio-download]。 這是 「 混合式網路上-增 VPN 」] 頁面。

![[0]][0]

- **Azure 虛擬網路 (VNets)。** 每個 VNet 在單一 Azure 區域中，並可以主控多個應用程式層。 應用程式層可以使用子網路中每個 VNet 區隔及/或網路安全性群組 (NSGs)。

- **公司網路內部部署。** 這是一個網路的電腦和裝置，透過私人執行在組織內的區域網路連線。

- **VPN 應用裝置。** 這是在裝置或服務，提供外部內部部署網路連線能力。 VPN 應用裝置可能硬體裝置，或在 Windows Server 2012 中的遠端存取服務 (RRAS) 路由等軟體解決方案。

> [AZURE.NOTE] 如需支援的 VPN 就和設定選取的 VPN 就需連線至 Azure VPN 閘道器的資訊的清單，請參閱[支援 Azure VPN 裝置清單]中適當的裝置的指示[vpn-appliance]。

- **VPN 虛擬網路閘道器。** VPN 虛擬網路閘道器可讓您連線到內部部署網路中 VPN 應用裝置 VNet。 VPN 虛擬網路閘道器設定為接受來自內部部署網路要求僅透過 VPN 應用裝置。 如需詳細資訊，請參閱[連線到 Microsoft Azure 虛擬網路內部部署網路][connect-to-an-Azure-vnet]。

- **ExpressRoute 虛擬網路閘道器。** ExpressRoute 虛擬網路閘道器可讓您連線至與您的內部部署網路連線所用的 ExpressRoute 電路 VNet。

- **閘道器子網路。** 虛擬網路閘道器會保留在相同的子網路。

- **VPN 連線。** 連線的屬性會指定連線類型 (IPSec)，以及共用與內部部署 VPN 應用裝置的鍵加密流量。

- **ExpressRoute 電路。** 這是階層 2 或連線提供者所提供階層 3 電路 Azure 邊緣路由器到該連接內部部署網路。 電路使用連線提供者管理的硬體基礎結構。

- **N 層雲端應用程式。** 這是裝載於 Azure 的應用程式。 它可以包含多個層級，具有多個子網路連線透過 Azure 負載平衡器。 在每一個子網路流量可能會使用[Azure 網路安全性群組]定義規則[azure-network-security-group](NSGs)。 如需詳細資訊，請參閱[快速入門 Microsoft Azure 安全性][getting-started-with-azure-security]。

## <a name="recommendations"></a>建議

Azure 提供許多不同的資源和資源類型，因此此參照架構可提供許多不同的方式。 我們提供安裝遵循這些建議參考架構 Azure 資源管理員範本。 如果您選擇，建立您自己參考架構，您應該遵循上述建議，除非您有建議不符合特定需求。

### <a name="vnet-and-gatewaysubnet"></a>VNet 和 GatewaySubnet

建立 ExpressRoute 虛擬網路閘道器和 VPN 虛擬網路閘道器在同一個 VNet。 這表示他們應該共用相同的子網路名稱為**GatewaySubnet**

如果 VNet 已經包含名稱為**GatewaySubnet**子網路，請確認它 /27 或更大的位址空間。 如果現有子網路太小，然後移除它，如下所示，並建立一個新的下一個項目符號所示︰

```powershell
$vnet = Get-AzureRmVirtualNetworkGateway -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
Remove-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet
```

如果 VNet 不包含名稱為**GatewaySubnet**子網路，然後建立新，如下所示︰

```powershell
$vnet = Get-AzureRmVirtualNetworkGateway -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.224/27"
$vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

### <a name="vpn-and-expressroute-gateways"></a>要有 vpn 才能與 ExpressRoute 閘道器

驗證您的組織是否符合[ExpressRoute 必備需求][expressroute-prereq]需連線至 Azure。

如果您已經在您 Azure VNet VPN 虛擬網路閘道器，請移除它，如下所示。

```powershell
Remove-AzureRmVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>
```

依照[實作與 Azure ExpressRoute 的混合式網路架構]的指示操作[implementing-expressroute]建立您的 ExpressRoute 連線。

依照[實作與 Azure 及內部部署 VPN 的混合式網路架構]的指示操作[implementing-vpn]建立 VPN 虛擬網路閘道器的連線。

您已建立虛擬網路閘道器連線之後，請測試為下列的環境︰

1. 請確定您可以連線至您的 Azure VNet 從您的內部部署網路。

2. 請連絡您的提供者，若要停止 ExpressRoute 連線以進行測試。

3. 確認您可以仍連線從您的內部部署網路使用 VPN 虛擬網路閘道器連線您 Azure VNet 到。

4. 請連絡您的提供者 reestabilish ExpressRoute 連線。

## <a name="considerations"></a>考量

ExpressRoute 考量，請參閱[執行混合式網路架構與 Azure ExpressRoute] [guidance-expressroute]指引。

網站-VPN 考量，請參閱[執行混合式網路架構與 Azure 及內部部署 VPN] [guidance-vpn]指引。

一般 Azure 的安全性考量，請參閱[Microsoft 雲端服務和網路安全性][best-practices-security]。

## <a name="solution-deployment"></a>解決方案部署

如果您有已適當的網路應用裝置使用現有的內部部署基礎結構時，您可以部署參考架構，遵循下列步驟︰

1. 以滑鼠右鍵按一下 [下] 按鈕，然後選取 [」 開啟連結新索引標籤中的 」 或 「 新視窗中的開啟連結 」:  
[![部署至 Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-vpn-er%2Fazuredeploy.json)

2. 等待連結来開啟在 Azure 入口網站，然後遵循下列步驟︰ 
  - **資源群組**名稱已經定義參數檔案中，**建立新檔案**，然後選取輸入`ra-hybrid-vpn-er-rg`在文字方塊中。
  - 從 [**位置**] 下拉式方塊中選取的地區。
  - 無法編輯**範本根 Uri**或**參數根 Uri**的 [文字] 方塊。
  - 檢閱條款與條件，然後按一下 [**我同意條款與條件上述**] 核取方塊。
  - 按一下 [**購買**] 按鈕。

3. 等到完成部署。

4. 以滑鼠右鍵按一下 [下] 按鈕，然後選取 [」 開啟連結新索引標籤中的 」 或 「 新視窗中的開啟連結 」:  
[![部署至 Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-vpn-er%2Fazuredeploy-expressRouteCircuit.json)

5. 等待的連結開啟在 Azure 入口網站，然後輸入，然後遵循下列步驟︰ 
  - 在 [**資源群組**] 區段中選取 [**使用現有的**然後輸入`ra-hybrid-vpn-er-rg`在文字方塊中。
  - 從 [**位置**] 下拉式方塊中選取的地區。
  - 無法編輯**範本根 Uri**或**參數根 Uri**的 [文字] 方塊。
  - 檢閱條款與條件，然後按一下 [**我同意條款與條件上述**] 核取方塊。
  - 按一下 [**購買**] 按鈕。

<!-- links -->

[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[vpn-appliance]: ../vpn-gateway/vpn-gateway-about-vpn-devices.md
[azure-vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[connect-to-an-Azure-vnet]: https://technet.microsoft.com/library/dn786406.aspx
[azure-network-security-group]: ../virtual-network/virtual-networks-nsg.md
[getting-started-with-azure-security]: ./../security/azure-security-getting-started.md
[expressroute-prereq]: ../expressroute/expressroute-prerequisites.md
[implementing-expressroute]: ./guidance-hybrid-network-expressroute.md#implementing-this-architecture
[implementing-vpn]: ./guidance-hybrid-network-vpn.md#implementing-this-architecture
[guidance-expressroute]: ./guidance-hybrid-network-expressroute.md
[guidance-vpn]: ./guidance-hybrid-network-vpn.md
[best-practices-security]: ../best-practices-network-security.md
[solution-script]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/Deploy-ReferenceArchitecture.ps1
[solution-script-bash]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/deploy-reference-architecture.sh
[vnet-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/parameters/virtualNetwork.parameters.json
[virtualnetworkgateway-vpn-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/parameters/virtualNetworkGateway-vpn.parameters.json
[virtualnetworkgateway-expressroute-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/parameters/virtualNetworkGateway-expressRoute.parameters.json
[er-circuit-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/parameters/expressRouteCircuit.parameters.json
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[naming conventions]: ./guidance-naming-conventions.md
[azure-cli]: https://azure.microsoft.com/documentation/articles/xplat-cli-install/
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[0]: ./media/blueprints/hybrid-network-expressroute-vpn-failover.png "使用 ExpressRoute 及 VPN 閘道器的高度可用的混合式網路架構的結構"
[ARM-Templates]: https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/
