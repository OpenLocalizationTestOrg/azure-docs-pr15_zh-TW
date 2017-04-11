## <a name="receive-messages-with-eventprocessorhost-in-java"></a>Java 中收到郵件 EventProcessorHost

EventProcessorHost 簡化接收事件來管理持續檢查點事件集線器 Java 類別，而平行會接收來自這些事件集線器。 使用 EventProcessorHost 可以分割事件跨多個接收器，即使裝載於不同的節點。 此範例顯示如何使用單一收 EventProcessorHost。

###<a name="create-a-storage-account"></a>建立儲存的帳戶

若要使用 EventProcessorHost，您必須擁有[Azure 儲存體帳戶][]︰

1. 登入至[Azure 傳統入口網站][]，並按一下 [**新增**] 畫面的底部。

2. 按一下 [**資料服務**]，然後**儲存空間**，然後**快速建立**，，然後輸入您儲存帳戶的名稱。 選取您想要的區域]，然後按一下 [**建立儲存帳戶**。

    ![][11]

3. 按一下 [建立新的儲存空間帳戶，然後按一下 [**管理便捷鍵**︰

    ![][12]

    複製稍後在本教學課程中使用主要便捷鍵。

###<a name="create-a-java-project-using-the-eventprocessor-host"></a>建立使用 EventProcessor 主機 Java 專案

事件集線器 Java 用戶端文件庫是可供使用，從[Maven 中央存放庫]Maven 專案[Maven Package]，並可使用下列的相依性宣告內 Maven 專案檔案參考︰    

``` XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>{VERSION}</version>
</dependency>
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs-eph</artifactId>
    <version>{VERSION}</version>
</dependency>
```
 
不同類型的 [建置環境中，您可以明確地取得最新發行的 JAR 檔案從[Maven 中央存放庫][Maven Package]或[GitHub 的發行版本分配點](https://github.com/Azure/azure-event-hubs/releases)。  

1. 下列範例中，首先您最愛的 Java 開發環境中建立新 Maven 專案主控台/命令介面應用程式。 將稱為類別```ErrorNotificationHandler```。     

    ``` Java
    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;

    public class ErrorNotificationHandler implements Consumer<ExceptionReceivedEventArgs>
    {
        @Override
        public void accept(ExceptionReceivedEventArgs t)
        {
            System.out.println("SAMPLE: Host " + t.getHostname() + " received general error notification during " + t.getAction() + ": " + t.getException().toString());
        }
    }
    ```

2. 使用下列代碼建立新類別名為```EventProcessor```。

    ```Java
    import com.microsoft.azure.eventhubs.EventData;
    import com.microsoft.azure.eventprocessorhost.CloseReason;
    import com.microsoft.azure.eventprocessorhost.IEventProcessor;
    import com.microsoft.azure.eventprocessorhost.PartitionContext;

    public class EventProcessor implements IEventProcessor
    {
        private int checkpointBatchingCount = 0;

        @Override
        public void onOpen(PartitionContext context) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is opening");
        }

        @Override
        public void onClose(PartitionContext context, CloseReason reason) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is closing for reason " + reason.toString());
        }
        
        @Override
        public void onError(PartitionContext context, Throwable error)
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " onError: " + error.toString());
        }

        @Override
        public void onEvents(PartitionContext context, Iterable<EventData> messages) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " got message batch");
            int messageCount = 0;
            for (EventData data : messages)
            {
                System.out.println("SAMPLE (" + context.getPartitionId() + "," + data.getSystemProperties().getOffset() + "," +
                        data.getSystemProperties().getSequenceNumber() + "): " + new String(data.getBody(), "UTF8"));
                messageCount++;
                
                this.checkpointBatchingCount++;
                if ((checkpointBatchingCount % 5) == 0)
                {
                    System.out.println("SAMPLE: Partition " + context.getPartitionId() + " checkpointing at " +
                        data.getSystemProperties().getOffset() + "," + data.getSystemProperties().getSequenceNumber());
                    context.checkpoint(data);
                }
            }
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " batch size was " + messageCount + " for host " + context.getOwner());
        }
    }
    ```

3. 建立一個名為最終類別```EventProcessorSample```，使用下列代碼。

    ```Java
    import com.microsoft.azure.eventprocessorhost.*;
    import com.microsoft.azure.servicebus.ConnectionStringBuilder;
    import com.microsoft.azure.eventhubs.EventData;

    public class EventProcessorSample
    {
        public static void main(String args[])
        {
            final String consumerGroupName = "$Default";
            final String namespaceName = "----ServiceBusNamespaceName-----";
            final String eventHubName = "----EventHubName-----";
            final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
            final String sasKey = "---SharedAccessSignatureKey----";

            final String storageAccountName = "---StorageAccountName----";
            final String storageAccountKey = "---StorageAccountKey----";
            final String storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=" + storageAccountName + ";AccountKey=" + storageAccountKey;
            
            ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
            
            EventProcessorHost host = new EventProcessorHost(eventHubName, consumerGroupName, eventHubConnectionString.toString(), storageConnectionString);
            
            System.out.println("Registering host named " + host.getHostName());
            EventProcessorOptions options = new EventProcessorOptions();
            options.setExceptionNotification(new ErrorNotificationHandler());
            try
            {
                host.registerEventProcessor(EventProcessor.class, options).get();
            }
            catch (Exception e)
            {
                System.out.print("Failure while registering: ");
                if (e instanceof ExecutionException)
                {
                    Throwable inner = e.getCause();
                    System.out.println(inner.toString());
                }
                else
                {
                    System.out.println(e.toString());
                }
            }

            System.out.println("Press enter to stop");
            try
            {
                System.in.read();
                host.unregisterEventProcessor();
                
                System.out.println("Calling forceExecutorShutdown");
                EventProcessorHost.forceExecutorShutdown(120);
            }
            catch(Exception e)
            {
                System.out.println(e.toString());
                e.printStackTrace();
            }
            
            System.out.println("End of sample");
        }
    }
    ```

4. 下列欄位換成您建立的事件中心及儲存帳戶時所使用的值。

    ``` Java
    final String namespaceName = "----ServiceBusNamespaceName-----";
    final String eventHubName = "----EventHubName-----";

    final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
    final String sasKey = "---SharedAccessSignatureKey----";

    final String storageAccountName = "---StorageAccountName----"
    final String storageAccountKey = "---StorageAccountKey----";
    ```

> [AZURE.NOTE] 本教學課程中使用 EventProcessorHost 一個執行個體。 若要增加處理量，建議您執行的 EventProcessorHost 的多個執行個體。 在這些情況下，不同的執行個體自動進行共同作業彼此才能負載平衡收到的事件。 如果您想要每一個處理程序*所有*的多個接收器事件時，您必須使用**ConsumerGroup**概念。 從不同的電腦接收事件，，可能會有幫助的 EventProcessorHost 根據機器 （或角色） 的執行個體名稱中已部署。

<!-- Links -->
[Event Hubs overview]: ../articles/event-hubs/event-hubs-overview.md
[Azure 儲存體帳戶]: ../articles/storage/storage-create-storage-account.md
[Azure 傳統入口網站]: http://manage.windowsazure.com
[Maven Package]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22

<!-- Images -->
[11]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp2.png
[12]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp3.png

