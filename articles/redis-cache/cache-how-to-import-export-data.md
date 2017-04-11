<properties 
    pageTitle="匯入及匯出 Azure Redis 快取中的資料 |Microsoft Azure" 
    description="瞭解如何匯入及匯出資料至您的進階版 Azure Redis 快取執行個體 blob 儲存體" 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/15/2016" 
    ms.author="sdanie"/>

# <a name="import-and-export-data-in-azure-redis-cache"></a>匯入及匯出 Azure Redis 快取中的資料

匯入/匯出是 Azure Redis 快取資料管理作業，可讓您將 Azure Redis 快取資料匯入或匯出 Azure Redis 快取中的資料，以匯入及匯出從進階版快取 Redis 快取資料庫 (RDB) 快照，以在 Azure 儲存體帳戶中的頁面 blob。 匯入/匯出可讓您移轉之間不同的 Azure Redis 快取執行個體，或填入資料之前使用快取。

本文提供指南匯入及匯出 Azure Redis 快取資料，並提供常見問題的解答。

>[AZURE.IMPORTANT] 匯入/匯出預覽中，而只適用於[進階版層](cache-premium-tier-intro.md)快取。

## <a name="import"></a>匯入

匯入可從任何執行中的任何雲端或環境，包括意指 Linux、 Windows，或任何雲端提供者，例如 Amazon Web 服務與其他人所執行的意指伺服器開啟意指相容 RDB 檔案。 匯入資料時輕鬆建立預先填入資料快取。 匯入期間 Azure Redis 快取從 Azure 儲存體載入記憶體 RDB 檔案，然後插入快取中的按鍵。

