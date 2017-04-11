<properties
    pageTitle="快速入門盡相同 |Microsoft Azure"
    description="本教學課程教您如何使用盡相同"
    services="iot-hub"
    documentationCenter="node"
    authors="fsautomata"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="node"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/13/2016"
     ms.author="elioda"/>

# <a name="tutorial-get-started-with-device-twins-preview"></a>教學課程︰ 開始使用裝置盡相同 （預覽版本）

[AZURE.INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

在本教學課程結尾，您就必須的.NET 和 Node.js 主控台應用程式︰

* **AddTagsAndQuery.sln**，從 [新增標籤及查詢裝置盡相同的後端執行的.NET 應用程式。
* **TwinSimulatedDevice.js**，模擬連線到您 IoT 集線器與先前建立的裝置身分識別的裝置和分析其連線條件 Node.js 應用程式。

> [AZURE.NOTE] 文章[IoT 中心 Sdk] [lnk-hub-sdks]提供各種 Sdk 的相關資訊，您可以使用建置裝置和後端的應用程式。

若要完成此教學課程您需要下列項目︰

+ Microsoft Visual Studio 2015。

+ Node.js 版本 0.10.x 或更新版本。

+ 使用中的 Azure 帳戶。 (如果您沒有帳戶，您可以建立一個[免費的帳戶][lnk-free-trial]在幾分鐘的時間。)

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-the-service-app"></a>建立服務應用程式

在此區段中，您可以建立位置的中繼資料加入雙生**myDeviceId**相關聯的 Node.js 主控台應用程式。 然後儲存在選取裝置中心盡相同位於美國，然後按一下 [項目報告行動數據連線的查詢。

1. 在 Visual Studio 中，將 Visual C# Windows 傳統桌面專案加入至目前的方案，使用**主控台應用程式**專案範本。 專案**AddTagsAndQuery**的名稱。

    ![新的 Visual C# Windows 傳統桌面專案][img-createapp]

2. 在方案總管中**AddTagsAndQuery**專案，以滑鼠右鍵按一下，然後再按一下 [**管理 Nuget 套件**。

3. 在 [ **Nuget 套件管理員**] 視窗中，請確定已核取 [**包含搶鮮版**搜尋**microsoft.azure.devices**、 選取 [**安裝**] 以安裝**Microsoft.Azure.Devices** *預先發行*版本封裝，並接受使用規定。 此程序下載、 安裝，並將[Microsoft Azure IoT 服務 SDK]參考[lnk-nuget-service-sdk]Nuget 封裝並相依性。

    ![Nuget 套件管理員] 視窗][img-servicenuget]

4. 新增下列`using` **Program.cs**檔案頂端的陳述式︰

        using Microsoft.Azure.Devices;

5. 將下列欄位加入**程式**類別。 取代 IoT 中樞在前一節中所建立的連線字串中的版面配置區的值。

        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";

6. 新增下列方法**程式**類別︰

        public static async Task AddTagsAndQuery()
        {
            var twin = await registryManager.GetTwinAsync("myDeviceId");
            var patch =
                @"{
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                        }
                    }
                }";
            await registryManager.UpdateTwinAsync(twin.DeviceId, patch, twin.ETag);

            var query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            var twinsInRedmond43 = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43: {0}", string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));

            query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43 using cellular network: {0}", string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
        }

    **RegistryManager**類別公開互動裝置盡相同，從服務所需的所有方法。 先前的程式碼第一次初始化**registryManager**物件，然後擷取的**myDeviceId**，雙生及最後的想要的位置資訊更新其標籤。

    在更新之後，會執行兩個的查詢︰ 第一個選取只的裝置位於**Redmond43**植物，裝置盡相同，並在第二個修改查詢，以選取也連線到行動電話網路的裝置。

    請注意的上一個的程式碼時，它會建立**查詢**的物件，指定所傳回的文件的最大數目。 **查詢**物件會包含可用來擷取所有結果多次呼叫**GetNextAsTwinAsync**方法**HasMoreResults**布林值屬性。 稱為**GetNextAsJson**的方法是可用的結果不是裝置盡相同，例如的彙總查詢的結果。

7. 最後，將下列幾行新增至**主要**的方法︰

        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddTagsAndQuery().Wait();
        Console.WriteLine("Press Enter to exit.");
        Console.ReadLine();

8. 執行這個應用程式，然後您應該會看到位於**Redmond43** ，而不使用行動電話網路的裝置限制搜尋結果的查詢的所有裝置都要求的查詢結果中裝置。

    ![在視窗中的查詢結果][img-addtagapp]

[下一步] 區段中，您會建立報表的連線資訊，並變更的前一節中的查詢結果的裝置應用程式。

## <a name="create-the-device-app"></a>建立裝置應用程式

在此區段中，您可以建立 Node.js 主控台應用程式連線到**myDeviceId**，為中心，然後更新其雙生報告的內容，包含已連線使用行動電話網路的資訊。

1. 建立新的空白資料夾稱為**reportconnectivity**。 在 [ **reportconnectivity** ] 資料夾中，建立新 package.json 檔案使用下列命令，在命令提示字元 」。 接受所有的預設值︰

    ```
    npm init
    ```

2. 在您命令提示字元**reportconnectivity**資料夾中，執行下列命令以安裝**azure iot 裝置**和**azure iot-裝置 mqtt**套件︰

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. 使用文字編輯器， **reportconnectivity**資料夾中建立新的**ReportConnectivity.js**檔案。

4. 下列程式碼新增至**ReportConnectivity.js**檔案，和替代**{裝置連線字串}**版面配置區以建立**myDeviceId**裝置身分識別所複製連線字串︰

        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;

        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);

        client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');

            client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                var patch = {
                    connectivity: {
                        type: 'cellular'
                    }
                };

                twin.properties.reported.update(patch, function(err) {
                    if (err) {
                        console.error('could not update twin');
                    } else {
                        console.log('twin state reported');
                        process.exit();
                    }
                });
            }
            });
        }
        });

    **用戶端**物件公開您需要進行互動與裝置盡相同，從裝置的所有方法。 程式碼，它會初始化**用戶端**物件之後, 擷取的**myDeviceId**雙生，並更新連線資訊其報告的屬性。

