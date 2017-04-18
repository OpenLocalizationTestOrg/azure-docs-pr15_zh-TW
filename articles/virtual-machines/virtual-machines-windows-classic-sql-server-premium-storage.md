<properties
    pageTitle="使用 SQL Server Azure 進階版儲存 |Microsoft Azure"
    description="本文會使用傳統的部署模型，以建立的資源，並讓指引使用 SQL Server 執行上 Azure 虛擬機器中的 Azure 進階版儲存空間。"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="danielsollondon"
    manager="jhubbard"
    editor="monicar"    
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/19/2016"
    ms.author="jroth"/>

# <a name="use-azure-premium-storage-with-sql-server-on-virtual-machines"></a>使用虛擬機器上的 SQL Server Azure 進階版儲存空間


## <a name="overview"></a>概觀

[Azure 進階版儲存](../storage/storage-premium-storage.md)是下一個產生提供低延遲和高處理量 IO 的儲存空間。 最適合用於金鑰 IO 大量負載，例如 SQL Server IaaS[虛擬機器](https://azure.microsoft.com/services/virtual-machines/)上的運作。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


本文提供規劃與移轉虛擬機器執行 SQL Server 使用進階版儲存空間的指引。 這包含 Azure 基礎架構 （網路，儲存區） 與來賓 Windows VM 步驟。 [附錄](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage)範例會示範如何移動以利用其改良本機的 SSD 儲存空間，使用 PowerShell 較大 Vm 完整全面涵蓋所有內容的端對端移轉。

請務必瞭解與 SQL Server 上 IAAS Vm 利用 Azure 進階版儲存的端對端程序。 包括︰

- 若要使用進階版儲存空間的必要條件的識別。
- 針對新的部署進階版儲存部署上 IaaS SQL Server 的範例。
- 移轉現有的部署，同時獨立伺服器及部署使用 SQL 永遠在可用性群組的範例。
- 可能的移轉方法。
- 完整端對端範例顯示 Azure、 Windows 和 SQL Server 移轉現有一直在實作的步驟。

如需背景 SQL Server Azure 虛擬機器中的商務連絡人資訊，請參閱[SQL Server Azure 虛擬機器中的商務連絡人](virtual-machines-windows-sql-server-iaas-overview.md)。

**作者︰**楊幣**技術檢閱者︰**路易斯卡洛斯斯空閒、 Sanjay Mishra、 Pravin Mital、 Juergen 邁可、 哥堤。

## <a name="prerequisites-for-premium-storage"></a>進階版儲存空間的先決條件

有數種使用進階版儲存空間的先決條件。

### <a name="machine-size"></a>電腦大小

使用進階版儲存空間，您必須使用 DS 數列虛擬機器 (VM)。 如果您不使用 DS 數列機器您之前的雲端服務中，您必須刪除現有 VM 保留連結的磁碟，與然後再重新建立 DS * 角色大小為 VM 建立新的雲端服務。 如需有關虛擬機器大小的詳細資訊，請參閱[虛擬機器和 Azure 雲端服務的大小](virtual-machines-linux-sizes.md)。

### <a name="cloud-services"></a>雲端服務

您可以只使用 DS * Vm 進階版儲存時建立新的雲端服務中。 如果您正在使用 SQL Server 永遠在 Azure 中，永遠上接聽會參照到雲端服務與相關聯的 Azure 內部或外部負載平衡器 IP 位址。 本文著重於如何移轉時維持在這個案例中的可用性。

> [AZURE.NOTE] DS * 數列必須部署至新的雲端服務的第一個 VM。

### <a name="regional-vnets"></a>地區 VNETS

您必須設定虛擬網路 (VNET) 主機服務是地區您 Vm DS * Vm。 此 」 會放大主體 」 VNET 是允許較大 Vm 佈建在其他叢集，並允許它們之間的通訊。 在下列的螢幕擷取畫面，醒目提示的位置會顯示地區 VNETs，而第一個結果會顯示 「 縮小 」 的 VNET。

![RegionalVNET][1]

您可以提高 Microsoft 支援票證移轉到地區的 VNET，Microsoft 就會進行的變更，然後完成移轉地區 VNETs，進行變更的網路設定的屬性 AffinityGroup。 第一次匯出 PowerShell 中網路設定，然後**位置**屬性取代**VirtualNetworkSite**項目中的 [ **AffinityGroup** ] 屬性。 指定`Location = XXXX`位置`XXXX`是 Azure 的區域。 然後再匯入新的設定。

例如，請考慮下列 VNET 設定︰

    <VirtualNetworkSite name="danAzureSQLnet" AffinityGroup="AzureSQLNetwork">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/8</AddressPrefix>
      <AddressPrefix>172.16.0.0/12</AddressPrefix>
    </AddressSpace>
    <Subnets>
    ...
    </VirtualNetworkSite>

要將此區域西歐洲 VNET，請變更 [下列設定︰

    <VirtualNetworkSite name="danAzureSQLnet" Location="West Europe">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/8</AddressPrefix>
      <AddressPrefix>172.16.0.0/12</AddressPrefix>
    </AddressSpace>
    <Subnets>
    ...
    </VirtualNetworkSite>

### <a name="storage-accounts"></a>儲存帳戶

您必須建立新的儲存空間帳戶設定進階版儲存空間。 請注意，使用進階版儲存區設定在儲存帳戶，不會在個別的 Vhd，不過使用 DS * 數列 VM 時，您可以附加 VHD 的進階版和標準儲存的帳戶。 如果您不想將 OS VHD 入進階版儲存的帳戶，您可以考慮這。

下列**新增 AzureStorageAccountPowerShell**命令與 「 Premium_LRS 」**類型**會建立進階版儲存帳戶︰

    $newstorageaccountname = "danpremstor"
    New-AzureStorageAccount -StorageAccountName $newstorageaccountname -Location "West Europe" -Type "Premium_LRS"   

### <a name="vhds-cache-settings"></a>Vhd 快取設定

建立進階版儲存帳戶的組件的磁碟的主要差異是磁碟快取設定。 SQL Server 資料大量磁碟的建議，您就會使用 「**快取已讀取**]。 用於交易記錄檔量，應該磁碟快取設定設為 [**無**]。 這是標準的儲存空間帳戶的建議的不同。

一旦附加 Vhd，就無法變更的快取設定。 您必須中斷連線，重新 VHD 附加更新快取設定。

### <a name="windows-storage-spaces"></a>Windows 儲存空間

按照您與上一個標準的儲存空間，您可以使用[Windows 儲存空間](https://technet.microsoft.com/library/hh831739.aspx)，這可讓您移轉 VM 的已利用儲存空間。 [附錄](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage)（步驟 9 和轉寄） 的範例會示範擷取及匯入使用多個附加 Vhd VM Powershell 程式碼。

儲存集區使用標準 Azure 儲存體帳戶用來增強處理量，並減少延遲。 您會發現值測試新的部署，儲存集區與進階版儲存空間，但它們執行新增其他複雜度儲存設定。

#### <a name="how-to-find-which-azure-virtual-disks-map-to-storage-pools"></a>如何找出哪些 Azure 虛擬磁碟對應] 以儲存集區

附加 vhd 建議不同的快取設定時，您可能決定 Vhd 複製到進階版儲存的帳戶。 不過，當您重新其附加至新的 DS 數列 VM，您可能需要變更快取設定。 會更容易套用建議快取設定，當您有另一個 Vhd SQL 資料檔案和記錄檔 （而非同時包含單一 VHD） 的進階版儲存空間。

> [AZURE.NOTE] 如果您有相同的磁碟區上的 SQL Server 資料和記錄檔，您選擇的快取選項，取決於您的資料庫工作負載的 IO 存取模式。 僅限測試可以示範的快取選項最適合這種情況。

不過，如果您使用的 Windows 儲存空間，這您需要查看的多個 Vhd 組成原始指令碼來找出哪些附加 Vhd 有哪些特定的資料庫，因此您可以在然後設定快取設定會相應地每個磁碟。

如果您沒有原始的指令碼，可用以顯示您 Vhd 對應的儲存集區，您可以使用下列步驟，來判斷磁碟/儲存集區對應。

每個磁碟，請使用下列步驟︰

1. 取得磁碟連接 vm**取得 AzureVM**命令清單︰

    取得 AzureVM ServiceName <servicename> -名稱<vmname>|取得 AzureDataDisk

1. 請注意 Diskname 和 LUN。

    ![DisknameAndLUN][2]

1. 將 VM 遠端桌面。 然後移至**電腦管理** | **裝置管理員** | **磁碟機**。 查看每個 ' Microsoft 虛擬磁碟 」 的屬性

    ![VirtualDiskProperties][3]

1. LUN 數字是您指定的 vm 附加 VHD LUN 數字的參照。
1. 如 「 Microsoft 虛擬磁碟] 移至 [**詳細資料**] 索引標籤，然後在 [**屬性**] 清單中，移至**驅動程式機碼**。 在**值**]，請注意**位移**，這是 0002 在下列的螢幕擷取畫面。 0002 表示參照的儲存集區 PhysicalDisk2。

    ![VirtualDiskPropertyDetails][4]

