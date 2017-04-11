<properties
    pageTitle="如何使用從 Java 資料表儲存體 |Microsoft Azure"
    description="結構化的資料儲存在雲端使用 Azure 資料表儲存體，NoSQL 資料存放區。"
    services="storage"
    documentationCenter="java"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="how-to-use-table-storage-from-java"></a>如何使用從 Java 資料表儲存體

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>概觀

本指南會顯示如何執行常見的案例使用 Azure 資料表儲存服務。 範例 Java 撰寫，並使用[Azure 儲存體 SDK java][]。 涵蓋的案例會包含在表格中**建立**、**清單**，以及**刪除**資料表，以及**插入**、**查詢**、**修改**和**刪除**項目。 如需有關表格的詳細資訊，請參閱[下一步](#Next-Steps)] 區段。

附註︰ SDK 適用於開發人員在 Android 裝置上使用 Azure 儲存體。 如需詳細資訊，請參閱[Azure 儲存體 SDK Android 版][]。

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>建立 Java 應用程式

本指南，您會使用本機，或在網頁角色或工作者角色 Azure 中的執行的程式碼中可以執行 Java 應用程式中儲存功能。

若要這麼做，您需要安裝 Java 開發套件 (JDK) 和 Azure 訂閱中建立 Azure 儲存體帳戶。 一旦您這麼做，則您必須以驗證您的開發系統符合的最低需求及上 GitHub [Azure 儲存體 SDK java][]存放庫中所列的相依性。 如果您的系統符合這些需求，您可以遵循下載並安裝 Java Azure 儲存文件庫，從該存放庫系統上的指示進行。 當您完成工作之後時，您可以建立 Java 應用程式會使用本文中的範例。

## <a name="configure-your-application-to-access-table-storage"></a>設定您的應用程式，來存取資料表儲存體

移至您要使用 windows Azure 儲存體 Api 來存取資料表 Java 檔案頂端新增下列匯入陳述式︰

    // Include the following imports to use table APIs
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.table.*;
    import com.microsoft.azure.storage.table.TableQuery.*;

## <a name="setup-an-azure-storage-connection-string"></a>設定 Azure 儲存連線字串

Azure 儲存用戶端使用儲存空間的連接字串儲存端點和的認證以存取資料管理服務。 在用戶端應用程式執行時，您必須提供儲存空間的連接字串，以下列格式，儲存帳戶列在 [ *AccountName* ] 和 [ *AccountKey*值[Azure 入口網站](https://portal.azure.com)中使用您儲存的帳戶和主要便捷鍵的名稱。 此範例會示範如何宣告靜態欄位，按住連線字串︰

    // Define the connection-string with your values.
    public static final String storageConnectionString =
        "DefaultEndpointsProtocol=http;" +
        "AccountName=your_storage_account;" +
        "AccountKey=your_storage_account_key";

應用程式中的角色 Microsoft Azure 中執行，這個字串可以儲存在服務設定檔中， *ServiceConfiguration.cscfg*，並可以使用**RoleEnvironment.getConfigurationSettings**方法呼叫來存取。 以下是範例稱為*StorageConnectionString*服務設定檔中的**設定**項目從取得連線字串︰

    // Retrieve storage account from connection-string.
    String storageConnectionString =
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

下列範例會假設您使用下列兩種方法之一來取得儲存連線字串。

## <a name="how-to-create-a-table"></a>如何︰ 建立表格

**CloudTableClient**物件可讓您取得資料表和項目參照的物件。 下列程式碼會建立**CloudTableClient**物件，然後用來建立新的**CloudTable**物件代表表格命名為 [人員]。 (附註︰ 還有其他方法來建立**CloudStorageAccount**物件; 如需詳細資訊，請參閱[Azure 儲存用戶端 SDK 參照]中的**CloudStorageAccount** 。)

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

       // Create the table client.
       CloudTableClient tableClient = storageAccount.createCloudTableClient();

       // Create the table if it doesn't exist.
       String tableName = "people";
       CloudTable cloudTable = tableClient.getTableReference(tableName);
       cloudTable.createIfNotExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-list-the-tables"></a>如何︰ 列出資料表

若要取得資料表的清單，呼叫**CloudTableClient.listTables()**方法來擷取遞迴清單中的資料表名稱。

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Loop through the collection of table names.
        for (String table : tableClient.listTables())
        {
          // Output each table name.
          System.out.println(table);
       }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-add-an-entity-to-a-table"></a>如何︰ 實體新增至資料表

項目對應到 Java 使用實作**TableEntity**的自訂類別的物件。 為了方便， **TableServiceEntity**類別實作**TableEntity** ，且使用反射將屬性對應至 getter 和設定器命名的摘要資訊的方法。 若要新增至資料表的實體，先建立一個類別，定義您的實體的屬性。 下列程式碼定義的實體類別的使用分割索引鍵為列，以及姓氏與客戶的第一個名稱。 在一起，實體的磁碟分割和列鍵可唯一識別資料表中的實體。 以相同的磁碟分割金鑰實體可以查詢的速度比使用不同的磁碟分割索引鍵。

    public class CustomerEntity extends TableServiceEntity {
        public CustomerEntity(String lastName, String firstName) {
            this.partitionKey = lastName;
            this.rowKey = firstName;
        }

        public CustomerEntity() { }

        String email;
        String phoneNumber;

        public String getEmail() {
            return this.email;
        }

        public void setEmail(String email) {
            this.email = email;
        }

        public String getPhoneNumber() {
            return this.phoneNumber;
        }

        public void setPhoneNumber(String phoneNumber) {
            this.phoneNumber = phoneNumber;
        }
    }

關於實體的資料表操作需要**TableOperation**物件。 這個物件定義在實體，可以執行與**CloudTable**物件上執行的作業。 下列程式碼會建立新類別的執行個體**CustomerEntity**含有儲存一些客戶資料。 程式碼接下來通話**TableOperation.insertOrReplace**建立實體插入表格、 **TableOperation**物件，然後新**CustomerEntity**關聯。 最後，程式碼會上**CloudTable**物件，指定 [人員] 資料表和新**TableOperation**，然後儲存資料表中插入新的客戶實體 [人員]，或取代實體，如果已經存在服務會傳送要求的通話的**執行**方式。

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Create a new customer entity.
        CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
        customer1.setEmail("Walter@contoso.com");
        customer1.setPhoneNumber("425-555-0101");

        // Create an operation to add the new customer to the people table.
        TableOperation insertCustomer1 = TableOperation.insertOrReplace(customer1);

        // Submit the operation to the table service.
        cloudTable.execute(insertCustomer1);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-insert-a-batch-of-entities"></a>如何︰ 插入的實體以批次

您可以在一個寫入操作中插入表格服務的實體批次。 下列程式碼建立**TableBatchOperation**物件，然後新增三個插入至該作業。 新增每個插入作業建立新的實體物件，設定其值，然後呼叫 [建立新的插入作業的關聯實體**TableBatchOperation**物件上的 [**插入**的方法。 然後程式碼**執行**上通話**CloudTable**指定的物件，[人員] 表格與資料表作業批次傳送至單一要求的儲存空間服務**TableBatchOperation**物件。

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Define a batch operation.
        TableBatchOperation batchOperation = new TableBatchOperation();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Create a customer entity to add to the table.
        CustomerEntity customer = new CustomerEntity("Smith", "Jeff");
        customer.setEmail("Jeff@contoso.com");
        customer.setPhoneNumber("425-555-0104");
        batchOperation.insertOrReplace(customer);

       // Create another customer entity to add to the table.
       CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
       customer2.setEmail("Ben@contoso.com");
       customer2.setPhoneNumber("425-555-0102");
       batchOperation.insertOrReplace(customer2);

       // Create a third customer entity to add to the table.
       CustomerEntity customer3 = new CustomerEntity("Smith", "Denise");
       customer3.setEmail("Denise@contoso.com");
       customer3.setPhoneNumber("425-555-0103");
       batchOperation.insertOrReplace(customer3);

       // Execute the batch of operations on the "people" table.
       cloudTable.execute(batchOperation);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

請注意，在批次作業的一些事項︰

- 您可以執行最多 100 個插入、 刪除、 合併、 取代、 插入或合併列印]，並插入或取代作業中單一批次的任何組合。
- 批次作業可以有作業，可擷取，如果是批次中唯一的作業。
- 在單一批次運算中的所有項目必須擁有相同的分割索引鍵。
- 批次作業僅限於 4 MB 資料裝載。

## <a name="how-to-retrieve-all-entities-in-a-partition"></a>如何︰ 擷取分割區中的所有項目

若要查詢的實體在磁碟分割表格，您可以使用**TableQuery**。 [撥號給**TableQuery.from**會傳回指定的結果類型的特定資料表上建立的查詢。 下列程式碼指定實體 'Smith' 所在的磁碟分割索引鍵的篩選。 **TableQuery.generateFilterCondition**是協助方法建立查詢的篩選。 呼叫**TableQuery.from**方法若要將篩選套用至查詢所傳回參照的**位置**。 執行查詢時使用**CloudTable**物件上的 [**執行**的通話，則會傳回**Iterator**指定**CustomerEntity**結果類型。 然後，您可以使用**Iterator**中傳回的每個迴圈，若要使用的結果。 將此程式碼會列印每個實體主控台查詢結果中的欄位。

    try
    {
        // Define constants for filters.
        final String PARTITION_KEY = "PartitionKey";
        final String ROW_KEY = "RowKey";
        final String TIMESTAMP = "Timestamp";

        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

       // Create a cloud table object for the table.
       CloudTable cloudTable = tableClient.getTableReference("people");

        // Create a filter condition where the partition key is "Smith".
        String partitionFilter = TableQuery.generateFilterCondition(
           PARTITION_KEY,
           QueryComparisons.EQUAL,
           "Smith");

       // Specify a partition query, using "Smith" as the partition key filter.
       TableQuery<CustomerEntity> partitionQuery =
           TableQuery.from(CustomerEntity.class)
           .where(partitionFilter);

        // Loop through the results, displaying information about the entity.
        for (CustomerEntity entity : cloudTable.execute(partitionQuery)) {
            System.out.println(entity.getPartitionKey() +
                " " + entity.getRowKey() +
                "\t" + entity.getEmail() +
                "\t" + entity.getPhoneNumber());
       }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-retrieve-a-range-of-entities-in-a-partition"></a>如何︰ 擷取實體分割區中的範圍

如果您不想要查詢分割區中的所有項目，您可以在篩選中使用的比較運算子來指定範圍。 下列程式碼會結合兩個篩選，以取得所有磁碟分割的實體"Smith"的列鍵 （名字） 位置截至 'E' 以字母開頭字母。 然後列印查詢結果。 如果您使用加入批次中資料表的實體插入本指南的小節，這次 （在精靈和韓旻 Smith）; 傳回只有兩個實體蕭 Smith 不包含在內。

    try
    {
        // Define constants for filters.
        final String PARTITION_KEY = "PartitionKey";
        final String ROW_KEY = "RowKey";
        final String TIMESTAMP = "Timestamp";

        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

       // Create the table client.
       CloudTableClient tableClient = storageAccount.createCloudTableClient();

       // Create a cloud table object for the table.
       CloudTable cloudTable = tableClient.getTableReference("people");

        // Create a filter condition where the partition key is "Smith".
        String partitionFilter = TableQuery.generateFilterCondition(
           PARTITION_KEY,
           QueryComparisons.EQUAL,
           "Smith");

        // Create a filter condition where the row key is less than the letter "E".
        String rowFilter = TableQuery.generateFilterCondition(
           ROW_KEY,
           QueryComparisons.LESS_THAN,
           "E");

        // Combine the two conditions into a filter expression.
        String combinedFilter = TableQuery.combineFilters(partitionFilter,
            Operators.AND, rowFilter);

        // Specify a range query, using "Smith" as the partition key,
        // with the row key being up to the letter "E".
        TableQuery<CustomerEntity> rangeQuery =
           TableQuery.from(CustomerEntity.class)
           .where(combinedFilter);

        // Loop through the results, displaying information about the entity
        for (CustomerEntity entity : cloudTable.execute(rangeQuery)) {
            System.out.println(entity.getPartitionKey() +
                " " + entity.getRowKey() +
                "\t" + entity.getEmail() +
                "\t" + entity.getPhoneNumber());
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-retrieve-a-single-entity"></a>如何︰ 擷取單一項目

您可以撰寫查詢擷取單一的特定的實體。 下列程式碼進行通話**TableOperation.retrieve**分割索引鍵和列參數指定客戶"Jeff Smith"，而非建立**TableQuery**和使用篩選來執行相同的動作。 執行時，[擷取] 作業會傳回一個實體，而不是集合。 **GetResultAsType**方法轉換資料類型的工作分派目標， **CustomerEntity**物件的結果。 如果此類型不相容於指定查詢的類型，就會擲回例外。 如果沒有實體有確切的磁碟分割及相符的資料列按鍵，就會傳回 null 值。 指定查詢中的列和資料分割索引鍵是擷取資料表服務中的單一項目最快速的方法。

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Retrieve the entity with partition key of "Smith" and row key of "Jeff"
        TableOperation retrieveSmithJeff =
           TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

       // Submit the operation to the table service and get the specific entity.
       CustomerEntity specificEntity =
            cloudTable.execute(retrieveSmithJeff).getResultAsType();

        // Output the entity.
        if (specificEntity != null)
        {
            System.out.println(specificEntity.getPartitionKey() +
                " " + specificEntity.getRowKey() +
                "\t" + specificEntity.getEmail() +
                "\t" + specificEntity.getPhoneNumber());
       }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-modify-an-entity"></a>如何︰ 修改實體

若要修改的實體，擷取資料表服務、 變更實體物件，然後將變更儲存回表格服務中，[取代] 或 [合併作業。 下列程式碼會變更現有客戶的電話號碼。 而非跟我們插入所做的一樣，請撥打**TableOperation.insert** ，將此程式碼的通話**TableOperation.replace**。 **CloudTable.execute**方法呼叫表格服務，而實體會取代，除非另一個應用程式之後，變更其時間此應用程式中擷取它。 發生時，發生例外狀況，並實體必須會擷取、 修改，一次儲存。 此樂觀重試模式是常見分散式的儲存系統中。

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
        TableOperation retrieveSmithJeff =
           TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

        // Submit the operation to the table service and get the specific entity.
        CustomerEntity specificEntity =
          cloudTable.execute(retrieveSmithJeff).getResultAsType();

        // Specify a new phone number.
        specificEntity.setPhoneNumber("425-555-0105");

        // Create an operation to replace the entity.
        TableOperation replaceEntity = TableOperation.replace(specificEntity);

        // Submit the operation to the table service.
        cloudTable.execute(replaceEntity);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-query-a-subset-of-entity-properties"></a>如何︰ 查詢實體屬性的子集

查詢的資料表可以取得一些屬性的實體。 這項技巧，稱為預測，減少頻寬，以及改善查詢效能，特別是大型的項目。 下列程式碼中的查詢使用**選取**方法來傳回只電子郵件地址的實體資料表中。 結果會投射**EntityResolver**，就在從伺服器所傳回的實體類型轉換的說明集合的**字串**。 您可以進一步瞭解在[Azure 資料表︰ 介紹了和查詢投影][]。 請注意，投影不支援在本機的儲存空間模擬器，讓表格服務使用的帳戶時，才執行此的程式碼。

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Define a projection query that retrieves only the Email property
        TableQuery<CustomerEntity> projectionQuery =
           TableQuery.from(CustomerEntity.class)
           .select(new String[] {"Email"});

        // Define a Entity resolver to project the entity to the Email value.
        EntityResolver<String> emailResolver = new EntityResolver<String>() {
            @Override
            public String resolve(String PartitionKey, String RowKey, Date timeStamp, HashMap<String, EntityProperty> properties, String etag) {
                return properties.get("Email").getValueAsString();
            }
        };

        // Loop through the results, displaying the Email values.
        for (String projectedString :
            cloudTable.execute(projectionQuery, emailResolver)) {
                System.out.println(projectedString);
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-insert-or-replace-an-entity"></a>如何︰ 插入或取代實體

通常您會想要新增至表格的實體，而不知道已經存在資料表中。 插入或取代作業，可讓您進行會插入實體，如果不存在於或如果是這樣，取代現有的單一要求。 建立在先前範例，下列程式碼插入或取代為 「 烏 Harp 」 的實體。 建立新的實體之後, 將此程式碼會呼叫**TableOperation.insertOrReplace**的方法。 將此程式碼然後呼叫 [插入表格與**CloudTable**物件上的 [**執行**，或取代做為參數的表格作業。 若要更新的實體的組件，可以改為使用**TableOperation.insertOrMerge**方法。 請注意，此程式碼執行表格服務使用的帳戶時，只讓，本機存放區模擬器上不支援的插入-或-取代。 您可以瞭解更多關於插入或取代和插入或合併此[Azure 資料表︰ 介紹了和查詢投影][]。

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Create a new customer entity.
        CustomerEntity customer5 = new CustomerEntity("Harp", "Walter");
        customer5.setEmail("Walter@contoso.com");
        customer5.setPhoneNumber("425-555-0106");

        // Create an operation to add the new customer to the people table.
        TableOperation insertCustomer5 = TableOperation.insertOrReplace(customer5);

        // Submit the operation to the table service.
        cloudTable.execute(insertCustomer5);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-delete-an-entity"></a>如何︰ 刪除實體

您擷取之後，您可以輕鬆地刪除實體。 一旦擷取實體，呼叫**TableOperation.delete**刪除實體。 然後呼叫**執行** **CloudTable**物件上。 下列程式碼會擷取，並刪除客戶實體。

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
        TableOperation retrieveSmithJeff = TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

        // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
        CustomerEntity entitySmithJeff =
            cloudTable.execute(retrieveSmithJeff).getResultAsType();

        // Create an operation to delete the entity.
        TableOperation deleteSmithJeff = TableOperation.delete(entitySmithJeff);

        // Submit the delete operation to the table service.
        cloudTable.execute(deleteSmithJeff);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-delete-a-table"></a>如何︰ 刪除表格

最後，下列程式碼會刪除資料表儲存體帳戶。 已遭刪除的資料表將無法重新建立一段時間之後刪除，通常小於主版頁面的秒數。

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Delete the table and all its data if it exists.
        CloudTable cloudTable = new CloudTable("people",tableClient);
        cloudTable.deleteIfExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="next-steps"></a>後續步驟

現在，您學到的資料表儲存體基本概念，請遵循這些連結，瞭解如何執行更複雜的儲存空間工作。

- [Azure 儲存體 java SDK][]
- [Azure 儲存用戶端 SDK 參考][]
- [Azure 儲存體 REST API][]
- [Azure 儲存體小組部落格][]

如需詳細資訊，請參閱[Java 開發人員中心](/develop/java/)。


[Azure SDK for Java]: http://go.microsoft.com/fwlink/?LinkID=525671
[Azure 儲存體 java SDK]: https://github.com/azure/azure-storage-java
[Android 版 SDK azure 儲存空間]: https://github.com/azure/azure-storage-android
[Azure 儲存用戶端 SDK 參考]: http://dl.windowsazure.com/storage/javadoc/
[Azure 儲存體 REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure 儲存體小組部落格]: http://blogs.msdn.com/b/windowsazurestorage/
[Azure 資料表︰ 介紹了及查詢的預測]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
