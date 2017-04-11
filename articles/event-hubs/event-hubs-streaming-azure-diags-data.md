<properties
    pageTitle="使用事件集線器快速鍵 path 中的 Azure 診斷串流 |Microsoft Azure"
    description="說明如何設定 Azure 診斷事件集線器端點，包括常見案例的指引。"
    services="event-hubs"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags
    ms.service="event-hubs"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="07/14/2016"
    ms.author="sethm" />

# <a name="streaming-azure-diagnostics-data-in-the-hot-path-by-using-event-hubs"></a>使用事件集線器串流 Azure 診斷快速鍵 path 中的資料

Azure 診斷提供非常有彈性的方法，可從雲端服務虛擬機器 (Vm) 收集指標和記錄，並將結果轉移至 Azure 儲存體。 開始在 3 月 2016 (SDK 2.9) 時間範圍，您可以在簡報中接收診斷完全自訂資料來源並傳送忙碌路徑資料秒數，使用[Azure 事件集線器](https://azure.microsoft.com/services/event-hubs/)。

支援的資料類型，包括︰

- 事件追蹤 Windows (ETW) 事件
- 效能計數器
- Windows 事件記錄檔
- 應用程式的記錄
- Azure 診斷基礎結構的記錄

本文將示範如何使用事件集線器端對端設定 Azure 診斷。 下列常見的情況下也提供指導方針︰

- 如何將自訂的記錄及取得 sinked 到事件的指標
- 如何變更每個環境中的設定
- 若要檢視事件集線器串流資料的方式
- 如何疑難排解連線問題  

## <a name="prerequisites"></a>必要條件

接收 Azure 診斷中的事件集線器支援雲端服務、 Vm、 虛擬機器比例組和服務布料的轉印圖樣的 Visual Studio 啟動 Azure SDK 2.9 和對應的 Azure 工具。

- Azure 診斷副檔名 1.6 （[Azure SDK.NET 2.9 或更新版本](https://azure.microsoft.com/downloads/)的目標此預設）
- [Visual Studio 2013 或更新版本](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
- 現有使用*.wadcfgx*檔案及以下兩種方法的其中一個應用程式中的 Azure 診斷程式的設定︰
    - Visual Studio︰[設定診斷 Azure 雲端服務與虛擬機器](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)
    - Windows PowerShell︰[啟用診斷中使用 PowerShell 的 Azure 雲端服務](../cloud-services/cloud-services-diagnostics-powershell.md)
- 佈建後，每個文件，[開始使用事件集線器](./event-hubs-csharp-ephcs-getstarted.md)事件集線器命名空間

## <a name="connect-azure-diagnostics-to-event-hubs-sink"></a>Azure 診斷程式連線至事件集線器接收

Azure 診斷永遠接收記錄及指標，根據預設，Azure 儲存體帳戶。 應用程式可能此外到事件接收器**WadCfg**中的項目*.wadcfgx*檔**PublicConfig**區段中新增新的**接收**一節。 在 Visual Studio 中， *.wadcfgx*檔案會儲存在下列路徑︰**雲端服務專案** > **角色** >  **角色 （名稱）** > **diagnostics.wadcfgx**檔案。

```
<SinksConfig>
  <Sink name="HotPath">
    <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" />
  </Sink>
</SinksConfig>
```

在此範例中，事件中心 URL 設定為 [事件] 中心的完整命名空間︰ 事件集線器命名空間 + 「 / 」 + 事件中心名稱。  

事件中心 URL 會顯示在[Azure 入口網站](http://go.microsoft.com/fwlink/?LinkID=213885)上的事件集線器儀表板。  

**接收**名稱可以設定為任何有效的字串，只要在設定檔一致的方式使用相同的值。

> [AZURE.NOTE]  可能會有其他接收，例如*applicationInsights*設定此區段中。 Azure 診斷可讓每一個接收也宣告**PrivateConfig** ] 區段中定義的一或多個接收。  

事件集線器接收也會宣告及定義*.wadcfgx*設定檔的 [ **PrivateConfig** ] 區段中。

```
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <StorageAccount name="" key="" endpoint="" />
  <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="9B3SwghJOGEUvXigc6zHPInl2iYxrgsKHZoy4nm9CUI=" />
</PrivateConfig>
```

`SharedAccessKeyName`共用 Access 簽章 (SA) 鍵和**事件集線器**命名空間中定義的原則，必須符合的值。 瀏覽至[Azure 入口網站](https://manage.windowsazure.com)中的事件集線器儀表板，按一下 [**設定**] 索引標籤，並設定已*傳送*的權限的命名原則 (例如，「 SendRule 」)。 **StorageAccount**也會在**PrivateConfig**宣告。 有不需要變更以下的值，如果他們使用。 在此範例中，我們空白值，這是符號下游的資產將設定這些值。 例如， *ServiceConfiguration.Cloud.cscfg*環境設定檔設定環境適當的名稱與索引鍵。  

> [AZURE.WARNING] 事件集線器 SA 鍵會儲存於*.wadcfgx*檔案中的 [純文字。 通常，此按鍵簽入原始程式碼控制項，或已作為資產，在您建立的伺服器，因此您應該視保護。 我們建議使用 SA 鍵*只傳送*的權限，讓惡意使用者可以寫入 [事件] 中心內，但不是接聽或管理它。

## <a name="configure-azure-diagnostics-logs-and-metrics-to-sink-with-event-hubs"></a>設定 Azure 診斷記錄及事件集線器與接收的指標

先前所述，所有的預設與自訂診斷資料，也就是指標與記錄，是自動 sinked 至 Azure 儲存體中設定的時間間隔。 事件集線器及任何其他接收，您可以指定要與事件中樞 sinked 階層中的任何根或分葉節點。 這包含 ETW 事件與效能計數器、 Windows 事件記錄檔，應用程式的記錄。   

請務必考慮資料點數量實際轉移到事件集線器。 一般而言，開發人員傳輸必須 consumed 和解譯快速低延遲忙碌路徑資料。 監控通知] 或 [自動調整大小規則的系統是範例。 開發人員也可能會設定替代分析存放區，或搜尋市集-，例如 Azure 資料流分析、 Elasticsearch、 自訂的監控系統或其他人的最愛監控系統。

以下是一些範例設定。

```
<PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
  <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
</PerformanceCounters>
```

在下列範例中，將接收會套用至在階層中，所有的子**PerformanceCounters**便會傳送到事件集線器這表示的父**PerformanceCounters**節點。  

```
<PerformanceCounters scheduledTransferPeriod="PT1M">
  <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" sinks="HotPath" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" sinks="HotPath"/>
  <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" sinks="HotPath"/>
</PerformanceCounters>
```

在上一個範例中，只有三個計數器套用接收︰**要求佇列****拒絕要求**，與**%處理器時間**。  

下列範例顯示開發人員可以列印文件的限制，請為適用於此服務健康狀況的要徑計量傳送資料量。  

```
<Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
```

在此範例中，接收會套用至記錄，且已篩選，只錯誤層級的追蹤。

## <a name="deploy-and-update-a-cloud-services-application-and-diagnostics-config"></a>部署及更新雲端服務應用程式和診斷設定

Visual Studio 提供部署的應用程式及事件集線器接收設定最簡單的路徑。 若要檢視和編輯檔案，Visual Studio 中開啟*.wadcfgx*檔案、 加以編輯，然後將其儲存。 路徑是**雲端服務專案** > **角色** > **角色 （名稱）** > **diagnostics.wadcfgx**。  

此時，所有的部署和部署更新動作，在 Visual Studio、 Visual Studio 小組系統，及所有命令或指令碼根據 MSBuild 並使用**/t︰ 發佈**目標包裝程序中包含*.wadcfgx* 。 此外，部署和更新部署檔案至 Azure 上您的 Vm 使用適當的 Azure 診斷代理程式副檔名。

部署的應用程式和 Azure 診斷設定之後，您會立即看到 [事件] 中心的儀表板中的活動。 這表示您準備好要移到您所選擇的接聽用戶端或分析工具中檢視忙碌路徑資料。  

在下圖中，[事件集線器儀表板會顯示健全傳送至 [事件] 中心診斷資料某個之後 11 PM 開始。 這是何時應用程式部署與更新的*.wadcfgx*檔案，和接收已正確設定。

![][0]  

> [AZURE.NOTE] 當您更新 Azure 診斷設定檔 (.wadcfgx) 時，建議整個應用程式，以及設定推更新，使用 Visual Studio 發佈或 Windows PowerShell 指令碼。  

## <a name="view-hot-path-data"></a>檢視忙碌路徑資料

如先前所述，有許多的使用情況下，為接聽和處理事件集線器資料。

一個簡單的方法是，建立 [事件] 中心聆聽及列印輸出資料流小測試主控台應用程式。 您可以將下列程式碼，[開始使用事件集線器](./event-hubs-csharp-ephcs-getstarted.md)更多詳細資料所述），主控台應用程式中。  

請注意，主控台應用程式必須包含[事件處理器主機 Nuget 套件](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/)。  

請記得要以方括弧**主要**函數中的值取代為您的資源的值。   

```
//Console application code for EventHub test client
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace EventHubListener
{
    class SimpleEventProcessor : IEventProcessor
    {
        Stopwatch checkpointStopWatch;

        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
            if (reason == CloseReason.Shutdown)
            {
                await context.CheckpointAsync();
            }
        }

        Task IEventProcessor.OpenAsync(PartitionContext context)
        {
            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
            this.checkpointStopWatch = new Stopwatch();
            this.checkpointStopWatch.Start();
            return Task.FromResult<object>(null);
        }

        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (EventData eventData in messages)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                    Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                        context.Lease.PartitionId, data));

                foreach (var x in eventData.Properties)
                {
                    Console.WriteLine(string.Format("    {0} = {1}", x.Key, x.Value));
                }
            }

            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string eventHubConnectionString = "Endpoint= <your connection string>”
            string eventHubName = "<Event Hub name>";
            string storageAccountName = "<Storage account name>";
            string storageAccountKey = "<Storage account key>”;
            string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);

            string eventProcessorHostName = Guid.NewGuid().ToString();
            EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
            Console.WriteLine("Registering EventProcessor...");
            var options = new EventProcessorOptions();
            options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
            eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();

            Console.WriteLine("Receiving. Press enter key to stop worker.");
            Console.ReadLine();
            eventProcessorHost.UnregisterEventProcessorAsync().Wait();
        }
    }
}
```

## <a name="troubleshoot-event-hubs-sink"></a>疑難排解事件集線器接收

- [事件] 中心不會顯示預期的內送或外寄事件活動。

    檢查您的事件集散地成功佈建後。 *.Wadcfgx* **PrivateConfig**一節中的所有連線資訊必須都符合您所見入口網站中的資源的值。 請確定您有定義 SA 原則 (在此範例中為 「 SendRule 」)，在入口網站及*傳送*的權限授與。  

- 更新後、 [事件] 中心不會再顯示送或外寄事件活動。

    首先，請確定 [事件] 中心] 和 [組態資訊是正確的先前所述。 有時**PrivateConfig**重設部署更新。 建議的修正是進行*.wadcfgx*專案中的所有變更，然後推播完整的應用程式更新。 如果不可行，請確定診斷更新推入包含 SA 鍵完成**PrivateConfig** 。  

- 我試著建議，及 [事件] 中心仍然無法運作。

    請嘗試 Azure 儲存體表格中含有記錄與錯誤的 Azure 診斷本身︰ **WADDiagnosticInfrastructureLogsTable**。 其中一個選項是連線到此儲存帳戶檢視此表格中，並在過去 24 小時的時間戳記中新增查詢中使用的工具，例如[Azure 儲存檔案總管](http://www.storageexplorer.com)。 若要匯出的.csv 檔案，並在應用程式，例如 Microsoft Excel 中開啟，您可以使用的工具。 Excel 可讓您輕鬆地搜尋電話卡字串，例如**EventHubs**，若要查看哪些錯誤報告。  

## <a name="next-steps"></a>後續步驟

•[深入瞭解事件集線器](https://azure.microsoft.com/services/event-hubs/)

## <a name="appendix-complete-azure-diagnostics-configuration-file-wadcfgx-example"></a>附錄︰ 完成 Azure 診斷設定檔 (.wadcfgx) 範例

```
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
        <Directories scheduledTransferPeriod="PT1M">
          <IISLogs containerName="wad-iis-logfiles" />
          <FailedRequestLogs containerName="wad-failedrequestlogs" />
        </Directories>
        <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
        </PerformanceCounters>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*" />
        </WindowsEventLog>
        <CrashDumps>
          <CrashDumpConfiguration processName="WaIISHost.exe" />
          <CrashDumpConfiguration processName="WaWorkerHost.exe" />
          <CrashDumpConfiguration processName="w3wp.exe" />
        </CrashDumps>
        <Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
      </DiagnosticMonitorConfiguration>
      <SinksConfig>
        <Sink name="HotPath">
          <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" />
        </Sink>
        <Sink name="applicationInsights">
          <ApplicationInsights />
          <Channels>
            <Channel logLevel="Error" name="errors" />
          </Channels>
        </Sink>
      </SinksConfig>
    </WadCfg>
    <StorageAccount />
  </PublicConfig>
  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <StorageAccount name="" key="" endpoint="" />
    <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" SharedAccessKey="YOUR_KEY_HERE" />
  </PrivateConfig>
  <IsEnabled>true</IsEnabled>
</DiagnosticsConfiguration>
```

此範例中的互補*ServiceConfiguration.Cloud.cscfg*類似下列所示。

```
<?xml version="1.0" encoding="utf-8"?>
<ServiceConfiguration serviceName="MyFixItCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2015-04.2.6">
  <Role name="MyFixIt.WorkerRole">
    <Instances count="1" />
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="YOUR_CONNECTION_STRING" />
    </ConfigurationSettings>
  </Role>
</ServiceConfiguration>
```

<!-- Images. -->
[0]: ./media/event-hubs-streaming-azure-diags-data/dashboard.png
