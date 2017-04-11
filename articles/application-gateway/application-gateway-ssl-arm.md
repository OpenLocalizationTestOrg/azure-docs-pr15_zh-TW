<properties
   pageTitle="使用 Azure 資源管理員設定的 SSL 卸載應用程式閘道 |Microsoft Azure"
   description="本頁面提供使用 SSL 建立應用程式閘道指示卸載使用 Azure 資源管理員"
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
   ms.date="09/09/2016"
   ms.author="gwallace"/>

# <a name="configure-an-application-gateway-for-ssl-offload-by-using-azure-resource-manager"></a>使用 Azure 資源管理員設定 SSL 卸載應用程式閘道的器

> [AZURE.SELECTOR]
-[Azure 入口網站](application-gateway-ssl-portal.md)
-[Azure 資源管理員 PowerShell](application-gateway-ssl-arm.md)
-[Azure 傳統 PowerShell](application-gateway-ssl.md)

 Azure 應用程式閘道器可以終止安全通訊端層 (SSL) 工作階段，若要避免在網頁伺服器陣列的成本 SSL 解密工作的閘道器設定。 SSL 卸載也可簡化的前端伺服器設定及管理 web 應用程式。

## <a name="before-you-begin"></a>開始之前

1. 使用 Web 平台安裝程式，安裝最新版 Azure PowerShell cmdlet。 您可以從下載並安裝最新版本的**Windows PowerShell**一節的[下載頁面](https://azure.microsoft.com/downloads/)。
2. 您建立虛擬網路和應用程式閘道器的子網路。 請確認沒有虛擬機器或雲端部署使用子網路。 應用程式的閘道器必須是單獨子虛擬網路。
3. 必須要有您要使用的應用程式閘道器設定伺服器或指派建立虛擬網路或公用 IP/VIP 其結束點。

## <a name="what-is-required-to-create-an-application-gateway"></a>若要建立的應用程式閘道器的必要條件為何？


- **後端伺服器集區︰**後端伺服器 IP 位址清單。 列出的 IP 位址應該是屬於虛擬網路的子網路或應該公用 IP/VIP。
- **後端伺服器資料庫設定︰**每個資料庫有設定，例如連接埠與通訊協定，cookie 為基礎的相關性。 這些設定會連結到資料庫]，就會套用到所有資料庫中的伺服器。
- **前端連接埠︰**此連接埠是開啟應用程式閘道器公用連接埠。 流量達到此連接埠]，然後會被重新導向至其中一個後端伺服器。
- **接聽︰**接聽具有前端連接埠通訊協定 （Http 或 Https，這些設定會區分大小寫），以及的 SSL 憑證名稱 （如果設定 SSL 卸載）。
- **規則︰**此規則繫結接聽和伺服器後端資料庫，並定義流量在時叫接聽會導向至的後端伺服器集區。 目前為支援*基本*的規則。 *基本*規則是循環載入通訊群組。

**其他設定備忘稿**

SSL 憑證設定，請在**HttpListener**的通訊協定應該變更為*Https* （區分大小寫）。 **SslCertificate**項目會新增至**HttpListener** ，以設定 SSL 憑證變數值。 前端連接埠應該會更新以 443。

