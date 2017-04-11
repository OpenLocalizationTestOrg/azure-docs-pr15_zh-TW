<properties 
   pageTitle="Azure 行動互動使用者介面範圍的方式"
   description="Azure 行動互動的使用者介面概觀" 
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

# <a name="how-to-get-started-using-and-managing-pushes-to-reach-out-to-your-end-users"></a>如何開始使用與管理推入至連絡您的使用者

一旦 SDK 完全整合應用程式，您可以開始使用應用程式的推入通知給使用者的使用者介面的 [範圍] 區段。  

## <a name="do-your-first-push-notification-campaign"></a>執行您的第一個推入通知活動
-    確認您的範圍整合 SDK 與應用程式。 
-    選取您的應用程式
 
![First1][1]

-    移至 [範圍] 區段中，按一下 「 新的通知 」
 
![First2][2]

-    建立新的行銷活動，並將其命名
 
 ![First3][3]

-    選取 [如何應該傳送通知的應用程式只能為
 
![First4][4]

-    建立您要發佈的郵件
 
![First5][5]

-    您可能會撰寫標題 （可省略）] 通知。
-    撰寫推入郵件內容。
-    您可以上傳圖像。 請注意的檔案大小不得超過 32768 個位元組。
-    您也可以選取進一步的選項，但此教學課程的焦點，我們會看見的更新版本。

-    只選取為通知的內容類型
 
![First6][6]

-    建立您的推入行銷活動，它會出現在行銷活動清單中。
 
![First7][7]

## <a name="test-your-push-notification-campaign"></a>測試您的推入通知行銷活動
![Test1][8]

-    註冊您的裝置。
-    按一下您要發佈的裝置] 核取方塊。
-    按一下 [傳送至裝置的 [推入 「 測試 」] 按鈕。
 
![Test2][9]

-    啟動行銷活動
 
![Test3][10]

-    現在，您有建立您的活動您只需要啟動您的使用者必須推入通知。
 
## <a name="send-personalized-pushes"></a>傳送個人化的推入
-    此範例會建立自訂的優待程式碼，輸入推入通知推入。
 
![Personalize1][11]

個人化運作正常，因此取代從應用程式資訊標籤所標記，您必須確認使用者擁有已定義之第一次適當應用程式資訊。 在此範例中的目標的使用者會有名為 [已定義之 rebate_code 應用程式資訊標記。
當您看到上方的推入通知的內容會包含標記 ${rebate_code} 表示要取代的實際標記的內容應用程式的資訊。

> 警告︰ 如果使用者未定義應用程式資訊標記，使用者將無法接收推入。

-    結果
 
![Personalize2][12]

### <a name="you-can-further-personalize-the-text-your-notification"></a>您可以進一步個人化文字在通知
![Personalize3][13]

-    包括通知的標題
-    和訊息的內容。
-    選擇公告 （文字檢視或 Web 檢視） 的類型
 
![Personalize4][14]

### <a name="the-body-of-an-announcement-may-also-be-personalized-with"></a>通知本文也可能會與個人化︰
-    巨集指令的 URL，應該要自訂登陸頁面
-    標題，
-    在郵件本文。
 
 
## <a name="differentiate-your-push-notification-in-or-out-of-app"></a>區分您推播通知 （近或拉應用程式）
-    選擇通知推播、 選取您的應用程式、 移至 「 達到 」 區段，選取或建立推入行銷活動並移至 [通知] 區段的類型。
 
-    按一下要在 「 傳遞模式 」。
-    按一下 」 限制活動] 核取方塊時您想要通知發生在特定的活動 （螢幕）。

![Differentiate1][15]

### <a name="out-of-app-only-delivery-mode"></a>「 不在應用程式只能 」 傳遞模式
![Differentiate2][16]

「 不在只應用程式] 關閉應用程式時，傳送模式提供推入通知。 這是標準的推入通知。
當您選取 「 不在只應用程式] 時，您必須已經提供 （APNS 或 GCM） 建立您的應用程式的平台的憑證。

