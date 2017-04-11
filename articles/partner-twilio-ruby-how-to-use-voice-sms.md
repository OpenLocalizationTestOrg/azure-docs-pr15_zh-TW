<properties 
    pageTitle="如何使用語音及簡訊 （並列文字） Twilio |Microsoft Azure" 
    description="瞭解如何撥打電話和 Azure 上的 [傳送簡訊，Twilio API 服務。 並列文字以撰寫程式碼範例。" 
    services="" 
    documentationCenter="ruby" 
    authors="devinrader" 
    manager="twilio" 
    editor=""/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ruby" 
    ms.topic="article" 
    ms.date="11/25/2014" 
    ms.author="MicrosoftHelp@twilio.com"/>





# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-ruby"></a>如何使用 Twilio 的語音及 [注音標示的簡訊功能
本指南將示範如何執行 Azure Twilio API 服務程式設計的一般工作。 涵蓋的案例包含撥打電話及傳送簡短訊息服務 (SMS) 訊息。 如需有關 Twilio 和您的應用程式中使用的語音及 SMS 的詳細資訊，請參閱 [[下一步](#NextSteps)] 區段。

## <a id="WhatIs"></a>什麼是 Twilio？
Twilio 是電話語音 web 服務 API，可讓您使用您現有的網站語言和技巧來建立語音及 SMS 應用程式。 Twilio 是協力廠商服務 （不 Azure 的功能和 Microsoft 產品）。

**Twilio 語音**可讓您的應用程式撥打與接聽電話。 **Twilio SMS**可讓您撥打及接收簡訊的應用程式。 **Twilio 用戶端**可讓您的應用程式，以啟用 [使用現有的網際網路連線，包括行動裝置連線的語音通訊。

## <a id="Pricing"></a>Twilio 價格和優惠
關於 Twilio 價格的資訊可在[Twilio 價格] [twilio_pricing]。 Azure 客戶接收[特殊優惠][special_offer]: 1000年文字免費信用卡或連入分鐘 1000年。 若要註冊這項優惠，或取得詳細資訊，請造訪[http://ahoy.twilio.com/azure][special_offer]。  

## <a id="Concepts"></a>概念
Twilio API 是 RESTful API 的應用程式提供語音及簡訊功能。 用戶端文件庫可在多種語言。如果是清單，請參閱[Twilio API 文件庫] [twilio_libraries]。

### <a id="TwiML"></a>TwiML
TwiML 是一組通知如何處理來電的 Twilio 或 SMS 以 XML 為基礎的指示。

例如，下列 TwiML 想要將文字轉換**什麼**語音。

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

所有 TwiML 文件會都有`<Response>`為其根項目。 從該位置，您可以使用 Twilio 動詞以定義您的應用程式的行為。

### <a id="Verbs"></a>TwiML 動作
Twilio 動詞是通知 Twilio**執行**的 XML 標記。 例如，**&lt;說&gt;**動詞指示 Twilio 輪流電話傳送訊息。 

以下是 Twilio 動詞的清單。

* **&lt;撥號&gt;**︰ 連線至其他電話的來電顯示。
* **&lt;收集&gt;**︰ 收集電話鍵盤上輸入的數字。
* **&lt;掛斷&gt;**︰ 結束通話。
* **&lt;播放&gt;**︰ 播放音訊檔案。
* **&lt;暫停&gt;**︰ 火車等待指定的秒數。
* **&lt;記錄&gt;**︰ 記錄來電者語音，並會傳回包含錄製檔案的 URL。
* **&lt;重新導向&gt;**︰ 將控制通話或簡訊傳送到不同的 URL，TwiML。
* **&lt;拒絕&gt;**︰ 不計費您拒絕來電至您的 Twilio 號碼
* **&lt;說&gt;**︰ 將文字轉換語音的電話。
* ** &lt;Sms&gt;**︰ 傳送簡訊形式的訊息。

如需有關 Twilio 動詞、 其屬性和 TwiML 的詳細資訊，請參閱[TwiML] [twiml]。 如需關於 Twilio API 的詳細資訊，請參閱[Twilio API] [twilio_api]。

## <a id="CreateAccount"></a>建立 Twilio 帳戶
當您準備好要取得 Twilio 帳戶時，登入，[請嘗試 Twilio] [try_twilio]。 您可以開始使用免費的帳戶，並在稍後升級您的帳戶。

當您註冊 Twilio 帳戶時，就會出現可用的電話號碼應用程式。 您也會收到帳戶 SID 與驗證 」 token。 同時將會需要進行 Twilio API 通話。 若要避免他人未經授權的存取您的帳戶，讓您驗證的權杖安全。 您的帳戶 SID 及驗證的權杖為[Twilio 帳戶] 頁面上]的檢視[twilio_account]，請在欄位**帳戶 SID**和**驗證權杖**，分別標記。

### <a id="VerifyPhoneNumbers"></a>確認電話號碼
除了數目您可以依 Twilio，您也可以驗證在應用程式中使用的號碼，您可以控制 （也就是您的行動電話或住家電話號碼）。 

如何驗證的電話號碼的資訊，請參閱[管理數字] [verify_phone]。

## <a id="create_app"></a>建立並列文字應用程式
並列文字的應用程式使用 Twilio 服務，並在 Azure 執行和並無不同任何並列文字的其他應用程式使用 Twilio 服務。 這份文件時 Twilio 服務是 RESTful，而且可以從 [注音標示呼叫數種方式，請將著重於如何與[Twilio [注音標示的列印文件庫]中使用 Twilio 服務[twilio_ruby]。

首先，[設定新的 Azure Linux VM] [azure_vm_setup]作為新並列文字的 web 應用程式的主機。 略過關於滑軌的應用程式，只要設定 VM 建立的步驟。 請確定您建立使用外部連接埠 80 和 5000 的內部連接埠的端點。

在下面的範例中，我們會使用[Sinatra][sinatra]，[注音標示的非常簡單的 web 架構。 但您確實可以使用 [注音標示的 Twilio 協助程式庫，以任何其他 web 架構，包括在機殼並列文字。

將您的新 VM SSH 並建立新的應用程式的目錄。 內目錄，建立名 Gemfile 的檔案並複製下列程式碼︰

    source 'https://rubygems.org'
    gem 'sinatra'
    gem 'thin'

在命令列執行`bundle install`。 這將會安裝上述相依性。 下一步建立稱為檔案`web.rb`。 這是您的 web 應用程式的程式碼所在。 下列程式碼貼入它︰

    require 'sinatra'

    get '/' do
        "Hello Monkey!"
    end

現在您可以執行命令`ruby web.rb -p 5000`。 這會微調向上小型的網頁伺服器連接埠 5000。 您可以瀏覽此應用程式，在瀏覽器中瀏覽 URL 您設定您的 Azure VM。 您連絡您的 web 應用程式，在瀏覽器中，當您準備好要開始建立 Twilio 應用程式。

## <a id="configure_app"></a>設定您的應用程式使用 Twilio
您可以設定您的 web 應用程式使用 Twilio 文件庫更新您`Gemfile`包含此行︰

    gem 'twilio-ruby'

在命令列中，執行`bundle install`。 現在，請開啟`web.rb`並包括這一行頂端︰

    require 'twilio-ruby'

您是現在所有設定 web 應用程式中使用 [注音標示 Twilio 協助程式庫。

## <a id="howto_make_call"></a>如何︰ 用外撥打電話
以下示範如何用外撥打電話。 重要概念包含進行 REST API 通話中使用 [注音標示的 Twilio 協助文件庫和呈現 TwiML。 取代值****] 及****電話號碼，並確保您之前執行程式碼 Twilio 帳戶驗證**的**電話號碼。

新增此函數， `web.md`:

    # Set your account ID and authentication token.
    sid = "your_twilio_account_sid";
    token = "your_twilio_authentication_token";

    # The number of the phone initiating the the call.
    # This should either be a Twilio number or a number that you've verified
    from = "NNNNNNNNNNN";

    # The number of the phone receiving call.
    to = "NNNNNNNNNNN";

    # Use the Twilio-provided site for the TwiML response.
    url = "http://yourdomain.cloudapp.net/voice_url";
      
    get '/make_call' do
      # Create the call client.
      client = Twilio::REST::Client.new(sid, token);
      
      # Make the call
      client.account.calls.create(to: to, from: from, url: url)
    end

    post '/voice_url' do
      "<Response>
         <Say>Hello Monkey!</Say>
       </Response>"
    end
    
如果您開啟向上`http://yourdomain.cloudapp.net/make_call`瀏覽器中，就會觸發電話撥號 Twilio API 通話。 中的前兩個參數`client.account.calls.create`是容易理解︰ 的數字呼叫的`from`的數字的通話是`to`。 

第三個參數 (`url`) 是 Twilio 要求取得指示的處理方式一旦通話已連線的 URL。 在這種情況下我們的 URL 設定 (`http://yourdomain.cloudapp.net`) 會傳回簡單的 TwiML 文件並使用`<Say>`動詞方式某些文字轉換語音功能，然後說 「 Hello 猴子 「 通話的人。

## <a id="howto_recieve_sms"></a>如何︰ 接收簡訊形式的訊息
在先前範例我們發起的**外寄**的電話。 這次，我們要使用的電話號碼，Twilio，提供我們註冊期間處理的**內送**的簡訊訊息。

首先，登入您[Twilio 儀表板][twilio_account]。 按一下 [數字 」 在上方導覽中，然後按一下所提供的 Twilio 數。 您會看到您可以設定的兩個 Url。 語音要求 URL] 與 [以簡訊要求 URL。 這些是 Twilio 通話時進行通話，或以簡訊傳送到您的電話號碼的 Url。 Url 也稱為 「 web 攔截 」。

我們想要處理的內送的簡訊，現在讓我們來更新的 URL `http://yourdomain.cloudapp.net/sms_url`。 請繼續，並按一下 [在頁面底部的 [儲存變更。 現在，回`web.rb`現在就讓我們程式處理此應用程式︰

    post '/sms_url' do
      "<Response>
         <Message>Hey, thanks for the ping! Twilio and Azure rock!</Message>
       </Response>"
    end

完成之後，變更，請務必重新啟動您的 web 應用程式。 現在，您的電話不，並以簡訊傳送至您的 Twilio 號碼。 您應盡速應收到的簡訊回應指出 「 嗨，偵測 （ping） 感謝您 ！ Twilio 和 Azure 岩石 ！ 」。

## <a id="additional_services"></a>如何︰ 使用其他 Twilio 服務
此處所示的範例，除了 Twilio 提供可用來運用從 Azure 應用程式的其他 Twilio 功能的 web 式 Api。 完整的詳細資訊，請參閱[Twilio API 文件] [twilio_api_documentation]。

### <a id="NextSteps"></a>後續步驟
現在，您學到的 Twilio 服務的基本概念，請遵循這些連結，深入瞭解︰

* [Twilio 安全性指導方針] [twilio_security_guidelines]
* [Twilio HowTos 和範例程式碼] [twilio_howtos]
* [Twilio 快速入門教學課程][twilio_quickstarts] 
* [在 GitHub Twilio] [twilio_on_github]
* [與 Twilio 支援] [twilio_support]

[twilio_ruby]: https://www.twilio.com/docs/ruby/install





[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: http://www.twilio.com/api
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart
[sinatra]: http://www.sinatrarb.com/
[azure_vm_setup]: http://www.windowsazure.com/develop/ruby/tutorials/web-app-with-linux-vm/
