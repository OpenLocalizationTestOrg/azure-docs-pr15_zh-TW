<properties
    pageTitle="建立 SQL Server 虛擬機器中 Azure PowerShell （傳統） |Microsoft Azure"
    description="提供步驟與 PowerShell 指令碼建立 Azure VM 與 SQL Server 虛擬機器圖庫的圖像。 本主題會使用傳統的部署模式。"
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/07/2016"
    ms.author="jroth" />

# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-classic"></a>佈建使用 PowerShell 的 Azure （傳統） 的 SQL Server 虛擬機器

## <a name="overview"></a>概觀

本文提供步驟說明如何建立 SQL Server 虛擬機器 Azure 中，使用 PowerShell cmdlet。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]本主題的資源管理員版本，請參閱[佈建 SQL Server 虛擬機器使用 Azure PowerShell 資源管理員](virtual-machines-windows-ps-sql-create.md)。

### <a name="install-and-configure-powershell"></a>安裝及設定 PowerShell:

1. 如果您沒有 Azure 帳戶，請造訪[Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/)。

2. [下載並安裝最新的 PowerShell 的 Azure 命令](../powershell-install-configure.md)。

3. 啟動 Windows PowerShell，並將其連線至您 Azure 的訂閱，使用 [**新增 AzureAccount** ] 命令。

        Add-AzureAccount

## <a name="determine-your-target-azure-region"></a>判斷您的目標 Azure 區域

SQL Server 虛擬機器會裝載雲端服務位於特定 Azure 區域中。 下列步驟協助您判斷您的地區、 儲存帳戶和雲端服務，就會使用教學課程的其餘部分。

1. 決定您要用來主控您的 SQL Server VM 資料中心。 下列的 PowerShell 命令會顯示可用的區域的摘要清單結尾的詳細資料。

        Get-AzureLocation
        (Get-AzureLocation).Name

2.  一旦您已識別您想要的位置，設定一個名為**$dcLocation**到該區域的變數。

        $dcLocation = "<region name>"

## <a name="set-your-subscription-and-storage-account"></a>設定您的訂閱，儲存的帳戶

1. 決定您可以使用的新的虛擬機器 Azure 訂閱。

        (Get-AzureSubscription).SubscriptionName

1. 將您的目標 Azure 訂閱指派給**$subscr**變數中。 然後將此設定為目前 Azure 訂閱。

        $subscr="<subscription name>"
        Select-AzureSubscription -SubscriptionName $subscr –Current

1. 然後檢查現有的儲存空間帳戶]。 下列指令碼會顯示您所選的區域中存在的所有儲存帳戶︰

        (Get-AzureStorageAccount | where { $_.GeoPrimaryLocation -eq $dcLocation }).StorageAccountName

    >[AZURE.NOTE] 如果您需要新的儲存空間帳戶時，第一次全部小寫儲存體帳戶名稱使用建立 [新增 AzureStorageAccount] 命令，如下列範例所示︰**新增 AzureStorageAccount StorageAccountName 」<storage account name>」 的位置 $dcLocation**

1. 將**$staccount**目標儲存體帳戶名稱。 若要設定的訂閱，目前儲存帳戶，然後使用**設定 AzureSubscription** 。

        $staccount="<storage account name>"
        Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

## <a name="select-a-sql-server-virtual-machine-image"></a>選取 SQL Server 虛擬機器圖像

1. 找出可用的 SQL Server 虛擬機器影像，從圖庫的清單。 所有這些圖像具有**ImageFamily**屬性的第一句是 「 SQL 」。 下列查詢會顯示您已預先安裝 SQL Server 可用的圖像系列。

        Get-AzureVMImage | where { $_.ImageFamily -like "SQL*" } | select ImageFamily -Unique | Sort-Object -Property ImageFamily

1. 當您找到的虛擬機器圖像系列時，這一系列中可能有多個已發佈的圖像。 尋找最新的已發佈的虛擬機器圖像名稱 （例如**SQL Server 2016 RTM 企業版的 Windows Server 2012 R2**） 選取的圖像家族使用下列指令碼︰

        $family="<ImageFamily value>"
        $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

        echo "Selected SQL Server image name:"
        echo "   $image"

## <a name="create-the-virtual-machine"></a>建立虛擬機器

最後，使用 PowerShell 建立虛擬機器︰

1. 建立裝載新 VM 雲端服務。 請注意，也可以改為使用現有的雲端服務。 建立新的變數**$svcname**雲端服務的簡短名稱。

        $svcname = "<cloud service name>"
        New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation

