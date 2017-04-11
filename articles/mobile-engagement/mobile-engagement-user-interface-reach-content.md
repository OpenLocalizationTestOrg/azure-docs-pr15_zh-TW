<properties 
   pageTitle="Azure 行動互動使用者介面達到內容" 
   description="瞭解如何管理不同類型的推入通知行銷活動中 Azure 行動互動的唯一內容" 
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

# <a name="how-to-manage-the-unique-content-of-the-different-types-of-push-notification-campaigns"></a>如何管理不同類型的推入通知行銷活動的唯一內容
 
若要修改的相關公告、 投票、 資料推入和磚 (Windows Phone) 的內容，您可以使用新的範圍行銷活動的 [內容] 區段。 推入行銷活動的內容設定為特定類型的活動。 
 
### <a name="content-types"></a>內容類型︰
- 宣告
- 投票
- 資料推入
- 並排 (Windows Phone)
 
## <a name="content-of-announcements"></a>內容的相關公告
 ![達到 Content1][30] 

### <a name="choose-the-type-of-your-announcement"></a>選擇您的通知的類型︰
-    僅限通知︰ 很簡單的標準通知。 表示若使用者按一下它，沒有其他檢視會顯示，但只將其相關聯的動作會發生。
-    文字通知︰ 則會請看看在文字檢視使用者的通知。
-    Web 公告︰ 則會請看看 web 檢視使用者的通知。

### <a name="see-also"></a>另請參閱
- [連絡-如何 To-公告][Link 3] 

### <a name="about-web-view-announcements"></a>關於網頁檢視宣告︰
在 HTML 程式碼或 JavaScript 程式碼的項目，您在此提供模式 」 {deviceid} 」 的項目將會顯示通知裝置的識別項自動取代。 這是輕鬆擷取裝載於您辦公室外部 web 服務中的 Azure 行動互動裝置識別碼。
如果您想要建立全螢幕網頁檢視 （不含預設動作，並結束按鈕我們提供） 您可以使用下列函數從您網站的檢視通知的 JavaScript 程式碼︰ 

-    執行公告的動作︰ ReachContent.actionContent()
-    結束通知︰ ReachContent.exitContent()
 
### <a name="choose-your-action"></a>選擇您的動作︰

### <a name="about-action-urls"></a>瞭解巨集指令的 Url:
可以在目標的裝置作業系統解譯任何 URL 可為巨集指令的 URL。
任何專用的應用程式可能會支援的 URL (例如，讓使用者跳至特定的螢幕) 也可為巨集指令的 URL。
{Deviceid} 模式的每個項目就會自動取代裝置執行動作的識別碼。 這可以用於輕鬆擷取透過外部 web 服務裝載於您辦公室的 Azure 行動互動裝置識別碼。

- **Android + iOS 動作**
    - 開啟網頁
    - http://\[網站--網域\] 
    - 範例︰ http://www.azure.com
    - 傳送電子郵件
    - mailto:\[電子-郵件的收件者\]?subject =\[主旨\]& 本文 =\[訊息\] 
    - Example:mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
    - 傳送簡訊
    - sms:\[電話號碼\] 
    - 範例︰ sms:2125551212
    - 撥打的電話號碼
    - tel:\[電話號碼\] 
    - 範例︰ tel:2125551212
- **Android 動作**
    - 下載 Play 商店上的應用程式
    - market://details?id=\[應用程式套件\] 
    - 範例︰ market://details?id=com.microsoft.office.word
    - 啟動地理本地化搜尋
    - geo:0、 0?q =\[搜尋查詢\] 
    - 範例︰ geo:0，0？ q&a = 樓下，巴黎
