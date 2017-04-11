<properties 
   pageTitle="Azure 行動互動使用者介面達到行銷活動" 
   description="Laern 方式來建立和管理推入通知行銷活動使用 Azure 行動互動" 
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


# <a name="how-to-create-and-manage-push-notification-campaigns"></a>如何建立和管理推入通知行銷活動
您可以使用 [範圍] 區段的 ui 複雜的公式以建立新的推入行銷活動，提供您需要推入通知傳送的所有資訊。 推入行銷活動的選項有些許根據四個行銷活動類型︰ 宣告、 投票、 資料推入和磚 (Windows Phone)。

### <a name="option-applies-to"></a>選項適用於︰
- 語言︰ 所有 （宣告投票資料推入，磚）
- 行銷活動︰ 所有 （宣告投票資料推入，磚）
- 通知︰ 宣告、 投票
- 內容︰ 唯一的每個行銷活動類型
- 對象︰ 所有 （宣告投票資料推入，磚）
- 時間範圍︰ 宣告、 投票磚
- 測試︰ 所有 （宣告投票資料推入，磚）
 
![達到 Campaign1][20]

## <a name="languages"></a>語言
您可以使用的語言下拉功能表不同版本的您推入傳送至 [設定為使用不同語言的裝置。 根據預設，所有裝置將會都收到相同的推入，不論其設定為使用哪種語言。 與裝置設定為不同語言的使用者都會收到推入的預設語言版本。 有許多推入行銷活動的選項可讓您針對每個您選取的其他語言指定替代的內容。 
 
![達到 Campaign2][21]

### <a name="language-differences-apply-to"></a>語言差異適用於︰
- 語言︰ 唯一的語言可能已選取 [除了的預設語言
- 行銷活動︰ 相同的所有語言
- 通知︰ 唯一的每個以外的預設語言的語言
- 內容︰ 唯一的每個以外的預設語言的語言
- 不同的語言準則來篩選對象︰
- 時間範圍︰ 為所有的語言相同
- 測試︰ 可能會傳送至每個語言的時間
 
### <a name="supported-languages"></a>支援的語言︰
- 阿拉伯文 (ar) 
- 保加利亞文 (bg) 
- 卡達隆尼亞 (ca) 
- 中文 （曆法） 
- 克羅埃西亞文 (hr) 
- Czech (cs) 
- 丹麥文 (da) 
- 荷蘭文 (nl) 
- 英文 (en) 
- 芬蘭文 (wi-fi) 
- 法文 （這個方法） 
- 德文 (de) 
- 希臘文 （席） 
- 希伯來文 （他） 
- 印度文 （高） 
- 匈牙利文 (hu) 
- 印尼文 （識別碼） 
- 義大利文 （，） 
- 日文 (ja) 
- 韓文 (ko) 
- 拉脫維亞文 (lv) 
- 立陶宛文 (lt) 
- 馬來文 (macrolanguage) （毫秒） 
- 挪威文巴克摩 (nb) 
- 波蘭文 (pl) 
- 葡萄牙文 (pt) 
- 羅馬尼亞文 (ro) 
- 俄文 (ru) 
- 塞爾維亞文 (sr) 
- 斯洛伐克文 (sk) 
- 斯洛維尼亞文 (sl) 
- 西班牙文 (es) 
- 瑞典文 (sv) 
- Tagalog (tl) 
- 泰文 （日） 
- 土耳其文 (tr) 
- 烏克蘭文 （英國） 
- 越南文 （六） 
 
## <a name="campaign"></a>行銷活動
若要設定 [名稱] 和 [您的活動，以及的類別，如同您計劃略過的推入行銷活動的 [對象] 區段，並請改為傳送此活動透過達到 API （和低階層推播 API 的某些項目），您可以使用 [活動] 區段。 類別可供使用預先定義的設定為基礎的控制項的應用程式通知自訂通知範本。 您可以取得您現有 」 類別清單 」 透過達到 API。

> 警告︰ 如果您使用的 「 略過對象，推入便會傳送到使用者透過 API 」 選項達到行銷活動的 「 活動 」 區段中不會自動傳送行銷活動，您必須透過達到 API 手動傳送。
 
![達到 Campaign3][22]
 
### <a name="option-applies-to"></a>選項適用於︰
- 名稱︰ 所有
- [類別]: 宣告、 投票
- 略過對象，推入便會傳送到使用者透過 API︰ 所有
 
## <a name="notification"></a>通知
您可以使用 [通知] 區段中，若要設定基本設定您的推入包括︰ 標題的推入、 郵件、 應用程式中圖像，或如果雖。 通知的許多設定專屬於您裝置的平台。 您可以選取是否 」 應用程式 」 或 「 不在應用程式]，將會傳送您的推入或兩者。 （請記住，使用者可以 「 參加 」 或 「 退出 」 的 「 不在應用程式 」 將位於推入作業系統的層級其在裝置上，Azure 行動互動不會覆寫此設定。 也請記住，達到 API 」 中應用程式] 的控點 」 應用程式出 「 推入。 推入 API 可處理 「 不在應用程式 「 推入太。）使用圖片或 HTML 內容，包括深的連結，連結外部應用程式或另一個位置 （Android SDK 2.1.0 或更新版本所需的意圖類別） 應用程式的可自訂的推入。 您可以變更圖示或 iOS 徽章，並傳送文字或網頁內容 （快顯視窗的 html 內容]，連結到另一個位置內部或外部應用程式）。 您也可以讓 Android 裝置撥打或震動與推入。 (請記住，您必須正確 SDK 您 Android 中的權限資訊清單來撥打或震動裝置的檔案。)目前有任何業界標準對於 Android [大型圖片]，因為螢幕大小不同在每個裝置上，但 400 x 100 圖片使用幾乎任何螢幕大小。

