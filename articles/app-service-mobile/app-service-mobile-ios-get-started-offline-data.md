<properties
    pageTitle="啟用離線同步處理 Azure 行動應用程式 (iOS)"
    description="瞭解如何使用應用程式服務行動應用程式在 iOS 應用程式中的快取及同步處理離線資料"
    documentationCenter="ios"
    authors="ysxu"
    manager="yochayk"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="enable-offline-sync-for-your-ios-mobile-app"></a>啟用離線同步處理您的 iOS 行動應用程式

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>概觀

本教學課程涵蓋 iOS Azure 行動應用程式的離線同步處理功能。 離線同步處理可讓使用者能在行動應用程式與&mdash;檢視、 新增或修改資料&mdash;即使沒有網路連線。 變更會儲存在本機資料庫。一旦裝置恢復連線時，這些變更會會與遠端後端同步處理。

如果這是您的第一個體驗 Azure 行動應用程式，您應該先完成教學課程中[建立 iOS 應用程式]。 如果您不使用下載快速入門伺服器專案，您必須新增至您的專案的資料存取擴充套件。 如需關於伺服器擴充套件的詳細資訊，請參閱[使用.NET 後端伺服器 SDK Azure Mobile 應用程式](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。

若要進一步瞭解離線同步處理功能，請參閱[Azure 行動應用程式中的 [離線資料同步處理]的主題。

## <a name="review-sync"></a>檢閱用戶端同步處理的程式碼

您已下載的教學課程中[建立 iOS 應用程式]的用戶端專案包含支援使用核心資料為基礎的本機資料庫的離線同步處理的程式碼。 本節會是已經中包含哪些產品教學課程的程式碼的摘要。 此功能的概觀，請參閱[離線 Azure 行動應用程式中的資料同步處理]。

離線資料同步處理同步處理功能的 Azure 行動應用程式可讓使用者無法存取網路時，與本機資料庫互動。 若要在您的應用程式中使用這些功能，您初始化同步處理內容的`MSClient`及參照本機存放區。 然後參照到表格`MSSyncTable`介面。

1. 在**QSTodoService.m** (目標 C) 或**ToDoTableViewController.swift** （快速），請注意的成員類型`syncTable`是`MSSyncTable`。 離線同步處理使用這個同步處理資料表介面，而不是`MSTable`。 使用同步處理資料表時，所有作業會移至本機存放區，並只會與遠端的後端與明確的推入和提取作業同步處理。

    若要同步處理資料表的參照，請使用方法`syncTableWithName`上`MSClient`。 若要移除離線同步處理功能，請使用`tableWithName`改為。

2. 在執行任何表格作業之前，必須先初始化本機存放區。 以下是相關的程式碼。 
    
    **目標 C**:
    
    在 [`QSTodoService.init`方法︰
    
    
            MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
            self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
    
    
    **快速**︰
    
    在 [`ToDoTableViewController.viewDidLoad`方法︰
    
    
            let client = MSClient(applicationURLString: "http:// ...") // URI of the Mobile App
            let managedObjectContext = (UIApplication.sharedApplication().delegate as! AppDelegate).managedObjectContext!
            self.store = MSCoreDataStore(managedObjectContext: managedObjectContext)
            client.syncContext = MSSyncContext(delegate: nil, dataSource: self.store, callback: nil)
    

    這樣會建立本機存放區使用介面`MSCoreDataStore`，它所提供的行動應用程式 SDK。 您可以改為提供不同的本機存放區實作`MSSyncContextDataSource`通訊協定。 
    
    此外，第一個參數的`MSSyncContext`用來指定衝突處理常式。 因為我們有傳遞`nil`，我們會收到預設衝突處理常式，在任何衝突會失敗。
    
3. 現在讓我們來執行實際的同步處理作業，並從遠端的後端取得資料。

    **目標 C**:
    
    `syncData`前推入新的變更，然後呼叫`pullData`可從遠端的後端的資料。 在開啟的方法`pullData`取得符合查詢的新資料︰
    
    
            -(void)syncData:(QSCompletionBlock)completion
            {
                // push all changes in the sync context, then pull new data
                [self.client.syncContext pushWithCompletion:^(NSError *error) {
                    [self logErrorIfNotNil:error];
                    [self pullData:completion];
                }];
            }
    
            -(void)pullData:(QSCompletionBlock)completion
            {
                MSQuery *query = [self.syncTable query];
    
                // Pulls data from the remote server into the local table.
                // We're pulling all items and filtering in the view
                // query ID is used for incremental sync
                [self.syncTable pullWithQuery:query queryId:@"allTodoItems" completion:^(NSError *error) {
                    [self logErrorIfNotNil:error];
    
                    // Let the caller know that we have finished
                    if (completion != nil) {
                        dispatch_async(dispatch_get_main_queue(), completion);
                    }
                }];
            }
        
        
      **快速**︰
        
        
        func onRefresh(sender: UIRefreshControl!) {
            UIApplication.sharedApplication().networkActivityIndicatorVisible = true
            
            self.table!.pullWithQuery(self.table?.query(), queryId: "AllRecords") {
                (error) -> Void in
                
                UIApplication.sharedApplication().networkActivityIndicatorVisible = false
                
                if error != nil {
                    // A real application would handle various errors like network conditions,
                    // server conflicts, etc via the MSSyncContextDelegate
                    print("Error: \(error!.description)")
                    
                    // We will just discard our changes and keep the servers copy for simplicity
                    if let opErrors = error!.userInfo[MSErrorPushResultKey] as? Array<MSTableOperationError> {
                        for opError in opErrors {
                            print("Attempted operation to item \(opError.itemId)")
                            if (opError.operation == .Insert || opError.operation == .Delete) {
                                print("Insert/Delete, failed discarding changes")
                                opError.cancelOperationAndDiscardItemWithCompletion(nil)
                            } else {
                                print("Update failed, reverting to server's copy")
                                opError.cancelOperationAndUpdateItem(opError.serverItem!, completion: nil)
                            }
                        }
                    }
                }
                self.refreshControl?.endRefreshing()
            }
        } 
    
    
    在目標 C 版本中，在`syncData`，我們先呼叫`pushWithCompletion`的同步處理內容。 這個方法是成員之一`MSSyncContext`（而不是同步處理資料表本身） 因為它會變更推跨所有的資料表。 本機 （透過 CUD 操作） 的一些方式已修改的記錄會傳送至伺服器。 然後協助人員`pullData`稱為，哪些呼叫`MSSyncTable.pullWithQuery`擷取遠端資料，並將儲存在本機資料庫。
    
    在迅速版本中，有不需要呼叫`pushWithCompletion`。 這是因為沒有絕對必要的推入作業。 如果在同步處理的內容執行推入操作的資料表中有擱置中的任何變更，拉永遠問題推入第一次。 不過，如果您有一個以上的同步處理資料表時，它是最適合明確呼叫推入，以確保所有項目是一致跨相關的資料表。
    
    在 [目標 C] 和 [快速版本中，此方法`pullWithQuery`可讓您指定您想要擷取的查詢以篩選記錄。 在此範例中，查詢只擷取遠端中的所有記錄`TodoItem`表格。
    
    第二個參數`pullWithQuery`是用於*累加同步處理*的查詢 id。 累加同步處理擷取自上次同步處理，使用記錄的修改過的記錄`UpdatedAt`時間戳記 (稱為`updatedAt`本機存放區中。)查詢識別碼應該對每個應用程式中的邏輯查詢是唯一的描述性字串。 退出的累加同步處理，以傳遞`nil`為查詢識別碼。 請注意，這可以可能沒有效率，因為它會擷取每個提取作業的所有記錄。

5. 目標 C 應用程式會我們修改或新增資料時，使用者執行的重新整理筆勢，並啟動] 上。 迅速應用程式會同步處理當使用者執行重新整理手勢-啟動] 上。 

因為應用程式同步處理資料是修改 (目標 C) 或 （目標 C 與快速），會啟動應用程式時，應用程式會假設使用者在線上。 在另一個區段中，我們將會更新應用程式，以便在離線時，可以編輯使用者。

## <a name="review-core-data"></a>檢閱核心資料模型

在使用核心資料離線儲存時，必須先定義您的資料模型中的特定的表格和欄位。 範例應用程式已經包含資料模型具有適當的格式。 此區段中，我們會逐步執行下列表格及使用方式。

- 開啟**QSDataModel.xcdatamodeld**。 有四個表格定義-sdk，您可以使用的三個和 todo 一個表格的項目本身︰     *MS_TableOperations︰ 追蹤需要與伺服器同步處理的項目    *MS_TableOperationErrors︰ 追蹤離線同步處理期間發生任何錯誤    *MS_TableConfig︰ 追蹤上次更新時間上次同步處理作業所有提取作業    *TodoItem︰ 儲存的 todo 項目。 系統欄**createdAt** **updatedAt**，與**版本**是選擇性的系統內容。

>[AZURE.NOTE] Azure 行動應用程式 SDK 會保留資料行名稱該使用的 「**``**」。 您不應使用此前置詞系統資料行以外的任何項目，否則您的資料行名稱將修改使用遠端後端時。

- 使用離線同步處理功能時，您必須先定義系統資料表，如下所示。

    ### <a name="system-tables"></a>系統資料表

    **MS_TableOperations**

    ![][defining-core-data-tableoperations-entity]

  	| 屬性  |    類型     |
  	|----------- |   ------    |
  	| 識別碼         | 整數 64  |
  	| 項目識別碼     | 字串      |
  	| 屬性 | 二進位資料 |
  	| 表格      | 字串      |
  	| tableKind  | 整數 16  |

    <br>**MS_TableOperationErrors**

    ![][defining-core-data-tableoperationerrors-entity]

  	| 屬性  |    類型     |
  	|----------- |   ------    |
  	| 識別碼         | 字串      |
  	| operationId | 整數 64 |
  	| 屬性 | 二進位資料 |
  	| tableKind  | 整數 16  |

    <br>**MS_TableConfig**

    ![][defining-core-data-tableconfig-entity]

  	| 屬性  |    類型     |
  	|----------- |   ------    |
  	| 識別碼         | 字串      |
  	| 索引鍵        | 字串      |
  	| keyType    | 整數 64  |
  	| 表格      | 字串      |
  	| 值      | 字串      |

    ### <a name="data-table"></a>運算列表

    **TodoItem**

  	| 屬性    |  類型   | 附註                                                   |
  	|-----------   |  ------ | -------------------------------------------------------|
  	| 識別碼           | 標示為必要的字串  | 在遠端存放區中的主索引鍵                            |
  	| 完成     | 布林值 | todo 項目欄位                                        |
  	| 文字         | 字串  | todo 項目欄位                                        |
  	| createdAt | 日期    | （選擇性） 對應至 createdAt 系統屬性         |
  	| updatedAt | 日期    | （選擇性） 對應至 updatedAt 系統屬性         |
  	| 版本   | 字串  | （選擇性） 用於偵測衝突，版本的地圖 |


## <a name="setup-sync"></a>變更應用程式的同步處理行為

在此區段中，您將修改應用程式，使其不會在 [應用程式開始，或當插入和更新項目，不同步處理，但只有在重新整理] 動作按鈕執行時，才。

**目標 C**:

1. 在**QSTodoListViewController.m**，來變更**viewDidLoad**方法若要移除的呼叫`[self refresh]`結尾的方法。 現在，資料會無法使用應用程式啟動] 上的伺服器同步處理，但是會改為本機存放區的內容。

2. 在**QSTodoService.m**，修改的定義`addItem`，讓沒有同步處理後插入項目。 移除`self syncData`封鎖，並以下列取代︰

            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }

3. 修改的定義`completeItem`上方。移除的區塊`self syncData`取代為下列動作︰

            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }

