<properties 
    pageTitle="如何使用 PHP 服務匯流排主題 |Microsoft Azure" 
    description="瞭解如何使用 PHP 中 Azure 服務匯流排主題。" 
    services="service-bus" 
    documentationCenter="php" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="PHP" 
    ms.topic="article" 
    ms.date="10/14/2016" 
    ms.author="sethm"/>


# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>如何使用服務匯流排主題和訂閱

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

本文將示範如何使用服務匯流排主題和訂閱。 範例 PHP 撰寫，並使用[PHP Azure SDK](../php-download-sdk.md)。 涵蓋的案例包括**建立主題和訂閱**、**建立訂閱篩選**、**傳送主題的郵件**、**接收來自訂閱**，與**刪除主題和訂閱**。

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="create-a-php-application"></a>建立 PHP 應用程式

建立 PHP 應用程式存取 Azure Blob 服務的唯一的需求是參照[PHP Azure SDK](../php-download-sdk.md)從程式碼中的類別。 您可以使用任何開發工具來建立您的應用程式或記事本。

> [AZURE.NOTE] 您的 PHP 安裝也必須[OpenSSL 副檔名](http://php.net/openssl)安裝並啟用。

本文將說明如何使用可在 PHP 應用程式，或執行 Azure 網頁角色、 工作者角色或網站內的程式碼的服務功能。

## <a name="get-the-azure-client-libraries"></a>取得 Azure 用戶端文件庫

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>設定您的應用程式使用服務匯流排

若要使用的服務匯流排 Api:

1. 參照[require_once]自動載入器檔案[require-once]陳述式。
2. 參照您可以使用任何類別。

下列範例會示範如何包含自動載入器檔案，並參考**ServiceBusService**類別。

> [AZURE.NOTE] 此範例 （和本文中的其他範例） 假設您有安裝編輯器透過 Azure PHP 用戶端文件庫。 如果您安裝的文件庫，以手動方式或梨子套件，您必須參考**WindowsAzure.php**自動載入器檔案。

```
require_once 'vendor\autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

在下列範例中，`require_once`陳述式則一律會顯示，但只執行範例所需的類別參照。

## <a name="set-up-a-service-bus-connection"></a>設定服務匯流排連線

若要產生服務匯流排用戶端您必須先有效的連接字串此格式︰

```
Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]
```

位置`Endpoint`通常是格式] 的`https://[yourNamespace].servicebus.windows.net`。

若要建立任何 Azure 服務用戶端中，您必須使用**ServicesBuilder**類別。 您可以︰

* 直接對傳遞的連接字串。
* 使用**CloudConfigurationManager (CCM)**核取多個的外部來源的連線字串︰
    * 根據預設，隨附一外部來源-環境變數支援。
    * 您可以新增新的來源擴充**ConnectionStringSource**類別。

此處所述的範例，如會直接傳遞的連接字串。

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
    
$connectionString = "Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-topic"></a>建立主題

您可以執行服務匯流排主題透過**ServiceBusRestProxy**類別的管理作業。 透過**ServicesBuilder::createServiceBusService**工廠方法以適當的連線字串封裝的權杖的權限管理其公式的結構**ServiceBusRestProxy**物件。

