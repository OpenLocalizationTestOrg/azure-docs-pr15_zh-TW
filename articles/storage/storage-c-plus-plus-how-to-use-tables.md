<properties
    pageTitle="如何使用資料表儲存體 （c + +） |Microsoft Azure"
    description="結構化的資料儲存在雲端使用 Azure 資料表儲存體，NoSQL 資料存放區。"
    services="storage"
    documentationCenter=".net"
    authors="dineshmurthy"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="dineshm"/>

# <a name="how-to-use-table-storage-from-c"></a>如何使用 c + + 資料表儲存體

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>概觀  
本指南會顯示如何使用 Azure 資料表儲存服務執行常見的案例。 範例 c + + 撰寫，並使用[Azure 儲存用戶端文件庫 c + +](https://github.com/Azure/azure-storage-cpp/blob/master/README.md)。 涵蓋的案例包括和**建立及刪除資料表****使用表格項目**。

>[AZURE.NOTE] 本指南為 c + + 版本 1.0.0 和上方目標 Azure 儲存用戶端的文件庫。 建議的版本是儲存用戶端程式庫 2.2.0，可透過[NuGet](http://www.nuget.org/packages/wastorage)或[GitHub](https://github.com/Azure/azure-storage-cpp/)。

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]
[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]


## <a name="create-a-c-application"></a>建立 c + + 應用程式  
本指南，您會使用可執行 c + + 應用程式中儲存功能。 若要這麼做，您將需要 c + + 安裝 Azure 儲存用戶端的文件庫和 Azure 訂閱中建立 Azure 儲存體帳戶。  

若要 c + + 安裝 Azure 儲存用戶端的文件庫，您可以使用以下兩種方法︰

-   **Linux:**按照在[Azure c + + 讀我檔案的儲存空間用戶端程式庫](https://github.com/Azure/azure-storage-cpp/blob/master/README.md)] 頁面上的指示。  
-   **Windows:**在 Visual Studio 中，按一下 [**工具 > NuGet 封裝管理員 > 封裝管理員主控台**。 [NuGet 封裝管理員主控台](http://docs.nuget.org/docs/start-here/using-the-package-manager-console)中輸入下列命令，然後按 Enter。  

        Install-Package wastorage

## <a name="configure-your-application-to-access-table-storage"></a>設定您的應用程式，來存取資料表儲存體  
新增下列包含您要使用 Azure 儲存空間 Api 來存取資料表 c + + 檔案頂端的陳述式︰  

    #include "was/storage_account.h"
    #include "was/table.h"

## <a name="set-up-an-azure-storage-connection-string"></a>設定 Azure 儲存連線字串  
Azure 儲存用戶端使用儲存空間的連接字串儲存端點和的認證以存取資料管理服務。 執行中時的用戶端應用程式，您必須提供下列格式的儲存空間連線字串。 [ *AccountName* ] 和 [ *AccountKey*值[Azure 入口網站](https://portal.azure.com)中所列的儲存空間帳戶使用您儲存的帳戶和儲存空間便捷鍵的名稱。 儲存帳戶及便捷鍵的資訊，請參閱[關於 Azure 儲存體帳戶](storage-create-storage-account.md)。 此範例會示範如何宣告靜態欄位，按住連線字串︰  

    // Define the connection string with your values.
    const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));

若要測試您的應用程式中您 Windows 型的本機電腦，您可以使用已安裝的[Azure SDK](https://azure.microsoft.com/downloads/)Azure[儲存模擬器](storage-use-emulator.md)。 儲存模擬器是公用程式模擬 Azure Blob 與佇列中，表格可在您的本機部署電腦上使用的服務。 下列範例會顯示如何宣告靜態欄位以保留您的本機存放區模擬器連線字串︰  

    // Define the connection string with Azure storage emulator.
    const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  

若要啟動 Azure 儲存模擬器，請按一下 [**開始**] 按鈕或按下 Windows 鍵。 開始輸入**Azure 儲存模擬器**，然後從應用程式清單中選取 [ **Microsoft Azure 儲存模擬器**。  

下列範例會假設您使用下列兩種方法之一來取得儲存連線字串。  

## <a name="retrieve-your-connection-string"></a>擷取連接字串  
您可以使用**cloud_storage_account**類別，代表您儲存的帳戶資訊。 若要從儲存連線字串中擷取您儲存的帳戶資訊，您可以使用剖析方法。

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

接下來，取得**cloud_table_client**類別的參照，如，可讓您取得資料表和儲存資料表儲存服務中的項目參照的物件。 下列程式碼會使用我們擷取上方的儲存空間帳戶物件，建立**cloud_table_client**物件︰  

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

## <a name="create-a-table"></a>建立表格
**Cloud_table_client**物件可讓您取得資料表和項目參照的物件。 下列程式碼建立**cloud_table_client**物件，並使用它來建立新的資料表。

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);  

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Retrieve a reference to a table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Create the table if it doesn't exist.
    table.create_if_not_exists();  

## <a name="add-an-entity-to-a-table"></a>實體新增至資料表
若要新增至資料表的實體，建立新的**table_entity**物件，然後將它**table_operation::insert_entity**。 下列程式碼會使用客戶的第一個名稱為列索引鍵和姓氏分割索引鍵。 在一起，實體的磁碟分割和列鍵可唯一識別資料表中的實體。 以相同的磁碟分割金鑰實體可比使用不同的磁碟分割的索引鍵，更快速查詢，但使用各種不同的磁碟分割鍵可讓更佳的平行作業延展性。 如需詳細資訊，請參閱[Microsoft Azure 儲存體效能與延展性檢查清單](storage-performance-checklist.md)。

下列程式碼儲存一些客戶資料建立新的執行個體的**table_entity** 。 程式碼接下來通話**table_operation::insert_entity**建立實體插入表格、 **table_operation**物件，然後新表格的實體關聯。 最後，程式碼會**cloud_table**物件上呼叫的執行方式。 與新**table_operation**表格服務的 [人員] 資料表中插入新的客戶實體傳送邀請。  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Retrieve a reference to a table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Create the table if it doesn't exist.
    table.create_if_not_exists();

    // Create a new customer entity.
    azure::storage::table_entity customer1(U("Harp"), U("Walter"));

    azure::storage::table_entity::properties_type& properties = customer1.properties();
    properties.reserve(2);
    properties[U("Email")] = azure::storage::entity_property(U("Walter@contoso.com"));

    properties[U("Phone")] = azure::storage::entity_property(U("425-555-0101"));

    // Create the table operation that inserts the customer entity.
    azure::storage::table_operation insert_operation = azure::storage::table_operation::insert_entity(customer1);

    // Execute the insert operation.
    azure::storage::table_result insert_result = table.execute(insert_operation);

## <a name="insert-a-batch-of-entities"></a>插入實體以批的次
您可以插入表格服務的實體批次中一個寫入作業。 下列程式碼會建立**table_batch_operation**物件，，並三個插入至該作業。 建立新的實體物件，設定其值，然後呼叫**table_batch_operation**物件，建立新的插入作業的關聯實體上的 [插入方法新增每個插入作業。 然後， **cloud_table.execute**稱為執行作業。  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Define a batch operation.
    azure::storage::table_batch_operation batch_operation;

    // Create a customer entity and add it to the table.
    azure::storage::table_entity customer1(U("Smith"), U("Jeff"));

    azure::storage::table_entity::properties_type& properties1 = customer1.properties();
    properties1.reserve(2);
    properties1[U("Email")] = azure::storage::entity_property(U("Jeff@contoso.com"));
    properties1[U("Phone")] = azure::storage::entity_property(U("425-555-0104"));

    // Create another customer entity and add it to the table.
    azure::storage::table_entity customer2(U("Smith"), U("Ben"));

    azure::storage::table_entity::properties_type& properties2 = customer2.properties();
    properties2.reserve(2);
    properties2[U("Email")] = azure::storage::entity_property(U("Ben@contoso.com"));
    properties2[U("Phone")] = azure::storage::entity_property(U("425-555-0102"));

    // Create a third customer entity to add to the table.
    azure::storage::table_entity customer3(U("Smith"), U("Denise"));

    azure::storage::table_entity::properties_type& properties3 = customer3.properties();
    properties3.reserve(2);
    properties3[U("Email")] = azure::storage::entity_property(U("Denise@contoso.com"));
    properties3[U("Phone")] = azure::storage::entity_property(U("425-555-0103"));

    // Add customer entities to the batch insert operation.
    batch_operation.insert_or_replace_entity(customer1);
    batch_operation.insert_or_replace_entity(customer2);
    batch_operation.insert_or_replace_entity(customer3);

    // Execute the batch operation.
    std::vector<azure::storage::table_result> results = table.execute_batch(batch_operation);

請注意，在批次作業的一些事項︰  

-   您可以執行最多 100 個插入、 刪除、 合併列印、 取代、 插入-或-合併]，然後插入或取代作業中單一批次的任何組合。  
-   批次作業可以有作業，可擷取，如果是批次中唯一的作業。  
-   在單一批次運算中的所有項目必須擁有相同的分割索引鍵。  
-   批次作業僅限於 4 MB 資料內容。  

## <a name="retrieve-all-entities-in-a-partition"></a>擷取分割區中的所有項目
若要查詢的所有項目在磁碟分割表格，請使用**table_query**物件。 下列範例會指定實體 'Smith' 所在的磁碟分割索引鍵的篩選。 此範例中列印每個實體主控台查詢結果中的欄位。  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    azure::storage::table_query query;

    query.set_filter_string(azure::storage::table_query::generate_filter_condition(U("PartitionKey"), azure::storage::query_comparison_operator::equal, U("Smith")));

    // Execute the query.
    azure::storage::table_query_iterator it = table.execute_query(query);

    // Print the fields for each customer.
    azure::storage::table_query_iterator end_of_results;
    for (; it != end_of_results; ++it)
    {
        const azure::storage::table_entity::properties_type& properties = it->properties();

        std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
            << U(", Property1: ") << properties.at(U("Email")).string_value()
            << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
    }  

此範例中的查詢顯示所有相符的篩選準則的項目。 如果您有大型的資料表，且需要下載表格項目頻率，我們建議您將資料儲存在儲存 Azure blob 改為。

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>擷取實體分割區中的範圍
如果您不想要查詢分割區中的所有項目，您可以藉由組合的磁碟分割索引鍵篩選資料列索引鍵篩選中指定範圍。 下列範例使用兩個篩選，以取得所有項目中的磁碟分割 'Smith 」 的列鍵 （名字） 早於 'E' 字母以字母開頭，然後列印 [查詢結果的位置。  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Create the table query.
    azure::storage::table_query query;

    query.set_filter_string(azure::storage::table_query::combine_filter_conditions(
        azure::storage::table_query::generate_filter_condition(U("PartitionKey"),
        azure::storage::query_comparison_operator::equal, U("Smith")),
        azure::storage::query_logical_operator::op_and,
        azure::storage::table_query::generate_filter_condition(U("RowKey"), azure::storage::query_comparison_operator::less_than, U("E"))));

    // Execute the query.
    azure::storage::table_query_iterator it = table.execute_query(query);

    // Loop through the results, displaying information about the entity.
    azure::storage::table_query_iterator end_of_results;
    for (; it != end_of_results; ++it)
    {
        const azure::storage::table_entity::properties_type& properties = it->properties();

        std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
            << U(", Property1: ") << properties.at(U("Email")).string_value()
            << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
    }  

## <a name="retrieve-a-single-entity"></a>擷取單一項目
您可以撰寫查詢擷取單一的特定的實體。 下列程式碼會使用**table_operation::retrieve_entity**指定客戶 ' Jeff Smith 」。 這個方法會傳回只要實體，而不是集合，並傳回的值是**table_result**。 指定查詢中的列和資料分割索引鍵是擷取資料表服務中的單一項目最快速的方法。  

    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
    azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
    azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

    // Output the entity.
    azure::storage::table_entity entity = retrieve_result.entity();
    const azure::storage::table_entity::properties_type& properties = entity.properties();

    std::wcout << U("PartitionKey: ") << entity.partition_key() << U(", RowKey: ") << entity.row_key()
        << U(", Property1: ") << properties.at(U("Email")).string_value()
        << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;

## <a name="replace-an-entity"></a>取代實體
若要取代的實體，擷取資料表服務，修改實體物件，然後再儲存所做的變更回表格服務。 下列程式碼會變更現有的客戶的電話號碼和電子郵件地址。 而非呼叫**table_operation::insert_entity**，此程式碼會使用**table_operation::replace_entity**。 這會使完全取代伺服器上的實體，除非在伺服器上的實體變更自擷取後，在此情況下操作會失敗。 此錯誤是為了防止您的應用程式不小心覆寫由另一個應用程式的元件之間的擷取及更新所做的變更。 適當處理此錯誤是一次擷取實體，進行變更 （如果仍然有效），然後執行其他**table_operation::replace_entity**作業。 [下一步] 區段會顯示如何覆寫此行為。  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Replace an entity.
    azure::storage::table_entity entity_to_replace(U("Smith"), U("Jeff"));
    azure::storage::table_entity::properties_type& properties_to_replace = entity_to_replace.properties();
    properties_to_replace.reserve(2);

    // Specify a new phone number.
    properties_to_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0106"));

    // Specify a new email address.
    properties_to_replace[U("Email")] = azure::storage::entity_property(U("JeffS@contoso.com"));

    // Create an operation to replace the entity.
    azure::storage::table_operation replace_operation = azure::storage::table_operation::replace_entity(entity_to_replace);

    // Submit the operation to the Table service.
    azure::storage::table_result replace_result = table.execute(replace_operation);

## <a name="insert-or-replace-an-entity"></a>插入或取代實體
如果已經變更實體，自從伺服器擷取後，將會失敗**table_operation::replace_entity**作業。 此外，您必須從**table_operation::replace_entity**成功順序中的第一個伺服器擷取的實體。 有時候，不過，您不知道是否實體存在於伺服器和儲存的目前值無關 — 您更新應該要覆寫所有。 若要這麼做，您可以使用**table_operation::insert_or_replace_entity**作業。 這項作業會插入實體不存在，或如果是，不論進行時，最新的更新會取代。 在下列程式碼範例中，仍會擷取 Jeff Smith 的客戶實體，但再儲存至伺服器透過**table_operation::insert_or_replace_entity**。 將會覆寫任何擷取和更新作業之間的實體所做的更新。  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Insert-or-replace an entity.
    azure::storage::table_entity entity_to_insert_or_replace(U("Smith"), U("Jeff"));
    azure::storage::table_entity::properties_type& properties_to_insert_or_replace = entity_to_insert_or_replace.properties();

    properties_to_insert_or_replace.reserve(2);

    // Specify a phone number.
    properties_to_insert_or_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0107"));

    // Specify an email address.
    properties_to_insert_or_replace[U("Email")] = azure::storage::entity_property(U("Jeffsm@contoso.com"));

    // Create an operation to insert-or-replace the entity.
    azure::storage::table_operation insert_or_replace_operation = azure::storage::table_operation::insert_or_replace_entity(entity_to_insert_or_replace);

    // Submit the operation to the Table service.
    azure::storage::table_result insert_or_replace_result = table.execute(insert_or_replace_operation);

## <a name="query-a-subset-of-entity-properties"></a>查詢實體屬性的子集  
查詢的資料表可以取得一些屬性的實體。 下列程式碼中的查詢會使用**table_query::set_select_columns**方法，在資料表中傳回只實體的電子郵件地址。  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Define the query, and select only the Email property.
    azure::storage::table_query query;
    std::vector<utility::string_t> columns;

    columns.push_back(U("Email"));
    query.set_select_columns(columns);

    // Execute the query.
    azure::storage::table_query_iterator it = table.execute_query(query);

    // Display the results.
    azure::storage::table_query_iterator end_of_results;
    for (; it != end_of_results; ++it)
    {
        std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key();

        const azure::storage::table_entity::properties_type& properties = it->properties();
        for (auto prop_it = properties.begin(); prop_it != properties.end(); ++prop_it)
        {
            std::wcout << ", " << prop_it->first << ": " << prop_it->second.str();
        }

        std::wcout << std::endl;
    }

>[AZURE.NOTE] 查詢從實體的幾個屬性是比擷取所有屬性更有效率的作業。

## <a name="delete-an-entity"></a>刪除實體
您擷取之後，您可以輕鬆地刪除實體。 實體會擷取之後，呼叫**table_operation::delete_entity**與要刪除的實體。 然後呼叫**cloud_table.execute**方法。 下列程式碼擷取及刪除實體磁碟分割的按鍵"Smith"與"Jeff 」 的列按鍵。  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
    azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
    azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

    // Create an operation to delete the entity.
    azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

    // Submit the delete operation to the Table service.
    azure::storage::table_result delete_result = table.execute(delete_operation);  

## <a name="delete-a-table"></a>刪除表格
最後，下列範例會刪除資料表儲存體帳戶。 一段時間之後刪除必須重新建立將無法使用資料表已被刪除。  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
    azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
    azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

    // Create an operation to delete the entity.
    azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

    // Submit the delete operation to the Table service.
    azure::storage::table_result delete_result = table.execute(delete_operation);

## <a name="next-steps"></a>後續步驟
現在，您學到的資料表儲存體基本概念，請遵循這些連結，深入瞭解 Azure 儲存體︰  

-   [如何使用 c + + Blob 儲存體](storage-c-plus-plus-how-to-use-blobs.md)
-   [如何使用 c + + 佇列中的儲存空間](storage-c-plus-plus-how-to-use-queues.md)
-   [清單 c + + Azure 儲存體資源](storage-c-plus-plus-enumeration.md)
-   [儲存空間 c + + 參考的用戶端文件庫](http://azure.github.io/azure-storage-cpp)
-   [Azure 儲存文件](https://azure.microsoft.com/documentation/services/storage/)
