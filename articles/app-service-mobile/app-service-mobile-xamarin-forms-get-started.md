<properties
    pageTitle="開始使用行動應用程式使用 Xamarin.Forms"
    description="請遵循此教學課程以開始使用 Xamarin.Forms 開發 Azure 行動應用程式"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

#<a name="create-a-xamarinforms-app"></a>建立 Xamarin.Forms 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##<a name="overview"></a>概觀

本教學課程教您如何將雲端後端服務新增至 Xamarin.Forms 的行動應用程式，使用 Azure Mobile 應用程式後端。 您會建立新的行動應用程式後端和簡易_待辦清單_的應用程式的資料儲存在 Azure Xamarin.Forms 應用程式。

完成此教學課程是所有其他行動應用程式的教學課程 Xamarin.Forms 的必要條件。

##<a name="prerequisites"></a>必要條件

若要完成此教學課程中，您需要下列項目︰

* 使用中的 Azure 帳戶。 如果您沒有帳戶，您可以註冊 Azure 試用版，並取得最多 10 免費您還是可以繼續使用您的試用期結束後，還是的行動應用程式。 如需詳細資訊，請參閱[Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/)。

* Visual Studio 中使用 Xamarin。 如需相關指示，請參閱[設定和安裝的 Visual Studio 與 Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) 。 