>[AZURE.NOTE] 在開始匯入作業，請確定 Redis 資料庫 (RDB) 檔案上傳到頁面 blob Azure 儲存體中，在相同的區域及為 Azure Redis 快取的執行個體的訂閱。 如需詳細資訊，請參閱[開始使用 Azure Blob 儲存體](../storage/storage-dotnet-how-to-use-blobs.md)。 如果您要匯出 RDB 檔案使用[Azure Redis 快取匯出](#export)功能，RDB 檔案已經儲存在頁面 blob，並準備好迎接匯入。

1. 若要匯入一或多個匯出快取 blob，[瀏覽至您的快取](cache-configure.md#configure-redis-cache-settings)中 Azure 入口網站，然後按一下 [從**設定**刀快取執行個體的 [**匯入資料**。

    ![匯入資料][cache-import-data]

2. 按一下 [**選擇 Blob(s)** ，選取包含要匯入之資料的儲存空間帳戶。

    ![選擇 [儲存帳戶][cache-import-choose-storage-account]

3. 按一下包含要匯入之資料的容器。

    ![選擇容器][cache-import-choose-container]

4. 選取要匯入區域左邊的 blob 名稱] 中，按一下一或多個二進位大型物件，然後按一下 [**選取**。

    ![選擇 [二進位大型物件][cache-import-choose-blobs]

5. 按一下 [開始匯入程序的 [**匯入**]。

    >[AZURE.IMPORTANT] 快取快取用戶端存取匯入程序期間並不會刪除快取中任何現有的資料。

    ![匯入][cache-import-blobs]

    從 Azure 入口網站的通知或檢視[稽核記錄](cache-configure.md#support-amp-troubleshooting-settings)中的事件，您可以監視匯入作業的進度。

    ![匯入進度][cache-import-data-import-complete] 


## <a name="export"></a>匯出

匯出可讓您匯出 Azure Redis 快取以 Redis 相容 RDB 檔案中儲存的資料。 若要將資料從 Azure Redis 快取執行個體之一，到另一個或另一個意指伺服器，您可以使用這項功能。 在匯出程序，暫存檔案會建立上 VM 裝載 Azure Redis 快取伺服器執行個體，且檔案上傳到指定的儲存空間帳戶。 匯出作業完成後任一狀態為成功或失敗，請刪除暫存檔案。

1. 若要匯出的快取到存放裝置、 Azure 入口網站中的 [[瀏覽至您的快取](cache-configure.md#configure-redis-cache-settings)目前的內容，然後按一下 [從**設定**刀快取執行個體的 [**匯出資料**。

    ![選擇儲存容器][cache-export-data-choose-storage-container]

2. 按一下 [**選擇存放容器**，然後選取您要的儲存的帳戶。 儲存帳戶必須是相同的訂閱和地區，為您的快取。

    >[AZURE.IMPORTANT] 匯入/匯出 blob 頁面，支援傳統與 ARM 儲存帳戶，但這次不支援的[Blob 儲存體帳戶](../storage/storage-blob-storage-tiers.md#blob-storage-accounts)的運作方式。

    ![儲存帳戶][cache-export-data-choose-account]

3. 選擇所要的 blob 容器，然後按一下 [**選取**]。 若要使用新的容器，按一下 [**新增容器**將其新增到第一次，然後從清單中選取]。

    ![選擇儲存容器][cache-export-data-container]

4. 輸入**Blob 名稱字首**，然後按一下 [開始匯出程序的 [**匯出**]。 Blob 名稱前置詞用來加上字首此匯出作業所產生的檔案名稱。

    ![匯出][cache-export-data]

    從 Azure 入口網站的通知或檢視[稽核記錄](cache-configure.md#support-amp-troubleshooting-settings)中的事件，您可以監視匯出作業的進度。

    ![][cache-export-data-export-complete]

    在匯出程序，快取保留為可供使用。


## <a name="importexport-faq"></a>匯入/匯出常見問題集

本節中的 [匯入/匯出] 功能的相關常見問題集的問題。

-   [哪些價格層可以使用匯入/匯出嗎？](#what-pricing-tiers-can-use-importexport)
-   [我是否可以從任何意指 server 匯入資料？](#can-i-import-data-from-any-redis-server)
-   [我的快取可匯入/匯出作業期間？](#will-my-cache-be-available-during-an-importexport-operation)
-   [可以使用匯入/匯出與意指叢集嗎？](#can-i-use-importexport-with-redis-cluster)
-   [如何匯入/匯出搭配使用自訂的資料庫時，設定？](#how-does-importexport-work-with-a-custom-databases-setting)
-   [有匯入/匯出不同意指持續性？](#how-is-importexport-different-from-redis-persistence)
-   [我能有自動化匯入/匯出使用 PowerShell、 CLI 或其他管理用戶端？](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
-   [我匯入/匯出作業期間收到逾時錯誤。這是什麼意思？](#i-received-a-timeout-error-during-my-importexport-operation.-what-does-it-mean)
-   [我的資料匯出至 Azure Blob 儲存體時，我收到錯誤。有何改變？](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage.-what-happened)


### <a name="what-pricing-tiers-can-use-importexport"></a>哪些價格層可以使用匯入/匯出嗎？

使用只能在價格層進階版中匯入/匯出]。

### <a name="can-i-import-data-from-any-redis-server"></a>我是否可以從任何意指 server 匯入資料？

是的匯入從 Azure Redis 快取執行個體匯出的資料，除了 RDB 檔案匯入任何雲端或 Linux，例如的環境中執行的 Windows，任何意指伺服器或雲端例如 Amazon Web 服務提供者。 若要這麼做，請從您要的意指伺服器 RDB 檔案上傳到頁面 blob Azure 儲存體帳戶，請在然後將您的進階版 Azure Redis 快取執行個體匯入。 例如，您可能要生產快取中的資料匯出及匯入到 [快取中執行的環境的一部分用於測試或移轉。 

### <a name="will-my-cache-be-available-during-an-importexport-operation"></a>我的快取可匯入/匯出作業期間？

-   **匯出**-快取都會持續為可用，而且您可以繼續使用您的快取匯出作業期間。
-   **匯入**的快取本可供使用，匯入作業完成後，您匯入作業啟動時，會變成無法使用。


### <a name="can-i-use-importexport-with-redis-cluster"></a>可以使用匯入/匯出與意指叢集嗎？

是的然後您可以匯入/匯出] 群組的快取與非叢集的快取之間。 自 Redis 叢集[僅支援資料庫 0](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)，0 以外的資料庫中的任何資料不會匯入]。 當群組的快取資料匯入時，按鍵重新之間的叢集擊碎分配。 

### <a name="how-does-importexport-work-with-a-custom-databases-setting"></a>如何匯入/匯出搭配使用自訂的資料庫時，設定？

匯入如果您設定的自訂值時有一些考量一些價格層有不同的[資料庫限制](cache-configure.md#databases)，`databases`期間建立快取設定。

-   匯入至價格與較低層時`databases`比您匯出的層的限制︰
    -   如果您使用的預設數目`databases`已針對所有價格層 16，會遺失任何資料。
    -   如果您使用的自訂數字的`databases`層到您要匯入，該落在限制範圍內會遺失任何資料。
    -   如果您匯出的資料包含超過新層的限制的資料庫中的資料，不會匯入這些較高的資料庫中的資料。

### <a name="how-is-importexport-different-from-redis-persistence"></a>有匯入/匯出不同意指持續性？

Azure Redis 快取持續可讓您保存意指至 Azure 儲存體中儲存的資料。 持續性設定時，Azure Redis 快取保留意指快取磁碟根據可設定的備份頻率意指二進位格式的快照。 如果嚴重的事件發生時停用 [主要] 和 [複本的快取，快取資料就會還原自動使用最新的快照。 如需詳細資訊，請參閱[如何設定進階版 Azure Redis 快取資料保存](cache-how-to-premium-persistence.md)。

匯入 / 匯出] 可讓您將資料或匯出 Azure Redis 快取。 它不會設定備份，並還原使用意指保存。


### <a name="can-i-automate-importexport-using-powershell-cli-or-other-management-clients"></a>我能有自動化匯入/匯出使用 PowerShell、 CLI 或其他管理用戶端？

是的如 PowerShell 指示請參閱[匯入意指快取](cache-howto-manage-redis-cache-powershell.md#to-import-a-redis-cache)和[匯出意指快取](cache-howto-manage-redis-cache-powershell.md#to-export-a-redis-cache)。



### <a name="i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean"></a>我匯入/匯出作業期間收到逾時錯誤。 這是什麼意思？

如果您仍在**資料匯入**或**匯出資料**的刀超過 15 分鐘左右，再開始操作，您會收到如下錯誤。

    The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.

若要解決這個問題，啟動匯入或匯出操作前 15 分鐘經過。

### <a name="i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened"></a>我的資料匯出至 Azure Blob 儲存體時，我收到錯誤。 有何改變？

匯入/匯出事務只 RDB 檔案儲存為網頁的二進位大型物件。 此時，包括以作用中且製作出酷炫的層 blob 儲存體帳戶不支援其他 blob 類型。


## <a name="next-steps"></a>後續步驟
瞭解如何使用更多進階快取功能。

-   [Azure Redis 快取進階版層簡介](cache-premium-tier-intro.md)    

  
<!-- IMAGES -->
[cache-settings-import-export-menu]: ./media/cache-how-to-import-export-data/cache-settings-import-export-menu.png
[cache-export-data-choose-account]: ./media/cache-how-to-import-export-data/cache-export-data-choose-account.png
[cache-export-data-choose-storage-container]: ./media/cache-how-to-import-export-data/cache-export-data-choose-storage-container.png
[cache-export-data-container]: ./media/cache-how-to-import-export-data/cache-export-data-container.png
[cache-export-data-export-complete]: ./media/cache-how-to-import-export-data/cache-export-data-export-complete.png
[cache-export-data]: ./media/cache-how-to-import-export-data/cache-export-data.png
[cache-import-data]: ./media/cache-how-to-import-export-data/cache-import-data.png
[cache-import-choose-storage-account]: ./media/cache-how-to-import-export-data/cache-import-choose-storage-account.png
[cache-import-choose-container]: ./media/cache-how-to-import-export-data/cache-import-choose-container.png
[cache-import-choose-blobs]: ./media/cache-how-to-import-export-data/cache-import-choose-blobs.png
[cache-import-blobs]: ./media/cache-how-to-import-export-data/cache-import-blobs.png
[cache-import-data-import-complete]: ./media/cache-how-to-import-export-data/cache-import-data-import-complete.png








