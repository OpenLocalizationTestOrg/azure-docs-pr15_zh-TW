<properties
    pageTitle="開始使用資料表儲存體與 Visual Studio 連線服務 （雲端服務） |Microsoft Azure"
    description="如何開始連線到儲存帳戶使用 Visual Studio 連接服務之後，在 Visual Studio 雲端服務專案中使用 Azure 資料表儲存體"
    services="storage"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="web"
    ms.tgt_pltfrm="vs-getting-started"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/18/2016"
    ms.author="tarcher"/>

# <a name="getting-started-with-azure-table-storage-and-visual-studio-connected-services-cloud-services-projects"></a>開始使用 Azure 資料表儲存體與 Visual Studio 連線 （雲端服務的專案） 的服務

[AZURE.INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

##<a name="overview"></a>概觀

本文將說明如何開始使用 Visual Studio Azure 資料表儲存體之後您建立或參照 Azure 儲存體帳戶雲端服務專案中的使用 Visual Studio**新增已連線服務**] 對話方塊。 [**已連線服務**] 作業安裝適當的 NuGet 封裝來存取您專案中的 Azure 儲存空間，並新增您的專案設定檔儲存帳戶的連線字串。

Azure 資料表儲存服務可讓您儲存大量的結構化資料。 服務已接受經過驗證的來電內部和外部 Azure 雲端 NoSQL 資料存放區。 Azure 資料表非常適合儲存結構化、 非關聯式資料。

若要開始，您需要建立資料表儲存體帳戶中。 我們會告訴您如何建立 Azure 表格中的程式碼，以及如何執行基本表格和實體作業，例如新增、 修改、 讀取及讀取表格項目。 範例撰寫 C\#程式碼，並使用[Microsoft Azure 儲存體.NET 的用戶端文件庫](https://msdn.microsoft.com/library/azure/dn261237.aspx)。

**附註︰**執行出的來電至 Azure 儲存空間的 Api 部分是非同步。 如需詳細資訊，請參閱[非同步程式設計非同步與 Await](http://msdn.microsoft.com/library/hh191443.aspx) 。 下列程式碼假設正在使用非同步程式設計的方法。

- 如需有關以程式設計方式操作的資料表，請參閱[快速入門使用.NET Azure 資料表儲存體](storage-dotnet-how-to-use-tables.md)。
- 有關 Azure 儲存體的一般資訊，請參閱[儲存文件](https://azure.microsoft.com/documentation/services/storage/)。
- 如需 Azure 雲端服務的一般資訊，請參閱[雲端服務文件](https://azure.microsoft.com/documentation/services/cloud-services/)。
- 如需撰寫 ASP.NET 應用程式的相關資訊，請參閱[ASP.NET](http://www.asp.net) 。

## <a name="access-tables-in-code"></a>在 [程式碼中的 access 資料表

若要存取雲端服務專案中的資料表，您必須包含下列的項目，來存取 Azure 資料表儲存體任何 C# 來源檔案。

1. 請確定在 C# 檔案頂端的命名空間宣告包含這些**使用**陳述式。

        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Table;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;

2. 取得**CloudStorageAccount**物件，表示您儲存的帳戶資訊。 您可以使用下列程式碼，從 Azure 服務設定取得的儲存空間的連接字串及儲存的帳戶資訊。

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage account name>
         _AzureStorageConnectionString"));
> [AZURE.NOTE]  使用下列範例中的所有代碼前面以上的程式碼。

3. 取得**CloudTableClient**物件參照資料表中的物件您儲存的帳戶。

         // Create the table client.
         CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

4. 取得特定的表格和項目參照的**CloudTable**參考物件。

        // Get a reference to a table named "peopleTable".
        CloudTable peopleTable = tableClient.GetTableReference("peopleTable");

## <a name="create-a-table-in-code"></a>在 [程式碼中建立表格

若要建立 Azure 資料表，只要加入**CreateIfNotExistsAsync**到通話中的 「 存取程式碼中的資料表 」 一節所述，取得**CloudTable**物件之後。

    // Create the CloudTable if it does not exist.
    await peopleTable.CreateIfNotExistsAsync();

## <a name="add-an-entity-to-a-table"></a>實體新增至資料表

若要新增至資料表的實體，建立一個類別，定義您的實體的屬性。 下列程式碼定義稱為**CustomerEntity**使用分割索引鍵列鍵和 [名字姓氏與客戶的第一個名稱的實體類別。

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

關於實體的資料表操作完成使用**CloudTable**物件您先前建立的 「 存取資料表中的程式碼。 」 **TableOperation**物件表示完成作業。 下列範例會示範如何建立**CloudTable**物件及**CustomerEntity**物件。 若要準備作業，請建立**TableOperation**客戶實體插入表格。 最後，藉由呼叫**CloudTable.ExecuteAsync**執行作業。

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    await peopleTable.ExecuteAsync(insertOperation);


## <a name="insert-a-batch-of-entities"></a>插入實體以批的次

您可以插入多個項目至單一寫入操作的資料表。 下列範例會建立兩個實體物件 （"Jeff Smith"及"精靈 Smith"），將它們新增至**TableBatchOperation**物件使用插入的方法，然後呼叫**CloudTable.ExecuteBatchAsync**開始操作。

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
    await peopleTable.ExecuteBatchAsync(batchOperation);

## <a name="get-all-of-the-entities-in-a-partition"></a>分割中取得所有實體

若要查詢的所有磁碟分割的實體表格，請使用**TableQuery**物件。 下列範例會指定實體 'Smith' 所在的磁碟分割索引鍵的篩選。 此範例中列印每個實體主控台查詢結果中的欄位。

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    TableContinuationToken token = null;
    do
    {
        TableQuerySegment<CustomerEntity> resultSegment = await peopleTable.ExecuteQuerySegmentedAsync(query, token);
        token = resultSegment.ContinuationToken;

        foreach (CustomerEntity entity in resultSegment.Results)
        {
            Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
        }
    } while (token != null);

    return View();


## <a name="get-a-single-entity"></a>取得單一項目

您可以撰寫取得單一、 特定的實體查詢。 下列程式碼會使用指定為 「 在精靈 Smith 「 客戶**TableOperation**物件。 這個方法會傳回一個的實體，而不是集合，並在**TableResult.Result**所傳回的值是**CustomerEntity**物件。 指定查詢中的列和資料分割索引鍵是擷取**資料表**服務中的單一項目最快速的方法。

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the retrieve operation.
    TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

    // Print the phone number of the result.
    if (retrievedResult.Result != null)
       Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
    else
       Console.WriteLine("The phone number could not be retrieved.");

## <a name="delete-an-entity"></a>刪除實體
您找到之後，您可以刪除實體。 下列程式碼會尋找一個名為 「 在精靈 Smith"的客戶實體並如果找到它，它將其刪除。

    // Create a retrieve operation that expects a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = peopleTable.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

    // Create the Delete TableOperation and then execute it.
    if (deleteEntity != null)
    {
       TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

       // Execute the operation.
       await peopleTable.ExecuteAsync(deleteOperation);

       Console.WriteLine("Entity deleted.");
    }

    else
       Console.WriteLine("Couldn't delete the entity.");

## <a name="next-steps"></a>後續步驟

[AZURE.INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]
