1. 在**應用程式**專案中，開啟 [檔案`AndroidManifest.xml`。 在接下來的兩個步驟中的程式碼，取代_`**my_app_package**`_專案的應用程式套件的名稱，其為的值`package`屬性`manifest`標籤。

2. 新增下列新的權限之後現存的`uses-permission`項目︰

        <permission android:name="**my_app_package**.permission.C2D_MESSAGE"
            android:protectionLevel="signature" />
        <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" />
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="android.permission.GET_ACCOUNTS" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />

3. 新增下列程式碼之後`application`開頭標記︰

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
                                        android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>


4. 開啟檔案*ToDoActivity.java*，並加入下列匯入陳述式︰

        import com.microsoft.windowsazure.notifications.NotificationsManager;


5. 將下列私用變數加入類別︰ 取代_`<PROJECT_NUMBER>`_專案數字由 Google 指派給您在上述程序中的應用程式︰

        public static final String SENDER_ID = "<PROJECT_NUMBER>";

6. 變更的*MobileServiceClient*定義從**私用****公用靜態**，現在看起來像這樣︰

        public static MobileServiceClient mClient;

7. 接下來，我們要加入新的類別，處理通知。 在 [專案總管] 中，開啟 [ **src** => **主** => **java**節點並以滑鼠右鍵按一下套件名稱節點︰ 按一下 [**新增**]，然後按一下 [ **Java 類別**。

8. 在 [**名稱**] 鍵入`MyHandler`，然後按一下**[確定]**。


    ![](./media/app-service-mobile-android-configure-push/android-studio-create-class.png)


9. 在 [MyHandler 檔案，取代與類別宣告

        public class MyHandler extends NotificationsHandler {


10. 新增下列匯入陳述式的`MyHandler`類別︰

        import com.microsoft.windowsazure.notifications.NotificationsHandler;
        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.os.AsyncTask;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;


11. 下一步新增成員] 此`MyHandler`類別︰

        public static final int NOTIFICATION_ID = 1;


12. 在 [`MyHandler`課程，請新增下列程式碼，覆寫**onRegistered**的方法，使用行動訊息服務通知中心註冊您的裝置。

        @Override
        public void onRegistered(Context context,  final String gcmRegistrationId) {
            super.onRegistered(context, gcmRegistrationId);

            new AsyncTask<Void, Void, Void>() {

                protected Void doInBackground(Void... params) {
                    try {
                        ToDoActivity.mClient.getPush().register(gcmRegistrationId);
                        return null;
                    }
                    catch(Exception e) {
                        // handle error             
                    }
                    return null;            
                }
            }.execute();
        }


13. 在`MyHandler`課程，請新增下列程式碼，覆寫**onReceive**方法，使顯示時收到通知。

        @Override
        public void onReceive(Context context, Bundle bundle) {
                String msg = bundle.getString("message");

                PendingIntent contentIntent = PendingIntent.getActivity(context,
                        0, // requestCode
                        new Intent(context, ToDoActivity.class),
                        0); // flags

                Notification notification = new NotificationCompat.Builder(context)
                        .setSmallIcon(R.drawable.ic_launcher)
                        .setContentTitle("Notification Hub Demo")
                        .setStyle(new NotificationCompat.BigTextStyle().bigText(msg))
                        .setContentText(msg)
                        .setContentIntent(contentIntent)
                        .build();

                NotificationManager notificationManager = (NotificationManager)
                        context.getSystemService(Context.NOTIFICATION_SERVICE);
                notificationManager.notify(NOTIFICATION_ID, notification);
        }


14. 回 TodoActivity.java 檔案，更新**onCreate**類別的方法*ToDoActivity*登錄通知處理常式類別。 請確定將此程式碼會產生*MobileServiceClient*後。


        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

    支援推入通知，現在已經更新您的應用程式。
