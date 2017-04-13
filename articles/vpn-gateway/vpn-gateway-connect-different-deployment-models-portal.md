<properties 
   pageTitle="如何將傳統的虛擬網路連線到入口網站中的資源管理員虛擬網路 |Microsoft Azure"
   description="瞭解如何建立 VPN 連線傳統 VNets 和使用 VPN 閘道和入口網站的資源管理員 VNets 之間"
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
   ms.date="10/03/2016"
   ms.author="cherylmc" />

# <a name="connect-virtual-networks-from-different-deployment-models-in-the-portal"></a>從入口網站中的不同的部署模型連線虛擬網路

> [AZURE.SELECTOR]
- [入口網站](vpn-gateway-connect-different-deployment-models-portal.md)
- [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)


Azure 目前有兩種管理模型︰ 傳統和資源管理員 (RM)。 如果您使用 Azure 段時間，您可能有 Azure Vm 以及在傳統的 VNet 中執行的執行個體角色。 資源管理員中建立 VNet 可能會執行您的較新的 Vm 和角色執行個體。 本文會引導您連線到資源管理員 VNets，以便在個別的部署模型，以透過閘道器連線彼此的資源的 [傳統 VNets。 

您可以建立不同的訂閱和不同區域中的 VNets 之間的連線。 您也可以連接 VNets 已連線到內部部署網路，只要閘道器設定使用動態，或按一下 [傳送型。 如需有關 VNet-VNet 連線的詳細資訊，請參閱本文結尾的[VNet-VNet 常見問題集](#faq)。

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>部署模型和 VNet-VNet 連線的方法

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

我們更新下表為新的文件及其他工具，就可以使用此設定。 使用文件時，我們直接從連結資料表。<br><br>

**VNet-VNet**

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)] 

#### <a name="vnet-peering"></a>對等 VNet

[AZURE.INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## <a name="before-beginning"></a>在開始之前

下列步驟會引導您完成設定的每個 VNet 的動態，或按一下 [傳送為基礎的閘道器，以及建立 VPN 連線閘道之間所需的設定。 此設定不支援靜態或原則為基礎的閘道器。 

本文中，我們使用傳統的入口網站 Azure 入口網站，與 PowerShell。 目前無法建立使用 Azure 入口網站此設定。

### <a name="prerequisites"></a>必要條件

 - 已建立兩個 VNets。
 - VNets 的位址範圍不彼此重疊或重疊的閘道器可能連線至其他連線的範圍。
 - 您已經安裝最新的 PowerShell cmdlet (1.0.2 或更新版本)。 如需詳細資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md) 。 請確定您同時安裝服務管理 (SM) 和資源管理員 (RM) cmdlet。 

### <a name="values"></a>範例設定

您可以使用 [範例] 設定參考。

**傳統 VNet 設定**

VNet 名稱 = ClassicVNet <br>
位置 = 西美制]。 <br>
虛擬網路位址空間 = 10.0.0.0/24 <br>
子網路 1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
區域網路名稱 = RMVNetLocal <br>

**資源管理員 VNet 設定**

VNet 名稱 = RMVNet <br>
資源群組 = RG1 <br>
虛擬網路 IP 位址空間 = 192.168.0.0/16 <br>
子網路 1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
位置 = 東亞美制]。 <br>
虛擬網路閘道器名稱 = RMGateway <br>
閘道器的公用 IP 名稱 = gwpip <br>
閘道器類型 = VPN <br>
VPN 類型 = 路由型 <br>
區域網路閘道器 = ClassicVNetLocal <br>

## <a name="createsmgw"></a>第 1 節︰ 設定傳統 VNet

在此區段中，我們為您傳統 VNet 建立區域網路和閘道器。 本節中的指示進行使用傳統入口網站。 目前，Azure 入口網站並未提供與傳統的 VNet 有關的所有設定。

### <a name="part-1---create-a-new-local-network"></a>第 1 部分-建立新的本機網路

