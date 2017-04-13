<properties
    pageTitle="建立 Windows 虛擬機器的監視與範本 Azure 資源管理員的診斷 |Microsoft Azure"
    description="使用 Azure 資源管理員範本來建立新的 Windows 虛擬機器 Azure 診斷副檔名。"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="sbtron"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/15/2015"
    ms.author="saurabh"/>

# <a name="create-a-windows-virtual-machine-with-monitoring-and-diagnostics-using-azure-resource-manager-template"></a>監控和範本 Azure 資源管理員的診斷與建立 Windows 虛擬機器

Azure 診斷副檔名提供監控而診斷功能，在 Windows Azure 虛擬機器。 您可以啟用這些功能虛擬機器上的所包含的副檔名為 azure 資源管理員範本的一部分。 如需有關包括任何分機虛擬機器範本的一部分，請參閱[副檔名為 VM 中撰寫 Azure 資源管理員範本](virtual-machines-windows-extensions-authoring-templates.md)。 本文將說明如何新增 Azure 診斷延伸到 windows 虛擬機器範本。  
  

## <a name="add-the-azure-diagnostics-extension-to-the-vm-resource-definition"></a>新增 VM 資源定義 Azure 診斷副檔名 

若要啟用診斷副檔名 Windows 虛擬機器上您要新增為 VM 資源資源管理員範本中的延伸。

簡單資源管理員為根據的虛擬機器陣列中加入延伸設定*資源*虛擬機器︰ 

    "resources": [
                {
                    "name": "Microsoft.Insights.VMDiagnosticsSettings",
                    "type": "extensions",
                    "location": "[resourceGroup().location]",
                    "apiVersion": "2015-06-15",
                    "dependsOn": [
                        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
                    ],
                    "tags": {
                        "displayName": "AzureDiagnostics"
                    },
                    "properties": {
                        "publisher": "Microsoft.Azure.Diagnostics",
                        "type": "IaaSDiagnostics",
                        "typeHandlerVersion": "1.5",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
                            "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
                        },
                        "protectedSettings": {
                            "storageAccountName": "[parameters('existingdiagnosticsStorageAccountName')]",
                            "storageAccountKey": "[listkeys(variables('accountid'), '2015-05-01-preview').key1]",
                            "storageAccountEndPoint": "https://core.windows.net"
                        }
                    }
                }
            ]


其他常見的慣例是新增資源的根節點的範本，而不是定義虛擬機器中的 [資源] 節點下延伸設定。 使用這個方法，您必須使用的*名稱*，然後*輸入*值，明確指定副檔名與虛擬機器之間的階層式關聯。 例如︰ 
  
    "name": "[concat(variables('vmName'),'Microsoft.Insights.VMDiagnosticsSettings')]",
    "type": "Microsoft.Compute/virtualMachines/extensions",

副檔名為一律虛擬機器與相關聯，您可以直接定義虛擬機器中的 [資源] 節點下直接或進行定義的底數層級，並將其關聯虛擬機器中使用的階層式的命名慣例。

虛擬機器比例組副檔名設定*VirtualMachineProfile* *extensionProfile*屬性中指定。
   
*Publisher*屬性**Microsoft.Azure.Diagnostics**的值與*類型*] 屬性的**IaaSDiagnostics**值可唯一識別 Azure 診斷副檔名。

[*名稱*] 屬性的值可以用於參考 [資源] 群組中的副檔名。 設定專為**Microsoft.Insights.VMDiagnosticsSettings**會啟用透過 Azure 傳統入口網站入口網站確保監控圖表顯示正確 Azure 傳統入口網站中便於識別。

*TypeHandlerVersion*指定您想要使用的擴充版本。 設定*autoUpgradeMinorVersion*為**true**的次要版本，確保您會收到延伸所提供的最新的次要版本。 強烈建議您永遠設定*autoUpgradeMinorVersion*永遠為**true** ，讓您永遠取得最新可用的診斷副檔名為使用您所有的新功能和修正錯誤。 

