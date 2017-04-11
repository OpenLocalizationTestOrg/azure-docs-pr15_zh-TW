<properties
    pageTitle="傳送與 Azure 通知集線器安全的推播通知"
    description="瞭解如何從 Azure 傳送至 Android 應用程式的安全的推播通知。 撰寫 Java 及 C# 程式碼範例。"
    documentationCenter="android"
    keywords="推播通知，推入通知，推播 android 推入通知的郵件"
    authors="ysxu"
    manager="erikre"
    editor=""
    services="notification-hubs"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>

#<a name="sending-secure-push-notifications-with-azure-notification-hubs"></a>傳送與 Azure 通知集線器安全的推播通知

> [AZURE.SELECTOR]
- [Windows 標準](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
- [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
- [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

##<a name="overview"></a>概觀

> [AZURE.IMPORTANT] 若要完成此教學課程中，您必須使用中的 Azure 帳戶。 如果您沒有帳戶，您可以建立的免費的試用帳戶在幾分鐘。 如需詳細資訊，請參閱[Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started)。

Microsoft Azure 中的推入通知支援可讓您存取大幅簡化的行動裝置平台的同時消費者版和企業版應用程式的推播通知方便使用多平台、 縮放出推入訊息基礎結構。

由於法規或安全性的限制式，有時候應用程式可能會想要併入項目無法傳送透過標準推入通知基礎結構的通知。 本教學課程說明如何藉由傳送透過安全、 已驗證的用戶端的 Android 裝置與應用程式後端之間連線的機密資訊達成相同的體驗。

在高的層級，流程如下︰

1. 應用程式後端︰
    - 儲存安全後端資料庫中的內容。
    - 在 Android 裝置 （不安全的資訊會傳送） 傳送此通知的識別碼。
2. 在裝置上時收到通知, 應用程式︰
    - 在 Android 裝置連絡人後端要求安全的內容。
    - 應用程式通知，以在裝置上的方式顯示的內容。

請務必請注意，上述 flow 中 （和在本教學課程），我們假設的裝置會儲存驗證權杖在本機的儲存空間，在使用者登之後。 這可以確保完全順暢的使用體驗，如裝置可擷取的通知安全的內容，使用此 token 加。 如果您的應用程式不會儲存在裝置上，驗證的權杖，或如果可以是這些權杖，裝置應用程式時，接收推入通知時，應該顯示一般通知提示使用者以啟動應用程式。 然後，應用程式授權的使用者，並顯示通知內容。

本教學課程中會顯示如何傳送安全的推播通知。 它會根據[通知使用者](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md)教學課程，因此您應該先完成步驟在教學課程中的第一次若您尚未這麼。

> [AZURE.NOTE] 本教學課程假設您建立，並[開始使用 (Android) 的通知集線器](notification-hubs-android-push-notification-google-gcm-get-started.md)所述設定您的通知中心。

[AZURE.INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-android-project"></a>修改 Android 的專案

現在，您修改您應用程式後端傳送只*識別碼*的推入通知，您必須變更控點的通知及撥] 以回您後端擷取安全性訊息以顯示您 Android 應用程式。
若要達到這個目的，必須以確定您的 Android 應用程式知道如何接收推入通知時使用您的後端驗證本身。

我們會立即修改*登入*流程，才能共用您的應用程式的喜好設定中儲存的驗證標頭值。 若要儲存任何應用程式必須使用不需要使用者認證的驗證權杖 （例如 OAuth 權杖） 可類似機制。

1. 在您的 Android 應用程式的專案，新增下列常數頂端的**MainActivity**類別︰

        public static final String NOTIFY_USERS_PROPERTIES = "NotifyUsersProperties";
        public static final String AUTHORIZATION_HEADER_PROPERTY = "AuthorizationHeader";

2. 仍在**MainActivity**類別中，更新`getAuthorizationHeader()`包含下列程式碼的方法︰

        private String getAuthorizationHeader() throws UnsupportedEncodingException {
            EditText username = (EditText) findViewById(R.id.usernameText);
            EditText password = (EditText) findViewById(R.id.passwordText);
            String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
            basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);

            SharedPreferences sp = getSharedPreferences(NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
            sp.edit().putString(AUTHORIZATION_HEADER_PROPERTY, basicAuthHeader).commit();

            return basicAuthHeader;
        }

3. 新增下列`import` **MainActivity**檔案頂端的陳述式︰

        import android.content.SharedPreferences;

現在，我們會變更稱為時收到通知的處理常式。

4. 在**MyHandler**課程變更`OnReceive()`包含的方法︰

        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String secureMessageId = bundle.getString("secureId");
            retrieveNotification(secureMessageId);
        }

5. 然後新增`retrieveNotification()`取代版面配置區的方法，`{back-end endpoint}`與後端端點時部署後端取得︰

        private void retrieveNotification(final String secureMessageId) {
            SharedPreferences sp = ctx.getSharedPreferences(MainActivity.NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
            final String authorizationHeader = sp.getString(MainActivity.AUTHORIZATION_HEADER_PROPERTY, null);

            new AsyncTask<Object, Object, Object>() {
                @Override
                protected Object doInBackground(Object... params) {
                    try {
                        HttpUriRequest request = new HttpGet("{back-end endpoint}/api/notifications/"+secureMessageId);
                        request.addHeader("Authorization", "Basic "+authorizationHeader);
                        HttpResponse response = new DefaultHttpClient().execute(request);
                        if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                            Log.e("MainActivity", "Error retrieving secure notification" + response.getStatusLine().getStatusCode());
                            throw new RuntimeException("Error retrieving secure notification");
                        }
                        String secureNotificationJSON = EntityUtils.toString(response.getEntity());
                        JSONObject secureNotification = new JSONObject(secureNotificationJSON);
                        sendNotification(secureNotification.getString("Payload"));
                    } catch (Exception e) {
                        Log.e("MainActivity", "Failed to retrieve secure notification - " + e.getMessage());
                        return e;
                    }
                    return null;
                }
            }.execute(null, null, null);
        }


這個方法會撥打您應用程式後端擷取通知內容使用共用的喜好設定中所儲存的認證，並顯示為標準通知。 通知的外觀如同任何其他推入通知應用程式使用者。

請注意，最好的後端處理遺失驗證標題屬性或拒絕的情況。 這種情況下的特定處理大多取決於您目標的使用者體驗。 其中一個選項是顯示通知和一般提示要驗證的使用者来擷取的實際的通知。

## <a name="run-the-application"></a>執行應用程式

若要執行應用程式，執行下列動作︰

1. 請確定**AppBackend**部署 Azure 中。 如果使用 Visual Studio，請執行**AppBackend** API Web 應用程式。 ASP.NET web 頁面會顯示。

2. 在蝕，執行應用程式在實體的 Android 裝置或模擬器。

3. 在 Android 應用程式使用者介面，輸入使用者名稱和密碼。 這些可以是任何字串，但必須相同的值。

4. 在 Android 應用程式使用者介面，按一下 [**登入**]。 然後按一下 [**傳送推入**]。
