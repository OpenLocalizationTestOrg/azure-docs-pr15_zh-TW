<properties
    pageTitle="雲端-裝置以傳送訊息 IoT 中心 |Microsoft Azure"
    description="請遵循此教學課程以瞭解如何傳送使用 Azure IoT 中心 C# 雲端-裝置訊息。"
    services="iot-hub"
    documentationCenter=".net"
    authors="fsautomata"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="06/23/2016"
     ms.author="elioda"/>

# <a name="tutorial-how-to-send-cloud-to-device-messages-with-iot-hub-and-net"></a>教學課程︰ 如何傳送郵件 IoT 中樞和.Net 雲端-裝置

[AZURE.INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>簡介

Azure IoT 中心是完全受管理的服務，協助啟用可靠和安全數百萬 IoT 裝置之間的雙向通訊和應用程式回結束。 [快速入門 IoT 中心]教學課程示範如何建立 IoT 中心與佈建裝置身分識別，程式碼模擬的裝置傳送裝置至雲端訊息。

[快速入門 IoT 中心]根據本教學課程。 您將會如何顯示以︰

- 從應用程式雲端後端，傳送雲端-裝置訊息到單一的裝置，透過 IoT 中心。
- 收到的雲端-裝置在裝置上的郵件。
- 從您的應用程式雲端後端，要求傳送通知 （*意見反應*） 從 IoT] 中心傳送至裝置的郵件。

您可以找到雲端至裝置的郵件] 的詳細資訊， [IoT 中心開發人員指南]中[IoT Hub Developer Guide - C2D]。

在本教學課程結尾，您將會執行兩個 Windows 主控台應用程式︰

* **SimulatedDevice**，修改[IoT 中心快速入門]]，連線到您 IoT 中心及接收雲端至裝置的郵件中建立的應用程式版本。
* **SendCloudToDevice**，透過 IoT 集線器模擬的裝置傳送雲端-裝置訊息，並再收到其傳送通知。

> [AZURE.NOTE] IoT] 中心內有許多裝置平台和語言 （包括 C、 Java 和 Javascript），透過 Azure IoT 裝置 Sdk 的 SDK 支援。 如需有關如何連接您的裝置，此教學課程的程式碼，並通常 Azure IoT 中樞的逐步指示，請參閱[Azure IoT 開發人員中心]。

若要完成此教學課程中，您需要下列動作︰

+ Microsoft Visual Studio 2015

+ 使用中的 Azure 帳戶。 (如果您沒有帳戶，您可以建立一個[免費的帳戶][lnk-free-trial]在幾分鐘的時間。)

## <a name="receive-messages-on-the-simulated-device"></a>在 [模擬的裝置上接收郵件

