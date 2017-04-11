<properties
    pageTitle="建立使用 Azure CLI IoT 中心 |Microsoft Azure"
    description="請遵循此文件，以建立使用 Azure 命令列介面 IoT 中心。"
    services="iot-hub"
    documentationCenter=".net"
    authors="BeatriceOltean"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="multiple"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/21/2016"
     ms.author="boltean"/>

# <a name="create-an-iot-hub-using-azure-cli"></a>建立使用 Azure CLI IoT 中心

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>簡介

您可以使用 Azure 命令列介面，來建立和管理 Azure IoT 集線器以程式設計方式。 本文將示範如何使用 Azure CLI 建立 IoT 中心。

若要完成此教學課程您需要下列項目︰

- 使用中的 Azure 帳戶。 如果您沒有帳戶，您可以建立一個[免費的帳戶][lnk-free-trial]在幾分鐘。
- [Azure CLI 0.10.4] [lnk-CLI-install]或更新版本。 如果您已經有 Azure CLI 可以驗證目前的版本，在命令提示字元中使用下列命令︰
```
    azure --version
```

> [AZURE.NOTE] Azure 有兩種不同的部署模型建立及使用的資源︰ [Azure 資源管理員] 及 [傳統](../resource-manager-deployment-model.md)。 Azure CLI 必須是 Azure 資源管理員模式︰
```
    azure config mode arm
```

## <a name="set-your-azure-account-and-subscription"></a>設定您的 Azure 帳戶及訂閱 

1. 在命令提示字元輸入下列命令的登入
```
    azure login
```
使用建議的網頁瀏覽器和程式碼來驗證。

2. 如果您有多個 Azure 訂閱，連線到 Azure 會授與存取權與您的認證相關聯的所有 Azure 訂閱。 您可以檢視 Azure 訂閱]，以及哪一種預設值，使用 [] 命令
```
    azure account list 
```

若要設定您想要執行命令使用的其餘部分的訂閱內容

```
    azure account set <subscription name>
```

3. 如果您沒有您可以建立一個命名的**exampleResourceGroup**資源群組 
```
    azure group create -n exampleResourceGroup -l westus
```

> [AZURE.TIP] 文章︰[使用 Azure CLI 管理 Azure 資源與資源群組][lnk-CLI-arm]提供有關如何使用 Azure CLI 管理 Azure 資源的詳細資訊。 


## <a name="create-an-iot-hub"></a>建立 IoT 中心

必要的參數︰

```
 azure iothub create -g <resource-group> -n <name> -l <location> -s <sku-name> -u <units>  
    - <resourceGroup> The resource group name (case insensitive alphanumeric, underscore and hyphen, 1-64 length)
    - <name> (The name of the IoT hub to be created. The format is case insensitive alphanumeric, underscore and hyphen, 3-50 length )
    - <location> (The location (azure region/datacenter) where the IoT hub will be provisioned.
    - <sku-name> (The name of the sku, one of: [F1, S1, S2, S3] etc. For the latest full list refer to the pricing page for IoT Hub.
    - <units> (The number of provisioned units. Range : F1 [1-1] : S1, S2 [1-200] : S3 [1-10]. IoT Hub units are based on your total message count and the number of devices you want to connect.)
```
若要查看所有參數可供建立您可以使用 [說明] 命令在命令提示字元
```
    azure iothub create -h 
```
簡單範例︰

 若要建立 IoT 中心中名為 [ **exampleIoTHubName**資源群組**exampleResourceGroup**只要執行以下命令
```
    azure iothub create -g exampleResourceGroup -n exampleIoTHubName -l westus -k s1 -u 1
```

> [AZURE.NOTE] 此 Azure CLI 命令會建立會向您收費 S1 標準 IoT 中心。 您可以刪除 IoT 中心**exampleIoTHubName**使用下列命令 
```
    azure iothub delete -g exampleResourceGroup -n exampleIoTHubName
```


## <a name="next-steps"></a>後續步驟
若要瞭解更多有關開發 IoT 集線器，請參閱下列各項︰
- [IoT 中心 Sdk][lnk-sdks]

若要進一步探索 IoT 中樞的功能，請參閱︰

- [使用 [Azure 入口網站管理 IoT 中心][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-CLI-install]: ../xplat-cli-install.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-CLI-arm]: ../xplat-cli-azure-resource-manager.md

[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-portal]: iot-hub-create-through-portal.md 
