<properties
    pageTitle="建立 SQL Server 虛擬機器中 Azure PowerShell （資源管理員） |Microsoft Azure"
    description="提供步驟與 PowerShell 指令碼建立 Azure VM 與 SQL Server 虛擬機器圖庫的圖像。"
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-resource-manager" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/25/2016"
    ms.author="jroth"/>

# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-resource-manager"></a>佈建 SQL Server 虛擬機器使用 PowerShell 的 Azure （資源管理員）

> [AZURE.SELECTOR]
- [入口網站](virtual-machines-windows-portal-sql-server-provision.md)
- [PowerShell](virtual-machines-windows-ps-sql-create.md)

## <a name="overview"></a>概觀

本教學課程教您如何建立單一 Azure 虛擬機器使用**Azure 資源管理員**部署模型使用 PowerShell 的 Azure cmdlet。 在本教學課程中，我們會建立單一虛擬機器 SQL 庫中使用單一磁碟機的圖像。 我們會建立新存放裝置、 網路和將虛擬機器所使用的計算資源的提供者。 如果您有這些資源的任何現有的提供者，您可以改為使用這些提供者。

如果您需要精簡版本的本文，請參閱[佈建使用 Azure PowerShell 傳統的 SQL Server 虛擬機器](virtual-machines-windows-classic-ps-sql-create.md)。

## <a name="prerequisites"></a>必要條件

在此教學課程，您會需要︰

- Azure 帳戶，並且在您開始之前的訂閱。 如果您還沒有，註冊[免費試用版](https://azure.microsoft.com/pricing/free-trial/)。
- [PowerShell 的 azure)](../powershell-install-configure.md)，最小版本 1.4.0 或更新版本 （使用版本 1.5.0 撰寫此教學課程）。
    - 若要擷取您的版本，請輸入**取得模組 Azure ListAvailable**。

## <a name="configure-your-subscription"></a>設定您的訂閱

開啟 Windows PowerShell，並執行下列 cmdlet 建立存取 Azure 帳戶。 您會看到登入畫面，輸入您的認證。 使用相同電子郵件與您用來登入 Azure 入口網站的密碼。

    Add-AzureRmAccount

順利登入後您會看到包含您登入 SubscriptionId 的畫面上的部分資訊。 這是在此教學課程的資源將會建立的除非您變更為不同的訂閱的訂閱。 如果您有多個 SubscriptionIds，請執行下列 cmdlet，以傳回的所有您 SubscriptionIds 清單︰

    Get-AzureRmSubscription

若要變更為另一個 SubscriptionID，請執行下列 cmdlet 與您所要的 SubscriptionId。

    Select-AzureRmSubscription -SubscriptionId xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

## <a name="define-image-variables"></a>定義圖像的變數

若要簡化合用性和了解已完成的指令碼，從本教學課程中，我們會先定義變數的數字。 當您看到符合，但小心命名時修改提供的值相關名稱長度和特殊字元的限制，請變更參數值。

### <a name="location-and-resource-group"></a>位置和資源群組
您可以使用兩個變數來定義資料區域，讓您將會建立虛擬機器的其他資源的 [資源] 群組。

視需要修改，然後執行下列 cmdlet，以啟動這些變數。

    $Location = "SouthCentralUS"
    $ResourceGroupName = "sqlvm1"

### <a name="storage-properties"></a>儲存屬性

您可以使用下列變數來定義的儲存空間帳戶]，然後輸入的虛擬機器所使用的儲存空間。

視需要修改，然後執行下列 cmdlet 初始化這些變數。 請注意，在此範例中，我們使用[進階版儲存空間](../storage/storage-premium-storage.md)，建議生產負載。 本指南和其他建議的詳細資訊，請參閱[效能的 SQL Server Azure 虛擬機器中的商務連絡人的最佳作法](virtual-machines-windows-sql-performance.md)。

    $StorageName = $ResourceGroupName + "storage"
    $StorageSku = "Premium_LRS"

### <a name="network-properties"></a>網路內容

