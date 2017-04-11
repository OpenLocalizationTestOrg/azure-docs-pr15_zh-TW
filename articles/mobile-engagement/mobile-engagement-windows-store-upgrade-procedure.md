<properties 
    pageTitle="Windows 通用應用程式 SDK 升級程序" 
    description="Azure 行動互動的 Windows 通用的應用程式 SDK 升級程序"                     
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-store" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="windows-universal-apps-sdk-upgrade-procedures"></a>Windows 通用應用程式 SDK 升級程序

如果您已經有整合互動的舊版應用程式，您必須升級 SDK 時，請考慮下列幾點。

您可能必須追蹤多個程序，如果您未接來電 SDK 的多個版本。 如果您將移轉 0.10.1 到 0.11.0 您必須先依照 「 從至 0.10.1 0.9.0 」 的程序然後 」 從至 0.11.0 0.10.1 」 程序的範例。

##<a name="from-330-to-340"></a>從 3.3.0 至 3.4.0

### <a name="test-logs"></a>測試記錄

主控台記錄所產生的 SDK 現在就可以啟用/停用/篩選。 若要自訂此，更新屬性`EngagementAgent.Instance.TestLogEnabled`其中一個可用的值`EngagementTestLogLevel`列舉，例如︰

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

### <a name="resources"></a>資源

已經過改良達到覆疊。 它是 SDK NuGet 套件的資源。

當您升級至新版的 SDK，可以選擇是否要保留現有的檔案從您的資源的 [重疊] 資料夾，或不︰

* 如果您正在先前的覆疊或整合`WebView`元素手動然後您可以決定要讓您現有的檔案，其是否仍能運作。 
* 如果您想要更新為新的覆疊，只會取代整個`overlay`從您的資源以新的 SDK 的資料夾 (UWP 應用程式︰ 升級之後，您可以從 %USERPROFILE%取得新的覆疊資料夾\\.nuget\packages\MicrosoftAzure.MobileEngagement\3.4.0\content\win81\Resources)。

> [AZURE.WARNING] 使用新的覆疊會覆寫先前的版本上所做的任何自訂。

##<a name="from-320-to-330"></a>從 3.2.0 至 3.3.0

### <a name="resources"></a>資源
此步驟是自訂的資源。 如果您有自訂 SDK （html、 圖像、 覆疊） 所提供的資源您必須在升級前先備份其及重新套用您的自訂升級後的資源。

##<a name="from-310-to-320"></a>從 3.1.0 至 3.2.0

### <a name="resources"></a>資源
此步驟是自訂的資源。 如果您有自訂 SDK （html、 圖像、 覆疊） 所提供的資源您必須在升級前先備份其及重新套用您的自訂升級後的資源。

### <a name="webview-integration"></a>網頁檢視整合
在這個版本中帶來一些符合不同的裝置規格的改良功能。 請確定您的網頁檢視的整合符合下列動作︰

在您 XAML 頁面 （）︰

            <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Right" VerticalAlignment="Top"/>
            <WebView x:Name="engagement_announcement_content" Visibility="Collapsed" HorizontalAlignment="Right" VerticalAlignment="Top"/> 

