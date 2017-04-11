<properties
    pageTitle="啟用診斷使用 PowerShell 的 Azure 雲端服務中 |Microsoft Azure"
    description="瞭解如何啟用診斷使用 PowerShell 雲端服務"
    services="cloud-services"
    documentationCenter=".net"
    authors="Thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="adegeo"/>


# <a name="enable-diagnostics-in-azure-cloud-services-using-powershell"></a>啟用診斷中使用 PowerShell 的 Azure 雲端服務

您可以收集診斷資料應用程式的記錄，例如效能計數器等雲端服務使用 Azure 診斷副檔名。 本文將說明如何啟用雲端服務使用 PowerShell 的 Azure 診斷副檔名。  本文所需的必要條件，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md) 。

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>啟用診斷副檔名為部署雲端服務的一部分

此方法的絕佳位置啟用診斷擴充功能，屬於部署雲端服務的案例的連續整合類型。 建立新的雲端服務部署時可以[新增 AzureDeployment](https://msdn.microsoft.com/library/azure/mt589089.aspx) cmdlet *ExtensionConfiguration*參數傳遞啟用診斷副檔名。 *ExtensionConfiguration*參數接受陣列，可以使用 [[新增 AzureServiceDiagnosticsExtensionConfig](https://msdn.microsoft.com/library/azure/mt589168.aspx)指令程式來建立的診斷程式設定。

下列範例會示範如何啟用診斷 WebRole 與 WorkerRole 雲端服務各有不同的診斷程式設定。

    $service_name = "MyService"
    $service_package = "CloudService.cspkg"
    $service_config = "ServiceConfiguration.Cloud.cscfg"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
    $workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

    New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)

如果診斷設定檔案儲存體帳戶名稱與指定 StorageAccount 項目，新增 AzureServiceDiagnosticsExtensionConfig 指令程式會自動使用該儲存帳戶。 此工作，儲存帳戶必須在同一份訂閱為部署雲端服務。

從 Azure SDK 2.6 開始 MSBuild 所產生的副檔名設定檔案發佈目標輸出中會包含根據 [服務設定檔 (.cscfg) 中所指定的診斷設定字串儲存體帳戶名稱。 以下的指令碼會顯示如何剖析發佈目標輸出的副檔名設定檔及部署雲端服務時，設定每一個角色診斷副檔名。

    $service_name = "MyService"
    $service_package = "C:\build\output\CloudService.cspkg"
    $service_config = "C:\build\output\ServiceConfiguration.Cloud.cscfg"

    #Find the Extensions path based on service configuration file
    $extensionsSearchPath = Join-Path -Path (Split-Path -Parent $service_config) -ChildPath "Extensions"

    $diagnosticsExtensions = Get-ChildItem -Path $extensionsSearchPath -Filter "PaaSDiagnostics.*.PubConfig.xml"
    $diagnosticsConfigurations = @()
    foreach ($extPath in $diagnosticsExtensions)
    {
    #Find the RoleName based on file naming convention PaaSDiagnostics.<RoleName>.PubConfig.xml
    $roleName = ""
    $roles = $extPath -split ".",0,"simplematch"
    if ($roles -is [system.array] -and $roles.Length -gt 1)
        {
        $roleName = $roles[1]
        $x = 2
        while ($x -le $roles.Length)
            {
               if ($roles[$x] -ne "PubConfig")
                {
                    $roleName = $roleName + "." + $roles[$x]
                }
                else
                {
                    break
                }
                $x++
            }
        $fullExtPath = Join-Path -path $extensionsSearchPath -ChildPath $extPath
        $diagnosticsconfig = New-AzureServiceDiagnosticsExtensionConfig -Role $roleName -DiagnosticsConfigurationPath $fullExtPath
        $diagnosticsConfigurations += $diagnosticsconfig
        }
    }
    New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration $diagnosticsConfigurations

Visual Studio 線上使用的雲端服務的自動化 deploymnts 診斷副檔名類似的方法。 如需完成的範例，請參閱[發佈 AzureCloudDeployment.ps1](https://github.com/Microsoft/vso-agent-tasks/blob/master/Tasks/AzureCloudPowerShellDeployment/Publish-AzureCloudDeployment.ps1) 。

如果沒有 StorageAccount 在診斷設定中指定，您就需要 StorageAccountName 參數傳遞 cmdlet。 如果指定 StorageAccountName 參數，cmdlet 會永遠會使用中的參數，而不指定診斷設定檔中的指定的儲存空間帳戶。

如果診斷儲存帳戶位於不同的訂閱來自雲端服務，您就需要明確 cmdlet 傳遞 StorageAccountName 和 StorageAccountKey 參數。 診斷儲存帳戶是在同一份訂閱，以及 cmdlet 可以自動查詢時啟用診斷延伸設定的值時，就不需要 StorageAccountKey 參數。 不過，如果診斷儲存帳戶位於不同的訂閱，然後 cmdlet 可能無法自動取得金鑰，然後您必須明確地指定透過 StorageAccountKey 參數的金鑰。

    $webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
    $workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key


## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>啟用診斷延伸現有的雲端服務

若要啟用或更新已在執行中的雲端服務上的 [診斷設定，您可以使用[設定 AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589140.aspx)指令程式。


    $service_name = "MyService"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
    $workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

    Set-AzureServiceDiagnosticsExtension -DiagnosticsConfiguration @($webrole_diagconfig,$workerrole_diagconfig) -ServiceName $service_name


## <a name="get-current-diagnostics-extension-configuration"></a>取得目前診斷延伸設定
若要取得雲端服務的目前診斷設定使用[取得 AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589204.aspx)指令程式。

    Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"

## <a name="remove-diagnostics-extension"></a>移除診斷副檔名
若要關閉 [診斷在雲端服務中，您可以使用[移除 AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589183.aspx)指令程式。

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"

如果您已啟用使用*設定 AzureServiceDiagnosticsExtension*或不含*角色*參數*新增 AzureServiceDiagnosticsExtensionConfig*診斷副檔名，您可以移除不含*角色*參數使用*移除 AzureServiceDiagnosticsExtension*副檔名。 如果時啟用副檔名為使用*角色*參數然後它必須也使用移除副檔名。

若要移除的診斷副檔名從每個個別的角色︰

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"


## <a name="next-steps"></a>後續步驟

- 其他使用 Azure 診斷和其他技術來進行問題的疑難排解，請參閱[Azure 雲端服務與虛擬機器中的 [啟用診斷](cloud-services-dotnet-diagnostics.md)。
- [診斷設定結構描述](https://msdn.microsoft.com/library/azure/dn782207.aspx)說明診斷副檔名的各種 xml 設定選項。
- 若要瞭解如何啟用虛擬機器診斷分機號碼，請參閱[建立的監控和範本 Azure 資源管理員的診斷與 Windows 虛擬機器](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)  
