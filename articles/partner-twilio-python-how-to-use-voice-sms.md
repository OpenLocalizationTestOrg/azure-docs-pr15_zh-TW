<properties
    pageTitle="如何使用語音及簡訊 (PHP) Twilio |Microsoft Azure"
    description="瞭解如何撥打電話和 Azure 上的 [傳送簡訊，Twilio API 服務。 PHP 以撰寫程式碼範例。"
    services=""
    documentationCenter="python"
    authors="devinrader"
    manager="twilio"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="python" 
    ms.topic="article"
    ms.date="02/19/2015"
    ms.author="MicrosoftHelp@twilio.com"/>





# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-php"></a>如何使用 Twilio 的語音及 SMS PHP 功能
本指南將示範如何執行 Azure Twilio API 服務程式設計的一般工作。 涵蓋的案例包含撥打電話及傳送簡短訊息服務 (SMS) 訊息。 如需有關 Twilio 和您的應用程式中使用的語音及 SMS 的詳細資訊，請參閱 [[下一步](#NextSteps)] 區段。

## <a id="WhatIs"></a>什麼是 Twilio？
Twilio 電源未來的公司通訊，讓內嵌語音、 VoIP、 和訊息應用程式開發人員。 他們虛擬化在雲端，全域環境中，將它公開透過 Twilio 通訊 API 平台所需的所有基礎結構。 應用程式會建立簡易與調整。 享受與支付-為-您的彈性移價格，並從雲端可靠性。

**Twilio 語音**可讓您的應用程式撥打與接聽電話。 **Twilio SMS**可讓您的應用程式傳送及接收文字訊息。 **Twilio 用戶端**可以讓您從任何電話、 平板電腦或在瀏覽器的 [VoIP 通話，以及支援 WebRTC。

## <a id="Pricing"></a>Twilio 價格和優惠

Azure 客戶升級 Twilio 帳戶時，收到 [特殊優惠] $10 / Twilio 信用卡。 此 Twilio 信用卡可以套用至任何 Twilio 使用量 （等於 1000 的簡訊收發郵件超過 1000 個輸入的語音通話分鐘方案，根據您的電話號碼和訊息或來電目的地的位置，$10 信用卡）。 兌換 Twilio 退款，並在開始使用: [ahoy.twilio.com/azure]。

Twilio 是 pay-as-you-go 服務。 有沒有設定費用，您可以隨時關閉您的帳戶。 您可以尋找更多詳細資料[Twilio 價格] [twilio_pricing]。

## <a id="Concepts"></a>概念
Twilio API 是 RESTful API 的應用程式提供語音及簡訊功能。 用戶端文件庫可在多種語言。如果是清單，請參閱[Twilio API 文件庫] [twilio_libraries]。

Twilio API 的重要是 Twilio 動詞和 Twilio 標記語言 (TwiML)。

### <a id="Verbs"></a>Twilio 動作
API 會使用 Twilio 動詞;例如，**&lt;說&gt;**動詞指示 Twilio 輪流電話傳送訊息。

以下是 Twilio 動詞的清單。 深入瞭解其他動詞命令和功能，透過 [Twilio 標記語言文件] [http://www.twilio.com/docs/api/twiml]。

* **&lt;撥號&gt;**︰ 連線至其他電話的來電顯示。
* **&lt;收集&gt;**︰ 收集電話鍵盤上輸入的數字。
* **&lt;掛斷&gt;**︰ 結束通話。
* **&lt;播放&gt;**︰ 播放音訊檔案。
* **&lt;暫停&gt;**︰ 火車等待指定的秒數。
* **&lt;記錄&gt;**︰ 記錄語音的來電者，並會傳回包含錄製檔案的 URL。
* **&lt;重新導向&gt;**︰ 將控制通話或簡訊傳送到不同的 URL，TwiML。
* **&lt;拒絕&gt;**︰ 不計費您拒絕來電至您的 Twilio 號碼
* **&lt;說&gt;**︰ 將文字轉換語音的電話。
* ** &lt;Sms&gt;**︰ 傳送簡訊形式的訊息。

### <a id="TwiML"></a>TwiML
TwiML 是以 XML 為基礎的指示，根據通知如何處理來電的 Twilio 或 SMS Twilio 物組。

例如，下列 TwiML 想要將文字轉換**什麼**語音。

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

當您的應用程式呼叫 Twilio API 時，其中一個 API 參數時的 TwiML 回應傳回的 URL。 為了開發，您可以使用 Twilio 所提供的 Url 提供 TwiML 回應應用程式使用。 您也可以裝載您自己的 Url，以產生 TwiML 回應，另一個選項是使用**TwiMLResponse**物件。

如需有關 Twilio 動詞、 其屬性和 TwiML 的詳細資訊，請參閱[TwiML] [twiml]。 如需關於 Twilio API 的詳細資訊，請參閱[Twilio API] [twilio_api]。

## <a id="CreateAccount"></a>建立 Twilio 帳戶
當您準備要 Twilio 帳戶時，登入，[請嘗試 Twilio] [try_twilio]。 您可以開始使用免費的帳戶，並在稍後升級您的帳戶。

當您註冊 Twilio 帳戶時，您會收到的帳戶識別碼和驗證權杖。 同時將會需要進行 Twilio API 通話。 若要避免他人未經授權的存取您的帳戶，讓您驗證的權杖安全。 您的帳戶識別碼和驗證權杖為[Twilio 帳戶] 頁面上]的檢視 [twilio_account]，請在欄位**帳戶 SID**和**驗證權杖**，分別標記。

## <a id="create_app"></a>建立 PHP 應用程式
使用 Twilio 服務，並在 Azure 執行 PHP 應用程式和並無不同使用 Twilio 服務的任何其他 PHP 應用程式。 這份文件時 Twilio 服務其餘為基礎，可以從 PHP 呼叫數種方式，將著重於如何使用 Twilio 服務與[Twilio 文件庫的 PHP 從 GitHub][twilio_php]。 如需有關使用 PHP Twilio 文件庫的詳細資訊，請參閱[http://readthedocs.org/docs/twilio-php/en/latest/index.html][twilio_lib_docs]。

建立並部署至 Azure Twilio/PHP 應用程式的詳細的說明可如何[進行上 Azure PHP 應用程式中的電話使用 Twilio][howto_phonecall_php]。

## <a id="configure_app"></a>設定您的應用程式使用 Twilio 文件庫
您可以設定您的應用程式使用 PHP Twilio 文件庫中的兩種方法︰

1. 下載適用於從 GitHub PHP Twilio 文件庫 ([https://github.com/twilio/twilio-php][twilio_php]) 並將**服務**目錄新增至您的應用程式。

    -或-

2. 安裝為梨子套件 PHP Twilio 文件庫。 您可以安裝並輸入下列命令︰

        $ pear channel-discover twilio.github.com/pear
        $ pear install twilio/Services_Twilio

一旦您安裝的 PHP Twilio 文件庫，您可以新增**require_once**陳述式頂端的 [您要參照文件庫的 PHP 檔案︰

        require_once 'Services/Twilio.php';

如需詳細資訊，請參閱[https://github.com/twilio/twilio-php/blob/master/README.md][twilio_github_readme]。

## <a id="howto_make_call"></a>如何︰ 用外撥打電話
以下示範如何用使用**Services_Twilio**類別外撥打電話。 將此程式碼也會使用 Twilio 所提供的網站，傳回 Twilio 標記語言 (TwiML) 回應。 取代值****] 及****電話號碼，並確保您之前執行程式碼 Twilio 帳戶驗證**的**電話號碼。

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";

    // The number of the phone initiating the the call.
    // (Must be previously validated with Twilio.)
    $from_number = "NNNNNNNNNNN";

    // The number of the phone receiving call.
    $to_number = "NNNNNNNNNNN";

    // Use the Twilio-provided site for the TwiML response.
    $url = "http://twimlets.com/message";

    // The phone message text.
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    //Make the call.
    try
    {
        $call = $client->account->calls->create(
            $from_number,
            $to_number,
            $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e)
    {
        echo 'Error: ' . $e->getMessage();
    }

如上所述，將此程式碼會使用 Twilio 所提供的網站，傳回 TwiML 回應。 您改為無法使用您自己的網站，提供 TwiML 回應。如需詳細資訊，請參閱[如何提供 TwiML 回應從您自己的網站](#howto_provide_twiml_responses)。


- **附註**︰ 若要疑難排解 SSL 憑證驗證錯誤，請參閱[http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html][ssl_validation]


## <a id="howto_send_sms"></a>如何︰ 傳送簡訊形式的訊息
以下示範如何傳送簡訊訊息使用**Services_Twilio**類別。 **從**數字是由 Twilio 試用帳戶傳送簡訊提供。 **以**數字必須驗證 Twilio 帳戶之前執行程式碼。

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";


    $from_number = "NNNNNNNNNNN"; // With trial account, texts can only be sent from your Twilio number.
    $to_number = "NNNNNNNNNNN";
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    // Send the SMS message.
    try
    {
        $client->account->sms_messages->create($from_number, $to_number, $message);
    }
    catch (Exception $e)
    {
        echo 'Error: ' . $e->getMessage();
    }

## <a id="howto_provide_twiml_responses"></a>如何︰ 提供 TwiML 回應從您的網站
當您的應用程式啟動通話 Twilio api 時，Twilio 會傳送邀請到預期會傳回 TwiML 回應的 URL。 上述範例使用 Twilio 所提供的 URL [http://twimlets.com/message][twimlet_message_url]。 （時 TwiML Twilio 專為使用設計，您可以檢視 it 在瀏覽器中。 例如，按一下 [ [http://twimlets.com/message] [twimlet_message_url]若要查看空`<Response>`項目。另一個範例中，按一下 [ [http://twimlets.com/message?Message%5B0%5D=Hello%20World] [twimlet_message_url_hello_world]若要查看`<Response>`包含項目`<Say>`項目。)

不要依賴 Twilio 所提供的 URL，您可以建立您自己的網站，傳回 HTTP 回應。 您可以建立網站 XML 回應; 傳回任何語言本主題假設您將建立 TwiML 使用 PHP。

下列 PHP 頁面產生 TwiML 回應指出**Hello 全球**通話。

    <?php
        header("content-type: text/xml");
        echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
    ?>
    <Response>
        <Say>Hello world.</Say>
    </Response>

您可以看到上述範例中，TwiML 回應是只要 XML 文件。 PHP Twilio 庫包含將會為您產生 TwiML 的類別。 下列範例會產生相同的回應，如下圖所示的上方，，但使用**服務\_Twilio\_Twiml**的 PHP Twilio 文件庫中的類別︰

    require_once('Services/Twilio.php');

    $response = new Services_Twilio_Twiml();
    $response->say("Hello world.");
    print $response;

如需有關 TwiML 的詳細資訊，請參閱[https://www.twilio.com/docs/api/twiml][twiml_reference]。

一旦您有提供 TwiML 回應您 PHP 頁面設定，請使用 PHP 頁面的 URL URL 傳入`Services_Twilio->account->calls->create`方法。 比方說，如果您有 Web 應用程式名稱為**MyTwiML**部署至 Azure 裝載的服務，，PHP 頁面名稱是**mytwiml.php**，URL 可以傳遞到**Services_Twilio]-> [帳戶]-> [來電]-> [建立**如下列範例所示︰

    require_once 'Services/Twilio.php';

    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";
    $from_number = "NNNNNNNNNNN";
    $to_number = "NNNNNNNNNNN";
    $url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.php";

    // The phone message text.
    $message = "Hello world.";

    $client = new Services_Twilio($sid, $token, "2010-04-01");

    try
    {
        $call = $client->account->calls->create(
            $from_number,
            $to_number,
            $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e)
    {
        echo 'Error: ' . $e->getMessage();
    }

其他關於使用 Twilio 與 PHP Azure 中的詳細資訊，請參閱[如何讓上 Azure PHP 應用程式中的電話使用 Twilio][howto_phonecall_php]。

## <a id="AdditionalServices"></a>如何︰ 使用其他 Twilio 服務
此處所示的範例，除了 Twilio 提供可用來運用從 Azure 應用程式的其他 Twilio 功能的 web 式 Api。 完整的詳細資訊，請參閱[Twilio API 文件] [twilio_api_documentation]。

## <a id="NextSteps"></a>後續步驟
現在，您已經學會 Twilio 服務的基本概念，請遵循這些連結，深入瞭解︰

* [Twilio 安全性指導方針] [twilio_security_guidelines]
* [Twilio 如何指南和範例程式碼] [twilio_howtos]
* [Twilio 快速入門教學課程][twilio_quickstarts]
* [在 GitHub Twilio] [twilio_on_github]
* [與 Twilio 支援] [twilio_support]

[twilio_php]: https://github.com/twilio/twilio-php
[twilio_lib_docs]: http://readthedocs.org/docs/twilio-php/en/latest/index.html
[twilio_github_readme]: https://github.com/twilio/twilio-php/blob/master/README.md
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html

[howto_phonecall_php]: http://windowsazure.com/documentation/articles/partner-twilio-php-make-phone-call
[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: http://www.twilio.com/pricing

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
