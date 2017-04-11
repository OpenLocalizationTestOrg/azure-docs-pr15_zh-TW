<properties
   pageTitle="建立管理解決方案中作業管理套件 (OMS) |Microsoft Azure"
   description="管理解決方案擴充功能的作業管理套件 (OMS)，提供客戶可以新增至其 OMS 工作區的封裝的管理案例。  本文提供您如何建立您自己的環境中使用的管理解決方案的詳細資訊，或提供給您的客戶。"
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/27/2016"
   ms.author="bwren" />

# <a name="creating-management-solutions-in-operations-management-suite-oms-preview"></a>建立作業管理套件 (OMS) （預覽版本） 中的管理解決方案

>[AZURE.NOTE]這是目前預覽中的 OMS 中建立管理解決方案的初步的文件。 任何如下所述的結構描述會有所變更。  

管理解決方案擴充功能的作業管理套件 (OMS)，提供客戶可以新增至其 OMS 工作區的封裝的管理案例。  本文提供詳細資料，建立您自己管理解決方案，您可以在自己的環境中使用，或提供客戶社群。

## <a name="planning-your-management-solution"></a>規劃您的管理解決方案
管理解決方案 OMS 中的包含多個支援特定管理案例的資源。  在規劃您的方案，您應該著重於您想要達到此案例，支援所有必要的資源。  每個解決方案應該會自動包含，並定義需要，每個資源，即使將一或多個資源也由其他方案所定義。  安裝管理解決方案時，每個資源會建立除非已經存在，而且您可以定義當移除解決方案時，資源會發生什麼情況。  

例如，管理方案可能會包含收集資料至使用[排程](../automation/automation-schedules.md)和提供各種視覺效果的收集的資料[檢視](../log-analytics/log-analytics-view-designer.md)記錄檔分析存放庫[Azure 自動化 runbook](../automation/automation-intro.md) 。  另一種解決方案可能會使用相同的排程。  管理解決方案的作者，為您想要定義所有三個資源，但指定的 runbook 和檢視時，應自動移除方案，會移除。    您也想要定義排程，但指定，它應該仍保留在原位如果方案所移除以防仍在使用其他方案。

## <a name="management-solution-files"></a>管理方案檔案
管理解決方案實作為[資源管理範本](../resource-manager-template-walkthrough.md)。  學習如何撰寫管理解決方案的主要工作學習如何[撰寫範本](../resource-group-authoring-templates.md)。  本文提供解決方案，以及如何定義常見的解決方案資源所使用的範本的唯一的詳細資訊。

