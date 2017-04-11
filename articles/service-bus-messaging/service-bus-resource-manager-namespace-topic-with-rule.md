<properties
    pageTitle="建立主題訂閱服務匯流排命名空間並規則使用 Azure 資源管理員範本 |Microsoft Azure"
    description="建立服務匯流排命名空間主題、 訂閱，與使用 Azure 資源管理員範本的規則"
    services="service-bus"
    documentationCenter=".net"
    authors="ShubhaVijayasarathy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="10/25/2016"
    ms.author="ShubhaVijayasarathy"/>

# <a name="create-a-service-bus-namespace-with-topic-subscription-and-rule-using-an-azure-resource-manager-template"></a>建立服務匯流排命名空間主題、 訂閱，與使用 Azure 資源管理員範本的規則

本文說明如何使用 Azure 資源管理員範本所建立的服務匯流排命名空間使用主題、 訂閱及規則 （篩選）。 您瞭解如何以定義部署的資源，以及如何定義的參數會指定部署執行時。 您可以使用這個範本，針對您自己的部署，或自訂，以符合您的需求

如需有關如何建立範本的詳細資訊，請參閱[撰寫 Azure 資源管理員範本][]。

練習的詳細資訊和 Azure 資源圖樣的命名慣例，請參閱[Azure 資源命名慣例][]。

完成的範本，請參閱[服務匯流排命名空間主題、 訂閱及規則][]範本。

>[AZURE.NOTE] 下列 Azure 資源管理員範本可供下載並部署項目。
>
>-    [建立服務匯流排命名空間佇列和授權的規則](service-bus-resource-manager-namespace-auth-rule.md)
>-    [使用佇列中建立的服務匯流排命名空間](service-bus-resource-manager-namespace-queue.md)
>-    [建立服務匯流排命名空間](service-bus-resource-manager-namespace.md)
>-    [主題與訂閱建立服務匯流排命名空間](service-bus-resource-manager-namespace-topic.md)
>
>若要檢查的最新的範本，請造訪[Azure 快速入門範本][]組件庫和服務匯流排搜尋。

## <a name="what-will-you-deploy"></a>您將部署什麼？

利用此範本，您可以部署主題、 訂閱及規則 （篩選） 的服務匯流排命名空間。

[服務匯流排主題和訂閱](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)提供一個一對多表單的*發佈/訂閱*模式中的通訊。 時使用的分散式應用程式的元件執行不彼此直接，主題和訂閱，請改為交換透過主題做為中間的訊息。主題訂閱類似虛擬佇列接收主題已傳送的郵件的複本。 篩選的訂閱可讓您指定主題的郵件應該出現在特定主題訂閱。

## <a name="what-are-rules-filters"></a>什麼是規則 （篩選器）？

在許多情況下，必須以不同方式處理有特定的特性的郵件。 若要啟用此，您可以設定中尋找郵件有想要的內容，然後執行這些屬性某些修改的訂閱。 服務匯流排訂閱，請參閱所有傳送至主題的訊息，而您僅可以將這些郵件子集複製虛擬訂閱佇列中。 這是使用訂閱篩選。 若要深入瞭解 rules(filters) 上，請參閱[服務匯流排佇列主題，以及訂閱][]。

若要自動執行的部署，請按一下 [動作] 按鈕︰

[![部署至 Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-subscription-rule%2Fazuredeploy.json)

## <a name="parameters"></a>參數

使用 Azure 資源管理員中，您應該定義您想要指定部署範本時的值參數。 範本包含名稱為`Parameters`包含的所有參數值。 您應該定義的參數，視情況而定根據您要部署的專案，或根據您要部署到環境這些值。 不會定義參數永遠保持不變的值。 每個參數值範本中用於定義部署的資源。

範本定義下列參數︰

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

若要建立服務匯流排命名空間的名稱。

```
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName

建立服務匯流排命名空間中主題的名稱。

```
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusSubscriptionName

服務匯流排命名空間中建立的訂閱名稱。

```
"serviceBusSubscriptionName": {
"type": "string"
}
```
### <a name="servicebusrulename"></a>serviceBusRuleName

建立服務匯流排命名空間中 rule(filter) 名稱。

```
   "serviceBusRuleName": {
   "type": "string",
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

建立標準服務匯流排命名空間類型**訊息**的主題和訂閱與規則。

```
 "resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "sku": {
            "name": "Standard",
            "tier": "Standard"
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusTopicName')]",
            "type": "Topics",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusTopicName')]"
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {},
                "resources": [{
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusRuleName')]",
                    "type": "Rules",
                    "dependsOn": [
                        "[parameters('serviceBusSubscriptionName')]"
                    ],
                    "properties": {
                        "filter": {
                            "sqlExpression": "StoreName = 'Store1'"
                        },
                        "action": {
                            "sqlExpression": "set FilterTag = 'true'"
                        }
                    }
                }]
            }]
        }]
    }]
```

## <a name="commands-to-run-deployment"></a>若要執行部署] 命令

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI

```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="next-steps"></a>後續步驟

現在，您建立及部署使用 Azure 資源管理員的資源，瞭解如何檢視這些文件管理這些資源︰

- [管理 Azure 服務匯流排使用 Azure 自動化](service-bus-automation-manage.md)
- [管理服務匯流排使用 PowerShell](service-bus-powershell-how-to-provision.md)
- [管理服務匯流排資源服務匯流排檔案總管](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)


  [撰寫 Azure 資源管理員範本]: ../resource-group-authoring-templates.md
  [Azure 快速入門範本]: https://azure.microsoft.com/documentation/templates/?term=service+bus
  [Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
  [Azure 資源命名慣例]: https://azure.microsoft.com/en-us/documentation/articles/guidance-naming-conventions/
  [服務匯流排主題、 訂閱及規則命名空間]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-subscription-rule/
  [服務匯流排佇列主題，以及訂閱]:service-bus-queues-topics-subscriptions.md
  
