<properties
    pageTitle="使用 PowerShell 來執行 Windows 虛擬機器中啟用 Azure 診斷 |Microsoft Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    description="瞭解如何使用 PowerShell 來執行 Windows 的虛擬機器中啟用 Azure 診斷程式"
    authors="sbtron"
    manager="timlt"
    editor=""/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/15/2015"
    ms.author="saurabh"/>


# <a name="use-powershell-to-enable-azure-diagnostics-in-a-virtual-machine-running-windows"></a>使用 PowerShell 來執行 Windows 的虛擬機器中啟用 Azure 診斷程式

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Azure 診斷是啟用診斷上部署的應用程式的資料收集的 Azure 中的功能。 您可以使用副檔名為診斷程式診斷的資料，例如應用程式的記錄檔] 或 [效能計數器收集 Azure 虛擬機器 (VM) 執行 Windows。 本文將說明如何使用 Windows PowerShell vm 啟用診斷副檔名。 本文所需的必要條件，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md) 。

## <a name="enable-the-diagnostics-extension-if-you-use-the-resource-manager-deployment-model"></a>如果您使用的資源管理員部署模型，啟用診斷副檔名

當您將延伸設定新增到資源管理員範本建立 Windows VM 透過 Azure 資源管理員部署模型時，您可以啟用診斷副檔名。 請參閱[建立的監控和範本 Azure 資源管理員的診斷與 Windows 虛擬機器](virtual-machines-windows-extensions-diagnostics-template.md)。

若要啟用資源管理員部署模型透過所建立的現有 VM 診斷副檔名，您可以使用[設定 AzureRMVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt603499.aspx) PowerShell cmdlet，如下所示。


    $vm_resourcegroup = "myvmresourcegroup"
    $vm_name = "myvm"
    $diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path


