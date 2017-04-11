<properties
    pageTitle="建立 Azure 應用程式服務中的 Socket.IO Node.js 聊天室應用程式"
    description="教學課程，示範使用 socket.io 裝載於 Azure node.js web 應用程式中。"
    services="app-service\web"
    documentationCenter="nodejs"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="create-a-nodejs-chat-application-with-socketio-in-azure-app-service"></a>建立 Azure 應用程式服務中的 Socket.IO Node.js 聊天室應用程式

Socket.IO 提供 node.js 伺服器和使用 WebSockets 用戶端之間的即時訊息。 也支援後援 （例如長輪詢） 使用較舊版本的瀏覽器的其他傳輸。 本教學課程會逐步引導您完成裝載 Azure web 應用程式]，為基礎的 Socket.IO 交談應用程式，並說明如何使用[Azure Redis 快取]的應用程式不按比例縮放。 如需有關 Socket.IO 的詳細資訊，請參閱<http://socket.io/>。

> [AZURE.NOTE] 這項工作的程序套用至[應用程式服務 Web 應用程式]。如需雲端服務，請參閱[建立 Node.js 交談應用程式與 Socket.IO Azure 雲端服務]。

## <a name="download-the-chat-example"></a>下載聊天室範例

為此專案中，我們將使用[Socket.IO GitHub 存放庫]的聊天室範例。 執行下列步驟，以下載的範例，並將其新增至您先前建立的專案。

1.  下載[郵遞區號 GZ 封存發行]的 Socket.IO 專案 （版本 1.3.5 用於這份文件）

1.  擷取封存與複製**範例\\聊天室**目錄至新位置。 例如，**\\節點\\聊天室**。

## <a name="modify-appjs-and-install-modules"></a>修改 app.js 並安裝模組

1.  重新命名**app.js** **index.js**檔案。 這個選項可讓 Azure 偵測這是 Node.js 應用程式。

1.  在文字編輯器中開啟**app.js**檔案。 變更線條包含`var io = require('../..')(server);`如下所示︰

        var express = require('express');
        var app = express();
        var server = require('http').createServer(app);
        // var io = require('../..')(server);
        // New:
        var io = require('socket.io')(server);
        var port = process.env.PORT || 3000;

1. 開啟**package.json**檔案，並新增下 socket.io 參照`dependencies`，如下所示︰

        "dependencies": {
          "express": "3.4.8",
          "socket.io": "1.3.5"
        }

1. 從命令列，變更為**\\節點\\聊天室**目錄和使用 npm 安裝這個應用程式所需的模組︰

        npm install

    這會將一個名為**node_modules**子安裝模組。

## <a name="create-an-azure-web-app"></a>建立 Azure Web 應用程式

請遵循這些步驟來建立 Azure web 應用程式、 啟用給發佈，然後啟用 WebSocket 支援 web 應用程式。

> [AZURE.NOTE] 若要完成此教學課程中，您需要 Azure 帳戶。 如果您沒有帳戶，您可以建立的免費的試用帳戶在幾分鐘。 如需詳細資訊，請參閱<a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Azure 免費試用版</a>。

1. 安裝 Azure 命令列介面 (Azure CLI)，並連線到您 Azure 的訂閱。 請參閱[安裝及設定 Azure CLI](../xplat-cli-install.md)。

1. 如果這是您第一次設定 Azure 中存放庫時，必須先建立登入認證。 從 Azure CLI 中，輸入下列命令︰

        azure site deployment user set [username] [password]

1. 若要變更**\\node\chat**目錄與使用下列命令以建立新的 Azure 網頁應用程式與本機給存放庫。 這個命令也會建立給遠端命名 'azure'。

        azure site create mysitename --git

    您必須以 'mysitename' 取代您的 web 應用程式的唯一名稱。

1. 使用下列命令認可本機存放庫現有的檔案︰

        git add .
        git commit -m "Initial commit"

