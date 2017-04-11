<properties
   pageTitle="實作與 Azure ExpressRoute 的混合式網路架構 |參照架構 |Microsoft Azure"
   description="如何實作橫跨 Azure 虛擬網路和連線 Azure ExpressRoute 內部網路的安全網站間的網路架構。"
   services=""
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="telmos"/>

# <a name="implementing-a-hybrid-network-architecture-with-azure-expressroute"></a>執行混合式網路架構與 Azure ExpressRoute

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

本文將說明內部部署網路使用連線到虛擬網路上 Azure ExpressRoute 最佳的作法。 ExpressRoute 連線使用協力廠商連線提供者所提供的私人專用的連線。 私人連線將您的內部部署網路延伸到 Azure 存取 Azure、 公用端點 PaaS 服務以及 Office365 SaaS 服務中使用您自己 IaaS 基礎結構。 使用連線至單一 Azure 虛擬網路 (VNet) 使用什麼稱為私人對等的 ExpressRoute 解釋這份文件。

> [AZURE.NOTE] Azure 有兩種不同的部署模型︰[資源管理員][resource-manager-overview]和傳統。 這個藍圖使用資源管理員，Microsoft 建議新的部署。

此結構的一般的使用案例包括︰

- 混合式應用程式之間的內部部署網路和 Azure 分佈工作負載的位置。

- 執行需要高性的大型、 重要的工作負載的應用程式。

- 大型備份與還原設備必須開儲存的資料。

- 處理大型資料的工作量。

- 使用 Azure 損毀修復網站。

> [AZURE.NOTE] [ExpressRoute 技術概觀][expressroute-technical-overview]提供 ExpressRoute 簡介。

## <a name="architecture-diagram"></a>架構圖表

下圖會醒目提示在這種架構的重要元件︰

> Visio 文件包含此架構圖是可從[Microsoft 下載中心][visio-download]。 這是在 「 混合式網路-永不 」] 頁面上。

![[0]][0]

- **Azure 虛擬網路 (VNets)。** 每個 VNet 在單一 Azure 區域中，並可以主控多個應用程式層。 應用程式層可以使用子網路中每個 VNet 區隔及/或網路安全性群組 (NSGs)。 

- **Azure 公用服務。** 這些是可以利用混合式應用程式中的 Azure 服務。 這些服務，也會出現在公用網際網路，但是透過 ExpressRoute 電路存取這些提供低延遲和更可預測效能由於流量不會繼續透過網際網路。 連線使用執行的**公用對等**，擁有您的組織或您的連線提供者所提供的地址。 

- **Office 365 服務。** 這些是由 Microsoft 提供的服務與可公開使用 Office 365 應用程式。 連線使用執行的**Microsoft 對等**，擁有您的組織或您的連線提供者所提供的地址。

>[AZURE.NOTE] 您也可以直接與 Microsoft CRM Online 透過 Microsoft 對等連線。

- **公司網路內部部署。** 這是一個網路的電腦和裝置，透過私人執行在組織內的區域網路連線。

- **本機邊緣路由器。** 這些都是內部部署網路連線提供者管理的電路路由器。 根據您的連線佈建方式，您需要提供路由器所使用的公用 IP 位址。 

- **Microsoft edge 路由器。** 這些是主動-主動高度可用設定中的兩個路由器。 這類路由器啟用連線提供者，其電路直接連接到其資料中心。 根據您的連線佈建方式，您需要提供路由器所使用的公用 IP 位址。

- **ExpressRoute 電路。** 這是階層 2 或連線提供者所提供階層 3 電路 Azure 邊緣路由器到該連接內部部署網路。 電路使用連線提供者管理的硬體基礎結構。

- **連線提供者。** 這些是公司提供連線使用的圖層 2 的資料中心和 Azure 資料中心之間的圖層 3 連線。

## <a name="recommendations"></a>建議

Azure 提供許多不同的資源和資源類型，因此此參照架構可提供許多不同的方式。 我們提供安裝遵循這些建議參考架構 Azure 資源管理員範本。 如果您選擇，建立您自己參考架構，您應該遵循上述建議，除非您有建議不符合特定需求。

### <a name="connectivity-providers"></a>連線提供者

選取適當的 ExpressRoute 連線提供者，您所在位置。 若要取得連線提供者提供所在位置的清單，請使用下列 PowerShell 的 Azure 命令︰

```powershell
Get-AzureRmExpressRouteServiceProvider
```

