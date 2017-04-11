<properties
    pageTitle="開始使用行動應用程式 Xamarin.Forms 應用程式中的驗證 |Microsoft Azure"
    description="瞭解如何使用行動應用程式驗證使用者透過各種不同的身分識別提供者，包括 AAD、 Google、 Facebook、 Twitter 和 Microsoft Xamarin 表單應用程式。"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="add-authentication-to-your-xamarinforms-app"></a>新增驗證 Xamarin.Forms 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

##<a name="overview"></a>概觀

本主題說明如何驗證使用者的應用程式服務行動應用程式從用戶端應用程式。 在本教學課程中，您可以新增驗證 Xamarin.Forms 快速入門專案使用的身分識別提供者所支援的應用程式服務。 正在順利驗證並由您的行動應用程式授權之後，會顯示使用者識別碼的值，然後您將能夠存取受限制的表格資料。

##<a name="prerequisites"></a>必要條件

本教學課程中使用最佳的結果，我們建議您先完成[建立 Xamarin.Forms 應用程式](app-service-mobile-xamarin-forms-get-started.md)的教學課程。 完成本教學課程後，您必須是多平台 TodoList 應用程式的 Xamarin.Forms 專案。

如果您不使用下載快速入門伺服器專案，您必須將驗證擴充套件新增至您的專案。 如需關於伺服器擴充套件的詳細資訊，請參閱[使用.NET 後端伺服器 SDK Azure Mobile 應用程式](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。

##<a name="register-your-app-for-authentication-and-configure-app-services"></a>註冊您的應用程式的驗證及設定應用程式服務

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="restrict-permissions-to-authenticated-users"></a>限制權限已驗證的使用者

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]


##<a name="add-authentication-to-the-portable-class-library"></a>新增驗證類別可攜式文件庫

行動應用程式會使用[LoginAsync]副檔名方法[MobileServiceClient]上登入應用程式服務驗證的使用者。 此範例使用的應用程式中顯示的提供者的登入介面伺服器管理驗證流程。 如需詳細資訊，請參閱[伺服器管理驗證](app-service-mobile-dotnet-how-to-use-client-library.md#serverflow)。 若要提供更佳的使用者體驗生產應用程式中，您可以考慮改為使用[用戶端管理驗證](app-service-mobile-dotnet-how-to-use-client-library.md#clientflow)。 

驗證方法 Xamarin.Forms 專案，您可以定義**IAuthenticate**介面可攜式類別庫應用程式中。 您也會更新可攜式類別文件庫，若要新增的**登入**] 按鈕，即可開始驗證使用者定義的使用者介面。 驗證成功之後, 行動應用程式後端載入資料。

您必須執行每個應用程式支援的平台的**IAuthenticate**介面。


1. 在 Visual Studio 或 Xamarin Studio 中，開啟 App.cs 從專案與**可攜式**在 [名稱] 這是可攜式類別庫專案，然後新增下列`using`陳述式︰

        using System.Threading.Tasks;

2. 在 App.cs，新增下列`IAuthenticate`立即之前介面定義`App`類別定義。

        public interface IAuthenticate
        {
            Task<bool> Authenticate();
        }

3. 將下列靜態成員加入**應用程式**類別初始化平台特定實作的介面。

        public static IAuthenticate Authenticator { get; private set; }

        public static void Init(IAuthenticate authenticator)
        {
            Authenticator = authenticator;
        }

4. 開啟 TodoList.xaml 從可攜式類別文件庫的專案，新增下列**按鈕**項目*buttonsPanel*版面配置項目，請在 [現有] 按鈕後︰ 

        <Button x:Name="loginButton" Text="Sign-in" MinimumHeightRequest="30" 
            Clicked="loginButton_Clicked"/>

    此按鈕會觸發伺服器管理您的行動應用程式後端驗證。

5. 開啟 TodoList.xaml.cs 從可攜式類別文件庫的專案，然後新增下列欄位，以`TodoList`類別︰

        // Track whether the user has authenticated. 
        bool authenticated = false;


6. 下列程式碼取代**OnAppearing**方法︰

        protected override async void OnAppearing()
        {
            base.OnAppearing();
    
            // Refresh items only when authenticated.
            if (authenticated == true)
            {
                // Set syncItems to true in order to synchronize the data 
                // on startup when running in offline mode.
                await RefreshItems(true, syncItems: false);

                // Hide the Sign-in button.
                this.loginButton.IsVisible = false;
            }
        }

    這可確保的資料只後進行重新整理來自服務使用者驗證。

