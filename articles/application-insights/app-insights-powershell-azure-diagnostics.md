<properties
    pageTitle="使用 PowerShell 來設定 Azure 中的應用程式獲得深入見解 |Microsoft Azure"
    description="自動設定 Azure 診斷管道應用程式獲得深入見解。"
    services="application-insights"
    documentationCenter=".net"
    authors="sbtron"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="11/17/2015"
    ms.author="awills"/>

# <a name="using-powershell-to-set-up-application-insights-for-an-azure-web-app"></a>使用 PowerShell 來設定 Azure web 應用程式的應用程式的深入見解

[Microsoft Azure](https://azure.com)可[設定為傳送 Azure 診斷](app-insights-azure-diagnostics.md) [Visual Studio 應用程式](app-insights-overview.md)獲得深入見解。 診斷與 Azure 雲端服務與 Azure Vm。 他們互補您從應用程式使用應用程式的深入見解 SDK 傳送遙測。 自動化 Azure 中建立新的資源的程序的一部分，您可以設定診斷使用 PowerShell。

## <a name="azure-template"></a>Azure 範本

如果是 Azure 中的 web 應用程式，並建立您使用 Azure 資源管理員範本的資源，您可以設定應用程式的深入見解，藉此資源的節點︰

    {
      resources: [
        /* Create Application Insights resource */
        {
          "apiVersion": "2015-05-01",
          "type": "microsoft.insights/components",
          "name": "nameOfAIAppResource",
          "location": "centralus",
          "kind": "web",
          "properties": { "ApplicationId": "nameOfAIAppResource" },
          "dependsOn": [
            "[concat('Microsoft.Web/sites/', myWebAppName)]"
          ]
        }
       ]
     } 

* `nameOfAIAppResource`-應用程式的深入見解資源的名稱
* `myWebAppName`-的 web 應用程式識別碼


## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>啟用診斷副檔名為部署雲端服務的一部分

`New-AzureDeployment` Cmdlet 具有參數`ExtensionConfiguration`，其中採用陣列的診斷程式設定。 這些可以建立使用`New-AzureServiceDiagnosticsExtensionConfig`指令程式。 例如︰

```ps

    $service_package = "CloudService.cspkg"
    $service_config = "ServiceConfiguration.Cloud.cscfg"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $primary_storagekey = (Get-AzureStorageKey `
     -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
       -StorageAccountName $diagnostics_storagename `
       -StorageAccountKey $primary_storagekey

    $webrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WebRole" -Storage_context $storageContext `
      -DiagnosticsConfigurationPath $webrole_diagconfigpath
    $workerrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WorkerRole" `
      -StorageContext $storage_context `
      -DiagnosticsConfigurationPath $workerrole_diagconfigpath

    New-AzureDeployment `
      -ServiceName $service_name `
      -Slot Production `
      -Package $service_package `
      -Configuration $service_config `
      -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)

``` 

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>啟用診斷延伸現有的雲端服務

在 [現有的服務，使用`Set-AzureServiceDiagnosticsExtension`。

```ps
 
    $service_name = "MyService"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"
    $primary_storagekey = (Get-AzureStorageKey `
         -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
        -StorageAccountName $diagnostics_storagename `
        -StorageAccountKey $primary_storagekey

    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $webrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WebRole" 
    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $workerrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WorkerRole"
```

## <a name="get-current-diagnostics-extension-configuration"></a>取得目前診斷延伸設定

```ps

    Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```


## <a name="remove-diagnostics-extension"></a>移除診斷副檔名

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

如果您使用的診斷副檔名為`Set-AzureServiceDiagnosticsExtension`或`New-AzureServiceDiagnosticsExtensionConfig`不含角色參數，您就可以移除副檔名使用`Remove-AzureServiceDiagnosticsExtension`沒有角色參數。 如果啟用延伸時使用角色參數然後它必須也使用移除副檔名。

若要移除的診斷副檔名從每個個別的角色︰

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```


## <a name="see-also"></a>另請參閱

* [監控 Azure 雲端服務應用程式搭配使用的應用程式的深入見解](app-insights-cloudservices.md)
* [傳送 Azure 診斷應用程式獲得深入見解](app-insights-azure-diagnostics.md)
* [自動化設定通知](app-insights-powershell-alerts.md)

