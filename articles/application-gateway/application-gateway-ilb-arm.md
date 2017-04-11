<properties
   pageTitle="建立及使用 Azure 資源管理員設定的內部負載平衡器 (ILB) 應用程式閘道 |Microsoft Azure"
   description="本頁面提供指示建立、 設定、 啟動，並刪除內部負載平衡器 (ILB) Azure 應用程式閘道的 Azure 資源管理員"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/19/2016"
   ms.author="gwallace"/>


# <a name="create-an-application-gateway-with-an-internal-load-balancer-ilb-by-using-azure-resource-manager"></a>建立內部負載平衡器 (ILB) 應用程式閘道使用 Azure 資源管理員

> [AZURE.SELECTOR]
- [Azure 傳統的步驟](application-gateway-ilb.md)
- [資源管理員 PowerShell 步驟](application-gateway-ilb-arm.md)

具網際網路 VIP 或內部端點未公開至網際網路，也就是內部負載平衡器 (ILB) 端點，您可以設定 azure 應用程式閘道器。 閘道器設定與 ILB 對相當實用的網際網路上公開內部線條的商務應用程式。 也很有用的服務並層多層應用程式內坐在安全性界限未公開至網際網路，但是仍需要循環載入通訊、 工作階段神奇結果或安全通訊端層 (SSL) 結束。

本文會引導您設定 ILB 應用程式閘道器的步驟。

## <a name="before-you-begin"></a>開始之前

1. 使用 Web 平台安裝程式，安裝最新版 Azure PowerShell cmdlet。 您可以從下載並安裝最新版本的**Windows PowerShell**一節的[下載頁面](https://azure.microsoft.com/downloads/)。
2. 您可以建立虛擬網路和子網路應用程式的閘道器。 請確認沒有虛擬機器或雲端部署使用子網路。 應用程式的閘道器必須是單獨子虛擬網路。
3. 您設定為使用應用程式閘道器的伺服器，必須存在或指派建立虛擬網路或公用 IP/VIP 其結束點。

## <a name="what-is-required-to-create-an-application-gateway"></a>若要建立的應用程式閘道器的必要條件為何？


- **後端伺服器集區︰**後端伺服器 IP 位址清單。 列出的 IP 位址應該是所屬的虛擬網路，但在不同應用程式閘道器的子網路或應該公用 IP/VIP。
- **後端伺服器資料庫設定︰**每個資料庫有設定，例如連接埠與通訊協定，cookie 為基礎的相關性。 這些設定會連結到資料庫]，就會套用到所有資料庫中的伺服器。
- **前端連接埠︰**此連接埠是開啟應用程式閘道器公用連接埠。 流量碰到此連接埠]，然後會被重新導向至其中一個後端伺服器。
- **接聽︰**接聽具有前端連接埠通訊協定 （Http 或 Https，這些是區分大小寫），以及的 SSL 憑證名稱 （如果設定 SSL 卸載）。
- **規則︰**此規則繫結接聽和伺服器後端資料庫，並定義流量在時叫接聽會導向至的後端伺服器集區。 目前為支援*基本*的規則。 *基本*規則是循環載入通訊群組。



## <a name="create-an-application-gateway"></a>建立應用程式閘道器

使用 Azure 傳統和 Azure 資源管理員之間的差異是您建立的應用程式閘道器的與項目需要設定的順序。
使用資源管理員，請使用應用程式閘道的所有項目是個別設定，然後將放在一起，建立應用程式閘道器資源。


若要建立應用程式閘道所需的步驟如下︰

1. 建立資源群組的 [資源管理員
2. 建立虛擬網路和應用程式閘道器的子網路
3. 建立應用程式閘道器組態物件
4. 建立應用程式閘道器資源


## <a name="create-a-resource-group-for-resource-manager"></a>建立資源群組的 [資源管理員

請確定您切換 PowerShell 模式]，使用 Azure 資源管理員 cmdlet。 [使用 Windows PowerShell 與資源管理員](../powershell-azure-resource-manager.md)的詳細資訊。

### <a name="step-1"></a>步驟 1

    Login-AzureRmAccount

### <a name="step-2"></a>步驟 2

核取訂閱的帳戶。

    Get-AzureRmSubscription

系統會提示您的認證以進行驗證。<BR>

### <a name="step-3"></a>步驟 3

選擇您要使用的 Azure 訂閱。 <BR>


    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### <a name="step-4"></a>步驟 4

建立新的資源群組 （略過此步驟如果您使用現有的資源群組）。

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

Azure 資源管理員需要所有資源群組都指定的位置。 這用於做為預設位置的 [資源] 群組中的資源。 請確認所有命令來建立應用程式閘道會都使用相同的資源群組。

在上述範例中，我們建立名為 「 appgw 路由 」 和 「 西部美國 」 的位置的資源群組。

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>建立虛擬網路和應用程式閘道器的子網路

