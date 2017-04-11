<properties
   pageTitle="部署資源的多個執行個體 |Microsoft Azure"
   description="若要重複使用複製作業和陣列 Azure 資源管理員範本中多次時部署資源。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/30/2016"
   ms.author="tomfitz"/>

# <a name="create-multiple-instances-of-resources-in-azure-resource-manager"></a>建立資源的多個執行個體中 Azure 資源管理員

本主題說明如何建立多個執行個體的資源在 Azure 資源管理員範本中重複。

## <a name="copy-copyindex-and-length"></a>複製、 copyIndex 和長度

若要建立多次資源，內，您可以定義**複製**指定的物件，若要重複的次數。 複製採用下列格式︰

    "copy": { 
        "name": "websitescopy", 
        "count": "[parameters('count')]" 
    } 

您可以存取目前反覆運算值**copyIndex()**函數，，例如 concat 函數內，如下所示。

    [concat('examplecopy-', copyIndex())]

在建立多個資源從值的陣列，您可以使用**長度**函數來指定計數。 提供陣列的長度函數的參數。

    "copy": {
        "name": "websitescopy",
        "count": "[length(parameters('siteNames'))]"
    }

## <a name="use-index-value-in-name"></a>在名稱中使用索引值

您可以使用複製作業建立的唯一名稱為根據遞增索引資源的多個執行個體。 例如，您可能要新增唯一的數字部署的每個資源名稱的結尾。 若要部署命名的三個網站︰

- examplecopy 0
- examplecopy-1
- examplecopy 為 2。

使用下列範本︰

    "parameters": { 
      "count": { 
        "type": "int", 
        "defaultValue": 3 
      } 
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', copyIndex())]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2015-08-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[parameters('count')]" 
          }, 
          "properties": {
              "serverFarmId": "hostingPlanName"
          }
      } 
    ]

## <a name="offset-index-value"></a>位移的索引值

您會發現在先前範例的索引值會從 0 到 2。 若要位移索引值，您可以傳送**copyIndex()**函數，例如**copyIndex(1)**中的值。 執行重複次數仍指定中複製項目，但卻 copyIndex 的值是指定的值。 因此前, 一個範例中，使用相同的範本，但指定**copyIndex(1)**部署命名的三個網站︰

- examplecopy-1
- examplecopy 2
- examplecopy-3

## <a name="use-copy-with-array"></a>使用陣列的複本
   
使用陣列，因為您可以逐一查看每個陣列中的項目時，[複製] 作業是特別有用。 若要部署命名的三個網站︰

- examplecopy Contoso
- examplecopy Fabrikam
- examplecopy Coho

使用下列範本︰

    "parameters": { 
      "org": { 
         "type": "array", 
             "defaultValue": [ 
             "Contoso", 
             "Fabrikam", 
             "Coho" 
          ] 
      }
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', parameters('org')[copyIndex()])]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2015-08-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[length(parameters('org'))]" 
          }, 
          "properties": {
              "serverFarmId": "hostingPlanName"
          } 
      } 
    ]

當然，您可以將複製計數為陣列的長度以外的值。 例如，您可能會建立具有多個值的陣列，然後將中指定的參數值多少部署的陣列項目。 在此情況下，您將複製計算第一個範例所示。 

## <a name="depending-on-resources-in-a-loop"></a>根據迴圈中的資源

您可以指定另一個資源後部署資源，使用**dependsOn**項目。 當您需要部署資源的循環資源的集合而定時，您可以使用提供的**dependsOn**項目中的複製循環播放的名稱。 下列範例會示範如何部署虛擬機器之前，先部署 3 儲存的帳戶。 不會顯示完整的虛擬機器定義。 請注意複製項目具有設定為**storagecopy**的**名稱**，而虛擬機器的**dependsOn**項目也會設定為 [ **storagecopy**。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "resources": [
            {
                "apiVersion": "2015-06-15",
                "type": "Microsoft.Storage/storageAccounts",
                "name": "[concat('storage', uniqueString(resourceGroup().id), copyIndex())]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "accountType": "Standard_LRS"
                 },
                "copy": { 
                    "name": "storagecopy", 
                    "count": 3 
                }
            },
           {
               "apiVersion": "2015-06-15", 
               "type": "Microsoft.Compute/virtualMachines", 
               "name": "[concat('VM', uniqueString(resourceGroup().id))]",  
               "dependsOn": ["storagecopy"],
               ...
           }
        ],
        "outputs": {}
    }

