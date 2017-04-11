<properties
    pageTitle="密度裝載於 Azure 應用程式服務上 |Microsoft Azure"
    description="密度裝載於 Azure 應用程式服務"
    authors="btardif"
    manager="wpickett"
    editor=""
    services="app-service\web"
    documentationCenter=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="byvinyal"/>

# <a name="high-density-hosting-on-azure-app-service"></a>密度裝載於 Azure 應用程式服務

使用應用程式服務，您的應用程式被分離容量由兩個概念配置以便從︰

- **應用程式︰**代表應用程式，然後執行階段設定。 例如，其包含的.NET 版本的載入執行階段，應用程式設定等。

- **應用程式服務方案︰**定義特性的容量、 提供的功能集，以及應用程式的位置。 例如，大型 （四個核心） 機器、 四個執行個體中設定適用於美國的進階功能，可能是特性。

應用程式一定會連結至應用程式服務方案，但計劃的應用程式服務可以提供一或多個應用程式的能力。

如此一來，平台提供隔離在單一應用程式或多個應用程式共用資源共用的應用程式服務方案。

不過，多個應用程式共用的應用程式服務方案時，該應用程式的執行個體上執行，該應用程式服務計劃的每個例項。

## <a name="per-app-scaling"></a>每個應用程式的縮放比例
*每個應用程式縮放*功能是可以啟用應用程式服務方案層級，然後使用每個應用程式的功能。

每個應用程式縮放比例調整大小從主控的應用程式服務方案應用程式。 如此一來，您可以設定的應用程式服務方案提供 10 個執行個體，但應用程式可供設定，這些只 5 不按比例縮放。

下列 Azure 資源管理員範本所建立的 10 個執行個體與應用程式設定為使用每個應用程式的縮放比例，並只 5 的執行個體不按比例縮放比例出的應用程式服務方案。

應用程式服務方案會將**每個網站縮放比例**] 屬性設為 true ( `"perSiteScaling": true`)。 應用程式設定用於 5**的工作者數目**(`"properties": { "numberOfWorkers": "5" }`)。

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters":{
            "appServicePlanName": { "type": "string" },
            "appName": { "type": "string" }
         },
        "resources": [
        {
            "comments": "App Service Plan with per site perSiteScaling = true",
            "type": "Microsoft.Web/serverFarms",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "size": "S1",
                "family": "S",
                "capacity": 10
                },
            "name": "[parameters('appServicePlanName')]",
            "apiVersion": "2015-08-01",
            "location": "West US",
            "properties": {
                "name": "[parameters('appServicePlanName')]",
                "perSiteScaling": true
            }
        },
        {
            "type": "Microsoft.Web/sites",
            "name": "[parameters('appName')]",
            "apiVersion": "2015-08-01-preview",
            "location": "West US",
            "dependsOn": [ "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" ],
            "properties": { "serverFarmId": "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" },
            "resources": [ {
                    "comments": "",
                    "type": "config",
                    "name": "web",
                    "apiVersion": "2015-08-01",
                    "location": "West US",
                    "dependsOn": [ "[resourceId('Microsoft.Web/Sites', parameters('appName'))]" ],
                    "properties": { "numberOfWorkers": "5" }
             } ]
         }]
    }


## <a name="recommended-configuration-for-high-density-hosting"></a>密度裝載的建議的設定

每個應用程式縮放為公用 Azure 區域與應用程式服務環境中已啟用的功能。 不過，建議使用的策略是容量的使用應用程式服務環境才能利用其進階功能和較大的集區。  

請遵循下列步驟，設定密度裝載您的應用程式︰

1. 設定應用程式服務環境，然後選擇 [致力於為密度裝載案例背景工作集區。

1. 建立單一的應用程式服務方案，並不按比例縮放以使用工作者集區上的所有可用的容量。

1. 設為 true 的每個網站的縮放比例旗標上的應用程式服務方案。

1. 建立新的網站和分派給該應用程式服務方案**numberOfWorkers**屬性設為**1**。 使用此設定會產生此工作者集區可能的最高密度。

1. 可以每個網站授與其他資源，視需要獨立設定的工作者數目。 例如，高使用網站可能**3**低用於網站會將**numberOfWorkers**設為**1**時擁有更多的處理容量，讓該應用程式中，設定**numberOfWorkers** 。
