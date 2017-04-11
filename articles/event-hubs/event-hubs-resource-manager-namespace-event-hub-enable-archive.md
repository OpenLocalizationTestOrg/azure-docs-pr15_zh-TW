<properties
    pageTitle="建立事件中樞的事件集線器命名空間，並啟用透過 Azure 資源管理員範本的封存 |Microsoft Azure"
    description="使用事件中心建立的事件集線器命名空間，並啟用封存使用 Azure 資源管理員範本"
    services="event-hubs"
    documentationCenter=".net"
    authors="ShubhaVijayasarathy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="09/14/2016"
    ms.author="ShubhaVijayasarathy"/>

# <a name="create-an-event-hubs-namespace-with-event-hub-and-enable-archive-using-an-azure-resource-manager-template"></a>建立事件中樞的事件集線器命名空間，並啟用封存使用 Azure 資源管理員範本

本文說明如何使用 Azure 資源管理員範本，建立事件中樞的事件集線器命名空間，並讓您事件集線器封存。 您瞭解如何以定義部署的資源，以及如何定義的參數會指定部署執行時。 您可以使用這個範本，針對您自己的部署，或自訂，以符合您的需求

如需有關如何建立範本的詳細資訊，請參閱[撰寫 Azure 資源管理員的範本][]。

練習的詳細資訊和 Azure 資源圖樣的命名慣例，請參閱[Azure 資源的命名慣例][]。

完成的範本，請參閱 GitHub 上的[事件中樞和啟用封存的範本][]。

>[AZURE.NOTE]
>若要檢查的最新的範本，請造訪[Azure 快速入門][]範本庫和事件集線器搜尋。

## <a name="what-you-deploy"></a>您將的部署？

利用此範本，您可以部署事件中樞事件集線器命名空間，並啟用封存。

[事件集線器](../event-hubs/event-hubs-what-is-event-hubs.md)是處理用來提供事件與遙測輸入 Azure 龐大的小數位數，在高可靠性低延遲與服務的事件。 事件集線器封存會讓您在自動傳送指定的時間或您所選擇的大小間隔內到您所選擇的 Azure Blob 儲存體您事件集線器串流的資料。

若要自動執行的部署，請按一下 [動作] 按鈕︰

