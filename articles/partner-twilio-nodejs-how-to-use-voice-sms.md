<properties 
    pageTitle="使用 Twilio 語音、 VoIP，和 Azure 中的 SMS 訊息" 
    description="瞭解如何撥打電話和 Azure 上的 [傳送簡訊，Twilio API 服務。 Node.js 以撰寫程式碼範例。" 
    services="" 
    documentationCenter="nodejs" 
    authors="devinrader" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="11/25/2014" 
    ms.author="wpickett"/>


# <a name="using-twilio-for-voice-voip-and-sms-messaging-in-azure"></a>使用 Twilio 語音、 VoIP，和 Azure 中的 SMS 訊息

本指南將示範如何建立與 Twilio 和 Azure 上的 node.js 通訊的相關應用程式。

<a id="whatis"/>
## <a name="what-is-twilio"></a>什麼是 Twilio？

Twilio 是方便適用於開發人員進行和接聽電話，請傳送和接收文字訊息，然後內嵌至瀏覽器和原生行動應用程式的 VoIP 通話 API 平台。  現在就讓我們簡要超過此不要深入的運作方式。

### <a name="receiving-calls-and-text-messages"></a>接收來電和文字訊息

Twilio 允許開發人員[購買可程式化的電話號碼][purchase_phone]可以使用傳送和接收來電和文字訊息。  當 Twilio 數字會收到輸入的呼叫或文字時，Twilio 會傳送您的 web 應用程式 HTTP 文章或取得邀請中，要求您如何處理通話或文字上的指示。  您的伺服器會回應[TwiML]Twilio 的 HTTP 邀請[twiml]，一組簡單的 XML 標記包含指示通話或文字的處理方式。  我們會看到 TwiML 範例中就森積差。

### <a name="making-calls-and-sending-text-messages"></a>撥打電話及傳送簡訊

藉由 HTTP 要求 Twilio web 服務 API，開發人員可以傳送簡訊或啟動輸出的行動電話。  輸出通話，開發人員也必須指定傳回 TwiML 指示如何處理輸出電話接通之後的 URL。

### <a name="embedding-voip-capabilities-in-ui-code-javascript-ios-or-android"></a>內嵌 UI 程式碼 （JavaScript、，在 iOS 或 Android） 中的 [VoIP 功能

Twilio 提供用戶端 SDK 的可以將任何桌面的網頁瀏覽器、 iOS 應用程式或 Android 應用程式變成 VoIP 電話。  本文中，我們將焦點放在如何使用 VoIP 呼叫瀏覽器中。  除了 Twilio JavaScript SDK 在瀏覽器中執行，必須使用伺服器端的應用程式 （我們 node.js 應用程式） JavaScript 用戶端發出的 「 功能權杖 」。  您可以閱讀更多關於使用 VoIP node.js [Twilio 開發部落格上][voipnode]。

<a id="signup"/>
## <a name="sign-up-for-twilio-microsoft-discount"></a>註冊 Twilio （Microsoft 折扣）

使用之前 Twilio 服務，您必須[註冊帳戶]的第一個[signup]。  Microsoft Azure 客戶接收特殊的折扣-[請務必登入以下][signup]！

<a id="azuresite"/>
## <a name="create-and-deploy-a-nodejs-azure-website"></a>建立並部署 node.js Azure 網站

接下來，您必須建立 node.js 網站 Azure 上執行。  [執行此動作的正式文件位於以下][azure_new_site]。  在高的層級，您將會執行下列動作︰

* 註冊 Azure 帳戶，如果您沒有
* 若要建立新的網站使用 Azure 管理主控台
* 新增來源控制項支援 （我們會假設您使用給）
* 建立檔案`server.js`簡單 node.js web 應用程式
* 部署至 Azure 這個簡單的應用程式

<a id="twiliomodule"/>
## <a name="configure-the-twilio-module"></a>設定 Twilio 模組

