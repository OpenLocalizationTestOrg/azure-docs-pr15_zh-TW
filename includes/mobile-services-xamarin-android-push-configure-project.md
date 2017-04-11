
1. 在方案檢視 （或在 Visual Studio**方案總管] 中**），以滑鼠右鍵按一下 [**元件**] 資料夾**取得更多元件...**、 **Google 雲端訊息用戶端**元件的搜尋，然後按一下將其新增至專案。

2. 開啟 ToDoActivity.cs 專案檔案，並新增下列使用陳述式的類別︰

        using Gcm.Client;

3. 在**ToDoActivity**類別中，新增下列新的程式碼︰ 

        // Create a new instance field for this activity.
        static ToDoActivity instance = new ToDoActivity();

        // Return the current activity instance.
        public static ToDoActivity CurrentActivity
        {
            get
            {
                return instance;
            }
        }
        // Return the Mobile Services client.
        public MobileServiceClient CurrentClient
        {
            get
            {
                return client;
            }
        }

    這可讓您存取推入處理常式服務程序的行動用戶端執行個體。

4.  建立**MobileServiceClient**後，請至**OnCreate**的方法，新增下列程式碼︰

        // Set the current instance of TodoActivity.
        instance = this;

        // Make sure the GCM client is set up correctly.
        GcmClient.CheckDevice(this);
        GcmClient.CheckManifest(this);

        // Register the app for push notifications.
        GcmClient.Register(this, ToDoBroadcastReceiver.senderIDs);

您**ToDoActivity**現在已準備新增推播通知。