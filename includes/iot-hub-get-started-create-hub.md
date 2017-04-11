## <a name="create-an-iot-hub"></a>建立 IoT 中心

建立連線到您模擬裝置 IoT 中心。 下列步驟將告訴您如何使用 Azure 入口網站完成這項工作。

1. [Azure 入口網站]登入[lnk-portal]。

2. 在 Jumpbar 中，按一下 [**新增** > **網際網路的項目** > **Azure IoT 中心**。

    ![Azure 入口網站 Jumpbar][1]

3. 在**IoT 中心**刀中，選擇 [IoT 中心設定]。

    ![IoT 中心刀][2]

    * 在 [**名稱**] 方塊中，輸入您的 IoT 中樞的名稱。 如果**名稱**有效且可用，綠色的核取記號會出現在 [**名稱**] 方塊中。
    * 選取[價格和小數位數層][lnk-pricing]。 本教學課程中不需要的特定層。 在此教學課程中，使用免費的 F1 層。
    * 在 [**資源] 群組**中，建立新的 [資源] 群組中，或選取現有的項目。 如需詳細資訊，請參閱[使用資源群組來管理您的 Azure 資源][lnk-resource-groups]。
    * 在**位置**，選取要主控 IoT 中心的位置。 在此教學課程中，選擇 [最近的位置]。

4. 當您選擇 IoT 中心設定選項時，請按一下 [**建立**]。  可能需要幾分鐘的 Azure 建立 IoT 中心。 若要查看狀態，您可以監視 Startboard 或 [通知] 面板中的進度。

    ![新的 IoT 中心狀態][3]

5. 當已成功建立 IoT 集線器時，請按一下 [IoT 中心開啟新的 IoT 集線器刀 Azure 入口網站中的新磚。 記下的 [**主機名稱**，然後按一下 [**共用存取原則**。

    ![新的 IoT 中心刀][4]

6. 在**共用存取原則**刀中，按一下**iothubowner**原則，然後複製並**iothubowner**刀記的連接字串。 如需詳細資訊，請參閱[存取控制][lnk-access-control]中 「 Azure IoT 中心開發人員指南 」。

    ![共用的存取原則刀][5]


<!-- Images. -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-get-started-create-hub/create-iot-hub3.png
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-resource-groups]: ../articles/azure-portal/resource-group-portal.md
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
