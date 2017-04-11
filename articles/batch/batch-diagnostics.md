<properties
   pageTitle="Azure 批次診斷記錄 |Microsoft Azure"
   description="錄製及分析診斷記錄事件的 Azure 批次帳戶資源，例如資料庫及工作。"
   services="batch"
   documentationCenter=""
   authors="mmacy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="big-compute"
   ms.date="10/12/2016"
   ms.author="marsma"/>

# <a name="azure-batch-diagnostic-logging"></a>Azure 批次診斷記錄

就跟許多 Azure 服務，批次服務會發出資源的存留期間的特定資源記錄事件。 您可以啟用 Azure 批次的資源資料庫及工作，例如記錄事件的診斷記錄，然後再使用的診斷評估，和監視的 [記錄。 建立資料庫等事件，資料庫刪除、 任務的開始，任務完成，以及其他人會包含在批次診斷記錄。

>[AZURE.NOTE] 本文將告訴您本身批次帳戶資源的事件記錄不工作與工作輸出資料。 儲存您的工作和工作的輸出資料的詳細資訊，請參閱[保存 Azure 批次的工作和工作成果](batch-task-output.md)。

## <a name="prerequisites"></a>必要條件

* [Azure 批次帳戶](batch-account-create-portal.md)

* [Azure 儲存體帳戶](../storage/storage-create-storage-account.md#create-a-storage-account)

  若要保存批次診斷記錄，您必須建立 Azure 儲存體帳戶 Azure 會儲存記錄檔的位置。 指定此儲存帳戶當您以批次帳戶來[啟用診斷記錄功能](#enable-diagnostic-logging)。 當您啟用記錄集合時，您指定的儲存空間帳戶不相同的[應用程式套件](batch-application-packages.md)和[工作輸出持續](batch-task-output.md)文章中提及的連結的儲存空間帳戶。

  >[AZURE.WARNING] 您是**支付**Azure 儲存體帳戶中儲存的資料。 這包含本文所述診斷記錄。 設計您的[記錄檔保留原則](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md)時，請記住這點。

## <a name="enable-diagnostic-logging"></a>啟用診斷記錄

批次帳戶的預設不會啟用診斷記錄。 您必須明確啟用診斷記錄，針對您想要監視每個批次帳戶︰

[如何啟用診斷記錄的集合](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-diagnostic-logs)

我們建議您閱讀完整的[概觀 Azure 診斷記錄](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)文章，以瞭解的不只如何啟用記錄功能，但記錄類別支援各種不同的 Azure 服務。 Azure 批次，例如目前支援記錄類別︰**服務記錄檔**。

## <a name="service-logs"></a>服務記錄檔

Azure 批次服務記錄檔包含發出 Azure 批次服務批次資源資料庫，或任務的存留期間的事件。 每個批次所發出的事件會儲存在指定的儲存空間帳戶 JSON 格式。 例如，這是本文範例，**資料庫建立的事件**︰

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "4",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicated": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

每個事件本文位於.json 檔案中指定的 Azure 儲存體帳戶。 如果您想要直接存取記錄，您可能會想要檢閱[的診斷記錄在儲存帳戶的結構描述](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md#schema-of-diagnostic-logs-in-the-storage-account)。

## <a name="service-log-events"></a>服務記錄事件

批次服務目前發出下列服務記錄事件。 這份清單，可能不詳盡，因為其他事件可能已新增，自上次更新這份文件。

| **服務記錄事件** |
| ------------------ |
| [建立資料庫][pool_create] |
| [集區刪除開始][pool_delete_start] |
| [集區刪除完成][pool_delete_complete] |
| [資料庫大小調整開始][pool_resize_start] |
| [完成的資料庫調整大小][pool_resize_complete] |
| [任務的開始][task_start] |
| [完成的工作][task_complete] |
| [任務會失敗。][task_fail] |

## <a name="next-steps"></a>後續步驟

除了在 Azure 儲存體帳戶中儲存診斷記錄事件，您也可以串流到[Azure 事件中心](../event-hubs/event-hubs-what-is-event-hubs.md)，批次服務記錄事件，並傳送給[Azure 記錄分析](../log-analytics/log-analytics-overview.md)。

* [串流到事件 Azure 診斷記錄](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md)

  串流彈性的資料輸入服務事件集線器批次診斷事件。 事件集線器可以內嵌數百萬秒，然後您可以轉換，並儲存使用任何即時分析提供者的事件。

* [分析使用記錄分析 Azure 診斷記錄](../log-analytics/log-analytics-azure-storage-json.md)

  傳送記錄檔分析，您可以在此分析作業管理套件 (OMS) 入口網站中，或在 Power BI 或 Excel 中分析的匯出您診斷記錄。

[pool_create]: https://msdn.microsoft.com/library/azure/mt743615.aspx
[pool_delete_start]: https://msdn.microsoft.com/library/azure/mt743610.aspx
[pool_delete_complete]: https://msdn.microsoft.com/library/azure/mt743618.aspx
[pool_resize_start]: https://msdn.microsoft.com/library/azure/mt743609.aspx
[pool_resize_complete]: https://msdn.microsoft.com/library/azure/mt743608.aspx
[task_start]: https://msdn.microsoft.com/library/azure/mt743616.aspx
[task_complete]: https://msdn.microsoft.com/library/azure/mt743612.aspx
[task_fail]: https://msdn.microsoft.com/library/azure/mt743607.aspx
