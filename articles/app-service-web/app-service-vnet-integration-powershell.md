<properties
    pageTitle="連線到您的虛擬網路的應用程式，使用 PowerShell"
    description="瞭解如何連線至並使用 PowerShell 來處理虛擬網路的指示"
    services="app-service"
    documentationCenter=""
    authors="ccompy"
    manager="wpickett"
    editor="cephalin"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="ccompy"/>

# <a name="connect-your-app-to-your-virtual-network-by-using-powershell"></a>連線到您的虛擬網路的應用程式，使用 PowerShell #

## <a name="overview"></a>概觀 ##

Azure 應用程式服務中，您可以在您的訂閱 （網頁、 行動電話或 API） 應用程式連線至 Azure 虛擬網路 (VNet)。 此功能稱為 VNet 整合。 不應使用應用程式服務環境功能，可讓您在虛擬網路的執行個體 Azure 應用程式服務混淆 VNet 整合功能。

VNet 整合功能有新的入口網站可供您使用傳統的部署模型或 Azure 資源管理員部署模型已部署的虛擬網路與整合的使用者介面 (UI)。 如果您想要深入瞭解功能，請參閱[整合 Azure 虛擬網路應用程式](web-sites-integrate-with-vnet.md)。

本文是不是有關如何使用使用者介面，但而有關如何使用 PowerShell 啟用整合。 因為不同的每一個部署模型的命令，這份文件會有每個部署模型的小節。  

您繼續使用這份文件之前，請確定您有︰

