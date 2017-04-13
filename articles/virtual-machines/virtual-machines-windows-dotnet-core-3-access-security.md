<properties
   pageTitle="存取和 Azure 資源管理員範本中的安全性 |Microsoft Azure" 
   description="Azure 虛擬機器 DotNet 核心教學課程"
   services="virtual-machines-windows"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/21/2016"
   ms.author="nepeters"/>

# <a name="access-and-security-in-azure-resource-manager-templates"></a>存取和 Azure 資源管理員範本中的安全性

應用程式裝載於 Azure 可能需要存取透過網際網路或 VPN / Azure Express 路由連線。 音樂市集應用程式範例中，使用網站可公開的 IP 位址的網際網路上。 使用 access 建立，應該保護連線至應用程式和存取虛擬機器資源本身。 網路安全性群組會提供此存取安全性。 

這份文件詳細資料範例 Azure 資源管理員範本中受到保護的音樂市集應用程式。 所有的相依性和唯一的設定會醒目提示。 為了獲得最佳體驗，預先部署至 Azure 訂閱及工作，以及 Azure 資源管理員範本解決方案的執行個體。 完成範本可找到以下 –[在 Windows 上的音樂市集部署](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)。


## <a name="public-ip-address"></a>公用 IP 位址

若要提供公用存取 Azure 資源，可以使用的公用 IP 位址資源。 使用靜態和動態 IP 位址，您可以設定的公用 IP 位址。 如果使用動態地址，且虛擬機器停止並取消配置，則會移除地址。 重新啟動電腦時，它可能會被指派不同的公用 IP 位址。 若要防止變更 IP 位址，可以使用保留的 IP 位址。 

使用 Visual Studio 新增資源精靈]，為 Azure 資源管理員範本或有效 JSON 插入範本，則可以新增的公用 IP 位址。 

請遵循這個連結，請參閱 JSON 範例中的資源管理員範本 –[公用 IP 位址](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L110)。


```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "[variables('publicIpAddressName')]",
  "location": "[resourceGroup().location]",
  "dependsOn": [],
  "tags": {
    "displayName": "public-ip"
  },
  "properties": {
    "publicIPAllocationMethod": "Dynamic",
    "dnsSettings": {
      "domainNameLabel": "[parameters('publicipaddressDnsName')]"
    }
  }
}
```

您可以與虛擬網路介面卡或負載平衡器相關聯的公用 IP 位址。 在此範例中，因為音樂市集網站負載平衡跨多個的虛擬機器公用 IP 位址被連接到負載平衡器。

請遵循這個連結，請參閱 JSON 範例中的資源管理員範本 –[負載平衡器與關聯的公用 IP 位址](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L211)。

```none
"frontendIPConfigurations": [
  {
    "properties": {
      "publicIPAddress": {
        "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIpAddressName'))]"
      }
    },
    "name": "LoadBalancerFrontend"
  }
]
```

從 Azure 入口網站指南公用 IP 位址。 請注意的公用 IP 位址負載平衡器並不虛擬機器相關聯。 本系列下一個文件中的網路負載平衡器的詳細資訊。

![公用 IP 位址](./media/virtual-machines-windows-dotnet-core/pubip-win.png)

如需有關 Azure 的公用 IP 位址的詳細資訊，請參閱[Azure 中的 IP 位址](../virtual-network/virtual-network-ip-addresses-overview-arm.md)。

## <a name="network-security-group"></a>網路安全性群組

一旦 access 建立 Azure 的資源，應該有限存取權。 Azure 虛擬機器，安全存取是使用網路安全性群組來完成。 音樂市集應用程式範例中，所有虛擬機器有限制存取除了 http 存取的連接埠 80 和連接埠 3389 RDP 存取。 使用 Visual Studio 新增資源精靈]，為 Azure 資源管理員範本或有效 JSON 插入範本，則可以新增網路安全性群組。

請遵循這個連結，請參閱資源管理員範本 –[網路安全性群組](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L57)內的 JSON 範例。

```none
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Network/networkSecurityGroups",
  "name": "[variables('networkSecurityGroup')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "network-security-group"
  },
  "properties": {
    "securityRules": [
      {
        "name": "http",
        "properties": {
          "description": "http endpoint",
          "protocol": "Tcp",
          "sourcePortRange": "*",
          "destinationPortRange": "80",
          "sourceAddressPrefix": "*",
          "destinationAddressPrefix": "*",
          "access": "Allow",
          "priority": 100,
          "direction": "Inbound"
        }
      },
      ........<truncated> 
    ]
  }
},
```

在此範例中，網路安全性群組是子網路物件宣告虛擬網路資源中的關聯。 

請遵循這個連結，請參閱在資源管理員樣板 –[虛擬網路的網路安全性群組關聯](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L143)中的 JSON 範例。


```none
"subnets": [
  {
    "name": "[variables('subnetName')]",
    "properties": {
      "addressPrefix": "10.0.0.0/24",
      "networkSecurityGroup": {
        "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroup'))]"
      }
    }
  }
]
```

以下是網路安全性群組從 Azure 入口網站的外觀。 請注意，NSG，可以與子網路和/或網路介面建立關聯。 在此情況下，NSG 是子網路相關聯。 在此組態中，輸入的規則套用至所有子網路連線的虛擬機器。

![網路安全性群組](./media/virtual-machines-windows-dotnet-core/nsg-win.png)

在您採取進階的網路安全性群組的詳細資訊，請參閱[什麼是網路安全性群組]( https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/)。

## <a name="next-step"></a>下一步

<hr>

[步驟 3-可用性和 Azure 資源管理員範本中的縮放比例](./virtual-machines-windows-dotnet-core-4-availability-scale.md)
