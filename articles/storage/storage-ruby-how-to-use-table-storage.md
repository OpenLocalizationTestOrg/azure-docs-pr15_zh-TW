<properties
    pageTitle="如何使用 [注音標示從 Azure 資料表儲存體 |Microsoft Azure"
    description="結構化的資料儲存在雲端使用 Azure 資料表儲存體，NoSQL 資料存放區。"
    services="storage"
    documentationCenter="ruby"
    authors="tamram"
    manager="carmonm"
    editor=""/>
<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="ruby"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="how-to-use-azure-table-storage-from-ruby"></a>如何使用 [注音標示從 Azure 資料表儲存體

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>概觀

本指南為您示範如何執行常見的案例使用 Azure 資料表服務。 使用並列文字 API 寫入範例。 涵蓋的案例包含**建立及刪除資料表、 插入及查詢表格中的項目**。

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>建立拼音應用程式

如需相關指示如何建立並列文字應用程式，請參閱[Azure VM 滑軌 Web 應用程式上的 [注音標示](../virtual-machines/linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md)。


## <a name="configure-your-application-to-access-storage"></a>設定您的應用程式，以存取儲存空間

若要使用 Azure 儲存空間，您需要下載並使用並列文字 azure 封裝包括一組方便時觀看文件庫與儲存其他服務。

### <a name="use-rubygems-to-obtain-the-package"></a>使用 RubyGems 取得套件

1. 使用**PowerShell** (Windows)、**終端機**(Mac)，或**被**(Unix) 等命令列介面。

2. 在安裝健身和相依性的 [命令] 視窗中，輸入**健身安裝 azure** 。

### <a name="import-the-package"></a>匯入套件

使用您偏好使用的文字編輯器，請移至您想要使用儲存的位置並列文字檔案頂端新增下列動作︰

    require "azure"

## <a name="set-up-an-azure-storage-connection"></a>設定連線至 Azure 儲存體

Azure 模組會讀取環境變數**AZURE\_儲存\_帳戶**和**AZURE\_儲存\_存取\_鍵**連線至 Azure 儲存體帳戶所需的資訊。 如果沒有設定這些環境變數，您必須指定下列程式碼中使用**Azure::TableService**之前的帳戶資訊︰

    Azure.config.storage_account_name = "<your azure storage account>"
    Azure.config.storage_access_key = "<your azure storage access key>"

若要從傳統或 Azure 入口網站中的資源管理員儲存帳戶中取得這些值︰

1. [Azure 入口網站](https://portal.azure.com)登入。
2. 瀏覽至您想要使用的儲存空間帳戶。
3. 在右側設定刀，按一下 [**便捷鍵**。
4. 在 Access 鍵刀出現，您會看到便捷鍵 1 和 2 的便捷鍵。 您可以使用下列其中一項。 
5. 按一下 [複製] 圖示，以將金鑰複製到剪貼簿。 

若要取得這些值傳統的儲存空間帳戶在傳統 Azure 入口網站︰

1. [傳統 Azure 入口網站](https://manage.windowsazure.com)登入。
2. 瀏覽至您想要使用的儲存空間帳戶。
3. 按一下 [**管理便捷鍵**底部的功能窗格]。
4. 在 [pop 設定] 對話方塊中，您會看到儲存體帳戶名稱、 存取主索引鍵和次要便捷鍵。 存取鍵，您可以使用的主索引項目或第二個項目。 
5. 按一下 [複製] 圖示，以將金鑰複製到剪貼簿。

## <a name="create-a-table"></a>建立表格

**Azure::TableService**物件可讓您使用的資料表和項目。 若要建立表格，使用**建立\_table()**方法。 下列範例會建立資料表或列印錯誤時，如果有任何。

    azure_table_service = Azure::TableService.new
    begin
      azure_table_service.create_table("testtable")
    rescue
      puts $!
    end

## <a name="add-an-entity-to-a-table"></a>實體新增至資料表

若要新增的實體，先建立雜湊物件，以定義您的實體屬性。 請注意，針對每個實體您必須指定的**PartitionKey**和**RowKey**。 這些是您的實體的唯一識別碼，並可查詢速度比您其他屬性的值。 Azure 儲存體使用**PartitionKey**自動許多儲存空間節點上發佈表格的項目。 使用相同的**PartitionKey**的項目會儲存在相同的節點。 **RowKey**是實體所屬的磁碟分割內的唯一識別碼。

    entity = { "content" => "test entity",
      :PartitionKey => "test-partition-key", :RowKey => "1" }
    azure_table_service.insert_entity("testtable", entity)

## <a name="update-an-entity"></a>更新實體

有多種方法可更新現有的實體︰

* **更新\_entity():**更新現有的實體取代。
* **合併\_entity():**更新現有的實體合併到現有的實體的新屬性值。
* **插入\_或\_合併\_entity():**更新現有的實體取代。 如果沒有實體存在，會插入新的項目︰
* **插入\_或\_取代\_entity():**更新現有的實體合併到現有的實體的新屬性值。 如果沒有實體存在，會插入新的項目。

下列範例會示範更新實體使用**更新\_entity()**:

    entity = { "content" => "test entity with updated content",
      :PartitionKey => "test-partition-key", :RowKey => "1" }
    azure_table_service.update_entity("testtable", entity)

使用**更新\_entity()**和**合併\_entity()**，如果您要更新的實體不存在，然後更新作業會失敗。 因此如果您想要儲存的實體，不論是否已經存在，您應該改用**插入\_或\_取代\_entity()**或**插入\_或\_合併\_entity()**。

## <a name="work-with-groups-of-entities"></a>使用群組的項目

有時讓提交合作以確保原子處理伺服器批次中的多個作業。 若要完成的工作，您需要先建立**批次**的物件，然後使用**執行\_batch()** **TableService**上的方法。 下列範例會示範送出兩個實體 RowKey 2 和 3 以批次。 請注意，它只適用於使用相同的 PartitionKey 的項目。

    azure_table_service = Azure::TableService.new
    batch = Azure::Storage::Table::Batch.new("testtable",
      "test-partition-key") do
      insert "2", { "content" => "new content 2" }
      insert "3", { "content" => "new content 3" }
    end
    results = azure_table_service.execute_batch(batch)

## <a name="query-for-an-entity"></a>實體的查詢

若要查詢的實體表格中，請使用**取得\_entity()**資料表名稱， **PartitionKey**和**RowKey**傳遞的方法。

    result = azure_table_service.get_entity("testtable", "test-partition-key",
      "1")

## <a name="query-a-set-of-entities"></a>查詢的實體一組

若要查詢一組的表格中的項目，請建立查詢雜湊物件及使用**查詢\_entities()**方法。 下列範例會示範快速使用相同的**PartitionKey**的所有項目︰

    query = { :filter => "PartitionKey eq 'test-partition-key'" }
    result, token = azure_table_service.query_entities("testtable", query)

> [AZURE.NOTE] 如果結果是單一查詢傳回的接續權杖會傳回您可以用來擷取後續頁面太大。

## <a name="query-a-subset-of-entity-properties"></a>查詢實體屬性的子集

查詢的資料表可以取得一些屬性的實體。 這項技巧，稱為 「 規劃 」，減少頻寬，以及改善查詢效能，特別是大型的項目。 使用 select 子句，並將您想要顯示用戶端屬性的名稱。

    query = { :filter => "PartitionKey eq 'test-partition-key'",
      :select => ["content"] }
    result, token = azure_table_service.query_entities("testtable", query)

## <a name="delete-an-entity"></a>刪除實體

若要刪除的實體，請使用**刪除\_entity()**方法。 您需要在 [名稱] 的資料表，其中包含的實體 PartitionKey 和 RowKey 的實體傳遞。

        azure_table_service.delete_entity("testtable", "test-partition-key", "1")

## <a name="delete-a-table"></a>刪除表格

若要刪除的表格，請使用**刪除\_table()**方法，並在您想要刪除的資料表名稱的階段。

        azure_table_service.delete_table("testtable")

## <a name="next-steps"></a>後續步驟

若要瞭解更複雜的儲存區工作，請遵循以下連結︰

- [Azure 儲存體小組部落格](http://blogs.msdn.com/b/windowsazurestorage/)
- 在 GitHub 上的[[注音標示的 azure SDK](http://github.com/WindowsAzure/azure-sdk-for-ruby)存放庫
