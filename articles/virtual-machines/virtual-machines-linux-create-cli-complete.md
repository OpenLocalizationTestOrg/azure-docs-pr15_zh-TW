
<properties
   pageTitle="建立使用 Azure CLI 完成 Linux 環境 |Microsoft Azure"
   description="建立儲存空間、 Linux VM、 虛擬網路和子網路、 負載平衡器、 NIC、 的公用 IP 和網路安全性群組，所有，從頭使用 Azure CLI。"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="iainfoulds"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="10/24/2016"
   ms.author="iainfou"/>

# <a name="create-a-complete-linux-environment-by-using-the-azure-cli"></a>使用 Azure CLI 建立完整 Linux 環境

本文中，我們建立簡單的網路負載平衡器與 Vm 適用於開發及簡單計算的一組。 我們會逐步程序命令的命令，直到您的您可以連線從任何地方在網際網路的兩個工作、 安全 Linux Vm。 然後您可以移至更複雜的網路和環境。

沿著方式，您深入了解相依性階層，資源管理員部署模型可讓您，並瞭解多少 power 提供。 您會看到如何建置系統之後，您可以重建其更快速地使用[Azure 資源管理員範本](../resource-group-authoring-templates.md)。 此外，您將瞭解您的環境的各個部分搭配之後，建立範本，以自動化它們成為更容易。

環境包含︰

- 可用性設定內的兩個 Vm。
- 在連接埠 80 負載平衡規則負載平衡器。
- 網路安全性群組 (NSG) 來保護您 VM 不想要的資料傳輸規則。

![基本的環境概觀](./media/virtual-machines-linux-create-cli-complete/environment_overview.png)

若要建立這個自訂的環境，您需要在資源管理員模式中最新的[Azure CLI](../xplat-cli-install.md) (`azure config mode arm`)。 您也需要 JSON 剖析工具。 此範例使用[jq](https://stedolan.github.io/jq/)。

## <a name="quick-commands"></a>快速命令
若要快速完成工作，下列區段詳細資料基底命令 VM 其上傳到 Azure。 更詳細的資訊和內容的每一個步驟，請參閱其餘的文件中，開始[以下](#detailed-walkthrough)。

請確定您有[Azure CLI](../xplat-cli-install.md)登入並使用資源管理員模式︰

```bash
azure config mode arm
```

在下列範例中，用您自己的值來取代範例參數名稱。 範例參數名稱包含`myResourceGroup`， `mystorageaccount`，及`myVM`。

建立資源群組。 下列範例會建立資源群組名稱`myResourceGroup`中`westeurope`位置︰

```bash
azure group create -n myResourceGroup -l westeurope
```

使用 JSON 剖析器，請確認 [資源] 群組︰

```bash
azure group show myResourceGroup --json | jq '.'
```

建立儲存帳戶。 下列範例會建立名為儲存帳戶`mystorageaccount`（儲存體帳戶名稱必須是唯一的所以請提供您自己的唯一名稱):

```bash
azure storage account create -g myResourceGroup -l westeurope \
  --kind Storage --sku-name GRS mystorageaccount
```

使用 JSON 剖析器，以驗證的儲存空間帳戶︰

```bash
azure storage account show -g myResourceGroup mystorageaccount --json | jq '.'
```

建立虛擬網路。 下列範例會建立名為虛擬網路`myVnet`:

```bash
azure network vnet create -g myResourceGroup -l westeurope\
  -n myVnet -a 192.168.0.0/16
```

建立子網路。 下列範例會建立名為子`mySubnet`」

```bash
azure network vnet subnet create -g myResourceGroup \
  -e myVnet -n mySubnet -a 192.168.1.0/24
```

使用 JSON 剖析器確認虛擬網路和子網路︰


```bash
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

建立的公用 IP。 下列範例會建立一個名為的公用 IP `myPublicIP` DNS 名稱`mypublicdns`（DNS 名稱必須是唯一的所以請提供您自己的唯一名稱):

```bash
azure network public-ip create -g myResourceGroup -l westeurope \
  -n myPublicIP  -d mypublicdns -a static -i 4
```

建立負載平衡器。 下列範例會建立名為負載平衡器`myLoadBalancer`:

```bash
azure network lb create -g myResourceGroup -l westeurope -n myLoadBalancer
```

建立負載平衡器的前端 IP 集區，並建立關聯的公用 IP。 下列範例會建立名為前端 IP 集區`mySubnetPool`:

```bash
azure network lb frontend-ip create -g myResourceGroup -l myLoadBalancer \
  -i myPublicIP -n myFrontEndPool 
```

建立負載平衡器的後端 IP 集區。 下列範例會建立名為後端 IP 集區`myBackEndPool`:

```bash
azure network lb address-pool create -g myResourceGroup -l myLoadBalancer \
  -n myBackEndPool
```

建立 SSH 連入的網路負載平衡器的地址轉譯 (NAT) 規則。 下列範例會建立兩個負載平衡器規則，`myLoadBalancerRuleSSH1`和`myLoadBalancerRuleSSH2`:

```bash
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH1 -p tcp -f 4222 -b 22
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH2 -p tcp -f 4223 -b 22
```

建立 web 連入負載平衡器 NAT 規則。 下列範例會建立名為的負載平衡器規則`myLoadBalancerRuleWeb`

```bash
azure network lb rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleWeb -p tcp -f 80 -b 80 \
  -t myFrontEndPool -o myBackEndPool
```

建立負載平衡器狀況探查。 下列範例會建立名為 TCP 探查`myHealthProbe`:

```bash
azure network lb probe create -g myResourceGroup -l myLoadBalancer \
  -n myHealthProbe -p "tcp" -i 15 -c 4
```

使用 JSON 剖析器確認負載平衡器 IP 資料庫與 NAT 規則︰

```bash
azure network lb show -g myResourceGroup -n myLoadBalancer --json | jq '.'
```

建立第一個網路介面卡 (NIC)。 取代`#####-###-###`節自己 Azure 訂閱 id。 訂閱 ID 所述的輸出`jq`檢查您所建立的資源時。 您也可以檢視您的訂閱識別碼與`azure account list`。 

下列範例會建立名為 NIC `myNic1`:

```bash
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic1 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
```

建立第二個 NIC 下列範例會建立名為 NIC `myNic2`:

```bash
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic2 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
```

使用 JSON 剖析器，以檢查兩個 Nic:

```bash
azure network nic show myResourceGroup myNic1 --json | jq '.'
azure network nic show myResourceGroup myNic2 --json | jq '.'
```

建立網路安全性群組。 下列範例會建立名為網路安全性群組`myNetworkSecurityGroup`:

```bash
azure network nsg create -g myResourceGroup -l westeurope \
  -n myNetworkSecurityGroup
```

新增兩個輸入的規則的網路安全性群組。 下列範例會建立兩個規則，`myNetworkSecurityGroupRuleSSH`和`myNetworkSecurityGroupRuleHTTP`:

```bash
azure network nsg rule create -p tcp -r inbound -y 1000 -u 22 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleSSH
azure network nsg rule create -p tcp -r inbound -y 1001 -u 80 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleHTTP
```

使用 JSON 剖析器確認網路安全性] 群組中，輸入的規則︰

