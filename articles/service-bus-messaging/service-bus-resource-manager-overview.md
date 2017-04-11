<properties
    pageTitle="建立使用 Azure 資源管理員範本的服務匯流排資源 |Microsoft Azure"
    description="使用 Azure 資源管理員範本自動建立服務匯流排資源"
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="10/14/2016"
    ms.author="sethm"/>

# <a name="create-service-bus-resources-using-azure-resource-manager-templates"></a>建立使用 Azure 資源管理員範本的服務匯流排資源

本文將說明如何建立並部署服務匯流排和事件集線器使用 Azure 資源管理員範本、 PowerShell 和服務匯流排資源提供者的資源。

Azure 資源管理員範本可協助您定義的資源來部署方案，並指定參數和變數，讓您輸入不同環境中的值。 範本包含 JSON 和可用來建構您的部署中的值的運算式。 如需撰寫 Azure 資源管理員範本及討論範本格式的詳細資訊，請參閱[撰寫 Azure 資源管理員範本](../resource-group-authoring-templates.md)。 

>[AZURE.NOTE] 本文中的範例顯示如何使用 Azure 資源管理員 」 建立的服務匯流排命名空間和訊息的實體 （佇列中）。 如需其他範本的範例，請參閱[Azure 快速入門範本庫][]及搜尋 「 服務匯流排 」。

## <a name="service-bus-and-event-hubs-resource-manager-templates"></a>服務匯流排和事件集線器資源管理員的範本

這些服務匯流排和事件集線器 Azure 資源管理員範本可供下載並部署項目。 按一下下列連結的詳細資料每筆 GitHub 上範本的連結︰ 

- [建立服務匯流排命名空間](service-bus-resource-manager-namespace.md)
- [使用佇列中建立的服務匯流排命名空間](service-bus-resource-manager-namespace-queue.md)
- [主題與訂閱建立服務匯流排命名空間](service-bus-resource-manager-namespace-topic.md)
- [建立服務匯流排命名空間佇列和授權的規則](service-bus-resource-manager-namespace-auth-rule.md)
- [與事件中樞和消費者群組建立的事件集線器命名空間](../event-hubs/event-hubs-resource-manager-namespace-event-hub.md)

## <a name="deploy-with-powershell"></a>使用 PowerShell 部署

下列程序說明如何使用 PowerShell 來部署 Azure 資源管理員範本所建立的**標準**層服務匯流排命名空間和佇列中的命名空間。 此範例中為基礎[建立佇列中的服務匯流排命名空間](https://github.com/Azure/azure-quickstart-templates/tree/master/201-servicebus-create-queue)範本。 大約工作流程如下所示︰

1. 安裝 PowerShell。
2. 建立範本與 （選擇性） 參數檔案。
2. PowerShell 的 Azure 帳戶登入。
3. 如果沒有 [建立新的資源群組。
4. 測試部署。
5. 如有需要，請設定部署模式。
6. 部署範本。

完整部署 Azure 資源管理員範本的詳細資訊，請參閱[使用 Azure 資源管理員範本部署資源][]。

### <a name="install-powershell"></a>安裝 PowerShell

PowerShell 的 Azure，依照[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)中的指示進行安裝。

### <a name="create-a-template"></a>建立範本

複製或複製 GitHub [201 servicebus-建立-佇列](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.json)範本︰

```
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Service Bus namespace"
            }
        },
        "serviceBusQueueName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Queue"
            }
        },
        "serviceBusApiVersion": {
            "type": "string",
            "defaultValue": "2015-08-01",
            "metadata": {
                "description": "Service Bus ApiVersion used by the template"
            }
        }
    },
    "variables": {
        "location": "[resourceGroup().location]",
        "sbVersion": "[parameters('serviceBusApiVersion')]",
        "defaultSASKeyName": "RootManageSharedAccessKey",
        "authRuleResourceId": "[resourceId('Microsoft.ServiceBus/namespaces/authorizationRules', parameters('serviceBusNamespaceName'), variables('defaultSASKeyName'))]"
    },
    "resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "StandardSku",
            "tier": "Standard"
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusQueueName')]",
            "type": "Queues",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusQueueName')]"
            }
        }]
    }],
    "outputs": {
        "NamespaceConnectionString": {
            "type": "string",
            "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryConnectionString]"
        },
        "SharedAccessPolicyPrimaryKey": {
            "type": "string",
            "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryKey]"
        }
    }
}
```

### <a name="create-a-parameters-file-optional"></a>建立參數檔案 （選用）

若要使用的選擇性參數檔案，請複製[201 servicebus-建立-佇列](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.parameters.json)檔案。 取代的值`serviceBusNamespaceName`服務匯流排命名空間，您想要在此部署中，建立及取代的值的名稱`serviceBusQueueName`佇列中您想要建立的名稱。 

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "value": "<myNamespaceName>"
        },
        "serviceBusQueueName": {
            "value": "<myQueueName>"
        },
        "serviceBusApiVersion": {
            "value": "2015-08-01"
        }
    }
}
```

如需詳細資訊，請參閱[參數檔案](../resource-group-template-deploy.md#parameter-file)主題。

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>登入 Azure 並設定 Azure 訂閱

從 PowerShell 提示中，執行下列命令︰

```
Login-AzureRmAccount
```

系統會提示您登入您的 Azure 帳戶。 登入後，執行下列命令以檢視您可用的訂閱。

```
Get-AzureRMSubscription
```

這個命令會傳回可用 Azure 訂閱的清單。 執行下列命令以選擇目前的工作階段的訂閱。 取代`<YourSubscriptionId>`含有您想要使用的 Azure 訂閱 GUID。

```
Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>設定資源群組

