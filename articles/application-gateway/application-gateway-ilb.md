<properties 
   pageTitle="建立及設定內部負載平衡器 (ILB) 虛擬網路中使用應用程式閘道 |Microsoft Azure"
   description="本頁面提供設定內部負載平衡端點 Azure 應用程式閘道器的指示"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="jdial"
   editor="tysonn"/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="08/19/2016"
   ms.author="gwallace"/>

# <a name="create-an-application-gateway-with-an-internal-load-balancer-ilb"></a>使用內部負載平衡器 (ILB) 建立應用程式閘道器

> [AZURE.SELECTOR]
- [Azure 傳統的步驟](application-gateway-ilb.md)
- [資源管理員 Powershell 步驟](application-gateway-ilb-arm.md)

您可以設定應用程式的閘道器，面對虛擬 IP 網際網路或內部區終端不公開至網際網路，也就是內部負載平衡器 (ILB) 結束點。 閘道器設定與 ILB 適合用於網際網路上公開內部線條的商務應用程式。 也很適合服務/層位於安全性界限不公開至網際網路，但仍需要循環載入通訊、 工作階段神奇結果或 SSL 終止多層應用程式內。 本文會引導您設定 ILB 應用程式閘道器的步驟。

## <a name="before-you-begin"></a>開始之前