接下來，我們就會開始撰寫簡單 node.js 應用程式，使 Twilio API 的使用。  開始之前，我們需要設定我們 Twilio 帳戶認證。  

### <a name="configuring-twilio-credentials-in-system-environment-variables"></a>在 [系統環境變數中設定 Twilio 認證

為了讓 Twilio 後端針對已驗證的要求，我們需要我們帳戶 SID 和驗證權杖，為使用者名稱和密碼設定我們 Twilio 帳戶的函數。 若要設定這些用於 Azure 中的節點模組包含最安全的方法是透過系統環境變數，您可以直接 Azure 管理主控台中檢視設定。

選取您 node.js 的網站，然後按一下 「 設定 」 連結。  如果您稍微向下捲動時，您會看到您可以在其中設定您的應用程式設定屬性的區域。  輸入您的 Twilio 帳戶認證 ([您 Twilio 儀表板上找到][twilio_dashboard]) 如下圖所示-請務必命名 「 TWILIO_ACCOUNT_SID 」 和 「 TWILIO_AUTH_TOKEN 」，分別︰

![Azure 管理主控台][azure-admin-console]

在您已設定這些變數，重新啟動您的應用程式，Azure 主控台中檢視。

### <a name="declaring-the-twilio-module-in-packagejson"></a>宣告 package.json Twilio 模組

接下來，我們需要建立 package.json 管理透過[npm]我們節點模組相依性。  在您建立 Azure/node.js 教學課程中的 「 server.js 」 檔案相同層級，建立一個名為 「 package.json 」 檔案。  此檔案中，將下列動作︰

  {「 名稱 」: 」 應用程式的名稱 「，」 版本 」: 「 0.0.1 」，「 私人 」: true 時，「 指令碼]: {[開始]: 「 節點伺服器 」}，「 相依性 」: {」 快速 」: 「 3.1.0 「，」 ejs 」: 「*」，「 twilio 」: 」*」}}

這會 twilio 模組宣告為相依性，以及常用的[快速的 web 架構][express]和 EJS 範本引擎。  好的現在我們已完成所有設定，讓我們來撰寫程式碼 ！

<a id="makecall"/>
## <a name="make-an-outbound-call"></a>輸出撥打電話

現在就讓我們建立將通話轉換成數字我們所選擇的簡易表單。  開啟 server.js，然後輸入下列程式碼。  請注意，其中顯示 「 CHANGE_ME 」-那里保留 azure 網站的名稱︰

    // Module dependencies
    var express = require('express'), 
      path = require('path'), 
      http = require('http'), 
      twilio = require('twilio');

    // Create Express web application
    var app = express();

    // Express configuration
    app.configure(function(){
      app.set('port', process.env.PORT || 3000);
      app.set('views', __dirname + '/views');
      app.set('view engine', 'ejs');
      app.use(express.favicon());
      app.use(express.logger('dev'));
      app.use(express.bodyParser());
      app.use(express.methodOverride());
      app.use(app.router);
      app.use(express.static(path.join(__dirname, 'public')));
    });
    app.configure('development', function(){
      app.use(express.errorHandler());
    });

    // Render an HTML user interface for the application's home page
    app.get('/', function(request, response) {
      response.render('index');
    });

    // Handle the form POST to place a call
    app.post('/call', function(request, response) {
      var client = twilio();
      client.makeCall({
          // make a call to this number
          to:request.body.number,

          // Change to a Twilio number you bought - see:
          // https://www.twilio.com/user/account/phone-numbers/incoming
          from:'+15558675309',

          // A URL in our app which generates TwiML
          // Change "CHANGE_ME" to your app's name
          url:'https://CHANGE_ME.azurewebsites.net/outbound_call'
      }, function(error, data) {
          // Go back to the home page
          response.redirect('/');
      });
    });

    // Generate TwiML to handle an outbound call
    app.post('/outbound_call', function(request, response) {
      var twiml = new twilio.TwimlResponse();

      // Say a message to the call's receiver 
      twiml.say('hello - thanks for checking out Twilio and Azure', {
          voice:'woman'
      });

      response.set('Content-Type', 'text/xml');
      response.send(twiml.toString());
    });

    // Start server
    http.createServer(app).listen(app.get('port'), function(){
      console.log("Express server listening on port " + app.get('port'));
    });

