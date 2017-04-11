<properties 
    pageTitle="什麼是 Azure WebJobs SDK" 
    description="Azure WebJobs SDK 簡介。 說明 SDK 是什麼，一般案例並相當實用，且程式碼範例。" 
    services="app-service\web, storage" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/01/2016" 
    ms.author="tdykstra"/>

# <a name="what-is-the-azure-webjobs-sdk"></a>什麼是 Azure WebJobs SDK

## <a id="overview"></a>概觀

本文會說明 WebJobs SDK，檢閱一些常見的案例實用，並提供的程式碼中您使用的方式概觀。

[WebJobs](websites-webjobs-resources.md)是 Azure 應用程式服務可讓您在相同的內容為 web 應用程式、 API 應用程式或行動應用程式中執行的程式或指令碼的功能。 [WebJobs SDK](websites-webjobs-resources.md)的目的，是以簡化 WebJob 可以執行，例如圖像處理、 佇列處理撰寫的一般工作的程式碼、 RSS 彙總、 檔案維護及傳送電子郵件。 WebJobs SDK 有使用 Azure 儲存體和服務匯流排的、 排程的工作，以及處理錯誤，以及其他許多常見的案例的內建功能。 此外，其具有專為可延伸。 [WebJobs SDK 是開啟的來源](https://github.com/Azure/azure-webjobs-sdk/)，且未[開啟的擴充的來源存放庫](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview)。

WebJobs SDK 包含下列元件︰

* **NuGet 套件**。 Visual Studio 主控台應用程式專案中加入的 NuGet 封包提供了架構的程式碼使用修飾您 WebJobs SDK 其屬性的方法。
  
* **儀表板**。 WebJobs SDK 的組件所包含的 Azure 應用程式服務，並使用 NuGet 套件的程式提供豐富的監控和診斷。 您不需要撰寫程式碼，使用這些監控和診斷功能。

## <a id="scenarios"></a>案例

以下是一些您可以更輕鬆地處理與 Azure WebJobs SDK 的一般案例︰

* 圖像處理或其他需要大量 CPU 的工作。 一般功能是網站的上傳圖像或視訊的能力。 通常您想要管理其內容之後上傳，但不想讓使用者等待時執行這項作業。

* 佇列中處理程序。 使用佇列可進行通訊的後端服務 web frontend 的常用方式。 當網站需要完成的工作時，其推入到佇列中的郵件。 後端服務會提取來自佇列中的郵件和工作。 您可以使用圖像處理佇列:，例如使用者上傳的檔案數字後，將檔案名稱以收取的後端處理的佇列中訊息。 您也可以使用佇列改善網站回應能力。 例如，而不是直接寫入 SQL 資料庫，撰寫佇列中，以告知您完成時的使用者，並讓後端服務控點高延遲關聯式資料庫運作。 使用圖像程序正在處理的佇列中的範例，請參閱[WebJobs SDK 開始教學課程](websites-dotnet-webjobs-sdk-get-started.md)。

* RSS 彙總。 如果您有維護的 RSS 摘要清單的網站，您可以拉中所有的摘要背景程序中的文章。

* 檔案進行的維修作業，例如彙總或清除 [記錄檔。  您可能有多個網站或針對您想要合併才能在其上執行分析作業跨越另一個時間建立的記錄檔。 或您可能會想要工作排程執行每週清理舊記錄檔。

* 輸入成 Azure 資料表。 您可能已儲存的檔案和二進位大型物件，並且要剖析它們儲存在資料表中的資料。 輸入函數無法撰寫的多個資料列 （在某些情況下百萬），然後 WebJobs SDK 可以讓您輕鬆地實作這項功能。 SDK 也會提供即時監視進度標記，例如撰寫資料表中的資料列數目。

* 您想要的背景執行緒，例如[傳送電子郵件](https://github.com/victorhurdugaci/AzureWebJobsSamples/tree/master/SendEmailOnFailure)中執行的其他長時間執行工作。 

* 您想要處理的排程，例如執行備份作業每天晚上執行任何工作。

有許多種情況可能會想要不按比例縮放的 web 應用程式，在多個 Vm，會同時執行多個 WebJobs 上執行。 在某些情況下這可能會導致相同的資料快速處理多次，但這不是當您使用內建的佇列中 blob，與服務匯流排引動程序 WebJobs SDK 的問題。 SDK 可確保您函數將會一次處理針對每個訊息或 blob。

WebJobs SDK 也可讓您輕鬆地處理常見的錯誤處理案例。 您可以設定提醒，函數會失敗，並讓如果它不會在指定的時間限制內完成，函數會自動取消，您可以設定逾時傳送通知。

## <a id="code"></a>程式碼範例

處理使用 Azure 儲存體的一般工作的程式碼很簡單。 在主控台應用程式的`Main`您建立的方法`JobHost`協調呼叫方法您撰寫的物件。 WebJobs SDK 架構知道何時撥打您的方法，使用參數值根據您使用的 WebJobs SDK 屬性。 SDK 提供*引動程序*所指定條件造成被稱為函數和*文件夾*指定如何取得出方法參數的資訊。

例如， [QueueTrigger](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)屬性會造成時收到的郵件上佇列中，並將郵件格式是 JSON 位元組陣列或自訂類型，如果郵件是自動還原序列化呼叫函數。 在 Azure 儲存體帳戶中建立新的 blob 時， [BlobTrigger](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)屬性會觸發程序。

以下是簡單的程式輪詢佇列中，建立 blob 每個佇列中收到的訊息︰

        public static void Main()
        {
            JobHost host = new JobHost();
            host.RunAndBlock();
        }

        public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")] string inputText, 
            [Blob("containername/blobname")]TextWriter writer)
        {
            writer.WriteLine(inputText);
        }

`JobHost`物件是一組背景函數的容器。 `JobHost`物件監視函數監看式的事件的觸發程序，並會觸發事件發生時所執行的函數。 電話`JobHost`方法來指定是否要在目前的執行緒或背景執行緒上執行的容器程序。 在範例中，`RunAndBlock`方法執行程序持續目前執行緒上。

因為`ProcessQueueMessage`在此範例中的方法有`QueueTrigger`屬性，觸發程序的該函數會在接收到的新佇列中的郵件。 `JobHost`物件會檢查指定佇列中 (在此範例中為 「 webjobsqueue 」) 上的新佇列中郵件，並找到一個時，它會呼叫`ProcessQueueMessage`。 

`QueueTrigger`屬性繫結`inputText`參數佇列中郵件的值。 與`Blob`屬性繫結`TextWriter`blob 名為 「 blobname 」 名為 「 containername 」 容器中的物件。  

        public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")]] string inputText, 
            [Blob("containername/blobname")]TextWriter writer)

然後，函數會使用這些參數來寫入 blob 佇列中郵件的值︰

        writer.WriteLine(inputText);

WebJobs SDK 的觸發程序並文件夾功能大幅簡化您撰寫程式碼。 所需處理佇列]、 [二進位大型物件或 [檔案]，或啟動排程的任務，低功率程式碼是為您 WebJobs SDK 架構。 例如，架構建立不存在，會開啟的佇列佇列中讀取佇列郵件、 刪除佇列郵件，當您處理完成後，會建立不存在的 blob 容器尚未，寫入 blob，依此類推。

下列範例會顯示各種不同的觸發程序中一個 WebJob: `QueueTrigger`， `FileTrigger`， `WebHookTrigger`，及`ErrorTrigger`。 

```
    public class Functions
    {
        public static void ProcessQueueMessage([QueueTrigger("queue")] string message,
        TextWriter log)
        {
            log.WriteLine(message);
        }

        public static void ProcessFileAndUploadToBlob(
            [FileTrigger(@"import\{name}", "*.*", autoDelete: true)] Stream file,
            [Blob(@"processed/{name}", FileAccess.Write)] Stream output,
            string name,
            TextWriter log)
        {
            output = file;
            file.Close();
            log.WriteLine(string.Format("Processed input file '{0}'!", name));
        }

        [Singleton]
        public static void ProcessWebHookA([WebHookTrigger] string body, TextWriter log)
        {
            log.WriteLine(string.Format("WebHookA invoked! Body: {0}", body));
        }

        public static void ProcessGitHubWebHook([WebHookTrigger] string body, TextWriter log)
        {
            dynamic issueEvent = JObject.Parse(body);
            log.WriteLine(string.Format("GitHub WebHook invoked! ('{0}', '{1}')",
                issueEvent.issue.title, issueEvent.action));
        }

        public static void ErrorMonitor(
        [ErrorTrigger("00:01:00", 1)] TraceFilter filter, TextWriter log,
        [SendGrid(
            To = "admin@emailaddress.com",
            Subject = "Error!")]
         SendGridMessage message)
        {
            // log last 5 detailed errors to the Dashboard
            log.WriteLine(filter.GetDetailedMessage(5));
            message.Text = filter.GetDetailedMessage(1);
        }
    }
```

## <a id="schedule"></a>排程

`TimerTrigger`屬性可讓您執行的排程的觸發程序函數。 您可以透過 Azure 或排程個別的函數，使用 WebJobs SDK WebJob 的整個排程 WebJob `TimerTrigger`。 以下是程式碼範例。

```
public class Functions
{
    public static void ProcessTimer([TimerTrigger("*/15 * * * * *", RunOnStartup = true)]
    TimerInfo info, [Queue("queue")] out string message)
    {
        message = info.FormatNextOccurrences(1);
    }
}
```

如需更多範例程式，請參閱 GitHub.com [TimerSamples.cs](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/ExtensionsSample/Samples/TimerSamples.cs) azure webjobs-sdk 副檔名儲存機制。

## <a name="extensibility"></a>擴充功能

您不會限制您為內建功能，WebJobs SDK 可讓您撰寫自訂引動程序和文件夾。  例如，您可以撰寫快取事件和定期排程的觸發程序。 [開啟來源存放庫](https://github.com/Azure/azure-webjobs-sdk-extensions)包含[在 WebJobs SDK 擴充功能的詳細的指南](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview)和範例的程式碼，以協助您開始撰寫文件夾自己引動程序。

## <a id="workerrole"></a>使用外部 WebJobs WebJobs SDK

使用的程式--WebJobs SDK 是標準的主控台應用程式，可以執行任何位置，沒有為 WebJob 執行。 開發在電腦上，在本機測試程式，在您可以執行在雲端服務工作者角色或 Windows 服務如果您喜歡這些環境。 

不過，儀表板僅適用於 Azure 應用程式服務 web 應用程式的副檔名為。 如果您想要執行 WebJob 以外，仍然可以使用儀表板，您可以設定 web 應用程式使用相同的儲存空間帳戶是指您 WebJobs SDK 儀表板的連接字串，並且 web 應用程式 WebJobs 儀表板] 就會顯示有關函數執行您執行的其他位置的程式中的資料。 您可以使用 URL https://*{webappname}*.scm.azurewebsites.net/azurejobs/#/functions，以前往儀表板。 如需詳細資訊，請參閱[開始使用 WebJobs SDK 本機開發的儀表板](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx)，但請注意部落格文章顯示舊的連接字串名稱。 

## <a id="nostorage"></a>儀表板功能

即使您不是使用 WebJobs SDK 引動程序或文件夾 WebJobs SDK 會提供幾項優點︰

* 您可以叫用函數從儀表板。
* 您可以重新執行儀表板中的函數。
* 您可以檢視記錄檔中的儀表板，連結至特定的 WebJob （應用程式記錄檔，撰寫使用 Console.Out、 Console.Error、 追蹤等），或是連結至呼叫產生的特定函數 (使用寫入記錄檔`TextWriter`SDK 傳遞給函數做為參數的物件)。 

如需詳細資訊，請參閱[如何手動叫用函數](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#manual)，以及[如何寫入記錄檔](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs) 

## <a id="nextsteps"></a>後續步驟

如需有關 WebJobs SDK 的詳細資訊，請參閱[Azure WebJobs 建議資源](http://go.microsoft.com/fwlink/?linkid=390226)。

最新的增強功能 WebJobs sdk 的變更的相關資訊，請參閱[版本資訊](https://github.com/Azure/azure-webjobs-sdk/wiki/Release-Notes)。
 
