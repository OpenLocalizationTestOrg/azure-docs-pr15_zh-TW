<properties
   pageTitle="建立裝載多個網站的應用程式閘道器 |Microsoft Azure"
   description="本頁面提供建立、 設定 Azure 應用程式閘道裝載於相同閘道的多個 web 應用程式的指示進行。"
   documentationCenter="na"
   services="application-gateway"
   authors="amsriva"
   manager="rossort"
   editor="amsriva"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="amsriva"/>


# <a name="create-an-application-gateway-for-hosting-multiple-web-applications"></a>建立應用程式閘道裝載多個 web 應用程式

> [AZURE.SELECTOR]
- [Azure 入口網站](application-gateway-create-multisite-portal.md)
- [Azure 資源管理員 PowerShell](application-gateway-create-multisite-azureresourcemanager-powershell.md)

多個網站主機服務可讓您部署上相同的應用程式閘道器的多個 web 應用程式。 必須在傳入 HTTP 邀請中，以決定哪些接聽會收到流量主機標題的目前狀態。 接聽然後引導至適當的後端資料庫的資料傳輸規則定義中的閘道器設定。 啟用 SSL web 應用程式中，選擇正確的接聽 web 流量的伺服器名稱的指示 (SNI) 副檔名必須使用應用程式的閘道器。 常用的多個網站主機服務是負載平衡要求以不同的後端伺服器集區的其他網站網域。 同樣的相同的根網域的多個子網域可能也會裝載於相同的應用程式閘道器。

## <a name="scenario"></a>案例

在下列範例中，應用程式的閘道器服務 contoso.com 和 fabrikam.com 的流量與兩個後端伺服器集區︰ contoso server 集區和 fabrikam server 集區。 類似的安裝程式無法用於 app.contoso.com 等 blog.contoso.com 主機子網域。

![imageURLroute](./media/application-gateway-create-multisite-azureresourcemanager-powershell/multisite.png)

## <a name="before-you-begin"></a>開始之前

1. 使用 Web 平台安裝程式，安裝最新版 Azure PowerShell cmdlet。 您可以從下載並安裝最新版本的**Windows PowerShell**一節的[下載頁面](https://azure.microsoft.com/downloads/)。
2. 新增至後端資料庫，若要使用的應用程式閘道器的伺服器，必須存在或其結束點建立虛擬的網路中不同的子網路或公用 IP/VIP 指派中。

## <a name="requirements"></a>需求

- **後端伺服器集區︰**後端伺服器 IP 位址清單。 列出的 IP 位址應該是屬於虛擬網路的子網路或應該公用 IP/VIP。 也可以用 FQDN。
- **後端伺服器資料庫設定︰**每個資料庫有設定，例如連接埠與通訊協定，cookie 為基礎的相關性。 這些設定會連結到資料庫]，就會套用到所有資料庫中的伺服器。
- **前端連接埠︰**此連接埠是開啟應用程式閘道器公用連接埠。 流量碰到此連接埠]，然後會被重新導向至其中一個後端伺服器。
- **接聽︰**接聽具有前端連接埠通訊協定 （Http 或 Https，這些值會區分大小寫），以及的 SSL 憑證名稱 （如果設定 SSL 卸載）。 多網站啟用應用程式閘道主機名稱和 SNI 指標也會加入。
- **規則︰**此規則繫結後端伺服器集區接聽程式，並定義流量在時叫接聽會導向至的後端伺服器集區。

## <a name="create-an-application-gateway"></a>建立應用程式閘道器

若要建立應用程式閘道所需的步驟如下︰

