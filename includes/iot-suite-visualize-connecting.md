## <a name="view-device-telemetry-in-the-dashboard"></a>檢視裝置遙測儀表板

儀表板中的遠端監視解決方案可讓您檢視您的裝置傳送給 IoT Hub 遙測。

1. 在瀏覽器中傳回遠端監視解決方案儀表板中，[左] 面板中瀏覽至**裝置] 清單**中按一下 [**裝置**。

2. 在**裝置] 清單**中，您應該會看到您的裝置的狀態現在的**執行**。

    ![][18]

3. 按一下 [返回儀表板，請選取您的裝置在**裝置與檢視**下拉式若要檢視其遙測**儀表板**]。 範例應用程式的遙測為 50 內部溫度單位、 55 外部溫度單位濕度 50 單位。 請注意預設儀表板顯示僅溫度和濕度的值。

    ![][img-telemetry]

## <a name="send-a-command-to-your-device"></a>將命令傳送至您的裝置

遠端監視解決方案中的儀表板可讓您將命令傳送至您的裝置透過 IoT 集線器。 例如，遠端監視解決方案中您可以傳送命令以設定內部溫度的裝置。

1. 遠端監視解決方案儀表板中，按一下 [左] 面板中瀏覽至**裝置] 清單**中的 [**裝置**]。

2. 按一下 [**裝置 ID**裝置的**裝置清單**。

3. 在 [**裝置詳細資料**] 面板中，按一下 [**命令**。

    ![][13]

4. 在**命令**下拉式、 選取**SetTemperature**，然後再**溫度**中輸入新的溫度值。 按一下 [**傳送命令**將命令傳送至裝置。

    ![][14]

    > [AZURE.NOTE] 命令歷程記錄一開始會顯示為**擱置**的 status 命令。 當裝置認可命令時、 狀態變更為 [**成功**。

5. 在儀表板，確認該裝置立即傳送 75 做為新的溫度值。

## <a name="next-steps"></a>後續步驟

[自訂預先設定的解決方案]的文章[lnk-customize]說明一些您可以擴充此範例的方法。 可能的副檔名包括使用實際感應及實作其他命令。

您可以深入了解[azureiotsuite.com 網站的權限][lnk-permissions]。

[13]: ./media/iot-suite-visualize-connecting/suite4.png
[14]: ./media/iot-suite-visualize-connecting/suite7-1.png
[18]: ./media/iot-suite-visualize-connecting/suite10.png
[img-telemetry]: ./media/iot-suite-visualize-connecting/telemetry.png
[lnk-customize]: ../articles/iot-suite/iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-permissions]: ../articles/iot-suite/iot-suite-permissions.md
