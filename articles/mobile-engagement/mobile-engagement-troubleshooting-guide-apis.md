<properties 
   pageTitle="疑難排解指南-Api azure 行動互動" 
   description="疑難排解 Azure 行動互動-Api 指南" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="10/04/2016"
   ms.author="piyushjo"/>

# <a name="troubleshooting-guide-for-api-issues"></a>API 問題的疑難排解指南

以下是可能的問題，您可能會遇到的系統管理員與 Api 透過 Azure 行動互動互動的方式。

## <a name="syntax-issues"></a>語法問題

### <a name="issue"></a>問題
- 使用 API （或非預期的行為） 語法錯誤。

### <a name="causes"></a>原因

- 語法問題︰
    - 請務必檢查以確認此選項可使用您的特定 api 的語法。
    - 處理的 API 使用狀況的一般問題是令人混淆達到 API 及推播 API （大部分的工作應該執行，而不是推播 API 達到 api）。 
    - 其他常見的問題與 SDK 整合的 API 使用狀況是 SDK 鍵和 API 令人混淆。
    - 連線至 Api 的指令碼需要至少每 10 分鐘的時間傳送資料，或是連線會逾時 （特別是在監視器 API 指令碼接聽資料中的常見）。 若要防止逾時，具有您要保留工作階段與伺服器作用傳送 XMPP 偵測 （ping） 每 10 分鐘的指令碼。

### <a name="see-also"></a>另請參閱
 
- [API 文件][Link 4]
- [XMPP 通訊協定資訊]( http://xmpp.org/extensions/xep-0199.html)
 
## <a name="unable-to-use-the-api-to-perform-the-same-action-available-in-the-azure-mobile-engagement-ui"></a>無法使用 API 來執行相同的動作提供 Azure 行動互動 UI

### <a name="issue"></a>問題
- 從相關的 Azure 行動互動 API 無法執行的動作，從 Azure 行動互動 UI 的運作。

### <a name="causes"></a>原因

- 確認您可以從 Azure 行動互動 UI 執行相同的動作會顯示，您有正確與 SDK 整合 Azure 行動互動的這項功能。

### <a name="see-also"></a>另請參閱
 
- [UI 文件][Link 1]
 
## <a name="error-messages"></a>錯誤訊息

### <a name="issue"></a>問題
- 使用 API 顯示在執行階段，或在記錄中的錯誤碼。

### <a name="causes"></a>原因

- 以下是常見 API 狀態碼數字的參照和初步疑難排解複合清單︰

        200        Success.
        200        Account updated: device registered, associated, updated, or removed from the current account.
        200        Returns a list of projects as a JSON object or an authentication token generated and returned in the response’s body.
        201        Account created.
        400        Invalid parameter or validation exception (check payload for details). The parameters provided to the API or service are invalid. In this case, the HTTP response will embed more details. Make sure to test for the MIME type of the response as the payload can either be plain text or a JSON object.
        401        Authentication error. No user is currently authenticated or connected (check the AppID and SDK key).
        402        Billing lock. The application is either off its quotas or is currently in a bad billing state.
        403        The application is not enabled or the specific API is disabled for this application.
        403        Unauthorized access to the project or application, invalid access key (the key must match the one provided when created).
        403        Campaign specific errors: campaign must be finished (or has already been activated), the suspend action can only be performed on an scheduled campaign, cannot finish a campaign that is not currently “in progress”, campaign must be “in progress” and the campaign’s property named, manual Push must be set to true.
        403        The email address is already associated to another account (a super user for instance). No authentication token will be generated.
        404        Application, device, campaign, or project identifier not found.
        404        Query parameter is invalid JSON or has a field with an unexpected value.
        404        The email address is not associated with an account. Please create or update the account first.
        405        Invalid HTTP method (GET, POST, etc.) or trying to edit a read only segment (i.e. add or update or delete a criterion). A segment becomes read only after it has been computed for the first time.
        409        Name already associated to a different device ID or campaign.
        413        Too many device identifiers (current limit is 1,000), POST URL encoded entity is over 2MB, or the period is too large to be displayed (the server didn’t retrieve the analytics because the user request is for a period that is too large).
        503        Analytics not available yet (the requested information is not computed yet for an application).
        504        The server was not able to handle your request in a reasonable time (if you make multiple calls to an API very quickly, try to make one call at a time and spread the calls out over time).

### <a name="see-also"></a>另請參閱

- [API 文件-詳細的錯誤，在每個特定的 API][Link 4]
 
## <a name="silent-failures"></a>無訊息失敗

### <a name="issue"></a>問題
- API 動作而失敗沒有顯示在執行階段，或在記錄中的錯誤訊息。

### <a name="causes"></a>原因

- 多個項目將會停用 Azure 行動互動 ui 上如果他們不正確，整合，但是會自動從 API 失效，因此請記住，若要測試的 UI，若要查看是否可從相同的功能。
- Azure 行動互動和 Azure 行動互動您嘗試使用，有許多進階的功能需要個別整合到您的應用程式，使用 SDK 為不同的步驟後，才能使用。

### <a name="see-also"></a>另請參閱

- [疑難排解指南-SDK][Link 25]
 
<!--Link references-->
[Link 1]: mobile-engagement-user-interface-home.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/en-us/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/en-us/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/en-us/pricing/details/mobile-engagement/
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
 
