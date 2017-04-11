<properties
   pageTitle="Azure 行動互動使用者介面狀況分析"
   description="瞭解如何分析歷史使用 Azure 行動互動的應用程式的相關的資料"
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

# <a name="how-to-analyze-historical-data-about-your-application"></a>如何分析應用程式的相關記錄的資料

本文將說明**行動互動**入口網站中的 [**分析**] 索引標籤。 您可以使用 [**行動互動**入口網站監控和管理您的行動應用程式。 請注意，若要開始使用入口網站您第一次需要建立**Azure 行動互動**帳戶。


[分析] 區段的使用者介面提供更新每 24 小時的歷史資料為基礎的應用程式的相關的彙總的資訊。 在不同組成的圓形/線條/橫條圖、 格線及對應的儀表板上會顯示的資訊。 資料也可以下載為.csv 檔案。 大部分的相同資訊的使用者介面，[監視器] 區段中的即時和提供也可以存取從分析 API。

>[AZURE.NOTE] **行動互動**入口網站使用者介面的多個區段包含**顯示說明**] 按鈕。 按下這個按鈕，即可進一步瞭解內容一節。

## <a name="standard-and-custom-analytics"></a>標準和自訂狀況分析

Azure 行動互動提供一組基本的標準分析相關資訊，您可以在您的應用程式整合 SDK 為以的應用程式。 Azure 行動互動也會提供收集使用者的行為您想要的其他自訂分析資訊的功能。 您可以建立的自訂 」 建立的標籤 （應用程式資訊）]，從 [**設定**這樣 Azure 行動互動能為您收集此其他資料標籤計劃。



## <a name="analytics"></a>分析
- 儀表板︰ 顯示您新增及啟動的使用者和其趨勢的一般資訊。
- 使用者︰ 使用者會識別其裝置識別碼︰ 此識別碼都是唯一的每個裝置 （一位新使用者是實際新裝置）。 使用者會被視為新在給定的時間間隔如果他所執行的他此時間間隔內的第一個工作階段。 使用者會被視為時保留他所執行的至少有一個工作階段期間過去 7 天。 作用中的使用者所做在指定期間內至少有一個工作階段的使用者。 您可以排序依據]，每月、 每週每日、 或每小時的時間週期。 所有圖表的看起來類似，但可讓您篩選不同的功能，例如您的應用程式的版本，然後以一段時間的排序。 整合 SDK 收集到的標準資訊包括下列︰ 作用中的使用者、 新的使用者、 工作階段的數字的長度每個工作階段、 技術資訊國家/地區、 區域變數、 位置、 語言航空公司、 裝置、 韌體、 網路 (WIFI)、 版本的應用程式和 sdk，您可以使用客戶。 這項資訊可以從 [監視器] 區段的即時檢視。

> 附註︰ 期間根據使用者的裝置設定]，從日期使用者的電話有設定不正確的日期無法顯示這些錯誤的期間。