1. 安裝最新版 Azure PowerShell cmdlet 使用 Web 平台安裝程式。 您可以從下載並安裝最新版本的**Windows PowerShell**一節的[下載頁面](https://azure.microsoft.com/downloads/)。
2. 確認您有使用正確的子網路以使用虛擬網路。
3. 確認您在虛擬網路中，或公用 IP/VIP 指派有後端伺服器。


若要建立的應用程式閘道器，請執行下列步驟列出的順序。 

1. [建立應用程式閘道器](#create-a-new-application-gateway)
2. [設定閘道器](#configure-the-gateway)
3. [設定閘道器組態設定](#set-the-gateway-configuration)
4. [啟動閘道器](#start-the-gateway)
4. [確認閘道器](#verify-the-gateway-status)



## <a name="create-an-application-gateway"></a>建立應用程式閘道器︰

**若要建立閘道器**，使用`New-AzureApplicationGateway`指令程式，使用您自己取代的值。 請注意閘道器的計費不會在此時啟動。 順利啟動閘道器時，在接下來的步驟，會開始帳單。

    PS C:\> New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

    VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway 
    VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error 
    ----       ----------------     ------------                             ----
    Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399

**確認**閘道器所建立，您可以使用`Get-AzureApplicationGateway`指令程式。 

在範例、*描述*、 *InstanceCount*及*GatewaySize*是選擇性參數。 *InstanceCount*的預設值是 2 的最大值 10。 *GatewaySize*的預設值是 [中]。 小型和大型的其他可用的值。 *Vip* *DnsName*會顯示為空白因為閘道器尚未啟動。 閘道器的執行狀態後，會建立這些項目。 

    PS C:\> Get-AzureApplicationGateway AppGwTest

    VERBOSE: 4:39:39 PM - Begin Operation:
    Get-AzureApplicationGateway VERBOSE: 4:39:40 PM - Completed 
    Operation: Get-AzureApplicationGateway
    Name: AppGwTest 
    Description: 
    VnetName: testvnet1 
    Subnets: {Subnet-1} 
    InstanceCount: 2 
    GatewaySize: Medium 
    State: Stopped 
    VirtualIPs: 
    DnsName:


## <a name="configure-the-gateway"></a>設定閘道器

應用程式的閘道器組態包含多個值。 可以繫結值放在一起以建構設定。
 
值為︰

- **後端伺服器集區︰**後端伺服器 IP 位址清單。 列出的 IP 位址應該是屬於 VNet 子網路或應該公用 IP/VIP。 
- **後端伺服器資料庫設定︰**每個資料庫有設定，例如連接埠與通訊協定，cookie 為基礎的相關性。 這些設定會連結到資料庫]，就會套用到所有資料庫中的伺服器。
- **Frontend 連接埠︰**此連接埠是開啟的應用程式閘道器的公用連接埠。 流量點擊此連接埠]，然後會被重新導向至其中一個後端伺服器。
- **接聽︰**接聽具有 frontend 連接埠、 通訊協定 （Http 或 Https，這些是區分大小寫），以及的 SSL 憑證名稱 （如果設定 SSL 卸載）。 
- **規則︰**此規則繫結接聽和後端伺服器集區，並定義流量在時叫接聽會導向至的後端伺服器集區。 目前為支援*基本*的規則。 *基本*規則是循環載入通訊群組。

建立設定物件，或設定 XML 檔案，您可以建立您的設定。 若要使用來建構您設定的設定 XML 檔案，使用下列範例。



請注意下列事項︰


- *FrontendIPConfigurations*項目描述與相關應用程式的閘道器設定與 ILB ILB 詳細資料。 

- Frontend IP*類型*應該會設定為 「 私人 」

- *StaticIPAddress*應該設為所要的閘道器接收流量的內部 IP。 請注意，是選擇性的*StaticIPAddress*項目。 如果沒有設定中，選擇使用內部部署子網路 IP。 

- *FrontendIPConfiguration*中指定*名稱*元素的值應該用於 HTTPListener *FrontendIP*項目，以參照 FrontendIPConfiguration。

 **設定 XML 範例**

 

        <?xml version="1.0" encoding="utf-8"?>
        <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
            <FrontendIPConfigurations>
                <FrontendIPConfiguration>
                    <Name>fip1</Name> 
                    <Type>Private</Type> 
                    <StaticIPAddress>10.0.0.10</StaticIPAddress> 
                </FrontendIPConfiguration>
            </FrontendIPConfigurations>
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
                    <FrontendIP>fip1</FrontendIP>
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
    


## <a name="set-the-gateway-configuration"></a>設定閘道器組態設定

接下來，您可以設定應用程式閘道器。 您可以使用`Set-AzureApplicationGatewayConfig`cmdlet 設定物件]，或設定 XML 檔案。 

    PS C:\> Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml

    VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig 
    VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
    Name       HTTP Status Code     Operation ID                             Error 
    ----       ----------------     ------------                             ----
    Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d

## <a name="start-the-gateway"></a>啟動閘道器

閘道器已設定之後，請使用`Start-AzureApplicationGateway`cmdlet 來啟動閘道器。 閘道器已順利啟動後，也會開始使用應用程式閘道的計費。 


> [AZURE.NOTE] `Start-AzureApplicationGateway` Cmdlet 可能需要 15 20 分鐘才能完成。 
   
    PS C:\> Start-AzureApplicationGateway AppGwTest 

    VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway 
    VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error 
    ----       ----------------     ------------                             ----
    Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## <a name="verify-the-gateway-status"></a>請確認閘道器狀態

使用`Get-AzureApplicationGateway`cmdlet 來檢查閘道器狀態。 如果*開始 AzureApplicationGateway*成功在先前的步驟，狀態應該*執行*，而 Vip 和 DnsName 應有有效的項目。 此範例顯示 cmdlet 的第一行，後面接著成果。 在此範例中，閘道正在執行，並已準備好使用流量。 

> [AZURE.NOTE] 應用程式閘道器設定為接受 10.0.0.10 在此範例中的設定 ILB 端點流量。

    PS C:\> Get-AzureApplicationGateway AppGwTest 

    VERBOSE: 8:09:28 PM - Begin Operation: Get-AzureApplicationGateway 
    VERBOSE: 8:09:30 PM - Completed Operation: Get-AzureApplicationGateway
    Name          : AppGwTest
    Description   : 
    VnetName      : testvnet1
    Subnets       : {Subnet-1}
    InstanceCount : 2
    GatewaySize   : Medium
    State         : Running
    VirtualIPs    : {10.0.0.10}
    DnsName       : appgw-b2a11563-2b3a-4172-a4aa-226ee4c23eed.cloudapp.net

## <a name="next-steps"></a>後續步驟


若要深入瞭解一般負載平衡的選項，請參閱︰

- [Azure 負載平衡器](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure 流量管理員](https://azure.microsoft.com/documentation/services/traffic-manager/)
