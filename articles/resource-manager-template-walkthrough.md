<properties
   pageTitle="資源管理員範本逐步解說 |Microsoft Azure"
   description="資源管理員範本佈建的基本 Azure IaaS 架構的逐步解說逐步解說。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/04/2016"
   ms.author="navale;tomfitz"/>
   
# <a name="resource-manager-template-walkthrough"></a>資源管理員範本逐步解說

第一個問題時建立的範本之一是 「 若要開始的方式嗎？ 」。 其中一個可以從空白範本，追蹤[製作的範本文件](resource-group-authoring-templates.md#template-format)中所述的基本結構，並新增資源並適當的參數和變數。 好的方法是先通過[快速入門] 庫](https://github.com/Azure/azure-quickstart-templates)，並尋找您想要建立項目類似的案例。 您可以合併多個範本，或編輯現有以符合您自己的特定案例。 

讓我們來看一下常見的基礎結構︰

* 使用相同的儲存空間帳戶、 在相同的可用性設定，並在相同的虛擬網路的子網路上的兩個虛擬機器。
* 每個虛擬機器單一 NIC 與 VM IP 位址。
* 負載平衡連接埠 80 規則的負載平衡器

![架構](./media/resource-group-overview/arm_arch.png)

本主題會引導您建立的基礎結構的資源管理員範本的步驟。 您建立的最後一個範本是以稱為[2 Vm 負載平衡器和負載平衡規則中](https://azure.microsoft.com/documentation/templates/201-2-vms-loadbalancer-lbrules/)的快速入門範本為基礎。

不過，很大，一次建立，所以讓我們先建立儲存帳戶並將其部署。 您有掌握建立儲存帳戶之後，您將會新增其他的資源，並重新部署完成基礎結構的範本。

>[AZURE.NOTE] 建立範本時，您可以使用任何類型的 [編輯器]。 Visual Studio 提供簡化範本開發的工具，但您不需要 Visual Studio 完成本教學課程。 使用 Visual Studio 建立 Web 應用程式和 SQL 資料庫部署的教學課程，請參閱[建立及部署 Visual Studio 透過 Azure 資源群組](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)。 

## <a name="create-the-resource-manager-template"></a>建立資源管理員範本

範本已定義的所有資源部署 JSON 檔案。 您也可以定義參數期間部署中，指定的其他值的運算式，並部署中的輸出建構的變數。 

最簡單的範本，讓我們開始︰

```json
    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {  },
      "variables": {  },
      "resources": [  ],
      "outputs": {  }
    }
 ```

將檔案儲存為**azuredeploy.json** （請注意，範本可以有任何您想要的名稱，只是它必須 json 檔案）。

## <a name="create-a-storage-account"></a>建立儲存的帳戶
在 [**資源**] 區段中，新增物件，定義的儲存空間帳戶，如下所示。 

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[parameters('storageAccountName')]",
    "apiVersion": "2015-06-15",
    "location": "[resourceGroup().location]",
    "properties": {
      "accountType": "Standard_LRS"
    }
  }
]
```

您可能會想要知道這些屬性和值來源位置。 內容**類型**、**名稱**、 **apiVersion**和**位置**是標準的項目所使用的所有資源類型。 您可以瞭解在[資源](resource-group-authoring-templates.md#resources)的常見項目。 設定**名稱**參數值傳遞中期間部署及**位置**作為資源群組所使用的位置。 我們會如何決定**類型**與**apiVersion**以下各節中。

[**屬性**] 區段包含的所有專屬的特定的資源類型的屬性。 您完全指定此區段中的值符合放入中作業 REST API 建立的資源類型。 在建立儲存帳戶時，您必須提供**accountType**。 請注意[REST API 建立儲存帳戶](https://msdn.microsoft.com/library/azure/mt163564.aspx)中的 [屬性] 區段的 [其他] 作業也包含**accountType** ] 屬性中，允許的值會記錄。 在此範例中的帳戶類型設定為 [ **Standard_LRS**，但您可以指定其他值或允許傳入做為參數的帳戶類型的使用者。

現在讓我們跳到 [**參數**] 區段中，並查看您定義的儲存體帳戶名稱的方式。 您可以進一步瞭解使用在[參數](resource-group-authoring-templates.md#parameters)的參數。 

```json
"parameters" : {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Storage Account Name"
      }
    }
}
```
這裡所定義的參數會保留儲存體帳戶名稱的字串類型。 範本部署時，將會提供此參數的值。

## <a name="deploying-the-template"></a>部署範本
我們具備完整的範本建立新的儲存空間帳戶。 如您**azuredeploy.json**檔案所儲存的範本︰

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters" : {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Storage Account Name"
      }
    }
  },  
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[parameters('storageAccountName')]",
      "apiVersion": "2015-06-15",
      "location": "[resourceGroup().location]",
      "properties": {
        "accountType": "Standard_LRS"
      }
    }
  ]
}
```

