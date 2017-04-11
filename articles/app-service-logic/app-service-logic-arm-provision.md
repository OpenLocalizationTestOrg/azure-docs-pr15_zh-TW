<properties 
    pageTitle="建立使用 Azure 應用程式服務中的 Azure 資源管理員範本邏輯應用程式 |Microsoft Azure" 
    description="若要將空白邏輯應用程式部署定義工作流程中使用 Azure 資源管理員範本。" 
    services="logic-apps" 
    documentationCenter="" 
    authors="MSFTMan" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/25/2016" 
    ms.author="deonhe"/>

# <a name="create-a-logic-app-using-a-template"></a>建立邏輯應用程式使用的範本

使用 Azure 資源管理員範本來建立空白的邏輯應用程式，可以用來定義工作流程。 您可以定義部署的資源，以及如何定義部署執行時所指定的參數。 您可以使用您自己的部署，這個範本，或自訂以符合您的需求。

如需邏輯應用程式屬性的詳細資訊，請參閱[邏輯應用程式工作流程管理 API](https://msdn.microsoft.com/library/azure/mt643788.aspx)。 

如需定義本身的範例，請參閱[作者邏輯應用程式定義](app-service-logic-author-definitions.md)。 

如需有關如何建立範本的詳細資訊，請參閱[撰寫 Azure 資源管理員範本](../resource-group-authoring-templates.md)。

完成的範本，請參閱[邏輯應用程式範本](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json)。

## <a name="what-you-will-deploy"></a>將部署

利用此範本，您可以部署邏輯應用程式。

若要自動執行的部署，請選取下列按鈕︰  

[![部署至 Azure](media/app-service-logic-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

## <a name="parameters"></a>參數

[AZURE.INCLUDE [app-service-logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

### <a name="testuri"></a>testUri

     "testUri": {
        "type": "string",
        "defaultValue": "http://azure.microsoft.com/en-us/status/feed/"
      }
    
## <a name="resources-to-deploy"></a>部署資源

### <a name="logic-app"></a>邏輯應用程式

建立邏輯應用程式。

範本會使用參數值的邏輯應用程式的名稱。 資源群組的同一個位置，它就會設定邏輯應用程式的位置。 

此特定的定義執行一次一個小時，並會偵測**testUri**參數中所指定的位置。 

    {
      "type": "Microsoft.Logic/workflows",
      "apiVersion": "2016-06-01",
      "name": "[parameters('logicAppName')]",
      "location": "[resourceGroup().location]",
      "tags": {
        "displayName": "LogicApp"
      },
      "properties": {
        "definition": {
          "$schema": "http://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "testURI": {
              "type": "string",
              "defaultValue": "[parameters('testUri')]"
            }
          },
          "triggers": {
            "recurrence": {
              "type": "recurrence",
              "recurrence": {
                "frequency": "Hour",
                "interval": 1
              }
            }
          },
          "actions": {
            "http": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('testUri')"
              },
              "runAfter": {}
            }
          },
          "outputs": {}
        },
        "parameters": {}
      }
    }


## <a name="commands-to-run-deployment"></a>若要執行部署] 命令

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -ResourceGroupName ExampleDeployGroup

### <a name="azure-cli"></a>Azure CLI

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -g ExampleDeployGroup


 