1. 發送檔案至 Azure Web 應用程式存放庫使用下列命令︰

        git push azure master

    出現提示時，請從步驟 2 中輸入您的認證。 模組匯入的伺服器上時，您會收到的狀態訊息。 完成此程序後，應用程式會裝載於 Azure web 應用程式。

    > [AZURE.NOTE] 模組在安裝期間，您可能會發現錯誤的 「 匯入的專案...找不到 」。 可安全地予以忽略。

1. Socket.IO 使用 WebSockets，依預設位於 Azure 不會啟用。 若要啟用 web 通訊端，請使用下列命令︰

        azure site set -w

    如果出現提示，請輸入 web 應用程式的名稱。

    >[AZURE.NOTE]
    >Azure 網站將-w] 命令將只使用版本 0.7.4 工作或更高的 Azure 命令列介面。 您也可以啟用 WebSocket 支援使用[Azure 入口網站](https://portal.azure.com)。
    >
    >若要啟用 WebSockets 使用 Azure 入口網站，請按一下 Web 應用程式刀的 web 應用程式，請按一下 [**所有設定** > **應用程式設定**。 在**Web 通訊端**，按一下 [**上**]。 然後按一下 [**儲存**]。

1. 若要檢視 Azure web 應用程式，請啟動您的網頁瀏覽器，然後瀏覽到裝載的 web 應用程式中使用下列命令︰

        azure site browse

您的應用程式現在 Azure 上執行，並可以轉接使用 Socket.IO 不同的用戶端之間的訊息。

## <a name="scale-out"></a>不按比例縮放出

Socket.IO 應用程式，可以調整散佈訊息和事件之間多個應用程式執行個體使用介面__卡__。 有數種介面卡， [socket.io 意指]介面卡可以輕鬆地使用具有 Azure Redis 快取功能。

> [AZURE.NOTE] 額外的需求，延展 Socket.IO 解決方案是支援自黏工作階段。 依預設會啟用自黏工作階段，透過 Azure 要求路由 Azure Web 應用程式。 如需詳細資訊，請參閱[執行個體相關性中 Azure 網站]。

### <a name="create-a-redis-cache"></a>建立意指快取

若要建立新的快取[建立 Azure Redis 快取中的快取]中執行的步驟。

> [AZURE.NOTE] [__主機名稱__] 和 [__主索引鍵__的快取，另存新這些將需要接下來的步驟。

### <a name="add-the-redis-and-socketio-redis-modules"></a>新增意指和 socket.io 意指模組

1. 從命令列中，變更為__\\節點\\聊天室__目錄與使用下列命令。

        npm install socket.io-redis@0.1.4 redis@0.12.1 --save

    > [AZURE.NOTE] 這個命令中指定的版本是測試這份文件時所使用的版本。

1. 修改__app.js__檔，以新增下列緊接線條`var io = require('socket.io')(server);`

        var pub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});
        var sub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});

        var redis = require('socket.io-redis');
        io.adapter(redis({pubClient: pub, subClient: sub}));

    使用 [主機名稱] 和 [索引鍵意指快取取代__redishostname__和__rediskey__ 。

    這會建立發佈，訂閱意指快取先前所建立的用戶端。 用戶端介面卡然後用來設定 Socket.IO 傳遞訊息和事件的應用程式的執行個體之間使用意指快取

    > [AZURE.NOTE] 雖然__socket.io 意指__介面卡可以直接與意指通訊，請目前版本不支援 Azure Redis 快取所需的驗證。 因此，使用__redis__模組，建立初始連線，然後用戶端傳遞到__socket.io 意指__介面卡]。
    >
    > 雖然 Azure Redis 快取支援使用連接埠 6380 安全連線，請在此範例中所使用的模組不支援安全連線到 7/14/2014年。 上述的程式碼會使用預設值，不安全 6379 連接埠。

1. 儲存修改的__app.js__

### <a name="commit-changes-and-redeploy"></a>認可變更並重新部署

從命令列中__\\節點\\聊天室__目錄中，使用下列命令以認可變更並重新部署應用程式。

    git add .
    git commit -m "implementing scale out"
    git push azure master

後所做的變更已推入伺服器，則您可以使用下列命令來調整跨多個執行個體您的網站。

    azure site scale instances --instances #

位置__#__是建立的執行個體數目。

