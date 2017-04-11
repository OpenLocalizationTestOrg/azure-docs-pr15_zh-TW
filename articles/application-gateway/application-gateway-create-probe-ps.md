<properties
   pageTitle="使用 PowerShell 資源管理員] 中建立自訂的檢查應用程式的閘道器 |Microsoft Azure"
   description="瞭解如何使用 PowerShell 資源管理員] 中建立自訂的檢查應用程式的閘道器"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/06/2016"
   ms.author="gwallace" />

# <a name="create-a-custom-probe-for-azure-application-gateway-by-using-powershell-for-azure-resource-manager"></a>建立自訂的檢查 Azure 應用程式的閘道器，使用 PowerShell 的 Azure 資源管理員

> [AZURE.SELECTOR]
- [Azure 入口網站](application-gateway-create-probe-portal.md)
- [Azure 資源管理員 PowerShell](application-gateway-create-probe-ps.md)
- [Azure 傳統 PowerShell](application-gateway-create-probe-classic-ps.md)

[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][傳統的部署模型](application-gateway-create-probe-classic-ps.md)。

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

### <a name="step-1"></a>步驟 1

使用登入 AzureRmAccount 進行驗證。

    Login-AzureRmAccount

### <a name="step-2"></a>步驟 2

核取訂閱的帳戶。

    Get-AzureRmSubscription

### <a name="step-3"></a>步驟 3

選擇您要使用的 Azure 訂閱。 <BR>


    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### <a name="step-4"></a>步驟 4

建立資源群組 （略過此步驟如果您使用現有的資源群組）。

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

Azure 資源管理員需要所有資源群組都指定的位置。 此位置作為預設位置的 [資源] 群組中的資源。 請確認所有命令來建立應用程式閘道都使用相同的資源群組。

在上述範例中，我們建立名為 「 appgw 路由 」 和 「 西部美國 」 的位置的資源群組。

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>建立虛擬網路和應用程式閘道器的子網路

下列步驟建立虛擬網路和應用程式閘道器的子網路。

### <a name="step-1"></a>步驟 1


指定要用來建立虛擬網路的子網路變數地址範圍 10.0.0.0/24。

    $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

### <a name="step-2"></a>步驟 2

建立虛擬網路的子網路 10.0.0.0/24 搭配使用前置詞 10.0.0.0/16 西美國地區具名資源群組 」 appgw-路由 」 中的 「 appgwvnet 」。

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### <a name="step-3"></a>步驟 3

指派的下一個步驟，建立應用程式閘道器的子網路變數。

    $subnet = $vnet.Subnets[0]

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>建立前端設定的公用 IP 位址


建立公用 IP 資源 」 publicIP01 」 資源群組 」 appgw-路由 」 西美國區域中。

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name publicIP01 -Location "West US" -AllocationMethod Dynamic


## <a name="create-an-application-gateway-configuration-object-with-a-custom-probe"></a>建立自訂的檢查應用程式閘道器組態物件

您建立的應用程式閘道器之前，先設定設定的所有項目。 下列步驟建立所需的應用程式閘道器資源設定項目。

### <a name="step-1"></a>步驟 1

建立名為 「 gatewayIP01 」 應用程式的閘道器 IP 設定。 應用程式閘道啟動時，它會挑選設定子網路中的 IP 位址，並將網路流量路由傳送後端 IP 集區中的 IP 位址。 請記住，每個執行個體需要一個 IP 位址。

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet


### <a name="step-2"></a>步驟 2


設定名為 「 pool01 」 的 IP 位址的後端 IP 位址集區 」 134.170.185.46，134.170.188.221,134.170.185.50 」。 這些值將會收到來自前端 IP 端點網路流量的 IP 位址。 您取代上述新增您自己的應用程式 IP 位址端點的 IP 位址。

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50


### <a name="step-3"></a>步驟 3


在此步驟中設定自訂探查。

所用的參數是︰

- **間隔**-設定探查間隔檢查秒數。
- **逾時**-定義 HTTP 回應核取的檢查逾時。
- **主機名稱和路徑**-完整的 URL 路徑叫用應用程式閘道器，以判定執行個體的健康情況。 例如，如果您有網站 http://contoso.com/，然後自訂探查可以設定的 「 http://contoso.com/path/custompath.htm 」 探查檢查有順利 HTTP 回應。
- **UnhealthyThreshold**失敗加上標幟為的後端執行個體所需的 HTTP 回應的數目*不佳*。

