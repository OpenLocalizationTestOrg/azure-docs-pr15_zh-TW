<properties
    pageTitle="管理 windows Azure 儲存體中並行"
    description="如何管理並行 Blob、 佇列、 表格和檔案的服務"
    services="storage"
    documentationCenter=""
    authors="jasonnewyork"
    manager="tadb"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="jahogg"/>

# <a name="managing-concurrency-in-microsoft-azure-storage"></a>管理 windows Azure 儲存體中並行

## <a name="overview"></a>概觀

現代網際網路為基礎的應用程式通常會有多個使用者檢視並同時更新的資料。 需要謹慎考慮如何提供使用者，特別是針對多個使用者可以更新的相同資料的位置的案例如預期呈現的體驗的應用程式開發人員。 有三個主要資料並行策略通常會考慮在開發人員︰  


1.  樂觀 – 上次更新將其更新的一部分確認的資料是否變更自應用程式的應用程式執行讀取該資料。 例如，如果兩個檢視的 wiki 頁面上的使用者對同一個頁面更新然後 wiki 平台必須確定第二個更新不覆寫第一次的更新 – 及兩個使用者瞭解其更新是否已經順利。 此策略最常用的 web 應用程式。
2.  封閉式並行 – 執行更新查詢的應用程式會鎖定，直到鎖定釋放更新資料時，防止其他使用者的物件。 例如位置的母片會執行更新母片/從屬資料複寫情境中操作母片會通常會保留獨佔鎖定長的時間，以確保沒有任何人也可以將其更新的資料。
3.  最後一個作者獲勝 – 繼續但不驗證是否任何其他應用程式有應用程式後第一次更新資料的任何更新作業 」 可讓一種方法可讀取的資料。 此策略 （或缺少的正式的策略） 通常用資料的方式有多個使用者會存取相同的資料沒有可能性的分割位置。 也很實用的正在處理短暫資料流。  

本文將概略瞭解如何 Azure 儲存體平台開發提供簡化第一本課程的支援所有三個這些並行策略。  

## <a name="azure-storage--simplifies-cloud-development"></a>Azure 儲存空間 – 雲端簡化
Azure 儲存服務會支援所有的三個策略，雖然特殊的樂觀並行提供完整的支援，因為它的設計運用儲存服務認可資料插入或所有的更新作業進一步存取資料會看到最新的更新時可確保強式一致性模型的能力。 使用一致性模型的儲存空間平台寫入執行一個使用者，因此複雜開發用戶端應用程式，以防止不一致性影響使用者的其他使用者可以看到更新的資料時，有之間延隔時間。  

除了選取適當的並行策略開發人員也應該要瞭解如何儲存平台隔離變更 – 特別變更過交易相同的物件。 Azure 儲存服務使用快照集隔離，以允許讀取與寫入作業單一的磁碟分割中發生的作業。 與其他隔離層級，不同的快照集隔離可確保所有讀取請都參閱時所發生的更新 – 基本上傳回，認可的最後一個值，更新時處理交易一致資料的快照。  

