<properties
   pageTitle="開啟與使用 PowerShell VM 的連接埠 |Microsoft Azure"
   description="瞭解如何開啟連接埠 / 建立使用 Azure 資源管理員部署模式和 PowerShell 的 Azure 您 Windows vm 端點"
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
   ms.date="10/27/2016"
   ms.author="iainfou"/>

# <a name="opening-ports-and-endpoints-to-a-vm-in-azure-using-powershell"></a>使用 PowerShell 的 Azure 中開啟連接埠和 vm 的端點
[AZURE.INCLUDE [virtual-machines-common-nsg-quickstart](../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>快速命令
若要建立網路安全性群組與 ACL 規則必須[安裝 PowerShell 的 Azure 的最新版本](../powershell-install-configure.md)。 您也可以[執行下列步驟使用 [Azure 入口網站](virtual-machines-windows-nsg-quickstart-portal.md)。

登入您的 Azure 帳戶︰

```powershell
Login-AzureRmAccount
```

在下列範例中，用您自己的值來取代範例參數名稱。 範例參數名稱包含`myResourceGroup`， `myNetworkSecurityGroup`，及`myVnet`。

建立規則。 下列範例會建立規則命名`myNetworkSecurityGroupRule`允許 TCP 連接埠 80 上的資料傳輸︰

```powershell
$httprule = New-AzureRmNetworkSecurityRuleConfig -Name "myNetworkSecurityGroupRule" `
    -Description "Allow HTTP" -Access "Allow" -Protocol "Tcp" -Direction "Inbound" `
    -Priority "100" -SourceAddressPrefix "Internet" -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 80
```

接下來，建立您的網路安全性群組，並指派 HTTP 您剛建立的規則，如下所示。 下列範例會建立名為網路安全性群組`myNetworkSecurityGroup`:

```powershell
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNetworkSecurityGroup" -SecurityRules $httprule
```

現在讓我們來將您的網路安全性群組指派給子網路。 下列範例會指派名為現有的虛擬網路`myVnet`變數`$vnet`:

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

建立您的網路安全性群組與您子網路之間的關聯。 下列範例會將名稱為子網路`mySubnet`與您的網路安全性群組︰

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

最後，更新您的虛擬網路，讓變更生效的順序︰

```powershell
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>網路安全性群組的詳細資訊
此處的快速命令可讓您快速上手與您 vm 流量。 網路安全性群組提供許多很棒的功能及資料粒度來控制存取您的資源。 您可以閱讀更多關於[建立網路安全性群組和以下 ACL 規則](../virtual-network/virtual-networks-create-nsg-arm-ps.md)。

您可以定義網路安全性群組以及 ACL 規則 Azure 資源管理員範本的一部分。 閱讀更多關於[建立網路安全性群組的範本](../virtual-network/virtual-networks-create-nsg-arm-template.md)。

如果您要使用內部連接埠上您 VM 對應唯一的外部連接埠的連接埠轉接，請使用負載平衡器和網路位址轉譯 (NAT) 規則。 例如，可能要公開 TCP 連接埠 8080 外部有流量導向至 TCP 連接埠 80 VM 上。 您可以瞭解如何[建立具網際網路負載平衡器](../load-balancer/load-balancer-get-started-internet-arm-ps.md)。

## <a name="next-steps"></a>後續步驟
在此範例中，您可以建立簡單的規則，以允許 HTTP 流量。 您可以找到下列文章中建立更詳細的環境的詳細資訊︰

- [Azure 資源管理員的概觀](../azure-resource-manager/resource-group-overview.md)
- [什麼是網路安全性群組 (NSG)？](../virtual-network/virtual-networks-nsg.md)
- [Azure 資源管理員概觀負載平衡器](../load-balancer/load-balancer-arm.md)