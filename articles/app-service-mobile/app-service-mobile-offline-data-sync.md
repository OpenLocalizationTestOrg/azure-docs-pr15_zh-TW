<properties
    pageTitle="離線資料 Azure 行動應用程式中的同步處理 |Microsoft Azure"
    description="概念性參考和 Azure Mobile 應用程式的離線資料同步處理功能的概觀"
    documentationCenter="windows"
    authors="adrianhall"
    manager="dwrede"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="offline-data-sync-in-azure-mobile-apps"></a>離線資料 Azure 行動應用程式中的同步處理

## <a name="what-is-offline-data-sync"></a>什麼是離線資料同步處理？

離線資料同步處理功能的用戶端和伺服器 SDK 功能，可讓您更輕鬆地建立沒有網路連線運作的應用程式開發人員 Azure 行動應用程式。

離線模式您的應用程式時，使用者仍然可以建立及修改資料，並將其儲存至本機存放區。 應用程式恢復連線時，就可以與您 Azure Mobile 應用程式後端同步處理本機的變更。 此功能也包含支援偵測到同一筆記錄後端和用戶端上變更時的衝突。 伺服器或用戶端上，然後可處理衝突。

離線同步處理有許多優點︰

* 改善應用程式回應能力快取伺服器本機裝置上的資料
* 建立網路問題時維持實用的強大應用程式
* 允許使用者建立及修改資料，即使是在沒有網路存取，請以幾乎不連線支援的案例
* 在多個裝置上同步處理資料和在相同記錄修改的兩個裝置時，偵測衝突
* 在高延遲或計量付費網路上的網路使用的限制