```bash
azure network nsg show -g myResourceGroup -n myNetworkSecurityGroup --json | jq '.'
```

兩個 Nic，繫結網路安全性群組︰

```bash
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic1
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic2
```

建立可用性設定。 下列範例會建立命名集可用性`myAvailabilitySet`:

```bash
azure availset create -g myResourceGroup -l westeurope -n myAvailabilitySet
```

建立第一個 Linux VM。 下列範例會建立名為 VM `myVM1`:

```bash
azure vm create \
    --resource-group myResourceGroup \
    --name myVM1 \
    --location westeurope \
    --os-type linux \
    --availset-name myAvailabilitySet \
    --nic-name myNic1 \
    --vnet-name myVnet \
    --vnet-subnet-name mySubnet \
    --storage-account-name mystorageaccount \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

建立第二個 Linux VM。 下列範例會建立名為 VM `myVM2`:

```bash
azure vm create \
    --resource-group myResourceGroup \
    --name myVM2 \
    --location westeurope \
    --os-type linux \
    --availset-name myAvailabilitySet \
    --nic-name myNic2 \
    --vnet-name myVnet \
    --vnet-subnet-name mySubnet \
    --storage-account-name mystorageaccount \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

使用 JSON 剖析器，以確認建立的所有項目︰

```bash
azure vm show -g myResourceGroup -n myVM1 --json | jq '.'
azure vm show -g myResourceGroup -n myVM2 --json | jq '.'
```

匯出您的新環境範本來快速重新建立新的執行個體︰

```bash
azure group export myResourceGroup
```

## <a name="detailed-walkthrough"></a>詳細逐步解說
遵循的詳細的步驟會說明每一個命令為您建立您的環境時執行的動作。 當您建立您自己的自訂環境開發或生產時，這些概念會很有幫助。

請確定您有[Azure CLI](../xplat-cli-install.md)登入並使用資源管理員模式︰

```bash
azure config mode arm
```

在下列範例中，用您自己的值來取代範例參數名稱。 範例參數名稱包含`myResourceGroup`， `mystorageaccount`，及`myVM`。

## <a name="create-resource-groups-and-choose-deployment-locations"></a>建立資源群組，並選擇部署位置

Azure 資源群組是邏輯部署實體包含設定資訊和啟用的資源部署邏輯管理的中繼資料。 下列範例會建立資源群組名稱`myResourceGroup`中`westeurope`位置︰

```bash
azure group create --name myResourceGroup --location westeurope
```

輸出︰

