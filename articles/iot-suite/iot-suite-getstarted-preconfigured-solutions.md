<properties
    pageTitle="開始使用預先設定的解決方案 |Microsoft Azure"
    description="請遵循此教學課程以瞭解如何部署預先設定的 Azure IoT 套件解決方案。"
    services=""
    suite="iot-suite"
    documentationCenter=""
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/16/2016"
     ms.author="dobett"/>

# <a name="tutorial-get-started-with-the-preconfigured-solutions"></a>教學課程︰ 開始使用的預先設定的解決方案

## <a name="introduction"></a>簡介

Azure IoT 套件[預先設定的解決方案][lnk-preconfigured-solutions]合併多個 Azure IoT 服務傳送實作一般 IoT 商務案例的端對端解決方案。 連線到*遠端監控*預先設定的解決方案，並監控您的裝置。 分析資料流的資料，從您的裝置並改善商務結果藉由資料流中的資料會自動回覆的處理程序，您可以使用解決方案。

本教學課程教您如何佈建遠端監視預先設定的解決方案。 也會引導您透過遠端監視解決方案的基本功能。 您可以透過以及預先設定的解決方案部署解決方案儀表板來存取這些功能︰

![遠端監控預先設定的解決方案儀表板][img-dashboard]

若要完成此教學課程中，您需要的作用中的 Azure 訂閱。

> [AZURE.NOTE]  如果您沒有帳戶，您可以建立的免費的試用帳戶在幾分鐘。 如需詳細資訊，請參閱[Azure 免費試用版][lnk_free_trial]。