**快速**︰

1. 在 [ `viewDidLoad` **ToDoTableViewController.swift**中的註解這兩行，若要停止同步處理應用程式開始。 在這份文件撰寫時，快速 Todo 應用程式並不會更新服務時新增或完成項目，只在 [應用程式開始。

        self.refreshControl?.beginRefreshing()
        self.onRefresh(self.refreshControl)


## <a name="test-app"></a>測試應用程式

在此區段中，您會連線到模擬離線案例無效的 URL。 當您新增資料的項目時，他們會存放在本機的核心資料存放區，但不是同步處理到行動裝置的後端。

1. 行動應用程式 URL 中**QSTodoService.m**變更無效的 URL，然後再次執行應用程式︰

    在 [QSTodoService.m**目標 C** :
    
            self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];
    
    **快速**ToDoTableViewController.swift 中︰

        let client = MSClient(applicationURLString: "https://sitename.azurewebsites.net.fail")

2. 新增一些 todo 項目。 結束模擬器 （或強制關閉應用程式），然後重新啟動。 確認已保存您的變更。

3. 檢視遠端 TodoItem 表格的內容︰

    + 針對 Node.js 後端，移至[Azure 入口網站](https://portal.azure.com/)，並在您的行動應用程式後端按一下**簡單的表格** > **TodoItem**若要檢視的內容`TodoItem`表格。
    + .NET 後端檢視目錄的 SQL 工具，例如 SQL Server Management Studio 中，或其他用戶端，例如 Fiddler 或郵差。

    確認新的項目*已伺服器進行同步處理*︰

4. URL 變更回正確上**QSTodoService.m**中，重新執行應用程式。 執行的重新整理筆勢取出項目清單。 您會看到進度微調按鈕。

5. 一次檢視 TodoItem 資料。 新增及變更 TodoItems 現在應該會出現。

## <a name="summary"></a>摘要

支援的離線同步處理功能，才能使用`MSSyncTable`介面和初始化`MSClient.syncContext`使用本機存放區。 在此情況下本機存放區是核心資料為基礎的資料庫。

使用時的核心資料的本機存放區，您必須定義使用[正確的系統內容](#review-core-data)的數個資料表。

標準的 CRUD 作業 Azure Mobile 應用程式運作的應用程式仍會連線，但發生針對本機存放區中的所有作業。

我們想要與伺服器同步處理本機存放區中，我們使用`MSSyncTable.pullWithQuery`方法。


## <a name="additional-resources"></a>其他資源

* [離線資料 Azure 行動應用程式中的同步處理]

* [雲端封面︰ Azure 行動服務中的離線同步處理]\(附註︰ 視訊行動服務]，但在 Azure 行動應用程式中以類似的方式運作的離線同步處理\)

<!-- URLs. -->


[建立 iOS 應用程式]: app-service-mobile-ios-get-started.md
[離線資料 Azure 行動應用程式中的同步處理]: app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Azure 行動服務中的雲端封面︰ 離線同步處理]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/en-us/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/
