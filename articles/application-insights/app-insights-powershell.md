<properties 
    pageTitle="建立使用 PowerShell 的應用程式的深入見解資源" 
    description="以程式設計方式建立應用程式的深入見解資源做為您建立的一部分。" 
    services="application-insights" 
    documentationCenter=""
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="03/02/2016" 
    ms.author="awills"/>
 
# <a name="create-application-insights-resources-using-powershell"></a>建立使用 PowerShell 的應用程式的深入見解資源

本文將示範如何 Azure 中自動建立[應用程式的深入見解](app-insights-overview.md)資源。 您可能，例如這麼做為建立程序的一部分。 基本的應用程式的深入見解資源，以及您可以建立[可用性 web 測試](app-insights-monitor-web-app-availability.md)，[設定提醒](app-insights-alerts.md)，，並建立其他 Azure 的資源。

若要建立這些資源，關鍵是[Azure 資源管理員](../powershell-azure-resource-manager.md)JSON 範本。 簡而言之，程序是︰ 下載 JSON 定義的現有的資源。參數化名稱; 例如特定值然後再執行範本，每當您想要建立新的資源。 您可以在一起封裝幾項資源，建立所有其中一種移-，例如使用可用性測試、 通知及連續匯出的儲存空間將應用程式監視器。 有幾項參數化，我們會說明在以下一些微妙。

## <a name="one-time-setup"></a>一次設定

如果您並未使用 PowerShell 的 Azure 之前訂閱︰

在您要執行指令碼的電腦上安裝 Azure Powershell 模組︰

1. 安裝[Microsoft Web 平台安裝程式 (v5 或更新版本)](http://www.microsoft.com/web/downloads/platform.aspx)。
2. 用來安裝 Microsoft Azure Powershell。

## <a name="copy-the-json-for-existing-resources"></a>複製現有的資源 JSON

1. 設定[應用程式的深入見解](app-insights-overview.md)專案類似於您想要自動產生的項目。 新增 web 測試和通知，如果您想要。
2. 建立新的.json 檔案-來看`template1.json`在此範例中。 將這個內容複製到其︰


    ```JSON

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "appName": { "type": "string" },
            "webTestName": { "type": "string" },
            "url": { "type": "string" },
            "text": { "type" : "string" }
          },
          "variables": {
            "testName": "[concat(parameters('webTestName'), 
               '-', toLower(parameters('appName')))]"
            "alertRuleName": "[concat(parameters('webTestName'), 
               '-', toLower(parameters('appName')), 
               '-', subscription().subscriptionId)]"
          },
          "resources": [
            {
              // component JSON file contents
            },
            {
              //web test JSON file contents
            },
            {
              //alert rule JSON file contents
            }
 
            // Any other resources go here
          ]
        }
    
    ```

    此範本就設定一個可用性測試，除了主要的資源。


2. 開啟[Azure 資源管理員](https://resources.azure.com/)]。 向下瀏覽`subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components`，您的應用程式資源。 

    ![Azure 資源檔案總管] 中的導覽](./media/app-insights-powershell/01.png)

    顯示應用程式的基本應用程式的深入見解資源為*元件*。 有另一個資源相關聯的通知規則和可用性 web 測試。

3. 複製到適當的位置中的元件的 JSON `template1.json`。
6. 刪除這些屬性︰
  * `id`
  * `InstrumentationKey`
  * `CreationDate`
4. 開啟 [webtests 及 alertrules] 區段，然後將個別項目的 JSON 複製到您的範本。 (不複製 webtests 或 alertrules 節點︰ 移到其下方的項目。)

    每個 web 測試都有相關聯的警示規則，因此您必須複製其中。

    Web 測試應之前通知的規則。

5. 若要滿足結構描述，請在每個資源插入這一行︰

    `"apiVersion": "2014-04-01",`

    (結構描述也抱怨相關資源類型名稱的大小寫`Microsoft.Insights/*`，但*不*變更這些。)


## <a name="parameterize-the-template"></a>參數化範本

現在，您有特定的名稱取代參數。 若要[參數化範本](../resource-group-authoring-templates.md)，您可以撰寫使用[設定協助函數的](../resource-group-template-functions.md)運算式。 

您無法參數化只將字串的一部分，因此請使用`concat()`建立字串。

以下是您需要進行的替代的範例。 有的每個替代的多個項目。 您可能需要其他人在範本中。 這些範例使用頂端的範本的參數和我們所定義的變數。

尋找 | 取代
---|---
`"hidden-link:/subscriptions/.../components/MyAppName"`| `"[concat('hidden-link:',`<br/>` resourceId('microsoft.insights/components',` <br/> ` parameters('appName')))]"`
`"/subscriptions/.../alertrules/myAlertName-myAppName-subsId",` | `"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",`
`"/subscriptions/.../webtests/myTestName-myAppName",` | `"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",`
`"myWebTest-myAppName"` | `"[variables(testName)]"'`
`"myTestName-myAppName-subsId"` | `"[variables('alertRuleName')]"`
`"myAppName"` | `"[parameters('appName')]"`
`"myappname"`（小寫） | `"[toLower(parameters('appName'))]"`
`"<WebTest Name=\"myWebTest\" ...`<br/>` Url=\"http://fabrikam.com/home\" ...>"`|`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]" `



## <a name="set-dependencies-between-the-resources"></a>設定資源間的相依性

Azure 應設定嚴格順序的資源。 若要確保一個安裝完成下一步] 開始之前，請先新增相依性的線條︰

