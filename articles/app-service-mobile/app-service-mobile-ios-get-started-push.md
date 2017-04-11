<properties
    pageTitle="推播通知加入 iOS Azure 行動應用程式的應用程式"
    description="瞭解如何使用 Azure 行動應用程式傳送推入通知給您的 iOS 應用程式。"
    services="app-service\mobile"
    documentationCenter="ios"
    manager="yochayk"
    editor=""
    authors="ysxu"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="yuaxu"/>


# <a name="add-push-notifications-to-your-ios-app"></a>新增至您的 iOS 應用程式的 [推入通知

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>概觀
在本教學課程中，您專案中新增推入通知[iOS 快速開始]，讓每次即會插入一筆記錄，將會傳送至裝置的推入通知。

如果您不使用下載快速入門伺服器專案，您需要推播通知擴充套件。 如需詳細資訊，請參閱[使用.NET 後端伺服器 SDK Azure Mobile 應用程式](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。

[IOS 模擬器不支援推播通知](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html)。 您需要在實體 iOS 裝置和[Apple 開發人員程式成員資格](https://developer.apple.com/programs/ios/)。

##<a name="configure-hub"></a>設定通知中心

[AZURE.INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>推播通知 register 應用程式

[AZURE.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-azure-to-send-push-notifications"></a>設定 Azure 傳送推入通知

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

##<a id="update-server"></a>更新後端傳送推入通知

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a id="add-push"></a>新增至應用程式的推播通知

[AZURE.INCLUDE [app-service-mobile-add-push-notifications-to-ios-app.md](../../includes/app-service-mobile-add-push-notifications-to-ios-app.md)]

## <a id="test"></a>測試推入通知

[AZURE.INCLUDE [Test Push Notifications in App](../../includes/test-push-notifications-in-app.md)]

##<a id="more"></a>更多

* 範本會提供您傳送跨平台推入和本地化推入的彈性。 [如何使用 iOS Azure Mobile 應用程式的用戶端文件庫](app-service-mobile-ios-how-to-use-client-library.md#templates)會顯示如何註冊範本。

<!-- Anchors.  -->

<!-- Images. -->

<!-- URLs. -->
[iOS 快速入門]: app-service-mobile-ios-get-started.md
