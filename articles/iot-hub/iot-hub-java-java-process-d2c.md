<properties
    pageTitle="處理 IoT 中心裝置至雲端訊息 (Java) |Microsoft Azure"
    description="遵循此 Java 教學課程以瞭解實用模式處理 IoT 中心裝置至雲端的郵件。"
    services="iot-hub"
    documentationCenter="java"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="java"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/01/2016"
     ms.author="dobett"/>

# <a name="tutorial-how-to-process-iot-hub-device-to-cloud-messages-using-java"></a>教學課程︰ 如何使用 Java IoT 中心裝置至雲端郵件處理程序

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

在本教學課程結尾，您可執行三個 Java 主控台應用程式︰

* **模擬裝置**的[快速入門 IoT 中心]教學課程中建立的應用程式修改版本傳送資料點裝置至雲端郵件每個第二個，視覺效果及互動式裝置至雲端訊息每 10 秒。 此應用程式使用 AMQP 通訊協定與 IoT 中心通訊。
* **處理 d2c 訊息**會從事件中心相容端點擷取郵件使用[EventProcessorHost]類別。 然後可靠的方式將資料點的郵件儲存在 Azure blob 儲存體，並轉寄至服務匯流排佇列互動式的郵件。
* **處理互動式訊息**就能取消佇列中服務匯流排佇列中互動的郵件。

> [AZURE.NOTE] IoT 中心有許多裝置平台和語言，包括 C、 Java 和 JavaScript SDK 支援。 如需如何在本教學課程中的模擬的裝置取代實體裝置，以及如何連線到 IoT 集線器的裝置上的指示，請參閱[Azure IoT 開發人員中心]。

本教學課程中會直接適用於使用事件中心相容的訊息，例如[HDInsight (Hadoop)]專案的其他方法。 如需詳細資訊，請參閱[Azure IoT 中心開發人員指南雲端的裝置]。

若要完成此教學課程中，您需要下列項目︰

+ [快速入門 IoT 中心]教學課程的完整工作版本。

+ Java SE 8。 <br/> [準備您的開發環境][lnk-dev-setup]說明如何安裝 Windows 或 Linux Java 本教學課程。

+ Maven 3。  <br/> [準備您的開發環境][lnk-dev-setup]說明如何安裝 Windows 或 Linux Maven 本教學課程。