- 安裝最新版 Azure PowerShell SDK。 您可以將它安裝 Web 平台安裝程式。
- 在 [標準] 或 [進階版 SKU 執行 Azure 應用程式服務應用程式。

## <a name="classic-virtual-networks"></a>傳統的虛擬網路 ##

本節說明使用傳統的部署模型的虛擬網路的三項工作︰

1. 查看已存在的虛擬網路的閘道器設定為點為網站連線至連接您的應用程式。
1. 更新您的應用程式的虛擬網路整合資訊。
1. 您的虛擬網路連線中斷應用程式。

### <a name="connect-an-app-to-a-classic-vnet"></a>將應用程式連線到傳統的 VNet ###

虛擬網路連線應用程式，請執行下列三個步驟︰

1. 宣告它將會加入特定的虛擬網路 web 應用程式。 應用程式就會產生會提供給虛擬網路，以點為網站連線的憑證。
1. 上傳的 web 應用程式憑證虛擬網路，並擷取點為網站 VPN 套件 URI。
1. 以點為網站套件 URI 更新 web app 的虛擬網路連線。

第一個和第三個步驟會完全編寫指令碼，但第二步需要一次性、 手動動作入口網站，或是存取虛擬網路 Azure 資源管理員端點上執行**放置**或**更新**的動作。 連絡 Azure 支援，以啟用此。 在開始之前，請確定您已有已啟用的點為網站連線與部署閘道器的傳統虛擬網路。 若要建立閘道器，並啟用點為網站的連線，您需要使用入口網站所述，[建立 VPN 閘道器][createvpngateway]。

傳統的虛擬網路必須中為您應用程式服務方案保留應用程式的整合與同一份訂閱。

##### <a name="set-up-azure-powershell-sdk"></a>Azure PowerShell SDK 設定 #####

開啟 PowerShell 視窗，然後使用來設定您的 Azure 帳戶和訂閱︰

    Login-AzureRmAccount

命令會開啟以取得 Azure 認證的提示。 當您登入之後，請使用下列命令以選取您想要使用的訂閱。 請確認您使用的您的虛擬網路和應用程式服務方案中的訂閱。

    Select-AzureRmSubscription –SubscriptionName [WebAppSubscriptionName]

或

    Select-AzureRmSubscription –SubscriptionId [WebAppSubscriptionId]

##### <a name="variables-used-in-this-article"></a>本文所用的變數 #####

若要簡化命令，我們會將**$Configuration** PowerShell 變數與特定設定。

設定一個變數，如下所示 PowerShell 使用下列參數︰

    $Configuration = @{}
    $Configuration.WebAppResourceGroup = "[Your web app resource group]"
    $Configuration.WebAppName = "[Your web app name]"
    $Configuration.VnetSubscriptionId = "[Your vnet subscription id]"
    $Configuration.VnetResourceGroup = "[Your vnet resource group]"
    $Configuration.VnetName = "[Your vnet name]"

應用程式位置應該不含任何空格的位置。 例如，西美國是 westus。

    $Configuration.WebAppLocation = "[Your web app Location]"

下一個項目是寫入憑證的位置。 應該會在您的本機電腦上的可寫入的路徑。 請確定包含.cer 結尾。

    $Configuration.GeneratedCertificatePath = "[C:\Path\To\Certificate.cer]"

若要查看您的設定，請輸入**$Configuration**。

    > $Configuration

    Name                           Value
    ----                           -----
    GeneratedCertificatePath       C:\vnetcert.cer
    VnetSubscriptionId             efc239a4-88f9-2c5e-a9a1-3034c21ad496
    WebAppResourceGroup            vnetdemo-rg
    VnetResourceGroup              testase1-rg
    VnetName                       TestNetwork
    WebAppName                     vnetintdemoapp
    WebAppLocation                 centralus

本節的其他部分假設您已建立依照上述的變數。

##### <a name="declare-the-virtual-network-to-the-app"></a>宣告虛擬的網路應用程式 #####

您可以使用下列命令，告知應用程式，將會使用這項特定的虛擬網路。 這會導致產生必要的憑證的應用程式︰

    $vnet = New-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -PropertyObject @{"VnetResourceId" = "/subscriptions/$($Configuration.VnetSubscriptionId)/resourceGroups/$($Configuration.VnetResourceGroup)/providers/Microsoft.ClassicNetwork/virtualNetworks/$($Configuration.VnetName)"} -Location $Configuration.WebAppLocation -ApiVersion 2015-07-01

如果此命令順利完成， **$vnet**應該有一個**內容**的變數。 **屬性**變數應該包含憑證指紋和憑證資料。

##### <a name="upload-the-web-app-certificate-to-the-virtual-network"></a>上傳至虛擬網路的 web 應用程式憑證 #####

手動一次性步驟時需要每個訂閱，虛擬網路組合。 也就是說，如果您以虛擬網路的連線訂閱的 office 應用程式，您必須執行此步驟中，一次在您設定無論多少應用程式。 如果您要新增新的應用程式到另一個虛擬網路，您必須再次執行此動作。 原因是在訂閱中的層級 Azure 應用程式服務，產生的憑證設定，並在設定為每個應用程式會連線到的虛擬網路產生一次。

憑證會已設定如果遵循下列步驟，或使用入口網站整合與相同的虛擬網路。

第一步是產生.cer 檔案。 .Cer 檔案上傳至虛擬網路為第二個步驟。 若要從先前的步驟中 API 通話產生.cer 檔案，請執行下列命令。

    $certBytes = [System.Convert]::FromBase64String($vnet.Properties.certBlob)
    [System.IO.File]::WriteAllBytes("$($Configuration.GeneratedCertificatePath)", $certBytes)

憑證會在位置找到該**$Configuration.GeneratedCertificatePath**指定。

若要以手動方式上傳憑證，請使用 [ [Azure 入口網站][azureportal]和**瀏覽虛擬網路 （傳統）** > **VPN 連線** > **點-網站** > **管理憑證**。 從這裡開始上, 傳您的憑證。

##### <a name="get-the-point-to-site-package"></a>取得點為網站套件 #####

設定 web app 的虛擬網路連線的下一個步驟是取得點為網站套件，並提供給您的 web 應用程式。

將下列範本儲存到您的電腦，例如 C:\Azure\Templates\GetNetworkPackageUri.json 位置稱為 GetNetworkPackageUri.json 的檔案。

    {
        "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "certData": {
                "type": "string"
            },
            "certThumbprint": {
                "type": "string"
            },
            "networkName": {
                "type": "string"
            }
        },
        "variables": {
            "legacyVnetName": "[concat('Group ', resourceGroup().name, ' ', parameters('networkName'))]"
            },
            "resources": [
            ],
        "outputs" : {
            "PackageUri" :
            {
            "value" : "[listPackage(resourceId('Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates', parameters('networkName'), 'primary', parameters('certThumbprint')), '2014-06-01').packageUri]", "type" : "string"
            }
        }
    }