<BR>

    $probe = New-AzureRmApplicationGatewayProbeConfig -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/path.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8

### <a name="step-4"></a>步驟 4

設定應用程式閘道器設定 「 poolsetting01 」 流量的後端資料庫中。 此步驟也有適用於應用程式閘道器要求的後端資料庫回應逾時設定。 當後端回應逾時的限制，應用程式的閘道器會取消邀請。 此值為不同僅適用於後端回應探查檢查探查逾時。

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 80

### <a name="step-5"></a>步驟 5

設定名為 「 frontendport01 」 的公用 IP 端點的前端 IP 連接埠。

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01 -Port 80

### <a name="step-6"></a>步驟 6

建立名為 「 fipconfig01 」 的前端 IP 設定，並與前端 IP 設定關聯的公用 IP 位址。


    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

### <a name="step-7"></a>步驟 7

建立接聽名稱 「 listener01 」，並建立關聯的前端的連接埠前端 IP 設定。

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

### <a name="step-8"></a>步驟 8

建立負載平衡器路由規則命名為 「 rule01 」，以設定負載平衡器行為。

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### <a name="step-9"></a>步驟 9

設定應用程式閘道執行個體大小。

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2


>[AZURE.NOTE]  *InstanceCount*的預設值是 2 的最大值 10。 *GatewaySize*的預設值是 [中]。 您可以選擇 Standard_Small、 Standard_Medium，與 Standard_Large 之間。

## <a name="create-an-application-gateway-by-using-new-azurermapplicationgateway"></a>使用新增 AzureRmApplicationGateway 建立應用程式閘道器

建立應用程式閘道使用上述步驟中的所有設定項目。 在此範例中，應用程式閘道稱為 「 appgwtest 」。

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -Probes $probe -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

## <a name="add-a-probe-to-an-existing-application-gateway"></a>新增至現有的應用程式閘道器的 [檢查

您必須將自訂的檢查新增至現有的應用程式閘道器的四個步驟。

### <a name="step-1"></a>步驟 1

使用**取得 AzureRmApplicationGateway**載入 PowerShell 變數應用程式閘道器資源。

    $getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### <a name="step-2"></a>步驟 2

將現有的閘道器組態檢查。

    $getgw = Add-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/custompath.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8


在範例中，自訂探查會設為檢查 URL 路徑 contoso.com/path/custompath.htm 每隔 30 秒。 設定 120 秒的逾時臨界值是 8 失敗的探查要求的最大的數字。

### <a name="step-3"></a>步驟 3

使用的後端資料庫設定的設定和逾時新增探查**-設定-AzureRmApplicationGatewayBackendHttpSettings**。


     $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 120

### <a name="step-4"></a>步驟 4

使用**設定 AzureRmApplicationGateway**應用程式閘道儲存設定。

    Set-AzureRmApplicationGateway -ApplicationGateway $getgw

## <a name="remove-a-probe-from-an-existing-application-gateway"></a>檢查移除現有的應用程式閘道器

以下是自訂的檢查移除現有的應用程式閘道器的步驟。

### <a name="step-1"></a>步驟 1

使用**取得 AzureRmApplicationGateway**載入 PowerShell 變數應用程式閘道器資源。

    $getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg


### <a name="step-2"></a>步驟 2

使用**移除 AzureRmApplicationGatewayProbeConfig**移除應用程式閘道探查設定。

    $getgw = Remove-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name $getgw.Probes.name

### <a name="step-3"></a>步驟 3

使用更新的後端資料庫設定，若要移除探查及逾時設定**-設定-AzureRmApplicationGatewayBackendHttpSettings**。


     $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol http -CookieBasedAffinity Disabled

### <a name="step-4"></a>步驟 4

使用**設定 AzureRmApplicationGateway**應用程式閘道儲存設定。 

    Set-AzureRmApplicationGateway -ApplicationGateway $getgw

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

瞭解如何設定 SSL 卸載瀏覽[設定 SSL 卸載](application-gateway-ssl-arm.md)