接下來，建立目錄，稱為 「 檢視 」-此目錄內，建立一個名為 「 index.ejs 」，使用下列內容檔案︰

    <!DOCTYPE html>
    <html>
    <head>
      <title>Twilio Test</title>
      <style>
        input { height:20px; width:300px; font-size:18px; margin:5px; padding:5px; }
      </style>
    </head>
    <body>
      <h1>Twilio Test</h1>
      <form action="/call" method="POST">
          <input placeholder="Enter a phone number" name="number"/>
          <br/>
          <input type="submit" value="Call the number above"/>
      </form>
    </body>
    </html>

現在，Azure 部署您的網站，並開啟您的首頁。  您應該可以在 [文字] 欄位中，輸入您的電話號碼，並從您 Twilio 數字接聽電話 ！

<a id="sendmessage"/>
## <a name="send-an-sms-message"></a>傳送簡訊形式的訊息

現在讓我們來設定的使用者介面和處理邏輯的表單來傳送簡訊。  開啟 「 server.js 」，並新增下列程式碼之後 「 app.post 」 的最後一個呼叫︰

    app.post('/sms', function(request, response) {
      var client = twilio();
      client.sendSms({
          // send a text to this number
          to:request.body.number,

          // A Twilio number you bought - see:
          // https://www.twilio.com/user/account/phone-numbers/incoming
          from:'+15558675309',

          // The body of the text message
          body: request.body.message
          
      }, function(error, data) {
          // Go back to the home page
          response.redirect('/');
      });
    });

在 「 views/index.ejs 」，新增另一個表單的 [送出數字和文字訊息的第一個下︰

    <form action="/sms" method="POST">
      <input placeholder="Enter a phone number" name="number"/>
      <br/>
      <input placeholder="Enter a message to send" name="message"/>
      <br/>
      <input type="submit" value="Send text to the number above"/>
    </form>

重新部署 Azure，您的應用程式，您現在應該可以送出表單並傳送您自己 （或任何您最接近的朋友的個人檔案） 的文字訊息 ！

<a id="nextsteps"/>
## <a name="next-steps"></a>後續步驟

現在您已經學會如何使用 node.js 和 Twilio 來建立通訊的相關應用程式的基本概念。  但這些範例幾乎可用的功能可能 Twilio 與 node.js 介面。  使用 node.js Twilio 的詳細資訊，請參閱下列資源︰

* [正式模組文件][docs]
* [Voip 需具備的教學課程，node.js 應用程式][voipnode]
* [Votr-即時 SMS 投票 node.js 與 CouchDB （三個部分） 的應用程式][votr]
* [在瀏覽器中使用 node.js 組程式設計][pair]

我們希望您喜歡在 Azure 駭客 node.js 和 Twilio ！

[purchase_phone]: https://www.twilio.com/user/account/phone-numbers/available/local
[twiml]: https://www.twilio.com/docs/api/twiml
[signup]: http://ahoy.twilio.com/azure
[azure_new_site]: /app-service-web/web-sites-nodejs-develop-deploy-mac.md
[twilio_dashboard]: https://www.twilio.com/user/account
[npm]: http://npmjs.org
[express]: http://expressjs.com
[voipnode]: http://www.twilio.com/blog/2013/04/introduction-to-twilio-client-with-node-js.html
[docs]: http://twilio.github.io/twilio-node/
[votr]: http://www.twilio.com/blog/2012/09/building-a-real-time-sms-voting-app-part-1-node-js-couchdb.html
[pair]: http://www.twilio.com/blog/2013/06/pair-programming-in-the-browser-with-twilio.html
[azure-admin-console]: ./media/partner-twilio-nodejs-how-to-use-voice-sms/twilio_1.png