您可以從多個瀏覽器或驗證的訊息會正確傳送給所有用戶端電腦連線到您的 web 應用程式。

## <a name="troubleshooting"></a>疑難排解

### <a name="connection-limits"></a>連線限制

Azure Web 應用程式中會有多個的 Sku，判斷您的網站，您可以使用的資源。 這包含允許 WebSocket 連線的數目。 如需詳細資訊，請參閱[Web 應用程式價格的頁面]。

### <a name="messages-arent-being-sent-using-websockets"></a>不使用 WebSockets 所傳送的郵件

如果用戶端瀏覽器保留回到時間有多長，而不是使用 WebSockets 輪詢，可能會因為下列其中一項。

* **請試著限制只 WebSockets 傳輸**

    為了讓 Socket.IO WebSockets 作為訊息傳輸，伺服器與用戶端必須支援 WebSockets。 如果其中一個不存在，Socket.IO 交涉另一種傳輸，例如長投票。 傳輸 Socket.IO 所使用的預設清單是` websocket, htmlfile, xhr-polling, jsonp-polling`。 您可以強制將只使用 WebSockets **app.js**檔案，下列程式碼新增後行包含`, nicknames = {};`。

        io.configure(function() {
          io.set('transports', ['websocket']);
        });

    > [AZURE.NOTE] 請注意，較舊版本的瀏覽器不支援 WebSockets 無法連線至網站，以上的程式碼作用中時，它會限制通訊 WebSockets 只為。

* **使用 SSL**

    WebSockets 依賴一些較小使用 HTTP 標題，例如**升級**的標題。 某些中繼網路裝置，例如網頁 proxy，可能會移除這些標題。 若要避免此問題，您可以透過 SSL 建立 WebSocket 連線。

    若要設定要 Socket.IO 就能輕鬆完成這項工作`match origin protocol`。 這會指示 Socket.IO WebSockets 通訊網頁上的原始 HTTP/HTTPS 要求相同的安全性。 如果在瀏覽器造訪您的網站使用 HTTPS URL，透過 Socket.IO 後續 WebSocket 通訊會受到保護，透過 SSL。

    若要修改此範例中啟用此設定，新增下列程式碼**app.js**檔案後行包含`, nicknames = {};`。

        io.configure(function() {
          io.set('match origin protocol', true);
        });

