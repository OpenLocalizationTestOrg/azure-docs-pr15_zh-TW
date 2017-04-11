<properties
    pageTitle="如何使用從 Node.js 佇列中的儲存 |Microsoft Azure"
    description="瞭解如何使用 Azure 佇列服務來建立及刪除佇列，並插入取得，與刪除的郵件。 撰寫 Node.js 的範例。"
    services="storage"
    documentationCenter="nodejs"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robinsh"/>


# <a name="how-to-use-queue-storage-from-nodejs"></a>如何使用從 Node.js 佇列中的儲存空間

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>概觀

本指南為您示範如何執行常見的案例使用 Microsoft Azure 佇列服務。 使用 Node.js API 寫入範例。 涵蓋的案例包含**插入**、**查看**、**快速**，及**刪除**佇列中的郵件，以及**建立及刪除佇列**。

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>建立 Node.js 應用程式

建立空白的 Node.js 應用程式。 如需建立 Node.js 應用程式的指示，請參閱[建立 Node.js web app Azure 應用程式服務中]，[建立並部署至 Azure 雲端服務的 Node.js 應用程式]使用 Windows PowerShell 或[建立並部署 Azure 使用 Web 矩陣 Node.js web 應用程式]。

## <a name="configure-your-application-to-access-storage"></a>設定您的應用程式，以存取儲存空間

若要使用 Azure 儲存空間，您需要 Node.js，其中包含一組方便時觀看文件庫與儲存其餘服務 Azure 儲存體 SDK。

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>若要取得套件使用節點套件管理員 (NPM)

1.  使用命令列介面**PowerShell** (Windows)，例如**終端機**(Mac，) 或**被**(Unix) 中，瀏覽至您用來建立範例應用程式的資料夾。

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

使用 [記事本] 或另一個文字編輯器，將以下頂端**server.js**檔案的應用程式，您想要使用儲存的位置︰

    var azure = require('azure-storage');

## <a name="setup-an-azure-storage-connection"></a>設定連線至 Azure 儲存體

Azure 模組會讀取環境變數 AZURE\_儲存\_帳戶和 AZURE\_儲存\_存取\_鍵或 AZURE\_儲存\_連線\_字串連線至 Azure 儲存體帳戶所需的資訊。 如果沒有設定這些環境變數，您必須在呼叫**createQueueService**時，指定帳戶資訊。

