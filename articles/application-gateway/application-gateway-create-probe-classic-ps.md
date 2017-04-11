<properties
   pageTitle="建立自訂的檢查應用程式的閘道器在傳統的部署模型中使用 PowerShell |Microsoft Azure"
   description="瞭解如何建立自訂的檢查應用程式的閘道器，在傳統的部署模型中使用 PowerShell"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace" />

# <a name="create-a-custom-probe-for-azure-application-gateway-classic-by-using-powershell"></a>建立自訂的檢查 Azure 應用程式的閘道器 （傳統） 使用 PowerShell

> [AZURE.SELECTOR]
- [Azure 入口網站](application-gateway-create-probe-portal.md)
- [Azure 資源管理員 PowerShell](application-gateway-create-probe-ps.md)
- [Azure 傳統 PowerShell](application-gateway-create-probe-classic-ps.md)

[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]瞭解如何[執行這些步驟後，使用資源管理員模型](application-gateway-create-probe-ps.md)。

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway"></a>建立應用程式閘道器

若要建立的應用程式閘道器︰

1. 建立應用程式閘道器資源。
2. 建立設定 XML 檔案或設定物件。
3. 確認新建立的應用程式的閘道器資源設定。

### <a name="create-an-application-gateway-resource"></a>建立應用程式閘道器資源