下列範例會示範如何產生**ServiceBusRestProxy**及呼叫**ServiceBusRestProxy]-> [createTopic**建立名為主題`mytopic`內`MySBNamespace`命名空間︰

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\TopicInfo;
    
// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
    
try {       
    // Create topic.
    $topicInfo = new TopicInfo("mytopic");
    $serviceBusRestProxy->createTopic($topicInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/windowsazure/dd179357
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [AZURE.NOTE] 您可以使用`listTopics`上的方法`ServiceBusRestProxy`檢查是否的主題，以指定名稱已經存在服務命名空間內的物件。

## <a name="create-a-subscription"></a>建立的訂閱

主題訂閱也會建立與**ServiceBusRestProxy]-> [createSubscription**方法。 訂閱名稱，並且可以有限制郵件傳遞給訂閱的虛擬佇列中的設定選擇性篩選。

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>使用預設 (MatchAll) 篩選器建立訂閱

**MatchAll**是如果建立新的訂閱時指定沒有篩選條件，則會使用預設篩選器。 使用**MatchAll**篩選時，發佈至主題中的所有郵件都會都放在訂閱的虛擬佇列中。 下列範例會建立名為 「 mysubscription 」 的訂閱，並使用預設**MatchAll**篩選器。

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\SubscriptionInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
    
try {
    // Create subscription.
    $subscriptionInfo = new SubscriptionInfo("mysubscription");
    $serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/azure/dd179357
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

### <a name="create-subscriptions-with-filters"></a>建立篩選的訂閱

您也可以設定篩選可讓您可以指定哪些主題的郵件應該出現在特定主題訂閱。 最有彈性的訂閱支援類型是篩選的**SqlFilter**，來實作 SQL92 子集。 SQL 篩選運作內容的發佈至主題的郵件。 如需有關 SqlFilters 的詳細資訊，請參閱[SqlFilter.SqlExpression 屬性][sqlfilter]。

> [AZURE.NOTE] 每個訂閱的規則處理內送郵件大小，將其結果郵件新增至訂閱。 此外，每個新的訂閱有的主題中的所有郵件都將訂閱篩選預設**規則**物件。 若要接收郵件符合您的篩選，您必須移除預設的規則。 您可以移除預設規則使用`ServiceBusRestProxy->deleteRule`方法。

下列範例會建立名為**HighMessages**只選取 [自訂**MessageNumber**屬性大於 3 （請參閱[將郵件傳送給主題](#send-messages-to-a-topic)資訊新增至郵件的自訂屬性） 的郵件**SqlFilter**與訂閱︰

```
$subscriptionInfo = new SubscriptionInfo("HighMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

$ruleInfo = new RuleInfo("HighMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber > 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);
```

請注意此程式碼，需要額外的命名空間的使用︰ `WindowsAzure\ServiceBus\Models\SubscriptionInfo`。

同樣地，下列範例會建立名為**LowMessages**只會選取郵件的**MessageNumber**屬性小於或等於 3 **SqlFilter**與訂閱︰

```
$subscriptionInfo = new SubscriptionInfo("LowMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

$ruleInfo = new RuleInfo("LowMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber <= 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);
```

現在，當訊息會傳送至`mytopic`主題中，它會永遠傳送至接收器訂閱`mysubscription`訂閱]，然後選擇性地傳送至接收器訂閱`HighMessages`和`LowMessages`訂閱 （視郵件內容）。

## <a name="send-messages-to-a-topic"></a>傳送郵件給主題

若要將訊息傳送給服務匯流排主題，您的應用程式呼叫**ServiceBusRestProxy]-> [sendTopicMessage**的方法。 下列程式碼示範如何傳送郵件給`mytopic`內部先前建立的主題`MySBNamespace`服務命名空間。

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\BrokeredMessage;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
try {
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message");
    
    // Send message.
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/azure/hh780775
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

傳送給服務匯流排主題的郵件是**BrokeredMessage**類別的執行個體。 **BrokeredMessage**物件都有一組標準的內容和方法 （例如**getLabel**、 **getTimeToLive**、 **setLabel**及**setTimeToLive**），以及可以用來保留自訂的應用程式的特定屬性的內容。 下列範例會示範如何傳送 5 測試郵件`mytopic`先前建立的主題。 [ **SetProperty** ] 方法用來新增自訂的屬性 (`MessageNumber`) 至每一封郵件。 請注意，`MessageNumber`屬性值不盡相同，在每一封郵件 （您可以使用此值來判斷哪些訂閱接收，[建立訂閱](#create-a-subscription)一節中所示）︰

```
for($i = 0; $i < 5; $i++){
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message ".$i);
            
    // Set custom property.
    $message->setProperty("MessageNumber", $i);
            
    // Send message.
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
```

服務匯流排主題支援[進階版層](service-bus-premium-messaging.md)中的郵件大小上限 256 KB[標準層](service-bus-premium-messaging.md)和 1 MB。 頁首，其中包含標準和自訂應用程式屬性，可以有 64 KB 的大小上限。 沒有限制的主題中的郵件數但還有首字放大的總大小由主題的郵件。 此主題大小的上限為 5 GB。 如需有關配額的詳細資訊，請參閱[服務匯流排配額][]。

## <a name="receive-messages-from-a-subscription"></a>從訂閱接收郵件

從訂閱接收郵件的最佳方式就是使用**ServiceBusRestProxy]-> [receiveSubscriptionMessage**的方法。 收到的郵件可使用兩種不同的模式︰ **ReceiveAndDelete** （預設值） 和**PeekLock**。

使用 「 **ReceiveAndDelete**模式時，收到時是單一擷取的作業。也就是說，當服務匯流排訂閱中收到郵件的讀取的要求，並標示該郵件時所使用且傳回應用程式。 **ReceiveAndDelete**模式最簡單的模型，最適合的案例的應用程式可容許不處理失敗訊息。 若要了解，請考慮消費者接收邀請中發生的問題，然後當機處理之前，先情況。 因為服務匯流排會有將郵件標示為所使用，然後重新啟動應用程式，然後開始一次使用訊息，它會有未接來電之前損毀的使用狀況的訊息。

**PeekLock**模式中收到一則訊息，將會變成兩個階段作業，讓它可能不允許遺失郵件的支援應用程式。 服務匯流排收到要求時，它會找出要使用下一封郵件、 鎖定，防止其他消費者接收，然後傳回應用程式。 應用程式完成處理郵件 （或供未來處理可靠的方式將它儲存之後），以將收到的郵件傳遞到**ServiceBusRestProxy]-> [deleteMessage**完成接收程序的第二個階段。 當服務匯流排看到**deleteMessage**通話時，它會將時所使用的郵件標示，並將它從佇列中移除。

下列範例會示範如何接收並處理使用**PeekLock**模式 （非預設模式） 的訊息。 

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
try {
    // Set receive mode to PeekLock (default is ReceiveAndDelete)
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();
    
    // Get message.
    $message = $serviceBusRestProxy->receiveSubscriptionMessage("mytopic", "mysubscription", $options);

    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";
        
    /*---------------------------
        Process message here.
    ----------------------------*/
        
    // Delete message. Not necessary if peek lock is not set.
    echo "Deleting message...<br />";
    $serviceBusRestProxy->deleteMessage($message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/hh780735
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>如何︰ 處理應用程式當機和無法讀取的郵件

服務匯流排提供的功能，可協助您順利復原錯誤，在您的應用程式或處理郵件的問題。 如果無法基於某種原因處理郵件的收件者的應用程式，就可以收到的郵件 （而非**deleteMessage**方法） 上呼叫**unlockMessage**方法。 這會導致服務匯流排，若要解除鎖定訊息佇列中的，使其可接收一次，相同的使用應用程式或另一個使用應用程式。

也有佇列中，在鎖定訊息相關聯的逾時，如果應用程式無法處理之前訊息鎖定逾 （例如，如果應用程式當機），然後服務匯流排會自動解除鎖定訊息，使其可接收一次。

應用程式當機之後處理郵件，但發行**deleteMessage**要求之前，然後郵件會被重新傳遞至應用程式時，重新啟動。 這通常稱為**至少一次處理**;也就是說，每一封郵件處理至少一次，但在某些情況下可能會被重新傳遞相同的郵件。 如果此案例不允許重複處理，應用程式開發人員應處理重複的郵件傳遞的應用程式新增額外的邏輯。 這通常被達成使用**getMessageId**方法的郵件中，在嘗試傳遞維持不變。

## <a name="delete-topics-and-subscriptions"></a>刪除主題及訂閱

若要刪除的主題或訂閱，請使用**ServiceBusRestProxy]-> [deleteTopic**或**ServiceBusRestProxy]-> [deleteSubscripton**方法，分別。 請注意，刪除的主題也會刪除任何訂閱註冊的主題。

下列範例會示範如何刪除命名主題`mytopic`及其註冊的訂閱。

```
require_once 'vendor/autoload.php';

use WindowsAzure\ServiceBus\ServiceBusService;
use WindowsAzure\ServiceBus\ServiceBusSettings;
use WindowsAzure\Common\ServiceException;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
    
try {       
    // Delete topic.
    $serviceBusRestProxy->deleteTopic("mytopic");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/azure/dd179357
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

藉由使用**deleteSubscription**方法，您可以個別刪除訂閱︰

```
$serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");
```

## <a name="next-steps"></a>後續步驟

現在，您學到的服務匯流排佇列基本概念，請參閱[佇列主題，以及訂閱][]，如需詳細資訊。

[佇列、 主題及訂閱]: service-bus-queues-topics-subscriptions.md
[sqlfilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[require-once]: http://php.net/require_once
[服務匯流排配額]: service-bus-quotas.md
