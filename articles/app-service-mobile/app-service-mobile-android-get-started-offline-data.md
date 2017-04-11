<properties
    pageTitle="啟用離線同步處理 Azure 行動應用程式 (Android)"
    description="瞭解如何使用應用程式服務行動應用程式在 Android 應用程式中的快取及同步處理離線資料"
    documentationCenter="android"
    authors="ysxu"
    manager="erikre"
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="enable-offline-sync-for-your-android-mobile-app"></a>啟用離線同步處理您在 Android 行動應用程式

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>概觀

本教學課程涵蓋 Azure Mobile 應用程式的離線同步處理功能 Android 版。 離線同步處理可讓使用者在行動應用程式與之互動&mdash;檢視、 新增或修改資料&mdash;即使沒有網路連線。 變更會儲存在本機資料庫。 一旦裝置恢復連線時，這些變更會會與遠端後端同步處理。

如果這是您的第一個體驗 Azure 行動應用程式，您應該先完成教學課程中[建立 Android 應用程式]。 如果您不使用下載快速入門伺服器專案，您必須新增至您的專案的資料存取擴充套件。 如需關於伺服器擴充套件的詳細資訊，請參閱[使用.NET 後端伺服器 SDK Azure Mobile 應用程式](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。

若要進一步瞭解離線同步處理功能，請參閱[Azure 行動應用程式中的 [離線資料同步處理]的主題。

## <a name="update-the-app-to-support-offline-sync"></a>支援離線同步處理應用程式更新

離線同步處理，您讀取與寫入*同步處理資料表*（使用*IMobileServiceSyncTable*介面），這是您的裝置上**SQLite**資料庫。

推入和提取裝置和 Azure 行動服務之間的變更，您可以使用 [*同步處理內容*(*MobileServiceClient.SyncContext*)，您初始化與本機資料庫來儲存本機資料。

1. 在 [ `TodoActivity.java`，註解的現有定義`mToDoTable`並取消註解的同步處理資料表版本︰

        private MobileServiceSyncTable<ToDoItem> mToDoTable;

2. 在 [`onCreate`方法，註解現有初始化`mToDoTable`並取消註解此定義︰

        mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);

3. 在 [`refreshItemsFromTable`註解的定義`results`並取消註解此定義︰

        // Offline Sync
        final List<ToDoItem> results = refreshItemsFromMobileServiceTableSyncTable();

4. 註解的定義`refreshItemsFromMobileServiceTable`。

5. 取消註解的定義`refreshItemsFromMobileServiceTableSyncTable`:

        private List<ToDoItem> refreshItemsFromMobileServiceTableSyncTable() throws ExecutionException, InterruptedException {
            //sync the data
            sync().get();
            Query query = QueryOperations.field("complete").
                    eq(val(false));
            return mToDoTable.read(query).get();
        }

6. 取消註解的定義`sync`:

        private AsyncTask<Void, Void, Void> sync() {
            AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
                @Override
                protected Void doInBackground(Void... params) {
                    try {
                        MobileServiceSyncContext syncContext = mClient.getSyncContext();
                        syncContext.push().get();
                        mToDoTable.pull(null).get();
                    } catch (final Exception e) {
                        createAndShowDialogFromTask(e, "Error");
                    }
                    return null;
                }
            };
            return runAsyncTask(task);
        }

## <a name="test-the-app"></a>測試應用程式

在此區段中，您可以測試 WiFi 與行為，然後關閉 [WiFi 建立離線的案例。

當您新增資料的項目時，他們存放在本機的 SQLite 存放區，但不是同步處理到行動訊息服務 [鍵，直到您按下 [**重新整理**] 按鈕。 其他應用程式可能會有不同的需求，相關的資料時需要同步處理，但示範進行本教學課程中明確要求使用者。

當您按下該按鈕時，就會啟動新的背景工作。 前推入本機存放區中使用同步處理內容，然後從 Azure 提取所有變更資料區域的資料表所做的變更。

### <a name="offline-testing"></a>離線測試

1. *飛機模式*中將裝置或模擬器。 這樣會建立離線的案例。

2. 新增一些*ToDo*項目，或將標示為完成的某些項目。 結束裝置或模擬器 （或強制關閉應用程式），然後重新啟動。 驗證您的變更已保存在裝置上因為他們會保留在本機 SQLite 存放區。

3. 檢視使用 SQL 工具例如*SQL Server Management Studio*中或其他用戶端，例如*Fiddler*或*郵差*Azure *TodoItem*表格的內容。 確認新的項目有_未_同步處理至伺服器

    + 針對 Node.js 後端，移至[Azure 入口網站](https://portal.azure.com/)，並在您的行動應用程式後端按一下**簡單的表格** > **TodoItem**若要檢視的內容`TodoItem`表格。
    + .NET 後端檢視目錄的 SQL 工具，例如*SQL Server Management Studio*中，或其他用戶端，例如*Fiddler*或*郵差*。

4. 開啟 WiFi 裝置或模擬器中。 接下來，請按 [**重新整理**] 按鈕。

5. Azure 入口網站中一次檢視 TodoItem 資料。 新增及變更 TodoItems 現在應該會出現。

## <a name="additional-resources"></a>其他資源

* [離線資料 Azure 行動應用程式中的同步處理]

* [雲端封面︰ Azure 行動服務中的離線同步處理]\(附註︰ 視訊行動服務]，但在 Azure 行動應用程式中以類似的方式運作的離線同步處理\)


<!-- URLs. -->

[離線資料 Azure 行動應用程式中的同步處理]: app-service-mobile-offline-data-sync.md

[建立 Android 應用程式]: app-service-mobile-android-get-started.md

[Azure 行動服務中的雲端封面︰ 離線同步處理]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

