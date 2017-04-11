<properties
    pageTitle="如何從 Twilio (PHP) 撥打電話 |Microsoft Azure"
    description="瞭解如何撥打電話和 Azure 上的 [傳送簡訊，Twilio API 服務。 範例是 PHP 應用程式。"
    documentationCenter="php"
    services=""
    authors="devinrader"
    manager="twilio"
    editor="mollybos"/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="11/25/2014"
    ms.author="microsofthelp@twilio.com"/>

# <a name="how-to-make-a-phone-call-using-twilio-in-a-php-application-on-azure"></a>如何撥打電話上 Azure PHP 應用程式中使用 Twilio

下列範例會顯示如何使用 Twilio PHP 網頁上裝載於 Azure 撥打電話。 產生的應用程式會提示使用者使用電話的值，如以下的螢幕擷取畫面所示。

![使用 Twilio 和 PHP azure 通話表單][twilio_php]

您必須執行下列動作，本主題中使用程式碼︰

1. 取得 Twilio 帳戶與驗證 token。 若要開始使用 Twilio，評估價格在[http://www.twilio.com/pricing][twilio_pricing]。 您可以註冊試用帳戶在[https://www.twilio.com/try-twilio][try_twilio]。 關於 API Twilio 所提供的資訊，請參閱[http://www.twilio.com/api][twilio_api]。
2. 取得[的 PHP Twilio 文件庫](https://github.com/twilio/twilio-php)，或將其安裝為梨子套件。 如需詳細資訊，請參閱[讀我檔案](https://github.com/twilio/twilio-php/blob/master/README.md)。
3. 安裝 PHP Azure SDK。 SDK 和指示進行安裝的概觀，請參閱[設定 PHP Azure SDK][setup_php_sdk]。

## <a name="create-a-web-form-for-making-a-call"></a>建立 web 表單的撥打電話

下列 HTML 程式碼會顯示如何建立網頁 (**callform.html**) 的擷取使用者資料撥打電話︰

    <html>
    <head>
        <title>Automated call form</title>
    </head>
    <body>
    <h1>Automated Call Form</h1>
    <p>Fill in all fields and click <b>Make this call</b>.</p>
    <form action="makecall.php" method="post">
    <table>
        <tr>
            <td>To:</td>
            <td><input type="text" size=50 name="callTo" value=""></td>
        </tr>
        <tr>
            <td>From:</td>
            <td><input type="text" size=50 name="callFrom" value=""></td>
        </tr>
        <tr>
            <td>Call message:</td>
            <td><input type="text" size=100 name="callText" value="Hello. This is the call text. Good bye." /></td>
        </tr>
        <tr>
            <td colspan=2><input type="submit" value="Make this call"></td>
        </tr>
    </table>
    </form>
    <br/>
    </body>
    </html>

## <a name="create-the-code-to-make-the-call"></a>建立要撥號的程式碼
下列程式碼會顯示如何建立網頁 (**makecall.php**) 就是當使用者提交**callform.html**所顯示的表單。 以下所示的程式碼會建立呼叫訊息，並產生通話。 （使用您的 Twilio 帳戶和驗證權杖，而不是指定給**$sid**和**$token**下列程式碼的版面配置區值）。

    <html>
    <head><title>Making call...</title></head>
    <body>
    <p>Your call is being made.</p>

    <?php
    require_once 'Services/Twilio.php';

    $sid = "your_account_sid";
    $token = "your_authentication_token";

    $from_number = $_POST['callFrom']; // Calls must be made from a registered Twilio number.
    $to_number = $_POST['callTo'];
    $message = $_POST['callText'];

    $client = new Services_Twilio($sid, $token, "2010-04-01");

    $call = $client->account->calls->create(
        $from_number,
        $to_number,
        'http://twimlets.com/message?Message='.urlencode($message)
    );

    echo "Call status: ".$call->status."<br />";
    echo "URI resource: ".$call->uri."<br />";
    ?>
    </body>
    </html>

除了進行通話，請**makecall.php**會顯示一些通話中繼資料 （在下面的螢幕擷取畫面所示的範例）。 如需有關通話的中繼資料的詳細資訊，請參閱[https://www.twilio.com/docs/api/rest/call#instance-properties][twilio_call_properties]。

![使用 Twilio 和 PHP azure 通話回覆][twilio_php_response]

## <a name="run-the-application"></a>執行應用程式
下一步是部署您 Azure 網站的應用程式。 下列文章中包含建立網站和部署您的程式碼，就可以給、 FTP，或 WebMatrix （但非全部每個文件中的資訊是有用） 的資訊︰

* [建立 PHP MySQL Azure 網站及部署使用給][website-git]
* [建立 PHP MySQL Azure 網站及部署使用 FTP][website-ftp]

## <a name="next-steps"></a>後續步驟
將此程式碼提供以顯示您使用 Twilio PHP 上 Azure 中的基本功能。 之前生產環境中部署 Azure，您可能會想要新增更多的錯誤處理或其他功能。 例如︰

* 而不是使用 web 表單，您可以使用儲存空間 Azure blob] 或 [SQL 資料庫來儲存電話號碼，然後呼叫文字。 PHP 中使用儲存空間 Azure blob 的相關資訊，請參閱[使用 PHP 應用程式使用 Azure 儲存體][howto_blob_storage_php]。 使用 SQL 資料庫中 PHP 的相關資訊，請參閱[PHP 應用程式使用 SQL 資料庫][howto_sql_azure_php]。
* **Makecall.php**程式碼使用 Twilio 所提供的 URL ([http://twimlets.com/message][twimlet_message_url]) 提供 Twilio 標記語言 (TwiML) 回應，通知 Twilio 如何繼續通話。 例如，則會傳回 TwiML 最多可以包含`<Say>`唸來電者的文字會產生的動作。 而不是使用 Twilio 所提供的 URL，您可以建立自己的服務，來回應 Twilio 的要求。如需詳細資訊，請參閱[如何使用 Twilio 的語音及簡訊功能 PHP][howto_twilio_voice_sms_php]。 深入瞭解 TwiML 位於[http://www.twilio.com/docs/api/twiml][twiml]，並深入瞭解`<Say>`和其他 Twilio 動作，請參閱[http://www.twilio.com/docs/api/twiml/say][twilio_say]。
* 閱讀 Twilio 安全性指導方針，在[https://www.twilio.com/docs/security][twilio_docs_security]。

如需 Twilio 的詳細資訊，請參閱[https://www.twilio.com/docs][twilio_docs]。

## <a name="see-also"></a>另請參閱
* [如何使用 Twilio 的語音及 SMS PHP 功能](partner-twilio-php-how-to-use-voice-sms.md)

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[setup_php_sdk]: http://azurephp.interoperabilitybridges.com/articles/setup-the-windows-azure-sdk-for-php
[twimlet_message_url]: http://twimlets.com/message
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api_service]: http://api.twilio.com
[build_php_azure_app]: http://azurephp.interoperabilitybridges.com/articles/build-and-deploy-a-windows-azure-php-application
[howto_twilio_voice_sms_php]: partner-twilio-php-how-to-use-voice-sms.md
[howto_blob_storage_php]: http://azure.microsoft.com/documentation/articles/storage-php-how-to-use-blobs/
[howto_sql_azure_php]: http://azure.microsoft.com/documentation/articles/sql-database-php-how-to-use/
[twilio_call_properties]: https://www.twilio.com/docs/api/rest/call#instance-properties
[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_php]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg
[twilio_php_response]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg
[website-git]: ./web-sites/web-sites-php-mysql-deploy-use-git.md
[website-ftp]: ./web-sites/web-sites-php-mysql-deploy-use-ftp.md
[twilio_php_github]: https://github.com/twilio/twilio-php