5. 執行裝置應用程式

        node ReportConnectivity.js

    您應該會看到訊息`twin state reported`。

6. 現在，裝置報告其連線資訊，它應該會出現在兩個查詢。 執行一次執行查詢的.NET **AddTagsAndQuery**應用程式。 此時間**myDeviceId**應該出現在兩個查詢的結果。

    ![][img-addtagapp2]

## <a name="next-steps"></a>後續步驟
在本教學課程中，您 Azure 入口網站中，在設定新的 IoT 中心，然後建立中樞的身分識別登錄中的 [裝置身分識別。 您新增為標記的裝置的中繼資料，從後端應用程式，並寫模擬的裝置應用程式的裝置雙生報表裝置連線資訊。 您也可以瞭解如何查詢使用 IoT 中心類似 SQL 查詢語言這項資訊。

使用下列資源，瞭解如何︰

- 從裝置的[快速入門 IoT] 中心]傳送遙測[lnk-iothub-getstarted]教學課程中，
- 設定裝置使用雙生的所需的內容的[使用所需的屬性設定裝置][lnk-twin-how-to-configure]教學課程中，
- 使用[使用直接方法]控制互動的方式 （例如風扇從使用者控制的應用程式開啟） 裝置[lnk-methods-tutorial]教學課程。

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-twin-getstarted/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-twin-getstarted/createnetapp.png
[img-addtagapp]: media/iot-hub-csharp-node-twin-getstarted/addtagapp.png
[img-addtagapp2]: media/iot-hub-csharp-node-twin-getstarted/addtagapp2.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/1.1.0-preview-004

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-c2d-methods.md
[lnk-twin-how-to-configure]: iot-hub-csharp-node-twin-how-to-configure.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