管理方案檔案的基本結構是[資源管理員範本](resource-group-authoring-templates.md#template-format)是以下列方式相同。  每個下列各節說明的最上層項目和解決方案中及其內容。  

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>參數

[參數](../resource-group-authoring-templates.md#parameters)是安裝管理解決方案時，您需要從使用者的值。  有標準的參數，將會有所有解決方案，而且您可以視需要新增其他參數，特定解決方案。  使用者如何提供他們安裝您的方案的參數值會取決於特定的參數和方案安裝。


當使用者安裝您透過[Azure Marketplace](operations-management-suite-solutions.md#finding-and-installing-solutions)或[Azure 快速入門範本](operations-management-suite-solutions.md#finding-and-installing-solutions)的項目，系統會提示選取[OMS 工作區與自動化帳戶](operations-management-suite-solutions-creating.md#oms-workspace-and-automation-account)的管理解決方案。  這些用來填入的每個標準的參數值。  使用者不會提示您以直接提供的標準的參數的值，但它們系統提示您提供的任何其他參數值。

當使用者安裝您解決方案的[另一種方法](operations-management-suite-solutions.md#finding-and-installing-solutions)時，他們必須提供一個值，所有的標準參數和所有其他參數。

如下所示的範例參數。

    "Daily Start Time": {
        "type": "string",
        "metadata": {
            "description": "Enter time for starting VMs by resource group.",
            "control": "datetime",
            "category": "Schedule"
        }

下表說明參數的屬性。

| 屬性 | 描述 |
|:--|:--|
| 類型        | 參數的資料類型。 顯示使用者輸入的控制項的資料類型而定。<br><br>bool-下拉式方塊<br>字串-文字方塊<br>int-文字方塊<br>securestring-密碼] 欄位<br> |
| 類別    | 選擇性參數的類別。  在同一個類別中的參數被組成群組。 |
| 控制項     | 字串參數的其他功能。<br><br>顯示日期時間-Datetime 控制項。<br>guid-Guid 值會自動產生，並不會顯示參數。 |
| 描述 | 選用的參數的詳細說明。  顯示在 [參數] 旁的資訊球形文字說明。 |


### <a name="standard-parameters"></a>標準的參數
下表列出所有管理解決方案的標準參數。  這些值，而不是從 Azure Marketplace 或快速入門範本︰ 安裝您的方案時提示使用者填入。  使用者必須為這些提供值，如果方案已安裝與另一種方法。

>[AZURE.NOTE]Azure Marketplace 及快速入門範本中的使用者介面預期的參數名稱會在資料表中。  如果您使用不同的參數名稱，然後將會提示使用者，他們不會自動填入。


| 參數 | 類型 | 描述 |
|:--|:--|:--|
| accountName       | 字串 |  Azure 自動化帳戶名稱。 |
| pricingTier       | 字串 | 價格層記錄分析工作區和 Azure 自動化帳戶。 |
| regionId          | 字串 | Azure 自動化帳戶的區域。 |
| solutionName      | 字串 | 方案名稱。 |
| workspaceName     | 字串 | 記錄檔分析工作區名稱。 |
| workspaceRegionId | 字串 | 記錄檔分析工作區的區域。 |





### <a name="sample"></a>範例
以下是範例參數項的解決方案。  這會包含所有的標準參數和兩個額外參數在相同的類別。

    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "A valid Log Analytics workspace name"
            }
        },
        "accountName": {
            "type": "string",
            "metadata": {
                "description": "A valid Azure Automation account name"
            }
        },
        "workspaceRegionId": {
            "type": "string",
            "metadata": {
                "description": "Region of the Log Analytics workspace"
            }
        },
        "regionId": {
            "type": "string",
            "metadata": {
                "description": "Region of the Azure Automation account"
            }
        },
        "pricingTier": {
            "type": "string",
            "metadata": {
                "description": "Pricing tier of both Log Analytics workspace and Azure Automation account"
            }
        },
        "jobIdGuid": {
        "type": "string",
            "metadata": {
                "description": "GUID for a runbook job",
                "control": "guid",
                "category": "Schedule"
            }
        },
        "startTime": {
            "type": "string",
            "metadata": {
                "description": "Time for starting the runbook.",
                "control": "datetime",
                "category": "Schedule"
            }
        }


您參照的語法**參數 （' 參數名稱 」）**的解決方案的其他項目中的參數值。  例如，若要存取工作區名稱，您會使用**parameters('workspaceName')** 

## <a name="variables"></a>變數

**變數**項目管理解決方案的其餘部分中包含您要使用的值。  這些值不被公開安裝方案的使用者。  這些是提供他們可在其中管理整個解決方案可能會使用多個時間值的單一位置中的作者。 您應該將任何值特定至您的方案中相對於硬式編碼這些**資源**項目中的變數。  這會讓更容易閱讀程式碼，並可讓您輕鬆地變更這些更新版本中的值。

下列是方案中使用的一般參數的**變數**項目的範例。

    "variables": { 
        "SolutionVersion": "1.1", 
        "SolutionPublisher": "Contoso", 
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

您參照的語法**變數 （「 變數名稱 」）**解決方案變數值。  例如，若要存取 SolutionName 變數，您會使用**variables('solutionName')** 


## <a name="resources"></a>資源

**資源**項目定義的不同的資源，包括管理方案中。  這將成為範本的最大值與最複雜的部分。  資源是以下列結構定義。  

    "resources": [
        {
            "name": "<name-of-the-resource>",           
            "apiVersion": "<api-version-of-resource>",
            "type": "<resource-provider-namespace/resource-type-name>",     
            "location": "<location-of-resource>",
            "tags": "<name-value-pairs-for-resource-tagging>",
            "comments": "<your-reference-notes>",
            "dependsOn": [
                "<array-of-related-resource-names>"
            ],
            "properties": "<unique-settings-for-the-resource>",
            "resources": [
                "<array-of-child-resources>"
            ]
        }
    ]

### <a name="dependencies"></a>相依性
**DependsOn**項目指定另一個資源[相依性](../resource-group-define-dependencies.md)。  安裝解決方案時，直到已建立所有的相依性，將不會建立資源。  例如，您的方案可能[開始 runbook](operations-management-suite-solutions-resources-automation.md#runbooks)安裝使用[工作資源](operations-management-suite-solutions-resources-automation.md#automation-jobs)時。  工作資源是 runbook 資源，請確定在建立工作之前，會建立 runbook 而定。

### <a name="oms-workspace-and-automation-account"></a>OMS 工作區與自動化帳戶
管理方案需要[OMS 工作區](../log-analytics/log-analytics-manage-access.md)以包含檢視及[自動化帳戶](../automation/automation-security-overview.md#automation-account-overview)包含 runbooks 和相關的資源。  建立方案中的資源，並不應方案本身中定義之前，則這些必須要有。  使用者將會在[指定的工作區和帳戶](operations-management-suite-solutions.md#oms-workspace-and-automation-account)時在部署您的方案，但作者，您必須考量下列點。


## <a name="solution-resource"></a>解決辦法資源
每個方案需要資源中的項目定義方案本身的**資源**項目。  這會有一種**Microsoft.OperationsManagement/solutions**。  下列是解決方案資源的範例。  以下各節說明其不同的項目。

    "name": "[concat(variables('SolutionName'), '[ ' ,parameters('workspacename'), ' ]')]",
    "location": "[parameters('workspaceRegionId')]",
    "tags": { },
    "type": "Microsoft.OperationsManagement/solutions",
    "apiVersion": "[variables('LogAnalyticsApiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('RunbookName'))]",
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/schedules/', variables('ScheduleName'))]",
        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
    ]
    "properties": {
        "workspaceResourceId": "[concat(resourceGroup().id, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
        "referencedResources": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/schedules/', variables('ScheduleName'))]"
        ],
        "containedResources": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('RunbookName'))]",
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
        ]
    },
    "plan": {
        "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
        "Version": "[variables('SolutionVersion')]",
        "product": "AzureSQLAnalyticSolution",
        "publisher": "[variables('SolutionPublisher')]",
        "promotionCode": ""
    }

