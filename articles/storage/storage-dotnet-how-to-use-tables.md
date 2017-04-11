<properties
    pageTitle="開始使用 Azure 資料表儲存體使用.NET |Microsoft Azure"
    description="結構化的資料儲存在雲端使用 Azure 資料表儲存體，NoSQL 資料存放區。"
    services="storage"
    documentationCenter=".net"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="get-started-with-azure-table-storage-using-net"></a>使用.NET Azure 資料表儲存體快速入門

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>概觀

Azure 資料表儲存體] 是儲存在雲端中的結構化的 NoSQL 資料的服務。 資料表儲存體是 schemaless 設計的主要/屬性存放區。 因為資料表儲存體是 schemaless，您可以輕鬆為您的應用程式 evolve 的需求，調整您的資料。 存取資料是快速且所有類型的應用程式的效益。 資料表儲存體通常是明顯降低的成本大於傳統的 SQL 類似的大量資料內容。

您可以使用資料表儲存體儲存彈性的資料集，例如使用者資料的 web 應用程式、 通訊錄、 裝置的詳細資訊及其他任何類型的中繼資料，您的服務要求。 您可以儲存任何數目的項目在資料表中，並且儲存帳戶可能包含表格的儲存空間帳戶的容量限制的任何數字。

### <a name="about-this-tutorial"></a>關於本教學課程

本教學課程中會顯示如何撰寫.NET 程式碼的一些常見的案例使用 Azure 資料表儲存體，包括建立及刪除資料表及插入、 更新、 刪除和查詢表格資料。

**估計完成時間︰** 45 分鐘

**Prerequisities:**

- [Microsoft Visual Studio](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx)
- [Azure 儲存體.NET 的用戶端文件庫](https://www.nuget.org/packages/WindowsAzure.Storage/)
- [.Net azure 組態管理員](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
- [Azure 儲存體帳戶](storage-create-storage-account.md#create-a-storage-account)

[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

### <a name="more-samples"></a>更多範例

使用資料表儲存體的其他範例，請參閱[開始使用.net Azure 資料表儲存體](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/)。 您可以下載的範例應用程式並執行，或瀏覽 GitHub 的程式碼。


[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-development-environment-include](../../includes/storage-development-environment-include.md)]

### <a name="add-namespace-declarations"></a>新增命名空間宣告

新增下列`using`陳述式以頂端`program.cs`檔案︰

    using Microsoft.Azure; // Namespace for CloudConfigurationManager
    using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
    using Microsoft.WindowsAzure.Storage.Table; // Namespace for Table storage types

### <a name="parse-the-connection-string"></a>剖析的連接字串

[AZURE.INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-table-service-client"></a>建立表格服務用戶端

**CloudTableClient**類別可讓您擷取資料表和資料表儲存體中儲存的實體。 以下是建立服務用戶端的其中一個方法︰

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

現在您已經準備好要撰寫程式碼的讀取和寫入資料表儲存體中的資料。

## <a name="create-a-table"></a>建立表格

此範例會示範如何建立表格，如果不存在︰

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Retrieve a reference to the table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create the table if it doesn't exist.
    table.CreateIfNotExists();

## <a name="add-an-entity-to-a-table"></a>新增至表格的實體

項目對應到 C\#使用的自訂類別的物件衍生自**TableEntity**。 若要新增至資料表的實體，建立一個類別，定義您的實體的屬性。 下列程式碼定義的實體類別使用分割索引鍵列鍵和 [姓氏與客戶的第一個名稱。 在一起，實體的磁碟分割和列鍵可唯一識別資料表中的實體。 以相同的磁碟分割金鑰實體可更快比使用不同的磁碟分割的索引鍵，查詢，但使用各種不同的磁碟分割鍵可讓更佳的延展性平行作業。  儲存在表格服務任何屬性，屬性必須公用公開兩者都支援類型的屬性`get`和`set`。
此外，您的實體類型，*必須*公開無參數建構函式。

    public class CustomerEntity : TableEntity
    {
        public CustomerEntity(string lastName, string firstName)
        {
            this.PartitionKey = lastName;
            this.RowKey = firstName;
        }

        public CustomerEntity() { }

        public string Email { get; set; }

        public string PhoneNumber { get; set; }
    }

透過**CloudTable**物件您先前建立 「 建立表格] 區段中，會執行涉及實體資料表操作。 若要執行的作業會以**TableOperation**物件。  下列範例顯示**CloudTable**物件，然後**CustomerEntity**物件的建立。  若要準備作業，請**TableOperation**會建立一個物件至客戶實體插入表格。  最後，藉由呼叫**CloudTable.Execute**執行作業。

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation object that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    table.Execute(insertOperation);

## <a name="insert-a-batch-of-entities"></a>插入實體以批的次

您可以插入表格中一寫入作業的實體以批次。 批次作業一些其他事項︰

-  您可以執行更新、 刪除，並插入以相同的單一批次操作。
-  單一批次作業可以包含最多 100 個項目。
-  在單一批次運算中的所有項目必須擁有相同的分割索引鍵。
-  雖然可以執行查詢作為批次作業，必須是唯一的作業批次中。

<!-- -->
下列範例會建立兩個實體物件，並將**TableBatchOperation**至每個藉由**插入**的方法。 然後， **CloudTable.Execute**稱為執行作業。

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create the batch operation.
    TableBatchOperation batchOperation = new TableBatchOperation();

    // Create a customer entity and add it to the table.
    CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
    customer1.Email = "Jeff@contoso.com";
    customer1.PhoneNumber = "425-555-0104";

    // Create another customer entity and add it to the table.
    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    customer2.PhoneNumber = "425-555-0102";

    // Add both customer entities to the batch insert operation.
    batchOperation.Insert(customer1);
    batchOperation.Insert(customer2);

    // Execute the batch operation.
    table.ExecuteBatch(batchOperation);

## <a name="retrieve-all-entities-in-a-partition"></a>擷取分割區中的所有項目

若要查詢的所有項目在磁碟分割表格，請使用**TableQuery**物件。
下列範例會指定實體 'Smith' 所在的磁碟分割索引鍵的篩選。 此範例中列印每個實體主控台查詢結果中的欄位。

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    foreach (CustomerEntity entity in table.ExecuteQuery(query))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>擷取實體分割區中的範圍

如果您不想要查詢分割區中的所有項目，您可以藉由組合的磁碟分割索引鍵篩選資料列索引鍵篩選中指定範圍。 下列範例會使用兩個篩選，以取得所有項目中的磁碟分割 'Smith 」 的列鍵 （名字） 早於 'E' 字母以字母開頭，然後列印 [查詢結果的位置。

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create the table query.
    TableQuery<CustomerEntity> rangeQuery = new TableQuery<CustomerEntity>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "E")));

    // Loop through the results, displaying information about the entity.
    foreach (CustomerEntity entity in table.ExecuteQuery(rangeQuery))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

