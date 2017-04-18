## <a name="create-a-device-management-enabled-iot-hub"></a>建立裝置管理啟用 IoT 中心

由於 IoT 中心裝置管理預覽中，您需要建立裝置管理啟用 IoT 中樞。 當 IoT 中心裝置管理達到一般的顯示狀態時，本教學課程中會隨著更新。 下列步驟將告訴您如何完成這項工作使用 Azure 入口網站。

1.  [Azure 入口網站]登入。
2.  在 Jumpbar，按一下 [**新增**]，然後按一下 [**網際網路的項目**，，然後按一下**Azure IoT 中心**。

    ![][img-new-hub]

3.  在**IoT 中心**刀中，選擇 [IoT 中心設定]。

    ![][img-configure-hub]

  -   在 [**名稱**] 方塊中，輸入您的 IoT 中樞的名稱。 如果**名稱**有效且可用，綠色的核取記號會出現在 [**名稱**] 方塊中。
  -   選取 [**價格和小數位數層**]。 本教學課程中不需要的特定層。
  -   在 [**資源] 群組**中，建立新的 [資源] 群組中，或選取現有的項目。 如需詳細資訊，請參閱[使用資源群組來管理您的 Azure 資源]。
  -   核取 [**啟用裝置**管理-預覽] 方塊。
  -   在**位置**，選取要主控 IoT 中心的位置。 IoT 中心裝置管理只適用於美國、 北歐洲和東亞期間公用預覽。

    > [AZURE.NOTE]如果您不核取 [**啟用裝置**管理] 方塊，則無法運作的範例。<br/>核取 [**啟用裝置管理**，您可以建立的預覽 IoT 中心支援只能在東亞美國、 北歐洲和中式地址，且不適合生產案例。 您無法在裝置管理啟用集線器進出移轉裝置。

4.  當您選擇 IoT 中心設定選項時，請按一下 [**建立**]。 它可能需要幾分鐘讓 Azure 建立 IoT 中心。 若要查看狀態，您可以監視**Startboard**或 [**通知**] 面板中的進度。

    ![][img-monitor]

5.  已成功建立 IoT 中心，當您集線器刀會自動開啟。 記下的 [**主機名稱**，然後按一下 [**共用存取原則**。

    ![][img-keys]

6.  按一下**iothubowner**原則，然後複製並**iothubowner**刀記的連接字串。 將其複製到您在因為必須完成本教學課程可供稍後存取的位置。

    > [AZURE.NOTE] 在生產情況下，確定不使用**iothubowner**認證。

    ![][img-connection]

您已經建立裝置管理啟用 IoT 中心。 您必須完成本教學課程中的連接字串。

## <a name="create-a-device-identity"></a>建立裝置的身分識別

您可以在此區段中，使用稱為[IoT 中心檔案總管]的節點工具[iot-hub-explorer]本教學課程為建立裝置身分識別。

1. 命令列環境中執行下列動作︰

    npm 安裝 giothub-explorer@latest

2. 然後，執行下列命令，來登入中心內，來取代記住`{service connection string}`含有您先前複製 IoT 中心連線字串︰

    iothub explorer 登入 」 {服務連線字串} 」

3. 最後，建立新的裝置身分識別，稱為`myDeviceId`命令︰

    iothub 總管建立 myDeviceId-連線字串

記下結果的裝置連接字串。 這使用的連接字串裝置應用程式連線到您為裝置的 IoT 集散地。

![][img-identity]

請參閱[快速入門 IoT 中心][lnk-getstarted]以程式設計方式建立裝置身分識別的方式。

<!-- images and links -->
[img-new-hub]: media/iot-hub-get-started-create-hub-pp/image1.png
[img-configure-hub]: media/iot-hub-get-started-create-hub-pp/image2.png
[img-monitor]: media/iot-hub-get-started-create-hub-pp/image3.png
[img-keys]: media/iot-hub-get-started-create-hub-pp/image4.png
[img-connection]: media/iot-hub-get-started-create-hub-pp/image5.png
[img-identity]: media/iot-hub-get-started-create-hub-pp/devidentity.png

[Azure 入口網站]: https://portal.azure.com/
[iot-hub-explorer]: https://github.com/Azure/azure-iot-sdks/tree/master/tools/iothub-explorer

[lnk-getstarted]: ../articles/iot-hub/iot-hub-csharp-csharp-getstarted.md
[使用資源群組來管理您的 Azure 資源]: ../articles/azure-portal/resource-group-portal.md