[![部署至 Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-archive%2Fazuredeploy.json)

## <a name="parameters"></a>參數

使用 Azure 資源管理員中，您可以定義您想要指定部署範本時的值的參數。 範本包含名稱為`Parameters`包含的所有參數值。 您應該定義的參數，視情況而定根據您要部署的專案，或根據您要部署到環境這些值。 不會定義參數永遠保持不變的值。 每個參數值範本中用於定義部署的資源。

範本定義下列參數。

### <a name="eventhubnamespacename"></a>eventHubNamespaceName

若要建立的事件集線器命名空間的名稱。

```
"eventHubNamespaceName":{  
     "type":"string",
     "metadata":{  
         "description":"Name of the EventHub namespace"
      }
}
```

### <a name="eventhubname"></a>eventHubName

建立集線器命名空間的事件中的 [事件] 中心名稱。

```
"eventHubName":{  
    "type":"string",
    "metadata":{  
        "description":"Name of the Event Hub"
    }
}
```

### <a name="messageretentionindays"></a>messageRetentionInDays

您要保留的郵件在您的事件中心內的天數。 

```
"messageRetentionInDays":{
    "type":"int",
    "defaultValue": 1,
    "minValue":"1",
    "maxValue":"7",
    "metadata":{
       "description":"How long to retain the data in Event Hub"
     }
 }
```

### <a name="partitioncount"></a>partitionCount

您想要在您的事件中心的磁碟分割區的數字。

```
"partitionCount":{
    "type":"int",
    "defaultValue":2,
    "minValue":2,
    "maxValue":32,
    "metadata":{
        "description":"Number of partitions chosen"
    }
 }
```

### <a name="archiveenabled"></a>archiveEnabled

啟用您的事件集線器封存。

```
"archiveEnabled":{
    "type":"string",
    "defaultValue":"true",
    "allowedValues": [
    "false",
    "true"],
    "metadata":{
        "description":"Enable or disable the Archive for your Event Hub"
    }
 }
```
### <a name="archiveencodingformat"></a>archiveEncodingFormat

指定序列化事件資料編碼格式。

```
"archiveEncodingFormat":{
    "type":"string",
    "defaultValue":"Avro",
    "allowedValues":[
    "Avro"],
    "metadata":{
        "description":"The encoding format Archive serializes the EventData"
    }
}
```

### <a name="archivetime"></a>archiveTime

[封存啟動封存 Azure blob 儲存體中的資料的時間間隔。

```
"archiveTime":{
    "type":"int",
    "defaultValue":300,
    "minValue":60,
    "maxValue":900,
    "metadata":{
         "description":"the time window in seconds for the archival"
    }
}
```

### <a name="archivesize"></a>archiveSize

[封存啟動封存 Azure blob 儲存體中的資料大小間隔。

```
"archiveSize":{
    "type":"int",
    "defaultValue":314572800,
    "minValue":10485760,
    "maxValue":524288000,
    "metadata":{
        "description":"the size window in bytes for archival"
    }
}
```

### <a name="destinationstorageaccountresourceid"></a>destinationStorageAccountResourceId

[封存需要儲存帳戶資源識別碼，若要啟用封存到您想要的 Azure 儲存體。

```
 "destinationStorageAccountResourceId":{
    "type":"string",
    "metadata":{
        "description":"Your existing storage Account resource id where you want the blobs be archived"
    }
 }
```

### <a name="blobcontainername"></a>blobContainerName

封存 blob 容器要事件資料的位置。

```
 "blobContainerName":{
    "type":"string",
    "metadata":{
        "description":"Your existing storage Container that you want the blobs archived in"
    }
}
```


### <a name="apiversion"></a>apiVersion

範本的 API 版本。

```
 "apiVersion":{  
    "type":"string",
    "defaultValue":"2015-08-01",
    "metadata":{  
        "description":"ApiVersion used by the template"
    }
 }
```

## <a name="resources-to-deploy"></a>部署資源

建立事件中樞的**EventHubs**，類型命名空間，並讓封存。

```
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('eventHubNamespaceName')]",
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
                  "[concat('Microsoft.EventHub/namespaces/', parameters('eventHubNamespaceName'))]"
               ],
               "properties":{  
                  "path":"[parameters('eventHubName')]",
                  "MessageRetentionInDays":"[parameters('messageRetentionInDays')]",
                  "PartitionCount":"[parameters('partitionCount')]",
                  "ArchiveDescription":{
                        "enabled":"[parameters('archiveEnabled')]",
                        "encoding":"[parameters('archiveEncodingFormat')]",
                        "intervalInSeconds":"[parameters('archiveTime')]",
                        "sizeLimitInBytes":"[parameters('archiveSize')]",
                        "destination":{
                            "name":"EventHubArchive.AzureBlockBlob",
                            "properties":{
                                "StorageAccountResourceId":"[parameters('destinationStorageAccountResourceId')]",
                                "BlobContainer":"[parameters('blobContainerName')]"
                            }
                        } 
                  }
                  
               }
               
            }
         ]
      }
   ]
```

## <a name="commands-to-run-deployment"></a>若要執行部署] 命令

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-archive/azuredeploy.json
```

## <a name="azure-cli"></a>Azure CLI

```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-archive/azuredeploy.json][]
```

[撰寫 Azure 資源管理員範本]: ../resource-group-authoring-templates.md
[Azure 快速入門範本]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event Hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-eventhubs-create-namespace-and-enable-archive/
[Azure 資源命名慣例]: https://azure.microsoft.com/en-us/documentation/articles/guidance-naming-conventions/
[事件中樞和啟用封存範本]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-archive
