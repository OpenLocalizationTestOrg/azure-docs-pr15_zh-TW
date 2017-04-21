
1. 開啟共用的專案檔案 MainPage.cs，並將下列程式碼片段加入 MainPage 類別︰
    
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

3. 註解或刪除呼叫**RefreshTodoItems**方法在現有**OnNavigatedTo**方法覆寫。

    防止資料載入之前驗證使用者。 接下來，您將加入**登入**] 按鈕會觸發驗證應用程式。

4. 新增下列程式碼片段 MainPage 類別︰

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile app.
            if (await AuthenticateAsync())
            {
                // Hide the login button and load items from the mobile app.
                ButtonLogin.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
                //await InitLocalStoreAsync(); //offline sync support.
                await RefreshTodoItems();
            }
        }
        
5. 在 Windows 市集應用程式專案中，開啟 MainPage.xaml 專案檔案，並新增下列**按鈕**項目之前的項目，以定義 [**儲存**] 按鈕︰

        <Button Name="ButtonLogin" Click="ButtonLogin_Click" 
                        Visibility="Visible">Sign in</Button>

6. 在 Windows Phone Store 應用程式專案中，加入下列**按鈕**元素**ContentPanel**中，在**文字方塊**中的項目之後︰

        <Button Grid.Row ="1" Grid.Column="1" Name="ButtonLogin" Click="ButtonLogin_Click" 
            Margin="10, 0, 0, 0" Visibility="Visible">Sign in</Button>

8. 開啟共用的 App.xaml.cs 專案檔案，並新增下列內容︰

        protected override void OnActivated(IActivatedEventArgs args)
        {
            // Windows Phone 8.1 requires you to handle the respose from the WebAuthenticationBroker.
            #if WINDOWS_PHONE_APP
            if (args.Kind == ActivationKind.WebAuthenticationBrokerContinuation)
            {
                // Completes the sign-in process started by LoginAsync.
                // Change 'MobileService' to the name of your MobileServiceClient instance. 
                App.MobileService.LoginComplete(args as WebAuthenticationBrokerContinuationEventArgs);
            }
            #endif

            base.OnActivated(args);
        }

    如果已經存在的**OnActivated**方法，只要將新增`#if...#endif`程式碼區塊。

9. 按 F5 鍵以執行 Windows 市集應用程式中，按一下 [**登入**] 按鈕，然後登入應用程式中，您所選的身分識別提供者。 

    當您順利登入，不具有錯誤，應該執行的應用程式，您應該可以查詢您的後端，並在進行的更新資料。

10. 以滑鼠右鍵按一下 Windows Phone Store 應用程式專案，按一下 [**設定為啟動專案**]，然後重複上述步驟，以確認 Windows Phone Store 應用程式也會執行正確。  

 