
1. 在 [MainPage.xaml.cs 專案檔案中，新增下列**使用**陳述式︰

        using System.Linq;      
        using Windows.Security.Credentials;

2. 下列程式碼取代**AuthenticateAsync**方法︰

        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;

            // This sample uses the Facebook provider.
            var provider = MobileServiceAuthenticationProvider.Facebook;

            // Use the PasswordVault to securely store and access credentials.
            PasswordVault vault = new PasswordVault();
            PasswordCredential credential = null;

            try
            {
                // Try to get an existing credential from the vault.
                credential = vault.FindAllByResource(provider.ToString()).FirstOrDefault();
            }
            catch (Exception)
            {
                // When there is no matching resource an error occurs, which we ignore.
            }

            if (credential != null)
            {
                // Create a user from the stored credentials.
                user = new MobileServiceUser(credential.UserName);
                credential.RetrievePassword();
                user.MobileServiceAuthenticationToken = credential.Password;

                // Set the user from the stored credentials.
                App.MobileService.CurrentUser = user;

                // Consider adding a check to determine if the token is 
                // expired, as shown in this post: http://aka.ms/jww5vp.

                success = true;
                message = string.Format("Cached credentials for user - {0}", user.UserId);
            }
            else
            {
                try
                {
                    // Login with the identity provider.
                    user = await App.MobileService
                        .LoginAsync(provider);

                    // Create and store the user credentials.
                    credential = new PasswordCredential(provider.ToString(),
                        user.UserId, user.MobileServiceAuthenticationToken);
                    vault.Add(credential);

                    success = true;
                    message = string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (MobileServiceInvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }
            }
            
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();

            return success;
        }

    在這個版本的**AuthenticateAsync**，應用程式會嘗試使用**PasswordVault**中儲存的認證存取服務。 不儲存的認證時，會也會執行 [一般的登入。

    >[AZURE.NOTE]快取的權杖可能已過期，並 token 到期也會發生驗證後應用程式時使用。 若要瞭解如何判斷權杖是否已過期，請參閱[檢查過期的驗證的權杖](http://aka.ms/jww5vp)。 處理過期的權杖相關的授權錯誤的解決方案，請參閱[快取及處理 Azure 行動服務中的過期的權杖受管理的 SDK](http://blogs.msdn.com/b/carlosfigueira/archive/2014/03/13/caching-and-handling-expired-tokens-in-azure-mobile-services-managed-sdk.aspx)的文章。 

3. 按兩次重新啟動應用程式。

    請注意，在第一個啟動時，登入的提供者一次必要。 不過，在第二個重新啟動使用快取的認證，登入略過。 