如果您沒有現有的資源群組，請使用 [**新增 AzureRmResourceGroup**命令建立新的資源群組。 提供資源群組和您想要使用的位置的名稱。 例如︰

```
New-AzureRmResourceGroup -Name MyDemoRG -Location "West US"
```

如果成功，則會顯示新的資源群組的摘要。

```
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>測試部署

驗證您的部署執行`Test-AzureRmResourceGroupDeployment`指令程式。 當測試部署，提供的參數，相同的方式時執行部署。

```
Test-AzureRmResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="create-the-deployment"></a>建立部署

若要建立新的部署，請執行`New-AzureRmResourceGroupDeployment`命令，並提供必要的參數提示。 參數會包含您的部署資源的群組，以及路徑或 URL 的名稱，以將範本檔案的名稱。 如果未指定**模式**參數，則會使用**累加**的預設值。 如需詳細資訊，請參閱[遞和完成部署](../resource-group-template-deploy.md#incremental-and-complete-deployments)。

下列命令會提示您輸入 PowerShell 視窗中的三個參數︰

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

若要改為指定參數檔案，請使用下列命令。

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

當您執行部署指令程式時，您也可以使用內嵌參數。 命令如下所示︰

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

若要執行[完整](../resource-group-template-deploy.md#incremental-and-complete-deployments)的部署，請為 「**完成**設定**模式**參數︰

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json 
```

### <a name="verify-the-deployment"></a>驗證部署

如果已成功部署資源，PowerShell 視窗會顯示摘要部署︰

```
DeploymentName    : MyDemoDeployment
ResourceGroupName : MyDemoRG
ProvisioningState : Succeeded
Timestamp         : 4/19/2016 10:38:30 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    serviceBusNamespaceName  String             <namespaceName>
                    serviceBusQueueName  String                 <queueName>
                    serviceBusApiVersion  String                2015-08-01

```

## <a name="next-steps"></a>後續步驟

您現在已經看到的基本工作流程及部署 Azure 資源管理員範本的命令。 如需詳細資訊，請造訪下列連結︰

- [Azure 資源管理員的概觀][]
- [部署資源的 Azure 資源管理員範本][]
- [製作的範本](../resource-group-authoring-templates.md)


[Azure 資源管理員的概觀]: ../resource-group-overview.md
[部署資源的 Azure 資源管理員範本]: ../resource-group-template-deploy.md
[Azure 快速入門的範本庫]: https://azure.microsoft.com/documentation/templates/?term=service+bus