1. 建立資源群組的 [資源管理員。
2. 建立虛擬網路與應用程式閘道器的公用 IP 子網路。
3. 建立應用程式閘道器組態物件。
4. 建立應用程式閘道器資源。

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

    Select-AzureRmSubscription -SubscriptionName "Name of subscription"

### <a name="step-4"></a>步驟 4

建立資源群組 （略過此步驟如果您使用現有的資源群組）。

    New-AzureRmResourceGroup -Name appgw-RG -location "West US"

或者您也可以建立應用程式的閘道器的資源群組的標記︰

    $resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US" -Tags @{Name = "testtag"; Value = "Application Gateway multiple site"}

Azure 資源管理員需要所有資源群組都指定的位置。 此位置作為預設位置的 [資源] 群組中的資源。 請確認所有命令來建立應用程式閘道都使用相同的資源群組。

在上述範例中，我們建立名為 「 appgw-路由 」 與 「 西部美國 」 的位置的資源群組。

>[AZURE.NOTE] 如果您需要設定自訂檢查您的應用程式閘道器，請參閱[建立自訂探查使用 PowerShell 應用程式閘道](application-gateway-create-probe-ps.md)。 [自訂探查和健康監視](application-gateway-probe-overview.md)的詳細資訊，請瀏覽。

## <a name="create-a-virtual-network-and-subnets"></a>建立虛擬網路和子網路

下列範例會示範如何使用資源管理員建立虛擬網路。 在此步驟中建立兩個子網路。 第一個子網路適用於應用程式閘道器本身。 應用程式閘道需要保留其執行個體自己子網路。 僅限其他應用程式的閘道器可以部署子網路。 第二個子網路用來儲存應用程式後端伺服器。

### <a name="step-1"></a>步驟 1

將地址範圍 10.0.0.0/24 指派到子網路變數，用來儲存應用程式閘道器。

    $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name appgatewaysubnet -AddressPrefix 10.0.0.0/24

### <a name="step-2"></a>步驟 2

將地址範圍 10.0.1.0/24 指派給 subnet2 變數用於後端資料庫。

    $subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name backendsubnet -AddressPrefix 10.0.1.0/24

### <a name="step-3"></a>步驟 3

建立虛擬網路命名為 「 資源群組 」 appgw-路由 」 中的 「 appgwvnet 」 的子網路 10.0.0.0/24 搭配使用前置詞 10.0.0.0/16 西美國地區和 10.0.1.0/24。

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet,$subnet2


### <a name="step-4"></a>步驟 4

指定子網路變數的下一個步驟中，建立應用程式閘道器。

    $appgatewaysubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name appgatewaysubnet -VirtualNetwork $vnet
    $backendsubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name backendsubnet -VirtualNetwork $vnet

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>建立前端設定的公用 IP 位址

建立公用 IP 資源 」 publicIP01 」 資源群組 」 appgw-路由 」 西美國區域中。

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic

啟動服務的 IP 位址被分派給應用程式閘道器。

## <a name="create-application-gateway-configuration"></a>建立應用程式閘道器組態

建立應用程式閘道之前，您必須設定設定的所有項目。 下列步驟建立所需的應用程式閘道器資源設定項目。

### <a name="step-1"></a>步驟 1

建立名為 「 gatewayIP01 」 應用程式的閘道器 IP 設定。 應用程式閘道啟動時，它會挑選設定子網路中的 IP 位址，並將網路流量路由傳送後端 IP 集區中的 IP 位址。 請記住，每個執行個體需要一個 IP 位址。

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $appgatewaysubnet

### <a name="step-2"></a>步驟 2

設定名為 「 pool01 」 和 「 pool2 」 的 IP 位址的後端 IP 位址集區 」 10.0.1.100，10.0.1.101,10.0.1.102 」 的 「 pool1 」 和 「 10.0.1.103 10.0.1.104、 10.0.1.105 」 的 「 pool2 」。

    $pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.0.1.100, 10.0.1.101, 10.0.1.102
    $pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 10.0.1.103, 10.0.1.104, 10.0.1.105

在此範例中，有兩個後端資料庫，將根據要求的網站的網路流量。 集區接收流量的網站 」 contoso.com 」 與其他資料庫接聽流量的網站 」 fabrikam.com 」。 您必須取代先前的 IP 位址，若要新增您自己的應用程式 IP 位址端點。 取代內部 IP 位址，您也可以為後端執行個體上使用的公用 IP 位址、 FQDN 或 VM 的 NIC。 使用 「-BackendFQDNs 」 中指定，而不是 IPs Fqdn PowerShell 參數。

### <a name="step-3"></a>步驟 3

設定應用程式閘道器設定 「 poolsetting01 」 和 「 poolsetting02 」 負載平衡網路流量後端資料庫中。 在此範例中，您設定不同的後端資料庫後端資料庫。 每個後端資料庫可以有自己的後端資料庫設定值。

    $poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120
    $poolSetting02 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting02" -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 240

### <a name="step-4"></a>步驟 4

設定公用 IP 端點前端 IP。

    $fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip

### <a name="step-5"></a>步驟 5

設定應用程式閘道器的前端連接埠。

    $fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 443

### <a name="step-6"></a>步驟 6

設定兩個 SSL 憑證的兩個網站我們將在此範例中所支援。 一個憑證 contoso.com 流量，而另一個是 fabrikam.com 流量。 這些憑證應該憑證授權單位發出您網站的憑證。 自我簽署的憑證的支援，但不是建議使用生產流量。

    $cert01 = New-AzureRmApplicationGatewaySslCertificate -Name contosocert -CertificateFile <file path> -Password <password>
    $cert02 = New-AzureRmApplicationGatewaySslCertificate -Name fabrikamcert -CertificateFile <file path> -Password <password>


### <a name="step-7"></a>步驟 7

在此範例中，設定兩個接聽兩個網站。 此步驟中設定的接聽公用 IP 位址、 連接埠] 及主機用來接收內送的流量。 主機名稱參數需要多個網站支援，應該會設定為適當的收到流量的網站。 RequireServerNameIndication 參數應該會設定為需要 SSL 支援在多個案例中主機的網站，則為 true。 如果需要 SSL 支援，您也需要指定來保護該 web 應用程式的流量 SSL 憑證。 FrontendIPConfiguration、 FrontendPort，和主機名稱必須是唯一接聽程式。 每個接聽程式可支援一個憑證。

    $listener01 = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Https -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -HostName "contoso11.com" -RequireServerNameIndication true  -SslCertificate $cert01
    $listener02 = New-AzureRmApplicationGatewayHttpListener -Name "listener02" -Protocol Https -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -HostName "fabrikam11.com" -RequireServerNameIndication true -SslCertificate $cert02

