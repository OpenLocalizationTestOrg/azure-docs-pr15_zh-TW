<properties
    pageTitle="啟用離線同步處理 Azure 行動應用程式 (Xamarin iOS)"
    description="瞭解如何使用快取及同步處理離線資料 Xamarin iOS 應用程式中的應用程式服務行動應用程式"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="dwrede"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="enable-offline-sync-for-your-xamarinios-mobile-app"></a>啟用離線同步處理 Xamarin.iOS 行動應用程式

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>概觀

本教學課程介紹 Xamarin.iOS Azure Mobile 應用程式的離線同步處理功能。 離線同步處理可讓使用者在行動應用程式檢視、 新增或修改資料，即使沒有網路連線與之互動。 變更會儲存在本機資料庫。 裝置恢復連線後，這些變更會與 [遠端服務進行同步處理。

在本教學課程中，[建立 Xamarin iOS 應用程式]支援 Azure Mobile 應用程式的離線功能 Xamarin.iOS 應用程式專案進行更新。 如果您不使用下載快速入門伺服器專案，您必須新增至您的專案的資料存取擴充套件。 如需關於伺服器擴充套件的詳細資訊，請參閱[使用.NET 後端伺服器 SDK Azure Mobile 應用程式](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。

若要進一步瞭解離線同步處理功能，請參閱[Azure 行動應用程式中的 [離線資料同步處理]的主題。

## <a name="update-the-client-app-to-support-offline-features"></a>更新支援離線功能的用戶端應用程式

Azure Mobile 應用程式離線功能可讓您離線的情境中操作時，與本機資料庫互動。 若要在您的應用程式中使用這些功能，初始化[SyncContext]本機存放區。 參照到 [IMobileServiceSyncTable] 介面表格。 SQLite 作為在裝置上的本機存放區。

1. 在您完成[建立 Xamarin iOS 應用程式]教學課程中，在 project 中開啟 NuGet 封裝管理員，然後搜尋並安裝**Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet 套件。

2. 開啟 QSTodoService.cs 檔案，並取消註解`#define OFFLINE_SYNC_ENABLED`定義。

3. 重建並執行的用戶端應用程式。 之前您啟用 [離線同步處理應用程式運作方式相同。 不過，本機資料庫現在填入可供離線使用的資料。

## <a name="update-sync"></a>若要中斷連線後端應用程式更新

在此區段中，您可以中斷連線至您的行動應用程式後端模擬的離線的情況。 當您新增資料的項目時，您的例外狀況處理常式會告訴您的應用程式位於離線模式。 在這個狀態，新的項目會新增本機存放區，並會同步處理到行動應用程式後端推入下一步執行在連線狀態時。

1. 編輯 QSToDoService.cs 共用專案中。 變更為指向無效的 URL **applicationURL** :

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    您也可以停用 wifi 和裝置上的行動電話網路，或使用飛機模式示範離線的行為。

2. 建立並執行應用程式。 請注意您的同步處理應用程式啟動時，在重新整理失敗。

3. 輸入新的項目，然後請注意，推入失敗 [CancelledByNetworkError] 狀態為 [每次您按一下 [**儲存**]。 不過，新的 todo 項目的在於本機存放區直到行動應用程式後端推入。  在生產應用程式中，如果您隱藏這些例外狀況的用戶端應用程式看起來像是它仍會連線到行動應用程式後端。

4. 關閉應用程式，並重新啟動以驗證您建立的新項目會保留本機存放區。

5. （選用）如果您有安裝在電腦上的 Visual Studio，開啟 [**伺服器總管**]。 瀏覽至您的資料庫中**Azure**-> **SQL 資料庫**。 以滑鼠右鍵按一下您的資料庫，然後選取 [ **SQL Server 物件檔案總管中開啟**。 現在您可以瀏覽至您的 SQL 資料庫表格和其內容。 確認已不變更後端資料庫中的資料。

6. （選用）使用其他工具，例如 Fiddler 或郵差來查詢您的行動後端，在表單中使用取得查詢`https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`。

## <a name="update-online-app"></a>若要重新連接您的行動應用程式後端應用程式更新

在此區段中，重新連線到行動應用程式後端的應用程式。 這會模擬從離線狀態移到 [線上狀態的行動應用程式後端的應用程式。   如果您關閉網路連線以模擬網路遭破壞，需要無程式碼的變更。
網路重新開啟。  當您第一次執行應用程式，`RefreshDataAsync`呼叫方法。 這會呼叫`SyncAsync`同步處理您的本機存放區後端資料庫。

1. 開啟 QSToDoService.cs 共用專案，並還原為您的變更**applicationURL**屬性。

2. 重建並執行應用程式。 與使用推入和提取作業 Azure Mobile 應用程式後端應用程式同步處理的本機變更時`OnRefreshItemsSelected`執行方法。

3. （選用）檢視使用 SQL Server 物件總管或 Fiddler 等其他工具更新的資料。 Azure Mobile 應用程式後端資料庫與本機存放區之間，具有已同步處理通知資料。

4. 在應用程式，按一下 [完成這些本機存放區中的幾個項目旁的核取方塊。

  `CompleteItemAsync`通話`SyncAsync`與 Mobile 應用程式後端的同步處理每個完成項目。 `SyncAsync`通話提取推入及。
  **當您執行對資料表的用戶端已進行變更，擷取的用戶端同步處理內容推入一定會執行第一次自動**。 隱含的推入可確保所有的資料表，以及 [資料庫關聯圖本機存放區中保持一致。 如需有關這種行為的詳細資訊，請參閱[離線 Azure 行動應用程式中的資料同步處理]。

## <a name="review-the-client-sync-code"></a>檢閱用戶端同步處理的程式碼

當您已完成教學課程[建立 Xamarin iOS 應用程式]時，您下載 Xamarin 用戶端專案包含支援使用本機 SQLite 資料庫的離線同步處理的程式碼。 以下是已中包含哪些產品教學課程的程式碼的簡介。 此功能的概觀，請參閱[離線 Azure 行動應用程式中的資料同步處理]。

* 在執行任何表格作業之前，必須先初始化本機存放區。 初始化本機存放區資料庫時`QSTodoListViewController.ViewDidLoad()`跟`QSTodoService.InitializeStoreAsync()`。 這個方法會建立新本機 SQLite 資料庫使用`MobileServiceSQLiteStore`Azure Mobile 應用程式用戶端 SDK 所提供的類別。

    `DefineTable`方法會建立本機存放區符合所提供的類型，在欄位中的表格`ToDoItem`在此情況下。 類型不一定要包含在遠端資料庫中的所有資料行。 很可能儲存只資料行的子集。

        // QSTodoService.cs

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }


* `todoTable`成員`QSTodoService`為`IMobileServiceSyncTable`而不是輸入`IMobileServiceTable`。 IMobileServiceSyncTable 指示所有建立、 讀取、 更新及刪除 (CRUD) 到本機存放區資料庫的資料表作業。

    您決定何時這些變更推入 Azure Mobile 應用程式後端，則可電話`IMobileServiceSyncContext.PushAsync()`。 同步處理內容可協助追蹤與發送的用戶端應用程式已修改的時機的所有資料表中的變更來保留資料表的關聯性`PushAsync`稱為。

    提供的程式碼通話`QSTodoService.SyncAsync()`同步每當 todoitem 清單會在重新整理或新增 todoitem 是否已完成。 每個本機變更之後應用程式同步處理。 如果擷取內容來執行已擱置的本機更新追蹤資料表時，該提取作業會自動觸發程序內容推入第一次。

    所有在遠端中提供的程式碼，記錄`TodoItem`資料表查詢，但，也可以藉由傳遞查詢識別碼篩選記錄和查詢以`PushAsync`。 如需詳細資訊，請參閱一節中[離線 Azure 行動應用程式中的資料同步處理]的*累加同步處理*。

        // QSTodoService.cs
        public async Task SyncAsync()
        {
            try
            {
                await client.SyncContext.PushAsync();
                await todoTable.PullAsync("allTodoItems", todoTable.CreateQuery()); // query ID is used for incremental sync
            }

            catch (MobileServiceInvalidOperationException e)
            {
                Console.Error.WriteLine(@"Sync Failed: {0}", e.Message);
            }
        }


## <a name="additional-resources"></a>其他資源

* [離線資料 Azure 行動應用程式中的同步處理]
* [Azure 行動應用程式.NET SDK 如何][8]

<!-- Images -->

<!-- URLs. -->
[建立 Xamarin iOS 應用程式]: app-service-mobile-xamarin-ios-get-started.md
[離線資料 Azure 行動應用程式中的同步處理]: app-service-mobile-offline-data-sync.md
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md