您可以看到[資源部署文章](resource-group-template-deploy.md)有很多部署範本，方法。 若要部署使用 PowerShell 的 Azure 的範本，請使用︰

```powershell
# create a new resource group
New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West Europe"

# deploy the template to the resource group
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile azuredeploy.json
```

或者，若要部署使用 Azure CLI 的範本，請使用︰

```
azure group create -n ExampleResourceGroup -l "West Europe"

azure group deployment create -f azuredeploy.json -g ExampleResourceGroup -n ExampleDeployment
```

您正驕傲儲存帳戶的擁有者 ！

若要新增部署開頭本教學課程中所描述架構所需的所有資源會接下來的步驟。 您會在您已在使用相同的範本中新增這些資源。

## <a name="availability-set"></a>顯示狀態設定
儲存帳戶的定義之後, 新增防範設定虛擬機器。 在此情況下，沒有其他屬性要求，讓它的定義是相當簡單。 請參閱[REST API 建立的顯示狀態設定](https://msdn.microsoft.com/library/azure/mt163607.aspx)為 [完整屬性] 區段中，，以防您想要定義更新網域 count 和故障網域計算值。

```json
{
   "type": "Microsoft.Compute/availabilitySets",
   "name": "[variables('availabilitySetName')]",
   "apiVersion": "2015-06-15",
   "location": "[resourceGroup().location]",
   "properties": {}
}
```

請注意，**名稱**會設定為變數的值。 此範本，請在幾個不同的地方需要可用性集的名稱。 您可以更輕鬆地維護您的範本，一次定義的值，並使用它在多個位置。

您指定**類型**的值包含資源者和資源類型。 可用性組資源提供者**Microsoft.Compute** ，而資源類型會**availabilitySets**。 您可以藉由執行下列動作的 PowerShell 命令取得可用的資源提供者的清單︰

```powershell
    Get-AzureRmResourceProvider -ListAvailable
```

或者，如果您使用的 Azure CLI，您可以執行下列命令︰
```
    azure provider list
```
假設本主題中建立與儲存帳戶、 虛擬機器和虛擬網路，您會使用︰

- Microsoft.Storage
- Microsoft.Compute
- Microsoft.Network

若要查看的資源類型為特定的提供者，請執行下列 PowerShell 命令︰

```powershell
    (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute).ResourceTypes
```

或者，Azure CLI 的下列命令會傳回 JSON 格式可用的類型，並將其儲存為檔案。

```
    azure provider show Microsoft.Compute --json > c:\temp.json
```

您應該會看到**availabilitySets**做為其中一個內**Microsoft.Compute**的類型。 類型的完整名稱是**Microsoft.Compute/availabilitySets**。 您可以判斷您的範本中的資源的資源類型名稱。

## <a name="public-ip"></a>公用 IP
定義的公用 IP 位址。 同樣地，查看您[的公用 IP 位址的 REST API](https://msdn.microsoft.com/library/azure/mt163590.aspx)來設定屬性的。

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "[parameters('publicIPAddressName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "publicIPAllocationMethod": "Dynamic",
    "dnsSettings": {
      "domainNameLabel": "[parameters('dnsNameforLBIP')]"
    }
  }
}
```

設定為 [**動態**配置方法，但您可以將它設定您需要或將其設定為接受參數值] 的值。 您已啟用您的網域名稱標籤中的值傳遞的範本的使用者。

現在，我們來看看您要如何判斷**apiVersion**。 您指定的值只會比對您想要建立資源時，使用 REST API 的版本。 因此，您可以查看該資源類型的 REST API 文件。 或者，您可以執行下列 PowerShell 命令，以特定類型。

```powershell
    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Network).ResourceTypes | Where-Object ResourceTypeName -eq publicIPAddresses).ApiVersions
