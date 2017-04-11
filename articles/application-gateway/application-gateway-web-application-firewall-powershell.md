<properties
   pageTitle="在新的或現有的應用程式閘道器設定 Web 應用程式防火牆 |Microsoft Azure"
   description="本文提供如何開始使用 web 應用程式防火牆現有或新增應用程式閘道指引。"
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
   ms.date="10/25/2016"
   ms.author="gwallace"/>

# <a name="configure-web-application-firewall-on-a-new-or-existing-application-gateway"></a>在新的或現有的應用程式閘道器設定 Web 應用程式防火牆

> [AZURE.SELECTOR]
- [Azure 入口網站](application-gateway-web-application-firewall-portal.md)
- [Azure 資源管理員 PowerShell](application-gateway-web-application-firewall-powershell.md)

Azure 應用程式的閘道器的 web 應用程式防火牆 (WAF) 可從網頁的常見攻擊，例如 SQL 插入、 跨網站指令碼攻擊和工作階段倘若保護 web 應用程式。

Azure 應用程式閘道是有 7 層負載平衡器。 是否位於雲端或內部部署，它會提供容錯移轉時，效能路由 HTTP 要求之間不同的伺服器。 應用程式提供許多應用程式傳送控制器 (ADC) 的功能，包括 HTTP 負載平衡、 cookie 為基礎的工作階段相關性、 安全通訊端層 (SSL) 卸載自訂健康探查、 支援多重網站和許多其他。 若要尋找的受支援功能的完整清單，請造訪應用程式閘道器概觀

下列文章︰ 顯示如何[新增至現有的應用程式閘道器的 web 應用程式防火牆](#add-web-application-firewall-to-an-existing-application-gateway)並[建立 web 應用程式防火牆使用的應用程式閘道器](#create-an-application-gateway-with-web-application-firewall)。

![案例圖像][scenario]

## <a name="waf-configuration-differences"></a>WAF 設定差異

如果您已讀取[建立使用 PowerShell 應用程式閘道器](application-gateway-create-gateway-arm.md)，您可以瞭解時建立應用程式閘道設定的 SKU 設定。 WAF 提供定義應用程式閘道設定 SKU 時的其他設定。 沒有其他應用程式閘道器本身上所做的變更。

**SKU** -一般應用程式的閘道器不 WAF 支援**標準\_小型**，**標準\_中型**，及**標準\_大**大小。 隨著 WAF，有兩個額外的 Sku， **WAF\_中型**和**WAF\_大**。 WAF 不支援在小型應用程式的閘道器。

**層**-可用的值是**標準**或**WAF**。 使用 Web 應用程式的防火牆，您必須選擇**WAF** 。

**模式**這項設定是 WAF 的模式。 允許的值會**偵測**並**防止**。 偵測模式中 WAF 設定後，所有威脅會都儲存在記錄檔。 防止模式中仍會記錄事件，但攻擊從應用程式閘道接收 403 未經授權的回應。

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>將 web 應用程式防火牆新增至現有的應用程式閘道器

請確認您使用的最新版的 PowerShell 的 Azure。 [使用 Windows PowerShell 與資源管理員](../powershell-azure-resource-manager.md)的詳細資訊。

### <a name="step-1"></a>步驟 1

登入您的 Azure 帳戶。

    Login-AzureRmAccount

### <a name="step-2"></a>步驟 2

選取要用於此案例訂閱]。

    Select-AzureRmSubscription -SubscriptionName "<Subscription name>"

### <a name="step-3"></a>步驟 3

擷取您要新增至 Web 應用程式防火牆的閘道器。

    $gw = Get-AzureRmApplicationGateway -Name "AdatumGateway" -ResourceGroupName "MyResourceGroup"


### <a name="step-4"></a>步驟 4

設定 web 應用程式防火牆 sku。 可用的大小會**WAF\_大**和**WAF\_中型**。 使用 web 應用程式防火牆時層必須**WAF**、 設定 sku 時，就必須確認容量。

    $gw | Set-AzureRmApplicationGatewaySku -Name WAF_Large -Tier WAF -Capacity 2

### <a name="step-5"></a>步驟 5

下列範例中所定義，請設定 WAF 設定︰

**WafMode**設定，可用的值會防止與偵測。

    $gw | Set-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode Prevention

### <a name="step-6"></a>步驟 6

使用上述步驟中定義的設定來更新應用程式閘道器。

    Set-AzureRmApplicationGateway -ApplicationGateway $gw

這個命令的 Web 應用程式防火牆更新應用程式閘道器。 建議您檢視[應用程式閘道器診斷](application-gateway-diagnostics.md)若要瞭解如何檢視您的應用程式閘道器的記錄。 WAF 的安全性特性，因為需要定期檢閱，若要瞭解 web 應用程式的安全性狀態記錄檔。

## <a name="create-an-application-gateway-with-web-application-firewall"></a>建立 Web 應用程式防火牆應用程式閘道器

下列步驟會引導您完成從開始到 Web 應用程式防火牆以建立應用程式閘道器的結束的整個程序。

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

    Select-AzureRmsubscription -SubscriptionName "<Subscription name>"

### <a name="step-4"></a>步驟 4