下列教學課程顯示如何新增至行動用戶端使用 Azure 行動應用程式的 [離線同步處理︰

* [Android︰ 啟用離線同步處理]
* [iOS︰ 啟用離線同步處理]
* [Xamarin iOS︰ 啟用離線同步處理]
* [Xamarin Android︰ 啟用離線同步處理]
* [Xamarin.Forms︰ 啟用離線同步處理](app-service-mobile-xamarin-forms-get-started-offline-data.md)
* [通用的 Windows 平台︰ 啟用離線同步處理]

## <a name="what-is-a-sync-table"></a>同步處理資料表是什麼？

若要存取 「 / 表格 」 端點，Azure 行動用戶端 Sdk 提供介面例如`IMobileServiceTable`（.NET 用戶端 SDK） 或`MSTable`（iOS 用戶端）。 這些 Api 直接連接到 Azure Mobile 應用程式後端和用戶端裝置沒有網路連線將會失敗。

若要支援離線使用，您的應用程式應該改用*同步處理資料表*的 Api，例如`IMobileServiceSyncTable`（.NET 用戶端 SDK） 或`MSSyncTable`（iOS 用戶端）。 所有相同 CRUD 作業 （建立、 讀取、 更新、 刪除） 的運作對同步處理資料表的 Api，但現在他們要讀取或寫入到*本機存放區*。 在執行任何同步處理資料表作業之前，必須先初始化本機存放區。

## <a name="what-is-a-local-store"></a>什麼是本機存放區？

在本機存放區是在用戶端裝置資料保存層。 Azure Mobile 應用程式的用戶端 Sdk 提供預設本機存放區實作。 在 Windows、 Xamarin 和 Android，它會根據 SQLite;在 iOS，它會根據核心資料。

若要在 Windows Phone 或 Windows 市集 8.1 上使用 SQLite 型實作，您必須安裝 SQLite 副檔名。 如需詳細資訊，請參閱[通用 Windows 平台︰ 啟用離線同步處理]。 Android 和 iOS 隨附 SQLite 裝置作業系統本身中的版本，因此不需要參考 SQLite 的版本。

開發人員也可以實作自己本機存放區。 例如，如果您想要在行動用戶端上加密格式儲存資料，您可以定義 SQLCipher 用於加密的本機存放區。

## <a name="what-is-a-sync-context"></a>什麼是同步處理內容？

*同步處理的內容*是與相關聯的行動用戶端物件 (例如`IMobileServiceClient`或`MSClient`) 及追蹤與同步處理資料表所做的變更。 同步處理內容維護*作業佇列中*其中保存 CUD 作業 （建立、 更新、 刪除） 的排序的清單稍後會傳送至伺服器。

在本機存放區功能相關聯的同步處理內容，例如使用初始化方法`IMobileServicesSyncContext.InitializeAsync(localstore)` [.NET 用戶端 SDK]中。

## <a name="how-sync-works"></a>同步處理的運作方式離線

使用同步處理的資料表，用戶端程式碼控制何時會 Azure Mobile 應用程式後端同步處理本機的變更。 *推入*本機變更在通話之前，不會傳送到後端。 同樣地，本機存放區填入新的資料，*擷取*資料在通話時。

* **推入**︰ 推入同步處理內容作業，並將所有 CUD 變更都傳送自上次推入。 請注意，它不可以傳送只個別表格的變更，因為否則作業無法傳送順序。 推入執行一系列的其餘部分的來電至您的 Azure Mobile 應用程式後端，依序將修改您的伺服器資料庫。

* **擷取**︰ 擷取執行每個資料表為基礎，並可以自訂使用查詢來擷取 server 資料的子集。 Azure 行動用戶端 Sdk 會將本機存放區，然後插入所得到的資料。

* **隱含推入**︰ 擷取如果提取針對表格中有待處理的本機更新執行，將第一次執行推入同步處理內容。 這可協助最小化已經佇列中的變更，並從伺服器的新資料之間的衝突。

* **累加同步處理**︰ 擷取作業的第一個參數是只適用於用戶端的*查詢名稱*。 如果您使用的非 null 查詢名稱，Azure 行動 SDK 會執行*累加同步處理*。
  每次提取作業會傳回的結果，最新的一組`updatedAt`SDK 本機系統資料表中儲存的結果集中的時間戳記。 後續提取作業只會擷取記錄後該時間戳記。

  若要使用累加同步處理，您的伺服器必須傳回有意義`updatedAt`值，也必須支援依這個欄位排序。 不過，由於 SDK updatedAt 欄位上新增自己的排序，您無法使用都有自己的擷取查詢`$orderBy$`子句。

  查詢名稱可以是任何字串您選擇，但必須是唯一的應用程式中的每一個邏輯查詢。
  否則，不同的提取作業可能會覆寫相同的累加同步處理時間戳記，在查詢可以傳回不正確的結果。

  如果查詢參數，建立唯一的查詢名稱的其中一個方法是將參數值。
  舉個例說，如果您要篩選的使用者識別碼，則可能是查詢名稱，如下所示 （在 C#):

        await todoTable.PullAsync("todoItems" + userid,
            syncTable.Where(u => u.UserId == userid));

  如果您想要退出累加同步處理，則傳遞`null`為查詢識別碼。 在此情況下，在每次呼叫擷取所有記錄`PullAsync`，這可能無效。

* **Purging**︰ 您可以清除本機存放區中使用的內容`IMobileServiceSyncTable.PurgeAsync`。
  如果您有資料失效在用戶端資料庫，或如果您想要捨棄所有擱置的變更，這可能是必要。

  清除會清除本機存放區中的資料表。 如果有擱置與資料庫伺服器的同步處理作業，請清除會引發例外狀況，除非*強制清除*參數設。

  用戶端過期資料的範例，假設 「 待辦清單 」 範例中，Device1 只會提取未完成的項目。 然後，todoitem 」 購買牛奶 」 標示為完成伺服器上的另一個裝置。 不過，Device1 仍然有 「 購買牛奶 」 todoitem 本機存放區中因為它只抽取未標示為完成的項目。 清除會清除此過期的項目。

## <a name="next-steps"></a>後續步驟

* [iOS︰ 啟用離線同步處理]
* [Xamarin iOS︰ 啟用離線同步處理]
* [Xamarin Android︰ 啟用離線同步處理]
* [通用的 Windows 平台︰ 啟用離線同步處理]

<!-- Links -->
[.NET 用戶端 SDK]: app-service-mobile-dotnet-how-to-use-client-library.md
[Android︰ 啟用離線同步處理]: app-service-mobile-android-get-started-offline-data.md
[iOS︰ 啟用離線同步處理]: app-service-mobile-ios-get-started-offline-data.md
[Xamarin iOS︰ 啟用離線同步處理]: app-service-mobile-xamarin-ios-get-started-offline-data.md
[Xamarin Android︰ 啟用離線同步處理]: app-service-mobile-xamarin-ios-get-started-offline-data.md
[通用的 Windows 平台︰ 啟用離線同步處理]: app-service-mobile-windows-store-dotnet-get-started-offline-data.md