### <a name="step-8"></a>步驟 8

在此範例中建立兩個 web 應用程式的兩個規則設定。 接聽、 後端資料庫及 http 設定規則的緊密整合。 此步驟設定應用程式閘道器至使用基本路由的規則，請針對每個網站並列文字格式。 每個網站流量收到其設定的接聽程式，然後會引導其設定的後端資料庫，使用 BackendHttpSettings 中指定的屬性。

    $rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule01" -RuleType Basic -HttpListener $listener01 -BackendHttpSettings $poolSetting01 -BackendAddressPool $pool1
    $rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule02" -RuleType Basic -HttpListener $listener02 -BackendHttpSettings $poolSetting02 -BackendAddressPool $pool2

### <a name="step-9"></a>步驟 9

設定數執行個體和應用程式閘道器的大小。

    $sku = New-AzureRmApplicationGatewaySku -Name "Standard_Medium" -Tier Standard -Capacity 2

## <a name="create-application-gateway"></a>建立應用程式的閘道器

從先前的步驟的所有設定物件都建立應用程式閘道器。

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "West US" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 -Sku $sku -SslCertificates $cert01, $cert02


>[AZURE.IMPORTANT] 應用程式閘道器佈建是長時間執行的作業，而且可能需要一些時間才能完成。

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

瞭解如何保護您的網站與[應用程式閘道-Web 應用程式防火牆](application-gateway-webapplicationfirewall-overview.md)