## <a name="looping-on-a-nested-resource"></a>巢狀資源迴圈

您無法使用 [複製迴圈巢狀的資源。 如果您需要建立多個執行個體，您通常是定義為另一個資源內巢狀的資源，您就必須改為建立最上層的資源，為資源，並定義與上層資源透過**類型**] 及 [**名稱**屬性的關係。

例如，假設您通常定義資料集內的資料工廠的巢狀資源為。

    "parameters": {
        "dataFactoryName": {
            "type": "string"
         },
         "dataSetName": {
            "type": "string"
         }
    },
    "resources": [
    {
        "type": "Microsoft.DataFactory/datafactories",
        "name": "[parameters('dataFactoryName')]",
        ...
        "resources": [
        {
            "type": "datasets",
            "name": "[parameters('dataSetName')]",
            "dependsOn": [
                "[parameters('dataFactoryName')]"
            ],
            ...
        }
    }]
    
若要建立的資料集的多個執行個體，您想要變更您的範本，如下所示。 完整完整的通知類型以及名稱中包含資料工廠名稱。

    "parameters": {
        "dataFactoryName": {
            "type": "string"
         },
         "dataSetName": {
            "type": "array"
         }
    },
    "resources": [
    {
        "type": "Microsoft.DataFactory/datafactories",
        "name": "[parameters('dataFactoryName')]",
        ...
    },
    {
        "type": "Microsoft.DataFactory/datafactories/datasets",
        "name": "[concat(parameters('dataFactoryName'), '/', parameters('dataSetName')[copyIndex()])]",
        "dependsOn": [
            "[parameters('dataFactoryName')]"
        ],
        "copy": { 
            "name": "datasetcopy", 
            "count": "[length(parameters('dataSetName'))]" 
        } 
        ...
    }]

## <a name="create-multiple-instances-when-copy-wont-work"></a>建立多個執行個體，當複製無法運作

資源類型，而非資源類型中的內容，您可以只使用**複製**。 這可能會想要建立多個執行個體的內容是資源時，為您建立問題。 建立多個資料磁碟虛擬機器是最常見的案例。 您無法使用**複製**資料磁碟，因為**dataDisks**虛擬機器，而不是自己資源類型的屬性。 不過，您可以建立陣列任意數目的資料磁碟當您將需要及傳入的磁碟資料來建立的實際次數。 在虛擬機器定義中，您可以使用**採取**函數從陣列取得您確實想要的項目數目。

此模式完整的範例會顯示在 [[建立動態的選取範圍的資料磁碟 VM](https://azure.microsoft.com/documentation/templates/201-vm-dynamic-data-disks-selection/)範本。

部署範本的相關的章節如下所示。 已經移除範本許多節動態建立資料磁碟的數字的反白顯示。 請注意可以讓您在磁碟來建立數傳遞的參數**numDataDisks** 。 

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...
    "numDataDisks": {
      "type": "int",
      "maxValue": 64,
      "metadata": {
        "description": "This parameter allows you to select the number of disks you want"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'dynamicdisk')]",
    "sizeOfDataDisksInGB": 100,
    "diskCaching": "ReadWrite",
    "diskArray": [
      {
        "name": "datadisk1",
        "lun": 0,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk1.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk2",
        "lun": 1,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk2.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk3",
        "lun": 2,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk3.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk4",
        "lun": 3,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk4.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      ...
      {
        "name": "datadisk63",
        "lun": 62,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk63.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk64",
        "lun": 63,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk64.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      }
    ]
  },
  "resources": [
    ...
    {
      "type": "Microsoft.Compute/virtualMachines",
      "properties": {
        ...
        "storageProfile": {
          ...
          "dataDisks": "[take(variables('diskArray'),parameters('numDataDisks'))]"
        },
        ...
      }
      ...
    }
  ]
}
```


## <a name="next-steps"></a>後續步驟
- 如果您想要深入了解各節的範本，請參閱[撰寫 Azure 資源管理員範本](./resource-group-authoring-templates.md)。
- 針對您所有的範本中，您可以使用的函數，請參閱[Azure 資源管理員範本函數](./resource-group-template-functions.md)。
- 若要瞭解如何在部署您的範本，請參閱[部署具有 Azure 資源管理員範本的應用程式](resource-group-template-deploy.md)。
