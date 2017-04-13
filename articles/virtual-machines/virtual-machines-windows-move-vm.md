<properties
    pageTitle="移動視窗 VM |Microsoft Azure"
    description="將 Windows VM 移到另一個 Azure 訂閱] 或 [資源群組資源管理員部署模型中。"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="cynthn"/>

    


# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>將 Windows VM 移到另一個 Azure 訂閱] 或 [資源群組 

本文會引導您如何移動 Windows VM 之間資源群組或訂閱。 訂閱之間移動，可以如果您原本在 VM 個人版訂閱，並現在想要將其移至您公司的訂閱，以繼續工作。

> [AZURE.NOTE] 移動的一部分，將會建立新的資源識別碼。 已移動 VM，您會需要更新您的工具和指令碼以使用新的資源識別碼。 


[AZURE.INCLUDE [virtual-machines-common-move-vm](../../includes/virtual-machines-common-move-vm.md)]


## <a name="use-powershell-to-move-a-vm"></a>使用 Powershell 來移動 VM

若要移動到另一個資源群組的虛擬機器，必須先確定您也移動所有相關的資源。 若要使用 [移動 AzureRMResource 指令程式，您需要資源名稱] 和 [資源類型。 您可以取得兩者都要尋找 AzureRMResource 指令程式。

    Find-AzureRMResource -ResourceGroupNameContains "<sourceResourceGroupName>"
    

若要移動 VM 我們需要移動多個資源。 我們只建立另一個變數，針對各個資源，然後將它們。 此範例包括 VM，大部分的基本的資源，但您可以新增更多視。

    $sourceRG = "<sourceResourceGroupName>"
    $destinationRG = "<destinationResourceGroupName>"
    
    $vm = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Compute/virtualMachines" -ResourceName "<vmName>"
    $storageAccount = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Storage/storageAccounts" -ResourceName "<storageAccountName>"
    $diagStorageAccount = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Storage/storageAccounts" -ResourceName "<diagnosticStorageAccountName>"
    $vNet = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/virtualNetworks" -ResourceName "<vNetName>"
    $nic = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/networkInterfaces" -ResourceName "<nicName>"
    $ip = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/publicIPAddresses" -ResourceName "<ipName>"
    $nsg = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/networkSecurityGroups" -ResourceName "<nsgName>"
    
    Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vm.ResourceId, $storageAccount.ResourceId, $diagStorageAccount.ResourceId, $vNet.ResourceId, $nic.ResourceId, $ip.ResourceId, $nsg.ResourceId

若要移動到不同的訂閱的資源，包括**-DestinationSubscriptionId**參數。 

    Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vm.ResourceId, $storageAccount.ResourceId, $diagStorageAccount.ResourceId, $vNet.ResourceId, $nic.ResourceId, $ip.ResourceId, $nsg.ResourceId



系統會要求您確認您要移動的指定的資源。 若要確認您要移動的資源類型**Y** 。

  
## <a name="next-steps"></a>後續步驟

您可以在資源群組及訂閱之間移動許多不同的資源類型。 如需詳細資訊，請參閱[移動到新的資源群組或訂閱的資源](../resource-group-move-resources.md)。    