*$diagnosticsconfig_path*是包含診斷設定中的 XML，[範例](#sample-diagnostics-configuration)以下所述之檔案的路徑。  

如果診斷設定檔案儲存體帳戶名稱與指定**StorageAccount**項目，*設定 AzureRMVMDiagnosticsExtension*指令碼會自動設定診斷副檔名儲存帳戶傳送診斷的資料。 這個工作，儲存帳戶必須在同一份訂閱為 VM。

在診斷設定中指定沒有**StorageAccount** ，如果您需要*StorageAccountName*參數傳遞 cmdlet。 如果指定*StorageAccountName*參數，cmdlet 一律會使用參數中指定的儲存空間帳戶並不在 [診斷設定檔中指定的一個。

如果診斷儲存帳戶位於 VM 從不同的訂閱，您就需要明確傳遞*StorageAccountName*和*StorageAccountKey*參數 cmdlet。 診斷儲存帳戶是在同一份訂閱，以及 cmdlet 可以自動查詢時啟用診斷延伸設定的值時，就不需要*StorageAccountKey*參數。 不過，如果診斷儲存帳戶位於不同的訂閱，然後 cmdlet 可能無法自動取得索引鍵，然後您必須明確地指定透過*StorageAccountKey*參數鍵。  

    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key

一旦 VM 上啟用診斷副檔名，您可以在使用[取得 AzureRMVmDiagnosticsExtension](https://msdn.microsoft.com/library/mt603678.aspx)指令程式來取得目前的設定。

    Get-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name

指令程式會傳回*PublicSettings*，其中包含 Base64 編碼格式的 XML 設定。 若要閱讀 XML，必須先將它解碼。

    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

[移除 AzureRMVmDiagnosticsExtension](https://msdn.microsoft.com/library/mt603782.aspx)指令程式可用來移除 VM 診斷副檔名。  

## <a name="enable-the-diagnostics-extension-if-you-use-the-classic-deployment-model"></a>如果您是使用傳統的部署模型，啟用診斷副檔名

若要啟用您建立傳統的部署模型透過 VM 診斷副檔名，您可以使用[設定 AzureVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt589189.aspx)指令程式。 下列範例會示範如何建立新的傳統部署模型透過 VM 啟用診斷副檔名。

    $VM = New-AzureVMConfig -Name $VM -InstanceSize Small -ImageName $VMImage
    $VM = Add-AzureProvisioningConfig -VM $VM -AdminUsername $Username -Password $Password -Windows
    $VM = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    New-AzureVM -Location $Location -ServiceName $Service_Name -VM $VM

若要啟用透過傳統部署模型建立現有 VM 診斷副檔名，第一次使用[取得 AzureVM](https://msdn.microsoft.com/library/mt589152.aspx) cmdlet 來取得 VM 設定。 然後更新 VM 設定使用來共用診斷副檔名[設定 AzureVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt589189.aspx)指令程式。 最後，更新的設定使用套用至 VM[更新 AzureVM](https://msdn.microsoft.com/library/mt589121.aspx)。

    $VM = Get-AzureVM -ServiceName $Service_Name -Name $VM_Name
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    Update-AzureVM -ServiceName $Service_Name -Name $VM_Name -VM $VM_Update.VM

## <a name="sample-diagnostics-configuration"></a>範例診斷設定

下列 XML 可以用於診斷公用設定使用上述的指令碼。 此範例設定將會傳送各種效能計數器診斷儲存的帳戶，以及從應用程式、 安全性和 Windows 事件記錄檔中的系統頻道的錯誤和診斷基礎結構記錄檔中的任何錯誤。

設定必須更新包括下列各項︰

- **指標**項目的*預設*屬性必須 vm 更新的資源識別碼。
    - 您可使用下列模式建構資源識別碼: 「 / 訂閱 / {*VM 訂閱訂閱識別碼*} /resourceGroups/ {*resourcegroup 名稱 vm*} / providers/Microsoft.Compute/virtualMachines/ {*VM 名稱*} 」。
    - 例如，如果正在 VM 訂閱的訂閱識別碼**11111111-1111-1111-1111-111111111111**、 [資源] 群組的資源群組名稱是**MyResourceGroup**，而 VM 名稱會**MyWindowsVM**，然後*預設*的值會︰

        ```
        <Metrics resourceId="/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/MyWindowsVM" >
        ```

    - 如需有關如何指標會產生根據效能計數器及標準設定，請參閱[Azure 診斷指標資料表儲存體中](virtual-machines-windows-extensions-diagnostics-template.md#wadmetrics-tables-in-storage)。

- **StorageAccount**項目必須以更新的診斷儲存體帳戶名稱。

    ```
    <?xml version="1.0" encoding="utf-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
          <DiagnosticMonitorConfiguration overallQuotaInMB="4096">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU utilization" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Privileged Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU privileged time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% User Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU user time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor Information(_Total)\Processor Frequency" sampleRate="PT15S" unit="Count">
            <annotation displayName="CPU frequency" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\System\Processes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Processes" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Thread Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Threads" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Handle Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Handles" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes In Use" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Memory usage" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory available" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory committed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Commit Limit" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory commit limit" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Paged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Nonpaged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory non-paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Read Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active read time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Write Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active write time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Transfers/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Reads/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk read operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Writes/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk write operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Read Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk read speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Write Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk write speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Read Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average read queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Write Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average write queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\% Free Space" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk free space (percentage)" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\Free Megabytes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk free space (MB)" locale="en-us"/>
          </PerformanceCounterConfiguration>
        </PerformanceCounters>
        <Metrics resourceId="(Update with resource ID for the VM)" >
            <MetricAggregation scheduledTransferPeriod="PT1H"/>
            <MetricAggregation scheduledTransferPeriod="PT1M"/>
        </Metrics>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]"/>
          <DataSource name="Security!*[System[(Level = 1 or Level = 2)]"/>
          <DataSource name="System!*[System[(Level = 1 or Level = 2)]]"/>
        </WindowsEventLog>
          </DiagnosticMonitorConfiguration>
        </WadCfg>
        <StorageAccount>(Update with diagnostics storage account name)</StorageAccount>
    </PublicConfig>
    ```

## <a name="next-steps"></a>後續步驟
- 其他使用 Azure 診斷功能及其他技術來進行問題的疑難排解，請參閱[Azure 雲端服務與虛擬機器中的 [啟用診斷](../cloud-services/cloud-services-dotnet-diagnostics.md)。
- [診斷設定結構描述](https://msdn.microsoft.com/library/azure/mt634524.aspx)說明診斷副檔名的各種 XML 設定選項。
