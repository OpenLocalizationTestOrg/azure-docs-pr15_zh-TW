<properties
    pageTitle="如何使用從 PHP 資料表儲存體 |Microsoft Azure"
    description="瞭解如何使用 PHP 從表格服務來建立及刪除表格]，然後插入、 刪除及查詢的資料表。"
    services="storage"
    documentationCenter="php"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="php"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="how-to-use-table-storage-from-php"></a>如何使用從 PHP 資料表儲存體

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>概觀

本指南為您示範如何執行常見的案例使用 Azure 資料表服務。 撰寫 PHP 的範例，並使用[PHP Azure SDK][download]。 涵蓋的案例包含**建立及刪除資料表，並插入、 刪除和查詢表格中的項目**。 如需有關 Azure 資料表服務的詳細資訊，請參閱[下一步](#next-steps)] 區段。

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>建立 PHP 應用程式

建立 PHP 應用程式存取的 Azure 資料表服務的唯一的需求是參照中的 Azure SDK 類別從 PHP 的程式碼中。 您可以使用任何開發工具來建立您的應用程式，包括記事本。

本指南中，您可以使用表格服務功能可以從稱為內 PHP 應用程式，或執行 Azure 網頁角色、 工作者角色或網站內的程式碼。

## <a name="get-the-azure-client-libraries"></a>取得 Azure 用戶端文件庫

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-access-the-table-service"></a>設定存取表格服務應用程式

若要使用的 Azure 資料表服務 Api，您需要︰

1. 參照[require_once]自動載入器檔案[require_once]陳述式，和
2. 參照您可以使用任何類別。

下列範例會示範如何包含自動載入器檔案，並參考**ServicesBuilder**類別。

> [AZURE.NOTE] 此範例 （和本文中的其他範例） 假設您已安裝編輯器透過 Azure PHP 用戶端文件庫。 如果您手動安裝文件庫，您需要參考<code>WindowsAzure.php</code>自動載入器檔案。

    require_once 'vendor/autoload.php';
    use WindowsAzure\Common\ServicesBuilder;


在下面的範例，`require_once`陳述式一定會顯示，但所參照只執行範例所需的類別。

## <a name="set-up-an-azure-storage-connection"></a>設定連線至 Azure 儲存體

若要產生 Azure 資料表服務用戶端，您必須先有效的連接字串。 為表格服務連線字串的格式︰

存取即時服務︰

    DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

用來存取模擬器儲存空間︰

    UseDevelopmentStorage=true


若要建立任何 Azure 服務用戶端，您需要使用**ServicesBuilder**類別。 您可以︰

* 傳遞的連接字串，以便直接或
* 使用**CloudConfigurationManager (CCM)**核取多個的外部來源的連線字串︰
    * 根據預設，其會出現一個外部來源-環境變數的支援
    * 您可以新增新的來源擴充**ConnectionStringSource**類別

此處所述的範例，如會直接傳遞的連接字串。

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;

    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);


## <a name="create-a-table"></a>建立表格

**TableRestProxy**物件可讓您建立具有**createTable**方法的表格。 在建立表格時，您可以將表格服務逾時。 (更多關於表格服務逾時的詳細資訊，請參閱[設定逾時，表格服務作業][table-service-timeouts]。)

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        // Create table.
        $tableRestProxy->createTable("mytable");
    }
    catch(ServiceException $e){
        $code = $e->getCode();
        $error_message = $e->getMessage();
        // Handle exception based on error codes and messages.
        // Error codes and messages can be found here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
    }

有關限制資料表名稱，請參閱[瞭解表格服務的資料模型][table-data-model]。

## <a name="add-an-entity-to-a-table"></a>實體新增至資料表