設定 [輸入的參數︰

    $parameters = @{"certData" = $vnet.Properties.certBlob ;
    certThumbprint = $vnet.Properties.certThumbprint ;
    "networkName" = $Configuration.VnetName }

撥號給指令碼︰

    $output = New-AzureRmResourceGroupDeployment -Name unused -ResourceGroupName $Configuration.VnetResourceGroup -TemplateParameterObject $parameters -TemplateFile C:\PATH\TO\GetNetworkPackageUri.json


變數**$output。Outputs.packageUri**現在包含 URI 將指定給您的 web 應用程式套件。

##### <a name="upload-the-point-to-site-package-to-your-app"></a>上傳至您的應用程式的點為網站套件 #####

最後一個步驟是提供這個套件的應用程式。 只要執行下一個命令︰

    $vnet = New-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)/primary" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-07-01 -PropertyObject @{"VnetName" = $Configuration.VnetName ; "VpnPackageUri" = $($output.Outputs.packageUri).Value } -Location $Configuration.WebAppLocation

如果郵件會要求您確認會覆寫現有的資源，請務必允許它。

此命令順利完成之後，您的應用程式現在應該連接至虛擬網路。 若要確認成功，請移至您的應用程式主控台，並輸入以下︰

    SET WEBSITE_

如果有一部分的目標虛擬網路名稱相符的值的 WEBSITE_VNETNAME 環境變數，已成功所有設定。

### <a name="update-classic-vnet-integration-information"></a>更新傳統 VNet 整合資訊 ###

若要更新或重新同步處理您的資訊，只要重複您先建立整合時，您所遵循的步驟。 這些步驟如下︰

1. 定義您的設定資訊。
1. 宣告虛擬的網路應用程式。
1. 取得點為網站套件。
1. 套件上傳點為網站至您的應用程式。

### <a name="disconnect-your-app-from-a-classic-vnet"></a>中斷傳統的 VNet 應用程式 ###

若要中斷應用程式，您需要虛擬網路的整合期間所設定的設定資訊。 使用這項資訊，有再從您的虛擬網路中斷應用程式命令。

    $vnet = Remove-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-07-01

## <a name="resource-manager-virtual-networks"></a>資源管理員虛擬網路 ##

資源管理員虛擬網路有 Azure 資源管理員 Api，其中簡化與傳統的虛擬網路比較時，某些程序。 我們還有的指令碼，可協助您完成下列工作︰

- 建立資源管理員虛擬網路，您的應用程式整合。
- 建立閘道器與中查看已存在的資源管理員虛擬網路，設定點為網站的連線，然後將您的應用程式整合。
- 整合查看已存在資源管理員虛擬有的網路的閘道器及點-網站連線啟用您的應用程式。
- 您的虛擬網路連線中斷應用程式。

### <a name="resource-manager-vnet-app-service-integration-script"></a>資源管理員 VNet 應用程式服務整合指令碼 ###

