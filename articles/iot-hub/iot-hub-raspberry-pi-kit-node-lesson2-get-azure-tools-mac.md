<properties
 pageTitle="取得 Azure 工具 (macOS 10.10) |Microsoft Azure"
 description="MacOS 上安裝 Python 和 Azure 命令列介面 (Azure CLI)。"
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

# <a name="21-get-azure-tools-macos-1010"></a>2.1 取得 Azure 工具 (macOS 10.10)

> [AZURE.SELECTOR]
- [Windows 7 +](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-win32.md)
- [Ubuntu 16.04](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-ubuntu.md)
- [macOS 10.10](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-mac.md)

## <a name="211-what-you-will-do"></a>2.1.1 您將會執行的動作

安裝 Azure 命令列介面 (Azure CLI)。 如果您符合任何問題，請搜尋中的[疑難排解頁面](iot-hub-raspberry-pi-kit-node-troubleshooting.md)的解決方案。

## <a name="212-what-you-will-learn"></a>2.1.2 您將學習什麼

- 如何安裝 Azure CLI。
- 如何新增的 Azure CLI IoT 子群組。

## <a name="213-what-you-need"></a>2.1.3 您需要的什麼

- 網際網路連線 Mac
- 作用中的 Azure 訂閱。 如果您沒有帳戶，您可以建立一個[免費的帳戶](https://azure.microsoft.com/free/)，在幾分鐘。

## <a name="214-install-python"></a>2.1.4 安裝 Python

雖然 macOS 隨附 Python 2.7 預設工作，但建議安裝 Python 透過 Homebrew。 請參閱[安裝的 Python macOS 上](http://docs.python-guide.org/en/latest/starting/install/osx/)。

安裝 Python 和 pip 藉由執行下列命令︰

```bash
brew install python
```

## <a name="215-install-the-azure-cli"></a>2.1.5 安裝 Azure CLI

Azure CLI 提供多平台的命令列體驗 Azure，讓您可以直接從您的命令列可佈建和管理資源。 

若要安裝最新的 Azure CLI，請遵循下列步驟︰

1. 終端機視窗中，執行下列命令。 可能需要 5 分鐘，才能安裝 Azure CLI。

    ```bash
    pip install azure-cli-core==0.1.0b7 azure-cli-vm==0.1.0b7 azure-cli-storage==0.1.0b7 azure-cli-role==0.1.0b7 azure-cli-resource==0.1.0b7 azure-cli-profile==0.1.0b7 azure-cli-network==0.1.0b7 azure-cli-iot==0.1.0b7 azure-cli-feedback==0.1.0b7 azure-cli-configure==0.1.0b7 azure-cli-component==0.1.0b7 azure-cli==0.1.0b7
    ```

2. 藉由執行下列命令確認安裝︰

    ```bash
    az iot -h
    ```
  
如果已成功安裝，您應該會看到下列輸出。

![az iot h](media/iot-hub-raspberry-pi-lessons/lesson2/az_iot_help_osx.png)

## <a name="215-summary"></a>2.1.5 摘要

您已安裝 Azure CLI。 繼續下一節建立使用 Azure CLI 您 Azure IoT 中樞和裝置的身分識別。

## <a name="next-steps"></a>後續步驟

[2.2 建立 IoT 中心和註冊您覆盆子 Pi 3](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md)
