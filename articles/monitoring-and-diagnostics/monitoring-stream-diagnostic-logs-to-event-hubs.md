<properties
    pageTitle="串流到事件 Azure 診斷記錄 |Microsoft Azure"
    description="瞭解如何將串流到事件的 Azure 診斷記錄。"
    authors="johnkemnetz"
    manager="rboucher"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="johnkem"/>

# <a name="stream-azure-diagnostic-logs-to-event-hubs"></a>串流到事件 Azure 診斷記錄

**[Azure 診斷記錄](monitoring-overview-of-diagnostic-logs.md)**可以使用內建的 [匯出至事件集線器 」 選項，在入口網站，或者啟用透過 Azure PowerShell Cmdlet 或 Azure CLI 診斷設定服務匯流排規則識別碼任何應用程式的近期即時資料流傳送。

## <a name="what-you-can-do-with-diagnostics-logs-and-event-hubs"></a>您可以執行診斷記錄與事件集線器
以下是一些您可能會用於診斷記錄中的資料流功能的方法︰

- **第 3 的廠商記錄與遙測系統串流記錄**– 一段時間，事件集線器串流會變成到第三方 SIEMs 管道您診斷記錄，記錄分析解決方案機制。

- **檢視服務健康狀況，以資料流中的 「 忙碌路徑 」 資料**– 使用事件集線器、 串流分析及中，您可以輕鬆地轉換為診斷資料附近即時獲得深入見解上 Azure 服務。 [文件本文件提供如何設定事件集線器，處理資料流分析的資料和使用中作為輸出的概觀](../stream-analytics/stream-analytics-power-bi-dashboard.md)。 以下是一些秘訣可以設定與診斷記錄︰
    - 當您核取入口網站中的選項，或透過 PowerShell 啟用，因此您要在服務匯流排命名空間名稱開頭為 「 深入見解-「 選取事件集線器時自動建立事件集線器分類的診斷記錄
    - 以下是您可以使用只要剖析所有記錄檔中的資料中表格範例資料流分析查詢︰

```
SELECT
records.ArrayValue.[Properties you want to track]
INTO
[OutputSourceName – the PowerBI source]
FROM
[InputSourceName] AS e
CROSS APPLY GetArrayElements(e.records) AS records
```

- **建立自訂的遙測與記錄平台**– 如果您已將自訂的遙測平台或只思考建置，彈性發行-訂閱性質事件集線器可讓您彈性內嵌診斷記錄。 [若要使用的全球遙測平台的事件集線器查看 Dan Rosanova 的指南](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)。

## <a name="enable-streaming-of-diagnostic-logs"></a>啟用串流的診斷記錄
您可以啟用串流的診斷記錄以程式設計方式，透過入口網站，或使用[Azure 監視器 REST API](https://msdn.microsoft.com/library/azure/dn931943.aspx)。 兩種方式，挑選服務匯流排命名空間和事件集線器會建立在您啟用每個記錄類別的命名空間。 診斷**記錄類別**是記錄的一種可能會收集資源。 您可以選取您想要在診斷刀 Azure 入口網站中的特定資源收集哪些記錄類別。

![入口網站中的記錄檔分類](./media/monitoring-stream-diagnostic-logs-to-event-hubs/log-categories.png)

> [AZURE.WARNING] 啟用與串流診斷記錄的計算資源 （例如，Vm 或服務布料的轉印圖樣）[需要一組不同的步驟](../event-hubs/event-hubs-streaming-azure-diags-data.md)。

### <a name="via-powershell-cmdlets"></a>透過 PowerShell Cmdlet
若要啟用透過[Azure PowerShell 指令程式](insights-powershell-samples.md)串流，您可以使用`Set-AzureRmDiagnosticSetting`指令程式，使用這些參數︰

```
Set-AzureRmDiagnosticSetting -ResourceId [your resource Id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
```

服務匯流排規則識別碼是使用此格式字串︰ `{service bus resource ID}/authorizationrules/{key name}`，例如， `/subscriptions/{subscription ID}/resourceGroups/Default-ServiceBus-WestUS/providers/Microsoft.ServiceBus/namespaces/{service bus namespace}/authorizationrules/RootManageSharedAccessKey`。


### <a name="via-azure-cli"></a>透過 Azure CLI
若要啟用透過[Azure CLI](insights-cli-samples.md)串流，您可以使用`insights diagnostic set`command 像這樣︰

```
azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
```

使用相同的格式服務匯流排規則識別碼，如下所述的 PowerShell 指令程式。

###<a name="via-azure-portal"></a>透過 Azure 入口網站
若要啟用串流透過 Azure 入口網站，請瀏覽至 [資源的診斷設定，選取 [匯出至事件中心 」。

![匯出至事件集線器，請在入口網站](./media/monitoring-stream-diagnostic-logs-to-event-hubs/portal-export.png)

若要設定讓它，選取 [現有的服務匯流排命名空間。 選取的命名空間都事件集線器建立 （如果這是您第一次串流診斷記錄檔） 或傳送至位置 （如果已經有串流到此命名空間該記錄類別的資源），及原則可定義串流機制具有權限。 現在，串流到事件集線器需要管理、 讀取及傳送的權限。 您可以建立或修改您的服務匯流排命名空間的 「 設定 」 索引標籤下方的傳統入口網站中的共用服務匯流排命名空間存取原則。 若要更新這些診斷設定之一，用戶端必須 ListKey 權限服務匯流排授權規則。

##<a name="how-do-i-consume-the-log-data-from-event-hubs"></a>如何使用記錄事件集線器資料？
以下是從事件集線器範例輸出資料︰

```
{
    "records": [
        {
            "time": "2016-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2016-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```

| 項目名稱 | 描述                                            |
|--------------|--------------------------------------------------------|
|記錄       | 此內容中的所有記錄事件的陣列。            |
|時間          | 事件發生的時間。                      |
|類別      | 此事件記錄檔類別。                           |
|預設    | 資源識別碼產生此事件的資源。 |
|operationName | 在作業名稱。                                 |
|層級         | 選用。 指出記錄事件層級。               |
|屬性    | 事件的內容。                               |


您可以檢視的所有資源提供者支援串流到事件集線器的[此處](monitoring-overview-of-diagnostic-logs.md)。

##<a name="next-steps"></a>後續步驟
- [閱讀更多關於 Azure 診斷記錄](monitoring-overview-of-diagnostic-logs.md)
- [開始使用事件集線器](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