複製下列指令碼，並將其儲存為檔案。 如果您不想要使用的指令碼，隨意瞭解它，請參閱如何使用資源管理員虛擬網路設定項目。


    function ReadHostWithDefault($message, $default)
    {
        $result = Read-Host "$message [$default]"
        if($result -eq "")
        {
            $result = $default
        }
            return $result
        }

    function PromptCustom($title, $optionValues, $optionDescriptions)
    {
        Write-Host $title
        Write-Host
        $a = @()
        for($i = 0; $i -lt $optionValues.Length; $i++){
            Write-Host "$($i+1))" $optionDescriptions[$i]
        }
        Write-Host

        while($true)
        {
            Write-Host "Choose an option: "
            $option = Read-Host
            $option = $option -as [int]

            if($option -ge 1 -and $option -le $optionValues.Length)
            {
                return $optionValues[$option-1]
            }
        }
    }

    function PromptYesNo($title, $message, $default = 0)
    {
        $yes = New-Object System.Management.Automation.Host.ChoiceDescription "&Yes", ""
        $no = New-Object System.Management.Automation.Host.ChoiceDescription "&No", ""
        $options = [System.Management.Automation.Host.ChoiceDescription[]]($yes, $no)
        $result = $host.ui.PromptForChoice($title, $message, $options, $default)
        return $result
    }

    function CreateVnet($resourceGroupName, $vnetName, $vnetAddressSpace, $vnetGatewayAddressSpace, $location)
    {
        Write-Host "Creating a new VNET"
        $gatewaySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $vnetGatewayAddressSpace
        New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName -Location $location -AddressPrefix $vnetAddressSpace -Subnet $gatewaySubnet
    }

    function CreateVnetGateway($resourceGroupName, $vnetName, $vnetIpName, $location, $vnetIpConfigName, $vnetGatewayName, $certificateData, $vnetPointToSiteAddressSpace)
    {
        $vnet = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName
        $subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet

        Write-Host "Creating a public IP address for this VNET"
        $pip = New-AzureRmPublicIpAddress -Name $vnetIpName -ResourceGroupName $resourceGroupName -Location $location -AllocationMethod Dynamic
        $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $vnetIpConfigName -Subnet $subnet -PublicIpAddress $pip

        Write-Host "Adding a root certificate to this VNET"
        $root = New-AzureRmVpnClientRootCertificate -Name "AppServiceCertificate.cer" -PublicCertData $certificateData

        Write-Host "Creating Azure VNET Gateway. This may take up to an hour."
        New-AzureRmVirtualNetworkGateway -Name $vnetGatewayName -ResourceGroupName $resourceGroupName -Location $location -IpConfigurations $ipconf -GatewayType Vpn -VpnType RouteBased -EnableBgp $false -GatewaySku Basic -VpnClientAddressPool $vnetPointToSiteAddressSpace -VpnClientRootCertificates $root
    }

    function AddNewVnet($subscriptionId, $webAppResourceGroup, $webAppName)
    {
        Write-Host "Adding a new Vnet"
        Write-Host
        $vnetName = Read-Host "Specify a name for this Virtual Network"

        $vnetGatewayName="$($vnetName)-gateway"
        $vnetIpName="$($vnetName)-ip"
        $vnetIpConfigName="$($vnetName)-ipconf"

        # Virtual Network settings
        $vnetAddressSpace="10.0.0.0/8"
        $vnetGatewayAddressSpace="10.5.0.0/16"
        $vnetPointToSiteAddressSpace="172.16.0.0/16"

        $changeRequested = 0
        $resourceGroupName = $webAppResourceGroup

        while($changeRequested -eq 0)
        {
            Write-Host
            Write-Host "Currently, I will create a VNET with the following settings:"
            Write-Host
            Write-Host "Virtual Network Name: $vnetName"
            Write-Host "Resource Group Name:  $resourceGroupName"
            Write-Host "Gateway Name: $vnetGatewayName"
            Write-Host "Vnet IP name: $vnetIpName"
            Write-Host "Vnet IP config name:  $vnetIpConfigName"
            Write-Host "Address Space:$vnetAddressSpace"
            Write-Host "Gateway Address Space:$vnetGatewayAddressSpace"
            Write-Host "Point-To-Site Address Space:  $vnetPointToSiteAddressSpace"
            Write-Host
            $changeRequested = PromptYesNo "" "Do you wish to change these settings?" 1

            if($changeRequested -eq 0)
            {
                $vnetName = ReadHostWithDefault "Virtual Network Name" $vnetName
                $resourceGroupName = ReadHostWithDefault "Resource Group Name" $resourceGroupName
                $vnetGatewayName = ReadHostWithDefault "Vnet Gateway Name" $vnetGatewayName
                $vnetIpName = ReadHostWithDefault "Vnet IP name" $vnetIpName
                $vnetIpConfigName = ReadHostWithDefault "Vnet IP configuration name" $vnetIpConfigName
                $vnetAddressSpace = ReadHostWithDefault "Vnet Address Space" $vnetAddressSpace
                $vnetGatewayAddressSpace = ReadHostWithDefault "Vnet Gateway Address Space" $vnetGatewayAddressSpace
                $vnetPointToSiteAddressSpace = ReadHostWithDefault "Vnet Point-to-site Address Space" $vnetPointToSiteAddressSpace
            }
        }

        $ErrorActionPreference = "Stop";

        # We create the virtual network and add it here. The way this works is:
        # 1) Add the VNET association to the App. This allows the App to generate certificates, etc. for the VNET.
        # 2) Create the VNET and VNET gateway, add the certificates, create the public IP, etc., required for the gateway
        # 3) Get the VPN package from the gateway and pass it back to the App.

        $webApp = Get-AzureRmResource -ResourceName $webAppName -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup
        $location = $webApp.Location

        Write-Host "Creating App association to VNET"
        $propertiesObject = @{
         "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($resourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnetName)"
        }
        $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnetName)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup -Force

        CreateVnet $resourceGroupName $vnetName $vnetAddressSpace $vnetGatewayAddressSpace $location

        CreateVnetGateway $resourceGroupName $vnetName $vnetIpName $location $vnetIpConfigName $vnetGatewayName $virtualNetwork.Properties.CertBlob $vnetPointToSiteAddressSpace

        Write-Host "Retrieving VPN Package and supplying to App"
        $packageUri = Get-AzureRmVpnClientPackage -ResourceGroupName $resourceGroupName -VirtualNetworkGatewayName $vnetGatewayName -ProcessorArchitecture Amd64

        # Put the VPN client configuration package onto the App
        $PropertiesObject = @{
        "vnetName" = $VirtualNetworkName; "vpnPackageUri" = $packageUri
        }

        New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnetName)/primary" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup -Force

        Write-Host "Finished!"
    }

    function AddExistingVnet($subscriptionId, $resourceGroupName, $webAppName)
    {
        $ErrorActionPreference = "Stop";

        # At this point, the gateway should be able to be joined to an App, but may require some minor tweaking. We will declare to the App now to use this VNET
        Write-Host "Getting App information"
        $webApp = Get-AzureRmResource -ResourceName $webAppName -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
        $location = $webApp.Location

        $webAppConfig = Get-AzureRmResource -ResourceName "$($webAppName)/web" -ResourceType "Microsoft.Web/sites/config" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
        $currentVnet = $webAppConfig.Properties.VnetName
        if($currentVnet -ne $null -and $currentVnet -ne "")
        {
            Write-Host "Currently connected to VNET $currentVnet"
        }

        # Display existing vnets
        $vnets = Get-AzureRmVirtualNetwork
        $vnetNames = @()
        foreach($vnet in $vnets)
        {
            $vnetNames += $vnet.Name
        }

        Write-Host
        $vnet = PromptCustom "Select a VNET to integrate with" $vnets $vnetNames

        # We need to check if this VNET is able to be joined to a App, based on following criteria
            # If there is no gateway, we can create one.
            # If there is a gateway:
                # It must be of type Vpn
                # It must be of VpnType RouteBased
                # If it doesn't have the right certificate, we will need to add it.
                # If it doesn't have a point-to-site range, we will need to add it.

        $gatewaySubnet = $vnet.Subnets | Where-Object { $_.Name -eq "GatewaySubnet" }

        if($gatewaySubnet -eq $null -or $gatewaySubnet.IpConfigurations -eq $null -or $gatewaySubnet.IpConfigurations.Count -eq 0)
        {
            $ErrorActionPreference = "Continue";
            # There is no gateway. We need to create one.
            Write-Host "This Virtual Network has no gateway. I will need to create one."

            $vnetName = $vnet.Name
            $vnetGatewayName="$($vnetName)-gateway"
            $vnetIpName="$($vnetName)-ip"
            $vnetIpConfigName="$($vnetName)-ipconf"

            # Virtual Network settings
            $vnetAddressSpace="10.0.0.0/8"
            $vnetGatewayAddressSpace="10.5.0.0/16"
            $vnetPointToSiteAddressSpace="172.16.0.0/16"

            $changeRequested = 0

            Write-Host "Your VNET is in the address space $($vnet.AddressSpace.AddressPrefixes), with the following Subnets:"
            foreach($subnet in $vnet.Subnets)
            {
                Write-Host "$($subnet.Name): $($subnet.AddressPrefix)"
            }

            $vnetGatewayAddressSpace = Read-Host "Please choose a GatewaySubnet address space"

            while($changeRequested -eq 0)
            {
                Write-Host
                Write-Host "Currently, I will create a VNET gateway with the following settings:"
                Write-Host
                Write-Host "Virtual Network Name: $vnetName"
                Write-Host "Resource Group Name:  $($vnet.ResourceGroupName)"
                Write-Host "Gateway Name: $vnetGatewayName"
                Write-Host "Vnet IP name: $vnetIpName"
                Write-Host "Vnet IP config name:  $vnetIpConfigName"
                Write-Host "Address Space:$($vnet.AddressSpace.AddressPrefixes)"
                Write-Host "Gateway Address Space:$vnetGatewayAddressSpace"
                Write-Host "Point-To-Site Address Space:  $vnetPointToSiteAddressSpace"
                Write-Host
                $changeRequested = PromptYesNo "" "Do you wish to change these settings?" 1

                if($changeRequested -eq 0)
                {
                    $vnetGatewayName = ReadHostWithDefault "Vnet Gateway Name" $vnetGatewayName
                    $vnetIpName = ReadHostWithDefault "Vnet IP name" $vnetIpName
                    $vnetIpConfigName = ReadHostWithDefault "Vnet IP configuration name" $vnetIpConfigName
                    $vnetGatewayAddressSpace = ReadHostWithDefault "Vnet Gateway Address Space" $vnetGatewayAddressSpace
                    $vnetPointToSiteAddressSpace = ReadHostWithDefault "Vnet Point-to-site Address Space" $vnetPointToSiteAddressSpace
                }
            }

            $ErrorActionPreference = "Stop";

            Write-Host "Creating App association to VNET"
            $propertiesObject = @{
             "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($vnet.ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnetName)"
            }

            $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force

            # If there is no gateway subnet, we need to create one.
            if($gatewaySubnet -eq $null)
            {
                $gatewaySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $vnetGatewayAddressSpace
                $vnet.Subnets.Add($gatewaySubnet);
                Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
            }

            CreateVnetGateway $vnet.ResourceGroupName $vnetName $vnetIpName $location $vnetIpConfigName $vnetGatewayName $virtualNetwork.Properties.CertBlob $vnetPointToSiteAddressSpace

            $gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $vnet.ResourceGroupName -Name $vnetGatewayName
        }
        else
        {
            $uriParts = $gatewaySubnet.IpConfigurations[0].Id.Split('/')
            $gatewayResourceGroup = $uriParts[4]
            $gatewayName = $uriParts[8]

            $gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $vnet.ResourceGroupName -Name $gatewayName

            # validate gateway types, etc.
            if($gateway.GatewayType -ne "Vpn")
            {
                Write-Error "This gateway is not of the Vpn type. It cannot be joined to an App."
                return
            }

            if($gateway.VpnType -ne "RouteBased")
            {
                Write-Error "This gateways Vpn type is not RouteBased. It cannot be joined to an App."
                return
            }

            if($gateway.VpnClientConfiguration -eq $null -or $gateway.VpnClientConfiguration.VpnClientAddressPool -eq $null)
            {
                Write-Host "This gateway does not have a Point-to-site Address Range. Please specify one in CIDR notation, e.g. 10.0.0.0/8"
                $pointToSiteAddress = Read-Host "Point-To-Site Address Space"
                Set-AzureRmVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $gateway.Name -VpnClientAddressPool $pointToSiteAddress
            }

            Write-Host "Creating App association to VNET"
            $propertiesObject = @{
             "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($vnet.ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnet.Name)"
            }

            $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force

            # We need to check if the certificate here exists in the gateway.
            $certificates = $gateway.VpnClientConfiguration.VpnClientRootCertificates

            $certFound = $false
            foreach($certificate in $certificates)
            {
                if($certificate.PublicCertData -eq $virtualNetwork.Properties.CertBlob)
                {
                    $certFound = $true
                    break
                }
            }

            if(-not $certFound)
            {
                Write-Host "Adding certificate"
                Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName "AppServiceCertificate.cer" -PublicCertData $virtualNetwork.Properties.CertBlob -VirtualNetworkGatewayName $gateway.Name
            }
        }

        # Now finish joining by getting the VPN package and giving it to the App
        Write-Host "Retrieving VPN Package and supplying to App"
        $packageUri = Get-AzureRmVpnClientPackage -ResourceGroupName $vnet.ResourceGroupName -VirtualNetworkGatewayName $gateway.Name -ProcessorArchitecture Amd64

        # Put the VPN client configuration package onto the App
        $PropertiesObject = @{
        "vnetName" = $vnet.Name; "vpnPackageUri" = $packageUri
        }

        New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)/primary" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force

        Write-Host "Finished!"
    }

    function RemoveVnet($subscriptionId, $resourceGroupName, $webAppName)
    {
        $webAppConfig = Get-AzureRmResource -ResourceName "$($webAppName)/web" -ResourceType "Microsoft.Web/sites/config" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
        $currentVnet = $webAppConfig.Properties.VnetName
        if($currentVnet -ne $null -and $currentVnet -ne "")
        {
            Write-Host "Currently connected to VNET $currentVnet"

            Remove-AzureRmResource -ResourceName "$($webAppName)/$($currentVnet)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
        }
          else
        {
          Write-Host "Not connected to a VNET."
        }
    }

    Write-Host "Please Login"
    Login-AzureRmAccount

    # Choose subscription. If there's only one we will choose automatically

    $subs = Get-AzureRmSubscription
    $subscriptionId = ""

    if($subs.Length -eq 0)
    {
        Write-Error "No subscriptions bound to this account."
        return
    }

    if($subs.Length -eq 1)
    {
        $subscriptionId = $subs[0].SubscriptionId
    }
    else
    {
        $subscriptionChoices = @()
        $subscriptionValues = @()

        foreach($subscription in $subs){
        $subscriptionChoices += "$($subscription.SubscriptionName) ($($subscription.SubscriptionId))";
        $subscriptionValues += ($subscription.SubscriptionId);
        }

        $subscriptionId = PromptCustom "Choose a subscription" $subscriptionValues $subscriptionChoices
    }

    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    $resourceGroup = Read-Host "Please enter the Resource Group of your App"

    $appName = Read-Host "Please enter the Name of your App"

    $options = @("Add a NEW Virtual Network to an App", "Add an EXISTING Virtual Network to an App", "Remove a Virtual Network from an App");
    $optionValues = @(0, 1, 2)
    $option = PromptCustom "What do you want to do?" $optionValues $options

    if($option -eq 0)
    {
        AddNewVnet $subscriptionId $resourceGroup $appName
    }
    if($option -eq 1)
    {
        AddExistingVnet $subscriptionId $resourceGroup $appName
    }
    if($option -eq 2)
    {
        RemoveVnet $subscriptionId $resourceGroup $appName
    }

