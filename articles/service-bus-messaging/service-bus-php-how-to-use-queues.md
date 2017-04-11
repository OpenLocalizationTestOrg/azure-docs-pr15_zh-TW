<properties 
    pageTitle="如何使用 PHP 服務匯流排佇列 |Microsoft Azure" 
    description="瞭解如何使用服務匯流排佇列 Azure 中。 PHP 以撰寫程式碼範例。" 
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
    ms.date="10/04/2016" 
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-queues"></a>如何使用服務匯流排佇列

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

本指南為您示範如何使用服務匯流排佇列。 範例 PHP 撰寫，並使用[PHP Azure SDK](../php-download-sdk.md)。 涵蓋的案例包含**建立佇列**、**傳送及接收訊息**，然後**刪除佇列**。

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="create-a-php-application"></a>建立 PHP 應用程式

建立 PHP 應用程式存取 Azure Blob 服務的唯一的需求是類別中[Azure SDK PHP](../php-download-sdk.md)從您的程式碼中的參照。 您可以使用任何開發工具來建立您的應用程式或記事本。

> [AZURE.NOTE] 您的 PHP 安裝也必須[OpenSSL 副檔名](http://php.net/openssl)安裝並啟用。

本指南，您會使用服務可從呼叫內 PHP 應用程式，或執行 Azure 網頁角色、 工作者角色或網站內的程式碼中的功能。

## <a name="get-the-azure-client-libraries"></a>取得 Azure 用戶端文件庫

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>設定您的應用程式使用服務匯流排

若要使用的服務匯流排佇列 Api，請執行下列動作︰

1. 參照[require_once]自動載入器檔案[require_once]陳述式。
2. 參照您可以使用任何類別。

下列範例會示範如何包含自動載入器檔案，並參考**ServicesBuilder**類別。

> [AZURE.NOTE] 此範例 （和本文中的其他範例） 假設您有安裝編輯器透過 Azure PHP 用戶端文件庫。 如果您安裝的文件庫，以手動方式或梨子套件，您必須參考**WindowsAzure.php**自動載入器檔案。

```
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

在下面的範例，`require_once`陳述式則一律會顯示，但只執行範例所需的類別參照。

## <a name="set-up-a-service-bus-connection"></a>設定服務匯流排連線

若要產生服務匯流排用戶端，您必須先有效的連接字串此格式︰

```
Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]
```

格式] 的通常是**端點** `[yourNamespace].servicebus.windows.net`。

若要建立任何 Azure 服務用戶端中，您必須使用**ServicesBuilder**類別。 您可以︰

* 直接對傳遞的連接字串。
* 使用**CloudConfigurationManager (CCM)**核取多個的外部來源的連線字串︰
    * 根據預設，隨附的一個外部來源-環境變數支援
    * 您可以新增新的來源擴充**ConnectionStringSource**類別

此處所述的範例，如會直接傳遞的連接字串。

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="how-to-create-a-queue"></a>如何︰ 建立佇列

您可以執行服務匯流排佇列透過**ServiceBusRestProxy**類別的管理的作業。 透過**ServicesBuilder::createServiceBusService**工廠方法以適當的連線字串封裝的權杖的權限管理其公式的結構**ServiceBusRestProxy**物件。

下列範例會示範如何產生**ServiceBusRestProxy**及呼叫**ServiceBusRestProxy]-> [createQueue**建立名為佇列`myqueue`內`MySBNamespace`服務命名空間︰

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\QueueInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
    
try {
    $queueInfo = new QueueInfo("myqueue");
        
    // Create queue.
    $serviceBusRestProxy->createQueue($queueInfo);
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

> [AZURE.NOTE] 您可以使用`listQueues`上的方法`ServiceBusRestProxy`檢查是否佇列中，以指定名稱已經存在命名空間內的物件。

## <a name="how-to-send-messages-to-a-queue"></a>如何︰ 傳送郵件給佇列中

若要將訊息傳送給服務匯流排佇列中，您的應用程式呼叫**ServiceBusRestProxy]-> [sendQueueMessage**的方法。 下列程式碼示範如何傳送郵件給`myqueue`內部先前建立的佇列中`MySBNamespace`服務命名空間。

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
    $serviceBusRestProxy->sendQueueMessage("myqueue", $message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/windowsazure/hh780775
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

郵件已傳送至 （和收到從） 服務匯流排佇列是**BrokeredMessage**類別的執行個體。 **BrokeredMessage**物件都有一組 （例如**getLabel**、 **getTimeToLive**、 **setLabel**和**setTimeToLive**） 的標準方法及屬性，用於保留自訂的應用程式的特定屬性和本文任意應用程式的資料。

服務匯流排佇列中[進階版層](service-bus-premium-messaging.md)支援的郵件大小上限 256 KB[標準層](service-bus-premium-messaging.md)和 1 MB。 頁首，其中包含標準和自訂應用程式屬性，可以有 64 KB 的大小上限。 沒有限制的郵件保留在佇列數但還有首字放大的總大小由佇列中的郵件。 此上限佇列大小是 5 GB。

## <a name="how-to-receive-messages-from-a-queue"></a>如何從佇列中接收郵件

若要從佇列中接收郵件的最佳方式是使用**ServiceBusRestProxy]-> [receiveQueueMessage**的方法。 在兩個不同的模式能接收的郵件︰ **ReceiveAndDelete** （預設值） 和**PeekLock**。

使用 「 **ReceiveAndDelete**模式時，收到時是單一擷取作業-也就是說，當服務匯流排佇列中收到郵件的讀取的要求，並標示該郵件為所使用且傳回應用程式。 **ReceiveAndDelete**模式最簡單的模型，最適合的案例的應用程式可容許不處理失敗訊息。 若要了解，請考慮消費者接收邀請中發生的問題，然後當機處理之前，先情況。 因為服務匯流排會有將郵件標示為所使用，然後重新啟動應用程式，然後開始一次使用訊息，它會有未接來電之前損毀的使用狀況的訊息。

**PeekLock**模式中收到一則訊息，將會變成兩個階段作業，讓它可能不允許遺失郵件的支援應用程式。 服務匯流排收到要求時，它會找出要使用下一封郵件、 鎖定以防止其他使用者皆接收，然後傳回應用程式。 應用程式完成處理郵件 （或供未來處理可靠的方式將它儲存之後），以將收到的郵件傳遞到**ServiceBusRestProxy]-> [deleteMessage**完成接收程序的第二個階段。 當服務匯流排看到**deleteMessage**通話時，它會將時所使用的郵件標示，並將它從佇列中移除。

下列範例會顯示如何能接收郵件，以及處理使用**PeekLock**模式 （非預設模式）。

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
try {
    // Set the receive mode to PeekLock (default is ReceiveAndDelete).
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();
        
    // Receive message.
    $message = $serviceBusRestProxy->receiveQueueMessage("myqueue", $options);
    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";
        
    /*---------------------------
        Process message here.
    ----------------------------*/
        
    // Delete message. Not necessary if peek lock is not set.
    echo "Message deleted.<br />";
    $serviceBusRestProxy->deleteMessage($message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/windowsazure/hh780735
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>如何︰ 處理應用程式當機和無法讀取的郵件

服務匯流排提供的功能，可協助您順利復原錯誤，在您的應用程式或處理郵件的問題。 如果無法基於某種原因處理郵件的收件者的應用程式，就可以收到的郵件 （而非**deleteMessage**方法） 上呼叫**unlockMessage**方法。 這會導致服務匯流排，若要解除鎖定訊息佇列中的，使其可接收一次，相同的使用應用程式或另一個使用應用程式。

也有佇列中，在鎖定訊息相關聯的逾時，如果應用程式無法處理之前訊息鎖定逾 （例如，如果應用程式當機），然後服務匯流排會自動解除鎖定訊息，使其可接收一次。

應用程式當機之後處理郵件，但發行**deleteMessage**要求之前，然後郵件會被重新傳遞至應用程式時，重新啟動。 這通常稱為**至少一次處理**;也就是說，每一封郵件處理至少一次，但在某些情況下可能會被重新傳遞相同的郵件。 如果此案例不允許重複處理，然後將其他邏輯新增至應用程式以處理重複的郵件傳遞會建議。 這通常被達成使用**getMessageId**方法的郵件中，在嘗試傳遞維持不變。

## <a name="next-steps"></a>後續步驟

現在，您學到的服務匯流排佇列基本概念，請參閱[佇列主題，以及訂閱][]，如需詳細資訊。

如需詳細資訊，請參閱[PHP 開發人員中心](/develop/php/)。

[佇列、 主題及訂閱]: service-bus-queues-topics-subscriptions.md
[require_once]: http://php.net/require_once

 
