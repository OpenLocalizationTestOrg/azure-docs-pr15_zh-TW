
上一個範例顯示標準登入，這需要用戶端應用程式啟動每次連絡後端 Azure 服務與身分識別提供者。 不只是此方法效率，您可以執行的使用狀況相關問題應該許多客戶嘗試啟動同時的應用程式。 更好的方法是快取 Azure 服務所傳回的授權權杖，並嘗試使用提供者為基礎登入前，先使用此第一個。 

>[AZURE.NOTE]您可以快取後端 Azure 服務，無論您是否使用用戶端管理或服務管理驗證所發行的 token。 本教學課程中所用的服務管理驗證。


1. 開啟 ToDoActivity.java 檔案，並新增下列匯入陳述式︰

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.content.SharedPreferences.Editor;

2. 新增下列成員`ToDoActivity`類別。

        public static final String SHAREDPREFFILE = "temp"; 
        public static final String USERIDPREF = "uid";  
        public static final String TOKENPREF = "tkn";   


3. 在 [ToDoActivity.java 檔案，新增下列定義`cacheUserToken`方法。
 
        private void cacheUserToken(MobileServiceUser user)
        {
            SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
            Editor editor = prefs.edit();
            editor.putString(USERIDPREF, user.getUserId());
            editor.putString(TOKENPREF, user.getAuthenticationToken());
            editor.commit();
        }   
  
    這個方法會標示為私人的喜好設定檔案中儲存的使用者識別碼及權杖。 這應該保護存取快取，以便在裝置上的其他應用程式沒有存取權杖因為沙箱化應用程式的喜好設定。 不過，如果有人取得裝置的存取權，可能是他們可能會取得存取權杖快取透過其他方式。 

    >[AZURE.NOTE]如果資料的存取權杖會被視為高度敏感和存取裝置，您可以進一步保護使用加密的 token。 不過，完全安全的解決方案是不在本教學課程，取決於您的安全性需求範圍內。


4. 在 [ToDoActivity.java 檔案，新增下列定義`loadUserTokenCache`方法。

        private boolean loadUserTokenCache(MobileServiceClient client)
        {
            SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
            String userId = prefs.getString(USERIDPREF, null); 
            if (userId == null)
                return false;
            String token = prefs.getString(TOKENPREF, null); 
            if (token == null)
                return false;
                
            MobileServiceUser user = new MobileServiceUser(userId);
            user.setAuthenticationToken(token);
            client.setCurrentUser(user);
                
            return true;
        }



5. 在 [ *ToDoActivity.java*檔案，取代`authenticate`使用下列方法使用權杖快取的方法。 如果您想要使用 Google 以外的帳戶，請變更登入提供者。

        private void authenticate() {
            // We first try to load a token cache if one exists.
            if (loadUserTokenCache(mClient))
            {
                createTable();
            }
            // If we failed to load a token cache, login and create a token cache
            else
            {
                // Login using the Google provider.    
                ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);
        
                Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                    @Override
                    public void onFailure(Throwable exc) {
                        createAndShowDialog("You must log in. Login Required", "Error");
                    }           
                    @Override
                    public void onSuccess(MobileServiceUser user) {
                        createAndShowDialog(String.format(
                                "You are now logged in - %1$2s",
                                user.getUserId()), "Success");
                        cacheUserToken(mClient.getCurrentUser());
                        createTable();  
                    }
                });
            }
        }

6. 建立應用程式和測試驗證使用的是正確的帳戶。 至少兩次執行。 在第一次執行中，您應該會收到提示登入，並建立權杖快取。 之後，每次執行會嘗試載入驗證的權杖快取，且您不能才能登入。



