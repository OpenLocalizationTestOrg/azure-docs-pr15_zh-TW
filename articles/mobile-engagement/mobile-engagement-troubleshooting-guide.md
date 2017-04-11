<properties 
   pageTitle="Azure 行動互動疑難排解指南" 
   description="Azure 行動互動疑難排解指南" 
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

# <a name="azure-mobile-engagement---troubleshooting-guide"></a>Azure 行動互動-疑難排解指南

## <a name="introduction"></a>簡介
下列疑難排解指南將協助您瞭解根本原因的一些常見的問題和會讓您疑難排解自己。 

## <a name="general"></a>一般

一般而言，您一定要確定下列動作︰

1. 請確定您已經在我們的[快速入門教學課程](mobile-engagement-windows-store-dotnet-get-started.md)中所述整合所需的所有步驟
2. 您使用的平台 Sdk 的最新版本。 
3. 由於模擬器只特殊的一些問題，請測試模擬器和實際的裝置上。 
4. 您不正中任何限制/節流從行動互動方面[以下](../azure-subscription-service-limits.md)
5. 如果您不是連線到行動互動服務後端或查看資料不載入持續然後確定沒有進行中的服務事件，請核取[以下](https://azure.microsoft.com/status/)

## <a name="monitor-issues"></a>「 監控 」 問題

### <a name="i-am-not-seeing-my-device-showing-up-on-the-monitor-tab"></a>我看不到我的裝置在 [監視器] 索引標籤上顯示
監視器] 索引標籤會顯示連接到您的行動互動平台即時的裝置。 如果您在模擬器和裝置上，您應該會看到以下至少有一個工作階段。 如果分散式的應用程式，您會看到反映裝置連接至即時的平台作用中的工作階段儀表。 

如果您沒有看到您的裝置在 [監視器] 索引標籤上，則可能 SDK 整合問題。 若要疑難排解需要採取一些常見步驟如下所示︰

1. 確定您的行動應用程式中使用正確的連接字串，並從 SDK 索引鍵一節及不 API 金鑰區段。 連接字串會將您的行動應用程式連線到行動互動應用程式，您會看到您的裝置在 [監視器] 索引標籤的執行個體。 
2. Windows 平台-如果您的頁面會覆寫`OnNavigatedTo`方法，請務必以撥打`base.OnNavigatedTo(e)`。
3. 如果您將行動互動整合至現有的行動應用程式，那麼您也可以確保您未查看進階的整合步驟來遺漏任何步驟，[在這裡](mobile-engagement-windows-store-integrate-engagement.md)
4. 請確定您要傳送至少有一個的畫面活動覆寫 [根據您正在[開始教學課程](mobile-engagement-windows-store-dotnet-get-started.md)中所述的平台 EngagementActivity] 頁面。

### <a name="i-am-seeing-the-monitor-tab-showing-a-session-even-when-i-have-disconnected-or-closed-my-app-emulator"></a>我看到監視器] 索引標籤顯示工作階段即使當我已中斷連線或關閉 [我的應用程式 / 模擬器。 
如果您僅有一個連線至平台此時，您會使用模擬器開啟應用程式這是可能的原因模擬器缺點。 一般而言，您需要，您回到主畫面上模擬器成功中斷的應用程式工作階段，請確定。 此外，在 Windows 平台，錯 Visual Studio 中，您可能需要以確保在 Visual Studio 中，移至**生命週期的事件**功能表列，然後按一下**暫停**真的關閉工作階段。 如需詳細資訊，請參閱[Windows 教學課程](mobile-engagement-windows-store-dotnet-get-started.md)。 

## <a name="analytics-issues"></a>[分析] 問題

### <a name="i-am-not-seeing-any-data-refreshed-data-on-analytics-tab"></a>我看不到任何資料/重新整理資料分析] 索引標籤上 
定期重新計算分析資料，可能需要最多 24 小時進行這項重新整理。 此資料不即時，您會看到它重新整理此 24 小時時間週期內。
請務必要確定不過您傳送至少有一個畫面或活動的平台後端以與 [覆寫至少一頁`EngagementActivity`或呼叫`SendActivity`explcitly。 

### <a name="i-am-seeing-incorrectly-captured-datetime-for-a-device-on-the-analytics-tab"></a>我看到擷取不正確的日期/時間裝置的 [分析] 索引標籤
分析時間範圍根據使用者的裝置設定的日期。 因此，確定該裝置已正確設定的日期。 

## <a name="segment-issues"></a>[區段] 中發生的問題

### <a name="i-created-a-segment-and-it-is-showing-up-as-greyed-out-or-not-showing-any-data"></a>我在自己建立一個區段，並且會顯示為灰色或不顯示任何資料
區段建立即時在時間中無法使用。 計算同時在彙總的分析資料時，因此，可能需要最多 24 小時的時間。 您應該稍後再回來，但同時也請務必在行動應用程式確實要傳送您以其構成區段的資料。 例如︰ 如果事件說出 「 foo 」 不所傳送的任何行動裝置，然後就不會建立 EventName 區段的任何區段資料，= foo 為準則。 您也應該檢查您的 SDK 整合，以確保您的行動應用程式正確傳送資料。 

## <a name="reach-or-push-notifications-issues"></a>「 達到' 或推入通知問題

### <a name="my-push-messages-are-not-being-delivered"></a>推入郵件未傳遞 

1. 請嘗試通知傳送到第一次，以確保所有元件-行動應用程式與服務 SDK 都都正確連線，並可進行推入通知測試裝置。 
2. 永遠最簡單 ' 出應用程式傳送通知給 「 第一次透過未排定的活動和也有指定任何對象準則。 這是一次以證明通知連線正確運作。 
3. 如果您有傳送的應用程式通知中的問題也則良好的第一個步驟，嘗試第一次傳送出應用程式通知。 
4. 請確定的 「 原生推播 ' 正確地設定為您的行動應用程式。 根據平台它可能會包含金鑰 （Android、 Windows） 或憑證 (iOS)。 請參閱[使用者介面-設定](mobile-engagement-user-interface-settings.md)
5. 登出應用程式通知可能也會被封鎖使用者透過行動作業系統，因此請確定不符合上述情況。 
6. 請確定，您不設定*略過的對象，將使用者透過 API 傳送推入*] 選項的範圍行銷活動的 [**活動**] 區段中因為這可確保只有無法透過 Api 傳送推入通知。 
7. 請確定您使用透過 WIFI 和電話業者網路，以利消除網路連線問題可能來源連接兩個裝置測試您的推入行銷活動。
8. 確定您的裝置/模擬器系統日期/時間正確，因為任何同步裝置也會干擾傳送通知的推入通知服務的能力。 

