<properties
    pageTitle="上傳 VHD 在 Windows 的 [資源管理員 |Microsoft Azure"
    description="瞭解如何上傳 Windows 虛擬機器 VHD 來自內部部署 Azure，使用資源管理員部署模型。 從 [通用 VHD 或特殊的 VM，您可以上傳。"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="cynthn"/>

# <a name="upload-a-windows-vhd-from-an-on-premises-vm-to-azure"></a>上傳的 Windows Azure 以從內部部署 VM VHD 


本文將示範如何建立和上傳 Windows 虛擬硬碟 (VHD) 若要在建立 Azure Vm。 您可以上傳一般化的 VM 或特殊的 VM VHD。 

如需磁碟和 Vhd Azure 中的詳細資訊，請參閱[關於磁碟及 Vhd 虛擬機器](virtual-machines-linux-about-disks-vhds.md)。


## <a name="prepare-the-vm"></a>準備 VM 

您可以將一般化及特殊 Vhd 上載至 Azure。 每個類型需要您開始之前，準備 VM。

- **一般化 VHD**一般化 VHD 已所有使用 Sysprep 移除您的個人帳戶資訊。 如果您想要做為圖像的 VHD，若要建立新的 Vm 從，您應該︰
    - [準備 Windows VHD 上傳至 Azure](virtual-machines-windows-prepare-for-upload-vhd-image.md)。 
    - [一般化虛擬機器使用 Sysprep](virtual-machines-windows-generalize-vhd.md)。 

- **特殊 VHD**特殊 VHD 維護的使用者帳戶與應用程式從您的原始 VM 其他狀態資料。 如果您想要使用為 VHD-可建立新的 VM，請確定完成下列步驟。 
    - [準備 Windows VHD 上傳至 Azure](virtual-machines-windows-prepare-for-upload-vhd-image.md)。 **請不要**一般化使用 Sysprep VM。
    - 移除任何來賓虛擬化工具和 VM （亦即 VMware 工具） 所安裝的代理程式。
    - 確定 VM 的設定來擷取其 IP 位址和透過 DHCP DNS 設定。 如此一來，可確保伺服器取得 IP 位址 VNet 中的，於啟動時。 

## <a name="log-in-to-azure"></a>登入 Azure

如果您還沒有 PowerShell 1.4 或已安裝，了解[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)上方的版本。

1. 開啟 Azure PowerShell，並登入您的 Azure 帳戶。 在快顯視窗隨即會開啟，以輸入您的 Azure 帳戶認證。

    ```powershell
    Login-AzureRmAccount
    ```


2. 訂閱識別碼取得您可用的訂閱。

    ```powershell
    Get-AzureRmSubscription
    ```

3. 設定正確訂閱使用訂閱識別碼。 取代`<subscriptionID>`的正確的訂閱的識別碼。

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="get-the-storage-account"></a>取得儲存帳戶

您必須儲存上傳的 VM 圖像 Azure 中的儲存空間帳戶。 您可以使用現有的儲存空間帳戶或建立新的範本。 

若要顯示的可用儲存帳戶，請輸入︰

```powershell
Get-AzureRmStorageAccount
```

如果您想要使用現有的儲存空間帳戶，請前往 [[上傳 VM 圖像](#upload-the-vm-vhd-to-your-storage-account)] 區段。

如果您需要建立儲存的帳戶，請遵循下列步驟︰

1. 您需要資源群組應會建立儲存體帳戶的名稱。 若要找出您的訂閱中的所有資源群組，請輸入︰

    ```powershell
    Get-AzureRmResourceGroup
    ```

若要建立名為**myResourceGroup** **西美國**地區的資源群組，請輸入︰

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. 建立名為**mystorageaccount**此資源] 群組中，使用[新增 AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx)指令程式來儲存帳戶︰

    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" -SkuName "Standard_LRS" -Kind "Storage"
    ```
            
    -SkuName 有效值為︰

    - **Standard_LRS** -本機多餘的儲存空間。 
    - **Standard_ZRS** -區域多餘的儲存空間。
    - **Standard_GRS** -地理多餘的儲存空間。 
    - **Standard_RAGRS** -讀取權限地理多餘的儲存空間。 
    - **Premium_LRS** -進階版本機多餘的儲存空間。 



## <a name="upload-the-vhd-to-your-storage-account"></a>將 VHD 上傳您儲存的帳戶

您可以使用 [[新增 AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx) cmdlet 來上傳至容器儲存帳戶中的圖像。 此範例中上傳的檔案**myVHD.vhd** `"C:\Users\Public\Documents\Virtual hard disks\"`儲存體帳戶名稱為**mystorageaccount** **myResourceGroup**的 [資源] 群組中。 檔案會放置在名為**mycontainer**容器，新的檔案名稱會**myUploadedVHD.vhd**。

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


如果成功，您會收到的回應，看起來就像這樣︰

```
  C:\> Add-AzureRmVhd -ResourceGroupName myResourceGroup -Destination https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
  MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
  MD5 hash calculation is completed.
  Elapsed time for the operation: 00:03:35
  Creating new page blob of size 53687091712...
  Elapsed time for upload: 01:12:49

  LocalFilePath           DestinationUri
  -------------           --------------
  C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

根據您的網路連線和 VHD 檔案的大小，這個命令可能需要一段時間才能完成


## <a name="next-steps"></a>後續步驟

- [在 [從一般化 VHD Azure 中建立 VM](virtual-machines-windows-create-vm-generalized.md)
- 當您建立新的 VM 附加為 OS 磁碟[建立從特殊 VHD Azure 中 VM](virtual-machines-windows-create-vm-specialized.md) 。


