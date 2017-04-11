<properties
    pageTitle="Azure 函數引動程序和繫結 |Microsoft Azure"
    description="瞭解如何使用引動程序和繫結 Azure 函數中。"
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="azure 函數、 函數、 事件處理、 webhooks、 動態計算、 無架構"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="10/27/2016"
    ms.author="chrande"/>

# <a name="azure-functions-triggers-and-bindings-developer-reference"></a>Azure 函數引動程序和繫結開發人員參考


本主題提供引動程序和繫結的一般的參考。 其包含的某些進階繫結功能與支援所有的繫結類型的語法。  

如果您正在尋找的設定，以及編碼特定類型的觸發程序或繫結的詳細資訊，您可能會想要按一下 [觸發程序或繫結以下列出的其中一個︰

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)] 

這些文章假設您已閱讀[Azure 函數開發人員參考](functions-reference.md)及[C#](functions-reference-csharp.md)、 [F #](functions-reference-fsharp.md)或[Node.js](functions-reference-node.md)開發人員參考文章。



## <a name="overview"></a>概觀

觸發程序是用來觸發自訂程式碼的事件回應。 他們可讓您透過 Azure 平台或內部部署回應事件。 繫結代表必要的中繼資料，用來連線到您要觸發程序或相關聯的輸入或輸出資料的 [程式碼。

若要取得您可以整合 Azure 函數應用程式使用不同的繫結的好，請參閱下表。

[AZURE.INCLUDE [dynamic compute](../../includes/functions-bindings.md)]  

若要進一步瞭解觸發並繫結一般而言，假設您想要執行程式碼來新增項目放入 Azure 儲存體佇列中的程序。 Azure 函數提供支援 Azure 佇列中觸發程序。 您需要，監控佇列中的下列資訊︰
 
- 儲存帳戶佇列中存在。
- 佇列中的名稱。
- 變數的名稱，用您的程式碼會用來參照已放入佇列中的新項目。  
 
佇列中觸發程序繫結包含 Azure 函數這項資訊。 每個函數*function.json*檔案會包含所有相關的繫結。  以下是範例*function.json*包含佇列觸發繫結。 

    {
      "bindings": [
        {
          "name": "myNewUserQueueItem",
          "type": "queueTrigger",
          "direction": "in",
          "queueName": "queue-newusers",
          "connection": "MY_STORAGE_ACCT_APP_SETTING"
        }
      ],
      "disabled": false
    }

您的程式碼可能會傳送不同類型的輸出根據新的佇列中項目的處理方式。 例如，您可能要 Azure 儲存體的資料表中撰寫新的記錄。  若要這麼做，您可以設定的輸出繫結至 Azure 儲存體的資料表。 以下是範例*function.json*包含的儲存空間表格輸出繫結，無法使用佇列中觸發程序。 


    {
      "bindings": [
        {
          "name": "myNewUserQueueItem",
          "type": "queueTrigger",
          "direction": "in",
          "queueName": "queue-newusers",
          "connection": "MY_STORAGE_ACCT_APP_SETTING"
        },
        {
          "type": "table",
          "name": "myNewUserTableBinding",
          "tableName": "newUserTable",
          "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING",
          "direction": "out"
        }
      ],
      "disabled": false
    }


下列 C# 函數回應被置放於佇列中的新項目，並將新使用者項目至 Azure 儲存體表格。

    #r "Newtonsoft.Json"

    using System;
    using Newtonsoft.Json;

    public static async Task Run(string myNewUserQueueItem, IAsyncCollector<Person> myNewUserTableBinding, 
                                    TraceWriter log)
    {
        // In this example the queue item is a JSON string representing an order that contains the name, 
        // address and mobile number of the new customer.
        dynamic order = JsonConvert.DeserializeObject(myNewUserQueueItem);
    
        await myNewUserTableBinding.AddAsync(
            new Person() { 
                PartitionKey = "Test", 
                RowKey = Guid.NewGuid().ToString(), 
                Name = order.name,
                Address = order.address,
                MobileNumber = order.mobileNumber }
            );
    }
    
    public class Person
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Name { get; set; }
        public string Address { get; set; }
        public string MobileNumber { get; set; }
    }

如需的程式碼範例與有關支援的 Azure 儲存體類型的詳細資訊，請參閱[Azure 函數引動程序和 Azure 儲存體繫結](functions-bindings-storage.md)。


若要使用更進階的繫結功能 Azure 入口網站中，按一下 [您函數**整合**] 索引標籤上的**進階的編輯器**選項]。 進階的編輯器，可讓您編輯*function.json*直接在入口網站。

## <a name="dynamic-parameter-binding"></a>動態參數繫結 

而不是靜態設定輸出繫結屬性的設定，您可以設定的設定，以動態繫結到屬於觸發程序的輸入繫結的資料。 新的位置，請考慮情況訂單的處理使用 Azure 儲存體佇列。 每個新的佇列中項目是 JSON 字串，至少包含下列屬性︰

    {
      name : "Customer Name",
      address : "Customer's Address".
      mobileNumber : "Customer's mobile number."
    }

您可能會想要傳送客戶使用 Twilio 帳戶為更新順序收到簡訊形式的文字訊息。  您可以設定`body`和`to`欄位您 Twilio 輸出繫結至動態繫結到`name`和`mobileNumber`的組件的輸入如下所示。

    {
      "name": "myNewOrderItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "queue-newOrders",
      "connection": "orders_STORAGE"
    },
    {
      "type": "twilioSms",
      "name": "message",
      "accountSid": "TwilioAccountSid",
      "authToken": "TwilioAuthToken",
      "to": "{mobileNumber}",
      "from": "+XXXYYYZZZZ",
      "body": "Thank you {name}, your order was received",
      "direction": "out"
    },
 
