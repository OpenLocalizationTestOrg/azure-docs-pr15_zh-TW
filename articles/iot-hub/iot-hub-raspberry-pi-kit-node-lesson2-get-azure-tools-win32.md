<properties
 pageTitle="取得 Azure 工具 (Windows 7 +) |Microsoft Azure"
 description="在 Windows 7 及更新版本上安裝 Python 和 Azure 命令列介面 (Azure CLI)。"
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

# <a name="21-get-azure-tools-windows-7-"></a>2.1 取得 Azure 工具 (Windows 7 +)

> [AZURE.SELECTOR]
- [Windows 7 +](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-win32.md)
- [Ubuntu 16.04](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-ubuntu.md)
- [macOS 10.10](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-mac.md)

## <a name="211-what-you-will-do"></a>2.1.1 您將會執行的動作

安裝 Python 和 Azure 命令列介面 (Azure CLI)。 如果您符合任何問題，請搜尋中的[疑難排解頁面](iot-hub-raspberry-pi-kit-node-troubleshooting.md)的解決方案。

## <a name="212-what-you-will-learn"></a>2.1.2 您將學習什麼

- 如何安裝 Python。
- 如何安裝 Azure CLI。

## <a name="213-what-you-need"></a>2.1.3 您需要的什麼

- 在 Windows 電腦網際網路連線
- 作用中的 Azure 訂閱。 如果您沒有帳戶，您可以建立一個[免費的帳戶](https://azure.microsoft.com/free/)，在幾分鐘。

## <a name="214-install-python"></a>2.1.4 安裝 Python

在 Windows 電腦上安裝 Python。 您可以安裝 Python 2.7，3.4 或 3.5。 本教學課程中會根據 Python 2.7。 如果您已經安裝 Python，請移至節 2.1.5。

[取得 Windows Python](https://www.python.org/downloads/)

您也需要新增 python.exe 和 pip.exe 系統的安裝位置的資料夾路徑`PATH`環境變數。 根據預設，python.exe 安裝在`C:\Python27`且 pip.exe 安裝在`C:\Python27\Scripts`。

## <a name="215-install-the-azure-cli"></a>2.1.5 安裝 Azure CLI

Azure CLI 提供多平台的命令列體驗 Azure，讓您可以直接從您的命令列可佈建和管理資源。

若要安裝 Azure CLI，請遵循下列步驟︰

1. 以管理員身分開啟命令提示字元視窗。
2. 執行下列命令︰

    ```bash
    pip install azure-cli-core==0.1.0b7 azure-cli-vm==0.1.0b7 azure-cli-storage==0.1.0b7 azure-cli-role==0.1.0b7 azure-cli-resource==0.1.0b7 azure-cli-profile==0.1.0b7 azure-cli-network==0.1.0b7 azure-cli-iot==0.1.0b7 azure-cli-feedback==0.1.0b7 azure-cli-configure==0.1.0b7 azure-cli-component==0.1.0b7 azure-cli==0.1.0b7
    ```
3. 藉由執行下列命令確認安裝︰

    ```bash
    az iot -h
    ```

如果已成功安裝，您會看到下列輸出。

![az iot h](media/iot-hub-raspberry-pi-lessons/lesson2/az_iot_help_win.png)

## <a name="216-summary"></a>2.1.6 摘要

您已安裝 Azure CLI。 繼續下一節建立使用 Azure CLI 您 Azure IoT 中樞和裝置的身分識別。

## <a name="next-steps"></a>後續步驟

[2.2 建立 IoT 中心和註冊您覆盆子 Pi 3](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md)
