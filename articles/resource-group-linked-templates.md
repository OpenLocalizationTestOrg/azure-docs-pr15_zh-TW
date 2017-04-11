<properties
   pageTitle="連結範本與資源管理員 |Microsoft Azure"
   description="說明如何使用 Azure 資源管理員範本中的連結的範本建立的模組化範本解決方案。 顯示如何傳遞的參數值，指定參數的檔案和動態建立的 Url。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/02/2016"
   ms.author="tomfitz"/>

# <a name="using-linked-templates-with-azure-resource-manager"></a>使用連結的範本與 Azure 資源管理員

從內 Azure 資源管理員範本，您可以連結至另一個範本，可讓您分解成一組部署目標，特定用途的範本。 使用分解應用程式，將多個程式碼類別，分解優點測試、 重複使用及可讀性。  

您可以將從主範本的參數傳遞至連結的範本，且這些參數可以直接對應至參數或變數公開呼叫的範本。 連結的範本也可以將傳遞輸出變數回到來源範本，讓雙向運算之間交換範本。

## <a name="linking-to-a-template"></a>連結至範本

您建立兩個藉由新增部署資源中的主要的範本，指向 [連結的範本的範本之間的連結。 您可以設定**templateLink**屬性的連結的範本 uri。 您可以提供參數值的連結的範本或連結至參數檔案來指定直接在您的範本中的值。 下列範例會使用 [**參數**] 屬性來直接指定參數值。

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "linkedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri": "https://www.contoso.com/AzureTemplates/newStorageAccount.json",
              "contentVersion": "1.0.0.0"
           }, 
           "parameters": { 
              "StorageAccountName":{"value": "[parameters('StorageAccountName')]"} 
           } 
         } 
      } 
    ] 

資源管理員服務必須能夠存取連結的範本。 您無法指定的本機檔案或僅提供的連結的範本區域網路上的檔案。 您可以只提供 URI 值，其中包含**http**或**https**。 儲存帳戶，將連結的範本，並使用 URI 該項目的的其中一個選項是，如下列範例所示。

    "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0",
    }

雖然必須使用外部連結的範本，它不需要通常可在公開。 您可以新增您的範本，只有儲存帳戶擁有者可以存取私人儲存帳戶。 然後，您會建立一個共用的存取簽章 (SA) 權杖部署期間啟用存取。 您可以新增該 SA 權杖 uri 連結的範本。 設定儲存帳戶中的範本，並產生 SA 權杖步驟，請參閱[部署資源與資源管理員範本與 PowerShell 的 Azure](resource-group-template-deploy.md)或[部署資源與資源管理員範本和 Azure CLI](resource-group-template-deploy-cli.md)。 

下列範例會顯示為父範本連結到另一個範本。 連結的範本是以傳遞的參數為 SA 權杖進行存取。

    "parameters": {
        "sasToken": { "type": "securestring" }
    },
    "resources": [
        {
            "apiVersion": "2015-01-01",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
            "properties": {
              "mode": "incremental",
              "templateLink": {
                "uri": "[concat('https://storagecontosotemplates.blob.core.windows.net/templates/helloworld.json', parameters('sasToken'))]",
                "contentVersion": "1.0.0.0"
              }
            }
        }
    ],

即使權杖會被安全的字串，該資源群組的部署作業會記錄的連結的範本，包括 SA 權杖，URI。 若要限制曝光度，請設定到期日的 token。

## <a name="linking-to-a-parameter-file"></a>連結至參數檔案

下一個範例使用**parametersLink**屬性來連結參數檔案。

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "linkedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri":"https://www.contoso.com/AzureTemplates/newStorageAccount.json",
              "contentVersion":"1.0.0.0"
           }, 
           "parametersLink": { 
              "uri":"https://www.contoso.com/AzureTemplates/parameters.json",
              "contentVersion":"1.0.0.0"
           } 
         } 
      } 
    ] 

連結的參數檔案的 URI 值和不是本機的檔案，必須包含**http**或**https**。 參數檔案也可以透過 SA 權杖存取限制。

## <a name="using-variables-to-link-templates"></a>若要連結的範本中使用的變數

前述範例顯示範本連結的硬式編碼 URL 值。 這種方式可能會使用簡單的範本，但無法運作時使用大量的模組化的範本。 不過，您可以建立一個儲存基底 URL 的主要範本的靜態變數，然後再連結的範本，該基底 URL 的動態建立 Url。 這種方法的優點是，您可以輕鬆地移動，或分叉範本，因為您只需要變更主要的範本中的靜態變數。 主要範本傳遞正確 Uri 整個分解範本。

下列範例會示範如何使用基底 URL 建立兩個連結的範本 （**sharedTemplateUrl**和**vmTemplate**） 的 Url。 

    "variables": {
        "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
        "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
        "tshirtSizeSmall": {
            "vmSize": "Standard_A1",
            "diskSize": 1023,
            "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
            "vmCount": 2,
            "slaveCount": 1,
            "storage": {
                "name": "[parameters('storageAccountNamePrefix')]",
                "count": 1,
                "pool": "db",
                "map": [0,0],
                "jumpbox": 0
            }
        }
    }