* Mac Xcode v7.0 或更新版本和 Xamarin Studio 社群安裝。 請參閱[設定與 Visual Studio 和 Xamarin 安裝](https://msdn.microsoft.com/library/mt613162.aspx)[設定、 安裝和 Mac 使用者驗證](https://msdn.microsoft.com/library/mt488770.aspx)(MSDN)。
 
>[AZURE.NOTE] 如果您想要開始使用 Azure 應用程式服務註冊 Azure 帳戶之前，請移至[嘗試應用程式服務](https://tryappservice.azure.com/?appServiceName=mobile)，可以讓您立即建立短暫入門 Mobile 應用程式在應用程式服務。 必要; 沒有信用卡沒有承諾。

## <a name="create-a-new-azure-mobile-app-backend"></a>建立新的 Azure Mobile 應用程式後端

請遵循這些步驟來建立新的行動應用程式後端。

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]


現在有佈建後可讓您的行動用戶端應用程式 Azure Mobile 應用程式後端。 接下來，您將下載伺服器專案進行簡單的 「 待辦清單 」 後端並將其發佈至 Azure。

## <a name="configure-the-server-project"></a>設定伺服器專案

請遵循下列步驟來設定要使用的 Node.js 」 或 「.NET 後端伺服器專案。

[AZURE.INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

##<a name="download-and-run-the-xamarinforms-solution"></a>下載並執行 Xamarin.Forms 解決方案

這裡，您有幾個選項。 您可以下載 mac 的方案，並在 Xamarin Studio 中，將其開啟，或您可以下載 Windows 電腦上的方案，並在 Visual Studio 中使用網路的 Mac 建置 iOS 應用程式中開啟。 如果您需要 Xamarin 設定案例的詳細的指示，請參閱[設定]，然後安裝的 Visual Studio 與 Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) 。

現在就讓我們進行︰

 1. 在 Mac 或 Windows 電腦上，請在瀏覽器視窗中開啟[Azure 入口網站]。
 2. 在行動應用程式設定刀，按一下 [（在 [行動電話） 底下的 [**快速入門**> **Xamarin.Forms**。 在 [步驟 3 中，按一下 [**建立新的應用程式**如果尚未選取。  接下來，請按一下 [**下載**] 按鈕。

    這下載包含已連線至您的行動應用程式的用戶端應用程式的專案。 將壓縮的專案檔案儲存至本機電腦，並記下您儲存的位置。

 3. 擷取您下載，專案，然後開啟它 Xamarin Studio 或 Visual Studio 中。

    ![][9]

    ![][8]


##<a name="optional-run-the-ios-project"></a>（選用）執行 iOS 專案

本節適用於執行 iOS 裝置的 Xamarin iOS 專案。 如果您不使用 iOS 裝置，您可以略過此節。

####<a name="in-xamarin-studio"></a>在 Xamarin Studio 中

1. IOS 專案，以滑鼠右鍵按一下，然後按一下 [**設定為啟動專案**。
2. 在 [**執行**] 功能表中，按一下 [**啟動偵錯**建置專案，並在 iPhone 模擬器中啟動應用程式。

####<a name="in-visual-studio"></a>在 Visual Studio 中
1. IOS 專案，以滑鼠右鍵按一下，然後按一下 [**設定為啟動專案**。
2. 在 [**建置**] 功能表上按一下 [**組態管理員**]。
3. 在 [**組態管理員**] 對話方塊中，選取的 iOS 專案的**建立**並**部署**核取方塊。
4. 按**F5**鍵以建立專案，並在 iPhone 模擬器中啟動應用程式。

    >[AZURE.NOTE] 如果您無法建立，執行 NuGet 封裝管理員和更新為最新版本的 Xamarin 支援套件。 有時快速入門專案可能會在最新更新後延遲。    

在應用程式中，輸入有意義的文字，例如_瞭解 Xamarin_ ，然後按一下**+**] 按鈕。

![][10]

這會裝載於 Azure 新的行動應用程式後端傳送文章要求。 從要求資料插入 TodoItem 表格。 儲存在資料表中的項目所傳回的行動應用程式後端，資料會顯示在清單中。

>[AZURE.NOTE]
> 您可以找到存取您的行動應用程式後端，在您的方案課程可攜式文件庫專案 TodoItemManager.cs C# 檔案的程式碼。

##<a name="optional-run-the-android-project"></a>（選用）執行 Android 的專案

本節適用於 Android 版執行 Xamarin droid 專案。 如果您不使用 Android 裝置，您可以略過此節。

####<a name="in-xamarin-studio"></a>在 Xamarin Studio 中

1. Android 的專案，以滑鼠右鍵按一下，然後按一下 [**設定為啟動專案**。
2. 在 [**執行**] 功能表中，按一下 [**啟動偵錯**建置專案，並啟動應用程式在 Android 模擬器中。

####<a name="in-visual-studio"></a>在 Visual Studio 中
1. Android (Droid) 專案，以滑鼠右鍵按一下，然後按一下 [**設定為啟動專案**。
4. 在 [**建置**] 功能表上按一下 [**組態管理員**]。
5. 在 [**組態管理員**] 對話方塊中，選取在 Android 專案的**建立**並**部署**核取方塊。
6. 按**F5**鍵以建立專案並啟動應用程式在 Android 模擬器中。

    >[AZURE.NOTE] 如果您無法建立，執行 NuGet 封裝管理員和更新為最新版本的 Xamarin 支援套件。 有時快速入門專案可能會在最新更新後延遲。    


在應用程式中，輸入有意義的文字，例如_瞭解 Xamarin_ ，然後按一下**+**] 按鈕。

![][11]

這會裝載於 Azure 新的行動應用程式後端傳送文章要求。 從要求資料插入 TodoItem 表格。 儲存在資料表中的項目所傳回的行動應用程式後端，資料會顯示在清單中。

> [AZURE.NOTE]
> 您可以找到存取您的行動應用程式後端，在您的方案課程可攜式文件庫專案 TodoItemManager.cs C# 檔案的程式碼。


##<a name="optional-run-the-windows-project"></a>（選用）執行 Windows 的專案


本節適用於執行 Windows 裝置的 Xamarin WinApp 專案。 如果您不使用 Windows 裝置，您可以略過此節。


####<a name="in-visual-studio"></a>在 Visual Studio 中
1. 以滑鼠右鍵按一下任何 Windows 專案，並按一下 [**設為啟動專案。**
4. 在 [**建置**] 功能表上按一下 [**組態管理員**]。
5. 在 [**組態管理員**] 對話方塊中，選取您所選擇的 Windows 專案的**建立**並**部署**核取方塊。
6. 按**F5**鍵以建立專案，以及 Windows 模擬器中啟動應用程式。

    >[AZURE.NOTE] 如果您無法建立，執行 NuGet 封裝管理員和更新為最新版本的 Xamarin 支援套件。 有時快速入門專案可能會在最新更新後延遲。    


在應用程式中，輸入有意義的文字，例如_瞭解 Xamarin_ ，然後按一下**+**] 按鈕。

這會裝載於 Azure 新的行動應用程式後端傳送文章要求。 從要求資料插入 TodoItem 表格。 儲存在資料表中的項目所傳回的行動應用程式後端，資料會顯示在清單中。

![][12]

> [AZURE.NOTE]
> 您可以找到存取您的行動應用程式後端，在您的方案課程可攜式文件庫專案 TodoItemManager.cs C# 檔案的程式碼。

##<a name="next-steps"></a>後續步驟

* [將驗證新增至您的應用程式](app-service-mobile-xamarin-forms-get-started-users.md)  
瞭解如何將項目會驗證您的應用程式與身分識別提供者的使用者。

* [新增至您的應用程式的 [推入通知](app-service-mobile-xamarin-forms-get-started-push.md)  
瞭解如何新增推入通知支援新增至您的應用程式，並設定您的行動應用程式後端使用 Azure 通知集線器傳送推入通知。

* [啟用離線同步處理應用程式](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  瞭解如何新增離線支援您使用行動應用程式後端的應用程式。 離線同步處理可讓使用者能在行動應用程式與&mdash;檢視、 新增或修改資料&mdash;即使沒有網路連線。

* [如何使用受管理的用戶端 Azure Mobile 應用程式](app-service-mobile-dotnet-how-to-use-client-library.md)  
瞭解如何使用受管理用戶端 SDK Xamarin 應用程式中。 


<!-- Anchors. -->
[Getting started with mobile app backends]:#getting-started
[Create a new mobile app backend]:#create-new-service
[Next Steps]:#next-steps


<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart.png
[8]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-vs.png
[9]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-xs.png
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png


<!-- URLs. -->
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile app SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure 入口網站]: https://portal.azure.com/