若要建立閘道器，使用 [**新增 AzureApplicationGateway**指令程式，使用您自己取代的值。 無法在此時啟動閘道器的帳單。 順利啟動閘道器時，在接下來的步驟，會開始帳單。

下列範例會建立應用程式閘道使用虛擬網路，稱為 「 testvnet1 」 及子網路，稱為 「 子網路 1 」。

    New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

若要建立閘道器的驗證，您可以使用**取得 AzureApplicationGateway**指令程式。

    Get-AzureApplicationGateway AppGwTest

>[AZURE.NOTE]  *InstanceCount*的預設值是 2 的最大值 10。 *GatewaySize*的預設值是 [中]。 您可以選擇小型、 中型企業版和大。

 *VirtualIPs* *DnsName*會顯示為空白因為閘道器尚未啟動。 閘道器的執行狀態後，會建立這些項目。

## <a name="configure-an-application-gateway"></a>設定應用程式閘道器

您可以使用 XML 或設定物件設定應用程式閘道器。

## <a name="configure-an-application-gateway-by-using-xml"></a>設定應用程式閘道使用 XML

在下列範例中，您可以使用 XML 檔案設定所有應用程式的閘道器設定，並確認其應用程式的閘道器資源。  

### <a name="step-1"></a>步驟 1

複製下列文字至記事本。

    <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendIPConfigurations>
        <FrontendIPConfiguration>
            <Name>fip1</Name>
            <Type>Private</Type>
        </FrontendIPConfiguration>
    </FrontendIPConfigurations>    
    <FrontendPorts>
        <FrontendPort>
            <Name>port1</Name>
            <Port>80</Port>
        </FrontendPort>
    </FrontendPorts>
    <Probes>
        <Probe>
            <Name>Probe01</Name>
            <Protocol>Http</Protocol>
            <Host>contoso.com</Host>
            <Path>/path/custompath.htm</Path>
            <Interval>15</Interval>
            <Timeout>15</Timeout>
            <UnhealthyThreshold>5</UnhealthyThreshold>
        </Probe>
      </Probes>
     <BackendAddressPools>
        <BackendAddressPool>
            <Name>pool1</Name>
            <IPAddresses>
                <IPAddress>1.1.1.1</IPAddress>
                <IPAddress>2.2.2.2</IPAddress>
            </IPAddresses>
        </BackendAddressPool>
    </BackendAddressPools>
    <BackendHttpSettingsList>
        <BackendHttpSettings>
            <Name>setting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            <RequestTimeout>120</RequestTimeout>
            <Probe>Probe01</Probe>
        </BackendHttpSettings>
    </BackendHttpSettingsList>
    <HttpListeners>
        <HttpListener>
            <Name>listener1</Name>
            <FrontendIP>fip1</FrontendIP>
        <FrontendPort>port1</FrontendPort>
            <Protocol>Http</Protocol>
        </HttpListener>
    </HttpListeners>
    <HttpLoadBalancingRules>
        <HttpLoadBalancingRule>
            <Name>lbrule1</Name>
            <Type>basic</Type>
            <BackendHttpSettings>setting1</BackendHttpSettings>
            <Listener>listener1</Listener>
            <BackendAddressPool>pool1</BackendAddressPool>
        </HttpLoadBalancingRule>
    </HttpLoadBalancingRules>
    </ApplicationGatewayConfiguration>


編輯設定項目括號之間的值。 將檔案儲存與副檔名.xml。

下列範例會示範如何使用設定檔設定應用程式閘道器，以負載平衡公用連接埠 80 HTTP 流量，並使用自訂的檢查兩個 IP 位址之間的後端連接埠 80 傳送網路流量。

>[AZURE.IMPORTANT] Http 或 Https 通訊協定項目會區分大小寫。

新的設定項目\<探查\>會新增至設定自訂探查。

設定參數是︰

- **名稱**-自訂探查參考名稱。
- **通訊協定**使用通訊協定 （可能值是 HTTP 或 HTTPS）。
- **Host （主機）**和**路徑**叫用的應用程式閘道器，以判定執行個體的健康情況的完整的 URL 路徑。 例如，如果您有網站 http://contoso.com/，然後自訂探查可以設定的 「 http://contoso.com/path/custompath.htm 」 探查檢查有順利 HTTP 回應。
- **間隔**-設定探查間隔檢查秒數。
- **逾時**-定義 HTTP 回應核取的檢查逾時。
- **UnhealthyThreshold**失敗加上標幟為的後端執行個體所需的 HTTP 回應的數目*不佳*。

檢查名稱中參照<BackendHttpSettings>指派的後端資料庫設定就會使用自訂探查設定。

## <a name="add-a-custom-probe-configuration-to-an-existing-application-gateway"></a>將自訂探查設定新增至現有的應用程式閘道器

變更目前的應用程式閘道設定需要三個步驟︰ 取得目前的 XML 設定檔、 修改擁有自訂的檢查，以及設定新的 XML 設定應用程式閘道器。

### <a name="step-1"></a>步驟 1

使用取得 AzureApplicationGatewayConfig 取得 XML 檔案。 這會匯出 XML 修改加入探查設定的設定。

    Get-AzureApplicationGatewayConfig -Name "<application gateway name>" -Exporttofile "<path to file>"


### <a name="step-2"></a>步驟 2

開啟文字編輯器中的 XML 檔案。 新增`<probe>`區段後`<frontendport>`。

    <Probes>
        <Probe>
            <Name>Probe01</Name>
            <Protocol>Http</Protocol>
            <Host>contoso.com</Host>
            <Path>/path/custompath.htm</Path>
            <Interval>15</Interval>
            <Timeout>15</Timeout>
            <UnhealthyThreshold>5</UnhealthyThreshold>
        </Probe>
    </Probes>

在 [XML 的 [backendHttpSettings] 區段中，新增探查名稱，如下列範例所示︰

        <BackendHttpSettings>
            <Name>setting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            <RequestTimeout>120</RequestTimeout>
            <Probe>Probe01</Probe>
        </BackendHttpSettings>

儲存 XML 檔案。

### <a name="step-3"></a>步驟 3

使用**設定 AzureApplicationGatewayConfig**使用新的 XML 檔案更新此應用程式的閘道器設定。 為新的設定，這就會更新您的應用程式閘道器。

    Set-AzureApplicationGatewayConfig -Name "<application gateway name>" -Configfile "<path to file>"


## <a name="next-steps"></a>後續步驟

如果您想要設定安全通訊端層 (SSL) 卸載，請參閱[設定應用程式的閘道 SSL 卸載](application-gateway-ssl.md)。

如果您想要設定為用於內部負載平衡器應用程式閘道器，請參閱[建立內部負載平衡器 (ILB) 應用程式閘道](application-gateway-ilb.md)。
