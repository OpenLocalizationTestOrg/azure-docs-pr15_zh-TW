<properties 
    pageTitle="Windows Phone Silverlight 達到 SDK 整合" 
    description="如何使用 Windows Phone Silverlight 應用程式整合 Azure 行動互動範圍"                    
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-phone" 
    ms.devlang="na" 
    ms.topic="article"
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="windows-phone-silverlight-reach-sdk-integration"></a>Windows Phone Silverlight 達到 SDK 整合

您必須遵循之前本指南所述[Windows Phone Silverlight 互動 SDK 整合](mobile-engagement-windows-phone-integrate-engagement.md)的整合程序。

##<a name="embed-the-engagement-reach-sdk-into-your-windows-phone-silverlight-project"></a>將您的 Windows Phone Silverlight 專案內嵌互動達到 SDK

您沒有要新增的任何項目。 `EngagementReach`參考資料與資源已在專案中。

> [AZURE.TIP]  您可以自訂圖像位於`Resources`專案，尤其是品牌圖示 （[互動] 圖示的預設值） 的資料夾。

##<a name="add-the-capabilities"></a>新增功能

互動達到 SDK 需要一些其他功能。

開啟您`WMAppManifest.xml`檔案，並確定宣告下列功能︰

-   `ID_CAP_PUSH_NOTIFICATION`
-   `ID_CAP_WEBBROWSERCOMPONENT`

第一個 MPNS 服務可讓快顯通知的顯示方式。 第二個用來將 SDK 內嵌瀏覽器工作。

編輯`WMAppManifest.xml`檔案，並新增內`<Capabilities />`標記︰

    <Capability Name="ID_CAP_PUSH_NOTIFICATION" />
    <Capability Name="ID_CAP_WEBBROWSERCOMPONENT" />

##<a name="enable-the-microsoft-push-notification-service"></a>啟用 Microsoft 推入通知服務

若要使用**Microsoft 推入通知服務**（稱為 MPNS） 您`WMAppManifest.xml`檔案，必須具有`<App />`標記與`Publisher`屬性設定為您的專案名稱。

##<a name="initialize-the-engagement-reach-sdk"></a>初始化互動達到 SDK

### <a name="engagement-configuration"></a>互動設定

互動設定集中放在`Resources\EngagementConfiguration.xml`專案的檔案。

編輯指定的範圍設定這個檔案︰

-   *可省略*，會指出啟動原生推入 (MPNS) 或不之間`<enableNativePush>`和`</enableNativePush>`標籤 (`true`預設)。
-   *可省略*，指出之間推入頻道的名稱`<channelName>`和`</channelName>`標籤，提供相同的應用程式可能目前使用，或將其保留空白。

如果您想要指定在執行階段，您可以呼叫互動代理程式初始化之前的下列方法︰

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
    
    engagementConfiguration.Reach.EnableNativePush = true;                  
    /* [Optional] whether the native push (MPNS) is activated or not. */
    
    engagementConfiguration.Reach.ChannelName = "YOUR_PUSH_CHANNEL_NAME";   
    /* [Optional] Provide the same channel name that your application may currently use. */
    
    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(engagementConfiguration);

> [AZURE.TIP] 您可以指定 MPNS 推入頻道應用程式的名稱。 根據預設，互動會建立根據 appId 的名稱。 您不需要自行指定的名稱，除了如果您打算使用推入頻道以外的互動。

### <a name="engagement-initialization"></a>互動初始化

修改`App.xaml.cs`:

-   新增至您`using`陳述式︰

        using Microsoft.Azure.Engagement;

-   插入`EngagementReach.Instance.Init`只之後`EngagementAgent.Instance.Init`中`Application_Launching`:

        private void Application_Launching(object sender, LaunchingEventArgs e)
        {
           EngagementAgent.Instance.Init();
           EngagementReach.Instance.Init();
        }

-   插入`EngagementReach.Instance.OnActivated`中`Application_Activated`方法︰

        private void Application_Activated(object sender, ActivatedEventArgs e)
        {
           EngagementAgent.Instance.OnActivated(e);
           EngagementReach.Instance.OnActivated(e);
        }

> [AZURE.IMPORTANT] `EngagementReach.Instance.Init`專用的執行緒中執行。 您不必自行。

##<a name="app-store-submission-considerations"></a>應用程式存放區送出考量

使用推入通知] 時，Microsoft 就會採用一些規則︰

從 Microsoft[應用程式原則]的文件，區段 2.9:

1) 您必須要求使用者接受接收推入通知。 然後，在您的設定，新增的方式來停用推入通知]。

EngagementReach 物件提供兩種方法可以管理選擇加入/選擇加入-輸出，`EnableNativePush()`和`DisableNativePush()`。 您，例如，可以停用或啟用 MPNS 與切換開關設定中建立選項。

您也可以決定要停用透過互動設定的 MPNS\<windows phone-sdk-抵達-設定\>。

> 2.9.1) 應用程式必須先描述提供的通知並**取得快速的權限的使用者 （參加）**，並**必須提供的機制透過該使用者可以退出接收推入通知**。 使用 Microsoft 推入通知服務，提供所有通知必須提供給使用者的描述與一致，而且必須遵守所有適用的[應用程式原則] [Content Policies]與[特定的應用程式類型額外的需求]。