### <a name="see-also"></a>另請參閱
-  [Apple 推入通知服務 – 憑證](http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9)、 Google 雲端訊息-憑證] (http://developer.android.com/google/gcm/index.html) 

### <a name="in-app-only-delivery-mode"></a>「 的應用程式只能 」 傳遞模式
![Differentiate3][17]

當應用程式正在執行時，「 的應用程式只能 」 傳遞模式會提供推入通知。
這個通知，您不需要 APNS 和 GCM 系統瀏覽。
您可以使用的應用程式傳送系統連絡您的使用者。
您可以完全自訂通知，並決定活動 （螢幕） 通知會顯示在。

### <a name="anytime-delivery-mode"></a>「 隨時 」 傳遞模式
您可以選擇 「 隨時 」 傳遞模式，可確保您是否正在執行應用程式，請連絡您的使用者。
當您選取 [任何時間 」 時，您必須已經提供您的應用程式奠定 （APNS 或 GCM） 的平台的憑證。 
 
## <a name="schedule-a-push-campaign"></a>排程推入行銷活動
### <a name="plan-to-start-a-campaign"></a>若要啟動的行銷活動計劃
![Shedule1][18]

21st 的年 3 月並有進行通知，並且已針對 22 的年 3 月計畫午夜。 您不需要保持前面執行推入介面 ！ 您可以計劃事先通知將會傳送完全分鐘。
-    取消核取 [無] 核取方塊，然後選取 [開始時間 
-    選擇 [日期和時間您想要開始推入行銷活動]。

### <a name="plan-to-end-a-campaign"></a>若要結束行銷活動計劃
![Shedule2][19]

您想要停止上 25 的年 3 月 pm 3: 00 時您行銷活動，但您知道您並不會處理。
您不需要的介面發送前方保持 ！ 您可以計劃事先完全分鐘會停止您的活動。
-    按一下 [無] 核取方塊，或選取 [結束時間
-    選擇 [日期和時間您想要完成推入行銷活動]。

### <a name="end-a-campaign-manually"></a>手動結束行銷活動
![Shedule3][20]

根據預設，「 無 「 已核取方塊。
這表示當您啟動 [範圍] 區段中，當您將會停止它在 [範圍] 區段會結束，就會開始活動。
 
> 附註︰ 建立無結束日期的行銷活動推入儲存在裝置上的本機，並顯示下一次開啟應用程式即使手動結束行銷活動。

## <a name="enhance-a-push-notification-with-a-text-view"></a>增強文字檢視推入的通知
### <a name="what-is-a-text-view"></a>什麼是文字檢視？
![TextView1][21]

在文字檢視是文字內容的快顯。 使用者按下推入通知後，就會出現此快顯。
在文字檢視可讓您展示您的使用者的其他內容。 這也是機會向動作，例如跳至您的應用程式中，然後再重新導向至 Store]，開啟網頁，傳送電子郵件]，啟動地理當地語系化搜尋，頁面等...

### <a name="example-text-view"></a>範例︰ 文字檢視
-    建立您的推入通知行銷活動 」 的範圍] 區段中，並為您的活動命名
 
![TextView2][22]

-    撰寫郵件會顯示通知。
-    選取 「 文字 」 的公告內容類型
 
![TextView3][23]

> 注意︰ 當您在文字檢視，其永遠隨附通知第一次。 

- 定義文字 （後有選取文字的公告內容，會出現 [子] 區段中，可讓您定義您想要顯示的文字）。
 
![TextView4][24]

-    撰寫郵件的頂端會顯示的標題。
-    撰寫文字檢視的主要內容。
-    撰寫會顯示的內容 （巨集指令按鈕可讓應用程式，讓特定的動作，例如開啟的應用程式中，然後再重新導向到應用程式商店或任何一種可提供的來源頁面） 的 [動作] 按鈕上。
-    撰寫的內容，就會出現在 [結束] 按鈕 （按一下 [結束] 按鈕，[文字] 檢視會消失。）
 
-    建立您的推入通知行銷活動，會出現在 [活動] 清單。
 
![TextView5][25]

-    啟動您的推入通知行銷活動傳送給使用者的 [文字] 檢視。
 
![TextView6][26]

-    結果
 
![TextView7][27]

-    使用者會收到通知，並按一下它。
-    [文字] 檢視會顯示為快顯讓使用者與其互動。

## <a name="enhance-a-push-notification-with-a-web-view"></a>增強網頁檢視推入的通知
### <a name="what-is-a-web-view"></a>什麼是 Web 檢視？
![WebView1][28]

Web 檢視是 web 內容快顯。 當使用者按下推入通知，會出現此快顯。
Web 檢視可讓您有多個使用者的互動。
這也是機會向動作，例如重新導向至 [App Store]，開啟網頁、 傳送電子郵件、 啟動地理當地語系化搜尋，等...

### <a name="example-web-view"></a>範例︰ 網頁檢視
-    建立您的推入行銷活動 」 的範圍] 區段中，並為您的活動命名。
 
