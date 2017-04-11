<properties
   pageTitle="檢視作業管理套件 (OMS) 管理解決方案的 |Microsoft Azure"
   description="管理解決方案中作業管理套件 (OMS) 通常會包含一個或多個檢視，以視覺化方式呈現資料。  本文將說明如何匯出檢視設計工具建立的檢視，並將其加入中管理解決方案。 "
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
   ms.date="10/17/2016"
   ms.author="bwren" />

# <a name="views-in-operations-management-suite-oms-management-solutions-preview"></a>檢視的作業管理套件 (OMS) 管理解決方案 （預覽版本）

>[AZURE.NOTE]這是目前預覽中的 OMS 中建立管理解決方案的初步的文件。 任何如下所述的結構描述會有所變更。    

[管理解決方案中作業管理套件 (OMS)](operations-management-suite-solutions.md)通常會包含一個或多個檢視，以視覺化方式呈現資料。  本文將說明如何匯出[檢視設計工具](../log-analytics/log-analytics-view-designer.md)所建立的檢視，並將其加入中管理解決方案。  

>[AZURE.NOTE]本文中的範例使用參數和 [必要] 或 [通用管理解決方案並[建立管理解決方案作業管理套件 (OMS) 中](operations-management-suite-solutions-creating.md)所述的變數 


## <a name="prerequisites"></a>必要條件
本文假設您已經熟悉如何與[建立管理解決方案](operations-management-suite-solutions-creating.md)的和結構解決方案檔案。


## <a name="overview"></a>概觀

若要檢視中的管理解決方案，請您**資源**為其建立[的解決方案檔案](operations-management-suite-solutions-creating.md)中。  描述檢視詳細的設定 JSON 會通常複雜透過和非一般的解決方案作者能夠以手動方式建立。  最常見的方法是使用[檢視設計工具](../log-analytics/log-analytics-view-designer.md)建立檢視，匯出，然後再將其詳細的設定新增至方案。 

若要檢視新增至的方案的基本步驟如下所示。  下列各節中詳細說明這些步驟。

1. 將檢視匯出至檔案]。
2. 檢視資源在中建立的解決方案。
3. 新增檢視詳細資料。

## <a name="export-the-view-to-a-file"></a>將檢視匯出至檔案
請依照下列指示[記錄分析檢視設計工具](../log-analytics/log-analytics-view-designer.md)，將檢視匯出至檔案]。  匯出的檔案就會在 JSON 相同的[項目為解決方案檔案](operations-management-suite-solutions-creating.md#management-solution-files)格式。  

**資源**檔案的項目] 檢視會有一種**Microsoft.OperationalInsights/workspaces**代表 OMS 工作區的資源。  這個項目會有子元素與**檢視**表示的檢視，其中包含詳細的設定的類型。  您會複製此項目的詳細資料，然後將它複製到您的方案。


## <a name="create-the-view-resource-in-the-solution"></a>在方案中建立檢視資源
新增下列檢視資源至您的方案檔案的**資源**項目。  這是使用如下所述，您也必須將的變數。  請注意，**儀表板**] 和 [ **OverviewTile**屬性您將會覆寫對應從匯出的檢視檔案內容的版面配置區。
 
    {
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
        "type": "Microsoft.OperationalInsights/workspaces/views",
        "location": "[parameters('workspaceregionId')]",
        "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
        dependson": [
            ],
        "properties": {
            "Id": "[variables('ViewName')]",
            "Name": "[variables('ViewName')]",
            "DisplayName": "[variables('ViewName')]",
            "Description": "",
            "Author": "[variables('ViewAuthor')]",
            "Source": "Local",
            "Dashboard": ,
            "OverviewTile": 
        }
    }

新增下列變數至的方案檔案的[變數](operations-management-suite-solutions-creating.md#variables)項目及取代的值，這些解決方案。

    "LogAnalyticsApiVersion": "2015-11-01-preview",
    "ViewAuthor": "Your name."
    "ViewDescription": "Optional description of the view."
    "ViewName": "Provide a name for the view here."


請注意，您可以將整個檢視資源複製您匯出的檢視的檔案，但您想要進行下列變更，即可在您的方案中。  

- **檢視**變更為**Microsoft.OperationalInsights/workspaces**需要檢視資源**類型**。
- 檢視資源的 [**名稱**] 屬性必須變更為包含的工作區名稱。
- 在工作區上的相依性必須後的工作區資源定義方案中移除。
- **DisplayName**屬性必須會新增至檢視。  **識別碼**、**名稱**，並**顯示名稱**必須所有相符。
- 參數名稱必須變更以符合參數的所需的設定。
- 變數應該方案中所定義，並用於適當的內容。

## <a name="add-the-view-details"></a>新增檢視詳細資料
檢視資源匯出的檢視檔案中會包含兩個項目命名**儀表板**， **OverviewTile** **內容**項目中包含的檢視詳細的設定。  將下列兩個項目] 和 [其內容複製到檢視資源解決方案檔案中的 [**屬性**] 項目中。 

## <a name="example"></a>範例
例如，下列範例顯示檢視的簡單的解決方案檔案。  省略符號 （...） 會顯示空間基於**儀表板**， **OverviewTile**內容。


    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspaceName": {
                "type": "string"
            },
            "accountName": {
                "type": "string"
            },
            "workspaceRegionId": {
                "type": "string"
            },
            "regionId": {
                "type": "string"
            },
            "pricingTier": {
                "type": "string"
            }
        },
        "variables": {
            "SolutionVersion": "1.1",
            "SolutionPublisher": "Contoso",
            "SolutionName": "Contoso Solution",
            "LogAnalyticsApiVersion": "2015-11-01-preview",
            "ViewAuthor":  "user@contoso.com",
            "ViewDescription":  "This is a sample view.",
            "ViewName":  "Contoso View"
        },
        "resources": [
            {
                "name": "[concat(variables('SolutionName'), '(' ,parameters('workspacename'), ')')]",
                "location": "[parameters('workspaceRegionId')]",
                "tags": { },
                "type": "Microsoft.OperationsManagement/solutions",
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspacename'), '/views/', variables('ViewName'))]"
                ],
                "properties": {
                    "workspaceResourceId": "[concat(resourceGroup().id, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspacename'))]",
                    "referencedResources": [
                    ],
                    "containedResources": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
                    ]
                },
                "plan": {
                    "name": "[concat(variables('SolutionName'), '(' ,parameters('workspaceName'), ')')]",
                    "Version": "[variables('SolutionVersion')]",
                    "product": "ContosoSolution",
                    "publisher": "[variables('SolutionPublisher')]",
                    "promotionCode": ""
                }
            },
            {
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
                "type": "Microsoft.OperationalInsights/workspaces/views",
                "location": "[parameters('workspaceregionId')]",
                "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
                "dependson": [
                ],
                "properties": {
                    "Id": "[variables('ViewName')]",
                    "Name": "[variables('ViewName')]",
                    "DisplayName": "[variables('ViewName')]",
                    "Description": "[variables('ViewDescription')]",
                    "Author": "[variables('ViewAuthor')]",
                    "Source": "Local",
                    "Dashboard": ...,
                    "OverviewTile": ...
                }
            }
        ]
    }




## <a name="next-steps"></a>後續步驟

- 瞭解建立[管理解決方案](operations-management-suite-solutions-creating.md)的完整詳細資料。
- 包含[自動化 runbooks，管理方案中](operations-management-suite-solutions-resources-automation.md)。