## <a name="retrieve-a-single-entity"></a>擷取單一項目

您可以撰寫查詢擷取單一的特定的實體。 下列程式碼會使用**TableOperation**指定客戶 」 精靈 Smith 」。
這個方法會傳回只要實體而不是集合，並在**TableResult.Result**所傳回的值是**CustomerEntity**物件。
指定查詢中的列和資料分割索引鍵是擷取資料表服務中的單一項目最快速的方法。

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the retrieve operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Print the phone number of the result.
    if (retrievedResult.Result != null)
       Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
    else
       Console.WriteLine("The phone number could not be retrieved.");

## <a name="replace-an-entity"></a>取代實體

若要更新的實體，擷取資料表服務，修改實體物件，然後再儲存所做的變更回表格服務。 下列程式碼會變更現有客戶的電話號碼。 而非呼叫**插入**，此程式碼會使用**取代**。 這會使完全取代伺服器上的實體，除非在伺服器上的實體變更自擷取後，在此情況下操作會失敗。  此錯誤是為了防止您的應用程式不小心覆寫由另一個應用程式的元件之間的擷取及更新所做的變更。  適當處理此錯誤是一次擷取實體，進行變更 （如果仍然有效），然後執行其他**取代**作業。  [下一步] 區段會顯示如何覆寫此行為。

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
    {
       // Change the phone number.
       updateEntity.PhoneNumber = "425-555-0105";

       // Create the Replace TableOperation.
       TableOperation updateOperation = TableOperation.Replace(updateEntity);

       // Execute the operation.
       table.Execute(updateOperation);

       Console.WriteLine("Entity updated.");
    }

    else
       Console.WriteLine("Entity could not be retrieved.");

## <a name="insert-or-replace-an-entity"></a>插入或取代實體

如果已經變更實體，自從伺服器擷取後，將會失敗**取代**作業。  此外，您必須從第一次，才能**取代**作業是成功伺服器擷取的實體。
有時候，不過，您不知道是否實體存在於伺服器和無關中儲存的目前值。 您的更新應該要覆寫所有。  若要這麼做，您可以使用**InsertOrReplace**作業。  這項作業會插入實體不存在，或如果是，不論進行時，最新的更新會取代。  在下列程式碼範例，仍會擷取精靈 Smith 的客戶實體，但再儲存至伺服器透過**InsertOrReplace**。  將會覆寫任何擷取和更新作業之間的實體所做的更新。

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
    {
       // Change the phone number.
       updateEntity.PhoneNumber = "425-555-1234";

       // Create the InsertOrReplace TableOperation.
       TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(updateEntity);

       // Execute the operation.
       table.Execute(insertOrReplaceOperation);

       Console.WriteLine("Entity was updated.");
    }

    else
       Console.WriteLine("Entity could not be retrieved.");