您可以使用下列變數來定義的網路介面、 TCP/IP 配置方法、 虛擬網路名稱、 虛擬子網路名稱、 IP 位址範圍的虛擬網路、 IP 位址範圍的子網路及公用的網域名稱標籤可藉由虛擬機器中的網路。

視需要修改，然後執行下列 cmdlet 初始化這些變數。

    $InterfaceName = $ResourceGroupName + "ServerInterface"
    $TCPIPAllocationMethod = "Dynamic"
    $VNetName = $ResourceGroupName + "VNet"
    $SubnetName = "Default"
    $VNetAddressPrefix = "10.0.0.0/16"
    $VNetSubnetAddressPrefix = "10.0.0.0/24"
    $DomainName = "sqlvm1"   

### <a name="virtual-machine-properties"></a>虛擬機器屬性

您可以使用下列變數來定義的虛擬機器名稱、 電腦名稱、 虛擬機器大小和虛擬機器的作業系統磁碟名稱。

視需要修改，然後執行下列 cmdlet 初始化這些變數。

    $VMName = $ResourceGroupName + "VM"
    $ComputerName = $ResourceGroupName + "Server"
    $VMSize = "Standard_DS13"
    $OSDiskName = $VMName + "OSDisk"

### <a name="image-properties"></a>圖像內容

您可以使用下列變數來定義的圖像，用於虛擬機器。 在此範例中，會使用 SQL Server 2016 企業圖像。

視需要修改，然後執行下列 cmdlet 初始化這些變數。

    $PublisherName = "MicrosoftSQLServer"
    $OfferName = "SQL2016-WS2016"
    $Sku = "Enterprise"
    $Version = "latest"

請注意，您可以取得完整的 SQL Server 取得 AzureRmVMImageOffer 命令的圖像售價清單︰

    Get-AzureRmVMImageOffer -Location 'East US' -Publisher 'MicrosoftSQLServer'

您可以看到 sku 皆可取得 AzureRmVMImageSku 命令提供使用。 下列命令會顯示所有 sku 皆可供**SQL2014SP1 WS2012R2**提供。

    Get-AzureRmVMImageSku -Location 'East US' -Publisher 'MicrosoftSQLServer' -Offer 'SQL2014SP1-WS2012R2' | Select Skus

## <a name="create-a-resource-group"></a>建立資源群組

