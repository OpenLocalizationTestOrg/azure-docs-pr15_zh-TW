<properties
    pageTitle="Azure 行動互動的 Windows Phone Silverlight 應用程式快速入門"
    description="瞭解如何使用狀況分析及推入通知中的 Azure 行動互動，for Windows Phone Silverlight 應用程式。"
    services="mobile-engagement"
    documentationCenter="windows"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-phone"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-windows-phone-silverlight-apps"></a>Azure 行動互動的 Windows Phone Silverlight 應用程式快速入門

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

本主題說明如何使用 Azure 行動互動，以瞭解您的應用程式使用狀況，並傳送區段使用者的 Windows Phone Silverlight 應用程式的推播通知。
本教學課程說明簡單的廣播的案例使用行動互動。 您可以建立空白的 Windows Phone Silverlight 應用程式的基本資料 」 會收集並會接收推入通知，使用 Microsoft 推入通知服務 (MPNS)。

> [AZURE.NOTE] 如果您的目標 Windows Phone 8.1 (非 Silverlight)，請參閱[Windows 通用教學課程](mobile-engagement-windows-store-dotnet-get-started.md)。

本教學課程的需求如下︰

+ Visual Studio 2013
+ [MicrosoftAzure.MobileEngagement] Nuget 套件

> [AZURE.NOTE] 若要完成此教學課程中，您必須使用中的 Azure 帳戶。 如果您沒有帳戶，您可以建立的免費的試用帳戶在幾分鐘。 如需詳細資訊，請參閱[Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-windows-phone-get-started)。

##<a id="setup-azme"></a>設定行動裝置的互動，為您的 Windows Phone 應用程式

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>將您的應用程式連線到行動互動後端

本教學課程中會顯示 「 基本整合 」，這是收集資料，並傳送推入通知所需的最小集合。 完成的整合文件，請參閱[行動互動 Windows Phone SDK 整合](mobile-engagement-windows-phone-sdk-overview.md)

我們會示範之間整合的 Visual studio 建立基本的應用程式。

###<a name="create-a-new-windows-phone-silverlight-project"></a>建立新的 Windows Phone Silverlight 專案

下列步驟假設使用 Visual Studio 2015，雖然步驟會在舊版的 Visual Studio 中類似。 

1. 啟動 [Visual Studio 中，並在 [**常用**] 畫面中，選取 [**新的專案**。

2. 在快顯功能表中，選取 [ **Windows 8** -> **Windows Phone** -> **空白的應用程式 (Windows Phone Silverlight)**。 應用程式**名稱**和**方案名稱**，填滿，然後按一下**[確定]**。

    ![][1]

3. 您可以選擇**在 Windows Phone 8.0**或**Windows Phone 8.1**目標。

現在您已建立新的 Windows Phone Silverlight 應用程式，讓我們會整合 Azure 行動互動 SDK。

###<a name="connect-your-app-to-the-mobile-engagement-backend"></a>將您的應用程式連線到行動互動後端

1. 在專案中安裝[MicrosoftAzure.MobileEngagement] nuget 套件。

2. 開啟`WMAppManifest.xml`（在 [屬性] 資料夾中），並確定下列宣告 （將其新增不） 中`<Capabilities />`標記︰

        <Capability Name="ID_CAP_NETWORKING" />
        <Capability Name="ID_CAP_IDENTITY_DEVICE" />

    ![][2]

3. 現在貼上您先前複製的互動行動應用程式的連接字串，然後貼入`Resources\EngagementConfiguration.xml`檔案之間`<connectionString>`和`</connectionString>`標籤︰

    ![][3]

4. 在 [`App.xaml.cs`檔案︰

    。 新增`using`陳述式︰

            using Microsoft.Azure.Engagement;

    b。 初始化中的 SDK`Application_Launching`方法︰

            private void Application_Launching(object sender, LaunchingEventArgs e)
            {
              EngagementAgent.Instance.Init();
            }

    c。 插入下列`Application_Activated`:

            private void Application_Activated(object sender, ActivatedEventArgs e)
            {
               EngagementAgent.Instance.OnActivated(e);
            }

##<a id="monitor"></a>啟用即時監視

若要開始傳送資料，並確保作用中使用者，請您必須行動互動後端傳送至少有一個畫面 （活動）。

1. 在 MainPage.xaml.cs，新增`using`陳述式︰

        using Microsoft.Azure.Engagement;

2. 取代**EngagementPage** **MainPage**，也就是**PhoneApplicationPage**之前的基底類別。

        class MainPage : EngagementPage 
    
3. 在您`MainPage.xml`檔案︰

    。 新增至您的命名空間宣告︰

         xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"

    b。 取代`phone:PhoneApplicationPage`中使用的 XML 標記名稱`engagement:EngagementPage`。

##<a id="monitor"></a>使用即時監視連線應用程式

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>啟用推入通知和訊息的應用程式

行動互動可讓您互動，以及連絡您的使用者推入通知與行銷活動的內容中的應用程式訊息。 本單元稱為行動互動入口網站中的範圍。
下列各節會接收設定您的應用程式。

###<a name="enable-your-app-to-receive-mpns-push-notifications"></a>啟用您的應用程式收到 MPNS 推播通知

新增新的功能，您`WMAppManifest.xml`檔案︰

        ID_CAP_PUSH_NOTIFICATION
        ID_CAP_WEBBROWSERCOMPONENT

   ![][5]

###<a name="initialize-the-reach-sdk"></a>初始化達到 SDK

1. 在 [ `App.xaml.cs`，來電`EngagementReach.Instance.Init();` **Application_Launching**函數中以滑鼠右鍵代理程式初始化之後︰

        private void Application_Launching(object sender, LaunchingEventArgs e)
        {
           EngagementAgent.Instance.Init();
           EngagementReach.Instance.Init();
        }

2. 在 [ `App.xaml.cs`，來電`EngagementReach.Instance.OnActivated(e);` **Application_Activated**函數中以滑鼠右鍵代理程式初始化之後︰

        private void Application_Activated(object sender, ActivatedEventArgs e)
        {
           EngagementAgent.Instance.OnActivated(e);
           EngagementReach.Instance.OnActivated(e);
        }

您已完成所有設定。 現在，我們會驗證您擁有正確哭查看此基本的整合。

##<a id="send"></a>傳送通知給您的應用程式

[AZURE.INCLUDE [Create Windows Push campaign](../../includes/mobile-engagement-windows-push-campaign.md)]

您現在應該會看到通知會顯示您裝置上的應用程式通知為如果否則快顯通知，如下所示為已開啟應用程式︰ 

![][6]

<!-- URLs. -->
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9874664
[Mobile Engagement Windows Phone SDK documentation]: ../mobile-engagement-windows-phone-integrate-engagement/

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-phone-get-started/project-properties.png
[2]: ./media/mobile-engagement-windows-phone-get-started/wmappmanifest-capabilities.png
[3]: ./media/mobile-engagement-windows-phone-get-started/add-connection-string.png
[5]: ./media/mobile-engagement-windows-phone-get-started/reach-capabilities.png
[6]: ./media/mobile-engagement-windows-phone-get-started/push-screenshot.png