```bash                        
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westeurope
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

## <a name="create-a-storage-account"></a>建立儲存的帳戶

您需要儲存帳戶的 VM 磁碟以及任何您想要新增的其他資料磁碟。 只有在您建立資源群組之後立即幾乎，您可以建立儲存帳戶。

以下我們使用`azure storage account create`命令，傳遞位置的帳戶]，[資源群組的控制項，且您想要的儲存空間支援的類型。 下列範例會建立名為儲存帳戶`mystorageaccount`:

```bash
azure storage account create \  
  --location westeurope \
  --resource-group myResourceGroup \
  --kind Storage --sku-name GRS \
  mystorageaccount
```

輸出︰

```bash
info:    Executing command storage account create
+ Creating storage account
info:    storage account create command OK
```

若要使用檢查我們資源群組`azure group show`命令，請讓我們來使用[jq](https://stedolan.github.io/jq/)工具沿著與`--json`Azure CLI 選項。 （您可以使用**jsawk**或您想要剖析 JSON 任何語言文件庫。）

```bash
azure group show myResourceGroup --json | jq '.'
```

輸出︰

```bash
{
  "tags": {},
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "name": "myResourceGroup",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
      "name": "mystorageaccount",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

若要使用 CLI 調查儲存帳戶，您必須設定的帳戶名稱] 和 [索引鍵。 在下列範例中的儲存體帳戶名稱取代您選擇的名稱︰

```
AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show mystorageaccount --resource-group myResourceGroup --json | jq -r '.string')"
```

然後您可以輕鬆地檢視您儲存的資訊︰

```bash
azure storage container list
```

輸出︰

```bash
info:    Executing command storage container list
+ Getting storage containers
data:    Name  Public-Access  Last-Modified
data:    ----  -------------  -----------------------------
data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
info:    storage container list command OK
```

## <a name="create-a-virtual-network-and-subnet"></a>建立一個虛擬網路和子網路

下一步您即將需要建立虛擬網路 Azure 和子網路，您可以在其中建立您的 Vm 中執行。 下列範例會建立名為虛擬網路`myVnet`與`192.168.0.0/16`位址首碼︰

```bash
azure network vnet create --resource-group myResourceGroup --location westeurope \
  --name myVnet --address-prefixes 192.168.0.0/16 
```

輸出︰

```bash
info:    Executing command network vnet create
+ Looking up virtual network "myVnet"
+ Creating virtual network "myVnet"
+ Loading virtual network state
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet
data:    Name                            : myVnet
data:    Type                            : Microsoft.Network/virtualNetworks
data:    Location                        : westeurope
data:    ProvisioningState               : Succeeded
data:    Address prefixes:
data:      192.168.0.0/16
info:    network vnet create command OK
```

同樣地，讓我們來使用-json 選擇`azure group show`和**jq**若要查看如何我們建立我們的資源。 現在我們有`storageAccounts`資源和`virtualNetworks`資源。  

```bash
azure group show myResourceGroup --json | jq '.'
```

輸出︰

```bash
{
  "tags": {},
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "name": "myResourceGroup",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
      "name": "myVnet",
      "type": "virtualNetworks",
      "location": "westeurope",
      "tags": null
    },
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
      "name": "mystorageaccount",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

現在讓我們來建立子網路中的`myVnet`虛擬 Vm 部署您的網路。 我們使用`azure network vnet subnet create`命令，以及我們已建立的資源︰`myResourceGroup`資源群組和`myVnet`虛擬網路。 在下列範例中，我們將子網路新增名為`mySubnet`與子網路位址首碼的`192.168.1.0/24`:

```bash
azure network vnet subnet create --resource-group myResourceGroup \
  --vnet-name myVnet --name mySubnet --address-prefix 192.168.1.0/24
```

輸出︰

```bash
info:    Executing command network vnet subnet create
+ Looking up the subnet "mySubnet"
+ Creating subnet "mySubnet"
+ Looking up the subnet "mySubnet"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet
data:    Type                            : Microsoft.Network/virtualNetworks/subnets
data:    ProvisioningState               : Succeeded
data:    Name                            : mySubnet
data:    Address prefix                  : 192.168.1.0/24
data:
info:    network vnet subnet create command OK
```

因為子網路邏輯位於虛擬網路中，我們子網路資訊的位置略有不同的命令。 我們使用命令`azure network vnet show`，但我們繼續使用**jq**檢查 JSON 輸出。

```bash
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

輸出︰

```bash
{
  "subnets": [
    {
      "ipConfigurations": [],
      "addressPrefix": "192.168.1.0/24",
      "provisioningState": "Succeeded",
      "name": "mySubnet",
      "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
    }
  ],
  "tags": {},
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "provisioningState": "Succeeded",
  "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "name": "myVnet",
  "location": "westeurope"
}
```

## <a name="create-a-public-ip-address-pip"></a>建立的公用 IP 位址 (PIP)

現在讓我們來建立的公用 IP 位址 (PIP) 我們指派給您的負載平衡器。 可讓您從網際網路連線到您的 Vm 使用`azure network public-ip create`] 命令。 預設的地址是動態，因為我們建立命名的 DNS 項目**cloudapp.azure.com**網域中使用`--domain-name-label`選項。 下列範例會建立一個名為的公用 IP `myPublicIP` DNS 名稱`mypublicdns`。 DNS 名稱必須是唯一的提供您自己的唯一 DNS 名稱︰

