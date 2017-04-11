<properties
    pageTitle="Azure 監視器自動縮放常見指標。 |Microsoft Azure"
    description="了解哪些指標常用自動調整您的雲端服務，虛擬機器和 Web 應用程式。"
    authors="kamathashwin"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/02/2016"
    ms.author="ashwink"/>

# <a name="azure-monitor-autoscaling-common-metrics"></a>Azure 監視器自動縮放常見的指標

Azure 監視器自動縮放可讓您不按比例縮放的執行個體數目，向上或向下，根據遙測資料 （指標）。 這份文件將說明您可能會想要使用的常見指標。 在雲端服務和伺服器陣列的 Azure 入口網站，您可以選擇要縮放之資源的度量。 不過，您也可以選擇不同的資源，若要縮放的任何公制。

以下是如何尋找，並讓您想要不按比例縮放的指標的詳細資料。 下列公式適用於以及縮放虛擬機器縮放比例設定。

## <a name="compute-metrics"></a>計算指標
根據預設，Azure VM v2 隨附診斷副檔名設定，而且有以下評估已開啟。

- [Windows VM v2 來賓度量資訊。](#compute-metrics-for-windows-vm-v2-as-a-guest-os)
- [Linux VM v2 來賓度量資訊。](#compute-metrics-for-linux-vm-v2-as-a-guest-os)

您可以使用`Get MetricDefinitions`API/PoSH/CLI 若要檢視您的 VMSS 資源可用的指標。 

如果您使用的 VM 縮放設定，而且您沒有看到所列的特定度量單位，則可能*已停用*診斷分機中。

如果尚未進行的特定度量取樣或傳輸在您要的頻率，您可以更新診斷程式設定。

如果上述任一情況為 true，檢閱[使用 PowerShell 來啟用在執行 Windows 的虛擬機器中的 Azure 診斷](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md)相關設定，並更新 Azure VM 診斷分機 PowerShell 啟用度量。 文件也包含範例診斷設定檔。

### <a name="compute-metrics-for-windows-vm-v2-as-a-guest-os"></a>計算 OS 以來賓身分的 Windows VM v2 的指標

當您建立新的 VM (v2) Azure 中時，會啟用診斷使用診斷副檔名。

您可以使用下列命令，在 PowerShell 中產生指標的清單。


```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

您可以建立下列指標的提醒。

|單位名稱|   單位|
|---|---|
|\Processor(_Total)\%處理器時間    |完成百分比|
|\Processor(_Total)\%時間的權限   |完成百分比|
|\Processor(_Total)\%使用者時間 |完成百分比|
|\Processor 資訊 (_Total) \Processor 頻率 |字數統計|
|\System\Processes| 字數統計|
|\Process (_Total) \Thread 計數| 字數統計|
|\Process (_Total) \Handle 計數  |字數統計|
|\Memory\%認可位元組使用中   |完成百分比|
|\Memory\Available 位元組|   位元組|
|\Memory\Committed 位元組    |位元組|
|\Memory\Commit 限制|  位元組|
|\Memory\Pool 分頁位元組|  位元組|
|\Memory\Pool 未分頁的位元組|   位元組|
|\PhysicalDisk(_Total)\%磁碟時間| 完成百分比|
|\PhysicalDisk(_Total)\%磁碟讀取時間|    完成百分比|
|\PhysicalDisk(_Total)\%磁碟寫入時間|   完成百分比|
|\PhysicalDisk (_Total) \Disk 傳輸秒   |CountPerSecond|
|\PhysicalDisk (_Total) \Disk 讀取/秒   |CountPerSecond|
|\PhysicalDisk (_Total) \Disk 寫入/秒  |CountPerSecond|
|\PhysicalDisk (_Total) \Disk 秒位元組   |BytesPerSecond|
|閱讀秒位元組 \PhysicalDisk (_Total) \Disk| BytesPerSecond|
|\PhysicalDisk (_Total) \Disk 寫入每秒位元組 |BytesPerSecond|
|\PhysicalDisk (_Total) \Avg。 磁碟佇列長度|  字數統計|
|\PhysicalDisk (_Total) \Avg。 磁碟讀取佇列長度| 字數統計|
|\PhysicalDisk (_Total) \Avg。 磁碟寫入佇列長度 |字數統計|
|\LogicalDisk(_Total)\%釋放空間| 完成百分比|
|\LogicalDisk (_Total) \Free mb|   字數統計|



### <a name="compute-metrics-for-linux-vm-v2-as-a-guest-os"></a>OS 以來賓身分計算 Linux VM v2 的指標

當您建立新的 VM (v2) Azure 中時，診斷預設會啟用藉由使用診斷副檔名。

您可以使用下列命令，在 PowerShell 中產生指標的清單。

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 您可以建立下列指標的提醒。

|單位名稱                            |單位|
|---|---|
|\Memory\AvailableMemory                |位元組|
|\Memory\PercentAvailableMemory         |完成百分比|
|\Memory\UsedMemory                     |位元組|
|\Memory\PercentUsedMemory              |完成百分比|
|\Memory\PercentUsedByCache             |完成百分比|
|\Memory\PagesPerSec                    |CountPerSecond|
|\Memory\PagesReadPerSec                |CountPerSecond|
|\Memory\PagesWrittenPerSec             |CountPerSecond|
|\Memory\AvailableSwap                  |位元組|
|\Memory\PercentAvailableSwap           |完成百分比|
|\Memory\UsedSwap                       |位元組|
|\Memory\PercentUsedSwap                |完成百分比|
|\Processor\PercentIdleTime             |完成百分比|
|\Processor\PercentUserTime             |完成百分比|
|\Processor\PercentNiceTime             |完成百分比|
|\Processor\PercentPrivilegedTime       |完成百分比|
|\Processor\PercentInterruptTime        |完成百分比|
|\Processor\PercentDPCTime              |完成百分比|
|\Processor\PercentProcessorTime        |完成百分比|
|\Processor\PercentIOWaitTime           |完成百分比|
|\PhysicalDisk\BytesPerSecond           |BytesPerSecond|
|\PhysicalDisk\ReadBytesPerSecond       |BytesPerSecond|
|\PhysicalDisk\WriteBytesPerSecond      |BytesPerSecond|
|\PhysicalDisk\TransfersPerSecond       |CountPerSecond|
|\PhysicalDisk\ReadsPerSecond           |CountPerSecond|
|\PhysicalDisk\WritesPerSecond          |CountPerSecond|
|\PhysicalDisk\AverageReadTime          |（秒)|
|\PhysicalDisk\AverageWriteTime         |（秒)|
|\PhysicalDisk\AverageTransferTime      |（秒)|
|\PhysicalDisk\AverageDiskQueueLength   |字數統計|
|\NetworkInterface\BytesTransmitted     |位元組|
|\NetworkInterface\BytesReceived        |位元組|
|\NetworkInterface\PacketsTransmitted   |字數統計|
|\NetworkInterface\PacketsReceived      |字數統計|
|\NetworkInterface\BytesTotal           |位元組|
|\NetworkInterface\TotalRxErrors        |字數統計|
|\NetworkInterface\TotalTxErrors        |字數統計|
|\NetworkInterface\TotalCollisions      |字數統計|




## <a name="commonly-used-web-server-farm-metrics"></a>常用的網頁 （伺服器陣列） 指標

您也可以執行自動調整大小根據常見網頁伺服器指標，例如 Http 佇列長度。 它的公制名稱就是**HttpQueueLength**。  下一節會列出可用的伺服器陣列 （Web 應用程式） 指標。

### <a name="web-apps-metrics"></a>Web 應用程式的指標

您可以使用下列命令，在 PowerShell 中產生 Web 應用程式指標的清單。

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

您可以在 [提醒或不按比例縮放這些度量。

|單位名稱        |單位|
|---                |---|
|CpuPercentage      |完成百分比|
|MemoryPercentage   |完成百分比|
|DiskQueueLength    |字數統計|
|HttpQueueLength    |字數統計|
|BytesReceived      |位元組|
|BytesSent          |位元組|


## <a name="commonly-used-storage-metrics"></a>常用的儲存指標
您可以調整儲存佇列長度儲存佇列中的訊息數。 儲存佇列長度特殊度量單位，並套用閥值將會每個執行個體的訊息數。 這表示如果有兩個執行個體，而且如果臨界值設定為 100，則會縮放總數佇列中的郵件時 200。 每個執行個體，例如 100 訊息。

您可以設定這是在中**設定**刀 Azure 入口網站。 VM 比例組，您可以更新 ARM 範本中自動調整大小設定為*ApproximateMessageCount*使用*metricName* ，並為*metricResourceUri*傳遞儲存佇列中的識別碼。

例如，使用傳統的儲存空間帳戶自動調整大小設定 metricTrigger 包含所示︰

```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
 "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
 ```

（非傳統） 儲存帳戶，包括 metricTrigger:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Storage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

## <a name="commonly-used-service-bus-metrics"></a>經常使用的服務匯流排指標

您可以縮放服務匯流排佇列長度服務匯流排佇列中的訊息數。 服務匯流排佇列長度特殊度量單位，並指定的閾值套用會每個執行個體的訊息數。 這表示如果有兩個執行個體，而且如果臨界值設定為 100，則會縮放總數佇列中的郵件時 200。 每個執行個體，例如 100 訊息。

VM 比例組，您可以更新 ARM 範本中的自動調整大小設定為*ApproximateMessageCount*使用*metricName* ，並為*metricResourceUri*傳遞的儲存空間佇列中的識別碼。

```
"metricName": "MessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

>[AZURE.NOTE] 服務匯流排資源群組概念不存在，但 Azure 資源管理員建立為每個地區預設資源群組。 資源群組通常是 [預設-ServiceBus-[region]] 格式。 例如 」 預設為 ServiceBus-EastUS 」、 「 預設-ServiceBus-WestUS 」、 「 預設-ServiceBus-AustraliaEast 」 等。
