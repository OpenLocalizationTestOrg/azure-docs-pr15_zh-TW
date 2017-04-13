<properties 
   pageTitle="如何將 VM 或角色的執行個體移至不同的子網路"
   description="瞭解如何將 Vm 與角色執行個體移至不同的子網路"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/22/2016"
   ms.author="jdial" />

# <a name="how-to-move-a-vm-or-role-instance-to-a-different-subnet"></a>如何將 VM 或角色的執行個體移至不同的子網路

您可以使用 PowerShell 將您 Vm 從一個子網路移到另一個在相同的虛擬網路 (VNet)。 可以編輯 CSCFG，而不是使用 PowerShell 來移動角色執行個體。

>[AZURE.NOTE] 本文包含相對於只 Azure 傳統部署的資訊。

為什麼將 Vm 移到另一個子網路？ 子網路移轉時，較舊的子網路太小而無法展開子網路中的現有執行 Vm 因為。 在此情況下，您可以建立新的較大的子網路，並將 Vm 移轉至新的子網路，然後完成移轉後，您可以刪除舊的空白子網路。

## <a name="how-to-move-a-vm-to-another-subnet"></a>如何將 VM 移到另一個子網路

若要移動 VM，請執行設定 AzureSubnet PowerShell 指令程式，使用下列範例另存為範本。 在下面的範例中，我們會將 TestVM 從其簡報的子網路，移至子網路 2。 請務必編輯以反映您的環境的範例。 請注意，每當您執行更新 AzureVM cmdlet 程序的一部分，它將重新啟動您 VM 更新程序的一部分。

    Get-AzureVM –ServiceName TestVMCloud –Name TestVM `
  	| Set-AzureSubnet –SubnetNames Subnet-2 `
  	| Update-AzureVM

如果您指定您 VM 靜態內部私人 ip 位址，您將需要您可以將 VM 移到新的子網路之前，請清除該設定。 在此情況下，使用下列步驟︰

    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
  	| Remove-AzureStaticVNetIP `
  	| Update-AzureVM
    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
  	| Set-AzureSubnet -SubnetNames Subnet-2 `
  	| Update-AzureVM

## <a name="to-move-a-role-instance-to-another-subnet"></a>若要將角色執行個體移到另一個子網路

若要移動的角色執行個體，請編輯 CSCFG 檔案。 在下面的範例中，我們移動 「 Role0 」 虛擬網路*VNETName*中從其簡報的子網路*的子網路 2*。 由於已經部署角色執行個體，您只會變更子網路名稱 = 子網路 2。 請務必編輯以反映您的環境的範例。

    <NetworkConfiguration>
        <VirtualNetworkSite name="VNETName" />
        <AddressAssignments>
           <InstanceAddress roleName="Role0">
                <Subnets><Subnet name="Subnet-2" /></Subnets>
           </InstanceAddress>
        </AddressAssignments>
    </NetworkConfiguration> 
