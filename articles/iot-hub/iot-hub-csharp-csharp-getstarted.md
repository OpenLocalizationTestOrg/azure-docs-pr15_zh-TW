<properties
    pageTitle="Azure IoT 中樞 C# 快速入門 |Microsoft Azure"
    description="Azure IoT 中心與 C# 快速開始教學課程。 使用 Azure IoT 中心及 C# Microsoft Azure IoT Sdk 實作網際網路的項目解決方案。"
    services="iot-hub"
    documentationCenter=".net"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/12/2016"
     ms.author="dobett"/>

# <a name="get-started-with-azure-iot-hub-for-net"></a>快速入門 Azure IoT 中心.net

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

在本教學課程結尾，您可以有三個 Windows 主控台應用程式︰

* **CreateDeviceIdentity**，建立裝置身分識別與相關聯的安全性鍵將模擬的裝置連線。
* **ReadDeviceToCloudMessages**，其中顯示由您模擬的裝置傳送遙測。
* **SimulatedDevice**，先前建立的裝置身分識別與連線到您 IoT 集線器和遙測傳送郵件給的每個第二個藉由 AMQP 通訊協定。

> [AZURE.NOTE] 建立、 裝置與您的方案後端上執行兩個應用程式，您可以使用各種 Sdk 的相關資訊，請參閱[IoT 中心 Sdk][lnk-hub-sdks]。

若要完成此教學課程中，您需要下列項目︰

+ Microsoft Visual Studio 2015。

+ 使用中的 Azure 帳戶。 (如果您沒有帳戶，您可以建立一個[免費的帳戶][lnk-free-trial]在幾分鐘的時間。)

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

您已經建立 IoT 中心，而且您具有主機名稱和連線字串，您必須完成本教學課程的其餘部分。

## <a name="create-a-device-identity"></a>建立裝置的身分識別

在此區段中，您可以建立 Windows 主控台應用程式中的身分識別您 IoT 中心建立裝置身分識別。 裝置無法連線到 IoT 集線器，除非裝置身分識別登錄中有一個項目。 如需詳細資訊，請參閱 「 裝置身分識別登錄 」 一節[IoT 中心開發人員指南][lnk-devguide-identity]。 當您執行此主控台應用程式時，則會產生的唯一裝置識別碼和您的裝置可用來找出本身裝置至雲端郵件傳送到 IoT 中樞的金鑰。

