<properties
    pageTitle="啟用離線同步處理 Azure 行動應用程式 (Cordova) |Microsoft Azure"
    description="瞭解如何使用 Cordova 應用程式中的快取及同步處理離線資料應用程式服務行動應用程式"
    documentationCenter="cordova"
    authors="adrianhall"
    manager="erikre"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-cordova-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="enable-offline-sync-for-your-cordova-mobile-app"></a>啟用離線同步處理 Cordova 行動應用程式

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

本教學課程介紹 Cordova Azure Mobile 應用程式的離線同步處理功能。 離線同步處理可讓使用者能在行動應用程式與&mdash;檢視、 新增或修改資料&mdash;即使沒有網路連線。 變更會儲存在本機資料庫。裝置恢復連線後，這些變更會與 [遠端服務進行同步處理。

本教學課程根據 Cordova 快速入門解決方案中，當您完成[Apache Cordova 快速開始]教學課程，您建立的行動應用程式。 在本教學課程中，您就會更新新增 Azure 行動應用程式的離線功能的快速入門解決方案。 我們也會醒目提示的應用程式中的特定離線的程式碼。

若要進一步瞭解離線同步處理功能，請參閱[Azure 行動應用程式中的 [離線資料同步處理]的主題。 API 使用狀況的詳細資訊，請參閱外掛程式的[讀我]檔案。

## <a name="add-offline-sync-to-the-quickstart-solution"></a>新增至快速入門方案的離線同步處理

必須離線同步處理的程式碼新增至應用程式。 離線同步處理需要 cordova sqlite 存放外掛程式，這會自動取得新增到您的應用程式時 Azure Mobile 應用程式增益集所包含的專案。 快速入門專案會包含這兩個這些增益集。

1. Visual Studio 方案總管] 中開啟 index.js 並取代下列程式碼

        var client,            // Connection to the Azure Mobile App backend
          todoItemTable;      // Reference to a table endpoint on backend

    使用此程式碼︰

        var client,             // Connection to the Azure Mobile App backend
          todoItemTable, syncContext; // Reference to table and sync context

2. 接下來，請將此程式碼︰

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');

    使用此程式碼︰

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');

        // Note: Requires at least version 2.0.0-beta6 of the Azure Mobile Apps plugin
        var store = new WindowsAzure.MobileServiceSqliteStore('store.db');

        store.defineTable({
          name: 'todoitem',
          columnDefinitions: {
              id: 'string',
              text: 'string',
              deleted: 'boolean',
              complete: 'boolean'
          }
        });

        // Get the sync context from the client
        syncContext = client.getSyncContext();

    上述的程式碼新增初始化本機存放區，並定義區域相符的資料行值的資料表中您 Azure 後端。 （您不需要在此程式碼中包含所有的資料行值）。

    您可以取得同步處理內容的參照，則可電話**getSyncContext**。 同步處理內容可協助追蹤與發送稱為 「**推入**時，用戶端應用程式已修改的所有資料表中的變更來保留資料表的關聯性。

3. 更新您的行動應用程式的應用程式 URL 的應用程式的 URL。

4. 接下來，取代此程式碼︰

        todoItemTable = client.getTable('todoitem'); // todoitem is the table name

    使用此程式碼︰

        // todoItemTable = client.getTable('todoitem');

        // Initialize the sync context with the store
        syncContext.initialize(store).then(function () {

        // Get the local table reference.
        todoItemTable = client.getSyncTable('todoitem');

        syncContext.pushHandler = {
            onConflict: function (serverRecord, clientRecord, pushError) {
                // Handle the conflict.
                console.log("Sync conflict! " + pushError.getError().message);
                // Update failed, revert to server's copy.
                pushError.cancelAndDiscard();
              },
              onError: function (pushError) {
                  // Handle the error
                  // In the simulated offline state, you get "Sync error! Unexpected connection failure."
                  console.log("Sync error! " + pushError.getError().message);
              }
        };

        // Call a function to perform the actual sync
        syncBackend();

        // Refresh the todoItems
        refreshDisplay();

        // Wire up the UI Event Handler for the Add Item
        $('#add-item').submit(addItemHandler);
        $('#refresh').on('click', refreshDisplay);

    上述的程式碼初始化同步處理內容，然後呼叫 getSyncTable （而非不），以取得區域表格的參考。

    針對您所有的本機資料庫建立、 讀取此程式碼使用更新和刪除 (CRUD) 表格作業。

    此範例會執行簡單的錯誤處理同步處理衝突。 實際的應用程式會處理各種不同的錯誤，例如網路條件、 伺服器衝突，與其他人。 程式碼範例，請參閱[離線同步處理的範例]。

5. 接下來，新增此函數來執行實際的同步處理。

        function syncBackend() {

          // Sync local store to Azure table when app loads, or when login complete.
          syncContext.push().then(function () {
              // Push completed

          });

          // Pull items from the Azure table after syncing to Azure.
          syncContext.pull(new WindowsAzure.Query('todoitem'));
        }

    您決定何時變更推入行動應用程式後端，用戶端使用**syncContext**物件上呼叫**推入**。 例如，您可以新增**syncBackend**在通話中新增的同步處理] 按鈕，例如應用程式] 按鈕事件處理常式或您無法將電話加入**addItemHandler**函數同步處理時加入新的項目。

##<a name="offline-sync-considerations"></a>離線同步處理考量

在範例中，在應用程式啟動時，在 [登入的回撥函數只呼叫**syncContext** **推入**方法。  在真實世界應用程式中，您也可以讓手動觸發此同步處理功能，或網路狀態的變更時。

擷取內容來執行已擱置的本機更新追蹤資料表時，該提取作業會自動觸發程序之前的內容推入。 重新整理時, 新增及完成項目，在此範例中，您可以省略明確**推入**通話，因為可能會重複 （第一個檢查目前的功能狀態的[讀我檔案]）。

在提供的程式碼，遠端 todoItem 資料表中的所有記錄的都查詢，但，也有可能要**推入**傳遞查詢識別碼和查詢中篩選記錄。 如需詳細資訊，請參閱一節中[離線 Azure 行動應用程式中的資料同步處理]的*累加同步處理*。

## <a name="optional-disable-authentication"></a>（選用）停用驗證

如果您沒有未設定驗證，而且不想要設定驗證之前先測試的離線同步處理、 註解回撥的函數的登入，但是保持內部的程式碼的回撥函數就會取消。

程式碼看起來應該像這樣後登入行程式碼註解。

      // Login to the service.
      // client.login('twitter')
      //    .then(function () {
        syncContext.initialize(store).then(function () {
          // Leave the rest of the code in this callback function  uncommented.
                ...
        });
      // }, handleError);

現在，應用程式會與同步處理 Azure 後端當您執行的應用程式，而不是當您登入。

## <a name="run-the-client-app"></a>執行用戶端應用程式

現在已啟用 [離線同步處理的情況下，您現在可以執行的用戶端應用程式至少一次在每個平台來填入本機存放區資料庫。 之後，您將模擬離線的案例，然後離線應用程式時，請修改本機存放區中的資料。

## <a name="optional-test-the-sync-behavior"></a>（選用）測試同步處理問題

在此區段中，您將修改以使用您的後端無效的應用程式 URL 來模擬離線使用的用戶端專案。 當您新增或變更資料的項目時，會存放在本機存放區中，這些變更，但不是同步處理到後端的資料存放區 [直到重新建立連線。

1. 在方案總管中開啟 index.js 專案檔案，並變更應用程式 URL 指向無效的 URL，如下所示︰

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net-fail');

2. 在 index.html，更新 [CSP`<meta>`具有相同的 URL 無效項目。

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: http://yourmobileapp.azurewebsites.net-fail; style-src 'self'; media-src *">

3. 建立並執行用戶端應用程式，請注意應用程式嘗試登入後端使用同步處理時，會在主控台記錄例外狀況。 您新增的任何新項目會只存在於本機存放區，直到行動後端推入。 用戶端應用程式看起來像是連線後端、 支援所有建立，讀取、 更新、 刪除 (CRUD) 作業。

4. 關閉應用程式，並重新啟動以驗證您建立的新項目會保留本機存放區。

5. （選用）使用 Visual Studio 檢視 Azure SQL 資料庫表格查看已不變更後端資料庫中的資料。

    在 Visual Studio 中，開啟 [**伺服器總管**]。 瀏覽至您的資料庫中**Azure**->**SQL 資料庫**。 以滑鼠右鍵按一下您的資料庫，然後選取 [ **SQL Server 物件檔案總管中開啟**。 現在您可以瀏覽至您的 SQL 資料庫表格和其內容。


## <a name="optional-test-the-reconnection-to-your-mobile-backend"></a>（選用）測試重新連線至您的行動後端

此區段中，您會重新連接行動模擬回到線上狀態的應用程式的後端，將應用程式。 當您登入時，資料會同步處理至您的行動後端。

1. 重新開啟 index.js，並更正應用程式 URL 指向正確的 URL。

2. 重新開啟 index.html 並更正應用程式中的 URL CSP`<meta>`項目。

3. 重建並執行的用戶端應用程式。 嘗試登入後，使用行動應用程式後端同步處理應用程式。 請確認沒有例外狀況的身分登入偵錯主控台。

4. （選用）檢視使用 SQL Server 物件總管或 Fiddler 等其他工具更新的資料。 請注意已同步處理後端資料庫與本機存放區之間的資料。

    請注意資料已同步處理資料庫與本機存放區之間，包含您的應用程式已中斷連線時，您新增的項目。

## <a name="additional-resources"></a>其他資源

* [離線資料 Azure 行動應用程式中的同步處理]

* [Cloud 封面︰ Azure 行動服務中的離線同步處理]\(附註︰ 視訊行動服務]，但在 Azure 行動應用程式中以類似的方式運作的離線同步處理\)

* [Visual Studio Apache Cordova 工具]

## <a name="next-steps"></a>後續步驟

* 查看更多進階的離線同步處理功能，例如[離線同步處理範例]中的衝突解決程序
* 查看 [離線同步處理[讀我檔案]中的 API 參照

<!-- ##Summary -->

<!-- Images -->

<!-- URLs. -->
[Apache Cordova 快速入門]: app-service-mobile-cordova-get-started.md
[讀我檔案]: https://github.com/Azure/azure-mobile-apps-js-client#offline-data-sync-preview
[離線同步處理的範例]: https://github.com/shrishrirang/azure-mobile-apps-quickstarts/tree/samples/client/cordova/ZUMOAPPNAME
[離線資料 Azure 行動應用程式中的同步處理]: app-service-mobile-offline-data-sync.md
[Azure 行動服務中的雲端封面︰ 離線同步處理]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Adding Authentication]: app-service-mobile-cordova-get-started-users.md
[authentication]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Visual Studio Apache Cordova 工具]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
