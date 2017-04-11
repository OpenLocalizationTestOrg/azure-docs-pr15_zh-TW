<properties
    pageTitle="部署電腦學習使用 Azure 資源管理員範本的工作區 |Microsoft Azure"
    description="如何為 Azure 電腦學習使用 Azure 資源管理員範本部署工作區"
    services="machine-learning"
    documentationCenter=""
    authors="ahgyger"
    manager="haining"
    editor="garye"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="ahgyger"/>
# <a name="deploy-machine-learning-workspace-using-azure-resource-manager"></a>部署電腦學習使用 Azure 資源管理員的工作區

## <a name="introduction"></a>簡介
使用部署範本提供的調整方式來節省時間 Azure 資源管理員部署與驗證互連式的元件，並再次機制。 若要設定 Azure 電腦學習工作區，例如，您需要先設定 [Azure 儲存體帳戶，然後再部署工作區。 假設執行此動作手動下數百個工作區。 另一個簡單的方法是使用 Azure 資源管理員範本部署 Azure 電腦學習工作區和其所有的相依性。 本文會引導您逐步此程序。 如概觀的 Azure 資源管理員，請參閱[Azure 資源管理員的概觀](../azure-resource-manager/resource-group-overview.md)。

## <a name="step-by-step-create-a-machine-learning-workspace"></a>逐步資訊︰ 建立電腦學習工作區
我們會建立 Azure 資源] 群組中，然後將新的 Azure 儲存體帳戶和新 Azure 電腦學習的工作區使用資源管理員範本部署。 部署完成後，我們會列印出來 （主索引鍵、 workspaceID 和工作區的 URL） 所建立的工作區的相關的重要資訊。

### <a name="create-an-azure-resource-manager-template"></a>建立可 Azure 資源管理員的範本
在電腦學習工作區需要 Azure 儲存體帳戶來儲存資料集的連結。
產生儲存體帳戶名稱的 [資源] 群組的名稱和工作區名稱，則會使用下列範本。  它也使用儲存體帳戶名稱為屬性時建立的工作區。

```
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "variables": {
        "namePrefix": "[resourceGroup().name]",
        "location": "[resourceGroup().location]",
        "mlVersion": "2016-04-01",
        "stgVersion": "2015-06-15",
        "storageAccountName": "[concat(variables('namePrefix'),'stg')]",
        "mlWorkspaceName": "[concat(variables('namePrefix'),'mlwk')]",
        "mlResourceId": "[resourceId('Microsoft.MachineLearning/workspaces', variables('mlWorkspaceName'))]",
        "stgResourceId": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "storageAccountType": "Standard_LRS"
    },
    "resources": [
        {
            "apiVersion": "[variables('stgVersion')]",
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[variables('location')]",
            "properties": {
                "accountType": "[variables('storageAccountType')]"
            }
        },
        {
            "apiVersion": "[variables('mlVersion')]",
            "type": "Microsoft.MachineLearning/workspaces",
            "name": "[variables('mlWorkspaceName')]",
            "location": "[variables('location')]",
            "dependsOn": ["[variables('stgResourceId')]"],
            "properties": {
                "UserStorageAccountId": "[variables('stgResourceId')]"
            }
        }
    ],
    "outputs": {
        "mlWorkspaceObject": {"type": "object", "value": "[reference(variables('mlResourceId'), variables('mlVersion'))]"},
        "mlWorkspaceToken": {"type": "string", "value": "[listWorkspaceKeys(variables('mlResourceId'), variables('mlVersion')).primaryToken]"},
        "mlWorkspaceWorkspaceID": {"type": "string", "value": "[reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId]"},
        "mlWorkspaceWorkspaceLink": {"type": "string", "value": "[concat('https://studio.azureml.net/Home/ViewWorkspace/', reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId)]"}
    }
}

```
將此範本儲存為下 c:\temp\ mlworkspace.json 檔案。

### <a name="deploy-the-resource-group-based-on-the-template"></a>部署資源] 群組中，以範本
* 開啟 PowerShell
* 安裝模組 Azure 資源管理員和 Azure 服務管理  

```
# Install the Azure Resource Manager modules from the PowerShell Gallery (press “A”)
Install-Module AzureRM -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press “A”)
Install-Module Azure -Scope CurrentUser
```

   這些步驟下載並安裝所需完成的剩餘的步驟模組。 這只需執行的 PowerShell 命令環境中執行一次即可。   

* Azure 驗證  

```
# Authenticate (enter your credentials in the pop-up window)
Add-AzureRmAccount
```
需要每個工作階段，重複此步驟。 驗證之後，應該會顯示您的訂閱資訊。

![Azure 帳戶][1]

現在，我們可以存取 Azure，我們可以建立資源群組。

* 建立資源群組

```
$rg = New-AzureRmResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

確認 [資源群組正確佈建後]。 **ProvisioningState**應該是 「 成功。 」
資源群組名稱範本用於產生儲存體帳戶名稱。 儲存體帳戶名稱必須介於 3 和 24 個字元，並使用數字和只有英文小寫字母。

![資源群組][2]

* 使用資源群組部署，部署新的電腦學習工作區。

```
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzureRmResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

一旦完成部署，則直接存取內容您部署工作區。 例如，您可以存取主索引鍵 Token。

```
# Access Azure ML Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

抓取權杖現有的工作區的另一個方法是使用叫用 AzureRmResourceAction] 命令。 例如，您可以列出所有工作區的主要和次要權杖。

```  
# List the primary and secondary tokens of all workspaces
Get-AzureRmResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |% { Invoke-AzureRmResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}  
```
工作區已佈建後，您也可以自動使用[PowerShell 模組的 Azure 電腦學習](http://aka.ms/amlps)許多 Azure 電腦學習 Studio 工作。

## <a name="next-steps"></a>後續步驟 
* 進一步瞭解[撰寫 Azure 資源管理員範本](../resource-group-authoring-templates.md)。 
* 看看[Azure 快速入門範本存放庫](https://github.com/Azure/azure-quickstart-templates)。 
* 觀看此影片[Azure 資源管理員](https://channel9.msdn.com/Events/Ignite/2015/C9-39)。 
 
<!--Image references-->
[1]: ../media/machine-learning-deploy-with-resource-manager-template/azuresubscription.png
[2]: ../media/machine-learning-deploy-with-resource-manager-template/resourcegroupprovisioning.png


<!--Link references-->
