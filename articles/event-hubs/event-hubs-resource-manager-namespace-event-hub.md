<properties
    pageTitle="使用事件中樞和消費者群組使用 Azure 資源管理員範本建立的事件集線器命名空間 |Microsoft Azure"
    description="使用事件中心] 和 [消費者群組使用 Azure 資源管理員範本建立的事件集線器命名空間"
    services="event-hubs"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="08/31/2016"
    ms.author="sethm;shvija"/>

# <a name="create-an-event-hubs-namespace-with-event-hub-and-consumer-group-using-an-azure-resource-manager-template"></a>使用事件中樞和消費者群組使用 Azure 資源管理員範本建立的事件集線器命名空間

本文說明如何使用 Azure 資源管理員範本所建立的事件集線器命名空間事件中心與消費者群組。 您將學習如何定義部署的資源，以及如何定義的參數會指定部署執行時。 您可以使用這個範本，針對您自己的部署，或自訂，以符合您的需求

如需有關如何建立範本的詳細資訊，請參閱[撰寫 Azure 資源管理員範本][]。

完成的範本，請參閱[事件中樞和消費者群組範本][]GitHub 上。

>[AZURE.NOTE]
>若要檢查的最新的範本，請造訪[Azure 快速入門範本][]組件庫和事件集線器搜尋。

## <a name="what-will-you-deploy"></a>您將部署什麼？

利用此範本，您會部署事件中心與消費者群組事件集線器命名空間。

[事件集線器](../event-hubs/event-hubs-what-is-event-hubs.md)是處理用來提供事件與遙測輸入 Azure 龐大的小數位數，在高可靠性低延遲與服務的事件。

若要自動執行的部署，請按一下 [動作] 按鈕︰

[![部署至 Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

## <a name="parameters"></a>參數

使用 Azure 資源管理員中，您可以定義您想要指定部署範本時的值的參數。 範本包含名稱為`Parameters`含有所有參數值。 您應該定義的參數會根據您要部署的專案，或根據您要部署到的環境而這些值。 不會定義參數會永遠保持不變的值。 每個參數值範本中用於定義部署的資源。

範本定義下列參數。

### <a name="eventhubnamespacename"></a>eventHubNamespaceName

若要建立的事件集線器命名空間的名稱。

```
"eventHubNamespaceName": {
"type": "string"
}
```

### <a name="eventhubname"></a>eventHubName

建立集線器命名空間的事件中的 [事件] 中心名稱。

```
"eventHubName": {
"type": "string"
}
```

### <a name="eventhubconsumergroupname"></a>eventHubConsumerGroupName

建立事件集線器消費者群組的名稱。

```
"eventHubConsumerGroupName": {
"type": "string"
}
```

### <a name="apiversion"></a>apiVersion

範本的 API 版本。

```
"apiVersion": {
"type": "string"
}
```

## <a name="resources-to-deploy"></a>部署資源

建立事件中心與消費者群組的類型**EventHubs**的命名空間。

```
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('namespaceName')]",
         "type":"Microsoft.EventHub/Namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources":[  
            {  
               "apiVersion":"[variables('ehVersion')]",
               "name":"[parameters('eventHubName')]",
               "type":"EventHubs",
               "dependsOn":[  
                  "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
               ],
               "properties":{  
                  "path":"[parameters('eventHubName')]"
               },
               "resources":[  
                  {  
                     "apiVersion":"[variables('ehVersion')]",
                     "name":"[parameters('consumerGroupName')]",
                     "type":"ConsumerGroups",
                     "dependsOn":[  
                        "[parameters('eventHubName')]"
                     ],
                     "properties":{  

                     }
                  }
               ]
            }
         ]
      }
   ],
```

## <a name="commands-to-run-deployment"></a>若要執行部署] 命令

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json
```

## <a name="azure-cli"></a>Azure CLI

```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json][]
```

[撰寫 Azure 資源管理員範本]: ../resource-group-authoring-templates.md
[Azure 快速入門範本]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[事件的中樞和消費者] 群組中的範本]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
