<properties
    pageTitle="Azure 函數引動程序和 Azure 儲存體的繫結 |Microsoft Azure"
    description="瞭解如何使用 Azure 函數中的 Azure 儲存體引動程序及連結。"
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="azure 函數，如函數、 事件處理，動態計算，無架構"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="08/22/2016"
    ms.author="chrande"/>

# <a name="azure-functions-triggers-and-bindings-for-azure-storage"></a>Azure 函數引動程序與繫結 Azure 儲存體

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

本文說明如何設定程式碼 Azure 儲存體引動程序和 Azure 函數中的連結。 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

## <a id="storagequeuetrigger"></a>Azure 儲存佇列中觸發程序

#### <a name="functionjson-for-storage-queue-trigger"></a>function.json 儲存佇列中觸發程序

*Function.json*檔案指定下列屬性。

- `name`︰ 在 [函數程式碼中用於佇列中] 或 [佇列中的郵件變數名稱。 
- `queueName`︰ 以獲得佇列的名稱。 佇列中命名規則，請參閱[命名佇列和中繼資料](https://msdn.microsoft.com/library/dd179349.aspx)。
- `connection`︰ 設定應用程式名稱包含儲存連線字串。 如果您離開`connection`空白，觸發程序可搭配函數應用程式，AzureWebJobsStorage 應用程式設定所指定的預設儲存空間連線字串。
- `type`︰ 必須設定為 [ *queueTrigger*。
- `direction`︰ 必須設定為 [*中*。 

範例*function.json*儲存佇列中觸發程序︰

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"",
            "type": "queueTrigger",
            "direction": "in"
        }
    ]
}
```

#### <a name="queue-trigger-supported-types"></a>佇列中觸發程序支援的類型

下列類型的任何可還原序列化佇列訊息︰

* （從 JSON) 的物件
* 字串
* 位元組陣列 
* `CloudQueueMessage`(C#) 

#### <a name="queue-trigger-metadata"></a>佇列中觸發程序中繼資料

您可以使用這些變數的名稱，以取得您函數佇列中的中繼資料︰

* expirationTime
* insertionTime
* nextVisibleTime
* 識別碼
* popReceipt
* dequeueCount
* queueTrigger （另一種方法來擷取佇列中的郵件文字字串形式）

此 C# 程式碼範例擷取和記錄佇列中繼資料︰

```csharp
public static void Run(string myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

#### <a name="handling-poison-queue-messages"></a>處理有害佇列中的郵件

其內容會導致失敗函數的郵件被稱為*有害的郵件*。 當函數失敗時，佇列中郵件不會刪除，並最後挑選一次，導致要重複循環圖。 SDK 可以自動中斷週期有限次數後，或您可以手動執行。

SDK 會 call 函數 5 倍處理佇列中的訊息。 如果第五個嘗試失敗，郵件會移至有害佇列中。

有害佇列中名稱為*{originalqueuename}*-有害。 您可以撰寫需要從有害的佇列中，使用這些記錄，或傳送通知給該手動處理程序郵件函數。 

如果您想要以手動方式處理有害的郵件，您可以取得的處理郵件挑選的次數，請核取`dequeueCount`。

## <a id="storagequeueoutput"></a>Azure 儲存佇列中輸出繫結

#### <a name="functionjson-for-storage-queue-output-binding"></a>儲存佇列 function.json 輸出繫結

*Function.json*檔案指定下列屬性。

- `name`︰ 在 [函數程式碼中用於佇列中] 或 [佇列中的郵件變數名稱。 
- `queueName`︰ 佇列的名稱。 佇列中命名規則，請參閱[命名佇列和中繼資料](https://msdn.microsoft.com/library/dd179349.aspx)。
- `connection`︰ 設定應用程式名稱包含儲存連線字串。 如果您離開`connection`空白，觸發程序可搭配函數應用程式，AzureWebJobsStorage 應用程式設定所指定的預設儲存空間連線字串。
- `type`︰ 必須設定為 [*佇列中*。
- `direction`︰ 若要*查看*必須設定。 

儲存佇列中的範例*function.json*輸出繫結的使用佇列中觸發程序，並將佇列中訊息︰

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myQueue",
      "queueName": "samples-workitems-out",
      "connection": "MyStorageConnection",
      "type": "queue",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### <a name="queue-output-binding-supported-types"></a>佇列中支援的輸出繫結類型

`queue`繫結可以序列化下列類型的佇列中的訊息︰

* 物件 (`out T` C# 中，以建立郵件 null 物件函數結束時，如果參數 null)
* 字串 (`out string` C# 中建立佇列中郵件如果參數值函式結束時，請為非 null)
* 位元組陣列 (`out byte[]` C# 中運作等字串) 
* `out CloudQueueMessage`(C# 的運作方式字串) 

C# 中您也可以繫結至`ICollector<T>`或`IAsyncCollector<T>`位置`T`是其中一個支援的類型。

#### <a name="queue-output-binding-code-examples"></a>佇列中輸出繫結的程式碼範例

這個 C# 程式碼範例將單一輸出佇列中訊息的每個佇列中輸入的訊息。

```csharp
public static void Run(string myQueueItem, out string myOutputQueueItem, TraceWriter log)
{
    myOutputQueueItem = myQueueItem + "(next step)";
}
```

使用這個 C# 程式碼範例所撰寫的多個訊息`ICollector<T>`(使用`IAsyncCollector<T>`非同步函數中):

```csharp
public static void Run(string myQueueItem, ICollector<string> myQueue, TraceWriter log)
{
    myQueue.Add(myQueueItem + "(step 1)");
    myQueue.Add(myQueueItem + "(step 2)");
}
```

## <a id="storageblobtrigger"></a>Azure 儲存體 blob 觸發程序

#### <a name="functionjson-for-storage-blob-trigger"></a>function.json 儲存 blob 觸發程序

*Function.json*檔案指定下列屬性。

- `name`: 在 [函數程式碼中用於 blob 變數名稱。 
- `path`︰ 指定要監視的容器路徑和 （選擇性） blob 名稱模式。
- `connection`︰ 設定應用程式名稱包含儲存連線字串。 如果您離開`connection`空白，觸發程序可搭配函數應用程式，AzureWebJobsStorage 應用程式設定所指定的預設儲存空間連線字串。
- `type`︰ 必須設定為 [ *blobTrigger*。
- `direction`︰ 必須設定為 [*中*。

範例*function.json*監看會新增至範例工作項目容器的 blob 儲存體 blob 觸發程序︰

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":""
        }
    ]
}
```

#### <a name="blob-trigger-supported-types"></a>Blob 觸發程序支援的類型

Blob 可還原至任何節點或 C# 函數中的下列類型的序列化︰

* （從 JSON) 的物件
* 字串

C# 函數中您也可以繫結至任何下列類型︰

* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `IEnumerable<CloudBlockBlob>`
* `IEnumerable<CloudPageBlob>`
* 還原序列化[ICloudBlobStreamBinder](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md#icbsb)的其他類型 

#### <a name="blob-trigger-c-code-example"></a>Blob 觸發程序 C# 程式碼範例

這個 C# 程式碼範例記錄新增至監視容器每個 blob 的內容。

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

#### <a name="blob-trigger-name-patterns"></a>Blob 觸發程序名稱模式

您可以指定在 blob 名稱模式`path`屬性。 例如︰

```json
"path": "input/original-{name}",
```

此路徑會尋找名為中*輸入*的容器和值的*原始 Blob1.txt* blob`name`函數程式碼中的變數應`Blob1`。

另一個範例︰

```json
"path": "input/{blobname}.{blobextension}",
```

此路徑也會尋找名為*原始 Blob1.txt*和值 blob`blobname`和`blobextension`*原始 Blob1*和*txt*，就會在 [函數程式碼中的變數。

您可以限制檔案的副檔名是固定的值與指定模式觸發函數的二進位大型物件的類型。 如果您設定`path`*範例 / {名稱}.png*，只*.png* blob*範例*容器中的將會觸發函數。

如果您要指定名稱中的括號 blob 名稱名稱模式，請按兩下大括號。 例如，如果您想要尋找 blob*圖像*容器具有如下的名稱中︰

        {20140101}-soundfile.mp3

使用此`path`屬性︰

        images/{{20140101}}-{name}

在範例中，`name`變數值應*soundfile.mp3*。 

#### <a name="blob-receipts"></a>Blob 信回條

Azure 函數執行階段可確保 blob 的觸發程序函式的同一個新的或更新 blob 取得多次呼叫。 它會維護*blob 信回條*，才能判斷是否已處理指定的 blob 版本。

Blob 信回條會儲存在名為 AzureWebJobsStorage 連線字串所指定的 Azure 儲存體帳戶中的 [*主辦 webjobs azure 城市*容器。 Blob 回條具有下列資訊︰

* 函數呼叫的 blob （"*{函數應用程式名稱}*。函數。*{函數名稱}*」，例如: 「 functionsf74b96f7。Functions.CopyBlob 」)
* 容器名稱
* Blob 類型 （「 BlockBlob 」 或 「 PageBlob 」）
* Blob 名稱
* ETag (blob 版本識別項，例如: 「 0x8D1DC6E70A277EF 」)

如果您想要強制重新處理 blob 的您可以手動刪除該 blob blob 回條，從*主辦城市 webjobs azure*容器。

#### <a name="handling-poison-blobs"></a>處理有害的二進位大型物件

Blob 觸發程序函數失敗時，SDK 通話，再次以避免失敗所造成的暫時性的錯誤。 如果 blob 的內容會導致失敗，函數就會失敗，每次嘗試 blob [處理程序。 根據預設，SDK 呼叫函數最多 5 乘以指定 blob。 如果第五個嘗試失敗，請 SDK 會新增至名稱為*webjobs-blobtrigger-位置*佇列中的郵件。

有害的二進位大型物件的佇列中訊息是 JSON 物件，包含下列屬性︰

* FunctionId （在格式*{函數應用程式名稱}*。函數。*{函數名稱}*）
* BlobType （「 BlockBlob 」 或 「 PageBlob 」）
* ContainerName
* BlobName
* ETag (blob 版本識別項，例如: 「 0x8D1DC6E70A277EF 」)

#### <a name="blob-polling-for-large-containers"></a>Blob 輪詢大型容器

如果 blob 容器監控觸發程序包含超過 10000 個二進位大型物件，函數執行階段掃描記錄新增或變更 blob 監看的檔案。 此程序不是即時;函數可能不輪直到幾分鐘的時間，或再建立 blob 後。 此外，[在 「 最大努力 」 建立記錄檔的儲存空間](https://msdn.microsoft.com/library/azure/hh343262.aspx)基礎;有保證會擷取所有事件。 在某些情況下可能會遺失記錄。 如果 blob 引動程序大型容器的速度與可靠性限制不接受您的應用程式，建議的方法就是建立佇列中的郵件，當您建立 blob，處理 blob 佇列中觸發程序使用，而不是 blob 觸發程序。
 
## <a id="storageblobbindings"></a>Azure 儲存體 blob 輸入與輸出繫結

#### <a name="functionjson-for-a-storage-blob-input-or-output-binding"></a>儲存 blob 的 function.json 輸入或輸出繫結

*Function.json*檔案指定下列屬性。

- `name`: 在 [函數程式碼用於 blob 變數名稱。 
- `path`︰ 指定讀取從 blob 或寫入，blob 的容器路徑和 （選擇性） blob 名稱模式。
- `connection`︰ 設定應用程式名稱包含儲存連線字串。 如果您離開`connection`空白，繫結可搭配函數應用程式，AzureWebJobsStorage 應用程式設定所指定的預設儲存空間連線字串。
- `type`︰ 必須設定為 [ *blob*。
- `direction`︰ 設定為*或*縮小**。 

儲存空間的範例*function.json* blob 輸入或輸出繫結時，使用複製 blob 的佇列中觸發程序︰

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### <a name="blob-input-and-output-supported-types"></a>Blob 輸入與輸出支援的類型

`blob`繫結可以序列化或還原序列化下列 Node.js 或 C# 函數的類型︰

* 物件 (`out T` C# 的輸出 blob︰ 如果參數值為 null 函數結束時，建立 blob null 物件)
* 字串 (`out string` C# 的輸出 blob︰ 只有字串參數是非 null，則函數會傳回時，會建立 blob)

在 C# 功能中，您也可以繫結至下列類型︰

* `TextReader`（僅限輸入）
* `TextWriter`（僅限輸出）
* `Stream`
* `CloudBlobStream`（僅限輸出）
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

#### <a name="blob-output-c-code-example"></a>Blob 輸出 C# 程式碼範例

本 C# 程式碼範例複製的 blob 佇列中郵件中收到其名稱。

```csharp
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

## <a id="storagetablesbindings"></a>Azure 儲存資料表輸入與輸出繫結

#### <a name="functionjson-for-storage-tables"></a>function.json 儲存資料表

*Function.json*指定下列屬性。

- `name`︰ 在 [函數程式碼中用於表格繫結變數名稱。 
- `tableName`︰ 資料表的名稱。
- `partitionKey`與`rowKey`︰ 一起使用讀取 C# 或節點函數中的單一項目，或撰寫節點函數中的單一項目。
- `take`︰ 閱讀節點函數中的 [輸入表格的資料列最大數目。
- `filter`︰ 輸入節點函數中的表格 OData 篩選運算式。
- `connection`︰ 設定應用程式名稱包含儲存連線字串。 如果您離開`connection`空白，繫結可搭配函數應用程式，AzureWebJobsStorage 應用程式設定所指定的預設儲存空間連線字串。
- `type`︰ 必須設定為*資料表*。
- `direction`︰ 設定為*或*縮小**。 

下列範例*function.json*使用讀取單一表格列佇列中觸發程序。 JSON 提供的硬式編碼的磁碟分割關鍵值，並指定 [資料列識別碼來自佇列中郵件。

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

#### <a name="storage-tables-input-and-output-supported-types"></a>輸入與輸出的儲存空間資料表支援的類型

`table`繫結可以序列化或還原序列化 Node.js 或 C# 函數中的物件。 物件必須 RowKey 和 PartitionKey 屬性。 

在 C# 功能中，您也可以繫結至下列類型︰

* `T`位置`T`實作`ITableEntity`
* `IQueryable<T>`（僅限輸入）
* `ICollector<T>`（僅限輸出）
* `IAsyncCollector<T>`（僅限輸出）

#### <a name="storage-tables-binding-scenarios"></a>儲存資料表繫結案例

表格繫結支援以下情況︰

* 閱讀 C# 或節點函數中的單一資料列。

    設定`partitionKey`和`rowKey`。 `filter`和`take`屬性不用在這個案例中。

* 閱讀 C# 函數中的多個資料列。

    函數執行階段提供`IQueryable<T>`物件繫結至資料表。 輸入`T`必須衍生自`TableEntity`或實作`ITableEntity`。 `partitionKey`， `rowKey`， `filter`，及`take`屬性不用在這個案例中;您可以使用`IQueryable`物件做任何所需的篩選。 

* 閱讀節點函數中的多個資料列。

    設定`filter`和`take`屬性。 未設定`partitionKey`或`rowKey`。

* 撰寫 C# 函數中的一或多個資料列。

    函數執行階段提供`ICollector<T>`或`IAsyncCollector<T>`繫結至資料表，其中`T`指定您想要新增的實體架構。 一般而言，輸入`T`衍生自`TableEntity`或實作`ITableEntity`，但不含至。 `partitionKey`， `rowKey`， `filter`，及`take`屬性不用在這個案例中。

#### <a name="storage-tables-example-read-a-single-table-entity-in-c-or-node"></a>儲存資料表範例︰ 讀取單一資料表中的實體 C# 或節點

下列 C# 程式碼範例搭配讀取單一資料表實體稍早所示的上一個*function.json*檔案。 佇列中郵件的資料列關鍵值而資料表實體讀取到*run.csx*檔案中定義的類型。 輸入包含`PartitionKey`和`RowKey`內容並不是衍生自`TableEntity`。 

```csharp
public static void Run(string myQueueItem, Person personEntity, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    log.Info($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

下列 F # 程式碼範例也會搭配讀取單一資料表實體的上一個*function.json*檔案。

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(myQueueItem: string, personEntity: Person) =
    log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
    log.Info(sprintf "Name in Person entity: %s" personEntity.Name)
```

下列節點範例也會搭配讀取單一資料表實體的上一個*function.json*檔案。

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

#### <a name="storage-tables-example-read-multiple-table-entities-in-c"></a>儲存資料表範例︰ 閱讀 C 中的多個表格項目# 

下列*function.json*及 C# 程式碼佇列中訊息中指定資料分割索引鍵的範例讀取項的目。

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnection",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

C# 程式碼新增 Azure 儲存體 sdk 的變更的參照，以便實體類型可以衍生自`TableEntity`。

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.Info($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
``` 

#### <a name="storage-tables-example-create-table-entities-in-c"></a>儲存資料表範例︰ C 中建立表格項目# 

下列*function.json*和*run.csx*範例示範如何在 C# 撰寫表格項目。

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnection",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

```csharp
public static void Run(string input, ICollector<Person> tableBinding, TraceWriter log)
{
    for (int i = 1; i < 10; i++)
        {
            log.Info($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

#### <a name="storage-tables-example-create-table-entities-in-f"></a>儲存資料表範例︰ F 中建立表格項目#

下列*function.json*和*run.fsx*範例示範如何在 F # 撰寫表格項目。

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnection",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(input: string, tableBinding: ICollector<Person>, log: TraceWriter) =
    for i = 1 to 10 do
        log.Info(sprintf "Adding Person entity %d" i)
        tableBinding.Add(
            { PartitionKey = "Test"
              RowKey = i.ToString()
              Name = "Name" + i.ToString() })
```

#### <a name="storage-tables-example-create-a-table-entity-in-node"></a>儲存資料表範例︰ 建立表格實體節點中

下列*function.json*和*run.csx*範例節點中撰寫資料表實體的方式。

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "name": "personEntity",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": true
}
```

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.bindings.personEntity = {"Name": "Name" + myQueueItem }
    context.done();
};
```

## <a name="next-steps"></a>後續步驟

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)] 