更多的平台特定疑難排解下列指示︰

1. **iOS** 

    - 請確定憑證有效且未到期的 iOS 推播通知]。 
    - 請確定已正確設定*生產*憑證互動行動應用程式中。 
    - 請確定您正在測試上*真的裝置。* IOS 模擬器無法處理推入訊息。
    - 請確定套件識別碼已正確設定行動應用程式。 請參閱指示，[在這裡](https://developer.apple.com/library/prerelease/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)
    - 測試時，請使用 「 臨機操作 「 通訊群組行動佈建設定檔中。 您會無法收到通知，如果您的應用程式會使用 「 偵錯 」 編譯

2. **Android**

    - 請確定您有 \n 字元的行動應用程式的 AndroidManifest.xml 檔案中指定正確的專案數字。 
    
            <meta-data android:name="engagement:gcm:sender" android:value="************\n" />
        
    - 請確定您不是遺失或正確設定的任何 Android 資訊清單檔案中的權限 
    - 請確定您要新增到您的用戶端應用程式的專案數字是從同一個帳戶有 GCM 伺服器金鑰的位置。 任何不相符的兩個可防止您推入送出。 
    - 如果您收到系統通知，但不是在應用程式然後檢閱[指定通知] 區段的圖示](mobile-engagement-android-get-started.md)，為可能並不指定正確的圖示 Android 資訊清單檔案中。 
    - 如果您要傳送 BigPicture 通知，請確定，如果您有外部圖像伺服器，然後他們必須能支援 HTTP 「 取得 」 和 「 標頭]。

3. **Windows**
    
    - 請確定您擁有有效的 Windows 市集應用程式相關應用程式。 在 Visual Studio-您必須以滑鼠右鍵按一下專案並選取 「 建立關聯應用程式與存放區 」 選項，選取您在 Windows 市集應用程式。 此 Windows 市集應用程式應該是同一個有原生推入認證，以設定行動互動入口網站中的位置。
    - 如果您收到出應用程式推入通知，但不是的應用程式通知與`EngagementOverlay`整合然後確保您的頁面中有根格線項目。 在頁面，將兩個 web xaml 檔案中找到的第一個 [格線] 項目檢視 EngagementOverlay 會使用。 如果您要找出網頁檢視將會設定，您可以定義格線，名為 「 EngagementGrid 」，不過您必須以確保有足夠的高度與寬度的兩個後續 web 檢視其會顯示通知和的應用程式通知為下列的宣告等︰
        
            <Grid x:Name="EngagementGrid"></Grid>

### <a name="i-created-a-push-notificationannouncement-campaign-and-even-after-it-sent-me-the-notification-it-is-showing-as-active-what-does-it-mean"></a>我在自己建立推入通知公告/活動，甚至是將其傳送我通知後，會顯示為 「 使用中 」。 這是什麼意思？ 
您在行動互動建立**行銷活動**稱為，因為它是長時間執行的推入通知意義，為新的裝置連接到您的行動裝置的互動平台，他們會自動傳送您在這裡，設定通知，只要符合您在行銷活動中設定的準則。 這是不擷取的單一通知安裝程式。 您必須以手動方式按一下 [**完成**] 按鈕來結束行銷活動，使其不會傳送通知]。 

### <a name="i-created-a-push-campaign-and-i-am-receiving-notifications-successfully-however-whenever-i-open-up-the-app-i-get-the-same-notification-even-when-i-had-actioned-it-before"></a>我在自己建立推入行銷活動，我收到通知成功不過應用程式開啟時，即取得相同的通知我時，即使 actioned 嗎？ 
這是可能會發生這種測試，如果您使用模擬器或某些測試等 TestFlight 架構。 有什麼新鮮事以下在執行執行個體，每個應用程式，它會取得新的 DeviceID，傳送給我們後端的導致行動互動平台視為新裝置，並傳送通知。 

## <a name="getting-support"></a>取得支援

如果您無法以解決此問題您自己，然後您可以︰

1. 搜尋中的現有執行緒 StackOverflow 論壇和[MSDN 論壇，在](https://social.msdn.microsoft.com/Forums/windows/en-US/home?forum=azuremobileengagement)您的問題，並如果不然後提出那里問題。 
2. 如果您找到遺失然後新增/投票要求上[UserVoice 論壇，在](https://feedback.azure.com/forums/285737-mobile-engagement/)我們的功能
3. 如果您有 Microsoft 支援開啟支援事件提供的下列詳細資料︰ 
    - Azure 訂閱識別碼
    - 平台 (例如 iOS，Android 等)
    - 應用程式識別碼
    - 行銷活動識別碼 （適用於推入通知問題）
    - 裝置識別碼
    - 行動互動 SDK 版本 (例如 Android SDK v2.1.0)
    - 正確的錯誤訊息與案例的錯誤詳細資料
