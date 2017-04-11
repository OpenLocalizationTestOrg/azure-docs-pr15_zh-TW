<properties
    pageTitle="推播通知加入 Azure 行動應用程式在 Android 應用程式"
    description="瞭解如何使用 Azure 行動應用程式傳送推入通知給您的 Android 應用程式。"
    services="app-service\mobile"
    documentationCenter="android"
    manager="erikre"
    editor=""
    authors="ysxu"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="yuaxu"/>

# <a name="add-push-notifications-to-your-android-app"></a>新增至您的 Android 應用程式的推播通知

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>概觀
在本教學課程中，讓每次即會插入一筆記錄，將會傳送至裝置的推入通知 」，推入通知新增到[Android 的快速入門]專案。

如果您不使用下載快速入門伺服器專案，您會需要推播通知擴充套件。 如需詳細資訊，請參閱[使用.NET 後端伺服器 SDK Azure Mobile 應用程式](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。

## <a name="prerequisites"></a>必要條件

您需要︰

* 根據您的專案後端 IDE:

    * [Android Studio](https://developer.android.com/sdk/index.html)如果此應用程式有 Node.js 後端。

    * [Visual Studio 社群 2013年](https://go.microsoft.com/fwLink/p/?LinkID=391934)或更新版本，此應用程式有.Net 後端。

* Android 2.3 或更新版本、 Google 存放庫修訂 27 或更新版本和 Google Play 服務 9.0.2 或更高的 Firebase 雲端通訊功能。

* 完成[Android 的快速入門]。

## <a name="create-a-project-that-supports-firebase-cloud-messaging"></a>建立專案的支援 Firebase 雲端訊息

[AZURE.INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>設定通知中心

[AZURE.INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="configure-azure-to-send-push-notifications"></a>設定 Azure 傳送推入通知

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="enable-push-notifications-for-the-server-project"></a>啟用伺服器專案的推播通知

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## <a name="add-push-notifications-to-your-app"></a>新增至您的應用程式的 [推入通知

在此區段中，您可以更新用戶端 Android 應用程式以處理推入通知。

### <a name="verify-android-sdk-version"></a>確認 Android SDK 版本

[AZURE.INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

下一步是安裝 Google Play 服務。 Google 雲端訊息具有最低 API 層級的一些需求開發和測試，必須符合資訊清單中的 [ **minSdkVersion** ] 屬性。

如果您正在測試與較舊的裝置，請[設定設定 Google 播放服務 SDK]來決定如何低您可以設定此值，並將它設定妥當之後，請參閱。

### <a name="add-google-play-services-to-the-project"></a>新增 Google 播放服務至專案

[AZURE.INCLUDE [Add Play Services](../../includes/app-service-mobile-add-google-play-services.md)]

### <a name="add-code"></a>新增程式碼

[AZURE.INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## <a name="test-the-app-against-the-published-mobile-service"></a>測試針對已發佈的行動訊息服務應用程式

直接附加在 Android 手機上的使用 USB 纜線，或在模擬器中使用虛擬裝置，您可以測試應用程式。

## <a name="more"></a>更多

<!-- URLs -->
[Android 的快速入門]: app-service-mobile-android-get-started.md

[Google Play 服務 SDK 設定]:https://developers.google.com/android/guides/setup