資源管理員部署模型中，您建立的第一個物件是資源群組。 我們會使用 [[新增 AzureRmResourceGroup](https://msdn.microsoft.com/library/mt759837.aspx)指令程式來建立 Azure 資源群組和它的資源與資源群組名稱和先前初始化變數所定義的位置。

執行下列 cmdlet 建立新的資源群組。

    New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

## <a name="create-a-storage-account"></a>建立儲存的帳戶

虛擬機器需要儲存資源，作業系統磁碟和 SQL Server 資料和記錄檔。 為求，我們會建立單一磁碟兩者。 您可以附加其他磁碟稍後使用項目才將 SQL Server 資料與記錄檔專用的磁碟上的 [[新增 Azure 磁碟](https://msdn.microsoft.com/library/azure/dn495252.aspx)指令程式。 我們會使用 [[新增 AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx)指令程式來建立標準儲存帳戶，以新的資源群組和儲存體帳戶名稱、 儲存體 Sku 名稱，並使用先前初始化變數所定義的位置。

執行下列 cmdlet 建立您的新儲存帳戶。  

    $StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

## <a name="create-network-resources"></a>建立網路資源

虛擬機器需要網路資源的數字的網路連線。

- 每個虛擬機器需要虛擬網路。
- 虛擬網路必須至少有一個定義的子網路。
- 網路介面必須定義公用或私用的 IP 位址。

### <a name="create-a-virtual-network-subnet-configuration"></a>建立一個虛擬網路的子網路設定

我們會開始建立虛擬網路的子網路設定。 教學課程中，我們會建立預設子網路使用[新增 AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt619412.aspx)指令程式。 我們會建立我們虛擬網路的子網路設定與使用先前初始化變數所定義的子網路名稱與地址首碼。

>[AZURE.NOTE] 您可以定義當您使用這個 cmdlet，虛擬網路的子網路設定的其他內容，但這是不在此教學課程的範圍之內。

執行下列 cmdlet 建立您的虛擬子網路設定。

    $SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix

### <a name="create-a-virtual-network"></a>建立虛擬網路

接下來，我們將會建立虛擬網路使用[新增 AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx)指令程式。 我們將會建立新的 [資源] 群組中的虛擬網路、 名稱、 位置和地址使用前置詞定義使用先前初始化變數，並使用您在上一個步驟中定義的子網路設定。

執行下列 cmdlet 建立虛擬網路。

    $VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig

### <a name="create-the-public-ip-address"></a>建立的公用 IP 位址

現在，我們已定義我們虛擬網路，我們需要設定連線至虛擬機器的 IP 位址。 在此教學課程中，我們會建立一個使用動態的 IP 位址可支援網際網路連線的公用 IP 位址。 我們會使用 [[新增 AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt603620.aspx)指令程式來建立 prevously 的 [資源] 群組中，含有名稱、 位置、 配置方法與使用先前初始化變數定義 DNS 網域名稱標籤建立的公用 IP 位址。

>[AZURE.NOTE] 您可以定義當您使用這個 cmdlet 的公用 IP 位址的其他內容，但這是本教學課程中初始的範圍之內。 您也可以建立私人的地址或地址，使用靜態的地址，但這也不在此教學課程的範圍之內。

執行下列 cmdlet 建立您的公用 IP 位址。

    $PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName

### <a name="create-the-network-interface"></a>建立網路介面

我們已準備好要建立的網路介面，會使用我們的虛擬機器。 若要建立我們的網路介面舊版和建立的名稱、 位置、 子網路和先前定義的公用 IP 位址的 [資源] 群組中，我們將使用[新增 AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx)指令程式。

執行下列 cmdlet 建立您的網路介面。

    $Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id

## <a name="configure-a-vm-object"></a>設定 VM 物件

現在，我們已定義的儲存空間和網路資源，我們已準備好定義計算資源的虛擬機器。 教學課程中，我們會指定虛擬機器大小和作業系統的各種內容，請指定網路介面的我們先前所建立的定義 blob 儲存體，]，然後指定作業系統磁碟。

### <a name="create-the-vm-object"></a>建立 VM 物件

我們會先指定虛擬機器大小。 在此教學課程中，我們會指定 DS13。 我們會使用 [[新增 AzureRmVMConfig](https://msdn.microsoft.com/library/mt603727.aspx)指令程式來建立可設定的虛擬機器物件的名稱和使用先前初始化變數所定義的大小。

執行下列 cmdlet 建立虛擬機器物件。

    $VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>建立的認證物件，如要保留的名稱和密碼本機系統管理員認證

我們可以設定虛擬機器作業系統屬性之前，我們需要提供為安全的字串的本機系統管理員帳戶的認證。 若要這麼做，我們將使用[取得認證](https://technet.microsoft.com/library/hh849815.aspx)指令程式。

執行下列 cmdlet，然後在 Windows PowerShell 認證邀請視窗中，輸入 [名稱與密碼，才能使用 Windows 虛擬機器中的本機系統管理員帳戶。

    $Credential = Get-Credential -Message "Type the name and password of the local administrator account."

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>設定虛擬機器的作業系統屬性

現在，我們已準備好設定虛擬機器的作業系統屬性。 若要設定為 Windows 的作業系統的資料類型，需要安裝、 指定 cmdlet 啟用自動更新及設定的虛擬機器名稱、 電腦名稱，以及使用先前初始化變數認證[虛擬機器代理程式](virtual-machines-windows-classic-agents-and-extensions.md)，我們將使用[設定 AzureRmVMOperatingSystem](https://msdn.microsoft.com/library/mt603843.aspx)指令程式。

執行下列 cmdlet 設定您的虛擬機器作業系統屬性。

    $VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate

### <a name="add-the-network-interface-to-the-virtual-machine"></a>加入虛擬機器中的網路介面

接下來，我們會將我們所建立的網路介面先前加入虛擬機器。 若要新增使用網路介面變數先前定義的網路介面，我們將使用[新增 AzureRmVMNetworkInterface](https://msdn.microsoft.com/library/mt619351.aspx)指令程式。

執行下列 cmdlet 來設定您的虛擬機器的網路介面。

    $VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>設定用於虛擬機器磁碟的 blob 儲存位置

接下來，我們將使用先前定義的變數的虛擬機器所使用的磁碟的 blob 儲存位置。

執行下列 cmdlet 設定 blob 儲存位置。

    $OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>設定作業系統的虛擬機器的磁碟屬性

接下來，我們會設定作業系統的虛擬機器磁碟屬性。 我們會使用[設定 AzureRmVMOSDisk](https://msdn.microsoft.com/library/mt603746.aspx)指令程式來指定虛擬機器作業系統的來源圖像，若要設定讀取 （因為相同的磁碟上安裝 SQL Server） 快取及定義的虛擬機器名稱與作業系統磁碟定義使用我們先前定義的變數。

執行下列 cmdlet 設定作業系統的虛擬機器的磁碟內容。

    $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>指定虛擬機器的平台圖像

我們設定的最後一個步驟是指定我們虛擬機器的平台圖像。 教學課程中，我們使用最新的 SQL Server 2016 CTP 圖像。 我們會使用[設定 AzureRmVMSourceImage](https://msdn.microsoft.com/library/mt619344.aspx)指令程式來使用此影像所定義的先前定義的變數。

執行下列 cmdlet，以指定虛擬機器的平台影像。

    $VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

## <a name="create-the-sql-vm"></a>建立 SQL VM

既然您完成設定步驟，您就準備好要建立虛擬機器。 我們會使用 [[新增 AzureRmVM](https://msdn.microsoft.com/library/mt603754.aspx)指令程式來建立虛擬機器使用，我們已定義的變數。

執行下列 cmdlet 建立您的虛擬機器。

    New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

建立虛擬機器。 請注意，標準的儲存空間建立帳戶開機診斷因為虛擬機器的磁碟指定的儲存帳戶是進階版儲存帳戶。

您現在可以檢視此電腦[的公用 IP 位址和其完整的網域名稱](virtual-machines-windows-portal-sql-server-provision.md#Connect)，請參閱 Azure 入口網站中。  

## <a name="example-script"></a>範例指令碼

下列指令碼包含在此教學課程的完整的 PowerShell 指令碼。 假設您有已設定 Azure 訂閱若要使用 [**新增 AzureRmAccount** ] 及 [**選取 AzureRmSubscription**命令。


    # Variables
    ## Global
    $Location = "SouthCentralUS"
    $ResourceGroupName = "sqlvm1"
    ## Storage
    $StorageName = $ResourceGroupName + "storage"
    $StorageSku = "Premium_LRS"

    ## Network
    $InterfaceName = $ResourceGroupName + "ServerInterface"
    $VNetName = $ResourceGroupName + "VNet"
    $SubnetName = "Default"
    $VNetAddressPrefix = "10.0.0.0/16"
    $VNetSubnetAddressPrefix = "10.0.0.0/24"
    $TCPIPAllocationMethod = "Dynamic"
    $DomainName = "sqlvm1"

    ##Compute
    $VMName = $ResourceGroupName + "VM"
    $ComputerName = $ResourceGroupName + "Server"
    $VMSize = "Standard_DS13"
    $OSDiskName = $VMName + "OSDisk"

    ##Image
    $PublisherName = "MicrosoftSQLServer"
    $OfferName = "SQL2016-WS2016"
    $Sku = "Enterprise"
    $Version = "latest"

    # Resource Group
    New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

    # Storage
    $StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

    # Network
    $SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
    $VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
    $PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
    $Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id

    # Compute
    $VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
    $Credential = Get-Credential -Message "Type the name and password of the local administrator account."
    $VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
    $VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
    $OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
    $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

    # Image
    $VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

    ## Create the VM in Azure
    New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

## <a name="next-steps"></a>後續步驟
建立虛擬機器之後，您準備好使用 RDP 並設定連線的虛擬機器連線。 如需詳細資訊，請參閱[連線至 SQL Server 虛擬機器上 Azure （資源管理員）](virtual-machines-windows-sql-connect.md)。
