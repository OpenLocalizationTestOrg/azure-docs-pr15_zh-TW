<properties 
   pageTitle="如何將傳統的虛擬網路連線到資源管理員的虛擬網路使用 PowerShell |Microsoft Azure"
   description="瞭解如何建立 VPN 連線之間傳統 VNets 和資源管理員 VNets 使用 VPN 閘道器與 PowerShell"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="cherylmc" />

# <a name="connect-virtual-networks-from-different-deployment-models-using-powershell"></a>從不同的部署模型使用 PowerShell 連線虛擬網路

> [AZURE.SELECTOR]
- [入口網站](vpn-gateway-connect-different-deployment-models-portal.md)
- [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)

Azure 目前有兩種管理模型︰ 傳統和資源管理員 (RM)。 如果您使用 Azure 段時間，您可能有 Azure Vm 以及在傳統的 VNet 中執行的執行個體角色。 資源管理員中建立 VNet 可能會執行您的較新的 Vm 和角色執行個體。 本文會引導您連線到資源管理員 VNets，以便在個別的部署模型，以透過閘道器連線彼此的資源的 [傳統 VNets。

您可以建立不同的訂閱和不同區域中的 VNets 之間的連線。 您也可以連接 VNets 已連線到內部部署網路，只要閘道器設定使用動態，或按一下 [傳送型。 如需有關 VNet-VNet 連線的詳細資訊，請參閱本文結尾的[VNet-VNet 常見問題集](#faq)。

### <a name="deployment-models-and-methods-for-connecting-vnets-in-different-deployment-models"></a>部署模型和連線 VNets 不同的部署模型中的方法

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

我們更新下表為新的文件及其他工具，就可以使用此設定。 使用文件時，我們直接從連結資料表。<br><br>

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)] 

#### <a name="vnet-peering"></a>對等 VNet

[AZURE.INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]


## <a name="before-beginning"></a>在開始之前

下列步驟會引導您完成設定的每個 VNet 的動態，或按一下 [傳送為基礎的閘道器，以及建立 VPN 連線閘道之間所需的設定。 此設定不支援靜態或原則為基礎的閘道器。

### <a name="prerequisites"></a>必要條件

 - 已建立兩個 VNets。
 - VNets 的位址範圍不彼此重疊或重疊的閘道器可能連線至其他連線的範圍。
 - 您已經安裝最新的 PowerShell cmdlet (1.0.2 或更新版本)。 如需詳細資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md) 。 請確定您同時安裝服務管理 (SM) 和資源管理員 (RM) cmdlet。 

### <a name="exampleref"></a>範例設定

您可以使用 [範例] 設定的參考。

**傳統 VNet 設定**

VNet 名稱 = ClassicVNet <br>
位置 = 西美制]。 <br>
虛擬網路位址空間 = 10.0.0.0/24 <br>
子網路 1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
區域網路名稱 = RMVNetLocal <br>
GatewayType = DynamicRouting

**資源管理員 VNet 設定**

VNet 名稱 = RMVNet <br>
資源群組 = RG1 <br>
虛擬網路 IP 位址空間 = 192.168.0.0/16 <br>
子網路 1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
位置 = 東亞美制]。 <br>
閘道器的公用 IP 名稱 = gwpip <br>
區域網路閘道器 = ClassicVNetLocal <br>
虛擬網路閘道器名稱 = RMGateway <br>
閘道器 IP 位址設定 = gwipconfig


## <a name="createsmgw"></a>第 1 節-設定傳統 VNet

### <a name="part-1---download-your-network-configuration-file"></a>第 1 部分-下載您的網路設定檔

1. 登入您在 PowerShell 主控台以提高權限的權限的 Azure 帳戶。 下列指令程式會提示您登入認證，Azure 帳戶。 登入之後下載您的帳戶設定，使其使用 PowerShell 的 Azure。 您將使用 SM PowerShell cmdlet 來完成這項設定的一部分。

        Add-AzureAccount

2. 執行下列命令以匯出 Azure 網路設定檔案。 您可以變更要匯出至另一個位置，如有必要的檔案的位置。 您會編輯檔案，然後將它匯入至 Azure。

        Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml

