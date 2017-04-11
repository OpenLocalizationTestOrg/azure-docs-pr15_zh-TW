<properties
    pageTitle="快速入門 Azure 行動互動凝聚力 iOS 部署"
    description="瞭解如何使用 Azure 行動互動分析及推入通知凝聚力應用程式部署 iOS 裝置。"
    services="mobile-engagement"
    documentationCenter="unity"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-unity-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-unity-ios-deployment"></a>快速入門 Azure 行動互動凝聚力 iOS 部署

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

本主題說明如何使用 Azure 行動互動，若要瞭解您的應用程式使用狀況及部署至 iOS 裝置時，如何傳送給推入通知，來區隔凝聚力應用程式的使用者。
本教學課程中使用傳統的凝聚力就緒球教學課程的起點。 您應該追蹤我們展現下列教學課程中的行動互動整合進行之前，先此[教學課程](mobile-engagement-unity-roll-a-ball.md)中的步驟進行。 

本教學課程的需求如下︰

+ [凝聚力編輯器](http://unity3d.com/get-unity)
+ [行動裝置的互動凝聚力 SDK](https://aka.ms/azmeunitysdk)
+ XCode 編輯器

> [AZURE.NOTE] 若要完成此教學課程中，您必須使用中的 Azure 帳戶。 如果您沒有帳戶，您可以建立的免費的試用帳戶在幾分鐘。 如需詳細資訊，請參閱[Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-unity-ios-get-started)。

##<a id="setup-azme"></a>IOS 應用程式的安裝行動互動

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>將您的應用程式連線到行動互動後端

###<a name="import-the-unity-package"></a>匯入凝聚力套件

1. 下載[行動互動凝聚力套件](https://aka.ms/azmeunitysdk)，並將其儲存到本機電腦。 

2. 移至 [**資產]-> [匯入套件]-> [自訂套件**，然後選取您下載套件上述步驟。 

    ![][70] 

3. 請確定已選取所有檔案，然後按一下 [**匯入**] 按鈕。 

    ![][71] 

4. 匯入成功後，您會看到 [匯入的 SDK 檔案在專案中。  

    ![][72] 

###<a name="update-the-engagementconfiguration"></a>更新 EngagementConfiguration

1. 從 [SDK 資料夾更新**EngagementConfiguration**指令碼檔開啟**IOS\_連線\_字串**含有較早從 Azure 入口網站取得連線字串。  

    ![][73]

2. 儲存檔案。 

###<a name="configure-the-app-for-basic-tracking"></a>設定應用程式的基本追蹤

1. 開啟**PlayerController**指令碼附加至播放程式物件進行編輯。 

2. 新增下列使用陳述式︰

        using Microsoft.Azure.Engagement.Unity;

3. 新增下列`Start()`方法
    
        EngagementAgent.Initialize();
        EngagementAgent.StartActivity("Home");

###<a name="deploy-and-run-the-app"></a>部署及執行應用程式

1. IOS 裝置連接到您的電腦。 

2. 開啟**檔案]-> [建立設定** 

    ![][40]

3. 選取**iOS** ，然後按一下 [**切換平台**上

    ![][41]

    ![][42]

4. 按一下 [**播放程式設定**，並提供正確的套件識別碼。 

    ![][53]

5. 最後按一下 [上**建立並執行**

    ![][54]

6. 可能會要求您提供資料夾的名稱來儲存 iOS 套件。 

    ![][43]

7. 一切關係，然後將編譯專案，而且您應該中開啟它，XCode 應用程式。 

8. 請確定**封裝識別項**是正確的專案。  

    ![][75]

10. 立即執行中 XCode 應用程式，以便將套件部署至您已連線的裝置，您應該會在您的電話上看到凝聚力遊戲 ！ 

##<a id="monitor"></a>使用即時監視連線應用程式

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>啟用推入通知和訊息的應用程式

行動互動可讓您與您的使用者進行互動，抵達推入通知與的應用程式行銷活動的內容中的訊息。 本單元稱為行動互動入口網站中的範圍。
您沒有進行任何其他設定，您會收到通知的應用程式中，並已為其設定。

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- Images. -->
[40]: ./media/mobile-engagement-unity-ios-get-started/40.png
[41]: ./media/mobile-engagement-unity-ios-get-started/41.png
[42]: ./media/mobile-engagement-unity-ios-get-started/42.png
[43]: ./media/mobile-engagement-unity-ios-get-started/43.png
[53]: ./media/mobile-engagement-unity-ios-get-started/53.png
[54]: ./media/mobile-engagement-unity-ios-get-started/54.png
[70]: ./media/mobile-engagement-unity-ios-get-started/70.png
[71]: ./media/mobile-engagement-unity-ios-get-started/71.png
[72]: ./media/mobile-engagement-unity-ios-get-started/72.png
[73]: ./media/mobile-engagement-unity-ios-get-started/73.png
[74]: ./media/mobile-engagement-unity-ios-get-started/74.png
[75]: ./media/mobile-engagement-unity-ios-get-started/75.png
