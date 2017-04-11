<properties
   pageTitle="撰寫 Azure 資源管理員範本 |Microsoft Azure"
   description="建立使用宣告式 JSON 語法部署 Azure 應用程式的 Azure 資源管理員範本。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="tomfitz"/>

# <a name="authoring-azure-resource-manager-templates"></a>撰寫 Azure 資源管理員範本

本主題說明 Azure 資源管理員範本的結構。 會呈現不同各區段的範本都可在這些節中的屬性。 範本包含 JSON 和可用來建構您的部署中的值的運算式。 

若要檢視您已經部署的資源的範本，請參閱[將現有的資源從 Azure 資源管理員範本匯出](resource-manager-export-template.md)。 如需建立的範本，請參閱[資源管理員範本逐步解說](resource-manager-template-walkthrough.md)。 建立範本的相關建議，請參閱[建立 Azure 資源管理員範本的最佳作法](resource-manager-template-best-practices.md)。

建議的 JSON 編輯器可簡化建立範本的工作。 使用 Visual Studio 範本的相關資訊，請參閱[建立及部署 Visual Studio 透過 Azure 資源群組](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)。 有關如何使用與程式碼，請參閱[Azure 資源管理員範本，在 Visual Studio 程式碼中使用](resource-manager-vs-code.md)。

1 MB、 範本與每個參數檔案為 64 KB，則限制大小。 1 MB 限制適用於範本的最終狀態之後展開反覆運算資源定義與的變數和參數值。 

## <a name="template-format"></a>範本格式

在其最簡單的結構，範本會包含下列元素︰

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

| 項目名稱   | 所需 | 描述
| :------------: | :------: | :----------
| $schema        |   [是]    | 將說明範本語言版本 JSON 結構描述檔案的位置。 使用在上述範例中所顯示的 URL。
| contentVersion |   [是]    | 版本的範本 （例如 1.0.0.0)。 您可以將任何值提供此項目。 部署時使用範本的資源，此值可用來確認使用的合適的範本。
| 參數     |   無     | 部署自訂資源部署執行時所提供的值。
| 變數      |   無     | 以簡化範本語言運算式作為 JSON 片段，在範本中的值。
| 資源      |   [是]    | 部署或更新的資源] 群組中的資源類型。
| 輸出        |   無     | 部署後所傳回的值。

我們檢查本主題稍後的詳細範本的節。 現在，我們會檢閱部分組成範本的語法。

## <a name="expressions-and-functions"></a>運算式及函數

基本範本的語法是 JSON。 不過，運算式及函數延伸 JSON 所提供的範本中。 使用運算式，您可以建立不是嚴格的常值的值。 使用括弧括住運算式 [和]，並部署範本時，會進行評估。 運算式可以出現 JSON 字串值中的任何位置，並傳回另一個 JSON 值。 如果您需要使用文字字串的開始使用括號 [，您必須使用兩個括號 [[。

一般而言，您使用的運算式函數來執行設定部署的作業。 就像在 JavaScript] 函數呼叫會格式化為**functionName(arg1,arg2,arg3)**。 您可以使用點和 [索引] 運算子參考內容。

下列範例會示範如何建構值時使用多個功能︰
 
    "variables": {
       "location": "[resourceGroup().location]",
       "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
       "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
    }

範本函數的完整清單，請參閱[Azure 資源管理員範本函數](resource-group-template-functions.md)。 


## <a name="parameters"></a>參數

在 [範本的 [參數] 區段中，您可以指定哪些部署資源時，您可以輸入的值。 下列參數值可讓您自訂部署為特定的環境 （例如開發、 測試和生產） 提供量身訂做的值。 您沒有提供您的範本中的參數，但不含參數範本會永遠部署相同資源的相同的名稱、 位置及屬性。

若要設定部署資源的值，您可以使用整個範本下列參數值。 範本的其他區段中，可在 [參數] 區段中宣告的參數。

您定義具有下列結構的參數︰

    "parameters": {
       "<parameter-name>" : {
         "type" : "<type-of-parameter-value>",
         "defaultValue": "<default-value-of-parameter>",
         "allowedValues": [ "<array-of-allowed-values>" ],
         "minValue": <minimum-value-for-int>,
         "maxValue": <maximum-value-for-int>,
         "minLength": <minimum-length-for-string-or-array>,
         "maxLength": <maximum-length-for-string-or-array-parameters>,
         "metadata": {
             "description": "<description-of-the parameter>" 
         }
       }
    }

| 項目名稱   | 所需 | 描述
| :------------: | :------: | :----------
| parameterName  |   [是]    | 參數名稱。 必須是有效的 JavaScript 識別碼。
| 類型           |   [是]    | 輸入參數值]。 請參閱下方的可容許的類型。
| 預設值]   |   無     | 如果沒有值提供給參數的參數的預設值。
| allowedValues  |   無     | 允許值參數，請確定提供正確的值的陣列。
| minValue       |   無     | Int 類型參數的最小值，此值都包含在內。
| maxValue       |   無     | Int 類型參數的最大值，此值都包含在內。
| minLength      |   無     | 字串、 secureString 及陣列輸入參數值的最小長度，此值都包含在內。
| maxLength      |   無     | 字串、 secureString 及陣列輸入參數值的最大長度，此值都包含在內。
| 描述    |   無     | 參數，這透過入口網站的自訂範本介面範本的使用者所顯示的描述。

允許的類型和值是︰

- **字串**
- **secureString**
- **int**
- **bool**
- **物件** 
- **secureObject**
- **陣列**

若要指定為選用的參數，提供 （可以留空字串） 的預設值]。 

