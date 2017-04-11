<properties
   pageTitle="開始建立，或刪除應用程式閘道 |Microsoft Azure"
   description="本頁面提供指示建立、 設定、 啟動，並刪除 Azure 應用程式閘道器"
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

# <a name="create-start-or-delete-an-application-gateway"></a>開始建立，或刪除應用程式閘道器

> [AZURE.SELECTOR]
- [Azure 入口網站](application-gateway-create-gateway-portal.md)
- [Azure 資源管理員 PowerShell](application-gateway-create-gateway-arm.md)
- [Azure 傳統 PowerShell](application-gateway-create-gateway.md)
- [Azure 資源管理員範本](application-gateway-create-gateway-arm-template.md)
- [Azure CLI](application-gateway-create-gateway-cli.md)

Azure 應用程式閘道是有 7 層負載平衡器。 是否位於雲端或內部部署，它會提供容錯移轉時，效能路由 HTTP 要求之間不同的伺服器。 應用程式的閘道器提供許多的應用程式傳送控制器 (ADC) 的功能，包括 HTTP 負載平衡、 cookie 為基礎的工作階段相關性、 安全通訊端層 (SSL) 卸載自訂健康探查、 支援多重網站和許多其他。 若要尋找的受支援功能的完整清單，請造訪[應用程式閘道器概觀](application-gateway-introduction.md)

本文會引導您建立、 設定、 啟動以及刪除應用程式閘道器的步驟。

## <a name="before-you-begin"></a>開始之前

1. 使用 Web 平台安裝程式，安裝最新版 Azure PowerShell cmdlet。 您可以從下載並安裝最新版本的**Windows PowerShell**一節的[下載頁面](https://azure.microsoft.com/downloads/)。
2. 如果您有現有的虛擬網路，請選取現有的空白子網路，或在您現有的虛擬網路僅用於建立新子網路的應用程式閘道器。 您無法部署應用程式閘道器至您要部署應用程式閘道後面，除非 vnet 外面使用的資源不同虛擬網路。 若要瞭解更多造訪[Vnet 外面](../virtual-network/virtual-network-peering-overview.md)
3. 確認您有使用正確的子網路以使用虛擬網路。 請確認沒有虛擬機器或雲端部署使用子網路。 應用程式閘道器必須是單獨子虛擬網路。
3. 您設定為使用應用程式閘道器的伺服器，必須存在或指派建立虛擬網路或公用 IP/VIP 其結束點。

## <a name="what-is-required-to-create-an-application-gateway"></a>若要建立的應用程式閘道器的必要條件為何？

當您可以使用 [**新增 AzureApplicationGateway** ] 命令來建立應用程式閘道器時，此時設定任何設定和新建立的資源設定以使用 XML 或設定物件。

值為︰

- **後端伺服器集區︰**後端伺服器 IP 位址清單。 列出的 IP 位址應該是屬於虛擬網路的子網路或應該公用 IP/VIP。
- **後端伺服器資料庫設定︰**每個資料庫有設定，例如連接埠與通訊協定，cookie 為基礎的相關性。 這些設定會連結到資料庫]，就會套用到所有資料庫中的伺服器。
- **前端連接埠︰**此連接埠是開啟應用程式閘道器公用連接埠。 流量碰到此連接埠]，然後會被重新導向至其中一個後端伺服器。
- **接聽︰**接聽具有前端連接埠通訊協定 （Http 或 Https，這些值會區分大小寫），以及的 SSL 憑證名稱 （如果設定 SSL 卸載）。
- **規則︰**此規則繫結接聽和伺服器後端資料庫，並定義流量在時叫接聽會導向至的後端伺服器集區。

## <a name="create-an-application-gateway"></a>建立應用程式閘道器

若要建立的應用程式閘道器︰

1. 建立應用程式閘道器資源。
2. 建立設定 XML 檔案或設定物件。
3. 確認新建立的應用程式的閘道器資源設定。

>[AZURE.NOTE] 如果您需要設定自訂檢查您的應用程式閘道器，請參閱[建立自訂探查使用 PowerShell 應用程式閘道](application-gateway-create-probe-classic-ps.md)。 查看[自訂探查和健康監視](application-gateway-probe-overview.md)的詳細資訊。