```
會傳回下列的值︰

    2015-06-15
    2015-05-01-preview
    2014-12-01-preview

Azure CLI API 版本，請執行相同**azure 提供者所顯示**的命令先前所示。

建立新的範本，選擇 [最新的 API 版本]。

## <a name="virtual-network-and-subnet"></a>虛擬網路和子網路
建立一個子網路虛擬網路。 查看[REST API 的虛擬網路](https://msdn.microsoft.com/library/azure/mt163661.aspx)設定的所有內容。

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Network/virtualNetworks",
   "name": "[parameters('vnetName')]",
   "location": "[resourceGroup().location]",
   "properties": {
     "addressSpace": {
       "addressPrefixes": [
         "10.0.0.0/16"
       ]
     },
     "subnets": [
       {
         "name": "[variables('subnetName')]",
         "properties": {
           "addressPrefix": "10.0.0.0/24"
         }
       }
     ]
   }
}
```

## <a name="load-balancer"></a>負載平衡器
現在，您會建立外部具負載平衡器。 由於此負載平衡器會使用的公用 IP 位址，您必須宣告相依性**dependsOn** ] 區段中的公用 IP 位址。 這表示將不取得部署負載平衡器，直到部署完成的公用 IP 位址。 未定義此相依性，您會收到錯誤，因為資源管理員會嘗試部署平行，資源，並嘗試將負載平衡器設定為尚不存在的公用 IP 位址。 