```bash
azure network public-ip create --resource-group myResourceGroup \
  --location westeurope --name myPublicIP --domain-name-label mypublicdns
```

輸出︰

```bash
info:    Executing command network public-ip create
+ Looking up the public ip "myPublicIP"
+ Creating public ip address "myPublicIP"
+ Looking up the public ip "myPublicIP"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP
data:    Name                            : myPublicIP
data:    Type                            : Microsoft.Network/publicIPAddresses
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Allocation method               : Dynamic
data:    Idle timeout                    : 4
data:    Domain name label               : mypublicdns
data:    FQDN                            : mypublicdns.westeurope.cloudapp.azure.com
info:    network public-ip create command OK
```

公用 IP 位址也是最上層的資源，即可查看與其`azure group show`。

```bash
azure group show myResourceGroup --json | jq '.'
```

輸出︰

```bash
{
"tags": {},
"id": "/subscriptions/guid/resourceGroups/myResourceGroup",
"name": "myResourceGroup",
"provisioningState": "Succeeded",
"location": "westeurope",
"properties": {
    "provisioningState": "Succeeded"
},
"resources": [
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
    "name": "myPublicIP",
    "type": "publicIPAddresses",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
    "name": "myVnet",
    "type": "virtualNetworks",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "name": "mystorageaccount",
    "type": "storageAccounts",
    "location": "westeurope",
    "tags": null
    }
],
"permissions": [
    {
    "actions": [
        "*"
    ],
    "notActions": []
    }
]
}
```

您可以檢查資源詳細資料，包括使用完整的完整的網域名稱 (FQDN) 的子網域， `azure network public-ip show` ] 命令。 公用 IP 位址資源分派方式分門別類，但尚未指派的特定的地址。 若要取得 IP 位址，您即將需要負載平衡器，我們尚未建立。

```bash
azure network public-ip show myResourceGroup myPublicIP --json | jq '.'
```

輸出︰

```bash
{
"tags": {},
"publicIpAllocationMethod": "Dynamic",
"dnsSettings": {
    "domainNameLabel": "mypublicdns",
    "fqdn": "mypublicdns.westeurope.cloudapp.azure.com"
},
"idleTimeoutInMinutes": 4,
"provisioningState": "Succeeded",
"etag": "W/\"c63154b3-1130-49b9-a887-877d74d5ebc5\"",
"id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
"name": "myPublicIP",
"location": "westeurope"
}
```

## <a name="create-a-load-balancer-and-ip-pools"></a>建立負載平衡器及 IP 集區
當您建立負載平衡器時，讓您將流量分散到多個 Vm。 同時也會提供您的應用程式的重複執行回應進行的維修作業或高度負載時的使用者要求的多個 Vm。 下列範例會建立名為負載平衡器`myLoadBalancer`:

```bash
azure network lb create --resource-group myResourceGroup --location westeurope \
  --name myLoadBalancer
```

輸出︰

```bash
info:    Executing command network lb create
+ Looking up the load balancer "myLoadBalancer"
+ Creating load balancer "myLoadBalancer"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer
data:    Name                            : myLoadBalancer
data:    Type                            : Microsoft.Network/loadBalancers
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
info:    network lb create command OK
```

我們負載平衡器是相當空的所以讓我們建立一些 IP 集區。 我們想要建立的我們負載平衡器的一個對前端，一個用於後端的兩個 IP 集區。 前端 IP 集區是公開顯示。 也是我們將指派 PIP 我們先前建立的位置。 然後我們作為後端資料庫的位置我們 Vm 連線至。 如此一來，流量透過負載平衡器至 Vm。

首先，讓我們來建立我們前端 IP 集區。 下列範例會建立一個名為的前端集區`myFrontEndPool`:

```bash
azure network lb frontend-ip create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --public-ip-name myPublicIP \
  --name myFrontEndPool 
```

輸出︰

```bash
info:    Executing command network lb frontend-ip create
+ Looking up the load balancer "myLoadBalancer"
+ Looking up the public ip "myPublicIP"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myFrontEndPool
data:    Provisioning state              : Succeeded
data:    Private IP allocation method    : Dynamic
data:    Public IP address id            : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP
info:    network lb mySubnet-ip create command OK
```

