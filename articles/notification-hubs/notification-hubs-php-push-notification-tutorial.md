<properties 
    pageTitle="如何使用 PHP 通知集線器" 
    description="瞭解如何使用 PHP 後端 Azure 通知集線器。" 
    services="notification-hubs" 
    documentationCenter="" 
    authors="ysxu" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="notification-hubs" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="php" 
    ms.devlang="php" 
    ms.topic="article" 
    ms.date="06/07/2016" 
    ms.author="yuaxu"/>

# <a name="how-to-use-notification-hubs-from-php"></a>如何使用從 PHP 通知集線器
[AZURE.INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

您可以從並列文字 Java/PHP 後端使用 MSDN 主題[通知集線器 REST Api](http://msdn.microsoft.com/library/dn223264.aspx)所述的通知中心其餘介面，來存取所有通知集線器功能。

在本主題我們會示範如何︰

* 建立通知集線器 PHP; 功能的其他用戶端
* 依照 [[快速入門教學課程中](notification-hubs-ios-apple-push-notification-apns-get-started.md)的選項，您行動裝置平台 PHP 在實作後端部分。

## <a name="client-interface"></a>用戶端介面
主要的用戶端介面可以提供相同的方法所提供的[.NET 通知集線器 SDK](http://msdn.microsoft.com/library/jj933431.aspx)，這可讓您直接翻譯所有的教學課程和目前提供的範例，在這個網站，並由在網際網路上社群。

您可以找到所有[PHP 其餘包裝紙範例]中，您可以使用的程式碼。

例如，若要建立用戶端︰

    $hub = new NotificationHub("connection string", "hubname"); 

若要傳送 iOS 原生通知︰
    
    $notification = new Notification("apple", '{"aps":{"alert": "Hello!"}}');
    $hub->sendNotification($notification, null);

## <a name="implementation"></a>實作
如果您並未尚未，請依照您有實作後端的最後一節截至我們[取得開始教學課程]。
此外，如果您想要您可以使用[PHP 其餘包裝紙範例]的程式碼，並直接移至 [] 區段中[完成教學課程](#complete-tutorial)。

所有詳細資料，來執行完整的其餘部分包裝紙可以[MSDN](http://msdn.microsoft.com/library/dn530746.aspx)上找到。 此區段中，我們會說明 PHP 實作存取通知集線器其餘端點所需的主要步驟︰

1. 剖析的連接字串
2. 產生授權權杖
3. 執行 HTTP 通話

### <a name="parse-the-connection-string"></a>剖析的連接字串

以下是實作用戶端，剖析連線字串，其建構函式的主要類別︰

    class NotificationHub {
        const API_VERSION = "?api-version=2013-10";
    
        private $endpoint;
        private $hubPath;
        private $sasKeyName;
        private $sasKeyValue;
    
        function __construct($connectionString, $hubPath) {
            $this->hubPath = $hubPath;
    
            $this->parseConnectionString($connectionString);
        }
    
        private function parseConnectionString($connectionString) {
            $parts = explode(";", $connectionString);
            if (sizeof($parts) != 3) {
                throw new Exception("Error parsing connection string: " . $connectionString);
            }
    
            foreach ($parts as $part) {
                if (strpos($part, "Endpoint") === 0) {
                    $this->endpoint = "https" . substr($part, 11);
                } else if (strpos($part, "SharedAccessKeyName") === 0) {
                    $this->sasKeyName = substr($part, 20);
                } else if (strpos($part, "SharedAccessKey") === 0) {
                    $this->sasKeyValue = substr($part, 16);
                }
            }
        }
    }


### <a name="create-security-token"></a>建立安全性 token
安全性權杖建立的詳細資訊，可[在這裡](http://msdn.microsoft.com/library/dn495627.aspx)。
下列方法有要加入至**NotificationHub**類別以建立根據目前要求和從 [連線字串中擷取的認證 URI 的 token。

    private function generateSasToken($uri) {
        $targetUri = strtolower(rawurlencode(strtolower($uri)));

        $expires = time();
        $expiresInMins = 60;
        $expires = $expires + $expiresInMins * 60;
        $toSign = $targetUri . "\n" . $expires;

        $signature = rawurlencode(base64_encode(hash_hmac('sha256', $toSign, $this->sasKeyValue, TRUE)));

        $token = "SharedAccessSignature sr=" . $targetUri . "&sig="
                    . $signature . "&se=" . $expires . "&skn=" . $this->sasKeyName;

        return $token;
    }

### <a name="send-a-notification"></a>傳送通知
首先，讓我們來定義類別，代表通知。

    class Notification {
        public $format;
        public $payload;
    
        # array with keynames for headers
        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
        public $headers;
    
        function __construct($format, $payload) {
            if (!in_array($format, ["template", "apple", "windows", "gcm", "windowsphone"])) {
                throw new Exception('Invalid format: ' . $format);
            }
    
            $this->format = $format;
            $this->payload = $payload;
        }
    }

這個類別是原生通知內文或一組屬性的大小寫的範本通知及一組標題包含格式 （原生的平台或範本） 和 （例如 Apple 到期屬性或 WNS 標頭） 的特定屬性的容器。

請參閱的[通知集線器 REST Api 文件](http://msdn.microsoft.com/library/dn495827.aspx)和特定的通知平台的格式，讓所有可用的選項。

有了此課程，現在我們可以撰寫傳送通知**NotificationHub**類別內的方法。

    public function sendNotification($notification, $tagsOrTagExpression="") {
        if (is_array($tagsOrTagExpression)) {
            $tagExpression = implode(" || ", $tagsOrTagExpression);
        } else {
            $tagExpression = $tagsOrTagExpression;
        }

        # build uri
        $uri = $this->endpoint . $this->hubPath . "/messages" . NotificationHub::API_VERSION;
        $ch = curl_init($uri);

        if (in_array($notification->format, ["template", "apple", "gcm"])) {
            $contentType = "application/json";
        } else {
            $contentType = "application/xml";
        }

        $token = $this->generateSasToken($uri);

        $headers = [
            'Authorization: '.$token,
            'Content-Type: '.$contentType,
            'ServiceBusNotification-Format: '.$notification->format
        ];

        if ("" !== $tagExpression) {
            $headers[] = 'ServiceBusNotification-Tags: '.$tagExpression;
        }

        # add headers for other platforms
        if (is_array($notification->headers)) {
            $headers = array_merge($headers, $notification->headers);
        }
        
        curl_setopt_array($ch, array(
            CURLOPT_POST => TRUE,
            CURLOPT_RETURNTRANSFER => TRUE,
            CURLOPT_SSL_VERIFYPEER => FALSE,
            CURLOPT_HTTPHEADER => $headers,
            CURLOPT_POSTFIELDS => $notification->payload
        ));

        // Send the request
        $response = curl_exec($ch);

        // Check for errors
        if($response === FALSE){
            throw new Exception(curl_error($ch));
        }

        $info = curl_getinfo($ch);

        if ($info['http_code'] <> 201) {
            throw new Exception('Error sending notificaiton: '. $info['http_code'] . ' msg: ' . $response);
        }
    } 

上述方法 HTTP 文章邀請傳送給您的通知中心，以正確的本文和標題傳送通知給 /messages 端點。

##<a name="complete-tutorial"></a>完成本教學課程
現在，您可以傳送通知給從 PHP 後端完成開始教學課程。

初始化通知集線器用戶端 (substitute 為 instructed[取得開始教學課程]中的連線字串和中心名稱):

    $hub = new NotificationHub("connection string", "hubname"); 

然後新增傳送程式碼，根據您的目標的行動裝置平台。

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows 市集和 Windows Phone 8.1 (非 Silverlight)

    $toast = '<toast><visual><binding template="ToastText01"><text id="1">Hello from PHP!</text></binding></visual></toast>';
    $notification = new Notification("windows", $toast);
    $notification->headers[] = 'X-WNS-Type: wns/toast';
    $hub->sendNotification($notification, null);

### <a name="ios"></a>iOS

    $alert = '{"aps":{"alert":"Hello from PHP!"}}';
    $notification = new Notification("apple", $alert);
    $hub->sendNotification($notification, null);

### <a name="android"></a>Android
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("gcm", $message);
    $hub->sendNotification($notification, null);

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0 及 8.1 Silverlight

    $toast = '<?xml version="1.0" encoding="utf-8"?>' .
                '<wp:Notification xmlns:wp="WPNotification">' .
                   '<wp:Toast>' .
                        '<wp:Text1>Hello from PHP!</wp:Text1>' .
                   '</wp:Toast> ' .
                '</wp:Notification>';
    $notification = new Notification("windowsphone", $toast);
    $notification->headers[] = 'X-WindowsPhone-Target : toast';
    $notification->headers[] = 'X-NotificationClass : 2';
    $hub->sendNotification($notification, null);


### <a name="kindle-fire"></a>Kindle Fire
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("adm", $message);
    $hub->sendNotification($notification, null);

執行碼應該會產生現在出現在您的目標裝置上的通知。


## <a name="next-steps"></a>後續步驟
本主題中，我們會說明如何建立簡單的通知集線器 Java 其餘用戶端。 您可以在這裡︰

* 下載完整[PHP 其餘包裝紙範例]，其中包含上述所有程式碼。
* 繼續學習如何通知集線器標記 [中斷新聞教學課程] 中的功能
* 深入了解發送至個別使用者的通知，在 [通知使用者教學課程]

如需詳細資訊，請參閱[PHP 開發人員中心](/develop/php/)。

[PHP 其餘包裝紙範例]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-php
[開始使用教學課程]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
 
