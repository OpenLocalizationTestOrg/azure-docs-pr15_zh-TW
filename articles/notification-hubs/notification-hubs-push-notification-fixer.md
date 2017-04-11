<properties 
    pageTitle="Azure 通知集線器-診斷指導方針" 
    description="診斷 Azure 通知集線器一般問題的指導方針。" 
    services="notification-hubs" 
    documentationCenter="Mobile" 
    authors="ysxu" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="notification-hubs" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="NA" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="10/03/2016" 
    ms.author="yuaxu"/>

#<a name="azure-notification-hubs---diagnosis-guidelines"></a>Azure 通知集線器診斷指導方針

##<a name="overview"></a>概觀

最常見的問題，我們聆聽 Azure 通知集線器客戶之一是如何找出為何會看到其應用程式後端傳送通知會出現在用戶端裝置-丟棄通知位置及原因，以及如何修正此問題。 本文中我們會通知可能卸除或不結尾的各種原因的裝置上。 我們也會尋找透過可以分析及找出根本原因的方式。 

首先，務必瞭解如何 Azure 通知集線器推入通知出的裝置。
![][0]

一般傳送的通知流程，**應用程式後端**會傳送郵件至**Azure 通知中心 (NH)**在沒有設定的標籤與標籤運算式，來判斷 「 目標 」 即所有登錄需接收推入通知，請考慮的所有登錄一些處理程序。 下列登錄可以橫跨在任一或所有我們的支援平台-iOS，Google，Windows Windows Phone 中，Kindle 和 Baidu android china （中國)。 NH 然後推入通知，查看目標建立之後，分割跨多個批次的裝置平台的登錄特定**推入通知服務 (PNS)** -例如的 Apple APNS、 GCM 的 Google 等。根據您在 [設定通知中心] 頁面上 [Azure 傳統入口網站中設定的認證分別 PNS NH 驗證。 PNS 然後轉寄個別的**用戶端裝置**的通知。 這是建議的方式傳送推入通知，並記下的通知傳送的最後階段生效的平台 PNS 與裝置之間的平台。 因此我們有四個主要元件的*用戶端*、*應用程式後端*、 *Azure 通知集線器 (NH)*和*推入通知服務 (PNS)*和下列其中一個可能會導致快速卸除的通知。 更多詳細資料，這種架構會提供[通知集線器概觀]。

階段可能表示組態問題，或它可能會發生生產位置全部或部分的通知可能會收到首字表示某些更深入的應用程式或訊息圖樣問題，可能會發生初始測試/臨時期間傳送通知失敗。 在區段中，以下我們會查看各種首字的通知案例範圍是從一般到少數類型，其中一些您可能會發現明顯和一些其他人不太多。 

##<a name="azure-notifications-hub-mis-configuration"></a>Azure 的通知中心做的設定 

Azure 通知集線器必須進行自我驗證，才能順利至個別 PNS 傳送通知開發人員的應用程式的內容中。 這是因為開發人員建立個別的平台 （Google、 Apple Windows 等等） 開發人員帳戶，然後註冊 [應用程式存取需要設定通知集線器組態] 區段底下入口網站中的認證的位置。 如果沒有通知要製作透過，第一個步驟應該確保正確的認證設定在符合這些應用程式建立的平台特定的開發人員的帳號] 之下的 [通知] 中心。 您會發現我們的[快速入門教學課程]有用到對此程序逐步的方式。 以下是一些常見的不正確設定︰

1. **一般**
 
    a） 請確定您的通知中心名稱 （不含拼字錯誤） 是相同︰

    - 您要註冊從用戶端， 
    - 要傳送通知後端，  
    - 您已在此設定 PNS 認證和 
    - 您已經設定用戶端與後端其 SA 認證。 
        
    b） 請確認您在用戶端和應用程式後端上使用正確的 SA 設定字串。 為法則，您必須使用**DefaultListenSharedAccessSignature**用戶端和**DefaultFullSharedAccessSignature**上應用程式後端 （讓權限，才能傳送通知給 NH）

2. **Apple 推入通知服務 (APNS) 設定**
 
    您必須有兩個不同的集線器-另一個用於生產，另一個用於測試用途。 這表示上傳您要在個別中樞的沙箱環境中使用的憑證和您要在個別的集線器生產環境中使用的憑證。 請勿嘗試時，可能會導致通知失敗，往下的，將不同類型的憑證上傳至相同的中心。 如果您發現自己，您不小心上傳不同類型的憑證到相同的中心的位置，建議您刪除中樞並重新開始。 如果基於某種原因，您無法刪除中心接著至少，您必須刪除所有現有的登錄從中心。 