![WebView2][29]

-    撰寫郵件會顯示通知。
-    選取 [網站] 公告內容類型
 
![WebView3][30]

### <a name="about-announcement-types"></a>關於公告類型︰
- 僅限通知︰ 很簡單的標準通知。 表示若使用者按一下它，沒有其他檢視會顯示，但只將其相關聯的動作會發生。
- 文字通知︰ 則會請看看在文字檢視使用者的通知。
- Web 公告︰ 則會請看看 web 檢視使用者的通知。
選取 「 Web 公告 」 內容。

> 注意︰ 當您的網頁檢視，其永遠隨附通知第一次。

- 定義網頁內容 （後無法選取 web 公告內容，會出現小節，可讓您定義您想要顯示的網頁檢視內容）。

 
![WebView4][31]

-    撰寫訊息 （選用） 頂端出現的標題。
-    撰寫您 HTML 程式碼。
-    按一下 [編輯模式] 按鈕切換 edition，請參閱如何它看起來像的來源。
-    撰寫會顯示的內容 （巨集指令按鈕可讓應用程式，讓特定的動作，例如開啟的應用程式中，然後再重新導向到商店或任何一種可提供的來源頁面） 的 [動作] 按鈕上。
-    撰寫的內容，就會出現在 [結束] 按鈕 （按一下 [結束] 按鈕，網頁檢視就會消失）。
 
-    結果
 
![WebView5][32]

-    使用者會收到通知，並按一下它。
-    [文字] 檢視會顯示為快顯讓使用者與其互動。

<!--Image references-->
[1]: ./media/mobile-engagement-how-tos/First1.png
[2]: ./media/mobile-engagement-how-tos/First2.png
[3]: ./media/mobile-engagement-how-tos/First3.png
[4]: ./media/mobile-engagement-how-tos/First4.png
[5]: ./media/mobile-engagement-how-tos/First5.png
[6]: ./media/mobile-engagement-how-tos/First6.png
[7]: ./media/mobile-engagement-how-tos/First7.png
[8]: ./media/mobile-engagement-how-tos/Test1.png
[9]: ./media/mobile-engagement-how-tos/Test2.png
[10]: ./media/mobile-engagement-how-tos/Test3.png
[11]: ./media/mobile-engagement-how-tos/Personalize1.png
[12]: ./media/mobile-engagement-how-tos/Personalize2.png
[13]: ./media/mobile-engagement-how-tos/Personalize3.png
[14]: ./media/mobile-engagement-how-tos/Personalize4.png
[15]: ./media/mobile-engagement-how-tos/Differentiate1.png
[16]: ./media/mobile-engagement-how-tos/Differentiate2.png
[17]: ./media/mobile-engagement-how-tos/Differentiate3.png
[18]: ./media/mobile-engagement-how-tos/Schedule1.png
[19]: ./media/mobile-engagement-how-tos/Schedule2.png
[20]: ./media/mobile-engagement-how-tos/Schedule3.png
[21]: ./media/mobile-engagement-how-tos/TextView1.png
[22]: ./media/mobile-engagement-how-tos/TextView2.png
[23]: ./media/mobile-engagement-how-tos/TextView3.png
[24]: ./media/mobile-engagement-how-tos/TextView4.png
[25]: ./media/mobile-engagement-how-tos/TextView5.png
[26]: ./media/mobile-engagement-how-tos/TextView6.png
[27]: ./media/mobile-engagement-how-tos/TextView7.png
[28]: ./media/mobile-engagement-how-tos/WebView1.png
[29]: ./media/mobile-engagement-how-tos/WebView2.png
[30]: ./media/mobile-engagement-how-tos/WebView3.png
[31]: ./media/mobile-engagement-how-tos/WebView4.png
[32]: ./media/mobile-engagement-how-tos/WebView5.png

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
 
