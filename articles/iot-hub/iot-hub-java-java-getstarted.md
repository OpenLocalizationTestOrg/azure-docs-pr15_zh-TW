<properties
    pageTitle="快速入門 Java azure IoT 中樞 |Microsoft Azure"
    description="Azure IoT 中心與 Java 快速開始教學課程。 使用 Azure IoT 中心和 Java Microsoft Azure IoT Sdk 實作網際網路的項目解決方案。"
    services="iot-hub"
    documentationCenter="java"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="java"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/11/2016"
     ms.author="dobett"/>

# <a name="get-started-with-azure-iot-hub-for-java"></a>快速入門 java Azure IoT 中心

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

在本教學課程結尾，您可以有三個 Java console 應用程式︰

* **建立裝置身分識別**，而建立裝置身分識別，並將模擬的裝置連線的相關的安全性鍵。
* **讀取 d2c 郵件**，以顯示由您模擬的裝置傳送遙測。
* **模擬裝置**，這與先前建立的裝置身分識別連線到您 IoT 集線器，並傳送遙測訊息每個第二個使用 AMQP 通訊協定。

> [AZURE.NOTE] 文章[IoT 中心 Sdk] [lnk-hub-sdks]提供各種 Sdk，您可以用來建立兩個裝置與您的方案後端上執行應用程式的相關資訊。

若要完成此教學課程中，您需要下列項目︰

+ Java SE 8。 <br/> [準備您的開發環境][lnk-dev-setup]說明如何安裝 Windows 或 Linux Java 本教學課程。

+ Maven 3。  <br/> [準備您的開發環境][lnk-dev-setup]說明如何安裝 Windows 或 Linux Maven 本教學課程。

+ 使用中的 Azure 帳戶。 (如果您沒有帳戶，您可以建立一個[免費的帳戶][lnk-free-trial]在幾分鐘的時間。)

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

最後一個步驟中，記下的**主索引鍵**值]，然後按一下 [**訊息**]。 在**訊息**刀，記下**事件中心相容的名稱**] 和 [**事件] 中心相容結束點**。 當您建立**讀取 d2c 郵件**應用程式時，您會需要這三個值。

![Azure IoT 中心訊息刀入口網站][6]

您已經建立 IoT 中心，並您具備 IoT 中心主機名稱、 IoT 中心連線字串、 IoT 中心主索引鍵、 事件中心相容的名稱和事件中心相容端點，您必須完成本教學課程。

## <a name="create-a-device-identity"></a>建立裝置的身分識別

在此區段中，您可以建立的 Java 主控台應用程式中的身分識別您 IoT 中心建立新的裝置身分識別。 裝置無法連線到 IoT 集線器，除非裝置身分識別登錄中有一個項目。 請參閱[IoT 中心開發人員指南]**裝置身分識別登錄**章節[lnk-devguide-identity]如需詳細資訊。 當您執行此主控台應用程式時，則會產生的唯一裝置識別碼和您的裝置可用來找出本身裝置至雲端郵件傳送到 IoT 中樞的金鑰。

1. 建立一個稱為 iot java-取得-啟動新的空白資料夾。 在 [iot java-取得-啟動] 資料夾中，建立新 Maven 專案稱為**建立裝置身分識別**使用下列命令，在命令提示字元 」。 請注意，這是單一、 完整的命令︰

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=create-device-identity -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. 在命令提示字元中，瀏覽至新建立的裝置身分識別資料夾。

3. 使用文字編輯器，開啟 pom.xml 檔案建立裝置識別資料夾中，新增下列相依性的**相依性**節點。 這可讓您在應用程式中使用 sdk 服務 iothub 可以套件︰

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.9</version>
    </dependency>
    ```
    
4. 儲存並關閉 pom.xml 檔案。

5. 使用文字編輯器，開啟 create-device-identity\src\main\java\com\mycompany\app\App.java 檔案。

6. 新增下列**匯入**陳述式的檔案︰

    ```
    import com.microsoft.azure.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.iot.service.sdk.Device;
    import com.microsoft.azure.iot.service.sdk.RegistryManager;

    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. 新增下列類別層級變數至**應用程式**類別，取代**{yourhubconnectionstring}**值您註明較舊版本︰

    ```
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "myFirstJavaDevice";
    
    ```
    
