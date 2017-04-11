<properties
    pageTitle="快速入門的 Xamarin.Android Azure 行動互動"
    description="瞭解如何使用 Azure 行動互動分析及推入通知 Xamarin.Android 應用程式。"
    services="mobile-engagement"
    documentationCenter="xamarin"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="06/16/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-xamarinandroid-apps"></a>開始使用 Azure 行動已 Xamarin.Android 應用程式的互動

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

本主題說明如何使用 Azure 行動互動，若要瞭解您的應用程式使用狀況，以及如何將推入通知傳送給區段 Xamarin.Android 應用程式的使用者。
本教學課程說明簡單的廣播的案例使用行動互動。 您可以建立空白的 Xamarin.Android 應用程式的基本資料 」 會收集並會接收推入通知使用 Google 雲端訊息 (GCM)。

本教學課程的需求如下︰

+ [Xamarin Studio](http://xamarin.com/studio)。 您也可以使用 Visual Studio 與 Xamarin 但本教學課程使用 Xamarin Studio。 如需安裝指示，請參閱[設定與安裝的 Visual Studio 與 Xamarin](https://msdn.microsoft.com/library/mt613162.aspx)。
+ [行動裝置的互動 Xamarin SDK](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [AZURE.NOTE] 若要完成此教學課程中，您必須使用中的 Azure 帳戶。 如果您沒有帳戶，您可以建立的免費的試用帳戶在幾分鐘。 如需詳細資訊，請參閱[Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-android-get-started)。

##<a id="setup-azme"></a>設定行動裝置 Android 應用程式的互動

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>將您的應用程式連線到行動互動後端

本教學課程中會顯示 「 基本整合 」，這是收集資料，並傳送推入通知所需的最小集合。 

我們會示範整合 Xamarin Studio 與建立基本的應用程式。

###<a name="create-a-new-xamarinandroid-project"></a>建立新的 Xamarin.Android 專案

1. 啟動**Xamarin Studio**移至 [**檔案** -> **新** -> **解決方案** 

    ![][1]

2. 選取**在 Android 應用程式**，然後確定所選的語言是**C#** ，按一下 [**下一步**。

    ![][2]

3. 填寫**應用程式名稱**] 和 [**組織識別碼**。 確認核取記號**Google 播放服務**，然後按一下 [**下一步**。 

    ![][3]
    
4. 更新的**專案名稱**、**方案名稱**和**位置**，如有必要，並按一下 [**建立**]。

    ![][4]
 
Xamarin Studio 將會建立應用程式中，我們會整合行動互動。 

###<a name="connect-your-app-to-mobile-engagement-backend"></a>將您的應用程式連線到行動互動後端

1. 以滑鼠右鍵按一下 [解決方案] 視窗中的 [**套件**] 資料夾，然後選取 [**新增封包**

    ![][5]

2. **Microsoft Azure 行動互動 Xamarin SDK**搜尋，並將其新增至您的方案。  

    ![][6]
   
3. 開啟**MainActivity.cs**並新增下列使用陳述式︰

        using Microsoft.Azure.Engagement;
        using Microsoft.Azure.Engagement.Activity;

4. 在 [`OnCreate`方法，新增下列初始化與行動互動後端的連線。 請務必先新增您的**連接字串**。 

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.ConnectionString = "YourConnectionStringFromAzurePortal";
        EngagementAgent.Init(engagementConfiguration);

###<a name="add-permissions-and-a-service-declaration"></a>新增權限和服務宣告

1. 開啟 [屬性] 資料夾底下的**Manifest.xml**檔案。 選取 [來源] 索引標籤，讓您直接更新 [XML 來源。
 
2. Manifest.xml （就可以在 [**屬性**] 資料夾底下找到） 的專案中新增這些權限立即之前或之後`<application>`標記︰

        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

3. 新增下列之間`<application>`和`</application>`宣告代理程式服務的標籤︰

        <service
            android:name="com.microsoft.azure.engagement.service.EngagementService"
            android:exported="false"
            android:label="<Your application name>"
            android:process=":Engagement"/>

4. 只貼上程式碼，取代`"<Your application name>"`的標籤。 這會顯示在 [**設定**] 功能表的使用者可以在這裡看見裝置上執行的服務。 您可以在該標籤，例如新增 word 」 服務]。

###<a name="send-a-screen-to-mobile-engagement"></a>將螢幕傳送給行動互動

若要開始傳送資料，並確保作用中使用者，請您必須行動互動後端傳送至少有一個畫面。 執行此動作-確保`MainActivity`繼承自`EngagementActivity`而不是`Activity`。

    public class MainActivity : EngagementActivity
    
或者，如果您無法從繼承`EngagementActivity`然後您必須新增`.StartActivity`和`.EndActivity`方式`OnResume`和`OnPause`分別。  

        protected override void OnResume()
            {
                EngagementAgent.StartActivity(EngagementAgentUtils.BuildEngagementActivityName(Java.Lang.Class.FromType(this.GetType())), null);
                base.OnResume();             
            }
    
            protected override void OnPause()
            {
                EngagementAgent.EndActivity();
                base.OnPause();            
            }

##<a id="monitor"></a>使用即時監視連線應用程式

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>啟用推入通知和訊息的應用程式

行動互動可讓您互動，也可以連絡您的應用程式行銷活動的內容中的訊息與推入通知的使用者。 本單元稱為行動互動入口網站中的範圍。
下列各節將接收設定您的應用程式。

[AZURE.INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[AZURE.INCLUDE [Enable in-app messaging](../../includes/mobile-engagement-android-send-push.md)]

[AZURE.INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

<!-- Images -->
[1]: ./media/mobile-engagement-xamarin-android-get-started/1.png
[2]: ./media/mobile-engagement-xamarin-android-get-started/2.png
[3]: ./media/mobile-engagement-xamarin-android-get-started/3.png
[4]: ./media/mobile-engagement-xamarin-android-get-started/4.png
[5]: ./media/mobile-engagement-xamarin-android-get-started/5.png
[6]: ./media/mobile-engagement-xamarin-android-get-started/6.png
