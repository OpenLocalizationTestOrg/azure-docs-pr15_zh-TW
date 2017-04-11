<properties
  pageTitle="Azure IoT 套件及邏輯應用程式 |Microsoft Azure"
  description="教學課程，說明如何將商務程序連結至 Azure IoT 套件邏輯應用程式。"
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="08/16/2016"
  ms.author="araguila"/>
  
# <a name="tutorial-connect-logic-app-to-your-azure-iot-suite-remote-monitoring-preconfigured-solution"></a>教學課程︰ 將邏輯應用程式連線至 Azure IoT 套件遠端監控預先設定的方案

[Microsoft Azure IoT 套件][lnk-internetofthings]遠端監視預先設定的解決方案是快速開始使用就是 IoT 解決方案的端對端功能集的絕佳方式。 本教學課程中會引導您如何將邏輯應用程式新增至您 Microsoft Azure IoT 套件遠端監視預先設定的解決方案。 這些步驟將示範如何您可以採取更進一步 IoT 方案連接至商務程序。

_如果您正在尋找如何佈建遠端監控預先設定的解決方案的逐步解說，請參閱[教學課程︰ 開始使用的預先設定的 IoT 解決方案][lnk-getstarted]。_

在開始此教學課程之前，您應該︰

- 佈建 Azure 訂閱中遠端監視預先設定的解決方案。

