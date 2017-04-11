<properties
    pageTitle="設定應用程式閘道 SSL 原則] 和 [端對端 SSL |Microsoft Azure"
    description="本文將說明如何設定應用程式使用 PowerShell 的 Azure 資源管理員的閘道器的端對端 SSL"
    services="application-gateway"
    documentationCenter="na"
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

# <a name="configure-ssl-policy-and-end-to-end-ssl-with-application-gateway-using-powershell"></a>使用 PowerShell 的應用程式閘道器設定 SSL 原則] 和 [端對端 SSL

## <a name="overview"></a>概觀

應用程式閘道支援資料傳輸的端對端的加密。 應用程式的閘道器會在應用程式閘道 SSL 連線。 閘道器適用於流量路由規則、 重新加密封包，然後轉送至適當的後端根據所定義的路由規則封包。 網頁伺服器的任何回應會回到使用者經歷的相同程序。

其他功能的應用程式閘道支援已停用某些 SSL 通訊協定版本。 應用程式閘道支援停用下列通訊協定版本。TLSv1.0 TLSv1.1 與 TLSv1.2。

> [AZURE.NOTE] SSL 2.0 SSL 3.0 預設為停用，並不會啟用。 他們會被視為不安全，而無法使用應用程式的閘道器

![案例圖像][scenario]

## <a name="scenario"></a>案例

在此案例中，您可以瞭解如何建立使用使用 PowerShell 的端對端 SSL 應用程式閘道器。

將這種情況︰

- 建立名為 「 appgw 路由 「 資源群組
- 建立名為 「 appgwvnet 「 10.0.0.0/16 保留 CIDR 區塊虛擬網路。
- 建立兩個子網路，稱為 「 appgwsubnet 」 和 「 appsubnet 」。
- 建立支援停用某些 SSL 通訊協定的端對端加密小型應用程式閘道器。

## <a name="before-you-begin"></a>開始之前

若要設定應用程式閘道器的端對端 SSL 憑證不需要的閘道器，且需後端伺服器的憑證。 閘道器憑證用來加密並解密傳送至並使用 SSL 的流量。 閘道器憑證必須位於 [個人資訊交換 (pfx) 格式。 這種檔案格式可以匯出私密金鑰所需執行的加密和解密流量的應用程式閘道器。

為端對端加密後端必須是 whitelisted 應用程式的閘道器。 這是上傳到應用程式閘道器的公用 backends 的憑證。 如此一來，可確保應用程式閘道器只通訊與已知的後端執行個體。 此進一步保護的端對端通訊。

此程序是由下列步驟所述︰

## <a name="create-the-resource-group"></a>建立資源群組

本節會引導您建立資源] 群組中，包含應用程式閘道器。

### <a name="step-1"></a>步驟 1

登入您的 Azure 帳戶。

    Login-AzureRmAccount

### <a name="step-2"></a>步驟 2

選取要用於此案例訂閱]。

    Select-AzureRmsubscription -SubscriptionName "<Subscription name>"

### <a name="step-3"></a>步驟 3

建立資源群組 （略過此步驟如果您使用現有的資源群組）。

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>建立虛擬網路和應用程式閘道器的子網路

下列範例會建立虛擬網路和兩個子網路。 一個子網路用來儲存應用程式閘道器。 其他子網路用於 backends 裝載 web 應用程式。

### <a name="step-1"></a>步驟 1

指派位址範圍的子網路用於應用程式閘道器本身。

    $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

> [AZURE.NOTE] 設定應用程式的閘道器的子網路應該要正確調整大小。 您可以設定應用程式閘道最多 10 個執行個體。 每個執行個體需要 1 子網路的 IP 位址。 太小的子網路可能會影響應用程式閘道出縮放比例。

### <a name="step-2"></a>步驟 2

指定要使用的後端位址集區位址範圍。

    $nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

### <a name="step-3"></a>步驟 3

