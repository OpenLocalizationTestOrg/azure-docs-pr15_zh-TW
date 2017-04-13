<properties
   pageTitle="Azure 混合式使用優勢視窗伺服器 |Microsoft Azure"
   description="瞭解如何將您的 Windows Server 軟體保證權益，若要將內部部署授權 Azure 最大化"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="07/13/2016"
   ms.author="georgem"/>

# <a name="azure-hybrid-use-benefit-for-windows-server"></a>Windows Server 的 azure 混合式使用優勢

使用軟體保證的 Windows Server 的客戶，您可以將您的內部部署 Windows Server 授權 Azure，Windows Server Vm 中 Azure 降低成本。 Azure 混合式使用優勢可讓您執行 Windows Server Vm Azure 中及只取得付款的底數計算工資率。 如需詳細資訊，請參閱[Azure 混合式使用優勢授權] 頁面](https://azure.microsoft.com/pricing/hybrid-use-benefit/)。 本文說明如何使用此項優惠，授權 Azure 中的 Windows Server Vm 」 部署。

> [AZURE.NOTE] 您無法使用 Azure Marketplace 圖像部署 Windows Server Vm 利用 Azure 混合式使用優點。 您必須部署您 Vm 正確基底計算工資率折扣可註冊您的 Vm 使用 PowerShell 或資源管理員的範本。

## <a name="pre-requisites"></a>必要條件
若要使用 Azure 混合式使用的優勢的 Windows Server Vm Azure 中有幾個必要條件︰

- 安裝 Azure PowerShell 模組
- 您的 Windows Server VHD 上傳到 Azure 儲存體嗎

### <a name="install-azure-powershell"></a>安裝 Azure PowerShell
請確定您有[安裝並設定最新 PowerShell 的 Azure](../powershell-install-configure.md)。 即使您打算在部署您使用的資源管理員範本的 Vm，您仍然需要上傳您的 Windows Server VHD 安裝 PowerShell 的 Azure （請參閱下列步驟）。

### <a name="upload-a-windows-server-vhd"></a>上傳的 Windows Server VHD

若要部署 Windows Server VM Azure 中，您必須建立包含您基底的 Windows Server 建立一個 VHD。 上傳至 Azure 之前，此 VHD，必須適當準備透過 Sysprep。 您可以[進一步瞭解 VHD 需求與 Sysprep 程序](./virtual-machines-windows-upload-image.md)和[Sysprep 支援的伺服器角色](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)。 執行 Sysprep 之前，先備份 VM。 當您準備好您 VHD 時上, 傳 VHD 您 Azure 儲存體帳戶使用`Add-AzureRmVhd`cmdlet，如下所示︰

```
Add-AzureRmVhd -ResourceGroupName MyResourceGroup -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd" -LocalFilePath 'C:\Path\To\myvhd.vhd'
```

> [AZURE.NOTE] Microsoft SQL Server 與 SharePoint Server Dynamics 也可以利用您的軟體保證授權。 您仍需要準備安裝應用程式，並提供授權產品金鑰，因此，然後上傳到 Azure 的磁碟映像的 Windows Server 圖像。 檢閱您的應用程式，例如[安裝 SQL Server 的考量使用 Sysprep](https://msdn.microsoft.com/library/ee210754.aspx)或[建立 SharePoint Server 2016 參照圖像 (Sysprep)](http://social.technet.microsoft.com/wiki/contents/articles/33789.build-a-sharepoint-server-2016-reference-image-sysprep.aspx)執行 Sysprep 的相關文件。

您也可以閱讀更多關於[上傳至 Azure 程序 VHD](./virtual-machines-windows-upload-image.md#upload-the-vm-image-to-your-storage-account)。

> [AZURE.TIP] 本文著重於部署 Windows Server Vm。 您也可以將 Windows 用戶端 Vm 部署相同的方式。 在下列範例中，您將`Server`與`Client`適當。

## <a name="deploy-a-vm-via-powershell-quick-start"></a>部署 VM 透過 PowerShell 快速開始
在部署您透過 PowerShell 的 Windows Server VM 時，您有其他參數的`-LicenseType`。 上傳至 Azure 您 VHD 之後，您就會建立新的 VM 使用`New-AzureRmVM`，並指定授權類型，如下所示︰

```
New-AzureRmVM -ResourceGroupName MyResourceGroup -Location "West US" -VM $vm -LicenseType Windows_Server
```

您可以[閱讀部署 VM 透過 PowerShell 的 Azure 中的詳細逐步解說](./virtual-machines-windows-hybrid-use-benefit-licensing.md#deploy-windows-server-vm-via-powershell-detailed-walkthrough)，或閱讀更多描述性指南上建立[Windows VM 資源管理員和 PowerShell 使用](./virtual-machines-windows-ps-create.md)不同的步驟。

## <a name="deploy-a-vm-via-resource-manager"></a>部署 VM 透過資源管理員
資源管理員範本，其他參數中`licenseType`可以指定。 您可以閱讀更多關於[製作 Azure 資源管理員範本](../resource-group-authoring-templates.md)。 上傳至 Azure 您 VHD 之後，編輯您加入授權類型計算提供者，並部署您的範本，資源管理員範本︰

```
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   },
```
 
## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>請確認您 VM 正在使用的授權的好處
一旦您已經部署您 VM 透過 PowerShell 或資源管理員部署方法，請確認授權類型與`Get-AzureRmVM`，如下所示︰
 
```
Get-AzureRmVM -ResourceGroup MyResourceGroup -Name MyVM
```

輸出非常類似下列動作︰

```
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

這種下列 VM 部署不 Azure 混合式使用優勢授權，例如 VM 部署直接從 Azure] 庫︰

```
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : 
```
 
## <a name="detailed-powershell-walkthrough"></a>詳細的 PowerShell 逐步解說

下列詳細的 PowerShell 步驟顯示完整 VM 部署。 您可以瞭解實際的 cmdlet 和[建立 Windows VM 使用資源管理員和 PowerShell](./virtual-machines-windows-ps-create.md)中建立的不同元件的更多內容。 您逐步執行建立您的資源群組、 儲存帳戶和虛擬網路，然後定義您 VM 及最後建立您 VM。
 
首先，安全地取得認證，設定的位置及資源群組名稱︰

```
$cred = Get-Credential
$location = "West US"
$resourceGroupName = "TestLicensing"
```

建立的公用 IP:

```
$publicIPName = "testlicensingpublicip"
$publicIP = New-AzureRmPublicIpAddress -Name $publicIPName -ResourceGroupName $resourceGroupName -Location $location -AllocationMethod Dynamic
```

定義您子網路、 NIC，以及 VNET:

```
$subnetName = "testlicensingsubnet"
$nicName = "testlicensingnic"
$vnetName = "testlicensingvnet"
$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/8
$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName -Location $location -AddressPrefix 10.0.0.0/8 -Subnet $subnetconfig
$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $resourceGroupName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $publicIP.Id
```

您 VM 的名稱，並建立 VM 設定︰

```
$vmName = "testlicensing"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"
```

定義您的作業系統︰

```
$computerName = "testlicensing"
$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
```

新增您 NIC vm:

```
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

定義要使用的儲存空間帳戶︰

```
$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -AccountName testlicensing
```

上傳您 VHD，適當準備，然後附加您 vm 用於︰

```
$osDiskName = "licensing.vhd"
$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName
$urlOfUploadedImageVhd = "https://testlicensing.blob.core.windows.net/vhd/licensing.vhd"
$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption FromImage -SourceImageUri $urlOfUploadedImageVhd -Windows
```

最後，建立您 VM，並定義利用 Azure 混合式使用優勢授權類型︰

```
New-AzureRmVM -ResourceGroupName $resourceGroupName -Location $location -VM $vm -LicenseType Windows_Server
```

## <a name="next-steps"></a>後續步驟

進一步瞭解[Azure 混合式使用優勢授權](https://azure.microsoft.com/pricing/hybrid-use-benefit/)。

進一步瞭解[使用資源管理員範本](../azure-resource-manager/resource-group-overview.md)。
