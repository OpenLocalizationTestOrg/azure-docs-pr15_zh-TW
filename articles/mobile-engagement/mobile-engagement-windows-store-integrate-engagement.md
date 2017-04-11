<properties 
    pageTitle="Windows 通用的應用程式的互動 SDK 整合" 
    description="如何使用 Windows 通用應用程式整合 Azure 行動互動"                  
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

# <a name="windows-universal-apps-engagement-sdk-integration"></a>Windows 通用的應用程式的互動 SDK 整合

> [AZURE.SELECTOR] 
- [通用 Windows](mobile-engagement-windows-store-integrate-engagement.md) 
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
- [iOS](mobile-engagement-ios-integrate-engagement.md) 
- [Android](mobile-engagement-android-integrate-engagement.md) 

此程序說明最簡單的方式啟動互動的分析及監視通用 Windows 應用程式中的函數。

以下是足以啟動報表的計算有關使用者、 工作階段、 活動、 當機和 Technicals 所有統計資料所需的記錄。 使用手動互動 API 必須完成計算其他統計資料事件、 錯誤等工作所需的記錄檔的報表 （這些統計資料與應用程式有關之後，請瞭解[如何使用進階標記 API 通用 Windows 應用程式中的行動互動](mobile-engagement-windows-store-use-engagement-api.md)。

## <a name="supported-versions"></a>支援的版本

Mobile 互動 SDK 的 Windows 通用應用程式只可以整合到 Windows 執行階段，並為目標的通用 Windows 平台應用程式︰

-   Windows 8
-   Windows 8.1
-   在 Windows Phone 8.1
-   在 Windows 10 （桌面與行動高中）

> [AZURE.NOTE] 如果您的目標在 Windows Phone Silverlight，請參閱[Windows Phone Silverlight 整合程序](mobile-engagement-windows-phone-integrate-engagement.md)。

## <a name="install-the-mobile-engagement-universal-apps-sdk"></a>安裝行動互動通用應用程式 SDK

### <a name="all-platforms"></a>所有的平台

使用為 Nuget 套件稱為*MicrosoftAzure.MobileEngagement*Mobile 互動 SDK 的 Windows 通用應用程式。 您可以安裝從 Visual Studio Nuget 套件管理員。

### <a name="windows-8x-and-windows-phone-81"></a>Windows 8.x 和 Windows Phone 8.1

NuGet 自動部署中的 SDK 資源`Resources`應用程式專案的根資料夾。

### <a name="windows-10-universal-windows-platform-applications"></a>Windows 10 通用 Windows 平台應用程式

NuGet 不會自動部署 SDK 中的資源尚未 UWP 應用程式。 您必須手動操作，直到資源部署會重新引入 NuGet 中︰

1.  開啟您的檔案總管]。
2.  瀏覽至下列位置 （**x.x.x**是您所安裝的互動的版本）︰ *%USERPROFILE%\\.nuget\packages\MicrosoftAzure.MobileEngagement\\**x.x.x**\\content\win81*
3.  拖放從 [檔案總管] 的 [**資源**] 資料夾，在 Visual Studio 專案的根目錄。
4.  在 Visual Studio 中選取您的專案，並啟動**方案總管]**的頂端 [**顯示所有檔案**] 圖示。
5.  有些檔案不會包含在專案中。 匯入其一次以滑鼠右鍵按一下 [**資源**] 資料夾中，**從專案中排除**然後另以滑鼠右鍵按一下 [**資源**] 資料夾中，重新將整個資料夾**包含在專案**。 從 [**資源**] 資料夾的所有檔案現在會都包含在專案中。

## <a name="add-the-capabilities"></a>新增功能

互動 SDK 需要 Windows SDK 的部分功能才能正常運作。

開啟您`Package.appxmanifest`檔案，並確定宣告下列功能︰

-   `Internet (Client)`

## <a name="initialize-the-engagement-sdk"></a>初始化 SDK 的互動

