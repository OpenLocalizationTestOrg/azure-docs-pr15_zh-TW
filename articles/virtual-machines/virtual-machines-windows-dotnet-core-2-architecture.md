<properties
   pageTitle="部署計算資源的 Azure 資源管理員範本 |Microsoft Azure"
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

# <a name="application-architecture-with-azure-resource-manager-templates"></a>應用程式架構與 Azure 資源管理員範本

在開發 Azure 資源管理員部署時，計算需求需要對應到 Azure 資源和服務。 如果應用程式，包含數個 http 端點與資料庫的資料快取服務，Azure 資源裝載的每個元件必須是縮編。 例如，範例音樂市集應用程式所包含的裝載在虛擬機器，web 應用程式與 SQL 資料庫裝載於 Azure SQL 資料庫。 

這份文件詳細資料範例 Azure 資源管理員範本中的音樂市集計算資源的設定方式。 所有的相依性和唯一的設定會醒目提示。 為了獲得最佳體驗，預先部署至 Azure 訂閱及工作，以及 Azure 資源管理員範本解決方案的執行個體。 完成範本可找到以下 –[在 Windows 上的音樂市集部署](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)。

## <a name="virtual-machine"></a>虛擬機器

音樂市集應用程式包含 web 應用程式客戶可以瀏覽及購買的音樂的位置。 有數種 Azure 服務可裝載 web 應用程式，例如，會使用虛擬機器。 使用範例音樂儲存範本，部署虛擬機器、 網頁伺服器安裝及音樂市集網站安裝和設定。 本文中，為詳細的虛擬機器部署。 網頁伺服器和應用程式的設定，有更新版本的文件中詳細說明。

使用 Visual Studio 加入新資源] 精靈中，為範本或有效 JSON 插入部署範本，則可以新增虛擬機器。 在部署虛擬機器，也需要多個相關的資源。 如果使用 Visual Studio 建立範本，會建立這些資源。 如果以手動方式建構範本時，這些資源需要插入及設定。

請遵循這個連結，請參閱中的資源管理員範本 –[虛擬機器 JSON](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L285)JSON 範例。

```none
{
  {
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[concat(variables('vmName'),copyindex())]",
  "location": "[resourceGroup().location]",
  "copy": {
    "name": "virtualMachineLoop",
    "count": "[parameters('numberOfInstances')]"
  },
  "tags": {
    "displayName": "virtual-machine"
  },
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('vhdStorageName'))]",
    "[concat('Microsoft.Compute/availabilitySets/', variables('availabilitySetName'))]",
    "nicLoop"
  ],
  "properties": {
    "availabilitySet": {
      "id": "[resourceId('Microsoft.Compute/availabilitySets', variables('availabilitySetName'))]"
    },
  ........<truncated>  
}
```

部署，可以看到的虛擬機器屬性 Azure 入口網站中。

![虛擬機器](./media/virtual-machines-windows-dotnet-core/vm-win.png)

## <a name="storage-account"></a>儲存帳戶

儲存帳戶有許多儲存空間的選項和功能。 Azure 虛擬機器中的內容，以儲存帳戶會保留虛擬機器及任何其他資料磁碟的虛擬的硬碟。 音樂市集範例包括如要保留的每個虛擬機器虛擬硬碟部署內的一個儲存帳戶。 

請遵循這個連結，請參閱 JSON 範例中的資源管理員範本 –[儲存帳戶](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L98)。


```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[variables('vhdStorageName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "storage-account"
  },
  "properties": {
    "accountType": "[variables('vhdStorageType')]"
  }
}
```

儲存帳戶是使用虛擬機器資源管理員範本宣告的虛擬機器中的關聯。 

請遵循這個連結，請參閱 JSON 範例中的資源管理員範本 –[虛擬機器和儲存帳戶的關聯](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L321)。

```none
"osDisk": {
  "name": "osdisk",
  "vhd": {
    "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/',variables('vhdStorageName')), '2015-06-15').primaryEndpoints.blob,'vhds/osdisk', copyindex(), '.vhd')]"
  },
  "caching": "ReadWrite",
  "createOption": "FromImage"
}
```

部署之後，可以 Azure 入口網站中檢視儲存的帳戶。

![儲存帳戶](./media/virtual-machines-windows-dotnet-core/storacct-win.png)

按一下儲存空間帳戶 blob 容器，可以看到每個虛擬機器部署與範本的虛擬硬碟檔案。

![虛擬硬碟](./media/virtual-machines-windows-dotnet-core/vhd-win.png)

Azure 儲存體的詳細資訊，請參閱[Azure 儲存體文件](https://azure.microsoft.com/documentation/services/storage/)。

## <a name="virtual-network"></a>虛擬網路

如果虛擬機器需要內部網路，例如進行通訊與其他的虛擬機器 Azure 的資源，Azure 虛擬網路需要。  虛擬的網路不會讓虛擬機器存取網際網路。 公用連線需要詳細稍後的這一系列的公用 IP 位址。

請遵循這個連結，請參閱 JSON 資源管理員樣板 –[虛擬網路和子網路](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L126)中的範例。

```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/virtualNetworks",
  "name": "[variables('virtualNetworkName')]",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Network/networkSecurityGroups/', variables('networkSecurityGroup'))]"
  ],
  "tags": {
    "displayName": "virtual-network"
  },
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "10.0.0.0/24"
      ]
    },
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
  }
}
```

從 Azure] 入口網站虛擬網路看起來類似下列圖像。 請注意，所有的虛擬機器範本部署附加至虛擬網路。

