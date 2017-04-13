<properties
   pageTitle="建立多個 Nic Linux VM |Microsoft Azure"
   description="瞭解如何使用多個 Nic 附加至並使用 Azure CLI] 或 [資源管理員範本建立 Linux VM。"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="10/27/2016"
   ms.author="iainfou"/>

# <a name="creating-a-linux-vm-with-multiple-nics"></a>建立多個 Nic Linux VM
您可以建立具有附加多個虛擬網路介面 (Nic) 的 Azure 虛擬機器 (VM)。 有不同的前端與後端的連線或網路專門用來監視或備份解決方案的子網路是最常見的案例。 本文提供使用多個 Nic 附加建立 VM 的快速命令。 如詳細資訊，包括如何建立您自己艦隊指令碼中的多個 Nic 閱讀更多有關[部署多重 NIC Vm](../virtual-network/virtual-network-deploy-multinic-arm-cli.md)。 不同[VM 大小](virtual-machines-linux-sizes.md)支援不同的數字的 Nic，因此會相應地大小您 VM。

>[AZURE.WARNING] 當您建立 VM-您無法將 Nic 新增至現有的 VM 時，您必須附加多個 Nic。 您可以[建立 VM 根據原始的虛擬磁碟](virtual-machines-linux-copy-vm.md)，以及視部署 VM 建立多個 Nic。

## <a name="quick-commands"></a>快速命令
請確定您有[Azure CLI](../xplat-cli-install.md)登入並使用資源管理員模式︰

```bash
azure config mode arm
```

在下列範例中，用您自己的值來取代範例參數名稱。 範例參數名稱包含`myResourceGroup`， `mystorageaccount`，及`myVM`。

首先，建立資源群組。 下列範例會建立資源群組名稱`myResourceGroup`中`WestUS`位置︰

```bash
azure group create myResourceGroup -l WestUS
```

建立保留您的 Vm 儲存帳戶。 下列範例會建立名為儲存帳戶`mystorageaccount`:

```bash
azure storage account create mystorageaccount -g myResourceGroup \
    -l WestUS --kind Storage --sku-name PLRS
```

建立虛擬網路連線至您 Vm。 下列範例會建立名為虛擬網路`myVnet`的地址前置詞`192.168.0.0/16`:

```bash
azure network vnet create -g myResourceGroup -l WestUS \
    -n myVnet -a 192.168.0.0/16
```

建立兩個虛擬網路的子網路-前端的交通和後端流量。 下列範例會建立兩個子網路，名為`mySubnetFrontEnd`和`mySubnetBackEnd`:

```bash
azure network vnet subnet create -g myResourceGroup -e myVnet \
    -n mySubnetFrontEnd -a 192.168.1.0/24
azure network vnet subnet create -g myResourceGroup -e myVnet \
    -n mySubnetBackEnd -a 192.168.2.0/24
```

建立兩個 Nic，附加至前端子網路 NIC 和 NIC 到後端的子網路。 下列範例會建立兩個 Nic，名為`myNic1`和`myNic2`，並將其附加至您子網路︰

```bash
azure network nic create -g myResourceGroup -l WestUS \
    -n myNic1 -m myVnet -k mySubnetFrontEnd
azure network nic create -g myResourceGroup -l WestUS \
    -n myNic2 -m myVnet -k mySubnetBackEnd
```

最後，建立您的 VM，附加您先前建立的兩個 Nic。 下列範例會建立名為 VM `myVM`:

```bash
azure vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location WestUS \
    --os-type linux \
    --nic-names myNic1,myNic2 \
    --vm-size Standard_DS2_v2 \
    --storage-account-name mystorageaccount \
    --image-urn UbuntuLTS \
    --admin-username ops \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
```