儲存的指令碼的複本。 在本文中，稱為 V2VnetAllinOne.ps1，但您可以使用另一個名稱。 沒有這個指令引數。 您只要執行。 將會執行指令碼的第一個項目會提示您登入。 登入後，指令碼取得您帳戶的詳細資料，並傳回的訂閱清單。 不計算您的認證的要求，初始的指令碼執行看起來像這樣︰

    PS C:\Users\ccompy\Documents\VNET> .\V2VnetAllInOne.ps1
    Please Login

    Environment           : AzureCloud
    Account               : ccompy@microsoft.com
    TenantId              : 722278f-fef1-499f-91ab-2323d011db47
    SubscriptionId        : af5358e1-acac-2c90-a9eb-722190abf47a
    CurrentStorageAccount :

    Choose a subscription

    1) 示範訂閱 (af5358e1-acac-2c90-a9eb-722190abf47a)
    2) MS 測試 (a5350f55-dd5a-41ec-2ddb-ff7b911bb2ef)
    3) 紫色示範訂閱 (2d4c99a4-57f9-4d5e-a0a1-0034c52db59d)

    選擇一個選項︰ 3

    帳戶︰ccompy@microsoft.com環境︰ AzureCloud 訂閱︰ 2d4c99a4-57f9-4d5e-a0a1-0034c52db59d 租用戶︰ 722278f-fef1-499f-91ab-2323d011db47

    請輸入您的應用程式的 [資源群組︰ hcdemo 路由，請輸入您的應用程式的名稱︰ v2vnetpowershell 您想要做什麼？

    1) 新增至應用程式虛擬網路
    2) 將現有虛擬網路新增至應用程式
    3) 移除應用程式虛擬網路

