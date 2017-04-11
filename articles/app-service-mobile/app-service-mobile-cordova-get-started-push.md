<properties
    pageTitle="推播通知加入 Azure 行動應用程式 Apache Cordova 應用程式 |Azure 應用程式服務"
    description="瞭解如何使用 Azure 行動應用程式傳送推入通知給您 Apache Cordova 應用程式。"
    services="app-service\mobile"
    documentationCenter="javascript"
    manager="erikre"
    editor=""
    authors="ysxu"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-html"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="add-push-notifications-to-your-apache-cordova-app"></a>新增至您 Apache Cordova 應用程式的推播通知

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>概觀

在本教學課程中，您專案中新增推入通知[Apache Cordova 快速開始]，讓每次即會插入一筆記錄，將會傳送至裝置的推入通知。

如果您不使用下載快速入門伺服器專案，您需要推播通知擴充套件。 如需詳細資訊，請參閱[使用.NET 後端伺服器 SDK Azure Mobile 應用程式](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。

##<a name="prerequisites"></a>必要條件

本教學課程涵蓋與 Google Android 模擬器、 Android 裝置、 Windows 裝置和在 iOS 裝置執行的 Visual Studio 2015 開發的 Apache Cordova 應用程式。

若要完成此教學課程中，您需要︰

* [Visual Studio 社群 2015年]或更新版本的電腦。
* [Visual Studio Apache Cordova 工具]。
* [作用中的 Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/)。
* 完成的[快速入門 Apache Cordova]專案。
* (Android)[Google 帳戶]與驗證電子郵件地址。
* (iOS)Apple 開發人員程式成員資格和 iOS 裝置 （iOS 模擬器不支援推入）。
* (Windows)Windows 市集開發人員帳戶，在 Windows 10 裝置。

##<a name="configure-hub"></a>設定通知中心

[AZURE.INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

[觀看影片，顯示此區段中的步驟](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-3-Create-azure-notification-hub)

##<a name="update-the-server-project-to-send-push-notifications"></a>更新伺服器專案可傳送推入通知

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##<a name="add-push-to-app"></a>修改您的 Cordova 應用程式接收推入通知

您必須確定 Apache Cordova 應用程式專案已安裝 Cordova 推入外掛程式加上任何特定推入服務來處理推入通知。

#### <a name="update-the-cordova-version-in-your-project"></a>更新您專案中的 Cordova 版本。

我們建議您以 Cordova 6.1.1 更新用戶端專案，如果您使用較舊的版本設定您的專案。 若要更新的專案，以滑鼠右鍵按一下 config.xml 開啟設定設計工具。 選取 [平台] 索引標籤，然後選擇 6.1.1 **Cordova CLI**的 [文字] 方塊中。

選擇 [**建立**，然後**建立的解決方案**以更新專案]。

#### <a name="install-the-push-plugin"></a>安裝推入外掛程式

Apache Cordova 應用程式原本就無法處理裝置或網路的功能。  這些功能所提供的[npm](https://www.npmjs.com/)或 GitHub 發佈的外掛程式。  `phonegap-plugin-push`外掛程式用來處理網路推播通知。

您可以在其中一種方式安裝推入外掛程式︰

**從的命令提示字元︰**

執行下列命令︰

    cordova plugin add phonegap-plugin-push

**從 Visual Studio 中︰**

1.  在方案總管中開啟`config.xml`檔案的 [**外掛程式** > **自訂**]，選取**給**做為安裝來源，然後輸入`https://github.com/phonegap/phonegap-plugin-push`做為來源。

    ![](./media/app-service-mobile-cordova-get-started-push/add-push-plugin.png)

2.  按一下安裝來源旁的箭號。

3. 中**SENDER_ID**，如果您已有數值專案 ID Google 開發人員主控台專案，您可以將其新增以下。 否則，輸入 [版面配置區的值，例如 777777，然後如果您的目標 Android 您可以更新此 config.xml 更新版本中的值。

4. 按一下 [**新增**]。

現在已經安裝推入外掛程式。

####<a name="install-the-device-plugin"></a>安裝裝置外掛程式

遵循安裝推入外掛程式，您使用的相同程序，但您會發現裝置外掛程式核心外掛程式] 清單中 (按一下 [**增益集** > **核心**找到)。 您需要此外掛程式，以取得平台名稱 (`device.platform`)。

#### <a name="register-your-device-for-push-on-start-up"></a>註冊您的裝置上啟動的推入

一開始，我們會在 Android 版包含最小的部分程式碼。 更新版本中，我們將在 iOS 或 Windows 10 上執行的一些小型修改。

1. 新增**registerForPushNotifications**在通話期間回撥登入程序中，或底部的**onDeviceReady**方法︰

        // Login to the service.
        client.login('google')
            .then(function () {
                // Create a table reference
                todoItemTable = client.getTable('todoitem');

                // Refresh the todoItems
                refreshDisplay();

                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);

                    // Added to register for push notifications.
                registerForPushNotifications();

            }, handleError);

    此範例顯示呼叫**registerForPushNotifications**在驗證成功後，其中時，建議您的應用程式中使用推入通知和驗證。

2. 加入新的**registerForPushNotifications**方法如下所示︰

        // Register for Push Notifications. Requires that phonegap-plugin-push be installed.
        var pushRegistration = null;
        function registerForPushNotifications() {
          pushRegistration = PushNotification.init({
              android: { senderID: 'Your_Project_ID' },
              ios: { alert: 'true', badge: 'true', sound: 'true' },
              wns: {}
          });

        // Handle the registration event.
        pushRegistration.on('registration', function (data) {
          // Get the native platform of the device.
          var platform = device.platform;
          // Get the handle returned during registration.
          var handle = data.registrationId;
          // Set the device-specific message template.
          if (platform == 'android' || platform == 'Android') {
              // Register for GCM notifications.
              client.push.register('gcm', handle, {
                  mytemplate: { body: { data: { message: "{$(messageParam)}" } } }
              });
          } else if (device.platform === 'iOS') {
              // Register for notifications.            
              client.push.register('apns', handle, {
                  mytemplate: { body: { aps: { alert: "{$(messageParam)}" } } }
              });
          } else if (device.platform === 'windows') {
              // Register for WNS notifications.
              client.push.register('wns', handle, {
                  myTemplate: {
                      body: '<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>',
                      headers: { 'X-WNS-Type': 'wns/toast' } }
              });
          }
        });

        pushRegistration.on('notification', function (data, d2) {
          alert('Push Received: ' + data.message);
        });

        pushRegistration.on('error', handleError);
        }

3. (Android)將上述的程式碼，取代`Your_Project_ID`以數字，請從[Google 開發人員主控台]應用程式專案識別碼。

## <a name="optional-configure-and-run-the-app-on-android"></a>（選用）設定，並在 Android 上執行應用程式

完成這個區段以啟用 [Android 適用的推播通知。

####<a name="enable-gcm"></a>啟用 Firebase 雲端訊息

因為我們的目標 Google Android 平台一開始，您必須啟用 Firebase 雲端訊息。 同樣地，如果您已指定目標 Microsoft Windows 裝置，您想要啟用 WNS 支援。

[AZURE.INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

####<a name="configure-backend"></a>設定行動應用程式後端傳送使用 FCM 推入要求

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

####<a name="configure-your-cordova-app-for-android"></a>Android 版中設定您的 Cordova 應用程式

在您 Cordova 應用程式中，開啟 config.xml 並取代`Your_Project_ID`以數字，請從[Google 開發人員主控台]應用程式專案識別碼。

        <plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
            <variable name="SENDER_ID" value="Your_Project_ID" />
        </plugin>

開啟 index.js 及更新程式碼，使用您的數字的專案識別碼。

        pushRegistration = PushNotification.init({
            android: { senderID: 'Your_Project_ID' },
            ios: { alert: 'true', badge: 'true', sound: 'true' },
            wns: {}
        });

####<a name="configure-device"></a>設定您的 Android 裝置 USB 偵錯

您可以將您的應用程式在 Android 裝置部署之前，必須先啟用 USB 偵錯。  在 Android 手機上執行下列步驟︰

1. 移至 [**設定** > **相關電話**，然後點選**建立數字**直到開發人員模式已啟用 （關於 7 次）。

2. 回**設定** > 啟用**USB 偵錯時**，**開發人員選項]** ，然後在 Android 電話連線到您的開發電腦的 USB 纜線。

我們測試此使用 Google 關係 5 X 裝置執行 Android 6.0 （軟糖）。  不過，技巧都有跨任何現代 Android 發行。

#### <a name="install-google-play-services"></a>安裝 Google Play 服務

推入外掛程式依賴 Android Google 播放服務推播通知。  

1.  在**Visual Studio**中，按一下 [**工具** > **Android** > **Android SDK 管理員**] 中，展開**Extras]**資料夾，然後核取方塊，請確定已安裝所有的下列 Sdk。
    * Android 2.3 或更新版本
    * Google 存放庫修訂 27 或更新版本
    * Google Play 服務 9.0.2 或更新版本

2.  按一下**安裝套件**，並等待安裝完成。

目前所需的文件庫會列出[phonegap 外掛程式-推入安裝文件]中。

#### <a name="test-push-notifications-in-the-app-on-android"></a>在 Android 上的應用程式中的測試推入通知

您可以執行的應用程式，TodoItem 表格中插入項目現在測試推播通知。 您可以從同一個裝置，或從第二個裝置，只要您使用相同的後端。 測試 Cordova 應用程式在 Android 的平台上下列方法之一︰

- **在實體裝置︰**  
將 Android 裝置附加至開發電腦的 USB 纜線。  而不是**Google Android 模擬器**中，選取 [**裝置**]。 Visual Studio 會部署到裝置的應用程式，並執行。  您可以在裝置上的應用程式與然後進行互動。  
改善您的開發經驗。  顯示螢幕共用應用程式，例如[Mobizen]可協助您的電腦上將 Android 哪網頁瀏覽器畫面開發 Android 應用程式。

- **在 Android 模擬器︰**  
有模擬器上執行時所需的其他設定步驟。

    請確定您是部署或偵錯時，如下所示，在 Android 虛擬裝置 (AVD) 管理員已設定為目標，Google Api 虛擬裝置上。

    ![](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

    如果您想要使用更快速的 x86 模擬器、 [HAXM 驅動程式的安裝](https://taco.visualstudio.com/en-us/docs/run-app-apache/#HAXM)及設定模擬器使用它。

    按一下 [**應用程式**，將 Google 帳戶新增至 Android 裝置 > **設定** > **新增帳戶**]，然後依照提示新增現有的 Google 帳戶的裝置 （我們建議使用現有的帳戶，而不是建立一個新）。

    ![](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

    執行 todolist 應用程式為之前，插入新的 todo 項目。 這次請通知圖示會顯示在通知區域。 您可以開啟通知抽屜，若要檢視通知的完整的文字。

    ![](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

##<a name="optional-configure-and-run-on-ios"></a>（選用）設定和 iOS 上執行

本節適用於 iOS 裝置上執行 Cordova 專案。 如果您不使用 iOS 裝置，您可以略過此節。

####<a name="install-and-run-the-ios-remotebuild-agent-on-a-mac-or-cloud-service"></a>安裝及執行 iOS remotebuild 代理程式 Mac 或雲端服務

您可以在 iOS 使用 Visual Studio 中執行 Cordova 應用程式之前，可幫助安裝及執行 remotebuild 代理程式在[iOS 設定指南](http://taco.visualstudio.com/en-us/docs/ios-guide/)中的步驟進行。

請確定您可以建立 iOS 版應用程式。 安裝指南中的步驟，才能從 Visual Studio 建立 iOS 版。 如果您沒有 Mac，您可以建立使用的服務，例如 MacInCloud remotebuild 代理程式的 iOS 版。 取得詳細資訊，請參閱[執行在雲端 iOS 應用程式](http://taco.visualstudio.com/en-us/docs/build_ios_cloud/)。

>[AZURE.NOTE] 若要在 iOS 上使用推入外掛程式需要 XCode 7 或更大。

####<a name="find-the-id-to-use-as-your-app-id"></a>尋找用來作為您的應用程式識別碼的識別碼

註冊您的應用程式的推入通知，Cordova 應用程式中開啟 config.xml 之前尋找`id`屬性小工具項目中的值，並將它複製以供日後使用。 在下列 XML，ID 是`io.cordova.myapp7777777`。

        <widget defaultlocale="en-US" id="io.cordova.myapp7777777"
        version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="http://www.w3.org/ns/widgets"
            xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">

當您建立的應用程式識別碼 Apple 的開發人員入口網站上，更新版本中，使用此識別碼。 （如果您在開發人員入口網站上建立不同的應用程式識別碼，並想要使用的您會需要採取一些額外步驟，稍後的變更此 ID config.xml 在本教學課程。 小工具項目中的識別碼必須符合的應用程式識別碼開發人員入口網站上。）

####<a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>註冊推播通知在 Apple 的開發人員入口網站上的應用程式

[AZURE.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

[觀看影片，顯示類似的步驟](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

####<a name="configure-azure-to-send-push-notifications"></a>設定 Azure 傳送推入通知

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

####<a name="verify-that-your-app-id-matches-your-cordova-app"></a>請確認您的應用程式識別碼符合 Cordova 應用程式

如果您已在您的 Apple 開發人員帳戶中建立應用程式識別碼符合小工具中的項目 config.xml 的識別碼，您可以略過此步驟。 不過，如果識別碼不相符，請採取下列步驟︰

1. 刪除平台資料夾從您的專案。

2. 刪除專案中的 [增益集] 資料夾。

3. 刪除 node_modules 資料夾從您的專案。

4. 小工具中的項目使用您的 Apple 開發人員帳戶中所建立的應用程式識別碼 config.xml id 屬性更新]。

5. 重建專案。

#####<a name="test-push-notifications-in-your-ios-app"></a>在 iOS 應用程式中的測試推入通知

1. 在 Visual Studio 中，請確認該**iOS**已為部署目標，，然後選擇 [連線的 iOS 裝置上執行的**裝置**。

    您可以執行 iOS 裝置連線到您的電腦上使用 iTunes。 IOS 模擬器不支援推播通知。

2. 在 Visual Studio 中建立專案並啟動應用程式在 iOS 裝置，請按**[執行**] 按鈕或**F5** ，然後按一下**[確定**] 接受推入通知]。

    >[AZURE.NOTE] 您必須明確接受推入通知，從您的應用程式。 這項要求才會發生的應用程式執行第一次。

3. 在應用程式中，輸入任務，然後按一下加號 （+） 圖示。

4. 請確認會收到通知，然後按一下 [確定] 關閉通知。

##<a name="optional-configure-and-run-on-windows"></a>（選用）設定和 Windows 上執行

本節適用於 （PhoneGap 推入外掛程式支援在 Windows 10） 的 Windows 10 裝置上執行 Apache Cordova 應用程式專案。 如果您不使用 Windows 裝置，您可以略過此節。

####<a name="register-your-windows-app-for-push-notifications-with-wns"></a>以 WNS 註冊您的 Windows 應用程式推播通知

若要在 Visual Studio 中使用 [儲存] 選項，請選取 Windows 目標方案平台清單中，例如**Windows x64**或**Windows x86** （推播通知避免**Windows AnyCPU** ）。

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[觀看影片，顯示類似的步驟](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-6-Set-up-wns-for-push)

####<a name="configure-the-notification-hub-for-wns"></a>針對 WNS 設定通知中心

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

####<a name="configure-your-cordova-app-to-support-windows-push-notifications"></a>設定您的 Cordova 應用程式支援 Windows 推入通知

開啟設定設計工具 （用滑鼠右鍵按一下 config.xml 和選取的**檢視設計工具**），選取 [ **Windows** ] 索引標籤並選擇底下**Windows 目標版本**的**Windows 10** 。

>[AZURE.NOTE] 如果您使用的 Cordova 之前的版本 Cordova 5.1.1 (6.1.1 建議使用)，您也必須為 true config.xml 中設定的快顯簡訊旗標。

若要支援推播通知在您的預設 （偵錯） 中建立時，開啟 build.json 檔案。 「 發佈 」 設定複製到您偵錯的設定。

        "windows": {
            "release": {
                "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
                "publisherId": "CN=yourpublisherID"
            }
        }

更新後、 前面的程式碼看起來應該像這樣。

    "windows": {
        "release": {
            "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
            "publisherId": "CN=yourpublisherID"
            },
        "debug": {
            "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
            "publisherId": "CN=yourpublisherID"
            }
        }

建立應用程式，並確認您有沒有錯誤。 您用戶端應用程式現在應該註冊 Mobile 應用程式後端通知。 在您的方案中每個 Windows 專案重複此區段。

####<a name="test-push-notifications-in-your-windows-app"></a>測試您的 Windows 應用程式的推播通知

在 Visual Studio 中，請確定已選取為部署目標，例如**Windows x64**或**Windows x86**的 Windows 平台。 若要在 Windows 10 的電腦上，裝載 Visual Studio 中執行應用程式，請選擇 [**本機電腦**]。

按下建立專案並啟動應用程式的 [執行] 按鈕。

在應用程式中，輸入新的 todoitem 的名稱，然後按一下加號 （+） 圖示，將它加入。

確認 [新增項目時收到的通知]。

##<a name="next-steps"></a>後續步驟

* 閱讀有關[通知集線器]若要深入瞭解推播通知。
* 如果您有不這麼做，繼續教學課程所[新增的驗證]您 Apache Cordova 應用程式。

瞭解如何使用 Sdk。

* [Apache Cordova SDK]
* [ASP.NET 伺服器 SDK]
* [Node.js 伺服器 SDK]

<!-- URLs -->
[新增驗證]: app-service-mobile-cordova-get-started-users.md
[Apache Cordova 快速入門]: app-service-mobile-cordova-get-started.md
[authentication]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Google 帳戶]: http://go.microsoft.com/fwlink/p/?LinkId=268302
[Google 開發人員主控台]: https://console.developers.google.com/home/dashboard
[推入外掛程式 phonegap 安裝文件]: https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md
[Mobizen]: https://www.mobizen.com/
[Visual Studio 社群 2015]: http://www.visualstudio.com/
[Visual Studio Apache Cordova 工具]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[通知集線器]: ../notification-hubs/notification-hubs-push-notification-overview.md
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET 伺服器 SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js 伺服器 SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
