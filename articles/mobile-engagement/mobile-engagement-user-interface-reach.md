<properties 
   pageTitle="Azure 行動互動使用者介面的範圍" 
   description="瞭解如何使用 Azure 行動互動的推入通知給您的應用程式使用者聯絡" 
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


# <a name="how-to-reach-out-to-the-users-of-your-application-with-push-notifications"></a>如何連絡您的應用程式與推入通知使用者

本文將說明**行動互動**入口網站的 [**連絡**] 索引標籤。 您可以使用 [**行動互動**入口網站監控和管理您的行動應用程式。 請注意，若要開始使用入口網站您第一次需要建立**Azure 行動互動**帳戶。 如需詳細資訊，請參閱[建立 Azure 行動互動帳戶](mobile-engagement-create.md)。

[範圍] 區段的使用者介面是讓您可以建立/編輯/啟動/完成/監視器的推入行銷活動管理工具，並取得推入通知行銷活動，也可以存取透過達到 API （和低層級推播 API 的某些項目） 功能的統計資料。 請記住，無論您使用的 Api 或使用者介面，您需要整合到應用程式中每個平台與 SDK，您才能使用的範圍和 Azure 行動互動達到行銷活動。

>[AZURE.NOTE] **行動互動**入口網站使用者介面的多個區段包含**顯示說明**] 按鈕。 按下這個按鈕，即可進一步瞭解內容一節。

## <a name="four-types-of-push-notifications"></a>四種類型的推播通知
1.    公告-可讓您傳送通知訊息給導向到其他位置，在您的應用程式的使用者，或是傳送給網頁或外部應用程式存放區。 
2.    投票-可讓您從使用者收集的資訊，要求他們的問題。
3.    資料推入-可讓您傳送的二進位或 base64 資料檔案。 在資料推入中所包含的資訊會傳送至您的應用程式修改您的應用程式中的使用者的目前體驗。 您的應用程式需要能處理程序中的資料推入的資料。

## <a name="campaign-details"></a>行銷活動詳細資料

您可以編輯、 複製、 刪除或啟動的行銷活動的有尚未啟動的游標暫留在他們的名稱，或者您可以按一下以開啟檔案。 您可以複製已由游標暫留在他們的名稱尚未啟動的行銷活動或您可以按一下以開啟檔案。 不過，您無法變更行銷活動，一旦啟動它。
 
![Reach1][18]

## <a name="reach-feedback"></a>連絡意見反應

按一下以查看達到行銷活動的詳細資料的**統計資料**。 **簡單**的檢視會提供視覺化的方式呈現後啟動的行銷活動時，發生了什麼事欄橫條圖的形式。 [**進階**] 檢視會提供更細緻資料推入行銷活動。 這些詳細資料，將無法使用，如果您要傳送測試行銷活動即傳送到測試裝置推入。 以下是您應解譯這些詳細資料的方式︰

1. **Pushed** -指定推入裝置的訊息數。 這個數字會取決於您建立推入行銷活動時指定目標對象。 如果您沒有指定任何目標對象，然後此推入將會傳送至所有已註冊的裝置。 所有其他的推入服務，例如，我們進行不推入通知，直接對裝置但改為傳送至個別的平台特定的推入通知服務 (PNS-APNS/GCM/WNS)，讓他們可以將通知傳送至裝置。 

2.  **已傳遞**-為接收的行動互動 SDK 指定其已成功傳遞到裝置 PNS 和確認訊息的數目。 
        
    *已傳遞的原因計算小於推入計算︰*
    
    1. 如果使用者已從裝置項目解除安裝應用程式，但 PNS 並不知道，我們傳送給 PNS 的推入次會被刪除的郵件。
    2. 如果裝置有應用程式，但卻自己的裝置離線長的時間，將無法傳送郵件給裝置 PNS。 
    3. 如果沒有裝置取得傳遞郵件，但行動應用程式中互動 SDK 無法辨識的郵件內容，其去掉的郵件。 如果自訂的應用程式中的通知產生攔截例外狀況 SDK 和拖放郵件中，則會發生此問題。 這可能也會發生的應用程式在裝置上使用行動互動 SDK 不能瞭解推入郵件寄件者的平台的新版本的版本，但這是只有在應用程式已升級後的通知已分派服務平台時。 [**進階**] 索引標籤會告訴您已經捨棄郵件數。 
    4. 在 iOS 裝置上郵件有時才傳送如果裝置上電力或處理遠端通知時，應用程式正在使用大量 power。 這是 iOS 裝置的限制。   

3.  **顯示**-此指定的成功顯示在表單中的通知中心系統推入/出的-應用程式通知或行動應用程式的應用程式通知裝置上的應用程式使用者的郵件數。  [**進階**] 索引標籤會告訴您如何許多系統通知，而且多少已在應用程式通知。 
    
    *顯示的理由計算小於傳遞 count （等待顯示）*
    
    1. 如果通知行銷活動透過結束日期，則可能已傳送的通知，但當時間開啟，並將其顯示應用程式使用者時，它已過期，永遠不會顯示。   
    2. 如果通知是在應用程式通知，則應用程式使用者開啟應用程式時，才會顯示通知。 在應用程式使用者尚未開啟應用程式的位置的情況下，通知已傳遞，但尚未顯示，直到開啟應用程式時，會報告 SDK。 
    2. 如果通知的應用程式通知，並設定顯示在特定的活動/畫面上，然後也會通知報告為傳遞，但尚未傳遞直到使用者開啟特定的畫面上的應用程式。 
    
4.  **使用者的互動**-此指定的郵件應用程式使用者具有互動，便會加入 actioned 或結束的郵件。 

    - *應用程式使用者可以通知中下列其中一項動作︰*
            
        1. 如果通知系統/出的-應用程式通知 」 或 「 傳送為通知專用應用程式使用者按一下通知中應用程式通知。
        2. 如果通知是文字或 web 檢視或投票的應用程式通知應用程式使用者按一下通知中的 [動作] 按鈕上。
        3. 如果應用程式使用者在 [網頁檢視 [Android 僅限] 中的 URL，然後通知 web 檢視的應用程式通知
    
    - *應用程式使用者可以結束通知中下列方法之一︰*
    
        1. 直接按一下 [關閉] 按鈕的通知。 
        2. 離開撥動，或刪除通知。 
        3. 使用文字/網頁內容和輪詢的回覆的應用程式通知通常會顯示應用程式中的使用者執行的程序。 先瞭解通知，並按一下它，就會看到後續文字/web/投票內容。 應用程式使用者可以結束其中一個步驟中的通知並在 [進階] 檢視中的詳細資料擷取此。 

5.  **Actioned** -此指定的已明確 actioned 應用程式使用者的郵件數。 這是最感興趣的數字，因為這會告訴多少應用程式使用者已有興趣，您推入通知中的訊息。 
 
> [AZURE.NOTE] 在 iOS 與 Windows 平台，如果使用者有應用程式開啟然後行銷活動的 「 隨時 「 行銷活動可以同時登出應用程式並在應用程式通知會顯示在同一時間]。 這可能會導致高於傳遞顯示字數統計。 如果使用者互動或動作通知，然後使用者互動/Actioned 計數可能大於傳遞。 


![Reach2][19]

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
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md
 
