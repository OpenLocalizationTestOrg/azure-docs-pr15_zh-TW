<properties
    pageTitle="Azure 通知集線器"
    description="瞭解如何使用 Azure 中的推播通知。 以 C# 使用.NET API 撰寫程式碼範例。"
    authors="ysxu"
    manager="erikre"
    editor=""
    services="notification-hubs"
    documentationCenter=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="multiple"
    ms.devlang="multiple"
    ms.topic="hero-article"
    ms.date="08/25/2016"
    ms.author="yuaxu"/>


#<a name="azure-notification-hubs"></a>Azure 通知集線器

##<a name="overview"></a>概觀

Azure 通知集線器提供容易使用多平台、 縮放出推入基礎結構的任何行動裝置平台可讓您從任何後端 （在內部部署或雲端） 傳送 mobile 推入通知。

使用通知集線器，您可以輕鬆地傳送跨平台、 個人化的推入通知，抽象化不同的平台通知系統 (PNS) 的詳細資料。 使用單一的 API 通話，您可以針對個別使用者或整份簡報對象區段包含數百萬個使用者，請在所有裝置。

您可以使用企業和消費者案例通知集線器。 例如︰

- 傳送新新聞通知給數百萬低的延遲 （通知集線器力量 Bing Windows 和 Windows Phone 的所有裝置上已預先安裝的應用程式）。
- 使用者線段傳送位置為基礎的優待券。
- 將事件通知傳送給使用者或群組的運動/財務遊戲應用程式。
- 通知的企業事件，例如新郵件/電子郵件，以及潛在的使用者。
- 傳送其中之一-單次密碼所需的多重因素驗證。



##<a name="what-are-push-notifications"></a>什麼是推播通知？

智慧型手機和平板電腦可以 」 「 時通知使用者發生事件。 這些通知很多種方式。

