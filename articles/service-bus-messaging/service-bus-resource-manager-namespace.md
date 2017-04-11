<properties
    pageTitle="建立使用資源管理員範本服務匯流排命名空間 |Microsoft Azure"
    description="使用 Azure 資源管理員範本來建立服務匯流排命名空間"
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
    ms.date="10/04/2016"
    ms.author="sethm;shvija"/>

# <a name="create-a-service-bus-namespace-using-an-azure-resource-manager-template"></a>建立使用 Azure 資源管理員範本服務匯流排命名空間

本文將說明如何使用 Azure 資源管理員範本會使用標準或基本 SKU 建立服務匯流排命名空間的輸入**訊息**。 本文也會定義所指定的部署執行參數。 您可以使用您自己的部署，這個範本，或自訂以符合您的需求。

如需有關如何建立範本的詳細資訊，請參閱[撰寫 Azure 資源管理員範本][]。

完成的範本，請參閱 GitHub[服務匯流排命名空間範本][]。

>[AZURE.NOTE] 下列 Azure 資源管理員範本可供下載並部署項目。 
>
>-    [與事件中樞和消費者群組建立的事件集線器命名空間](../event-hubs/event-hubs-resource-manager-namespace-event-hub.md)
>-    [使用佇列中建立的服務匯流排命名空間](service-bus-resource-manager-namespace-queue.md)
>-    [主題與訂閱建立服務匯流排命名空間](service-bus-resource-manager-namespace-topic.md)
>-    [建立服務匯流排命名空間佇列和授權的規則](service-bus-resource-manager-namespace-auth-rule.md)
>
>若要檢查的最新的範本，請造訪[Azure 快速入門範本][]組件庫和服務匯流排搜尋。

## <a name="what-will-you-deploy"></a>您將部署什麼？

利用此範本，您會部署[基本、 標準、 或付費](https://azure.microsoft.com/pricing/details/service-bus/)SKU 的服務匯流排命名空間。

若要自動執行的部署，請按一下 [動作] 按鈕︰

[![部署至 Azure](./media/service-bus-resource-manager-namespace/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-servicebus-create-namespace%2Fazuredeploy.json)

## <a name="parameters"></a>參數

使用 Azure 資源管理員中，您可以定義您想要指定部署範本時的值的參數。 範本包含名稱為`Parameters`含有所有參數值。 您應該定義的參數會根據您要部署的專案，或根據您要部署到的環境而這些值。 不會定義參數會永遠保持不變的值。 每個參數值範本中用於定義部署的資源。

此範本可定義下列參數。

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

若要建立服務匯流排命名空間的名稱。

```
"serviceBusNamespaceName": {
"type": "string",
"metadata": { 
    "description": "Name of the Service Bus namespace" 
    }
}
```

### <a name="servicebussku"></a>serviceBusSKU

若要建立服務匯流排[SKU](https://azure.microsoft.com/pricing/details/service-bus/)的名稱。

```
"serviceBusSku": { 
    "type": "string", 
    "allowedValues": [ 
        "Basic", 
        "Standard",
        "Premium" 
    ], 
    "defaultValue": "Standard", 
    "metadata": { 
        "description": "The messaging tier for service Bus namespace" 
    } 

```

範本定義允許使用此參數 （基本、 標準、 或進階版） 的值，並會指派預設值 （標準），如果未指定的值。

如需有關服務匯流排價格的詳細資訊，請參閱[服務匯流排價格與帳單][]。

### <a name="servicebusapiversion"></a>serviceBusApiVersion

範本的服務匯流排 API 版本。

```
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2015-08-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       } 
```

## <a name="resources-to-deploy"></a>部署資源

### <a name="service-bus-namespace"></a>服務匯流排命名空間

建立標準服務匯流排命名空間的輸入**訊息**。

```
"resources": [
    {
        "apiVersion": "[parameters('serviceBusApiVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "StandardSku",
            "tier": "Standard"
        },
        "properties": {
        }
    }
]
```

## <a name="commands-to-run-deployment"></a>若要執行部署] 命令

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json
```

### <a name="azure-cli"></a>Azure CLI

```
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json
```

## <a name="next-steps"></a>後續步驟

既然您已建立及部署使用 Azure 資源管理員的資源，瞭解如何管理這些資源閱讀下列文章︰

- [管理服務匯流排使用 PowerShell](service-bus-powershell-how-to-provision.md)
- [管理服務匯流排資源服務匯流排檔案總管](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)

  [撰寫 Azure 資源管理員範本]: ../resource-group-authoring-templates.md
  [服務匯流排命名空間範本]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/
  [Azure 快速入門範本]: https://azure.microsoft.com/documentation/templates/?term=service+bus
  [服務匯流排價格與帳單]: https://azure.microsoft.com/documentation/articles/service-bus-pricing-billing/
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