如果您指定符合其中一個命令部署範本中參數的參數名稱，系統會提示您提供使用的後置**FromTemplate**參數的值。 例如，如果包含名**ResourceGroupName**為在範本中的參數的是**ResourceGroupName**參數中[新增 AzureRmResourceGroupDeployment]相同[deployment2cmdlet]cmdlet，當系統提示您提供**ResourceGroupNameFromTemplate**的值。 一般而言，您應避免混淆，不使用相同的名稱以用於部署作業的參數命名參數。

>[AZURE.NOTE] 所有的密碼、 索引鍵和其他機密應該使用**secureString**類型。 資源部署後，便無法讀取 secureString 類型與範本參數。 

下列範例會示範如何定義參數︰

    "parameters": {
      "siteName": {
        "type": "string",
        "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]"
      },
      "hostingPlanName": {
        "type": "string",
        "defaultValue": "[concat(parameters('siteName'),'-plan')]"
      },
      "skuName": {
        "type": "string",
        "defaultValue": "F1",
        "allowedValues": [
          "F1",
          "D1",
          "B1",
          "B2",
          "B3",
          "S1",
          "S2",
          "S3",
          "P1",
          "P2",
          "P3",
          "P4"
        ]
      },
      "skuCapacity": {
        "type": "int",
        "defaultValue": 1,
        "minValue": 1
      }
    }