Windows 市集和 Windows Phone 應用程式中，用通知，可在表單中的_快顯_︰ 強制回應的視窗隨即出現，與音效]，表示新訊息通知。 支援的其他告知類型包含_並排顯示__原始_，與_徽章_的通知。 在 Windows 裝置上支援的通知的類型的詳細資訊，請參閱[磚、 徽章和通知](http://msdn.microsoft.com/library/windows/apps/hh779725.aspx)。

在 Apple iOS 裝置上推入同樣會通知使用者的對話方塊中，要求使用者檢視或關閉通知。 按一下 [**檢視**開啟所收到的郵件應用程式。 如需有關 iOS 通知的詳細資訊，請參閱[iOS 通知](http://go.microsoft.com/fwlink/?LinkId=615245)。

推播通知協助顯示最新的資訊時剩餘能源的行動裝置。 行動裝置可以通知傳送的後端系統，即使在裝置上的對應應用程式不是作用中。 推播通知是消費者應用程式，相當元件位置用來增加應用程式的互動及使用方式。 通知，也會對企業，有幫助時最新版本的資訊會增加商務事件的員工回應能力。

行動裝置的互動情況的一些特定的範例是︰

1.  更新 Windows 8 或 Windows Phone 上的磚目前的財務資訊。
2.  警示與快顯某些工作項目已指派給該使用者，在工作流程為基礎的企業應用程式中的使用者。
3.  顯示目前的銷售數字與徽章引導 CRM 應用程式 （例如 Microsoft Dynamics CRM) 中。

##<a name="how-push-notifications-work"></a>如何推播通知工作

推播通知傳送到稱為_平台通知系統_(PNS) 的特定基礎結構。 PNS 提供最起碼函數 （也就不是支援個人化廣播），並沒有任何一般介面。 例如，若要傳送的 Windows 市集應用程式通知，開發人員必須連絡 WNS （Windows 通知服務）。 若要在 iOS 裝置中傳送通知，相同的開發人員必須連絡 APNS （Apple 推入通知服務），並傳送訊息一次。 Azure 通知集線器協助提供常見的介面，以及其他功能，在每個平台支援推播通知。

不過，在高的層級，所有的平台通知系統遵循相同的模式︰

1.  用戶端應用程式連絡人以擷取其_處理_PNS。 控點類型取決於系統。 若是 WNS，則 URI 或 「 通知頻道]。 若是 APNS，則權杖。
2.  用戶端應用程式會將此控點儲存應用程式_後端_的更新版本的使用方式。 後端的 WNS，通常是雲端服務。 Apple，系統稱為_提供者_。
3.  若要傳送推入通知，應用程式後端連絡人 PNS 目標特定的用戶端應用程式執行個體使用控點。
4.  PNS 轉寄通知控點來指定的裝置。

![][0]

##<a name="the-challenges-of-push-notifications"></a>推播通知的挑戰

這些系統很強大，他們仍離開的工作量應用程式開發人員才能實作甚至常見推入通知案例中，例如廣播或推入通知傳送至 [區段的使用者。

推播通知是在行動應用程式的雲端服務中的最要求功能。 原因是，使其工作所需的基礎結構很複雜，而且通常不相關的應用程式的主要的商務邏輯。 建立視推入基礎結構的挑戰如下︰

- **平台相依性。** 以不同的平台上的裝置傳送通知，請多個介面必須在後端撰寫程式碼。 不只是低層級的詳細資料的不同，但通知 （磚、 快顯或徽章） 的簡報也是平台相關。 這些變更可能會造成複雜和難維護的後端程式碼。

- **小數位數。** 縮放比例基礎結構有兩個重點︰
    + 每個 PNS 指導方針，每次啟動應用程式，必須重新整理裝置權杖。 這會導致大量的只是為了保持在最新裝置權杖流量 （和連續資料庫存取）。 裝置的數目會增加 （可能是以百萬），建立和維護此基礎結構的成本時 nonnegligible。

    + 大部分的 PNSs 不支援廣播到多個裝置。 看來數百萬裝置廣播結果中的來電至 PNSs 數百萬。 能夠不按比例縮放這些要求，所以重要，通常應用程式開發人員想要保留的總延遲向下。 例如，接收郵件的最後一個裝置應該不會收到通知傳送通知之後 30 分鐘，先許多情況下，等於有推入通知的用途。
- **路由。** PNSs 提供傳送郵件給裝置。 不過，大部分的應用程式中通知對象是使用者及/或感興趣的群組 （例如，所有員工指派給特定客戶帳戶）。 因此，才能將通知傳送到正確的裝置，應用程式後端必須維護登錄使感興趣的群組與裝置權杖。 此開銷會新增至應用程式的市場及維護成本的總時間。

##<a name="why-use-notification-hubs"></a>為什麼要使用通知集線器？

集線器排除複雜度通知︰ 您沒有管理推入通知的挑戰。 不過，您可以使用通知中心。 通知集線器使用完整多平台、 縮放出推入通知基礎結構，並大幅降低執行應用程式後端的推入特定程式碼。 通知集線器實作推入基礎結構的所有功能。 裝置只負責註冊 PNS 控點，而後端負責傳送平台獨立郵件使用者或感興趣的群組，如下圖所示︰

![][1]


通知集線器提供準備使用推入通知基礎架構與下列優點︰

- **多個平台。**
    +  所有的主要行動裝置平台支援。 推播通知可以傳送通知集線器 Windows 市集、 iOS、 Android 和 Windows Phone 應用程式。

    +  通知集線器提供常見的介面傳送通知給所有受支援的平台。 特定通訊協定且並非必要。 應用程式後端可以傳送通知平台特定或平台無關的格式。 應用程式只會與通知集線器通訊。

    +  裝置控點的管理。 通知集線器維護控點登錄和從 PNSs 的意見反應。

- **適用於任何後端**︰ 雲端或內部部署，.NET、 PHP、 Java 節點，等等。

- **小數位數。** 通知集線器不按比例縮放的裝置，而不需要重新設計或晶怪。


- **Rtf 設定的傳遞模式**︰

    - *廣播*︰ 可讓數百萬單一 API 呼叫裝置靠近同時廣播。

    - *單/多點傳送*︰ 推入標籤代表個別的使用者，包括所有的裝置。或更多的群組。例如，另一個表單係數 （電話與平板電腦）。

    - *分割*︰ 推入標籤運算式 （例如，在追蹤洋基隊紐約裝置） 來定義複雜的區段。

    每個裝置，將其控點傳送到 [通知] 中心內時, 可以指定一個或多個_標籤_。 如需有關[標籤]的詳細資訊。 預先佈建後或處置沒有標籤。 標籤會提供一個簡單的方法，傳送通知給使用者或感興趣的群組。 因為標籤可以包含任何應用程式特定的識別項 （例如使用者或群組識別碼），使用釋放應用程式後端從的不必儲存和管理裝置控點。

- **個人化**︰ 每個裝置有一或多個範本，以達到每個裝置已進行本地化和個人化，而不會影響後端程式碼。

- **安全性**︰ 存取私人 (SA) 或同盟的驗證共用。

- **Rtf 遙測**︰ 可在入口網站以程式設計方式。


##<a name="integration-with-app-service-mobile-apps"></a>整合應用程式服務行動應用程式

若要透過 Azure 服務加速順暢和統一的體驗，[應用程式服務行動應用程式]會有內建支援使用通知集線器推播通知。 [應用程式服務行動應用程式]提供彈性，全域可用的行動應用程式開發平台的企業開發人員與系統整合，讓行動開發人員豐富的功能。

行動應用程式開發人員可以利用通知集線器使用下列的工作流程︰

1. 擷取裝置 PNS 控點
2. 通知集線器，好方便您的行動應用程式的用戶端 SDK register API 透過中註冊裝置與[範本]
    + 請注意，Mobile 應用程式階梯狀離開所有標籤上的安全性考量註冊。 使用通知集線器，從您的後端直接來建立標籤與裝置之間的關聯。
3. 從您的應用程式後端與通知集線器傳送通知

以下是一些便利引薦此整合的開發人員︰

- **行動應用程式的用戶端 Sdk。** 這些多平台 Sdk 註冊提供簡單的 Api 和洽詢自動連結行動應用程式的 [通知] 中心。 開發人員不需要挖通知集線器認證，並與其他服務共同作業。
    + Sdk 自動標記與 Mobile 應用程式已驗證的使用者識別碼啟用使用者案例的推入指定的裝置。
    + Sdk 自動使用行動應用程式安裝 ID 為 GUID 登錄通知集線器，儲存開發人員維護多個服務 Guid 的問題。
    
- **安裝模型。** 行動應用程式搭配通知集線器的最新推入模型，代表所有中 JSON 安裝的推入通知服務與對齊且易於使用的裝置與相關聯的推入屬性。

- **彈性。** 開發人員可以隨時使用直接通知集線器，即使使用中的位置的整合。

- **在[Azure 入口網站]整合後的體驗。** 推播功能以視覺化方式呈現在行動應用程式中，並用於開發人員可以輕鬆地處理相關聯的通知中心，透過行動應用程式。



##<a name="next-steps"></a>後續步驟

您可以瞭解更多有關通知集線器這些主題︰

+ **[客戶如何使用通知集線器]**

+ **[通知集線器教學課程及輔助線]**

+ **通知集線器開始教學課程**([iOS]， [Android]、[標準 Windows]、 [Windows Phone]、 [Kindle]、 [Xamarin.iOS]、 [Xamarin.Android])

相關的.NET 受管理的 API 參照的推入通知可以在這裡找到︰

+ [Microsoft.WindowsAzure.Messaging.NotificationHub]
+ [Microsoft.ServiceBus.Notifications]


  [0]: ./media/notification-hubs-overview/registration-diagram.png
  [1]: ./media/notification-hubs-overview/notification-hub-diagram.png
  [客戶如何使用通知集線器]: http://azure.microsoft.com/services/notification-hubs
  [通知集線器教學課程及輔助線]: http://azure.microsoft.com/documentation/services/notification-hubs
  [iOS]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started
  [Android]: http://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started
  [Windows 標準]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started
  [在 Windows Phone]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started
  [Kindle]: http://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started
  [Xamarin.iOS]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
  [Xamarin.Android]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started
  [Microsoft.WindowsAzure.Messaging.NotificationHub]: http://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx
  [Microsoft.ServiceBus.Notifications]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx
  [應用程式服務行動應用程式]: https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-value-prop/
  [範本]: notification-hubs-templates-cross-platform-push-messages.md
  [Azure 入口網站]: https://portal.azure.com
  [標記]: (http://msdn.microsoft.com/library/azure/dn530749.aspx)
