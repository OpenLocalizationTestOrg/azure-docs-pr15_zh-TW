<properties
   pageTitle="建立、 開始，或刪除應用程式閘道使用 Azure 資源管理員 |Microsoft Azure"
   description="本頁面提供指示建立、 設定、 啟動，並使用 Azure 資源管理員刪除 Azure 應用程式閘道器"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace"/>


# <a name="create-start-or-delete-an-application-gateway-by-using-azure-resource-manager"></a>建立、 開始，或刪除應用程式閘道使用 Azure 資源管理員

> [AZURE.SELECTOR]
- [Azure 入口網站](application-gateway-create-gateway-portal.md)
- [Azure 資源管理員 PowerShell](application-gateway-create-gateway-arm.md)
- [Azure 傳統 PowerShell](application-gateway-create-gateway.md)
- [Azure 資源管理員範本](application-gateway-create-gateway-arm-template.md)
- [Azure CLI](application-gateway-create-gateway-cli.md)

Azure 應用程式閘道是有 7 層負載平衡器。 是否位於雲端或內部部署，它會提供容錯移轉時，效能路由 HTTP 要求之間不同的伺服器。 應用程式的閘道器提供許多的應用程式傳送控制器 (ADC) 的功能，包括 HTTP 負載平衡、 cookie 為基礎的工作階段相關性、 安全通訊端層 (SSL) 卸載自訂健康探查、 支援多重網站和許多其他。 若要尋找的受支援功能的完整清單，請造訪[應用程式閘道器概觀](application-gateway-introduction.md)

本文會引導您建立、 設定、 啟動以及刪除應用程式閘道器的步驟。

>[AZURE.IMPORTANT] 您使用 Azure 資源之前，請務必瞭解 Azure 目前有兩種部署模型︰ 資源管理員] 及 [傳統。 請確定您瞭解[部署模型和工具](../azure-classic-rm.md)使用的任何 Azure 資源之前。 您可以在這份文件頂端的索引標籤，即可檢視不同的工具的文件。 這份文件包含使用 Azure 資源管理員建立應用程式閘道器。 若要使用精簡版本，請移至[建立傳統使用 PowerShell 部署應用程式閘道器](application-gateway-create-gateway.md)。


## <a name="before-you-begin"></a>開始之前

1. 使用 Web 平台安裝程式，安裝最新版 Azure PowerShell cmdlet。 您可以從下載並安裝最新版本的**Windows PowerShell**一節的[下載頁面](https://azure.microsoft.com/downloads/)。
2. 如果您有現有的虛擬網路，請選取現有的空白子網路，或在您現有的虛擬網路僅用於建立子網路的應用程式閘道器。 您無法部署應用程式閘道器至您要部署應用程式閘道後面的資源不同虛擬網路。
3. 您設定為使用應用程式閘道器的伺服器，必須存在或指派建立虛擬網路或公用 IP/VIP 其結束點。

## <a name="what-is-required-to-create-an-application-gateway"></a>若要建立的應用程式閘道器的必要條件為何？

- **後端伺服器集區︰**後端伺服器 IP 位址清單。 列出的 IP 位址應該是屬於虛擬網路的子網路或應該公用 IP/VIP。
- **後端伺服器集區設定︰**每個資料庫有設定，例如連接埠與通訊協定，cookie 為基礎的相關性。 這些設定會連結到資料庫]，就會套用到所有集區中的伺服器。
- **前端連接埠︰**此連接埠是開啟應用程式閘道器公用連接埠。 流量碰到此連接埠]，然後會被重新導向至其中一個後端伺服器。
- **接聽︰**接聽具有前端連接埠通訊協定 （Http 或 Https，這些值會區分大小寫），以及的 SSL 憑證名稱 （如果設定 SSL 卸載）。
- **規則︰**此規則繫結的後端伺服器集區接聽程式，並定義流量應該會導向至時叫接聽的後端伺服器集區。

## <a name="create-an-application-gateway"></a>建立應用程式閘道器

使用 Azure 傳統和 Azure 資源管理員之間的差異是您建立的應用程式閘道器的與項目需要設定的順序。