8. 修改簽章，如下所示包含例外狀況的**主要**方式︰

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException, Exception
    ```
    
9. 將下列程式碼新增為**主要**方法本文中。 將此程式碼會建立裝置*javadevice* IoT 中心身分識別登錄中名為 [如果尚不存在。 然後，它會顯示的裝置 id 與您需要更新版本的金鑰︰

    ```
    RegistryManager registryManager = RegistryManager.createFromConnectionString(connectionString);

    Device device = Device.createFromId(deviceId, null, null);
    try {
      device = registryManager.addDevice(device);
    } catch (IotHubException iote) {
      try {
        device = registryManager.getDevice(deviceId);
      } catch (IotHubException iotf) {
        iotf.printStackTrace();
      }
    }
    System.out.println("Device id: " + device.getDeviceId());
    System.out.println("Device key: " + device.getPrimaryKey());
    ```

10. 儲存並關閉 App.java 檔案。

11. 若要建立使用 Maven**建立裝置識別**應用程式，執行下列命令在命令提示字元中建立裝置識別資料夾︰

    ```
    mvn clean package -DskipTests
    ```

12. 若要執行使用 Maven**建立裝置識別**應用程式，執行下列命令在命令提示字元中建立裝置識別資料夾︰

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

13. 請記下的**裝置識別碼**與**裝置金鑰**。 您必須更新這些版本當您建立的連線到裝置 IoT 中樞的應用程式。

> [AZURE.NOTE] IoT 中心身分識別登錄機碼只會儲存裝置啟用安全存取中樞的身分識別。 它會儲存裝置識別碼與索引鍵作為安全性憑證和啟用或停用旗標，您可以使用來停用 [在個別的裝置存取。 如果您的應用程式需要將其他裝置的特定中繼資料儲存，它應該使用特定應用程式存放區。 請參閱[IoT 中心開發人員指南][lnk-devguide-identity]如需詳細資訊。

## <a name="receive-device-to-cloud-messages"></a>接收裝置至雲端的郵件

在此區段中，您可以建立裝置至雲端郵件讀取 IoT 中樞的 Java 主控台應用程式。 IoT 中心公開[事件中心][lnk-event-hubs-overview]-相容的端點，讓您讀取裝置至雲端的郵件。 若要簡潔，本教學課程中建立基本的讀者不適合高處理量部署。 [處理裝置至雲端訊息][lnk-process-d2c-tutorial]教學課程教您如何小數位數的裝置至雲端訊息。 [開始使用事件集線器][lnk-eventhubs-tutorial]教學課程提供進一步資訊如何處理郵件從事件集線器與適用於 IoT 中心事件中心相容結束點。

> [AZURE.NOTE] 事件中心相容的端點讀取裝置至雲端的郵件一律會使用 AMQP 通訊協定。

1. 在 iot java-取得-啟動的資料夾，您建立*建立裝置身分識別*] 區段中，建立新 Maven 專案稱為**讀取 d2c 郵件**使用下列命令，在命令提示字元 」。 請注意，這是單一、 完整的命令︰

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. 在命令提示字元中，瀏覽至新的讀取 d2c 郵件資料夾。

3. 使用文字編輯器，開啟 [讀取 d2c 郵件] 資料夾中的 pom.xml 檔案和**相依性**節點中加入下列相依性。 這可讓您從事件中心相容的結束閱讀應用程式中使用 eventhubs 用戶端套件︰

    ```
    <dependency> 
        <groupId>com.microsoft.azure</groupId> 
        <artifactId>azure-eventhubs</artifactId> 
        <version>0.8.2</version> 
    </dependency>
    ```

4. 儲存並關閉 pom.xml 檔案。

5. 使用文字編輯器，開啟 read-d2c-messages\src\main\java\com\mycompany\app\App.java 檔案。

6. 新增下列**匯入**陳述式的檔案︰

    ```
    import java.io.IOException;
    import com.microsoft.azure.eventhubs.*;
    import com.microsoft.azure.servicebus.*;
    
    import java.io.IOException;
    import java.nio.charset.Charset;
    import java.time.*;
    import java.util.Collection;
    import java.util.concurrent.ExecutionException;
    import java.util.function.*;
    import java.util.logging.*;
    ```

7. 新增下列類別層級變數**應用程式**類別。 **{Youriothubkey}**， **{youreventhubcompatibleendpoint}**， **{youreventhubcompatiblename}**取代先前記下的值︰

    ```
    private static String connStr = "Endpoint={youreventhubcompatibleendpoint};EntityPath={youreventhubcompatiblename};SharedAccessKeyName=iothubowner;SharedAccessKey={youriothubkey}";
    ```

8. 在**應用程式**類別新增下列**receiveMessages**方法。 這個方法建立連線到事件中心相容端點**EventHubClient**執行個體，然後非同步建立事件中心分割讀取**PartitionReceiver**執行個體。 它會不斷地重複並列印訊息詳細資料，直到該應用程式結束。

    ```
    private static EventHubClient receiveMessages(final String partitionId)
    {
      EventHubClient client = null;
      try {
        client = EventHubClient.createFromConnectionStringSync(connStr);
      }
      catch(Exception e) {
        System.out.println("Failed to create client: " + e.getMessage());
        System.exit(1);
      }
      try {
        client.createReceiver( 
          EventHubClient.DEFAULT_CONSUMER_GROUP_NAME,  
          partitionId,  
          Instant.now()).thenAccept(new Consumer<PartitionReceiver>()
        {
          public void accept(PartitionReceiver receiver)
          {
            System.out.println("** Created receiver on partition " + partitionId);
            try {
              while (true) {
                Iterable<EventData> receivedEvents = receiver.receive(100).get();
                int batchSize = 0;
                if (receivedEvents != null)
                {
                  for(EventData receivedEvent: receivedEvents)
                  {
                    System.out.println(String.format("Offset: %s, SeqNo: %s, EnqueueTime: %s", 
                      receivedEvent.getSystemProperties().getOffset(), 
                      receivedEvent.getSystemProperties().getSequenceNumber(), 
                      receivedEvent.getSystemProperties().getEnqueuedTime()));
                    System.out.println(String.format("| Device ID: %s", receivedEvent.getProperties().get("iothub-connection-device-id")));
                    System.out.println(String.format("| Message Payload: %s", new String(receivedEvent.getBody(),
                      Charset.defaultCharset())));
                    batchSize++;
                  }
                }
                System.out.println(String.format("Partition: %s, ReceivedBatch Size: %s", partitionId,batchSize));
              }
            }
            catch (Exception e)
            {
              System.out.println("Failed to receive messages: " + e.getMessage());
            }
          }
        });
      }
      catch (Exception e)
      {
        System.out.println("Failed to create receiver: " + e.getMessage());
      }
      return client;
    }
    ```

    > [AZURE.NOTE] 這個方法，讓收件者僅限讀取寄給 IoT 中心接收者開始執行之後建立收件者時，使用篩選。 讓您可以看到目前設定的郵件，這是測試環境中非常有用。 請確定您的程式碼生產環境中的程序的所有郵件，-瞭解[如何處理 IoT 中心裝置至雲端訊息][lnk-process-d2c-tutorial]教學課程，如需詳細資訊。

9. 修改簽章，如下所示包含例外狀況的**主要**方式︰

    ```
    public static void main( String[] args ) throws IOException
    ```

10. 將下列程式碼新增至 [**應用程式**類別中**主要**的方法。 將此程式碼會建立兩個**EventHubClient**和**PartitionReceiver**執行個體，並可讓您處理郵件完成後，請關閉應用程式︰

    ```
    EventHubClient client0 = receiveMessages("0");
    EventHubClient client1 = receiveMessages("1");
    System.out.println("Press ENTER to exit.");
    System.in.read();
    try
    {
      client0.closeSync();
      client1.closeSync();
      System.exit(0);
    }
    catch (ServiceBusException sbe)
    {
      System.exit(1);
    }
    ```

    > [AZURE.NOTE] 將此程式碼，假設您建立的 F1 （免費） 層 IoT 中心。 免費 IoT 中樞有兩個命名為 「 0 」 和 「 1 」 的磁碟分割。

11. 儲存並關閉 App.java 檔案。

12. 若要建立使用 Maven 的**讀取 d2c 郵件**應用程式，執行下列命令在命令提示字元中讀取 d2c 郵件資料夾︰

    ```
    mvn clean package -DskipTests
    ```

## <a name="create-a-simulated-device-app"></a>建立模擬的裝置應用程式

在此區段中，您可以建立模擬會裝置至雲端郵件傳送給 IoT 中樞的裝置的 Java 主控台應用程式。

1. 在 iot java-取得-啟動的資料夾，您建立*建立裝置身分識別*] 區段中，建立新 Maven 專案稱為**模擬裝置**使用下列命令，在命令提示字元 」。 請注意，這是單一、 完整的命令︰

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. 在命令提示字元中，瀏覽至新的模擬裝置資料夾。

3. 使用文字編輯器，開啟 pom.xml 資料夾中檔案模擬裝置和**相依性**節點中加入下列相依性。 這可讓您應用程式中使用的用戶端 java iothub 套件與 IoT 中心通訊和序列化 JSON Java 物件︰

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-device-client</artifactId>
      <version>1.0.14</version>
    </dependency>
    <dependency>
      <groupId>com.google.code.gson</groupId>
      <artifactId>gson</artifactId>
      <version>2.3.1</version>
    </dependency>
    ```