![案例範例][scenario]

### <a name="create-an-application-gateway-resource"></a>建立應用程式閘道器資源

若要建立閘道器，使用 [**新增 AzureApplicationGateway**指令程式，使用您自己取代的值。 無法在此時啟動閘道器的帳單。 順利啟動閘道器時，在接下來的步驟，會開始帳單。

下列範例會建立應用程式閘道使用虛擬網路，稱為 「 testvnet1 」 及子網路，稱為 「 子網路 1 」。


    New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

    VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway
    VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399


 *描述*、 *InstanceCount*及*GatewaySize*是選擇性參數。

若要建立閘道器的驗證，您可以使用**取得 AzureApplicationGateway**指令程式。

    Get-AzureApplicationGateway AppGwTest
    Name          : AppGwTest
    Description   :
    VnetName      : testvnet1
    Subnets       : {Subnet-1}
    InstanceCount : 2
    GatewaySize   : Medium
    State         : Stopped
    VirtualIPs    : {}
    DnsName       :

>[AZURE.NOTE]  *InstanceCount*的預設值是 2 的最大值 10。 *GatewaySize*的預設值是 [中]。 您可以選擇小型、 中型和大型之間。

*VirtualIPs* *DnsName*會顯示為空白因為閘道器尚未啟動。 閘道器的執行狀態後，會建立這些項目。

## <a name="configure-the-application-gateway"></a>設定應用程式的閘道

您可以使用 XML 或設定物件設定應用程式閘道器。

## <a name="configure-the-application-gateway-by-using-xml"></a>設定應用程式的閘道使用 XML

在下列範例中，您可以使用 XML 檔案設定所有應用程式的閘道器設定，並確認其應用程式的閘道器資源。  

### <a name="step-1"></a>步驟 1  

複製下列文字至記事本。

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
        <FrontendPorts>
            <FrontendPort>
                <Name>(name-of-your-frontend-port)</Name>
                <Port>(port number)</Port>
            </FrontendPort>
        </FrontendPorts>
        <BackendAddressPools>
            <BackendAddressPool>
                <Name>(name-of-your-backend-pool)</Name>
                <IPAddresses>
                    <IPAddress>(your-IP-address-for-backend-pool)</IPAddress>
                    <IPAddress>(your-second-IP-address-for-backend-pool)</IPAddress>
                </IPAddresses>
            </BackendAddressPool>
        </BackendAddressPools>
        <BackendHttpSettingsList>
            <BackendHttpSettings>
                <Name>(backend-setting-name-to-configure-rule)</Name>
                <Port>80</Port>
                <Protocol>[Http|Https]</Protocol>
                <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            </BackendHttpSettings>
        </BackendHttpSettingsList>
        <HttpListeners>
            <HttpListener>
                <Name>(name-of-the-listener)</Name>
                <FrontendPort>(name-of-your-frontend-port)</FrontendPort>
                <Protocol>[Http|Https]</Protocol>
            </HttpListener>
        </HttpListeners>
        <HttpLoadBalancingRules>
            <HttpLoadBalancingRule>
                <Name>(name-of-load-balancing-rule)</Name>
                <Type>basic</Type>
                <BackendHttpSettings>(backend-setting-name-to-configure-rule)</BackendHttpSettings>
                <Listener>(name-of-the-listener)</Listener>
                <BackendAddressPool>(name-of-your-backend-pool)</BackendAddressPool>
            </HttpLoadBalancingRule>
        </HttpLoadBalancingRules>
    </ApplicationGatewayConfiguration>

編輯設定項目括號之間的值。 將檔案儲存與副檔名.xml。

>[AZURE.IMPORTANT] Http 或 Https 通訊協定項目會區分大小寫。

下列範例會示範如何使用設定檔設定應用程式閘道器。 範例負載平衡公用連接埠 80 HTTP 流量，並傳送到兩個 IP 位址之間的後端連接埠 80 的網路流量。

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
        <FrontendPorts>
            <FrontendPort>
                <Name>FrontendPort1</Name>
                <Port>80</Port>
            </FrontendPort>
        </FrontendPorts>
        <BackendAddressPools>
            <BackendAddressPool>
                <Name>BackendPool1</Name>
                <IPAddresses>
                    <IPAddress>10.0.0.1</IPAddress>
                    <IPAddress>10.0.0.2</IPAddress>
                </IPAddresses>
            </BackendAddressPool>
        </BackendAddressPools>
        <BackendHttpSettingsList>
            <BackendHttpSettings>
                <Name>BackendSetting1</Name>
                <Port>80</Port>
                <Protocol>Http</Protocol>
                <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            </BackendHttpSettings>
        </BackendHttpSettingsList>
        <HttpListeners>
            <HttpListener>
                <Name>HTTPListener1</Name>
                <FrontendPort>FrontendPort1</FrontendPort>
                <Protocol>Http</Protocol>
            </HttpListener>
        </HttpListeners>
        <HttpLoadBalancingRules>
            <HttpLoadBalancingRule>
                <Name>HttpLBRule1</Name>
                <Type>basic</Type>
                <BackendHttpSettings>BackendSetting1</BackendHttpSettings>
                <Listener>HTTPListener1</Listener>
                <BackendAddressPool>BackendPool1</BackendAddressPool>
            </HttpLoadBalancingRule>
        </HttpLoadBalancingRules>
    </ApplicationGatewayConfiguration>


### <a name="step-2"></a>步驟 2

接下來，設定應用程式閘道器。 使用**設定 AzureApplicationGatewayConfig** cmdlet 設定 XML 檔案。


    Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile "D:\config.xml"

    VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig
    VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d

## <a name="configure-the-application-gateway-by-using-a-configuration-object"></a>使用 [設定物件設定應用程式閘道器

下列範例會示範如何使用 [設定物件設定應用程式閘道器。 設定的所有項目必須個別設定，然後將其新增至應用程式閘道器組態物件。 建立設定物件後，您可以使用 [**設定 AzureApplicationGateway**命令認可先前建立的應用程式的閘道器資源設定。

>[AZURE.NOTE] 之前設定的每個物件指派值，必須先宣告哪一類的物件 PowerShell 用來儲存空間。 若要建立的個別項目第一行定義哪些 Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model （物件的名稱） 使用。

### <a name="step-1"></a>步驟 1

建立個別的設定的所有項目。

建立前端 IP，如下列範例所示。

    $fip = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration
    $fip.Name = "fip1"
    $fip.Type = "Private"
    $fip.StaticIPAddress = "10.0.0.5"

下列範例所示，請建立前端連接埠。

    $fep = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort
    $fep.Name = "fep1"
    $fep.Port = 80

建立後端伺服器資料庫。

 定義的 IP 位址新增到下一個範例所示的後端伺服器集區。


    $servers = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendServerCollection
    $servers.Add("10.0.0.1")
    $servers.Add("10.0.0.2")

 使用 $server 物件新增至後端資料庫物件 ($pool) 的值。

    $pool = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool
    $pool.BackendServers = $servers
    $pool.Name = "pool1"

建立後端伺服器集區設定。

    $setting = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings
    $setting.Name = "setting1"
    $setting.CookieBasedAffinity = "enabled"
    $setting.Port = 80
    $setting.Protocol = "http"

建立接聽。

    $listener = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener
    $listener.Name = "listener1"
    $listener.FrontendPort = "fep1"
    $listener.FrontendIP = "fip1"
    $listener.Protocol = "http"
    $listener.SslCert = ""

建立的規則。

    $rule = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule
    $rule.Name = "rule1"
    $rule.Type = "basic"
    $rule.BackendHttpSettings = "setting1"
    $rule.Listener = "listener1"
    $rule.BackendAddressPool = "pool1"

### <a name="step-2"></a>步驟 2

指派的應用程式閘道器設定物件 ($appgwconfig) 個別設定的所有項目。

新增前端 IP 設定。

    $appgwconfig = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.ApplicationGatewayConfiguration
    $appgwconfig.FrontendIPConfigurations = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration]"
    $appgwconfig.FrontendIPConfigurations.Add($fip)

新增前端連接埠設定。

    $appgwconfig.FrontendPorts = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort]"
    $appgwconfig.FrontendPorts.Add($fep)

新增後端伺服器資料庫設定。

    $appgwconfig.BackendAddressPools = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool]"
    $appgwconfig.BackendAddressPools.Add($pool)