7. 加入下列事件處理常式**Clicked** **TodoList**類別︰

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true to synchronize the data on startup when offline is enabled.
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }

8. 儲存變更並重建可攜式類別庫專案，驗證沒有錯誤。


##<a name="add-authentication-to-the-android-app"></a>新增驗證 Android 應用程式

本節說明如何在 Android 應用程式專案中實作**IAuthenticate**介面。 如果您不支援 Android 的裝置，請略過此區段。

1. 在 Visual Studio 或 Xamarin Studio 中，以滑鼠右鍵按一下**droid**專案，然後**設定為啟動專案**。

2. 按 F5 以開始專案偵錯程式，然後在 [驗證應用程式啟動後，會引發處理的例外狀況的狀態碼 401 （未經授權）。 這是因為存取後端限於授權的使用者。

3. 開啟 MainActivity.cs Android 專案，並新增下列`using`陳述式︰

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;

4. 更新**MainActivity**類別實作**IAuthenticate**介面，，如下所示︰

        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate


5. 藉由新增**MobileServiceUser**欄位和**驗證**方法，如下所需的**IAuthenticate**介面，來更新**MainActivity**類別︰

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(this,
                    MobileServiceAuthenticationProvider.Facebook);
                if (user != null)
                {
                    message = string.Format("you are now signed-in as {0}.",
                        user.UserId);
                    success = true;
                }
            }
            catch (Exception ex)
            {
                message = ex.Message;
            }

            // Display the success or failure message.
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(message);
            builder.SetTitle("Sign-in result");
            builder.Create().Show();

            return success;
        }


    如果您使用的 Facebook 以外的身分識別提供者，請用[MobileServiceAuthenticationProvider]選擇不同的值。

6. 將下列程式碼新增至**OnCreate**類別的方法**MainActivity**通話之前`LoadApplication()`:

        // Initialize the authenticator before loading the app.
        App.Init((IAuthenticate)this);

    這可確保驗證初始化之前載入的應用程式。

7. 重建應用程式，執行，然後驗證提供者以登入方法，您可以選擇，並確認能夠存取資料驗證的使用者。

##<a name="add-authentication-to-the-ios-app"></a>新增驗證 iOS 應用程式

本節說明如何實作**IAuthenticate**介面 iOS 應用程式專案中。 如果您不支援 iOS 裝置，請略過此區段。

1. 在 Visual Studio 或 Xamarin Studio 中，以滑鼠右鍵按一下**iOS**專案，然後**設定為啟動專案**。

2. 按 F5 以開始專案偵錯程式，然後在 [驗證應用程式啟動後，會引發處理的例外狀況的狀態碼 401 （未經授權）。 這是因為存取後端限於授權的使用者。

4. IOS project 中開啟 AppDelegate.cs 並新增下列`using`陳述式︰

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;

4. 更新**AppDelegate**類別實作**IAuthenticate**介面，，如下所示︰

        public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate

5. 藉由新增**MobileServiceUser**欄位和**驗證**方法，如下所需的**IAuthenticate**介面，來更新**AppDelegate**類別︰

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(UIApplication.SharedApplication.KeyWindow.RootViewController,
                        MobileServiceAuthenticationProvider.Facebook);
                    if (user != null)
                    {
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                        success = true;                        
                    }
                }        
            }
            catch (Exception ex)
            {
               message = ex.Message;
            }

            // Display the success or failure message.
            UIAlertView avAlert = new UIAlertView("Sign-in result", message, null, "OK", null);
            avAlert.Show();         

            return success;
        }

    如果您使用的 Facebook 以外的身分識別提供者，請用[MobileServiceAuthenticationProvider]選擇不同的值。

6. 將下列程式碼新增至通話之前的**FinishedLaunching**方法`LoadApplication()`: 

        App.Init(this);

    這可確保驗證會載入應用程式之前。

7. 重建應用程式，執行，然後驗證提供者以登入方法，您可以選擇，並確認能夠存取資料驗證的使用者。


##<a name="add-authentication-to-windows-app-projects"></a>新增驗證 Windows 應用程式的專案