2. 針對每個儲存集區，傾印出的相關聯的磁碟︰

    取得 StoragePool-FriendlyName AMS1pooldata |取得平均

    ![GetStoragePool][5]

現在，您可以使用此資訊來建立關聯附加 Vhd 儲存集區中的實體磁碟。

一旦您對應 Vhd 然後卸離和進階版儲存帳戶將它們複製的儲存集區中的實體磁碟，然後將其以正確的快取設定。 請參閱步驟 8 到 12[附錄](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage)中的範例。 下列步驟說明如何 VM 附加 VHD 磁碟設定為 CSV 檔案中擷取、 複製 Vhd、 變更磁碟快取設定，並在最後重新 VM 部署為所有連接的磁碟的 DS 數列 VM。

### <a name="vm-storage-bandwidth-and-vhd-storage-throughput"></a>VM 儲存頻寬和 VHD 儲存處理量

儲存空間效能取決於指定 DS * 虛擬記憶體大小和 VHD 大小。 Vm 具備可附加的 Vhd 數目的不同配額和最大的頻寬，它們會支援 (MB/s)。 特定的頻寬數字，請參閱[虛擬機器和 Azure 雲端服務的大小](virtual-machines-linux-sizes.md)。

提高的 IOPS 達成與更大的磁碟大小。 您應該考慮這時思考您移轉路徑。 如需詳細資訊，[請參閱 IOPS 和磁碟類型的資料表](../storage-premium-storage.md#scalability-and-performance-targets-when-using-premium-storage)。

最後，請考慮 Vm 有不同的最大磁碟頻寬它們會支援所有附加的磁碟。 在高負載時，您可能會滲透的虛擬記憶體角色大小的最大的磁碟頻寬。 例如 Standard_DS14 將支援截至 512 MB/s;因此，您可能會與三個 P30 磁碟滲透 VM 的磁碟頻寬。 但在此範例中，可能會超過處理量限制的讀取和寫入 IOs 根據。

## <a name="new-deployments"></a>新的部署

以下兩節將示範如何將 SQL Server Vm 部署至進階版儲存空間。 如之前所述，您不一定需要將 OS 磁碟到進階版儲存空間。 您可以選擇這麼做，如果您想要將任何大量 IO 負載放在 OS VHD。

第一個範例示範利用現有 Azure 圖庫的圖像。 第二個範例會示範如何使用您在現有的標準儲存帳戶的自訂 VM 圖像。

> [AZURE.NOTE] 這些範例，假設您已經建立地區的 VNET。

### <a name="create-a-new-vm-with-premium-storage-with-gallery-image"></a>建立新的 VM 含有進階版存放庫的圖像

下列範例示範如何將 OS VHD 到進階版儲存空間，並附加進階版儲存 Vhd。 不過，您可以也將在標準的儲存空間帳戶中的 [OS 磁碟]，然後附加 Vhd 位於進階版儲存帳戶的。 會示範這兩種案例。

    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Set up subscription
    Set-AzureSubscription -SubscriptionName $mysubscription
    Select-AzureSubscription -SubscriptionName $mysubscription -Current  

#### <a name="step-1-create-a-premium-storage-account"></a>步驟 1︰ 建立進階版儲存帳戶


    #Create Premium Storage account, note Type
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  


#### <a name="step-2-create-a-new-cloud-service"></a>步驟 2︰ 建立新的雲端服務

    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location


#### <a name="step-3-reserve-a-cloud-service-vip-optional"></a>步驟 3︰ 保留雲端服務 VIP （選用）
    #check exisitng reserved VIP
    Get-AzureReservedIP

    $reservedVIPName = “sqlcloudVIP”
    New-AzureReservedIP –ReservedIPName $reservedVIPName –Label $reservedVIPName –Location $location

#### <a name="step-4-create-a-vm-container"></a>步驟 4︰ 建立 VM 容器
    #Generate storage keys for later
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    ##Generate storage acc contexts
    $xioContext = New-AzureStorageContext –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary   

    #Create container
    $containerName = 'vhds'
    New-AzureStorageContainer -Name $containerName -Context $xioContext

#### <a name="step-5-placing-os-vhd-on-standard-or-premium-storage"></a>步驟 5︰ 將 OS VHD 放置在標準或進階版儲存空間
    #NOTE: Set up subscription and default storage account which will be used to place the OS VHD in

    #If you want to place the OS VHD Premium Storage Account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $newxiostorageaccountname  

    #If you wanted to place the OS VHD Standard Storage Account but attach Premium Storage VHDs then you would run this instead:
    $standardstorageaccountname = "danstdams"

    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $standardstorageaccountname

#### <a name="step-6-create-vm"></a>步驟 6︰ 建立 VM
    #Get list of available SQL Server Images from the Azure Image Gallery.
    $galleryImage = Get-AzureVMImage | where-object {$_.ImageName -like "*SQL*2014*Enterprise*"}
    $image = $galleryImage.ImageName

    #Set up Machine Specific Information
    $vmName = "dsDan1"
    $vnet = "dansvnetwesteur"
    $subnet = "SQL"
    $ipaddr = "192.168.0.8"

    #Remember to change to DS series VM
    $newInstanceSize = "Standard_DS1"

    #create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS"

    #Machine User Credentials
    $userName = "myadmin"
    $pass = "mycomplexpwd4*"

    #Create VM Config
    $vmConfigsl = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $image  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    #Add Data and Log Disks to VM Config
    #Note the size specified ‘-DiskSizeInGB 1023’, this will attach 2 x P30 Premium Storage Disk Type
    #Utilising the Premium Storage enabled Storage account

    $vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-data1.vhd"
    $vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "logDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-log1.vhd"

    #Create VM
    $vmConfigsl  | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)  

    #Add RDP Endpoint
    $EndpointNameRDPInt = "3389"
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Add-AzureEndpoint -Name "EndpointNameRDP" -Protocol "TCP" -PublicPort "53385" -LocalPort $EndpointNameRDPInt  | Update-AzureVM

    #Check VHD storage account, these should be in $newxiostorageaccountname
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Get-AzureDataDisk
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName |Get-AzureOSDisk


