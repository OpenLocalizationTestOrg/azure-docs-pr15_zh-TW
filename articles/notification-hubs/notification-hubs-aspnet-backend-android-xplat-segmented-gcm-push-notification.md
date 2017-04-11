<properties
    pageTitle="通知集線器最新消息教學課程-Android"
    description="瞭解如何使用 Azure 服務匯流排通知集線器相關消息通知傳送到 Android 的裝置。"
    services="notification-hubs"
    documentationCenter="android"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>


# <a name="use-notification-hubs-to-send-breaking-news"></a>使用通知集線器傳送相關消息

[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

##<a name="overview"></a>概觀

本主題說明如何使用 Azure 通知集線器廣播至 Android 應用程式的相關消息通知。 完成時，您將無法註冊中斷您感興趣的新聞類別，並接收只推入通知這些類別。 這種情況下是許多應用程式的常見模式通知傳送至先前已經宣告它們，例如 RSS 讀取程式、 應用程式的音樂風扇等感興趣的使用者群組有的位置。

在 [通知] 中心建立註冊時，將一或多個_標籤_啟用廣播的案例。 當通知會傳送至標籤中時，所有的裝置註冊標記會收到通知。 標籤是只是字串，因為他們沒有事先佈建。 如需標記的詳細資訊，請參閱[通知集線器路由和標籤運算式](notification-hubs-tags-segment-push-message.md)。


##<a name="prerequisites"></a>必要條件

本主題是根據您在[開始使用通知集線器]建立的應用程式[get-started]。 在開始之前本教學課程中，您必須已經完成[快速入門通知集線器][get-started]。

##<a name="add-category-selection-to-the-app"></a>類別選取項目新增至應用程式

第一個步驟是將使用者介面元素新增至您現有的主要活動，可讓使用者選取登錄的類別。 使用者選取類別會儲存在裝置上。 應用程式啟動時，就會在您的通知中心內，選取類別與裝置註冊建立為標記。

1. 開啟您 res/layout/activity_main.xml 的檔案，和替代以下列內容︰

        <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
            xmlns:tools="http://schemas.android.com/tools"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:paddingBottom="@dimen/activity_vertical_margin"
            android:paddingLeft="@dimen/activity_horizontal_margin"
            android:paddingRight="@dimen/activity_horizontal_margin"
            android:paddingTop="@dimen/activity_vertical_margin"
            tools:context="com.example.breakingnews.MainActivity"
            android:orientation="vertical">

                <CheckBox
                    android:id="@+id/worldBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_world" />
                <CheckBox
                    android:id="@+id/politicsBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_politics" />
                <CheckBox
                    android:id="@+id/businessBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_business" />
                <CheckBox
                    android:id="@+id/technologyBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_technology" />
                <CheckBox
                    android:id="@+id/scienceBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_science" />
                <CheckBox
                    android:id="@+id/sportsBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_sports" />
                <Button
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:onClick="subscribe"
                    android:text="@string/button_subscribe" />
        </LinearLayout>

2. 開啟 res/values/strings.xml 檔案，然後將下列行︰

        <string name="button_subscribe">Subscribe</string>
        <string name="label_world">World</string>
        <string name="label_politics">Politics</string>
        <string name="label_business">Business</string>
        <string name="label_technology">Technology</string>
        <string name="label_science">Science</string>
        <string name="label_sports">Sports</string>

    您 main_activity.xml 圖形的版面配置現在看起來應該像這樣︰

    ![][A1]

3. 現在相同的封裝為**MainActivity**課程中建立課程**通知**。

        import java.util.HashSet;
        import java.util.Set;

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.os.AsyncTask;
        import android.util.Log;
        import android.widget.Toast;

        import com.google.android.gms.gcm.GoogleCloudMessaging;
        import com.microsoft.windowsazure.messaging.NotificationHub;

        public class Notifications {
            private static final String PREFS_NAME = "BreakingNewsCategories";
            private GoogleCloudMessaging gcm;
            private NotificationHub hub;
            private Context context;
            private String senderId;

            public Notifications(Context context, String senderId, String hubName, 
                                    String listenConnectionString) {
                this.context = context;
                this.senderId = senderId;
        
                gcm = GoogleCloudMessaging.getInstance(context);
                hub = new NotificationHub(hubName, listenConnectionString, context);
            }

            public void storeCategoriesAndSubscribe(Set<String> categories)
            {
                SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
                settings.edit().putStringSet("categories", categories).commit();
                subscribeToCategories(categories);
            }

            public Set<String> retrieveCategories() {
                SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
                return settings.getStringSet("categories", new HashSet<String>());
            }

            public void subscribeToCategories(final Set<String> categories) {
                new AsyncTask<Object, Object, Object>() {
                    @Override
                    protected Object doInBackground(Object... params) {
                        try {
                            String regid = gcm.register(senderId);
        
                            String templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";
        
                            hub.registerTemplate(regid,"simpleGCMTemplate", templateBodyGCM, 
                                categories.toArray(new String[categories.size()]));
                        } catch (Exception e) {
                            Log.e("MainActivity", "Failed to register - " + e.getMessage());
                            return e;
                        }
                        return null;
                    }
        
                    protected void onPostExecute(Object result) {
                        String message = "Subscribed for categories: "
                                + categories.toString();
                        Toast.makeText(context, message,
                                Toast.LENGTH_LONG).show();
                    }
                }.execute(null, null, null);
            }

        }

    此課程會使用本機存放區來儲存此裝置已接收新聞的類別。 同時也包含註冊這些類別的方法。


4. **MainActivity**課堂中移除您私人的欄位**NotificationHub**和**GoogleCloudMessaging**，並**通知**新增欄位︰

        // private GoogleCloudMessaging gcm;
        // private NotificationHub hub;
        private Notifications notifications;

5. 然後，在**onCreate**方法中，移除初始化**集線器**欄位和**registerWithNotificationHubs**方法。 然後新增下列幾行的初始化**通知**類別的執行個體。 


        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            MyHandler.mainActivity = this;
    
            NotificationsManager.handleNotifications(this, SENDER_ID,
                    MyHandler.class);
    
            notifications = new Notifications(this, SENDER_ID, HubName, HubListenConnectionString);
    
            notifications.subscribeToCategories(notifications.retrieveCategories());
        }

    `HubName`與`HubListenConnectionString`已以設定`<hub name>`和`<connection string with listen access>`版面配置區，您的通知中心名稱與*DefaultListenSharedAccessSignature*取得較舊版本的連接字串。

    > [AZURE.NOTE] 因為隨附於用戶端應用程式的認證通常安全，，您只應該使用您的用戶端應用程式發佈接聽存取鍵。 聆聽存取可讓您的應用程式註冊通知，但現有登錄無法修改，且不傳送通知。 完整便捷鍵會傳送通知，及變更現有的登錄受到保護的後端服務中使用。


