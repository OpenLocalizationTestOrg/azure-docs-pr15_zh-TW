<properties
    pageTitle="雲端-裝置以傳送訊息 IoT 中心 |Microsoft Azure"
    description="請遵循此教學課程以瞭解如何傳送 java 使用 Azure IoT 中樞的雲端-裝置訊息。"
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
     ms.date="09/13/2016"
     ms.author="dobett"/>

# <a name="tutorial-how-to-send-cloud-to-device-messages-with-iot-hub-and-java"></a>教學課程︰ 如何傳送郵件 IoT 中心和 Java 雲端-裝置

[AZURE.INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>簡介

Azure IoT 中心是完全受管理的服務，協助啟用可靠和安全數百萬 IoT 裝置之間的雙向通訊和應用程式回結束。 [快速入門 IoT 中心]教學課程示範如何建立 IoT 中心與佈建裝置身分識別，程式碼模擬的裝置傳送裝置至雲端訊息。

[快速入門 IoT 中心]根據本教學課程。 您將會如何顯示以︰

- 從應用程式雲端後端，傳送雲端-裝置訊息到單一的裝置，透過 IoT 中心。
- 收到的雲端-裝置在裝置上的郵件。
- 從您的應用程式雲端後端，要求傳送通知 （*意見反應*） 從 IoT] 中心傳送至裝置的郵件。

您可以找到雲端至裝置的郵件] 的詳細資訊， [IoT 中心開發人員指南]中[IoT Hub Developer Guide - C2D]。

在本教學課程結尾，您必須執行兩個 Java 主控台應用程式︰

* **模擬裝置**的[快速入門 IoT 中心]]，連線到您 IoT 中心及接收雲端至裝置的郵件中建立的應用程式修改版本。
* **傳送 c2d 郵件**是雲端-裝置郵件模擬 IoT] 中心內，透過裝置收發然後其傳送通知。

> [AZURE.NOTE] IoT] 中心內有許多裝置平台和語言 （包括 C、 Java 和 Javascript），透過 Azure IoT 裝置 Sdk 的 SDK 支援。 如需有關如何連接您的裝置，此教學課程的程式碼，並通常 Azure IoT 中樞的逐步指示，請參閱[Azure IoT 開發人員中心]。

若要完成此教學課程中，您需要下列項目︰

+ Java SE 8。 <br/> [準備您的開發環境][lnk-dev-setup]說明如何安裝 Windows 或 Linux Java 本教學課程。

+ Maven 3。  <br/> [準備您的開發環境][lnk-dev-setup]說明如何安裝 Windows 或 Linux Maven 本教學課程。

+ 使用中的 Azure 帳戶。 (如果您沒有帳戶，您可以建立一個[免費的帳戶][lnk-free-trial]在幾分鐘的時間。)

## <a name="receive-messages-on-the-simulated-device"></a>在 [模擬的裝置上接收郵件