如需設定環境變數 Azure 網站[Azure 入口網站](https://portal.azure.com)中的範例，請參閱[使用 Azure 資料表服務 Node.js web 應用程式]。

## <a name="how-to-create-a-queue"></a>如何︰ 建立佇列

下列程式碼會建立**QueueService**物件，這可讓您使用佇列。

    var queueSvc = azure.createQueueService();

使用**createQueueIfNotExists**方法，它會傳回指定佇列中，如果已經存在，或建立新的佇列以指定的名稱，如果不存在。

    queueSvc.createQueueIfNotExists('myqueue', function(error, result, response){
      if(!error){
        // Queue created or exists
      }
    });

如果建立佇列中，`result.created`為 true。 如果佇列中存在，`result.created`為 false。

### <a name="filters"></a>篩選

選用的篩選作業可以套用到使用**QueueService**執行的作業。 篩選作業，可以包含記錄、 自動重試等。篩選是物件的實作具有簽章的方法︰

    function handle (requestOptions, next)

執行其前置處理要求選項之後, 需要呼叫 [下一步] 傳遞使用下列的簽章的回撥方法︰

    function (returnObject, finalCallback, next)

此回撥，和 [後中處理 returnObject （回應要求從伺服器），回撥必須 [下一步呼叫若存在繼續處理其他篩選] 或 [即可叫用 finalCallback 否則就服務叫用。

實作重試邏輯的兩個篩選所包含的 Node.js、 **ExponentialRetryPolicyFilter**及**LinearRetryPolicyFilter**Azure SDK 的。 下列範例會建立使用**ExponentialRetryPolicyFilter**的**QueueService**物件︰

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var queueSvc = azure.createQueueService().withFilter(retryOperations);

## <a name="how-to-insert-a-message-into-a-queue"></a>如何︰ 插入佇列中的郵件

若要插入佇列中的郵件，請使用**createMessage**方法建立新郵件，並將其新增至佇列中。

    queueSvc.createMessage('myqueue', "Hello world!", function(error, result, response){
      if(!error){
        // Message inserted
      }
    });

## <a name="how-to-peek-at-the-next-message"></a>如何︰ 查看下一封郵件

您可以查看前面佇列訊息，而不移除呼叫**peekMessages**方法佇列。 根據預設， **peekMessages**查看一封郵件。

    queueSvc.peekMessages('myqueue', function(error, result, response){
      if(!error){
        // Message text is in messages[0].messageText
      }
    });

`result`包含的訊息。

> [AZURE.NOTE] 有佇列中的任何訊息會傳回錯誤時，請使用**peekMessages** ，但是沒有郵件將會不傳回。

## <a name="how-to-dequeue-the-next-message"></a>如何︰ 取消佇列下一封郵件

處理郵件是兩階段程序︰

1. 取消佇列郵件。

2. 刪除的郵件。

若要取消佇列一則訊息，使用**getMessages**。 如此可讓郵件隱藏在佇列中，讓其他用戶端可以處理它們。 一旦您的應用程式已處理郵件，呼叫**deleteMessage**將它從佇列中刪除。 下列範例會取得的訊息，然後將其刪除︰

    queueSvc.getMessages('myqueue', function(error, result, response){
      if(!error){
        // Message text is in messages[0].messageText
        var message = result[0];
        queueSvc.deleteMessage('myqueue', message.messageId, message.popReceipt, function(error, response){
          if(!error){
            //message deleted
          }
        });
      }
    });

> [AZURE.NOTE] 根據預設，只是隱藏訊息 30 秒的時間之後, 會看到其他用戶端。 您可以指定不同的值，使用`options.visibilityTimeout`與**getMessages**。

> [AZURE.NOTE]
> 有佇列中的任何訊息會傳回錯誤時，請使用**getMessages** ，但是沒有郵件將會不傳回。

## <a name="how-to-change-the-contents-of-a-queued-message"></a>如何︰ 變更佇列訊息的內容

您可以變更郵件中的位置使用**updateMessage**佇列中的內容。 下列範例會更新訊息的文字︰

    queueSvc.getMessages('myqueue', function(error, result, response){
      if(!error){
        // Got the message
        var message = result[0];
        queueSvc.updateMessage('myqueue', message.messageId, message.popReceipt, 10, {messageText: 'new text'}, function(error, result, response){
          if(!error){
            // Message updated successfully
          }
        });
      }
    });

## <a name="how-to-additional-options-for-dequeuing-messages"></a>如何︰ 取消佇列的其他選項的郵件

有兩種方法您可以自訂郵件擷取佇列中︰

* `options.numOfMessages`-擷取批次的郵件 （最多 32。)
* `options.visibilityTimeout`-設定拉長或縮短隱形術逾時。

下列範例會使用**getMessages**方法一通電話取得 15 的郵件。 然後處理每個訊息使用的循環播放。 它也會以 5 分鐘，這個方法所傳回的所有郵件的設定隱形術逾時。

    queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, result, response){
      if(!error){
        // Messages retreived
        for(var index in result){
          // text is available in result[index].messageText
          var message = result[index];
          queueSvc.deleteMessage(queueName, message.messageId, message.popReceipt, function(error, response){
            if(!error){
              // Message deleted
            }
          });
        }
      }
    });

## <a name="how-to-get-the-queue-length"></a>如何︰ 取得佇列長度

**GetQueueMetadata**傳回佇列中，包括郵件佇列中等待大約數目的中繼資料。

    queueSvc.getQueueMetadata('myqueue', function(error, result, response){
      if(!error){
        // Queue length is available in result.approximateMessageCount
      }
    });

## <a name="how-to-list-queues"></a>如何︰ 清單佇列

若要擷取的佇列清單，請使用**listQueuesSegmented**。 若要擷取做為篩選依據特定的前置字元的清單，請使用**listQueuesSegmentedWithPrefix**。

    queueSvc.listQueuesSegmented(null, function(error, result, response){
      if(!error){
        // result.entries contains the list of queues
      }
    });

如果無法傳回所有佇列， `result.continuationToken` **listQueuesSegmented**的第一個參數或第二個參數的**listQueuesSegmentedWithPrefix**可以用來擷取其他結果。

## <a name="how-to-delete-a-queue"></a>如何︰ 刪除佇列

若要刪除佇列中所包含的所有郵件，呼叫**deleteQueue**方法佇列中物件上。

    queueSvc.deleteQueue(queueName, function(error, response){
      if(!error){
        // Queue has been deleted
      }
    });

若要清除佇列中的所有郵件，而不加以刪除，請使用**clearMessages**。