與您相關聯的.cs 檔案中︰

    using Microsoft.Azure.Engagement;
    using System;
    using Windows.ApplicationModel.Core;
    using Windows.UI.ViewManagement;
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Navigation;

    namespace My.Namespace.Example
    {
            /// <summary>
            /// An empty page that can be used on its own or navigated to within a Frame.
            /// </summary>
            public sealed partial class ExampleEngagementReachPage : EngagementPage
            {
              public ExampleEngagementReachPage()
              {
                this.InitializeComponent();
            
                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }
            
              #region to implement
              /* Attach events when page is navigated. */
              protected override void OnNavigatedTo(NavigationEventArgs e)
              {
                /* Update the webview when the app window is resized. */
                Window.Current.SizeChanged += DisplayProperties_OrientationChanged;

                /* Update the webview when the app/status bar is resized. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged += DisplayProperties_VisibleBoundsChanged; 
    #endif
                base.OnNavigatedTo(e);
              }

              /* When page is left ensure to detach SizeChanged handler. */
              protected override void OnNavigatedFrom(NavigationEventArgs e)
              {
                Window.Current.SizeChanged -= DisplayProperties_OrientationChanged;
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged -= DisplayProperties_VisibleBoundsChanged;
    #endif
                base.OnNavigatedFrom(e);
              }
              
              /* "width" and "height" are the current size of your application display. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
              double width = ApplicationView.GetForCurrentView().VisibleBounds.Width;
              double height = ApplicationView.GetForCurrentView().VisibleBounds.Height;
    #else
              double width =  Window.Current.Bounds.Width;
              double height =  Window.Current.Bounds.Height;
    #endif
            
              /// <summary>
              /// Set your webview elements to the correct size.
              /// </summary>
              /// <param name="width">The width of your current display.</param>
              /// <param name="height">The height of your current display.</param>
              private void SetWebView(double width, double height)
              {
                #pragma warning disable 4014
                CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
                        () =>
                        {
                          this.engagement_notification_content.Width = width;
                          this.engagement_announcement_content.Width = width;
                          this.engagement_announcement_content.Height = height;
                        });
              }
            
              /// <summary>
              /// Handler that takes the Windows.Current.SizeChanged and indicates that webviews have to be resized.
              /// </summary>
              /// <param name="sender">Original event trigger.</param>
              /// <param name="e">Window Size Changed Event arguments.</param>
              private void DisplayProperties_OrientationChanged(object sender, Windows.UI.Core.WindowSizeChangedEventArgs e)
              {
                double width = e.Size.Width;
                double height = e.Size.Height;
            
                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }

    #if WINDOWS_PHONE_APP || WINDOWS_UWP              
              /// <summary>
              /// Handler that takes the ApplicationView.VisibleBoundsChanged and indicates that webviews have to be resized
              /// </summary>
              /// <param name="sender">The related application view.</param>
              /// <param name="e">Related event arguments.</param>
              private void DisplayProperties_VisibleBoundsChanged(ApplicationView sender, Object e)
              {
                double width = sender.VisibleBounds.Width;
                double height = sender.VisibleBounds.Height;
            
                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }
    #endif
              #endregion
            }
    }

##<a name="from-200-to-300"></a>從 2.0.0 至 3.0.0

### <a name="resources"></a>資源
此步驟是自訂的資源。 如果您有自訂 SDK （html、 圖像、 覆疊） 所提供的資源您必須在升級前先備份其及重新套用您的自訂升級後的資源。

##<a name="from-111-to-200"></a>從 1.1.1 至 2.0.0

以下說明如何從將由 Azure 行動互動應用程式提供 Capptain SA Capptain 服務移轉 SDK 整合。 

> [Azure.IMPORTANT] Capptain 及 Mobile 使用不相同的服務，如下所示的程序只會醒目提示要移轉的用戶端應用程式的方式。 移轉應用程式中的 SDK 不會移轉您的資料從 Capptain 伺服器到行動互動伺服器

如果您從舊版升級，請參閱 Capptain 網站，以第一次移轉到 1.1.1，然後套用下列程序

### <a name="nuget-package"></a>Nuget 套件

取代**Capptain.WindowsPhone** **MicrosoftAzure.MobileEngagement** Nuget 套件。

### <a name="applying-mobile-engagement"></a>套用行動互動

SDK 使用字詞`Engagement`。 您需要更新您的專案，以符合這項變更。

您需要解除安裝您目前的 Capptain nuget 套件。 請考慮您 Capptain 資源資料夾中的所有變更將會都移除。 如果您想要保留的檔案，然後進行的複本。

之後，請在專案上安裝新的 Microsoft Azure 互動 nuget 套件。 您可以直接在 [nuget 網站] 上找到。 或此索引。 這個動作會取代互動所用的所有資源檔案，並將新的互動 DLL 加入至您專案的參照。

您必須以刪除 Capptain DLL 參照清除您專案的參照。 如果您不確定此，Capptain 版本會發生衝突，會發生錯誤。

如果您有自訂 Capptain 資源，請將舊的檔案內容中複製並貼這些新的互動檔案。 請注意同時 xaml 與資訊科學檔案有更新。

完成這些步驟只需要取代舊的新的互動參照 Capptain 參照。

1. 所有 Capptain 命名空間都需要加以更新。

    前移轉︰
    
        using Capptain.Agent;
        using Capptain.Reach;
    
    移轉︰ 後
    
        using Microsoft.Azure.Engagement;

2. 包含 「 Capptain 」 的所有 Capptain 類別應都包含 「 參與 」。

    前移轉︰
    
        public sealed partial class MainPage : CapptainPage
        {
          protected override string GetCapptainPageName()
          {
            return "Capptain Demo";
          }
          ...
        }
    
    移轉︰ 後
    
        public sealed partial class MainPage : EngagementPage
        {
          protected override string GetEngagementPageName()
          {
            return "Engagement Demo";
          }
          ...
        }

3. Xaml 檔案 Capptain 命名空間和屬性也變更。

    前移轉︰
    
        <capptain:CapptainPage
        ...
        xmlns:capptain="clr-namespace:Capptain.Agent;assembly=Capptain.Agent.WP"
        ...
        </capptain:CapptainPage>
    
    移轉︰ 後
    
        <engagement:EngagementPage
        ...
        xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
        ...
        </engagement:EngagementPage>

4. 覆疊頁面變更
    > [AZURE.IMPORTANT] 覆疊也會變更。 新的命名空間是`Microsoft.Azure.Engagement.Overlay`。 有要用於同時 xaml 與資訊科學檔案。 再者`CapptainGrid`是命名`EngagementGrid`，`capptain_notification_content`和`capptain_announcement_content`命名`engagement_notification_content`和`engagement_announcement_content`。
    
    重疊︰
    
        <capptain:CapptainPageOverlay
          xmlns:capptain="using:Capptain.Overlay"
          ...
        </capptain:CapptainPageOverlay>
    
    它會變成︰
    
        <EngagementPageOverlay
          engagement="using:Microsoft.Azure.Engagement.Overlay"
          ...
        </engagement:EngagementPageOverlay>

5. 其他資源的 Capptain 圖片的 HTML 檔案等，請注意，他們也有重新命名使用 「 參與 」。

### <a name="project-declaration"></a>專案宣告

在 Package.appxmanifest`File Type Associations`已從更新︰

 -   capptain\_達到\_內容互動\_達到\_內容
 -   capptain\_記錄\_檔案，以互動\_記錄\_檔案

### <a name="application-id--sdk-key"></a>應用程式識別碼 / SDK 索引鍵

互動使用連接字串。 您沒有使用行動互動指定應用程式識別碼和 SDK 鍵，您只有指定連線字串。 您可以將其設定上 EngagementConfiguration 檔案。

互動設定可以在中設定您`Resources\EngagementConfiguration.xml`專案的檔案。

編輯指定此檔案︰

-   您的應用程式的連接字串標記之間`<connectionString>`和`<\connectionString>`。

如果您想要指定在執行階段，您可以呼叫互動代理程式初始化之前的下列方法︰

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
    
    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(args, engagementConfiguration);

Azure 傳統入口網站上，會顯示您的應用程式的連接字串。

### <a name="items-name-change"></a>變更項目名稱

名為*capptain*的所有項目已指名您*互動*。 同樣的*Capptain*以*互動*。

常用的 Capptain 項目的範例︰

-   現在稱為 EngagementConfiguration CapptainConfiguration
-   現在稱為 EngagementAgent CapptainAgent
-   現在稱為 EngagementReach CapptainReach
-   現在稱為 EngagementHttpConfig CapptainHttpConfig
-   現在稱為 GetEngagementPageName GetCapptainPageName

請注意，重新命名也會影響覆寫的方法。

 
