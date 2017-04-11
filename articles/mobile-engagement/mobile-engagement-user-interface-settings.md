<properties 
   pageTitle="Azure 行動互動使用者介面設定" 
   description="瞭解如何管理您的應用程式使用 Azure 行動互動的通用設定" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="08/19/2016"
   ms.author="piyushjo"/>

# <a name="how-to-manage-the-global-settings-of-your-application"></a>如何管理應用程式的通用設定

可用的應用程式會有所不同，取決於應用程式與您已獲授予他人應用程式的權限的平台的**設定**] 功能表選項。 設定包括下列︰ 詳細資料、 專案、 原生推播、 推播速度、 標籤 （應用程式資訊），與商業壓力。 可管理的 [設定] 區段的 [標籤 （應用程式資訊）] 功能表選項，您的應用程式 （SDK），或您的後端 （使用裝置 API）。 


>[AZURE.NOTE] **行動互動**入口網站使用者介面的多個區段包含**顯示說明**] 按鈕。 按下這個按鈕，即可進一步瞭解內容一節。

## <a name="details"></a>詳細資料

可讓您變更名稱與您的應用程式的說明，請檢視您的應用程式與您的角色的權限的擁有者。 

分析設定可讓您檢視或變更週開始的日期和天內的保留時間。
 
  ![settings1][46]
 
## <a name="projects"></a>專案

可讓您選取 [所有應用程式中顯示的專案。 

您也可以搜尋專案，並檢視名稱、 描述、 擁有人和您的角色權限，您的應用程式屬於任何專案。

如需詳細資訊，請參閱︰ [UI 文件-家用版][Link 13]
 
  ![settings3][48]

## <a name="native-push"></a>原生推入

可讓您以原生推入註冊新的憑證或刪除與現有的憑證用於。 原生推入啟用 Azure 行動互動，若要將您的應用程式隨時推偶數時不在執行中。 

至少有一個原生推入服務，提供認證或憑證後，您可以選取 [任何時間 」 推播 API 中建立達到行銷活動，以及使用 「 上線 」 參數時。



### <a name="apple-push-notification-service-apns"></a>Apple 推入通知服務 (APNS)

若要啟用原生推播使用 Apple 推入通知服務，您必須註冊您的憑證。 您必須為開發 （開發） 或 （產品） 的生產指定類型的憑證。 然後您會需要上傳您的憑證和密碼]。

如需詳細資訊，請參閱︰ [SDK 文件-iOS-如何準備您的應用程式 Apple 推播通知][Link 5]
 
![settings4][49]
 
### <a name="windows-push-notification-service-wpns"></a>Windows 推入通知服務 (WPNS)

若要啟用原生推播使用 Windows 通知服務，您必須提供您的應用程式的認證。 您需要套件安全性識別碼信用及您私人的金鑰。
 
![settings5][50]
 
### <a name="google-cloud-messaging-for-android-gcm"></a>Google 雲端訊息版 (GCM)

若要啟用使用 GCM 原生推入，您需要從 Google 依照指示操作。 您必須將範例貼伺服器簡單 API 金鑰，然後設定 IP 限制。 需要與 SDK 整合 Android v1.12.0 +。

如需詳細資訊，請參閱︰ 

- [SDK 文件 Android 如何整合 GCM][Link 5]
- [Google 開發人員 GCM 指南](http://developer.android.com/guide/google/gcm/gs.html)
 
### <a name="amazon-device-messaging-for-android-adm"></a>Amazon 裝置版 (ADM) 訊息

若要啟用使用 ADM 原生推入，您必須提供 Amazon<OAuth credentials>組成的用戶端識別碼和用戶端私人 （需要整合 SDK for Android v2.1.0 +）。

如需詳細資訊，請參閱︰ 

- [SDK 文件 Android 如何整合 ADM][Link 5]
- [Amazon 開發人員 ADM 文件](https://developer.amazon.com/sdk/adm/credentials.html#Getting)
 
![settings6][51]

## <a name="push-speed"></a>推入速度

顯示目前的推入速度，您的應用程式，並可讓您定義您的應用程式的推入速度。
 
  ![settings7][52]

## <a name="tag-app-info"></a>標記 （應用程式資訊）

![settings11][56]
  
## <a name="commercial-pressure"></a>商業壓力


![settings12][57]


## <a name="see-also"></a>另請參閱

- [概念][Link 6]
- [疑難排解指南服務][Link 24]

 

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: ../mobile-engagement-how-tos-first-push.md
[Link 28]: ../mobile-engagement-how-tos-test-campaign.md
[Link 29]: ../mobile-engagement-how-tos-personalize-push.md
[Link 30]: ../mobile-engagement-how-tos-differentiate-push.md
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign.md
[Link 32]: ../mobile-engagement-how-tos-text-view.md
[Link 33]: ../mobile-engagement-how-tos-web-view.md
 