您也會使用此資源定義中的連接埠 80 tcp 探查建立後端位址集區、 幾個 RDP Vm，將輸入的 NAT 規則和負載平衡規則。 簽出[REST API 負載平衡器的](https://msdn.microsoft.com/library/azure/mt163574.aspx)所有內容。

```json
{
   "apiVersion": "2015-06-15",
   "name": "[parameters('lbName')]",
   "type": "Microsoft.Network/loadBalancers",
   "location": "[resourceGroup().location]",
   "dependsOn": [
     "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]"
   ],
   "properties": {
     "frontendIPConfigurations": [
       {
         "name": "LoadBalancerFrontEnd",
         "properties": {
           "publicIPAddress": {
             "id": "[variables('publicIPAddressID')]"
           }
         }
       }
     ],
     "backendAddressPools": [
       {
         "name": "BackendPool1"
       }
     ],
     "inboundNatRules": [
       {
         "name": "RDP-VM0",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "protocol": "tcp",
           "frontendPort": 50001,
           "backendPort": 3389,
           "enableFloatingIP": false
         }
       },
       {
         "name": "RDP-VM1",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "protocol": "tcp",
           "frontendPort": 50002,
           "backendPort": 3389,
           "enableFloatingIP": false
         }
       }
     ],
     "loadBalancingRules": [
       {
         "name": "LBRule",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "backendAddressPool": {
             "id": "[variables('lbPoolID')]"
           },
           "protocol": "tcp",
           "frontendPort": 80,
           "backendPort": 80,
           "enableFloatingIP": false,
           "idleTimeoutInMinutes": 5,
           "probe": {
             "id": "[variables('lbProbeID')]"
           }
         }
       }
     ],
     "probes": [
       {
         "name": "tcpProbe",
         "properties": {
           "protocol": "tcp",
           "port": 80,
           "intervalInSeconds": 5,
           "numberOfProbes": 2
         }
       }
     ]
   }
}
```

## <a name="network-interface"></a>網路介面
您將建立 2 網路介面，針對每個 VM 並列文字格式。 不必包含重複的項目之網路介面，您可以使用[copyIndex() 函數](resource-group-create-multiple.md)逐一查看複製循環播放 （又稱為 nicLoop），並建立數字的網路介面中所定義`numberOfInstances`變數。 網路介面取決於建立虛擬網路和負載平衡器。 它會使用虛擬網路建立，與負載平衡器識別碼中定義的子網路設定的負載平衡器位址集區與輸入的 NAT 規則。
查看您[的網路介面 REST API](https://msdn.microsoft.com/library/azure/mt163668.aspx)的所有內容。

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Network/networkInterfaces",
   "name": "[concat(parameters('nicNamePrefix'), copyindex())]",
   "location": "[resourceGroup().location]",
   "copy": {
     "name": "nicLoop",
     "count": "[variables('numberOfInstances')]"
   },
   "dependsOn": [
     "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]",
     "[concat('Microsoft.Network/loadBalancers/', parameters('lbName'))]"
   ],
   "properties": {
     "ipConfigurations": [
       {
         "name": "ipconfig1",
         "properties": {
           "privateIPAllocationMethod": "Dynamic",
           "subnet": {
             "id": "[variables('subnetRef')]"
           },
           "loadBalancerBackendAddressPools": [
             {
               "id": "[concat(variables('lbID'), '/backendAddressPools/BackendPool1')]"
             }
           ],
           "loadBalancerInboundNatRules": [
             {
               "id": "[concat(variables('lbID'),'/inboundNatRules/RDP-VM', copyindex())]"
             }
           ]
         }
       }
     ]
   }
}
```

## <a name="virtual-machine"></a>虛擬機器
您將建立 2 的虛擬機器中的[網路介面](#network-interface)建立使用 copyIndex() 函數。
儲存帳戶，取決於 VM 建立網路介面和可用的設定。 將從服務商場的圖像，建立這個 VM 中, 所定義`storageProfile`屬性-`imageReference`用來定義圖像 publisher、 優惠、 sku 和版本。 最後，若要啟用診斷 vm 設定診斷的設定檔。 

若要尋找相關的內容服務商場圖像，請[選取 Linux 虛擬機器圖像](./virtual-machines/virtual-machines-linux-cli-ps-findimage.md)或[選取 [Windows 虛擬機器圖像](./virtual-machines/virtual-machines-windows-cli-ps-findimage.md)的文件。

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Compute/virtualMachines",
   "name": "[concat(parameters('vmNamePrefix'), copyindex())]",
   "copy": {
     "name": "virtualMachineLoop",
     "count": "[variables('numberOfInstances')]"
   },
   "location": "[resourceGroup().location]",
   "dependsOn": [
     "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
     "[concat('Microsoft.Network/networkInterfaces/', parameters('nicNamePrefix'), copyindex())]",
     "[concat('Microsoft.Compute/availabilitySets/', variables('availabilitySetName'))]"
   ],
   "properties": {
     "availabilitySet": {
       "id": "[resourceId('Microsoft.Compute/availabilitySets',variables('availabilitySetName'))]"
     },
     "hardwareProfile": {
       "vmSize": "[parameters('vmSize')]"
     },
     "osProfile": {
       "computerName": "[concat(parameters('vmNamePrefix'), copyIndex())]",
       "adminUsername": "[parameters('adminUsername')]",
       "adminPassword": "[parameters('adminPassword')]"
     },
     "storageProfile": {
       "imageReference": {
         "publisher": "[parameters('imagePublisher')]",
         "offer": "[parameters('imageOffer')]",
         "sku": "[parameters('imageSKU')]",
         "version": "latest"
       },
       "osDisk": {
         "name": "osdisk",
         "vhd": {
           "uri": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net/vhds/','osdisk', copyindex(), '.vhd')]"
         },
         "caching": "ReadWrite",
         "createOption": "FromImage"
       }
     },
     "networkProfile": {
       "networkInterfaces": [
         {
           "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'),copyindex()))]"
         }
       ]
     },
     "diagnosticsProfile": {
       "bootDiagnostics": {
          "enabled": "true",
          "storageUri": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net')]"
       }
     }
}
```

>[AZURE.NOTE] 發佈**第 3 廠商所提供**的圖像，您必須指定另一個名為`plan`。 可以在[此範本](https://github.com/Azure/azure-quickstart-templates/tree/master/checkpoint-single-nic)，從 [快速入門] 庫中找到範例。 

您已定義的資源，為您的範本。

## <a name="parameters"></a>參數

在 [參數] 區段中，定義部署範本時，可以指定的值。 僅限定義參數您認為應該不同部署期間的值。 您可以用如果部署期間不提供一個參數提供的預設值。 您也可以定義所允許的值，如下圖所示**imageSKU**參數。

```json
"parameters": {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of storage account"
      }
    },
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Admin username"
      }
    },
    "adminPassword": {
      "type": "securestring",
      "metadata": {
        "description": "Admin password"
      }
    },
    "dnsNameforLBIP": {
      "type": "string",
      "metadata": {
        "description": "DNS for Load Balancer IP"
      }
    },
    "vmNamePrefix": {
      "type": "string",
      "defaultValue": "myVM",
      "metadata": {
        "description": "Prefix to use for VM names"
      }
    },
    "imagePublisher": {
      "type": "string",
      "defaultValue": "MicrosoftWindowsServer",
      "metadata": {
        "description": "Image Publisher"
      }
    },
    "imageOffer": {
      "type": "string",
      "defaultValue": "WindowsServer",
      "metadata": {
        "description": "Image Offer"
      }
    },
    "imageSKU": {
      "type": "string",
      "defaultValue": "2012-R2-Datacenter",
      "allowedValues": [
        "2008-R2-SP1",
        "2012-Datacenter",
        "2012-R2-Datacenter"
      ],
      "metadata": {
        "description": "Image SKU"
      }
    },
    "lbName": {
      "type": "string",
      "defaultValue": "myLB",
      "metadata": {
        "description": "Load Balancer name"
      }
    },
    "nicNamePrefix": {
      "type": "string",
      "defaultValue": "nic",
      "metadata": {
        "description": "Network Interface name prefix"
      }
    },
    "publicIPAddressName": {
      "type": "string",
      "defaultValue": "myPublicIP",
      "metadata": {
        "description": "Public IP Name"
      }
    },
    "vnetName": {
      "type": "string",
      "defaultValue": "myVNET",
      "metadata": {
        "description": "VNET name"
      }
    },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_D1",
      "metadata": {
        "description": "Size of the VM"
      }
    }
  }
```

## <a name="variables"></a>變數

在 [變數] 區段中，您可以定義值會用在一個以上的位置，在您的範本或從其他運算式或變數建構的值。 以簡化您的範本的語法經常使用的變數。

```json
"variables": {
    "availabilitySetName": "myAvSet",
    "subnetName": "Subnet-1",
    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('vnetName'))]",
    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables ('subnetName'))]",
    "publicIPAddressID": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]",
    "numberOfInstances": 2,
    "lbID": "[resourceId('Microsoft.Network/loadBalancers',parameters('lbName'))]",
    "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/LoadBalancerFrontEnd')]",
    "lbPoolID": "[concat(variables('lbID'),'/backendAddressPools/BackendPool1')]",
    "lbProbeID": "[concat(variables('lbID'),'/probes/tcpProbe')]"
  }
```

您已完成範本 ！ 您可以比較您針對[2 Vm 與負載平衡器與負載平衡器規則範本](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-loadbalancer-lbrules)] 下的[快速入門] 庫](https://github.com/Azure/azure-quickstart-templates)中的完整範本的範本。 您的範本可能稍有不同根據使用不同版本的數字。 

您可以重新部署範本，藉由使用部署儲存帳戶時所用的同一個命令。 您不需要重新部署因為資源管理員會略過重新建立的資源已經存在，並沒有改變之前，先刪除儲存的帳戶。

## <a name="next-steps"></a>後續步驟

- [Azure 資源管理員範本視覺化檢視 (ARMViz)](http://armviz.io/#/)是以視覺化方式呈現 ARM 範本的絕佳工具可能會變得太大，了解從讀取 json 檔案。
- 若要進一步瞭解範本的結構，請參閱[撰寫 Azure 資源管理員範本](resource-group-authoring-templates.md)。
- 若要瞭解部署範本，請參閱[部署 Azure 資源管理員範本與資源群組](resource-group-template-deploy.md)