[AZURE.INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## <a name="view-the-solution-dashboard"></a>檢視的解決方案儀表板

解決方案儀表板，可讓您管理部署的方案。 例如，您可以檢視遙測、 新增裝置，並設定規則。

1.  佈建完成且您預先設定的解決方案的磚指出**準備好**，請按一下 [開啟遠端監控解決方案入口網站中的新索引標籤的 [**啟動**]。

    ![啟動預先設定的解決方案][img-launch-solution]

2.  根據預設，[解決方案] 入口網站會顯示*解決方案儀表板*。 您可以選取其他檢視，使用 [左] 功能表。

    ![遠端監控預先設定的解決方案儀表板][img-dashboard]

儀表板中會顯示下列資訊︰

- 地圖會顯示每個裝置連接到解決方案的位置。 當您第一次執行解決方案時，有四個模擬的裝置。 為 Azure WebJobs 實作模擬的裝置，且方案使用 Bing 地圖服務 API 繪製在地圖上的資訊。
- **遙測記錄**面板繪製潮濕和溫度遙測從附近即時及顯示彙總的資料，例如 [最大值、 最小值和平均濕度中選取裝置。
- [**警示歷程記錄**] 面板會顯示最近警示事件當遙測值超過臨界值。 您可以定義自己的警示，除了預先設定的解決方案所建立的範例。

## <a name="view-the-device-list"></a>檢視 [裝置] 清單

[裝置] 清單會顯示已註冊的所有裝置方案中。 您檢視及編輯裝置中繼資料、 新增或移除裝置，並傳送至裝置的命令。

1.  在 [左] 功能表，顯示此方案*裝置清單*中按一下 [**裝置**]。

    ![儀表板中的 [裝置] 清單][img-devicelist]

2.  [裝置] 清單會顯示有四個佈建程序建立的模擬的裝置。

3.  按一下 [檢視裝置詳細資料的 [裝置] 清單中的裝置]。

    ![在儀表板的裝置詳細資料][img-devicedetails]

**裝置詳細資料**] 面板包含三個區段︰

- [**動作**] 區段會列出您可以在裝置執行的動作。 如果您停用的裝置，它不會再允許傳送遙測或接收命令。 如果您停用的裝置，您可以然後再次啟用。 您可以新增觸發警示，當遙測值超過臨界值的裝置與相關聯的規則。 您也可以傳送至裝置的命令。 當裝置第一次連線時，它就是告訴方案能夠回應的命令。
- [**裝置屬性**] 區段會列出裝置中繼資料。 此中繼資料的某些來自裝置本身 （例如製造商） 和解決方案 （例如建立的時間） 來產生的部分。 您可以編輯裝置中繼資料從這裡開始。
- **驗證金鑰**區段列出裝置可以使用驗證解決方案的按鍵。

## <a name="send-a-command-to-a-device"></a>傳送至裝置的命令

裝置詳細資料窗格會顯示所有支援特定裝置的命令和能讓您傳送至裝置的命令。 裝置第一次啟動時，它會傳送至的方案命令支援的相關資訊。

1.  按一下 [在所選裝置的裝置詳細資料] 窗格中的 [**命令**]。

    ![儀表板中的 [裝置] 命令][img-devicecommands]

2.  從 [命令] 清單中選取 [ **PingDevice** ]。

3.  按一下 [**傳送] 命令**。

4.  您可以看到 [命令歷程記錄] 命令的狀態。

    ![儀表板中的命令狀態][img-pingcommand]

解決方案追蹤的狀態會傳送的每一個命令。 一開始，則結果為**擱置**的。 當裝置報告，具有執行此命令時，**成功**設定結果。

## <a name="add-a-new-simulated-device"></a>新增模擬的裝置

當您部署預先設定的方案時，您會自動提供您可以在 [裝置] 清單中看到的四個範例裝置。 這些裝置是執行中 Azure WebJob*模擬的裝置*。 模擬的裝置，方便您嘗試使用的預先設定的方案，而不需要使用部署真的裝置。 如果您想實際裝置連線至的方案，請參閱[連線至遠端裝置監視預先設定的解決方案]的[lnk-connect-rm]教學課程。

下列步驟說明如何新增模擬的裝置至的方案︰

1.  瀏覽至 [裝置] 清單。

2.  按一下 [ **+ 新增裝置**的左下角新增裝置]。

    ![新增裝置預先設定的方案][img-adddevice]

3.  **模擬裝置**圖磚上，按一下 [**新增**]。

    ![在 [儀表板中設定新的裝置詳細資料][img-addnew]
    
    除了建立新的模擬的裝置，您也可以新增實體裝置如果您選擇要建立**自訂的裝置**。 若要進一步瞭解實體裝置連線至的方案，請參閱[連線到遠端監視預先設定的解決方案 IoT 套件裝置][lnk-connect-rm]。

4.  選取 [**讓我來定義自己的裝置識別碼**，並輸入唯一裝置識別碼名稱，例如**mydevice_01**。

5.  按一下 [**建立**]。

    ![儲存新的裝置][img-definedevice]

6. 在步驟 3 中的 [**新增模擬的裝置**，按一下 [**完成**] 以返回 [裝置] 清單中。

7. 在 [裝置] 清單中，您可以檢視您的裝置**執行**。

    ![檢視裝置清單中的新裝置][img-runningnew]

8. 您也可以檢視模擬的遙測儀表板上新裝置︰

    ![從新裝置檢視遙測][img-runningnew-2]

## <a name="edit-the-device-metadata"></a>編輯裝置中繼資料

當裝置第一次連線至的方案時，它會傳送至的方案它的中繼資料。 當您編輯的解決方案儀表板透過裝置中繼資料時，它會傳送至裝置的新的中繼資料值，並儲存方案 DocumentDB 資料庫中的新值。 如需詳細資訊，請參閱[裝置身分識別登錄和 DocumentDB][lnk-devicemetadata]。

1.  瀏覽至 [裝置] 清單。

2.  在**裝置] 清單**中，選取您的新裝置，並按一下 [**編輯**] 以編輯**裝置的內容**︰

    ![編輯裝置中繼資料][img-editdevice]

3. 向下捲動，並對緯度和經度 vales 進行變更。 然後按一下 [**儲存至裝置登錄的變更**。

    ![編輯裝置中繼資料][img-editdevice2]

4. 瀏覽至儀表板上一步]，在地圖上已變更的裝置的位置︰

    ![編輯裝置中繼資料][img-editdevice3]

## <a name="add-a-rule-for-the-new-device"></a>新增新裝置的規則

沒有您剛才新增的新裝置的規則。 在此區段中，您可以新增觸發警示，新裝置報告的溫度超過 47 度時的規則。 在開始之前，請注意新裝置儀表板上的遙測記錄會顯示裝置溫度永遠不會超過 45 度。

1.  瀏覽至 [裝置] 清單。

2.  在**裝置] 清單**中，選取您的新裝置，然後按一下 [**新增規則**以新增裝置的規則。

3. 建立規則的資料欄位為使用**溫度**並使用**AlarmTemp**做為輸出溫度超過 47 度時︰

    ![新增裝置規則][img-adddevicerule]

4. 按一下 [**儲存] 和 [檢視規則**以儲存變更。

5.  按一下 [裝置詳細資料窗格中的新裝置的 [**命令**]。

    ![新增裝置規則][img-adddevicerule2]

6.  從 [命令] 清單中選取**ChangeSetPointTemp**並將**SetPointTemp** 45。 然後按一下 [**傳送] 命令**︰

    ![新增裝置規則][img-adddevicerule3]

7.  瀏覽回解決方案儀表板。 在一段時間之後，您會看到**警示歷程記錄**] 窗格中的新項目時報告您的新裝置的溫度超過 47 度臨界值進行︰

    ![新增裝置規則][img-adddevicerule4]

8. 您可以檢視及編輯您的儀表板的 [**規則**] 頁面上的所有規則︰

    ![清單裝置規則][img-rules]

9. 您可以檢閱及編輯回應的儀表板的 [**動作**] 頁面上的規則可以採用的所有動作︰

    ![清單裝置動作][img-actions]

> [AZURE.NOTE] 可以定義動作可以傳送電子郵件或簡訊規則來回應或透過[邏輯應用程式]的業務系統整合[lnk-logic-apps]。 如需詳細資訊，請參閱[連線至您 Azure IoT 套件遠端監控邏輯應用程式預先設定的解決方案][lnk-logicapptutorial]。

## <a name="other-features"></a>其他功能

使用解決方案入口網站，您可以搜尋特定的特性，例如模型數字與裝置︰

![裝置的相關的搜尋][img-search]

您可以停用的裝置，並已停用之後，您可以移除︰

![停用並移除裝置][img-disable]

## <a name="behind-the-scenes"></a>在幕後

當您部署預先設定的方案時，部署程序會在您選取的 Azure 訂閱中建立多個資源。 您可以在 Azure[入口網站]檢視這些資源[lnk-portal]。 部署程序會根據您選擇為您預先設定的方案名稱的名稱與建立**資源群組**︰

![Azure 入口網站中預先設定的解決方案][img-portal]

您可以檢視每個資源的設定以選取清單中的 [資源] 群組中的資源。

您也可以檢視預先設定的解決方案的程式碼。 [Azure iot-遠端-監視]位於遠端監控預先設定的解決方案原始程式碼[lnk-rmgithub]GitHub 存放庫︰

- **DeviceAdministration**資料夾包含儀表板的程式碼。
- **模擬器**資料夾包含模擬裝置的程式碼。
- **EventProcessor**資料夾包含控點的內送遙測的後端程序的程式碼。

當您完成時，您可以從 Azure 訂閱[azureiotsuite.com]上刪除預先設定的解決方案[lnk-azureiotsuite]網站。 此網站可讓您輕鬆地刪除您建立預先設定的解決方案時所佈建後的所有資源。

> [AZURE.NOTE] 若要確保您刪除與預先設定的方案相關的所有項目，將其刪除上[azureiotsuite.com] [lnk-azureiotsuite]網站，並不只刪除入口網站中的 [資源] 群組。

## <a name="next-steps"></a>後續步驟

既然您已部署預先設定的工作解決方案，您可以繼續閱讀下列文章開始使用 IoT 套件︰

- [遠端監控預先設定的解決方案逐步解說][lnk-rm-walkthrough]
- [將您的裝置連線到遠端監視預先設定的解決方案][lnk-connect-rm]
- [Azureiotsuite.com 網站上的權限][lnk-permissions]

[img-launch-solution]: media/iot-suite-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-getstarted-preconfigured-solutions/dashboard.png
[img-devicelist]: media/iot-suite-getstarted-preconfigured-solutions/devicelist.png
[img-devicedetails]: media/iot-suite-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-getstarted-preconfigured-solutions/devicecommands.png
[img-pingcommand]: media/iot-suite-getstarted-preconfigured-solutions/pingcommand.png
[img-adddevice]: media/iot-suite-getstarted-preconfigured-solutions/adddevice.png
[img-addnew]: media/iot-suite-getstarted-preconfigured-solutions/addnew.png
[img-definedevice]: media/iot-suite-getstarted-preconfigured-solutions/definedevice.png
[img-runningnew]: media/iot-suite-getstarted-preconfigured-solutions/runningnew.png
[img-runningnew-2]: media/iot-suite-getstarted-preconfigured-solutions/runningnew2.png
[img-rules]: media/iot-suite-getstarted-preconfigured-solutions/rules.png
[img-editdevice]: media/iot-suite-getstarted-preconfigured-solutions/editdevice.png
[img-editdevice2]: media/iot-suite-getstarted-preconfigured-solutions/editdevice2.png
[img-editdevice3]: media/iot-suite-getstarted-preconfigured-solutions/editdevice3.png
[img-adddevicerule]: media/iot-suite-getstarted-preconfigured-solutions/addrule.png
[img-adddevicerule2]: media/iot-suite-getstarted-preconfigured-solutions/addrule2.png
[img-adddevicerule3]: media/iot-suite-getstarted-preconfigured-solutions/addrule3.png
[img-adddevicerule4]: media/iot-suite-getstarted-preconfigured-solutions/addrule4.png
[img-actions]: media/iot-suite-getstarted-preconfigured-solutions/actions.png
[img-portal]: media/iot-suite-getstarted-preconfigured-solutions/portal.png
[img-search]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_07.png
[img-disable]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_08.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devicemetadata]: iot-suite-what-are-preconfigured-solutions.md#device-identity-registry-and-documentdb
[lnk-logicapptutorial]: iot-suite-logic-apps-tutorial.md
[lnk-rm-walkthrough]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md
