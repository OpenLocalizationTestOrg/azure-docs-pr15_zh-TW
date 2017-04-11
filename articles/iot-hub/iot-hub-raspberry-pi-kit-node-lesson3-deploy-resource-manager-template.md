<properties
 pageTitle="建立 Azure 儲存體帳戶和 Azure 函數應用程式 |Microsoft Azure"
 description="Azure 函數應用程式接聽 Azure IoT 中心事件，處理內送郵件，並將它們寫入 Azure 資料表儲存體。"
 services="iot-hub"
 documentationCenter=""
 authors="shizn"
 manager="timlt"
 tags=""
 keywords=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/21/2016"
 ms.author="xshi"/>

# <a name="31-create-an-azure-function-app-and-azure-storage-account"></a>3.1 建立 Azure 函數應用程式和 Azure 儲存體帳戶

[Azure 函數](../../articles/azure-functions/functions-overview.md)是方案，輕鬆地執行程式碼，稱為 「 函數 」，在雲端的小部分。 Azure 函數應用程式會裝載您 Azure 中的函數的執行。

## <a name="311-what-will-you-do"></a>3.1.1 您會執行什麼動作

您可以使用 [Azure 資源管理員範本來建立 Azure 函數應用程式和 Azure 儲存體帳戶。 Azure 函數應用程式接聽 Azure IoT 中心事件，處理內送郵件，並將它們寫入 Azure 資料表儲存體。 如果您符合任何問題，請搜尋中的[疑難排解頁面](iot-hub-raspberry-pi-kit-node-troubleshooting.md)的解決方案。

## <a name="312-what-will-you-learn"></a>3.1.2 您將學習什麼

- 如何使用[Azure 資源管理員](../../articles/azure-resource-manager/resource-group-overview.md)部署 Azure 資源。
- 如何使用 Azure 函數應用程式，以處理 IoT 中樞的訊息，並填入 Azure 資料表儲存體中的表格。

## <a name="313-what-do-you-need"></a>3.1.3 您需要什麼

- 您必須已順利完成的上一個課程︰[開始使用您的覆盆子 Pi 3](iot-hub-raspberry-pi-kit-node-get-started.md)並[建立您的 Azure IoT 集散地](iot-hub-raspberry-pi-kit-node-get-started.md)。

## <a name="314-open-the-sample-app"></a>3.1.4 開啟範例應用程式

開啟 Visual Studio 程式碼中的範例專案藉由執行下列命令︰

```bash
cd Lesson3
code .
```

![Repo 結構](media/iot-hub-raspberry-pi-lessons/lesson3/repo_structure.png)

- `app.js`檔案中`app`子資料夾是索引鍵的來源檔案。 來源檔案包含要傳送的郵件 20 時間 IoT 中心和閃爍會傳送每封郵件的 LED 的程式碼。
- `arm-template.json`檔案是包含 Azure 函數應用程式和 Azure 儲存體帳戶 Azure 資源管理員範本。
- `arm-template-param.json` Azure 資源管理員範本所使用的設定檔是。
- `ReceiveDeviceMessages`子資料夾包含 Azure 函數的 Node.js 程式碼。

## <a name="315-configure-azure-resource-manager-templates-and-create-resources-in-azure"></a>3.1.5 設定 Azure 資源管理員範本，並建立 Azure 中的資源

更新`arm-template-param.json`Visual Studio 程式碼中的檔案。

![Azure 資源管理員範本參數](media/iot-hub-raspberry-pi-lessons/lesson3/arm_para.png)

- **{我中心名稱}**在[教學單元 2](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md)所指定取代**[您 IoT 中心名稱]** 。
- 取代任何您想要的前置詞中的**[前置字元字串的新資源]** 。 前置詞可確保資源名稱是全域唯一若要避免發生衝突。 不要使用虛線或初始前置詞中的數字。

> [AZURE.NOTE] 您不需要`azure_storage_connection_string`此區段中。 保留現狀。

更新後`arm-template-param.json`檔案時，請執行下列命令以 Azure 部署資源︰

```bash
az resource group deployment create --template-file-path arm-template.json --parameters-file-path arm-template-param.json -g iot-sample -n mydeployment
```

若要建立這些資源需要約 5 分鐘的時間。 在進行資源建立時，您可以移至下一節。

## <a name="316-summary"></a>3.1.6 摘要

您已建立 Azure 函數應用程式以處理 IoT 中心訊息和 Azure 儲存體帳戶儲存這些訊息。 您可以移至下一節部署及執行範例，將裝置至雲端郵件傳送到您 Pi。

## <a name="next-steps"></a>後續步驟

[3.2 捨位執行範例應用程式，將裝置至雲端郵件傳送到您覆盆子 Pi 3](iot-hub-raspberry-pi-kit-node-lesson3-run-azure-blink.md)