在此區段中，您可以修改模擬的裝置應用程式中接收來自 IoT 中心雲端-裝置訊息的 [[快速入門 IoT 中心]所建立。

1. 使用文字編輯器，開啟 simulated-device\src\main\java\com\mycompany\app\App.java 檔案。

2. 新增**應用程式**類別內巢狀類別下列**MessageCallback**類別。 當裝置收到一則訊息，從 IoT 中心叫用的**執行**方式。 在此範例中，裝置一律會通知中心完成的郵件︰

    ```
    private static class MessageCallback implements
    com.microsoft.azure.iothub.MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));

        return IotHubMessageResult.COMPLETE;
      }
    }
    ```

3. 修改建立**MessageCallback**執行個體，然後呼叫**setMessageCallback**方法，將其開啟，如下所示的用戶端之前的**主要**方式︰

    ```
    client = new DeviceClient(connString, protocol);

    MessageCallback callback = new MessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

    > [AZURE.NOTE] 如果您使用 HTTP，而非 MQTT 或 AMQP 傳輸，從 IoT 中心不常 （每個 25 分鐘） 的郵件會檢查**DeviceClient**執行個體。 更多關於 MQTT、 AMQP 和 HTTP 支援和 IoT 中心節流之間的差異的詳細資訊，請參閱[IoT 中心開發人員指南][IoT Hub Developer Guide - C2D]。

## <a name="send-a-cloud-to-device-message"></a>傳送雲端-裝置郵件

在此區段中，您可以建立會雲端-裝置訊息傳送給模擬的裝置應用程式的 Java 主控台應用程式。 您需要裝置您[開始使用 IoT 中心]教學課程中新增裝置識別碼。 您也 IoT 中心，您可以[Azure 入口網站]中找到需要的連接字串。

1. 建立 Maven 專案稱為**傳送 c2d 郵件**使用下列命令，在命令提示字元 」。 請注意，這是單一、 完整的命令︰

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. 在命令提示字元中，瀏覽至新的傳送 c2d 郵件資料夾。

3. 使用文字編輯器，開啟 pom.xml 檔案傳送 c2d 郵件資料夾中，新增下列相依性的**相依性**節點。 新增相依性，可讓您進行通訊 IoT 中心服務應用程式中使用**iothub-java-服務-用戶端**套件︰

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.7</version>
    </dependency>
    ```

4. 儲存並關閉 pom.xml 檔案。

5. 使用文字編輯器，開啟 send-c2d-messages\src\main\java\com\mycompany\app\App.java 檔案。

6. 新增下列**匯入**陳述式的檔案︰

    ```
    import com.microsoft.azure.iot.service.sdk.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. 新增下列類別層級變數至**應用程式**類別， **{yourhubconnectionstring}** ， **{yourdeviceid}**使用值來取代您所述較舊版本︰

    ```
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQP;
    ```
    
8. 取代下列程式碼，連線到您 IoT 中心、 傳送訊息給您的裝置，然後等待認可裝置已接收，並處理郵件的**主要**的方法︰

    ```
    public static void main(String[] args) throws IOException,
        URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(
        connectionString, protocol);
      
      if (serviceClient != null) {
        serviceClient.open();
        FeedbackReceiver feedbackReceiver = serviceClient
          .getFeedbackReceiver(deviceId);
        if (feedbackReceiver != null) feedbackReceiver.open();

        Message messageToSend = new Message("Cloud to device message.");
        messageToSend.setDeliveryAcknowledgement(DeliveryAcknowledgement.Full);

        serviceClient.send(deviceId, messageToSend);
        System.out.println("Message sent to device");

        FeedbackBatch feedbackBatch = feedbackReceiver.receive(10000);
        if (feedbackBatch != null) {
          System.out.println("Message feedback received, feedback time: "
            + feedbackBatch.getEnqueuedTimeUtc().toString());
        }

        if (feedbackReceiver != null) feedbackReceiver.close();
        serviceClient.close();
      }
    }
    ```

    > [AZURE.NOTE] 為了簡單起見本教學課程並未實作任何重試原則。 在實際執行程式碼，您應該實作重試原則 （例如指數輪詢），建議在[暫時性錯誤處理]的 MSDN 文章。

## <a name="run-the-applications"></a>執行應用程式

您已準備好執行應用程式。

1. 在命令提示字元模擬裝置資料夾中，執行下列命令開始遙測傳送到您 IoT 集線器並聽取雲端的裝置傳送的郵件從您的中心︰

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![執行模擬的裝置應用程式][img-simulated-device]

2. 在命令提示字元傳送 c2d 郵件] 資料夾中，執行下列命令以傳送雲端-裝置郵件，並等待 [意見反應通知︰

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![執行 [雲端-裝置郵件傳送] 命令][img-send-command]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您學到如何傳送和接收雲端-裝置訊息。 

若要完成使用 IoT 中樞的端對端解決方案的範例，請參閱[Azure IoT 套件]。

若要進一步瞭解開發 IoT 中樞的方案，請參閱[IoT 中心開發人員指南]。


<!-- Images -->
[img-simulated-device]: media/iot-hub-java-java-c2d/receivec2d.png
[img-send-command]:  media/iot-hub-java-java-c2d/sendc2d.png
<!-- Links -->

[快速入門 IoT 中心]: iot-hub-java-java-getstarted.md
[IoT Hub Developer Guide - C2D]: iot-hub-devguide-messaging.md
[IoT 中心開發人員指南]: iot-hub-devguide.md
[Azure IoT 開發人員中心]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[暫時錯誤處理]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure 入口網站]: https://portal.azure.com
[Azure IoT 套件]: https://azure.microsoft.com/documentation/suites/iot-suite/