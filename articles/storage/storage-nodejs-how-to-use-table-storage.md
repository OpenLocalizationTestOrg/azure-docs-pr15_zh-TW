<properties
    pageTitle="如何使用從 Node.js 的 Azure 資料表儲存體 |Microsoft Azure"
    description="結構化的資料儲存在雲端使用 Azure 資料表儲存體，NoSQL 資料存放區。"
    services="storage"
    documentationCenter="nodejs"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="tamram"/>


# <a name="how-to-use-azure-table-storage-from-nodejs"></a>如何使用從 Node.js 的 Azure 資料表儲存體

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>概觀

本主題說明如何執行常見的案例 Node.js 應用程式中使用 Azure 資料表服務。

本主題中的程式碼範例假設您已經有 Node.js 應用程式。 如需如何 Azure 中建立 Node.js 應用程式，請參閱下列主題的資訊︰

- [Azure 應用程式服務中建立 Node.js web 應用程式](../app-service-web/web-sites-nodejs-develop-deploy-mac.md)
- [建立並部署 Node.js web 應用程式來使用 WebMatrix Azure](../app-service-web/web-sites-nodejs-use-webmatrix.md)
- [建立並部署至 Azure 雲端服務的 Node.js 應用程式](../cloud-services/cloud-services-nodejs-develop-deploy-app.md)（使用 Windows PowerShell）


[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]


## <a name="configure-your-application-to-access-azure-storage"></a>設定您的應用程式，來存取 Azure 儲存體

若要使用 Azure 儲存空間，您需要 Node.js，其中包含一組方便時觀看文件庫的儲存空間的其餘服務與通訊 Azure 儲存體 SDK。

### <a name="use-node-package-manager-npm-to-install-the-package"></a>使用節點套件管理員 (NPM) 來安裝套件

1.  使用**PowerShell** (Windows)、 **Terminal** (Mac)，或**被**(Unix)，例如命令列介面，並瀏覽至您用來建立您的應用程式的資料夾。

2.  在 [命令] 視窗中，輸入**npm 安裝 azure 儲存體**。 命令輸出很類似以下的範例。

        azure-storage@0.5.0 node_modules\azure-storage
        +-- extend@1.2.1
        +-- xmlbuilder@0.4.3
        +-- mime@1.2.11
        +-- node-uuid@1.4.3
        +-- validator@3.22.2
        +-- underscore@1.4.4
        +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
        +-- xml2js@0.2.7 (sax@0.5.2)
        +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)

3.  您可以手動執行**1**命令，以確認**節點\_模組**建立資料夾。 該資料夾內，您會發現**azure 儲存體**套件，其中包含您要存取儲存的文件庫。

### <a name="import-the-package"></a>匯入套件

移至您的應用程式中的**server.js**檔案頂端新增下列程式碼︰

    var azure = require('azure-storage');

## <a name="set-up-an-azure-storage-connection"></a>設定連線至 Azure 儲存體

Azure 模組會讀取環境變數 AZURE\_儲存\_帳戶和 AZURE\_儲存\_存取\_鍵或 AZURE\_儲存\_連線\_字串連線至 Azure 儲存體帳戶所需的資訊。 如果沒有設定這些環境變數，您必須在撥打**TableService**時，指定帳戶資訊。

如需設定環境變數 Azure 網站[Azure 入口網站](https://portal.azure.com)中的範例，請參閱[使用 Azure 資料表服務 Node.js web 應用程式]。

## <a name="create-a-table"></a>建立表格

下列程式碼建立**TableService**物件，並使用它來建立新的資料表。 新增下列**server.js**頂端附近。

    var tableSvc = azure.createTableService();

如果不存在**createTableIfNotExists**通話會使用指定的名稱建立新的資料表。 下列範例會建立新名為 「 我的表格]，如果不存在的資料表︰

    tableSvc.createTableIfNotExists('mytable', function(error, result, response){
      if(!error){
        // Table exists or created
      }
    });

`result.created`會`true`如果建立新的資料表，並`false`如果資料表已經存在。 `response`會包含要求的資訊。

### <a name="filters"></a>篩選

選用的篩選作業可以套用到使用**TableService**執行的作業。 篩選作業，可以包含記錄、 自動重試等。篩選是物件的實作具有簽章的方法︰

    function handle (requestOptions, next)

執行其前置處理要求選項之後, 方法需要呼叫 [下一步]，傳遞使用下列的簽章的回撥︰

    function (returnObject, finalCallback, next)

