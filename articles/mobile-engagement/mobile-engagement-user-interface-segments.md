<properties 
   pageTitle="Azure 行動互動使用者介面的區段" 
   description="瞭解如何建立及管理使用者識別使用模式使用 Azure 行動互動的區段" 
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

# <a name="how-to-create-and-manage-segments-of-users-to-identify-usage-patterns"></a>如何建立及管理使用者識別使用模式的區段

本文將說明**行動互動**入口網站中的 [**區段**] 索引標籤。 您可以使用 [**行動互動**入口網站監控和管理您的行動應用程式。

[區段] 區段的使用者介面，可讓您處理切割根據不同的行為和分析您能從應用程式，也可以透過區段 API 存取您的使用者。 區段會先計算 24 小時後建立的他們會根據最新的分析資訊每 24 小時會重新計算。 一旦區段的計算方式，會顯示 「 一天歷程記錄 」 圖表的每一天。


>[AZURE.NOTE] **行動互動**入口網站使用者介面的多個區段包含**顯示說明**] 按鈕。 按下這個按鈕，即可進一步瞭解內容一節。

## <a name="create-segments"></a>建立區段
您可以建立根據最多 10 個準則在特定期間設定到過去的 60 天 [分析] 區段中的區段。 例如，您可以建立區段，以檢視特定頁面或最後 10 天內搜尋您的應用程式中的特定內容的人員。 在 [分析] 區段中有這項資訊。 因此，您可以使用其建立區段中，並選擇 [設定推入通知目標這個子集使用者取得休完假回來應用程式。 
 
> 附註︰ 一旦已計算一個區段，就無法進行編輯。可以只複製 （複製） 或損毀 （刪除）。 區段來複製 （使用相同的 AppID)，在同一個應用程式中，也可以複製到其他應用程式 （使用不同的 AppID)。 
 
 ![segments1][35] 

## <a name="examples-segments"></a>範例區段
 ![segments2][36]

區段可讓您區段應用程式的使用者。
切割使用者是重要的行銷策略。 Azure 行動互動可讓您取得歷程記錄的資料，並建立自訂的區段。 這項功能強大的工具可讓您瞭解您的客戶體驗，在您的應用程式。 您可以輕鬆地分析您的區段，您的區段為推入目標。
常見的使用案例是您想要傳送推入通知，鼓勵您若要對您的應用程式存放區中的使用者。 而不傳送通知給所有使用者，您可以建立想要指定唯一的使用者擁有您的應用程式每天用於上個月，有很棒的使用者體驗的區段。 當您傳送較少、 目標更明確的推入通知時，您會取得更好的投資報酬率。
 
 ![segments3][37]

### <a name="segments-you-can-create-based-on-the-major-azure-mobile-engagement-elements"></a>您可以建立的區段為基礎的主要 Azure 行動互動的項目︰
- 事件︰ 建立區段目標一個的應用程式有多個每週的特定事件。 
- 工作階段︰ 建立的使用者用過的應用程式超過 5 乘以最後一週的區段。
- 活動︰ 建立使用者用過的一頁或內容或小於 10 次上個月的區段。
- 工作︰ 建立有超過兩次一天完成工作的使用者的區段。
- 當機︰ 建立的所有使用者都有當機 10 次以上的最後一週的區段。 （您可能會推播道歉或甚至的優待券本區段 ！）
- 錯誤︰ 建立的所有使用者都有超過 100 次過去 3 天錯誤的區段。
- 應用程式的資訊︰ 建立自訂的應用程式狀態，在 [最近的 25 天期間發生的目標的區段。
 
 ![segments4][38]

若要以最佳方式使用區段，您必須完成 SDK 自訂的整合應用程式的 [應用程式資訊] 標籤標記計劃中。
然後，移至介面的 [首頁] 頁面，選取您想要然後按一下 [區段] 區段的應用程式。

1. 選取 [區段] 區段。
2. 按一下 「 新的區段] 按鈕來建立新的區段。

## <a name="real-life-example-create-a-simple-segment-based-on-session-information"></a>實數的生命週期範例︰ 建立簡單的區段，根據 「 工作階段 」 資訊
建立您的應用程式使用至少 50 次中的最後一週的所有使用者的區段。 從這裡尋找花了至少 30 秒每個工作階段應用程式中的使用者。 這會顯示在您的應用程式中有正數體驗的所有使用者。 然後，建立區段無法用來發送給這些使用者要求他們對您的應用程式存放區中的通知。
 
 ![segments5][39]

1. 若要尋找 [區段] 清單中的快速地為您區段名稱。
2. 按一下 [建立] 按鈕。
 
 ![segments6][40]

選取 [工作階段]。
 
 ![segments7][41]

1. 選取的 「 最後一週 」。
2. 按一下 [下一步]。
 
 ![segments8][42]

1. 選取相關清單之間的運算子: =;≥, ≤.
2. 輸入您想要的計算。
3. 選取您想要的項目。 
4. 按一下 [下一步]。
設定此範例中，移到上一週、 符合，讓使用者對至少 50 工作階段。
 
 ![segments9][43]

針對工作階段區段中，您可以選擇每個做為準則的工作階段的長度。

1. 從清單中選取的運算子。
2. 提供每個工作階段的長度。
3. 按一下 [下一步]。
在此範例中，顯示的上所有的工作階段，具有已分割的項目區段，選取花了多個 30 秒工作階段的使用者。
 
 ![segments10][44]

命名準則以擷取其完整漏斗圖]，然後按一下 [完成]。
 
 ![segments11][45]

當您設定完成您的準則，就會出現在區段漏斗圖。
區段根據分析資料，因為區段會計算每日一次。
在此範例中，47,7%的總使用者符合準則。 他們應該針對有好的體驗，並會可能會提供更高的評等，如果推通知要求他們應用程式存放區中的使用者。


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
 