+ 使用中的 Azure 帳戶。 <br/>如果您沒有帳戶，您可以建立一個[免費的帳戶](https://azure.microsoft.com/free/)，在幾分鐘。

您應該[Azure 儲存體]和[Azure 服務匯流排]的一些基本知識。


## <a name="send-interactive-messages-from-a-simulated-device"></a>從模擬的裝置傳送互動式的郵件

在此區段中，您可以修改您在傳送互動式的裝置至雲端郵件給 IoT 中心[快速入門 IoT 中心]教學課程中建立的模擬的裝置應用程式。

1. 若要開啟 simulated-device\src\main\java\com\mycompany\app\App.java 檔案中使用文字編輯器。 此檔案包含您在[開始使用 IoT 中心]教學課程中建立的**模擬裝置**應用程式的程式碼。

2. 新增下列巢狀的類別至**應用程式**類別︰

    ```
    private static class InteractiveMessageSender implements Runnable {
      public void run() {
        try {
          while (true) {
            String msgStr = "Alert message!";
            Message msg = new Message(msgStr);
            msg.setMessageId(java.util.UUID.randomUUID().toString());
            msg.setProperty("messageType", "interactive");
            System.out.println("Sending interactive message: " + msgStr);

            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);

            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(10000);
          }
        } catch (InterruptedException e) {
          System.out.println("Finished sending interactive messages.");
        }
      }
    }
    ```

    這個類別是**MessageSender**類別**模擬裝置**專案中類似。 僅限差異是，您現在設定**訊息**系統屬性，以及呼叫**訊息類型**的自訂屬性。
    程式碼會將全域唯一識別碼 (UUID) 指派給 [**訊息**] 屬性。 服務匯流排可以使用這個識別項，就能取消重複收到的郵件。 此範例使用**訊息類型**屬性來區分互動式的資料點的郵件。 應用程式傳遞這項資訊，在 [郵件內容，而不是郵件本文中，讓事件處理器不需要還原序列化執行郵件路由訊息。

    > [AZURE.NOTE] 請務必建立用來就能取消複製裝置程式碼中的互動式訊息的**訊息**。 間歇性網路通訊或其他失敗次數，可能會導致多個重新傳輸從該裝置相同的郵件。 您也可以使用語意訊息 ID，例如的相關訊息的資料欄位，取代 UUID 雜湊。

3. 修改**主要**的方法，將傳送兩種互動式的訊息，並資料點的郵件程式碼片段之下列所示︰

    ````
    MessageSender sender = new MessageSender();
    InteractiveMessageSender interactiveSender = new InteractiveMessageSender();

    ExecutorService executor = Executors.newFixedThreadPool(2);
    executor.execute(sender);
    executor.execute(interactiveSender);
    ````

4. 儲存並關閉 simulated-device\src\main\java\com\mycompany\app\App.java 檔案。

    > [AZURE.NOTE] 為方便，此教學課程並未實作任何重試原則。 實際執行程式碼中，您應該實作重試原則，例如指數輪詢，建議[暫時性錯誤處理]MSDN 文章中。

5. 若要建立使用 Maven 的**模擬裝置**應用程式，執行下列命令在命令提示字元中模擬裝置資料夾︰

    ```
    mvn clean package -DskipTests
    ```

## <a name="process-device-to-cloud-messages"></a>處理程序裝置至雲端的郵件

在此區段中，您可以建立程序 IoT 中樞的裝置至雲端來自 Java 主控台應用程式。 Iot 中心公開 [事件中心]-相容的端點，若要啟用讀取裝置至雲端訊息應用程式。 本教學課程中使用[EventProcessorHost]類別處理這些主控台應用程式中的郵件。 如需有關如何從事件集線器處理郵件的詳細資訊，請參閱[開始使用事件集線器]教學課程。

當您實作可靠的儲存空間的資料點的郵件或事件處理依賴訊息消費者提供進度檢查點轉接、 互動式訊息的是主要挑戰。 此外，當您從事件集線器讀取您應提供大型批次檢查點達成高的流量。 這個方法會建立重複處理的大量郵件，如果失敗，而且您還原成先前的檢查點的可能性。 在本教學課程中，您可以瞭解如何**EventProcessorHost**檢查點與同步處理 Azure 儲存體寫入和服務匯流排取消重複視窗。

可靠 Azure 儲存體撰寫郵件，此範例使用[區塊 blob]的個別區塊認可功能[Azure Block Blobs]。 事件處理器累積記憶體中的郵件，直到提供檢查點的時間。 例如，累積的緩衝的郵件到達 4 MB 的最大的區塊大小或後服務匯流排取消重複經過時間範圍。 然後檢查點之前, 的程式碼認可 blob 新區塊。

事件處理器使用區塊識別碼事件集線器訊息位移。 這個機制可讓使用者執行取消重複檢查之前將其儲存空間，先測試區塊和檢查點之間可能當機的認可新事件處理器。

> [AZURE.NOTE] 本教學課程中使用單一 Azure 儲存體帳戶，撰寫從 IoT 中心擷取的所有郵件。 若要決定您是否需要您的方案中使用多個 Azure 儲存體帳戶，請參閱[Azure 儲存體延展性指導方針]。

應用程式使用服務匯流排取消重複功能來處理互動式的郵件時，避免重複的項目。 模擬的裝置有唯一的**訊息**與戳記每個互動的訊息。 此識別碼服務匯流排，以確保，指定取消重複時間在視窗中，沒有兩個使用相同的**訊息**會傳送訊息給接收器。 此取消複製，與每個訊息完成語意提供的服務匯流排佇列中，讓您更容易實作可靠處理互動式的訊息。

若要確保任何訊息會重新送出取消重複視窗以外，程式碼同步處理服務匯流排佇列中取消重複視窗**EventProcessorHost**檢查點機制。 這項同步處理由每次取消重複視窗經過至少一次強制檢查點 （在此教學課程中，視窗為 1 小時）。

> [AZURE.NOTE] 本教學課程中使用單一分割的服務匯流排佇列互動式的所有郵件都擷取從 IoT 中心的程序。 如需有關如何使用服務匯流排佇列符合您的方案延展性需求的詳細資訊，請參閱[Azure 服務匯流排]文件。

### <a name="provision-an-azure-storage-account-and-a-service-bus-queue"></a>佈建 Azure 儲存體帳戶與服務匯流排佇列中

若要使用的[EventProcessorHost]類別，您必須啟用記錄檢查點資訊**EventProcessorHost** Azure 儲存體帳戶。 您可以使用現有的 Azure 儲存體帳戶，或遵循[相關 Azure 儲存體]中的指示，以建立一個新。 記下 Azure 儲存體帳戶的連線字串。

> [AZURE.NOTE] 當您複製並貼上 Azure 儲存體帳戶的連線字串時，請確定包含沒有空格。

您也必須啟用可靠處理互動式訊息的服務匯流排佇列。 您可以建立佇列以程式設計方式，在一小時取消重複視窗[如何使用服務匯流排佇列][服務匯流排佇列中]所述。 或者，您可以使用[Azure 傳統入口網站][lnk-classic-portal]，遵循下列步驟︰

1. 按一下左下角的 [**新增**]。 然後按一下 [**應用程式服務** > **服務匯流排** > **佇列中** > **建立自訂**。 輸入名稱**d2ctutorial**，選取一個區域中，並使用現有的命名空間或建立新。 記下命名空間名稱，您需要更新版本在本教學課程。 在下一個頁面上，選取 [**啟用重複偵測**，並將**重複偵測記錄時間] 視窗中**的一個小時。 然後按一下 [儲存您的佇列中設定右下角的核取記號。

    ![建立佇列中 Azure 入口網站][30]

2. 在清單服務匯流排佇列中，按一下**d2ctutorial**，然後再按一下 [**設定**。 建立兩個共用的存取原則、 一個稱為**傳送**使用**傳送**的權限，以及一個稱為**聽取**以**聆聽**權限。 記下這兩個原則的**主索引鍵**值，您需要進行稍後在本教學課程。 當您完成時，按一下 [底部的 [**儲存**]。

    ![設定佇列中 Azure 入口網站][31]

### <a name="create-the-event-processor"></a>建立事件處理器

在此區段中，您可以建立 Java 應用程式處理郵件從事件中心相容端點。

第一個工作可新增稱為**處理 d2c 訊息**接收來自 IoT 中心事件中心相容端點的裝置至雲端訊息，並將這些郵件路由到其他後端服務 Maven 專案。

1. 在您在[開始使用 IoT 中心]教學課程中建立 iot java-取得-啟動的資料夾中建立 Maven 專案稱為**處理 d2c 訊息**使用下列命令，在命令提示字元 」。 請注意，這是單一、 完整的命令︰

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=process-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. 在命令提示字元中，瀏覽至新的處理 d2c 郵件資料夾。

3. 使用文字編輯器，開啟 pom.xml 檔案處理 d2c 郵件資料夾中，新增下列相依性的**相依性**節點。 這些相依性，讓您使用 azure eventhubs azure eventhubs eph，與 azure servicebus 封包應用程式中互動 IoT 中心與服務匯流排佇列中︰

    ```
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-eventhubs</artifactId>
      <version>0.8.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-eventhubs-eph</artifactId>
      <version>0.8.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-servicebus</artifactId>
      <version>0.9.4</version>
    </dependency>
    ```

4. 儲存並關閉 pom.xml 檔案。

下一步是**ErrorNotificationHandler**類別新增至專案。

1. 使用文字編輯器建立 process-d2c-messages\src\main\java\com\mycompany\app\ErrorNotificationHandler.java 檔案。 將下列程式碼新增至 [要顯示從**EventProcesssorHost**執行個體的錯誤訊息的檔案︰

    ```
    package com.mycompany.app;

    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;

    public class ErrorNotificationHandler implements
        Consumer<ExceptionReceivedEventArgs> {
      @Override
      public void accept(ExceptionReceivedEventArgs t) {
        System.out.println("EventProcessorHost: Host " + t.getHostname()
            + " received general error notification during " + t.getAction() + ": "
            + t.getException().toString());
      }
    }
    ```

2. 儲存並關閉 ErrorNotificationHandler.java 檔案。

現在您可以新增實作**IEventProcessor**介面的類別。 **EventProcessorHost**類別呼叫此課程處理裝置至雲端從 IoT 中心收到的郵件。 本課程中的程式碼實作可靠儲存 blob 容器中的郵件和互動式將郵件轉寄到服務匯流排佇列中的邏輯。

**OnEvents**方法設定**latestEventData**變數追蹤 offset 及順序閱讀此事件處理器最新訊息數。 請記住，每個處理器會負責單一磁碟分割。 **OnEvents**方法然後 IoT] 中心內，從接收的郵件以批次，並處理它們，如下所示︰ 互動式郵件傳送至服務匯流排佇列中，及例子**toAppend**記憶體緩衝資料點的訊息。 如果記憶體緩衝達到 4 MB 封鎖限制，或取消重複時間 windows 經過 （在此教學課程中的最後一個檢查點之後的一小時），然後方法會觸發檢查點。

**AppendAndCheckPoint**方法第一次產生的區塊將附加至 blob **blockId** 。 Azure 儲存體需要所有封鎖識別碼有相同的長度，此方法填補有前置零的位移。 然後，如果此識別碼區塊位於 blob，方法會覆寫它與目前的緩衝內容。

> [AZURE.NOTE] 若要簡化程式碼，本教學課程會使用每個資料分割的單一 blob 來儲存郵件。 實際的方案會實作循環超過一段時間，或當達到特定大小建立其他檔案的檔案。 請記住，封鎖 Azure blob 可以包含最 195 GB 的資料。

下一步是實作**IEventProcessor**介面︰

1. 使用文字編輯器建立 process-d2c-messages\src\main\java\com\mycompany\app\EventProcessor.java 檔案。

2. 新增下列匯入及類別定義 EventProcessor.java 檔案。 **EventProcessor**類別實作**IEventProcessor**介面定義事件集線器用戶端的行為︰

    ```
    package com.mycompany.app;

    import java.io.ByteArrayInputStream;
    import java.io.ByteArrayOutputStream;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.nio.charset.StandardCharsets;
    import java.time.Duration;
    import java.time.Instant;
    import java.util.ArrayList;
    import java.util.Base64;
    import java.util.concurrent.ExecutionException;

    import com.microsoft.azure.eventhubs.EventData;
    import com.microsoft.azure.eventprocessorhost.*;
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;
    import com.microsoft.windowsazure.services.servicebus.*;
    import com.microsoft.windowsazure.services.servicebus.models.BrokeredMessage;

    public class EventProcessor implements IEventProcessor {

    }
    ```

3. 新增下列方法實作**IEventProcessor**介面**EventProcessor**類別︰

    ```
    @Override
    public void onOpen(PartitionContext context) throws Exception {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " is opening");
    }

    @Override
    public void onClose(PartitionContext context, CloseReason reason)
        throws Exception {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " is closing for reason "
          + reason.toString());
    }

    @Override
    public void onError(PartitionContext context, Throwable error) {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " onError: " + error.toString());
    }

    @Override
    public void onEvents(PartitionContext context, Iterable<EventData> messages)
        throws Exception {
    }
    ```

4. 新增下列類別層級變數**EventProcessor**類別︰

    ```
    public static CloudBlobContainer blobContainer;
    public static ServiceBusContract serviceBusContract;

    // Use a smaller MAX_BLOCK_SIZE value to test.
    final private int MAX_BLOCK_SIZE = 4 * 1024 * 1024;
    final private Duration MAX_CHECKPOINT_TIME = Duration.ofHours(1);

    private ByteArrayOutputStream toAppend = new ByteArrayOutputStream(
        MAX_BLOCK_SIZE);
    private Instant start = Instant.now();
    private EventData latestEventData;
    ```

5. 新增下列簽章的**AppendAndCheckPoint**方法**EventProcessor**類別︰

    ```
    private void AppendAndCheckPoint(PartitionContext context)
      throws URISyntaxException, StorageException, IOException,
      IllegalArgumentException, InterruptedException, ExecutionException {
    }
    ```

6. 新增下列程式碼來擷取目前訊息 offset 及順序中的數字的磁碟分割**AppendAndCheckPoint**方法︰

    ```
    String currentOffset = latestEventData.getSystemProperties().getOffset();
    Long currentSequence = latestEventData.getSystemProperties().getSequenceNumber();
    System.out
        .printf(
            "\nAppendAndCheckPoint using partition: %s, offset: %s, sequence: %s\n",
            context.getPartitionId(), currentOffset, currentSequence);
    ```

7. 在**AppendAndCheckPoint**的方法，使用目前的位移的值以建立下一個區塊儲存至 blob **BlockEntry**執行個體︰

    ```
    Long blockId = Long.parseLong(currentOffset);
    String blockIdString = String.format("startSeq:%1$025d", blockId);
    String encodedBlockId = Base64.getEncoder().encodeToString(
        blockIdString.getBytes(StandardCharsets.US_ASCII));
    BlockEntry block = new BlockEntry(encodedBlockId);
    ```

8. 在**AppendAndCheckPoint**方法上, 傳至區塊 blob 的最新的一組郵件，擷取目前的封鎖清單︰

    ```
    String blobName = String.format("iothubd2c_%s", context.getPartitionId());
    CloudBlockBlob currentBlob = blobContainer.getBlockBlobReference(blobName);

    currentBlob.uploadBlock(block.getId(),
        new ByteArrayInputStream(toAppend.toByteArray()), toAppend.size());
    ArrayList<BlockEntry> blockList = currentBlob.downloadBlockList();
    ```

9. 在**AppendAndCheckPoint**方法中，您可以建立的初始區塊中的新 blob，或附加至現有的 blob 的 [封鎖︰

    ```
    if (currentBlob.exists()) {
      // Check if we should append new block or overwrite existing block
      BlockEntry last = blockList.get(blockList.size() - 1);
      if (blockList.size() > 0 && !last.getId().equals(block.getId())) {
        System.out.printf("Appending block %s to blob %s\n", blockId, blobName);
        blockList.add(block);
      } else {
        System.out.printf("Overwriting block %s in blob %s\n", blockId,
            blobName);
      }
    } else {
      System.out.printf("Creating initial block %s in new blob: %s\n", blockId,
          blobName);
      blockList.add(block);
    }
    currentBlob.commitBlockList(blockList);
    ```

10. 最後，在**AppendAndCheckPoint**方法中，在分割建立檢查點並準備儲存郵件的下一個區塊︰

    ```
    context.checkpoint(latestEventData);

    // Reset everything after the checkpoint.
    toAppend.reset();
    start = Instant.now();
    System.out.printf("Checkpointed on partition id: %s\n",
        context.getPartitionId());
    ```

11. 在**onEvents**方法中，新增下列程式碼服務匯流排佇列接收訊息的 IoT 中心結束點及轉寄互動式的郵件。 封鎖已滿或逾時，然後呼叫**AppendAndCheckPoint**方法︰

    ```
    if (messages != null) {
      for (EventData eventData : messages) {
        latestEventData = eventData;
        byte[] data = eventData.getBody();
        if (eventData.getProperties().containsKey("messageType")
            && eventData.getProperties().get("messageType")
                .equals("interactive")) {
          String messageId = (String) eventData.getSystemProperties().get(
              "message-id");
          BrokeredMessage message = new BrokeredMessage(data);
          message.setMessageId(messageId);
          serviceBusContract.sendQueueMessage("d2ctutorial", message);
          continue;
        }
        if (toAppend.size() + data.length > MAX_BLOCK_SIZE
            || Duration.between(start, Instant.now()).compareTo(
                MAX_CHECKPOINT_TIME) > 0) {
          AppendAndCheckPoint(context);
        }
        toAppend.write(data);
      }
    }
    ```

12. 最後， **onEvents**方法中新增 「 else if 」 子句撥打**AppendAndCheckPoint**如果逾時沒有來自 IoT 中樞的郵件︰

    ```
    else if ((toAppend.size() > 0)
        && Duration.between(start, Instant.now())
            .compareTo(MAX_CHECKPOINT_TIME) > 0) {
      AppendAndCheckPoint(context);
    }
    ```

13. 儲存 EventProcessor.java 檔案的變更。

**處理 d2c 訊息**專案中的最後一個工作是將程式碼新增至**主**方法會產生**EventProcessorHost**執行個體。

1. 若要開啟 process-d2c-messages\src\main\java\com\mycompany\app\App.java 檔案中使用文字編輯器。

2. 新增下列**匯入**陳述式的檔案︰

    ```
    import com.microsoft.azure.eventprocessorhost.*;
    import com.microsoft.azure.servicebus.ConnectionStringBuilder;
    import com.microsoft.azure.storage.CloudStorageAccount;
    import com.microsoft.azure.storage.StorageException;
    import com.microsoft.azure.storage.blob.CloudBlobClient;
    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.services.servicebus.ServiceBusConfiguration;
    import com.microsoft.windowsazure.services.servicebus.ServiceBusService;

    import java.net.URISyntaxException;
    import java.security.InvalidKeyException;
    import java.util.concurrent.*;
    ```

3. 在**應用程式**類別新增下列類別層級的變數。 **{Yourstorageaccountconnectionstring}**取代您所做的筆記先前[佈建 Azure 儲存體帳戶與服務匯流排佇列](#provision-an-azure-storage-account-and-a-service-bus-queue)] 區段中的 Azure 儲存體帳戶連線字串︰

    ```
    private final static String storageConnectionString = "{yourstorageaccountconnectionstring}";
    ```

4. 將下列類別層級變數新增至**應用程式**類別，與您的服務匯流排命名空間**{yourservicebussendkey}** **{yourservicebusnamespace}**取代您的佇列**傳送**鍵。 您先前記下的命名空間和**接聽**鍵值[佈建 Azure 儲存體帳戶與服務匯流排佇列](#provision-an-azure-storage-account-and-a-service-bus-queue)] 區段中︰

    ```
    private final static String serviceBusNamespace = "{yourservicebusnamespace}";
    private final static String serviceBusSasKeyName = "send";
    private final static String serviceBusSASKey = "{yourservicebussendkey}";
    private final static String serviceBusRootUri = ".servicebus.windows.net";
    ```

5. 新增下列類別層級變數**應用程式**類別。 **{Youreventhubcompatibleendpoint}**取代事件中心相容端點值。 端點值看起來像**他...命名空間**，請移除**sb: / /**首碼] 和 [ **.servicebus.windows.net/**後置字元。 **{Youreventhubcompatiblename}**取代中心相容事件名稱。 **{Youriothubkey}**取代**iothubowner**鍵。 您記下這些值在[建立 IoT 中心][ lnk-create-an-iot-hub] ] 區段中*開始使用 Azure IoT 中心 java*教學課程︰

    ```
    private final static String consumerGroupName = "$Default";
    private final static String namespaceName = "{youreventhubcompatibleendpoint}";
    private final static String eventHubName = "{youreventhubcompatiblename}";
    private final static String sasKeyName = "iothubowner";
    private final static String sasKey = "{youriothubkey}";
    ```

6. 修改簽章的**主要**的方法如下所示︰

    ```
    public static void main(String args[]) throws InvalidKeyException,
      URISyntaxException, StorageException {
    }
    ```

7. 將下列程式碼新增至 [取得 blob 容器儲存郵件的參考的**主要**方式︰

    ```
    System.out.println("Process D2C messages using EventProcessorHost");
    CloudStorageAccount account = CloudStorageAccount
        .parse(storageConnectionString);
    CloudBlobClient client = account.createCloudBlobClient();
    EventProcessor.blobContainer = client
        .getContainerReference("d2cjavatutorial");
    EventProcessor.blobContainer.createIfNotExists();
    ```

8. 將下列程式碼新增至**主要**的方法，以取得服務匯流排服務的參考︰

    ```
    Configuration config = ServiceBusConfiguration
        .configureWithSASAuthentication(serviceBusNamespace,
            serviceBusSasKeyName, serviceBusSASKey, serviceBusRootUri);
    EventProcessor.serviceBusContract = ServiceBusService.create(config);
    ```

9. 在**主要**的方法，設定並建立**EventProcessorHost**執行個體。 **SetInvokeProcessorAfterReceiveTimeout**選項可確保沒有處理的郵件時，即使**EventProcessorHost**執行個體叫用**IEventProcessor**介面**onEvents**方法。 **OnEvents**方法然後一律叫用**AppendAndCheckPoint**方法逾時。

    ```
    ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder(
        namespaceName, eventHubName, sasKeyName, sasKey);
    EventProcessorHost host = new EventProcessorHost(eventHubName,
        consumerGroupName, eventHubConnectionString.toString(),
        storageConnectionString);
    EventProcessorOptions options = new EventProcessorOptions();
    options.setExceptionNotification(new ErrorNotificationHandler());
    options.setInvokeProcessorAfterReceiveTimeout(true);
    ```

10. 在**主要**的方法，登錄**IEventProcessor**實作**EventProcessorHost**執行個體︰

    ```
    try {
      System.out.println("Registering host named " + host.getHostName());
      host.registerEventProcessor(EventProcessor.class, options).get();
    } catch (Exception e) {
      System.out.print("Failure while registering: ");
      if (e instanceof ExecutionException) {
        Throwable inner = e.getCause();
        System.out.println(inner.toString());
      } else {
        System.out.println(e.toString());
      }
      System.out.println(e.toString());
    }
    ```

11. 最後，請將邏輯新增至關閉**EventProcessorHost**執行個體的**主要**方式︰

    ```
    System.out.println("Press enter to stop");
    try {
      System.in.read();
      host.unregisterEventProcessor();

      System.out.println("Calling forceExecutorShutdown");
      EventProcessorHost.forceExecutorShutdown(120);
    } catch (Exception e) {
      System.out.println(e.toString());
      e.printStackTrace();
    }

    System.out.println("End of sample");
    ```

12. 儲存並關閉 process-d2c-messages\src\main\java\com\mycompany\app\App.java 檔案。

13. 若要建立使用 Maven**處理 d2c 訊息**應用程式，執行下列命令在命令提示字元中處理 d2c 郵件資料夾︰

    ```
    mvn clean package -DskipTests
    ```

## <a name="receive-interactive-messages"></a>接收互動式的郵件

在此區段中，您撰寫的互動式郵件接收服務匯流排佇列中的 Java 主控台應用程式。

第一個任務是新增 Maven 專案稱為**程序互動式郵件**接收郵件的寄件者服務匯流排佇列**EventProcessor**執行個體。

1. 在您在[開始使用 IoT 中心]教學課程中建立 iot java-取得-啟動的資料夾中建立 Maven 專案稱為**程序互動式郵件**使用下列命令，在命令提示字元 」。 請注意，這是單一、 完整的命令︰

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=process-interactive-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. 在命令提示字元中，瀏覽至新的程序互動式郵件資料夾。

3. 使用文字編輯器，開啟 pom.xml 檔案，在 [程序互動式郵件] 資料夾和**相依性**節點中加入下列相依性。 此相依性可讓您應用程式中使用 azure servicebus 套件與您的服務匯流排佇列互動︰

    ```
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-servicebus</artifactId>
      <version>0.9.4</version>
    </dependency>
    ```

4. 儲存並關閉 pom.xml 檔案。

下一步是新增程式碼，從服務匯流排佇列中擷取的郵件。

1. 若要開啟 process-interactive-messages\src\main\java\com\mycompany\app\App.java 檔案中使用文字編輯器。

2. 新增下列`import`陳述式的檔案︰

    ```
    import java.io.IOException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;

    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.exception.ServiceException;
    import com.microsoft.windowsazure.services.servicebus.*;
    import com.microsoft.windowsazure.services.servicebus.models.*;
    ```

3. 將下列類別層級的變數新增至**應用程式**類別，與您的服務匯流排命名空間**{yourservicebuslistenkey}** **{yourservicebusnamespace}**取代您的佇列**聆聽**鍵。 您先前記下的命名空間和**接聽**鍵值[佈建 Azure 儲存體帳戶與服務匯流排佇列](#provision-an-azure-storage-account-and-a-service-bus-queue)] 區段中︰

    ```
    private final static String serviceBusNamespace = "{yourservicebusnamespace}";
    private final static String serviceBusSasKeyName = "listen";
    private final static String serviceBusSASKey = "{yourservicebuslistenkey}";
    private final static String serviceBusRootUri = ".servicebus.windows.net";
    private final static String queueName = "d2ctutorial";
    private static ServiceBusContract service = null;
    ```

4. 若要從佇列接收郵件**應用程式**類別中新增下列巢狀的類別︰

    ```
    private static class MessageReceiver implements Runnable {
      public void run() {
        ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
        try {
          while (true) {
            ReceiveQueueMessageResult resultQM = service.receiveQueueMessage(
                queueName, opts);
            BrokeredMessage message = resultQM.getValue();
            if (message != null && message.getMessageId() != null) {
              System.out.println("MessageID: " + message.getMessageId());
              System.out.print("From queue: ");
              byte[] b = new byte[200];
              String s = null;
              int numRead = message.getBody().read(b);
              while (-1 != numRead) {
                s = new String(b);
                s = s.trim();
                System.out.print(s);
                numRead = message.getBody().read(b);
              }
              System.out.println();
            } else {
              Thread.sleep(1000);
            }
          }
        } catch (InterruptedException e) {
          System.out.println("Finished.");
        } catch (ServiceException e) {
          System.out.println("ServiceException: " + e.getMessage());
        } catch (IOException e) {
          System.out.println("IOException: " + e.getMessage());
        }
      }
    }
    ```

5. 修改簽章的**主要**的方法如下所示︰

    ```
    public static void main(String args[]) throws ServiceException, IOException {
    }
    ```

6. 在**主**方法中，新增下列開始接聽新郵件︰

    ```
    System.out.println("Process interactive messages");

    Configuration config = ServiceBusConfiguration
        .configureWithSASAuthentication(serviceBusNamespace,
            serviceBusSasKeyName, serviceBusSASKey, serviceBusRootUri);
    service = ServiceBusService.create(config);

    MessageReceiver receiver = new MessageReceiver();

    ExecutorService executor = Executors.newFixedThreadPool(2);
    executor.execute(receiver);

    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    ```

7. 儲存並關閉 process-interactive-messages\src\main\java\com\mycompany\app\App.java 檔案。

8. 若要建立使用 Maven 的**程序互動式郵件**應用程式，執行下列命令在命令提示字元程序互動式郵件] 資料夾中︰

    ```
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>執行應用程式

現在您已經準備好執行三個應用程式。

1.  若要執行的**程序互動式郵件**應用程式，在命令提示字元或命令介面中瀏覽至 [處理程序互動式郵件] 資料夾，然後執行下列命令︰

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![執行程序互動式郵件][processinteractive]

2.  若要執行的**處理 d2c 訊息**應用程式，在命令提示字元或命令介面瀏覽至處理 d2c 郵件資料夾，然後執行下列命令︰

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![執行處理 d2c 訊息][processd2c]

3.  若要執行**模擬裝置**應用程式，在命令提示字元或命令介面瀏覽至模擬裝置資料夾，然後執行下列命令︰

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![執行模擬裝置][simulateddevice]

> [AZURE.NOTE] 若要查看您 blob 的更新，您可能需要較小的值，例如**1024年**減少**MAX_BLOCK_SIZE**常數**StoreEventProcessor**類別中。 這項變更相當實用，因為需要花一些時間以達到區塊大小限制的模擬裝置所傳送的資料。 具有較小的區塊大小，您不需要等候這麼久的時間，查看 blob 被建立及更新。 不過，使用較大的封鎖可讓應用程式更具彈性。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您學到如何安心地處理資料點以及互動式裝置至雲端訊息使用[EventProcessorHost]類別。

[如何傳送郵件 IoT 中心雲端-裝置][lnk-c2d]示範如何傳送郵件給您的裝置，從您的後端。

若要完成使用 IoT 中樞的端對端解決方案的範例，請參閱[Azure IoT 套件][lnk-suite]。

若要進一步瞭解如何開發 IoT 中樞的方案，請參閱[IoT 中樞的開發人員指南]。

<!-- Images. -->
[simulateddevice]: ./media/iot-hub-java-java-process-d2c/runsimulateddevice.png
[processinteractive]: ./media/iot-hub-java-java-process-d2c/runprocessinteractive.png
[processd2c]: ./media/iot-hub-java-java-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-java-java-process-d2c/createqueue2.png
[31]: ./media/iot-hub-java-java-process-d2c/createqueue3.png

<!-- Links -->

[Azure blob 儲存體]: ../storage/storage-dotnet-how-to-use-blobs.md
[Azure 資料工廠]: https://azure.microsoft.com/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/documentation/services/hdinsight/
[服務匯流排佇列中]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md

[Azure IoT 中心開發人員指南-雲端的裝置]: iot-hub-devguide-messaging.md

[Azure 儲存體]: https://azure.microsoft.com/documentation/services/storage/
[Azure 服務匯流排]: https://azure.microsoft.com/documentation/services/service-bus/

[IoT 中心開發人員指南]: iot-hub-devguide.md
[快速入門 IoT 中心]: iot-hub-java-java-getstarted.md
[Azure IoT 開發人員中心]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[暫時錯誤處理]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[關於 Azure 儲存體]: ../storage/storage-create-storage-account.md#create-a-storage-account
[開始使用事件集線器]: ../event-hubs/event-hubs-java-ephjava-getstarted.md
[Azure 儲存體延展性指導方針]: ../storage/storage-scalability-targets.md
[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[Event Hubs]: ../event-hubs/event-hubs-overview.md
[EventProcessorHost]: https://github.com/Azure/azure-event-hubs/tree/master/java/azure-eventhubs-eph
[暫時錯誤處理]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-c2d]: iot-hub-java-java-process-d2c.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[lnk-create-an-iot-hub]: iot-hub-java-java-getstarted.md#create-an-iot-hub