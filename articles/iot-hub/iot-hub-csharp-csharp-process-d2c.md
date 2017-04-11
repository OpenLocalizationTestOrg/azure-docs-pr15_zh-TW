<properties
    pageTitle="處理 IoT 中心裝置至雲端訊息 (.Net) |Microsoft Azure"
    description="請遵循此教學課程以瞭解實用模式處理 IoT 中心裝置至雲端的郵件。"
    services="iot-hub"
    documentationCenter=".net"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="csharp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="10/05/2016"
     ms.author="dobett"/>

# <a name="tutorial-how-to-process-iot-hub-device-to-cloud-messages-using-net"></a>教學課程︰ 如何使用.Net IoT 中心裝置至雲端郵件處理程序

[AZURE.INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

## <a name="introduction"></a>簡介

Azure IoT 中心完全受管理的服務，讓可靠，而安全數百萬 IoT 裝置與應用程式之間的雙向通訊後端。 其他教學課程 ([開始使用 IoT 中心]以及[雲端-裝置以傳送訊息 IoT 中心][lnk-c2d]) 示範如何使用裝置至雲端和雲端-裝置訊息的基本功能 IoT 中心。

本教學課程為基礎，在 [[快速入門 IoT 中心]教學課程中所顯示的程式碼，它會顯示兩個可調整模式您可以用來處理裝置至雲端的訊息︰

- 裝置至雲端中的郵件[Azure blob 儲存體]可靠的儲存空間。 常見的案例是*低溫路徑*分析，您可以在其中儲存遙測資料的使用狀況分析程序將輸入的二進位大型物件。 這些處理程序可能會受到例如[Azure 資料工廠]或[HDInsight (Hadoop)]堆疊的工具。

- 可靠處理*互動式*裝置至雲端的訊息。 立即觸發程序，針對一組中的應用程式後端的動作時，都能互動裝置至雲端的郵件。 例如，在裝置可能會傳送觸發票證插入 CRM 系統警示訊息。 相反地，*資料點*的郵件直接傳送到分析引擎。 例如，從儲存以供日後分析裝置的溫度遙測是資料點訊息。

因為 IoT 中心公開[事件中心][lnk-event-hubs]-相容的端點接收的郵件裝置至雲端，此教學課程使用[EventProcessorHost]執行個體。 此執行個體︰

* 安心地儲存 Azure blob 儲存體中的*資料點*的郵件。
* 轉寄*互動式*裝置至雲端 Azure[服務匯流排佇列中]立即處理郵件。

服務匯流排，有助於確保可靠處理互動式的訊息，因為它提供訊息每個檢查點及時間] 視窗為基礎的取消重複。

> [AZURE.NOTE] **EventProcessorHost**執行個體是只有一個方法可處理互動式的訊息。 其他選項包括[Azure 服務布料的轉印圖樣][lnk-service-fabric]和[Azure 資料流分析][lnk-stream-analytics]。

在本教學課程結尾，您可執行三個 Windows 主控台應用程式︰

* **SimulatedDevice**，應用程式的修改版本中建立[快速入門 IoT 中心]教學課程中，傳送資料點裝置至雲端郵件每個第二個，視覺效果及互動式裝置至雲端郵件每 10 秒。 此應用程式使用 AMQP 通訊協定與 IoT 中心通訊。
* **ProcessDeviceToCloudMessages**使用[EventProcessorHost]類別從事件中心相容端點擷取郵件。 然後可靠 Azure blob 儲存體，儲存資料點的訊息並轉寄至服務匯流排佇列互動式的郵件。
* **ProcessD2CInteractiveMessages**就能取消佇列中服務匯流排佇列中互動的郵件。

> [AZURE.NOTE] IoT 中心有許多裝置平台和語言，包括 C、 Java 和 JavaScript SDK 支援。 若要瞭解如何在本教學課程中的模擬的裝置取代實體裝置，以及如何裝置連接到 IoT 集線器，請參閱[Azure IoT 開發人員中心]。

本教學課程中會直接適用於使用事件中心相容的訊息，例如[HDInsight (Hadoop)]專案的其他方法。 如需詳細資訊，請參閱[Azure IoT 中心開發人員指南雲端的裝置]。

若要完成此教學課程中，您需要下列項目︰

+ Microsoft Visual Studio 2015。

+ 使用中的 Azure 帳戶。 <br/>如果您沒有帳戶，您可以建立一個[免費的帳戶](https://azure.microsoft.com/free/)，在幾分鐘。

您應該[Azure 儲存體]和[Azure 服務匯流排]的一些基本知識。


## <a name="send-interactive-messages-from-a-simulated-device"></a>從模擬的裝置傳送互動式的郵件

在此區段中，您可以修改您在傳送互動式的裝置至雲端郵件給 IoT 中心[快速入門 IoT 中心]教學課程中建立的模擬的裝置應用程式。

1. 在 Visual Studio 中，在**SimulatedDevice**專案中，新增下列方法**程式**類別。

    ```
    private static async void SendDeviceToCloudInteractiveMessagesAsync()
    {
      while (true)
      {
        var interactiveMessageString = "Alert message!";
        var interactiveMessage = new Message(Encoding.ASCII.GetBytes(interactiveMessageString));
        interactiveMessage.Properties["messageType"] = "interactive";
        interactiveMessage.MessageId = Guid.NewGuid().ToString();

        await deviceClient.SendEventAsync(interactiveMessage);
        Console.WriteLine("{0} > Sending interactive message: {1}", DateTime.Now, interactiveMessageString);

        Task.Delay(10000).Wait();
      }
    }
    ```

    這個方法是**SimulatedDevice**專案中的**SendDeviceToCloudMessagesAsync**方法類似。 僅限差異是，您現在設定**訊息**系統屬性，以及使用者屬性稱為**訊息類型**。
    程式碼會將全域唯一識別碼 (GUID) 指派給 [**訊息**] 屬性。 服務匯流排可以使用這個識別項，就能取消重複收到的郵件。 此範例使用**訊息類型**屬性來區分互動式的資料點的訊息。 應用程式傳遞這項資訊，在 [郵件內容，而不是郵件本文中，讓事件處理器不需要還原序列化執行郵件路由訊息。

    > [AZURE.NOTE] 請務必建立用來就能取消複製裝置程式碼中的互動式訊息的**訊息**。 間歇性網路通訊或其他失敗次數，可能會導致多個重新傳輸從該裝置相同的郵件。 您也可以使用語意訊息 ID，例如的相關訊息的資料欄位，取代 GUID 雜湊。

2. 新增下列方法，**主要**方法中的前`Console.ReadLine()`行︰

    ````
    SendDeviceToCloudInteractiveMessagesAsync();
    ````

    > [AZURE.NOTE] 為方便，此教學課程並未實作任何重試原則。 實際執行程式碼中，您應該實作重試原則，例如指數輪詢，建議[暫時性錯誤處理]MSDN 文章中。

## <a name="process-device-to-cloud-messages"></a>處理程序裝置至雲端的郵件

在此區段中，您可以建立程序 IoT 中樞的裝置至雲端來自 Windows 主控台應用程式。 Iot 中心公開 [事件中心]-相容的端點，若要啟用讀取裝置至雲端訊息應用程式。 本教學課程中使用[EventProcessorHost]類別處理這些主控台應用程式中的郵件。 如需有關如何從事件集線器處理郵件的詳細資訊，請參閱[開始使用事件集線器]教學課程。

當您執行可靠的儲存空間的資料點的郵件或互動式郵件轉寄該事件處理時的挑戰依賴訊息消費者提供檢查點的進度。 此外，當您從事件集線器讀取您應提供大型批次檢查點達成高的流量。 這個方法會建立重複處理的大量郵件如果失敗，而且您還原成先前的檢查點的可能性。 在本教學課程中，您可以瞭解如何**EventProcessorHost**檢查點與同步處理 Azure 儲存體寫入和服務匯流排取消重複視窗。

可靠 Azure 儲存體撰寫郵件，此範例使用[區塊 blob]的個別區塊認可功能[Azure Block Blobs]。 事件處理器累積記憶體中的郵件，直到提供檢查點的時間。 例如，累積的緩衝的郵件到達 4 MB 的最大的區塊大小或後服務匯流排取消重複經過時間範圍。 然後檢查點之前, 的程式碼認可 blob 新區塊。

事件處理器使用區塊識別碼事件集線器訊息位移。 這個機制可讓使用者執行取消重複檢查之前將其儲存空間，先測試區塊和檢查點之間可能當機的認可新事件處理器。

> [AZURE.NOTE] 本教學課程中使用單一 Azure 儲存體帳戶，撰寫從 IoT 中心擷取的所有郵件。 若要決定您是否需要您的方案中使用多個 Azure 儲存體帳戶，請參閱[Azure 儲存體延展性指導方針]。

應用程式使用服務匯流排取消重複功能來處理互動式的郵件時，避免重複的項目。 模擬的裝置有唯一的**訊息**與戳記每個互動的訊息。 這些識別碼會啟用服務匯流排，以確保，指定取消重複時間在視窗中，沒有兩個使用相同的**訊息**會傳送訊息給接收器。 此取消複製，與每個訊息完成語意提供的服務匯流排佇列中，讓您更容易實作可靠處理互動式的訊息。

若要確保任何訊息會重新送出取消重複視窗以外，程式碼同步處理服務匯流排佇列中取消重複視窗**EventProcessorHost**檢查點機制。 這項同步處理由每次取消重複視窗經過至少一次強制檢查點 （在此教學課程中，視窗為 1 小時）。

> [AZURE.NOTE] 本教學課程中使用單一分割的服務匯流排佇列互動式的所有郵件都擷取從 IoT 中心的程序。 如需有關如何使用服務匯流排佇列符合您的方案延展性需求的詳細資訊，請參閱[Azure 服務匯流排]文件。

### <a name="provision-an-azure-storage-account-and-a-service-bus-queue"></a>佈建 Azure 儲存體帳戶與服務匯流排佇列中
若要使用的[EventProcessorHost]類別，您必須啟用記錄檢查點資訊**EventProcessorHost** Azure 儲存體帳戶。 您可以使用現有的 Azure 儲存體帳戶，或遵循[相關 Azure 儲存體]中的指示，以建立一個新。 記下 Azure 儲存體帳戶的連線字串。

> [AZURE.NOTE] 當您複製並貼上 Azure 儲存體帳戶的連線字串時，請確定包含沒有空格。

您也必須啟用可靠處理互動式訊息的服務匯流排佇列。 您可以建立佇列以程式設計方式，在一小時取消重複視窗[如何使用服務匯流排佇列][服務匯流排佇列中]所述。 或者，您可以使用[Azure 傳統入口網站][lnk-classic-portal]，遵循下列步驟︰

1. 按一下左下角的 [**新增**]。 然後按一下 [**應用程式服務** > **服務匯流排** > **佇列中** > **建立自訂**。 輸入名稱**d2ctutorial**，選取一個區域中，並使用現有的命名空間或建立新。 在下一個頁面上，選取 [**啟用重複偵測**，並將**重複偵測記錄時間] 視窗中**的一個小時。 然後按一下 [儲存您的佇列中設定右下角的核取記號。

    ![建立佇列中 Azure 入口網站][30]

2. 在清單服務匯流排佇列中，按一下**d2ctutorial**，然後再按一下 [**設定**。 建立兩個共用的存取原則、 一個稱為**傳送**使用**傳送**的權限，以及一個稱為**聽取**以**聆聽**權限。 當您完成時，按一下 [底部的 [**儲存**]。

    ![設定佇列中 Azure 入口網站][31]

3. 按一下 [**儀表板**在上方，然後**連線資訊**] 下方。 記下這兩個連接字串。

    ![Azure 入口網站中的佇列中儀表板][32]

### <a name="create-the-event-processor"></a>建立事件處理器

1. 在目前的 Visual Studio 解決方案中，建立 Visual C# Windows 專案使用**主控台應用程式**專案範本，請按一下**檔案** > **新增** > **新的專案**。 請確定.NET Framework 版本是 4.5.1 或更新版本。 **ProcessDeviceToCloudMessages**，為專案的名稱，然後按一下**[確定**]。

    ![在 Visual Studio 中的新專案][10]

2. 在方案總管中**ProcessDeviceToCloudMessages**專案，以滑鼠右鍵按一下，然後再按一下 [**管理 NuGet 套件**。 [ **NuGet 封裝管理員**] 對話方塊隨即出現。

3. 搜尋**WindowsAzure.ServiceBus**、 按一下 [**安裝**]，並接受使用規定。 這項作業，下載、 安裝，並新增[Azure 服務匯流排 NuGet 套件](https://www.nuget.org/packages/WindowsAzure.ServiceBus)，所有相依性的參考。

4. 搜尋**Microsoft.Azure.ServiceBus.EventProcessorHost**、 按一下 [**安裝**]，並接受使用規定。 這項作業，下載、 安裝，並新增[Azure 服務匯流排事件中心 EventProcessorHost NuGet 套件](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost)，所有相依性的參考。

5. 以滑鼠右鍵按一下**ProcessDeviceToCloudMessages**專案，按一下 [**新增**]，再按一下**類別**。 命名新課程**StoreEventProcessor**，，然後再按一下 [建立類別的**[確定**]。

6. 在 StoreEventProcessor.cs 檔案頂端新增下列陳述式︰

    ```
    using System.IO;
    using System.Diagnostics;
    using System.Security.Cryptography;
    using Microsoft.ServiceBus.Messaging;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

7. 替代下列程式碼的類別︰

    ```
    class StoreEventProcessor : IEventProcessor
    {
      private const int MAX_BLOCK_SIZE = 4 * 1024 * 1024;
      public static string StorageConnectionString;
      public static string ServiceBusConnectionString;

      private CloudBlobClient blobClient;
      private CloudBlobContainer blobContainer;
      private QueueClient queueClient;

      private long currentBlockInitOffset;
      private MemoryStream toAppend = new MemoryStream(MAX_BLOCK_SIZE);

      private Stopwatch stopwatch;
      private TimeSpan MAX_CHECKPOINT_TIME = TimeSpan.FromHours(1);

      public StoreEventProcessor()
      {
        var storageAccount = CloudStorageAccount.Parse(StorageConnectionString);
        blobClient = storageAccount.CreateCloudBlobClient();
        blobContainer = blobClient.GetContainerReference("d2ctutorial");
        blobContainer.CreateIfNotExists();
        queueClient = QueueClient.CreateFromConnectionString(ServiceBusConnectionString);
      }

      Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
      {
        Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
        return Task.FromResult<object>(null);
      }

      Task IEventProcessor.OpenAsync(PartitionContext context)
      {
        Console.WriteLine("StoreEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);

        if (!long.TryParse(context.Lease.Offset, out currentBlockInitOffset))
        {
          currentBlockInitOffset = 0;
        }
        stopwatch = new Stopwatch();
        stopwatch.Start();

        return Task.FromResult<object>(null);
      }

      async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
      {
        foreach (EventData eventData in messages)
        {
          byte[] data = eventData.GetBytes();

          if (eventData.Properties.ContainsKey("messageType") && (string) eventData.Properties["messageType"] == "interactive")
          {
            var messageId = (string) eventData.SystemProperties["message-id"];

            var queueMessage = new BrokeredMessage(new MemoryStream(data));
            queueMessage.MessageId = messageId;
            queueMessage.Properties["messageType"] = "interactive";
            await queueClient.SendAsync(queueMessage);

            WriteHighlightedMessage(string.Format("Received interactive message: {0}", messageId));
            continue;
          }

          if (toAppend.Length + data.Length > MAX_BLOCK_SIZE || stopwatch.Elapsed > MAX_CHECKPOINT_TIME)
          {
            await AppendAndCheckpoint(context);
          }
          await toAppend.WriteAsync(data, 0, data.Length);

          Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
            context.Lease.PartitionId, Encoding.UTF8.GetString(data)));
        }
      }

      private async Task AppendAndCheckpoint(PartitionContext context)
      {
        var blockIdString = String.Format("startSeq:{0}", currentBlockInitOffset.ToString("0000000000000000000000000"));
        var blockId = Convert.ToBase64String(ASCIIEncoding.ASCII.GetBytes(blockIdString));
        toAppend.Seek(0, SeekOrigin.Begin);
        byte[] md5 = MD5.Create().ComputeHash(toAppend);
        toAppend.Seek(0, SeekOrigin.Begin);

        var blobName = String.Format("iothubd2c_{0}", context.Lease.PartitionId);
        var currentBlob = blobContainer.GetBlockBlobReference(blobName);

        if (await currentBlob.ExistsAsync())
        {
          await currentBlob.PutBlockAsync(blockId, toAppend, Convert.ToBase64String(md5));
          var blockList = await currentBlob.DownloadBlockListAsync();
          var newBlockList = new List<string>(blockList.Select(b => b.Name));

          if (newBlockList.Count() > 0 && newBlockList.Last() != blockId)
          {
            newBlockList.Add(blockId);
            WriteHighlightedMessage(String.Format("Appending block id: {0} to blob: {1}", blockIdString, currentBlob.Name));
          }
          else
          {
            WriteHighlightedMessage(String.Format("Overwriting block id: {0}", blockIdString));
          }
          await currentBlob.PutBlockListAsync(newBlockList);
        }
        else
        {
          await currentBlob.PutBlockAsync(blockId, toAppend, Convert.ToBase64String(md5));
          var newBlockList = new List<string>();
          newBlockList.Add(blockId);
          await currentBlob.PutBlockListAsync(newBlockList);

          WriteHighlightedMessage(String.Format("Created new blob", currentBlob.Name));
        }

        toAppend.Dispose();
        toAppend = new MemoryStream(MAX_BLOCK_SIZE);

        // checkpoint.
        await context.CheckpointAsync();
        WriteHighlightedMessage(String.Format("Checkpointed partition: {0}", context.Lease.PartitionId));

        currentBlockInitOffset = long.Parse(context.Lease.Offset);
        stopwatch.Restart();
      }

      private void WriteHighlightedMessage(string message)
      {
        Console.ForegroundColor = ConsoleColor.Yellow;
        Console.WriteLine(message);
        Console.ResetColor();
      }
    }
    ```

    **EventProcessorHost**類別呼叫此課程處理裝置至雲端從 IoT 中心收到的郵件。 本課程中的程式碼實作可靠儲存 blob 容器中的郵件和互動式將郵件轉寄到服務匯流排佇列中的邏輯。

    **OpenAsync**方法初始化**currentBlockInitOffset**變數，追蹤目前位移閱讀此事件處理器第一則訊息。 請記住，每個處理器會負責單一磁碟分割。

    **ProcessEventsAsync**方法 IoT] 中心內，從接收的郵件以批次，並處理它們，如下所示︰ 互動式郵件傳送至服務匯流排佇列中，及例子稱為**toAppend**記憶體緩衝資料點的訊息。 如果記憶體緩衝達到 4 MB 限制，或取消重複時間 windows 經過 （在此教學課程中檢查點之後的一小時），應用程式就會觸發檢查點。

    第一次， **AppendAndCheckpoint**方法會產生 blockId 要附加的區塊。 Azure 儲存體需要所有封鎖識別碼有相同的長度，此方法填補以前置零的位移`currentBlockInitOffset.ToString("0000000000000000000000000")`。 然後，如果此識別碼區塊位於 blob，方法會覆寫它的緩衝目前的內容。

    > [AZURE.NOTE] 若要簡化程式碼，本教學課程會使用每個資料分割的單一 blob 來儲存郵件。 實際的方案會實作循環超過一段時間，或當達到特定大小建立其他檔案的檔案。 請記住，封鎖 Azure blob 可以包含最 195 GB 的資料。

8. 在**程式**類別中，請在頂端新增下列**使用**陳述式︰

    ```
    using Microsoft.ServiceBus.Messaging;
    ```

9. 修改**程式**類別中的**主要**方法如下所示。 使用[快速入門 IoT 中心]教學課程的**iothubowner**連接字串來取代**{iot 中心連線字串}** 。 取代您記下這一節的開頭的連線字串中的儲存空間連線字串。 **傳送**佇列中名為**d2ctutorial**您記下這一節的開頭的權限取代服務匯流排連線字串︰

    ```
    static void Main(string[] args)
    {
      string iotHubConnectionString = "{iot hub connection string}";
      string iotHubD2cEndpoint = "messages/events";
      StoreEventProcessor.StorageConnectionString = "{storage connection string}";
      StoreEventProcessor.ServiceBusConnectionString = "{service bus send connection string}";

      string eventProcessorHostName = Guid.NewGuid().ToString();
      EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, iotHubD2cEndpoint, EventHubConsumerGroup.DefaultGroupName, iotHubConnectionString, StoreEventProcessor.StorageConnectionString, "messages-events");
      Console.WriteLine("Registering EventProcessor...");
      eventProcessorHost.RegisterEventProcessorAsync<StoreEventProcessor>().Wait();

      Console.WriteLine("Receiving. Press enter key to stop worker.");
      Console.ReadLine();
      eventProcessorHost.UnregisterEventProcessorAsync().Wait();
    }
    ```

    > [AZURE.NOTE] 為方便，本教學課程，請使用單一[EventProcessorHost]類別的執行個體。 如需詳細資訊，請參閱[事件集線器程式設計指南]。

## <a name="receive-interactive-messages"></a>接收互動式的郵件
在此區段中，您撰寫的互動式郵件接收服務匯流排佇列中的 Windows 主控台應用程式。 如需有關如何設計使用服務匯流排解決方案的詳細資訊，請參閱[建立多層應用程式與服務匯流排][]。

1. 在目前的 Visual Studio 解決方案中，使用**主控台應用程式**專案範本建立 Visual C# Windows 專案。 專案**ProcessD2CInteractiveMessages**的名稱。

2. 在方案總管中**ProcessD2CInteractiveMessages**專案，以滑鼠右鍵按一下，然後再按一下 [**管理 NuGet 套件**。 這項作業會顯示 [ **NuGet 套件管理員**] 視窗。

3. 搜尋**WindowsAzure.ServiceBus**、 按一下 [**安裝**]，並接受使用規定。 這項作業，下載、 安裝，並新增[Azure 服務匯流排](https://www.nuget.org/packages/WindowsAzure.ServiceBus)，所有相依性的參考。

4. 在**Program.cs**檔案頂端新增下列**使用**陳述式︰

    ```
    using System.IO;
    using Microsoft.ServiceBus.Messaging;
    ```

5. 最後，新增下列幾行**主要**方法。 替代**聆聽**名為**d2ctutorial**佇列中的權限的連線字串︰

    ```
    Console.WriteLine("Process D2C Interactive Messages app\n");

    string connectionString = "{service bus listen connection string}";
    QueueClient Client = QueueClient.CreateFromConnectionString(connectionString);

    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = false;
    options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

    Client.OnMessage((message) =>
    {
      try
      {
        var bodyStream = message.GetBody<Stream>();
        bodyStream.Position = 0;
        var bodyAsString = new StreamReader(bodyStream, Encoding.ASCII).ReadToEnd();

        Console.WriteLine("Received message: {0} messageId: {1}", bodyAsString, message.MessageId);

        message.Complete();
      }
      catch (Exception)
      {
        message.Abandon();
      }
    }, options);

    Console.WriteLine("Receiving interactive messages from SB queue...");
    Console.WriteLine("Press any key to exit.");
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>執行應用程式

現在您已經準備好執行應用程式。

1.  在方案總管] 中的 Visual Studio 中以滑鼠右鍵按一下您的方案，然後選取 [**設定啟動專案**。 選取**多個啟動專案**]，然後選取 [**開始**為**ProcessDeviceToCloudMessages** **SimulatedDevice**，與**ProcessD2CInteractiveMessages**專案的動作。

2.  若要開始的三個主控台應用程式按**F5** 。 **ProcessD2CInteractiveMessages**應用程式應該處理互動式**SimulatedDevice**應用程式從傳送的每封郵件。

  ![三個主控台應用程式][50]

> [AZURE.NOTE] 若要查看您 blob 的更新，您可能需要較小的值，例如**1024年**減少**MAX_BLOCK_SIZE**常數**StoreEventProcessor**類別中。 這項變更相當實用，因為需要花一些時間以達到區塊大小限制的模擬裝置所傳送的資料。 具有較小的區塊大小，您不需要等候這麼久的時間，查看 blob 被建立及更新。 不過，使用較大的封鎖可讓應用程式更具彈性。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您學到如何確實使用[EventProcessorHost]類別，以處理資料點及互動式裝置至雲端郵件。

[如何傳送郵件 IoT 中心雲端-裝置][lnk-c2d]示範如何傳送郵件給您的裝置，從您的後端。

若要完成使用 IoT 中樞的端對端解決方案的範例，請參閱[Azure IoT 套件][lnk-suite]。

若要進一步瞭解如何開發 IoT 中樞的方案，請參閱[IoT 中樞的開發人員指南]。

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png
[10]: ./media/iot-hub-csharp-csharp-process-d2c/create-identity-csharp1.png

[30]: ./media/iot-hub-csharp-csharp-process-d2c/createqueue2.png
[31]: ./media/iot-hub-csharp-csharp-process-d2c/createqueue3.png
[32]: ./media/iot-hub-csharp-csharp-process-d2c/createqueue4.png

<!-- Links -->

[Azure blob 儲存體]: ../storage/storage-dotnet-how-to-use-blobs.md
[Azure 資料工廠]: https://azure.microsoft.com/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/documentation/services/hdinsight/
[服務匯流排佇列中]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md

[Azure IoT 中心開發人員指南-雲端的裝置]: iot-hub-devguide-messaging.md

[Azure 儲存體]: https://azure.microsoft.com/documentation/services/storage/
[Azure 服務匯流排]: https://azure.microsoft.com/documentation/services/service-bus/

[IoT 中心開發人員指南]: iot-hub-devguide.md
[快速入門 IoT 中心]: iot-hub-csharp-csharp-getstarted.md
[Azure IoT 開發人員中心]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[暫時錯誤處理]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[關於 Azure 儲存體]: ../storage/storage-create-storage-account.md#create-a-storage-account
[開始使用事件集線器]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[Azure 儲存體延展性指導方針]: ../storage/storage-scalability-targets.md
[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[Event Hubs]: ../event-hubs/event-hubs-overview.md
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx
[事件集線器程式設計指南]: ../event-hubs/event-hubs-programming-guide.md
[暫時錯誤處理]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[建立多層應用程式與服務匯流排]: ../service-bus-messaging/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md

[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-c2d]: iot-hub-csharp-csharp-process-d2c.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
