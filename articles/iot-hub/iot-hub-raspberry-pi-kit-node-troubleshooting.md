<properties
 pageTitle="疑難排解 |Microsoft Azure"
 description="疑難排解覆盆子 Pi Node.js 體驗的頁面"
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

# <a name="troubleshooting"></a>疑難排解

## <a name="hardware-issues"></a>硬體問題

### <a name="the-application-runs-well-but-the-led-is-not-blinking"></a>在應用程式以及執行，但不是閃爍 LED

硬體電路連線永遠有關此問題。 您可以使用下列步驟，找出問題。

1. 如果您選擇正確的**GPIO**您區上的核取。 在本課程中的兩個連接埠應該**GPIO GND (釘選 6)**和**GPIO 04 (釘選 7)**。
2. 檢查您的 LED 的信號是否正確。 更長的時間以應指示**正數**、 anode pin。
3. 使用**3.3V 釘選**及**GND 釘選**您木莓澆 pi 3。 視為 DC Power 您 Pi。 核取 [是否 LED 運作正常。

![LED 規格](media/iot-hub-raspberry-pi-lessons/troubleshooting/led_spec.png)

### <a name="other-hardware-issues"></a>其他硬體問題

解決常見問題上覆盆子 Pi 3 的相關資訊，請參閱[正式疑難排解的頁面](http://elinux.org/R-Pi_Troubleshooting)。

## <a name="nodejs-package-issues"></a>Node.js 套件問題

### <a name="no-response-during-gulp-tasks"></a>沒有回應大工作期間

如果您遇到問題執行大的工作，您可以新增`--verbose`偵錯] 選項。 請試著終止目前大工作`Ctrl + C`，然後執行下列命令主控台視窗中，若要查看偵錯訊息。 您可能會看到詳細的錯誤訊息主控台輸出中列印。 

```bash
gulp --verbose
```

### <a name="device-discovery-issues"></a>裝置探索的問題

如需說明疑難排解常見的問題`devdisco`命令，請檢查[讀我檔案](https://github.com/Azure/device-discovery-cli/blob/develop/readme.md)。

### <a name="other-npm-issues"></a>其他 NPM 問題

請嘗試更新您的 NPM 套件使用下列命令︰

```bash
npm install -g npm
```

如果問題仍存在，讓您在本文結尾的註解，或我們的[範例存放庫](https://github.com/Azure-Samples/iot-hub-node-raspberrypi-getting-started)中建立的 Github 問題

## <a name="remote-debugging"></a>遠端偵錯

### <a name="run-the-sample-application-in-debug-mode"></a>在 [偵錯模式中執行範例應用程式

```bash
gulp run --debug
```

當偵錯引擎時，您應該會看到```Debugger listening on port 5858```主控台輸出中。

### <a name="configure-vs-code-to-connect-to-the-remote-device"></a>設定連線到遠端裝置與程式碼

開啟 [左側的 [**偵錯**] 面板。

按一下綠色的**啟動偵錯**(F5)] 按鈕。 與程式碼會開啟**launch.json**檔案，您需要更新。

更新下列內容**launch.json**檔案。 取代`[device hostname or IP address]`實際裝置的 IP 位址或主機名稱。   

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Attach",
            "type": "node",
            "request": "attach",
            "port": 5858,
            "address": "[device hostname or IP address]",
            "restart": false,
            "sourceMaps": false,
            "outDir": null,
            "localRoot": "${workspaceRoot}",
            "remoteRoot": null
        }
    ]
}
```

![遠端偵錯資訊](media/iot-hub-raspberry-pi-lessons/troubleshooting/remote_debugging_configuration.png)

### <a name="attach-to-the-remote-application"></a>附加至遠端應用程式

按一下綠色的**啟動偵錯**(F5)] 按鈕以啟動偵錯。 

閱讀[JavaScript 與程式碼中的](https://code.visualstudio.com/docs/languages/javascript#_debugging)，若要進一步瞭解偵錯工具。

![遠端偵錯互動式](media/iot-hub-raspberry-pi-lessons/troubleshooting/remote_debugging_interactive.png)

## <a name="azure-cli-issues"></a>Azure CLI 問題

Azure CLI 為預覽建置。 您可以參考尋找解決方案的[Preview 安裝指南](https://github.com/Azure/azure-cli/blob/master/doc/preview_install_guide.md)。

如果您發現任何錯誤的工具，檔案有[問題](https://github.com/Azure/azure-cli/issues)的 GitHub repo [**問題**] 區段中。

疑難排解常見的問題的說明，請核取[讀我檔案](https://github.com/Azure/azure-cli/blob/master/README.rst)。

## <a name="python-installation-issues"></a>Python 安裝問題

### <a name="legacy-installation-issues-macos"></a>舊版的安裝問題 (macOS)

安裝**pip**， **su**權限與一起安裝的舊版套件時，會傳回 expression.error 權限錯誤。 前一次安裝的 Python 使用酒 (macOS) 不完全解除安裝，就會發生這種情況。 從先前的安裝部分**pip**封包所建立的根目錄，因而造成權限錯誤。 解決方法是，若要移除安裝的根套件。 您可以使用下列步驟，完成這項工作︰

1. 移至 /usr/local/lib/python2.7/site-packages
2. 封裝清單建立根︰`ls -l | grep root`
3. 遵循步驟 2 從 mac 解除安裝套件︰`sudo rm -rf {package name}`
4. 重新安裝 Python。

## <a name="azure-iot-hub-issues"></a>Azure IoT 中心問題

如果您已成功佈建與 Azure IoT 中心`azure-cli`，您需要管理裝置連線到您 IoT 集線器，請嘗試下列工具的工具︰

### <a name="device-explorer"></a>裝置總管

[裝置總管](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md)會在您的 Windows 本機電腦上執行，並且連線到 IoT 中心 Azure 中。 它會與下列[IoT 中心端點](iot-hub-devguide.md)進行通訊︰

- *裝置身分識別管理*佈建和管理裝置註冊 IoT 中心。
- *接收裝置-雲端*，您可以監視 IoT 中心從您的裝置傳送的郵件。
- 可讓您在傳送郵件給您的裝置，從您 IoT 中心的 [*傳送雲端-裝置*]。

設定您`IoT hub connection string`內這項工具，若要使用的所有功能。

### <a name="iot-hub-explorer"></a>IoT 中心總管

[IoT 集線器 Explorer](https://github.com/Azure/azure-iot-sdks/blob/master/tools/iothub-explorer/readme.md)是範例多平台 CLI 工具來管理裝置用戶端。 工具可讓您管理的身分識別登錄中裝置、 監視裝置至雲端的郵件，並傳送雲端-裝置命令。

若要安裝 iothub 檔案總管] 工具的最新版本 （預先發行版本），請您命令列的環境中執行下列命令︰

```
npm install -g iothub-explorer@latest
```

您可以使用下列命令以取得有關所有 iothub 檔案總管命令及參數的其他說明︰

```bash
iothub-explorer help
```

### <a name="use-azure-portal-to-manage-your-resources"></a>使用 Azure 入口網站管理您的資源

在所有這些課程中，來建立和管理您的所有 Azure 資源提供完整的 CLI 體驗。 您也可以使用[Azure 入口網站](../azure-portal-overview.md)以協助佈建、 管理及偵錯 Azure 資源。

## <a name="azure-storage-issues"></a>Azure 儲存體中發生的問題

[Microsoft Azure 儲存檔案總管 （預覽版本）](http://storageexplorer.com)已從 Microsoft，可讓您使用 Windows、 macOS，以及 Linux Azure 儲存體資料的獨立應用程式。 這項工具可讓您連線到您的表格，請參閱中的資料。 您可以使用這項工具，以疑難排解您 Azure 儲存體問題。