## <a name="creating-multiple-nics-using-azure-cli"></a>建立多個 Nic 使用 Azure CLI
如果您先前已建立使用 Azure CLI VM，則必須熟悉的快速命令。 程序會建立一個 NIC 或多個 Nic 相同。 您可以瞭解更多詳細資料的相關[部署使用 Azure CLI 的多個 Nic](../virtual-network/virtual-network-deploy-multinic-arm-cli.md)，包括指令碼的程序，逐一建立所有 Nic。

下列範例會建立兩個 Nic，名為`myNic1`和`myNic2`，與一個 NIC 每一個子網路連線︰

```bash
azure network nic create --resource-group myResourceGroup --location WestUS \
    -n myNic1 --subnet-vnet-name myVnet --subnet-name mySubnetFrontEnd
azure network nic create --resource-group myResourceGroup --location WestUS \
    -n myNic2 --subnet-vnet-name myVnet --subnet-name mySubnetBackEnd
```

通常您也建立[網路安全性群組](../virtual-network/virtual-networks-nsg.md)或[負載平衡器](../load-balancer/load-balancer-overview.md)可協助管理及分散您 Vm 流量。 同樣地，命令會完全相同，當您使用多個 Nic 時。 下列範例會建立名為網路安全性群組`myNetworkSecurityGroup`:

```bash
azure network nsg create --resource-group myResourceGroup --location WestUS \
    --name myNetworkSecurityGroup
```

將您 Nic 繫結至網路安全性群組使用`azure network nic set`。 下列範例會繫結`myNic1`和`myNic2`與`myNetworkSecurityGroup`:

```bashazure 
azure network nic set --resource-group myResourceGroup --name myNic1 \
    --network-security-group-name myNetworkSecurityGroup
azure network nic set --resource-group myResourceGroup --name myNic2 \
    --network-security-group-name myNetworkSecurityGroup
```

在建立 VM 時，您現在可以指定多個 Nic。 而使用`--nic-name`提供單一 NIC，不過您使用`--nic-names`，並提供的 Nic 逗點分隔的清單。 您也需要注意當您選取虛擬記憶體大小。 有限制，您可以新增至 VM 的 Nic 總數。 進一步瞭解[Linux VM 大小](virtual-machines-linux-sizes.md)。 下列範例會示範如何指定多個 Nic 然後支援使用多個 Nic 虛擬記憶體大小 (`Standard_DS2_v2`):

```bash
azure vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location WestUS \
    --os-type linux \
    --nic-names myNic1,myNic2 \
    --vm-size Standard_DS2_v2 \
    --storage-account-name mystorageaccount \
    --image-urn UbuntuLTS \
    --admin-username ops \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
```

## <a name="creating-multiple-nics-using-resource-manager-templates"></a>建立多個 Nic 使用資源管理員範本
Azure 資源管理員範本會使用宣告式 JSON 檔案，以定義您的環境。 您可以瞭解[Azure 資源管理員的概觀](../azure-resource-manager/resource-group-overview.md)。 資源管理員範本提供建立資源的多個執行個體部署，例如建立多個 Nic 期間的方式。 您可以使用*複製*至指定的執行個體建立數︰

```bash
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

進一步瞭解[建立多個執行個體，使用*複製*](../resource-group-create-multiple.md)。 

您也可以使用`copyIndex()`然後附加到資源的名稱，可讓您建立的 [數字`myNic1`，`myNic2`等。以下顯示附加的索引值的範例︰

```bash
"name": "[concat('myNic', copyIndex())]", 
```

您可以瞭解[建立多個 Nic 使用資源管理員範本](../virtual-network/virtual-network-deploy-multinic-arm-template.md)的完整的範例。

## <a name="next-steps"></a>後續步驟
請確定時嘗試建立多個 Nic VM 檢閱[Linux VM 大小](virtual-machines-linux-sizes.md)。 請注意的每個虛擬記憶體大小支援的 Nic 數上限。 

請記住，您無法將其他 Nic 新增至現有的 VM，當您部署 VM 時，您必須建立所有 Nic。 請注意規劃您的部署，請確定您已從開始的所有必要的網路連線時。