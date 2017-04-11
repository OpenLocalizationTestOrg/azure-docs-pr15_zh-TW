<properties
    pageTitle="進階的 Windows 通用的應用程式的互動 SDK 設定"
    description="進階 Azure 行動互動使用 Windows 通用應用程式的設定選項"                    
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
    ms.date="10/04/2016"
    ms.author="piyushjo;ricksal" />

# <a name="advanced-configuration-for-windows-universal-apps-engagement-sdk"></a>進階的 Windows 通用的應用程式的互動 SDK 設定

> [AZURE.SELECTOR]
- [通用 Windows](mobile-engagement-windows-store-advanced-configuration.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-advanced-configuration.md)

此程序說明如何設定 Azure 行動互動 Android 應用程式的不同設定選項。

## <a name="prerequisites"></a>必要條件

[AZURE.INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

##<a name="advanced-configuration"></a>進階的組態

### <a name="disable-automatic-crash-reporting"></a>停用自動損毀報告

您可以停用自動當機報告的互動功能。 然後，您也可以處理的例外狀況時，互動不執行任何動作。

> [AZURE.WARNING] 如果您停用此功能，然後在您的應用程式，因為內含無法處理損毀時互動不會傳送損毀，**並**不會關閉工作階段與工作。

若要停用自動損毀報告，自訂您的設定，視您宣告它的方式而定︰

#### <a name="from-engagementconfigurationxml-file"></a>從`EngagementConfiguration.xml`檔案

若要設定報表當機`false`之間`<reportCrash>`和`</reportCrash>`標籤。

#### <a name="from-engagementconfiguration-object-at-run-time"></a>從`EngagementConfiguration`物件在執行階段

設定為 false 使用 EngagementConfiguration 物件的報表當機。

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

        /* Disable Engagement crash reporting. */
        engagementConfiguration.Agent.ReportCrash = false;

### <a name="disable-real-time-reporting"></a>停用即時報告

根據預設，互動服務報表中進行即時記錄。 如果您的應用程式常見問題報告記錄，但最好緩衝記錄和一般時間為基礎，一次報告。 這稱為 「 尖峰模式 」。

若要這麼做，請連絡方法︰

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

引數是以**毫秒為單位**的值。 每當您要重新啟動即時記錄時，呼叫沒有任何參數，或 0 值的方法。

尖峰模式稍微增加電池，但會影響互動監視器︰ 所有的工作階段及工作期間會四捨五入至突發臨界值 （因此，工作階段及工作短比突發閥值可能無法看到）。 我們建議使用不超過 30000 （30 秒） 的突發閾值。 儲存記錄檔限於 300 個項目。 如果傳送太長，您可能會遺失某些記錄檔。

> [AZURE.WARNING] 期間小於一個第二，您無法設定尖峰閥值。 如果這麼做，SDK 會顯示錯誤追蹤，並自動重設為預設值，0 秒數。 這樣會觸發 SDK 報告即時記錄檔。

[here]:http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]:http://docs.nuget.org/docs/start-here/overview
