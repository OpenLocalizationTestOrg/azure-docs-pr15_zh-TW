<properties
    pageTitle="擷取一般化 Azure VM VM 圖像 |Microsoft Azure"
    description="瞭解如何擷取從一般化 Azure VM 資源管理員部署模型中建立的 VM 圖像"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="cynthn"/>

# <a name="how-to-capture-a-vm-image-from-a-generalized-azure-vm"></a>如何擷取一般化 Azure VM VM 圖像


本文將示範如何使用 PowerShell 的 Azure 建立一般化 Azure VM 的圖像。 圖像然後可用來建立另一個 VM。 圖像包含 OS 磁碟及附加至虛擬機器資料磁碟。 圖像，因此您需要設定這些資源，當您建立新的 VM 時，不包括虛擬網路資源。 


## <a name="prerequisites"></a>必要條件

- 您必須已經[一般化 VM](virtual-machines-windows-generalize-vhd.md)。 一般化 VM 移除所有的個人帳戶資訊，除此之外，並準備要做為圖像的電腦。

- 您必須具備 PowerShell 的 Azure 版本 1.0.x 或較新的安裝。 如果您還沒有安裝 PowerShell，請閱讀[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)安裝步驟。


## <a name="log-in-to-azure-powershell"></a>登入 Azure PowerShell

1. 開啟 Azure PowerShell，並登入您的 Azure 帳戶。

    ```powershell
    Login-AzureRmAccount
    ```

    在快顯視窗隨即會開啟，以輸入您的 Azure 帳戶認證。

2. 訂閱識別碼取得您可用的訂閱。

    ```powershell
    Get-AzureRmSubscription
    ```

3. 設定正確訂閱使用訂閱識別碼。

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>解除 VM 並設定通用的狀態       

1. 解除 VM 資源。

    ```powershell
    Stop-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```

    **停止 （解除配置）**從**停止**變更*狀態*vm Azure 入口網站中。

2. 將**Generalized**虛擬機器的狀態。 

    ```powershell
    Set-AzureRmVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```

3. 檢查 VM 狀態。 **一般化**OSState/VM 區段應該具有設定為 [**通用 VM** **DisplayStatus** 。  

    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>建立圖像 

1. 虛擬機器圖像複製到目的地存放容器，使用此命令。 圖像會建立在同一個儲存帳戶與原始的虛擬機器中。 `-Path`參數會儲存在本機 JSON 範本的複本。 `-DestinationContainerName`參數是您想要保留圖像容器的名稱。 如果容器不存在，則會建立適合您。

    ```powershell
    Save-AzureRmVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
        -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
        -Path <C:\local\Filepath\Filename.json>
    ```

    您可以從 JSON 檔案範本，以取得您圖像的 URL。 移至 [**資源** > **storageProfile** > **osDisk** > **圖像** > **uri**一節的圖像的完整路徑。 圖像的 URL 看起來像︰ `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`。
    
    您也可以驗證在入口網站 URI。 圖像複製到名稱為您儲存的帳戶中的 [**系統**容器。 


## <a name="next-steps"></a>後續步驟

- 現在您可以[建立從影像 VM](virtual-machines-windows-create-vm-generalized.md)。

