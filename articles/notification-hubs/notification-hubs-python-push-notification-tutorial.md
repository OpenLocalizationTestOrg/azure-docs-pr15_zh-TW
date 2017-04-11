<properties 
    pageTitle="如何使用 Python 通知集線器" 
    description="瞭解如何使用 Python 後端 Azure 通知集線器。" 
    services="notification-hubs" 
    documentationCenter="" 
    authors="ysxu"
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="notification-hubs" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="python" 
    ms.devlang="php" 
    ms.topic="article" 
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>

# <a name="how-to-use-notification-hubs-from-python"></a>如何使用從 Python 通知集線器
[AZURE.INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]
        
您可以從 Java/PHP/Python/注音後端使用 MSDN 主題[通知集線器 REST Api](http://msdn.microsoft.com/library/dn223264.aspx)所述的通知中心其餘介面，來存取所有通知集線器功能。

> [AZURE.NOTE] 此範例參考實作 Python 中實作通知給，而不是正式受支援的通知中心 Python SDK。
>
> 此範例使用 Python 3.4 寫入。

在本主題我們會示範如何︰

* 建立通知集線器 Python 功能的其他用戶端。
* 傳送給使用 Python 介面通知中心 REST api 的通知。 
* 取得偵錯/教育目的 HTTP 其餘要求/回應的傾印。 

您可以依照[取得開始教學課程](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)的選項，您行動裝置平台 Python 在實作後端部分。

> [AZURE.NOTE] 樣本範圍僅限於傳送通知，它不會執行任何註冊管理。

## <a name="client-interface"></a>用戶端介面
主要的用戶端介面可以提供相同的方法所提供的[.NET 通知集線器 SDK](http://msdn.microsoft.com/library/jj933431.aspx)。 這可讓您直接翻譯所有的教學課程和目前提供的範例，在此網站上，而在網際網路上社群。

您可以找到所有[Python 其餘包裝紙範例]中，您可以使用的程式碼。

例如，若要建立用戶端︰

    isDebug = True
    hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
    
若要傳送 Windows 快顯通知︰
    
    wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello world!</text></binding></visual></toast>"""
    hub.send_windows_notification(wns_payload)
    
## <a name="implementation"></a>實作
如果您並未尚未，請依照您有實作後端的最後一節截至我們[取得開始教學課程]。

所有詳細資料，來執行完整的其餘部分包裝紙可以[MSDN](http://msdn.microsoft.com/library/dn530746.aspx)上找到。 此區段中，我們會說明 Python 實作存取通知集線器其餘結束點及傳送通知所需的主要步驟

1. 剖析的連接字串
2. 產生授權權杖
3. 傳送通知使用 HTTP REST API

### <a name="parse-the-connection-string"></a>剖析的連接字串

以下是實作用戶端，其建構函式剖析的連接字串的主要類別︰

    class NotificationHub:
        API_VERSION = "?api-version=2013-10"
        DEBUG_SEND = "&test"
    
        def __init__(self, connection_string=None, hub_name=None, debug=0):
            self.HubName = hub_name
            self.Debug = debug
    
            # Parse connection string
            parts = connection_string.split(';')
            if len(parts) != 3:
                raise Exception("Invalid ConnectionString.")
    
            for part in parts:
                if part.startswith('Endpoint'):
                    self.Endpoint = 'https' + part[11:]
                if part.startswith('SharedAccessKeyName'):
                    self.SasKeyName = part[20:]
                if part.startswith('SharedAccessKey'):
                    self.SasKeyValue = part[16:]


### <a name="create-security-token"></a>建立安全性 token
安全性權杖建立的詳細資訊，可[在這裡](http://msdn.microsoft.com/library/dn495627.aspx)。
下列方法，將會新增至**NotificationHub**類別以建立根據目前要求和從 [連線字串中擷取的認證 URI 的 token。

    @staticmethod
    def get_expiry():
        # By default returns an expiration of 5 minutes (=300 seconds) from now
        return int(round(time.time() + 300))

    @staticmethod
    def encode_base64(data):
        return base64.b64encode(data)

    def sign_string(self, to_sign):
        key = self.SasKeyValue.encode('utf-8')
        to_sign = to_sign.encode('utf-8')
        signed_hmac_sha256 = hmac.HMAC(key, to_sign, hashlib.sha256)
        digest = signed_hmac_sha256.digest()
        encoded_digest = self.encode_base64(digest)
        return encoded_digest

    def generate_sas_token(self):
        target_uri = self.Endpoint + self.HubName
        my_uri = urllib.parse.quote(target_uri, '').lower()
        expiry = str(self.get_expiry())
        to_sign = my_uri + '\n' + expiry
        signature = urllib.parse.quote(self.sign_string(to_sign))
        auth_format = 'SharedAccessSignature sig={0}&se={1}&skn={2}&sr={3}'
        sas_token = auth_format.format(signature, expiry, self.SasKeyName, my_uri)
        return sas_token

### <a name="send-a-notification-using-http-rest-api"></a>傳送通知使用 HTTP REST API
首先，讓使用來定義類別，代表通知。

    class Notification:
        def __init__(self, notification_format=None, payload=None, debug=0):
            valid_formats = ['template', 'apple', 'gcm', 'windows', 'windowsphone', "adm", "baidu"]
            if not any(x in notification_format for x in valid_formats):
                raise Exception(
                    "Invalid Notification format. " +
                    "Must be one of the following - 'template', 'apple', 'gcm', 'windows', 'windowsphone', 'adm', 'baidu'")
    
            self.format = notification_format
            self.payload = payload
    
            # array with keynames for headers
            # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
            # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry
            # in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
            self.headers = None

這個類別是原生通知本文或一組的內容若範本通知，其中包含格式 （原生的平台或範本） 和 （例如 Apple 到期屬性或 WNS 標頭） 的特定屬性的一組標題的容器。

請參閱的[通知集線器 REST Api 文件](http://msdn.microsoft.com/library/dn495827.aspx)和特定的通知平台的格式，讓所有可用的選項。

現在有此類別中，我們可以撰寫傳送通知方法**NotificationHub**類別內。

    def make_http_request(self, url, payload, headers):
        parsed_url = urllib.parse.urlparse(url)
        connection = http.client.HTTPSConnection(parsed_url.hostname, parsed_url.port)

        if self.Debug > 0:
            connection.set_debuglevel(self.Debug)
            # adding this querystring parameter gets detailed information about the PNS send notification outcome
            url += self.DEBUG_SEND
            print("--- REQUEST ---")
            print("URI: " + url)
            print("Headers: " + json.dumps(headers, sort_keys=True, indent=4, separators=(' ', ': ')))
            print("--- END REQUEST ---\n")

        connection.request('POST', url, payload, headers)
        response = connection.getresponse()

        if self.Debug > 0:
            # print out detailed response information for debugging purpose
            print("\n\n--- RESPONSE ---")
            print(str(response.status) + " " + response.reason)
            print(response.msg)
            print(response.read())
            print("--- END RESPONSE ---")

        elif response.status != 201:
            # Successful outcome of send message is HTTP 201 - Created
            raise Exception(
                "Error sending notification. Received HTTP code " + str(response.status) + " " + response.reason)

        connection.close()

    def send_notification(self, notification, tag_or_tag_expression=None):
        url = self.Endpoint + self.HubName + '/messages' + self.API_VERSION

        json_platforms = ['template', 'apple', 'gcm', 'adm', 'baidu']

        if any(x in notification.format for x in json_platforms):
            content_type = "application/json"
            payload_to_send = json.dumps(notification.payload)
        else:
            content_type = "application/xml"
            payload_to_send = notification.payload

        headers = {
            'Content-type': content_type,
            'Authorization': self.generate_sas_token(),
            'ServiceBusNotification-Format': notification.format
        }

        if isinstance(tag_or_tag_expression, set):
            tag_list = ' || '.join(tag_or_tag_expression)
        else:
            tag_list = tag_or_tag_expression

        # add the tags/tag expressions to the headers collection
        if tag_list != "":
            headers.update({'ServiceBusNotification-Tags': tag_list})

        # add any custom headers to the headers collection that the user may have added
        if notification.headers is not None:
            headers.update(notification.headers)

        self.make_http_request(url, payload_to_send, headers)

    def send_apple_notification(self, payload, tags=""):
        nh = Notification("apple", payload)
        self.send_notification(nh, tags)

    def send_gcm_notification(self, payload, tags=""):
        nh = Notification("gcm", payload)
        self.send_notification(nh, tags)

    def send_adm_notification(self, payload, tags=""):
        nh = Notification("adm", payload)
        self.send_notification(nh, tags)

    def send_baidu_notification(self, payload, tags=""):
        nh = Notification("baidu", payload)
        self.send_notification(nh, tags)

    def send_mpns_notification(self, payload, tags=""):
        nh = Notification("windowsphone", payload)

        if "<wp:Toast>" in payload:
            nh.headers = {'X-WindowsPhone-Target': 'toast', 'X-NotificationClass': '2'}
        elif "<wp:Tile>" in payload:
            nh.headers = {'X-WindowsPhone-Target': 'tile', 'X-NotificationClass': '1'}

        self.send_notification(nh, tags)

    def send_windows_notification(self, payload, tags=""):
        nh = Notification("windows", payload)

        if "<toast>" in payload:
            nh.headers = {'X-WNS-Type': 'wns/toast'}
        elif "<tile>" in payload:
            nh.headers = {'X-WNS-Type': 'wns/tile'}
        elif "<badge>" in payload:
            nh.headers = {'X-WNS-Type': 'wns/badge'}

        self.send_notification(nh, tags)

    def send_template_notification(self, properties, tags=""):
        nh = Notification("template", properties)
        self.send_notification(nh, tags)

上述方法 HTTP 文章邀請傳送給您的通知中心，以正確的本文和標題傳送通知給 /messages 端點。

### <a name="using-debug-property-to-enable-detailed-logging"></a>若要啟用詳細的記錄使用偵錯屬性
啟用偵錯屬性初始化通知中心時將撰寫出 HTTP 要求的相關詳細的記錄資訊及回應傾印，以及詳細的通知訊息傳送結果。 我們最近新增的這個屬性稱為[通知集線器 TestSend 屬性](http://msdn.microsoft.com/library/microsoft.servicebus.notifications.notificationhubclient.enabletestsend.aspx)所傳回的通知傳送結果的詳細的資訊。 若要使用的初始化使用下列步驟︰

    hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)

通知中心傳送邀請 HTTP URL 取得附加 「 測試 」 查詢的結果。 

##<a name="complete-tutorial"></a>完成本教學課程
現在，您可以傳送通知給從 Python 後端完成開始教學課程。

初始化通知集線器用戶端 （取代為 instructed[取得開始教學課程]中的連線字串和中心名稱）︰

    hub = NotificationHub("myConnectionString", "myNotificationHubName")

然後新增傳送程式碼，根據您的目標的行動裝置平台。 此範例也會加入較高等級的方法，讓例如 send_windows_notification; 在 windows 版的平台以傳送通知send_apple_notification （適用於 apple) 等。 

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows 市集和 Windows Phone 8.1 (非 Silverlight)

    wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Test</text></binding></visual></toast>"""
    hub.send_windows_notification(wns_payload)

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0 及 8.1 Silverlight

    hub.send_mpns_notification(toast)

### <a name="ios"></a>iOS

    alert_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_apple_notification(alert_payload)

### <a name="android"></a>Android
    gcm_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_gcm_notification(gcm_payload)

### <a name="kindle-fire"></a>Kindle Fire
    adm_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_adm_notification(adm_payload)

### <a name="baidu"></a>Baidu
    baidu_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_baidu_notification(baidu_payload)

執行 Python 程式碼，應該產生出現在您的目標裝置上的通知。

## <a name="examples"></a>範例︰

### <a name="enabling-debug-property"></a>啟用偵錯屬性
當您啟用偵錯旗標初始化 NotificationHub，然後您會看到詳細的 HTTP 要求與回應傾印以及 NotificationOutcome 類似下列您可以在此瞭解哪些 HTTP 標頭傳入要求和從 [通知] 中心收到哪些 HTTP 回應時︰    ![][1]

您會看到詳細的通知中心結果例如︰ 

- 當郵件順利傳送推入通知服務。 
    
        <Outcome>The Notification was successfully sent to the Push Notification System</Outcome>

- 如果有任何找到的任何推入通知的目標然後您可能會看到下列回應 （表示沒有找到可能傳遞通知，因為註冊有一些不相符的標記沒有登錄） 中

        '<NotificationOutcome xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect" xmlns:i="http://www.w3.org/2001/XMLSchema-instance"><Success>0</Success><Failure>0</Failure><Results i:nil="true"/></NotificationOutcome>'

### <a name="broadcast-toast-notification-to-windows"></a>廣播到 Windows 的快顯通知 

請注意當您傳送廣播的快顯通知給 Windows 用戶端取得送出的標題。 

    hub.send_windows_notification(wns_payload)

![][2]

### <a name="send-notification-specifying-a-tag-or-tag-expression"></a>傳送通知指定標籤 （或標記運算式）

請注意也會加入至 HTTP 要求標籤 HTTP 頁首 （在下面的範例中，我們會傳送通知給 「 運動 」 內容的登錄）

    hub.send_windows_notification(wns_payload, "sports")

![][3]

### <a name="send-notification-specifying-multiple-tags"></a>傳送通知指定多個標籤

請注意標籤 HTTP 標頭如何變更傳送的多個標籤。 
    
    tags = {'sports', 'politics'}
    hub.send_windows_notification(wns_payload, tags)

![][4]

### <a name="templated-notification"></a>樣板的通知

請注意，變更格式 HTTP 標題及內容本文傳送 HTTP 要求主體的一部分︰

**用戶端-已註冊的範本**

        var template =
                        @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(greeting_en)</text></binding></visual></toast>";
            
**伺服器端-傳送內容**
        
        template_payload = {'greeting_en': 'Hello', 'greeting_fr': 'Salut'}
        hub.send_template_notification(template_payload)

![][5]


## <a name="next-steps"></a>後續步驟
本主題中，我們會說明如何建立簡單的通知集線器 Python 其餘用戶端。 您可以在這裡︰

* 下載完整[Python 其餘包裝紙範例]，其中包含上述所有程式碼。
* 繼續學習如何通知集線器標記[中斷新聞教學課程]中的功能
* 繼續學習如何通知集線器範本功能中[當地語系化新聞教學課程]

<!-- URLs -->
[Python 其餘包裝紙範例]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-python
[開始使用教學課程]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[中斷新聞教學課程]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[當地語系化新聞教學課程]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/

<!-- Images. -->
[1]: ./media/notification-hubs-python-backend-how-to/DetailedLoggingInfo.png
[2]: ./media/notification-hubs-python-backend-how-to/BroadcastScenario.png
[3]: ./media/notification-hubs-python-backend-how-to/SendWithOneTag.png
[4]: ./media/notification-hubs-python-backend-how-to/SendWithMultipleTags.png
[5]: ./media/notification-hubs-python-backend-how-to/TemplatedNotification.png
 