本節的其餘部分說明每三個選項。

### <a name="create-a-resource-manager-vnet-and-integrate-with-it"></a>建立資源管理員 VNet 並與其整合 ###

若要建立新的虛擬網路使用的資源管理員部署模型，並整合應用程式，請選取 [ **1) 新增至應用程式虛擬網路**。 這會提示您輸入的虛擬網路的名稱。 在這個案例中，如下列設定值，您可以看到我使用的名稱，v2pshell。

指令碼提供正在建立虛擬網路的相關詳細資訊。 如果我想，我可以變更的任何值。 在此範例中執行時，我在自己建立虛擬網路，有下列設定︰

    Virtual Network Name:         v2pshell
    Resource Group Name:          hcdemo-rg
    Gateway Name:                 v2pshell-gateway
    Vnet IP name:                 v2pshell-ip
    Vnet IP config name:          v2pshell-ipconf
    Address Space:                10.0.0.0/8
    Gateway Address Space:        10.5.0.0/16
    Point-To-Site Address Space:  172.16.0.0/12

    Do you wish to change these settings?
    [Y] Yes  [N] No  [?] Help (default is "N"):

如果您想要變更的任何值，請輸入**Y** ，然後進行變更。 當您滿意虛擬網路設定，請輸入**N** ，或只需按下 Enter 當畫面提示您瞭解如何變更設定。 從該處上直到完成為止，指令碼會告訴您一些其 「 i 執行，直到它開始建立虛擬網路閘道器。 為小時，最多可能會的步驟。 沒有進度指示器在此階段，但是指令碼會讓您知道閘道器時所建立。