ExpressRoute 連線提供者會連線到 Microsoft 資料中心，以下列方式︰

- **共同位於雲端 exchange。** 如果您共同位於雲端 exchange 設備，您就可以排序虛擬代管提供者的乙太網路 exchange 透過 Microsoft 雲端的跨連線。 第 2 層跨連線或受管理圖層 3 跨-之間的連線內共同位置基礎結構與 Microsoft 雲端，可以提供代管提供者。

- **點對點乙太網路連線。** 您可以透過點對點乙太網路連結 Microsoft 雲端連線您內部部署資料中心/辦公室。 點對點乙太網路提供者可以提供第 2 層連線，或管理您的網站與 Microsoft 雲端之間的圖層 3 連線。

- **任何-任何 (IPVPN) 網路。** 您可以整合您 WAN 與 Microsoft 雲端。 IPVPN 提供者 (通常是 MPLS VPN) 提供任何-任何分公司和資料中心之間的連線。 可以相互連接的 Microsoft cloud，至，使其看起來就像其他任何分公司您 WAN。 WAN 提供者通常會提供受管理的圖層 3 連線。

如需連線提供者的詳細資訊，請參閱[ExpressRoute 電路與路由網域][connectivity-providers]。

### <a name="expressroute-circuit"></a>ExpressRoute 電路

確保您的組織有符合[ExpressRoute 必備需求][expressroute-prereqs]需連線至 Azure。

如果您尚未這麼做，新增名為子網路`GatewaySubnet`至您的 Azure VNet 並建立使用 Azure VPN 閘道器服務 ExpressRoute 虛擬網路閘道器。 如需更多關於此程序的詳細資訊，請參閱[ExpressRoute 工作流程電路佈建及電路狀態][ExpressRoute-provisioning]。

建立 ExpressRoute 電路如下所示︰

1. 執行下列動作的 PowerShell 命令︰

    ```powershell
    New-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>> -Location <<location>> -SkuTier <<sku-tier>> -SkuFamily <<sku-family>> -ServiceProviderName <<service-provider-name>> -PeeringLocation <<peering-location>> -BandwidthInMbps <<bandwidth-in-mbps>>
    ```

2. 傳送`ServiceKey`新電路服務提供者。

3. 佈建電路的提供者，請等候。 您可以使用下列 PowerShell 命令，以確認電路佈建狀態︰

    ```powershell
    Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
    ```

`Provisioning state`欄位中`Service Provider`的輸出] 區段會變更`NotProvisioned`至`Provisioned`電路時好。

>[AZURE.NOTE]如果您使用的圖層 3 連線，提供者應設定和管理路由。您提供啟用實作適當的路由的提供者所需的資訊。

如果您使用的第 2 層連線，請遵循下列步驟︰

1. 保留兩個/30 子網路組成的有效的公用 IP 位址，每一種類型的對等您想要實作。 這些/30 子網路將用來提供路由器電路所用的 IP 位址。 如果您在實作私人公用，與 Microsoft 對等，您必須 6 /30 子網路有效的公用 IP 位址。     

2. 設定的 ExpressRoute 電路路由。 您需要執行以下命令，每一種類型的對等您想要設定 （私人與公用，Microsoft）。

    >[AZURE.NOTE]請參閱[建立及修改的 ExpressRoute 電路路由][configure-expressroute-routing]如需詳細資訊。 若要新增路由流量的對等的網路使用下列 PowerShell 命令︰

    ```powershell
    Set-AzureRmExpressRouteCircuitPeeringConfig -Name <<peering-name>> -Circuit <<circuit-name>> -PeeringType <<peering-type>> -PeerASN <<peer-asn>> -PrimaryPeerAddressPrefix <<primary-peer-address-prefix>> -SecondaryPeerAddressPrefix <<secondary-peer-address-prefix>> -VlanId <<vlan-id>>

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit <<circuit-name>>
    ```

3. 保留有效的公用 IP 位址，用於公用，針對 NAT 和 Microsoft 對等的另一個資料庫。 建議您有不同的集區的每個對等。 指定連線提供者，資料庫，讓他們可以設定 BGP 廣告的範圍。

[連結您在雲端的私人 VNet(s) ExpressRoute 電路][link-vnet-to-expressroute]。 使用下列 PowerShell 命令︰

```
$circuit = Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
$gw = Get-AzureRmVirtualNetworkGateway -Name <<gateway-name>> -ResourceGroupName <<resource-group>>
New-AzureRmVirtualNetworkGatewayConnection -Name <<connection-name>> -ResourceGroupName <<resource-group>> -Location <<location> -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
```

