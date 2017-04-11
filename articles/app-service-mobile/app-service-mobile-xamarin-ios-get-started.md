<properties
    pageTitle="快速入門 Xamarin.iOS 應用程式的 Azure 應用程式服務行動應用程式 |Microsoft Azure"
    description="請遵循此教學課程，瞭解如何開始使用 Xamarin.iOS 開發使用行動應用程式。"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>


#<a name="create-a-xamarinios-app"></a>建立 Xamarin.iOS 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##<a name="overview"></a>概觀

本教學課程教您如何使用 Azure 行動應用程式後端 Xamarin.iOS 行動應用程式新增雲端後端服務。  您建立新的行動應用程式後端和簡易_待辦清單_的應用程式的資料儲存在 Azure Xamarin.iOS 應用程式。

完成此教學課程是 Azure 應用程式服務中使用行動應用程式功能的相關所有其他 Xamarin.iOS 教學課程的必要條件。

##<a name="prerequisites"></a>必要條件

若要完成此教學課程中，您需要下列先決條件︰

* 使用中的 Azure 帳戶。 如果您沒有帳戶，請註冊 Azure 試用版，並取得最多 10 個可用的行動應用程式，您還是可以繼續使用您的試用期結束後，還是。 如需詳細資訊，請參閱[Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/)。

* Visual Studio 中使用 Xamarin。 如需相關指示，請參閱[設定和安裝的 Visual Studio 與 Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) 。

* Mac Xcode v7.0 或更新版本和 Xamarin Studio 社群安裝。 請參閱[設定與 Visual Studio 和 Xamarin 安裝](https://msdn.microsoft.com/library/mt613162.aspx)[設定、 安裝和 Mac 使用者驗證](https://msdn.microsoft.com/library/mt488770.aspx)(MSDN)。

>[AZURE.NOTE]如果您想要快速入門 Azure 應用程式服務註冊 Azure 帳戶之前，請移至[嘗試應用程式服務](https://tryappservice.azure.com/?appServiceName=mobile)。 您可以立即將短暫入門行動應用程式建立應用程式服務中，必要時，沒有信用卡和沒有承諾。

## <a name="create-an-azure-mobile-app-backend"></a>建立 Azure Mobile 應用程式後端

請遵循這些步驟來建立 Mobile 應用程式後端。

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="configure-the-server-project"></a>設定伺服器專案

現在有佈建後可讓您的行動用戶端應用程式 Azure Mobile 應用程式後端。 接下來，請下載伺服器專案進行簡單的 「 待辦清單 」 後端並將其發佈至 Azure。

請遵循下列步驟，設定要使用的 Node.js 」 或 「.NET 後端伺服器專案。

[AZURE.INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinios-app"></a>下載並執行 Xamarin.iOS 應用程式

1. 在瀏覽器視窗中開啟[Azure 入口網站]。

2. 在行動應用程式設定刀，按一下 [**開始使用** > **Xamarin.iOS**。 在 [步驟 3 中，按一下 [**建立新的應用程式**如果尚未選取。  接下來，請按一下 [**下載**] 按鈕。

    連線到您行動裝置的後端的用戶端應用程式會下載。 將壓縮的專案檔案儲存至本機電腦，並記下您儲存的位置。

3. 擷取您下載，專案，然後開啟它 Xamarin Studio （或 Visual Studio） 中。

    ![][9]

    ![][8]

4. 按 F5 鍵以建立專案，並在 iPhone 模擬器中啟動應用程式。

5. 在應用程式中，輸入有意義的文字，_瞭解 Xamarin_，例如，然後按一下**+**] 按鈕。

    ![][10]

    從要求資料插入 TodoItem 表格。 儲存在資料表中的項目所傳回的行動應用程式後端，資料會顯示在清單中。

>[AZURE.NOTE]您可以檢閱的程式碼，存取您的行動應用程式後端查詢及插入 QSTodoService.cs C# 檔案中的資料。

##<a name="next-steps"></a>後續步驟

* [新增至您的應用程式的 [離線同步處理](app-service-mobile-xamarin-ios-get-started-offline-data.md)
* [將驗證新增至您的應用程式](app-service-mobile-xamarin-ios-get-started-users.md)
* [新增至您 Xamarin.Android 應用程式的推播通知](app-service-mobile-xamarin-ios-get-started-push.md)
* [如何使用受管理的用戶端 Azure Mobile 應用程式](app-service-mobile-dotnet-how-to-use-client-library.md)

<!-- Anchors. -->
[Getting started with mobile app backends]:#getting-started
[Create a new mobile app backend]:#create-new-service
[Next Steps]:#next-steps

<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-ios-get-started/xamarin-ios-quickstart.png
[8]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Azure 入口網站]: https://portal.azure.com/
