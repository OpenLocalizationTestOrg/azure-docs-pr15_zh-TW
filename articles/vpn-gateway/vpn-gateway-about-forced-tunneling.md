<properties 
   pageTitle="設定為使用傳統的部署模型至網站連線強制通道 |Microsoft Azure"
   description="如何重新導向或所有的網際網路繫結流量 ' 強制' 返回您內部部署的位置。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/10/2016"
   ms.author="cherylmc" />

# <a name="configure-forced-tunneling-using-the-classic-deployment-model"></a>設定使用傳統的部署模型強制通道

> [AZURE.SELECTOR]
- [PowerShell-傳統](vpn-gateway-about-forced-tunneling.md)
- [PowerShell-資源管理員](vpn-gateway-forced-tunneling-rm.md)

強制通道可讓您重新導向或 「 強制 」 的所有網際網路繫結流量透過檢查及稽核網站-VPN 通道都返回您內部部署的位置。 這是大部分的企業 IT 重要的安全性需求原則。 

不強制通道，從您的 Vm Azure 中的網際網路繫結流量會永遠從 Azure 網路基礎結構直接出往返至網際網路，而不允許您檢查或稽核流量的選項。 未經授權的存取網際網路可能會導致資訊公開或其他類型的安全性漏洞。

本文會引導您完成設定強制通道建立使用傳統的部署模型的虛擬網路。 

**關於 Azure 部署模型**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

**部署模型和工具強制通道**

您可以設定強制通道連線傳統部署模型和資源管理員部署模型兩者均適用。 請參閱下表中的詳細資訊。 為新的文章，新的部署模型和其他工具會變成適用於此設定，我們就會更新此表格。 使用文件時，我們直接從連結資料表。

[AZURE.INCLUDE [vpn-gateway-forcedtunnel](../../includes/vpn-gateway-table-forcedtunnel-include.md)] 


## <a name="requirements-and-considerations"></a>需求和考量

強制通道 Azure 中透過虛擬網路的使用者定義路由 (UDR) 設定。 Azure VPN 閘道器的預設路由，就會將流量重新導向至內部部署網站來表示。 下一節列出 Azure 虛擬網路上的傳遞的資料表，而目前限制︰


-  每個虛擬網路的子網路會有一個內建的系統路由的資料表。 系統路由表有下列三個群組的路由︰

    - **本機 VNet 路由︰**直接到目的地 Vm 中相同的虛擬網路
    
    - **上部署路由︰**Azure VPN 閘道
    
    - **預設路由︰**直接至網際網路。 指向 [私用的 IP 位址未涵蓋的上一個兩個路由封包會遭到捨棄。


-  版的使用者定義的路徑，您可以建立將預設路由，路由資料表，然後關聯到您 VNet 之後，若要啟用強制通道這些子網路上的 [路由表格。

- 您需要設定 「 預設網站 」 之間跨內部部署本機網站連線至虛擬網路。

- 強制通道必須具有動態路由 VPN 閘道器 （不靜態閘道） VNet 與相關聯。
 
- 強制通道 ExpressRoute 未設定這個機制，透過，但反而會啟用通知的預設路由，透過 ExpressRoute BGP 對等工作階段。 請參閱[ExpressRoute 文件](https://azure.microsoft.com/documentation/services/expressroute/)，詳細的資訊。



## <a name="configuration-overview"></a>設定概觀

在下列範例中，不會被強迫子網路 Frontend 通道。 接受和回應客戶要求從網際網路直接可以繼續 Frontend 子網路中的工作量。 在多層和後端子網路強制通道。 從下列兩個子網路任何輸出連線到網際網路會強制或重新導向到其中一個 S2S VPN 通道透過內部部署網站。

這個選項可讓您限制，並檢查您的虛擬機器中的網際網路存取或同時，繼續啟用您所需的多層服務架構在雲端中 Azure 服務。 您也可以套用強制通道到整個虛擬網路如果虛擬網路中不有任何網際網路的工作量。


![強制通道](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)



## <a name="before-you-begin"></a>開始之前

確認您有下列項目，才能開始設定。

- Azure 的訂閱。 如果您還沒有 Azure 的訂閱，您可以啟動您的[MSDN 訂閱者優惠](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊設定[免費的帳戶](https://azure.microsoft.com/pricing/free-trial/)。

- 設定的虛擬網路。 

- Azure PowerShell cmdlet 最新版本。 如需安裝的 PowerShell 指令程式的相關資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md) 。


## <a name="configure-forced-tunneling"></a>設定 [強制通道

下列程序將協助您指定的虛擬網路強制通道。 設定步驟會對應到 VNet 網路設定檔。



    <VirtualNetworkSite name="MultiTier-VNet" Location="North Europe">
     <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="DefaultSiteHQ">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch1">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch2">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch3">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSite>

在此範例中，虛擬網路 「 多層 VNet 」 有三個子網路︰ *Frontend* *Midtier*，與*後端*子網路有四個交叉部署連線︰ *DefaultSiteHQ*及三個*分支*。 

會將*DefaultSiteHQ*設為預設網站連線強制通道] 的步驟，並將其設定 Midtier，若要使用的後端子網路強制通道。


1. 建立路由表格。 您可以使用下列 cmdlet，建立您傳送的資料表。

        New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"

2. 新增預設路由至路由的資料表。 

    下列範例會預設路由至路由在步驟 1 建立的資料表。 請注意，只傳送支援是目的地 」 0.0.0.0/0 」 以 「 VPNGateway 」 個躍點前置字元。
 
        Get-AzureRouteTable -Name "MyRouteTable" | Set-AzureRoute –RouteTable "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway

3. 建立子網路路由表格之間的關聯。 

    路由資料表會建立並新增路由之後，請使用下列範例新增，或建立 VNet 子網路路由表格之間的關聯。 範例 Midtier 和後端子網路的 VNet 多層-VNet 中新增路由表 」 MyRouteTable 」。

        Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"

        Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"

4. 強制通道的請指定預設網站。 

    在上述步驟中，指令碼範例 cmdlet 建立路由表與兩個 VNet 子路由表格。 剩餘的步驟是將預設網站或通道選取本機網站之間的虛擬網路的多網站連線。

        $DefaultSite = @("DefaultSiteHQ")
        Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"

## <a name="additional-powershell-cmdlets"></a>其他 PowerShell cmdlet

### <a name="to-delete-a-route-table"></a>若要刪除路由表格

    Remove-AzureRouteTable -Name <routeTableName>

### <a name="to-list-a-route-table"></a>清單路由表格

    Get-AzureRouteTable [-Name <routeTableName> [-DetailLevel <detailLevel>]]

### <a name="to-delete-a-route-from-a-route-table"></a>若要刪除路由傳送表格

    Remove-AzureRouteTable –Name <routeTableName>

### <a name="to-remove-a-route-from-a-subnet"></a>若要移除子網路路徑

    Remove-AzureSubnetRouteTable –VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>

### <a name="to-list-the-route-table-associated-with-a-subnet"></a>清單與子網路相關聯的路由表
    
    Get-AzureSubnetRouteTable -VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>

### <a name="to-remove-a-default-site-from-a-vnet-vpn-gateway"></a>若要移除 VNet VPN 閘道器的預設網站

    Remove-AzureVnetGatewayDefaultSite -VNetName <virtualNetworkName>