### <a name="solution-name"></a>方案名稱
方案名稱必須是唯一的 Azure 訂閱。 以下是要使用的建議的值。  若要確保名稱是唯一的這就會使用變數，稱為基底名稱] 和 [ **workspaceName**參數**SolutionName** 。

    [concat(variables('SolutionName'), ' [' ,parameters('workspaceName'), ']')]

這會解析的名稱，如下所示。

    My Solution Name [MyWorkspace]
 

### <a name="dependencies"></a>相依性
解決方案資源必須對於清單有[相依性](../resource-group-define-dependencies.md)方案中的每個其他資源因為他們需要存在於才能建立的解決方案。  藉由新增**dependsOn**項目中的每個資源的項目執行此動作。


### <a name="properties"></a>屬性
解決方案資源具有下表中的內容。  這包含參照和定義解決方案安裝後如何管理資源方案所包含的資源。  在方案中的每個資源應該會列在**referencedResources**或**containedResources**屬性。

| 屬性 | 描述 |
|:--|:--|
| workspaceResourceId | 在表單中 OMS 工作區的識別碼*<Resource Group ID>/providers/Microsoft.OperationalInsights/workspaces/\<工作區名稱\>*。 |
| referencedResources   | 移除方案時應移除的方案中的資源的清單。 |
| containedResources   | 移除方案時應移除的方案中的資源的清單。 |

上述範例中為 runbook、 排程和檢視的方案。  排程和 runbook 是*參照*中的**內容**項目，不會移除移除解決方案時。  檢視是*包含*移除解決方案時，就會移除。


### <a name="plan"></a>計劃
解決方案資源的**計劃**實體下表中具有的屬性。 

| 屬性 | 描述 |
|:--|:--|
| 名稱          | 方案名稱。 |
| 版本       | 由作者解決方案的版本。 |
| 產品       | 唯一識別解決方案的字串。 |
| publisher     | 解決方案的發行者。 |


## <a name="other-resources"></a>其他資源
您可以取得詳細資料] 與 [通用下列文章中的管理解決方案的資源的範例。

- [檢視及儀表板](operations-management-suite-solutions-resources-views.md)
- [自動化資源](operations-management-suite-solutions-resources-automation.md)

## <a name="testing-a-management-solution"></a>測試管理解決方案
之前在部署您管理解決方案，建議您先使用[測試 AzureRmResourceGroupDeployment](../resource-group-template-deploy.md#deploy-with-powershell)進行測試。  這會驗證您的方案檔案及您之前，先將其部署識別任何問題的說明。



## <a name="next-steps"></a>後續步驟

- 學習[撰寫 Azure 資源管理員範本](../resource-group-authoring-templates.md)的詳細資料。
- 搜尋[Azure 快速入門範本](https://azure.microsoft.com/documentation/templates)的不同的資源管理員範本的範例。
- 檢視 [[新增檢視，以管理解決方案](operations-management-suite-solutions-resources-views.md)的詳細資料。
- 檢視[新增自動化資源管理解決方案](operations-management-suite-solutions-resources-automation.md)的詳細資料。
 