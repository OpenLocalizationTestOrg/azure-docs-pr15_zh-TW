<properties 
    pageTitle="部署 web 應用程式的已連結至 GitHub 存放庫" 
    description="若要部署包含從 GitHub 存放庫專案的 web 應用程式使用 Azure 資源管理員範本。" 
    services="app-service" 
    documentationCenter="" 
    authors="cephalin" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/27/2016" 
    ms.author="cephalin"/>

# <a name="deploy-a-web-app-linked-to-a-github-repository"></a>部署 web 應用程式連結至 GitHub 存放庫

本主題中，您將學習如何建立可部署已連結至 GitHub 存放庫中某個現有專案的 web 應用程式的 Azure 資源管理員範本。 您將學習如何定義部署的資源，以及如何定義的參數會指定部署執行時。 您可以使用您自己的部署，這個範本，或自訂以符合您的需求。

如需有關如何建立範本的詳細資訊，請參閱[撰寫 Azure 資源管理員範本](../resource-group-authoring-templates.md)。

完成的範本，請參閱[Web 應用程式連結至 GitHub 範本](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-github-deploy/azuredeploy.json)。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="what-you-will-deploy"></a>將部署

利用此範本，您會部署包含從 GitHub 中專案的程式碼的 web 應用程式。

若要自動執行的部署，請按一下 [動作] 按鈕︰

[![部署至 Azure](./media/app-service-web-arm-from-github-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-github-deploy%2Fazuredeploy.json)

## <a name="parameters"></a>參數

[AZURE.INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

### <a name="repourl"></a>repoURL

包含要部署專案的 GitHub 存放庫 URL。 這個參數會包含預設值，但此值只用來顯示您如何提供存放庫 URL。 測試範本時，您可以使用此值，但您會想要使用範本的使用者 URL 提供您自己的存放庫。

    "repoURL": {
        "type": "string",
        "defaultValue": "https://github.com/davidebbo-test/Mvc52Application.git"
    }

### <a name="branch"></a>分支

部署應用程式時要使用存放庫的分支。 預設值是主版，但您可以在您想要部署存放庫中的任何分支的名稱。

    "branch": {
        "type": "string",
        "defaultValue": "master"
    }
    
## <a name="resources-to-deploy"></a>部署資源

[AZURE.INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### <a name="web-app"></a>Web 應用程式

建立 web 應用程式的已連結至 GitHub 中的專案。 

您指定的 web 應用程式，透過**站台名稱**參數的名稱，以及透過**siteLocation**參數的 web 應用程式的位置。 **DependsOn**項目中的範本定義 web 應用程式的主機服務方案的服務而定。 因為它的主機服務方案而定，直到建立完成的主機服務方案時，將不會建立 web 應用程式。 **DependsOn**元素只會用來指定部署順序。 如果您不要將標示為的主機服務方案而定的 web 應用程式，Azure 資源管理程式將會嘗試同時建立兩個資源，然後如果 web 應用程式建立的主機服務方案之前，您可能會收到錯誤。

Web 應用程式也會有子資源下列**資源**一節中所定義。 此子資源定義專案的 web 應用程式部署來源控制。 此範本中的來源控制項已連結至特定的 GitHub 存放庫。 使用程式碼定義 GitHub 存放庫**「 RepoUrl 」: 「 https://github.com/davidebbo-test/Mvc52Application.git 」**您可能會硬式編碼存放庫 URL 時您想要建立的重複部署時需要參數的最小的數字的單一專案範本。
而非硬式編碼存放庫 URL，您可以新增存放庫 url 的參數，該值**RepoUrl**屬性。

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/sites",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
      ],
      "properties": {
        "serverFarmId": "[parameters('hostingPlanName')]"
      },
      "resources": [
        {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/Sites', parameters('siteName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('repoURL')]",
            "branch": "[parameters('branch')]",
            "IsManualIntegration": true
          }
        }
      ]
    }

## <a name="commands-to-run-deployment"></a>若要執行部署] 命令

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-github-deploy/azuredeploy.json -siteName ExampleSite -hostingPlanName ExamplePlan -siteLocation "West US" -ResourceGroupName ExampleDeployGroup

### <a name="azure-cli"></a>Azure CLI

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-github-deploy/azuredeploy.json


 
