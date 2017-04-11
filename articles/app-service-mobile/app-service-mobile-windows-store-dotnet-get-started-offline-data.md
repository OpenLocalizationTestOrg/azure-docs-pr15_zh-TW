<properties
    pageTitle="啟用離線同步處理通用 Windows 平台 (UWP) 應用程式與 Mobile 應用程式 |Azure 應用程式服務"
    description="瞭解如何使用通用 Windows 平台 (UWP) 應用程式中的 [快取及同步處理的離線資料 Azure 行動應用程式。"
    documentationCenter="windows"
    authors="adrianhall"
    manager="erikre"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="enable-offline-sync-for-your-windows-app"></a>啟用離線同步處理您的 Windows 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>概觀

本教學課程教您如何將離線支援新增至通用 Windows 平台 (UWP) 應用程式中用 Azure Mobile 應用程式後端。 離線同步處理可讓使用者在行動應用程式檢視、 新增或修改資料-，即使沒有網路連線與之互動。 變更會儲存在本機資料庫。 一旦裝置恢復連線時，這些變更會會與遠端後端同步處理。

在本教學課程中，您可以更新 UWP 應用程式從專案教學課程[建立 Windows 應用程式]，以支援離線 Azure Mobile 應用程式的功能。 如果您不使用下載快速入門伺服器專案，您必須新增至您的專案的資料存取擴充套件。 如需關於伺服器擴充套件的詳細資訊，請參閱[使用.NET 後端伺服器 SDK Azure Mobile 應用程式](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。

若要進一步瞭解離線同步處理功能，請參閱[Azure 行動應用程式中的 [離線資料同步處理]的主題。

## <a name="requirements"></a>需求

本教學課程需要下列的必要條件︰

* 執行 Windows 8.1 或更新版本的 visual Studio 2013。
* 完成的[建立 Windows 應用程式][建立的 windows 應用程式]。
* [Azure 行動服務 SQLite 存放區][sqlite store nuget]
* [通用 Windows 平台開發 SQLite](http://www.sqlite.org/downloads)

## <a name="update-the-client-app-to-support-offline-features"></a>更新支援離線功能的用戶端應用程式

Azure Mobile 應用程式離線功能可讓您離線的情境中操作時，與本機資料庫互動。 若要在您的應用程式中使用這些功能，您初始化[SyncContext] [synccontext]本機存放區。 然後參考透過[IMobileServiceSyncTable][IMobileServiceSyncTable]介面表格。 SQLite 作為在裝置上的本機存放區。

1. 安裝[SQLite runtime 通用的 Windows 平台](http://sqlite.org/2016/sqlite-uwp-3120200.vsix)。

2. 在 Visual Studio 中，開啟您完成[建立 Windows 應用程式]教學課程中 UWP 應用程式專案的 NuGet 套件經理。
    搜尋並安裝**Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet 套件。

4. 在方案總管中，以滑鼠右鍵按一下**參照** > **加入參考** > **通用 Windows** > **副檔名]**，然後啟用**SQLite 通用 Windows 平台**和**Visual c + + 2015 Runtime 通用 Windows 平台應用程式**。

    ![新增 SQLite UWP 參照][1]

5. 開啟 MainPage.xaml.cs 檔案，並取消註解`#define OFFLINE_SYNC_ENABLED`定義。

6. 在 Visual Studio 中，按**F5**鍵重建並執行用戶端應用程式。 之前您啟用 [離線同步處理應用程式運作方式相同。 不過，本機資料庫現在填入可供離線使用的資料。

## <a name="update-sync"></a>若要中斷連線後端應用程式更新

在此區段中，您可以中斷連線至您的行動應用程式後端模擬的離線的情況。 當您新增資料的項目時，您的例外狀況處理常式會告訴您的應用程式位於離線模式。 在這個狀態，新的項目會新增本機存放區，並會同步處理到行動應用程式後端推入下一步執行在連線狀態時。

1. 編輯 App.xaml.cs 共用專案中。 註解**MobileServiceClient**初始化，並新增下列命令，使用不正確的行動應用程式 URL:

         public static MobileServiceClient MobileService = new MobileServiceClient("https://your-service.azurewebsites.fail");

    您也可以停用 wifi 和裝置上的行動電話網路示範離線的行為，或使用飛機模式。

2. 按**F5**以建立並執行應用程式。 請注意您的同步處理應用程式啟動時，在重新整理失敗。

3. 輸入新的項目，然後請注意，推入失敗[CancelledByNetworkError]狀態為 [每次您按一下 [**儲存**]。 不過，新的 todo 項目的在於本機存放區直到行動應用程式後端推入。  在生產應用程式中，如果您隱藏這些例外狀況的用戶端應用程式看起來像是它仍會連線到行動應用程式後端。

4. 關閉應用程式，並重新啟動以驗證您建立的新項目會保留本機存放區。

5. （選用）在 Visual Studio 中，開啟 [**伺服器總管**]。 瀏覽至您的資料庫中**Azure**->**SQL 資料庫**。 以滑鼠右鍵按一下您的資料庫，然後選取 [ **SQL Server 物件檔案總管中開啟**。 現在您可以瀏覽至您的 SQL 資料庫表格和其內容。 確認已不變更後端資料庫中的資料。

6. （選用）使用其他工具，例如 Fiddler 或郵差來查詢您的行動後端，在表單中使用取得查詢`https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`。

## <a name="update-online-app"></a>若要重新連接您的行動應用程式後端應用程式更新

在此區段中，您重新連線到行動應用程式後端的應用程式。 這些變更模擬應用程式上的網路重新連線。

當您第一次執行應用程式，`OnNavigatedTo`事件處理常式呼叫`InitLocalStoreAsync`。 這個方法依序呼叫`SyncAsync`同步處理您的本機存放區後端資料庫。 嘗試啟動同步處理應用程式。

1. 在共用專案中，開啟 App.xaml.cs，並取消註解您先前的初始化`MobileServiceClient`使用正確的行動應用程式的 URL。

2. 按**F5**鍵重建並執行應用程式。 與使用推入和提取作業 Azure Mobile 應用程式後端應用程式同步處理的本機變更時`OnNavigatedTo`事件處理常式執行。

3. （選用）檢視使用 SQL Server 物件總管或 Fiddler 等其他工具更新的資料。 Azure Mobile 應用程式後端資料庫與本機存放區之間，具有已同步處理通知資料。

4. 在應用程式，按一下 [完成這些本機存放區中的幾個項目旁的核取方塊。

  `UpdateCheckedTodoItem`通話`SyncAsync`與 Mobile 應用程式後端的同步處理每個完成項目。 `SyncAsync`通話提取推入及。 不過，**當您執行對資料表的用戶端已進行變更，擷取推入一律自動執行**。 此行為可確保所有的資料表，以及 [資料庫關聯圖本機存放區中保持一致。 此問題可能會導致非預期的推入。  如需有關這種行為的詳細資訊，請參閱[離線 Azure 行動應用程式中的資料同步處理]。


##<a name="api-summary"></a>API 摘要

若要支援的行動電話服務離線功能，我們使用[IMobileServiceSyncTable]介面並初始化[MobileServiceClient.SyncContext] [synccontext]使用本機 SQLite 資料庫。 離線時，如果為應用程式仍會連線時對本機存放區中發生的作業，也會使用標準 CRUD 作業的行動應用程式。 與伺服器同步處理本機存放區中使用下列方法︰

*  **[PushAsync]**由於此方法是[IMobileServicesSyncContext]的成員，跨所有資料表的變更會被後端。 只使用本機變更的記錄會傳送至伺服器。

* **[PullAsync] ** 
   [IMobileServiceSyncTable]從開始擷取。 當有資料表中的追蹤的修訂時，請隱含的推入會執行，請確定以及關聯的本機存放區中的所有資料表都保持一致。 *PushOtherTables*參數可控制是否內容中的其他資料表推入隱含的推入。 *查詢*參數會使用[IMobileServiceTableQuery<T> ] [ IMobileServiceTableQuery] 
  或 OData 查詢字串傳回的資料進行篩選。 *內容*參數用來定義累加同步處理。 如需詳細資訊，請參閱 [離線 Azure 行動應用程式中的資料同步處理](app-service-mobile-offline-data-sync.md#how-sync-works)。

* **[PurgeAsync]**您的應用程式應該定期呼叫此方法，以清除資料失效從本機存放區。 當您需要清除不同步處理過的任何變更時，請使用*強制*參數。

如需有關這些概念的詳細資訊，請參閱[離線 Azure 行動應用程式中的資料同步處理](app-service-mobile-offline-data-sync.md#how-sync-works)。

## <a name="more-info"></a>其他資訊

下列主題提供的行動應用程式的離線同步處理功能的其他背景資訊︰

* [離線資料 Azure 行動應用程式中的同步處理]
* [Azure 行動應用程式.NET SDK 如何][8]

<!-- Anchors. -->
[Update the app to support offline features]: #enable-offline-app
[Update the sync behavior of the app]: #update-sync
[Update the app to reconnect your Mobile Apps backend]: #update-online-app
[Next Steps]:#next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[離線資料 Azure 行動應用程式中的同步處理]: app-service-mobile-offline-data-sync.md
[建立 windows 應用程式]: app-service-mobile-windows-store-dotnet-get-started.md
[SQLite for Windows 8.1]: http://go.microsoft.com/fwlink/?LinkID=716919
[SQLite for Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkID=716920
[SQLite for Windows 10]: http://go.microsoft.com/fwlink/?LinkID=716921
[synccontext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[sqlite store nuget]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[IMobileServiceSyncTable]: https://msdn.microsoft.com/library/azure/mt691742(v=azure.10).aspx
[IMobileServiceTableQuery]: https://msdn.microsoft.com/library/azure/dn250631(v=azure.10).aspx
[IMobileServicesSyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynccontext(v=azure.10).aspx
[MobileServicePushFailedException]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushfailedexception(v=azure.10).aspx
[Status]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushcompletionresult.status(v=azure.10).aspx
[CancelledByNetworkError]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushstatus(v=azure.10).aspx
[PullAsync]: https://msdn.microsoft.com/library/azure/mt667558(v=azure.10).aspx
[PushAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileservicesynccontextextensions.pushasync(v=azure.10).aspx
[PurgeAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable.purgeasync(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