## <a name="managing-concurrency-in-blob-storage"></a>管理並行中 Blob 儲存體
您可以選擇使用 [開放式或悲觀並行模型管理二進位大型物件和容器 blob 服務的存取權。 如果您沒有明確地指定策略最後一個寫入獲勝是預設值。  

### <a name="optimistic-concurrency-for-blobs-and-containers"></a>樂觀二進位大型物件和容器  

儲存服務會將識別碼指派給儲存每個物件。 每次更新作業的物件上執行時，此識別碼會更新。 識別碼會傳回用戶端使用 ETag （實體標記） 標頭 HTTP 通訊協定中定義的 HTTP GET 回應的一部分。 這類物件上執行更新使用者可以傳送原始 ETag 以及條件標頭中，以確保如果已符合特定條件 – 條件是需要儲存服務，以確保 ETag 更新要求中指定的值會儲存在儲存服務的相同 」 If 比對 「 標頭的這種情況下才會發生更新。  

此程序的大綱如下所示︰  

1.  擷取 blob 儲存體服務、 回應包含 HTTP ETag 標頭值，識別目前版本的儲存空間服務中的物件。
2.  當您更新 blob 時，包含您在步驟 1 中**If 符合**條件的標頭要求您傳送至服務所收到的 ETag 值。
3.  服務會比較 ETag 值在邀請中 blob 的現有 ETag 值。
4.  如果目前的 blob ETag 值的**If 符合**條件標頭中要求 ETag 版本不同，服務會傳回 412 錯誤用戶端。 指出用戶端的用戶端擷取它之後，另一個處理程序已更新 blob。
5.  如果目前的 blob ETag 值的**If 符合**條件標頭中要求 ETag 相同的版本，服務執行要求的操作，並更新以顯示它所建立的新版本的 blob 的目前 ETag 值。  

下列 C# 程式碼片段 （使用用戶端儲存文件庫 4.2.0） 會顯示一個簡單如何建構**If Match AccessCondition**範例根據從 blob 的已先前擷取或插入的內容存取 ETag 值。 然後會使用**AccessCondition**物件時，更新 blob: **AccessCondition**物件新增**If**標頭的要求。 如果其他處理程序有更新 blob，blob 服務會傳回 HTTP 412 （指定條件無法） 狀態訊息。 您可以下載完整的範例︰[使用 Azure 儲存體管理並行](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)。  

    // Retrieve the ETag from the newly created blob
    // Etag is already populated as UploadText should cause a PUT Blob call
    // to storage blob service which returns the etag in response.
    string orignalETag = blockBlob.Properties.ETag;

    // This code simulates an update by a third party.
    string helloText = "Blob updated by a third party.";

    // No etag, provided so orignal blob is overwritten (thus generating a new etag)
    blockBlob.UploadText(helloText);
    Console.WriteLine("Blob updated. Updated ETag = {0}",
    blockBlob.Properties.ETag);

    // Now try to update the blob using the orignal ETag provided when the blob was created
    try
    {
        Console.WriteLine("Trying to update blob using orignal etag to generate if-match access condition");
        blockBlob.UploadText(helloText,accessCondition:
        AccessCondition.GenerateIfMatchCondition(orignalETag));
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        {
            Console.WriteLine("Precondition failure as expected. Blob's orignal etag no longer matches");
            // TODO: client can decide on how it wants to handle the 3rd party updated content.
        }
        else
            throw;
    }  

儲存服務也包含支援其他條件的標頭例如**If 修改後**，**如果修改自**和**If-無-符合的項目**，以及產品組合。 如需詳細資訊請參閱 MSDN 上的[指定條件 Blob 服務作業的標題](http://msdn.microsoft.com/library/azure/dd179371.aspx)。  

下表摘要列出的接受例如**If 符合**條件的標題在邀請中，並可在回應中傳回 ETag 值的容器作業。  

| 作業                | 傳回容器 ETag 值 | 接受條件的標題 |
|:-------------------------|:-----------------------------|:----------------------------|
| 建立容器         | [是]                          | 無                          |
| 取得容器屬性 | [是]                          | 無                          |
| 取得容器中繼資料   | [是]                          | 無                          |
| 設定容器中繼資料   | [是]                          | [是]                         |
| 取得容器 ACL        | [是]                          | 無                          |
| 設定容器 ACL        | [是]                          | 是 （*）                     |
| 刪除容器         | 無                           | [是]                         |
| 租賃容器          | [是]                          | [是]                         |
| 清單二進位大型物件               | 無                           | 無                          |

(*)快取 SetContainerACL 所定義的權限，並更新這些權限採取哪些期間更新並不保證一致，才會傳播 30 秒。  

下表摘要列出的接受例如**If 符合**條件的頁首在邀請中，並可在回應中傳回 ETag 值的 blob 作業。

| 作業           | 傳回 ETag 值 | 接受條件的標題           |
|:--------------------|:-------------------|:--------------------------------------|
| 將 Blob            | [是]                | [是]                                   |
| 取得 Blob            | [是]                | [是]                                   |
| 取得 Blob 屬性 | [是]                | [是]                                   |
| 設定 Blob 屬性 | [是]                | [是]                                   |
| 取得 Blob 中繼資料   | [是]                | [是]                                   |
| 設定 Blob 中繼資料   | [是]                | [是]                                   |
| 租賃 Blob （*）      | [是]                | [是]                                   |
| 快照 Blob       | [是]                | [是]                                   |
| 複製 Blob           | [是]                | 是 （適用於來源與目的地 blob) |
| 中止複本 Blob     | 無                 | 無                                    |
| 刪除 Blob         | 無                 | [是]                                   |
| 將區塊           | 無                 | 無                                    |
| 將區塊清單      | [是]                | [是]                                   |
| 取得的封鎖清單      | [是]                | 無                                    |
| 將頁面            | [是]                | [是]                                   |
| 取得頁面範圍     | [是]                | [是]                                   |


(*)租賃 Blob 不會變更 blob 上的 ETag。  

### <a name="pessimistic-concurrency-for-blobs"></a>二進位大型物件的悲觀並行
若要鎖定 blob 供自己使用，您可以取得其[租用](http://msdn.microsoft.com/library/azure/ee691972.aspx)。 當您取得租用時，您指定的時間長度，您需要租用︰ 這可以是 15 到 60 秒的時間或無限為獨占鎖定的金額。 您可以更新有限租用延伸，並與其完成後，您可以釋放任何租用。 Blob 服務會自動釋放有限租用到期。  

租用啟用不同的同步處理的策略支援，包括獨佔寫入 / 共用讀取、 獨佔寫入 / 獨佔讀取和寫入的共用 / 獨佔讀取。 使用期存在的位置儲存服務會強制執行獨佔寫入 （保留、 設定及刪除作業） 不過確保讀取作業的專用性需要開發人員，以確保所有的用戶端應用程式，一次一個租用識別碼，而且只有一個用戶端使用具有有效租用識別碼。 讀取共用讀取中不包含租用識別碼結果的作業。  

下列 C# 程式碼片段顯示 30 秒 blob 上取得獨佔租用、 更新 blob 的內容，再放開租用的範例。 如果上已經有有效租用 blob 當您嘗試擷取新租用，blob 服務會傳回 「 HTTP (409) 衝突 」 狀態結果。 下列程式碼片段封裝租用資訊時，可讓您的要求更新 blob 儲存體服務中的使用**AccessCondition**物件。  您可以下載完整的範例︰[使用 Azure 儲存體管理並行](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)。

    // Acquire lease for 15 seconds
    string lease = blockBlob.AcquireLease(TimeSpan.FromSeconds(15), null);
    Console.WriteLine("Blob lease acquired. Lease = {0}", lease);

    // Update blob using lease. This operation will succeed
    const string helloText = "Blob updated";
    var accessCondition = AccessCondition.GenerateLeaseCondition(lease);
    blockBlob.UploadText(helloText, accessCondition: accessCondition);
    Console.WriteLine("Blob updated using an exclusive lease");

    //Simulate third party update to blob without lease
    try
    {
        // Below operation will fail as no valid lease provided
        Console.WriteLine("Trying to update blob without valid lease");
        blockBlob.UploadText("Update without lease, will fail");
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
            Console.WriteLine("Precondition failure as expected. Blob's lease does not match");
        else
            throw;
    }  

如果您嘗試租用 blob 寫入作業，而不傳遞租用識別碼，412 錯誤而失敗要求。 請注意，是否租用到期之前呼叫**UploadText**方法，但您仍傳遞租用識別碼，要求也錯誤而失敗**412** 。 如需有關管理租用到期時間和租用識別碼的詳細資訊，請參閱[租用 Blob](http://msdn.microsoft.com/library/azure/ee691972.aspx)的其餘部分文件。  

下列 blob 作業可用來管理封閉式並行租用︰  


-   將 Blob
-   取得 Blob
-   取得 Blob 屬性
-   設定 Blob 屬性
-   取得 Blob 中繼資料
-   設定 Blob 中繼資料
-   刪除 Blob
-   將區塊
-   將區塊清單
-   取得區塊清單
-   將頁面
-   取得頁面範圍
-   快照 Blob-租用識別碼選擇性如果租用存在
-   複製 Blob-租用識別碼所需的目的地 blob 上是否存在租用
-   中止複製 Blob-租賃需要如果租用存在於目的地 blob ID
-   租賃 Blob  

### <a name="pessimistic-concurrency-for-containers"></a>封閉式並行容器的
在容器上的租用啟用同步處理策略支援在二進位大型物件上 (獨佔寫入 / 共用讀取、 獨佔寫入 / 獨佔讀取和寫入的共用 / 獨佔讀取) 不過跟 blob 儲存服務只會強制執行刪除操作的專用性。 若要刪除的作用中的租用容器，用戶端必須包含與刪除要求的作用中租用識別碼。 所有其他容器作業成功租用容器，而不包括期 ID 是這種情況下共用作業。 如果是必要的更新 （放入或設定）] 或 [讀取的作業的專用性然後開發人員應確保所有用戶端使用租用識別碼，只有一次一個用戶端具有有效租用識別碼。  

下列容器作業可用來管理封閉式並行租用︰  

-   刪除容器
-   取得容器屬性
-   取得容器中繼資料
-   設定容器中繼資料
-   取得容器 ACL
-   設定容器 ACL
-   租賃容器  

如需詳細資訊，請參閱︰  

- [Blob 服務作業指定條件的標題](http://msdn.microsoft.com/library/azure/dd179371.aspx)
- [租賃容器](http://msdn.microsoft.com/library/azure/jj159103.aspx)
- [租賃 Blob](http://msdn.microsoft.com/library/azure/ee691972.aspx)

## <a name="managing-concurrency-in-the-table-service"></a>管理並行表格服務
表格服務會使用開放式並行存取檢查做為預設行為，當您正在使用實體，與您必須明確選擇要執行樂觀檢查 blob 服務不同。 資料表和 blob 服務之間的差異是，您可以僅限管理的實體並行行為而 blob 服務中，您可以管理容器與 blob 的並行。  

若要使用樂觀並核取 [取自表格儲存服務之後，另一個處理程序是否修改實體，您可以使用表格服務傳回實體時收到 ETag 值。 此程序的大綱如下所示︰  

1.  擷取資料表儲存體服務的實體、 回應包含 ETag 值，識別目前儲存服務中的實體相關聯的識別碼。
2.  當您更新實體時，包括您收到您傳送至服務要求強制**If 符合**標頭中的步驟 1 中 ETag 值。
3.  服務會比較 ETag 值在邀請中使用目前的實體 ETag 值。
4.  如果目前的實體 ETag 值不同的必要的**If Match**標頭中要求 ETag，服務會傳回用戶端 412 的錯誤。 指出用戶端的用戶端擷取它之後，另一個處理程序已更新實體。
5.  如果目前 ETag 值的實體是在邀請中強制**If 符合**標頭中 ETag 相同，或**如果符合**標頭包含萬用字元 （*），服務執行要求的操作，並更新目前顯示的已更新的實體 ETag 值。  

請注意，與 blob 服務不同表格服務要求包含**If**標頭中更新要求的用戶端。 不過，就可以強制無條件更新 （最後一個作者獲勝策略） 並略過並行檢查，如果用戶端設定**If 相符**的標頭中要求的萬用字元字元 （*）。  

下列 C# 程式碼片段會顯示 [先前建立或擷取有更新其電子郵件地址的客戶實體。 初始的插入或擷取作業儲存 ETag 物件中的值客戶，並執行取代作業時，此範例使用相同的物件執行個體，因為它會自動傳送給 ETag 值回到表格服務，讓服務檢查並行違規。 如果其他處理程序有更新資料表儲存體中的實體，服務會傳回 HTTP 412 （指定條件無法） 狀態訊息。  您可以下載完整的範例︰[使用 Azure 儲存體管理並行](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)。

    try
    {
        customer.Email = "updatedEmail@contoso.org";
        TableOperation replaceCustomer = TableOperation.Replace(customer);
        customerTable.Execute(replaceCustomer);
        Console.WriteLine("Replace operation succeeded.");
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == 412)
            Console.WriteLine("Optimistic concurrency violation – entity has changed since it was retrieved.");
        else
            throw;
    }  

若要明確停用並行核取，您應該**ETag**屬性設定**員工**物件 「 * 」 執行取代作業之前。  

    customer.ETag = "*";  

下表摘要列出資料表實體作業使用 ETag 值的方式︰

| 作業                | 傳回 ETag 值 | 需要 If 符合要求標頭 |
|:-------------------------|:-------------------|:---------------------------------|
| 查詢項目           | [是]                | 無                               |
| 插入實體            | [是]                | 無                               |
| 更新實體            | [是]                | [是]                              |
| 合併實體             | [是]                | [是]                              |
| 刪除實體            | 無                 | [是]                              |
| 插入或取代實體 | [是]                | 無                               |
| 插入或合併實體   | [是]                | 無                               |

請注意，執行**插入或取代實體**，並**插入或合併實體**作業*不*執行任何並行檢查，因為其不會傳送 ETag 值表格服務。  

在 [一般開發人員使用的表格，應該依賴樂觀開發可調整應用程式時。 如果需要封閉式鎖定，其中一種方法開發人員可能需要時存取資料表指派指定的 blob 的每個資料表，並試著採取 blob 租用之前作業系統表格。 此方法需要應用程式，以確保所有的資料存取路徑取得租用之前作業系統表格。 您也應該注意的最小的期是 15 秒需要延展性仔細考量。  

如需詳細資訊，請參閱︰  

- [實體的作業](http://msdn.microsoft.com/library/azure/dd179375.aspx)  

## <a name="managing-concurrency-in-the-queue-service"></a>管理並行佇列服務
在哪些並行是佇列服務中的其中一種情況是多個用戶端位置佇列中擷取的郵件。 當郵件從佇列中擷取時，回應包含郵件與 pop 回條的值所需刪除的郵件。 從佇列中，不會自動刪除郵件，但已擷取之後，就看不到其他用戶端 visibilitytimeout 參數所指定的時間間隔。 若要刪除的郵件已被處理，並 TimeNextVisible 所指定的時間之前的回應，其中的計算項目會根據 visibilitytimeout 參數值後預期擷取郵件用戶端。 Visibilitytimeout 的值會新增至哪些來決定 TimeNextVisible 的值擷取訊息的時間。  

佇列服務沒有支援樂觀或悲觀並行並這個處理擷取佇列中的訊息的原因用戶端應確保冪的方式處理郵件。 最後的作者獲勝策略用於更新作業，例如 SetQueueServiceProperties、 SetQueueMetaData、 SetQueueACL 和 UpdateMessage。  

如需詳細資訊，請參閱︰  

- [佇列中服務 REST API](http://msdn.microsoft.com/library/azure/dd179363.aspx)
- [收到的郵件](http://msdn.microsoft.com/library/azure/dd179474.aspx)  

## <a name="managing-concurrency-in-the-file-service"></a>管理並行檔案服務
使用兩個不同的通訊協定端點 – SMB 和其他可以存取的檔案的服務。 其餘服務沒有支援開放式鎖定或封閉式鎖定且所有更新的最後一個作者獲勝策略。 用戶端的裝載檔案共用可利用管理共用的檔案 – 包括能夠執行封閉式鎖定存取檔案系統鎖定機制。 當 SMB 用戶端開啟檔案時，它會指定檔案存取和共用模式。 將 「 撰寫 」 或 「 讀/寫 」 以及檔案共用模式的 「 無 」 的檔案存取選項，將會造成 SMB 用戶端在關閉檔案之後，鎖定檔案。 如果檔案 SMB 用戶端已鎖定檔案的位置嘗試執行其餘作業的其餘服務會傳回錯誤碼 SharingViolation 的狀態碼 409 （衝突）。  

SMB 用戶端開啟時的 [刪除的檔案，將其標記檔案，為擱置刪除所有其他 SMB 用戶端直到關閉開啟該檔案控點也一樣。 當檔案已經標示為 「 擱置中刪除時，任何的其餘部分操作，在該檔案會傳回錯誤碼 SMBDeletePending 的狀態碼 409 （衝突）。 因為可能的 SMB 用戶端移除擱置刪除標幟之前關閉檔案，不會傳回狀態碼 404 （找不到）。 換句話說，狀態碼 404 （找不到） 只預期時已移除檔案。 請注意，在擱置刪除狀態中小企業檔案時，它將不會包含在結果清單檔案中。另請注意，其餘刪除檔案] 和 [其餘刪除目錄作業認可完整而不會導致擱置刪除狀態。  

如需詳細資訊，請參閱︰  

- [管理檔案鎖定](http://msdn.microsoft.com/library/azure/dn194265.aspx)  

## <a name="summary-and-next-steps"></a>摘要] 和 [下一步
Microsoft Azure 儲存體服務設計不必開發人員危害或重新思考關鍵設計假設，例如並行和資料有他們授與對採取的一致性符合最複雜的線上應用程式的需求。  

在此部落格的參照完整的範例應用程式︰  

- [管理並行使用 Azure 儲存體-範例應用程式](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)  

如需詳細資料，請參閱 Azure 儲存體的詳細資訊︰  

- [Microsoft Azure 儲存體首頁](https://azure.microsoft.com/services/storage/)
- [Azure 儲存空間的簡介](storage-introduction.md)
- 快速入門的[Blob](storage-dotnet-how-to-use-blobs.md)、[表格](storage-dotnet-how-to-use-tables.md)、[佇列](storage-dotnet-how-to-use-queues.md)和[檔案](storage-dotnet-how-to-use-files.md)的儲存空間
- 儲存架構 – [Azure 儲存空間︰ 高度可用的雲端儲存服務強式一致性](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
