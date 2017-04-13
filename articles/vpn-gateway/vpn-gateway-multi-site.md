<properties 
   pageTitle="虛擬網路連線到多個網站使用 VPN 閘道器與 PowerShell |Microsoft Azure"
   description="本文會引導您執行 VPN 閘道器用傳統部署模型虛擬網路連線多個本機內部部署網站。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
   tags="azure-service-management"/>

<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/11/2016"
   ms.author="yushwang" />

# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>新增網站至網站連線至現有的 VPN 閘道器連線 VNet

> [AZURE.SELECTOR]
- [資源管理員-入口網站](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
- [傳統-PowerShell](vpn-gateway-multi-site.md)

本文會引導您使用 PowerShell 來新增至網站的 (S2S) 連線 VPN 閘道器已有現有的連線。 此連線類型通常稱為 「 多的網站 」 設定。 

本文適用於虛擬網路建立使用傳統的部署模型 （也稱為服務管理）。 下列步驟不適用於 ExpressRoute /-網站共存連線設定。 如需有關共存連線資訊，請參閱[ExpressRoute/S2S 共存連線](../expressroute/expressroute-howto-coexist-classic.md)。

### <a name="deployment-models-and-methods"></a>部署模型和方法

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

我們更新此資料表為新的文件和其他工具，就可以使用此設定。 使用文件時，我們連結直接從這個資料表。

[AZURE.INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)] 



## <a name="about-connecting"></a>連線的相關

您可以在單一虛擬網路連線多個內部部署網站。 這是建立混合式雲端解決方案，特別是吸引人。 建立其他網站-連線非常類似，建立您 Azure 虛擬網路閘道器的多網站連線。 事實上，您可以使用現有的 Azure VPN 閘道器，只要閘道器動態 （路由型）。

如果您已連線至虛擬網路靜態閘道器，您可以變更為動態閘道器類型，並不需要以容納多網站中重建虛擬網路。 變更之前路由類型，請確定您的內部部署 VPN 閘道的支援路由型 VPN 設定。 

![多網站圖表](./media/vpn-gateway-multi-site/multisite.png "多網站")

## <a name="points-to-consider"></a>要考慮的事項

**您將無法使用此虛擬網路進行變更的 Azure 傳統入口網站。** 這個版本中，您需要變更網路設定檔，而不是使用 Azure 傳統入口網站。 如果您在 Azure 傳統入口網站中進行變更，他們會覆寫此虛擬網路的多網站參照設定。 

您應該覺得很您已完成的多網站程序時使用網路設定檔。 不過，如果您有多人使用您的網路設定，必須以確保所有人知道這項限制。 這表示您無法在使用 Azure 傳統入口網站。 您可以使用其事，除了變更此特定的虛擬網路的設定。

## <a name="before-you-begin"></a>開始之前

設定之前，請確定您有下列動作︰

