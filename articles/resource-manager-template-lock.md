<properties
   pageTitle="資源管理員資源鎖定範本 |Microsoft Azure"
   description="顯示部署資源鎖定透過樣板的資源管理員結構描述。"
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

# <a name="resource-locks-template-schema"></a>資源鎖定範本結構描述

建立鎖定資源和其子資源。

## <a name="schema-format"></a>結構描述格式

若要建立鎖定，請新增下列結構描述至您的範本的 [資源] 區段。
    
    {
        "type": enum,
        "apiVersion": "2015-01-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "level": enum,
            "notes": string
        }
    }



## <a name="values"></a>值

下表描述您要設定結構描述中的值。

| 名稱 | 所需 | 描述 |
| ---- | -------- | ----------- |
| 類型 | [是] | 若要建立資源類型。<br /><br />資源︰<br />**{命名空間} {輸入} / 提供者/鎖定 /**<br /><br/>資源群組︰<br />**Microsoft.Authorization/locks** |
| apiVersion | [是] | 要用來建立資源 API 版本。<br /><br />使用︰<br />**2015-01-01**<br /><br /> |
| 名稱 | [是] | 指定要鎖定的資源] 和 [鎖定名稱的值。 可以多達 64 個字元，而且不能包含 <>，%，與，？，或任何控制字元。<br /><br />資源︰<br />**{resource}/Microsoft.Authorization/{lockname}**<br /><br />資源群組︰<br />**{lockname}** |
| dependsOn | 無 | 逗點分隔資源的清單名稱或資源的唯一識別碼。<br /><br />這個鎖定而定的資源的集合。 如果您要鎖定的資源已部署在相同的範本，請此項目，以確保第一次部署資源中包含該資源名稱。 | 
| 屬性 | [是] | 可識別類型鎖定及鎖定的相關附註的物件。<br /><br />請參閱[屬性的物件](#properties-object)。 |  

### <a name="properties-object"></a>屬性的物件

| 名稱 | 所需 | 描述 |
| ---- | -------- | ----------- |
| 層級   | [是] | 若要套用至範圍鎖定類型。<br /><br />**CannotDelete** -使用者可以修改資源，但不是將其刪除。<br />**唯讀**-使用者可讀取資源，但是他們無法將它刪除，或執行任何動作。 |
| 備忘稿   | 無 | 鎖定的描述。 可以有最多 512 個字元。 |


## <a name="how-to-use-the-lock-resource"></a>如何使用鎖定資源

您可以新增此資源至您的範本，以防止資源上指定的動作。 鎖定適用於所有使用者和群組。

若要建立或刪除管理鎖定，您必須具備的存取權**Microsoft.Authorization/** *或* *Microsoft.Authorization/locks/* **動作。內建的角色，只**擁有者**和**使用者存取系統管理員 * * 授與這些動作。 如需角色型存取控制相關資訊，請參閱[Azure 角色型存取控制](./active-directory/role-based-access-control-configure.md)。

鎖定會套用至指定的資源及任何子資源。

您可以移除的 PowerShell 命令**移除 AzureRmResourceLock**或[刪除作業](https://msdn.microsoft.com/library/azure/mt204562.aspx)的 REST API 鎖定。

## <a name="examples"></a>範例

下列範例會套用至 web app 無法刪除鎖定。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "hostingPlanName": {
                "type": "string"
            }
        },
        "variables": {
            "siteName": "[concat('site',uniqueString(resourceGroup().id))]"
        },
        "resources": [
            {
                "apiVersion": "2015-08-01",
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                },
            },
            {
                "type": "Microsoft.Web/sites/providers/locks",
                "apiVersion": "2015-01-01",
                "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
                "dependsOn": [ "[variables('siteName')]" ],
                "properties":
                {
                    "level": "CannotDelete",
                    "notes": "my notes"
                }
             }
        ],
        "outputs": {}
    }

下一個範例適用於資源群組不能刪除鎖定。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Authorization/locks",
                "apiVersion": "2015-01-01",
                "name": "MyGroupLock",
                "properties":
                {
                    "level": "CannotDelete",
                    "notes": "my notes"
                }
            }
        ],
        "outputs": {}
    }

## <a name="next-steps"></a>後續步驟

- 範本結構的相關資訊，請參閱[撰寫 Azure 資源管理員範本](resource-group-authoring-templates.md)。
- 如需有關鎖定的詳細資訊，請參閱[鎖定資源與 Azure 資源管理員](resource-group-lock-resources.md)。