在上述步驟中定義的子網路建立虛擬網路。

    $vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet

### <a name="step-4"></a>步驟 4

擷取的虛擬網路資源及子網路資源可用於下列步驟︰

    $vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
    $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
    $nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>建立前端設定的公用 IP 位址

建立要用於應用程式閘道器的公用 IP 資源。 使用此公用 IP 位址下列步驟。

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "West US" -AllocationMethod Dynamic

> [AZURE.IMPORTANT] 應用程式閘道不支援使用網域標籤定義，以建立的公用 IP 位址。 只有公用 IP 位址，以動態方式建立的網域標籤都支援。 如果您需要的應用程式閘道器的易記 dns 名稱時，建議使用為別名的 cname 記錄。

## <a name="create-an-application-gateway-configuration-object"></a>建立應用程式閘道器組態物件

建立應用程式閘道器前，您必須設定設定的所有項目。 下列步驟建立所需的應用程式閘道資源設定項目。

### <a name="step-1"></a>步驟 1

建立應用程式閘道器 IP 設定，此設定會設定應用程式閘道器會使用何種子網路。 應用程式閘道啟動時，它會挑選設定子網路中的 IP 位址，並將網路流量路由至後端 IP 集區中的 IP 位址。 請記住，每個執行個體需要一個 IP 位址。

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

### <a name="step-2"></a>步驟 2

建立一個前端 IP 設定，此設定對應至前端應用程式閘道器的私人或公用 ip 位址。 下列步驟將前端 IP 設定與關聯前述步驟中的公用 IP 位址。

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

### <a name="step-3"></a>步驟 3

設定後端 IP 位址集區的後端網頁伺服器的 IP 位址。 這些 IP 位址是接收來自前端 IP 端點網路流量的 IP 位址。 您會取代下列 IP 位址新增您自己的應用程式 IP 位址結束點。

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3

> [AZURE.NOTE] 完整的網域名稱 (FQDN) 是使用-BackendFqdns 參數也是有效的值取代後端伺服器 ip 位址。

### <a name="step-4"></a>步驟 4

設定公用 IP 端點的前端 IP 連接埠。 此連接埠是使用者連線至的連接埠。

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443

### <a name="step-5"></a>步驟 5

設定應用程式閘道器的憑證。 這個憑證用於解密並重新加密上的應用程式閘道器的流量。

    $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>

> [AZURE.NOTE] 此範例中設定 SSL 連線所使用的憑證。 憑證必須以.pfx 格式及密碼必須介於 4 到 12 個字元。

### <a name="step-6"></a>步驟 6

建立應用程式閘道器的 HTTP 接聽。 指定要使用的前端 ip 設定、 連接埠及 ssl 憑證。

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

### <a name="step-7"></a>步驟 7

上傳用於 ssl 憑證啟用後端資料庫資源。

> [AZURE.NOTE] 預設探查取得公開金鑰從**預設**的 SSL 繫結後端的 IP 位址，並比較公開金鑰值接收到您在此處所提供的公開金鑰值。 擷取的公開金鑰可能不一定是預定的流量會**如果**您使用主機標題和 SNI 後端上的網站。 如果有疑問，請造訪 https://127.0.0.1/ 上，確認的憑證用於**預設**SSL 繫結後端。 此區段中，使用公開金鑰從該要求。 如果您使用主機標題和 SNI HTTPS 繫結，不會收到的回應和憑證的手動瀏覽器要求以 https://127.0.0.1/ 後端上，您必須設定預設 SSL 繫結後端上。 如果您不要這麼做，探查會失敗，且後端就無法 whitelisted。

    $authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile C:\users\gwallace\Desktop\cert.cer

> [AZURE.NOTE] 在此步驟中提供的憑證應該出現在後端 pfx 憑證的公開金鑰。 匯出憑證 （不根憑證） 中的後端伺服器上安裝。CER 格式，並將其用於此步驟。 此步驟 whitelists 後端應用程式閘道器。

