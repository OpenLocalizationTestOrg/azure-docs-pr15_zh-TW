
1. 在 Android Studio 中的**[專案總管**] 中，開啟 ToDoActivity.java 檔案並將下列匯入陳述式。

        import java.util.concurrent.ExecutionException;
        import java.util.concurrent.atomic.AtomicBoolean;

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.content.SharedPreferences.Editor;

        import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
        import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;

2. 新增下列方法**ToDoActivity**類別︰ 
    
        private void authenticate() {
            // Login using the Google provider.
            
            ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);
    
            Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                @Override
                public void onFailure(Throwable exc) {
                    createAndShowDialog((Exception) exc, "Error");
                }           
                @Override
                public void onSuccess(MobileServiceUser user) {
                    createAndShowDialog(String.format(
                            "You are now logged in - %1$2s",
                            user.getUserId()), "Success");
                    createTable();  
                }
            });     
        }


    這樣會建立新的方法來處理驗證程序。 使用 Google 登入驗證使用者。 會顯示對話方塊，其中會顯示的已驗證的使用者識別碼。 您無法繼續沒有正驗證。

    > [AZURE.NOTE] 如果您使用的 Google 以外的身分識別提供者，變更傳遞給**登入**上述方法將下列的值︰ _MicrosoftAccount_、 _Facebook_、 _Twitter_，或_windowsazureactivedirectory_。

3. 在**onCreate**方法中，新增下列程式碼會產生的程式碼之後`MobileServiceClient`物件。

        authenticate();

    這個呼叫啟動驗證程序。

4. 移動剩餘的程式碼之後`authenticate();`在新**createTable**方法的**onCreate**方式，其看起來像這樣︰

        private void createTable() {
    
            // Get the table instance to use.
            mToDoTable = mClient.getTable(ToDoItem.class);
    
            mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);
    
            // Create an adapter to bind the items with the view.
            mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);
    
            // Load the items from Azure.
            refreshItemsFromTable();
        }

9. 從 [**執行**] 功能表中，按一下 [**執行應用程式**] 以啟動應用程式，並使用您所選的身分識別提供者登入。 

    當您順利登入，不具有錯誤，應該執行的應用程式，您應該可以可查詢後端服務和更新資料。