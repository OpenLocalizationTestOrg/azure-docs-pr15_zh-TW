<properties
    pageTitle="路由和標籤運算式"
    description="本主題說明路由和標記 Azure 通知集線器的運算式。"
    services="notification-hubs"
    documentationCenter=".net"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="routing-and-tag-expressions"></a>路由與標籤運算式

##<a name="overview"></a>概觀

標記運算式可讓您的裝置或更明確地說登錄的目標特定組時傳送通知集線器透過推入通知。


## <a name="targeting-specific-registrations"></a>針對特定的註冊

目標的唯一方法特定通知登錄會建立關聯標籤，然後目標這些標籤。 [註冊管理](notification-hubs-push-notification-registration-management.md)所述，才會收到推入通知應用程式有註冊裝置上的控點的通知中心。 一旦在通知中心建立註冊，應用程式後端可以傳送推入通知。
應用程式後端可以選擇特定的通知與目標登錄下列方式︰

1. **廣播**︰ 所有登錄，在 [通知] 中心會都收到通知。
2. **標記**︰ 包含指定的標籤的所有登錄會都收到通知。
3. **標記運算式**︰ 所有登錄其設定的標籤符合指定的運算式會都收到通知。

## <a name="tags"></a>標記

標籤可以是任何字串，最多 120，包含英數字元及下列非英數字元: '_'， ‘@’, '#'，'。 」、 「: 「，」-'。 下列範例會顯示您可以接收關於特定的音樂群組的快顯通知的應用程式。 在此案例中，傳送通知的簡單方法是標籤進行之註冊代表不同的圖示，如如下圖所示的標籤。

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags.png)

在此圖片]，在訊息標記的**披頭四**到達登錄的標籤**披頭四**平板電腦。

如需有關如何建立標籤註冊的詳細資訊，請參閱[註冊管理](notification-hubs-push-notification-registration-management.md)。

您可以傳送通知給標籤使用的傳送通知方法`Microsoft.Azure.NotificationHubs.NotificationHubClient` [Microsoft Azure 通知集線器](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)SDK 中的類別。 您也可以使用 Node.js 或推播通知 REST Api。  以下是使用 SDK 範例。


    Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

    // Windows 8.1 / Windows Phone 8.1
    var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
    "You requested a Beatles notification</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Beatles");

    // Windows 10
    toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
    "You requested a Wailers notification</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Wailers");




標記沒有為預先能夠與可以參照多個特定應用程式的概念。 例如，此範例應用程式的使用者可以註解圖示，並想要從其朋友，無論在他們的註解頻接收 toasts，而不只是在其最愛的帶狀，註解的所有註解。 下圖顯示這種情況的範例︰



![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags2.png)

在此圖片]，Alice 感興趣的更新披頭四，而百勝會感興趣的 Wailers 更新。 百勝也是他的註解，想要與他位於 Wailers 有興趣。 通知傳送時加上披頭四他的註解的同時愛麗絲 」 和 「 鮑收到該資料。

雖然您可以編碼標籤 （例如，「 band_Beatles 」 或 「 follows_Charlie 」） 中的多個疑慮，標籤是簡單的字串與不含有值的屬性。 只在特定標籤存在相符，註冊。

如何使用標籤將傳送給感興趣的群組的完整逐步教學課程，請參閱[中斷消息](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)。


## <a name="using-tags-to-target-users"></a>使用目標使用者的標籤

使用標籤的另一種方式是識別特定使用者的所有裝置。 登錄可以標記與標記包含的使用者識別碼，如如下圖所示︰


![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags3.png)

此圖片]，將郵件標記 uid:Alice 達到所有登錄標記的 uid:Alice;因此，所有 Alice 的裝置。


##<a name="tag-expressions"></a>標記運算式

有通知對目標一組登錄識別不是單一標籤，但在標籤上的布林運算式的情況。

請考慮傳送提醒給所有人在給遊戲紅色 Sox 與紅雀隊之間的相關的運動應用程式。 如果用戶端應用程式登錄小組和位置的興趣相關的標籤，通知應該針對在有興趣紅色 Sox 或紅雀隊的每個人。 使用下列的布林運算式可以表示這種情況︰

    (follows_RedSox || follows_Cardinals) && location_Boston


![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags4.png)

標記運算式最多可以包含所有的布林值運算子，例如 AND (& &)，或者 (|)，而不 （！）。 也可以包含括號括住。 標記運算式限於 20 標籤包含只 ORs;否則，其受限於 6 的標籤。

以下是傳送通知與使用 SDK 的標籤運算式的範例。


    Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

    String userTag = "(location_Boston && !follows_Cardinals)"; 

    // Windows 8.1 / Windows Phone 8.1
    var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
    "You want info on the Red Socks</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

    // Windows 10
    toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
    "You want info on the Red Socks</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