3. **Google 雲端訊息 (GCM) 設定** 

    a） 請確定您在雲端專案要啟用 「 Google 雲端訊息 Android 適用的]。 
    
    ![][2]
    
    b） 請確定您建立的是 「 伺服器金鑰 」，同時藉由取得認證的 NH 會使用 GCM 驗證方法。 
    
    ![][3]
    
    c） 請確認您已在用戶端，這是您可以從儀表板取得完全數值實體設定 「 專案識別碼 」:
    
    ![][1]

##<a name="application-issues"></a>應用程式的問題

1) **標籤 / 標記運算式**

如果您使用的標籤或標籤運算式區段您對象，隨時都可能是，當您傳送的通知，是根據您指定在您傳送的通話中的標籤/標記運算式找不到目標。 最好檢閱您註冊，以確保標籤時，符合您傳送通知，然後確認僅從這些登錄的用戶端通知回條。 例如︰ 如果 NH 與您進行之註冊已完成所有說 「 政治 」 標記，而且您會傳送通知標記 「 運動 」，它不會傳送至任何裝置。 複雜的情況下可能包括標記運算式，您僅註冊 」 標記 A 」 或 「 標籤 B"，但時傳送通知，您的目標 」 標記 A 和和標籤 B 」。 在下面的自動診斷秘訣區段中，有的方法您可以檢視您註冊，以及他們擁有的標籤。 

2) **範本的問題**

如果您使用範本，請確定您追蹤在[範本指南]所述的指導方針。 

3) **無效的註冊**

假設通知中心已正確設定，而且任何標籤/標記的運算式，用正確結果的有效目標需要會傳送通知的尋找，NH 會引發關閉平行-傳送訊息給一組登錄的每個批次的數個處理批次。 

> [AZURE.NOTE] 因為我們執行平行處理程序，我們不保證傳送通知的順序。 

現在 Azure 通知中心已最佳化而適合 」 在大多數一次 」 郵件傳遞模型。 這表示我們嘗試取消重複，讓沒有通知將多次傳遞到裝置。 確定我們查看註冊，並確保只有一個郵件會傳送每裝置識別碼實際 PNS 傳送郵件之前，先。 為每個批次會傳送至 PNS，依序是接受並驗證註冊，有可能 PNS 偵測與一或多個批次中註冊錯誤至 Azure NH 會傳回錯誤，並會停止處理藉此完全卸除該批次。 特別是使用 APNS 使用 TCP 串流通訊協定。 雖然我們適用於在大部分後傳遞，在這種情況下我們從資料庫移除錯誤註冊，然後再試一次該批次裝置的其餘部分中的通知傳送。