如何輸入參數值部署時，請參閱[部署具有 Azure 資源管理員範本的應用程式](resource-group-template-deploy.md#parameter-file)。 

## <a name="variables"></a>變數

變數在區段中，您會在您的範本建構可用的值。 一般而言，變數根據提供的參數值。 您不需要定義變數，但它們通常簡化您的範本來減少複雜的運算式。

您定義變數結構如下︰

    "variables": {
       "<variable-name>": "<variable-value>",
       "<variable-name>": { 
           <variable-complex-type-value> 
       }
    }

下列範例會示範如何定義公式的結構從兩個參數值的變數︰

     "variables": {
       "connectionString": "[concat('Name=', parameters('username'), ';Password=', parameters('password'))]"
    }

下一個範例顯示的變數是複雜的 JSON 類型，並從其他變數建構的變數︰

    "parameters": {
       "environmentName": {
         "type": "string",
         "allowedValues": [
           "test",
           "prod"
         ]
       }
    },
    "variables": {
       "environmentSettings": {
         "test": {
           "instancesSize": "Small",
           "instancesCount": 1
         },
         "prod": {
           "instancesSize": "Large",
           "instancesCount": 4
         }
       },
       "currentEnvironmentSettings": "[variables('environmentSettings')[parameters('environmentName')]]",
       "instancesSize": "[variables('currentEnvironmentSettings').instancesSize]",
       "instancesCount": "[variables('currentEnvironmentSettings').instancesCount]"
    }

## <a name="resources"></a>資源

在 [資源] 區段中，您可以定義部署或更新的資源。 本節可以變得很複雜，因為您必須先瞭解您要部署提供正確的值的類型。 

您可以在定義具有下列結構的資源︰

    "resources": [
       {
         "apiVersion": "<api-version-of-resource>",
         "type": "<resource-provider-namespace/resource-type-name>",
         "name": "<name-of-the-resource>",
         "location": "<location-of-resource>",
         "tags": "<name-value-pairs-for-resource-tagging>",
         "comments": "<your-reference-notes>",
         "dependsOn": [
           "<array-of-related-resource-names>"
         ],
         "properties": "<settings-for-the-resource>",
         "copy": {
           "name": "<name-of-copy-loop>",
           "count": "<number-of-iterations>"
         }
         "resources": [
           "<array-of-child-resources>"
         ]
       }
    ]

| 項目名稱             | 所需 | 描述
| :----------------------: | :------: | :----------
| apiVersion               |   [是]    | 要用來建立資源 REST API 的版本。
| 類型                     |   [是]    | 資源類型。 此值是資源提供者和資源類型 （例如**Microsoft.Storage/storageAccounts**) 的命名空間的組合。
| 名稱                     |   [是]    | 資源的名稱。 名稱必須遵循 RFC3986 中定義的 URI 元件限制。 此外，公開外廠商驗證，確保它的名稱的資源名稱的 Azure 服務不嘗試詐騙另一個身分識別。 請參閱[檢查資源名稱](https://msdn.microsoft.com/library/azure/mt219035.aspx)。
| 位置                 |   變化  | 提供的資源的支援的地理位置。 您可以選取任何可用的位置，但通常這麼做，從中挑選接近您的使用者。 通常，也讓放置在相同地區彼此互動的資源。 大部分的資源類型需要一個位置，但某些類型 （例如角色指派） 不需要的位置。
| 標記                     |   無     | 與資源相關聯的標籤。
| 註解                 |   無     | 您的筆記記錄在範本中的資源
| dependsOn                |   無     | 取決於所定義的資源的資源。 資源之間的相依性的評估，和其相依順序部署資源。 未相互依存性資源時，他們會部署平行。 值可以逗點分隔資源的清單名稱或資源的唯一識別碼。
| 屬性               |   無     | 特定資源設定的設定。 屬性值是為您提供在邀請內文中建立資源 REST API 作業 （放入方法） 的值相同。 資源結構描述文件或 REST API 的連結，請參閱[資源管理員提供者、 區域、 API 版本和結構描述](resource-manager-supported-services.md)。
| 複製                     |   無     | 如果需要多個執行個體，若要建立資源的數目。 如需詳細資訊，請參閱[建立資源 Azure 資源管理員] 中的多個執行個體](resource-group-create-multiple.md)。 |
| 資源                |   無     | 子取決於所定義的資源的資源。 您可以提供父項資源的結構描述所允許的資源類型。 子資源類型的完整的名稱包含上層資源類型，例如**Microsoft.Web/sites/extensions**。 父項資源之間的相依性不表示;您必須明確定義的相依性。 

了解值指定**apiVersion**，**類型**和**位置**不顯而易見。 所幸，您可以決定透過 PowerShell 的 Azure 或 Azure CLI 這些值。

使用**PowerShell**相關的所有資源提供者，請使用︰

    Get-AzureRmResourceProvider -ListAvailable

傳回清單中，尋找您感興趣的資源提供者。 若要取得資源提供者 （例如儲存區） 的資源類型，請使用︰

    (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Storage).ResourceTypes

若要取得的資源類型 （例如儲存帳戶） 中的 API 版本，請使用︰

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Storage).ResourceTypes | Where-Object ResourceTypeName -eq storageAccounts).ApiVersions

若要取得支援的資源類型的位置，請使用︰

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Storage).ResourceTypes | Where-Object ResourceTypeName -eq storageAccounts).Locations

**Azure CLI**與相關的所有資源提供者，請使用︰

    azure provider list

傳回清單中，尋找您感興趣的資源提供者。 若要取得資源提供者 （例如儲存區） 的資源類型，請使用︰

    azure provider show Microsoft.Storage

若要取得支援的位置和 API 版本，請使用︰

    azure provider show Microsoft.Storage --details --json

