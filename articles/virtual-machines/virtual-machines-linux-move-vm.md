<properties
    pageTitle="移動 Linux VM |Microsoft Azure"
    description="將 Linux VM 移到另一個 Azure 訂閱] 或 [資源群組資源管理員部署模型中。"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="cynthn"/>

    


# <a name="move-a-linux-vm-to-another-subscription-or-resource-group"></a>將 Linux VM 移到另一個訂閱] 或 [資源群組

本文會引導您如何移動 Linux VM 之間資源群組或訂閱。 訂閱之間移動 VM，可以十分方便您個人的訂閱中建立 VM，而現在想要將其移至您公司的訂閱。

> [AZURE.NOTE] 移動的組件會建立新的資源識別碼。 已移動 VM，您需要更新您的工具和指令碼以使用新的資源識別碼。 


## <a name="use-the-azure-cli-to-move-a-vm"></a>若要移動 VM 使用 Azure CLI 

若要順利移動 VM，必須先將 VM 和所有支援資源。 使用**azure 群組顯示**命令清單中的資源群組和其識別碼的所有資源。 有助於管道這個命令的輸出檔案，讓您可以複製並貼到更新命令的 [識別碼。

    azure group show <resourceGroupName>

若要移動到另一個資源群組 VM 它的資源，請使用**移動 azure 資源**CLI 命令。 下列範例會示範如何移動 VM 和其所需的最常見的資源。 我們會使用**-i**參數和傳遞逗點分隔的清單中 （不含空格） 若要移動的資源的識別碼。

    
    vm=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Compute/virtualMachines/<vmName>
    nic=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/networkInterfaces/<nicName>
    nsg=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/networkSecurityGroups/<nsgName>
    pip=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/publicIPAddresses/<publicIPName>
    vnet=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/virtualNetworks/<vnetName>
    diag=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Storage/storageAccounts/<diagnosticStorageAccountName>
    storage=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Storage/storageAccounts/<storageAcountName>      
    
    azure resource move --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag -d "<destinationResourceGroup>"
    
如果您想要將 VM 和其資源移到不同的訂閱，新增**-目的地 subscriptionId 與 #60; destinationSubscriptionID 與 #62;**參數指定目標訂閱。

如果您在 Windows 電腦上使用命令提示字元中，您需要新增**$**前面的變數的名稱，當您將宣告。 這不需要 Linux。

系統要求您確認您要移動指定的資源。 若要確認您要移動的資源類型**Y** 。
    

[AZURE.INCLUDE [virtual-machines-common-move-vm](../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>後續步驟

您可以在資源群組及訂閱之間移動許多不同的資源類型。 如需詳細資訊，請參閱[移動到新的資源群組或訂閱的資源](../resource-group-move-resources.md)。    