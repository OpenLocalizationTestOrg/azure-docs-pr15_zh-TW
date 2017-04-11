<properties 
   pageTitle="Azure 行動互動使用者介面的帳戶" 
   description="瞭解如何管理您的帳戶的設定檔和測試的裝置使用 Azure 行動互動" 
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

# <a name="how-to-manage-your-account-profile-and-test-devices"></a>如何管理您的帳戶的設定檔和測試裝置
 
本文將說明**行動互動**入口網站 [**首頁**] 頁面。 您可以使用 [**行動互動**入口網站監控和管理您的行動應用程式。 
 
若要移至 [**我的帳戶**] 頁面，按一下頁面上方的您的帳戶。

[我的帳戶] 區段的使用者介面是您可以用它來檢視及變更您的帳戶，包括您的設定檔與相關聯的設定，並測試裝置識別碼。 這些設定包含也可以透過裝置 API 存取的項目。

![MyAccount1][7]  

## <a name="profile"></a>設定檔︰
您可以檢視或變更任何您的帳戶設定以下所示。 您也可以授權讓其他使用者權限，以使用您的應用程式根據他們的電子郵件地址，從 [[常用]](mobile-engagement-user-interface-home.md)。

![MyAccount2][8]  

## <a name="devices"></a>裝置︰
您可以檢視、 新增或移除測試裝置 ID，您可以使用以測試您**到達**或**推入**行銷活動的測試裝置。 如何尋找裝置識別碼的每個平台裝置的關聯式說明 (iOS Android、 Windows Phone 等) 當您按一下 [「 新增裝置 」 會顯示。 
 
![MyAccount3][9]  
 
若要使用推播 API 或裝置 API 必須知道使用者的唯一裝置識別碼 （deviceid 參數）。 有數種方式可擷取它︰
 
1. 從您的後端，您可以使用 「 Get 」 功能的裝置 API 取得裝置識別碼的完整清單。
2. 從您的應用程式，您可以將它使用 SDK。 （在 Android 上呼叫 getDeviceID() 函數代理程式類別，然後在 iOS，讀取 deviceid 代理程式類別屬性）。
3. 從範圍的通知，如果與通知相關聯的動作 URL 包含 {deviceid} 圖樣，它會自動被取代來觸發動作裝置的識別項。
http://<example>.com/registeruser?deviceid = {deviceid} & otherparam = myparamdata 都會被取代︰ http://<example>.com/registeruser?deviceid = XXXXXXXXXXXXXXXX 與 otherparam = myparamdata 
4. 從達到 web 通知如果公告的 HTML 程式碼包含 {deviceid} 圖樣，它會自動被取代來顯示網頁公告裝置的識別項。
以下是 [我的裝置識別碼: {deviceid} 會遭到︰ 以下是 [我的裝置識別碼︰ XXXXXXXXXXXXXXXX
5.  開啟您的裝置上的應用程式，並執行您已標記的應用程式中的事件。
從 「 UI-您的應用程式-監視器-事件的詳細資料 」，找出您執行清單中的事件。
按一下此 [監視器] 中的事件。
您應該執行此事件的裝置清單中找到您裝置的識別碼。
然後，您可以複製此裝置識別碼，並登錄 」 UI-我的帳戶-裝置的新裝置-選取您的裝置平台 」。
>（請注意，iOS 版停用 IDFA 時，裝置識別碼可能會變更一段時間如果您解除安裝並重新安裝您的應用程式）。

##<a name="troubleshooting-guide"></a>疑難排解指南
-  [疑難排解指南-服務][Link 24]

## <a name="see-also"></a>另請參閱
-  [UI 文件-首頁][Link 13]


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


 
 