指令碼完成時，它會顯示**已完成**]。 此時，您必須具有 [名稱] 和 [設定您所選的資源管理員虛擬網路。 此新的虛擬網路也會與您的應用程式整合。

### <a name="integrate-your-app-with-a-preexisting-resource-manager-vnet"></a>與現有的資源管理員 VNet 整合應用程式 ###

當您整合已經存在的虛擬網路，如果所提供的閘道器或點為網站連線沒有資源管理員虛擬網路時，指令碼會設定的。 如果 VNET 已經設定這些項目，指令碼會直接移至應用程式的整合。 若要開始此程序，請選取即可**2) 新增至應用程式的現有的虛擬網路**。

這個選項只有在您有已經存在的資源管理員虛擬網路的同一份訂閱為您的應用程式。 選取選項之後，您就會顯示資源管理員虛擬網路的清單。   

    Select a VNET to integrate with

    1) v2demonetwork
    2) v2pshell
    3) v2vnetintdemo
    4) v2asenetwork
    5) v2pshell2

    選擇一個選項︰ 5

選取您想要與整合虛擬網路] 即可。 如果您已經有的閘道器之已啟用的點為網站連線，指令碼只會與虛擬網路整合應用程式。 如果您沒有閘道器，您必須指定閘道器子網路。 閘道器子網路必須在虛擬網路位址空間，而且不能在其他任何子網路。 如果您有沒有閘道器虛擬網路，並執行此步驟中，項目看起來像這樣︰

    This Virtual Network has no gateway. I will need to create one.
    Your VNET is in the address space 172.16.0.0/16, with the following Subnets:
    default: 172.16.0.0/24
    Please choose a GatewaySubnet address space: 172.16.1.0/26

