<properties 
   pageTitle="疑難排解指南-SDK azure 行動互動" 
   description="在 Azure 行動互動 SDK 整合問題的疑難排解" 
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

# <a name="troubleshooting-guide-for-sdk-integration-issues"></a>SDK 整合問題的疑難排解指南

以下是可能的問題，您可能會遇到的 Azure 行動互動如何整合到您的應用程式。

## <a name="sdk-issues-discovered-by-a-failure-in-another-area-of-your-application"></a>在您的應用程式的其他區域失敗所發現的 SDK 問題

### <a name="issue"></a>問題
- UI 資料 （在分析、 監控、 分割或儀表板） 的集合失敗。
- 推播失敗 （應用程式，登出應用程式，或兩者皆不用於推入）。
- 進階功能失敗 （追蹤、 地理位置或特定的推入無法運作的平台）。
- API 失敗 （Api 失敗通常火車不錯誤訊息）。
- （無 Azure 行動互動的適合您的應用程式） 服務失敗。

### <a name="causes"></a>原因

- 在您的應用程式 （例如 UI 資料集合失敗、 推入失敗、 進階的功能失敗、 API 失敗、 應用程式當機或明顯服務中斷） 錯誤，並會發現大部分需要使用 Azure 行動互動 SDK 解決的問題。  
- 如果 Azure 行動互動的特定功能具有永遠不會在您的應用程式之前，您必須完成整合。 
- 如果 Azure 行動互動的特定功能運作，而停止，您可能需要升級到最後一個版本和 Azure 行動互動 SDK。 請記得在不同版本的 Azure 行動互動 SDK 針對每個 Azure 行動互動 （Android、 iOS、 Windows 和 Windows Phone） 所支援的平台。

#### <a name="sdk-integration"></a>SDK 整合

- 在 SDK （分析） 不正確整合 azure 行動互動。
- 連絡不正確地整合在 SDK （應用程式和的應用程式將推入）。
- 憑證過期或不正確產品與開發 (僅限 iOS)。
- 在 SDK 不正確地整合 GCM 或 ADM (Android 只有-服務特定推入)。
- 不正確追蹤整合 SDK （追蹤安裝儲存）。
- 延遲位置或 GPS 位置不正確地整合 SDK （目標地理位置）。


**另請參閱︰**

- [SDK 文件-整合輔助線][Link 5] 
- [疑難排解指南-推入][Link 23]

#### <a name="sdk-upgrade"></a>SDK 升級

- 需要升級 SDK 解決與較舊版本的 SDK （通常相關的裝置 OS 較新版本） 的問題。
- 解除安裝應用程式的所有舊版本從您的裝置，重新安裝最新版的應用程式，重新註冊您的裝置識別碼，從 Azure 行動互動 UI 如要確認您的裝置使用最新版的應用程式。

**另請參閱︰**

- [SDK 文件的版本資訊](http://go.microsoft.com/fwlink/?LinkId= 525554) 
- [SDK 文件-升級指南](http://go.microsoft.com/fwlink/?LinkId= 525554)

#### <a name="sdk-other"></a>其他 SDK

- 在 [應用程式資訊清單 」 AndroidManifest.xml 」 的錯誤可能會導致 Azure 行動互動，不能運作 (僅限 Android)。
- 使用 SDK 整合和的 API 使用狀況的一般問題是 SDK 鍵和 API 令人混淆。

**另請參閱︰**

- [概念-詞彙][Link 6]

## <a name="advanced-coding-issues"></a>進階程式碼的問題

### <a name="issue"></a>問題
-  與不直接相關，Azure 行動互動的平台特定程式碼 iOS、 Android 和 Windows Phone 上可能會導致問題。

### <a name="causes"></a>原因

- 許多進階 Azure 行動互動編碼問題會因撰寫不正確的平台與不直接相關，Azure 行動互動的特定程式碼。 您必須請參閱 Azure 行動互動文件中，使用 （Android、 iOS、 網頁、 Windows 和 Windows Phone） 除了開發的平台特定的文件。
- 未正確設定 「 類別 」，可避免通知連結到另一個位置內部或外部應用程式 (僅限 Android)。 
- 未設定 「 UIKit.framework 」 「 選擇性 」 在 iOS 程式碼中顯示的 「 找不到錯誤符號]，及/或較舊的 iOS 裝置 (僅限 iOS) 當機。
- 過期憑證，或不正確地使用開發或 Prod 版本的憑證，原因推入問題 (僅限 iOS)。
- 有一些限制原有的平台 Azure 行動互動無法控制 （例如系統管理中心的運作方式的登出應用程式在 Android 和 iOS 推入）。
- Azure 行動互動發佈內部套件使用 iOS 和 Android 版參照 Azure 行動互動的完整清單。 請記住 Azure 行動互動的某些功能是特定平台 （Android、 iOS、 網頁、 Windows 和 Windows Phone）。

### <a name="see-also"></a>另請參閱

 - [疑難排解指南-推入][Link 23] 
 - [SDK 文件的版本資訊][Link 5]
 - [SDK 文件-升級指南][Link 5]

## <a name="application-crashes"></a>應用程式當機

### <a name="issue"></a>問題
- 您的應用程式當機使用者的裝置上。

### <a name="causes"></a>原因

- 在*分析 UI*或*分析 API*可以檢視當機資訊
- 您可以尋找測試裝置的裝置識別碼，並執行相同動作造成您的應用程式當機使用者可以協助您識別您損毀的原因。
- Azure 行動互動 SDK 的造成損毀的應用程式的已知的問題有時候會解析升級至最新版的 SDK。 請務必瞭解您的平台的版本資訊時調查當機。

### <a name="see-also"></a>另請參閱

- [SDK 文件的版本資訊][Link 5]
- [SDK 文件-升級指南][Link 5]

## <a name="app-store-upload-failures"></a>App store 上傳失敗

### <a name="issue"></a>問題
- 若要將最新版的應用程式上傳到 Apple、 Google 或 Windows 應用程式存放區有關的錯誤。

### <a name="causes"></a>原因

- 應用程式儲存有時區塊應用程式啟用某些功能 （例如 Apple Store 防止存放區中的應用程式中使用 IDFV 和 GooglePlay 存放區會防止共用應用程式之間的應用程式資訊）。 
- 請確定您檢查版本的相關附註您的平台和目前版本的 SDK，如果您無法將應用程式上傳至市集]。

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
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
 
