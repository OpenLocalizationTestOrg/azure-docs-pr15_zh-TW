<properties
   pageTitle="私人金鑰保存庫中的資源管理員範本 |Microsoft Azure"
   description="會顯示資源管理員的結構描述部署透過樣板的金鑰保存庫機密資料。"
   services="azure-resource-manager,key-vault"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/23/2016"
   ms.author="tomfitz"/>

# <a name="key-vault-secret-template-schema"></a>索引鍵保存庫私人範本結構描述

建立索引鍵保存庫中所儲存的密碼。 為子資源的[金鑰保存庫](resource-manager-template-keyvault.md)經常部署此資源類型。

## <a name="schema-format"></a>結構描述格式

若要建立索引鍵保存庫私人，新增下列結構描述至您的範本。 可定義密碼，為其中一個子資源的金鑰保存庫或最上層的資源。 您可以定義為子資源金鑰保存庫部署相同的範本中時。 您必須定義私人設為最上層的資源，當金鑰保存庫不部署在相同的範本，或您需要建立多個機密迴圈資源類型。 

    {
        "type": enum,
        "apiVersion": "2015-06-01",
        "name": string,
        "properties": {
            "value": string
        },
        "dependsOn": [ array values ]
    }

## <a name="values"></a>值

下表描述您要設定結構描述中的值。

| 名稱 | 值 |
| ---- | ---- | 
| 類型 | 列舉<br />所需<br />**機密資料**（當部署為子資源的金鑰保存庫） 或<br /> **Microsoft.KeyVault/vaults/secrets**（當部署設為最上層的資源）<br /><br />若要建立資源類型。 |
| apiVersion | 列舉<br />所需<br />**2015-06-01** ] 或 [ **2014年 12-19 預覽**<br /><br />要用來建立資源 API 版本。 | 
| 名稱 | 字串<br />所需<br />部署為子資源索引鍵的保存庫，或格式時的一個單字**{機碼保存庫-名稱} / {私人名稱}**部署為最上層的資源，以新增至現有的按鍵保存庫。<br /><br />若要建立私人的名稱。 |
| 屬性 | 物件<br />所需<br />[屬性的物件](#properties)<br /><br />指定的值建立私人的物件。 |
| dependsOn | 陣列<br />選擇性<br />逗點分隔資源的清單名稱或資源的唯一識別碼。<br /><br />取決於此連結的資源的集合。 如果密碼的重要保存庫已部署在同一個範本，包含關鍵保存庫的名稱以確保部署第一次此項目。 |

<a id="properties" />
### <a name="properties-object"></a>屬性的物件

| 名稱 | 值 |
| ---- | ---- | 
| 值 | 字串<br />所需<br /><br />在主要保存庫中儲存密碼的值。 當此屬性的傳遞的值，使用參數的類型**securestring**。  |

    
## <a name="examples"></a>範例

第一個範例中為子資源的金鑰保存庫部署密碼。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "keyVaultName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the vault"
                }
            },
            "tenantId": {
                "type": "string",
                "metadata": {
                   "description": "Tenant ID for the subscription and use assigned access to the vault. Available from the Get-AzureRmSubscription PowerShell cmdlet"
                }
            },
            "objectId": {
                "type": "string",
                "metadata": {
                    "description": "Object ID of the AAD user or service principal that will have access to the vault. Available from the Get-AzureRmADUser or the Get-AzureRmADServicePrincipal cmdlets"
                }
            },
            "keysPermissions": {
                "type": "array",
                "defaultValue": [ "all" ],
                "metadata": {
                    "description": "Permissions to grant user to keys in the vault. Valid values are: all, create, import, update, get, list, delete, backup, restore, encrypt, decrypt, wrapkey, unwrapkey, sign, and verify."
                }
            },
            "secretsPermissions": {
                "type": "array",
                "defaultValue": [ "all" ],
                "metadata": {
                    "description": "Permissions to grant user to secrets in the vault. Valid values are: all, get, set, list, and delete."
                }
            },
            "vaultSku": {
                "type": "string",
                "defaultValue": "Standard",
                "allowedValues": [
                    "Standard",
                    "Premium"
                ],
                "metadata": {
                    "description": "SKU for the vault"
                }
            },
            "enabledForDeployment": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for VM or Service Fabric deployment"
                }
            },
            "enabledForTemplateDeployment": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for ARM template deployment"
                }
            },
            "enableVaultForVolumeEncryption": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for volume encryption"
                }
            },
            "secretName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the secret to store in the vault"
                }
            },
            "secretValue": {
                "type": "securestring",
                "metadata": {
                    "description": "Value of the secret to store in the vault"
                }
            }
        },
        "resources": [
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[parameters('keyVaultName')]",
            "apiVersion": "2015-06-01",
            "location": "[resourceGroup().location]",
            "tags": {
                "displayName": "KeyVault"
            },
            "properties": {
                "enabledForDeployment": "[parameters('enabledForDeployment')]",
                "enabledForTemplateDeployment": "[parameters('enabledForTemplateDeployment')]",
                "enabledForVolumeEncryption": "[parameters('enableVaultForVolumeEncryption')]",
                "tenantId": "[parameters('tenantId')]",
                "accessPolicies": [
                {
                    "tenantId": "[parameters('tenantId')]",
                    "objectId": "[parameters('objectId')]",
                    "permissions": {
                        "keys": "[parameters('keysPermissions')]",
                        "secrets": "[parameters('secretsPermissions')]"
                    }
                }],
                "sku": {
                    "name": "[parameters('vaultSku')]",
                    "family": "A"
                }
            },
            "resources": [
            {
                "type": "secrets",
                "name": "[parameters('secretName')]",
                "apiVersion": "2015-06-01",
                "properties": {
                    "value": "[parameters('secretValue')]"
                },
                "dependsOn": [
                    "[concat('Microsoft.KeyVault/vaults/', parameters('keyVaultName'))]"
                ]
            }]
        }]
    }

第二個範例部署密碼設為最上層的資源，儲存在現有的按鍵保存庫。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "keyVaultName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the existing vault"
                }
            },
            "secretName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the secret to store in the vault"
                }
            },
            "secretValue": {
                "type": "securestring",
                "metadata": {
                    "description": "Value of the secret to store in the vault"
                }
            }
        },
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.KeyVault/vaults/secrets",
                "apiVersion": "2015-06-01",
                "name": "[concat(parameters('keyVaultName'), '/', parameters('secretName'))]",
                "properties": {
                    "value": "[parameters('secretValue')]"
                }
            }
        ],
        "outputs": {}
    }


## <a name="next-steps"></a>後續步驟

- 一般金鑰保存庫的詳細資訊，請參閱[開始使用 Azure 金鑰保存庫](./key-vault/key-vault-get-started.md)。
- 部署範本時，請參考金鑰保存庫私人的範例，請參閱[傳遞安全部署期間的值](resource-manager-keyvault-parameter.md)。


