<properties
   pageTitle="使用傳統的部署設定的 SSL 卸載應用程式閘道 |Microsoft Azure"
   description="本文提供使用 SSL 建立應用程式閘道指示卸載使用 Azure 傳統部署模型。"
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

# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-classic-deployment-model"></a>使用傳統的部署模型中設定 SSL 卸載應用程式閘道

> [AZURE.SELECTOR]
-[Azure 入口網站](application-gateway-ssl-portal.md)
-[資源管理員 PowerShell 的 Azure](application-gateway-ssl-arm.md)
-[傳統 PowerShell 的 Azure](application-gateway-ssl.md)

Azure 應用程式閘道器可以終止安全通訊端層 (SSL) 工作階段，若要避免在網頁伺服器陣列的成本 SSL 解密工作的閘道器設定。 SSL 卸載也可簡化的前端伺服器設定及管理 web 應用程式。

## <a name="before-you-begin"></a>開始之前

1. 使用 Web 平台安裝程式，安裝最新版 Azure PowerShell cmdlet。 您可以從下載並安裝最新版本的**Windows PowerShell**一節的[下載頁面](https://azure.microsoft.com/downloads/)。
2. 確認您有使用正確的子網路以使用虛擬網路。 請確認沒有虛擬機器或雲端部署使用子網路。 應用程式閘道器必須是單獨子虛擬網路。
3. 您設定為使用應用程式閘道器的伺服器，必須存在或指派建立虛擬網路或公用 IP/VIP 其結束點。

若要在應用程式閘道設定 SSL 卸載，執行下列步驟，列出的順序︰

1. [建立應用程式閘道器](#create-an-application-gateway)
2. [上傳的 SSL 憑證](#upload-ssl-certificates)
3. [設定閘道器](#configure-the-gateway)
4. [設定閘道器組態設定](#set-the-gateway-configuration)
5. [啟動閘道器](#start-the-gateway)
6. [請確認閘道器狀態](#verify-the-gateway-status)


## <a name="create-an-application-gateway"></a>建立應用程式閘道器

若要建立閘道器，使用 [**新增 AzureApplicationGateway**指令程式，使用您自己取代的值。 無法在此時啟動閘道器的帳單。 順利啟動閘道器時，在接下來的步驟，會開始帳單。

    New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

若要建立閘道器的驗證，您可以使用**取得 AzureApplicationGateway**指令程式。

在範例、*描述*、 *InstanceCount*及*GatewaySize*是選擇性參數。 *InstanceCount*的預設值是 2 的最大值 10。 *GatewaySize*的預設值是 [中]。 小型和大型的其他可用的值。 *VirtualIPs* *DnsName*會顯示為空白因為閘道器尚未啟動。 閘道器的執行狀態後，會建立這些值。

    Get-AzureApplicationGateway AppGwTest

## <a name="upload-ssl-certificates"></a>上傳的 SSL 憑證

使用**新增 AzureApplicationGatewaySslCertificate**上傳至應用程式閘道器的伺服器憑證*pfx*格式。 憑證名稱的使用者選擇名稱，而必須是唯一的應用程式閘道器。 這個憑證被指在所有應用程式閘道器上的憑證管理作業名稱。

這個下列範例會示範指令程式，使用您自己取代範例中的值。

    Add-AzureApplicationGatewaySslCertificate  -Name AppGwTest -CertificateName GWCert -Password <password> -CertificateFile <full path to pfx file>

接下來，驗證憑證上傳]。 使用**取得 AzureApplicationGatewayCertificate**指令程式。

此範例顯示 cmdlet 的第一行，後面接著成果。

    Get-AzureApplicationGatewaySslCertificate AppGwTest

    VERBOSE: 5:07:54 PM - Begin Operation: Get-AzureApplicationGatewaySslCertificate
    VERBOSE: 5:07:55 PM - Completed Operation: Get-AzureApplicationGatewaySslCertificate
    Name           : SslCert
    SubjectName    : CN=gwcert.app.test.contoso.com
    Thumbprint     : AF5ADD77E160A01A6......EE48D1A
    ThumbprintAlgo : sha1RSA
    State..........: Provisioned

>[AZURE.NOTE] 4 到 12 個字元、 字母或數字之間有憑證密碼。 不接受特殊字元。

## <a name="configure-the-gateway"></a>設定閘道器

應用程式的閘道器組態包含多個值。 可以繫結值放在一起以建構設定。

值為︰

- **後端伺服器集區︰**後端伺服器 IP 位址清單。 列出的 IP 位址應該是屬於虛擬網路的子網路或應該公用 IP/VIP。
- **後端伺服器集區設定︰**每個集區有設定，例如連接埠與通訊協定，cookie 為基礎的相關性。 這些設定會連結到資料庫]，就會套用到所有資料庫中的伺服器。
- **前端連接埠︰**此連接埠是開啟應用程式閘道器公用連接埠。 流量碰到此連接埠]，然後會被重新導向至其中一個後端伺服器。
- **接聽︰**接聽具有前端連接埠通訊協定 （Http 或 Https，這些值會區分大小寫），以及的 SSL 憑證名稱 （如果設定 SSL 卸載）。
- **規則︰**此規則繫結接聽和伺服器後端資料庫，並定義流量在時叫接聽會導向至的後端伺服器集區。 目前為支援*基本*的規則。 *基本*規則是循環載入通訊群組。

**其他設定備忘稿**

SSL 憑證設定，請在**HttpListener**的通訊協定應該變更為*Https* （區分大小寫）。 使用此值設為使用中的 SSL 憑證節前面上傳相同的名稱， **SslCert**項目會新增至**HttpListener** 。 前端連接埠應該會更新以 443。

**若要啟用 cookie 為基礎的相關性**︰ 您可以設定應用程式閘道確保從用戶端工作階段要求一律會被導向網頁伺服器陣列中的相同 vm。 插入工作階段 cookie，可讓閘道器至適當地將流量導向是這種情況。 若要啟用 cookie 為基礎的相關性，設定為 [*已啟用* **CookieBasedAffinity** **BackendHttpSettings**元素中。



建立設定物件，或使用設定 XML 檔案，您可以建立您的設定。
若要使用來建構您設定的設定 XML 檔案，使用下列範例︰

**設定 XML 範例**

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
        <FrontendIPConfigurations />
        <FrontendPorts>
            <FrontendPort>
                <Name>FrontendPort1</Name>
                <Port>443</Port>
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
                <Protocol>Https</Protocol>
                <SslCert>GWCert</SslCert>
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

接下來，您可以設定應用程式閘道器。 設定物件或設定 XML 檔案，您可以使用**設定 AzureApplicationGatewayConfig**指令程式。

    Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml

## <a name="start-the-gateway"></a>啟動閘道器

閘道器已設定之後，使用**開始 AzureApplicationGateway**指令程式來啟動閘道器。 閘道器已順利啟動後，也會開始使用應用程式閘道的計費。


**附註︰****開始 AzureApplicationGateway** cmdlet 可能需要 15 20 分鐘才能完成。

    Start-AzureApplicationGateway AppGwTest

## <a name="verify-the-gateway-status"></a>請確認閘道器狀態

您可以使用**取得 AzureApplicationGateway**指令程式來檢查閘道器狀態。 如果**開始 AzureApplicationGateway**成功在先前的步驟，應該執行*狀態*，並*VirtualIPs*和*DnsName*應有有效的項目。

此範例顯示應用程式的閘道器，會執行，，並準備好使用流量。

    Get-AzureApplicationGateway AppGwTest

    Name          : AppGwTest2
    Description   :
    VnetName      : testvnet1
    Subnets       : {Subnet-1}
    InstanceCount : 2
    GatewaySize   : Medium
    State         : Running
    VirtualIPs    : {23.96.22.241}
    DnsName       : appgw-4c960426-d1e6-4aae-8670-81fd7a519a43.cloudapp.net


## <a name="next-steps"></a>後續步驟


若要深入瞭解一般負載平衡的選項，請參閱︰

- [Azure 負載平衡器](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure 流量管理員](https://azure.microsoft.com/documentation/services/traffic-manager/)