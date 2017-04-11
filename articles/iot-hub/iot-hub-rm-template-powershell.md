<properties
    pageTitle="建立使用 Azure 資源管理員範本與 PowerShell IoT 中心 |Microsoft Azure"
    description="請遵循此教學課程以開始使用建立 IoT 中心使用 PowerShell 的 Azure 資源管理員範本。"
    services="iot-hub"
    documentationCenter=".net"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="multiple"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/07/2016"
     ms.author="dobett"/>

# <a name="create-an-iot-hub-using-powershell"></a>建立使用 PowerShell IoT 中心

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>簡介

您可以使用 Azure 資源管理員來建立和管理 Azure IoT 集線器以程式設計方式。 本教學課程教您如何使用 Azure 資源管理員範本來建立 IoT 中心使用 PowerShell。

> [AZURE.NOTE] Azure 有兩種不同的部署模型建立及使用的資源︰ [Azure 資源管理員] 及 [傳統](../resource-manager-deployment-model.md)。  本文涵蓋使用 Azure 資源管理員部署模型。

若要完成此教學課程您需要下列項目︰

- 使用中的 Azure 帳戶。 <br/>如果您沒有帳戶，您可以建立一個[免費的帳戶][lnk-free-trial]在幾分鐘。
- [Microsoft Azure PowerShell 1.0] [lnk-powershell-install]或更新版本。

> [AZURE.TIP] [使用 PowerShell 的 Azure 與 Azure 資源管理員]的文件[lnk-powershell-arm]提供有關如何使用 PowerShell 指令碼和 Azure 資源管理員範本來建立 Azure 資源的詳細資訊。 

## <a name="connect-to-your-azure-subscription"></a>連線到您 Azure 的訂閱

在 PowerShell 命令提示字元中，輸入下列命令以登入您的 Azure 訂閱︰

```
Login-AzureRmAccount
```

若要找出您可以在其中部署 IoT 中樞和目前支援的 API 版本，您可以使用下列命令︰

```
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).Locations
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).ApiVersions
```

建立包含在其中一個支援 IoT 集線器位置中使用下列命令 IoT 中心的資源群組。 此範例會建立一個稱為**MyIoTRG1**的資源群組︰

```
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="submit-an-azure-resource-manager-template-to-create-an-iot-hub"></a>送出 Azure 資源管理員範本，來建立 IoT 中心

若要在資源的群組建立 IoT 中心使用 JSON 範本。 您也可以使用 Azure 資源管理員範本，若要變更現有的 IoT 中心。

1. 使用文字編輯器建立稱為**template.json**使用下列的資源定義，以建立新的標準 IoT 中心 Azure 資源管理員範本。 此範例中新增 IoT 中心**東亞美國**地區、 事件中心相容端點，建立兩個消費者群組 （**cg1**和**cg2**），以及使用**2016年-02-03** API 版本。 此範本也需要您做為參數，稱為**hubName**傳入 IoT 中心名稱。 針對目前的支援 IoT 中心位置的清單，請參閱[Azure 狀態][lnk-status]。

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
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg1')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg2')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
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

2. Azure 資源管理員範本檔案儲存在您的本機電腦上。 假設您將其儲存在一個稱為**c:\templates**資料夾中。

3. 執行下列命令以部署新 IoT 中心，做為參數傳遞 IoT 中心的名稱。 在此範例中，IoT 中樞的名稱為**abcmyiothub** （請注意，它應該包含您的名稱或縮寫，讓此名稱必須是全域唯一）︰

    ```
    New-AzureRmResourceGroupDeployment -ResourceGroupName MyIoTRG1 -TemplateFile C:\templates\template.json -hubName abcmyiothub
    ```

4. 輸出會顯示您所建立的 IoT 中樞的索引鍵。

5. 您可以驗證您的應用程式新增的新 IoT 中心造訪[Azure 入口網站]以取得[lnk-azure-portal]及檢視您的資源，或使用**取得 AzureRmResource** PowerShell 指令程式的清單。

> [AZURE.NOTE] 此範例應用程式會將會向您收費 S1 標準 IoT 中心。 您可以刪除透過[Azure 入口網站]的 IoT 中心[lnk-azure-portal]，或當您完成時，使用**移除 AzureRmResource** PowerShell 指令程式。

## <a name="next-steps"></a>後續步驟

現在您已經部署使用 PowerShell 的 Azure 資源管理員範本 IoT 中心，您可能會想要進一步探索︰

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
[lnk-powershell-arm]: ../powershell-azure-resource-manager.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
