<properties
    pageTitle="推播通知加入 Xamarin.Android 應用程式 |Azure 應用程式服務"
    description="瞭解如何使用 Azure 應用程式服務和 Azure 通知集線器 Xamarin.Android 應用程式傳送推入通知"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="yuaxu"/>

# <a name="add-push-notifications-to-your-xamarinandroid-app"></a>新增至您 Xamarin.Android 應用程式的推播通知

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##<a name="overview"></a>概觀


在本教學課程中，您專案中新增推入通知[Xamarin.Android 快速開始](app-service-mobile-windows-store-dotnet-get-started.md)，讓每次即會插入一筆記錄，將會傳送至裝置的推入通知。

如果您不使用下載快速入門伺服器專案，您需要推播通知擴充套件。 如需詳細資訊，請參閱[使用.NET 後端伺服器 SDK Azure Mobile 應用程式](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。


##<a name="prerequisites"></a>必要條件

本教學課程的需求如下︰

+ 使用中的 Google 帳戶。 您可以在[accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302)Google 帳戶登入。
+ [Google 雲端訊息用戶端元件](http://components.xamarin.com/view/GCMClient/)。

##<a name="configure-hub"></a>設定通知中心

[AZURE.INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

##<a id="register"></a>啟用 Firebase 雲端訊息

[AZURE.INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

##<a name="configure-azure-to-send-push-requests"></a>設定 Azure 傳送推入要求

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

##<a id="update-server"></a>更新伺服器專案可傳送推入通知

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##<a id="configure-app"></a>設定推入通知的用戶端專案

[AZURE.INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

##<a id="add-push"></a>將推入通知程式碼新增至您的應用程式

[AZURE.INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="test"></a>測試推播通知在您的應用程式

您可以在模擬器中使用虛擬裝置測試應用程式。 有模擬器上執行時所需的其他設定步驟。

1. 請確定您是部署或偵錯時，如下所示，在 Android 虛擬裝置 (AVD) 管理員已設定為目標，Google Api 虛擬裝置上。

    ![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)

2. 按一下 [**應用程式**，將 Google 帳戶新增至 Android 裝置 > **設定** > **新增帳戶**]，然後依照提示操作。

    ![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)

3. 執行 todolist 應用程式為之前，插入新的 todo 項目。 這次請通知圖示會顯示在通知區域。 您可以開啟通知抽屜，若要檢視通知的完整的文字。

    ![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)


<!-- URLs. -->
[Xamarin.Android quick start]: app-service-mobile-xamarin-android-get-started.md
[Google Cloud Messaging Client Component]: http://components.xamarin.com/view/GCMClient/
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