3. 開啟您下載加以編輯.xml 檔案。 如需網路設定檔的範例，請參閱[網路設定結構描述](https://msdn.microsoft.com/library/jj157100.aspx)。
 

### <a name="part-2--verify-the-gateway-subnet"></a>組件 2-驗證的閘道器子網路

在**VirtualNetworkSites**項目，請將閘道器子網路加入至您 VNet，如果其中一個不已經建立。 閘道器子網路時使用網路設定檔，必須名稱 「 GatewaySubnet 」 或 Azure 無法辨識的內容，並將其作為閘道器子網路。

[AZURE.INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

**範例︰**
    
    <VirtualNetworkSites>
      <VirtualNetworkSite name="ClassicVNet" Location="West US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/24</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Subnet-1">
            <AddressPrefix>10.0.0.0/27</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.0.0.32/29</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
       
### <a name="part-3---add-the-local-network-site"></a>第 3 部分-新增 [區域網路網站

您新增的區域網路網站代表您要連線 RM VNet。 您可能要新增的檔案，如果有一個**LocalNetworkSites**項目尚不存在。 此時的設定，VPNGatewayAddress 可以是任何有效的公用 IP 位址因為我們的資源管理員 VNet 尚未建立閘道器。 一旦我們建立閘道器，我們會取代 RM 閘道器已指派正確公用 IP 位址的此版面配置區的 IP 位址。

    <LocalNetworkSites>
      <LocalNetworkSite name="RMVNetLocal">
        <AddressSpace>
          <AddressPrefix>192.168.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>

### <a name="part-4---associate-the-vnet-with-the-local-network-site"></a>第 4 部分-建立 VNet 與區域網路網站之間的關聯

在此區段中，我們可以指定您要連線至 VNet 的區域網路網站。 在此情況下，則您先前已參考資源管理員 VNet。 請確定名稱相符。 此步驟中無法建立閘道器。 它會指定閘道器會連線到本機網路。

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="RMVNetLocal">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

### <a name="part-5---save-the-file-and-upload"></a>第 5 部分-儲存檔案，然後上傳

儲存檔案，然後執行下列命令以將其匯入 Azure。 請確定您變更檔案路徑，視您的環境。

        Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml

您應該會看到類似此顯示匯入成功的結果。

        OperationDescription        OperationId                      OperationStatus                                                
        --------------------        -----------                      ---------------                                                
        Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 

### <a name="part-6---create-the-gateway"></a>第 6-建立閘道器 

您可以使用 [傳統] 入口網站，或使用 PowerShell 來建立 VNet 閘道器。

若要建立動態路由閘道器，請使用下列範例︰

    New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting

您可以使用檢查閘道器狀態`Get-AzureVNetGateway`指令程式。


## <a name="creatermgw"></a>第 2 節︰ 設定 RM VNet 閘道器

若要建立 RM VNet VPN 閘道器，請依照下列指示。 不開始的步驟，直到之後您擷取傳統 VNet 的閘道器的公用 IP 位址。 

1. PowerShell 主控台中檢視**您 Azure 帳戶登入**。 下列指令程式會提示您登入認證，Azure 帳戶。 登入之後，使其使用 PowerShell 的 Azure 下載您的帳戶設定。

        Login-AzureRmAccount 

    如果您有多個訂閱，請取得您 Azure 訂閱的清單。

        Get-AzureRmSubscription

    指定您想要使用的訂閱。 

        Select-AzureRmSubscription -SubscriptionName "Name of subscription"


2.  **建立區域網路閘道器**。 在虛擬網路中，區域網路閘道器通常是指您內部部署的位置。 在此情況下，您傳統 VNet 是指區域網路閘道器。 為依據 Azure 參考，及也指定的地址空間前置字元的名稱。 Azure 所用的 IP 位址首碼您指定識別哪些流量傳送給您內部部署的位置。 如果您需要更新版本中，建立您的閘道之前，先調整的資訊您可以修改值，並再次執行範例。<br><br>
    - `-Name`是您想要指派的區域網路閘道器參照的名稱。<br>
    - `-AddressPrefix`為您傳統 VNet 位址空間。<br>
    - `-GatewayIpAddress`為傳統 VNet 的閘道器的公用 IP 位址。 請務必變更下面的範例，以反映正確的 IP 位址。
    
            New-AzureRmLocalNetworkGateway -Name ClassicVNetLocal `
            -Location "West US" -AddressPrefix "10.0.0.0/24" `
            -GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1

3. 資源管理員 VNet 配置虛擬網路閘道，**要求的公用 IP 位址**。 您無法指定您想要使用的 IP 位址。 虛擬網路閘道動態配置的 IP 位址。 不過，這不表示的 IP 位址會變更。 只有虛擬網路閘道器 IP 位址變更時，刪除並重新建立閘道器。 它不會變更過調整大小、 重設，或其他內部維護/升級的閘道器。<br>在此步驟中，我們也會將在接下來的步驟中使用的變數。


        $ipaddress = New-AzureRmPublicIpAddress -Name gwpip `
        -ResourceGroupName RG1 -Location 'EastUS' `
        -AllocationMethod Dynamic

4. **驗證您的虛擬網路有閘道器子網路**。 如果沒有閘道器子網路存在，新增一個。 確認閘道器子網路名稱為*GatewaySubnet*。

5. **擷取子網路**執行下列命令以用於閘道器。 在此步驟中，我們也會將在下一個步驟中使用的變數。
    - `-Name`是您的資源管理員 VNet 的名稱。
    - `-ResourceGroupName`是 VNet 相關聯的 [資源] 群組。 閘道器子網路的此 VNet 時，必須已存在，而且會名稱*GatewaySubnet*才能正常運作。


            $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet `
            -VirtualNetwork (Get-AzureRmVirtualNetwork -Name RMVNet -ResourceGroupName RG1) 

6. **建立閘道器 IP 位址設定**。 閘道器組態定義子網路，以及要使用的公用 IP 位址。 您可以使用下列範例來建立您的閘道器設定。<br>在此步驟中，`-SubnetId`和`-PublicIpAddressId`參數必須傳遞 [識別碼] 屬性的子網路及 IP 位址的物件，分別。 您無法使用簡單的字串。 在步驟設定這些變數要求的公用 IP 和步驟來擷取子網路。

        $gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig `
        -Name gwipconfig -SubnetId $subnet.id `
        -PublicIpAddressId $ipaddress.id
    
7. 執行下列命令以**建立資源管理員虛擬網路閘道器**。 `-VpnType`必須*RouteBased*。 花費 45 分鐘以上的完成。

        New-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
        -Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
        -IpConfigurations $gwipconfig `
        -EnableBgp $false -VpnType RouteBased

8. 已建立**將複製的公用 IP 位址**一次 VPN 閘道器。 您使用，當您設定您的傳統 VNet 的區域網路設定。 您可以使用下列 cmdlet，以擷取的公用 IP 位址。 *IpAddress*的公用 IP 位址列在 return 鍵。

        Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName RG1

## <a name="section-3-modify-the-local-network-for-the-classic-vnet"></a>區段 3]: 為傳統 VNet 修改區域的網路

您可以執行此步驟中匯出網路設定檔、 編輯，然後儲存並匯入檔案以返回 Azure。 或者，您可以修改 [傳統] 入口網站中的此設定。 

### <a name="to-modify-in-the-portal"></a>若要修改在入口網站

1. 開啟 [[傳統] 入口網站](https://manage.windowsazure.com)。

2. 在 [傳統] 入口網站，左側的 [向下捲動並按一下**網路**。 在 [**網路**] 頁面上，按一下 [**區域網路**頂端的頁面]。 

3. 在**本機的網路**] 頁面上，按一下以選取 [區域網路設定在第 1 部分，然後移至頁面底部，按一下 [**編輯**]。

4. 在 [**指定的區域網路詳細資料**] 頁面的版面配置區的 IP 位址換成資源管理員閘道器在前一節中所建立的公用 IP 位址。 按一下箭號以移至下一節。 驗證**位址空間**正確，，然後按一下 [接受變更核取記號。

### <a name="to-modify-using-the-network-configuration-file"></a>若要修改使用網路設定檔

1. 匯出網路設定檔。
2. 在文字編輯器中修改 VPN 閘道器 IP 位址。

        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>

3. 儲存變更，並回到 Azure 匯入已編輯的檔案。


## <a name="connect"></a>區段 4︰ 建立閘道器之間的連線

建立閘道器之間的連線，您必須 PowerShell。 您可能需要新增 Azure 帳戶使用傳統的 PowerShell 指令程式。 若要這麼做，您可以使用下列 cmdlet: 
        
    Add-AzureAccount

1. **設定您的共用的金鑰**來執行下列命令。 在此範例中，`-VNetName`傳統 VNet 的名稱和`-LocalNetworkSiteName`是您在設定時，在 [傳統] 入口網站中指定的區域網路的名稱。 `-SharedKey`可以產生，並指定的值。 您在這裡指定的值必須是當您建立您的連線下一個步驟中所指定的值相同。 在這個範例傳回應該會顯示**狀態︰ 成功**。

        Set-AzureVNetGatewayKey -VNetName ClassicVNet `
        -LocalNetworkSiteName RMVNetLocal -SharedKey abc123

2. 執行下列命令，以建立 VPN 連線。

    **設定變數**

        $vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
        $vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1

    **建立連線**<br> 請注意，`-ConnectionType`為 IPsec，不 Vnet2Vnet。
        
        New-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
        -Location "East US" -VirtualNetworkGateway1 `
        $vnet02gateway -LocalNetworkGateway2 `
        $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

3. 您可以檢視您的連線，在任一入口網站，或使用`Get-AzureRmVirtualNetworkGatewayConnection`指令程式。

        Get-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1

## <a name="faq"></a>VNet-VNet 常見問題集

檢視 VNet-VNet 連線的其他資訊的常見問題集詳細資料。

[AZURE.INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)] 


