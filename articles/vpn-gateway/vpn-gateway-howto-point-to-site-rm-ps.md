<properties 
   pageTitle="設定點為網站 VPN 閘道器連線到虛擬網路使用的資源管理員部署模型 |Microsoft Azure"
   description="安全地連結至 Azure 虛擬網路藉由建立點為網站 VPN 閘道器連線。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/17/2016"
   ms.author="cherylmc" />

# <a name="configure-a-point-to-site-connection-to-a-vnet-using-powershell"></a>使用 PowerShell VNet 以點為網站連線的設定

> [AZURE.SELECTOR]
- [資源管理員-Azure 入口網站](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
- [資源管理員-PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
- [傳統-Azure 入口網站](vpn-gateway-howto-point-to-site-classic-azure-portal.md)

點網站 (P2S) 設定，可讓您建立虛擬網路個別的用戶端電腦從安全的連線。 當您要連線到您 VNet 從遠端位置，例如從家用版或會議，或您只需要幾個需要連線到虛擬網路的用戶端，P2S 連線相當實用。 

點-網站的連線不需要 VPN 裝置或-在公開的 IP 位址搭配使用。 從用戶端電腦開始連線建立 VPN 連線時。 如需有關點為網站連線的詳細資訊，請參閱[VPN 閘道器常見問題集](vpn-gateway-vpn-faq.md#point-to-site-connections)和[規劃及設計](vpn-gateway-plan-design.md)。 

本文會引導您以點為網站連線在資源管理員部署模型中使用 PowerShell 建立 VNet。

### <a name="deployment-models-and-methods-for-p2s-connections"></a>部署模型和 P2S 連線的方法

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

下表顯示的兩種部署模型及部署 P2S 設定的方法。 當有包含設定步驟的文章時，我們連結直接從這個資料表。

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)] 

## <a name="basic-workflow"></a>基本的工作流程 

![點-至-網站-圖表](./media/vpn-gateway-howto-point-to-site-rm-ps/p2srm.png "點-網站")

在此案例中，您將建立虛擬網路，以點為網站連線。 指示也會協助您產生所需的此設定的憑證。 P2S 連線由下列項目所組成︰ VNet 閘道、 與根憑證.cer 檔案 （公開金鑰），用戶端憑證，VPN 設定用戶端上的。 

我們使用此設定為下列的值。 我們設定文件的區段[1](#declare) ] 中的變數。 您可以為逐步解說，使用的步驟，並使用的值，而不變更，或變更以反映您的環境。 

### <a name="example"></a>範例值

- **名稱︰ VNet1**
- **位址空間︰ 192.168.0.0/16**和**10.254.0.0/16**<br>例如，我們會使用一個以上的地址空間以描繪出此設定可搭配多個地址空格。 不過，多個地址空格並不需要此設定。
- **子網路名稱︰ 主選單**
    - **子網路位址範圍︰ 192.168.1.0/24**
- **子網路名稱︰ 後端**
    - **子網路位址範圍︰ 10.254.1.0/24**
- **子網路名稱︰ GatewaySubnet**<br>子網路名稱*GatewaySubnet*是強制 VPN 閘道器搭配使用。
    - **子網路位址範圍︰ 192.168.200.0/24** 
- **VPN 用戶端位址集區︰ 172.16.201.0/24**<br>VPN 用戶端連線到使用此點為網站連線 VNet 收到 VPN 用戶端位址集區中的 IP 位址。
- **訂閱︰**如果您有多個訂閱，請確認您使用的正確的項目。
- **資源群組︰ TestRG**
- **位置︰ 東亞美國**
- **的 DNS 伺服器︰ IP 位址**您想要使用的名稱解析 DNS 伺服器。
- **GW 名稱︰ Vnet1GW**
- **公用 IP 名稱︰ VNet1GWPIP**
- **VpnType: RouteBased**

## <a name="before-beginning"></a>在開始之前

- 請確認您擁有 Azure 訂閱。 如果您還沒有 Azure 的訂閱，您可以啟動您的[MSDN 訂閱者優惠](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊設定[免費的帳戶](https://azure.microsoft.com/pricing/free-trial/)。
    
- 安裝最新版 Azure 資源管理員 PowerShell cmdlet。 如需安裝的 PowerShell 指令程式的相關資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md) 。 當使用 PowerShell 此設定，請確定您以系統管理員身分執行。 

## <a name="declare"></a>第 1 部份-記錄中的，設定變數

在此區段中，您可以登入並宣告用於此設定的值。 在 [指令碼範例使用宣告的值。 變更要反映您自己的環境的值。 或者，您可以使用宣告的值，並進行練習的步驟。

1. 在 PowerShell 主控台中，登入您的 Azure 帳戶。 這個指令程式會提示您登入認證，Azure 帳戶。 登入之後下載您的帳戶設定，使其使用 PowerShell 的 Azure。

        Login-AzureRmAccount 

2. 取得您 Azure 訂閱的清單。

        Get-AzureRmSubscription

3. 指定您想要使用的訂閱。 

        Select-AzureRmSubscription -SubscriptionName "Name of subscription"

4. 您想要使用的變數宣告。 使用下列範例中，以取代您自己時所需的值。

        $VNetName  = "VNet1"
        $FESubName = "FrontEnd"
        $BESubName = "Backend"
        $GWSubName = "GatewaySubnet"
        $VNetPrefix1 = "192.168.0.0/16"
        $VNetPrefix2 = "10.254.0.0/16"
        $FESubPrefix = "192.168.1.0/24"
        $BESubPrefix = "10.254.1.0/24"
        $GWSubPrefix = "192.168.200.0/26"
        $VPNClientAddressPool = "172.16.201.0/24"
        $RG = "TestRG"
        $Location = "East US"
        $DNS = "8.8.8.8"
        $GWName = "VNet1GW"
        $GWIPName = "VNet1GWPIP"
        $GWIPconfName = "gwipconf"
        
## <a name="ConfigureVNet"></a>第 2 部分-設定 VNet 

1. 建立資源群組。

        New-AzureRmResourceGroup -Name $RG -Location $Location

2. 建立區分*FrontEnd*與*後端*， *GatewaySubnet*虛擬網路的子網路設定。 這些前置詞必須是您宣告 VNet 位址空間的一部分。

        $fesub = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
        $besub = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
        $gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix

3. 建立虛擬網路。 指定應該可以解決您連線到的資源名稱的 DNS 伺服器。 例如，我們要使用的公用 IP 位址。 請務必使用您自己的值。
    
        New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer $DNS

4. 指定您所建立的虛擬網路的變數。

        $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet

5. 要求動態指派的公用 IP 位址。 這個 IP 位址是必要的閘道器至正常運作。 您稍後再連線閘道器至閘道器 IP 設定。
        
        $pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
        $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip

## <a name="Certificates"></a>第 3 部分-憑證

Azure 會使用憑證驗證的點為網站 Vpn VPN 用戶端。 為 64 基本編碼 X.509.cer 檔案從企業憑證解決方案，所產生的根憑證或自我簽署的根憑證，您就會匯出公用憑證資料 （非私密金鑰）。 您然後資料匯入公用憑證根憑證至 Azure。 此外，您需要從根憑證的用戶端憑證產生的用戶端。 想要連線至虛擬網路使用 P2S 連線每個用戶端必須從根憑證產生的用戶端憑證安裝。

### <a name="cer"></a>1.取得根憑證的.cer 檔案

您需要取得您想要使用的根憑證公用憑證資料。

- 如果您使用的企業憑證系統，取得您想要使用的根憑證的.cer 檔案。 

- 如果您不使用企業憑證解決方案，您需要產生自我簽署的根憑證。 在 Windows 10 的步驟，您可以使用[自我簽署的根憑證點為網站設定](vpn-gateway-certificates-point-to-site.md)參考。


1. 若要取得.cer 檔案從憑證，請開啟**certmgr.msc**並找出根憑證。 以滑鼠右鍵按一下自我簽署的根憑證，按一下 [**全部任務**]，再按一下 [**匯出**。 這會開啟 [**憑證匯出精靈**]。

2. 在精靈中，按一下 [**下一步****否，不會匯出私密金鑰**]，然後選取然後按 [**下一步**。

3. 在 [**匯出檔案格式**] 頁面上選取**64 基本編碼 X.509 (。CER)。** 然後，請按一下 [**下一步**]。 

4. 在**[要匯出的檔案**，**瀏覽**到您要匯出的憑證的位置。 [**檔案名稱**] 中，名稱憑證檔案。 然後按一下 [**下一步**。

5. 按一下 [**完成**]，匯出的憑證。

### <a name="generate"></a>2.產生的用戶端憑證

接下來，產生的用戶端憑證。 您可以產生的唯一的憑證每個用戶端連線，或您可以在多個用戶端上使用相同的憑證。 產生唯一的用戶端憑證的優點是撤銷單一憑證，如有需要的能力。 否則，如果每個人都使用相同的用戶端憑證，而您找到您需要撤銷一部用戶端的憑證，您必須產生，並為所有用於憑證驗證的用戶端安裝新的憑證。 稍後在練習中每個用戶端電腦上安裝用戶端憑證。

- 如果您使用的企業憑證解決方案，產生的常見的名稱值格式的用戶端憑證'name@yourdomain.com',而非 NetBIOS 「 網域 \ 使用者名稱 」 格式。 

- 如果您使用自我簽署的憑證，請參閱[使用點為網站設定自我簽署的根憑證](vpn-gateway-certificates-point-to-site.md)產生的用戶端憑證。

### <a name="exportclientcert"></a>3.匯出用戶端憑證

需要驗證的用戶端憑證。 產生之後的用戶端憑證，請將其匯出。 將每個用戶端電腦上稍後安裝您匯出的用戶端憑證。

1. 若要匯出的用戶端憑證，您可以使用*certmgr.msc*。 以滑鼠右鍵按一下您想要匯出，按一下 [**所有任務**，再按一下 [**匯出**用戶端憑證。
2. 匯出私密金鑰的用戶端憑證。 這是*.pfx*檔。 請務必記錄，或請記住，您設定這個憑證的密碼 （按鍵）。

### <a name="upload"></a>4.根憑證.cer 檔案上傳

將您的憑證名稱，取代值以自己的變數宣告︰

        $P2SRootCertName = "Mycertificatename.cer"

新增 Azure 公用憑證資料根憑證。 您可以上傳檔案的 20 個根憑證。 您不上載至 Azure 私密金鑰的根憑證。 上傳的.cer 檔案後 Azure 會用它來驗證連線至虛擬網路的用戶端。 

檔案路徑取代為 [您自己的圖片，然後再執行 cmdlet。
    
        $filePathForCert = "C:\cert\Mycertificatename.cer"
        $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
        $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
        $p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64

## <a name="creategateway"></a>第 4 部分-建立 VPN 閘道器

設定並建立您 VNet 虛擬網路閘道器。 *-GatewayType*必須**要有 vpn 才能**和*-VpnType*必須**RouteBased**。 此花費 45 分鐘才能完成。

        New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
        -Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
        -VpnType RouteBased -EnableBgp $false -GatewaySku Standard `
        -VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert

## <a name="clientconfig"></a>第 5 部分-下載 VPN 用戶端設定套件

用戶端連線至 Azure 使用 P2S 必須有一個用戶端憑證和 VPN 用戶端設定套件的安裝。 VPN 用戶端設定套件可供使用 Windows 用戶端。 VPN 用戶端套件包含可設定 VPN 用戶端軟體的 Windows 內建且 VPN 連線到您想要的特定資訊。 封裝不會安裝其他軟體。 請參閱[VPN 閘道器常見問題集](vpn-gateway-vpn-faq.md#point-to-site-connections)的詳細資訊。

1. 在建立閘道器之後，您可以下載用戶端設定套件。 此範例中為 64 位元用戶端下載套件。 如果您想要下載 32 位元用戶端，取代 'x86' 'Amd64 」。 您也可以使用 [Azure 入口網站，以下載 VPN 用戶端。

        Get-AzureRmVpnClientPackage -ResourceGroupName $RG `
        -VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64

2. PowerShell 指令程式會傳回 URL 的連結。 這是什麼其傳回的 URL 看起來像範例︰

        "https://mdsbrketwprodsn1prod.blob.core.windows.net/cmakexe/4a431aa7-b5c2-45d9-97a0-859940069d3f/amd64/4a431aa7-b5c2-45d9-97a0-859940069d3f.exe?sv=2014-02-14&sr=b&sig=jSNCNQ9aUKkCiEokdo%2BqvfjAfyhSXGnRG0vYAv4efg0%3D&st=2016-01-08T07%3A10%3A08Z&se=2016-01-08T08%3A10%3A08Z&sp=r&fileExtension=.exe"

3. 複製並貼上連結的網頁瀏覽器下載套件會傳回。 然後在用戶端電腦上安裝套件。 如果您收到 SmartScreen 快顯視窗，請按一下 [**詳細資訊**，然後才能安裝套件的 [**繼續執行**。

4. 用戶端電腦上，瀏覽至 [**網路設定**，然後按一下 [ **VPN**]。 您會看到所列的連線。 隨即會顯示的名稱會連線至的虛擬網路和看起來類似此範例中︰ 

    ![VPN 用戶端](./media/vpn-gateway-howto-point-to-site-rm-ps/vpn.png "VPN 用戶端")


## <a name="clientcertificate"></a>組件 6-安裝用戶端憑證

每個用戶端電腦必須用戶端憑證以進行驗證。 安裝用戶端憑證，您必須匯出用戶端憑證時所建立的密碼。

1. 將.pfx 檔複製到 [用戶端電腦。
2. 按兩下.pfx 檔，進行安裝。 請勿修改安裝位置。

## <a name="connect"></a>第 7-連線至 Azure

1. 若要連線到您 VNet，用戶端電腦上，瀏覽至 VPN 連線，找出您建立 VPN 連線。 名稱虛擬網路相同的名稱。 按一下 [**連線**]。 可能會出現快顯的訊息參照到使用的憑證。 如果這種情況，請按一下 [**繼續**] 以使用提高權限的權限。 

2. 在 [**連線**狀態] 頁面中，按一下 [**連線**]，開始連線。 如果您看到**選取憑證]**畫面，請確認顯示的用戶端憑證的您要用來連線。 如果不是，選取正確的認證，請使用下拉式箭號，然後按一下**[確定]**。

    ![VPN 用戶端連線](./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png "VPN 用戶端連線")

3. 現在應該建立您的連線。

    ![建立連線](./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png "建立連線")

## <a name="verify"></a>第 8-驗證您的連線

1. 若要確認您的 VPN 連線作用中，開啟提高權限的命令提示字元中，並執行*ipconfig/所有*。

2. 檢視結果。 請注意，您所收到的 IP 位址是其中一個中點對網站 VPN 用戶端位址集區設定中指定的地址。 結果應該是類似以下內容︰
    
        PPP adapter VNet1:
            Connection-specific DNS Suffix .:
            Description.....................: VNet1
            Physical Address................:
            DHCP Enabled....................: No
            Autoconfiguration Enabled.......: Yes
            IPv4 Address....................: 172.16.201.3(Preferred)
            Subnet Mask.....................: 255.255.255.255
            Default Gateway.................:
            NetBIOS over Tcpip..............: Enabled

## <a name="addremovecert"></a>若要新增或移除受信任的根憑證

憑證用來驗證的點為網站 Vpn VPN 用戶端。 下列步驟會引導您新增及移除根憑證。 當您將 Base64 編碼的 X.509 (.cer) 檔案新增至 Azure 時，會告訴至信任的根憑證檔案所代表的 Azure。 

您可以新增或移除受信任的根憑證，使用 PowerShell，或在 Azure 入口網站。 如果您想要執行此動作使用 Azure 入口網站，請移至您**虛擬網路閘道器 > 設定 > 點為網站設定 > 根憑證**。 下列步驟會引導您完成使用 PowerShell 這些工作。 

### <a name="add-a-trusted-root-certificate"></a>新增受信任的根憑證

您可以新增 Azure 20 個受信任的根憑證.cer 檔案。 請遵循下列步驟來新增根憑證。

1. 建立並準備新的根憑證，您將會新增至 Azure。 匯出公開金鑰為 64 基本編碼 X.509 (。CER) 並將其開啟以文字編輯器。 然後複製以下所示的一節。 
 
    將值，複製下列範例所示︰

    ![憑證](./media/vpn-gateway-howto-point-to-site-rm-ps/copycert.png "憑證")
    
2. 為變數指定的憑證名稱和重要資訊。 取代您自己的如下列所示的範例中的資訊︰

        $P2SRootCertName2 = "ARMP2SRootCert2.cer"
        $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"


3. 新增新的根憑證。 您只可以一次新增一個憑證。

        Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $MyP2SCertPubKeyBase64_2


4. 您可以檢查新的憑證已正確新增使用下列指令程式。

        Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
        -VirtualNetworkGatewayName "VNet1GW"

### <a name="to-remove-a-trusted-root-certificate"></a>若要移除信任的根憑證

您可以從 Azure 移除受信任的根憑證。 當您移除受信任的憑證時，從根憑證產生的用戶端憑證會無法連線至 Azure 透過點到網站。 如果您想用戶端連線時，他們必須安裝新的用戶端憑證產生從 Azure 中信任的憑證。

1. 若要移除信任的根憑證，修改下列範例︰

    宣告變數。

        $P2SRootCertName2 = "ARMP2SRootCert2.cer"
        $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"

2. 移除的憑證。

        Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
 
3. 使用下列 cmdlet，以驗證憑證已成功移除。 

        Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
        -VirtualNetworkGatewayName "VNet1GW"

## <a name="revoke"></a>若要管理的清單已撤銷用戶端憑證

您可以撤銷用戶端憑證。 憑證撤銷清單可讓您可以選擇性地拒絕個別的用戶端憑證以點為網站連線。 如果您是從 Azure 移除根憑證.cer，它會撤銷所有用戶端憑證產生/簽章來撤銷的根憑證存取。 如果您想要撤銷特定的用戶端憑證，不根目錄，您可以執行此作業。 如此一來從根憑證產生的憑證仍會是有效的。

常見的做法是使用根憑證時使用的個別使用者的微調存取控制撤銷的用戶端憑證管理小組或組織層級的存取權。

### <a name="revoke-a-client-certificate"></a>撤銷用戶端憑證

1. 取得用戶端憑證撤銷的指紋。

        $RevokedClientCert1 = "ClientCert1"
        $RevokedThumbprint1 = "‎ef2af033d0686820f5a3c74804d167b88b69982f"

2. 新增指紋撤銷指紋的清單。

        Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
        -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1

3. 請檢查指紋已新增至憑證撤銷清單。 您必須一次新增一個指紋。

        Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

### <a name="reinstate-a-client-certificate"></a>恢復用戶端憑證

您可於用戶端憑證恢復指紋移除撤銷的用戶端憑證的清單。

1.  撤銷的用戶端憑證指紋的清單中移除指紋。

        Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
        -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1

2. 請檢查指紋是否從中撤銷清單。

        Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

## <a name="next-steps"></a>後續步驟

您可以新增虛擬機器至虛擬網路。 如需的步驟，請參閱[建立虛擬機器](../virtual-machines/virtual-machines-windows-hero-tutorial.md)。