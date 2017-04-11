<properties
    pageTitle="Azure 函數 DocumentDB 繫結 |Microsoft Azure"
    description="瞭解如何使用 Azure DocumentDB 繫結 Azure 函數中。"
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
    ms.author="chrande; glenga"/>

# <a name="azure-functions-documentdb-bindings"></a>Azure 函數 DocumentDB 繫結

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

本文說明如何設定和 Azure 函數中的 [程式碼 Azure DocumentDB 繫結。 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

## <a id="docdbinput"></a>Azure DocumentDB 輸入繫結

輸入繫結可以從 DocumentDB 集合載入文件，並將其直接傳遞給繫結。 文件識別碼，可以根據叫用函數的觸發程序來決定。 C# 函數中記錄所做的任何變更將會自動傳送集合函數成功結束時。

#### <a name="functionjson-for-documentdb-input-binding"></a>function.json DocumentDB 輸入繫結

*Function.json*檔案會提供下列屬性︰

- `name`︰ 用於函數程式碼中的文件的名稱變數。
- `type`︰ 必須設定為 「 documentdb 」。
- `databaseName`︰ 包含文件的資料庫。
- `collectionName`︰ 包含文件的集合。
- `id`︰ 若要擷取識別碼的文件。 此屬性支援繫結類似 「 {queueTrigger} 」，這會使用佇列中郵件的字串值的文件識別碼
- `connection`︰ 這個字串必須是應用程式設定] 設定為 [DocumentDB 帳戶的端點。 如果您從 [整合] 索引標籤中選擇您的帳戶，將會為您建立新的應用程式設定，採用下列格式，也就是 yourAccount_DOCUMENTDB 的名稱。 若要手動建立 [應用程式設定，實際的連接字串必須採取動作的表單，AccountEndpoint =<Endpoint for your account>;AccountKey =<Your primary access key>;。
- 「 方向︰ 必須設定為*「 中 」*。

範例*function.json*:
 
    {
      "bindings": [
        {
          "name": "document",
          "type": "documentdb",
          "databaseName": "MyDatabase",
          "collectionName": "MyCollection",
          "id" : "{queueTrigger}",
          "connection": "MyAccount_DOCUMENTDB",     
          "direction": "in"
        }
      ],
      "disabled": false
    }

#### <a name="azure-documentdb-input-code-example-for-a-c-queue-trigger"></a>C# 佇列中觸發程序的 azure DocumentDB 輸入的程式碼範例
 
使用上述範例 function.json，DocumentDB 輸入繫結會擷取文件中，符合佇列中的郵件之字串的識別碼，並將它傳遞給 「 文件 」 參數。 如果找不到文件，「 文件 」 參數將為 null。 函數結束時，文件然後會更新為新的文字值。
 
    public static void Run(string myQueueItem, dynamic document)
    {   
        document.text = "This has changed.";
    }

#### <a name="azure-documentdb-input-code-example-for-an-f-queue-trigger"></a>F # 佇列中引動程序的 azure DocumentDB 輸入的程式碼範例

使用上述範例 function.json，DocumentDB 輸入繫結會擷取文件中，符合佇列中的郵件之字串的識別碼，並將它傳遞給 「 文件 」 參數。 如果找不到文件，「 文件 」 參數將為 null。 函數結束時，文件然後會更新為新的文字值。

    open FSharp.Interop.Dynamic
    let Run(myQueueItem: string, document: obj) =
        document?text <- "This has changed."

您將需要`project.json`NuGet 用來指定檔案`FSharp.Interop.Dynamic`和`Dynamitey`封裝為封裝相依性，像這樣︰

    {
      "frameworks": {
        "net46": {
          "dependencies": {
            "Dynamitey": "1.0.2",
            "FSharp.Interop.Dynamic": "3.0.0"
          }
        }
      }
    }

這會使用 NuGet 擷取相依性，並將指令碼參考。

#### <a name="azure-documentdb-input-code-example-for-a-nodejs-queue-trigger"></a>Azure DocumentDB 輸入的程式碼範例 Node.js 佇列中觸發程序
 
使用上述範例 function.json，DocumentDB 輸入繫結會擷取文件中，符合佇列中的郵件之字串的識別碼，並傳遞至`documentIn`繫結屬性。 Node.js 函數中不會傳送更新的文件集合。 不過，您可以輸入繫結直接傳遞給繫結 DocumentDB 輸出命名`documentOut`支援更新。 此範例中的程式碼更新輸入文件中的 [文字] 屬性，並將它設定為輸出文件。
 
    module.exports = function (context, input) {   
        context.bindings.documentOut = context.bindings.documentIn;
        context.bindings.documentOut.text = "This was updated!";
        context.done();
    };

## <a id="docdboutput"></a>Azure DocumentDB 輸出繫結

您的函數撰寫 JSON Azure DocumentDB 資料庫使用**Azure DocumentDB 文件**的文件輸出繫結。 如需有關 Azure DocumentDB 檢閱[DocumentDB 簡介](../documentdb/documentdb-introduction.md)和[開始教學課程](../documentdb/documentdb-get-started.md)。

#### <a name="functionjson-for-documentdb-output-binding"></a>function.json DocumentDB 的輸出繫結

Function.json 檔案會提供下列屬性︰

- `name`︰ 在 [函數程式碼中用於新文件變數的名稱。
- `type`︰ 必須設定為*「 documentdb 」*。
- `databaseName`︰ 包含將會建立新文件集合的資料庫。
- `collectionName`︰ 建立新文件的位置集合。
- `createIfNotExists`︰ 這是一個 boolean 值，指出不存在，是否會建立集合。 預設為*false*。 原因這是新的集合建立與保留處理量，具有價格的含意。 如需詳細資訊，請造訪[價格頁面](https://azure.microsoft.com/pricing/details/documentdb/)。
- `connection`︰**應用程式設定**設為端點 DocumentDB 帳戶時，必須這個字串。 如果您從 [**整合**] 索引標籤中選擇您的帳戶，就會新的應用程式設定為您建立的名稱，格式如下`yourAccount_DOCUMENTDB`。 若要手動建立 [應用程式設定，實際的連接字串必須採取動作的表單， `AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>;`。 
- `direction`︰ 必須設定為*「 取出 」*。 
 
範例 function.json:

    {
      "bindings": [
        {
          "name": "document",
          "type": "documentdb",
          "databaseName": "MyDatabase",
          "collectionName": "MyCollection",
          "createIfNotExists": false,
          "connection": "MyAccount_DOCUMENTDB",
          "direction": "out"
        }
      ],
      "disabled": false
    }


#### <a name="azure-documentdb-output-code-example-for-a-nodejs-queue-trigger"></a>Node.js 佇列中觸發程序的 azure DocumentDB 輸出程式碼範例

    module.exports = function (context, input) {
       
        context.bindings.document = {
            text : "I'm running in a Node function! Data: '" + input + "'"
        }   
     
        context.done();
    };

輸出文件︰

    {
      "text": "I'm running in a Node function! Data: 'example queue data'",
      "id": "01a817fe-f582-4839-b30c-fb32574ff13f"
    }
 

#### <a name="azure-documentdb-output-code-example-for-an-f-queue-trigger"></a>F # 佇列中觸發程序的 azure DocumentDB 輸出程式碼範例

    open FSharp.Interop.Dynamic
    let Run(myQueueItem: string, document: obj) =
        document?text <- (sprintf "I'm running in an F# function! %s" myQueueItem)

#### <a name="azure-documentdb-output-code-example-for-a-c-queue-trigger"></a>C# 佇列中觸發程序的 azure DocumentDB 輸出程式碼範例


    using System;

    public static void Run(string myQueueItem, out object document, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
       
        document = new {
            text = $"I'm running in a C# function! {myQueueItem}"
        };
    }


#### <a name="azure-documentdb-output-code-example-setting-file-name"></a>Azure DocumentDB 輸出的程式碼範例設定檔名稱

如果您想要設定在函數中的文件的名稱，則只要設定`id`值。  例如，如果 JSON 員工的內容已被置放於類似下列佇列中︰

    {
      "name" : "John Henry",
      "employeeId" : "123456",
      "address" : "A town nearby"
    }

您可以使用下列 C# 程式碼佇列中觸發程序函數中︰ 
    
    #r "Newtonsoft.Json"
    
    using System;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    
    public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
        
        dynamic employee = JObject.Parse(myQueueItem);
        
        employeeDocument = new {
            id = employee.name + "-" + employee.employeeId,
            name = employee.name,
            employeeId = employee.employeeId,
            address = employee.address
        };
    }

或相當於 F # 程式碼︰

    open FSharp.Interop.Dynamic
    open Newtonsoft.Json

    type Employee = {
        id: string
        name: string
        employeeId: string
        address: string
    }

    let Run(myQueueItem: string, employeeDocument: byref<obj>, log: TraceWriter) =
        log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
        let employee = JObject.Parse(myQueueItem)
        employeeDocument <-
            { id = sprintf "%s-%s" employee?name employee?employeeId
              name = employee?name
              employeeId = employee?id
              address = employee?address }

輸出範例︰

    {
      "id": "John Henry-123456",
      "name": "John Henry",
      "employeeId": "123456",
      "address": "A town nearby"
    }

## <a name="next-steps"></a>後續步驟

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]
