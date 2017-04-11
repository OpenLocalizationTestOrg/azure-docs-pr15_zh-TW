<properties
    pageTitle="匯入資料至 Azure 搜尋 Azure 入口網站中使用索引子 |Microsoft Azure |裝載的雲端搜尋服務"
    description="使用 [Azure 搜尋匯入資料] 精靈 Azure 入口網站中編目資料從 Azure Blob 儲存體、 表格 stroage、 SQL 資料庫和 SQL Server Azure Vm 上。"
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="jhubbard"
    editor=""
    tags="Azure Portal"/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="heidist"/>

# <a name="import-data-to-azure-search-using-the-portal"></a>若要使用入口網站的 Azure 搜尋匯入資料

Azure 入口網站提供**資料匯入**精靈 Azure 搜尋儀表板上載入索引中的資料。 

  ![命令列上的 [匯入資料][1]

在內部，精靈會設定，並叫用*索引*，自動化編製索引的程序的幾個步驟︰ 

- 連線至外部資料來源中目前的 Azure 訂閱
- 自動產生的索引結構描述根據來源資料結構
- 建立基礎資料來源擷取資料列集的文件
- 上傳文件，在您的搜尋服務的索引

您可以嘗試此 DocumentDB 中使用範例資料的工作流程。 如需相關指示，請造訪[開始使用 Azure Azure 入口網站中的搜尋](search-get-started-portal.md)。

## <a name="data-sources-supported-by-the-import-data-wizard"></a>[匯入資料] 精靈所支援的資料來源

[匯入資料] 精靈支援下列資料來源︰ 

- Azure SQL 資料庫
- SQL Server Azure VM 關聯式資料
- Azure DocumentDB
- Azure Blob 儲存體 （以預覽版本）
- Azure 資料表儲存體 （以預覽版本）

平面資料集是所需的資訊。 您只可以匯入單一資料表、 資料庫檢視]，或對等的資料結構。 您應該執行精靈之前，先建立這個資料結構。

請注意幾個索引子仍在預覽中，這表示重新定義方法 API 的 preview 版本。 如需詳細資訊，以及連結，請參閱[重新概觀](search-indexer-overview.md)。

## <a name="connect-to-your-data"></a>連線至您的資料