在此區段中，您可以修改模擬的裝置應用程式中接收來自 IoT 中心雲端-裝置訊息的 [[快速入門 IoT 中心]所建立。

1. 在 Visual Studio 中，在**SimulatedDevice**專案中，新增下列方法**程式**類別。

        private static async void ReceiveC2dAsync()
        {
            Console.WriteLine("\nReceiving cloud to device messages from service");
            while (true)
            {
                Message receivedMessage = await deviceClient.ReceiveAsync();
                if (receivedMessage == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received message: {0}", Encoding.ASCII.GetString(receivedMessage.GetBytes()));
                Console.ResetColor();

                await deviceClient.CompleteAsync(receivedMessage);
            }
        }

    `ReceiveAsync`方法非同步傳回收到的郵件時，收到裝置。 會傳回之後，可逾時*null* （在此情況下，使用預設的一分鐘）。 這種情況，程式碼應該繼續等候新郵件。 這是原因`if (receivedMessage == null) continue`線條。

    通話`CompleteAsync()`通知 IoT 中心郵件已成功處理。 從裝置佇列都可以移除安全地的郵件。 如果發生了，導致無法裝置應用程式完成的郵件處理，IoT 中心提供其一次。 請務必然後裝置應用程式中的郵件處理邏輯會*冪*，以便接收相同的郵件多次產生相同的結果。 應用程式，可以暫時也放棄郵件中，藉此在 IoT 中心保留供日後使用佇列中的訊息。 或者，如果應用程式可以拒絕的訊息，將會永久移除佇列中的郵件。 如需有關雲端-裝置訊息生命週期的詳細資訊，請參閱[IoT 中心開發人員指南][IoT Hub Developer Guide - C2D]。

    > [AZURE.NOTE] 使用，而非 MQTT 或 AMQP 的 HTTP 作為傳輸時,`ReceiveAsync`方法會立即傳回。 支援的圖樣的 HTTP 雲端-裝置郵件是不常郵件 （每個 25 分鐘） 的核取間歇性連線的裝置。 發行更多 HTTP 接收 IoT 中心節流要求的結果。 更多關於 MQTT、 AMQP 和 HTTP 支援和 IoT 中心節流之間的差異的詳細資訊，請參閱[IoT 中心開發人員指南][IoT Hub Developer Guide - C2D]。

2. 新增下列方法，**主要**方法中的前`Console.ReadLine()`行︰

        ReceiveC2dAsync();

> [AZURE.NOTE] 為了簡單起見本教學課程並未實作任何重試原則。 在實際執行程式碼，您應該實作重試原則 （例如指數輪詢），建議在[暫時性錯誤處理]的 MSDN 文章。

## <a name="send-a-cloud-to-device-message"></a>傳送雲端-裝置郵件

在此區段中，您會撰寫會雲端-裝置訊息傳送給模擬的裝置應用程式在 Windows 主控台應用程式。

1. 在目前的 Visual Studio 解決方案中，使用**主控台應用程式**專案範本建立新的 Visual C# 桌面應用程式專案。 專案**SendCloudToDevice**的名稱。

    ![在 Visual Studio 中的新專案][20]

2. 在方案總管解決方案，請以滑鼠右鍵按一下，然後按一下**管理 NuGet 套件解決方案...**。 

    這會開啟 [**管理 NuGet 套件**] 視窗。

3. 搜尋`Microsoft Azure Devices`、 按一下 [**安裝**]，並接受使用規定。 

    此下載、 安裝]，並新增[Azure IoT-服務 SDK NuGet 套件]的參考。

4. 新增下列`using` **Program.cs**檔案頂端的陳述式︰

        using Microsoft.Azure.Devices;

5. 將下列欄位加入**程式**類別。 替代[快速入門 IoT 中樞]的 IoT 中心連接字串的版面配置區值︰

        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";

6. 新增下列方法**程式**類別︰

        private async static Task SendCloudToDeviceMessageAsync()
        {
            var commandMessage = new Message(Encoding.ASCII.GetBytes("Cloud to device message."));
            await serviceClient.SendAsync("myFirstDevice", commandMessage);
        }

    這個方法會新雲端-裝置郵件傳送至裝置的識別碼， `myFirstDevice`。 相應地變更此參數，以便您修改中[開始使用 IoT 中心]時所用的。

7. 最後，將下列幾行新增至**主要**的方法︰

        Console.WriteLine("Send Cloud-to-Device message\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

        Console.WriteLine("Press any key to send a C2D message.");
        Console.ReadLine();
        SendCloudToDeviceMessageAsync().Wait();
        Console.ReadLine();

8. 在 Visual Studio 中，以滑鼠右鍵按一下您的方案，並選取**...設定啟動專案**。 選取**多個啟動專案**]，然後選取**ProcessDeviceToCloudMessages** **SimulatedDevice**，與**SendCloudToDevice**的**啟動**動作。

9.  按**F5**。 所有的三個應用程式應該會啟動。 選取 [ **SendCloudToDevice**視窗]，然後按**Enter**。 您應該會看到正在模擬的應用程式所收到的訊息。

    ![應用程式接收郵件][21]

## <a name="receive-delivery-feedback"></a>接收傳送意見反應
有可能要求送達 （或過期） 通知從 IoT 中心針對每個雲端-裝置訊息。 這會啟用雲端後端，輕鬆地通知重試或補償邏輯。 如需有關雲端至裝置的意見反應的詳細資訊，請參閱[IoT 中心開發人員指南][IoT Hub Developer Guide - C2D]。

在此區段中，您將修改要求意見反應，並從 IoT 中心接收**SendCloudToDevice**應用程式。

1. 在 Visual Studio 中，在**SendCloudToDevice**專案中，新增下列方法**程式**類別。
   
        private async static void ReceiveFeedbackAsync()
        {
            var feedbackReceiver = serviceClient.GetFeedbackReceiver();

            Console.WriteLine("\nReceiving c2d feedback from service");
            while (true)
            {
                var feedbackBatch = await feedbackReceiver.ReceiveAsync();
                if (feedbackBatch == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received feedback: {0}", string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
                Console.ResetColor();

                await feedbackReceiver.CompleteAsync(feedbackBatch);
            }
        }

    請注意，接收圖樣接收雲端-裝置郵件從裝置應用程式中使用相同的項目。

2. 新增下列方法在**主要**方法中，向右後`serviceClient = ServiceClient.CreateFromConnectionString(connectionString)`行︰

        ReceiveFeedbackAsync();

3. 若要要求的雲端-裝置訊息傳送意見反應，您必須**SendCloudToDeviceMessageAsync**方法中指定的屬性。 新增下列命令之後,`var commandMessage = new Message(...);`行︰

        commandMessage.Ack = DeliveryAcknowledgement.Full;

4.  按**F5**執行應用程式。 您應該會看到所有開始的三個應用程式。 選取 [ **SendCloudToDevice**視窗]，然後按**Enter**。 您應該會看到正在收到模擬的應用程式，以及幾秒鐘之後，所接收的**SendCloudToDevice**應用程式的意見反應 」 訊息的訊息。

    ![應用程式接收郵件][22]

> [AZURE.NOTE] 為了簡單起見本教學課程並未實作任何重試原則。 在實際執行程式碼，您應該實作重試原則 （例如指數輪詢），建議在[暫時性錯誤處理]的 MSDN 文章。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您學到如何傳送和接收雲端-裝置訊息。 

若要完成使用 IoT 中樞的端對端解決方案的範例，請參閱[Azure IoT 套件]。

若要進一步瞭解開發 IoT 中樞的方案，請參閱[IoT 中心開發人員指南]。

<!-- Images -->
[20]: ./media/iot-hub-csharp-csharp-c2d/create-identity-csharp1.png
[21]: ./media/iot-hub-csharp-csharp-c2d/sendc2d1.png
[22]: ./media/iot-hub-csharp-csharp-c2d/sendc2d2.png

<!-- Links -->

[Azure IoT-服務 SDK NuGet 套件]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[暫時錯誤處理]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[IoT Hub Developer Guide - C2D]: iot-hub-devguide-messaging.md

[IoT 中心開發人員指南]: iot-hub-devguide.md
[快速入門 IoT 中心]: iot-hub-csharp-csharp-getstarted.md
[Azure IoT 開發人員中心]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Azure IoT 套件]: https://azure.microsoft.com/documentation/suites/iot-suite/