2) 您不應使用太多的推播通知。 互動會處理您的通知。

> 2.9.2) 應用程式和其使用 Microsoft 推入通知服務必須不過度使用網路容量或頻寬 Microsoft 推入通知服務，否則不當 burden 在 Windows Phone 或其他 Microsoft 裝置或使用過多的推入通知，取決於其比較合理排除或限制的 Microsoft 服務和必須不傷害或干擾任何 Microsoft 網路或伺服器或任何協力廠商伺服器或連線到 Microsoft 推入通知服務的網路。

3) 不要依賴 MPNS 傳送 criticals 資訊。 互動，使用 MPNS，因此此規則也適用於建立內互動前端行銷活動的。

> 2.9.3) Microsoft 推入通知服務可能不是使用傳送通知的要徑或其他任務可能會影響 [準備好的生活或死亡的項目，包括但不限於重要通知與醫療裝置或條件。 MICROSOFT 明文聲明不提供任何保證 MICROSOFT 推入通知服務使用 MICROSOFT 推入通知服務通知傳送會或不受干擾，、 保證即時為基礎發生錯誤或其他形式免費提供。

**我們不保證應用程式會通過驗證程序，如果您不受影響這些建議。**

##<a name="handle-data-push-optional"></a>處理資料推入 （選用）

如果您想要接收達到資料推入您的應用程式，您必須執行兩個事件的 EngagementReach 類別︰

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

##<a name="customize-ui-optional"></a>自訂使用者介面 （選用）

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

然後，設定 [內容`EngagementReach.Instance.Handler`欄位與您自訂的物件，在您`App.xaml.cs`類別內`Application_Launching`方法。

**範例︰**

    private void Application_Launching(object sender, LaunchingEventArgs e)
    {
       // your app initialization 
       EngagementReach.Instance.Handler = new ExampleReachHandler();
       // Engagement Agent and Reach initialization
    }

> [AZURE.NOTE] 根據預設，互動使用自己的實作`EngagementReachHandler`。 您還沒有建立您自己，和您這麼做，如果您沒有覆寫每一個方法。 預設行為是互動基底物件加以選取。

### <a name="layouts"></a>版面配置

根據預設，範圍就會使用內嵌的 DLL 的資源來顯示通知和頁面。

不過，您可以決定要使用您自己的資源，以反映您在這些元件的品牌。

您可以覆寫`EngagementReachHandler`子類別中，若要判斷要使用您的版面配置的互動方式︰

**範例︰**

    // In your subclass of EngagementReachHandler
    
    public override string GetTextViewAnnouncementUri()
    {
       // return the path of your own xaml
    }
    
    public override string GetWebViewAnnouncementUri()
    {
       // return the path of your own xaml
    }
    
    public override string GetPollUri()
    {
       // return the path of your own xaml
    }
    
    public override EngagementNotificationView CreateNotification(EngagementNotificationViewModel viewModel)
    {
       // return a new instance of your own notification
    }

> [AZURE.TIP] `CreateNotification`方法可以傳回 null。 不會顯示通知，並會遭到捨棄達到行銷活動。

若要簡化您的版面配置實作，我們也會提供我們自己 xaml 這可以做為您的程式碼的基礎。 位於互動 SDK 封存 (/ src/達到 /)。

> [AZURE.WARNING] 我們提供的來源是我們使用完全相同的。 因此，如果您想要直接加以修改，別忘了變更的命名空間和名稱。

### <a name="notification-position"></a>通知位置

根據預設，在應用程式通知會顯示在左下方的 [應用程式。 您可以藉由覆寫變更這個行為`GetNotificationPosition`的方法`EngagementReachHandler`物件。

    // In your subclass of EngagementReachHandler
    
    public override EngagementReachHandler.NotificationPosition GetNotificationPosition(EngagementNotificationViewModel viewModel)
    {
       // return a value of the EngagementReachHandler.NotificationPosition enum (TOP or BOTTOM)
    }

目前，您可以選擇之間`BOTTOM`（預設值） 和`TOP`位置。

### <a name="launch-message"></a>啟動訊息

當使用者按一下 [系統通知 （快顯） 時，互動啟動應用程式、 載入的推入郵件內容，並顯示對應的行銷活動的頁面。

在啟動應用程式之間 （根據您的網路速度） 頁面的顯示有延遲。

項目正在載入表示給使用者，您應該提供視覺化的資訊，例如進度列或進度。 互動無法處理本身，但您提供一些處理常式。

若要執行的回撥，請執行︰

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

您可以在中設定回撥您`Application_Launching`的方法將`App.xaml.cs`檔案，最好是之前`EngagementReach.Instance.Init()`來電。

> [AZURE.TIP] 每個處理常式是由 UI 執行緒呼叫。 您不必擔心時使用的訊息方塊，或內容的 UI 相關。

[應用程式原則]:http://msdn.microsoft.com/library/windows/apps/hh184841(v=vs.105).aspx
[Content Policies]:http://msdn.microsoft.com/library/windows/apps/hh184842(v=vs.105).aspx
[特定應用程式類型額外的需求]:http://msdn.microsoft.com/library/windows/apps/hh184838(v=vs.105).aspx
 
