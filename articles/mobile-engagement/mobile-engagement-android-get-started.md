<properties
    pageTitle="開始使用 Android 應用程式 Azure 行動互動"
    description="瞭解如何使用 Android 應用程式使用狀況分析及推入通知 Azure 行動互動。"
    services="mobile-engagement"
    documentationCenter="android"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="hero-article"
    ms.date="08/10/2016"
    ms.author="piyushjo;ricksal" />

# <a name="get-started-with-azure-mobile-engagement-for-android-apps"></a>開始使用 Azure 行動互動 for Android 應用程式

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

本主題說明如何使用 Azure 行動互動，若要瞭解您的應用程式使用狀況，以及如何傳送推入通知給區段使用者 Android 應用程式。
本教學課程說明簡單的廣播的案例使用行動互動。 您可以建立空白的 Android 應用程式的基本資料 」 會收集並會接收推入通知使用 Google 雲端訊息 (GCM)。

## <a name="prerequisites"></a>必要條件

完成此教學課程需要[Android 開發人員工具](https://developer.android.com/sdk/index.html)，包括 Android Studio 整合式環境，以及最新的 Android 平台。

也必須採取[行動互動 Android SDK](https://aka.ms/vq9mfn)。

> [AZURE.IMPORTANT] 若要完成此教學課程中，您需要使用中的 Azure 帳戶。 如果您沒有帳戶，您可以建立的免費的試用帳戶在幾分鐘。 如需詳細資訊，請參閱[Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-android-get-started)。

## <a name="set-up-mobile-engagement-for-your-android-app"></a>設定行動互動 Android 應用程式

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a name="connect-your-app-to-the-mobile-engagement-backend"></a>將您的應用程式連線到行動互動後端

本教學課程中會顯示 「 基本整合 」，這是收集資料，並傳送推入通知所需的最小集合。 您可以建立基本的應用程式與 Android Studio 示範整合。

在[行動互動 Android SDK 整合](mobile-engagement-android-sdk-overview.md)找完成整合文件。

### <a name="create-an-android-project"></a>建立 Android 的專案

1. 啟動**Android Studio**，然後在快顯功能表中，選取 [**啟動新的 Android Studio 專案**。

    ![][1]

2. 提供的應用程式的名稱和公司網域。 記下您填寫內容，因為您稍後需要。 按一下 [**下一步**]。

    ![][2]

3. 選取目標表單係數和 API 層級]，然後按一下 [**下一步**]。

    >[AZURE.NOTE] 行動互動需要 API 層級 10 個最小值 (Android 2.3.3)。

    ![][3]

4. 選取 [**空白的活動**，也就是此應用程式的僅限畫面，然後按一下 [**下一步**]。

    ![][4]

5. 最後，將預設值，並按一下 [**完成]**。

    ![][5]

Android Studio 現在建立的示範應用程式，讓我們整合行動互動。

### <a name="include-the-sdk-library-in-your-project"></a>在專案中包含的 SDK 文件庫

1. 下載[行動互動 Android SDK](https://aka.ms/vq9mfn)。
2. 擷取封存的檔案到您的電腦中的資料夾。
3. 找出此 SDK 的目前版本的.jar 文件庫，並將它複製到剪貼簿。

      ![][6]

4. 瀏覽至 [**專案**] 區段 （1） 並貼上.jar (2) 上的 [程式庫] 資料夾中。

      ![][7]

5. 若要載入文件庫，同步處理專案。

      ![][8]

### <a name="connect-your-app-to-mobile-engagement-backend-with-the-connection-string"></a>將您的應用程式連線到行動互動後端連接字串

1. 將下列幾行程式碼複製到 [活動建立 （必須完成僅在您的應用程式，通常是主要活動的同一個地方）。 此範例應用程式中，開啟下 src MainActivity]-> [主要]-> [java 資料夾並新增下列動作︰

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
        EngagementAgent.getInstance(this).init(engagementConfiguration);

2. 按下 Alt + Enter，或新增下列匯入陳述式來解決參考資料︰

        import com.microsoft.azure.engagement.EngagementAgent;
        import com.microsoft.azure.engagement.EngagementConfiguration;

3. 回到 Azure 傳統入口網站在您的應用程式**連線資訊**] 頁面，然後複製**連線字串**。

      ![][9]

4. 將它貼到`setConnectionString`參數，取代整個字串下列所示︰

        engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

### <a name="add-permissions-and-a-service-declaration"></a>新增權限和服務宣告

1. 立即之前或之後，這些權限新增至您的專案 Manifest.xml`<application>`標記︰

        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

2. 若要宣告代理程式服務，將此程式碼之間新增`<application>`和`</application>`標籤︰

        <service
            android:name="com.microsoft.azure.engagement.service.EngagementService"
            android:exported="false"
            android:label="<Your application name>"
            android:process=":Engagement"/>

3. 在您貼上的程式碼，取代`"<Your application name>"`標籤中顯示您可以在這裡看見裝置上執行的服務**設定**] 功能表中。 您可以在該標籤，例如新增 word 」 服務]。

### <a name="send-a-screen-to-mobile-engagement"></a>將螢幕傳送給行動互動

若要開始傳送資料，並確保作用中的使用者，您必須行動互動後端傳送至少有一個畫面 （活動）。

移至**MainActivity.java** ，並新增下列取代**MainActivity**至**EngagementActivity**的基底類別︰

    public class MainActivity extends EngagementActivity {

> [AZURE.NOTE] 如果您基底類別不*活動*，請參閱如何繼承不同類別的 [[進階 Android 的報告](mobile-engagement-android-advanced-reporting.md#modifying-your-codeactivitycode-classes)。


註解的這個簡單範例案例的下列行︰

    // setSupportActionBar(toolbar);

如果您想要保留`ActionBar`在您的應用程式，請參閱[進階 Android 的報告](mobile-engagement-android-advanced-reporting.md#modifying-your-codeactivitycode-classes)。

## <a name="connect-app-with-real-time-monitoring"></a>使用即時監視連線應用程式

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a name="enable-push-notifications-and-in-app-messaging"></a>啟用推入通知和訊息的應用程式

行動互動內建在行銷活動，可讓您互動，也可以連絡您的使用者與推入通知訊息的應用程式。 本單元稱為行動互動入口網站中的範圍。
下一節將接收設定您的應用程式。

### <a name="copy-sdk-resources-in-your-project"></a>在專案中複製 SDK 資源

1. 瀏覽回 SDK 下載內容，然後複製 [**解析度**] 資料夾。

    ![][10]

2. 移回 Android Studio 中，選取您的專案檔案的**主**目錄，然後再貼上將資源新增至您的專案。

    ![][11]

[AZURE.INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[AZURE.INCLUDE [Enable in-app messaging](../../includes/mobile-engagement-android-send-push.md)]

[AZURE.INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

## <a name="next-steps"></a>後續步驟

移至[Android SDK](mobile-engagement-android-sdk-overview.md)以取得關於 SDK 整合的詳細的資訊。

<!-- Images. -->
[1]: ./media/mobile-engagement-android-get-started/android-studio-new-project.png
[2]: ./media/mobile-engagement-android-get-started/android-studio-project-props.png
[3]: ./media/mobile-engagement-android-get-started/android-studio-project-props2.png
[4]: ./media/mobile-engagement-android-get-started/android-studio-add-activity.png
[5]: ./media/mobile-engagement-android-get-started/android-studio-activity-name.png
[6]: ./media/mobile-engagement-android-get-started/sdk-content.png
[7]: ./media/mobile-engagement-android-get-started/paste-jar.png
[8]: ./media/mobile-engagement-android-get-started/sync-project.png
[9]: ./media/mobile-engagement-android-get-started/app-connection-info-page.png
[10]: ./media/mobile-engagement-android-get-started/copy-resources.png
[11]: ./media/mobile-engagement-android-get-started/paste-resources.png
