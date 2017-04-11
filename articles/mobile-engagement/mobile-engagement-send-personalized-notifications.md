<properties 
    pageTitle="傳送個人化的通知 Azure 行動互動" 
    description="如何傳送個人化的通知，包括在他們的名稱等通知中的使用者設定檔資訊"        
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="all" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="personalize-notifications-by-including-user-name"></a>將個人化通知包括使用者名稱

在您的任務，讓通知更吸引人應用程式使用者，您應該考慮這些個人化。 選擇性地使用地址，使其更多個人通知的 [應用程式的使用者名稱，包括一個功能強大的方法。 注意以下-應該達到新增使用者名稱的通知謹慎因為過度此策略然後它可能有部分應用程式使用者為怪異。 您也應該確保您會讓使用者選擇加入集，以開始使用此之前，先使用行動應用程式的完整同意提供這些個人詳細資料給您。 

技術上而言，與 Azure 行動互動，您可以完成個人化的通知，依照下列步驟，我們會使用包含通知] 中的使用者名稱的案例。 您會使用應用程式資訊的概念，或其值表示無法傳 Sdk 的標籤整合在行動應用程式，或透過 Api。 然後可以使用這些應用程式 Infos 或標籤︰

1. 為目標應用程式資訊的數值為根據的特定使用者的通知或 
2. 為在通知] 會取代使用者專屬的裝置/值時傳送通知給該裝置的預留位置。 

> [AZURE.IMPORTANT] 請注意傳送通知的速度會看到減少，由於這個額外的應用程式資訊值取代為每個通知的處理。 

##<a name="register-app-info-in-the-mobile-engagement-portal"></a>在行動裝置的互動入口網站中登錄應用程式資訊

1) 開始註冊 Azure 入口網站中的應用程式的資訊] 或 [標籤]。 移至 [**設定** -> 的**標籤 （應用程式資訊）** 。  

![][1]  

2) 按一下 [**新增**標籤 （應用程式資訊），提供*使用者名稱*的名稱，然後為*字串*輸入並按一下 [**提交**]。 

![][2]

3) 最後，您會看到如下所示註冊此應用程式資訊︰

![][3]

##<a name="send-app-info-from-the-client-sdk"></a>從用戶端 SDK 傳送應用程式資訊

以下我們使用 Windows 通用的應用程式範例，但對等的方法也有其他我們 Sdk。 

假設您有方法位置您的設定檔資訊從他們的名稱等使用者可能後取得驗證這些行動應用程式中，您會呼叫`SendAppInfo`以下方法，並填入的值`user_name`註冊，讓您稍早到行動互動服務後端的應用程式資訊。 

    Dictionary<object, object> appInfo = new Dictionary<object, object>();
    appInfo.Add("user_name", str);
    EngagementAgent.Instance.SendAppInfo(appInfo); 

##<a name="send-personalized-notifications"></a>傳送個人化的通知

現在您已傳送通知使用此**電腦**的所有設定。 

1) 移至行動互動入口網站上建立通知，[**連線**] 索引標籤，然後您可以使用此版面配置區以下列格式的通知標題或內文中的任何位置。 

![][4]  

> [AZURE.NOTE] 任何使用者的使用者名稱應用程式資訊未設定，則不會收到任何通知。 如果您在測試模式中執行通知行銷活動，而且您不會有應用程式資訊設我們會傳送給 「？ 」 字元取代版面配置區。 

2) 當行動互動會選取的裝置傳送此通知，然後將查看此應用程式資訊，並取代版面配置區中的值。  
例如，我們已設定`str = "Scott"`使用者的裝置註冊會收到的應用程式資訊與相關聯**電腦 = 史**的此使用者，此使用者，則會看到下列格式的應用程式推入通知辦公室。 

![][5]  

<!-- Images. -->
[1]: ./media/mobile-engagement-send-personalized-notifications/app-info.png
[2]: ./media/mobile-engagement-send-personalized-notifications/create-app-info.png
[3]: ./media/mobile-engagement-send-personalized-notifications/app-info-user-name.png
[4]: ./media/mobile-engagement-send-personalized-notifications/personal-notification.png
[5]: ./media/mobile-engagement-send-personalized-notifications/notification.png