本節說明如何在 Windows 8.1 和 Windows Phone 8.1 版應用程式專案中實作**IAuthenticate**介面。 相同的步驟適用於通用 Windows 平台 (UWP) 專案。 如果您不支援 Windows 裝置，請略過此區段。

1. 在 Visual Studio 中，以滑鼠右鍵按一下**WinApp**或**WinPhone81**專案，然後**設定為啟動專案**。

2. 按 F5 以開始專案偵錯工具，然後在 [驗證應用程式啟動後，會引發處理的例外狀況的狀態碼 401 （未經授權）。 這是因為存取後端限於授權的使用者。

3. 開啟 MainPage.xaml.cs Windows 應用程式專案，並新增下列`using`陳述式︰

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.UI.Popups;
        using <your_Portable_Class_Library_namespace>;

    取代`<your_Portable_Class_Library_namespace>`與您的課程可攜式文件庫的命名空間。

4. 更新**MainPage**類別實作**IAuthenticate**介面，，如下所示︰

        public sealed partial class MainPage : IAuthenticate


5. 藉由新增**MobileServiceUser**欄位和**驗證**方法，如下所需的**IAuthenticate**介面，來更新**MainPage**類別︰

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            string message = string.Empty;
            var success = false;

            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                    if (user != null)
                    {
                        success = true;
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                    }
                }
                
            }
            catch (Exception ex)
            {
                message = string.Format("Authentication Failed: {0}", ex.Message);
            }

            // Display the success or failure message.
            await new MessageDialog(message, "Sign-in result").ShowAsync();

            return success;
        }


    如果您使用的 Facebook 以外的身分識別提供者，請用[MobileServiceAuthenticationProvider]選擇不同的值。

6. 對於**MainPage**課程之前通話中新增下列程式碼`LoadApplication()`:

        // Initialize the authenticator before loading the app.
        <your_Portable_Class_Library_namespace>.App.Init(this);
 
    取代`<your_Portable_Class_Library_namespace>`與您的課程可攜式文件庫的命名空間。  
    如果這是 WinApp 專案時，您可以跳到步驟 8。 下一個步驟只適用於 WinPhone81 專案時，必須先完成登入回撥的位置。

7. （選用）在**WinPhone81**應用程式專案中，開啟 App.xaml.cs，然後將下列文字新增`using`陳述式︰

        using Microsoft.WindowsAzure.MobileServices;
        using <your_Portable_Class_Library_namespace>;

    取代`<your_Portable_Class_Library_namespace>`與您的課程可攜式文件庫的命名空間。

8.  新增下列**OnActivated**方法覆寫到的**應用程式**類別︰

        protected override void OnActivated(IActivatedEventArgs args)
        {
            base.OnActivated(args);

            // We just need to handle activation that occurs after web authentication. 
            if (args.Kind == ActivationKind.WebAuthenticationBrokerContinuation)
            {
                // Get the client and call the LoginComplete method to complete authentication.
                var client = TodoItemManager.DefaultManager.CurrentClient as MobileServiceClient;
                client.LoginComplete(args as WebAuthenticationBrokerContinuationEventArgs);
            }
        }

    方法覆寫已存在，只要從上方的程式碼片段新增條件的程式碼。

7. 重建應用程式，執行，然後驗證提供者以登入方法，您可以選擇，並確認您可以存取資料驗證的使用者。

##<a name="next-steps"></a>後續步驟

現在您完成此基本驗證教學課程，請考慮繼續執行下列教學課程其中一項︰

+ [新增至您的應用程式的 [推入通知](app-service-mobile-xamarin-forms-get-started-push.md)  
  瞭解如何新增推入通知支援新增至您的應用程式，並設定您的行動應用程式後端使用 Azure 通知集線器傳送推入通知。

+ [啟用離線同步處理應用程式](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  瞭解如何新增離線支援您使用行動應用程式後端的應用程式。 離線同步處理可讓使用者能在行動應用程式與&mdash;檢視、 新增或修改資料&mdash;即使沒有網路連線。

<!-- Images. -->

<!-- URLs. -->
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[LoginAsync]: https://msdn.microsoft.com/library/azure/dn268341(v=azure.10).aspx
[MobileServiceClient]: https://msdn.microsoft.com/library/azure/JJ553674(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: https://msdn.microsoft.com/library/azure/jj730936(v=azure.10).aspx

