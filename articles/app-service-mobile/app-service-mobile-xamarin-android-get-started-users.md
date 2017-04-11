<properties
    pageTitle="驗證 Xamarin Android 中的行動應用程式快速入門"
    description="瞭解如何使用行動應用程式驗證使用者透過各種不同的身分識別提供者，包括 AAD、 Google、 Facebook、 Twitter 和 Microsoft Xamarin Android 應用程式。"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="add-authentication-to-your-xamarinandroid-app"></a>新增驗證 Xamarin.Android 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

本主題說明如何驗證行動應用程式從用戶端應用程式的使用者。 在本教學課程中，您可以新增驗證使用 Azure Mobile 應用程式支援的身分識別提供者快速入門專案。 正在順利驗證，並在行動應用程式授權之後, 會顯示使用者識別碼的值。

本教學課程為基礎的行動應用程式快速入門。 您也會先必須完成本教學課程[建立 Xamarin.Android 應用程式]。 如果您不使用下載快速入門伺服器專案，您必須將驗證擴充套件新增至您的專案。 如需關於伺服器擴充套件的詳細資訊，請參閱[使用.NET 後端伺服器 SDK Azure Mobile 應用程式](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。

##<a name="register"></a>註冊您的應用程式的驗證及設定應用程式服務

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>限制權限已驗證的使用者

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

在 Visual Studio 或 Xamarin Studio 中，請在裝置或模擬器上執行用戶端專案。 驗證應用程式啟動後，會引發處理的例外狀況的狀態碼 401 （未經授權）。 這是因為應用程式嘗試存取您的行動應用程式後端非驗證的使用者。 *TodoItem*表格現在需要驗證。

接下來，您將會更新的用戶端應用程式要求資源 Mobile 應用程式後端與已驗證的使用者。

##<a name="add-authentication"></a>新增驗證應用程式

應用程式會要求使用者點選 [**登入**] 按鈕，以及驗證資料顯示前更新。

1. 將下列程式碼新增至**TodoActivity**類別︰

        // Define a authenticated user.
        private MobileServiceUser user;
        private async Task<bool> Authenticate()
        {
                var success = false;
                try
                {
                    // Sign in with Facebook login using a server-managed flow.
                    user = await client.LoginAsync(this,
                        MobileServiceAuthenticationProvider.Facebook);
                    CreateAndShowDialog(string.Format("you are now logged in - {0}",
                        user.UserId), "Logged in!");

                    success = true;
                }
                catch (Exception ex)
                {
                    CreateAndShowDialog(ex, "Authentication failed");
                }
                return success;
        }

        [Java.Interop.Export()]
        public async void LoginUser(View view)
        {
            // Load data only after authentication succeeds.
            if (await Authenticate())
            {
                //Hide the button after authentication succeeds.
                FindViewById<Button>(Resource.Id.buttonLoginUser).Visibility = ViewStates.Gone;

                // Load the data.
                OnRefreshItemsSelected();
            }
        }

    這樣會建立新的方法來驗證使用者與新的**登入**] 按鈕的方法處理常式。 使用者在上述範例程式碼中的使用通過的 Facebook 登入。 對話方塊用來顯示通過驗證的使用者識別碼。

    > [AZURE.NOTE] 如果您使用的 Facebook 以外的身分識別提供者，變更值傳遞給**LoginAsync**上方以下一項︰ _MicrosoftAccount_、 _Twitter_、 _Google_或_WindowsAzureActiveDirectory_。

3. 在**OnCreate**方法中，刪除或註解出的程式碼的下列行︰

        OnRefreshItemsSelected ();

4. 在 Activity_To_Do.axml 檔案中，新增下列*LoginUser*按鈕定義之前現有*AddItem* ] 按鈕︰

        <Button
            android:id="@+id/buttonLoginUser"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="LoginUser"
            android:text="@string/login_button_text" />

5. 將下列項目新增至 Strings.xml 資源檔案︰

        <string name="login_button_text">Sign in</string>

6. 在 Visual Studio 或 Xamarin Studio 中，執行用戶端專案裝置或模擬器上，並使用您所選的身分識別提供者登入。

    當您順利登入，應用程式會顯示您的登入識別碼和 todo 項目清單，您可以對資料進行更新。


<!-- URLs. -->
[建立 Xamarin.Android 應用程式]: app-service-mobile-xamarin-android-get-started.md