您可以取得傳遞失敗嘗試針對使用 Azure 通知集線器 REST Api，註冊錯誤資訊︰[每個訊息遙測︰ 取得通知訊息遙測](https://msdn.microsoft.com/library/azure/mt608135.aspx)和[PNS 意見反應](https://msdn.microsoft.com/library/azure/mt705560.aspx)。 請參閱[SendRESTExample](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/SendRestExample)例如程式碼。

##<a name="pns-issues"></a>PNS 問題

分別 PNS 已收到通知訊息後再負責其傳遞至裝置的通知。 Azure 通知集線器超出的圖片，然後在有沒有控制項時，或如果通知傳送至裝置。 由於平台通知服務很強大，通知似乎經常犯到達 PNS 從裝置幾秒鐘。 如果 PNS 不過節流然後 Azure 通知集線器並套用關閉策略指數後，如果 PNS 仍然無法連線的 30 分鐘然後我們有原則過期，並將會永久放置這些郵件的位置。 

如果 PNS 嘗試傳送通知，但該裝置已離線，通知 PNS 儲存的一段有限的時間，而傳送到裝置可供使用。 儲存特定的應用程式只能有一個最近的通知。 如果裝置離線時，會傳送多個通知，每個新訊息通知會導致捨棄事先通知。 保持最新的通知的此行為被指聯合 APNS 中的通知及摺疊在 GCM （即使用摺疊索引鍵）。 如果裝置保持離線的較長的時間，會捨棄任何已儲存它的通知。 來源- [APNS 指引] & [GCM 指南]

使用 Azure 通知集線器-您可以透過使用一般 HTTP 標頭傳遞聯合鍵`SendNotification`API (例如的.NET SDK – `SendNotificationAsync`) 其中也會傳遞為 HTTP 標題是個別 PNS。 

##<a name="self-diagnose-tips"></a>自動診斷秘訣

以下將介紹各種途徑診斷和根會造成任何通知中心問題︰

###<a name="verify-credentials"></a>驗證認證

1. **PNS 開發人員入口網站**

    請確認其在個別 PNS 開發人員入口網站 （APN，GCM，WNS 等） 使用我們的[快速入門教學課程]。

2. **Azure 傳統入口網站**

    移至 [設定] 索引標籤，檢閱並與其從 PNS 開發人員入口網站取得符合的認證。 

    ![][4]

###<a name="verify-registrations"></a>確認登錄

1. **Visual Studio**

    如果您是使用 Visual Studio 開發然後您可以連線到 Microsoft Azure 及檢視及管理一堆 Azure 服務包括 「 伺服器總管 」 的通知中心。 有幫助您的裝置/測試環境。 

    ![][9]

    您可以檢視及管理所有登錄中中心分類精美的平台，原生或範本註冊、 任何標記、 PNS 識別碼，註冊 id 和到期日。 您也可以編輯登錄即時-這是假設是如果您想要編輯任何標籤時，才有幫助。 

    ![][8]
 
    > [AZURE.NOTE] 若要編輯登錄的 visual Studio 功能應該只用於開發/測試有限的數字的登錄期間。 如果那里發生需要大量修正您註冊，請考慮使用匯出/匯入註冊功能此處所述-[匯出/匯入登錄](https://msdn.microsoft.com/library/dn790624.aspx)

2. **服務匯流排總管**

    許多客戶使用檔案總管此處所述- [ServiceBus 總管]檢視及管理其通知中心 ServiceBus。 它是由 code.microsoft.com- [ServiceBus 總管程式碼]提供的開啟來源專案

###<a name="verify-message-notifications"></a>確認訊息通知

1. **Azure 傳統入口網站**

    您可以移到傳送給您的用戶端的測試通知，而不需要任何服務後端設定及執行的 「 偵錯] 索引標籤。 

    ![][7]

2. **Visual Studio**

    您也可以從 Visual studio comforts 傳送測試通知︰

    ![][10]

    您可以閱讀更多以下-Visual Studio 通知中心 Azure 檔案總管] 功能 
    
    - [與伺服器總管概觀]
    - [與伺服器總管部落格文章-1]
    - [與伺服器的檔案總管部落格文章 2]

###<a name="debug-failed-notifications-review-notification-outcome"></a>偵錯失敗的通知/檢閱通知結果

**EnableTestSend 屬性**

當您傳送通知集線器透過通知時，一開始，只要取得佇列中等待 NH 如何找出所有目標處理，然後最後 NH 傳送給 PNS。 這表示，當您在使用 REST API 或任何用戶端 SDK，您傳送的電話轉接成功傳回只代表的郵件已經順利佇列的通知中心。 並未提供深入 NH 最後有傳送訊息給 PNS 有何改變。 如果您通知不會在用戶端裝置送達，有可能的當 NH 嘗試傳遞郵件給 PNS，例如內容大小超過 PNS 所允許的最大值時發生錯誤或 NH 中設定的認證無效等。若要深入了解 PNS 錯誤，我們引進稱為[EnableTestSend 功能]的屬性。 這個屬性會自動啟用，當您從網站或 Visual Studio 用戶端傳送測試郵件時，因此可讓您查看偵錯的詳細的資訊。 您可以使用這個透過記錄的位置，現在.NET SDK 範例的 Api，並將會新增到所有的用戶端 Sdk 最後。 若要使用此其餘通話，只需新增查詢字串參數結尾的傳送通話中例如稱為 「 測試 」 

    https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test

*範例 (.NET SDK)*
 
假設您使用.NET SDK 傳送原生的快顯通知︰

    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
    var result = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(result.State);
 