* 在網頁版中測試資源︰

    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`

* 在 [提醒的資源︰

    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`

## <a name="create-application-insights-resources"></a>建立應用程式的深入見解的資源

1. 在 PowerShell 中登入 Azure

    `Login-AzureRmAccount`

2. 執行命令，就像這樣︰

    ```PS

        New-AzureRmResourceGroupDeployment -ResourceGroupName Fabrikam `
               -templateFile .\template1.json `
               -appName myNewApp `
               -webTestName aWebTest `
               -Url http://myapp.com `
               -text "Welcome!"
               -siteName "MyAzureSite"

    ``` 

    * -ResourceGroupName 是群組，您要建立新的資源。
    * 自訂參數之前，就必須-templateFile。
    * -應用程式名稱來建立資源的名稱。
    * -webTestName web 測試，若要建立的名稱。
    * Url 的 web 應用程式的 url。
    * -會出現在您的網頁上的文字的字串。
    * -的站台名稱-如果 Azure 網站使用


## <a name="define-metric-alerts"></a>定義公制的通知

沒有[設定通知的 PowerShell 方法](app-insights-alerts.md#set-alerts-by-using-powershell)。


## <a name="an-example"></a>範例

以下是完成元件 web 測試，測試通知的網站範本建立︰

``` JSON

