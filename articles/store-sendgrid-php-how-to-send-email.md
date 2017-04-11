<properties 
    pageTitle="如何使用 SendGrid 電子郵件服務 (PHP) |Microsoft Azure" 
    description="瞭解如何傳送 Azure 中的 [SendGrid 電子郵件服務的電子郵件。 PHP 以撰寫程式碼範例。" 
    documentationCenter="php" 
    services="" 
    manager="sendgrid" 
    editor="mollybos" 
    authors="thinkingserious"/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="PHP" 
    ms.topic="article" 
    ms.date="10/30/2014" 
    ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork; matt.bernier@sendgrid.com"/>
# <a name="how-to-use-the-sendgrid-email-service-from-php"></a>如何使用 PHP SendGrid 電子郵件服務

本文將示範如何執行 Azure SendGrid 電子郵件服務與程式的一般工作。 PHP 撰寫範例。
涵蓋的案例包含**建構電子郵件**、**傳送電子郵件**，以及**新增附件**。 如需有關 SendGrid 和傳送電子郵件的詳細資訊，請參閱[下一步](#next-steps)] 區段。

## <a name="what-is-the-sendgrid-email-service"></a>什麼是 SendGrid 電子郵件服務？

SendGrid 是[以雲端為基礎的電子郵件服務]，提供可靠的[交易的電子郵件傳遞]、 延展性和彈性的 Api，讓您輕鬆地自訂整合以及即時分析。 一般 SendGrid 使用情況包括︰

-   自動傳送回條的客戶
-   管理通訊群組清單的傳送給客戶，每月 e fliers 和特殊優惠
-   收集項目，例如 [封鎖的電子郵件，以及客戶回應的即時的指標
-   產生報告，找出趨勢
-   轉接客戶查詢
- 從應用程式的電子郵件通知

如需詳細資訊，請參閱[https://sendgrid.com][]。

## <a name="create-a-sendgrid-account"></a>建立 SendGrid 帳戶

[AZURE.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="using-sendgrid-from-your-php-application"></a>使用 SendGrid 從 PHP 應用程式

Azure PHP 應用程式中使用 SendGrid 需要沒有特殊設定] 或 [程式碼。 SendGrid 是一項服務，因為它可以存取完全相同的方式來自雲端應用程式時，可以從內部部署應用程式。

## <a name="how-to-send-an-email"></a>如何︰ 傳送電子郵件

您可以傳送電子郵件 SMTP 或 Web API SendGrid 提供使用。

### <a name="smtp-api"></a>SMTP API

若要傳送電子郵件使用 SendGrid SMTP API，使用 [*快速寄件者*，元件為基礎的文件庫傳送電子郵件從 PHP 應用程式。 您可以從[http://swiftmailer.org/download][] v5.3.0 （使用[編輯器]安裝快速寄件者），以下載*快速寄件者*的文件庫。 傳送電子郵件與文件庫需要建立的執行個體<span class="auto-style2">快速\_SmtpTransport</span>，<span class="auto-style2">快速\_寄件者</span>，及<span class="auto-style2">快速\_訊息</span>課程]，設定適當的內容，以及呼叫<span class="auto-style2">快速\_Mailer::send</span>方法。

    <?php
     include_once "vendor/autoload.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the reciever is able to view html emails then only the html
      * email will be displayed
      */ 
     $text = "Hi!\nHow are you?\n";
     $html = "<html>
           <head></head>
           <body>
               <p>Hi!<br>
                   How are you?<br>
               </p>
           </body>
           </html>";
     // This is your From email address
     $from = array('someone@example.com' => 'Name To Appear');
     // Email recipients
     $to = array(
           'john@contoso.com'=>'Destination 1 Name',
           'anna@contoso.com'=>'Destination 2 Name'
     );
     // Email subject
     $subject = 'Example PHP Email';

     // Login credentials
     $username = 'yoursendgridusername';
     $password = 'yourpassword';
     
     // Setup Swift mailer parameters
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
     $transport->setUsername($username);
     $transport->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);

     // Create a message (subject)
     $message = new Swift_Message($subject);

     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     
     // send message 
     if ($recipients = $swift->send($message, $failures))
     {
         // This will let us know how many users received this message
         echo 'Message sent out to '.$recipients.' users';
     }
     // something went wrong =(
     else
     {
         echo "Something went wrong - ";
         print_r($failures);
     }

### <a name="web-api"></a>網路 API

使用 PHP [curl 函數][]來使用 SendGrid Web API 傳送電子郵件。

    <?php

     $url = 'https://api.sendgrid.com/';
     $user = 'USERNAME';
     $pass = 'PASSWORD'; 

     $params = array(
          'api_user' => $user,
          'api_key' => $pass,
          'to' => 'john@contoso.com',
          'subject' => 'testing from curl',
          'html' => 'testing body',
          'text' => 'testing body',
          'from' => 'anna@contoso.com',
       );
       
     $request = $url.'api/mail.send.json';
     
     // Generate curl request
     $session = curl_init($request);
     
     // Tell curl to use HTTP POST
     curl_setopt ($session, CURLOPT_POST, true);
     
     // Tell curl that this is the body of the POST
     curl_setopt ($session, CURLOPT_POSTFIELDS, $params);
     
     // Tell curl not to return headers, but do return the response
     curl_setopt($session, CURLOPT_HEADER, false);
     curl_setopt($session, CURLOPT_RETURNTRANSFER, true);
     
     // obtain response
     $response = curl_exec($session);
     curl_close($session);
     
     // print everything out
     print_r($response);

SendGrid 的 Web API 會與 REST API，非常類似，但它不是真正 RESTful 的 API 之後，在大部分的電話，同時而且文章動詞可以交換使用。

## <a name="how-to-add-an-attachment"></a>如何︰ 新增附件

### <a name="smtp-api"></a>SMTP API

傳送附件使用 SMTP API 涉及一行其他程式碼的範例指令碼傳送內含快速寄件者的電子郵件。

    <?php
     include_once "vendor/autoload.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the reciever is able to view html emails then only the html
      * email will be displayed
      */
     $text = "Hi!\nHow are you?\n";
      $html = "<html>
          <head></head>
          <body>
             <p>Hi!<br>
                How are you?<br>
             </p>
          </body>
          </html>";

     // This is your From email address
     $from = array('someone@example.com' => 'Name To Appear');
     
     // Email recipients
     $to = array(
          'john@contoso.com'=>'Destination 1 Name',
          'anna@contoso.com'=>'Destination 2 Name'
     );
     // Email subject
     $subject = 'Example PHP Email';
     
     // Login credentials
     $username = 'yoursendgridusername';
     $password = 'yourpassword';
     
     // Setup Swift mailer parameters
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
     $transport->setUsername($username);
     $transport->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);
     
     // Create a message (subject)
     $message = new Swift_Message($subject);
     
     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName("file_name"));
     
     // send message 
     if ($recipients = $swift->send($message, $failures))
     {
          // This will let us know how many users received this message
          echo 'Message sent out to '.$recipients.' users';
     }
     // something went wrong =(
     else
     {
          echo "Something went wrong - ";
          print_r($failures);
     }

程式碼的其他行如下所示︰

     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName('file_name'));

這一行上呼叫附加方法<span class="auto-style2">快速\_訊息</span>物件，並會使用靜態方法<span class="auto-style2">fromPath</span> <span class="auto-style2">快速\_附件</span>取得，並將檔案附加至郵件的類別。

### <a name="web-api"></a>網路 API

傳送附件的方式使用網頁 API 會傳送電子郵件使用 Web API 十分相似。 不過，請注意，在後面範例中，參數陣列必須包含此項目︰

    'files['.$fileName.']' => '@'.$filePath.'/'.$fileName

範例︰

    <?php

     $url = 'https://api.sendgrid.com/';
     $user = 'USERNAME';
     $pass = 'PASSWORD';
     
     $fileName = 'myfile';
     $filePath = dirname(__FILE__);

     $params = array(
         'api_user' => $user,
         'api_key' => $pass,
         'to' =>'john@contoso.com',
         'subject' => 'test of file sends',
         'html' => '<p> the HTML </p>',
         'text' => 'the plain text',
         'from' => 'anna@contoso.com',
         'files['.$fileName.']' => '@'.$filePath.'/'.$fileName
     );
     
     print_r($params);
     
     $request = $url.'api/mail.send.json';
     
     // Generate curl request
     $session = curl_init($request);
     
     // Tell curl to use HTTP POST
     curl_setopt ($session, CURLOPT_POST, true);
     
     // Tell curl that this is the body of the POST
     curl_setopt ($session, CURLOPT_POSTFIELDS, $params);
     
     // Tell curl not to return headers, but do return the response
     curl_setopt($session, CURLOPT_HEADER, false);
     curl_setopt($session, CURLOPT_RETURNTRANSFER, true);
     
     // obtain response
     $response = curl_exec($session);
     curl_close($session);
     
     // print everything out
     print_r($response);

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>如何︰ 使用篩選來啟用頁尾]、 [追蹤] 和 [狀況分析

SendGrid 提供透過 「 篩選 」 使用的其他電子郵件功能。 這些是可以新增至電子郵件來啟用特定的功能，例如啟用追蹤按一下、 Google analytics、 追蹤，訂閱等的設定。

可以使用 [篩選] 屬性，將篩選套用至郵件。 包含特定的篩選設定雜湊指定每個篩選。 下列範例會啟用 [頁尾] 篩選，並指定將附加至電子郵件訊息的底端的文字訊息。
此範例中，我們將使用[sendgrid php 文件庫]。
您可以使用[編輯器]來安裝文件庫︰
    
    php composer.phar require sendgrid/sendgrid 2.1.1

範例︰    

    <?php
     /*
      * This example is used for sendgrid-php V2.1.1 (https://github.com/sendgrid/sendgrid-php/tree/v2.1.1)
      */
     include "vendor/autoload.php";

     $email = new SendGrid\Email();
     // The list of addresses this message will be sent to
     // [This list is used for sending multiple emails using just ONE request to SendGrid]
     $toList = array('john@contoso.com', 'anna@contoso.com');

     // Specify the names of the recipients
     $nameList = array('Name 1', 'Name 2');

     // Used as an example of variable substitution
     $timeList = array('4 PM', '5 PM');

     // Set all of the above variables
     $email->setTos($toList);
     $email->addSubstitution('-name-', $nameList);
     $email->addSubstitution('-time-', $timeList);

     // Specify that this is an initial contact message
     $email->addCategory("initial");

     // You can optionally setup individual filters here, in this example, we have 
     // enabled the footer filter
     $email->addFilter('footer', 'enable', 1);
     $email->addFilter('footer', "text/plain", "Thank you for your business");
     $email->addFilter('footer', "text/html", "Thank you for your business");

     // The subject of your email
     $subject = 'Example SendGrid Email';

     // Where is this message coming from. For example, this message can be from 
     // support@yourcompany.com, info@yourcompany.com
     $from = 'someone@example.com';

     // If you do not specify a sender list above, you can specifiy the user here. If 
     // a sender list IS specified above, this email address becomes irrelevant.
     $to = 'john@contoso.com';

     # Create the body of the message (a plain-text and an HTML version). 
     # text is your plain-text email 
     # html is your html version of the email
     # if the receiver is able to view html emails then only the html
     # email will be displayed

     /*
      * Note the variable substitution here =)
      */
     $text = "
     Hello -name-,
     Thank you for your interest in our products. We have set up an appointment to call you at -time- EST to discuss your needs in more detail.
     Regards,
     Fred";

     $html = "
     <html> 
     <head></head>
     <body>
     <p>Hello -name-,<br>
     Thank you for your interest in our products. We have set up an appointment
     to call you at -time- EST to discuss your needs in more detail.

     Regards,

     Fred<br>
     </p>
     </body>
     </html>";

     // set subject
     $email->setSubject($subject);

     // attach the body of the email
     $email->setFrom($from);
     $email->setHtml($html);
     $email->addTo($to);
     $email->setText($text);

     // Your SendGrid account credentials
     $username = 'sendgridusername@yourdomain.com';
     $password = 'example';

     // Create SendGrid object
     $sendgrid = new SendGrid($username, $password);

     // send message
     $response = $sendgrid->send($email);

     print_r($response);

## <a name="next-steps"></a>後續步驟

現在，您學到的 SendGrid 電子郵件服務的基本概念，請遵循這些連結，瞭解更多。

-   SendGrid 文件︰ <https://sendgrid.com/docs>
-   SendGrid PHP 文件庫︰ <https://github.com/sendgrid/sendgrid-php>
-   Azure 客戶的 SendGrid 特殊優惠︰ <https://sendgrid.com/windowsazure.html>

如需詳細資訊，請參閱[PHP 開發人員中心](/develop/php/)。


  [https://sendgrid.com]: https://sendgrid.com
  [https://sendgrid.com/transactional-email/pricing]: https://sendgrid.com/transactional-email/pricing
  [special offer]: https://www.sendgrid.com/windowsazure.html
  [Packaging and Deploying PHP Applications for Azure]: http://msdn.microsoft.com/library/windowsazure/hh674499(v=VS.103).aspx
  [http://swiftmailer.org/download]: http://swiftmailer.org/download
  [捲曲函數]: http://php.net/curl
  [雲端架構電子郵件服務]: https://sendgrid.com/email-solutions
  [交易的電子郵件傳遞]: https://sendgrid.com/transactional-email
  [sendgrid php 文件庫]: https://github.com/sendgrid/sendgrid-php/tree/v2.1.1
  [編輯器]: https://getcomposer.org/download/