您也可以使用[deployment()](resource-group-template-functions.md#deployment)基底 URL 取得目前的範本，並使用的同一個位置中取得其他範本的 URL。 如果您的範本位置變更 （可能是因為版本設定），或您想要避免硬式編碼中的範本檔案的 Url，此方法很有用。 

    "variables": {
        "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
    }

## <a name="conditionally-linking-to-templates"></a>條件化連結至範本

透過傳入參數值用來建構 URI 連結的範本，您可以連結至不同的範本。 您要連結的範本以使用部署期間所指定時，此方法均會運作。 例如，您可以指定一個範本，以使用現有的儲存空間帳戶，並使用新的儲存空間帳戶的其他範本。

下列範例顯示的參數，儲存體帳戶名稱，以及指定儲存帳戶是新的或現有的參數。

    "parameters": {
        "storageAccountName": {
            "type": "String"
        },
        "newOrExisting": {
            "type": "String",
            "allowedValues": [
                "new",
                "existing"
            ]
        }
    },

您建立的範本 URI，包括新的或現有的參數值的變數。

    "variables": {
        "templatelink": "[concat('https://raw.githubusercontent.com/exampleuser/templates/master/',parameters('newOrExisting'),'StorageAccount.json')]"
    },

提供部署資源中的變數的值。

    "resources": [
        {
            "apiVersion": "2015-01-01",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "incremental",
                "templateLink": {
                    "uri": "[variables('templatelink')]",
                    "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "StorageAccountName": {
                        "value": "[parameters('storageAccountName')]"
                    }
                }
            }
        }
    ],

URI 解析到範本命名**existingStorageAccount.json**或**newStorageAccount.json**。 建立這些 Uri 的範本。

下列範例顯示**existingStorageAccount.json**範本。

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "storageAccountName": {
          "type": "String"
        }
      },
      "variables": {},
      "resources": [],
      "outputs": {
        "storageAccountInfo": {
          "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')),providers('Microsoft.Storage', 'storageAccounts').apiVersions[0])]",
          "type" : "object"
        }
      }
    }

下一個範例**newStorageAccount.json**範本。 請注意，現有的儲存空間等帳戶範本儲存帳戶物件會傳回輸出中。 主版範本搭配其中一個連結的範本。

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "storageAccountName": {
          "type": "string"
        }
      },
      "resources": [
        {
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[parameters('StorageAccountName')]",
          "apiVersion": "2016-01-01",
          "location": "[resourceGroup().location]",
          "sku": {
            "name": "Standard_LRS"
          },
          "kind": "Storage",
          "properties": {
          }
        }
      ],
      "outputs": {
        "storageAccountInfo": {
          "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('StorageAccountName')),providers('Microsoft.Storage', 'storageAccounts').apiVersions[0])]",
          "type" : "object"
        }
      }
    }

## <a name="complete-example"></a>完成的範例

下列範例範本顯示簡化的排列的連結的範本說明幾個本文中的概念。 假設範本已新增至相同的容器儲存帳戶已關閉的公開存取。 連結的範本傳遞回到主範本**輸出**] 區段中的值。

**Parent.json**檔案所組成︰

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "containerSasToken": { "type": "string" }
      },
      "resources": [
        {
          "apiVersion": "2015-01-01",
          "name": "linkedTemplate",
          "type": "Microsoft.Resources/deployments",
          "properties": {
            "mode": "incremental",
            "templateLink": {
              "uri": "[concat(uri(deployment().properties.templateLink.uri, 'helloworld.json'), parameters('containerSasToken'))]",
              "contentVersion": "1.0.0.0"
            }
          }
        }
      ],
      "outputs": {
        "result": {
          "type": "object",
          "value": "[reference('linkedTemplate').outputs.result]"
        }
      }
    }

**Helloworld.json**檔案所組成︰

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "variables": {},
      "resources": [],
      "outputs": {
        "result": {
            "value": "Hello World",
            "type" : "string"
        }
      }
    }
    
在 PowerShell 您取得容器的權杖，並部署範本︰

    Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
    $token = New-AzureStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
    New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ("https://storagecontosotemplates.blob.core.windows.net/templates/parent.json" + $token) -containerSasToken $token

Azure CLI 中您可以取得容器的權杖及部署下列程式碼的範本。 目前，您必須提供的名稱，以供部署時使用範本包含 SA 權杖 URI。  

    expiretime=$(date -I'minutes' --date "+30 minutes")  
    azure storage container sas create --container templates --permissions r --expiry $expiretime --json | jq ".sas" -r
    azure group deployment create -g ExampleGroup --template-uri "https://storagecontosotemplates.blob.core.windows.net/templates/parent.json?{token}" -n tokendeploy  

系統會提示您提供 SA 權杖，做為參數。 您需要開頭為權杖**？**。

## <a name="next-steps"></a>後續步驟
- 若要瞭解定義您的資源的部署順序，請參閱[定義 Azure 資源管理員範本中的相依性](resource-group-define-dependencies.md)
- 若要瞭解如何定義某個資源，但建立多個執行個體，請參閱[建立資源 Azure 資源管理員] 中的多個執行個體](resource-group-create-multiple.md)