在此範例中，建立虛擬網路的閘道器之有下列設定︰

    Virtual Network Name:         v2pshell2
    Resource Group Name:          vnetdemo-rg
    Gateway Name:                 v2pshell2-gateway
    Vnet IP name:                 v2pshell2-ip
    Vnet IP config name:          v2pshell2-ipconf
    Address Space:                172.16.0.0/16
    Gateway Address Space:        172.16.1.0/26
    Point-To-Site Address Space:  172.16.0.0/12

    Do you wish to change these settings?
    [Y] Yes  [N] No  [?] Help (default is "N"):
    Creating App association to VNET

如果您想要變更這些設定，您可以執行。 否則，請按 enter 鍵，然後指令碼會建立您的閘道器，並將您的應用程式附加至虛擬網路。 閘道器建立時間仍小時，不過，因此請確定您記住。 所有項目完成時，指令碼會顯示**已完成**。

### <a name="disconnect-your-app-from-a-resource-manager-vnet"></a>中斷資源管理員 VNet 應用程式 ###

從您的虛擬網路中斷連線應用程式不會關閉使用中閘道器或停用點為網站的連線。 您可能會將其用於其他項目。 它也不會中斷，從您提供以外的任何其他應用程式。 若要執行此動作，請選取 [ **3) 移除應用程式虛擬網路**。 當您這麼做時，您會看到像這樣︰

    Currently connected to VNET v2pshell

    Confirm
    Are you sure you want to delete the following resource:
    /subscriptions/edcc99a4-b7f9-4b5e-a9a1-3034c51db496/resourceGroups/hcdemo-rg/providers/Microsoft.Web/sites/v2vnetpowers
    hell/virtualNetworkConnections/v2pshell
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

雖然指令碼說明刪除，它不會刪除虛擬網路。 它只會移除整合。 確認 [這是您想要做什麼之後，命令很快處理，並會告訴您**，則為 True**時。

<!--Links-->
[createvpngateway]: http://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/
[azureportal]: http://portal.azure.com