這回撥，和 [後中處理 returnObject （回應要求從伺服器），回撥必須 [下一步呼叫若存在繼續處理其他篩選] 或 [即可叫用 finalCallback 否則結束服務叫用。

實作重試邏輯的兩個篩選所包含的 Node.js、 **ExponentialRetryPolicyFilter**及**LinearRetryPolicyFilter**Azure SDK 的。 下列範例會建立使用**ExponentialRetryPolicyFilter**的**TableService**物件︰

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var tableSvc = azure.createTableService().withFilter(retryOperations);

## <a name="add-an-entity-to-a-table"></a>新增至表格的實體

若要新增的實體，先建立物件，定義您的實體屬性。 所有項目必須包含**PartitionKey**和**RowKey**，也就是實體的唯一識別碼。

* **PartitionKey** -決定實體儲存在磁碟分割

* **RowKey** -唯一識別磁碟分割內實體

**PartitionKey**和**RowKey**都必須是字串值。 如需詳細資訊，請參閱[瞭解表格服務的資料模型](http://msdn.microsoft.com/library/azure/dd179338.aspx)。

以下是範例定義實體。 請注意該**dueDate**定義為**Edm.DateTime**類型。 指定類型，而且類型會被推斷如果未指定。

    var task = {
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '1'},
      description: {'_':'take out the trash'},
      dueDate: {'_':new Date(2015, 6, 20), '$':'Edm.DateTime'}
    };

> [AZURE.NOTE] 也有**時間戳記**欄位時插入或更新的實體 Azure 設定每一筆記錄。

您也可以使用**entityGenerator**建立項目。 下列範例會建立相同的工作實體使用**entityGenerator**。

    var entGen = azure.TableUtilities.entityGenerator;
    var task = {
      PartitionKey: entGen.String('hometasks'),
      RowKey: entGen.String('1'),
      description: entGen.String('take out the trash'),
      dueDate: entGen.DateTime(new Date(Date.UTC(2015, 6, 20))),
    };

若要新增至表格的實體的實體物件傳遞至**insertEntity**的方法。

    tableSvc.insertEntity('mytable',task, function (error, result, response) {
      if(!error){
        // Entity inserted
      }
    });

如果作業是成功，`result`包含[ETag](http://en.wikipedia.org/wiki/HTTP_ETag)插入記錄的和`response`包含作業的相關資訊。

範例回應︰

    { '.metadata': { etag: 'W/"datetime\'2015-02-25T01%3A22%3A22.5Z\'"' } }

> [AZURE.NOTE] 根據預設， **insertEntity**不會傳回插入的實體屬於`response`資訊。 如果您計劃在其他上執行的作業此實體，或要快取資訊時，它會有幫助，讓該部分的形式傳回`result`。 您可以啟用**echoContent** ，如下所示︰
>
> `tableSvc.insertEntity('mytable', task, {echoContent: true}, function (error, result, response) {...}`

## <a name="update-an-entity"></a>更新實體

有多個可用更新現有的實體的方法︰

* **replaceEntity** -以更新現有的實體取代

* **mergeEntity** -以更新現有的實體合併到現有的實體的新屬性值

* **insertOrReplaceEntity** -以更新現有的實體取代。 如果沒有實體存在，會插入新的項目

* **insertOrMergeEntity** -更新現有的實體合併到現有的 [新的屬性值。 如果沒有實體存在，會插入新的項目

下列範例會示範更新使用**replaceEntity**實體︰

    tableSvc.replaceEntity('mytable', updatedTask, function(error, result, response){
      if(!error) {
        // Entity updated
      }
    });

> [AZURE.NOTE] 根據預設，更新實體不會檢查以查看資料更新後是否先前由其他處理程序修改。 支援同時更新︰
>
> 1. 取得 ETag 更新後的物件。 這會傳回屬於`response`任何實體相關作業，可透過擷取`response['.metadata'].etag`。
>
> 2. 執行在實體上的更新作業時，新增先前擷取至新的實體 ETag 資訊。 例如︰
>
>     `entity2['.metadata'].etag = currentEtag;`
>
> 3. 執行更新作業。 如果之後您擷取的 ETag 值，例如另一個應用程式中，執行個體經過修改實體`error`說明在邀請中所指定的更新條件不符合會傳回。

與**replaceEntity** **mergeEntity**，如果要更新的實體不存在，然後更新作業會失敗。 因此如果您想要儲存的實體，而不論是否已經存在，請使用**insertOrReplaceEntity**或**insertOrMergeEntity**。

`result`成功更新作業會包含**Etag**的更新的實體。

## <a name="work-with-groups-of-entities"></a>使用群組的項目

有時讓提交合作以確保原子處理伺服器批次中的多個作業。 若要完成的工作，建立批次，使用**TableBatch**類別，然後使用**TableService**的**executeBatch**方法執行批次的作業。

 下列範例會示範送出以批次的兩個項目︰

    var task1 = {
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '1'},
      description: {'_':'Take out the trash'},
      dueDate: {'_':new Date(2015, 6, 20)}
    };
    var task2 = {
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '2'},
      description: {'_':'Wash the dishes'},
      dueDate: {'_':new Date(2015, 6, 20)}
    };

    var batch = new azure.TableBatch();

    batch.insertEntity(task1, {echoContent: true});
    batch.insertEntity(task2, {echoContent: true});

    tableSvc.executeBatch('mytable', batch, function (error, result, response) {
      if(!error) {
        // Batch completed
      }
    });

成功批次作業`result`會包含每個批次中作業的資訊。

### <a name="work-with-batched-operations"></a>使用批次的作業

新增至以批次的作業可以檢視]，以檢查`operations`屬性。 您也可以使用下列方法，使用作業︰

* **清除**-清除所有作業以批次

* **getOperations** -取得作業從批次

* **hasOperations** -如果批次中含有作業，則傳回 true

* **removeOperations** -移除作業

* **大小**-批次中傳回運算的數目

## <a name="retrieve-an-entity-by-key"></a>擷取實體索引鍵

要傳回特定的實體根據**PartitionKey**和**RowKey**，請使用**retrieveEntity**的方法。

    tableSvc.retrieveEntity('mytable', 'hometasks', '1', function(error, result, response){
      if(!error){
        // result contains the entity
      }
    });

這項作業完成之後，`result`包含實體。

## <a name="query-a-set-of-entities"></a>查詢的實體一組

若要查詢的資料表，請使用下列子句的查詢運算式建立使用**TableQuery**物件︰

* **選取**-從查詢傳回的欄位

* **位置**-where 子句

    * **與**-`and`位置條件

    * **或**-`or`位置條件

* **頁首**以擷取的項目數


下列範例會建立一個查詢，會傳回前五個項目與 PartitionKey 的 「 hometasks 」。

    var query = new azure.TableQuery()
      .top(5)
      .where('PartitionKey eq ?', 'hometasks');

不使用**選取**，所以會傳回所有欄位。 若要執行查詢對表格，使用**queryEntities**。 下列範例會使用這個查詢傳回的實體從 「 我的表格]。

    tableSvc.queryEntities('mytable',query, null, function(error, result, response) {
      if(!error) {
        // query was successful
      }
    });

