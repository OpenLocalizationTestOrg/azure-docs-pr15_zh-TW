> [AZURE.SELECTOR]
- [Windows C](../articles/iot-suite/iot-suite-connecting-devices.md)
- [Linux 上的 C](../articles/iot-suite/iot-suite-connecting-devices-linux.md)
- [在 mbed C](../articles/iot-suite/iot-suite-connecting-devices-mbed.md)
- [Node.js](../articles/iot-suite/iot-suite-connecting-devices-node.md)

## <a name="scenario-overview"></a>案例概觀

在此案例中，您可以建立傳送給遠端監視[預先設定的解決方案]的下列遙測裝置[lnk-what-are-preconfig-solutions]：

- 外部的溫度
- 內部溫度
- 濕度

簡化了程序，在裝置上的程式碼會產生樣本值，但我們建議您在實際感應器連線至您的裝置，並傳送實際遙測擴充樣本。

若要完成本教學課程中，您需要使用中的 Azure 帳戶。 如果您沒有帳戶，您可以建立免費的試用帳戶在幾分鐘。 如需詳細資訊，請參閱[Azure 免費試用版][lnk-free-trial]。

## <a name="before-you-start"></a>開始之前

您為裝置撰寫任何程式碼之前，您必須佈建遠端監控預先設定的解決方案，然後佈建該解決方案的新自訂裝置。

### <a name="provision-your-remote-monitoring-preconfigured-solution"></a>佈建遠端監控預先設定的解決方案

在本教學課程中建立的裝置會將資料傳送至[遠端監控]的執行個體[lnk-remote-monitoring]預先設定的解決方案。 如果您已經您尚未中 Azure 帳戶佈建的遠端監視預先設定的解決方案，請遵循下列步驟：

1. 在 [ <https://www.azureiotsuite.com/> ] 頁面上，按一下 [**+**來建立新的解決方案。

2. 按一下 [**遠端監控**] 面板中建立新的解決方案上的 [**選取**]。

3. 在 [**建立遠端監視方案**] 頁面上輸入您所選擇的**方案名稱**、 選取您要部署至、**區域**及選取想要使用 Azure 訂閱。 再按一下 [**建立方案**。

4. 等候佈建程序完成。

> [AZURE.WARNING] 預先設定的解決方案使用計費 Azure 服務。 請務必要與其避免任何不必要的費用完成時的預先設定的解決方案移除您的訂閱。 您可以完全移除預先設定的解決方案您的訂閱非常<https://www.azureiotsuite.com/>頁面。

遠端監視解決方案的佈建程序完成時，按一下 [在瀏覽器中開啟解決方案儀表板的 [**啟動]** 。

![][img-dashboard]

### <a name="provision-your-device-in-the-remote-monitoring-solution"></a>佈建您的裝置中的遠端監視解決方案

> [AZURE.NOTE] 如果您已經有中您的解決方案佈建裝置，您可以略過此步驟。 您必須知道的裝置認證時建立的用戶端應用程式。

裝置連線至預先設定的解決方案，它必須以使用有效認證的 IoT Hub 識別本身擷取。 您可以擷取裝置認證方案儀表板。 您在稍後的本教學課程中用戶端應用程式包含裝置認證。 

若要將新的裝置新增至遠端監視解決方案，完成解決方案儀表板中的下列步驟：

1.  在儀表板的較低的左上角，按一下 [**新增裝置**。

    ![][1]

2.  在 [**自訂裝置**] 面板中，按一下 [在**新增新**。

    ![][2]

3.  選擇 [**讓我定義我自己的裝置 ID**、 輸入**mydevice**等裝置 ID、 按一下 [**檢查識別碼**來確認該名稱不是已在使用中，然後按一下 [**建立**佈建裝置。

    ![][3]

5. 請記下裝置認證 （裝置 ID、 IoT Hub 主機名稱，與裝置金鑰）、 用戶端應用程式需要它們可以連線到遠端的監視解決方案。 然後按一下 [**完成**]。

    ![][4]

6. 請確定您的裝置會顯示在 [裝置] 區段。 裝置狀態為**擱置**，直到裝置建立遠端監視解決方案的連線。

    ![][5]

[img-dashboard]: ./media/iot-suite-selector-connecting/dashboard.png
[1]: ./media/iot-suite-selector-connecting/suite0.png
[2]: ./media/iot-suite-selector-connecting/suite1.png
[3]: ./media/iot-suite-selector-connecting/suite2.png
[4]: ./media/iot-suite-selector-connecting/suite3.png
[5]: ./media/iot-suite-selector-connecting/suite5.png

[lnk-what-are-preconfig-solutions]: ../articles/iot-suite/iot-suite-what-are-preconfigured-solutions.md
[lnk-remote-monitoring]: ../articles/iot-suite/iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/