<properties 
    pageTitle="佈建意指快取 |Microsoft Azure" 
    description="使用 Azure 資源管理員範本部署 Azure Redis 快取。" 
    services="app-service" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="web" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/27/2016" 
    ms.author="sdanie"/>

# <a name="create-a-redis-cache-using-a-template"></a>建立使用範本的 Redis 快取

本主題中，您可以瞭解如何建立可部署 Azure Redis 快取的 Azure 資源管理員範本。 快取可以使用現有的儲存空間帳戶診斷資料。 您也可以瞭解如何定義部署的資源，及如何定義的參數會指定執行部署時。 您可以使用您自己的部署，這個範本，或自訂以符合您的需求。

目前訂閱的同一個區域中的所有快取共用診斷設定。 更新一個區域中的快取會影響所有區域的快取。

如需有關如何建立範本的詳細資訊，請參閱[撰寫 Azure 資源管理員範本](../resource-group-authoring-templates.md)。

完成的範本，請參閱[Redis 快取] 範本](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json)。

>[AZURE.NOTE] 新的[進階版層](cache-premium-tier-intro.md)資源管理員範本可供使用。 
>
>-    [建立進階版 Redis 快取與叢集](https://azure.microsoft.com/documentation/templates/201-redis-premium-cluster-diagnostics/)
>-    [建立進階版 Redis 快取的資料](https://azure.microsoft.com/documentation/templates/201-redis-premium-persistence/)
>-    [建立進階版 Redis 快取與 VNet 選擇性叢集](https://azure.microsoft.com/documentation/templates/201-redis-premium-vnet-cluster-diagnostics/)
>
>若要檢查的最新的範本，請參閱[Azure 快速入門範本](https://azure.microsoft.com/documentation/templates/)，然後搜尋`Redis Cache`。

## <a name="what-you-will-deploy"></a>將部署

此範本，您會部署 Azure Redis 快取診斷資料使用現有的儲存空間帳戶。

若要自動執行的部署，請按一下 [動作] 按鈕︰

[![部署至 Azure](./media/cache-redis-cache-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="parameters"></a>參數

使用 Azure 資源管理員中，您可以定義您想要指定部署範本時的值的參數。 範本包含稱為參數的參數值的所有節。
您應該定義的參數，視情況而定根據您要部署的專案，或根據您要部署到環境這些值。 不會定義參數永遠保持不變的值。 每個參數值範本中用於定義部署的資源。 


[AZURE.INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### <a name="rediscachelocation"></a>redisCacheLocation

Redis 快取的位置。 最佳效能，請使用作為應用程式的同一個位置，使用快取。

    "redisCacheLocation": {
      "type": "string"
    }

### <a name="existingdiagnosticsstorageaccountname"></a>existingDiagnosticsStorageAccountName

若要使用的診斷現有的儲存體帳戶名稱。 

    "existingDiagnosticsStorageAccountName": {
      "type": "string"
    }

### <a name="enablenonsslport"></a>enableNonSslPort

布林值，指出是否允許存取透過非 SSL 連接埠。

    "enableNonSslPort": {
      "type": "bool"
    }

### <a name="diagnosticsstatus"></a>diagnosticsStatus

指出是否已啟用診斷的值。 使用 [開啟] 或 [關閉]。

    "diagnosticsStatus": {
      "type": "string",
      "defaultValue": "ON",
      "allowedValues": [
            "ON",
            "OFF"
        ]
    }
    
## <a name="resources-to-deploy"></a>部署資源

### <a name="redis-cache"></a>Redis 快取

建立 Azure 意指快取。

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('redisCacheName')]",
      "type": "Microsoft.Cache/Redis",
      "location": "[parameters('redisCacheLocation')]",
      "properties": {
        "enableNonSslPort": "[parameters('enableNonSslPort')]",
        "sku": {
          "capacity": "[parameters('redisCacheCapacity')]",
          "family": "[parameters('redisCacheFamily')]",
          "name": "[parameters('redisCacheSKU')]"
        }
      },
      "resources": [
        {
          "apiVersion": "2015-07-01",
          "type": "Microsoft.Cache/redis/providers/diagnosticsettings",
          "name": "[concat(parameters('redisCacheName'), '/Microsoft.Insights/service')]",
          "location": "[parameters('redisCacheLocation')]",
          "dependsOn": [
            "[concat('Microsoft.Cache/Redis/', parameters('redisCacheName'))]"
          ],
          "properties": {
            "status": "[parameters('diagnosticsStatus')]",
            "storageAccountName": "[parameters('existingDiagnosticsStorageAccountName')]"
          }
        }
      ]
    }



## <a name="commands-to-run-deployment"></a>若要執行部署] 命令

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)] 

### <a name="powershell"></a>PowerShell

    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup -redisCacheName ExampleCache -redisCacheLocation "West US"

### <a name="azure-cli"></a>Azure CLI

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -g ExampleDeployGroup