### <a name="create-a-new-vm-to-use-premium-storage-with-a-custom-image"></a>建立新的 VM，若要使用的自訂圖像的進階版儲存空間

此案例示範必須位於標準儲存帳戶的現有自訂的圖像的位置。 如上所述如果您想要將 OS VHD 放在 [進階版儲存空間，您必須在 [標準儲存帳戶複製存在的圖像，並將其轉移到進階版存放，可供使用前。 如果您擁有內部部署的圖像，您也可以使用這個方法若要將複製的直接進階版儲存帳戶。

#### <a name="step-1-create-storage-account"></a>步驟 1︰ 建立儲存帳戶
    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Create Premium Storage account
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

    #Standard Storage account
    $origstorageaccountname = "danstdams"

#### <a name="step-2-create-cloud-service"></a>步驟 2 建立雲端服務
    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location


#### <a name="step-3-use-existing-image"></a>步驟 3︰ 使用現有的圖像
您可以使用現有的圖像。 或者，您可以[採取的現有機器圖像](virtual-machines-windows-classic-capture-image.md)。 請注意您圖像沒有 DS*電腦的電腦。下列步驟圖像之後，顯示如何將其複製到進階版儲存帳戶**開始 AzureStorageBlobCopy** PowerShell commandlet。

    #Get storage account keys:
    #Standard Storage account
    $originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
    #Premium Storage account
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    #Set up contexts for the storage accounts:
    $origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
    $destContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  

#### <a name="step-4-copy-blob-between-storage-accounts"></a>步驟 4︰ 複製 Blob 儲存體帳戶之間
    #Get Image VHD
    $myImageVHD = "dansoldonorsql2k14-os-2015-04-15.vhd"
    $containerName = 'vhds'

    #Copy Blob between accounts
    $blob = Start-AzureStorageBlobCopy -SrcBlob $myImageVHD -SrcContainer $containerName `
    -DestContainer vhds -Destblob "prem-$myImageVHD" `
    -Context $origContext -DestContext $destContext  

#### <a name="step-5-regularly-check-copy-status"></a>步驟 5︰ 定期檢查複製狀態︰
    $blob | Get-AzureStorageBlobCopyState

#### <a name="step-6-add-image-disk-to-azure-disk-repository-in-subscription"></a>步驟 6: Azure 磁碟存放庫訂閱中新增圖像磁碟
    $imageMediaLocation = $destContext.BlobEndPoint+"/"+$myImageVHD
    $newimageName = "prem"+"dansoldonorsql2k14"

    Add-AzureVMImage -ImageName $newimageName -MediaLocation $imageMediaLocation

> [AZURE.NOTE] 您可能會發現即使狀態報告為成功，您可能仍收到磁碟租用錯誤。 在此情況下，請等候 10 分鐘。

#### <a name="step-7--build-the-vm"></a>步驟 7︰ 建立 VM
以下圖像和附加兩個進階版儲存 Vhd 建置 VM:

    $newimageName = "prem"+"dansoldonorsql2k14"
    #Set up Machine Specific Information
    $vmName = "dansolchild"
    $vnet = "westeur"
    $subnet = "Clients"
    $ipaddr = "192.168.0.41"

    #This will need to be a new cloud service
    $destcloudsvc = "danregsvcamsxio2"

    #Use to DS Series VM
    $newInstanceSize = "Standard_DS1"

    #create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS3"

    #Machine User Credentials
    $userName = "myadmin"
    $pass = "theM)stC0mplexP@ssw0rd!”


    #Create VM Config
    $vmConfigsl2 = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $newimageName  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    $vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-Datadisk-1.vhd"
    $vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "LogDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-logdisk-1.vhd"



    $vmConfigsl2 | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet

## <a name="existing-deployments-that-do-not-use-always-on-availability-groups"></a>不使用永遠在可用性群組的現有部署

> [AZURE.NOTE] 針對現有的部署，首先請參閱本主題的[先決條件](#prerequisites-for-premium-storage)一節。

有不同永遠在可用性群組與的未使用的 SQL Server 部署考量。 如果您不使用永遠上並現有獨立的 SQL Server，您可以使用新的雲端儲存空間的服務與帳戶升級至進階版儲存空間。 請考慮下列選項︰

- **建立新的 SQL Server VM**。 如詳加說明新的部署，您可以建立新的 SQL Server VM 使用一個進階版儲存的帳戶。 然後備份及還原您的 SQL Server 組態和使用者資料庫。 應用程式必須更新，以參照新的 SQL Server 的內部或外部存取。 您想要複製所有的 「 不在 db' 物件時，如果您執行的並排顯示 (SxS) SQL Server 移轉。 這包含登入與連結的伺服器的憑證，例如的物件。
- **將現有的 SQL Server VM**。 這需要將 SQL Server VM 離線，然後將它傳送到新的雲端服務，包括複製所有其附加 Vhd 進階版儲存的帳戶。 VM 線上時，應用程式會參考伺服器主機名稱為之前。 請注意現有磁碟的大小會影響效能特性。 例如，400 GB 磁碟會無條件進位至 P20。 如果您知道您不需要效能的磁碟，然後您可以重新建立 DS 數列 VM，為 VM，附加您所需要的大小效能規格進階版儲存 Vhd。 您可能會中斷的連結，並重新連接的 SQL 資料庫檔案。

> [AZURE.NOTE] 當複製您應該知道的大小，根據大小 VHD 機，即表示進階版儲存空間的磁碟類型可將其分為時，這會決定磁碟效能規格。 Azure 會四捨五入，進位至最接近的磁碟調整大小，因此如果您有 400 GB 磁碟，這會被無條件進位至 P20。 根據 OS VHD 您現有 IO 需求，您可能不需要將此移轉到進階版儲存的帳戶。

如果外部存取您的 SQL Server 時，會變更雲端服務 VIP。 您也必須更新結束點、 Acl 和 DNS 設定。

## <a name="existing-deployments-that-use-always-on-availability-groups"></a>使用永遠在可用性群組的現有部署

> [AZURE.NOTE] 針對現有的部署，首先請參閱本主題的[先決條件](#prerequisites-for-premium-storage)一節。

一開始此區段中，我們會看看永遠上的互動方式 Azure 網路。 我們會劃分兩個案例中移轉︰ 移轉可容許某些停機時間的位置，您必須在此達到最小停機時間的移轉。

內部部署 SQL Server 永遠在可用性群組使用接聽內部部署的登錄虛擬 DNS 名稱以及共用一或多個 SQL Server 之間的 IP 位址。 用戶端連線時它們會經由接聽 IP 主要的 SQL Server。 這是擁有永遠上 IP 資源在該時間的伺服器。

![在 DeploymentsUseAlways][6]

在 Microsoft Azure，您可以讓只有一個指派給 VM 上 NIC 的 IP 位址，因此才能達成相同的圖層的內部部署，為抽象 Azure 利用指派給內部/外部負載平衡器 (ILB/ELB) 的 IP 位址。 在伺服器之間共用 IP 資源會設定為 ILB/ELB 為相同的 IP。 此刊登在 [DNS]，然後用戶端流量透過 ILB/ELB 傳遞至主要的 SQL Server 複本]。 ILB/ELB 知道這 SQL Server 是主要，因為它會使用探查探查永遠上 IP 資源。 在上一個範例中，它探查具有參照 ELB/ILB 端點的每個節點，以回應主要的 SQL Server。

> [AZURE.NOTE] ILB 及 ELB 同時指派給特定的 Azure 雲端服務，因此 Azure 中的任何雲端移轉最可能表示負載平衡器 IP 會變更。

### <a name="migrating-always-on-deployments-that-can-allow-some-downtime"></a>移轉一直在部署，可以讓一些停機時間

有兩種策略移轉一直在部署，讓某些停機時間的︰

1. **將更多的次要複本新增到現有永遠在叢集**
1. **移轉至新永遠在叢集**

#### <a name="1-add-more-secondary-replicas-to-an-existing-always-on-cluster"></a>1.將更多的次要複本新增到現有永遠在叢集

新增更多的次要連結到永遠在可用性群組可策略。 您需要新增這些到新的雲端服務及使用新的負載平衡器 IP 更新接聽程式。

##### <a name="points-of-downtime"></a>停機時間的事項︰

- 叢集驗證。
- 測試新的次要連結一直在容錯移轉。

如果您使用 Windows 的 VM 內的儲存集區為較高的 IO 處理量，然後這些會離線使用完整的叢集驗證期間。 當您新增至叢集的節點需要驗證測試。 執行測試的時間而有所不同，因此您應該取得大約需要多久時間代表測試環境中測試。

您應提供您要執行手動錯誤移轉及混亂測試新加入節點，以確保永遠在高可用性函數，如預期般的時間。

![DeploymentUseAlways On2][7]

> [AZURE.NOTE] 您應該停止所有可儲存資料庫的 SQL Server 執行個體驗證執行之前。
##### <a name="high-level-steps"></a>高層級的步驟

1. 建立新的雲端服務，具有附加的進階版儲存區中的兩個新的 SQL Server。
1. 複製完整的備份與還原利用**NORECOVERY**。
1. 複製 「 不在使用者 DB' 依存的物件，例如登入等。
1. 建立新的新的內部負載平衡器 (ILB) 或使用外部負載平衡器 (ELB)，然後再設定負載平衡結束點上兩個新的節點。
> [AZURE.NOTE] 檢查所有節點都有正確的端點設定，再繼續進行

1. （如果使用的儲存集區），請停止 SQL Server 使用者/應用程式存取。
1. （如果使用的儲存集區），請停止上所有的節點的 SQL Server 引擎服務。
1. 加入新的節點叢集，並執行完整驗證。
1. 驗證成功後，啟動 [所有的 SQL Server 服務]。
1. 備份交易記錄檔，及還原使用者資料庫。
1. 將 [永遠在可用性] 群組中的新節點，並將複寫到**同步**。
1. 新增一直在根據[附錄](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage)中的多網站範例的 IP 位址資源的新雲端服務 ILB/ELB 透過 PowerShell。 在 Windows 中叢集，將**可能的擁有者**的**IP 位址**資源為新舊的節點。 請參閱[附錄](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage)的 [新增 IP 位址資源相同子網路] 區段。
1. 容錯移轉到其中一個新的節點。
1. 自動容錯移轉合作夥伴及測試錯誤後移轉進行新的節點。
1. 可用性] 群組中移除原始的節點。

##### <a name="advantages"></a>優點

- 新的 SQL Server 可之前即會新增到 [永遠在測試 （SQL Server 和應用程式）。
- 您可以變更虛擬記憶體大小，並自訂您的確切需求的儲存空間。 不過，就會很有幫助保留所有 SQL 檔案路徑相同。
- 您可以控制何時會開始傳輸 DB 備份到第二個複本。 這與使用 Azure**開始 AzureStorageBlobCopy** commandlet 複製 Vhd，因為這是非同步複本。

##### <a name="disadvantages"></a>缺點
- 使用 Windows 儲存集區，有時叢集停機時間期間所新增的其他節點完整叢集驗證。
- 根據 SQL Server 版本和次要複本的現有的數字，您可能無法新增更多的次要複本，而不移除現有的次要連結。
- 可能長時設定次要連結 SQL 資料傳送時間。
- 時您有新的電腦，同時執行移轉中有其他成本。

#### <a name="2-migrate-to-a-new-always-on-cluster"></a>2.移轉至新永遠在叢集

另一種策略是全新永遠上以建立叢集全新節點新雲端服務中，然後重新導向用戶端使用它。

##### <a name="points-of-downtime"></a>停機時間的點

當您將應用程式和使用者傳送到新永遠上接聽有停機時間。 停機時間而定︰

- 若要將最後一個交易記錄檔備份還原資料庫在新的伺服器上所需的時間。
- 若要更新的用戶端應用程式，以使用新的永遠上接聽所花費的時間。

##### <a name="advantages"></a>優點

- 您可以測試實際生產環境，SQL Server，並 OS 建立的變更。
- 您可以選擇自訂儲存空間，並會減少 VM 的大小。 這可能會導致降低成本。
- 此程序期間，您可以更新您的 SQL Server 建立或版本。 您也可以升級的作業系統。
- 上一個永遠在叢集可做為實心復原目標。

##### <a name="disadvantages"></a>缺點

- 您需要變更接聽 DNS 名稱，如果您想要同時執行兩個一直在叢集。 當用戶端應用程式字串必須反映新接聽程式名稱，如此會將系統管理負擔移轉過程。
- 您必須執行兩種環境，讓為關閉，可能最小化移轉前的最後一個同步處理需求之間同步處理機制。
- 那里加入成本期間移轉時，您可以執行新環境。

### <a name="migrating-always-on-deployments-for-minimal-downtime"></a>移轉永遠上部署的最小停機時間

最小停機時間的有兩個移轉永遠上部署的策略︰

1. **使用現有的次要︰ 單一網站**
1. **使用現有的次要複本︰ 多網站**

#### <a name="1-utilize-an-existing-secondary-single-site"></a>1.使用現有的次要︰ 單一網站

最小停機時間的策略之一是現有的雲端次要並移除目前的雲端服務。 將 Vhd 複製到新的進階版儲存帳戶，然後建立新的雲端服務中的 VM。 然後更新中叢集和容錯移轉接聽程式。

##### <a name="points-of-downtime"></a>停機時間的點

- 當您更新最終節點與負載平衡端點時，有停機時間。
- 您的用戶端重新連線可能會根據您的用戶端/DNS 設定延遲。
- 如果您選擇需要永遠在叢集群組離線交換 IP 位址，有其他停機時間。 您可以使用的 OR 相依性和可能的擁有者新增的 IP 位址資源避免此問題。 請參閱[附錄](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage)的 [新增 IP 位址資源相同子網路] 區段。

> [AZURE.NOTE] 當您想要做為一律上容錯移轉夥伴斷崖中的新增的節點時，您需要新增 Azure 端點的負載平衡設定參考。 當您執行的 [**新增 AzureEndpoint**命令，請執行下列動作時，保持開啟，但新連線接聽目前連線會無法建立直到負載平衡器已更新。 測試此中看到至最後一個 90-120seconds，這應該測試。

##### <a name="advantages"></a>優點

- 沒有額外移轉時所產生的成本。
- 一對一移轉。
- 精簡的複雜性。
- 從進階版儲存 sku 皆可允許增加 IOPS。 當磁碟 VM 中斷連結複製到新的雲端服務，3rd 協力廠商工具可用來放大 VHD，可提供較高的歸於。 增加 VHD 大小，請參閱此[論壇討論](https://social.msdn.microsoft.com/Forums/azure/4a9bcc9e-e5bf-4125-9994-7c154c9b0d52/resizing-azure-data-disk?forum=WAVirtualMachinesforWindows)。

##### <a name="disadvantages"></a>缺點

- 在移轉過程中有 HA 和 DR 暫時遺失。
- 當這是 1:1 移轉時，您必須使用會支援您 Vhd 的數目，因此您可能無法 downsize 您 Vm 最低虛擬記憶體大小。
- 這種情況下會使用 Azure**開始 AzureStorageBlobCopy** commandlet，這是非同步。 複製完成沒有 SLA。 複製的時間，而異時，還會取決於要傳送的資料量的佇列中等待而定。 如果傳輸前往支援進階版儲存在另一個區域中的另一個 Azure 資料中心，也會提高複製時間。 如果您只需 2 節點，請考慮可能的降低以防複製時間長於測試。 這可能會包含下列的想法。
    - 新增與同意停機時間移轉前 HA 暫時第 3 SQL Server 節點。
    - 執行移轉外部 Azure 排程進行的維修作業。
    - 請確定您已正確地設定您的叢集仲裁。  

##### <a name="high-level-steps"></a>高層級的步驟

這份文件不會示範完整的端對端範例，不過[附錄](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage)提供可以利用執行此的詳細資料。

![MinimalDowntime][8]

- 收集磁碟設定，並移除節點 （不刪除附加的 Vhd）。
- 建立進階版儲存帳戶和 Vhd 複製標準儲存帳戶
- 建立新的雲端服務，並重新部署 SQL2 VM 的雲端服務中。 建立使用複製的原始 OS VHD 和附加複製的 Vhd VM。
- 設定 ILB / ELB，並加入端點。
- [更新接聽程式︰
    - 離線永遠在群組，並以新的 ILB 更新永遠上接聽 / ELB IP 位址。
    - 或新增到 Windows 叢集的 IP 位址資源的新雲端服務 ILB/ELB 透過 PowerShell。 接著將可能的擁有者的 IP 位址資源設定為 [移轉] 節點，SQL2，然後將此設為網路名稱或相依性。 請參閱[附錄](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage)的 [新增 IP 位址資源相同子網路] 區段。
- 核取 [DNS 設定/propogation 用戶端。
- 移轉 SQL1 VM，然後移至步驟 2-4。
- 如果使用的步驟 5ii，然後新增 SQL1 可能擁有新增的 IP 位址資源
- 測試容錯移轉。

#### <a name="2-utilize-existing-secondary-replicas-multi-site"></a>2.使用現有的次要複本︰ 多網站

如果您有一個以上的 Azure 資料中心 (DC) 中的節點，或如果您有混合式環境，然後您可以一直在設定環境中使用此停機降到最低。

方法是變更同步一直在同步處理內部部署或次要 Azure DC，然後移至該 SQL server 的容錯移轉。 然後 Vhd 複製到進階版儲存的帳戶，並重新部署到新的雲端服務的電腦。 更新接聽程式，並回會失敗。

##### <a name="points-of-downtime"></a>停機時間的點

停機時間組成容錯移轉到替代的 DC 和後的時間。 這也取決於您的用戶端/DNS 設定，可能會耽擱您用戶端重新連線。
請考慮下列混合式永遠上設定的範例︰

![MultiSite1][9]

##### <a name="advantages"></a>優點

- 您可以使用現有的基礎結構。
- 您可以選擇升級前的 Azure 儲存空間的 DR Azure DC 上第一次。
- 可以重新設定 DR Azure DC 儲存空間。
- 移轉過程中排除測試容錯移轉有兩個容錯移轉的最小值。
- 您不需要將 SQL Server 資料使用備份與還原。

##### <a name="disadvantages"></a>缺點

- 根據 SQL Server 的用戶端存取，有可能增加的延遲時在替代的 DC 執行 SQL Server 應用程式。
- 進階版存放裝置 Vhd 複製時可能會很長。 這可能會影響您決定是否要保留的可用性] 群組中的節點。 請考慮這載入執行移轉中的記錄大量工作時，因為主要節點必須將其交易記錄檔中的未複寫的交易。 因此這可能會變大。
- 這種情況下會使用 Azure**開始 AzureStorageBlobCopy** commandlet，這是非同步。 完成時，沒有 SLA。 複製的時間不盡相同，這取決於在佇列中，等待時，也會取決於要傳送的資料量。 因此您只需要一個節點第 2 資料中心，您應該採取降低步驟，以避免複製時間長於測試。 這可能會包含下列的想法。
    - 新增與同意停機時間移轉前 HA 暫存的第 2 SQL 節點。
    - 執行移轉外部 Azure 排程進行的維修作業。
    - 請確定您已正確地設定您的叢集仲裁。

此案例假設您記錄您的安裝，並瞭解如何在儲存對應才能進行變更的最佳的磁碟快取設定。

##### <a name="high-level-steps"></a>高層級的步驟
![Multisite2][10]

- 讓內部部署 / 替代 Azure DC SQL Server 主要，並將其其他自動容錯移轉合作夥伴 (AFP)。
- 從 SQL2，收集磁碟設定資訊並移除節點 （不刪除附加的 Vhd）。
- 建立進階版儲存帳戶並複製 Vhd 從標準儲存的帳戶。
- 建立新的雲端服務，並建立 SQL2 VM 附加其 Premiums 儲存磁碟。
- 設定 ILB / ELB，並加入端點。
- 使用新的 ILB 更新永遠上接聽 / ELB IP 位址和測試容錯移轉。
- 核取 [DNS 設定。
- 變更 AFP SQL2，接著移轉 SQL1，然後移至步驟 2 到 5。
- 測試容錯移轉。
- 切換回 [SQL1 和 SQL2 AFP

## <a name="appendix-migrating-a-multisite-always-on-cluster-to-premium-storage"></a>附錄︰ 移轉叢集上永遠 Multisite 到進階版儲存體

本主題的其餘部分會提供詳細的範例將叢集最多網站轉換成付費儲存空間。 也將接聽程式，使用外部負載平衡器 (ELB) 內部負載平衡器 (ILB)。

### <a name="environment"></a>環境

- Windows 2 k 12 / SQL 2 k 12
- 在 [預存程序 1 DB 檔案
- 每個節點的儲存集區 x 2

![Appendix1][11]

### <a name="vm"></a>VM:

在此範例中，我們會示範從 ELB 移至 ILB。 這會顯示如何在移轉期間切換到此之前 ILB，可使用 ELB。

![Appendix2][12]

### <a name="pre-steps-connect-to-subscription"></a>連線至訂閱的重要步驟︰

    Add-AzureAccount

    #Set up subscription
    Get-AzureSubscription

#### <a name="step-1-create-new-storage-account-and-cloud-service"></a>步驟 1︰ 建立新的儲存空間帳戶與雲端服務
    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Storage accounts
    #current storage account where the vm to migrate resides
    $origstorageaccountname = "danstdams"

    #Create Premium Storage account
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

    #Generate storage keys for later
    $originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    #Generate storage acc contexts
    $origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
    $xioContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  

    #Set up subscription and default storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $origstorageaccountname
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

    #CREATE NEW CLOUD SVC
    $vnet = "dansvnetwesteur"

    ##Existing cloud service
    $sourceSvc="dansolSrcAms"

    ##Create new cloud service
    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location

#### <a name="step-2-increase-the-permitted-failures-on-resources-optional"></a>步驟 2︰ 增加資源允許的失敗<Optional>
某些屬於您永遠在可用性] 群組中的資源限制上有多少發生的期間，叢集服務將會嘗試重新啟動 [資源] 群組中的失敗次數。 建議您將它增加而您會說明此程序之後如果您沒有手動錯誤移轉及觸發程序的容錯移轉來關閉，就可以關閉此限制的電腦。

應最好按兩下失敗金額，執行此動作的容錯移轉叢集管理員中，移至 [永遠在資源群組的屬性︰

![Appendix3][13]

變更最大失敗到 6。

#### <a name="step-3-addition-ip-address-resource-for-cluster-group-optional"></a>步驟 3︰ 新增 IP 位址資源叢集群組<Optional>

如果您有一個 IP 位址叢集群組以及此對齊到雲端的子網路，請注意，如果您不小心離線所有叢集節點在雲端，然後叢集 IP 資源的網路上並叢集網路名稱不能連線。 發生此它會防止其他叢集資源的更新。

#### <a name="step-4-dns-configuration"></a>步驟 4: DNS 設定

若要實作平滑轉場效果取決於 DNS 的正在使用及更新。
一律在安裝時，它會建立 Windows 叢集資源] 群組中，如果您開啟容錯移轉叢集管理員，您會看到的最小，會有三個資源，兩個文件是指的是︰

- 這是虛擬網路名稱 (VNN) – DNS 名稱的用戶端連線至時想要連線至 SQL Server，透過永遠。
- 這是 VNN 相關聯的 IP 位址的 IP 位址資源 – 您可以有多個與多站台設定，您會有每個網站/子網路 IP 位址。

連線至 SQL Server，SQL Server 用戶端驅動程式會擷取接聽相關聯的 DNS 記錄，並嘗試連線到每個一直在相關聯的 IP 位址，當下方我們討論可能會影響此的因素。

數字的同時接聽名稱與相關聯的 DNS 記錄不只而定的 IP 位址相關聯，數，但 ' RegisterAllIpProviders'setting 中容錯永遠開啟 VNN 資源。

當您部署永遠 Azure 中不同的步驟來建立接聽和 IP 位址，您必須以手動方式設定 'RegisterAllIpProviders' 1，這是上的部署一直在部署至不同位置將其已設為 1。

如果 'RegisterAllIpProviders' 是 0，則只會看到一個與接聽相關聯的 DNS 中的 DNS 記錄︰

![Appendix4][14]

如果 「 RegisterAllIpProviders' 1:

![Appendix5][15]

下列程式碼會傾印出的 VNN 設定為您，請注意，接聽離線造成此記錄，變更才會的生效，您需要讓 VNN 離線，並將其重新連線，用戶端連線中斷。

    ##Always On Listener Name
    $ListenerName = "Mylistener"
    ##Get AlwaysOn Network Name Settings
    Get-ClusterResource $ListenerName| Get-ClusterParameter
    ##Set RegisterAllProvidersIP
    Get-ClusterResource $ListenerName| Set-ClusterParameter RegisterAllProvidersIP  1

在您需要更新 IP 位址會參照的負載平衡器，以更新永遠上接聽之後的移轉步驟涉及的 IP 位址移除資源和元件。 IP 更新之後您需要確認新的 IP 位址已更新 DNS 區域中，用戶端要更新其本機 DNS 快取。

如果您的用戶端位於不同的網路區段，並且參照不同的 DNS 伺服器，您需要考慮時會發生什麼事瞭解 DNS 區域傳送移轉中，為應用程式重新連線時間會受限於至少區域傳送時間的任何新的 IP 位址接聽程式。 如果您是在時間限制式，您應該相互討論並測試強制與您的 Windows 小組增量區域轉送及也保留 DNS 主機記錄至較低的時間 (TTL)，請更新，因此用戶端。 如需詳細資訊，請參閱[累加區域轉送](https://technet.microsoft.com/library/cc958973.aspx)和[開始 DnsServerZoneTransfer](https://technet.microsoft.com/library/jj649917.aspx)。

依預設在一直在 Azure 中接聽相關聯的 DNS 記錄的 TTL 是 1200 秒。 若要減少如果您是在 [限制式，以確保用戶端移轉期間的更新其 DNS 更新的 IP 位址的接聽的時間。 檢視及修改設定，藉由傾印出的 VNN 設定︰

    $AGName = "myProductionAG"
    $ListenerName = "Mylistener"
    #Look at HostRecordTTL
    Get-ClusterResource $ListenerName| Get-ClusterParameter

    #Set HostRecordTTL Examples
    Get-ClusterResource $ListenerName| Set-ClusterParameter -Name "HostRecordTTL" 120

請注意，較低 'HostRecordTTL'，會發生 DNS 流量較高的數量。

##### <a name="client-application-settings"></a>用戶端應用程式設定

如果您的 SQL 用戶端應用程式支援的.Net 4.5 SQLClient，然後您可以使用 「 MULTISUBNETFAILOVER = true，則 「 關鍵字，建議要為其允許更快速地連線至 SQL 永遠在可用性] 群組中，在容錯移轉套用。 它列舉所有平行，永遠上接聽相關聯的 IP 位址，並執行更積極的 TCP 連線重試速度故障時。

如需有關上述設定的詳細資訊，請參閱[MultiSubnetFailover 關鍵字和相關聯的功能](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover)。 請參閱[高的顯示狀態，損毀修復 SqlClient 支援](https://msdn.microsoft.com/library/hh205662(v=vs.110).aspx)。

#### <a name="step-5-cluster-quorum-settings"></a>步驟 5︰ 叢集仲裁設定

當您要花出至少有一個 SQL Server，向下的一次，如果使用的檔案共用見證 (FSW) 與 2 節點，您應該修改叢集仲裁設定，您應該將允許節點多數，並使用動態投票，仲裁，這是以單一節點維持站允許。


    Set-ClusterQuorum -NodeMajority  

如需有關管理及設定叢集仲裁的詳細資訊，請參閱[設定及管理 Windows Server 2012 容錯移轉叢集仲裁](https://technet.microsoft.com/library/jj612870.aspx)。

#### <a name="step-6-extract-existing-endpoints-and-acls"></a>步驟 6︰ 擷取現有的結束點及 Acl
    #GET Endpoint info
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureEndpoint
    #GET ACL Rules for Each EP, this example is for the Always On Endpoint
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureAclConfig -EndpointName "myAOEndPoint-LB"  

儲存這些內容至文字檔案。

#### <a name="step-7-change-failover-partners-and-replication-modes"></a>步驟 7︰ 變更容錯移轉合作夥伴與複寫模式

如果您有 2 個以上的 SQL Server，您應該另一個次要的另一個 dc 或內部部署的容錯移轉變更為 ['同步 」，並使其自動容錯移轉合作夥伴 (AFP)，這是您維護 HA，而您進行變更。 您可以透過 TSQL 的修改透過 SSMS:

![Appendix6][16]

#### <a name="step-8-remove-secondary-vm-from-cloud-service"></a>步驟 8︰ 雲端服務中移除次要 VM

您應該規劃移轉雲端的第二個節點首先，如果這是目前主索引，您應該會開始手動容錯移轉。

    $vmNameToMigrate="dansqlams2"

    #Check machine status
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

    #Shutdown secondary VM
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM


    #Extract disk configuration

    ##Building Existing Data Disk Configuration
    $file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
    $datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
    Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
    foreach ($disk in $datadisks)
    {
      $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
      $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
    }

    #Get OS Disk
    $osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
    $osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
    $osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
    $addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
    $addosdisk | add-content -path $file

    #Import disk config
    $diskobjects  = Import-CSV $file

    #Check disk config, make sure below returns the disks associated with the VM
    $diskobjects

    #Identify OS Disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osdiskforbuild = $osdiskimport.diskName

    #Check machibe is off
    Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

    #Drop machine and rebuild to new cls
    Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#### <a name="step-9-change-disk-caching-settings-in-csv-file-and-save"></a>步驟 9︰ 磁碟快取設定 CSV 檔案中的變更及儲存

資料區這些應該設為唯讀。

記錄區這些應該設定為 [無]。

![Appendix7][17]

#### <a name="step-10-copy-vhds"></a>步驟 10︰ 複製 VHD
    #Ensure you have created the container for these:
    $containerName = 'vhds'

    #Create container
    New-AzureStorageContainer -Name $containerName -Context $xioContext

    ####DISK COPYING####
    #Get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName
       Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

       #Start async copy
       Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname.blob.core.windows.net/vhds/$vhdname" `
    -SrcContext $origContext `
    -DestContainer $containerName `
    -DestBlob $vhdname `
    -DestContext $xioContext
       }



您可以檢查進階版儲存帳戶 Vhd 複製的狀態︰

    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName

       $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContext
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
       }

![Appendix8][18]

全部這些記錄成功等到。

個別的二進位大型物件的資訊︰

    Get-AzureStorageBlobCopyState -Blob "blobname.vhd" -Container $containerName -Context $xioContext

#### <a name="step-11-register-os-disk"></a>步驟 11: Register OS 磁碟

    #Change storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountname
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

    #Register OS disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osvhd = $osdiskimport.vhdname
    $osdiskforbuild = $osdiskimport.diskName

    #Registering OS disk, but as XIO disk
    $xioDiskName = $osdiskforbuild + "xio"
    Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"

#### <a name="step-12-import-secondary-into-new-cloud-service"></a>步驟 12︰ 匯入第二個新的雲端服務

下列程式碼也會使用 [新增] 選項在這裡您可以匯入電腦，並使用 retainable VIP。

    #Build VM Config
    $ipaddr = "192.168.0.5"
    #Remember to change to XIO
    $newInstanceSize = "Standard_DS13"
    $subnet = "SQL"

    #Create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS"

    #build machine config into object
    $vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    #Reload disk config
    $diskobjects  = Import-CSV $file
    $datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

    ForEach ( $attachdatadisk in $datadiskimport)
       {
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###Attaching disks to a VM during a deploy to a new cloud service and new storage account is different from just attaching VHDs to just a redeploy in a new cloud service
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

    }

    #Create VM
    $vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)

#### <a name="step-13-create-ilb-on-new-cloud-svc-add-load-balanced-endpoints-and-acls"></a>步驟 13: ILB 上建立新的雲端服務，新增負載平衡端點和 Acl
    #Check for existing ILB
    GET-AzureInternalLoadBalancer -ServiceName $destcloudsvc

    $ilb="sqlIntIlbDest"
    $subnet = "SQL"
    $IP="192.168.0.25"
    Add-AzureInternalLoadBalancer -ServiceName $destcloudsvc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP

    #Endpoints
    $epname="sqlIntEP"
    $prot="tcp"
    $locport=1433
    $pubport=1433
    Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM

    #SET Azure ACLs or Network Security Groups & Windows FWs

    #http://msdn.microsoft.com/library/azure/dn495192.aspx

    ####WAIT FOR FULL AlwaysOn RESYNCRONISATION!!!!!!!!!#####

####<a name="step-14-update-always-on"></a>在隨時更新步驟 14:
    #Code to be executed on a Cluster Node
    $ClusterNetworkNameAmsterdam = "Cluster Network 2" # the azure cluster subnet network name
    $newCloudServiceIPAmsterdam = "192.168.0.25" # IP address of your cloud service

    $AGName = "myProductionAG"
    $ListenerName = "Mylistener"


    Add-ClusterResource "IP Address $newCloudServiceIPAmsterdam" -ResourceType "IP Address" -Group $AGName -ErrorAction Stop |  Set-ClusterParameter -Multiple @{"Address"="$newCloudServiceIPAmsterdam";"ProbePort"="59999";SubnetMask="255.255.255.255";"Network"=$ClusterNetworkNameAmsterdam;"OverrideAddressMatch"=1;"EnableDhcp"=0} -ErrorAction Stop

    #set dependancy and NETBIOS, then remove old IP address

    #set NETBIOS, then remove old IP address
    Get-ClusterGroup $AGName | Get-ClusterResource -Name "IP Address $newCloudServiceIPAmsterdam" | Set-ClusterParameter -Name EnableNetBIOS -Value 0

    #set dependency to Listener (OR Dependency) and delete previous IP Address resource that references:

    #Make sure no static records in DNS

![Appendix9][19]

現在移除舊的雲端服務的 IP 位址。

![Appendix10][20]

#### <a name="step-15-dns-update-check"></a>步驟 15: DNS 更新核取

您現在應該查看您 SQL Server 的用戶端網路 DNS 伺服器，並確定叢集已新增額外的主機記錄新增的 IP 位址。 如果這些 DNS 伺服器有不會更新，考慮強制 DNS 區域傳輸，並確定中的用戶端那里子網路就能解決兩個永遠上的 IP 位址，這是，因此您不需要等候自動 DNS 複寫。

#### <a name="step-16-reconfigure-always-on"></a>步驟 16︰ 一律重新設定上

此時您等待次要完全重新同步處理內部部署節點並切換到同步複寫節點，並讓 AFP 已移轉的節點。  

#### <a name="step-17-migrate-second-node"></a>步驟 17︰ 移轉第二個節點
    $vmNameToMigrate="dansqlams1"

    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

    #Get endpoint information
    $endpoint = Get-AzureVM -ServiceName $sourceSvc  -Name $vmNameToMigrate | Get-AzureEndpoint

    #Shutdown VM
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM

    #Get disk config

    #Building Existing Data Disk Configuration
    $file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
    $datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
    Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
    foreach ($disk in $datadisks)
    {
      $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
      $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
    }

    #Get OS Disk
    $osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
    $osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
    $osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
    $addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
    $addosdisk | add-content -path $file

    #Import disk config
    $diskobjects  = Import-CSV $file

    #Check disk configuration
    $diskobjects

    #Identify OS Disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osdiskforbuild = $osdiskimport.diskName

    #Check machine is off
    Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

    #Drop machine and rebuild to new cls
    Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#### <a name="step-18-change-disk-caching-settings-in-csv-file-and-save"></a>步驟 18︰ 磁碟快取設定 CSV 檔案中的變更及儲存

資料區這些應該設為唯讀。

記錄區這些應該設定為 [無]。

![Appendix11][21]

#### <a name="step-19-create-new-independent-storage-account-for-secondary-node"></a>步驟 19︰ 建立第二個節點的新獨立的儲存空間帳戶
    $newxiostorageaccountnamenode2 = "danspremsams2"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountnamenode2 -Location $location -Type "Premium_LRS"  

    #Reset the storage account src if node 1 in a different storage account
    $origstorageaccountname2nd = "danstdams2"

    #Generate storage keys for later
    $xiostoragenode2 = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountnamenode2

    #Generate storage acc contexts
    $xioContextnode2 = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountnamenode2 -StorageAccountKey $xiostoragenode2.Primary  

    #Set up subscription and default storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

#### <a name="step-20-copy-vhds"></a>步驟 20︰ 複製 VHD
    #Ensure you have created the container for these:
    $containerName = 'vhds'

    #Create container
    New-AzureStorageContainer -Name $containerName -Context $xioContextnode2  

    ####DISK COPYING####
    ##get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName
       Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

       #Start async copy
       Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname2nd.blob.core.windows.net/vhds/$vhdname" `
        -SrcContext $origContext `
        -DestContainer $containerName `
        -DestBlob $vhdname `
        -DestContext $xioContextnode2
       }

    #Check for copy progress

    #check induvidual blob status
    Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContext


您可以檢查所有 Vhd VHD 複製狀態︰ ForEach ($disk 中 $diskobjects) {$lun = $disk。Lun $vhdname = $disk.vhdname $cacheoption = $disk。HostCaching $disklabel = $disk。DiskLabel $diskName = $disk。DiskName

       $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContextnode2
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
       }

![Appendix12][22]

全部這些記錄成功等到。

個別的二進位大型物件的資訊︰ #Check induvidual blob 狀態取得 AzureStorageBlobCopyState-Blob 」 danRegSvcAms-dansqlams1-2014年-07-03.vhd 」-容器 $containerName-$xioContextnode2 的內容

#### <a name="step-21-register-os-disk"></a>步驟 21: Register OS 磁碟
    #change storage account to the new XIO storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

    #Register OS disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osvhd = $osdiskimport.vhdname
    $osdiskforbuild = $osdiskimport.diskName

    #Registering OS disk, but as XIO disk
    $xioDiskName = $osdiskforbuild + "xio"
    Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"

    #Build VM Config
    $ipaddr = "192.168.0.4"
    $newInstanceSize = "Standard_DS13"

    #Join to existing Avaiability Set

    #Build machine config into object
    $vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    #Reload disk config
    $diskobjects  = Import-CSV $file
    $datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

    ForEach ( $attachdatadisk in $datadiskimport)
       {
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###This is different to just a straight cloud service change
    #note if you do not have a disk label the command below will fail, populate as required.
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

    }

    #Create VM
    $vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet -Verbose

#### <a name="step-22-add-load-balanced-endpoints-and-acls"></a>步驟 22︰ 新增負載平衡端點和 Acl
    #Endpoints
    $epname="sqlIntEP"
    $prot="tcp"
    $locport=1433
    $pubport=1433
    Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM


    #STOP!!! CHECK in the Azure classic portal or Machine Endpoints through powershell that these Endpoints are created!

    #SET ACLs or Azure Network Security Groups & Windows FWs

    #http://msdn.microsoft.com/library/azure/dn495192.aspx

#### <a name="step-23-test-failover"></a>步驟 23︰ 測試容錯移轉

您現在應該讓移轉的節點節點一直在內部部署與同步處理、 放在要同步的複寫模式和等到其同步處理。 然後從內部部署的第一個節點的容錯移轉移轉，這是 AFP。 之後，完成之後，變更 AFP 的最後一個移轉的節點。

您應該測試所有的節點的容錯移轉，並執行雖然混亂測試，以確保容錯移轉工作為預期和及時莊園。

#### <a name="step-24-put-back-cluster-quorum-settings--dns-ttl--failover-pntrs--sync-settings"></a>步驟 24︰ 放回叢集仲裁設定 / DNS TTL / 容錯移轉 Pntrs / 同步處理設定
##### <a name="adding-ip-address-resource-on-same-subnet"></a>在相同的子網路上新增 IP 位址資源

如果您只是 2 的 SQL Server 和要移轉至新的雲端服務，但想要將相同的子網路上，您可以不必接聽離線，刪除原始永遠在 IP 位址，並新增新的 IP 位址。 如果您要 Vm 移轉至另一個子網路您不需要這麼做，如可將參考該子其他叢集網路。

當您啟動移轉次要並新增新的 IP 位址資源的容錯移轉現有的主要之前新增的雲端服務中時，您應該採取以下步驟內叢集容錯移轉管理員︰

若要在 [IP 位址，請參閱[附錄](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage)，步驟 14。

1. 針對目前的 IP 位址資源，變更可能擁有者 」 現有主要 SQL Server]，在下面的範例，「 dansqlams4 」:

    ![Appendix13][23]

1. 新的 IP 位址資源，變更可能 ' Migrated 次要 SQL Server]，在下面的範例，「 dansqlams5 「 擁有者︰

    ![Appendix14][24]

1. 此設定後可以容錯移轉，並在移轉的最後一個節點時，該節點會加總為可能的擁有者，可能的擁有者應加以編輯︰

    ![Appendix15][25]

## <a name="additional-resources"></a>其他資源
- [Azure 進階版儲存空間](../storage/storage-premium-storage.md)
- [虛擬機器](https://azure.microsoft.com/services/virtual-machines/)
- [Azure 虛擬機器中的 SQL Server](virtual-machines-windows-sql-server-iaas-overview.md)

<!-- IMAGES -->
[1]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/1_VNET_Portal.png
[2]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/2_Diskname_Lun.png
[3]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/3_Virtual_Disk_Properties.png
[4]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/4_Virtual_Disk_Properties_Details.png
[5]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/5_Get_Storage_Pool.png
[6]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/6_Deployments_Always_On.png
[7]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/7_Add_More_Secondaries.png
[8]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/8_Use_Existing_Secondary_Single_Site.png
[9]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site.png
[10]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site_b.png
[11]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_01.png
[12]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_02.png
[13]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_03.png
[14]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_04.png
[15]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_05.png
[16]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_06.png
[17]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_07.png
[18]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_08.png
[19]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_09.png
[20]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_10.png
[21]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_11.png
[22]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_12.png
[23]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_13.png
[24]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_14.png
[25]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_15.png