### <a name="step-8"></a>步驟 8

設定應用程式的閘道器後端 http 設定。 指定 http 設定的上一個步驟中上傳的憑證。

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert

### <a name="step-9"></a>步驟 9

建立負載平衡器路由規則，以設定負載平衡器行為。 在此範例中，建立基本循環規則。

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### <a name="step-10"></a>步驟 10

設定應用程式閘道執行個體大小。  可用的大小會**標準\_小型**，**標準\_中型**，及**標準\_大**。  容量，可用的值是 1 到 10。

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

>[AZURE.NOTE] 供測試之用，可以選擇的執行個體計數為 1。 請務必知道下兩個執行個體的任何執行個體計數不涵蓋 SLA，因此不建議。 小型的閘道器會針對開發測試而非生產用途使用。

### <a name="step-11"></a>步驟 11

設定 SSL 原則，可用於應用程式閘道器]。 應用程式的閘道器支援的功能，若要停用某些 SSL 通訊協定版本。

下列的值是可以停用的通訊協定版本的清單。

- **TLSv1_0**
- **TLSv1_1**
- **TLSv1_2**

下列範例會停用 TLSv1\_0。

    $sslPolicy = New-AzureRmApplicationGatewaySslPolicy -DisabledSslProtocols TLSv1_0

## <a name="create-the-application-gateway"></a>建立應用程式閘道器

使用所有先前的步驟，建立應用程式閘道器。 閘道器建立是長時間執行的程序。

    $appgw = New-AzureRmApplicationGateway -Name appgateway -SslCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslPolicy $sslPolicy -AuthenticationCertificates $authcert -Verbose

## <a name="disable-ssl-protocol-versions-on-an-existing-application-gateway"></a>停用現有的應用程式閘道 SSL 通訊協定版本

上述步驟可讓您透過建立應用程式的端對端 ssl 和停用某些 SSL 通訊協定版本。 下列範例會停用現有的應用程式閘道特定 SSL 原則。

### <a name="step-1"></a>步驟 1

擷取應用程式閘道器至更新。

    $gw = Get-AzureRmApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG

### <a name="step-2"></a>步驟 2

定義 SSL 原則。 在下列範例中，已停用 TLSv1.0 和 TLSv1.1。

    Set-AzureRmApplicationGatewaySslPolicy -DisabledSslProtocols TLSv1_0, TLSv1_1 -ApplicationGateway $gw

### <a name="step-3"></a>步驟 3

最後，更新閘道器。 請務必請注意，最後一個步驟是長時間執行的工作。 完成之後，端對端 ssl 應用程式閘道器設定。

    $gw | Set-AzureRmApplicationGateway

## <a name="get-application-gateway-dns-name"></a>取得應用程式閘道器 DNS 名稱

閘道器建立後下, 一步是設定通訊前端。 使用時的公用 IP，應用程式閘道需要不是易記的動態指派的 DNS 名稱。 若要確保使用者可以按下應用程式閘道器的 CNAME 記錄可以用來指向應用程式閘道器公用端點。 [設定 Azure 中的自訂網域名稱](../cloud-services/cloud-services-custom-domain-name-portal.md)。 若要這麼做，擷取的應用程式閘道和其相關聯的 IP/DNS 名稱，使用 PublicIPAddress 項目附加到應用程式閘道器的詳細資料。 應用程式閘道器的 DNS 名稱應用於建立指向這個 DNS 名稱將兩個 web 應用程式的 CNAME 記錄。 不建議使用 A 記錄，因為 VIP 可能會在重新啟動應用程式的閘道器的變更。
    
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

深入了解瀏覽[Web 應用程式防火牆概觀](application-gateway-webapplicationfirewall-overview.md)強化 web 應用程式以透過應用程式的閘道器的 Web 應用程式防火牆的安全性

[scenario]: ./media/application-gateway-end-to-end-ssl-powershell/scenario.png