**若要啟用 cookie 為基礎的相關性**︰ 您可以設定應用程式閘道確保從用戶端工作階段要求一律會被導向網頁伺服器陣列中的相同 vm。 插入工作階段 cookie，可讓閘道器至適當地將流量導向是這種情況。 若要啟用 cookie 為基礎的相關性，設定為 [*已啟用* **CookieBasedAffinity** **BackendHttpSettings**元素中。


## <a name="create-an-application-gateway"></a>建立應用程式閘道器

使用傳統 Azure 部署模型和 Azure 資源管理員之間的差異為您建立的應用程式閘道和需要設定的項目順序。

使用資源管理員，使用應用程式閘道的所有元件是個別設定，然後將放在一起，建立一個應用程式的閘道器資源。


建立應用程式閘道器時所需的步驟如下︰

1. 建立資源群組的 [資源管理員
2. 建立虛擬網路、 子網路及應用程式閘道器的公用 IP
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

建立資源群組 （略過此步驟如果您使用現有的資源群組）。

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

Azure 資源管理員需要所有資源群組都指定的位置。 資源群組中的資源為預設的位置使用此設定。 請確認所有命令來建立應用程式閘道會都使用相同的資源群組。

在上述範例中，我們建立名為 「 appgw 路由 」 和 「 西部美國 」 的位置的資源群組。

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>建立虛擬網路和應用程式閘道器的子網路

下列範例會示範如何建立虛擬網路使用資源管理員︰

### <a name="step-1"></a>步驟 1

    $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

此範例會將地址範圍 10.0.0.0/24 指派給要用來建立虛擬網路的子網路變數。

### <a name="step-2"></a>步驟 2

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

此範例會建立虛擬網路的子網路 10.0.0.0/24 搭配使用前置詞 10.0.0.0/16 西美國地區具名資源群組 」 appgw-路由 」 中的 「 appgwvnet 」。

### <a name="step-3"></a>步驟 3

    $subnet = $vnet.Subnets[0]

此範例會將子網路物件指派給變數 $subnet 的下一個步驟。

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>建立前端設定的公用 IP 位址

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic

此範例會建立公用 IP 資源 」 publicIP01 」 資源群組 」 appgw-路由 」 西美國區域中。


## <a name="create-an-application-gateway-configuration-object"></a>建立應用程式閘道器組態物件

### <a name="step-1"></a>步驟 1

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

此範例會建立名為 「 gatewayIP01 」 應用程式的閘道器 IP 設定。 應用程式閘道啟動時，它會挑選設定子網路中的 IP 位址，並將網路流量路由傳送後端 IP 集區中的 IP 位址。 請記住，每個執行個體需要一個 IP 位址。

### <a name="step-2"></a>步驟 2

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

此範例設定名為 「 pool01 」 的 IP 位址的後端 IP 位址集區 」 134.170.185.46，134.170.188.221,134.170.185.50。 」 這些值將會收到來自前端 IP 端點網路流量的 IP 位址。 取代您的 web 應用程式端點的 IP 位址與上述範例的 IP 位址。

### <a name="step-3"></a>步驟 3

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled

此範例設定應用程式閘道器設定 「 poolsetting01 」 負載平衡後端資料庫中的網路流量。

### <a name="step-4"></a>步驟 4

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 443

此範例設定名為 「 frontendport01 」 的公用 IP 端點的前端 IP 連接埠。

### <a name="step-5"></a>步驟 5

    $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path for certificate file> -Password ‘<password>’

此範例中設定 SSL 連線所使用的憑證。 憑證必須以.pfx 格式及密碼必須介於 4 到 12 個字元。

### <a name="step-6"></a>步驟 6

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

此範例會建立名為 「 fipconfig01 」 的前端 IP 設定，並與前端 IP 設定產生關聯的公用 IP 位址。

### <a name="step-7"></a>步驟 7

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert


此範例會建立接聽名稱 「 listener01 」，並將前端的連接埠前端 IP 設定與憑證產生關聯。

### <a name="step-8"></a>步驟 8

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

此範例會建立負載平衡器路由規則命名為 「 rule01 」，以設定負載平衡器行為。

### <a name="step-9"></a>步驟 9

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

此範例會設定應用程式閘道執行個體大小。

>[AZURE.NOTE]  *InstanceCount*的預設值是 2 的最大值 10。 *GatewaySize*的預設值是 [中]。 您可以選擇 Standard_Small、 Standard_Medium，與 Standard_Large 之間。

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>使用新增 AzureApplicationGateway 建立應用程式閘道器

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert

此範例會建立應用程式閘道，所有設定項目從先前的步驟。 在範例中，應用程式閘道稱為 「 appgwtest 」。

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

如果您想要設定為用於內部負載平衡器 (ILB) 應用程式閘道器，請參閱[建立內部負載平衡器 (ILB) 應用程式閘道](application-gateway-ilb.md)。

若要深入瞭解一般負載平衡的選項，請參閱︰

- [Azure 負載平衡器](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure 流量管理員](https://azure.microsoft.com/documentation/services/traffic-manager/)
