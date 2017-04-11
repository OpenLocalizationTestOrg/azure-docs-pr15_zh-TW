<properties
    pageTitle="Windows 標準進階使用 MobileApps 互動的報告"
    description="如何使用 Windows 通用應用程式整合 Azure 行動互動"                  
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-store"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/12/2016"
    ms.author="piyushjo;ricksal" />

# <a name="advanced-reporting-with-the-windows-universal-apps-engagement-sdk"></a>進階使用 Windows 通用的應用程式互動 SDK 的報告

> [AZURE.SELECTOR]
- [通用 Windows](mobile-engagement-windows-store-advanced-reporting.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-advanced-reporting.md)

本主題說明 Windows 通用應用程式中的其他報告案例。 這些情況包括您可以選擇要套用至 [[快速入門](mobile-engagement-windows-store-dotnet-get-started.md)教學課程中建立的應用程式的選項。

## <a name="prerequisites"></a>必要條件

[AZURE.INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

在開始之前本教學課程中，您必須先完成[快速入門](mobile-engagement-windows-store-dotnet-get-started.md)教學課程中，這是故意直接和簡單。 本教學課程說明您可以選擇其他選項。

## <a name="specifying-engagement-configuration-at-runtime"></a>指定在執行階段互動設定

互動設定集中放在`Resources\EngagementConfiguration.xml`專案，也就是其中指定 [[快速入門](mobile-engagement-windows-store-dotnet-get-started.md)] 主題中的檔案。

但您也可以指定其在執行階段︰ 您可以呼叫互動代理程式初始化之前的下列方法︰

          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Set the Engagement connection string. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);



## <a name="recommended-method-overload-your-page-classes"></a>建議使用的方法︰ 超載您`Page`類別

若要啟動的互動，來計算使用者、 工作階段、 活動、 當機和統計資料所需的所有記錄報告，讓所有您`Page`子類別繼承自`EngagementPage`類別。

以下是您的應用程式的頁面範例。 您可以執行應用程式的所有頁面的相同項的目。

### <a name="c-source-file"></a>C# 來源檔案

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

### <a name="xaml-file"></a>XAML 檔案

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

### <a name="override-the-default-behaviour"></a>覆寫預設行為

根據預設，作為活動名稱，沒有額外的報告] 頁面的類別名稱。 如果類別使用 「 頁 」 後置字元，互動會移除。

若要覆寫名稱的預設行為，請新增此程式碼︰

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
          /* your code */
          return "new name";
        }

若要報告與您的活動的額外資訊，請將此程式碼新增︰

        // in the .xaml.cs file
        protected override Dictionary<object,object> GetEngagementPageExtra()
        {
          /* your code */
          return extra;
        }

中的呼叫這些方法`OnNavigatedTo`頁面的方法。

### <a name="alternate-method-call-startactivity-manually"></a>替代方法︰ 來電`StartActivity()`手動

如果您無法或不希望超載您`Page`課程]，您可以改為啟動您的活動，則可電話`EngagementAgent`直接方法。

我們建議您電話`StartActivity`內您`OnNavigatedTo`頁面的方法。

            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
              base.OnNavigatedTo(e);
              EngagementAgent.Instance.StartActivity("MyPage");
            }

> [AZURE.IMPORTANT]  請確定您正確結束工作階段。
>
> Windows 通用 SDK 會自動呼叫`EndActivity`關閉應用程式時的方法。 因此，是**強烈**建議您撥打`StartActivity`方法每活動使用者的變更，請為**永不**呼叫`EndActivity`方法。 這個方法通知互動伺服器目前的使用者已離開的應用程式會影響所有應用程式的記錄。

## <a name="advanced-reporting"></a>進階報告

或者，您可能會想回報特定應用程式的事件，錯誤與工作，若要這麼做，請使用其他方法集中`EngagementAgent`類別。 互動 API 」 可讓所有的互動進階功能的用途。

如需詳細資訊，請參閱[如何使用進階標記 API 通用 Windows 應用程式中的行動互動](mobile-engagement-windows-store-use-engagement-api.md)。
