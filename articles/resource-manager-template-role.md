<properties
   pageTitle="資源管理員角色指派範本 |Microsoft Azure"
   description="會顯示資源管理員的結構描述部署角色指派到範本。"
   services="azure-resource-manager"
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
   ms.date="10/03/2016"
   ms.author="tomfitz"/>

# <a name="role-assignments-template-schema"></a>角色指派範本結構描述

若要在指定的範圍的角色指派使用者、 群組或服務主要。

## <a name="resource-format"></a>資源格式

若要建立角色指派，請新增下列結構描述至您的範本的 [資源] 區段。
    
    {
        "type": string,
        "apiVersion": "2015-07-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "roleDefinitionId": string,
            "principalId": string,
            "scope": string
        }
    }

## <a name="values"></a>值

下表描述您要設定結構描述中的值。

| 名稱 | 所需 | 描述 |
| ---- | -------- | ----------- |
| 類型 | [是]    | 若要建立資源類型。<br /><br /> 資源群組︰<br />**Microsoft.Authorization/roleAssignments**<br /><br />資源︰<br />**{者命名空間} / {資源類型} / 提供者/roleAssignments** |
| apiVersion |[是] | 要用來建立資源 API 版本。<br /><br /> 使用**2015年-07-01**。 | 
| 名稱 | [是] | 新的角色指派全域唯一識別碼。 |
| dependsOn | 無 | 以逗號分隔的陣列資源的名稱或資源的唯一識別碼。<br /><br />取決於此角色指派資源的集合。 如果指派的角色的範圍資源與資源部署在相同的範本，請此項目，以確保第一次部署資源中包含該資源名稱。 |
| 屬性 | [是] | 識別角色定義、 本金，以及範圍屬性物件。 |

### <a name="properties-object"></a>屬性的物件

| 名稱 | 所需 | 描述 |
| ---- | -------- | ----------- |
| roleDefinitionId | [是] |  現有的角色定義来用於角色指派的識別碼。<br /><br /> 使用下列格式︰<br /> **/ subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}** |
| principalId | [是] | 現有的本金全域唯一識別碼。 此值對應至目錄內的識別碼，並指向使用者、 服務主要或安全性群組。 |
| 範圍 | 無 | 此角色指派適用於範圍。<br /><br />資源群組，請使用︰<br />**{訂閱識別碼} /subscriptions/ /resourceGroups/ {資源的群組名稱的}**  <br /><br />資源，請使用︰<br />**/ subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}** |  |


## <a name="how-to-use-the-role-assignment-resource"></a>如何使用角色工作分派資源

當您需要新增使用者、 群組或服務本金至角色部署時，您可以新增至您的範本的角色指派。 角色指派繼承自較高層級的範圍，因此如果您已新增一個主體訂閱層級的角色，您不需要重新資源群組或資源。

有許多您需要提供時使用的角色指派的識別碼值。 您可以擷取透過 PowerShell 或 Azure CLI 的值。

### <a name="powershell"></a>PowerShell

角色指派的名稱會需要的全域唯一識別碼。 您可以產生新的識別碼**名稱**︰

    $name = [System.Guid]::NewGuid().toString()

您可以擷取角色定義的識別碼︰

    $roledef = (Get-AzureRmRoleDefinition -Name Reader).id

您可以使用下列命令的主體擷取的識別碼。

名為**員**群組︰

    $principal = (Get-AzureRmADGroup -SearchString Auditors).id

名為**exampleperson**使用者︰

    $principal = (Get-AzureRmADUser -SearchString exampleperson).id

服務的主體名稱為**exampleapp**:

    $principal = (Get-AzureRmADServicePrincipal -SearchString exampleapp).id 

### <a name="azure-cli"></a>Azure CLI

您可以擷取角色定義的識別碼︰

    azure role show Reader --json | jq .[].Id -r

您可以使用下列命令的主體擷取的識別碼。

名為**員**群組︰

    azure ad group show --search Auditors --json | jq .[].objectId -r

名為**exampleperson**使用者︰

    azure ad user show --search exampleperson --json | jq .[].objectId -r

服務的主體名稱為**exampleapp**:

    azure ad sp show --search exampleapp --json | jq .[].objectId -r

## <a name="examples"></a>範例

角色的識別碼以及使用者、 群組或服務主要的識別碼，會收到下列範本。 指派資源群組層級的角色。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "roleDefinitionId": {
                "type": "string"
            },
            "roleAssignmentId": {
                "type": "string"
            },
            "principalId": {
                "type": "string"
            }
        },
        "resources": [
            {
              "type": "Microsoft.Authorization/roleAssignments",
              "apiVersion": "2015-07-01",
              "name": "[parameters('roleAssignmentId')]",
              "properties":
              {
                "roleDefinitionId": "[concat(subscription().id, '/providers/Microsoft.Authorization/roleDefinitions/', parameters('roleDefinitionId'))]",
                "principalId": "[parameters('principalId')]",
                "scope": "[concat(subscription().id, '/resourceGroups/', resourceGroup().name)]"
              }
            }
        ],
        "outputs": {}
    }



下一個範本建立的儲存空間帳戶，並將讀取者角色指派給儲存帳戶。 已經包含兩個群組與讀取者角色的識別項在範本，以簡化部署。 這些值，可以擷取次部署透過指令碼中，做為參數傳遞中。

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "roleName": {
          "type": "string"
        },
        "groupToAssign": {
          "type": "string",
          "allowedValues": [
            "Auditors",
            "Limited"
          ]
        }
      },
      "variables": {
        "readerRole": "[concat('/subscriptions/',subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
        "storageName": "[concat('storage', uniqueString(resourceGroup().id))]",
        "scope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Storage/storageAccounts/', variables('storageName'))]",
        "Auditors": "1c272299-9729-462a-8d52-7efe5ece0c5c",
        "Limited": "7c7250f0-7952-441c-99ce-40de5e3e30b5",
        "fullRoleName": "[concat(variables('storageName'), '/Microsoft.Authorization/', parameters('roleName'))]"
      },
      "resources": [
        {
          "apiVersion": "2016-01-01",
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[variables('storageName')]",
          "location": "[resourceGroup().location]",
          "sku": {
            "name": "Standard_LRS"
          },
          "kind": "Storage",
          "tags": {
            "displayName": "MyStorageAccount"
          },
          "properties": {}
        },
        {
          "type": "Microsoft.Storage/storageAccounts/providers/roleAssignments",
          "apiVersion": "2015-07-01",
          "name": "[variables('fullRoleName')]",
          "dependsOn": [
            "[variables('storageName')]"
          ],
          "properties": {
            "roleDefinitionId": "[variables('readerRole')]",
            "principalId": "[variables(parameters('groupToAssign'))]"
          }
        }
      ]
    }

## <a name="quickstart-templates"></a>快速入門範本

下列範本會顯示如何使用角色指派資源︰

- [內建的角色指派給資源群組](https://azure.microsoft.com/documentation/templates/101-rbac-builtinrole-resourcegroup)
- [內建的角色指派給現有 VM](https://azure.microsoft.com/documentation/templates/101-rbac-builtinrole-virtualmachine)
- [內建的角色指派給多個現有 Vm](https://azure.microsoft.com/documentation/templates/201-rbac-builtinrole-multipleVMs)

## <a name="next-steps"></a>後續步驟

- 範本結構的相關資訊，請參閱[撰寫 Azure 資源管理員範本](resource-group-authoring-templates.md)。
- 如需有關角色型存取控制的詳細資訊，請參閱[Azure Active Directory 角色型存取控制](./active-directory/role-based-access-control-configure.md)。