1. 在 Visual Studio 中，新增至目前的方案 Visual C# Windows 傳統桌面專案使用**主控台應用程式**專案範本。 請確定.NET Framework 版本是 4.5.1 或更新版本。 專案**CreateDeviceIdentity**的名稱。

    ![新的 Visual C# Windows 傳統桌面專案][10]

2. 在方案總管中**CreateDeviceIdentity**專案，以滑鼠右鍵按一下，然後再按一下 [**管理 Nuget 套件**。

3. 在**Nuget 套件管理員**] 視窗中，選取 [**瀏覽**、 搜尋**microsoft.azure.devices**、 選取 [**安裝**] 以安裝**Microsoft.Azure.Devices**套件，並接受使用規定。 此程序下載、 安裝，並將[Microsoft Azure IoT 服務 SDK]參考[lnk-nuget-service-sdk]Nuget 封裝並相依性。

    ![Nuget 套件管理員] 視窗][11]

4. 新增下列`using` **Program.cs**檔案頂端的陳述式︰

        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Common.Exceptions;

5. 將下列欄位加入**程式**類別。 取代 IoT 中樞在前一節中所建立的連線字串中的版面配置區的值。

        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";

6. 新增下列方法**程式**類別︰

        private static async Task AddDeviceAsync()
        {
            string deviceId = "myFirstDevice";
            Device device;
            try
            {
                device = await registryManager.AddDeviceAsync(new Device(deviceId));
            }
            catch (DeviceAlreadyExistsException)
            {
                device = await registryManager.GetDeviceAsync(deviceId);
            }
            Console.WriteLine("Generated device key: {0}", device.Authentication.SymmetricKey.PrimaryKey);
        }

    這個方法建立識別碼**myFirstDevice**裝置身分識別。 （如果該裝置識別碼已存在於登錄機碼，程式碼只要擷取現有裝置的資訊。）應用程式就會顯示該身分識別的主索引鍵。 您使用此按鍵模擬裝置中連線到您的 IoT 集散地。

7. 最後，將下列幾行新增至**主要**的方法︰

        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddDeviceAsync().Wait();
        Console.ReadLine();

8. 執行這個應用程式，並記下裝置機碼。

    ![應用程式所產生的裝置機碼][12]

> [AZURE.NOTE] IoT 中心身分識別登錄機碼只會儲存裝置啟用安全存取中樞的身分識別。 它會儲存裝置識別碼與索引鍵作為安全性認證，並啟用或停用旗標，您可以使用來停用 [在個別的裝置存取。 如果您的應用程式需要將其他裝置的特定中繼資料儲存，它應該使用特定應用程式存放區。 如需詳細資訊，請參閱[IoT 中心開發人員指南][lnk-devguide-identity]。

## <a name="receive-device-to-cloud-messages"></a>接收裝置至雲端的郵件

在此區段中，您可以建立裝置至雲端郵件讀取 IoT 中樞的 Windows 主控台應用程式。 IoT 中心公開[Azure 事件集線器][lnk-event-hubs-overview]-相容的端點，讓您讀取裝置至雲端的郵件。 若要簡潔，本教學課程中建立基本的讀者不適合高處理量部署。 若要瞭解如何在裝置至雲端小數位數的訊息，請參閱[處理裝置至雲端訊息][lnk-process-d2c-tutorial]教學課程。 進一步瞭解如何處理郵件從事件集線器，請參閱[開始使用事件集線器][lnk-eventhubs-tutorial]教學課程。 （此教學課程為適用於 IoT 中心事件中心相容結束點）。

> [AZURE.NOTE] 事件中心相容的端點讀取裝置至雲端的郵件一律會使用 AMQP 通訊協定。

1. 在 Visual Studio 中，新增 Visual C# Windows 傳統桌面專案至目前的方案，使用**主控台應用程式**專案範本。 請確定.NET Framework 版本是 4.5.1 或更新版本。 專案**ReadDeviceToCloudMessages**的名稱。

    ![新的 Visual C# Windows 傳統桌面專案][10]

2. 在方案總管中**ReadDeviceToCloudMessages**專案，以滑鼠右鍵按一下，然後再按一下 [**管理 Nuget 套件**。

3. 在 [ **Nuget 封裝管理員**] 視窗中，搜尋**WindowsAzure.ServiceBus**、 選取 [**安裝**]，並接受使用規定。 此程序下載、 安裝]，並新增至[Azure 服務匯流排]參考[lnk-servicebus-nuget]，所有相依性。 此套件可讓應用程式連線至您 IoT 中樞的事件中心相容端點。

4. 新增下列`using` **Program.cs**檔案頂端的陳述式︰

        using Microsoft.ServiceBus.Messaging;
        using System.Threading;

5. 將下列欄位加入**程式**類別。 取代您在 「 建立 IoT 中心 」 一節中建立 IoT 中樞的連接字串中的版面配置區的值。

        static string connectionString = "{iothub connection string}";
        static string iotHubD2cEndpoint = "messages/events";
        static EventHubClient eventHubClient;

6. 新增下列方法**程式**類別︰

        private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
        {
            var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
            while (true)
            {
                if (ct.IsCancellationRequested) break;
                EventData eventData = await eventHubReceiver.ReceiveAsync();
                if (eventData == null) continue;

                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
            }
        }

    這個方法使用**EventHubReceiver**執行個體，來接收來自所有 IoT 中心裝置-至-雲端接收資料分割。 請注意如何通過`DateTime.Now`參數，當您建立**EventHubReceiver**物件，以便只會收到開始後所傳送的郵件。 這個篩選器，實用的測試環境中，讓您瞭解郵件的目前的設定。 生產環境中您的程式碼應該確認它處理的所有郵件。 如需詳細資訊，請參閱[如何處理 IoT 中心裝置至雲端訊息][lnk-process-d2c-tutorial]教學課程。

7. 最後，將下列幾行新增至**主要**的方法︰

        Console.WriteLine("Receive messages. Ctrl-C to exit.\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, iotHubD2cEndpoint);

        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;

        CancellationTokenSource cts = new CancellationTokenSource();

        System.Console.CancelKeyPress += (s, e) =>
        {
          e.Cancel = true;
          cts.Cancel();
          Console.WriteLine("Exiting...");
        };

        var tasks = new List<Task>();
        foreach (string partition in d2cPartitions)
        {
           tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }  
        Task.WaitAll(tasks.ToArray());

## <a name="create-a-simulated-device-app"></a>建立模擬的裝置應用程式

在此區段中，您可以建立 Windows 主控台應用程式的模擬會裝置至雲端郵件傳送給 IoT 中樞的裝置。

1. 在 Visual Studio 中，新增 Visual C# Windows 傳統桌面專案至目前的方案，使用**主控台應用程式**專案範本。 請確定.NET Framework 版本是 4.5.1 或更新版本。 專案**SimulatedDevice**的名稱。

    ![新的 Visual C# Windows 傳統桌面專案][10]

2. 在方案總管中**SimulatedDevice**專案，以滑鼠右鍵按一下，然後再按一下 [**管理 Nuget 套件**。

3. 在**Nuget 套件管理員**] 視窗中，選取 [**瀏覽**、 搜尋**Microsoft.Azure.Devices.Client**、 選取 [**安裝**] 以安裝**Microsoft.Azure.Devices.Client**套件，並接受使用規定。 此程序下載、 安裝]，並新增[Azure IoT-裝置 SDK Nuget 套件]的參考[lnk-device-nuget]和其相依性。

4. 新增下列`using` **Program.cs**檔案頂端的陳述式︰

        using Microsoft.Azure.Devices.Client;
        using Newtonsoft.Json;

5. 將下列欄位加入**程式**類別。 取代預留位置值，以您在 「 建立 IoT 中心 」 區段中，擷取 IoT 中心主機名稱，而且裝置機碼擷取在 「 建立裝置的身分識別 」 區段。

        static DeviceClient deviceClient;
        static string iotHubUri = "{iot hub hostname}";
        static string deviceKey = "{device key}";

6. 新增下列方法**程式**類別︰

        private static async void SendDeviceToCloudMessagesAsync()
        {
            double avgWindSpeed = 10; // m/s
            Random rand = new Random();

            while (true)
            {
                double currentWindSpeed = avgWindSpeed + rand.NextDouble() * 4 - 2;

                var telemetryDataPoint = new
                {
                    deviceId = "myFirstDevice",
                    windSpeed = currentWindSpeed
                };
                var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
                var message = new Message(Encoding.ASCII.GetBytes(messageString));

                await deviceClient.SendEventAsync(message);
                Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, messageString);

                Task.Delay(1000).Wait();
            }
        }

    這個方法會傳送新裝置至雲端郵件每秒。 郵件包含 JSON 序列化物件，與裝置識別碼的隨機產生的數字以模擬風速度感應器。

7. 最後，將下列幾行新增至**主要**的方法︰

        Console.WriteLine("Simulated device\n");
        deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

        SendDeviceToCloudMessagesAsync();
        Console.ReadLine();

  根據預設，**建立**方法會建立 AMQP 通訊協定通訊使用 IoT 中心**DeviceClient**執行個體。 若要使用的 HTTP 通訊協定，請使用**建立**方法可讓您指定的通訊協定的覆寫。 如果您使用的 HTTP 通訊協定，您也應該新增至您的專案包含**System.Net.Http.Formatting**命名空間**Microsoft.AspNet.WebApi.Client** Nuget 套件。

本教學課程中會帶領您完成建立 IoT 中心裝置用戶端的步驟。 您也可以使用[連線 Azure IoT 中樞的服務][lnk-connected-service]將必要的程式碼新增至您的裝置用戶端應用程式的 Visual Studio 副檔名。


> [AZURE.NOTE] 若要保留項目簡單，此教學課程並未實作任何重試原則。 在實際執行程式碼，您應該實作重試原則 （例如指數輪詢），建議在[暫時性錯誤處理]的 MSDN 文章[lnk-transient-faults]。

## <a name="run-the-applications"></a>執行應用程式

您已準備好執行應用程式。

1.  在 Visual Studio 中，在方案總管中，以滑鼠右鍵按一下您的方案，然後按一下**設定啟動專案**。 選取**多個啟動專案**]，然後選取**開始**用**ReadDeviceToCloudMessages**和**SimulatedDevice**專案動作。

    ![啟動專案屬性][41]

2.  按**F5**啟動執行兩個應用程式。 主控台輸出從**SimulatedDevice**應用程式中顯示您模擬的裝置傳送給您 IoT 中樞的郵件。 主控台輸出從**ReadDeviceToCloudMessages**應用程式中會顯示您 IoT 中心收到的郵件。

    ![從應用程式的主控台輸出][42]

3. [Azure 入口網站]中的 [**使用狀況**] 磚[lnk-portal]的中樞的郵件數︰

    ![Azure 入口網站的 [使用狀況] 磚][43]


## <a name="next-steps"></a>後續步驟

在本教學課程中，您 IoT 中心中設定 Azure] 入口網站，以及然後中樞的身分識別登錄中建立裝置身分識別。 您可以使用此裝置身分識別來啟用裝置至雲端郵件傳送到中樞模擬的裝置應用程式。 您也會建立顯示中樞收到的郵件應用程式。 

若要繼續快速入門 IoT 中心，並瀏覽其他 IoT 案例，請參閱︰

- [連接您的裝置][lnk-connect-device]
- [開始使用裝置管理][lnk-device-management]
- [快速入門 IoT 閘道器 SDK][lnk-gateway-SDK]

若要瞭解如何將延伸 IoT 方案和小數位數的裝置至雲端訊息，請參閱[處理裝置至雲端訊息][lnk-process-d2c-tutorial]教學課程。

<!-- Images. -->
[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png
[10]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp1.png
[11]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp2.png
[12]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp3.png

<!-- Links -->
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-servicebus-nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-device-nuget]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-connected-service]: https://visualstudiogallery.msdn.microsoft.com/e254a3a5-d72e-488e-9bd3-8fee8e0cd1d6
[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