新增後端資料庫設定的設定。

    $appgwconfig.BackendHttpSettingsList = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings]"
    $appgwconfig.BackendHttpSettingsList.Add($setting)

新增接聽項設定。

    $appgwconfig.HttpListeners = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener]"
    $appgwconfig.HttpListeners.Add($listener)

新增至設定的規則。

    $appgwconfig.HttpLoadBalancingRules = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule]"
    $appgwconfig.HttpLoadBalancingRules.Add($rule)

### <a name="step-3"></a>步驟 3

使用**設定 AzureApplicationGatewayConfig**認可應用程式閘道器資源設定物件。

    Set-AzureApplicationGatewayConfig -Name AppGwTest -Config $appgwconfig

## <a name="start-the-gateway"></a>啟動閘道器

閘道器已設定之後，使用**開始 AzureApplicationGateway**指令程式來啟動閘道器。 閘道器已順利啟動後，也會開始使用應用程式閘道的計費。

> [AZURE.NOTE] **開始 AzureApplicationGateway** cmdlet 可能需要 15 20 分鐘才能完成。

    Start-AzureApplicationGateway AppGwTest

    VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway
    VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## <a name="verify-the-gateway-status"></a>請確認閘道器狀態

您可以使用**取得 AzureApplicationGateway**指令程式來檢查閘道器狀態。 如果**開始 AzureApplicationGateway**成功在先前的步驟，應該執行的*狀態*]，然後*Vip*和*DnsName*應有有效的項目。

