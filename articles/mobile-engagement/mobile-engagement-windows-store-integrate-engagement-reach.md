<properties 
    pageTitle="Windows 通用應用程式達到 SDK 整合" 
    description="如何使用 Windows 通用應用程式整合 Azure 行動互動範圍"
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

# <a name="windows-universal-apps-reach-sdk-integration"></a>Windows 通用應用程式達到 SDK 整合

您必須遵循之前本指南所述[Windows 通用互動 SDK 整合](mobile-engagement-windows-store-integrate-engagement.md)的整合程序。

## <a name="embed-the-engagement-reach-sdk-into-your-windows-universal-project"></a>互動達到 SDK 嵌入 Windows 通用專案

您沒有要新增的任何項目。 `EngagementReach`參考資料與資源已在專案中。

> [AZURE.TIP] 您可以自訂圖像位於`Resources`專案，尤其是品牌圖示 （[互動] 圖示的預設值） 的資料夾。 通用的應用程式您也可以移動`Resources`資料夾共用專案，以共用其內容之間應用程式，但您必須以保留`Resources\EngagementConfiguration.xml`，取決於平台上的預設位置檔案。

## <a name="enable-the-windows-notification-service"></a>啟用 Windows 通知服務

### <a name="windows-8x-and-windows-phone-81-only"></a>Windows 8.x 和 Windows Phone 8.1 只

若要使用**Windows 通知服務**（稱為 WNS） 中您`Package.appxmanifest`檔案上`Application UI`按一下`All Image Assets`左的傀儡方塊中。 在方塊中右側`Notifications`，變更`toast capable`從`(not set)`至`(Yes)`。

### <a name="all-platforms"></a>所有的平台