![虛擬網路](./media/virtual-machines-windows-dotnet-core/vnet-win.png)

## <a name="network-interface"></a>網路介面

 網路介面虛擬網路，更精確地已經定義在虛擬網路的子網路連線虛擬機器。 
 
 請遵循這個連結，請參閱 JSON 範例中的資源管理員範本 –[網路介面](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L156)。
 
```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/networkInterfaces",
  "name": "[concat(variables('networkInterfaceName'), copyindex())]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "network-interface"
  },
  "copy": {
    "name": "nicLoop",
    "count": "[parameters('numberOfInstances')]"
  },
  "dependsOn": [
    "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
    "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
    "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIpAddressName'))]",
    "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'), '/inboundNatRules/', 'RDP-VM', copyIndex())]"
  ],
  "properties": {
    "ipConfigurations": [
      {
        "name": "ipconfig",
        "properties": {
          "privateIPAllocationMethod": "Dynamic",
          "subnet": {
            "id": "[variables('subnetRef')]"
          },
          "loadBalancerBackendAddressPools": [
            {
              "id": "[variables('lbPoolID')]"
            }
          ],
          "loadBalancerInboundNatRules": [
            {
              "id": "[concat(variables('lbID'),'/inboundNatRules/RDP-VM', copyIndex())]"
            }
          ]
        }
      }
    ]
  }
}
```

每個虛擬機器資源包括網路設定檔。 網路介面是這個設定檔中的虛擬機器與相關聯。  

請遵循這個連結，請參閱在資源管理員樣板 –[虛擬機器網路的設定檔](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L330)中的 JSON 範例。


```none
"networkProfile": {
  "networkInterfaces": [
    {
      "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('networkInterfaceName'), copyindex()))]"
    }
  ]
}
```

從 Azure] 入口網站的網路介面看起來類似下列圖像。 內部的 IP 位址和虛擬機器關聯能夠看到在網路介面資源。

![網路介面](./media/virtual-machines-windows-dotnet-core/nic-win.png)

Azure 虛擬網路的詳細資訊，請參閱[Azure 虛擬網路文件](https://azure.microsoft.com/documentation/services/virtual-network/)。

## <a name="azure-sql-database"></a>Azure SQL 資料庫

虛擬機器音樂存放區的網站，除了部署 Azure SQL 資料庫裝載音樂儲存資料庫。 以下使用 Azure SQL 資料庫的優點是虛擬機器第二個設定並非必要，，和比例] 和 [可用性內建的服務。

您可以使用 Visual Studio 新增新的資源 」 精靈或範本中插入有效 JSON 加入 Azure SQL 資料庫。 SQL Server 資源包括使用者名稱和密碼授與系統管理權限的 SQL 執行個體。 此外，也會新增 SQL 防火牆資源。 根據預設，裝載於 Azure 的應用程式都能使用的 SQL 執行個體連線。 若要允許外部應用程式連線至 SQL 執行個體，防火牆例如 SQL Server Management studio 必須設定。 這是因為音樂存放區示範，預設的設定就沒問題了。 

請遵循這個連結，請參閱 JSON 範例中的資源管理員範本 – [Azure SQL 資料庫](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L379)。


```none
{
  "apiVersion": "2014-04-01-preview",
  "type": "Microsoft.Sql/servers",
  "name": "[variables('musicstoresqlName')]",
  "location": "[resourceGroup().location]",
  "dependsOn": [],
  "tags": {
    "displayName": "sql-music-store"
  },
  "properties": {
    "administratorLogin": "[parameters('adminUsername')]",
    "administratorLoginPassword": "[parameters('adminPassword')]"
  },
  "resources": [
    {
      "apiVersion": "2014-04-01-preview",
      "type": "firewallrules",
      "name": "firewall-allow-azure",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Sql/servers/', variables('musicstoresqlName'))]"
      ],
      "properties": {
        "startIpAddress": "0.0.0.0",
        "endIpAddress": "0.0.0.0"
      }
    }
  ]
}
```

SQL server 和 MusicStore 資料庫 Azure 入口網站中的檢視。

![SQL Server](./media/virtual-machines-windows-dotnet-core/sql-win.png)

如需部署 Azure SQL 資料庫的詳細資訊，請參閱[Azure SQL 資料庫文件](https://azure.microsoft.com/documentation/services/sql-database/)。

## <a name="next-step"></a>下一步

<hr>

[步驟 2-存取和 Azure 資源管理員範本中的安全性](./virtual-machines-windows-dotnet-core-3-access-security.md)