* **驗證 web.config 設定**

    主控 Node.js 應用程式的 azure web 應用程式使用**web.config**將傳入的邀請傳送至 Node.js 應用程式。 WebSockets 正確與 Node.js 應用程式的函數， **web.config**必須包含下列項目。

        <webSocket enabled="false"/>

    這會停用 IIS WebSockets 模組，包括自己的實作 WebSockets 和例如 Socket.IO Node.js 特定 WebSocket 模組與衝突。 如果這一行不存在，或設為`true`，這可能是 WebSocket 傳輸應用程式無法運作的原因。

    一般而言，Node.js 應用程式包含**web.config**檔案]，讓 Azure 網站時，將會自動產生一個 Node.js 應用程式部署。 由於此檔案會自動產生的伺服器上，則您必須使用 FTP 或 FTP 網站的 URL，檢視此檔案。 您可以找到 [傳統入口網站中您網站的 FTP 和 FTP Url，選取您的 web 應用程式，然後按一下 [**儀表板**連結。 Url 會顯示在 [**快速規則**] 區段。

    > [AZURE.NOTE] 如果您的應用程式不提供 Azure 網站只會產生**web.config**檔案。 如果您提供的應用程式專案根目錄中**web.config**檔案時，它將會用於由 Azure Web 應用程式。

    如果項目不存在，或設定值為`true`，您應該建立**web.config** Node.js 應用程式的根目錄，並指定其值為`false`。  參照，以下是使用**app.js**為進入點的應用程式的預設**web.config** 。

        <?xml version="1.0" encoding="utf-8"?>
        <!--
             This configuration file is required if iisnode is used to run node processes behind
             IIS or IIS Express.  For more information, visit:

             https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config
        -->

        <configuration>
          <system.webServer>
            <!-- Visit http://blogs.msdn.com/b/windowsazure/archive/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites.aspx for more information on WebSocket support -->
            <webSocket enabled="false" />
            <handlers>
              <!-- Indicates that the server.js file is a node.js web app to be handled by the iisnode module -->
              <add name="iisnode" path="app.js" verb="*" modules="iisnode"/>
            </handlers>
            <rewrite>
              <rules>
                <!-- Do not interfere with requests for node-inspector debugging -->
                <rule name="NodeInspector" patternSyntax="ECMAScript" stopProcessing="true">
                  <match url="^app.js\/debug[\/]?" />
                </rule>

                <!-- First we consider whether the incoming URL matches a physical file in the /public folder -->
                <rule name="StaticContent">
                  <action type="Rewrite" url="public{REQUEST_URI}"/>
                </rule>

                <!-- All other URLs are mapped to the node.js web app entry point -->
                <rule name="DynamicContent">
                  <conditions>
                    <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="True"/>
                  </conditions>
                  <action type="Rewrite" url="app.js"/>
                </rule>
              </rules>
            </rewrite>
            <!--
              You can control how Node is hosted within IIS using the following options:
                * watchedFiles: semi-colon separated list of files that will be watched for changes to restart the server
                * node_env: will be propagated to node as NODE_ENV environment variable
                * debuggingEnabled - controls whether the built-in debugger is enabled

              See https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config for a full list of options
            -->
            <!--<iisnode watchedFiles="web.config;*.js"/>-->
          </system.webServer>
        </configuration>

    如果您的應用程式使用進入點**app.js**以外，您必須正確的項目點來取代**app.js**的所有項目。 例如，取代**server.js** **app.js** 。

>[AZURE.NOTE] 如果您想要開始使用 Azure 應用程式服務註冊 Azure 帳戶之前，請移至[嘗試應用程式服務]，可以讓您立即建立短暫入門 web 應用程式在應用程式服務。 必要; 沒有信用卡沒有承諾。

## <a name="next-steps"></a>後續步驟

在此教學課程中，您學到如何建立聊天室應用程式裝載於 Azure web 應用程式的內容。 您也可以裝載 Azure 雲端服務這個應用程式。 如何完成此步驟，請參閱[建立具有 Azure 雲端服務的 Socket.IO Node.js 交談應用程式]。

如需詳細資訊，請參閱[Node.js 開發人員中心]。

## <a name="whats-changed"></a>變更的項目

* 若要變更的指南，從網站應用程式服務請參閱︰ [Azure 應用程式服務與程式影響現有 Azure 服務]。

<!-- URL List -->

[Azure 意指快取]: /documentation/services/redis-cache/
[應用程式服務 Web 應用程式]: http://go.microsoft.com/fwlink/?LinkId=529714
[應用程式價格網頁]: http://go.microsoft.com/fwlink/?LinkId=511643
[Azure 雲端服務上建立與 Socket.IO Node.js 交談應用程式]: ../cloud-services/cloud-services-nodejs-chat-app-socketio.md
[Install and Configure the Azure CLI]: ../xplat-cli-install.md
[Azure 應用程式服務及對現有 Azure 服務的影響]: http://go.microsoft.com/fwlink/?LinkId=529714
[Node.js 開發人員中心]: /develop/nodejs/
[請嘗試應用程式服務]: http://go.microsoft.com/fwlink/?LinkId=523751
[Azure 網站中的執行個體相關性]: https://azure.microsoft.com/blog/2013/11/18/disabling-arrs-instance-affinity-in-windows-azure-web-sites/
[Azure Redis 快取中建立的快取]: ../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md

[socket.io 意指]: https://github.com/socketio/socket.io-redis
[Socket.IO GitHub 存放庫]: https://github.com/socketio/socket.io
[郵遞區號 GZ 封存發行]: https://github.com/socketio/socket.io/releases

<!-- IMG List -->

[chat-example-view]: ./media/web-sites-nodejs-chat-app-socketio/socketio-2.png
[npm-output]: ./media/web-sites-nodejs-chat-app-socketio/socketio-7.png
[completed-app]: ./media/web-sites-nodejs-chat-app-socketio/websitesocketcomplete.png
