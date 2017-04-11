<properties
    pageTitle="進階 Azure 行動互動 Android SDK 報表選項"
    description="說明如何進行的 Azure 行動互動 Android SDK 擷取分析的進階報告"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="piyushjo;ricksal" />

# <a name="advanced-reporting-with-engagement-on-android"></a>進階使用互動的 Android 上的報告

> [AZURE.SELECTOR]
- [通用 Windows](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-advanced-reporting.md)

本主題即說明您 Android 應用程式中的其他報告案例。 在 [[快速入門](mobile-engagement-android-get-started.md)教學課程中建立的應用程式，您可以套用這些選項。

## <a name="prerequisites"></a>必要條件

[AZURE.INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

教學課程您完成故意直接和簡單，但進階您可以選擇的選項。

## <a name="modifying-your-activity-classes"></a>修改您`Activity`類別

[開始教學課程](mobile-engagement-android-get-started.md)中，在所有您必須是讓您`*Activity`子類別繼承對應`Engagement*Activity`類別。 例如，如果您舊版的活動延伸`ListActivity`，可以將它延伸`EngagementListActivity`。

> [AZURE.IMPORTANT] 使用時`EngagementListActivity`或`EngagementExpandableListActivity`，請確定任何撥`requestWindowFeature(...);`進行通話之前`super.onCreate(...);`，否則會當機。

您可以找到這些類別中的`src`資料夾，並可以將其複製到您的專案。 類別，也會**JavaDoc**。

## <a name="alternate-method-call-startactivity-and-endactivity-manually"></a>替代方法︰ 來電`startActivity()`和`endActivity()`手動

如果您無法或不希望超載您`Activity`課程]，您可以改為開始及結束您的活動，則可電話`EngagementAgent`的直接方法。

> [AZURE.IMPORTANT] Android SDK 永不通話`endActivity()`方法，即使關閉應用程式時 （在 Android 上的應用程式不關閉）。 因此，是*強烈*建議您撥打`startActivity()`中的方法`onResume`的*所有*的回撥您的活動，以及`endActivity()`中的方法`onPause()`的*所有*的回撥您的活動。 這是唯一的方法，確定不會遺漏工作階段。 如果遺漏工作階段，互動服務永遠不會中斷互動後端 （因為服務保持連線，只要工作階段中）。

以下是範例︰

    public class MyActivity extends Some3rdPartyActivity
    {
      @Override
      protected void onResume()
      {
        super.onResume();
        String activityNameOnEngagement = EngagementAgentUtils.buildEngagementActivityName(getClass()); // Uses short class name and removes "Activity" at the end.
        EngagementAgent.getInstance(this).startActivity(this, activityNameOnEngagement, null);
      }

      @Override
      protected void onPause()
      {
        super.onPause();
        EngagementAgent.getInstance(this).endActivity();
      }
    }

此範例中是類似`EngagementActivity`類別和其變化，其原始程式碼所提供的`src`資料夾。

## <a name="using-applicationoncreate"></a>使用 Application.onCreate()

您將在任何程式碼`Application.onCreate()`及其他應用程式中執行所有應用程式的處理程序，包括互動服務回撥。 可能不想要的側邊效果，例如不需要的記憶體配置和互動的程序，或重複的廣播的接收器或服務的對話。

如果您覆寫`Application.onCreate()`，我們建議您新增下列程式碼片段開頭您`Application.onCreate()`函數︰

     public void onCreate()
     {
       if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
         return;

       ... Your code...
     }

您可以執行相同動作`Application.onTerminate()`， `Application.onLowMemory()`，及`Application.onConfigurationChanged(...)`。

您也可以延伸`EngagementApplication`，而不是延伸`Application`: 回撥`Application.onCreate()`程序檢查和通話`Application.onApplicationProcessCreate()`僅其他的回撥如果目前的程序沒有裝載互動服務的項目，請套用相同的規則。

## <a name="tags-in-the-androidmanifestxml-file"></a>AndroidManifest.xml 檔案中的標籤

在 AndroidManifest.xml 檔案中的服務標記中`android:label`屬性可讓您選擇的互動服務名稱，使用者在他們的電話的 「 執行中的服務] 畫面中所顯示的樣子。 我們建議您設定此屬性`"<Your application name>Service"`(例如， `"AcmeFunGameService"`)。

指定`android:process`屬性可確保互動服務執行本身 （為您的應用程式，可讓您的主要/UI 執行緒可能會降低回應，互動執行中的相同程序） 的程序中。

## <a name="building-with-proguard"></a>建立與 ProGuard

如果您建置 ProGuard 與應用程式套件時，您需要讓某些類別。 您可以使用下列設定程式碼片段︰

    -keep public class * extends android.os.IInterface
    -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
    <methods>;
    }