1. 登入[Azure 入口網站](https://portal.azure.com)和開啟服務儀表板。 您可以跳轉列顯示目前的訂閱中的現有的服務中，按一下**搜尋服務**。 

2. 按一下 [**匯入資料**至 [投影片開啟匯入資料刀的命令列上。  

3. 按一下 [指定索引所使用的資料來源定義**連線到您的資料**。 針對內部訂閱資料來源，精靈通常偵測並閱讀連線的詳細資訊，最小化整體設定需求。

| | |
|--------|------------|
|**現有的資料來源** | 如果您已經有索引子定義在您的搜尋服務，您可以選取另一個匯入的現有資料來源定義。|
|**Azure SQL 資料庫** | 在頁面上，或透過 ADO.NET 連線字串，可以指定讀取權限，資料庫使用者認證的服務名稱和資料庫名稱。 選擇 [連線字串] 選項，來檢視或自訂內容]。 <br/><br/>必須在頁面上指定的資料表或檢視提供資料列。 成功連接，提供下拉式清單，讓您可以選取範圍後，會出現這個選項。|
|**SQL Server Azure VM 上** | 指定完整的服務名稱、 使用者識別碼和密碼和資料庫做為連線字串。 若要使用這個資料來源，您必須先前安裝的憑證來加密連線本機存放區中。 <br/><br/>必須在頁面上指定的資料表或檢視提供資料列。 成功連接，提供下拉式清單，讓您可以選取範圍後，會出現這個選項。
|**DocumentDB** |需求包括帳戶、 資料庫及集合。 在索引中，將會包含在集合中的所有文件。 您可以定義查詢簡維或篩選資料列，或是偵測後續的資料重新整理作業的變更文件。|
|**Azure Blob 儲存體** | 需求包含的儲存空間帳戶和容器。 （選擇性） 如果 blob 名稱所遵循的虛擬的命名慣例，用於群組，您可以指定名稱的虛擬目錄部分為容器下資料夾。 如需詳細資訊，請參閱[編製索引作業 Blob 儲存體 （預覽版本）](search-howto-indexing-azure-blob-storage.md) 。 |
|**Azure 資料表儲存體** | 需求包含的儲存空間帳戶及表格名稱。 或者，您可以指定查詢來擷取子集的資料表。 如需詳細資訊，請參閱[編製索引作業資料表儲存體 （預覽版本）](search-howto-indexing-azure-tables.md) 。 |

## <a name="customize-target-index"></a>自訂目標索引

初步的索引通常被推斷資料集。 新增、 編輯或刪除完成結構描述] 欄位。 此外，以判斷其後續搜尋行為的功能變數層級設定屬性。

1. 在 [**自訂目標索引**中，指定名稱] 和 [**索引鍵**用於唯一識別每份文件。 索引鍵必須是字串。 如果欄位的值包含空白或虛線請務必**將資料匯入**隱藏這些字元驗證檢查中設定進階的選項]。

2. 檢閱並修改其餘的欄位。 欄位名稱與類型是通常是為您填入。 您可以變更資料類型。

3. 設定為每個欄位的索引屬性︰

 - 可擷取搜尋結果中傳回的欄位。
 - 可篩選可讓功能變數，在篩選運算式中參考。
 - 可排序可讓要用於排序的欄位。
 - Facetable 啟用多面向導覽的欄位。
 - 可讓全文檢索搜尋。
  
4. 如果您想要指定語言分析欄位層級，請按一下 [**分析**] 索引標籤。 僅限語言分析器可以指定這一次。 使用自訂的分析或非語言分析，例如關鍵字、 圖樣等，會要求的程式碼。

 - 按一下 [ **Searchable**指定欄位的全文檢索搜尋，並啟用 [分析] 下拉式清單]。
 - 選擇您想要的分析]。 如需詳細資訊，請參閱[建立多語言的文件索引](search-language-support.md)。

5. 按一下 [啟用選取在欄位上預先輸入的查詢建議**Suggester** ]。


## <a name="import-your-data"></a>將資料匯入

1. 在**匯入您的資料**，提供索引器的名稱。 回收的 [匯入資料] 精靈的產品是索引子。 之後，如果您想要檢視或編輯它，您會選取它從入口網站，而不是由重新執行精靈。 

2. 指定的排程，根據服務已佈建地區和時區。

3. 設定進階的選項，來指定臨界值是否編製索引，可以繼續如果文件會遭到捨棄。 此外，您可以指定是否允許**索引鍵**欄位包含空格及斜線。  

## <a name="edit-an-existing-indexer"></a>編輯現有的索引

在服務儀表板中，按兩下 [索引] 方塊，以投影片為您的訂閱所建立的所有成員的清單。 按兩下其中一項以執行、 編輯或刪除索引子。 您可以取代現有的另一個索引，變更資料來源，並編製索引作業期間設定錯誤臨界值的選項。

## <a name="edit-an-existing-index"></a>編輯現有的索引

Azure 搜尋索引的結構化更新會要求的索引，其中包含的刪除索引，重新建立索引，重新載入資料重建。 結構化的更新包含變更資料類型和重新命名或刪除欄位。

不需要重建的編輯包括新增新的欄位，變更計分設定檔，變更 suggesters，或變更語言分析器。 如需詳細資訊，請參閱[更新索引](https://msdn.microsoft.com/library/azure/dn800964.aspx)。

## <a name="next-step"></a>下一步

檢閱這些連結，深入瞭解索引子︰

- [編製索引的 Azure SQL 資料庫](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [DocumentDB 編製索引](../documentdb/documentdb-search-indexer.md)
- [編製索引作業 Blob 儲存體 （預覽版本）](search-howto-indexing-azure-blob-storage.md)
- [編製索引的資料表儲存體 （預覽版本）](search-howto-indexing-azure-tables.md)



<!--Image references-->
[1]: ./media/search-import-data-portal/search-import-data-command.png

