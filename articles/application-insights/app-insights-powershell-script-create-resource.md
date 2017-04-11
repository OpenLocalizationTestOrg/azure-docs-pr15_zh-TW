<properties 
    pageTitle="若要建立的應用程式的深入見解資源的 PowerShell 指令碼" 
    description="自動建立應用程式的深入見解資源。" 
    services="application-insights" 
    documentationCenter="windows"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/19/2016" 
    ms.author="awills"/>

#  <a name="powershell-script-to-create-an-application-insights-resource"></a>若要建立的應用程式的深入見解資源的 PowerShell 指令碼

*應用程式的深入見解位於預覽。*

當您想要監視新的應用程式-或新版本的應用程式-使用[Visual Studio 應用程式深入資訊](https://azure.microsoft.com/services/application-insights/)時，您會設定 Microsoft Azure 中新的資源。 這項資源是分析及顯示您的應用程式的遙測資料的位置。 

您可以使用 PowerShell 來自動化建立新的資源。

例如，如果您開發的行動裝置應用程式，很可能，隨時會有數種版本發佈您的應用程式中使用您的客戶。 您不想要從不同的版本混合取得遙測結果。 因此，您會收到建立程序建立的每一個建立新的資源。

## <a name="script-to-create-an-application-insights-resource"></a>若要建立的應用程式的深入見解資源的指令碼

請參閱相關的 cmdlet 規格︰

* [新 AzureRmResource](https://msdn.microsoft.com/library/mt652510.aspx)
* [新 AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt678995.aspx)


*PowerShell 指令碼*  

```PowerShell


###########################################
# Set Values
###########################################

# If running manually, uncomment before the first 
# execution to login to the Azure Portal:

# Add-AzureRmAccount

# Set the name of the Application Insights Resource

$appInsightsName = "TestApp"

# Set the application name used for the value of the Tag "AppInsightsApp" 
# - http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-tags/
$applicationTagName = "MyApp"

# Set the name of the Resource Group to use.  
# Default is the application name.
$resourceGroupName = "MyAppResourceGroup"

###################################################
# Create the Resource and Output the name and iKey
###################################################

#Select the azure subscription
Select-AzureSubscription -SubscriptionName "MySubscription"

# Create the App Insights Resource

$resource = New-AzureRmResource `
  -ResourceName $appInsightsName `
  -ResourceGroupName $resourceGroupName `
  -Tag @{ Name = "AppInsightsApp"; Value = $applicationTagName} `
  -ResourceType "Microsoft.Insights/Components" `
  -Location "Central US" `
  -PropertyObject @{"Type"="ASP.NET"} `
  -Force

# Give owner access to the team

New-AzureRmRoleAssignment `
  -SignInName "myteam@fabrikam.com" `
  -RoleDefinitionName Owner `
  -Scope $resource.ResourceId 


#Display iKey
Write-Host "App Insights Name = " $resource.Name
Write-Host "IKey = " $resource.Properties.InstrumentationKey

```

## <a name="what-to-do-with-the-ikey"></a>如何處理 iKey

每個資源被識別其儀器索引鍵 (iKey)。 IKey 是資源建立指令碼的輸出。 您建立的指令碼應提供應用程式的深入見解 sdk 的變更 iKey 內嵌在您的應用程式中。

有兩種方法讓 iKey 使用 sdk 的變更︰
  
* 在 [ [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md): 
 * `<instrumentationkey>`*ikey*`</instrumentationkey>`
* 或[初始化程式碼](app-insights-api-custom-events-metrics.md)︰ 
 * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*iKey*`";`



## <a name="see-also"></a>另請參閱

* [從範本建立應用程式的深入見解和 web 測試資源](app-insights-powershell.md)
* [監視使用 PowerShell 的 Azure 診斷程式的設定](app-insights-powershell-azure-diagnostics.md) 
* [使用 PowerShell 設定通知](app-insights-powershell-alerts.md)

 