`result.State`將只狀態`Enqueued`沒有任何深入了解找不到您的推入執行的結尾。 現在，您可以使用`EnableTestSend`初始化時的布林值屬性`NotificationHubClient`可以取得詳細的狀態相關 PNS 錯誤時傳送通知。 在傳送呼叫會額外的時間，因為它只會傳回之後 NH 已經傳遞到 PNS 來決定結果的通知。 
 
    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);
    
    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);
    
    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }

*輸出範例*

    DetailedStateAvailable
    windows
    7619785862101227384-7840974832647865618-3
    The Token obtained from the Token Provider is wrong
 
此訊息表示有不正確的認證設定在 [通知] 中心或集線器上註冊發生問題，但建議的課程就會刪除此登錄，並讓重新傳送郵件之前，先建立用戶端。 
 
> [AZURE.NOTE] 請注意，此屬性的使用大量經流速控制，因此您必須只使用此有限的登錄開發/測試環境中。 我們只會傳送到 10 裝置的偵錯通知。 我們也可以處理偵錯傳送為 10 每分鐘的限制。 

###<a name="review-telemetry"></a>檢閱遙測 

1. **使用 Azure 傳統入口網站**

    入口網站可讓您在 [您的通知中心上取得所有活動的快速概觀。 
    
    a） 從 [儀表板] 索引標籤中，您可以檢視進行之註冊、 通知，以及每個平台錯誤的彙總的檢視。 
    
    ![][5]
    
    b） 您也可以新增多個其他平台特定計量採取更深入查看特別時傳送通知給 PNS 嘗試 NH 傳回任何 PNS 特定錯誤的 「 監控 」] 索引標籤。 
    
    ![][6]
    
    c） 您應該開始檢閱**內送郵件**，**註冊作業****成功通知**，然後移至每個檢閱 PNS 特定錯誤的平台] 索引標籤。 
    
    d） 如果您有 [通知] 中心內使用 [驗證設定的設定錯誤，您會看到 PNS 驗證錯誤。 這是很好的指標，以檢查 PNS 認證。 

2) **以程式設計方式存取**

這裡有更多詳細資料 

- [以程式設計方式遙測存取]
- [遙測存取透過 Api 範例] 

> [AZURE.NOTE] 多個遙測相關功能將**匯出/匯入註冊**，例如只提供標準層**遙測透過 Api 的存取權**等等。 如果您嘗試使用這些功能，如果您是在免費或基本層然後您就會收到此效果的例外狀況訊息時使用直接從 REST Api 使用 SDK 和 HTTP 403 （禁止）。 請確定您已移至標準層透過 Azure 傳統入口網站。  

<!-- IMAGES -->
[0]: ./media/notification-hubs-diagnosing/Architecture.png
[1]: ./media/notification-hubs-diagnosing/GCMConfigure.png
[2]: ./media/notification-hubs-diagnosing/GCMEnable.png
[3]: ./media/notification-hubs-diagnosing/GCMServerKey.png
[4]: ./media/notification-hubs-diagnosing/PortalConfigure.png
[5]: ./media/notification-hubs-diagnosing/PortalDashboard.png
[6]: ./media/notification-hubs-diagnosing/PortalMonitoring.png
[7]: ./media/notification-hubs-diagnosing/PortalTestNotification.png
[8]: ./media/notification-hubs-diagnosing/VSRegistrations.png
[9]: ./media/notification-hubs-diagnosing/VSServerExplorer.png
[10]: ./media/notification-hubs-diagnosing/VSTestNotification.png
 
<!-- LINKS -->
[通知集線器概觀]: notification-hubs-push-notification-overview.md
[開始使用教學課程]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[範本指南]: https://msdn.microsoft.com/library/dn530748.aspx 
[APNS 指南]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW4
[GCM 指南]: http://developer.android.com/google/gcm/adv.html
[匯出/匯入註冊]: http://msdn.microsoft.com/library/dn790624.aspx
[ServiceBus 總管]: http://msdn.microsoft.com/library/dn530751.aspx
[ServiceBus 總管程式碼]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[與伺服器總管概觀]: http://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx 
[與伺服器總管部落格文章-1]: http://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs 
[與伺服器的檔案總管部落格文章 2]: http://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/ 
[EnableTestSend 功能]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.notificationhubclient.enabletestsend.aspx
[以程式設計方式遙測存取]: http://msdn.microsoft.com/library/azure/dn458823.aspx
[遙測存取透過 Api 範例]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel

 