- **iOS 動作**
    - 下載應用程式存放區上的應用程式
    - [國家/地區] http://itunes.apple.com/ /app/ [應用程式名稱] /id [應用程式識別碼]？ mt = 8 
    - 範例︰ http://itunes.apple.com/fr/app/briquet-virtuel/id430154748?mt=8
    - Windows 動作
    - 開啟網頁
    - http://\[網站--網域\] 
    - 範例︰ http://www.azure.com
    - 傳送電子郵件
    - mailto:\[電子-郵件的收件者\]?subject =\[主旨\]& 本文 =\[訊息\] 
    - Example:mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
    - 傳送簡訊 （Skype 市集應用程式所需）
    - sms:\[電話號碼\] 
    - 範例︰ sms:2125551212
    - 撥打的電話號碼 （Skype 市集應用程式所需）
    - tel:\[電話號碼\] 
    - 範例︰ tel:2125551212
    - 下載 Play 商店上的應用程式
    - ms-windows-市集︰ PDP?PFN =\[應用程式套件識別碼\] 
    - 範例︰ ms-windows-市集︰ PDP 嗎？PFN = 4d91298a-07cb-40fb-aecc-4cb5615d53c1
    - 啟動 bingmaps 搜尋
    - bingmaps:?q =\[搜尋查詢\] 
    - 範例︰ bingmaps:？ q&a = 樓下，巴黎
    - 使用自訂的配置
    - \[自訂的配置\]://\[自訂的配置參數\] 
    - 範例︰ myCustomProtocol://myCustomParams
    - 使用封裝資料 （市集應用程式延伸閱讀必要）
    - \[資料夾\]\[資料\]。\[副檔名\] 
    - Example:myfolderdata.txt
 
### <a name="build-a-tracking-url"></a>建立追蹤 URL:
-    請參閱 「 設定 」 一節<UI Documentation>的指示建立追蹤 URL 會允許使用者下載其中一個其他應用程式。
 
### <a name="define-the-texts-of-your-announcement"></a>定義您的通知的文字
填入標題、 內容和您的通知的按鈕文字。 您可以針對根據範圍意見反應的使用者如何回應行銷未來行銷活動的對象。 目標對象可以根據是否行銷只要按下，回覆、 actioned，或結束的意見反應。

### <a name="see-also"></a>另請參閱
- [UI 文件-抵達-新推入準則][Link 28]

## <a name="content-of-polls"></a>投票的內容
![達到 Content2][31]填入標題、 描述及按鈕文字中的 [您的通知。 然後，新增問題及答案的選擇。
您可以針對根據範圍意見反應的使用者如何回應行銷未來行銷活動的對象。 目標對象可以根據是否行銷只要按下，回覆、 actioned，或結束。 目標對象也可以根據投票 answer 意見反應，位置的問題及答案的選擇會用來做為準則。

### <a name="see-also"></a>另請參閱
- [UI 文件-抵達-新推入準則][Link 28]
 
## <a name="content-of-data-pushes"></a>推入資料的內容
![達到 Content3][32] 

### <a name="choose-the-type-of-your-data"></a>選擇您的資料類型︰
- 文字
- 二進位資料
- Base64 資料

### <a name="define-the-content-of-your-data"></a>定義您的資料的內容
- 如果您選取推入文字資料，請複製並貼上 「 內容] 方塊中的文字。
- 如果您選取推播二進位或 base64 資料，請使用上傳檔案] 按鈕，將檔案上傳。
- 您可以針對根據範圍意見反應的使用者如何回應行銷未來行銷活動的對象。 目標對象可以根據是否行銷只要按下，回覆、 actioned，或結束。

### <a name="see-also"></a>另請參閱
- [UI 文件-抵達-新推入準則][Link 28]

## <a name="content-of-tiles-windows-phone-only"></a>並排 (Windows Phone) 的內容
![達到 Content4][33]

### <a name="define-the-content-of-your-tile"></a>定義您的磚的內容
並排顯示內容是顯示您在 Windows Phone 裝置上的應用程式的磚中的文字。
並排顯示推入是 Microsoft 推入通知服務 (MPNS) 版本的 Windows Phone 版的原生推入。 並排顯示推入類型是只推入類型沒有回應，未來的行銷活動的對象，因此無法建立結果的並排顯示推入行銷活動。 

### <a name="see-also"></a>另請參閱
- [API 文件-抵達 API-原生推入][Link 4]

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
 