請注意，我們如何使用`--public-ip-name`切換至 [傳遞`myPublicIP`我們先前建立的。 指派的公用 IP 位址的負載平衡器可讓您透過網際網路連絡您的 Vm。

接下來，讓我們來建立我們第二個 IP 資料庫，這次，我們的後端流量。 下列範例會建立名為後端資料庫`myBackEndPool`:

```bash
azure network lb address-pool create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myBackEndPool
```

輸出︰

```bash
info:    Executing command network lb address-pool create
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myBackEndPool
data:    Provisioning state              : Succeeded
info:    network lb address-pool create command OK
```

我們可以看到我們負載平衡器來尋找的執行方式`azure network lb show`並檢查 JSON 輸出︰

```bash
azure network lb show myResourceGroup myLoadBalancer --json | jq '.'
```

輸出︰

```bash
{
  "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "name": "myLoadBalancer",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "mySubnetIPConfigurations": [
    {
      "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
      "name": "myFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
      "name": "myBackEndPool",
      "provisioningState": "Succeeded",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
    }
  ],
  "loadBalancingRules": [],
  "probes": []
}
```

## <a name="create-load-balancer-nat-rules"></a>建立負載平衡器 NAT 規則
若要取得流量通過我們負載平衡器，我們需要建立的網路位址轉譯 (NAT) 規則，以指定輸入或輸出的動作。 您可以指定的通訊協定，若要使用，然後將外部連接埠對應到所需的內部連接埠。 我們的環境，讓我們來建立透過至我們 Vm 我們負載平衡器允許 SSH 一些規則。 我們將 TCP 連接埠 4222 和 4223 設定為 TCP 連接埠 22 直接在我們 Vm （其中我們稍後建立）。 下列範例會建立規則命名`myLoadBalancerRuleSSH1`TCP 連接埠 4222 對應到 22 的連接埠︰

```bash
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH1 \
  --protocol tcp --frontend-port 4222 --backend-port 22
```

輸出︰

```bash
info:    Executing command network lb inbound-nat-rule create
+ Looking up the load balancer "myLoadBalancer"
warn:    Using default enable floating ip: false
warn:    Using default idle timeout: 4
warn:    Using default mySubnet IP configuration "myFrontEndPool"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myLoadBalancerRuleSSH1
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    mySubnet port                   : 4222
data:    Backend port                    : 22
data:    Enable floating IP              : false
data:    Idle timeout in minutes         : 4
data:    mySubnet IP configuration id    : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool
info:    network lb inbound-nat-rule create command OK
```

第二個 NAT 規則對 SSH 程序。 下列範例會建立規則命名`myLoadBalancerRuleSSH2`TCP 連接埠 4223 對應到 22 的連接埠︰

```bash
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22
```

現在就讓我們也請繼續，並建立 web 流量，我們 IP 集區最攔截規則的 TCP 連接埠 80 NAT 規則。 如果我們連結 IP 集區，而不是個別連結我們 Vm 規則規則我們可以新增或移除 Vm IP 資料庫中。 負載平衡器會自動調整流量傳送。 下列範例會建立規則命名`myLoadBalancerRuleWeb`TCP 連接埠 80 對應至連接埠 80:

```bash
azure network lb rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleWeb --protocol tcp \
  --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEndPool \
  --backend-address-pool-name myBackEndPool
```

輸出︰

```bash
info:    Executing command network lb rule create
+ Looking up the load balancer "myLoadBalancer"
warn:    Using default idle timeout: 4
warn:    Using default enable floating ip: false
warn:    Using default load distribution: Default
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myLoadBalancerRuleWeb
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    mySubnet port                   : 80
data:    Backend port                    : 80
data:    Enable floating IP              : false
data:    Load distribution               : Default
data:    Idle timeout in minutes         : 4
data:    mySubnet IP configuration id    : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool
data:    Backend address pool id         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool
info:    network lb rule create command OK
```

## <a name="create-a-load-balancer-health-probe"></a>建立負載平衡器狀況檢查

狀況檢查定期檢查 Vm 後，請確定並時，操作回應要求定義我們負載平衡器。 如果沒有，這些從操作以確保使用者不被導向至中移除。 您可以定義自訂的檢查的狀況探查，以及間隔和逾時值。 如需有關狀況探查的詳細資訊，請參閱[負載平衡器探查](../load-balancer/load-balancer-custom-probe-overview.md)。 下列範例會建立 TCP 狀況探查命名`myHealthProbe`:

```bash
azure network lb probe create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myHealthProbe --protocol "tcp" \
  --interval 15 --count 4
```

輸出︰

```bash
info:    Executing command network lb probe create
warn:    Using default probe port: 80
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myHealthProbe
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    Port                            : 80
data:    Interval in seconds             : 15
data:    Number of probes                : 4
info:    network lb probe create command OK
```

在這裡，我們已指定為我們的狀況檢查 15 秒的時間間隔。 我們可以錯過的四個探查 （一分鐘） 之前負載平衡器會根據主機已不再運作。

## <a name="verify-the-load-balancer"></a>驗證負載平衡器
現已完成的負載平衡器設定。 以下是您用的步驟︰

1. 第一次您建立負載平衡器。
2. 然後您建立一個前端 IP 集區，並為其指派的公用 IP。
3. 下一步，就會建立 Vm 可以連線到後端 IP 集區。
4. 之後，您可以建立 NAT 規則，允許 SSH 至 Vm 進行管理，以及允許 TCP 連接埠 80 我們 web 應用程式的規則。
5. 最後，您要新增定期檢查 Vm 狀況檢查。 此狀況探查可確保使用者不嘗試存取 VM 不再運作或提供內容。

讓我們來檢閱您的負載平衡器看起來像現在︰

```bash
azure network lb show --resource-group myResourceGroup \
  --name myLoadBalancer --json | jq '.'
```

輸出︰

```bash
{
  "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleSSH1",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "protocol": "Tcp",
      "mySubnetPort": 4222,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    },
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleSSH2",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "protocol": "Tcp",
      "mySubnetPort": 4223,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    }
  ],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "name": "myLoadBalancer",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "mySubnetIPConfigurations": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb"
        }
      ],
      "inboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
        },
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myBackEndPool",
      "provisioningState": "Succeeded",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
    }
  ],
  "loadBalancingRules": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleWeb",
      "provisioningState": "Succeeded",
      "enableFloatingIP": false,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "backendAddressPool": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
      },
      "protocol": "Tcp",
      "loadDistribution": "Default",
      "mySubnetPort": 80,
      "backendPort": 80,
      "idleTimeoutInMinutes": 4
    }
  ],
  "probes": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myHealthProbe",
      "provisioningState": "Succeeded",
      "numberOfProbes": 4,
      "intervalInSeconds": 15,
      "port": 80,
      "protocol": "Tcp",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/probes/myHealthProbe"
    }
  ]
}
```

## <a name="create-an-nic-to-use-with-the-linux-vm"></a>若要使用 Linux VM NIC 建立

因為您可以將規則套用至使用，Nic 會以程式設計方式提供。 您也可以有多個。 在下列`azure network nic create`命令，您連結到載入後端 IP 集區 NIC 及關聯 NAT 規則，允許 SSH 流量。
 
取代`#####-###-###`節自己 Azure 訂閱 id。 訂閱 ID 所述的輸出`jq`檢查您所建立的資源時。 您也可以檢視您的訂閱識別碼與`azure account list`。 

下列範例會建立名為 NIC `myNic1`:

```bash
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic1 \
  --lb-address-pool-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
```

輸出︰

```bash
info:    Executing command network nic create
+ Looking up the subnet "mySubnet"
+ Looking up the network interface "myNic1"
+ Creating network interface "myNic1"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1
data:    Name                            : myNic1
data:    Type                            : Microsoft.Network/networkInterfaces
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Enable IP forwarding            : false
data:    IP configurations:
data:      Name                          : Nic-IP-config
data:      Provisioning state            : Succeeded
data:      Private IP address            : 192.168.1.4
data:      Private IP allocation method  : Dynamic
data:      Subnet                        : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet
data:      Load balancer backend address pools:
data:        Id                          : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool
data:      Load balancer inbound NAT rules:
data:        Id                          : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
data:
info:    network nic create command OK
```

您可以直接檢查資源，請參閱詳細資料。 使用檢查資源`azure network nic show`命令︰

```bash
azure network nic show myResourceGroup myNic1 --json | jq '.'
```

輸出︰

```bash
{
  "etag": "W/\"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a\"",
  "provisioningState": "Succeeded",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1",
  "name": "myNic1",
  "type": "Microsoft.Network/networkInterfaces",
  "location": "westeurope",
  "ipConfigurations": [
    {
      "etag": "W/\"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1/ipConfigurations/Nic-IP-config",
      "loadBalancerBackendAddressPools": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
        }
      ],
      "loadBalancerInboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
        }
      ],
      "privateIPAddress": "192.168.1.4",
      "privateIPAllocationMethod": "Dynamic",
      "subnet": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
      },
      "provisioningState": "Succeeded",
      "name": "Nic-IP-config"
    }
  ],
  "dnsSettings": {
    "appliedDnsServers": [],
    "dnsServers": []
  },
  "enableIPForwarding": false,
  "resourceGuid": "a20258b8-6361-45f6-b1b4-27ffed28798c"
}
```

現在，我們要建立第二個 NIC，連結在我們的後端 IP 集區。 此時間第二個 NAT 規則允許 SSH 流量。 下列範例會建立名為 NIC `myNic2`:

```bash
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic2 \
  --lb-address-pool-ids  /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2
```

## <a name="create-a-network-security-group-and-rules"></a>建立網路安全性群組和規則

現在我們建立網路安全性群組與管理存取權 NIC 的連入的規則 網路安全性群組可套用至 NIC 或子網路。 您定義規則，以控制流量傳送與您的 Vm 登出。 下列範例會建立名為網路安全性群組`myNetworkSecurityGroup`:

```bash
azure network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

讓我們來新增為允許連接埠 22 （以支援 SSH） 上的連入的連線 NSG 輸入的規則。 下列範例會建立規則命名`myNetworkSecurityGroupRuleSSH`允許 22 的 TCP 連接埠︰

```bash
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1000 --destination-port-range 22 --access allow \
  --name myNetworkSecurityGroupRuleSSH
```

現在讓我們來新增為允許連接埠 80 （以支援網頁流量） 上的連入的連線 NSG 輸入的規則。 下列範例會建立規則命名`myNetworkSecurityGroupRuleHTTP`允許 TCP 連接埠 80:

```bash
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1001 --destination-port-range 80 --access allow \
  --name myNetworkSecurityGroupRuleHTTP
```

> [AZURE.NOTE] 輸入的規則是輸入的網路連線篩選器。 在此範例中，我們將繫結 NSG Vm 虛擬 NIC，表示的連接埠 22 任何要求已傳遞至 NIC 我們 VM 上。 此輸入的規則的網路連線，以及不需端點，哪些是什麼它會是關於在傳統的部署。 若要開啟連接埠，您必須離開`--source-port-range`設定為 「\*' 接受傳入的邀請，從**任何**要求連接埠的 （預設值）。 連接埠是通常是動態。

## <a name="bind-to-the-nic"></a>繫結至 NIC

繫結 Nic NSG。 我們需要我們 Nic 聯繫我們網路安全性群組。 執行兩個命令，以這兩個我們 Nic 連結︰

```bash
azure network nic set --resource-group myResourceGroup --name myNic1 \
  --network-security-group-name myNetworkSecurityGroup
```

```bash
azure network nic set --resource-group myResourceGroup --name myNic2 \
  --network-security-group-name myNetworkSecurityGroup
```

## <a name="create-an-availability-set"></a>建立可用性設定
可用性設定說明模式您 Vm 故障網域和升級的網域。 讓我們來建立設定為您的 Vm 可用性。 下列範例會建立命名集可用性`myAvailabilitySet`:

```bash
azure availset create --resource-group myResourceGroup --location westeurope
  --name myAvailabilitySet
```

故障網域定義共用一般 power 來源和網路切換參數的虛擬機器的群組。 根據預設，設定您的顯示狀態組中虛擬機器分隔各最多三個故障網域。 是其中一個這些錯誤網域的硬體問題不會影響您的應用程式執行每個 VM。 Azure 自動分配 Vm 故障網域時將它們放在可用性設定。

升級的網域會指出虛擬機器和可以重新開機，同時的基礎實體硬體群組。 重新啟動升級的網域的順序不連續期間可能預定進行的維修作業，但只有一個升級重新開機，一次。 同樣地，Azure 自動分配您 Vm 升級網域時將其置於可用性網站中。

進一步瞭解[管理 Vm 的可用性](./virtual-machines-linux-manage-availability.md)。

## <a name="create-the-linux-vms"></a>建立 Linux Vm

您已建立以支援網際網路的可存取 Vm 儲存和網路資源。 現在讓我們來建立這些 Vm 及保護其與沒有密碼 SSH 索引鍵。 在此案例中，我們建立 Ubuntu VM 根據最近 LTS。 我們使用來找出圖像資訊`azure vm image list`、[尋找 Azure VM 圖像](virtual-machines-linux-cli-ps-findimage.md)所述。

我們使用命令選取圖像`azure vm image list westeurope canonical | grep LTS`。 在此案例中，我們使用`canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`。 最後一個欄位中，我們將傳遞`latest`以便在未來我們自動取得最新的建立。 (我們使用的字串是`canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`)。

此下一步是熟悉已經建立的任何人 ssh rsa 公開及私密金鑰金鑰配對 Linux 或 Mac 上使用**ssh-keygen-t rsa-b 2048**。 如果您沒有任何憑證金鑰組您`~/.ssh`目錄，您可以建立︰

- 使用自動`azure vm create --generate-ssh-keys`選項。
- 以手動方式，您可以使用[的指示，自行建立](virtual-machines-linux-mac-create-ssh-keys.md)。

或者，您可以使用`--admin-password`VM 建立後，驗證 SSH 連線的方法。 這個方法是通常是較不安全。

讓我們的所有資源和資訊一起，我們建立 VM`azure vm create`命令︰

```bash
azure vm create \
  --resource-group myResourceGroup \
  --name myVM1 \
  --location westeurope \
  --os-type linux \
  --availset-name myAvailabilitySet \
  --nic-name myNic1 \
  --vnet-name myVnet \
  --vnet-subnet-name mySubnet \
  --storage-account-name mystorageaccount \
  --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username ops
```

輸出︰

```bash
info:    Executing command vm create
+ Looking up the VM "myVM1"
info:    Verifying the public key SSH file: /home/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account mystorageaccount
+ Looking up the availability set "myAvailabilitySet"
info:    Found an Availability set "myAvailabilitySet"
+ Looking up the NIC "myNic1"
info:    Found an existing NIC "myNic1"
info:    Found an IP configuration with virtual network subnet id "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet" in the NIC "myNic1"
info:    This is an NIC without publicIP configured
info:    The storage URI 'https://mystorageaccount.blob.core.windows.net/' will be used for boot diagnostics settings, and it can be overwritten by the parameter input of '--boot-diagnostics-storage-uri'.
info:    vm create command OK
```

您可以連線至您 VM 立即使用預設 SSH 索引鍵。 請確定您指定的適當的連接埠，因為我們通過負載平衡器。 （我們的第一個 VM，我們設定 NAT 規則轉寄連接埠 4222 我們 vm）︰

```bash
 ssh ops@mypublicdns.westeurope.cloudapp.azure.com -p 4222
```

輸出︰

```bash
The authenticity of host '[mypublicdns.westeurope.cloudapp.azure.com]:4222 ([xx.xx.xx.xx]:4222)' can't be established.
ECDSA key fingerprint is 94:2d:d0:ce:6b:fb:7f:ad:5b:3c:78:93:75:82:12:f9.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[mypublicdns.westeurope.cloudapp.azure.com]:4222,[xx.xx.xx.xx]:4222' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.1 LTS (GNU/Linux 4.4.0-34-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

ops@myVM1:~$
```

請繼續進行並建立您的第二個 VM 相同的方式︰

```bash
azure vm create \
  --resource-group myResourceGroup \
  --name myVM2 \
  --location westeurope \
  --os-type linux \
  --availset-name myAvailabilitySet \
  --nic-name myNic2 \
  --vnet-name myVnet \
  --vnet-subnet-name mySubnet \
  --storage-account-name mystorageaccount \
  --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username ops
```

您現在可以使用`azure vm show myResourceGroup myVM1`命令，以檢查您建立的內容。 此時，您執行的您的 Ubuntu Vm 負載平衡器前，您可以登入只能搭配您 SSH 組 （因為密碼已停用） Azure 中。 您可以安裝 nginx 或 httpd、 部署 web 應用程式，並查看流量負載平衡器至這兩個 Vm 流程。

```bash
azure vm show --resource-group myResourceGroup --name myVM1
```

輸出︰

```bash
info:    Executing command vm show
+ Looking up the VM "TestVM1"
+ Looking up the NIC "myNic1"
data:    Id                              :/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM1
data:    ProvisioningState               :Succeeded
data:    Name                            :myVM1
data:    Location                        :westeurope
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :16.04.0-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clib45a8b650f4428a1-os-1471973896525
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://mystorageaccount.blob.core.windows.net/vhds/clib45a8b650f4428a1-os-1471973896525.vhd
data:
data:    OS Profile:
data:      Computer Name                 :myVM1
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-24-D4-AA
data:          Provisioning State        :Succeeded
data:          Name                      :LmyNic1
data:          Location                  :westeurope
data:
data:    AvailabilitySet:
data:      Id                            :/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/availabilitySets/myAvailabilitySet
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://mystorageaccount.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm show command OK

```


## <a name="export-the-environment-as-a-template"></a>匯出另存為範本的環境
現在您已經建立此環境時，該怎麼辦您要建立其他的開發環境使用相同的參數或符合其生產環境？ 資源管理員使用的定義您的環境的所有參數 JSON 範本。 藉由參考此 JSON 範本建置整個環境。 您可以[手動建立 JSON 範本](../resource-group-authoring-templates.md)或匯出為您建立 JSON 範本現有的環境︰

```bash
azure group export --name myResourceGroup
```

這個命令會建立`myResourceGroup.json`您目前的工作目錄中的檔案。 當您從範本建立的環境時，系統會提示您的所有資源的名稱，包括負載平衡器、 網路介面或 Vm 的名稱。 您也可以新增您的範本檔案中填入下列名稱`-p`或`--includeParameterDefaultValue`參數`azure group export`先前顯示的命令。 編輯指定的資源名稱，或[建立 parameters.json 檔案](../resource-group-authoring-templates.md#parameters)指定資源名稱的 JSON 範本。

若要從您的範本建立環境︰

```bash
azure group deployment create --resource-group myNewResourceGroup \
  --template-file myResourceGroup.json
```

您可能會想要閱讀[更多有關如何從範本部署](../resource-group-template-deploy-cli.md)。 深入了解如何累加更新環境，使用參數的檔案，並從單一儲存位置存取範本。

## <a name="next-steps"></a>後續步驟

現在您已準備好要開始使用多個網路元件與 Vm。 您可以使用此範例環境來查看您的應用程式建立以下使用推出的核心元件。