2. 指定的虛擬機器名稱與大小。 如需有關虛擬機器大小的詳細資訊，請參閱[Azure 虛擬機器大小](virtual-machines-windows-sizes.md)。

        $vmname="<machine name>"
        $vmsize="<Specify one: Large, ExtraLarge, A5, A6, A7, A8, A9, or see the link to the other VM sizes>"
        $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

3. 指定本機系統管理員帳戶與密碼。

        $cred=Get-Credential -Message "Type the name and password of the local administrator account."
        $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

4. 執行下列指令碼來建立虛擬機器。

        New-AzureVM –ServiceName $svcname -VMs $vm1

>[AZURE.NOTE] 如需其他說明與設定選項，請參閱[使用 Azure PowerShell 來建立及預先設定 Windows 型虛擬機器](virtual-machines-windows-classic-create-powershell.md)中的 [**建立您的命令集**] 區段。

## <a name="example-powershell-script"></a>範例 PowerShell 指令碼

提供下列指令碼，並完成的指令碼範例的建立**SQL Server 2016 RTM 企業版的 Windows Server 2012 R2**虛擬機器。 如果您使用這個指令碼，您必須自訂初始變數根據本主題中的上一個步驟。

    # Customize these variables based on your settings and requirements:
    $dcLocation = "East US"
    $subscr="mysubscription"
    $staccount="mystorageaccount"
    $family="SQL Server 2016 RTM Enterprise on Windows Server 2012 R2"
    $svcname = "mycloudservice"
    $vmname="myvirtualmachine"
    $vmsize="A5"

    # Set the current subscription and storage account
    # Comment out the New-AzureStorageAccount line if the account already exists
    Select-AzureSubscription -SubscriptionName $subscr –Current
    New-AzureStorageAccount -StorageAccountName $staccount -Location $dcLocation
    Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

    # Select the most recent VM image in this image family:
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

    # Create the new cloud service; comment out this line if cloud service exists already:
    New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation

    # Create the VM config:
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    # Set administrator credentials:
    $cred=Get-Credential -Message "Type the name and password of the local administrator account."
    $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

    # Create the SQL Server VM:
    New-AzureVM –ServiceName $svcname -VMs $vm1


## <a name="connect-with-remote-desktop"></a>使用遠端桌面連線

1. 建立。RDP 目前使用者的文件資料夾，以啟動以完成安裝這些虛擬機器中的檔案︰

        $documentspath = [environment]::getfolderpath("mydocuments")
        Get-AzureRemoteDesktopFile -ServiceName $svcname -Name $vmname -LocalPath "$documentspath\vm1.rdp"

1. 在文件的目錄，啟動 RDP 檔案。 與管理員的使用者名稱和密碼提供更舊版本連線 （例如，如果您的使用者名稱是 VMAdmin，指定 「 \VMAdmin 」 為使用者，並提供密碼）。

        cd $documentspath
        .\vm1.rdp

## <a name="complete-the-configuration-of-the-sql-server-machine-for-remote-access"></a>完成的 SQL Server 電腦遠端存取的設定

登入電腦與遠端桌面，設定後 SQL Server 會根據中[Azure VM 中的 SQL Server 連線的設定步驟](virtual-machines-windows-classic-sql-connect.md#steps-for-configuring-sql-server-connectivity-in-an-azure-vm)的指示進行。

## <a name="next-steps"></a>後續步驟

您可以找到提供使用 PowerShell 的虛擬機器[虛擬機器文件](virtual-machines-windows-classic-create-powershell.md)中的其他指示。 SQL Server 和進階版儲存相關的其他指令碼，請參閱[使用 Azure 進階版儲存虛擬機器上的 SQL server](virtual-machines-windows-classic-sql-server-premium-storage.md)。

在許多情況下下, 一步是此新的 SQL Server vm 移轉您的資料庫。 資料庫移轉，請參閱[移轉 Azure VM 上 SQL Server 資料庫](virtual-machines-windows-migrate-sql.md)。

如果您也想要建立 SQL 虛擬機器中使用 Azure 入口網站，請參閱[佈建 Azure SQL Server 虛擬機器](virtual-machines-windows-portal-sql-server-provision.md)。 請注意，逐步引導您進行入口網站的教學課程建立 Vm 使用建議的資源管理員模型，而不是使用 PowerShell 主題中的傳統模型。

除了這些資源，我們建議您檢閱[執行 SQL Server Azure 虛擬機器中的商務連絡人的相關的其他主題](virtual-machines-windows-sql-server-iaas-overview.md)。