下列範例會顯示最多，請執行，應用程式閘道器，並準備好採取流量傳送至`http://<generated-dns-name>.cloudapp.net`。

    Get-AzureApplicationGateway AppGwTest

    VERBOSE: 8:09:28 PM - Begin Operation: Get-AzureApplicationGateway
    VERBOSE: 8:09:30 PM - Completed Operation: Get-AzureApplicationGateway
    Name          : AppGwTest
    Description   :
    VnetName      : testvnet1
    Subnets       : {Subnet-1}
    InstanceCount : 2
    GatewaySize   : Medium
    State         : Running
    Vip           : 138.91.170.26
    DnsName       : appgw-1b8402e8-3e0d-428d-b661-289c16c82101.cloudapp.net


## <a name="delete-an-application-gateway"></a>刪除應用程式閘道器

若要刪除的應用程式閘道器︰

1. 若要停止閘道器使用**停駐點 AzureApplicationGateway**指令程式。
2. 您可以使用 [**移除 AzureApplicationGateway** cmdlet 來移除閘道器。
3. 確認 [閘道器，已使用**取得 AzureApplicationGateway**指令程式來移除]。

下列範例會顯示第一行，後面接著輸出**停駐點 AzureApplicationGateway**指令程式。

    Stop-AzureApplicationGateway AppGwTest

    VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
    VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

停止狀態中的應用程式閘道器後，使用**移除 AzureApplicationGateway** cmdlet 來移除這項服務。


    Remove-AzureApplicationGateway AppGwTest

    VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
    VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

若要驗證服務已經移除，您可以使用**取得 AzureApplicationGateway**指令程式。 不需要此步驟。


    Get-AzureApplicationGateway AppGwTest

    VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

    Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
    .....

## <a name="next-steps"></a>後續步驟

如果您想要設定 SSL 卸載，請參閱[設定應用程式的閘道 SSL 卸載](application-gateway-ssl.md)。

如果您想要設定為用於內部負載平衡器應用程式閘道器，請參閱[建立內部負載平衡器 (ILB) 應用程式閘道](application-gateway-ilb.md)。

若要深入瞭解一般負載平衡的選項，請參閱︰

- [Azure 負載平衡器](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure 流量管理員](https://azure.microsoft.com/documentation/services/traffic-manager/)

[scenario]: ./media/application-gateway-create-gateway/scenario.png