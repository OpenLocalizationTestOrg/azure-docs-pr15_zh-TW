<properties
    pageTitle="上傳檔案從使用 IoT 中心裝置 |Microsoft Azure"
    description="請遵循此教學課程以瞭解如何上傳檔案從裝置使用 Azure IoT 中心 C#。"
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
     ms.date="06/21/2016"
     ms.author="elioda"/>

# <a name="tutorial-how-to-upload-files-from-devices-to-the-cloud-with-iot-hub"></a>教學課程︰ 如何上傳檔案從裝置 IoT 中樞的雲端

## <a name="introduction"></a>簡介

Azure IoT 中心完全受管理的服務，讓可靠，而安全數百萬 IoT 裝置與應用程式之間的雙向通訊後端。 上一個教學課程 （[快速入門 IoT 中心]和[雲端-裝置以傳送訊息 IoT 中樞]） 說明基本至雲端的裝置，雲端-裝置訊息功能 IoT] 中心內，與[程序裝置-雲端郵件]教學課程說明可靠儲存 Azure blob 儲存體中的 [裝置至雲端郵件的方式。 不過，在某些情況下無法輕鬆對應您的裝置傳送到較小的裝置至雲端郵件 IoT 中心接受的資料。 範例包括大型檔案，包含圖像、 視訊、 取樣高頻率震動資料，或是在包含的某種前置資料。 這些檔案通常是使用工具，例如[Azure 資料工廠]或[Hadoop]堆疊雲端中處理批次。 從裝置的檔案上傳慣用傳送事件時，您還是可以使用 IoT 中心的安全性和可靠性功能。

本教學課程中會根據在[雲端的裝置以傳送訊息 IoT 中心]教學課程中，顯示您如何使用檔案上傳的功能 IoT 中樞的程式碼。 顯示您如何安全地將裝置提供 Azure blob URI 的上傳檔案]，並瞭解如何使用 IoT 中心檔案上傳通知觸發處理您的應用程式後端] 中的檔案。

在本教學課程結尾處，您會執行兩個 Windows 主控台應用程式︰

* **SimulatedDevice**，修改[雲端-裝置以傳送訊息 IoT 中心]教學課程，其上傳至使用 SA URI IoT 中心所提供的儲存空間的檔案中所建立的應用程式版本。
* **ReadFileUploadNotification**，會從您 IoT 中心接收檔案上傳通知。

> [AZURE.NOTE] IoT 中心會透過 Azure IoT 裝置 Sdk 支援許多裝置平台和語言 （包括 C、 Java 和 Javascript）。 請參閱[Azure IoT 開發人員中心]如何連接您的裝置，若要在本教學課程中，顯示的程式碼，並通常 Azure IoT 中樞的逐步指示。

若要完成此教學課程中，您需要下列項目︰

+ Microsoft Visual Studio 2015，

+ 使用中的 Azure 帳戶。 (如果您沒有帳戶，您可以建立一個[免費的帳戶][lnk-free-trial]在幾分鐘的時間。)

## <a name="associate-an-azure-storage-account-to-iot-hub"></a>建立到 IoT 集線器 Azure 儲存體帳戶之間的關聯

模擬的裝置上傳至 blob 的檔案，因為您必須擁有[Azure 儲存體]帳戶 IoT 中心與相關聯。 當您將 Azure 儲存體帳戶關聯 IoT 中心時，中樞可以產生 SA URI 裝置可用於安全地將檔案上傳至 blob 容器。 IoT 中心服務與裝置 Sdk 進行共同作業的程序會產生 SA URI，可供使用上傳檔案所使用的裝置。

[設定檔案上傳使用 Azure 入口網站]中的指示，請依照下列[lnk-configure-upload]關聯到 IoT 集線器 Azure 儲存體帳戶。

## <a name="upload-a-file-from-a-simulated-device"></a>將檔案從模擬的裝置上傳

在此區段中，您可以修改模擬的裝置應用程式，您在[雲端的裝置以傳送訊息 IoT 中樞]接收來自 IoT 中心雲端-裝置訊息。

1. 在 Visual Studio 中，以滑鼠右鍵按一下**SimulatedDevice**專案，按一下 [**新增**]，然後按一下**現有的項目**。 瀏覽至圖像檔案，並將其加入您的專案中。 本教學課程假設為圖像`image.jpg`。

