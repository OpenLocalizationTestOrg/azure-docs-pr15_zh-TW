<properties 
    pageTitle="Windows Phone Silverlight SDK 概觀" 
    description="Windows Phone Silverlight SDK Azure 行動互動的概觀"                     
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

#<a name="windows-phone-silverlight-sdk-overview-for-azure-mobile-engagement"></a>Azure 行動互動版的 windows Phone Silverlight SDK 概觀

從這裡開始，以取得詳細資料整合在 Windows Phone Silverlight 應用程式中的 Azure 行動互動的方式。 如果您想要嘗試一下第一次，請確定您完成我們[15 分鐘教學課程](mobile-engagement-windows-phone-get-started.md)。

按一下以查看[SDK 內容](mobile-engagement-windows-phone-sdk-content.md)

##<a name="integration-procedures"></a>整合程序

1. 從這裡開始︰[整合 Windows Phone Silverlight 應用程式中的行動互動的方式](mobile-engagement-windows-phone-integrate-engagement.md)

2. 通知︰ 「[整合 Windows Phone Silverlight 應用程式中的範圍 （通知） 的方式](mobile-engagement-windows-phone-integrate-engagement-reach.md)

3. 標記計劃實作︰[使用進階標記 API Windows Phone Silverlight 應用程式中的行動互動的方式](mobile-engagement-windows-phone-use-engagement-api.md)

##<a name="release-notes"></a>版本資訊

###<a name="330-04192016"></a>3.3.0 (04/19/2016)
部分*MicrosoftAzure.MobileEngagement* nuget 封裝**v3.4.0**

-   新增 「 TestLogLevel 」 API 篩選器啟用/停用主控台記錄 SDK 所發出。

舊版，請參閱[完整版本資訊](mobile-engagement-windows-phone-release-notes.md)

##<a name="upgrade-procedures"></a>升級程序

如果您已經有整合我們 SDK 的舊版應用程式，您必須升級 SDK 時，請考慮下列幾點。

您可能必須追蹤多個程序，如果您未接來電 SDK 的多個版本。 請完成的[升級程序](mobile-engagement-windows-phone-upgrade-procedure.md)。 如果您將移轉 0.10.1 到 0.11.0 您必須先依照 「 從至 0.10.1 0.9.0 」 的程序然後 」 從至 0.11.0 0.10.1 」 程序的範例。

###<a name="from-200-to-330"></a>從 2.0.0 至 3.3.0

####<a name="test-logs"></a>測試記錄

主控台記錄所產生的 SDK 現在就可以啟用/停用/篩選。 若要自訂此，更新屬性`EngagementAgent.Instance.TestLogEnabled`其中一個可用的值`EngagementTestLogLevel`列舉，例如︰

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

### <a name="upgrade-from-older-versions"></a>從舊版升級

請參閱[升級程序](mobile-engagement-windows-phone-upgrade-procedure.md)
 