建立資源群組 （略過此步驟如果您使用現有的資源群組）。

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

Azure 資源管理員需要所有資源群組都指定的位置。 此位置作為預設位置的 [資源] 群組中的資源。 請確認所有命令來建立應用程式閘道會都使用相同的資源群組。

在上述範例中，我們建立名為 「 appgw 路由 」 和 「 西部美國 」 的位置的資源群組。

>[AZURE.NOTE] 如果您需要設定自訂檢查您的應用程式閘道器，請參閱[建立自訂探查使用 PowerShell 應用程式閘道](application-gateway-create-probe-ps.md)。 查看[自訂探查和健康監視](application-gateway-probe-overview.md)的詳細資訊。

### <a name="step-5"></a>步驟 5

指派位址範圍的子網路用於應用程式閘道器本身。

    $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

> [AZURE.NOTE] 應用程式的子網路應有 28 遮罩位元的最小值。 此值會保留 10 提供的地址的應用程式閘道執行個體的子網路。 具有較小的子網路，您可能無法在未來新增更多應用程式閘道執行個體。

### <a name="step-6"></a>步驟 6

指定要使用的後端位址集區位址範圍。

    $nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

### <a name="step-7"></a>步驟 7

建立在步驟中的 [資源] 群組中的上一個子網路的虛擬網路建立︰[建立資源群組](#create-the-resource-group)

    $vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet

### <a name="step-8"></a>步驟 8

擷取的虛擬網路資源及子網路資源可用於下列步驟︰

    $vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
    $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
    $nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet

### <a name="step-9"></a>步驟 9

建立要用於應用程式閘道器的公用 IP 資源。 使用此公用的 IP 位址中執行下列步驟︰

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name 'appgwpip' -Location "West US" -AllocationMethod Dynamic

> [AZURE.IMPORTANT] 應用程式閘道不支援使用網域標籤定義，以建立的公用 IP 位址。 只有公用 IP 位址，以動態方式建立的網域標籤都支援。 如果您需要的應用程式閘道器的易記 dns 名稱時，建議使用為別名的 cname 記錄。

### <a name="step-10"></a>步驟 10

建立應用程式閘道之前，您必須設定設定的所有項目。 下列步驟建立所需的應用程式閘道器資源設定項目。

建立應用程式閘道器 IP 設定，此設定應用程式閘道器會使用何種子網路。 應用程式閘道啟動時，它會挑選子網路設定中的 IP 位址，並將網路流量路由至後端 IP 集區中的 IP 位址。 請記住，每個執行個體需要一個 IP 位址。

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

### <a name="step-11"></a>步驟 11

設定後端的 IP 位址集區的後端網頁伺服器的 IP 位址。 這些 IP 位址是接收來自前端 IP 端點網路流量的 IP 位址。 您會取代下列 IP 位址新增您自己的應用程式 IP 位址結束點。

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3

### <a name="step-12"></a>步驟 12

上傳用於 ssl 憑證啟用後端資料庫資源。

    $authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile <full path to .cer file>

### <a name="step-13"></a>步驟 13

設定應用程式的閘道器後端 http 設定。 指定 http 設定的上一個步驟中上傳的憑證。

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert

### <a name="step-14"></a>步驟 14

設定公用 IP 端點的前端 IP 連接埠。 此連接埠是使用者連線至的連接埠。

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443

### <a name="step-15"></a>步驟 15

建立一個前端 IP 設定，此設定對應至前端應用程式閘道器的私人或公用 ip 位址。 下列步驟將前端 IP 設定與關聯前述步驟中的公用 IP 位址。

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

### <a name="step-16"></a>步驟 16

設定應用程式閘道器的憑證。 這個憑證用於解密並重新加密上的應用程式閘道器的流量。

    $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>

### <a name="step-17"></a>步驟 17

建立應用程式閘道器的 HTTP 接聽。 指定要使用的前端 ip 設定、 連接埠及 ssl 憑證。

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

### <a name="step-18"></a>步驟 18

建立負載平衡器路由規則，以設定負載平衡器行為。 在此範例中，建立基本循環規則。

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   
### <a name="step-19"></a>步驟 19

設定應用程式閘道執行個體大小。

    $sku = New-AzureRmApplicationGatewaySku -Name WAF_Medium -Tier WAF -Capacity 2

>[AZURE.NOTE]  您可以選擇**WAF\_中型**和**WAF\_大**，層時使用 WAF 永遠**WAF**。 容量是介於 1 和 10 之間的任何數字。

### <a name="step-20"></a>步驟 20

針對 WAF 設定模式，可接受的值會**防止**與**偵測**。

    $config = New-AzureRmApplicationGatewayWafConfig -Enabled $true -WafMode "Prevention"

### <a name="step-21"></a>步驟 21

建立應用程式閘道器的所有設定項目從先前的步驟。 在此範例中，應用程式閘道稱為 「 appgwtest 」。

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert

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

瞭解如何設定診斷記錄]，瀏覽[應用程式閘道器診斷](application-gateway-diagnostics.md)登 Web 應用程式防火牆會偵測到或無法的事件


[scenario]: ./media/application-gateway-web-application-firewall-powershell/scenario.png