### <a name="engagement-configuration"></a>互動設定

互動設定集中放在`Resources\EngagementConfiguration.xml`專案的檔案。

編輯指定此檔案︰

-   您的應用程式連接字串標記之間`<connectionString>`和`<\connectionString>`。

如果您想要指定在執行階段，您可以呼叫互動代理程式初始化之前下列方法︰
          
          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Set the Engagement connection string. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);

Azure 傳統入口網站上，會顯示您的應用程式的連接字串。

### <a name="engagement-initialization"></a>互動初始化

當您建立新的專案，`App.xaml.cs`會產生檔案。 繼承自這個類別`Application`且包含許多重要的方法。 它也會用來初始化互動 SDK。

修改`App.xaml.cs`:

-   新增至您`using`陳述式︰

        using Microsoft.Azure.Engagement;

-   定義要共用一次為所有呼叫互動初始設定的方法︰

        private void InitEngagement(IActivatedEventArgs e)
        {
          EngagementAgent.Instance.Init(e);
        
          // or
        
          EngagementAgent.Instance.Init(e, engagementConfiguration);
        }
        
-   通話`InitEngagement`中`OnLaunched`方法︰

        protected override void OnLaunched(LaunchActivatedEventArgs e)
        {
          InitEngagement(e);
        }

-   當您的應用程式啟動時使用自訂的配置、 另一個應用程式或命令列然後`OnActivated`呼叫方法。 您也需要進行互動 SDK，您的應用程式啟動時。 若要這麼做，覆寫`OnActivated`方法︰

        protected override void OnActivated(IActivatedEventArgs args)
        {
          InitEngagement(args);
        }

> [AZURE.IMPORTANT] 我們強烈建議您在應用程式的其他位置新增互動初始化。

## <a name="basic-reporting"></a>基本報表

### <a name="recommended-method-overload-your-page-classes"></a>建議使用的方法︰ 超載您`Page`類別

若要啟動互動，來計算使用者、 工作階段、 活動、 當機和統計資料所需的所有記錄的報表，只要進行所有您`Page`子類別繼承自`EngagementPage`類別。

以下是應用的如何進行此動作程式頁面的範例。 您可以執行應用程式的所有頁面的相同項的目。

#### <a name="c-source-file"></a>C# 來源檔案

修改您的頁面`.xaml.cs`檔案︰

-   新增至您`using`陳述式︰

        using Microsoft.Azure.Engagement;

-   取代`Page`與`EngagementPage`:

**不互動︰**
    
        namespace Example
        {
          public sealed partial class ExamplePage : Page
          {
            [...]
          }
        }

**使用互動︰**

        using Microsoft.Azure.Engagement;
        
        namespace Example
        {
          public sealed partial class ExamplePage : EngagementPage 
          {
            [...]
          }
        }

> [AZURE.IMPORTANT] 如果您的頁面會覆寫`OnNavigatedTo`方法，請務必呼叫`base.OnNavigatedTo(e)`。 否則，不會報告活動 (`EngagementPage`通話`StartActivity`內其`OnNavigatedTo`方法)。

#### <a name="xaml-file"></a>XAML 檔案

修改您的頁面`.xaml`檔案︰

-   新增至您的命名空間宣告︰

        xmlns:engagement="using:Microsoft.Azure.Engagement"

-   取代`Page`與`engagement:EngagementPage`:

**不互動︰**

        <Page>
            <!-- layout -->
            ...
        </Page>

**使用互動︰**

        <engagement:EngagementPage 
            xmlns:engagement="using:Microsoft.Azure.Engagement">
            <!-- layout -->
            ...
        </engagement:EngagementPage >

#### <a name="override-the-default-behaviour"></a>覆寫預設行為

根據預設，作為活動名稱，沒有額外的報告] 頁面的類別名稱。 如果類別使用 「 頁 」 後置字元，互動也會移除它。