## <a name="query-a-subset-of-entity-properties"></a>查詢實體屬性的子集

資料表查詢可以取得一些屬性的實體，而不是所有的實體屬性。 這項技巧，稱為預測，減少頻寬，以及改善查詢效能，特別是大型的項目。 下列程式碼中的查詢會傳回資料表中僅實體的電子郵件地址。 這是使用**DynamicTableEntity**和也**EntityResolver**的查詢。 您可以進一步瞭解預測上[介紹了和查詢投影部落格文章][]。 請注意，投影不支援本機存放區模擬器，讓您使用的是帳戶表格服務時，才執行此的程式碼。

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Define the query, and select only the Email property.
    TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });

    // Define an entity resolver to work with the entity after retrieval.
    EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;

    foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
    {
        Console.WriteLine(projectedEmail);
    }

## <a name="delete-an-entity"></a>刪除實體

您已使用相同的模式顯示的更新實體擷取之後，您可以輕鬆地刪除實體。  下列程式碼會擷取，並刪除客戶實體。

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that expects a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity.
    CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

    // Create the Delete TableOperation.
    if (deleteEntity != null)
    {
       TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

       // Execute the operation.
       table.Execute(deleteOperation);

       Console.WriteLine("Entity deleted.");
    }

    else
       Console.WriteLine("Could not retrieve the entity.");

## <a name="delete-a-table"></a>刪除表格

最後，下列範例會刪除資料表儲存體帳戶。 一段時間之後刪除必須重新建立將無法使用的資料表已被刪除。

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Delete the table it if exists.
    table.DeleteIfExists();

## <a name="retrieve-entities-in-pages-asynchronously"></a>非同步擷取頁面中的項目

如果您閱讀大量的項目，而且您想要程序/顯示項目取出，而不是正在等候的所有傳回，您可以擷取實體使用分段的查詢。 此範例會示範如何在頁面中傳回的結果，使用非同步等模式，以便在您要傳回的結果大型組等待時執行未被封鎖。 如需.NET 中使用非同步 Await 模式的詳細資訊，請參閱[非同步程式設計非同步與 Await （C# 和 Visual Basic）](https://msdn.microsoft.com/library/hh191443.aspx)。

    // Initialize a default TableQuery to retrieve all the entities in the table.
    TableQuery<CustomerEntity> tableQuery = new TableQuery<CustomerEntity>();

    // Initialize the continuation token to null to start from the beginning of the table.
    TableContinuationToken continuationToken = null;

    do
    {
        // Retrieve a segment (up to 1,000 entities).
        TableQuerySegment<CustomerEntity> tableQueryResult =
            await table.ExecuteQuerySegmentedAsync(tableQuery, continuationToken);

        // Assign the new continuation token to tell the service where to
        // continue on the next iteration (or null if it has reached the end).
        continuationToken = tableQueryResult.ContinuationToken;

        // Print the number of rows retrieved.
        Console.WriteLine("Rows retrieved {0}", tableQueryResult.Results.Count);

    // Loop until a null continuation token is received, indicating the end of the table.
    } while(continuationToken != null);

## <a name="next-steps"></a>後續步驟

現在，您學到的資料表儲存體基本概念，請遵循這些連結，瞭解更複雜的儲存空間工作︰

- 請參閱更多的表格儲存空間範例，開始[使用.net Azure 資料表儲存體](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/)
- 檢視可用的 Api 的完整詳細的表格服務參考文件︰
    - [儲存.NET 參照的用戶端文件庫](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
    - [REST API 參照](http://msdn.microsoft.com/library/azure/dd179355)
- 瞭解如何以簡化您撰寫與 Azure 儲存體搭配使用[Azure WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk-get-started.md)的程式碼
- 檢視更多的功能指南，瞭解如何將資料儲存在 Azure 中的其他選項。
    - [使用.NET Azure Blob 儲存體快速入門](storage-dotnet-how-to-use-blobs.md)來儲存非結構化的資料。
    - [使用.NET (C#) SQL 資料庫連線](../sql-database/sql-database-develop-dotnet-simple.md)到儲存關聯式資料。

  [Download and install the Azure SDK for .NET]: /develop/net/
  [Creating an Azure Project in Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx

  [Blob5]: ./media/storage-dotnet-how-to-use-table-storage/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-table-storage/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-table-storage/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-table-storage/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-table-storage/blob9.png

  [介紹了和查詢投影部落格文章]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
  [.NET Client Library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Azure Storage Team blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configure Azure Storage connection strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
  [How to: Programmatically access Table storage]: #tablestorage