如果成功，`result.entries`會包含陣列的實體符合查詢。 如果查詢無法傳回所有項目，`result.continuationToken`會在非*null* ，可用於**queryEntities**的第三個參數以擷取更多結果。 初始查詢，使用*null*第三個參數。

### <a name="query-a-subset-of-entity-properties"></a>查詢實體屬性的子集

查詢的資料表可以取得只需要幾個欄位的實體。
這會減少頻寬，並可以改善查詢效能，特別是大型的項目。 使用**select**子句，而且將會傳回欄位的名稱。 例如，下列查詢會傳回只有**描述**] 和 [ **dueDate**欄位。

    var query = new azure.TableQuery()
      .select(['description', 'dueDate'])
      .top(5)
      .where('PartitionKey eq ?', 'hometasks');

## <a name="delete-an-entity"></a>刪除實體

您可以刪除使用其分割及列的實體。 在此範例中，**任務 1**物件包含**RowKey**和**PartitionKey**遭到刪除的實體。 然後物件傳遞**deleteEntity**方法。

    var task = {
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '1'}
    };

    tableSvc.deleteEntity('mytable', task, function(error, response){
      if(!error) {
        // Entity deleted
      }
    });

> [AZURE.NOTE] 請考慮使用 Etag 刪除項目，以確保由其他處理程序尚未已修改的項目。 使用 Etag 資訊，請參閱[更新實體](#update-an-entity)。

## <a name="delete-a-table"></a>刪除表格

下列程式碼會刪除一個資料表儲存體帳戶。

    tableSvc.deleteTable('mytable', function(error, response){
        if(!error){
            // Table deleted
        }
    });

如果您不確定是否有資料表，請使用 [ **deleteTableIfExists**。

## <a name="use-continuation-tokens"></a>使用 [接續權杖

當您查詢的結果大量的資料表時，尋找接續權杖。 可能會有大量的資料可供您可能會發現是否您無法建立接續權杖簡報時，都可以辨識的查詢。

結果物件期間查詢實體集傳回`continuationToken`此權杖簡報時的屬性。 您可以再使用這個執行查詢時若要繼續的磁碟分割和資料表實體間移動。

查詢時，可能會提供 continuationToken 參數查詢的物件執行個體之間的回撥函數︰

```
var nextContinuationToken = null;
dc.table.queryEntities(tableName,
    query,
    nextContinuationToken,
    function (error, results) {
        if (error) throw error;

        // iterate through results.entries with results

        if (results.continuationToken) {
            nextContinuationToken = results.continuationToken;
        }

    });
```

如果您檢查`continuationToken`物件，您會發現屬性例如`nextPartitionKey`，`nextRowKey`和`targetLocation`，其可逐一查看所有的結果。

此外，還有內上 GitHub Azure 儲存體 Node.js repo 接續樣本。 尋找`examples/samples/continuationsample.js`。

## <a name="work-with-shared-access-signatures"></a>使用共用的 access 簽章

共用的 access 簽章 (SA) 是安全的方法，但不供您儲存體帳戶名稱或鍵提供細緻的存取權的資料表。 SA 通常用於提供您的資料，例如讓查詢記錄行動應用程式的有限存取權。

信任的應用程式，例如雲端服務所使用的**TableService**， **generateSharedAccessSignature** SA，並將其提供給受信任或完全信任應用程式上，例如行動應用程式。 SA 會產生使用原則，將說明在 SA 是有效的開始和結束日期，以及 SA 持有者授與存取層級。

下列範例會產生新的共用的存取原則，可讓 SA 持有進行查詢 (「 r 」) 資料表，並到期 100 的分鐘後就會建立的時間。

    var startDate = new Date();
    var expiryDate = new Date(startDate);
    expiryDate.setMinutes(startDate.getMinutes() + 100);
    startDate.setMinutes(startDate.getMinutes() - 100);

    var sharedAccessPolicy = {
      AccessPolicy: {
        Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
        Start: startDate,
        Expiry: expiryDate
      },
    };

    var tableSAS = tableSvc.generateSharedAccessSignature('mytable', sharedAccessPolicy);
    var host = tableSvc.host;

請注意，host （主機） 必須提供資訊也，因為它是在 SA 持有者嘗試存取資料表時所需。

然後，用戶端應用程式使用**TableServiceWithSAS**執行作業表格 SA。 下列範例會連結到資料表，並執行查詢。

    var sharedTableService = azure.createTableServiceWithSas(host, tableSAS);
    var query = azure.TableQuery()
      .where('PartitionKey eq ?', 'hometasks');

    sharedTableService.queryEntities(query, null, function(error, result, response) {
      if(!error) {
        // result contains the entities
      }
    });

由於 SA 產生透過只查詢存取時，如果您已嘗試插入、 更新或刪除項目，就會傳回錯誤。

### <a name="access-control-lists"></a>存取控制清單

您也可以使用存取控制清單 (ACL) 若要設定 SA 存取原則。 這是很有用，如果您想要允許多個用戶端存取表格，但每個用戶端提供不同的存取原則。

ACL 實作存取原則的陣列，使用每個原則相關聯的識別碼。 下列範例會定義兩個原則，一個 'user1'，一個用於 'user2 」:

    var sharedAccessPolicy = {
      user1: {
        Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
        Start: startDate,
        Expiry: expiryDate
      },
      user2: {
        Permissions: azure.TableUtilities.SharedAccessPermissions.ADD,
        Start: startDate,
        Expiry: expiryDate
      }
    };

下列範例會取得目前 ACL **hometasks**資料表，然後新增 [使用**setTableAcl**的新原則。 可讓這種方法︰

    var extend = require('extend');
    tableSvc.getTableAcl('hometasks', function(error, result, response) {
    if(!error){
        var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
        tableSvc.setTableAcl('hometasks', newSignedIdentifiers, function(error, result, response){
          if(!error){
            // ACL set
          }
        });
      }
    });

一旦 ACL 設定，然後您可以建立 SA，根據原則的識別碼。 下列範例會建立新的 「 user2' SA:

    tableSAS = tableSvc.generateSharedAccessSignature('hometasks', { Id: 'user2' });

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列資源。

-   [Azure 儲存體小組部落格][]。
-   在 GitHub [Azure 儲存體 SDK 節點][]存放庫。
-   [Node.js 開發人員中心](/develop/nodejs/)

  [Azure 儲存體 SDK 節點]: https://github.com/Azure/azure-storage-node
  [OData.org]: http://www.odata.org/
  [Using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
  [Azure Portal]: portal.azure.com

  [Node.js Cloud Service]: ../cloud-services-nodejs-develop-deploy-app.md
  [Azure 儲存體小組部落格]: http://blogs.msdn.com/b/windowsazurestorage/
  [Website with WebMatrix]: ../web-sites-nodejs-use-webmatrix.md
  [Node.js Cloud Service with Storage]: ../storage-nodejs-use-table-storage-cloud-service-app.md
  [使用 Azure 資料表服務 Node.js web 應用程式]: ../storage-nodejs-use-table-storage-web-site.md
  [Create and deploy a Node.js application to an Azure website]: ../web-sites-nodejs-develop-deploy-mac.md
