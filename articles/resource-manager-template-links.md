<properties
   pageTitle="資源管理員範本連結資源 |Microsoft Azure"
   description="會顯示資源管理員的結構描述部署透過範本的相關資源之間的連結。"
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
   ms.date="04/05/2016"
   ms.author="tomfitz"/>

# <a name="resource-links-template-schema"></a>資源的連結範本結構描述

建立兩個資源的連結。 連結會套用至稱為來源資源的資源。 在連結的第二個資源稱為目標資源。

## <a name="schema-format"></a>結構描述格式

若要建立的連結，將您的範本的 [資源] 區段中的下列結構描述。
    
    {
        "type": enum,
        "apiVersion": "2015-01-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "targetId": string,
            "notes": string
        }
    }



## <a name="values"></a>值

下表描述您要設定結構描述中的值。

| 名稱 | 值 |
| ---- | ---- |
| 類型 | 列舉<br />所需<br />**{命名空間} / {輸入} / 提供者/連結**<br /><br />若要建立資源類型。 {命名空間}，{類型} 值參考來源資源的提供者命名空間和資源類型。 |
| apiVersion | 列舉<br />所需<br />**2015-01-01**<br /><br />要用來建立資源 API 版本。 |  
| 名稱 | 字串<br />所需<br />**{resouce}/Microsoft.Resources/{linkname}**<br /> 最多 64 個字元，並不能包含 <>，%&、？，或任何控制字元。<br /><br />A 值的指定兩個來源資源的名稱和連結的名稱。 |
| dependsOn | 陣列<br />選擇性<br />逗點分隔資源的清單名稱或資源的唯一識別碼。<br /><br />取決於此連結的資源的集合。 如果您要連結的資源部署在同一個範本，請此項目，以確保在第一次部署中包含這些資源名稱。 | 
| 屬性 | 物件<br />所需<br />[屬性的物件](#properties)<br /><br />若要連結至資源識別的物件，並連結的相關附註。 |  

<a id="properties" />
### <a name="properties-object"></a>屬性的物件

| 名稱 | 值 |
| ------- | ---- |
| targetId | 字串<br />所需<br />**{資源識別碼}**<br /><br />若要連結至目標資源的識別碼。 |
| 備忘稿 | 字串<br />選擇性<br />超過 512 個字元<br /><br />鎖定的描述。 |


## <a name="how-to-use-the-link-resource"></a>如何使用連結資源

您套用時的資源有持續部署後的相依性的兩個資源之間的連結。 例如，應用程式可能會連線至資料庫中不同的資源群組。 您可以從應用程式中建立的連結至資料庫，以定義的相依性。 連結可讓您在文件的兩個資源之間的關聯。 之後，您或您組織中其他人可以查詢探索其他資源與資源的運作方式的連結的資源。

所有連結的資源必須屬於相同的訂閱。 每個資源可以連結至 50 其他資源。 任何連結的資源已被刪除或移動，如果連結擁有者必須清理剩餘的連結。

若要使用透過其餘的連結，請參閱[連結的資源](https://msdn.microsoft.com/library/azure/mt238499.aspx)。

使用下列 PowerShell 的 Azure 命令，查看所有的訂閱中的連結。 您可以提供其他參數以限制結果。

    Get-AzureRmResource -ResourceType Microsoft.Resources/links -isCollection -ResourceGroupName <YourResourceGroupName>

## <a name="examples"></a>範例

下列範例會套用至 web app 唯讀鎖定。

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
                "type": "Microsoft.Web/serverfarms",
                "name": "[parameters('hostingPlanName')]",
                "location": "[resourceGroup().location]",
                "sku": {
                    "tier": "Free",
                    "name": "f1",
                    "capacity": 0
                },
                "properties": {
                    "numberOfWorkers": 1
                }
            },
            {
                "apiVersion": "2015-08-01",
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
                "dependsOn": [ "[parameters('hostingPlanName')]" ],
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                }
            },
            {
                "type": "Microsoft.Web/sites/providers/links",
                "apiVersion": "2015-01-01",
                "name": "[concat(variables('siteName'),'/Microsoft.Resources/SiteToStorage')]",
                "dependsOn": [ "[variables('siteName')]" ],
                "properties": {
                    "targetId": "[resourceId('Microsoft.Storage/storageAccounts','storagecontoso')]",
                    "notes": "This web site uses the storage account to store user information."
                }
            }
        ],
        "outputs": {}
    }

## <a name="quickstart-templates"></a>快速入門範本

下列的快速入門範本部署資源的連結。

- [使用邏輯應用程式佇列中通知](https://azure.microsoft.com/documentation/templates/201-alert-to-queue-with-logic-app)
- [通知與邏輯應用程式的可寬延時間](https://azure.microsoft.com/documentation/templates/201-alert-to-slack-with-logic-app)
- [佈建與現有的閘道器的 API 應用程式](https://azure.microsoft.com/documentation/templates/201-api-app-gateway-existing)
- [佈建 API 應用程式新增閘道器](https://azure.microsoft.com/documentation/templates/201-api-app-gateway-new)
- [建立邏輯應用程式加上 API 的應用程式，使用範本](https://azure.microsoft.com/documentation/templates/201-logic-app-api-app-create)
- [當提醒啟動時傳送文字訊息的邏輯應用程式](https://azure.microsoft.com/documentation/templates/201-alert-to-text-message-with-logic-app)


## <a name="next-steps"></a>後續步驟

- 範本結構的相關資訊，請參閱[撰寫 Azure 資源管理員範本](resource-group-authoring-templates.md)。
