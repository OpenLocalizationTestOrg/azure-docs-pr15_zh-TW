<properties
   pageTitle="使用 PowerShell 的 Azure 管理您的虛擬機器 |Microsoft Azure"
   description="瞭解您可以使用來管理您的虛擬機器中的工作自動化的命令。"
   services="virtual-machines-windows"
   documentationCenter="windows"
   authors="singhkays"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

   <tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/12/2016"
   ms.author="kasing"/>

# <a name="manage-your-virtual-machines-by-using-azure-powershell"></a>使用 PowerShell 的 Azure 管理您的虛擬機器

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


可以使用 Azure PowerShell cmdlet 來自動化許多您執行的動作來管理您的 Vm 每天的工作。 本文可讓您範例命令較簡單的工作和連結顯示更複雜的工作的命令的文章。

>[AZURE.NOTE] 如果您還沒有安裝和設定 PowerShell 的 Azure，您可以取得指示，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)。

## <a name="how-to-use-the-example-commands"></a>如何使用 [範例] 命令
您需要的部分文字命令中的取代為適用於您的環境的文字。 < 並 > 符號會指出您要取代的文字。 當您取代文字時，移除符號，但保留在引號中的位置。

## <a name="get-a-vm"></a>取得 VM
這是您會經常使用的基本工作。 用來取得 VM 的相關資訊、 執行工作上 VM，或取得輸出儲存在變數中。

若要取得 VM 的相關資訊，請執行此命令，取代所有項目中的報價，包括 < 及 > 字元︰

     Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

若要將輸出 $vm 變數中，執行︰

    $vm = Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="log-on-to-a-windows-based-vm"></a>登入 Windows 型 VM

執行下列命令︰

>[AZURE.NOTE] 您可以在顯示的 [**取得 AzureVM**命令中取得的虛擬機器和雲端服務的名稱。
>
    $svcName = "<cloud service name>"
    $vmName = "<virtual machine name>"
    $localPath = "<drive and folder location to store the downloaded RDP file, example: c:\temp >"
    $localFile = $localPath + "\" + $vmname + ".rdp"
    Get-AzureRemoteDesktopFile -ServiceName $svcName -Name $vmName -LocalPath $localFile -Launch

## <a name="stop-a-vm"></a>停止 VM

執行此命令時︰

    Stop-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

>[AZURE.IMPORTANT]若要保留虛擬 IP (VIP) 的雲端服務，以便在該雲端服務中的最後一個 VM 使用此參數。 <br><br> 如果您使用 StayProvisioned 參數時，會仍然付費 vm。

## <a name="start-a-vm"></a>啟動 VM

執行此命令時︰

    Start-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="attach-a-data-disk"></a>附加資料磁碟
此工作需要幾個步驟。 首先，您使用 * * * 新增-AzureDataDisk * * * 若要新增至 $vm 物件的磁碟指令程式。 然後，您可以使用**更新 AzureVM**指令程式來更新 VM 的設定。

您也需要決定是否要將新的磁碟或包含資料的其中一個附加。 新的磁碟] 命令會建立.vhd 檔案，並將它附加。

若要將新的磁碟的連結，執行此命令時︰

    Add-AzureDataDisk -CreateNew -DiskSizeInGB 128 -DiskLabel "<main>" -LUN <0> -VM $vm | Update-AzureVM

若要將現有的資料磁碟的連結，執行此命令時︰

    Add-AzureDataDisk -Import -DiskName "<MyExistingDisk>" -LUN <0> | Update-AzureVM

若要將資料磁碟附加從現有.vhd 檔案 blob 儲存體中，執行此命令時︰

    Add-AzureDataDisk -ImportFrom -MediaLocation `
              "<https://mystorage.blob.core.windows.net/mycontainer/MyExistingDisk.vhd>" `
              -DiskLabel "<main>" -LUN <0> |
              Update-AzureVM

## <a name="create-a-windows-based-vm"></a>建立 Windows 型 VM

若要建立新的 Windows 型虛擬機器 Azure 中，使用 [在[使用 Azure PowerShell 來建立及預先設定 Windows 型虛擬機器](virtual-machines-windows-classic-create-powershell.md)中的指示。 此主題步驟您建立的 PowerShell 的 Azure 設定的命令會建立 Windows 型 VM 可以預先設定的︰

- 使用 Active Directory 網域成員資格。
- 與其他磁碟。
- 為現有的負載平衡的成員設定。
- 使用靜態 IP 位址。
