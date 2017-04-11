<properties
   pageTitle="在 [資源管理員範本的相依性 |Microsoft Azure"
   description="說明如何設定為相依於其他資源的某個資源，以確保正確的順序部署資源的部署期間。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/12/2016"
   ms.author="tomfitz"/>

# <a name="defining-dependencies-in-azure-resource-manager-templates"></a>在範本中 Azure 資源管理員定義的相依性

針對指定的資源，可以部署資源之前，必須存在其他資源。 例如，SQL server 必須存在於之前，先部署的 SQL 資料庫。 您可以定義此關聯，可將某個資源為依存於其他的資源。 一般而言，您所定義的相依性**dependsOn**項目，但您也可以定義它透過**參考**函數。 

資源管理員評估資源之間的相依性，並部署其其從屬參照的順序。 未相互依存性資源時，資源管理員部署其平行。

## <a name="dependson"></a>dependsOn

在您的範本，dependsOn 元素可讓您根據一或多個資源為定義某個資源。 其值可逗點分隔資源名稱的清單。 

下列範例顯示取決於負載平衡器、 虛擬網路，以及建立多個儲存帳戶循環播放虛擬機器縮放比例設定。 下列範例中，不會顯示這些其他的資源，但就需要存在於別處範本中。

    {
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "name": "[variables('namingInfix')]",
      "location": "[variables('location')]",
      "apiVersion": "2016-03-30",
      "tags": {
        "displayName": "VMScaleSet"
      },
      "dependsOn": [
        "storageLoop",
        "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
      ],
      ...
    }

若要定義資源與資源的整個複製迴圈建立之間的相依性，將 dependsOn 項目為循環播放的名稱。 例如，請參閱[建立資源 Azure 資源管理員] 中的多個執行個體](resource-group-create-multiple.md)。

雖然您可能需要使用 dependsOn 對應資源之間的關聯，務必瞭解為什麼您的動作，因為它會影響您的部署的效能。 例如，文件資源的互相、 dependsOn 不正確的方法。 您無法查詢部署後 dependsOn 項目中定義的資源。 藉由使用 dependsOn，您可能會影響部署時間因為資源管理員不會部署平行的兩個資源，有相依性。 文件資源之間的關聯，請改為使用 [[資源連結](resource-group-link-resources.md)。

## <a name="child-resources"></a>子資源

資源屬性可讓您指定子相關資源的資源所定義。 子資源只能定義五個層。 請務必注意子資源與上層資源之間不會建立隱含的相依性。 如果您需要子資源部署父資源之後，您必須明確敘述 dependsOn 屬性的相依性。 

每個父資源為子資源接受特定資源類型。 父項資源的[範本結構描述](https://github.com/Azure/azure-resource-manager-schemas)中指定已接受的資源類型。 子資源類型的名稱包含上層資源類型的名稱，例如**Microsoft.Web/sites/config**和**Microsoft.Web/sites/extensions**是**Microsoft.Web/sites**的兩個子資源。

下列範例顯示的 SQL server 和 SQL 資料庫。 即使資料庫伺服器的子標題，請注意，明確的相依性 SQL 資料庫與 SQL server 之間定義。

    "resources": [
      {
        "name": "[variables('sqlserverName')]",
        "type": "Microsoft.Sql/servers",
        "location": "[resourceGroup().location]",
        "tags": {
          "displayName": "SqlServer"
        },
        "apiVersion": "2014-04-01-preview",
        "properties": {
          "administratorLogin": "[parameters('administratorLogin')]",
          "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
        },
        "resources": [
          {
            "name": "[parameters('databaseName')]",
            "type": "databases",
            "location": "[resourceGroup().location]",
            "tags": {
              "displayName": "Database"
            },
            "apiVersion": "2014-04-01-preview",
            "dependsOn": [
              "[variables('sqlserverName')]"
            ],
            "properties": {
              "edition": "[parameters('edition')]",
              "collation": "[parameters('collation')]",
              "maxSizeBytes": "[parameters('maxSizeBytes')]",
              "requestedServiceObjectiveName": "[parameters('requestedServiceObjectiveName')]"
            }
          }
        ]
      }
    ]


## <a name="reference-function"></a>參照函數

[參照函數](resource-group-template-functions.md#reference)可讓您從其他 JSON 名稱和值組或 runtime 資源衍生其值的運算式。 參照運算式隱含宣告某個資源取決於另一個。 

    reference('resourceName').propertyPath

您可以使用這個項目或 dependsOn 項目指定的相依性，但您不需要使用兩者相同從屬參照資源。 可能的話，使用隱含的參照，以避免意外新增不必要的相依性。

若要深入瞭解，請參閱[參考函數](resource-group-template-functions.md#reference)。

## <a name="next-steps"></a>後續步驟

- 若要瞭解如何建立 Azure 資源管理員範本，請參閱[撰寫範本](resource-group-authoring-templates.md)。 
- 如需可用範本中的功能清單，請參閱[範本函數](resource-group-template-functions.md)。

