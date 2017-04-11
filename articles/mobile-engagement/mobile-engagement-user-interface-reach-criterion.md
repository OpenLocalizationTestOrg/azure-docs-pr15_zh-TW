<properties 
   pageTitle="Azure 行動互動使用者介面達到準則" 
   description="瞭解如何使用目標的準則，選取您的使用者使用 Azure 行動互動的子集傳送推入行銷活動" 
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


# <a name="how-to-use-targeting-criteria-to-send-push-campaigns-to-a-select-subset-of-your-users"></a>如何使用目標的準則，若要傳送您的使用者選取子集推入行銷活動

以您對象為目標特定準則的 「 新增條件] 按鈕是其中一個可協助您傳送相關推播通知的最有效概念中 Azure 行動互動的客戶，將回應，而非垃圾郵件的每個人。 您可以限制觀眾根據標準的準則及模擬推入決定多少的人員會收到通知。

**另請參閱︰**

- [UI 文件-抵達-新推入行銷活動][Link 27]

## <a name="audience-criteria-can-include"></a>對象準則可以包含︰
- **Technicals:**您可以將目標設根據相同的技術資訊，您可以看到 [分析] 及 [監視器區段。 **另請參閱︰**[UI 文件-狀況分析][ Link 15]， [UI 文件-監視器][Link 16]
- **位置︰**使用 [即時位置報告 」 地理圍欄的應用程式可以做為準則使用地理位置目標對象的 GPS 位置。 「 麻煩區域位置報告 」 通話也可用於目標對象從行動電話的位置 （「 即時位置報告 」 與 「 麻煩區域位置報告 」 必須啟動從 SDK）。 **另請參閱︰**[SDK 文件-iOS-整合][ Link 5]， [SDK 文件-Android-整合][Link 5]
- **達到意見反應︰**您可以針對您根據從先前的範圍通知，透過達到意見反應的相關公告、 投票及資料推入他們的意見反應的對象。 這可讓您更好的目標觀眾後兩個或三個達到行銷活動可以第一次。 也可以篩選出已收到相似的內容，通知設定不會傳送至已收到特定的上一個行銷活動的使用者活動的使用者使用。 您甚至可以排除使用者是誰包含接收新推入仍在使用中的特定行銷活動。 **另請參閱︰**[UI 文件-抵達-推入內容][Link 29]
- **安裝追蹤︰**您可以根據您的使用者安裝應用程式的資訊。 **另請參閱︰**[UI 文件-設定][Link 20]
- **使用者設定檔︰**您可以將目標設根據的標準使用者資訊，而您可以根據您所建立的自訂應用程式資訊的目標。 這包含目前登入的使用者，並已回答您要求他們在應用程式本身，而不是只如何它們是回覆前一個行銷活動中設定的特定問題的使用者。 在此清單上您應用程式顯示所有您的應用程式資訊的定義。
- 區段︰ 您也可以根據您所建立的區段的目標根據包含多個準則的特定使用者行為。 您為您的應用程式定義的區段的所有出現在此清單。 **另請參閱︰**[UI 文件-區段][Link 18]
- **應用程式的資訊︰**從 [設定] 來追蹤使用者的行為，可以建立自訂的應用程式資訊標記。 **另請參閱︰**[UI 文件-設定][Link 20]

## <a name="example"></a>範例︰ 
如果您想要只推播通知您已執行的應用程式的使用者的子集合購買動作。

1. 移至您的應用程式設定] 頁面，選取 [應用程式資訊] 功能表選取 [新增應用程式資訊]
2. 註冊新的布林值的應用程式資訊，稱為 「 inAppPurchase 」
3. 請設定此應用程式的資訊傳送給 「 true 」 當使用者順利執行的應用程式購買應用程式 (使用 sendAppInfo (「 inAppPurchase 」，...) 函數)
4. 如果您不想要從您的應用程式執行此動作，您可以從您的後端執行使用裝置 API）
5. 然後，您只需要建立您的通知，具有限制使用者有 「 inAppPurchase 」 設定為觀眾準則"true")
 
> 注意︰ 針對選取目標應用程式資訊標籤以外的標準需要 Azure 行動互動，若要傳送推入前，可能會導致延遲，請從使用者的裝置收集的資訊。 推入選項 （例如更新徽章） 也可以延遲的複雜的推入設定。 使用從推入 API 」 一次 「 行銷活動是 Azure 行動互動的絕對最快的推入方法。 使用應用程式資訊標籤做推入準則範圍的行銷活動 （可能是從達到 API 或 UI） 是下一個最快的方法，由於應用程式資訊標籤儲存在伺服器端。 使用其他目標的準則推入行銷活動的是最彈性，但慢推入方法，因為 Azure 行動互動的查詢才能傳送行銷活動的裝置。
 
![達到 Criterion1][29] 

## <a name="criterion-options-apply-to"></a>若要套用的條件選項︰
- **Technicals**     
- 韌體名稱︰ 韌體名稱
- 韌體版本︰ 韌體版本
- 裝置模型︰ 裝置模型
- 裝置製造商︰ 裝置製造商
- 應用程式版本︰ 應用程式版本
- 公司名稱︰ 公司名稱、 未定義
- 航空公司國家/地區︰ 航空公司國家/地區，未定義
- 網路類型︰ 網路類型
- 地區設定︰ 地區設定
- 螢幕大小︰ 螢幕畫面大小
- **位置**      
- 上次的區域︰ 國家、 地區、 位置
- 即時地理圍欄︰ 清單的 POIs （名稱、 動作）、 循環 POI 名稱、 緯度、 經緯度 (半徑公尺）
- **連絡意見反應**     
- 宣告意見反應︰ 意見反應的通知
- 獲得的意見反應︰ 投票的意見反應
- 獲得回應的意見反應︰ 獲得回應的意見反應、 問題、 選項
- 資料推入意見反應︰ 資料發送，意見反應
- **安裝追蹤**     
- 儲存︰ 儲存、 未定義
- 來源︰ 來源，未定義
- **使用者設定檔**     
- [性別︰ 男性或女性，未定義
- 出生日期︰ 運算子、 未定義的日期
- 選擇加入集︰ 為 true 或 false，定義
- **應用程式的資訊**      
- 字串︰ 字串，未定義
- 日期︰ 運算子、 未定義的日期
- 整數︰ 運算子、 數字、 未定義
- 布林值︰ true 或 false，未定義
- **區段**    
- （從下拉式清單中），區段的名稱排除 （不屬於本區段目標使用者）。

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
 
