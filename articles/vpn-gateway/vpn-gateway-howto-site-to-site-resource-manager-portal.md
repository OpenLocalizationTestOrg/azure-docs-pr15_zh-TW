<properties
   pageTitle="使用 Azure 資源管理員和 Azure 入口網站至網站 VPN 連線建立虛擬網路 |Microsoft Azure"
   description="如何建立 VNet 使用資源管理員部署模型，並將其連線至您的本機內部部署網路使用 S2S VPN 閘道器連線。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/14/2016"
   ms.author="cherylmc"/>

# <a name="create-a-vnet-with-a-site-to-site-connection-using-the-azure-portal"></a>建立 VNet 使用 Azure 入口網站的連線

> [AZURE.SELECTOR]
- [資源管理員-Azure 入口網站](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [資源管理員-PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
- [傳統-傳統入口網站](vpn-gateway-site-to-site-create.md)


本文會引導您建立虛擬網路和網站-VPN 閘道器連線到內部部署網路使用 Azure 資源管理員部署模型和 Azure 入口網站。 跨內部部署與混合式用於網站的連線設定。

![圖表](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/s2srmportal.png)


### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>部署模型和網站的連線的方法

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

下表顯示目前可用的部署模型和網站的網站設定的方法。 當有包含設定步驟的文章時，我們連結直接從這個資料表。

[AZURE.INCLUDE [site-to-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>其他設定 

如果您要放在一起，連線 VNets，但不是建立連線到內部部署的位置，請參閱[設定 VNet-VNet 連線](vpn-gateway-vnet-vnet-rm-ps.md)。 如果您想要新增至網站連線至 VNet 已有連線，請參閱[新增現有的 VPN 閘道器連線 VNet S2S 連線](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)。

## <a name="before-you-begin"></a>開始之前

確認您擁有下列項目，才能開始您的設定︰

- 相容的 VPN 裝置，可以設定讓它的人。 請參閱[關於 VPN 裝置](vpn-gateway-about-vpn-devices.md)。 如果您不熟悉設定您要有 vpn 才能的裝置，或不熟悉 IP 位址範圍位於您的內部部署網路設定，您需要進行共同作業的人可以為您提供這些詳細資料。

- 對外公開 IP 位址 VPN 裝置。 Nat 找不到這個 IP 位址
    
- Azure 的訂閱。 如果您還沒有 Azure 的訂閱，您可以啟動您的[MSDN 訂閱者優惠](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊設定[免費的帳戶](http://azure.microsoft.com/pricing/free-trial/)。

### <a name="values"></a>在這個練習範例設定值


練習使用這些步驟，您可以使用範例設定值︰

- **VNet 名稱︰**TestVNet1
- **位址空間︰** 10.11.0.0/16 和 10.12.0.0/16
- **子網路︰**
    - FrontEnd: 10.11.0.0/24
    - 後端︰ 10.12.0.0/24
    - GatewaySubnet: 10.12.255.0/27
- **資源群組︰**TestRG1
- **位置︰**設定適用於美國
- **DNS 伺服器︰** 8.8.8.8
- **閘道器名稱︰**VNet1GW
- **公用 IP:**VNet1GWIP
- **VPN 類型︰**傳送型
- **連線類型︰**網站的網站 」 (IPsec)
- **閘道器類型︰**VPN
- **區域網路閘道器名稱︰**站台 2
- **連線名稱︰**VNet1toSite2


## <a name="CreatVNet"></a>1.建立虛擬網路 

如果您已經有 VNet，確認您要有 vpn 才能閘道設計與相容的設定。 注意特定可能會與其他網路重疊任何子網路。 如果您有重疊的子網路，您的連線無法正常運作。 如果您 VNet 設定以正確的設定，您就可以開始在[指定的 DNS 伺服器](#dns)] 區段中的步驟。

### <a name="to-create-a-virtual-network"></a>若要建立虛擬網路

[AZURE.INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]  

## <a name="subnets"></a>2.新增額外的位址空間和子網路

您可以新增額外的位址空間和子網路您 VNet，只要建立之後。

[AZURE.INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)] 

## <a name="dns"></a>3.指定 DNS 伺服器

### <a name="to-specify-a-dns-server"></a>若要指定 DNS 伺服器

[AZURE.INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="gatewaysubnet"></a>4.建立閘道器子網路

之前將虛擬的網路連線到閘道器，您必須建立閘道器網路的子虛擬您要連線。 如果可能的話，最好建立閘道器子網路使用 CIDR 區塊 /28 或 /27 才能提供足夠的 IP 位址，以容納其他未來設定需求。

如果您正在建立練習此設定，請參閱以下的[值](#values)建立閘道器子網路時。

### <a name="to-create-a-gateway-subnet"></a>若要建立閘道器子網路


[AZURE.INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="VNetGateway"></a>5。 建立虛擬網路閘道器

如果您正在建立練習此設定，您可以參照的[範例設定值](#values)。

### <a name="to-create-a-virtual-network-gateway"></a>若要建立虛擬網路閘道器

[AZURE.INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="LocalNetworkGateway"></a>6。 建立區域網路閘道器

區域網路閘道是指您內部部署的位置。 區域網路閘道器的名稱為依據 Azure 參考。 

如果您正在建立練習此設定，您可以參照的[範例設定值](#values)。

### <a name="to-create-a-local-network-gateway"></a>若要建立的區域網路閘道器

[AZURE.INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## <a name="VPNDevice"></a>7。 設定 VPN 裝置

[AZURE.INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="CreateConnection"></a>8。 建立網站-VPN 連線

建立網站為網站之間的連線虛擬網路閘道和 VPN 裝置。 請務必的值取代為您自己。 共用的金鑰必須符合您所使用 VPN 裝置設定的值。 

在開始之前此節，請確認的虛擬網路閘道與本機網路閘道器已經建立完成。 如果您正在建立練習此設定，請參閱以下的[值](#values)建立您的連線時。

### <a name="to-create-the-vpn-connection"></a>若要建立 VPN 連線


[AZURE.INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-rm-portal-include.md)]

## <a name="VerifyConnection"></a>9。 驗證 VPN 連線

您可以驗證您的 VPN 連線在入口網站，或使用 PowerShell。

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="next-steps"></a>後續步驟

- 完成您的連線之後，您可以新增到您的虛擬網路的虛擬機器。 請參閱虛擬機器[學習路徑](https://azure.microsoft.com/documentation/learning-paths/virtual-machines)如需詳細資訊。

- BGP 的相關資訊，請參閱[BGP 概觀](vpn-gateway-bgp-overview.md)以及[如何設定 BGP](vpn-gateway-bgp-resource-manager-ps.md)。