如果您想要覆寫預設行為，其名稱，只要將此新增至您的程式碼︰

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
          /* your code */
          return "new name";
        }

如果您想要報告您的活動與一些額外資訊，您可以將此加入您的程式碼︰

        // in the .xaml.cs file
        protected override Dictionary<object,object> GetEngagementPageExtra()
        {
          /* your code */
          return extra;
        }

中的呼叫這些方法`OnNavigatedTo`頁面的方法。

### <a name="alternate-method-call-startactivity-manually"></a>替代方法︰ 來電`StartActivity()`手動

如果您無法或不希望超載您`Page`課程]，您可以改為啟動您的活動，則可電話`EngagementAgent`直接方法。

我們建議您撥打`StartActivity`內您`OnNavigatedTo`頁面的方法。

            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
              base.OnNavigatedTo(e);
              EngagementAgent.Instance.StartActivity("MyPage");
            }

> [AZURE.IMPORTANT]  請確定您正確結束工作階段。
> 
> Windows 通用 SDK 會自動呼叫`EndActivity`關閉應用程式時的方法。 因此，是**強烈**建議您撥打`StartActivity`方法每活動使用者的變更，請為**永不**呼叫`EndActivity`方法，這個方法會傳送給互動 server，目前的使用者已離開應用程式，這會影響所有應用程式的記錄。

## <a name="advanced-reporting"></a>進階報告

或者，您可能會想要報表應用程式中的特定事件與錯誤的工作，若要這麼做，請使用其他方法中找到`EngagementAgent`類別。 使用所有互動的進階功能，可都讓互動 API。

如需詳細資訊，請參閱[如何使用進階標記 API 通用 Windows 應用程式中的行動互動](mobile-engagement-windows-store-use-engagement-api.md)。

##<a name="advanced-configuration"></a>進階的組態

### <a name="disable-automatic-crash-reporting"></a>停用自動損毀報告

您可以停用自動當機報告的互動功能。 然後，在時處理的例外狀況，就會發生的互動不會執行任何動作。

> [AZURE.WARNING] 如果您打算停用此功能，請注意，當未處理損毀，就會發生應用程式中互動不會傳送損毀，**並**不會關閉工作階段與工作。

若要停用自動損毀報告，只要自訂您的設定，視您宣告它的方式而定︰

#### <a name="from-engagementconfigurationxml-file"></a>從`EngagementConfiguration.xml`檔案

若要設定報表當機`false`之間`<reportCrash>`和`</reportCrash>`標籤。

#### <a name="from-engagementconfiguration-object-at-run-time"></a>從`EngagementConfiguration`物件在執行階段

將報表當機設定為使用您的 EngagementConfiguration 物件的 false。

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
        
        /* Disable Engagement crash reporting. */
        engagementConfiguration.Agent.ReportCrash = false;

### <a name="burst-mode"></a>尖峰模式

根據預設，互動服務報表中進行即時記錄。 如果您的應用程式經常報告記錄，但最好緩衝記錄，以及報告它們，一次一般時間基底 （又稱為 「 突發模式 」）。

若要這麼做，請連絡方法︰

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

引數是以**毫秒為單位**的值。 在任何時候，如果您想要重新啟動即時記錄，只會撥打沒有任何參數，或 0 值的方法。

尖峰模式稍微增加電池，但會影響互動監視器︰ 所有的工作階段及工作期間會捨入至突發臨界值 （因此，工作階段及工作短比突發閥值可能無法看到）。 建議使用不超過 30000 （30 秒） 的突發閾值。 您必須注意儲存記錄檔的限於 300 個項目。 如果傳送太長，您可能會遺失某些記錄檔。

> [AZURE.WARNING] 尖峰閥值無法設定期間少於較順序為 1。 如果您嘗試執行此作業，SDK 會顯示錯誤追蹤，會自動重設為預設值，也就是 0。 這樣會觸發 SDK 報告即時記錄檔。

[here]:http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]:http://docs.nuget.org/docs/start-here/overview
 