6. 然後，新增下列匯入和`subscribe`按一下 [事件] 來處理 [訂閱] 按鈕的方法︰
        
        import android.widget.CheckBox;
        import java.util.HashSet;
        import java.util.Set;

        public void subscribe(View sender) {
            final Set<String> categories = new HashSet<String>();

            CheckBox world = (CheckBox) findViewById(R.id.worldBox);
            if (world.isChecked())
                categories.add("world");
            CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
            if (politics.isChecked())
                categories.add("politics");
            CheckBox business = (CheckBox) findViewById(R.id.businessBox);
            if (business.isChecked())
                categories.add("business");
            CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
            if (technology.isChecked())
                categories.add("technology");
            CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
            if (science.isChecked())
                categories.add("science");
            CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
            if (sports.isChecked())
                categories.add("sports");

            notifications.storeCategoriesAndSubscribe(categories);
        }

    這個方法所建立的類別清單，並使用儲存在本機存放區中的清單，並登錄的相對應的標籤通知中心的**通知**類別。 變更類別時, 註冊將重新建立新的類別。

您的應用程式現在能夠將類別的一組儲存在本機裝置上的儲存空間，並登錄 [通知] 中心每次使用者變更的類別選取範圍。

##<a name="register-for-notifications"></a>註冊通知

在啟動時使用的類別已經儲存在本機存放區中的 [通知] 中心登錄這些步驟。

> [AZURE.NOTE] 因為指派的 Google 雲端訊息 (GCM) registrationId 可以隨時變更，您應該註冊經常為避免通知失敗的通知。 此範例中登錄應用程式啟動每次通知。 經常執行的應用程式，超過一天，您可以可能略過註冊，若要保留的頻寬，如果小於某一天已經過了後的前一個註冊。


1. 新增下列程式碼結尾的**MainActivity**類別中**onCreate**方法︰

        notifications.subscribeToCategories(notifications.retrieveCategories());

    如此可確保的每次應用程式啟動時，擷取類別從本機存放區，並要求 registeration 這些類別。 

2. 然後更新`onStart()`的方法`MainActivity`課程，如下所示︰

    @Override受保護的 void onStart() {super.onStart();     isVisible = true;

        Set<String> categories = notifications.retrieveCategories();

        CheckBox world = (CheckBox) findViewById(R.id.worldBox);
        world.setChecked(categories.contains("world"));
        CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
        politics.setChecked(categories.contains("politics"));
        CheckBox business = (CheckBox) findViewById(R.id.businessBox);
        business.setChecked(categories.contains("business"));
        CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
        technology.setChecked(categories.contains("technology"));
        CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
        science.setChecked(categories.contains("science"));
        CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
        sports.setChecked(categories.contains("sports"));
    }

    這會更新主根據先前儲存的類別的狀態的活動。

應用程式現已完成，可以將類別的一組儲存在每次使用者變更的類別選取 [通知] 中心註冊使用的裝置本機儲存區。 接下來，我們會定義後端可以將類別通知傳送到此應用程式。

##<a name="sending-tagged-notifications"></a>傳送的標記的通知

[AZURE.INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

##<a name="run-the-app-and-generate-notifications"></a>執行應用程式，並產生通知

1. 在 Android Studio 中，建立應用程式並在裝置或模擬器上啟動它。

    附註的應用程式使用者介面，提供一組切換，可讓您選擇要訂閱的類別。

2. 啟用一或多個類別切換，然後按一下 [**訂閱**]。

    應用程式會將所選的類別轉換成標籤，然後從 [通知] 中心要求，選取標籤的新裝置註冊。 註冊的類別會傳回，並顯示在快顯通知。

4. 執行的.NET Console 應用程式，傳送新的通知。  或者，您可以傳送標記的範本通知[Azure 傳統入口網站]中使用您的通知中心的 [偵錯] 索引標籤。

    選取類別的通知會顯示為快顯通知。

##<a name="next-steps"></a>後續步驟

在本教學課程中，我們學會如何依類別廣播相關消息。 請考慮完成下列教學課程醒目提示其他進階的通知集線器案例的其中一項︰

+ [使用通知集線器廣播本地化的相關消息]

    瞭解如何展開相關消息應用程式，才能傳送的本地化的通知。





<!-- Images. -->
[A1]: ./media/notification-hubs-aspnet-backend-android-breaking-news/android-breaking-news1.PNG

<!-- URLs.-->
[get-started]: notification-hubs-android-push-notification-google-gcm-get-started.md
[使用通知集線器廣播本地化的相關消息]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Azure 傳統入口網站]: https://manage.windowsazure.com
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