下列範例會示範如何建立虛擬網路使用資源管理員︰

### <a name="step-1"></a>步驟 1

    $subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

這會指定要用來建立虛擬網路的子網路變數位址範圍 10.0.0.0/24。

### <a name="step-2"></a>步驟 2

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnetconfig

這樣會建立虛擬網路的子網路 10.0.0.0/24 搭配使用前置詞 10.0.0.0/16 西美國地區具名資源群組 」 appgw-路由 」 中的 「 appgwvnet 」。

### <a name="step-3"></a>步驟 3

    $subnet = $vnet.subnets[0]

這會指定子網路物件變數 $subnet 的下一個步驟。

## <a name="create-an-application-gateway-configuration-object"></a>建立應用程式閘道器組態物件

### <a name="step-1"></a>步驟 1

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

這樣會建立名為 「 gatewayIP01 」 應用程式的閘道器 IP 設定。 應用程式閘道啟動時，它會挑選設定子網路中的 IP 位址，並將網路流量路由傳送後端 IP 集區中的 IP 位址。 請記住，每個執行個體需要一個 IP 位址。


### <a name="step-2"></a>步驟 2

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

這會設定名為 「 pool01 」 的 IP 位址的後端 IP 位址集區 」 134.170.185.46，134.170.188.221,134.170.185.50 」。 這些是收到來自前端 IP 端點網路流量的 IP 位址。 您取代上述新增您自己的應用程式 IP 位址端點的 IP 位址。

### <a name="step-3"></a>步驟 3

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled

此設定後端資料庫中的應用程式閘道器設定 「 poolsetting01 」 的負載平衡網路流量。

### <a name="step-4"></a>步驟 4

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

這會設定為 ILB 命名 「 frontendport01 」 的前端 IP 連接埠。

### <a name="step-5"></a>步驟 5

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -Subnet $subnet

這會建立名為 「 fipconfig01 」 的前端 IP 設定，並將它從目前虛擬網路的子網路私人 IP 關聯。

### <a name="step-6"></a>步驟 6

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

這會建立接聽程式稱為 「 listener01 」，並建立關聯的前端 IP 設定的前端連接埠。

### <a name="step-7"></a>步驟 7

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

這樣會建立負載平衡器路由規則稱為 「 rule01 」，以設定負載平衡器行為。

### <a name="step-8"></a>步驟 8

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

此設定應用程式閘道執行個體的大小。

>[AZURE.NOTE]  *InstanceCount*的預設值是 2 的最大值 10。 *GatewaySize*的預設值是 [中]。 您可以選擇 Standard_Small、 Standard_Medium，與 Standard_Large 之間。

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>使用新增 AzureApplicationGateway 建立應用程式閘道器

建立應用程式閘道使用上述步驟中的所有設定項目。 在此範例中，應用程式閘道稱為 「 appgwtest 」。


    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

這會以上述步驟中的所有設定項目建立應用程式閘道器。 在範例中，應用程式閘道稱為 「 appgwtest 」。


## <a name="delete-an-application-gateway"></a>刪除應用程式閘道器

若要刪除的應用程式閘道，您必須執行下列動作順序︰

1. 若要停止閘道器使用**停駐點 AzureRmApplicationGateway**指令程式。
2. 您可以使用 [**移除 AzureRmApplicationGateway** cmdlet 來移除閘道器。
3. 確認 [閘道器，已使用**取得 AzureApplicationGateway**指令程式來移除]。


### <a name="step-1"></a>步驟 1

取得應用程式閘道器物件，然後與變數 「 $getgw 」 建立關聯。

    $getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### <a name="step-2"></a>步驟 2

若要停止應用程式閘道使用**停駐點 AzureRmApplicationGateway** 。 此範例顯示**停駐點 AzureRmApplicationGateway** cmdlet 的第一行，後面接著輸出。

    PS C:\> Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  

    VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
    VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

停止狀態中的應用程式閘道器後，使用**移除 AzureRmApplicationGateway** cmdlet 來移除這項服務。


    PS C:\> Remove-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Force

    VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
    VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

>[AZURE.NOTE] **-強制**切換可以用來隱藏 [移除] 確認訊息。


若要驗證服務已經移除，您可以使用**取得 AzureRmApplicationGateway**指令程式。 不需要此步驟。


    PS C:\>Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

    VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

    Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
    .....

## <a name="next-steps"></a>後續步驟

如果您想要設定 SSL 卸載，請參閱[設定應用程式的閘道 SSL 卸載](application-gateway-ssl.md)。

如果您想要設定的應用程式閘道器與 ILB 搭配使用，請參閱[建立內部負載平衡器 (ILB) 應用程式閘道](application-gateway-ilb.md)。

若要深入瞭解一般負載平衡的選項，請參閱︰

- [Azure 負載平衡器](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure 流量管理員](https://azure.microsoft.com/documentation/services/traffic-manager/)