您要同步處理您的應用程式至您的 Microsoft 帳戶，然後進行互動平台。 這個必須先建立一個帳戶或登入[windows 開發人員中心](https://dev.windows.com)。 之後建立新的應用程式，並尋找 SID 和私密金鑰。 在您的應用程式設定中上移互動主選單中，`native push`並貼上您的認證。 之後，以滑鼠右鍵按一下您專案中，選取`store`和`Associate App with the Store...`。 您只需要以選取您之前先建立進行同步處理應用程式。

## <a name="initialize-the-engagement-reach-sdk"></a>初始化互動達到 SDK

修改`App.xaml.cs`:

-   插入`EngagementReach.Instance.Init`只之後`EngagementAgent.Instance.Init`中您`InitEngagement`方法︰

        private void InitEngagement(IActivatedEventArgs e)
        {
          EngagementAgent.Instance.Init(e);
          EngagementReach.Instance.Init(e);
        }

    `EngagementReach.Instance.Init`專用的執行緒中執行。 您不必自行。

> [AZURE.NOTE] 如果您其他地方使用推播通知在您的應用程式中您必須與互動到達 [[共用您的推入頻道](#push-channel-sharing)。

## <a name="integration"></a>整合

互動提供兩種方式可以新增到應用程式的範圍的應用程式橫幅及插入的相關公告和輪詢的回覆的檢視︰ 覆疊整合以及網頁檢視手動整合。 您不應該合併兩個方法相同頁面上。

無法彙總的兩個整合選擇這種方式︰

-   如果已經繼承代理程式的頁面，您可以選擇覆疊整合`EngagementPage`，它只是取代的`EngagementPage`以`EngagementPageOverlay`和`xmlns:engagement="using:Microsoft.Azure.Engagement"`以`xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"`頁面中。
-   如果您想要精確地放置在網頁中達到 UI，或如果您不想要新增頁面的另一個繼承層您可以選擇指定的網頁檢視手動整合。 

### <a name="overlay-integration"></a>覆疊整合

互動覆疊動態會將用來顯示您的頁面範圍行銷活動的使用者介面元素。 如果覆疊不符合您的版面配置您應該考慮網頁檢視手動整合改為。

在您的.xaml 檔案變更`EngagementPage`參考`EngagementPageOverlay`

-   新增至您的命名空間宣告︰

        xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"

-   取代`engagement:EngagementPage`與`engagement:EngagementPageOverlay`:

**使用 EngagementPage:**

        <engagement:EngagementPage 
            xmlns:engagement="using:Microsoft.Azure.Engagement">
        
            <!-- Your layout -->
        </engagement:EngagementPage>

**使用 EngagementPageOverlay:**

        <engagement:EngagementPageOverlay 
            xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay">
        
            <!-- Your layout -->
        </engagement:EngagementPageOverlay>

然後.cs 檔案中標記您的頁面的`EngagementPageOverlay`而不是`EngagementPage`及匯入`Microsoft.Azure.Engagement.Overlay`。

            using Microsoft.Azure.Engagement.Overlay;

-   取代`EngagementPage`與`EngagementPageOverlay`:

**使用 EngagementPage:**

            using Microsoft.Azure.Engagement;
            
            namespace Example
            {
              public sealed partial class ExamplePage : EngagementPage
              {
                [...]
              }
            }

**使用 EngagementPageOverlay:**

            using Microsoft.Azure.Engagement.Overlay;
            
            namespace Example
            {
              public sealed partial class ExamplePage : EngagementPageOverlay 
              {
                [...]
              }
            }


新增互動覆疊`Grid`您頁面的頂端的項目組成的版面配置和兩個`WebView`橫幅與另一個的 [插入] 檢視的項目一個。

您可以自訂的覆疊元素直接在`EngagementPageOverlay.cs`檔案。

### <a name="web-views-manual-integration"></a>網頁檢視手動整合

範圍搜尋的兩個頁面`WebView`負責橫幅和插入的檢視顯示的項目。 唯一就必須執行的是將這兩`WebView`項目位置的頁面，以下是範例︰

    <Grid x:Name="engagementGrid">

      <!-- Your layout -->

      <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Stretch" VerticalAlignment="Top"/>
      <WebView x:Name="engagement_announcement_content" Visibility="Collapsed"  HorizontalAlignment="Stretch"  VerticalAlignment="Stretch"/>
    </Grid>


在此範例中`WebView`項目會自動縮放以符合其自動重新調整大小這些畫面旋轉或視窗大小的變更若的容器。

> [AZURE.WARNING] 請務必將相同的命名`engagement_notification_content`和`engagement_announcement_content`的`WebView`項目。 範圍用來識別其依其名稱。 

## <a name="handle-datapush-optional"></a>處理 datapush （選用）

如果您想要接收達到資料推入您的應用程式，您必須執行兩個事件的 EngagementReach 類別︰

在 App.xaml.cs App() 建構函式中加上︰

            EngagementReach.Instance.DataPushStringReceived += (body) =>
            {
              Debug.WriteLine("String data push message received: " + body);
              return true;
            };
            
            EngagementReach.Instance.DataPushBase64Received += (decodedBody, encodedBody) =>
            {
              Debug.WriteLine("Base64 data push message received: " + encodedBody);
              // Do something useful with decodedBody like updating an image view
              return true;
            };

您可以看到每一種方法的回撥會傳回一個 boolean。 互動會之後分派資料推入傳送給其後端的意見反應。 如果回撥會傳回 false，`exit`意見反應會傳送。 否則，它將會`action`。 如果沒有回撥設定的事件，`drop`意見反應會回到互動。

> [AZURE.WARNING] 互動不能接收多重圖表的資料推入的意見反應。 如果您打算設定數個處理常式的事件，請注意以下事項的意見反應會對應到最後一個一個傳送。 在此案例中，我們建議您一定會傳回相同的值，若要避免混淆的意見反應上前端。

## <a name="customize-ui-optional"></a>自訂使用者介面 （選用）

### <a name="first-step"></a>第一個步驟

讓您自訂範圍 ui。

若要這麼做，您必須建立的子類別`EngagementReachHandler`類別。

**範例︰**

            using Microsoft.Azure.Engagement;
            
            namespace Example
            {
              internal class ExampleReachHandler : EngagementReachHandler
              {
               // Override EngagementReachHandler methods depending on your needs
              }
            }

然後，設定 [內容`EngagementReach.Instance.Handler`欄位與您自訂的物件，在您`App.xaml.cs`類別內`App()`方法。

**範例︰**

            protected override void OnLaunched(LaunchActivatedEventArgs args)
            {
              // your app initialization 
              EngagementReach.Instance.Handler = new ExampleReachHandler();
              // Engagement Agent and Reach initialization
            }

> [AZURE.NOTE]根據預設，互動使用自己的實作`EngagementReachHandler`。
> 您還沒有建立您自己，和您這麼做，如果您沒有覆寫每一個方法。 預設行為是互動基底物件加以選取。

### <a name="web-view"></a>網頁檢視

根據預設，範圍就會使用內嵌的 DLL 的資源來顯示通知和頁面。

若要提供完整的自訂我們只能使用 web 檢視的可能性。 如果您想要自訂版面配置，覆寫直接資源檔案`EngagementAnnouncement.html`和`EngagementNotification.html`。 互動必須在所有的程式碼`<body></body>`正確執行。 但您可以新增標籤外部`engagement_webview_area`。

不過，您可以決定要使用您自己的資源。

您可以覆寫`EngagementReachHandler`告知互動使用您的版面配置，但請注意您子類別中的方法內嵌的互動機制︰

**範例︰**
            
            // In your subclass of EngagementReachHandler
            
            public override string GetAnnouncementHTML()
            {
              return base.GetAnnouncementHTML();
            }
            public override string GetAnnouncementName()
            {
              return base.GetAnnouncementName();
            }
            public override string GetNotfificationHTML()
            {
              return base.GetNotfificationHTML();
            }
            public override string GetNotfificationName()
            {
              return base.GetNotfificationName();
            }


根據預設，是 AnnouncementHTML `ms-appx-web:///Resources/EngagementAnnouncement.html`。 它會表示 html 檔案的設計推入郵件 （文字公告、 Web anoucement 和投票公告） 的內容。 AnnouncementName 是`engagement_announcement_content`。 它是在 xaml] 頁面的網頁檢視設計的名稱。

NotfificationHTML 是`ms-appx-web:///Resources/EngagementNotification.html`。 它會表示設計郵件的推入通知的 html 檔案。 NotfificationName 是`engagement_notification_content`。 它是在 xaml] 頁面的網頁檢視設計的名稱。

### <a name="customization"></a>自訂

您可以自訂通知和網頁檢視中有您想要保留的互動物件的公告。 請小心該網頁檢視物件描述三次-xaml 的第一次在 「 setwebview() 」 的方法，.cs 檔案中的第二個時間與 html 檔案中的第三次。

-   在 [xaml 您說明目前的圖形版面配置的網頁檢視元件。
-   .Cs 檔案中，您可以定義您可以在其中設定維度的兩個網頁檢視 （通知、 公告） 「 setwebview() 」。 應用程式會調整大小時，它是非常有效。
-   在 [互動的 html 檔案我們會說明網頁內容、 設計及彼此之間的項目位置。

### <a name="launch-message"></a>啟動訊息

互動當使用者按一下 [系統通知 （快顯） 時，啟動應用程式、 載入的推入郵件內容，並顯示對應的行銷活動的頁面。

在啟動應用程式之間 （根據您的網路速度） 頁面的顯示有延遲。

項目正在載入表示給使用者，您應該提供視覺化的資訊，例如進度列或進度。 互動無法處理本身，但您提供一些處理常式。

若要執行的回撥，在 App.xaml.cs 」 公用 App() {」 中加上︰

            /* The application has launched and the content is loading.
             * You should display an indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageStarted += () => { [...] };
            
            /* The application has finished loading the content and the page
             * is about to be displayed.
             * You should hide the indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageCompleted += () => { [...] };
            
            /* The content has been loaded, but an error has occurred.
             * You can provide an information to the user.
             * You should hide the indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageFailed += () => { [...] };

您可以設定您的 「 公用 App() {」 方法的回撥您`App.xaml.cs`檔案，最好是之前`EngagementReach.Instance.Init()`來電。

> [AZURE.TIP] 每個處理常式是由 UI 執行緒呼叫。 您不必擔心時使用的訊息方塊，或內容的 UI 相關。

##<a id="push-channel-sharing"></a>推播頻道共用

如果您用於其他用途使用推入通知，在應用程式中您必須使用推入頻道共用互動 SDK 的功能。 這是為了避免未接的推入。

- 您可以提供互動達到初始化推入頻道。 SDK 會使用它，而不是要求新。

更新的推入頻道中的互動達到初始化`InitEngagement`的方法`App.xaml.cs`檔案︰
    
    /* Your own push channel logic... */
    var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    
    /*...Engagement initialization */
    EngagementAgent.Instance.Init(e);
    EngagementReach.Instance.Init(e,pushChannel);

- 或者，如果您只想要使用推入頻道達到初始化然後您可以設定回撥互動連絡以取得推入頻道一次後就會建立以 SDK。

在任何位置**後**達到初始化設定您的回撥︰

    /* Set action on the SDK push channel. */
    EngagementReach.Instance.SetActionOnPushChannel((PushNotificationChannel channel) => 
    {
      /* The forwarded channel can be null if its creation fails for any reason. */
      if (channel != null)
      {
        /* Your own push channel logic... */
      });
    }

## <a name="custom-scheme-tip"></a>自訂的配置的秘訣

我們會提供使用自訂的配置。 您可以從使用您的行動應用程式中互動 frontend 傳送不同類型的 URI。 預設的配置，例如`http, ftp, ...`管理 windows 視窗會提示如果不有任何裝置上安裝的預設應用程式。 您也可以建立您自己自訂的配置，您的應用程式。

若要在應用程式中設定自訂的配置的簡單方法是開啟您`Package.appxmanifest`移入`Declarations`面板。 選取 [`Protocol`中可用的宣告捲動方塊，並將其新增。 編輯`Name`與新的通訊協定] 欄位所需的名稱。

現在若要使用這個通訊協定，編輯您`App.xaml.cs`與`OnActivated`方法，和初始化以下的互動，也請記住︰

            /// <summary>
            /// Enter point when app his called by another way than user click
            /// </summary>
            /// <param name="args">Activation args</param>
            protected override void OnActivated(IActivatedEventArgs args)
            {
              /* Init engagement like it was launch by a custom uri scheme */
              EngagementAgent.Instance.Init(args);
              EngagementReach.Instance.Init(args);
            
              //TODO design action to do when app is launch
            
              #region Custom scheme use
              if (args.Kind == ActivationKind.Protocol)
              {
                ProtocolActivatedEventArgs myProtocol = (ProtocolActivatedEventArgs)args;
            
                if (myProtocol.Uri.Scheme.Equals("protocolName"))
                {
                  string path = myProtocol.Uri.AbsolutePath;
                }
              }
              #endregion
 