開啟[傳統入口網站](https://manage.windowsazure.com)登入您的 Azure 帳戶。

1. 在左下角的畫面中，按一下 [**新增** > **網路服務** > **虛擬網路** > **新增區域網路**。

2. 在**指定的區域網路詳細資料**] 視窗中，輸入您要連線至 RM VNet 的名稱。 在 [**要有 vpn 才能裝置的 IP 位址 （選用）** ] 方塊中，輸入任何有效的公用 IP 位址。 這是只暫時版面配置區。 您可以稍後再變更此 IP 位址。 在右下角的視窗中，按一下箭號] 按鈕。
 
3. 在**指定的地址空間**頁面上，在**啟動 IP** ] 文字方塊中，輸入網路前置詞和 CIDR 封鎖您要連線到資源管理員 VNet。 這項設定用來指定路由至 RM VNet 位址空間。

### <a name="part-2---associate-the-local-network-to-your-vnet"></a>第 2 部分-建立您 VNet 區域網路之間的關聯

1. 按一下 [**虛擬網路**在頁面頂端的切換至 [網路虛擬] 畫面中，然後按一下以選取您的傳統 VNet。 在您 VNet 的頁面上，按一下 [**設定**]，以瀏覽至 [設定] 頁面。

2. 在**網站-連線**連線] 區段中，選取 [**區域網路的連線**] 核取方塊。 然後選取 [區域網路所建立的。 如果您有多個區域網路所建立的請務必選取您要建立代表您資源管理員 VNet，從下拉式清單的項目。

3. 按一下 [在頁面底部的 [**儲存**]。

### <a name="part-3---create-the-gateway"></a>第 3 部分-建立閘道器

1. 儲存之後的設定，按一下 [**儀表板**頂端的 [若要變更為儀表板頁面的頁面]。 在儀表板頁面底部，按一下 [**建立閘道器**，然後按一下**動態路由**。 按一下 [ **** ，開始建立您的閘道器。 動態路由閘道器都需要此設定。

2. 若要建立閘道器，請等候。 這有時候會花費 45 分鐘或更多] 以完成。

### <a name="ip"></a>第 4 部分-檢視閘道器的公用 IP 位址

建立閘道器之後，您可以檢視在**儀表板**頁面上的閘道器 IP 位址。 這是您的閘道器的公用 IP 位址。 寫下或複製的公用 IP 位址。 您在稍後步驟中時使用您在資源管理員 VNet 設定建立區域網路。


## <a name="creatermgw"></a>第 2 節︰ 資源管理員 VNet 設定

在此區段中，我們為您資源管理員 VNet 建立虛擬網路閘道器及區域網路。 不開始直到下列步驟之後您擷取傳統 VNet 的閘道器的公用 IP 位址。

螢幕擷取畫面所提供的範例。 請務必的值取代為您自己。 如果您正在建立練習此設定，請參閱以下的[值](#values)。


### <a name="part-1---create-a-gateway-subnet"></a>第 1 部分-建立閘道器子網路

之前將虛擬的網路連線到閘道器，您必須建立閘道器網路的子虛擬您要連線。 建立具有 CIDR 計數 /28 或更大的閘道器子網路 (/ 27 日/26，等等。)

[AZURE.INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

從瀏覽器中，瀏覽至[Azure 入口網站](http://portal.azure.com)，然後使用您 Azure 帳戶登入。

[AZURE.INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

### <a name="part-2---create-a-virtual-network-gateway"></a>第 2 部分-建立虛擬網路閘道器


[AZURE.INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]


### <a name="part-3---create-a-local-network-gateway"></a>第 3 部分-建立區域網路閘道器

區域網路閘道通常是指您內部部署的位置。 它會告知 Azure 的 IP 位址範圍路由到的位置和位置的裝置的公用 IP 位址。 不過，在此情況下，所參照的地址範圍及公用傳統 VNet 與虛擬網路閘道器相關聯的 IP 位址。

區域網路閘道器的名稱為依據 Azure 參考。 建立虛擬網路閘道時，您可以建立您的區域網路閘道器。 此設定，您可以使用已指派給傳統 VNet 閘道[前一節](#ip)中的公用 IP 位址。

[AZURE.INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]


### <a name="part-4---copy-the-public-ip-address"></a>第 4 部分-將複製的公用 IP 位址

一旦完成建立虛擬網路閘道器，複製 [閘道器相關聯的公用 IP 位址。 您使用，當您設定您的傳統 VNet 的區域網路設定。 


## <a name="section-3-modify-the-local-network-for-the-classic-vnet"></a>區段 3]: 為傳統 VNet 修改區域的網路

開啟 [[傳統] 入口網站](https://manage.windowsazure.com)。

2. 在 [傳統] 入口網站，左側的 [向下捲動並按一下**網路**。 在 [**網路**] 頁面上，按一下 [**區域網路**頂端的頁面]。 

3. 按一下以選取 [區域網路中第 1 部分設定。 在頁面底部，按一下 [**編輯**]。

4. 在 [**指定的區域網路詳細資料**] 頁面的版面配置區的 IP 位址換成資源管理員閘道器在前一節中所建立的公用 IP 位址。 按一下箭號以移至下一節。 驗證**位址空間**正確，，然後按一下 [接受變更核取記號。

## <a name="connect"></a>區段 4︰ 建立連線

在此區段中，我們建立 VNets 之間的連線。 這個步驟需要 PowerShell。 您無法在其中一個入口網站中建立此連線。 請確定您已下載並安裝傳統 (SM) 和資源管理員 (RM) PowerShell cmdlet。


1. PowerShell 主控台中檢視您 Azure 帳戶登入。 下列指令程式會提示您登入認證，Azure 帳戶。 登入之後，使其使用 PowerShell 的 Azure 下載您的帳戶設定。

        Login-AzureRmAccount 

    如果您有多個訂閱，請取得您 Azure 訂閱的清單。

        Get-AzureRmSubscription

    指定您想要使用的訂閱。 

        Select-AzureRmSubscription -SubscriptionName "Name of subscription"


2. 新增 Azure 帳戶使用傳統的 PowerShell 指令程式。 若要這麼做，您可以使用下列命令︰

        Add-AzureAccount

3. 藉由執行下列範例中設定您的共用的金鑰。 在此範例中，`-VNetName`傳統 VNet 的名稱和`-LocalNetworkSiteName`是您在設定時，在 [傳統] 入口網站中指定的區域網路的名稱。 `-SharedKey`可以產生，並指定的值。 您在這裡指定的值必須是當您建立您的連線下一個步驟中所指定的值相同。

        Set-AzureVNetGatewayKey -VNetName ClassicVNet `
        -LocalNetworkSiteName RMVNetLocal -SharedKey abc123

4. 執行下列命令，以建立 VPN 連線︰
    
    **設定變數**

        $vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
        $vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1

    **建立連線**<br> 請注意，`-ConnectionType`為 「 IPsec'，不是 「 Vnet2Vnet 」。 在此範例中，`-Name`就是您要撥打您的連線的名稱。 下列範例會建立名為 「*連接至傳統 rm*」 的連線。
        
        New-AzureRmVirtualNetworkGatewayConnection -Name rm-to-classic-connection -ResourceGroupName RG1 `
        -Location "East US" -VirtualNetworkGateway1 `
        $vnet02gateway -LocalNetworkGateway2 `
        $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

## <a name="verify-your-connection"></a>確認您的連線

您可以使用 [傳統] 入口網站、 Azure 入口網站或 PowerShell 驗證您的連線。 您可以使用下列步驟，以驗證您的連線。 值取代為您自己。

[AZURE.INCLUDE [vpn-gateway-verify connection](../../includes/vpn-gateway-verify-connection-rm-include.md)] 

## <a name="faq"></a>VNet-VNet 常見問題集

檢視 VNet-VNet 連線的其他資訊的常見問題集詳細資料。

[AZURE.INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)] 


