<properties
    pageTitle="使用 blob 儲存體 IIS 和表格的儲存空間的事件 |Microsoft Azure"
    description="撰寫診斷以資料表儲存體的 Azure 服務記錄檔] 或 [寫入 blob 儲存體 IIS 記錄，可以讀取記錄分析。"
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="banders"/>


# <a name="using-blob-storage-for-iis-and-table-storage-for-events"></a>使用事件 iis blob 儲存體和資料表儲存體

記錄檔分析可以閱讀下列服務所撰寫至資料表儲存體診斷記錄檔] 或 [寫入 blob 儲存體 IIS 記錄︰

+ 服務布料的轉印圖樣叢集 （預覽版本）
+ 虛擬機器
+ Web/工作者角色

記錄檔分析可以收集這些資源的資料之前，必須先啟用 Azure 診斷。

一旦啟用診斷，您可以使用 [Azure 入口網站，或 PowerShell 設定記錄以收集記錄的分析。

Azure 診斷是 Azure 延伸可讓您從工作者角色、 網頁角色或執行中 Azure 虛擬機器收集診斷的資料。 資料儲存於 Azure 儲存體帳戶，然後收集記錄分析。

記錄以收集這些 Azure 診斷記錄檔的分析，記錄必須在下列位置︰

| 記錄類型 | 資源類型 | 位置 |
| -------- | ------------- | -------- |
| IIS 記錄檔 | 虛擬機器 <br> Web 角色 <br> 工作者角色 | wad iis-記錄檔 」 （Blob 儲存體） |
| 系統 | 虛擬機器 | LinuxsyslogVer2v0 （資料表儲存體） |
| 服務布料的轉印圖樣作業事件 | 服務布料的轉印圖樣節點 | WADServiceFabricSystemEventTable |
| 服務布料的轉印圖樣可靠的動作項目事件 | 服務布料的轉印圖樣節點 | WADServiceFabricReliableActorEventTable |
| 服務布料的轉印圖樣可靠的服務事件 | 服務布料的轉印圖樣節點 | WADServiceFabricReliableServiceEventTable |
| Windows 事件記錄檔 | 服務布料的轉印圖樣節點 <br> 虛擬機器 <br> Web 角色 <br> 工作者角色 | WADWindowsEventLogsTable （資料表儲存體） |
| Windows ETW 記錄 | 服務布料的轉印圖樣節點 <br> 虛擬機器 <br> Web 角色 <br> 工作者角色 | WADETWEventTable （資料表儲存體） |

>[AZURE.NOTE] 目前不支援從 Azure 網站 IIS 記錄。

虛擬機器中，您可以選擇將您的虛擬機器安裝[記錄分析代理程式](log-analytics-azure-vm-extension.md)的啟用其他深入資訊。 除了能夠分析 IIS 記錄和事件記錄，您可以執行其他的分析，包括設定變更追蹤、 SQL 評估和更新評估。

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection"></a>啟用 Azure 虛擬機器中的診斷程式的事件記錄檔和 IIS 記錄集合

若要啟用 Azure 診斷使用 Microsoft Azure 入口網站的事件記錄檔和 IIS 記錄集合的虛擬機器中使用下列程序。

### <a name="to-enable-azure-diagnostics-in-a-virtual-machine-with-the-azure-portal"></a>若要啟用 Azure 虛擬機器 Azure 入口網站中的診斷程式

1. 當您建立虛擬機器時，請安裝 VM 代理程式。 如果虛擬機器已經存在，請確認已安裝 [VM 代理程式。
    - 在 Azure 入口網站中，瀏覽至虛擬機器、 選取 [**選擇性的設定**，然後**診斷**和設定**的狀態**為 [**開啟**。

    完成時，VM 會有安裝及執行 Azure 診斷副檔名。 這個延伸負責收集診斷資料。

2. 啟用 [監控和設定登入現有的 VM 的事件。 您可以啟用診斷 VM 層級。 若要啟用診斷並設定事件記錄，請執行下列步驟︰
    1. 選取 [VM]。
    2. 按一下 [**監視**]。
    3. 按一下 [**診斷**]。
    4. 設定**狀態**至 [**開啟]**。
    5. 選取您要收集每個診斷記錄檔。
    7. 按一下**[確定]**。

使用 PowerShell 的 Azure 您可以更精確地指定 Azure 儲存體寫入的事件。 請參閱[收集使用 Azure 資料表儲存體或寫入 blob 的 IIS 記錄撰寫的診斷程式的資料](log-analytics-azure-storage-json.md)。


## <a name="enable-azure-diagnostics-in-a-web-role-for-iis-log-and-event-collection"></a>啟用 Web 角色 IIS 記錄與事件集合中的 Azure 診斷程式

如需啟用 Azure 診斷程式的一般步驟，請參閱[如何以啟用診斷雲端服務中](../cloud-services/cloud-services-dotnet-diagnostics.md)。 下列指示使用這項資訊，並加以自訂的記錄檔分析搭配使用。

Azure 診斷啟用︰

- 根據預設，儲存 IIS 記錄記錄資料傳輸 scheduledTransferPeriod 傳輸間隔。
- 預設未 Windows 事件記錄檔。

### <a name="to-enable-diagnostics"></a>若要啟用診斷程式

若要啟用 Windows 事件記錄，或變更 scheduledTransferPeriod，設定 Azure 診斷程式使用 XML 設定檔案 (diagnostics.wadcfg)，如下所示[步驟 4︰ 建立您的診斷設定檔，並安裝副檔名為](../cloud-services/cloud-services-dotnet-diagnostics.md)

下列範例設定檔案會從應用程式與系統記錄收集 IIS 記錄檔和所有事件︰

```
    <?xml version="1.0" encoding="utf-8" ?>
    <DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"
          configurationChangePollInterval="PT1M"
          overallQuotaInMB="4096">

      <Directories bufferQuotaInMB="0"
         scheduledTransferPeriod="PT10M">  
        <!-- IISLogs are only relevant to Web roles -->
        <IISLogs container="wad-iis" directoryQuotaInMB="0" />
      </Directories>

      <WindowsEventLog bufferQuotaInMB="0"
         scheduledTransferLogLevelFilter="Verbose"
         scheduledTransferPeriod="PT10M">
        <DataSource name="Application!*" />
        <DataSource name="System!*" />
      </WindowsEventLog>

    </DiagnosticMonitorConfiguration>
```

請確定您 ConfigurationSettings 指定儲存帳戶，如下列範例所示︰

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>
```

在儲存帳戶儀表板，在 [管理便捷鍵下 Azure 入口網站中，找到 [ **AccountName** ] 和 [ **AccountKey**值。 連接字串的通訊協定必須**https**。

後更新診斷設定會套用至您的雲端服務，它會寫入診斷 Azure 儲存體您準備好要設定記錄分析。


## <a name="use-the-azure-portal-to-collect-logs-from-azure-storage"></a>若要從 Azure 儲存體收集記錄使用 Azure 入口網站

若要設定記錄分析，收集下列 Azure 服務的記錄，您可以使用 [Azure 入口網站︰

+ 服務布料的轉印圖樣叢集
+ 虛擬機器
+ Web/工作者角色

在 Azure 入口網站，瀏覽至您的記錄檔分析工作區，並執行下列工作︰

1. 按一下 [*儲存帳戶記錄*
2. 按一下 [*新增*工作
3. 選取的儲存空間帳戶包含診斷記錄
  - 此帳戶可傳統的儲存空間帳戶或 Azure 資源管理員儲存帳戶
4. 選取您要收集的記錄的資料類型
  - 選項包括 [IIS 記錄檔。事件。系統 (Linux);ETW 記錄檔。服務布料的轉印圖樣事件
5. 來源的值會自動填入資料型別，且無法變更
6. 按一下 [確定] 儲存設定

重複步驟 2-6 的額外儲存空間帳戶和您想要收集記錄分析的資料類型。

在 30 分鐘左右，您就可以看到儲存的帳戶記錄分析的資料。 您只會看到套用設定後，會寫入儲存的資料。 記錄檔分析不會讀取從儲存的帳戶已存在的資料。

>[AZURE.NOTE] 來源存在於儲存帳戶，或如果寫入新資料，並不會驗證入口網站。

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection-using-powershell"></a>啟用 Azure 虛擬機器中的診斷程式的事件記錄檔和 IIS 登入使用 PowerShell 的集合

使用中[索引 Azure 診斷設定記錄分析](log-analytics-powershell-workspace-configuration.md#configuring-log-analytics-to-index-azure-diagnostics)的步驟，使用 PowerShell 的 Azure 資料表儲存體寫入的診斷讀取。

使用 PowerShell 的 Azure 您可以更精確地指定 Azure 儲存體寫入的事件。
如需詳細資訊，請參閱[啟用診斷 Azure 虛擬機器中的商務連絡人](../virtual-machines-dotnet-diagnostics.md)。

您可以啟用並更新 Azure 診斷使用下列 PowerShell 指令碼。
您也可以使用這個指令碼的自訂記錄設定。
修改指令碼來設定儲存帳戶、 服務名稱及虛擬機器名稱。
指令碼使用傳統的虛擬機器 cmdlet。

檢閱下列指令碼範例、 複製、 視需要修改、 將範例儲存為 PowerShell 指令碼檔案，並執行指令碼。

```
    #Connect to Azure
    Add-AzureAccount

    # settings to change:
    $wad_storage_account_name = "myStorageAccount"
    $service_name = "myService"
    $vm_name = "myVM"

    #Construct Azure Diagnostics public config and convert to config format

    # Collect just system error events:
    $wad_xml_config = "<WadCfg><DiagnosticMonitorConfiguration><WindowsEventLog scheduledTransferPeriod=""PT1M""><DataSource name=""System!* "" /></WindowsEventLog></DiagnosticMonitorConfiguration></WadCfg>"

    $wad_b64_config = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($wad_xml_config))
    $wad_public_config = [string]::Format("{{""xmlCfg"":""{0}""}}",$wad_b64_config)

    #Construct Azure diagnostics private config

    $wad_storage_account_key = (Get-AzureStorageKey $wad_storage_account_name).Primary
    $wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)

    #Enable Diagnostics Extension for Virtual Machine

    $wad_extension_name = "IaaSDiagnostics"
    $wad_publisher = "Microsoft.Azure.Diagnostics"
    $wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Version # Gets latest version of the extension

    (Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM
```


## <a name="next-steps"></a>後續步驟

- 讀取記錄 Azure [blob 儲存體中的使用 JSON 檔案](log-analytics-azure-storage-json.md)服務至 blob 儲存體 JSON 格式的寫入診斷。
- [啟用解決方案](log-analytics-add-solutions.md)提供深入資料。
- [使用搜尋查詢](log-analytics-log-searches.md)，以分析的資料。