### <a name="delivery-types"></a>傳遞類型如下︰
-    登出應用程式只能︰ 使用者不使用應用程式時，就會傳送通知。
- 應用程式只通知的出需要從 Apple 或 Google （APNS 或 GCM 憑證） 的憑證。
- 在 [應用程式只能︰ 只有在執行應用程式時才會出現的通知。
- 通知連接使用者使用 Capptain 傳遞系統。 您可以完全自訂您的推入視覺版面配置/顯示。
- 隨時︰ 這個選項可確保您傳送或不執行應用程式通知。

 
![達到 Campaign4][23]

### <a name="option-applies-to"></a>選項適用於︰
- 通知︰ 宣告、 投票
 
## <a name="content"></a>內容
若要修改的相關公告、 投票、 資料推入和磚 (Windows Phone) 的內容，您可以使用 [內容] 區段。 推入行銷活動的內容設定為特定類型的活動。 

### <a name="see-also"></a>另請參閱
- [UI 文件-抵達-推入內容][Link 29]
 
![達到 Campaign5][24]

## <a name="audience"></a>對象
您可以使用 [對象] 區段，以定義標準來限制您的活動或限制您的活動根據自訂的準則的項目清單。 一組標準的限制觀眾選項可讓您推入新的或舊版的使用者或僅限原生推入的使用者。 您也可以設定的配額限制接收推入的使用者數目。 您可以手動編輯您的活動包含目標使用者的一或多個條件的篩選方式的運算式。 您可以手動輸入運算式的對象。 這類運算式必須明確定義準則之間的關聯。 識別項，必須以放大的字母開頭，而且不能包含空格來說明的準則。 可以使用說明準則之間的關係 '和' '或' 'not' 運算子，以及 '' （'）'。 範例: 「 Criterion1 或 （Criterion1 和不 Criterion2） 」。

> 附註︰ 大型的對象，包含在行銷活動中，選取目標掃描的伺服器端就能變得很慢，尤其是如果您嘗試同時啟動多個行銷活動。

- 如果可能的話，只開始一個活動，一次。
- 在最，只開始四個行銷活動，一次。
- 推入作用中的使用者只 (核取方塊 」 加入可以使用原生推入連絡使用者 」 和 「 加入作用中的使用者]) 以便只有您的使用者仍然有安裝的應用程式，並使用它必須掃描。
一旦您對象定義後，您可以使用 [模擬] 按鈕，找出多少使用者將會收到此推入。 這會計算已知潛在目標依此 （這是根據使用者的隨機樣本估計值） 的對象的使用者數目。 請注意解除安裝應用程式的使用者屬於也此對象，但無法存取。

### <a name="see-also"></a>另請參閱
- [UI 文件-抵達-新推入準則][Link 28]

![達到 Campaign6][25]

### <a name="edit-expression"></a>編輯運算式
![達到 Campaign7][26]
 
### <a name="limit-your-audience-option-applies-to"></a>限制適用於您的簡報對象選項︰
- 加入使用者的子集︰ 所有的相關公告、 投票、 資料推入 (磚）
- 加入舊的使用者︰ 所有的相關公告、 投票、 資料推入 (磚）
- 加入新的使用者︰ 所有的相關公告、 投票、 資料推入 (磚）
- 加入閒置的使用者︰ 宣告、 投票磚
- 加入作用中的使用者︰ 所有的相關公告、 投票、 資料推入 (磚）
- 加入 [可以使用原生推播連絡使用者︰ 宣告、 投票
 
## <a name="time-frame"></a>時間範圍
若要設定將會傳送推入，或您可以留時間範圍立即啟動的行銷活動時，您可以使用 [時間範圍] 區段。 請記住，使用一般使用者的時區可能行銷活動一天開始之前，您的使用者在亞洲預期，傳送一次在全世界的哪個的時區符合設定您的行銷活動的時間範圍，直到推入的小批次。 使用一般使用者的時區可能也會造成延遲行銷活動因為它沒有啟動推入之前，從手機要求時間。

> 附註︰ 行銷活動不可以快取的結束日期推入本機和仍顯示註解後手動完成行銷活動。 若要避免此問題，特定行銷活動的結束時間。

### <a name="see-also"></a>另請參閱
- [連絡-如何 To – 排程][Link 3] 
 
![達到 Campaign8][27]

### <a name="settings-apply-to"></a>設定適用於︰
- 時間範圍︰ 宣告、 投票磚
 
## <a name="test"></a>測試
若要傳送此推入儲存行銷活動之前先測試裝置，您可以使用 [測試] 區段。 如果您已設定行銷任何自訂的語言，您可以測試推入每一種語言。 您可以設定 「 我的帳戶 」 的測試裝置。
> 注意︰ 當您使用的按鈕來 「 測試 」 記錄資料沒有伺服器端推入、 資料只會為實數推入行銷活動記錄。

### <a name="see-also"></a>另請參閱
- [UI 文件-我的帳號][Link 14]
 
![達到 Campaign9][28]

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
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md
 