- 保留︰ 使用者會被視為時保留在給定的時間間隔如果他所執行的他此時間間隔內的第一個工作階段。 您可以變更時保留的使用者 （和新的使用者） 會計算的時間間隔時間、 天、 週或月數。 使用者保留分析內建 cohorts 上方。 Cohort 是在指定期間偵測到的新使用者的設定 （亦即執行他們在這段期間的第一個工作階段的使用者設定）。 我們使用 cohorts 1 天、 2 天、 4 天、 7 天或 1 個月。 每個 1 天，2 天，4 天、 7 天或 1 個月、 Azure 行動互動計算所有使用者 cohort 及是屬於一組指定 cohort，仍作用中 （亦即執行至少有一個工作階段期間的使用者設定）。 此設定的使用者稱為 cohort 版本。 (Azure 行動互動功能可以顯示多少的使用者仍在使用您的應用程式，但平台特定市集可以告訴您如何許多使用者解除安裝您的應用程式-例如 GooglePlay iTunes 中 Windows 市集等等。)。
- 工作階段︰ 使用一由使用者的應用程式。 工作階段從使用者所執行的活動的順序產生 （活動是通常相關的應用程式，一個畫面中的使用狀況，但這可能會因 SDK 已經整合應用程式中的方式）。 使用者一次只能執行一個活動︰ 工作階段就會立即開始使用者啟動他的第一個活動，並停駐點，他完成他的最後一個活動。 如果使用者保持多個的秒數，而不執行任何動作，他們的活動的順序將分成兩個不同的工作階段。
- 活動︰ 應用程式中的每個畫面的名稱，以及使用者花在每一個畫面。 活動是自訂的分析選項會對應到您自己的應用程式設定的 [應用程式資訊] 標籤︰
- 使用者路徑︰ 顯示您的使用者瀏覽您的應用程式的活動 （螢幕） 的方式。 您可以移動滑桿可調整的層級的詳細資料。 藍色節點代表應用程式的活動。 其大小是按比例的時間中所花費的使用者。 白色節點代表工作階段開始及停止。 紅色的節點代表當機。 連結代表應用程式的活動 （或是之間的活動和當機） 的轉場效果。 按一下 [節點或顯示含有您資料的相關資訊的詳細資訊的工具提示的連結︰ 轉場效果，以及從來源活動的轉場效果，目的地活動的百分比在特定的畫面中，花費的時間。 (---60%---> B 表示使用者活動 A 上移至 [活動 B 60%的時間。)當您想要釐清;，可以重新整理該圖表每次進行變更時，會儲存的位置。 您可以顯示或隱藏以淡化圖形當機。
- 事件︰ 特定採取行動應用程式中的使用者。 分配的事件會顯示為每位使用者每工作階段的事件的計數。 事件代表立即動作，例如，按一下按鈕或在接收到的通知。 （事件的意義取決於 SDK 如何整合應用程式中）。事件發生工作階段或工作期間，也可以是獨立。
- 工作︰ 類似事件，但其焦點長度的動作。 比方說，工作可能會告訴您所需的時間內容載入或打電話至 web 服務的相關技術資訊。 也可能會顯示使用者填寫表單、 建立帳戶，或是購買所需的時間。 工作代表任務的工期，例如，下載任務或時間的工期橫幅會顯示在螢幕上。 （意義的工作取決於 SDK 如何整合應用程式中）。工作是通常與執行 （亦即，沒有任何使用者活動） 的工作階段的範圍以外的背景工作相關聯。
- Technicals︰ 技術資訊和裝置有關您可以用來追蹤，例如地區設定、 公司、 網路、 裝置、 韌體，以及螢幕畫面使用者的裝置的大小，以及您的應用程式的版本和 SDK 版應用程式中使用應用程式的使用者。
- 錯誤︰ 技術錯誤的相關資訊的應用程式不會導致當機的應用程式。 錯誤代表立即問題，例如網路失敗或不正確的操作。 （事件的意義取決於 SDK 如何整合應用程式中）。可獨立或工作階段或工作期間發生錯誤。
- 當機︰ 錯誤的相關資訊會導致您的應用程式當機。 當機是未預期的情況，應用程式停止執行其預期的功能，必須停止的位置。 當機通常是因為應用程式中的錯誤。

![Analytics2][11]

## <a name="accessing-the-retention-overview"></a>存取保留概觀
![Analytics3][12]

保留概觀是分解中間數個卡片，將每個特定保留期間內顯示的概觀。 2 天保留期限是範例所示。 其他卡片顯示 4 天和 7 天保留期間。

## <a name="understanding-the-retention-overview-cards"></a>了解保留概觀卡片
![Analytics4][13]

### <a name="each-card-is-composed-of-3-main-parts"></a>每張卡片 3 的主要部分組成︰
1. 1: cohort 和期間視為
2. 2-4︰ 目前期間保留
3. 5︰ 歷程記錄的走勢圖

### <a name="here-is-detailed-information-about-each-element"></a>以下是每一個元素的詳細的資訊︰
1.    Cohort 和期間︰ 此標題會提供 cohort 的類型。 以下 「 2 天內 」 表示我們看看行為的使用者超過 2 天，連線 2 天下列區塊中收到一段 2 天，以及是否的使用者。 上述範例根據 21 和 22nd 年 11 月之間的使用者活動。
2.    如此保留工資率 21 和年 11 月 22 送達 19 和 20 年 11 月的使用者。 以下我們會透過 3 19th 和 20 之間的新使用者，有 1 之間的 21 及 22nd，作用中的使用者。
3.    此視覺指標會提供相同資訊上方以圖形表示。 （第三個圓形是從 33%數字）。色彩提供其他資訊︰ 綠色表示成長這個數字從先前的計算。 黃色表示穩定，及紅色所代表的意義減少。
4.    指出用來計算的值。
5.    這是保留值的歷程記錄的走勢圖。 其可讓您看到的值，讓它的發展出的主要檢視過去的。


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
