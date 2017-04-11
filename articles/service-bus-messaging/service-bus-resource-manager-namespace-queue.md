<properties
    pageTitle="建立與使用 Azure 資源管理員範本的佇列中的服務匯流排命名空間 |Microsoft Azure"
    description="建立服務匯流排命名空間和使用 Azure 資源管理員範本佇列中"
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
    ms.author="sethm;shvija"/>

# <a name="create-a-service-bus-namespace-and-a-queue-using-an-azure-resource-manager-template"></a>建立服務匯流排命名空間和使用 Azure 資源管理員範本佇列中

本文說明如何使用 Azure 資源管理員範本所建立的服務匯流排命名空間和佇列。 您將學習如何定義部署的資源，以及如何定義的參數會指定部署執行時。 您可以使用您自己的部署，這個範本，或自訂以符合您的需求。

如需有關如何建立範本的詳細資訊，請參閱[撰寫 Azure 資源管理員範本][]。

完成的範本，請參閱 GitHub[服務匯流排命名空間和佇列中的範本][]。

>[AZURE.NOTE] 下列 Azure 資源管理員範本可供下載並部署項目。
>
>-    [建立服務匯流排命名空間佇列和授權的規則](service-bus-resource-manager-namespace-auth-rule.md)
>-    [主題與訂閱建立服務匯流排命名空間](service-bus-resource-manager-namespace-topic.md)
>-    [建立服務匯流排命名空間](service-bus-resource-manager-namespace.md)
>-    [與事件中樞和消費者群組建立的事件集線器命名空間](../event-hubs/event-hubs-resource-manager-namespace-event-hub.md)
>
>若要檢查的最新的範本，請造訪[Azure 快速入門範本][]組件庫和搜尋 「 服務匯流排 」。

## <a name="what-will-you-deploy"></a>您將部署什麼？

利用此範本，您會部署佇列中的服務匯流排命名空間。

第一個在第一個查看 (FIFO) 訊息傳送給一或多個競爭消費者[服務匯流排佇列](service-bus-queues-topics-subscriptions.md#queues)優惠。

若要自動執行的部署，請按一下 [動作] 按鈕︰

[![部署至 Azure](./media/service-bus-resource-manager-namespace-queue/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="parameters"></a>參數

使用 Azure 資源管理員中，您可以定義您想要指定部署範本時的值的參數。 範本包含名稱為`Parameters`含有所有參數值。 您應該定義的參數會根據您要部署的專案，或根據您要部署到的環境而這些值。 不會定義參數會永遠保持不變的值。 每個參數值範本中用於定義部署的資源。

範本定義下列參數。

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

### <a name="servicebusqueuename"></a>serviceBusQueueName

建立服務匯流排命名空間中佇列中的名稱。

```
"serviceBusQueueName": {
"type": "string"
}
```

### <a name="servicebusapiversion"></a>serviceBusApiVersion

範本的服務匯流排 API 版本。

```
"serviceBusApiVersion": {
"type": "string"
}
```

## <a name="resources-to-deploy"></a>部署資源

建立佇列中輸入**訊息**的標準服務匯流排命名空間。

```
"resources ": [{
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
                "path": "[parameters('serviceBusQueueName')]",
            }
        }]
    }]
```

## <a name="commands-to-run-deployment"></a>若要執行部署] 命令

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI

```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## <a name="next-steps"></a>後續步驟

現在，您建立及部署使用 Azure 資源管理員的資源，瞭解如何檢視這些文件管理這些資源︰

- [管理服務匯流排使用 PowerShell](service-bus-powershell-how-to-provision.md)
- [管理服務匯流排資源服務匯流排檔案總管](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)


  [撰寫 Azure 資源管理員範本]: ../resource-group-authoring-templates.md
  [服務匯流排命名空間和佇列中的範本]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
  [Azure 快速入門範本]: https://azure.microsoft.com/documentation/templates/?term=service+bus
  [Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