{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "webTestName": { "type": "string" },
    "appName": { "type": "string" },
    "URL": { "type": "string" },
    "text": { "type" : "string" }
  },
  "variables": {
    "alertRuleName": "[concat(parameters('webTestName'), '-', toLower(parameters('appName')), '-', subscription().subscriptionId)]",
    "testName": "[concat(parameters('webTestName'), '-', toLower(parameters('appName')))]"
  },
  "resources": [
    {
      //"id": "[resourceId('Microsoft.Insights/components', parameters('appName'))]",
      "apiVersion": "2014-04-01",
      "kind": "web",
      "location": "Central US",
      "name": "[parameters('appName')]",
      "properties": {
        "TenantId": "9122605a-471fc50f8438",
        "Application_Type": "web",
        "Flow_Type": "Brownfield",
        "Request_Source": "VSIX3.3.1.0",
        "Name": "[parameters('appName')]",
        //"CreationDate": "2015-10-14T15:55:10.0917441+00:00",
        "PackageId": null,
        "ApplicationId": "[parameters('appName')]"
      },
      "tags": { },
      "type": "microsoft.insights/components"
    },
    {
      //"id": "[resourceId('Microsoft.Insights/webtests', variables('testName'))]",
      "name": "[variables('testName')]",
      "apiVersion": "2014-04-01",
      "type": "microsoft.insights/webtests",
      "location": "Central US",
      "tags": {
        "[concat('hidden-link:', resourceId('microsoft.insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "provisioningState": "Succeeded",
        "Name": "[parameters('webTestName')]",
        "Description": "",
        "Enabled": true,
        "Frequency": 900,
        "Timeout": 120,
        "Kind": "ping",
        "RetryEnabled": true,
        "Locations": [
          {
            "Id": "us-va-ash-azr"
          },
          {
            "Id": "emea-nl-ams-azr"
          },
          {
            "Id": "emea-gb-db3-azr"
          }
        ],
        "Configuration": {
          "WebTest": "[concat(
             '<WebTest   Name=\"', 
                parameters('webTestName'), 
              '\"  Id=\"32cfc791-aaad-4b50-9c8d-993c21beb218\"   Enabled=\"True\"         CssProjectStructure=\"\"    CssIteration=\"\"  Timeout=\"120\"  WorkItemIds=\"\"         xmlns=\"http://microsoft.com/schemas/VisualStudio/TeamTest/2010\"         Description=\"\"  CredentialUserName=\"\"  CredentialPassword=\"\"         PreAuthenticate=\"True\"  Proxy=\"default\"  StopOnError=\"False\"         RecordedResultFile=\"\"  ResultsLocale=\"\">  <Items>  <Request Method=\"GET\"         Guid=\"a6f2c90b-61bf-b28hh06gg969\"  Version=\"1.1\"  Url=\"', 
              parameters('Url'), 
              '\" ThinkTime=\"0\"  Timeout=\"300\" ParseDependentRequests=\"True\"         FollowRedirects=\"True\" RecordResult=\"True\" Cache=\"False\"         ResponseTimeGoal=\"0\"  Encoding=\"utf-8\"  ExpectedHttpStatusCode=\"200\"         ExpectedResponseUrl=\"\" ReportingName=\"\" IgnoreHttpStatusCode=\"False\" />        </Items>  <ValidationRules> <ValidationRule  Classname=\"Microsoft.VisualStudio.TestTools.WebTesting.Rules.ValidationRuleFindText, Microsoft.VisualStudio.QualityTools.WebTestFramework, Version=10.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a\" DisplayName=\"Find Text\"         Description=\"Verifies the existence of the specified text in the response.\"         Level=\"High\"  ExectuionOrder=\"BeforeDependents\">  <RuleParameters>        <RuleParameter Name=\"FindText\" Value=\"', 
              parameters('text'), 
              '\" />  <RuleParameter Name=\"IgnoreCase\" Value=\"False\" />  <RuleParameter Name=\"UseRegularExpression\" Value=\"False\" />  <RuleParameter Name=\"PassIfTextFound\" Value=\"True\" />  </RuleParameters> </ValidationRule>  </ValidationRules>  </WebTest>')]"
        },
        "SyntheticMonitorId": "[variables('testName')]"
      }
    },
    {
      //"id": "[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",
      "name": "[variables('alertRuleName')]",
      "apiVersion": "2014-04-01",
      "type": "microsoft.insights/alertrules",
      "location": "East US",
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]",
        "[resourceId('Microsoft.Insights/webtests', variables('testName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource",
        "[concat('hidden-link:', resourceId('Microsoft.Insights/webtests', variables('testName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('alertRuleName')]",
        "description": "",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.LocationThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.LocationThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/webtests', variables('testName'))]",
            "metricName": "GSMT_AvRaW"
          },
          "windowSize": "PT15M",
          "failedLocationCount": 2
        },
        "action": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
          "sendToServiceOwners": true,
          "customEmails": [ ]
        },
        "provisioningState": "Succeeded",
        "actions": [ ]
      }

    }
  ]
}

```

## <a name="see-also"></a>另請參閱

自動化的文章︰

* [建立應用程式的深入見解資源](app-insights-powershell-script-create-resource.md)-快速的方法，而不使用的範本。
* [設定提醒](app-insights-powershell-alerts.md)
* [建立 web 測試](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [傳送 Azure 診斷應用程式獲得深入見解](app-insights-powershell-azure-diagnostics.md)
* [建立版本註釋](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)
