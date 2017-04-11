<properties
   pageTitle="資源管理員金鑰保存庫範本 |Microsoft Azure"
   description="會顯示資源管理員的結構描述部署透過樣板的金鑰保存庫。"
   services="azure-resource-manager,key-vault"
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
   ms.date="06/23/2016"
   ms.author="tomfitz"/>

# <a name="key-vault-template-schema"></a>索引鍵保存庫範本結構描述

建立索引鍵保存庫。

## <a name="schema-format"></a>結構描述格式

若要建立索引鍵保存庫，請新增下列結構描述至您的範本的 [資源] 區段。

    {
        "type": "Microsoft.KeyVault/vaults",
        "apiVersion": "2015-06-01",
        "name": string,
        "location": string,
        "properties": {
            "enabledForDeployment": bool,
            "enabledForTemplateDeployment": bool,
            "enabledForVolumeEncryption": bool,
            "tenantId": string,
            "accessPolicies": [
                {
                    "tenantId": string,
                    "objectId": string,
                    "permissions": {
                        "keys": [ keys permissions ],
                        "secrets": [ secrets permissions ]
                    }
                }
            ],
            "sku": {
                "name": enum,
                "family": "A"
            }
        },
        "resources": [
             child resources
        ]
    }

## <a name="values"></a>值

下表描述您要設定結構描述中的值。

| 名稱 | 值 |
| ---- | ---- | 
| 類型 | 列舉<br />所需<br />**Microsoft.KeyVault/vaults**<br /><br />若要建立資源類型。 |
| apiVersion | 列舉<br />所需<br />**2015-06-01** ] 或 [ **2014年 12-19 預覽**<br /><br />要用來建立資源 API 版本。 | 
| 名稱 | 字串<br />所需<br />可透過 Azure 都是唯一的名稱。<br /><br />若要建立索引鍵的保存庫的名稱。 請考慮使用[uniqueString](resource-group-template-functions.md#uniquestring)函數與您的命名慣例，建立唯一的名稱，在下面的範例所示。 |
| 位置 | 字串<br />所需<br />索引鍵保存庫的有效的區域。 若要判斷有效的區域，請參閱[支援的區域](resource-manager-supported-services.md#supported-regions)。<br /><br />如果要主控金鑰保存庫的區域。 |
| 屬性 | 物件<br />所需<br />[屬性的物件](#properties)<br /><br />指定建立索引鍵保存庫的類型的物件。 |
| 資源 | 陣列<br />選擇性<br />允許值︰[鍵保存庫私人資源](resource-manager-template-keyvault-secret.md)<br /><br />索引鍵保存庫的子資源。 |

<a id="properties" />
### <a name="properties-object"></a>屬性的物件

| 名稱 | 值 |
| ---- | ---- | 
| enabledForDeployment | 布林值<br />選擇性<br />**true**或**false**<br /><br />指定保存庫是否啟用虛擬機器或服務結構的部署。 |
| enabledForTemplateDeployment | 布林值<br />選擇性<br />**true**或**false**<br /><br />指定保存庫是否啟用資源管理員範本部署中使用。 如需詳細資訊，請參閱[傳遞安全部署期間的值](resource-manager-keyvault-parameter.md) |
| enabledForVolumeEncryption | 布林值<br />選擇性<br />**true**或**false**<br /><br />指定保存庫是否啟用大量加密。 |
| tenantId | 字串<br />所需<br />**全域唯一識別碼**<br /><br />訂閱租用戶識別碼。 您可以使用[取得 AzureRmSubscription](https://msdn.microsoft.com/library/azure/mt619284.aspx) PowerShell cmdlet 或**azure 帳戶顯示**Azure CLI] 命令來擷取它。 |
| accessPolicies | 陣列<br />所需<br />[accessPolicies 物件](#accesspolicies)<br /><br />物件的陣列，最多 16，指定使用者或服務主要的權限。 |
| sku | 物件<br />所需<br />[sku 物件](#sku)<br /><br />索引鍵保存庫的 SKU。 |

<a id="accesspolicies" />
### <a name="propertiesaccesspolicies-object"></a>properties.accessPolicies 物件

| 名稱 | 值 |
| ---- | ---- | 
| tenantId | 字串<br />所需<br />**全域唯一識別碼**<br /><br />Azure Active Directory 租用戶包含在此存取原則**objectId**租用戶識別項 |
| objectId | 字串<br />所需<br />**全域唯一識別碼**<br /><br />Azure Active Directory 使用者或都可以存取保存庫的服務主要的物件識別碼。 您可以從[取得 AzureRmADUser](https://msdn.microsoft.com/library/azure/mt679001.aspx)或[取得 AzureRmADServicePrincipal](https://msdn.microsoft.com/library/azure/mt678992.aspx) PowerShell cmdlet 或**azure ad 使用者**或**azure ad 預存程序**的 Azure CLI 命令來擷取的值。 |
| 權限 | 物件<br />所需<br />[物件權限](#permissions)<br /><br />在 Active Directory 物件此保存庫授與權限。 |

<a id="permissions" />
### <a name="propertiesaccesspoliciespermissions-object"></a>properties.accessPolicies.permissions 物件

| 名稱 | 值 |
| ---- | ---- | 
| 索引鍵 | 陣列<br />所需<br />**所有**、**備份**、**建立**、**解密**、**刪除**、**加密**，**取得****匯入**、**清單**、**還原**、**登**、 **unwrapkey**、**更新**、**驗證**、 **wrapkey**<br /><br />在 Active Directory 物件此保存庫中的按鍵授與權限。 此值必須指定做為陣列的一或多個允許的值。 |
| 機密資料 | 陣列<br />所需<br />**所有**、**刪除**、**取得****清單**、**設定**<br /><br />在 Active Directory 物件此保存庫中的機密授與權限。 此值必須指定做為陣列的一或多個允許的值。 |

<a id="sku" />
### <a name="propertiessku-object"></a>properties.sku 物件

| 名稱 | 值 |
| ---- | ---- | 
| 名稱 | 列舉<br />所需<br />**標準**，或**進階版** <br /><br />若要使用的 KeyVault 服務層級。  標準支援機密資料與軟體保護的索引鍵。  進階版加入支援 HSM 受保護的按鍵。 |
| 家庭 | 列舉<br />所需<br />**A** <br /><br />若要使用 sku 系列。 |
 
    
## <a name="examples"></a>範例

下列範例會部署的金鑰保存庫和密碼。

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

## <a name="quickstart-templates"></a>快速入門範本

下列的快速入門範本部署金鑰保存庫。

- [建立索引鍵保存庫](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)


## <a name="next-steps"></a>後續步驟

- 一般金鑰保存庫的詳細資訊，請參閱[開始使用 Azure 金鑰保存庫](./key-vault/key-vault-get-started.md)。
- 部署範本時，請參考金鑰保存庫私人的範例，請參閱[傳遞安全部署期間的值](resource-manager-keyvault-parameter.md)。

