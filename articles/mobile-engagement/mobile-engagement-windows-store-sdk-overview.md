<properties
    pageTitle="Windows SDK 通用整合"
    description="Windows 通用適用於整合 SDK 的 Azure 行動互動"                                     
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
    ms.date="08/12/2016"
    ms.author="piyushjo;ricksal" />

#<a name="windows-universal-sdk-integration-for-azure-mobile-engagement"></a>Windows Azure 行動互動通用 SDK 整合

這份文件將說明整合及設定的所有選項可用 Azure 行動互動 Windows 通用 SDK。

## <a name="prerequisites"></a>必要條件

在開始之前本教學課程中，您必須先完成我們[15 分鐘教學課程](mobile-engagement-windows-store-dotnet-get-started.md)。

## <a name="advanced-features"></a>進階的功能

### <a name="reporting-features"></a>報表功能
您可以新增下列功能︰

1. [進階報表的選項](mobile-engagement-windows-store-advanced-reporting.md)

2. [進階的設定選項](mobile-engagement-windows-store-advanced-configuration.md)

### <a name="notifications"></a>通知

[如何將整合 Windows 通用應用程式中的範圍 （通知）](mobile-engagement-windows-store-integrate-engagement-reach.md)

### <a name="tag-plan-implementation"></a>標記計劃實作︰

[如何使用 Windows 通用應用程式中標記 API 進階的行動互動](mobile-engagement-windows-store-use-engagement-api.md)

##<a name="release-notes"></a>版本資訊

###<a name="340-04192016"></a>3.4.0 (04/19/2016)

-   連絡覆疊的改良功能。
-   新增 「 TestLogLevel 」 API 篩選器啟用/停用主控台記錄 SDK 所發出。
-   針對選取目標應用程式上未顯示的第一個活動的固定在活動通知開始。

舊版，請參閱[完整版本資訊](mobile-engagement-windows-store-release-notes.md)

##<a name="upgrade-procedures"></a>升級程序

如果您已經有整合互動的舊版應用程式，您必須升級 SDK 時，請考慮下列幾點。

如果您未接來電 SDK 的多個版本，您可能必須執行幾個程序。 請完成的[升級程序](mobile-engagement-windows-store-upgrade-procedure.md)。 如果您將移轉 0.10.1 到 0.11.0 您必須先依照 「 從至 0.10.1 0.9.0 」 的程序然後 」 從至 0.11.0 0.10.1 」 程序的範例。

###<a name="from-330-to-340"></a>從 3.3.0 至 3.4.0

####<a name="test-logs"></a>測試記錄

主控台記錄所產生的 SDK 現在就可以啟用/停用/篩選。 若要自訂，更新屬性`EngagementAgent.Instance.TestLogEnabled`其中一個可用的值`EngagementTestLogLevel`列舉，例如︰

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

####<a name="resources"></a>資源

已經過改良達到覆疊。 它是 SDK NuGet 套件的資源。

升級至新版本的 SDK，您可以選擇是否要保留現有的檔案從您的資源的 [重疊] 資料夾，或不︰

* 如果先前的覆疊正常運作，或者整合`WebView`元素以手動方式，然後您可以決定要讓您結束檔案，其是否仍能運作。
* 若要更新至新的覆疊，取代整個`overlay`從您的資源以新的 SDK 的資料夾 (UWP 應用程式︰ 升級之後，您可以從 %USERPROFILE%取得新的覆疊資料夾\\.nuget\packages\MicrosoftAzure.MobileEngagement\3.4.0\content\win81\Resources)。

> [AZURE.WARNING] 使用新的覆疊會覆寫先前的版本上所做的任何自訂。

### <a name="upgrade-from-older-versions"></a>從舊版升級

請參閱[升級程序](mobile-engagement-windows-store-upgrade-procedure.md)
