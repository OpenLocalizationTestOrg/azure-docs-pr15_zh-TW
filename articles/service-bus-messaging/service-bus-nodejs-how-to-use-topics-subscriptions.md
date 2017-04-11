<properties 
    pageTitle="如何使用 Node.js 服務匯流排主題 |Microsoft Azure" 
    description="瞭解如何使用服務匯流排主題和訂閱 Azure 中 Node.js 應用程式。" 
    services="service-bus" 
    documentationCenter="nodejs" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="sethm"/>


# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>如何使用服務匯流排主題及訂閱

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

本指南將說明如何使用服務匯流排主題和從 Node.js 應用程式的訂閱。 涵蓋的案例包含**建立主題與訂閱****建立訂閱篩選**、 主題、**接收來自訂閱的郵件**，以及**刪除主題和訂閱**的 [**傳送郵件**。 如需主題和訂閱的詳細資訊，請參閱[下一步](#next-steps)] 區段。

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="create-a-nodejs-application"></a>建立 Node.js 應用程式

建立空白的 Node.js 應用程式。 如需建立 Node.js 應用程式的指示，請參閱[建立並部署 Node.js 應用程式至 Azure 網站]、 [Node.js 雲端服務][]WebMatrix 中使用 Windows PowerShell 或網站。

## <a name="configure-your-application-to-use-service-bus"></a>設定您的應用程式使用服務匯流排

若要使用服務匯流排，下載 Node.js Azure 套件。 此套件包含一組與服務匯流排其餘服務的文件庫。

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>若要取得套件使用節點套件管理員 (NPM)

1.  使用命令列介面**PowerShell** (Windows)，例如**終端機**(Mac，) 或**被**(Unix) 中，瀏覽至您用來建立範例應用程式的資料夾。

2.  在應該會導致下列輸出的 [命令] 視窗中，輸入**npm 安裝 azure** :

    ```
        azure@0.7.5 node_modules\azure
    ├── dateformat@1.0.2-1.2.3
    ├── xmlbuilder@0.4.2
    ├── node-uuid@1.2.0
    ├── mime@1.2.9
    ├── underscore@1.4.4
    ├── validator@1.1.1
    ├── tunnel@0.0.2
    ├── wns@0.5.3
    ├── xml2js@0.2.7 (sax@0.5.2)
    └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)
    ```

3.  您可以手動執行**1**命令，以確認**節點\_模組**建立資料夾。 資料夾中尋找**azure**套件，其中包含您要存取服務匯流排主題的文件庫。

### <a name="import-the-module"></a>匯入模組

使用 [記事本] 或另一個文字編輯器，將以下**server.js**檔案頂端的應用程式︰

```
var azure = require('azure');
```

### <a name="set-up-a-service-bus-connection"></a>設定服務匯流排連線

Azure 模組讀取環境變數 AZURE\_SERVICEBUS\_命名空間和 AZURE\_SERVICEBUS\_存取\_鍵以連線至服務匯流排所需的資訊。 如果沒有設定這些環境變數，您必須在呼叫**createServiceBusService**時，指定帳戶資訊。

如需的設定檔中設定環境變數，Azure 雲端服務的範例，請參閱[Node.js 雲端服務中，儲存空間][]。

如需設定環境變數 Azure 網站[Azure 傳統入口網站][]中的範例，請參閱[Node.js Web 應用程式，使用儲存空間][]。

## <a name="create-a-topic"></a>建立主題

**ServiceBusService**物件可讓您使用的主題。 下列程式碼會建立**ServiceBusService**物件。 將其新增靠近頂端的**server.js**的檔案，匯入 azure 模組陳述式之後︰

```
var serviceBusService = azure.createServiceBusService();
```

呼叫**createTopicIfNotExists** **ServiceBusService**物件上時，（如果存在，），就會傳回指定的主題，或將會建立新的主題，以指定的名稱。 下列程式碼會使用**createTopicIfNotExists**來建立或連線到名稱為 「 MyTopic 」 的主題︰

```
serviceBusService.createTopicIfNotExists('MyTopic',function(error){
    if(!error){
        // Topic was created or exists
        console.log('topic created or exists.');
    }
});
```

**createServiceBusService**也支援其他選項，讓您將會覆寫預設主題設定，例如郵件存留時間] 或 [最大的主題大小。 下列範例將 live 1 分鐘的時間設定為 5 GB 的最大的主題大小︰

```
var topicOptions = {
        MaxSizeInMegabytes: '5120',
        DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createTopicIfNotExists('MyTopic', topicOptions, function(error){
    if(!error){
        // topic was created or exists
    }
});
```

### <a name="filters"></a>篩選

選用的篩選作業可以套用到使用**ServiceBusService**執行的作業。 篩選作業，可以包含記錄、 自動重試等。篩選是物件的實作具有簽章的方法︰

```
function handle (requestOptions, next)
```

執行後前置處理要求選項，方法呼叫`next`傳遞使用下列的簽章的回撥︰

```
function (returnObject, finalCallback, next)
```

此回撥，和 [後中處理**returnObject** （回應要求從伺服器），先將回撥需求叫用下一步如果存在繼續處理其他篩選器，或只是要服務引動否則呼叫**finalCallback** 。

實作重試邏輯的兩個篩選所包含的 Node.js、 **ExponentialRetryPolicyFilter**及**LinearRetryPolicyFilter**Azure SDK 的。 下列範例會建立使用**ExponentialRetryPolicyFilter**的**ServiceBusService**物件︰

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);

## <a name="create-subscriptions"></a>建立訂閱

主題訂閱也會建立**ServiceBusService**物件。 訂閱名稱，並且可以有限制的郵件傳遞到訂閱的虛擬佇列中一組選擇性篩選。

> [AZURE.NOTE] 訂閱會持續，並會繼續存在，直到任一他們，或有相關聯的主題，會刪除。 如果您的應用程式中包含邏輯來建立的訂閱，它應該先檢查是否訂閱已經存在使用**getSubscription**方法。

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>使用預設 (MatchAll) 篩選器建立訂閱

**MatchAll**是如果建立新的訂閱時指定沒有篩選條件，則會使用預設篩選器。 使用**MatchAll**篩選時，發佈至主題中的所有郵件都會都放在訂閱的虛擬佇列中。 下列範例會建立名為 「 AllMessages 」 的訂閱，並使用預設**MatchAll**篩選器。

```
serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
    if(!error){
        // subscription created
    }
});
```

### <a name="create-subscriptions-with-filters"></a>建立篩選的訂閱

您也可以建立可讓您的訊息傳送給主題範圍應該會顯示特定主題訂閱中的篩選。

最有彈性的訂閱支援類型是篩選的**SqlFilter**，來實作 SQL92 子集。 SQL 篩選運作內容的發佈至主題的郵件。 如需更多詳細資料可供使用 SQL 篩選運算式檢閱[SqlFilter.SqlExpression]的[SqlFilter.SqlExpression]語法。

篩選可以新增至訂閱中，使用**createRule** **ServiceBusService** ] 物件的方法。 這個方法可讓您新增至現有訂閱的篩選器。

> [AZURE.NOTE] 因為預設篩選器會自動套用到所有的新訂閱，您必須先移除預設篩選器或**MatchAll**會覆寫任何其他您可能會指定的篩選。 您可以使用**deleteRule**物件的方法**ServiceBusService**移除預設的規則。

下列範例會建立名為訂閱`HighMessages`與**SqlFilter**只選取 [自訂**messagenumber**屬性大於 3 的訊息︰

```
serviceBusService.createSubscription('MyTopic', 'HighMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'HighMessages', 
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME, 
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber > 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic', 
            'HighMessages', 
            'HighMessageFilter', 
            ruleOptions, 
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

同樣地，下列範例會建立名為訂閱`LowMessages`與**SqlFilter**只選取**messagenumber**屬性小於或等於 3 的訊息︰

```
serviceBusService.createSubscription('MyTopic', 'LowMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'LowMessages', 
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME, 
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber <= 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic', 
            'LowMessages', 
            'LowMessageFilter', 
            ruleOptions, 
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

郵件時立即傳送到`MyTopic`，它會永遠傳遞給接收器訂閱`AllMessages`主題訂閱，然後選擇性地傳送至接收器訂閱`HighMessages`和`LowMessages`主題訂閱 （視郵件內容）。

## <a name="how-to-send-messages-to-a-topic"></a>如何傳送郵件給主題

若要將訊息傳送給服務匯流排主題，您的應用程式必須使用**sendTopicMessage** **ServiceBusService** ] 物件的方法。
傳送給服務匯流排主題的郵件是**BrokeredMessage**物件。
用來保留自訂的應用程式的特定屬性，字典和本文字串資料， **BrokeredMessage**物件都有一組標準的屬性 （例如**標籤**和**TimeToLive**）。 應用程式，可以將字串值傳遞給**sendTopicMessage**來設定郵件的本文，然後將填入任何必要的標準屬性，預設值。

下列範例會示範如何傳送五個測試 'MyTopic 」 的訊息。 請注意，每一封郵件的**messagenumber**屬性值不盡相同上的循環播放反覆運算 （這會決定哪些訂閱收到）︰

```
var message = {
    body: '',
    customProperties: {
        messagenumber: 0
    }
}

for (i = 0;i < 5;i++) {
    message.customProperties.messagenumber=i;
    message.body='This is Message #'+i;
    serviceBusService.sendTopicMessage(topic, message, function(error) {
      if (error) {
        console.log(error);
      }
    });
}
```

服務匯流排主題支援[進階版層](service-bus-premium-messaging.md)中的郵件大小上限 256 KB[標準層](service-bus-premium-messaging.md)和 1 MB。 頁首，其中包含標準和自訂應用程式屬性，可以有 64 KB 的大小上限。 沒有限制的主題中的郵件數但還有首字放大的總大小由主題的郵件。 此主題檔案大小定義在建立時，使用 5 GB 的上限。

## <a name="receive-messages-from-a-subscription"></a>從訂閱接收郵件

從訂閱**ServiceBusService**物件上使用**receiveSubscriptionMessage**方法接收訊息。 根據預設，刪除的郵件從訂閱讀取; 時不過，您可以讀取 （預覽），但不刪除其從訂閱設定為**true**的選擇性參數**isPeekLock**鎖定訊息。

閱讀和接收作業刪除郵件的預設行為是最簡單的模型，而最適合的應用程式可容許不處理失敗訊息的案例。 若要了解，請考慮消費者接收邀請中發生的問題，然後當機處理之前，先情況。 因為服務匯流排會有將郵件標示為所使用，然後重新啟動應用程式，然後開始一次使用訊息，它會有未接來電之前損毀的使用狀況的訊息。

如果**isPeekLock**參數設定為**true**，接收會變成兩個階段作業，讓它可能不允許遺失郵件的支援應用程式。 服務匯流排收到要求時，它會找出要使用下一封郵件、 鎖定，防止其他消費者接收，然後傳回應用程式。
應用程式完成處理郵件 （或供未來處理可靠的方式將它儲存之後），完成接收程序的第二個階段呼叫**deleteMessage**方法，並提供做為參數遭到刪除的訊息。 **DeleteMessage**方法會標示郵件時所使用，並將它從訂閱移除。

下列範例會示範如何能接收郵件，並使用**receiveSubscriptionMessage**處理。 範例第一次收到從 'LowMessages' 訂閱，請刪除郵件，然後從使用**isPeekLock**設為 true 的 「 HighMessages' 訂閱接收訊息。 接著，它會刪除使用**deleteMessage**的訊息︰

```
serviceBusService.receiveSubscriptionMessage('MyTopic', 'LowMessages', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
        console.log(receivedMessage);
    }
});
serviceBusService.receiveSubscriptionMessage('MyTopic', 'HighMessages', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        console.log(lockedMessage);
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
                console.log('message has been deleted.');
            }
        }
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>如何處理應用程式當機和無法讀取的郵件

服務匯流排提供的功能，可協助您順利復原錯誤，在您的應用程式或處理郵件的問題。 如果無法基於某種原因處理郵件的收件者的應用程式，就可以**ServiceBusService**物件上呼叫**unlockMessage**方法。 這會導致服務匯流排，若要解除鎖定訂閱中的訊息，使其可接收一次，相同的使用應用程式或另一個使用應用程式。

也有訂閱，請在鎖定訊息相關聯的逾時，如果應用程式無法處理之前訊息鎖定逾 （例如，如果應用程式當機），然後服務匯流排會自動解除鎖定訊息，並可接收一次。

應用程式當機之後處理郵件，但呼叫**deleteMessage**方法之前，然後郵件會被重新傳遞至應用程式時，重新啟動。 這通常稱為**至少後處理**，也就是將至少一次處理每一封郵件，但在某些情況下可能會被重新傳遞相同的郵件。 如果此案例不允許重複處理，應用程式開發人員應該將其他邏輯新增至其應用程式來處理重複的郵件傳遞。 這是通常用來達成會保持不變跨嘗試傳遞訊息的**訊息識別碼**] 屬性。

## <a name="delete-topics-and-subscriptions"></a>刪除主題及訂閱

主題和訂閱常設，而必須在 [透過[Azure 傳統入口網站][]或以程式設計方式明確刪除。
下列範例會示範如何刪除本主題`MyTopic`:

    serviceBusService.deleteTopic('MyTopic', function (error) {
        if (error) {
            console.log(error);
        }
    });

刪除主題也會刪除任何訂閱註冊的主題。 您也可以個別刪除訂閱。 下列範例會示範如何刪除命名訂閱`HighMessages`從`MyTopic`主題︰

    serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
        if(error) {
            console.log(error);
        }
    });

## <a name="next-steps"></a>後續步驟

現在，您學到服務匯流排主題的基本概念，請遵循這些連結，瞭解更多。

-   請參閱[佇列主題，以及訂閱][]。
-   [SqlFilter][]API 參考。
-   瀏覽 GitHub[節點 Azure SDK][]存放庫。

  [Azure SDK 節點]: https://github.com/Azure/azure-sdk-for-node
  [Azure 傳統入口網站]: https://manage.windowsazure.com
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [佇列、 主題及訂閱]: service-bus-queues-topics-subscriptions.md
  [SqlFilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx
  [Node.js 雲端服務]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
  [建立及部署 Node.js 應用程式至 Azure 網站]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
  [使用儲存空間的 Node.js 雲端服務]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
  [使用儲存空間的 Node.js Web 應用程式]: ../cloud-services/storage-nodejs-use-table-storage-cloud-service-app.md
 