2. 在圖像中，以滑鼠右鍵按一下，然後按一下 [**摘要資訊**。 請確定，**複製到輸出目錄**設定為 [**永遠複製**。

    ![][1]

3. **Program.cs**檔案中，請在檔案頂端新增下列陳述式︰

        using System.IO;

4. 新增下列方法**程式**類別︰
         
        private static async void SendToBlobAsync()
        {
            string fileName = "image.jpg";
            Console.WriteLine("Uploading file: {0}", fileName);
            var watch = System.Diagnostics.Stopwatch.StartNew();

            using (var sourceData = new FileStream(@"image.jpg", FileMode.Open))
            {
                await deviceClient.UploadToBlobAsync(fileName, sourceData);
            }

            watch.Stop();
            Console.WriteLine("Time to upload file: {0}ms\n", watch.ElapsedMilliseconds);
        }

    `UploadToBlobAsync`方法所需的檔案名稱和資料流來源的檔案上傳和控點上的傳至儲存體。 主控台應用程式顯示上傳檔案所花費的時間。

5. 新增下列方法，**主要**方法中的前`Console.ReadLine()`行︰

        SendToBlobAsync();

> [AZURE.NOTE] 為了簡單起見本教學課程並未實作任何重試原則。 在實際執行程式碼，您應該實作重試原則 （例如指數輪詢），建議在[暫時性錯誤處理]的 MSDN 文章。

## <a name="receive-a-file-upload-notification"></a>將收到的檔案上傳通知

在此區段中，您撰寫從 IoT 中樞接收檔案上傳通知訊息的 Windows 主控台應用程式。

1. 在目前的 Visual Studio 解決方案中，使用**主控台應用程式**專案範本建立新的 Visual C# Windows 專案。 專案**ReadFileUploadNotification**的名稱。

    ![在 Visual Studio 中的新專案][2]

2. 在方案總管中**ReadFileUploadNotification**專案，以滑鼠右鍵按一下，然後再按一下 [**管理 NuGet 套件**。

    隨後便會顯示 [管理 NuGet 套件] 視窗。

2. 搜尋`Microsoft.Azure.Devices`、 按一下 [**安裝**]，並接受使用規定。 

    此下載、 安裝]，並**ReadFileUploadNotification**專案中新增[Azure IoT-服務 SDK NuGet 套件]的參考。

3. **Program.cs**檔案中，請在檔案頂端新增下列陳述式︰

        using Microsoft.Azure.Devices;

4. 將下列欄位加入**程式**類別。 替代[快速入門 IoT 中樞]的 IoT 中心連接字串的版面配置區值︰

        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
        
5. 新增下列方法**程式**類別︰
   
        private async static Task ReceiveFileUploadNotificationAsync()
        {
            var notificationReceiver = serviceClient.GetFileNotificationReceiver();

            Console.WriteLine("\nReceiving file upload notification from service");
            while (true)
            {
                var fileUploadNotification = await notificationReceiver.ReceiveAsync();
                if (fileUploadNotification == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received file upload noticiation: {0}", string.Join(", ", fileUploadNotification.BlobName));
                Console.ResetColor();

                await notificationReceiver.CompleteAsync(fileUploadNotification);
            }
        }

    請注意，接收圖樣接收雲端-裝置郵件從裝置應用程式中使用相同的項目。

6. 最後，將下列幾行新增至**主要**的方法︰

        Console.WriteLine("Receive file upload notifications\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
        ReceiveFileUploadNotificationAsync().Wait();
        Console.ReadLine();

## <a name="run-the-applications"></a>執行應用程式

現在您已經準備好執行應用程式。

1. 在 Visual Studio 中，以滑鼠右鍵按一下您的方案，然後選取 [**設定啟動專案**。 選取**多個啟動專案**]，然後選取**ReadFileUploadNotification**和**SimulatedDevice****啟動**動作。

2. 按**F5**。 兩個應用程式應該會啟動。 您應該會看到一個主控台應用程式和所接收的其他主控台應用程式上傳通知訊息中完成上傳。 您可以使用[Azure 入口網站]或 Visual Studio 伺服器總管 Azure 儲存體帳戶中檢查存在的上傳的檔案。

  ![][50]


## <a name="next-steps"></a>後續步驟

在本教學課程中，您將學會如何運用 IoT 中樞的簡化從裝置的檔案上傳的檔案上傳功能。 您可以繼續探索 IoT 中心功能及案例下列文章︰

- [以程式設計方式建立 IoT 中心][lnk-create-hub]
- [C SDK 簡介][lnk-c-sdk]
- [IoT 中心 Sdk][lnk-sdks]

若要進一步探索 IoT 中樞的功能，請參閱︰

- [模擬 IoT 閘道器 SDK 的裝置][lnk-gateway]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png
[1]: ./media/iot-hub-csharp-csharp-file-upload/image-properties.png
[2]: ./media/iot-hub-csharp-csharp-file-upload/create-identity-csharp1.png

<!-- Links -->

[Azure 入口網站]: https://portal.azure.com/

[Azure 資料工廠]: https://azure.microsoft.com/documentation/services/data-factory/
[Hadoop]: https://azure.microsoft.com/documentation/services/hdinsight/

[雲端-裝置以傳送訊息 IoT 中心]: iot-hub-csharp-csharp-c2d.md
[處理程序裝置至雲端的郵件]: iot-hub-csharp-csharp-process-d2c.md
[快速入門 IoT 中心]: iot-hub-csharp-csharp-getstarted.md
[Azure IoT 開發人員中心]: http://www.azure.com/develop/iot

[暫時錯誤處理]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure 儲存體]: ../storage/storage-create-storage-account.md#create-a-storage-account
[lnk-configure-upload]: iot-hub-configure-file-upload.md
[Azure IoT-服務 SDK NuGet 套件]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md