4. 儲存並關閉 pom.xml 檔案。

5. 使用文字編輯器，開啟 simulated-device\src\main\java\com\mycompany\app\App.java 檔案。

6. 新增下列**匯入**陳述式的檔案︰

    ```
    import com.microsoft.azure.iothub.DeviceClient;
    import com.microsoft.azure.iothub.IotHubClientProtocol;
    import com.microsoft.azure.iothub.Message;
    import com.microsoft.azure.iothub.IotHubStatusCode;
    import com.microsoft.azure.iothub.IotHubEventCallback;
    import com.microsoft.azure.iothub.IotHubMessageResult;
    import com.google.gson.Gson;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Random;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```

7. 新增下列類別層級變數取代您 IoT 中心名稱，並與您在*建立裝置的身分識別*] 區段中所產生的裝置索引鍵值**{yourdevicekey}** **{youriothubname}** **應用程式**類別︰

    ```
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myFirstJavaDevice;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.AMQP;
    private static String deviceId = "myFirstJavaDevice";
    private static DeviceClient client;
    ```

    這個範例應用程式時，會產生**DeviceClient**物件，請使用**通訊協定**變數。 您可以使用 [HTTP] 或 [AMQP 通訊協定與 IoT 中樞通訊。

8. 新增下列巢狀**TelemetryDataPoint**類別內，指定您的裝置傳送給您 IoT 中樞的遙測資料的**應用程式**類別︰

    ```
    private static class TelemetryDataPoint {
      public String deviceId;
      public double windSpeed;
        
      public String serialize() {
        Gson gson = new Gson();
        return gson.toJson(this);
      }
    }
    ```

9. 新增下列**應用程式**類別，以顯示處理模擬裝置郵件時，會傳回 IoT 中心的通知狀態內部巢狀的**EventCallback**類別。 在處理郵件時，此方法也會通知應用程式中的主執行緒︰

    ```
    private static class EventCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to message with status: " + status.name());
      
        if (context != null) {
          synchronized (context) {
            context.notify();
          }
        }
      }
    }
    ```

10. 新增下列**應用程式**類別內巢狀的**MessageSender**類別。 此類別中的 [**執行**] 方法會產生傳送給您 IoT 中樞的範例遙測資料，並等待的通知，然後再傳送下一封郵件︰

    ```
    private static class MessageSender implements Runnable {
      public volatile boolean stopThread = false;
      
      public void run()  {
        try {
          double avgWindSpeed = 10; // m/s
          Random rand = new Random();
          
          while (!stopThread) {
            double currentWindSpeed = avgWindSpeed + rand.nextDouble() * 4 - 2;
            TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
            telemetryDataPoint.deviceId = deviceId;
            telemetryDataPoint.windSpeed = currentWindSpeed;
            
            String msgStr = telemetryDataPoint.serialize();
            Message msg = new Message(msgStr);
            System.out.println("Sending: " + msgStr);
            
            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);
            
            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(1000);
          }
        } catch (InterruptedException e) {
          System.out.println("Finished.");
        }
      }
    }
    ```

    這個方法會傳送新裝置至雲端郵件一秒後 IoT 中心確認上一封郵件。 郵件中包含 JSON 序列化物件 deviceId 與模擬風速度感應器的隨機產生的號碼。

11. 取代建立傳送裝置至雲端郵件給您 IoT 中樞的執行緒下列程式碼的**主要**的方法︰

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException {
      client = new DeviceClient(connString, protocol);
      client.open();

      MessageSender sender = new MessageSender();

      ExecutorService executor = Executors.newFixedThreadPool(1);
      executor.execute(sender);

      System.out.println("Press ENTER to exit.");
      System.in.read();
      executor.shutdownNow();
      client.close();
    }
    ```

12. 儲存並關閉 App.java 檔案。

13. 若要建立使用 Maven 的**模擬裝置**應用程式，執行下列命令在命令提示字元中模擬裝置資料夾︰

    ```
    mvn clean package -DskipTests
    ```

> [AZURE.NOTE] 若要保留項目簡單，此教學課程並未實作任何重試原則。 在實際執行程式碼，您應該實作重試原則 （例如指數輪詢），建議在[暫時性錯誤處理]的 MSDN 文章[lnk-transient-faults]。

## <a name="run-the-applications"></a>執行應用程式

您已準備好執行應用程式。

1. 在命令提示字元讀取 d2c 資料夾中，執行下列命令以開始監視 IoT 中心中的第一個磁碟分割︰

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Java IoT 中心監控郵件裝置至雲端服務用戶端應用程式][7]

2. 在命令提示字元模擬裝置資料夾中，執行下列命令以開始遙測資料傳送到您 IoT 中心︰

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![Java IoT 中心裝置用戶端應用程式傳送裝置至雲端的郵件][8]

3. [Azure 入口網站]中的 [**使用狀況**] 磚[lnk-portal]的中樞的郵件數︰

    ![Azure 入口網站使用方式] 方塊顯示數字寄給 IoT 中心][43]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您 Azure 入口網站中，在設定新的 IoT 中心，然後建立中樞的身分識別登錄中的 [裝置身分識別。 您可以使用此裝置身分識別來啟用裝置至雲端郵件傳送到中樞模擬的裝置應用程式。 您也會建立顯示中樞收到的郵件應用程式。 

若要繼續快速入門 IoT 中心，然後瀏覽其他 IoT 案例就會看到︰

- [連接您的裝置][lnk-connect-device]
- [開始使用裝置管理][lnk-device-management]
- [快速入門 IoT 閘道器 SDK][lnk-gateway-SDK]

若要瞭解如何將延伸 IoT 方案和小數位數的裝置至雲端訊息，請參閱[處理裝置至雲端訊息][lnk-process-d2c-tutorial]教學課程。

<!-- Images. -->
[6]: ./media/iot-hub-java-java-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-java-java-getstarted/runapp1.png
[8]: ./media/iot-hub-java-java-getstarted/runapp2.png
[43]: ./media/iot-hub-java-java-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/