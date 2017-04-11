<properties
    pageTitle="啟用離線同步處理 Azure 行動應用程式 (Xamarin.Forms) |Microsoft Azure"
    description="瞭解如何使用 Xamarin.Forms 應用程式中的快取及同步處理離線資料應用程式服務行動應用程式"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="yochayk"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="adrianha"/>

# <a name="enable-offline-sync-for-your-xamarinforms-mobile-app"></a>啟用離線同步處理 Xamarin.Forms 行動應用程式

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>概觀
本教學課程介紹 Xamarin.Forms Azure Mobile 應用程式的離線同步處理功能。 離線同步處理可讓使用者在行動應用程式檢視、 新增或修改資料，即使沒有網路連線與之互動。 變更會儲存在本機資料庫。 裝置恢復連線後，這些變更會與 [遠端服務進行同步處理。

本教學課程根據 Xamarin.Forms 快速入門解決方案中，當您完成教學課程 [建立 Xamarin iOS 應用程式]，您建立的行動應用程式。 快速入門解決方案的 Xamarin.Forms 包含支援只需要啟用的離線同步處理的程式碼。 在本教學課程中，您可以更新快速入門方案，以開啟 [離線 Azure Mobile 應用程式的功能。 我們也醒目提示的應用程式中的特定離線的程式碼。 如果您不使用下載快速入門解決方案，您必須新增至您的專案的資料存取擴充套件。 更多關於伺服器擴充套件的詳細資訊，請參閱[使用.NET 後端伺服器 Azure Mobile 應用程式的 SDK][1]。

若要進一步瞭解離線同步處理功能，請參閱[Azure 行動應用程式中的 [離線資料同步處理]的主題[2]。

## <a name="enable-offline-sync-functionality-in-the-quickstart-solution"></a>啟用快速入門方案中的離線同步處理功能

離線同步處理的程式碼會包含在專案中，使用 C# 前置處理器指引。 當**離線\_同步處理\_啟用**定義符號、 在建立包含這些程式碼路徑。 For Windows 應用程式，您也必須安裝 SQLite 平台。

1. 在 Visual Studio 中，以滑鼠右鍵按一下方案 >**管理 NuGet 套件解決方案...**]，然後搜尋並安裝方案中的所有專案的**Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet 套件。

2. 在方案總管中開啟 TodoItemManager.cs 檔案從與**可攜式**專案，在 [名稱] 這是可攜式類別庫專案，然後取消註解下列前置處理器指示詞︰

        #define OFFLINE_SYNC_ENABLED

4. （選用）若要支援 Windows 裝置，請安裝下列 SQLite runtime 封裝其中一項︰

    * **Windows 8.1 執行階段︰**安裝[windows 8.1 SQLite][3]。
    * **Windows Phone 8.1:**安裝[Windows Phone 8.1 版的 SQLite][4]。
    * **通用 Windows 平台**安裝[的通用 Windows 標準 SQLite][5]。

    快速入門不包含通用 Windows 專案，雖然通用 Windows 平台支援使用 Xamarin 表單。

5. （選用）在每個專案中 Windows 應用程式，請以滑鼠右鍵按一下**參照** > **加入參考**，展開 [ **Windows** ] 資料夾 >**副檔名**。
    啟用適當**SQLite for Windows** SDK 以及**Visual c + + 2013 Runtime Windows** SDK。
    SQLite SDK 名稱略有不同的每個 Windows 平台。

## <a name="review-the-client-sync-code"></a>檢閱用戶端同步處理的程式碼

以下是 [已中包含哪些產品教學課程的程式碼內的簡短概觀`#if OFFLINE_SYNC_ENABLED`指示詞。 離線同步處理功能可攜式類別庫專案中 TodoItemManager.cs 專案檔案中。 此功能的概觀，請參閱[離線 Azure 行動應用程式中的資料同步處理][2]。

* 在執行任何表格作業之前，必須先初始化本機存放區。 使用下列程式碼中**TodoItemManager**類別建構函式初始化本機存放區資料庫︰

        var store = new MobileServiceSQLiteStore(OfflineDbPath);
        store.DefineTable<TodoItem>();

        //Initializes the SyncContext using the default IMobileServiceSyncHandler.
        this.client.SyncContext.InitializeAsync(store);

        this.todoTable = client.GetSyncTable<TodoItem>();

    將此程式碼會建立新的本機 SQLite 資料庫使用**MobileServiceSQLiteStore**類別。

    **DefineTable**方法會建立本機存放區符合所提供的類型中的欄位中的資料表。  類型不一定要包含在遠端資料庫中的所有資料行。 很可能儲存的資料行的子集合。

* **TodoItemManager**的**todoTable**欄位是，而不是**IMobileServiceTable** **IMobileServiceSyncTable**類型。 針對您所有的本機資料庫建立、 讀取此課程用途更新和刪除 (CRUD) 表格作業。 您決定何時這些變更推入行動應用程式後端呼叫**PushAsync** **IMobileServiceSyncContext**上。 同步處理內容可協助追蹤與發送呼叫**PushAsync**時，用戶端應用程式已修改的所有資料表中的變更來保留資料表的關聯性。

    呼叫下列**SyncAsync**方法與 Mobile 應用程式後端同步處理至︰

        public async Task SyncAsync()
        {
            ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

            try
            {
                await this.client.SyncContext.PushAsync();

                await this.todoTable.PullAsync(
                    "allTodoItems",
                    this.todoTable.CreateQuery());
            }
            catch (MobileServicePushFailedException exc)
            {
                if (exc.PushResult != null)
                {
                    syncErrors = exc.PushResult.Errors;
                }
            }

            // Simple error/conflict handling.
            if (syncErrors != null)
            {
                foreach (var error in syncErrors)
                {
                    if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
                    {
                        //Update failed, reverting to server's copy.
                        await error.CancelAndUpdateItemAsync(error.Result);
                    }
                    else
                    {
                        // Discard local change.
                        await error.CancelAndDiscardItemAsync();
                    }

                    Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.",
                        error.TableName, error.Item["id"]);
                }
            }
        }

    此範例使用簡單的錯誤處理預設的同步處理常式。 實際的應用程式會使用自訂的**IMobileServiceSyncHandler**實作處理網路條件和伺服器衝突等各種不同的錯誤。

## <a name="offline-sync-considerations"></a>離線同步處理考量

在範例中，啟動時，並要求同步處理時，只會呼叫**SyncAsync**方法。  若要初始化同步處理，在 Android 或 iOS 應用程式，請下拉式功能表上項目] 清單中。windows 上，使用 [**同步處理**] 按鈕。 您也可以在真實世界應用程式中，讓同步處理觸發程序的網路狀態變更時。

擷取針對表格中有擱置中執行時本機更新追蹤的內容，引動程序會自動提取作業前面的內容推入。 時重新整理、 新增和完成這個範例中的項目，您可以略過明確**PushAsync**通話。

在提供的程式碼，遠端 TodoItem 資料表中的所有記錄的都查詢，但，也有可能要**PushAsync**傳遞查詢識別碼和查詢中篩選記錄。 如需詳細資訊，請參閱一節中[離線 Azure 行動應用程式中的資料同步處理]的*累加同步處理*[2]。

## <a name="run-the-client-app"></a>執行用戶端應用程式

現在已啟用 [離線同步處理的情況下，執行用戶端應用程式來填入本機存放區資料庫的每個平台的至少一次。 之後，模擬離線的案例，然後離線應用程式時，請修改本機存放區中的資料。

## <a name="update-the-sync-behavior-of-the-client-app"></a>更新用戶端應用程式同步處理的行為

在 [此節]，修改用戶端專案，以使用您的後端無效的應用程式 URL 來模擬離線的案例。 或者，您可以關閉網路連線將您的裝置移至 [飛機模式]。  當您新增或變更資料的項目時，這些變更存放在本機存放區，但不是同步處理到後端的資料存放區 [直到重新建立連線。

1. 在方案總管中開啟 Constants.cs 專案檔案從**可攜式**專案並變更的值`ApplicationURL`指向無效的 URL:

        public static string ApplicationURL = @"https://your-service.azurewebsites.net/";

2. 從**可攜式**project 中，開啟 TodoItemManager.cs 檔案，然後**嘗試...攔截**區塊**SyncAsync**中新增 [**掌握**基底**例外狀況**類別]。 **此 try**寫入的例外狀況訊息主控台，，如下所示︰

            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }

3. 建立並執行用戶端應用程式。  新增一些新的項目。 請注意的例外狀況已有登入後端與同步處理的每一次嘗試主控台。 這些新的項目只存在於本機存放區，直到行動後端推入。 用戶端應用程式看起來像是已連接到後端支援所有建立，讀取、 更新、 刪除 (CRUD) 作業。

4. 關閉應用程式，並重新啟動以驗證您建立的新項目會保留本機存放區。

5. （選用）使用 Visual Studio 檢視 Azure SQL 資料庫表格查看已不變更後端資料庫中的資料。

    在 Visual Studio 中，開啟 [**伺服器總管**]。 瀏覽至您的資料庫中**Azure**->**SQL 資料庫**。 以滑鼠右鍵按一下您的資料庫，然後選取 [ **SQL Server 物件檔案總管中開啟**。 現在您可以瀏覽至您的 SQL 資料庫表格和其內容。

## <a name="update-the-client-app-to-reconnect-your-mobile-backend"></a>若要重新連接您行動裝置的後端用戶端應用程式更新

在此區段中，重新連線到行動裝置的後端，模擬回到線上狀態的應用程式的應用程式。 當您執行的重新整理筆勢時，資料會同步處理至您的行動後端。

1. 重新開啟 Constants.cs。 修正`applicationURL`指向正確的 URL。

2. 重建並執行的用戶端應用程式。 嘗試啟動後，使用行動應用程式後端同步處理應用程式。 請確認沒有例外狀況的身分登入偵錯主控台。

3. （選用）檢視與更新的資料使用 SQL Server 物件總管或其他工具，例如 Fiddler 或[郵差][6]。 請注意已同步處理後端資料庫與本機存放區之間的資料。

    請注意資料已同步處理資料庫與本機存放區之間，包含您的應用程式已中斷連線時，您新增的項目。

## <a name="additional-resources"></a>其他資源

* [離線資料 Azure 行動應用程式中的同步處理][2]
* [Azure 行動應用程式.NET SDK 如何][8]

<!-- URLs. -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: app-service-mobile-offline-data-sync.md
[3]: http://go.microsoft.com/fwlink/p/?LinkID=716919
[4]: http://go.microsoft.com/fwlink/p/?LinkID=716920
[5]: http://sqlite.org/2016/sqlite-uwp-3120200.vsix
[6]: https://www.getpostman.com/
[7]: http://www.telerik.com/fiddler
[8]: app-service-mobile-dotnet-how-to-use-client-library.md