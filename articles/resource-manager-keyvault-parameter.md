<properties
   pageTitle="使用資源管理員範本的金鑰保存庫私人 |Microsoft Azure"
   description="顯示如何在部署期間做為參數傳遞私人從主要的保存庫。"
   services="azure-resource-manager,key-vault"
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
   ms.date="06/23/2016"
   ms.author="tomfitz"/>

# <a name="pass-secure-values-during-deployment"></a>在 [部署期間傳遞安全的值

當您需要將安全值 （例如密碼） 做為參數傳遞部署時，您可以將該值儲存為[Azure 金鑰保存庫](./key-vault/key-vault-whatis.md)中的密碼，並參照其他資源管理員範本中的值。 您只私人的參考中包含您的範本，以便私人絕不會公開，並不需要手動輸入的值私人部署資源每次。 您可以指定哪些使用者或服務原則可以存取的機密。  

## <a name="deploy-a-key-vault-and-secret"></a>部署的金鑰保存庫和密碼

若要建立可以從其他資源管理員範本參照的重要保存庫，您必須為**true**，設定**enabledForTemplateDeployment**屬性，您必須授與存取權的使用者或服務主要執行部署參考密碼。

若要瞭解部署的金鑰保存庫和密碼，請參閱[金鑰保存庫結構描述](resource-manager-template-keyvault.md)和[鍵保存庫私人結構描述](resource-manager-template-keyvault-secret.md)。

## <a name="reference-a-secret-with-static-id"></a>參照靜態識別碼密碼

您參考從將值傳遞給您的範本的參數檔案中的密碼。 您可以參考私人傳遞的金鑰保存庫資源識別碼] 和 [機密的名稱。 在此範例中，必須已經存在的金鑰保存庫密碼，並您正在使用靜態值，資源識別碼。

    "parameters": {
      "adminPassword": {
        "reference": {
          "keyVault": {
            "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
          }, 
          "secretName": "sqlAdminPassword" 
        } 
      }
    }

整個參數檔案可能看起來像︰

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "sqlsvrAdminLogin": {
          "value": ""
        },
        "sqlsvrAdminLoginPassword": {
          "reference": {
            "keyVault": {
              "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
            },
            "secretName": "adminPassword"
          }
        }
      }
    }

接受私人參數應該**securestring**。 下列範例顯示的部署 SQL server 需要系統管理員的密碼的範本的相關章節。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "sqlsvrAdminLogin": {
                "type": "string",
                "minLength": 4
            },
            "sqlsvrAdminLoginPassword": {
                "type": "securestring"
            },
            ...
        },
        "resources": [
        {
            "name": "[variables('sqlsvrName')]",
            "type": "Microsoft.Sql/servers",
            "location": "[resourceGroup().location]",
            "apiVersion": "2014-04-01-preview",
            "properties": {
                "administratorLogin": "[parameters('sqlsvrAdminLogin')]",
                "administratorLoginPassword": "[parameters('sqlsvrAdminLoginPassword')]"
            },
            ...
        }],
        "variables": {
            "sqlsvrName": "[concat('sqlsvr', uniqueString(resourceGroup().id))]"
        },
        "outputs": { }
    }

## <a name="reference-a-secret-with-dynamic-id"></a>參照動態識別碼密碼

顯示如何傳遞金鑰保存庫私人的靜態資源 id 前一節。 不過，在某些情況下，您需要參考不同根據目前的部署金鑰保存庫密碼。 在此情況下，您無法硬式編碼參數檔案中的資源識別碼。 很抱歉，您無法以動態方式資源識別碼檔案中產生參數因為範本的運算式不允許參數檔案中。

動態產生金鑰保存庫私人的資源識別碼，您必須移動的資源，需要密碼到巢狀的範本。 在主版範本中，您可以新增的巢狀的範本及傳遞的參數，包含動態產生的資源識別碼。

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "vaultName": {
          "type": "string"
        },
        "secretName": {
          "type": "string"
        }
      },
      "resources": [
        {
          "apiVersion": "2015-01-01",
          "name": "nestedTemplate",
          "type": "Microsoft.Resources/deployments",
          "properties": {
            "mode": "incremental",
            "templateLink": {
              "uri": "https://www.contoso.com/AzureTemplates/newVM.json",
              "contentVersion": "1.0.0.0"
            },
            "parameters": {
              "adminPassword": {
                "reference": {
                  "keyVault": {
                    "id": "[concat(resourceGroup().id, '/providers/Microsoft.KeyVault/vaults/', parameters('vaultName'))]"
                  },
                  "secretName": "[parameters('secretName')]"
                }
              }
            }
          }
        }
      ],
      "outputs": {}
    }


## <a name="next-steps"></a>後續步驟

- 一般金鑰保存庫的詳細資訊，請參閱[開始使用 Azure 金鑰保存庫](./key-vault/key-vault-get-started.md)。
- 有關如何使用虛擬機器中的關鍵保存庫，請參閱[Azure 資源管理員的安全性考量](best-practices-resource-manager-security.md)。
- 完成範例參照索引鍵的機密資料的詳細資訊，請參閱[金鑰保存庫範例](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)。