與資源管理員，請使用應用程式閘道的所有項目是個別設定，然後將放在一起，建立應用程式閘道器資源。

以下是需要建立的應用程式閘道器的步驟。

## <a name="create-a-resource-group-for-resource-manager"></a>建立資源群組的 [資源管理員

請確認您使用的最新版的 PowerShell 的 Azure。 [使用 Windows PowerShell 與資源管理員](../powershell-azure-resource-manager.md)的詳細資訊。

### <a name="step-1"></a>步驟 1

登入 Azure

    Login-AzureRmAccount

系統會提示您的認證以進行驗證。

### <a name="step-2"></a>步驟 2

核取訂閱的帳戶。

    Get-AzureRmSubscription

### <a name="step-3"></a>步驟 3

選擇您要使用的 Azure 訂閱。

    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"

### <a name="step-4"></a>步驟 4

建立資源群組 （略過此步驟如果您使用現有的資源群組）。

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

Azure 資源管理員需要所有資源群組都指定的位置。 此位置作為預設位置的 [資源] 群組中的資源。 請確認所有命令來建立應用程式閘道都使用相同的資源群組。

在上述範例中，我們建立名為 「 appgw 路由 」 和 「 西部美國 」 的位置的資源群組。

>[AZURE.NOTE] 如果您需要設定自訂檢查您的應用程式閘道器，請參閱[建立自訂探查使用 PowerShell 應用程式閘道](application-gateway-create-probe-ps.md)。 查看[自訂探查和健康監視](application-gateway-probe-overview.md)的詳細資訊。

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>建立虛擬網路和應用程式閘道器的子網路

下列範例會示範如何使用資源管理員建立虛擬網路。

### <a name="step-1"></a>步驟 1

指定要用來建立虛擬網路的子網路變數地址範圍 10.0.0.0/24。

    $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

### <a name="step-2"></a>步驟 2

建立虛擬網路的子網路 10.0.0.0/24 搭配使用前置詞 10.0.0.0/16 西美國地區具名資源群組 」 appgw-路由 」 中的 「 appgwvnet 」。

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

### <a name="step-3"></a>步驟 3

指派的下一個步驟，建立應用程式閘道器的子網路變數。

    $subnet=$vnet.Subnets[0]

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>建立前端設定的公用 IP 位址

建立公用 IP 資源 」 publicIP01 」 資源群組 」 appgw-路由 」 西美國區域中。

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic


## <a name="create-an-application-gateway-configuration-object"></a>建立應用程式閘道器組態物件

建立應用程式閘道之前，您必須設定設定的所有項目。 下列步驟建立所需的應用程式閘道器資源設定項目。

### <a name="step-1"></a>步驟 1

建立名為 「 gatewayIP01 」 應用程式的閘道器 IP 設定。 應用程式閘道啟動時，它會挑選設定子網路中的 IP 位址，並將網路流量路由至後端 IP 集區中的 IP 位址。 請記住，每個執行個體需要一個 IP 位址。

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

### <a name="step-2"></a>步驟 2

設定名稱為 「 pool01 」 的 IP 位址的後端 IP 位址集區 」 134.170.185.46，134.170.188.221,134.170.185.50 」。 這些 IP 位址是接收來自前端的 IP 端點網路流量的 IP 位址。 您會取代先前的 IP 位址，若要新增您自己的應用程式 IP 位址端點。

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

### <a name="step-3"></a>步驟 3

設定應用程式閘道器設定 「 poolsetting01 」 負載平衡網路流量後端資料庫中。

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled

### <a name="step-4"></a>步驟 4

設定名為 「 frontendport01 」 的公用 IP 端點的前端 IP 連接埠。

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

### <a name="step-5"></a>步驟 5

建立名為 「 fipconfig01 」 的前端 IP 設定，並與前端 IP 設定關聯的公用 IP 位址。

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

### <a name="step-6"></a>步驟 6

建立接聽名稱 「 listener01 」，並建立關聯的前端的連接埠前端 IP 設定。

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

