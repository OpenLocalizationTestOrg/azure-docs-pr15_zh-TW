<properties
    pageTitle="將驗證新增至通用 Windows 平台 (UWP) 應用程式 |Azure 行動應用程式"
    description="瞭解如何使用 Azure 應用程式服務行動應用程式來驗證使用者通用 Windows 平台 (UWP) 應用程式使用各種不同的身分識別提供者，包括︰ AAD、 Google、 Facebook、 Twitter 和 Microsoft。"
    services="app-service\mobile"
    documentationCenter="windows"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="add-authentication-to-your-windows-app"></a>新增驗證您的 Windows 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

本主題說明如何將雲端驗證新增至您的行動應用程式。 在本教學課程中，您專案中新增驗證通用 Windows 平台 (UWP) 快速入門的行動應用程式使用支援的 Azure 應用程式服務的身分識別提供者。 正在順利驗證，並由您的行動應用程式後端授權之後, 會顯示使用者識別碼的值。

本教學課程為基礎的行動應用程式快速入門。 您必須先完成教學課程[開始使用行動應用程式](app-service-mobile-windows-store-dotnet-get-started.md)。

##<a name="register"></a>註冊您的應用程式的驗證及設定應用程式服務

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>限制權限已驗證的使用者

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

現在，您也可以驗證已停用匿名存取您的後端。 設定為啟動專案 UWP 應用程式的專案，部署，並執行應用程式。驗證應用程式啟動後，會引發處理的例外狀況的狀態碼 401 （未經授權）。 這是因為應用程式嘗試存取您的行動應用程式程式碼，以未經授權的使用者，但*TodoItem*表格現在需要驗證。

接下來，您將會更新驗證的使用者，再從您的應用程式服務要求資源的應用程式。

##<a name="add-authentication"></a>新增驗證應用程式

1. 在 UWP 應用程式會專案檔案 MainPage.cs，然後將下列程式碼片段加入 MainPage 類別︰
    
        // Define a member variable for storing the signed-in user. 
        private MobileServiceUser user;

        // Define a method that performs the authentication process
        // using a Facebook sign-in. 
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
            try
            {
                // Change 'MobileService' to the name of your MobileServiceClient instance.
                // Sign-in using Facebook authentication.
                user = await App.MobileService
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                message =
                    string.Format("You are now signed in - {0}", user.UserId);

                success = true;
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }

            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
            return success;
        }

    將此程式碼會驗證使用者與 Facebook 登入。 如果您使用的 Facebook 以外的身分識別提供者，變更的值**MobileServiceAuthenticationProvider**上方的值，您的提供者。

3. 註解或刪除現有的**OnNavigatedTo**方法覆寫中**ButtonRefresh_Click**方法 （或**InitLocalStoreAsync**方法）。 防止資料載入之前驗證使用者。 接下來，您將加入**登入**] 按鈕會觸發驗證應用程式。

4. 新增下列程式碼片段 MainPage 類別︰

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile app.
            if (await AuthenticateAsync())
            {
                // Switch the buttons and load items from the mobile app.
                ButtonLogin.Visibility = Visibility.Collapsed;
                ButtonSave.Visibility = Visibility.Visible;
                //await InitLocalStoreAsync(); //offline sync support.
                await RefreshTodoItems();
            }
        }
        
5. 開啟 MainPage.xaml 專案檔案，找出 [**儲存**] 按鈕會定義的項目，取代下列程式碼︰

        <Button Name="ButtonSave" Visibility="Collapsed" Margin="0,8,8,0" 
                Click="ButtonSave_Click">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Add"/>
                <TextBlock Margin="5">Save</TextBlock>
            </StackPanel>
        </Button>
        <Button Name="ButtonLogin" Visibility="Visible" Margin="0,8,8,0" 
                Click="ButtonLogin_Click" TabIndex="0">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Permissions"/>
                <TextBlock Margin="5">Sign in</TextBlock> 
            </StackPanel>
        </Button>

9. 按 F5 鍵以執行應用程式中，按一下 [**登入**] 按鈕，然後登入應用程式中，您所選的身分識別提供者。 登入成功後，應用程式執行不具有錯誤，並將查詢您的後端，並在進行的更新資料。


##<a name="tokens"></a>用戶端上儲存的 「 驗證 」 token

上一個範例顯示標準登入，這需要用戶端應用程式啟動每次連絡身分識別提供者和應用程式服務。 不只是效率，您可以執行此方法將使用方式的相關問題應該許多客戶嘗試啟動同時的應用程式。 更好的方法是快取傳回應用程式服務的授權權杖，並嘗試使用提供者為基礎登入前，先使用此第一個。

>[AZURE.NOTE]由服務應用程式，無論您是否使用用戶端管理或服務管理驗證的權杖，您可以快取。 本教學課程中所用的服務管理驗證。

[AZURE.INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

##<a name="next-steps"></a>後續步驟

現在您完成此基本驗證教學課程，請考慮繼續執行下列教學課程其中一項︰

+ [新增至您的應用程式的 [推入通知](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  瞭解如何新增推入通知支援新增至您的應用程式，並設定您的行動應用程式後端使用 Azure 通知集線器傳送推入通知。

+ [啟用離線同步處理應用程式](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  瞭解如何新增離線支援您使用行動應用程式後端的應用程式。 離線同步處理可讓使用者能在行動應用程式與&mdash;檢視、 新增或修改資料&mdash;即使沒有網路連線。


<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md

