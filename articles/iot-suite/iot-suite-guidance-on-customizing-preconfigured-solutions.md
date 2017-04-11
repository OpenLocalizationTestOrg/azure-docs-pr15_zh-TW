<properties
    pageTitle="自訂預先設定的解決方案 |Microsoft Azure"
    description="提供如何自訂預先設定的 Azure IoT 套件解決方案的指引。"
    services=""
    suite="iot-suite"
    documentationCenter=".net"
    authors="aguilaaj"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="10/11/2016"
     ms.author="aguilaaj"/>

# <a name="customize-a-preconfigured-solution"></a>自訂預先設定的解決方案

預先設定的解決方案提供 Azure IoT 套件示範合作進行端對端解決方案套件內的服務。 從這個起點有各種不同的位置，您可以延伸並自訂特定案例的解決方案。 下列各節說明這些常見的自訂點。

## <a name="finding-the-source-code"></a>尋找原始程式碼

預先設定的解決方案的程式碼會提供下列存放庫中的 GitHub:

- 遠端監控︰ [https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)
- 預測進行的維修作業︰ [https://github.com/Azure/azure-iot-predictive-maintenance](https://github.com/Azure/azure-iot-predictive-maintenance)

預先設定的解決方案的程式碼提供給示範模式和實務用於實作使用 Azure IoT 套件 IoT 解決方案的端對端功能。 您可以找到有關如何建立並部署 GitHub 存放庫中的解決方案的詳細資訊。

## <a name="changing-the-preconfigured-rules"></a>變更預先設定的規則

遠端監控方案包含實作方案所顯示的裝置資訊、 遙測和規則邏輯的三個[Azure 資料流分析](https://azure.microsoft.com/services/stream-analytics/)工作。

三個資料流分析作業，其語法，請參閱在[遠端監控預先設定的解決方案逐步解說](iot-suite-remote-monitoring-sample-walkthrough.md)中的深度。 

您可以編輯直接來變更邏輯，這些工作，或將特定的邏輯新增至您的狀況。 您可以找到資料流分析作業，如下所示︰
 
1. 移至[Azure 入口網站](https://portal.azure.com)。
2. 瀏覽至使用 IoT 方案的相同名稱的 [資源] 群組。 
3. 選取您想要修改的 Azure 資料流分析工作。 
4. 選取 [在設定] 命令的 [**停止**停止工作。 
5. 編輯輸入、 查詢和輸出。

    簡單的修改可變更的查詢**規則**工作使用**"< 」**而不是**」 > 」**。 仍會顯示 [解決方案] 入口網站**」 > 」**當您編輯的規則，但您會發現行為翻轉因為中的基本工作的變更。

6. 啟動工作

> [AZURE.NOTE] 遠端監控儀表板而定特定的資料，讓變更工作可能會導致失敗的儀表板。

## <a name="adding-your-own-rules"></a>新增您自己的規則

除了變更預先設定的 Azure 資料流分析工作，您可以使用 Azure 入口網站，來新增新的工作或現有的工作新增新查詢。

## <a name="customizing-devices"></a>自訂裝置

其中一個最常用的副檔名活動適用於您的狀況的特定的裝置。 有幾種方法可以使用的裝置。 這些方法包含變更的模擬的裝置，以符合您的狀況，或使用[IoT 裝置 SDK][] ，您的實體裝置連線至的方案。

若要將裝置新增至遠端監視預先設定的解決方案的逐步指南，請參閱[Iot 套件連線裝置](iot-suite-connecting-devices.md)和[遠端監控 C SDK 範例](https://github.com/Azure/azure-iot-sdks/tree/master/c/serializer/samples/remote_monitoring)是設計用來使用遠端監視預先設定的解決方案。

### <a name="creating-your-own-simulated-device"></a>建立您自己的模擬裝置

包含在遠端監控解決方案原始程式碼 （參考上方），.NET 模擬器。 此模擬器佈建的解決方案並可以變更傳送不同的中繼資料，遙測或回覆不同的命令。

預先設定的模擬器中遠端監視預先設定的解決方案是製冷器裝置發出溫度和濕度遙測，當您已分叉 GitHub 存放庫時，您可以修改[Simulator.WebJob](https://github.com/Azure/azure-iot-remote-monitoring/tree/master/Simulator/Simulator.WebJob)專案中的模擬器。

### <a name="available-locations-for-simulated-devices"></a>模擬裝置的可用的位置

預設位置是西雅圖/雷蒙市，州，美國。 您可以變更下列位置中[SampleDeviceFactory.cs][lnk-sample-device-factory]。


### <a name="building-and-using-your-own-physical-device"></a>建立並使用您自己 （實體） 的裝置

[Azure IoT Sdk](https://github.com/Azure/azure-iot-sdks) IoT 解決方案的連線許多裝置類型 （語言與作業系統） 提供文件庫。

## <a name="modifying-dashboard-limits"></a>修改儀表板限制

### <a name="number-of-devices-displayed-in-dashboard-dropdown"></a>在儀表板下拉式清單中顯示的裝置的數字

預設值為 200。 您可以變更此[DashboardController.cs]中的數字[lnk-dashboard-controller]。

### <a name="number-of-pins-to-display-in-bing-map-control"></a>數字的 Bing 地圖控制項中顯示的 pin 碼

預設值為 200。 您可以變更此[TelemetryApiController.cs]中的數字[lnk-telemetry-api-controller-01]。

### <a name="time-period-of-telemetry-graph"></a>遙測圖形的時間範圍

預設值為 10 分鐘的時間。 您可以變更此[TelmetryApiController.cs][lnk-telemetry-api-controller-02]。

## <a name="manually-setting-up-application-roles"></a>手動設定應用程式角色

下列程序說明如何新增**系統管理**] 及 [**唯讀**應用程式角色預先設定的解決方案。 請注意預先設定已從 azureiotsuite.com 網站佈建的解決方案，包括**系統管理**] 及 [**唯讀**角色。

**唯讀**角色的成員可以看到儀表板和裝置清單中，但不是允許新增裝置、 變更裝置屬性，或傳送的命令。  **管理員**角色的成員的方案中有完整存取所有的功能。

1. 移至 [ [Azure 傳統入口網站][lnk-classic-portal]。

2. 選取 [ **Active Directory**]。

3. 按一下 AAD 租用戶時提供您的方案，使用名稱。

4. 按一下 [**應用程式**]。

5. 按一下您預先設定的方案名稱相符的應用程式的名稱。 如果您沒有看到您的應用程式清單中，選取**擁有我公司的應用程式**中**顯示**的下拉式清單，然後按一下 [核取記號。

6.  在頁面底部，按一下 [**管理資訊清單**，然後**下載資訊清單**。

7. 這會.json 檔案下載到您的本機電腦。  開啟此檔案，在您所選擇的文字編輯器中編輯。

8. 在第三個.json 檔案的行中，您會看到︰

  ```
  "appRoles" : [],
  ```
  取代此動作︰

  ```
  "appRoles": [
  {
  "allowedMemberTypes": [
  "User"
  ],
  "description": "Administrator access to the application",
  "displayName": "Admin",
  "id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
  "isEnabled": true,
  "value": "Admin"
  },
  {
  "allowedMemberTypes": [
  "User"
  ],
  "description": "Read only access to device information",
  "displayName": "Read Only",
  "id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
  "isEnabled": true,
  "value": "ReadOnly"
  } ],
  ```

9. 儲存更新的.json 檔案 （您可以覆寫現有的檔案）。

10.  在 Azure 管理入口網站，底部的頁面上，選取 [**管理資訊清單**然後**上傳資訊清單**上傳您儲存在上一個步驟中的.json 檔案。

11. 您現在已新增應用程式的**系統管理**] 及 [**唯讀**角色。

12. 若要將其中一個角色指派給您的目錄中的使用者，請參閱[azureiotsuite.com 網站上的權限][lnk-permissions]。

## <a name="feedback"></a>意見反應

您有自訂您想要查看文件中涵蓋嗎？ 請在[使用者的語音](https://feedback.azure.com/forums/321918-azure-iot)、 或在以下這份文件的註解新增功能建議。 

## <a name="next-steps"></a>後續步驟

若要進一步瞭解自訂的預先設定的解決方案的選項，請參閱︰

- [邏輯應用程式連線至 Azure IoT 套件遠端監控預先設定的方案][lnk-logicapp]
- [使用動態遙測遠端監控預先設定的解決方案][lnk-dynamic]
- [裝置中遠端監視預先設定的解決方案的資訊中繼資料][lnk-devinfo]

[lnk-logicapp]: iot-suite-logic-apps-tutorial.md
[lnk-dynamic]: iot-suite-dynamic-telemetry.md
[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[IoT 裝置 SDK]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-permissions]: iot-suite-permissions.md
[lnk-dashboard-controller]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/Controllers/DashboardController.cs#L27
[lnk-telemetry-api-controller-01]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L27
[lnk-telemetry-api-controller-02]: https://github.com/Azure/azure-iot-remote-monitoring/blob/e7003339f73e21d3930f71ceba1e74fb5c0d9ea0/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L25 
[lnk-sample-device-factory]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Common/Factory/SampleDeviceFactory.cs#L40
[lnk-classic-portal]: https://manage.windowsazure.com