*設定*項目包含設定屬性的副檔名，可以設定及讀取回從延伸 （有時稱為公用設定）。 *Xmlcfg*屬性包含 xml 架構診斷記錄] 中，設定效能計數器會收集診斷代理程式的等。 Xml 結構描述本身的相關資訊，請參閱[診斷設定結構描述](https://msdn.microsoft.com/library/azure/dn782207.aspx)。 常見的做法是將實際的 xml 設定儲存為 Azure 資源管理員範本中的變數，然後 concatenate 和 base64 編碼即可設定*xmlcfg*] 的值。 若要了解更多有關如何儲存在變數中的 xml[診斷設定變數](#diagnostics-configuration-variables)，請參閱] 區段。 *StorageAccount*屬性會指定將會傳送診斷資料的儲存體帳戶名稱。 
 
可以設定*protectedSettings* （有時稱為私人設定） 中的內容，但無法讀取回後所設定。 *ProtectedSettings*唯性質可讓儲存儲存帳戶金鑰等機密診斷資料寫入的位置。    

## <a name="specifying-diagnostics-storage-account-as-parameters"></a>指定做為參數的診斷儲存的帳戶 

診斷副檔名 json 程式碼片段上述假設兩個參數*existingdiagnosticsStorageAccountName*和*existingdiagnosticsStorageResourceGroup* ，若要指定儲存診斷資料的儲存空間診斷帳戶。 指定診斷儲存帳戶參數可讓您輕鬆地變更的診斷儲存帳戶不同環境例如您可能會想要測試和生產部署的另一個使用不同的診斷儲存帳戶。  

        "existingdiagnosticsStorageAccountName": {
            "type": "string",
            "metadata": {
        "description": "The name of an existing storage account to which diagnostics data will be transfered."
            }        
        },
        "existingdiagnosticsStorageResourceGroup": {
            "type": "string",
            "metadata": {
        "description": "The resource group for the storage account specified in existingdiagnosticsStorageAccountName"
            }
        }

是，指定診斷儲存帳戶位於不同的資源群組的虛擬機器中的 [資源] 群組的最佳作法。 資源群組可以被視為部署單位使用自己的存留時間，可以部署及部署新設定會更新，以便，但是您可能會想要繼續儲存診斷資料在同一個儲存帳戶，跨這些虛擬機器部署虛擬機器。 在不同的資源時遇到的儲存空間帳戶可接受來自各種不同的虛擬機器部署幫助您輕鬆問題進行疑難排解不同版本之間的資料的儲存空間帳戶。

>[AZURE.NOTE] 如果您從 Visual Studio 建立 windows 虛擬機器範本的預設儲存的帳戶可能會設為使用相同的儲存空間帳戶虛擬機器 VHD 上傳的所在位置。 這是以簡化 VM 初始設定。 您應該重新因素要使用不同的儲存帳戶，可以做為參數傳遞中的範本。 

## <a name="diagnostics-configuration-variables"></a>診斷設定變數
 
診斷副檔名 json 程式碼片段上述定義*accountid*變數以簡化快速診斷儲存空間的儲存空間帳戶金鑰︰   
    
    "accountid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',parameters('existingdiagnosticsStorageResourceGroup'), '/providers/','Microsoft.Storage/storageAccounts/', parameters('existingdiagnosticsStorageAccountName'))]"


診斷副檔名*xmlcfg*屬性定義中使用多個變數串連在一起。 Xml 的這些變數的值是，因此需要逸出正確時設定 json 變數。

下列說明診斷設定 xml 收集標準系統層級的效能計數器還有一些的 windows 事件記錄檔和診斷基礎結構的記錄。 已逸出字元及正確格式化，以便設定直接可貼到您的範本的變數一節。 請參閱設定 xml 的其他人可讀取範例[診斷設定結構描述](https://msdn.microsoft.com/library/azure/dn782207.aspx)。
    
        "wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
        "wadperfcounters1": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Privileged Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU privileged time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% User Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU user time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor Information(_Total)\\Processor Frequency\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"CPU frequency\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\System\\Processes\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Processes\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Threads\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Handle Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Handles\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\% Committed Bytes In Use\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Memory usage\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Available Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory available\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Committed Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory committed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Commit Limit\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory commit limit\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active time\" locale=\"en-us\"/></PerformanceCounterConfiguration>",
        "wadperfcounters2": "<PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Read Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active read time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Write Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active write time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Transfers/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Reads/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk read operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Writes/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk write operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Read Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk read speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Write Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk write speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\LogicalDisk(_Total)\\% Free Space\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk free space (percentage)\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
        "wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters1'), variables('wadperfcounters2'), '<Metrics resourceId=\"')]",
        "wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name , '/providers/', 'Microsoft.Compute/virtualMachines/')]",
        "wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>"

上述設定中的指標定義 xml 節點是重要設定項目定義*PerformanceCounter*節點中 xml 較舊版本中定義的效能計數器如何將彙總，儲存。 

> [AZURE.IMPORTANT] 這些計量驅動 Azure 入口網站中的監控的圖表和通知。  *預設*與**MetricAggregation** **指標**節點必須包含您 VM 的診斷設定中，如果您想要查看 Azure 入口網站中的 VM 監控資料。 

指標定義的 xml 範例如下︰ 

        <Metrics resourceId="/subscriptions/subscription().subscriptionId/resourceGroups/resourceGroup().name/providers/Microsoft.Compute/virtualMachines/vmName">
            <MetricAggregation scheduledTransferPeriod="PT1H"/>
            <MetricAggregation scheduledTransferPeriod="PT1M"/>
        </Metrics>

*預設*屬性可唯一識別虛擬機器中您的訂閱。 請確定使用的 subscription() 及 resourceGroup() 功能，讓範本會自動更新訂閱及資源] 群組中您要部署到這些值。

如果您要建立多個虛擬機器中的循環播放您會有填入正確分辨每個個別 VM copyIndex() 函式的*預設*值。 *XmlCfg*值都可以更新支援，如下所示︰  

    "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), concat(parameters('vmNamePrefix'), copyindex()), variables('wadcfgxend')))]", 

MetricAggregation 值*PT1H*和*PT1M*表示彙總時數分鐘與上一個小時彙總。

## <a name="wadmetrics-tables-in-storage"></a>WADMetrics 資料表儲存體

上述的指標設定會產生下列的命名慣例診斷儲存帳戶中的表格︰

- **WADMetrics** ︰ 所有 WADMetrics 表格的標準字首
- **PT1H**或**PT1M** ︰ 代表資料表包含超過 1 小時] 或 [1 分鐘的彙總資料
- **P10D** ︰ 代表資料表會從資料表開始收集資料 10 天包含資料
- **V2S** ︰ 字串常數
- **yyyymmdd** ︰ 表格開始收集資料的日期

範例︰ *WADMetricsPT1HP10DV2S20151108*含有指標開始 11-年 11 月-2015 年 10 天小時進行彙總的資料    

每個 WADMetrics 資料表會包含下列欄位︰

- **PartitionKey**: partitionkey 公式的結構，可唯一識別 VM 資源的*預設*值。 針對例如︰ 002Fsubscriptions:<subscriptionID>: 002FresourceGroups:002F<ResourceGroupName>: 002Fproviders:002FMicrosoft:002ECompute:002FvirtualMachines:002F<vmName>  
- **RowKey** : 格式<Descending time tick>:<Performance Counter Name>。 遞減的時間刻度計算是最大時間刻度減號彙總期間開始的時間。 例如︰ 如果範例期間啟動 10-年 11 月-2015 年，而 00:00Hrs UTC 然後計算可能會︰ DateTime.MaxValue.Ticks-(新 DateTime(2015,11,10,0,0,0,DateTimeKind.Utc)。刻度）。 可用的位元組效能計數器列索引鍵的記憶體看起來像︰ 2519551871999999999__:005CMemory:005CAvailable:0020 位元組
- **CounterName** ︰ 是效能計數器的名稱。 此比對*counterSpecifier* xml 設定中所定義。
- **最大值**︰ 效能計數器彙總期間的最大值。
- **最小**︰ 效能計數器彙總期間的最小值。
- **總**︰ 效能計數器的所有值的總和報告彙總時間。
- **計數**︰ 效能計數器報告的合計值的數目。
- **平均**︰ 效能計數器彙總時間的平均值 （總計/計數） 值。


## <a name="next-steps"></a>後續步驟

- Windows 虛擬機器診斷副檔名的完整的範例範本，請參閱[201-vm-監控-診斷-副檔名](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-monitoring-diagnostics-extension)   
- 部署資源管理員範本使用[PowerShell 的 Azure](virtual-machines-windows-ps-manage.md)或[Azure 命令列](virtual-machines-linux-cli-deploy-templates.md)
- 深入瞭解[撰寫 Azure 資源管理員範本](../resource-group-authoring-templates.md)