- Azure 的訂閱。 如果您還沒有 Azure 的訂閱，您可以啟動您的[MSDN 訂閱者優惠](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊設定[免費的帳戶](https://azure.microsoft.com/pricing/free-trial/)。

- 相容的 VPN 硬體，每個內部部署的位置。 核取 [[關於 VPN 裝置的虛擬網路連線](vpn-gateway-about-vpn-devices.md)以驗證您想要使用的裝置是否有已知相容性]。

- 對外公開 IPv4 IP 位址的每個 VPN 裝置。 Nat 找不到的 IP 位址 這是需求。

- 您需要安裝最新版 Azure PowerShell cmdlet。 如需安裝的 PowerShell 指令程式的相關資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md) 。

- 給熟練設定 VPN 之硬體的人員。 您將無法使用自動產生 VPN 指令碼從 Azure 傳統入口網站設定您要有 vpn 才能裝置。 這表示您必須有強式了解如何設定您要有 vpn 才能的裝置，或使用時的任何人。

- 您想要用於虛擬網路 （如果您尚未建立一個） IP 位址範圍。 

- 您可以連線到本機網路網站的每個 IP 位址範圍。 您需要，請確定您的 IP 位址範圍的每個您要連線至不會重疊的區域網路網站。 否則，Azure 傳統入口網站或 REST API 將拒絕正上傳的設定。 

    例如，如果您有兩個區域網路網站，都包含 IP 位址範圍 10.2.3.0/24，而且您有使用目的地位址 10.2.3.3 套件，Azure 不知道您想要傳送至套件，因為位址範圍重疊的網站。 若要防止路由的問題，Azure 不允許您具有重疊的範圍設定檔案上傳。



## <a name="1-create-a-site-to-site-vpn"></a>1。 建立網站-VPN

如果您已經有網站-VPN 動態路由閘道器，很好 ！ 您可以繼續[匯出虛擬網路設定的設定](#export)。 如果沒有的話，請執行下列動作︰

### <a name="if-you-already-have-a-site-to-site-virtual-network-but-it-has-a-static-policy-based-routing-gateway"></a>如果您已經有網站-虛擬網路，但它有靜態 （原則架構） 路由閘道器︰

1. 您的閘道器類型變更為動態路由。 多網站 VPN 需要動態 （也稱為路由為基礎） 路由閘道器。 若要變更您的閘道器類型，您必須先刪除現有的閘道器，然後建立一個新。 如需相關指示，請參閱[如何變更您的閘道器的 VPN 路由類型](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md#how-to-change-the-vpn-routing-type-for-your-gateway)。  

2. 設定您的新閘道器，並建立您的 VPN 通道。 如需相關指示，請參閱[Azure 傳統入口網站中要有 vpn 才能閘道器設定](vpn-gateway-configure-vpn-gateway-mp.md)。 首先，變更閘道器類型動態路由。 

### <a name="if-you-dont-have-a-site-to-site-virtual-network"></a>如果您沒有網站-虛擬網路︰

1. 建立網站的虛擬網路使用這些指示︰[建立具有網站-VPN 連線 Azure 傳統入口網站中的虛擬網路](vpn-gateway-site-to-site-create.md)。  

2. 設定動態路由閘道使用這些指示︰[設定 VPN 閘道器](vpn-gateway-configure-vpn-gateway-mp.md)。 請務必選取 [**動態路由**您閘道器類型。

## <a name="export"></a>2.匯出網路設定檔 

匯出您的網路設定檔。 您匯出的檔案會用來設定新的多網站設定值。 如需如何匯出檔案的相關指示，請參閱 [] 區段中，請參閱︰[如何建立使用 Azure 入口網站中的網路設定檔 VNet](../virtual-network/virtual-networks-create-vnet-classic-portal.md#how-to-create-a-vnet-using-a-network-config-file-in-the-azure-portal)。 

## <a name="3-open-the-network-configuration-file"></a>3.開啟網路設定檔

開啟您下載網路設定檔中的最後一個步驟。 使用任何您喜歡的 xml 編輯器。 看起來類似下列檔案︰

        <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
          <VirtualNetworkConfiguration>
            <LocalNetworkSites>
              <LocalNetworkSite name="Site1">
                <AddressSpace>
                  <AddressPrefix>10.0.0.0/16</AddressPrefix>
                  <AddressPrefix>10.1.0.0/16</AddressPrefix>
                </AddressSpace>
                <VPNGatewayAddress>131.2.3.4</VPNGatewayAddress>
              </LocalNetworkSite>
              <LocalNetworkSite name="Site2">
                <AddressSpace>
                  <AddressPrefix>10.2.0.0/16</AddressPrefix>
                  <AddressPrefix>10.3.0.0/16</AddressPrefix>
                </AddressSpace>
                <VPNGatewayAddress>131.4.5.6</VPNGatewayAddress>
              </LocalNetworkSite>
            </LocalNetworkSites>
            <VirtualNetworkSites>
              <VirtualNetworkSite name="VNet1" AffinityGroup="USWest">
                <AddressSpace>
                  <AddressPrefix>10.20.0.0/16</AddressPrefix>
                  <AddressPrefix>10.21.0.0/16</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="FE">
                    <AddressPrefix>10.20.0.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="BE">
                    <AddressPrefix>10.20.1.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="GatewaySubnet">
                    <AddressPrefix>10.20.2.0/29</AddressPrefix>
                  </Subnet>
                </Subnets>
                <Gateway>
                  <ConnectionsToLocalNetwork>
                    <LocalNetworkSiteRef name="Site1">
                      <Connection type="IPsec" />
                    </LocalNetworkSiteRef>
                  </ConnectionsToLocalNetwork>
                </Gateway>
              </VirtualNetworkSite>
            </VirtualNetworkSites>
          </VirtualNetworkConfiguration>
        </NetworkConfiguration>

## <a name="4-add-multiple-site-references"></a>4.新增多個網站參照

當您新增或移除網站參考資訊時，您會對 ConnectionsToLocalNetwork/LocalNetworkSiteRef 來變更設定。 新增新本機網站參照引動程序建立新的通道 Azure。 在下面的範例中，網路設定是單一網站連線。 當您完成變更後，請儲存檔案。

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

    To add additional site references (create a multi-site configuration), simply add additional "LocalNetworkSiteRef" lines, as shown in the example below: 

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

## <a name="5-import-the-network-configuration-file"></a>5.匯入網路設定檔

匯入網路設定檔。 當您匯入變更此檔案時，便會新增新通道。 通道會使用您先前建立的動態閘道器。 如需如何匯入檔案的相關指示，請參閱 [] 區段中，請參閱︰[如何建立使用 Azure 入口網站中的網路設定檔 VNet](../virtual-network/virtual-networks-create-vnet-classic-portal.md#how-to-create-a-vnet-using-a-network-config-file-in-the-azure-portal)。 

## <a name="6-download-keys"></a>6。 下載鍵

新增您的新通道後, 使用 PowerShell cmdlet`Get-AzureVNetGatewayKey`以取得每個通道 IPsec/IKE 預先共用索引鍵。

例如︰

    Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"

    Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"

如果您想要的話，您也可以使用*取得虛擬網路閘道器共用金鑰*REST API 取得預先共用的金鑰。

## <a name="7-verify-your-connections"></a>7。 驗證您的連線

檢查多網站通道狀態。 下載之後的每個通道按鍵，您需要驗證連線。 使用`Get-AzureVnetConnection`取得虛擬網路通道的清單，在下面的範例所示。 VNet1 是 VNet 的名稱。

    Get-AzureVnetConnection -VNetName VNET1
        
    ConnectivityState         : Connected
    EgressBytesTransferred    : 661530
    IngressBytesTransferred   : 519207
    LastConnectionEstablished : 5/2/2014 2:51:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site1' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site1
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7f68a8e6-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded
        
    ConnectivityState         : Connected
    EgressBytesTransferred    : 789398
    IngressBytesTransferred   : 143908
    LastConnectionEstablished : 5/2/2014 3:20:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site2' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site2
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7893b329-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded

## <a name="next-steps"></a>後續步驟

若要進一步瞭解 VPN 閘道器，請參閱[關於 VPN 閘道器](../vpn-gateway/vpn-gateway-about-vpngateways.md)。

