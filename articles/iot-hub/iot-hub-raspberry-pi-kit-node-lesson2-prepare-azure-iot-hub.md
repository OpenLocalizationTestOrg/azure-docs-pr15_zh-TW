<properties
 pageTitle="建立您 IoT 中心和註冊您覆盆子 Pi 3 |Microsoft Azure"
 description="建立資源群組 Azure IoT] 中心內，並在使用 Azure CLI Azure IoT 中心註冊您 Pi。"
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

# <a name="22-create-your-iot-hub-and-register-your-raspberry-pi-3"></a>2.2 建立 IoT 中心和註冊您覆盆子 Pi 3

## <a name="221-what-you-will-do"></a>2.2.1 您將會執行的動作

- 建立資源群組。
- [資源] 群組中，建立您 Azure IoT 集散地。
- 若要使用 Azure CLI Azure IoT 中心新增您覆盆子 Pi 3。

當您將您 Pi 新增至您 IoT 中心使用 Azure CLI 時，服務就會產生驗證服務您 pi 的金鑰。 如果您符合任何問題，請搜尋中的[疑難排解頁面](iot-hub-raspberry-pi-kit-node-troubleshooting.md)的解決方案。

## <a name="222-what-you-will-learn"></a>2.2.2 您將學習什麼

- 若要建立 Azure IoT 中心使用 Azure CLI 方式。
- 如何在您 Azure IoT 中心您 pi 建立裝置的身分識別。

## <a name="223-what-you-need"></a>2.2.3 您需要的什麼

- Azure 帳戶
- 在 Mac 或 Windows Azure CLI 安裝的電腦

## <a name="224-create-your-azure-iot-hub"></a>2.2.4 建立 Azure IoT 中心

Azure IoT 中心可協助您連線、 監控和管理數百萬 IoT 資產。 若要建立 Azure IoT 集線器，請遵循下列步驟︰

1. 執行下列命令以登入您的 Azure 帳戶︰

    ```bash
    az login
    ```

    順利登入後，會列出所有的可用 Azure 訂閱。

2. 設定預設要使用，請執行下列命令的 Azure 訂閱︰

    ```bash
    az account set -n {subscription id or name}
    ```

    訂閱 ID 或名稱，請參閱的輸出`az login`。

3. 註冊提供者來執行下列命令︰

    ```bash
    az resource provider register -n "Microsoft.Devices"
    ```

    您必須註冊提供者，才能部署 Azure 提供者提供的資源。

    > [AZURE.NOTE] 大部分提供者是註冊會自動以 Azure 入口網站或 Azure CLI 您使用的，但非全部。 如需有關提供者的詳細資訊，請參閱[疑難排解常見 Azure 部署錯誤與 Azure 資源管理員](../resource-manager-common-deployment-errors.md)

4. 建立資源群組名稱 iot 樣本西美國地區，藉由執行下列命令︰

    ```bash
    az resource group create --name iot-sample --location westus
    ```

5. 建立 IoT 中心 iot 範例資源] 群組中，執行下列命令︰

    ```bash
    az iot hub create --name {my hub name} --resource-group iot-sample
    ```

    您建立 IoT 中樞的預設版本是**F1**，這是**免費**。 如需詳細資訊，請參閱[Azure IoT 中心價格](https://azure.microsoft.com/pricing/details/iot-hub/)。

> [AZURE.NOTE] 您 IoT 中樞的名稱必須是全域唯一的。
>
> 您可以建立 Azure 訂閱 Azure IoT 中心只有一個**F1**版本。

## <a name="225-register-your-pi-in-your-iot-hub"></a>2.2.5 IoT 中心中註冊您 Pi

每個傳送/接收郵件從 Azure IoT 中心的裝置必須註冊唯一識別碼。

在您 Azure IoT 中心登錄您 Pi 執行以下命令︰

```bash
az iot device create --device-id myraspberrypi --hub {my hub name} --resource-group iot-sample
```

## <a name="226-summary"></a>2.2.6 摘要

您已建立 Azure IoT 中心，並登錄您 Pi 的裝置身分識別，在您 Azure IoT 中心。 您準備好要移至下一個課程，瞭解如何從您 Pi 的郵件傳送到 IoT 集線器。

## <a name="next-steps"></a>後續步驟

現在，您準備好要開始教學單元 3，開始[建立 Azure 函數應用程式和 Azure 儲存體帳戶來處理程序及儲存 IoT 中樞的郵件](iot-hub-raspberry-pi-kit-node-lesson3-deploy-resource-manager-template.md)。