- 建立 SendGrid 帳戶，您可以傳送電子郵件會觸發商務程序。 您可以註冊免費的試用帳戶在[SendGrid](https://sendgrid.com/) **免費試用**] 即可。 您已經註冊免費試用帳戶後，您必須在傳送郵件的權限授與的 SendGrid 建立[API 金鑰](https://sendgrid.com/docs/User_Guide/Settings/api_keys.html)。 您需要此 API 金鑰稍後的教學課程。

假設您已提供遠端監控預先設定的方案、 瀏覽至方案[Azure 入口網站]中的 [資源] 群組[lnk-azureportal]。 資源群組具有相同名稱的方案名稱您選擇當您佈建後遠端監視解決方案。 在 [資源] 群組中，您可以看到所有能夠 Azure 資源除了的 Azure Active Directory 應用程式，您可以 Azure 傳統入口網站中找到解決方案。 下圖顯示 [遠端監控預先設定解決方案範例**資源群組**刀︰

![](media/iot-suite-logic-apps-tutorial/resourcegroup.png)

若要開始，將邏輯應用程式設定為使用預先設定的解決方案。

## <a name="set-up-the-logic-app"></a>設定邏輯應用程式

1. 按一下 [__新增__您在 Azure 入口網站的資源群組刀頂端。

2. 搜尋__邏輯應用程式__加以選取，然後按一下 [**建立**]。

3. 輸入__名稱__，並使用相同的**訂閱**及您佈建遠端監視解決方案時所使用的**資源群組**。 按一下 [__建立__]。

    ![](media/iot-suite-logic-apps-tutorial/createlogicapp.png)

4. 當您部署完成時，您可以看到邏輯應用程式會列為您的 [資源] 群組中的資源。

5. 按一下 [瀏覽至邏輯應用程式刀，請選取 [**空白的邏輯應用程式**範本，以開啟**邏輯應用程式的設計工具**將邏輯應用程式]。

    ![](media/iot-suite-logic-apps-tutorial/logicappsdesigner.png)

6. 選取 [__邀請__]。 這個動作會指定特定的 JSON 傳入的 HTTP 要求的格式化的觸發程序裝載的動作。

7. 貼上下列要求本文 JSON 結構描述至︰

    ```
    {
      "$schema": "http://json-schema.org/draft-04/schema#",
      "id": "/",
      "properties": {
        "DeviceId": {
          "id": "DeviceId",
          "type": "string"
        },
        "measuredValue": {
          "id": "measuredValue",
          "type": "integer"
        },
        "measurementName": {
          "id": "measurementName",
          "type": "string"
        }
      },
      "required": [
        "DeviceId",
        "measurementName",
        "measuredValue"
      ],
      "type": "object"
    }
    ```
    
    > [AZURE.NOTE] 您儲存邏輯應用程式，但您必須先新增動作後，您可以複製 HTTP 文章的 URL。

8. 按一下 [ __+ 新步驟__下手動觸發程序。 然後按一下 [**新增動作**。

    ![](media/iot-suite-logic-apps-tutorial/logicappcode.png)

9. 搜尋**SendGrid-傳送電子郵件**，然後按一下它。

    ![](media/iot-suite-logic-apps-tutorial/logicappaction.png)

10. 輸入連線，例如**SendGridConnection**的名稱，輸入**SendGrid API 金鑰**您建立設定 SendGrid 帳戶，然後按一下 [**建立**]。

    ![](media/iot-suite-logic-apps-tutorial/sendgridconnection.png)

11. 新增您要同時**從**] 欄位**及**欄位擁有的電子郵件地址。 新增**遠端監控提醒 [DeviceId]**至 [**主旨**] 欄位。 在 [**電子郵件內文**] 欄位中，新增**裝置 [DeviceId] 報告 [measurementName] 與 [measuredValue] 的值。** 您可以新增**[DeviceId]**、 **[measurementName]**及**[measuredValue]** ，即可在**您可以插入從先前的步驟的資料**] 區段中。

    ![](media/iot-suite-logic-apps-tutorial/sendgridaction.png)

12. 在頂端的功能表中，按一下 [__儲存__]。

13. 按一下 [**要求**的觸發程序，複製__這個 url Http 文章__的值。 您需要將此 URL 稍後在本教學課程。

> [AZURE.NOTE] 邏輯應用程式可讓您執行[許多不同類型的動作][lnk-logic-apps-actions]包括在 Office 365 中的動作。 

## <a name="set-up-the-eventprocessor-web-job"></a>設定 EventProcessor Web 工作

在此區段中，您可以連線您預先設定的解決方案邏輯應用程式所建立。 若要完成此工作，您可以新增觸發的動作，或當裝置感應器值超過臨界值的邏輯應用程式的 URL。

1. 若要複製的最新版本中使用給客戶[azure iot-遠端-監視 github 存放庫][lnk-rmgithub]。 例如︰

    ```
    git clone https://github.com/Azure/azure-iot-remote-monitoring.git
    ```

2. 在 Visual Studio 中，開啟__RemoteMonitoring.sln__從存放庫的本機複本。

3. 開啟__ActionRepository.cs__檔案的**基礎結構\\存放庫**資料夾。

4. 更新**actionIds**字典使用__此 URL Http 張貼__您記下您邏輯應用程式，如下所示︰

    ```
    private Dictionary<string,string> actionIds = new Dictionary<string, string>()
    {
        { "Send Message", "<Http Post to this UR>" },
        { "Raise Alarm", "<Http Post to this UR> }
    };
    ```

5. 儲存方案中的變更，然後結束 Visual Studio。

## <a name="deploy-from-the-command-line"></a>部署從命令列

在此區段中，您可以部署遠端監視解決方案來取代目前執行 Azure 中的版本您更新的版本。

1. 追蹤[開發設定][lnk-devsetup]設定以供部署環境的相關指示。

2.  若要從本機部署，請依照下列[本機部署][lnk-localdeploy]指示。

3.  若要部署至雲端，並更新現有的雲端部署，請依照下列[雲端部署][lnk-clouddeploy]指示。 部署名稱為使用您的原始部署中的名稱。 範例如果原始部署稱為**demologicapp**，請使用下列命令︰

    ``
    build.cmd cloud release demologicapp
    ``
    
    建立指令碼執行時，請務必使用相同的 Azure 帳戶、 訂閱、 地區和使用您佈建的解決方案時所用的 Active Directory 執行個體。

## <a name="see-your-logic-app-in-action"></a>請參閱邏輯應用程式的實際操作

遠端監視預先設定的解決方案有提供解決方案時，設定預設的兩個規則。 兩個規則是**SampleDevice001**裝置上︰

* 溫度 > 38.00
* 濕度 > 48.00

溫度規則觸發**提高警示**動作並濕度規則觸發**SendMessage**巨集指令。 假設您使用的相同 URL 的兩個動作**ActionRepository**類別，其中一個規則會觸發邏輯應用程式。 兩個規則使用 SendGrid 傳送電子郵件給**地址使用之通知的詳細資料**。

> [AZURE.NOTE] 繼續觸發每次臨界值符合邏輯應用程式。 若要避免不必要的電子郵件，您可以停用您的方案入口網站中的規則，或停用[Azure 入口網站]中的邏輯應用程式[lnk-azureportal]。

除了收到的電子郵件，您也可以查看在入口網站執行的邏輯應用程式時︰

![](media/iot-suite-logic-apps-tutorial/logicapprun.png)

## <a name="next-steps"></a>後續步驟

既然您已連線至商務程序的預先設定的解決方案是使用邏輯應用程式，您可以進一步瞭解自訂的預先設定的解決方案的選項︰

- [使用動態遙測遠端監控預先設定的解決方案][lnk-dynamic]
- [裝置中遠端監視預先設定的解決方案的資訊中繼資料][lnk-devinfo]

[lnk-dynamic]: iot-suite-dynamic-telemetry.md
[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[lnk-internetofthings]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-getstarted]: iot-suite-getstarted-preconfigured-solutions.md
[lnk-azureportal]: https://portal.azure.com
[lnk-logic-apps-actions]: ../connectors/apis-list.md
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devsetup]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/dev-setup.md
[lnk-localdeploy]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/local-deployment.md
[lnk-clouddeploy]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/cloud-deployment.md
