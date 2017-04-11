<properties
    pageTitle="建立使用 ARM 範本及 C# IoT 中心 |Microsoft Azure"
    description="請遵循此教學課程以開始建立 IoT 中心與 C# 程式使用 Azure 資源管理員範本。"
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

# <a name="create-an-iot-hub-using-a-c-program-with-an-azure-resource-manager-template"></a>建立使用 Azure 資源管理員範本 C# 程式 IoT 中心

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>簡介

您可以使用 Azure 資源管理員來建立和管理 Azure IoT 集線器以程式設計方式。 本教學課程教您如何使用 Azure 資源管理員範本來建立 IoT 中心從 C# 程式。

> [AZURE.NOTE] Azure 有兩種不同的部署模型建立及使用的資源︰ [Azure 資源管理員] 及 [傳統](../resource-manager-deployment-model.md)。  本文涵蓋使用 Azure 資源管理員部署模型。

若要完成此教學課程中，您需要下列項目︰

- Microsoft Visual Studio 2015。
- 使用中的 Azure 帳戶。 <br/>如果您沒有帳戶，您可以建立一個[免費的帳戶][lnk-free-trial]在幾分鐘。
- [Azure 儲存體帳戶][lnk-storage-account]您可以在此儲存 Azure 資源管理員範本檔案。
- [Microsoft Azure PowerShell 1.0] [lnk-powershell-install]或更新版本。

[AZURE.INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>準備 Visual Studio 專案

1. 在 Visual Studio 中，建立 Visual C# Windows 專案使用**主控台應用程式**的專案範本。 專案**CreateIoTHub**的名稱。

2. 在方案總管中，以滑鼠右鍵按一下您的專案，然後按一下**管理 NuGet 套件**。

3. 在 Nuget 套件管理員中，核取 [**包含搶鮮版**，，然後搜尋**Microsoft.Azure.Management.ResourceManager**。 按一下 [**安裝**]，在 [**校閱變更**按一下**[確定**]，然後按一下 [**我接受]**接受授權。

4. 在 [Nuget 套件管理員] 中，搜尋**Microsoft.IdentityModel.Clients.ActiveDirectory**。  按一下 [**安裝**]，在 [**校閱變更**按一下**[確定**]，然後按一下 [**我接受]**接受授權。

5. 在 Program.cs，取代現有的**使用**陳述式下列動作︰

    ```
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```
    
6. 在 Program.cs，新增下列靜態變數取代版面配置區的值。 您之前在本教學課程中做筆記的**ApplicationId**、 **SubscriptionId**、 **TenantId**和**密碼**。 **您的 Azure 儲存體帳戶名稱**是 Azure 儲存體帳戶名稱儲存 Azure 資源管理員範本檔案的位置。 **資源群組名稱**是當您建立 IoT 中心時所使用的 [資源] 群組的名稱，可預先現有的資源群組或新 id。 **部署名稱**是部署中，例如**Deployment_01**的名稱。

    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    static string storageAddress = "https://{Your storage account name}.blob.core.windows.net";
    static string rgName = "{Resource group name}";
    static string deploymentName = "{Deployment name}";
    ```

[AZURE.INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="submit-an-azure-resource-manager-template-to-create-an-iot-hub"></a>送出 Azure 資源管理員範本，來建立 IoT 中心

使用 JSON 範本參數及參數檔案來建立 IoT 中心資源群組中。 您也可以使用 Azure 資源管理員範本，若要變更現有的 IoT 中心。

1. 在方案總管中，以滑鼠右鍵按一下您的專案，按一下 [**新增**]，然後按一下**新的項目**。 新增 JSON 檔案稱為**template.json**至您的專案。

2. 使用下列資源定義標準 IoT 中樞加入**東亞美國**地區取代**template.json**的內容 (目前清單的支援 IoT 中樞的區域，請參閱[Azure 狀態][lnk-status]):

    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

3. 在方案總管中，以滑鼠右鍵按一下您的專案，按一下 [**新增**]，然後按一下**新的項目**。 新增 JSON 檔案稱為**parameters.json**至您的專案。

4. 設定新的 IoT 中樞的名稱，例如下列參數資訊取代**parameters.json**內容**{您的名稱簡寫} mynewiothub**。 請注意，它應該包含您的名稱或縮寫，讓 IoT 中心名稱必須是全域唯一）︰

    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": { "value": "mynewiothub" }
      }
    }
    ```

5. 在 [**伺服器總管]**中，連線到 Azure 訂閱，並 Azure 儲存體帳戶中建立稱為**範本**的容器。 在 [**屬性**] 面板中，設定**公用讀取權限**的權限**範本**容器至**Blob**。

6. 在**伺服器總管**] 中，以滑鼠右鍵按一下 [**範本**] 容器，然後按一下**檢視 Blob 容器**。 按一下 [**上傳 Blob** ] 按鈕，選取兩個檔案， **parameters.json**然後**templates.json**，，再按一下 [**開啟**上傳 JSON 檔案**範本**容器。 包含 JSON 資料 blob 的 Url 是︰

    ```
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.blob.core.windows.net/templates/template.json
    ```

7. 加入 Program.cs 下列方法︰
    
    ```
    static void CreateIoTHub(ResourceManagementClient client)
    {
        
    }
    ```

5. 新增下列程式碼提交範本與參數檔案至 Azure 資源管理員**CreateIoTHub**方法︰

    ```
    var createResponse = client.Deployments.CreateOrUpdate(
        rgName,
        deploymentName,
        new Deployment()
        {
          Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink
            {
              Uri = storageAddress + "/templates/template.json"
            },
            ParametersLink = new ParametersLink
            {
              Uri = storageAddress + "/templates/parameters.json"
            }
          }
        });
    ```

6. 新增下列程式碼會顯示狀態，以及新的 IoT 中樞的按鍵**CreateIoTHub**方法︰

    ```
    string state = createResponse.Properties.ProvisioningState;
    Console.WriteLine("Deployment state: {0}", state);

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
    }
    Console.WriteLine(createResponse.Properties.Outputs);
    ```

## <a name="complete-and-run-the-application"></a>完成並執行應用程式

您現在可以建立並執行之前呼叫**CreateIoTHub**方法完成應用程式。

1. 將下列程式碼的**主要**方法︰

    ```
    CreateIoTHub(client);
    Console.ReadLine();
    ```
    
2. 按一下 [**建立**再**建立的解決方案**。 修正錯誤。

3. 按一下 [**偵錯**]，然後**開始偵錯時，**以執行應用程式]。 可能需要幾分鐘的時間執行部署。

4. 您可以驗證您的應用程式新增的新 IoT 中心造訪[Azure 入口網站]以取得[lnk-azure-portal]及檢視您的資源，或使用**取得 AzureRmResource** PowerShell 指令程式的清單。

> [AZURE.NOTE] 此範例應用程式會將會向您收費 S1 標準 IoT 中心。 您可以刪除透過[Azure 入口網站]的 IoT 中心[lnk-azure-portal]，或當您完成時，使用**移除 AzureRmResource** PowerShell 指令程式。

## <a name="next-steps"></a>後續步驟

現在您已經部署 Azure 資源管理員範本與 C# 程式 IoT 中心，您可能會想要進一步探索︰

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
[lnk-storage-account]: ../storage/storage-create-storage-account.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