請注意下列重點︰

- ExpressRoute 路由的資訊，您的網路和 Azure 之間交換用於框線閘道器通訊協定 (BGP)。

- 您可以連線位於不同的區域，以相同的 ExpressRoute 電路，只要所有 VNets 和 ExpressRoute 電路位於相同的淉區域內的多個 VNets。

## <a name="scalability-considerations"></a>延展性考量

ExpressRoute 電路提供高頻寬路徑網路之間。 一般而言，較高的頻寬更大的成本。 雖然部分提供者可讓您要變更您的頻寬，請務必挑選初始的頻寬，超過您的需求，並提供成長的空間。 您需要在未來增加的頻寬，您會保留下列兩個選項。

增加頻寬。 請記住並非所有提供者允許您執行這項作業以動態方式。 您應該不需要執行此盡可能。 但是，如果有需要之後檢查您的提供者，請執行下列命令。

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
$ckt.ServiceProviderProperties.BandwidthInMbps = <<bandwidth-in-mbps>>
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

您可以增加的頻寬，而不會遺失的連線。 降級頻寬會導致中斷連線。 您有刪除線電路並重新建立新的設定。

如果您使用的標準的 SKU 的 ExpressRoute 並需要升級至進階版，或變更您正在您價格計劃 （計量付費或無限制），請執行下列命令。 `Sku.Tier`屬性可`Standard`或`Premium`;`Sku.Name`屬性可`MeteredData`或`UnlimitedData`。

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>

$ckt.Sku.Tier = "Premium"
$ckt.Sku.Family = "MeteredData"
$ckt.Sku.Name = "Premium_MeteredData"

 Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

>[AZURE.IMPORTANT] 請確定`Sku.Name`屬性相符的項目`Sku.Tier`和`Sku.Family`。 如果您變更系列及層，但不是名稱，您的連線會停用。

您可以在升級的 SKU，而不中斷，但您無法從不受限制的價格計劃以計量付費切換。 降級的 SKU，當您頻寬使用量必須保持標準 SKU 的預設限制。

> [AZURE.NOTE] ExpressRoute 提供兩個定價給客戶，根據測量或不受限制的資料。 請參閱[ExpressRoute 價格][expressroute-pricing]如需詳細資訊。 費用電路頻寬而有所不同。 可用的頻寬可能會有所不同提供者以提供者。 使用`Get-AzureRmExpressRouteServiceProvider`指令程式，請參閱可用的提供者在您的地區和他們提供頻寬。

單一 ExpressRoute 電路可支援 peerings 及 VNet 連結。 請參閱[ExpressRoute 限制][expressroute-limits]如需詳細資訊。

額外的費用，ExpressRoute 進階版附加元件提供︰

- 最多 10000 個每電路路由。 不 ExpressRoute 進階版附加元件，限制為每個電路 4000 路由。

- 全域的連線，啟用位於世界存取資源在任何區域，而不是在同一個大陸只區域中的任一處 ExpressRoute 電路。

- 增加每 10 較大的限制，根據的電路頻寬的電路 VNet 連結的數目。

ExpressRoute 電路被設計用兩次的頻寬限制的您採購沒有其他成本的暫時網路高速量。 這被達成使用重複的連結。 不過，並非所有連線提供者都支援這項功能。請確認您的連線提供者啟用之前，根據這項功能。

## <a name="availability-considerations"></a>可用性考量

您可以設定高可用性 Azure 連線，以不同的方式，取決於您使用，提供者的類型和數字的 ExpressRoute 電路與您願意設定虛擬網路閘道器的連線。 指向下列摘要您可用的選項︰

- ExpressRoute 不支援路由器重複通訊協定，例如 HSRP 和 VRRP 實作可用性。 不過，它會使用重複的配對的每個對等的 BGP 工作階段。 若要加快高度可用連線到您的網路，Microsoft Azure 佈建份您的兩個路由器 （Microsoft edge 的一部分），在作用中作用中設定兩個重複的連接埠。

- 如果您使用的第 2 層連線，部署多餘路由器內部網路的作用中作用中的設定。 連線的主要電路路由器，及其他次要電路。 這可讓您連線的兩端的高度可用連線。 這是有需要，如果您需要 ExpressRoute SLA。 請參閱[Azure ExpressRoute SLA] [sla-for-expressroute]如需詳細資訊。