### <a name="step-7"></a>步驟 7

建立負載平衡器路由規則命名為 「 rule01 」，以設定負載平衡器行為。

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### <a name="step-8"></a>步驟 8

設定應用程式閘道執行個體大小。

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

>[AZURE.NOTE]  *InstanceCount*的預設值是 2 的最大值 10。 *GatewaySize*的預設值是 [中]。 您可以選擇 Standard_Small、 Standard_Medium，與 Standard_Large 之間。

## <a name="create-an-application-gateway-by-using-new-azurermapplicationgateway"></a>使用新增 AzureRmApplicationGateway 建立應用程式閘道器

建立應用程式閘道器的所有設定項目從先前的步驟。 在此範例中，應用程式閘道稱為 「 appgwtest 」。

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

### <a name="step-9"></a>步驟 9

從 [附加至應用程式閘道器的公用 IP 資源擷取 DNS 及 VIP 應用程式閘道器的詳細資料。

    Get-AzureRmPublicIpAddress -Name publicIP01 -ResourceGroupName appgw-rg  

## <a name="delete-an-application-gateway"></a>刪除應用程式閘道器

若要刪除的應用程式閘道器，請遵循下列步驟︰

### <a name="step-1"></a>步驟 1

取得應用程式閘道器物件，然後與變數 「 $getgw 」 建立關聯。

    $getgw = Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### <a name="step-2"></a>步驟 2

若要停止應用程式閘道使用**停駐點 AzureRmApplicationGateway** 。

    Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  

停止狀態中的應用程式閘道器後，使用**移除 AzureRmApplicationGateway** cmdlet 來移除這項服務。

    Remove-AzureRmApplicationGateway -Name $appgwtest -ResourceGroupName appgw-rg -Force

>[AZURE.NOTE] **-強制**切換可以用來隱藏 [移除] 確認訊息。

若要驗證服務已經移除，您可以使用**取得 AzureRmApplicationGateway**指令程式。 不需要此步驟。

    Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

## <a name="get-application-gateway-dns-name"></a>取得應用程式閘道器 DNS 名稱

閘道器建立後下, 一步是設定通訊前端。 使用時的公用 IP，應用程式閘道需要不是易記的動態指派的 DNS 名稱。 若要確保使用者可以按下應用程式閘道器的 CNAME 記錄可以用來指向應用程式閘道器公用端點。 [設定 Azure 中的自訂網域名稱](../cloud-services/cloud-services-custom-domain-name-portal.md)。 若要這麼做，擷取的應用程式閘道器使用 PublicIPAddress 項目附加到應用程式閘道及其相關聯的 IP/DNS 名稱的詳細資料。 應用程式閘道器的 DNS 名稱應用於建立指向這個 DNS 名稱將兩個 web 應用程式的 CNAME 記錄。 不建議使用 A 記錄，因為 VIP 可能會在重新啟動應用程式的閘道器的變更。
    
    Get-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
        
    Name                     : publicIP01
    ResourceGroupName        : appgw-RG
    Location                 : westus
    Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
    Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
    ResourceGuid             : 00000000-0000-0000-0000-000000000000
    ProvisioningState        : Succeeded
    Tags                     : 
    PublicIpAllocationMethod : Dynamic
    IpAddress                : xx.xx.xxx.xx
    PublicIpAddressVersion   : IPv4
    IdleTimeoutInMinutes     : 4
    IpConfiguration          : {
                                 "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                               Configurations/frontend1"
                               }
    DnsSettings              : {
                                 "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                               }

## <a name="next-steps"></a>後續步驟

如果您想要設定 SSL 卸載，請參閱[設定應用程式的閘道 SSL 卸載](application-gateway-ssl.md)。

如果您想要設定為用於內部負載平衡器應用程式閘道器，請參閱[建立內部負載平衡器 (ILB) 應用程式閘道](application-gateway-ilb.md)。

若要深入瞭解一般負載平衡的選項，請參閱︰

- [Azure 負載平衡器](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure 流量管理員](https://azure.microsoft.com/documentation/services/traffic-manager/)