若要進一步瞭解資源提供者，請參閱[資源管理員提供者、 區域、 API 版本和結構描述](resource-manager-supported-services.md)。

[資源] 區段包含陣列的部署資源。 在每個資源，您也可以定義子資源的陣列。 因此，您的 [資源] 區段可能的結構，例如︰

    "resources": [
       {
           "name": "resourceA",
       },
       {
           "name": "resourceB",
           "resources": [
               {
                   "name": "firstChildResourceB",
               },
               {   
                   "name": "secondChildResourceB",
               }
           ]
       },
       {
           "name": "resourceC",
       }
    ]


下列範例顯示**Microsoft.Web/serverfarms**資源和子系**副檔名**資源的**Microsoft.Web/sites**資源。 請注意網站會標示為 [伺服器陣列而定，因為在部署網站之前，必須存在於伺服器陣列。 請注意，太**副檔名**資源是網站的子系。

    "resources": [
      {
        "apiVersion": "2015-08-01",
        "name": "[parameters('hostingPlanName')]",
        "type": "Microsoft.Web/serverfarms",
        "location": "[resourceGroup().location]",
        "tags": {
          "displayName": "HostingPlan"
        },
        "sku": {
          "name": "[parameters('skuName')]",
          "capacity": "[parameters('skuCapacity')]"
        },
        "properties": {
          "name": "[parameters('hostingPlanName')]",
          "numberOfWorkers": 1
        }
      },
      {
        "apiVersion": "2015-08-01",
        "type": "Microsoft.Web/sites",
        "name": "[parameters('siteName')]",
        "location": "[resourceGroup().location]",
        "tags": {
          "environment": "test",
          "team": "Web"
        },
        "dependsOn": [
          "[concat('Microsoft.Web/serverFarms/', parameters('hostingPlanName'))]"
        ],
        "properties": {
          "name": "[parameters('siteName')]",
          "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
        },
        "resources": [
          {
            "apiVersion": "2015-08-01",
            "type": "extensions",
            "name": "MSDeploy",
            "dependsOn": [
              "[concat('Microsoft.Web/sites/', parameters('siteName'))]"
            ],
            "properties": {
              "packageUri": "https://auxmktplceprod.blob.core.windows.net/packages/StarterSite-modified.zip",
              "dbType": "None",
              "connectionString": "",
              "setParameters": {
                "Application Path": "[parameters('siteName')]"
              }
            }
          }
        ]
      }
    ]


## <a name="outputs"></a>輸出

在 [輸出] 區段中，您可以指定部署從所傳回的值。 例如，您可能會傳回存取部署的資源 URI。

下列範例顯示的輸出定義的結構︰

    "outputs": {
       "<outputName>" : {
         "type" : "<type-of-output-value>",
         "value": "<output-value-expression>"
       }
    }

| 項目名稱   | 所需 | 描述
| :------------: | :------: | :----------
| outputName     |   [是]    | 輸出值的名稱。 必須是有效的 JavaScript 識別碼。
| 類型           |   [是]    | 輸出值類型。 輸出值支援做為範本輸入參數的相同類型。
| 值          |   [是]    | 範本語言運算式評估並傳回輸出值。


下列範例會顯示在 [輸出] 區段中傳回的值。

    "outputs": {
       "siteUri" : {
         "type" : "string",
         "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
       }
    }

如需有關如何使用輸出的詳細資訊，請參閱[Azure 資源管理員範本中的共用狀態](best-practices-resource-manager-state.md)。

## <a name="next-steps"></a>後續步驟
- 若要檢視的許多不同類型的解決方案完成範本，請參閱[Azure 快速入門範本](https://azure.microsoft.com/documentation/templates/)。
- 如需您可以從範本中使用的函數的詳細資訊，請參閱[Azure 資源管理員範本函數](resource-group-template-functions.md)。
- 若要合併多個範本部署時，請參閱[使用連結的範本與 Azure 資源管理員](resource-group-linked-templates.md)。
- 您可能需要使用不同的資源群組中的資源。 使用儲存的帳戶] 或 [跨多個資源群組共用的虛擬網路時，這種情況下是常見。 如需詳細資訊，請參閱[預設函數](resource-group-template-functions.md#resourceid)。


[deployment2cmdlet]: https://msdn.microsoft.com/library/mt740620(v=azure.200).aspx