現在您函數的程式碼僅有初始化輸出參數，如下所示。 在執行期間的輸出屬性會繫結到所要的輸入資料。

C#

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the message variable.
    message = new SMSMessage();

    // When using dynamic parameter binding no need to set this in code.
    // message.body = msg;
    // message.to = myNewOrderItem.mobileNumber

Node.js

    context.bindings.message = {
        // When using dynamic parameter binding no need to set this in code.
        //body : msg,
        //to : myNewOrderItem.mobileNumber
    };




## <a name="random-guids"></a>隨機 Guid

Azure 函數提供產生隨機 Guid 與您的繫結的語法。 下列繫結語法會將輸出寫入新 BLOB Azure 儲存體容器中的唯一名稱︰ 

    {
      "type": "blob",
      "name": "blobOutput",
      "direction": "out",
      "path": "my-output-container/{rand-guid}"
    }



## <a name="returning-a-single-output"></a>傳回單一輸出

您可以在 [函數程式碼會傳回單一輸出位置的情況下，使用名為輸出繫結`$return`來保留在程式碼中的更多自然函數簽章。 這只可支援傳回的值 （C# Node.js、 F #） 的語言。 繫結會類似下列 blob 輸出繫結的使用佇列中觸發程序。

    {
      "bindings": [
        {
          "type": "queueTrigger",
          "name": "input",
          "direction": "in",
          "queueName": "test-input-node"
        },
        {
          "type": "blob",
          "name": "$return",
          "direction": "out",
          "path": "test-output-node/{id}"
        }
      ]
    }


下列 C# 程式碼會傳回輸出自然更不使用`out`參數函數簽章中的。


    public static string Run(WorkItem input, TraceWriter log)
    {
        string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
        log.Info($"C# script processed queue message. Item={json}");
        return json;
    }

    // Async example
    public static Task<string> Run(WorkItem input, TraceWriter log)
    {
        string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
        log.Info($"C# script processed queue message. Item={json}");
        return json;
    }


相同的方式與 Node.js 示範下方。

    module.exports = function (context, input) {
        var json = JSON.stringify(input);
        context.log('Node.js script processed queue message', json);
        context.done(null, json);
    }

F # 提供的範例。

    let Run(input: WorkItem, log: TraceWriter) =
        let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
        log.Info(sprintf "F# script processed queue message '%s'" json)
        json

這也可使用多個輸出參數與會資訊與單一輸出`$return`。


## <a name="route-support"></a>傳送支援

依預設當您建立 HTTP 觸發程序或 WebHook，函數函數是定址與表單的路由︰

    http://<yourapp>.azurewebsites.net/api/<funcname> 

您可以自訂使用選擇性此路由`route`屬性 HTTP 觸發程序的輸入繫結。 例如，下列*function.json*檔案定義`route`HTTP 觸發程序] 屬性︰

    {
      "bindings": [
        {
          "type": "httpTrigger",
          "name": "req",
          "direction": "in",
          "methods": [ "get" ],
          "route": "products/{category:alpha}/{id:int?}"
        },
        {
          "type": "http",
          "name": "res",
          "direction": "out"
        }
      ]
    }

使用此設定，此函數現在定址與下列路徑，而不是原始路由。

    http://<yourapp>.azurewebsites.net/api/products/electronics/357

這個選項可讓 [函數程式碼，支援在 [位址] 的兩個參數`category`和`id`。 您可以使用任何[網頁 API 傳送限制式](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints)，與您的參數。 下列 C# 函數程式碼會利用這兩個參數。

    public static Task<HttpResponseMessage> Run(HttpRequestMessage request, string category, int? id, 
                                                    TraceWriter log)
    {
        if (id == null)
           return  req.CreateResponse(HttpStatusCode.OK, $"All {category} items were requested.");
        else
           return  req.CreateResponse(HttpStatusCode.OK, $"{category} item with id = {id} has been requested.");
    }

以下是使用相同的路由參數 Node.js 函數程式碼。

    module.exports = function (context, req) {

        var category = context.bindingData.category;
        var id = context.bindingData.id;
    
        if (!id) {
            context.res = {
                // status: 200, /* Defaults to 200 */
                body: "All " + category + " items were requested."
            };
        }
        else {
            context.res = {
                // status: 200, /* Defaults to 200 */
                body: category + " item with id = " + id + " was requested."
            };
        }
    
        context.done();
    } 

根據預設，所有的函數路由前置字元*的 api*。 您也可以自訂或移除前置詞使用`http.routePrefix` *host.json*檔案中的屬性。 下列範例會移除*api*路由首碼*host.json*檔案中的前置詞使用空字串。

    {
      "http": {
        "routePrefix": ""
      }
    }

如需如何更新您函數，請參閱[如何函數應用程式更新檔案](functions-reference.md#fileupdate) *host.json*檔案的詳細資訊。 

藉由新增這項設定，現在可使用下列路徑定位函數︰

    http://<yourapp>.azurewebsites.net/products/electronics/357

您可以設定*host.json*檔案中的其他屬性的資訊，請參閱[host.json 參照](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json)。





## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列資源︰

* [測試函數](functions-test-a-function.md)
* [不按比例縮放函數](functions-scale.md)
