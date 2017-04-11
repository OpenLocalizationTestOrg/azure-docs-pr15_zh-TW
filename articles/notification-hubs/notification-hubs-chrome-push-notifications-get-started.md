<properties
    pageTitle="推播通知傳送至 Azure 通知集線器與 Chrome 應用程式 |Microsoft Azure"
    description="瞭解如何使用 Azure 通知集線器推入通知傳送至 Chrome 應用程式。"
    services="notification-hubs"
    keywords="mobile 推入通知，推入通知，推入通知，chrome 推入通知"
    documentationCenter=""
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-chrome"
    ms.devlang="JavaScript"
    ms.topic="hero-article"
    ms.date="10/03/2016"
    ms.author="yuaxu"/>

# <a name="send-push-notifications-to-chrome-apps-with-azure-notification-hubs"></a>Chrome 應用程式與 Azure 通知集線器傳送推入通知

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

本主題說明如何使用 Azure 通知集線器推入通知傳送至 Chrome 應用程式，將會顯示在 Google Chrome 瀏覽器的內容。 在本教學課程中，我們會建立 Chrome 應用程式會使用[Google 雲端訊息 (GCM)](https://developers.google.com/cloud-messaging/)接收推入通知。 

>[AZURE.NOTE] 若要完成此教學課程中，您必須使用中的 Azure 帳戶。 如果您沒有帳戶，您可以建立的免費的試用帳戶在幾分鐘。 如需詳細資訊，請參閱[Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%notification-hubs-chrome-get-started%2F)。

教學課程逐步引導您進行基本步驟，以啟用 [推入通知︰

* [啟用 Google 雲端訊息](#register)
* [設定您的通知中心](#configure-hub)
* [將您的 Chrome 應用程式連線到 [通知] 中心](#connect-app)
* [推入通知傳送至您的 Chrome 應用程式](#send)
* [其他功能與功能](#next-steps)

>[AZURE.NOTE] Chrome 應用程式推入通知不是一般的在瀏覽器通知-各瀏覽器擴充模型 （如需詳細資訊，請參閱[Chrome 應用程式概觀]）。 桌面瀏覽器中，除了 Chrome 應用程式 （Android 和 iOS） 的行動電話上執行透過 Apache Cordova。 請參閱若要深入瞭解[在行動電話上的 Chrome 應用程式]。

設定 GCM 和 Azure 通知集線器是相同設定 for Android 中，由於已被取代 [ [Google 雲端訊息若是 Chrome] ，相同的 GCM 現在支援在 Android 裝置和 Chrome 執行個體。

##<a id="register"></a>啟用 Google 雲端訊息

1. 瀏覽至 [ [Google 雲端主控台]網站，您的 Google 帳戶認證，登入，然後按一下 [**建立專案**] 按鈕。 提供適當的**專案名稱**，然後再按一下 [**建立**] 按鈕。

    ![Google 雲端主控台-建立專案][1]

2. 記下**專案數字**的上您剛剛建立的專案的 [**專案**] 頁面。 您會為**GCM 寄件者識別碼**的 Chrome 應用程式中使用此選項以 GCM 註冊。

    ![Google 雲端主控台-專案數字][2]

3. 在左窗格中，按一下**Api 與驗證**]，然後向下捲動，並按一下切換] 以啟用 [ **Android 適用的 Google 雲端訊息**。 您沒有啟用**若是 Chrome 的 Google 雲端訊息**。

    ![Google 雲端主控台-伺服器機碼][3]

4. 在左窗格中，按一下 [**認證** > **建立新的金鑰** > **伺服器金鑰** > **建立**。

    ![Google 雲端主控台-認證][4]

5. 記下伺服器**API 金鑰**。 您將會設定這在您的通知中心接下來，請將它傳送推入通知給 GCM 啟用。

    ![Google 雲端主控台-API 金鑰][5]

##<a id="configure-hub"></a>設定您的通知中心

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


&emsp;&emsp;6.在中**設定**刀中，選取 [**通知服務**]，然後按一下 [ **Google (GCM)**。 輸入 API 金鑰並儲存。

&emsp;&emsp;![Azure 通知集線器-Google (GCM)](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

##<a id="connect-app"></a>將您的 Chrome 應用程式連線到 [通知] 中心

您的通知中心正在設定以使用 GCM，且您擁有註冊您的應用程式收到和傳送推入通知的連線字串。 LK

###<a name="create-a-new-chrome-app"></a>建立新的 Chrome 應用程式

下列範例會根據[Chrome 應用程式 GCM 範例]，並使用建立 Chrome 應用程式的建議的方式。 我們將會醒目提示與相關 Azure 通知集線器的步驟。 

>[AZURE.NOTE] 我們建議您從[Chrome 應用程式通知中心範例]此 Chrome 應用程式下載來源。

Chrome 應用程式會建立透過 JavaScript]，然後您可以使用您慣用的 word 編輯器建立它。 以下是此 Chrome 應用程式的外觀。

![Google Chrome 應用程式][15]

1. 建立資料夾，並將其命名`ChromePushApp`。 當然，名稱為任意-如果您將其命名不同的項目，請確定您使用替代必要的程式碼區段中的路徑。

2. 下載您在第二個步驟建立的資料夾中的[加密 js 文件庫]。 此文件庫資料夾會包含兩個資料夾︰`components`和`rollups`。

3. 建立`manifest.json`檔案。 備份所有 Chrome 應用程式的資訊清單的檔案所包含的應用程式中繼資料和最重要的是，所有的權限授與應用程式時使用者安裝它。

        {
          "name": "NH-GCM Notifications",
          "description": "Chrome platform app.",
          "manifest_version": 2,
          "version": "0.1",
          "app": {
            "background": {
              "scripts": ["background.js"]
            }
          },
          "permissions": ["gcm", "storage", "notifications", "https://*.servicebus.windows.net/*"],
          "icons": { "128": "gcm_128.png" }
        }

    請注意`permissions`項目，請指定此 Chrome 應用程式都能收到來自 GCM 推入通知。 它也必須指定位置 Chrome 應用程式會撥打電話其餘註冊 Azure 通知集線器 URI。
    我們的範例應用程式也會使用圖示檔， `gcm_128.png`，您會發現在來源與原始 GCM 範例重複使用的。 您可以用它取代可以容納[圖示準則](https://developer.chrome.com/apps/manifest/icons)的任何影像。

4. 建立檔案稱為`background.js`下列程式碼︰

        // Returns a new notification ID used in the notification.
        function getNotificationId() {
          var id = Math.floor(Math.random() * 9007199254740992) + 1;
          return id.toString();
        }

        function messageReceived(message) {
          // A message is an object with a data property that
          // consists of key-value pairs.

          // Concatenate all key-value pairs to form a display string.
          var messageString = "";
          for (var key in message.data) {
            if (messageString != "")
              messageString += ", "
            messageString += key + ":" + message.data[key];
          }
          console.log("Message received: " + messageString);

          // Pop up a notification to show the GCM message.
          chrome.notifications.create(getNotificationId(), {
            title: 'GCM Message',
            iconUrl: 'gcm_128.png',
            type: 'basic',
            message: messageString
          }, function() {});
        }

        var registerWindowCreated = false;

        function firstTimeRegistration() {
          chrome.storage.local.get("registered", function(result) {

            registerWindowCreated = true;
            chrome.app.window.create(
              "register.html",
              {  width: 520,
                 height: 500,
                 frame: 'chrome'
              },
              function(appWin) {}
            );
          });
        }

        // Set up a listener for GCM message event.
        chrome.gcm.onMessage.addListener(messageReceived);

        // Set up listeners to trigger the first-time registration.
        chrome.runtime.onInstalled.addListener(firstTimeRegistration);
        chrome.runtime.onStartup.addListener(firstTimeRegistration);

    這是彈出 Chrome 應用程式視窗 HTML (**register.html**)，而且也會定義處理的內送的推入通知的處理常式**messageReceived**的檔案。

5. 建立檔案稱為`register.html`-定義的 Chrome 應用程式在 ui。 

   >[AZURE.NOTE] 此範例使用**CryptoJS v3.1.2**。 如果您已下載的文件庫另一個版本，請確定您正確替代中的版本`src`路徑。

        <html>

        <head>
        <title>GCM Registration</title>
        <script src="register.js"></script>
        <script src="CryptoJS v3.1.2/rollups/hmac-sha256.js"></script>
        <script src="CryptoJS v3.1.2/components/enc-base64-min.js"></script>
        </head>

        <body>

        Sender ID:<br/><input id="senderId" type="TEXT" size="20"><br/>
        <button id="registerWithGCM">Register with GCM</button>
        <br/>
        <br/>
        <br/>

        Notification Hub Name:<br/><input id="hubName" type="TEXT" style="width:400px"><br/><br/>
        Connection String:<br/><textarea id="connectionString" type="TEXT" style="width:400px;height:60px"></textarea>

        <br/>

        <button id="registerWithNH" disabled="true">Register with Azure Notification Hubs</button>

        <br/>
        <br/>

        <textarea id="console" type="TEXT" readonly style="width:500px;height:200px;background-color:#e5e5e5;padding:5px"></textarea>

        </body>

        </html>

6. 建立檔案稱為`register.js`與下列程式碼。 此檔案指定後面的指令碼`register.html`。 Chrome 應用程式不允許內嵌執行，因此您不必自行建立 UI 個別支援指令碼。

        var registrationId = "";
        var hubName        = "", connectionString = "";
        var originalUri    = "", targetUri = "", endpoint = "", sasKeyName = "", sasKeyValue = "", sasToken = "";

        window.onload = function() {
           document.getElementById("registerWithGCM").onclick = registerWithGCM;  
           document.getElementById("registerWithNH").onclick = registerWithNH;
           updateLog("You have not registered yet. Please provider sender ID and register with GCM and then with  Notification Hubs.");
        }

        function updateLog(status) {
          currentStatus = document.getElementById("console").innerHTML;
          if (currentStatus != "") {
            currentStatus = currentStatus + "\n\n";
          }

          document.getElementById("console").innerHTML = currentStatus  + status;
        }

        function registerWithGCM() {
          var senderId = document.getElementById("senderId").value.trim();
          chrome.gcm.register([senderId], registerCallback);

          // Prevent register button from being clicked again before the registration finishes.
          document.getElementById("registerWithGCM").disabled = true;
        }

        function registerCallback(regId) {
          registrationId = regId;
          document.getElementById("registerWithGCM").disabled = false;

          if (chrome.runtime.lastError) {
            // When the registration fails, handle the error and retry the
            // registration later.
            updateLog("Registration failed: " + chrome.runtime.lastError.message);
            return;
          }

          updateLog("Registration with GCM succeeded.");
          document.getElementById("registerWithNH").disabled = false;

          // Mark that the first-time registration is done.
          chrome.storage.local.set({registered: true});
        }

        function registerWithNH() {
          hubName = document.getElementById("hubName").value.trim();
          connectionString = document.getElementById("connectionString").value.trim();
          splitConnectionString();
          generateSaSToken();
          sendNHRegistrationRequest();
        }

        // From http://msdn.microsoft.com/library/dn495627.aspx
        function splitConnectionString()
        {
          var parts = connectionString.split(';');
          if (parts.length != 3)
          throw "Error parsing connection string";

          parts.forEach(function(part) {
            if (part.indexOf('Endpoint') == 0) {
            endpoint = 'https' + part.substring(11);
            } else if (part.indexOf('SharedAccessKeyName') == 0) {
            sasKeyName = part.substring(20);
            } else if (part.indexOf('SharedAccessKey') == 0) {
            sasKeyValue = part.substring(16);
            }
          });

          originalUri = endpoint + hubName;
        }

        function generateSaSToken()
        {
          targetUri = encodeURIComponent(originalUri.toLowerCase()).toLowerCase();
          var expiresInMins = 10; // 10 minute expiration

          // Set expiration in seconds.
          var expireOnDate = new Date();
          expireOnDate.setMinutes(expireOnDate.getMinutes() + expiresInMins);
          var expires = Date.UTC(expireOnDate.getUTCFullYear(), expireOnDate
            .getUTCMonth(), expireOnDate.getUTCDate(), expireOnDate
            .getUTCHours(), expireOnDate.getUTCMinutes(), expireOnDate
            .getUTCSeconds()) / 1000;
          var tosign = targetUri + '\n' + expires;

          // Using CryptoJS.
          var signature = CryptoJS.HmacSHA256(tosign, sasKeyValue);
          var base64signature = signature.toString(CryptoJS.enc.Base64);
          var base64UriEncoded = encodeURIComponent(base64signature);

          // Construct authorization string.
          sasToken = "SharedAccessSignature sr=" + targetUri + "&sig="
                          + base64UriEncoded + "&se=" + expires + "&skn=" + sasKeyName;
        }

        function sendNHRegistrationRequest()
        {
          var registrationPayload =
          "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
          "<entry xmlns=\"http://www.w3.org/2005/Atom\">" +
              "<content type=\"application/xml\">" +
                  "<GcmRegistrationDescription xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/netservices/2010/10/servicebus/connect\">" +
                      "<GcmRegistrationId>{GCMRegistrationId}</GcmRegistrationId>" +
                  "</GcmRegistrationDescription>" +
              "</content>" +
          "</entry>";

          // Update the payload with the registration ID obtained earlier.
          registrationPayload = registrationPayload.replace("{GCMRegistrationId}", registrationId);

          var url = originalUri + "/registrations/?api-version=2014-09";
          var client = new XMLHttpRequest();

          client.onload = function () {
            if (client.readyState == 4) {
              if (client.status == 200) {
                updateLog("Notification Hub Registration succesful!");
                updateLog(client.responseText);
              } else {
                updateLog("Notification Hub Registration did not succeed!");
                updateLog("HTTP Status: " + client.status + " : " + client.statusText);
                updateLog("HTTP Response: " + "\n" + client.responseText);
              }
            }
          };

          client.onerror = function () {
                updateLog("ERROR - Notification Hub Registration did not succeed!");
          }

          client.open("POST", url, true);
          client.setRequestHeader("Content-Type", "application/atom+xml;type=entry;charset=utf-8");
          client.setRequestHeader("Authorization", sasToken);
          client.setRequestHeader("x-ms-version", "2014-09");

          try {
              client.send(registrationPayload);
          }
          catch(err) {
              updateLog(err.message);
          }
        }

    上述的指令碼具有下列參數︰
    - **window.onload**定義按一下按鈕事件的兩個按鈕的 UI 上。 您使用 GCM，其中一個註冊，另會傳回與註冊 Azure 通知集線器 GCM 註冊後的註冊識別碼。
    - **updateLog**是函數，以讓我們來處理簡單的記錄功能。
    - **registerWithGCM**是第一份按一下按鈕處理常式，這會使`chrome.gcm.register`撥 GCM 註冊目前 Chrome 應用程式執行個體。
    - **registerCallback**是回撥函數所傳回 GCM 註冊通話時呼叫。
    - **registerWithNH**是第二個按一下按鈕處理常式，使用通知集線器註冊。 它會取得`hubName`和`connectionString`（哪些使用者所指定） 和花瓶通知集線器註冊 REST API 通話。
    - **splitConnectionString**和**generateSaSToken**是代表 JavaScript 實作 Sa token 建立程序，必須在所有的 REST API 通話中使用的程式。 如需詳細資訊，請參閱[常見的概念](http://msdn.microsoft.com/library/dn495627.aspx)。
    - **sendNHRegistrationRequest**是 Azure 通知集線器進行呼叫 HTTP 其餘的函數。
    - **registrationPayload**定義註冊 XML 內容。 如需詳細資訊，請參閱[建立註冊 NH REST API]。 我們使用我們收到從 GCM 更新中的註冊識別碼。
    - **用戶端**是**XMLHttpRequest**了我們用來進行 HTTP 文章要求的執行個體。 請注意，我們更新`Authorization`具有標頭`sasToken`。 完成這個呼叫會註冊 Azure 通知集線器此 Chrome 應用程式執行個體。


此專案的整體資料夾結構看起來應該像這樣︰     ![Google Chrome 應用程式-資料夾結構][21]

###<a name="set-up-and-test-your-chrome-app"></a>設定及測試您的 Chrome 應用程式

1. 開啟 Chrome 瀏覽器。 開啟**Chrome 副檔名**並啟用**開發人員模式**。

    ![Google Chrome-啟用開發人員模式][16]

2. 按一下 [**載入解壓縮的分機號碼**，然後瀏覽至您用來建立檔案的資料夾。 您也可以選擇可以使用**Chrome 應用程式與副檔名開發人員工具**。 這項工具是在本身 （從 Chrome Web 市集安裝） Chrome 應用程式，並提供您 Chrome 應用程式開發進階偵錯的功能。

    ![Google Chrome-載入解壓縮的副檔名][17]

3. 如果 Chrome 應用程式建立沒有任何錯誤，您會看到顯示 Chrome 應用程式。

    ![Google Chrome-Chrome 應用程式顯示][18]

4. 輸入您先前適合從**Google 雲端主控台**的寄件者識別碼，為**專案數字**，然後按一下 [**以 GCM 註冊**]。 您必須看到訊息**GCM 成功登錄。**

    ![Google Chrome-Chrome 應用程式自訂][19]

5. 輸入您的**通知中心名稱**及更早版本，從入口網站取得**DefaultListenSharedAccessSignature** ，然後按一下 [**註冊 Azure 通知中心**。 您必須看到訊息**通知中心註冊成功 ！** 和 id 註冊的回應，其中包含 Azure 通知集線器註冊的詳細資料]。

    ![Google Chrome-指定通知中心的詳細資料][20]  

##<a name="send"></a>傳送通知給您的 Chrome 應用程式

供測試之用，我們會傳送給使用.NET Chrome 推入通知主控台應用程式。 

>[AZURE.NOTE] 您可以從任何<a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">其餘介面</a>我們公用透過的後端傳送通知集線器與推入通知。 請查看我們[的說明文件入口網站](https://azure.microsoft.com/documentation/services/notification-hubs/)的更多的跨平台範例。

1. 在 Visual Studio 中，從 [**檔案**] 功能表中，選取 [**新增**]，然後按一下 [**專案**。 在**Visual C#**，按一下 [ **Windows**和**主控台應用程式**，然後再按一下**[確定]**。  這樣會建立新的主控台應用程式專案。

2. 從 [**工具**] 功能表中，按一下 [**文件庫封裝管理員**]，然後按一下 [**封裝管理員 Console**]。 隨後便會顯示封裝管理員主控台。

3. 在 [主控台] 視窗中，執行下列命令︰

        Install-Package Microsoft.Azure.NotificationHubs

    如此會將 Azure 服務匯流排 SDK <a href="http://nuget.org/packages/  WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet 套件</a>的參考。

4. 開啟`Program.cs`並新增下列`using`陳述式︰

        using Microsoft.Azure.NotificationHubs;

5. 在 [`Program`課程，請新增下列方法︰

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            String message = "{\"data\":{\"message\":\"Hello Chrome from Azure Notification Hubs\"}}";
            await hub.SendGcmNativeNotificationAsync(message);
        }

    請務必將`<hub name>`版面配置區的名稱出現在[入口網站](https://portal.azure.com)中您的通知中心刀的通知中心。 此外，取代一部分的連接字串中的連線字串版面配置區`DefaultFullSharedAccessSignature`您在 [通知] 中心組態] 區段中得到。

    >[AZURE.NOTE] 請確定您的**完整**存取權，未**接聽**的 access 使用連接字串。 **聆聽**存取連線字串不授予權限可傳送推入通知。

5. 新增下列呼叫中的`Main`方法︰

         SendNotificationAsync();
         Console.ReadLine();
         
6. 請確定的 Chrome 正在執行，並執行主控台應用程式。

7. 您應該會看到下列通知快顯桌面上。

    ![Google Chrome-通知][13]

8. 您也可以使用 [Chrome 通知視窗的工作列中 （在 Windows) 中查看您所有的通知時執行 Chrome。

    ![Google Chrome 的通知清單][14]

>[AZURE.NOTE] 您不需要以 Chrome 應用程式執行或 （雖然必須執行 Chrome 瀏覽器本身），在瀏覽器中開啟。 您也可以存取您的通知的合併的檢視 Chrome 通知] 視窗中。

## <a name="next-steps"></a>接下來的步驟

進一步瞭解通知集線器[通知集線器]概觀。

若要針對特定的使用者，請參閱[Azure 通知集線器通知使用者]教學課程。 

如果您想要區段您感興趣的群組的使用者，您可以遵循[Azure 通知集線器最新消息]教學課程。

<!-- Images. -->
[1]: ./media/notification-hubs-chrome-get-started/GoogleConsoleCreateProject.PNG
[2]: ./media/notification-hubs-chrome-get-started/GoogleProjectNumber.png
[3]: ./media/notification-hubs-chrome-get-started/EnableGCM.png
[4]: ./media/notification-hubs-chrome-get-started/CreateServerKey.png
[5]: ./media/notification-hubs-chrome-get-started/ServerKey.png
[6]: ./media/notification-hubs-chrome-get-started/CreateNH.png
[7]: ./media/notification-hubs-chrome-get-started/NHNamespace.png
[8]: ./media/notification-hubs-chrome-get-started/NamespaceConfigure.png
[9]: ./media/notification-hubs-chrome-get-started/NHConfigure.png
[10]: ./media/notification-hubs-chrome-get-started/NHConfigureGCM.png
[11]: ./media/notification-hubs-chrome-get-started/NHDashboard.png
[12]: ./media/notification-hubs-chrome-get-started/NHConnString.png
[13]: ./media/notification-hubs-chrome-get-started/ChromeNotification.png
[14]: ./media/notification-hubs-chrome-get-started/ChromeNotificationWindow.png
[15]: ./media/notification-hubs-chrome-get-started/ChromeApp.png
[16]: ./media/notification-hubs-chrome-get-started/ChromeExtensions.png
[17]: ./media/notification-hubs-chrome-get-started/ChromeLoadExtension.png
[18]: ./media/notification-hubs-chrome-get-started/ChromeAppLoaded.png
[19]: ./media/notification-hubs-chrome-get-started/ChromeAppGCM.png
[20]: ./media/notification-hubs-chrome-get-started/ChromeAppNH.png
[21]: ./media/notification-hubs-chrome-get-started/FinalFolderView.png

<!-- URLs. -->
[Chrome 應用程式通知中心範例]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToChromeApps
[Google 雲端主控台]: http://cloud.google.com/console
[Azure Classic Portal]: https://manage.windowsazure.com/
[通知集線器概觀]: notification-hubs-push-notification-overview.md
[Chrome 應用程式概觀]: https://developer.chrome.com/apps/about_apps
[Chrome 應用程式 GCM 範例]: https://github.com/GoogleChrome/chrome-app-samples/tree/master/samples/gcm-notifications
[Installable Web Apps]: https://developers.google.com/chrome/apps/docs/
[在行動電話上的 chrome 應用程式]: https://developer.chrome.com/apps/chrome_apps_on_mobile
[建立註冊 NH REST API]: http://msdn.microsoft.com/library/azure/dn223265.aspx
[加密 js 文件庫]: http://code.google.com/p/crypto-js/
[GCM with Chrome Apps]: https://developer.chrome.com/apps/cloudMessaging
[訊息的 Chrome Google 雲端]: https://developer.chrome.com/apps/cloudMessagingV1
[Azure 通知集線器通知使用者]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Azure 通知集線器相關消息]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