下圖顯示的多餘的內部部署路由器設定連線到主要和次要電路。 每個電路控點的流量的外面公用與私人的對等 (每個對等指定 /30 一組位址空格、 上一節所述)。

![[1]][1]

如果您使用的圖層 3 連線，請確認，它提供重複 BGP 處理可用性為您的工作階段。

虛擬網路可以連接到多個 ExpressRoute 電路，每個電路可提供不同的服務提供者。 這種方法提供其他高可用性和損毀修復功能。

將網站-VPN 設定的 ExpressRoute 容錯移轉路徑。 這只適用於私人對等。 如需 Azure 和 Office 365 服務，網際網路是唯一的容錯移轉路徑。

根據預設，BGP 工作階段，請使用 60 秒的閒置逾時值。 一旦工作階段逾時的 3 次，路由標示為 [無法使用，，而且所有流量重新都導向至剩餘路由器。 此 180 第二逾時可能太長，為應用程式。 在這種情況下，您可以變更您 BGP 逾時設定的內部部署路由器較小的值。

## <a name="manageability-considerations"></a>管理能力考量

您可以使用[Azure 連線工具組 (AzureCT)] [azurect]監控您的內部部署資料中心和 Azure 之間的連線。

## <a name="security-considerations"></a>安全性考量

您可以設定 Azure 連線的安全性選項，以不同的方式，取決於您的安全性考量和法規遵循的需求。 指向下列摘要安全性選項。

ExpressRoute 運作階層 3。 使用正確的資源限制流量網路安全性應用裝置無法威脅應用程式層。 此外，使用公用對等的 ExpressRoute 連線只可以從內部部署啟動。 如此可避免惡意服務存取與危害公用網際網路的內部部署資料。

若要提高安全性，新增內部部署網路之間的提供者邊緣路由器網路安全性應用裝置。 這是為了 VNet 限制的未經授權的流量流程︰

![[2]][2]

稽核或法規遵循用途，可能需要禁止元件執行中 VNet 網際網路直接存取及實作[強制通道][forced-tuneling]。 在此情況下，應該網際網路流量重新導向回透過 proxy 執行位置要稽核的內部部署。 您可以設定 proxy 封鎖未經授權的流量書寫方向，並篩選潛在惡意輸入的流量。

![[3]][3]

根據預設，Azure Vm 公開提供登入存取的管理-RDP 和遠端 Powershell 的 Windows Vm SSH Linux 型 Vm 部署透過 Azure 入口網站時所用的端點。 最大化安全性，、 執行啟用的公用 IP 位址，為您的 Vm，並使用 NSGs，以確保這些 Vm 不可以公開存取。 Vm 應該僅能使用的內部 IP 位址。 這些位址可透過 ExpressRoute 網路，讓內部部署 DevOps 員工來執行任何必要的設定] 或 [進行的維修作業。

如果外部網路，您必須針對 Vm 公開管理結束點，使用 NSGs 及/或存取控制清單來限制的 IP 位址或網路 whitelist 下列連接埠的可見性。

## <a name="troubleshooting-considerations"></a>疑難排解考量

如果先前正常運作的 ExpressRoute 電路現在無法連線時，如果沒有任何設定變更內部部署或您私人的 VNet，您可能需要連絡連線提供者，並使用這些修正問題。 您也可以使用下列 PowerShell 的 Azure 命令來執行某些有限的檢查，幫助您判斷問題可能位於位置︰

- 確認已佈建電路︰

```powershell
Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
```

這個命令的輸出會顯示您的電路數個屬性包括`ProvisioningState`， `CircuitProvisioningState`，及`ServiceProviderProvisioningState`如下所示。

```
ProvisioningState                : Succeeded
Sku                              : {
                                     "Name": "Standard_MeteredData",
                                     "Tier": "Standard",
                                     "Family": "MeteredData"
                                   }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : NotProvisioned
```

如果`ProvisioningState`未設定為 [`Succeeded`您嘗試建立新的電路之後，請使用下列命令移除電路，然後再試一次建立。

```powershell
Remove-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
```

如果您的提供者必須已經提供電路，以及`ProvisioningState`設定為 [ `Failed`，或`CircuitProvisioningState`不`Enabled`，連絡您的提供者取得協助。

## <a name="solution-deployment"></a>解決方案部署

如果您有已適當的網路應用裝置使用現有的內部部署基礎結構時，您可以部署參考架構，遵循下列步驟︰

如果您有已 VPN 應用裝置使用現有的內部部署基礎結構時，您可以部署參考架構，遵循下列步驟︰

1. 以滑鼠右鍵按一下 [下] 按鈕，然後選取 [」 開啟連結新索引標籤中的 」 或 「 新視窗中的開啟連結 」:  
[![部署至 Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-er%2Fazuredeploy.json)

2. 等待連結来開啟在 Azure 入口網站，然後遵循下列步驟︰ 
  - **資源群組**名稱已經定義參數檔案中，**建立新檔案**，然後選取輸入`ra-hybrid-er-rg`在文字方塊中。
  - 從 [**位置**] 下拉式方塊中選取的地區。
  - 無法編輯**範本根 Uri**或**參數根 Uri**的 [文字] 方塊。
  - 檢閱條款與條件，然後按一下 [**我同意條款與條件上述**] 核取方塊。
  - 按一下 [**購買**] 按鈕。

3. 等到完成部署。

4. 以滑鼠右鍵按一下 [下] 按鈕，然後選取 [」 開啟連結新索引標籤中的 」 或 「 新視窗中的開啟連結 」:  
[![部署至 Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-er%2Fazuredeploy-expressRouteCircuit.json)

5. 等待連結来開啟在 Azure 入口網站，然後遵循下列步驟︰ 
  - 在 [**資源群組**] 區段中選取 [**使用現有的**然後輸入`ra-hybrid-er-rg`在文字方塊中。
  - 從 [**位置**] 下拉式方塊中選取的地區。
  - 無法編輯**範本根 Uri**或**參數根 Uri**的 [文字] 方塊。
  - 檢閱條款與條件，然後按一下 [**我同意條款與條件上述**] 核取方塊。
  - 按一下 [**購買**] 按鈕。

6. 等到完成部署。

## <a name="next-steps"></a>後續步驟

- 請參閱[實作高度可用的混合式網路架構][highly-available-network-architecture]的增加混合式網路的詳細資訊，根據 ExpressRoute 無法透過 VPN 連線。

<!-- links -->
[expressroute-technical-overview]: ../expressroute/expressroute-introduction.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[azure-powershell]: ../powershell-azure-resource-manager.md
[expressroute-prereqs]: ../expressroute/expressroute-prerequisites.md
[configure-expressroute-routing]: ../expressroute/expressroute-howto-routing-arm.md
[sla-for-expressroute]: https://azure.microsoft.com/support/legal/sla/expressroute/v1_0/
[link-vnet-to-expressroute]: ../expressroute/expressroute-howto-linkvnet-arm.md
[ExpressRoute-provisioning]: ../expressroute/expressroute-workflows.md
[expressroute-pricing]: https://azure.microsoft.com/pricing/details/expressroute/
[expressroute-limits]: ../azure-subscription-service-limits.md#networking-limits
[sample-script]: #sample-solution-script
[connectivity-providers]: ../expressroute/expressroute-introduction.md#how-can-i-connect-my-network-to-microsoft-using-expressroute
[azurect]: https://github.com/Azure/NetworkMonitoring/tree/master/AzureCT
[forced-tuneling]: ./guidance-iaas-ra-secure-vnet-hybrid.md
[highly-available-network-architecture]: ./guidance-hybrid-network-expressroute-vpn-failover.md
[arm-templates]: ../resource-group-authoring-templates.md
[naming-conventions]: ./guidance-naming-conventions.md
[solution-script]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-er/Deploy-ReferenceArchitecture.ps1
[solution-script-bash]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-er/deploy-reference-architecture.sh
[vnet-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-er/parameters/virtualNetwork.parameters.json
[virtualnetworkgateway-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-er/parameters/virtualNetworkGateway.parameters.json
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[er-circuit-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-er/parameters/expressRouteCircuit.parameters.json
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[azure-cli]: https://azure.microsoft.com/documentation/articles/xplat-cli-install/
[0]: ./media/guidance-hybrid-network-expressroute/figure1.png "混合式網路架構使用 Azure ExpressRoute"
[1]: ./media/guidance-hybrid-network-expressroute/figure2.png "重複的路由器使用 ExpressRoute 主要和次要電路"
[2]: ./media/guidance-hybrid-network-expressroute/figure3.png "新增至內部網路的安全性裝置"
[3]: ./media/guidance-hybrid-network-expressroute/figure4.png "使用強制稽核網際網路繫結流量通道"
[4]: ./media/guidance-hybrid-network-expressroute/figure5.png "找出 ExpressRoute 電路的 ServiceKey"