若要新增至資料表的實體，建立新的**實體**物件並傳遞至**TableRestProxy]-> [insertEntity**。 請注意，當您建立實體，您必須指定`PartitionKey`和`RowKey`。 這些是實體的唯一識別碼，並可查詢速度比使用其他實體屬性的值。 系統會使用`PartitionKey`自動許多儲存空間節點上發佈表格的項目。 使用相同的實體`PartitionKey`會儲存在相同的節點。 (作業儲存在相同的節點的多個項目上執行更多儲存到不同的節點的實體。)`RowKey`是磁碟分割內的實體的唯一識別碼。

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;
    use MicrosoftAzure\Storage\Table\Models\Entity;
    use MicrosoftAzure\Storage\Table\Models\EdmType;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $entity = new Entity();
    $entity->setPartitionKey("tasksSeattle");
    $entity->setRowKey("1");
    $entity->addProperty("Description", null, "Take out the trash.");
    $entity->addProperty("DueDate",
                         EdmType::DATETIME,
                         new DateTime("2012-11-05T08:15:00-08:00"));
    $entity->addProperty("Location", EdmType::STRING, "Home");

    try{
        $tableRestProxy->insertEntity("mytable", $entity);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
    }

資料表屬性和類型的相關資訊，請參閱[瞭解表格服務的資料模型][table-data-model]。

**TableRestProxy**課程提供兩種插入實體的替代方法︰ **insertOrMergeEntity**和**insertOrReplaceEntity**。 若要使用下列方法，建立新的**實體**並將它做為參數傳遞至任何一種方法。 如果不存在，每一種方法會插入實體。 如果已經存在的實體， **insertOrMergeEntity**如果屬性存在於更新屬性值，並新增新屬性，如果不存在，而**insertOrReplaceEntity**完全會取代現有的實體。 下列範例會示範如何使用**insertOrMergeEntity**。 如果使用實體`PartitionKey`「 tasksSeattle 」 及`RowKey`「 1 」 不存在，就會插入。 不過，如果已經先前已插入 （如上述範例所示），`DueDate`屬性將會更新，以及`Status`屬性加入。 `Description`和`Location`也會更新內容，但含有值的有效地使其保持不變。 如果不新增如下所示的範例，但目標實體存在於這些後面的兩個屬性，其現有的值會維持不變。

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;
    use MicrosoftAzure\Storage\Table\Models\Entity;
    use MicrosoftAzure\Storage\Table\Models\EdmType;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    //Create new entity.
    $entity = new Entity();

    // PartitionKey and RowKey are required.
    $entity->setPartitionKey("tasksSeattle");
    $entity->setRowKey("1");

    // If entity exists, existing properties are updated with new values and
    // new properties are added. Missing properties are unchanged.
    $entity->addProperty("Description", null, "Take out the trash.");
    $entity->addProperty("DueDate", EdmType::DATETIME, new DateTime()); // Modified the DueDate field.
    $entity->addProperty("Location", EdmType::STRING, "Home");
    $entity->addProperty("Status", EdmType::STRING, "Complete"); // Added Status field.

    try {
        // Calling insertOrReplaceEntity, instead of insertOrMergeEntity as shown,
        // would simply replace the entity with PartitionKey "tasksSeattle" and RowKey "1".
        $tableRestProxy->insertOrMergeEntity("mytable", $entity);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }


## <a name="retrieve-a-single-entity"></a>擷取單一項目

**TableRestProxy]-> [getEntity**方法可讓您的查詢來擷取單一項目其`PartitionKey`和`RowKey`。 在下方的磁碟分割鍵範例`tasksSeattle`及列按鍵`1`傳給**getEntity**方法。

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        $result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $entity = $result->getEntity();

    echo $entity->getPartitionKey().":".$entity->getRowKey();

## <a name="retrieve-all-entities-in-a-partition"></a>擷取分割區中的所有項目

使用篩選來建構實體查詢 (如需詳細資訊，請參閱[查詢的資料表和實體][filters])。 若要擷取資料分割中的所有項目，請使用篩選 」 PartitionKey eq *partition_name*」。 下列範例會示範如何擷取中的所有項目`tasksSeattle`磁碟分割傳遞到**queryEntities**方法的篩選。

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $filter = "PartitionKey eq 'tasksSeattle'";

    try {
        $result = $tableRestProxy->queryEntities("mytable", $filter);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $entities = $result->getEntities();

    foreach($entities as $entity){
        echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
    }

## <a name="retrieve-a-subset-of-entities-in-a-partition"></a>擷取子集分割區中的項目

在先前範例所用的同一個圖樣可用來擷取實體分割區中的任何子集合。 您所擷取的實體子集取決於您使用的篩選器 (如需詳細資訊，請參閱[查詢的資料表和實體][filters])。下列範例會示範如何使用篩選以擷取特定的所有項目`Location`和`DueDate`小於指定的日期。

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $filter = "Location eq 'Office' and DueDate lt '2012-11-5'";

    try {
        $result = $tableRestProxy->queryEntities("mytable", $filter);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $entities = $result->getEntities();

    foreach($entities as $entity){
        echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
    }

## <a name="retrieve-a-subset-of-entity-properties"></a>擷取實體屬性的子集

查詢可擷取實體屬性的子集。 這項技巧，稱為*預測*，減少頻寬，以及改善查詢效能，特別是大型的項目。 若要指定要擷取的屬性，請**查詢]-> [addSelectField**方法傳遞屬性的名稱。 您可以新增多個屬性呼叫此方法多次。 在執行後**TableRestProxy]-> [queryEntities**，所傳回的實體只會有選取的屬性。 （如果您想要傳回資料表的實體子集，使用篩選上述查詢中所示）。

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;
    use MicrosoftAzure\Storage\Table\Models\QueryEntitiesOptions;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $options = new QueryEntitiesOptions();
    $options->addSelectField("Description");

    try {
        $result = $tableRestProxy->queryEntities("mytable", $options);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    // All entities in the table are returned, regardless of whether
    // they have the Description field.
    // To limit the results returned, use a filter.
    $entities = $result->getEntities();

    foreach($entities as $entity){
        $description = $entity->getProperty("Description")->getValue();
        echo $description."<br />";
    }

## <a name="update-an-entity"></a>更新實體

使用**實體]-> [setproperty] 巨集**和**實體]-> [addProperty**方法，在實體上，然後呼叫 [ **TableRestProxy]-> [updateEntity**可以更新現有的實體。 下列範例會擷取實體、 修改一個屬性，移除其他屬性，並將新屬性的資料。 請注意，您可以移除屬性的值設定為**null**。

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;
    use MicrosoftAzure\Storage\Table\Models\Entity;
    use MicrosoftAzure\Storage\Table\Models\EdmType;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);

    $entity = $result->getEntity();

    $entity->setPropertyValue("DueDate", new DateTime()); //Modified DueDate.

    $entity->setPropertyValue("Location", null); //Removed Location.

    $entity->addProperty("Status", EdmType::STRING, "In progress"); //Added Status.

    try {
        $tableRestProxy->updateEntity("mytable", $entity);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <a name="delete-an-entity"></a>刪除實體

若要刪除的實體，傳遞資料表名稱，然後的實體`PartitionKey`和`RowKey` **TableRestProxy]-> [deleteEntity**方法。

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        // Delete entity.
        $tableRestProxy->deleteEntity("mytable", "tasksSeattle", "2");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

請注意，並行檢查，您可以設定使用**DeleteEntityOptions]-> [setEtag**方法，將**DeleteEntityOptions**物件傳遞至**deleteEntity**為第四個要刪除的實體 Etag 參數。

## <a name="batch-table-operations"></a>批次表格作業

**TableRestProxy]-> [批次**的方法可讓您在單一要求執行多項作業。 以下的圖樣涉及新增作業至**BatchRequest**物件和**TableRestProxy]-> [批次**方法傳遞**BatchRequest**物件。 若要新增作業至**BatchRequest**物件，您可以下列方法之一多次呼叫︰

* **addInsertEntity**（將 insertEntity 操作）
* **addUpdateEntity**（將 updateEntity 操作）
* **addMergeEntity**（將 mergeEntity 操作）
* **addInsertOrReplaceEntity**（將 insertOrReplaceEntity 操作）
* **addInsertOrMergeEntity**（將 insertOrMergeEntity 操作）
* **addDeleteEntity**（將 deleteEntity 操作）

下列範例會示範如何在單一要求執行**insertEntity**和**deleteEntity**作業︰

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;
    use MicrosoftAzure\Storage\Table\Models\Entity;
    use MicrosoftAzure\Storage\Table\Models\EdmType;
    use MicrosoftAzure\Storage\Table\Models\BatchOperations;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    // Create list of batch operation.
    $operations = new BatchOperations();

    $entity1 = new Entity();
    $entity1->setPartitionKey("tasksSeattle");
    $entity1->setRowKey("2");
    $entity1->addProperty("Description", null, "Clean roof gutters.");
    $entity1->addProperty("DueDate",
                          EdmType::DATETIME,
                          new DateTime("2012-11-05T08:15:00-08:00"));
    $entity1->addProperty("Location", EdmType::STRING, "Home");

    // Add operation to list of batch operations.
    $operations->addInsertEntity("mytable", $entity1);

    // Add operation to list of batch operations.
    $operations->addDeleteEntity("mytable", "tasksSeattle", "1");

    try {
        $tableRestProxy->batch($operations);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

如需有關批次處理表格作業的詳細資訊，請參閱[執行實體群組交易][entity-group-transactions]。

## <a name="delete-a-table"></a>刪除表格

最後，若要刪除表格，請**TableRestProxy]-> [deleteTable**方法傳遞的資料表名稱。

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        // Delete table.
        $tableRestProxy->deleteTable("mytable");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <a name="next-steps"></a>後續步驟

現在，您學到的 Azure 資料表服務的基本概念，請遵循這些連結，瞭解更複雜的儲存空間工作。

- 請造訪[Azure 儲存體小組部落格](http://blogs.msdn.com/b/windowsazurestorage/)

如需詳細資訊，請參閱[PHP 開發人員中心](/develop/php/)。

[download]: http://go.microsoft.com/fwlink/?LinkID=252473
[require_once]: http://php.net/require_once
[table-service-timeouts]: http://msdn.microsoft.com/library/azure/dd894042.aspx

[table-data-model]: http://msdn.microsoft.com/library/azure/dd179338.aspx
[filters]: http://msdn.microsoft.com/library/azure/dd894031.aspx
[entity-group-transactions]: http://msdn.microsoft.com/library/azure/dd894038.aspx
