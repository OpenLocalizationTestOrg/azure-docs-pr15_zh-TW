<properties
   pageTitle="開啟連接埠和 Linux vm 端點 |Microsoft Azure"
   description="瞭解如何開啟連接埠 / 建立使用 Azure 資源管理員部署模型和 Azure CLI 您 Linux vm 端點"
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
   ms.workload="infrastructure-services"
   ms.date="10/27/2016"
   ms.author="iainfou"/>

# <a name="opening-ports-and-endpoints-to-a-linux-vm-in-azure"></a>開啟連接埠和與 Linux VM Azure 中的端點
您開啟連接埠]，或 Azure 中建立端點，虛擬機器 (VM)，以建立子網路或 VM 網路介面網路篩選。 您可以控制傳入和傳出流量，這些篩選器置於網路安全性群組附加至接收的流量的資源。 讓我們來使用連接埠 80 網路流量的常見範例。

## <a name="quick-commands"></a>快速命令
若要建立網路安全性群組，您需要[Azure CLI](../xplat-cli-install.md)安裝並使用資源管理員模式的規則︰

```bash
azure config mode arm
```

在下列範例中，用您自己的值來取代範例參數名稱。 範例參數名稱包含`myResourceGroup`， `myNetworkSecurityGroup`，及`myVnet`。

建立網路安全性群組中，輸入適當的名稱和位置。 下列範例會建立名為網路安全性群組`myNetworkSecurityGroup`中`WestUS`位置︰

```
azure network nsg create --resource-group myResourceGroup --location westus \
    --name myNetworkSecurityGroup
```

新增規則，允許 HTTP 流量至您的 web 伺服器上 （或 [調整成您自己的情況，例如 SSH access 或資料庫的連線）。 下列範例會建立規則命名`myNetworkSecurityGroupRule`允許 TCP 連接埠 80 上的資料傳輸︰

```
azure network nsg rule create --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRule \
    --protocol tcp --direction inbound --priority 1000 \
    --destination-port-range 80 --access allow
```

建立網路安全性群組您 VM 網路介面 (NIC) 之間的關聯。 下列範例會將名稱為現有 NIC`myNic`與網路安全性群組名為`myNetworkSecurityGroup`:

```
azure network nic set --resource-group myResourceGroup \
    --network-security-group-name myNetworkSecurityGroup --name myNic
```

或者，您也可以使用子虛擬網路，而不是只是要在單一 VM 的網路介面關聯網路安全性群組。 下列範例會將名稱為現有子網路`mySubnet`中`myVnet`虛擬網路名稱為網路安全性群組`myNetworkSecurityGroup`:

```
azure network vnet subnet set --resource-group myResourceGroup \
    --network-security-group-name myNetworkSecurityGroup \
    --vnet-name myVnet --name mySubnet
```

## <a name="more-information-on-network-security-groups"></a>網路安全性群組的詳細資訊
此處的快速命令可讓您快速上手與您 vm 流量。 網路安全性群組提供許多很棒的功能及資料粒度來控制存取您的資源。 您可以閱讀更多關於[建立網路安全性群組和以下 ACL 規則](../virtual-network/virtual-networks-create-nsg-arm-cli.md)。

您可以定義網路安全性群組以及 ACL 規則 Azure 資源管理員範本的一部分。 閱讀更多關於[建立網路安全性群組的範本](../virtual-network/virtual-networks-create-nsg-arm-template.md)。

如果您要使用內部連接埠上您 VM 對應唯一的外部連接埠的連接埠轉接，請使用負載平衡器和網路位址轉譯 (NAT) 規則。 例如，可能要公開 TCP 連接埠 8080 外部有流量導向至 TCP 連接埠 80 VM 上。 您可以瞭解如何[建立具網際網路負載平衡器](../load-balancer/load-balancer-get-started-internet-arm-cli.md)。

## <a name="next-steps"></a>後續步驟
在此範例中，您可以建立簡單的規則，以允許 HTTP 流量。 您可以找到下列文章中建立更詳細的環境的詳細資訊︰

- [Azure 資源管理員的概觀](../azure-resource-manager/resource-group-overview.md)
- [什麼是網路安全性群組 (NSG)？](../virtual-network/virtual-networks-nsg.md)
- [Azure 資源管理員概觀負載平衡器](../load-balancer2    /load-balancer-arm.md)