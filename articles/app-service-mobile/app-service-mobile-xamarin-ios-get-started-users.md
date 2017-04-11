<properties
    pageTitle="驗證 Xamarin ios 行動應用程式快速入門"
    description="瞭解如何使用行動應用程式驗證使用者透過各種不同的身分識別提供者，包括 AAD、 Google、 Facebook、 Twitter 和 Microsoft Xamarin iOS 應用程式。"
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
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="add-authentication-to-your-xamarinios-app"></a>新增驗證 Xamarin.iOS 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

本主題說明如何驗證使用者的應用程式服務行動應用程式從用戶端應用程式。 在本教學課程中，您可以新增驗證 Xamarin.iOS 快速入門專案使用的身分識別提供者所支援的應用程式服務。 正在順利驗證並由您的行動應用程式授權之後，會顯示使用者識別碼的值，您將能夠存取受限制的表格資料。

您必須先完成教學課程中[建立 Xamarin.iOS 應用程式]。 如果您不使用下載快速入門伺服器專案，您必須將驗證擴充套件新增至您的專案。 如需關於伺服器擴充套件的詳細資訊，請參閱[使用.NET 後端伺服器 SDK Azure Mobile 應用程式](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。

##<a name="register-your-app-for-authentication-and-configure-app-services"></a>註冊您的應用程式的驗證及設定應用程式服務

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="restrict-permissions-to-authenticated-users"></a>限制權限已驗證的使用者

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

&nbsp;&nbsp;4.在 Visual Studio 或 Xamarin Studio，請在裝置或模擬器上執行用戶端專案。 驗證應用程式啟動後，會引發處理的例外狀況的狀態碼 401 （未經授權）。 偵錯工具主控台是登入失敗。 因此，在 Visual Studio 中，您應該會看到輸出視窗中的失敗。

&nbsp;&nbsp;因為應用程式嘗試存取您的行動應用程式後端非驗證的使用者，就會發生此未經授權的錯誤。 *TodoItem*表格現在需要驗證。

接下來，您將會更新的用戶端應用程式要求資源 Mobile 應用程式後端與已驗證的使用者。

##<a name="add-authentication-to-the-app"></a>新增驗證應用程式

在此區段中，您將修改顯示登入畫面，顯示資料之前的應用程式。 應用程式啟動時，它會無法連線到您的應用程式服務並不會顯示任何資料。 第一次使用者執行重新整理手勢-登入畫面會出現。順利登入後 todo 項目清單隨即出現。

1. 在用戶端專案中，開啟檔案**QSTodoService.cs** ，並新增下列使用陳述式和`MobileServiceUser`與子 QSTodoService 類別︰

    ```
        using UIKit;
    ```

        // Logged in user
        private MobileServiceUser user;
        public MobileServiceUser User { get { return user; } }

2. 新增以下列定義命名**驗證** **QSTodoService**至新的方法︰


        public async Task Authenticate(UIViewController view)
        {
            try
            {
                user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

    >[AZURE.NOTE] 如果您使用的 Facebook 以外的身分識別提供者，變更值傳遞給**LoginAsync**上方以下一項︰ _MicrosoftAccount_、 _Twitter_、 _Google_或_WindowsAzureActiveDirectory_。

3. 開啟**QSTodoListViewController.cs**。 修改**ViewDidLoad**移除**RefreshAsync()**附近結束通話的方法定義︰

        public override async void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            todoService = QSTodoService.DefaultService;
           await todoService.InitializeStoreAsync ();

           RefreshControl.ValueChanged += async (sender, e) => {
                await RefreshAsync ();
           }

            // Comment out the call to RefreshAsync
            // await RefreshAsync ();
        }


4. 修改**RefreshAsync**進行驗證，如果**使用者**屬性是空值的方法。 方法定義頂端新增下列程式碼︰

        // start of RefreshAsync method
        if (todoService.User == null) {
            await QSTodoService.DefaultService.Authenticate (this);
            if (todoService.User == null) {
                Console.WriteLine ("couldn't login!!");
                return;
            }
        }
        // rest of RefreshAsync method

5. 在 Visual Studio 或 Xamarin Studio 中連線到您 Xamarin 所建立的主機執行目標裝置或模擬器用戶端專案，在 Mac 上。 驗證應用程式會顯示任何資料。

    執行的重新整理筆勢取出，會導致登入畫面，顯示這些項目清單。 一旦您已經輸入有效的認證，應用程式會顯示 todo 項目清單，然後您可以輕鬆更新資料。


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[建立 Xamarin.iOS 應用程式]: app-service-mobile-xamarin-ios-get-started.md
