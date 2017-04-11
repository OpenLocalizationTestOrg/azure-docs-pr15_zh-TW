<properties
    pageTitle="建立使用 REST API IoT 中心 |Microsoft Azure"
    description="請遵循此教學課程以開始使用 REST API 來建立 IoT 中心。"
    services="iot-hub"
    documentationCenter=".net"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/16/2016"
     ms.author="dobett"/>

# <a name="tutorial-create-an-iot-hub-using-a-c-program-and-the-rest-api"></a>教學課程︰ 建立使用 C# 程式和 REST API IoT 中心

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>簡介

您可以使用[IoT 中心資源提供者 REST API] [lnk-rest-api]來建立和管理 Azure IoT 集線器以程式設計方式。 本教學課程教您如何使用 IoT 中心資源提供者 REST API 從 C# 程式建立 IoT 中心。

> [AZURE.NOTE] Azure 有兩種不同的部署模型建立及使用的資源︰ [Azure 資源管理員] 及 [傳統](../resource-manager-deployment-model.md)。  本文涵蓋使用 Azure 資源管理員部署模型。

若要完成此教學課程中，您需要下列項目︰

- Microsoft Visual Studio 2015。
- 使用中的 Azure 帳戶。 <br/>如果您沒有帳戶，您可以建立一個[免費的帳戶][lnk-free-trial]在幾分鐘。
- [Microsoft Azure PowerShell 1.0] [lnk-powershell-install]或更新版本。

[AZURE.INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>準備 Visual Studio 專案

1. 在 Visual Studio 中，建立 Visual C# Windows 專案使用**主控台應用程式**的專案範本。 專案**CreateIoTHubREST**的名稱。

2. 在方案總管中，以滑鼠右鍵按一下您的專案，然後按一下**管理 NuGet 套件**。

3. 在 Nuget 套件管理員中，核取 [**包含搶鮮版**，，然後搜尋**Microsoft.Azure.Management.ResourceManager**。 按一下 [**安裝**]，在 [**校閱變更**按一下**[確定**]，然後按一下 [**我接受]**接受授權。

4. 在 [Nuget 套件管理員] 中，搜尋**Microsoft.IdentityModel.Clients.ActiveDirectory**。  按一下 [**安裝**]，在 [**校閱變更**按一下**[確定**]，然後按一下 [**我接受]**接受授權。

6. 在 Program.cs，取代現有的**使用**陳述式下列動作︰

    ```
    using System;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json;
    using Microsoft.Rest;
    using System.Linq;
    using System.Threading;
    ```
    
7. 在 Program.cs，新增下列靜態變數取代版面配置區的值。 您之前在本教學課程中做筆記的**ApplicationId**、 **SubscriptionId**、 **TenantId**和**密碼**。 **資源群組名稱**是當您建立 IoT 中心時所使用的 [資源] 群組的名稱，可預先現有的資源群組或新 id。 **IoT 中心 name**是您建立，例如**MyIoTHub** （此名稱必須是全域唯一的讓它應該包含您的名稱或縮寫） IoT 中樞的名稱。 **部署名稱**是部署中，例如**Deployment_01**的名稱。

    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    
    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name including your initials}";
    ```

[AZURE.INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="use-the-rest-api-to-create-an-iot-hub"></a>使用 REST API 建立 IoT 中心

使用[IoT 中心 REST API] [lnk-rest-api]在資源的群組建立 IoT 中心。 您也可以使用 REST API，若要變更現有的 IoT 中心。

1. 加入 Program.cs 下列方法︰
    
    ```
    static void CreateIoTHub(string token)
    {
        
    }
    ```

2. 將下列程式碼新增至**CreateIoTHub**方法，以建立具有標頭中的驗證 token 的**HttpClient**物件︰

    ```
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```

3. 描述 [IoT] 中心上建立並產生 JSON 表示**CreateIoTHub**方法新增下列程式碼 (如目前的支援 IoT 中心的位置清單請參閱[Azure 狀態][lnk-status]):

    ```
    var description = new
    {
      name = iotHubName,
      location = "East US",
      sku = new
      {
        name = "S1",
        tier = "Standard",
        capacity = 1
      }
    };
    
    var json = JsonConvert.SerializeObject(description, Formatting.Indented);
    ```

4. 將下列程式碼新增至 [送出 Azure 其餘邀請、 檢查回應，以及擷取您可用來監控工作狀態的部署的 URL 的**CreateIoTHub**方法︰

    ```
    var content = new StringContent(JsonConvert.SerializeObject(description), Encoding.UTF8, "application/json");
    var requestUri = string.Format("https://management.azure.com/subscriptions/{0}/resourcegroups/{1}/providers/Microsoft.devices/IotHubs/{2}?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var result = client.PutAsync(requestUri, content).Result;
      
    if (!result.IsSuccessStatusCode)
    {
      Console.WriteLine("Failed {0}", result.Content.ReadAsStringAsync().Result);
      return;
    }
    
    var asyncStatusUri = result.Headers.GetValues("Azure-AsyncOperation").First();
    ```

5. 等到完成部署使用前一個步驟所擷取的**asyncStatusUri**地址的**CreateIoTHub**方法的結尾新增下列程式碼︰

    ```
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{\"status\":\"Running\"}");
    ```

6. 將下列程式碼來擷取您已建立並列印主控台 IoT 中樞的索引鍵的**CreateIoTHub**方法︰

    ```
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2015-08-15-preview", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;
    
    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```
    
## <a name="complete-and-run-the-application"></a>完成並執行應用程式

您現在可以建立並執行之前呼叫**CreateIoTHub**方法完成應用程式。

1. 將下列程式碼的**主要**方法︰

    ```
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```
    
2. 按一下 [**建立**再**建立的解決方案**。 修正錯誤。

3. 按一下 [**偵錯**]，然後**開始偵錯時，**以執行應用程式]。 可能需要幾分鐘的時間執行部署。

4. 您可以驗證您的應用程式新增的新 IoT 中心造訪[Azure 入口網站]以取得[lnk-azure-portal]及檢視您的資源，或使用**取得 AzureRmResource** PowerShell 指令程式的清單。

> [AZURE.NOTE] 此範例應用程式會將會向您收費 S1 標準 IoT 中心。 當您完成時，您可以刪除透過[Azure 入口網站]的 IoT 中心[lnk-azure-portal]，或當您完成時，使用**移除 AzureRmResource** PowerShell 指令程式。

## <a name="next-steps"></a>後續步驟

現在您已經部署使用 REST API IoT 中心，您可能會想要進一步探索︰

- 閱讀有關的[IoT 中心資源提供者 REST API]功能[lnk-rest-api]。
- 閱讀[Azure 資源管理員的概觀][lnk-azure-rm-overview]若要深入瞭解功能的 Azure 資源管理員。

若要瞭解更多有關開發 IoT 集線器，請參閱下列各項︰

- [C SDK 簡介][lnk-c-sdk]
- [IoT 中心 Sdk][lnk-sdks]

若要進一步探索 IoT 中樞的功能，請參閱︰

- [模擬 IoT 閘道器 SDK 的裝置][lnk-gateway]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: ../powershell-install-configure.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