## <a name="how-to-work-with-shared-access-signatures"></a>如何︰ 使用 [共用的 Access 簽章

共用 Access 簽章 (SA) 是安全的方法，但不供您儲存體帳戶名稱或鍵提供細微存取佇列。 SA 通常用於提供您佇列，例如允許傳送訊息的行動應用程式的有限存取權。

信任的應用程式，例如雲端服務所使用的**QueueService**， **generateSharedAccessSignature** SA，並將其提供給受信任或完全信任應用程式。 例如，在行動應用程式。 SA 會產生使用原則，將說明在 SA 是有效的開始和結束日期，以及 SA 持有者授與存取層級。

下列範例會產生新的共用的存取原則，可讓 SA 持有者，若要將郵件新增至佇列中，並到期 100 分鐘後就會建立的時間。

    var startDate = new Date();
    var expiryDate = new Date(startDate);
    expiryDate.setMinutes(startDate.getMinutes() + 100);
    startDate.setMinutes(startDate.getMinutes() - 100);

    var sharedAccessPolicy = {
      AccessPolicy: {
        Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
        Start: startDate,
        Expiry: expiryDate
      }
    };

    var queueSAS = queueSvc.generateSharedAccessSignature('myqueue', sharedAccessPolicy);
    var host = queueSvc.host;

請注意，主機必須提供資訊也，因為它是在 SA 持有者嘗試存取佇列中時所需。

然後，用戶端應用程式使用**QueueServiceWithSAS**執行作業佇列中的 SA。 下列範例會連線到佇列中，並建立一封郵件。

    var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
    sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
      if(!error){
        //message added
      }
    });

由於 SA 產生透過新增存取時，如果您已嘗試讀取、 更新或刪除的郵件，則會傳回錯誤。

### <a name="access-control-lists"></a>存取控制清單

您也可以使用存取控制清單 (ACL) 若要設定 SA 存取原則。 這是很有用，如果您想要允許多個用戶端存取佇列中，但每個用戶端提供不同的存取原則。

ACL 實作存取原則的陣列，使用每個原則相關聯的識別碼。 下列範例會定義兩個原則。一個 「 user1 」 和 「 user2 」:

    var sharedAccessPolicy = {
      user1: {
        Permissions: azure.QueueUtilities.SharedAccessPermissions.PROCESS,
        Start: startDate,
        Expiry: expiryDate
      },
      user2: {
        Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
        Start: startDate,
        Expiry: expiryDate
      }
    };

下列範例會取得目前 ACL **myqueue**，，然後將使用**setQueueAcl**的新原則。 這種方法可讓︰

    var extend = require('extend');
    queueSvc.getQueueAcl('myqueue', function(error, result, response) {
      if(!error){
        var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
        queueSvc.setQueueAcl('myqueue', newSignedIdentifiers, function(error, result, response){
          if(!error){
            // ACL set
          }
        });
      }
    });

一旦 ACL 設定，然後您可以建立 SA，根據原則的識別碼。 下列範例會建立新的 「 user2' SA:

    queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });

## <a name="next-steps"></a>後續步驟

現在，您學到佇列中儲存的基本概念，請遵循這些連結，瞭解更複雜的儲存空間工作。

-   請造訪[Azure 儲存體小組部落格][]。
-   瀏覽 GitHub [Azure 儲存體 SDK 節點][]存放庫。

  [Azure 儲存體 SDK 節點]: https://github.com/Azure/azure-storage-node
  [using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
  [Azure Portal]: https://portal.azure.com
  [Azure 應用程式服務中建立 Node.js web 應用程式]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
  [Node.js Cloud Service with Storage]: ../cloud-services/storage-nodejs-use-table-storage-cloud-service-app.md
  [使用 Azure 資料表服務 Node.js web 應用程式]: ../app-service-web/storage-nodejs-use-table-storage-web-site.md


  [Queue1]: ./media/storage-nodejs-how-to-use-queues/queue1.png
  [plus-new]: ./media/storage-nodejs-how-to-use-queues/plus-new.png
  [quick-create-storage]: ./media/storage-nodejs-how-to-use-queues/quick-storage.png



  [建立並部署至 Azure 雲端服務的 Node.js 應用程式]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
  [Azure 儲存體小組部落格]: http://blogs.msdn.com/b/windowsazurestorage/
  [建立並部署 Node.js web 應用程式至 Azure 使用 Web 矩陣]: ../app-service-web/web-sites-nodejs-use-webmatrix.md
