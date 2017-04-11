<properties
    pageTitle="Azure Xamarin.Android 應用程式的行動應用程式快速入門"
    description="請遵循此教學課程以開始使用 Xamarin Android 開發 Azure 行動應用程式"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="erikre"
    editor="" />

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="adrianha" />

#<a name="create-a-xamarinandroid-app"></a>建立 Xamarin.Android 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##<a name="overview"></a>概觀

本教學課程中會顯示如何新增雲端後端服務 Xamarin.Android 應用程式。 如需詳細資訊，請參閱[什麼是行動應用程式](app-service-mobile-value-prop.md)。

下方，為已完成的應用程式的螢幕擷取畫面︰

![][0]

完成此教學課程是所有其他行動應用程式的教學課程 Xamarin.Android 應用程式的必要條件。

##<a name="prerequisites"></a>必要條件

若要完成此教學課程中，您需要下列先決條件︰

* 使用中的 Azure 帳戶。 如果您沒有帳戶，請註冊 Azure 試用版，並取得最多 10 個可用的行動應用程式。 如需詳細資訊，請參閱[Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/)。

* Visual Studio 中使用 Xamarin。 如需相關指示，請參閱[設定和安裝的 Visual Studio 與 Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) 。

>[AZURE.NOTE]如果您想要開始使用 Azure 應用程式服務註冊 Azure 帳戶之前，請[嘗試應用程式服務](https://tryappservice.azure.com/?appServiceName=mobile)。  您可以立即建立短暫入門 Mobile 應用程式，在應用程式服務。 必要; 沒有信用卡沒有承諾。

## <a name="create-an-azure-mobile-app-backend"></a>建立 Azure Mobile 應用程式後端

請遵循這些步驟來建立 Mobile 應用程式後端。

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

現在有佈建後可讓您的行動用戶端應用程式 Azure Mobile 應用程式後端。 接下來，請下載伺服器專案進行簡單的 「 待辦清單 」 後端並將其發佈至 Azure。

## <a name="configure-the-server-project"></a>設定伺服器專案

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinandroid-app"></a>下載並執行 Xamarin.Android 應用程式

1. **下載並執行 Xamarin.Android 專案**，請按一下 [**下載**] 按鈕。

    將壓縮的專案檔案儲存至本機電腦，並記下您儲存的位置。

2. 按**F5**鍵以建立專案並啟動應用程式。

3. 在應用程式中，輸入有意義的文字，例如_完成教學課程_，然後按一下 [**新增**] 按鈕。

    ![][10]

    從要求資料插入 TodoItem 表格。 儲存在資料表中的項目所傳回的行動應用程式後端，並顯示在清單中的資料。

    > [AZURE.NOTE] 您可以檢閱的程式碼，存取您的行動應用程式後端查詢及插入 ToDoActivity.cs C# 檔案中找到的資料。

##<a name="next-steps"></a>後續步驟

* [新增至您的應用程式的 [離線同步處理](app-service-mobile-xamarin-android-get-started-offline-data.md)
* [將驗證新增至您的應用程式](app-service-mobile-xamarin-android-get-started-users.md)
* [新增至您 Xamarin.Android 應用程式的推播通知](app-service-mobile-xamarin-android-get-started-push.md)
* [如何使用受管理的用戶端 Azure Mobile 應用程式](app-service-mobile-dotnet-how-to-use-client-library.md)


<!-- Images. -->
[0]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/app-service-mobile-xamarin-android-get-started/mobile-portal-quickstart-xamarin.png
[8]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-vs.png
[9]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-xs.png
[10]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-startup-android.png

<!-- URLs. -->
[Azure Portal]: https://azure.portal.com/
[Visual Studio]: https://go.microsoft.com/fwLink/p/?LinkID=534203
