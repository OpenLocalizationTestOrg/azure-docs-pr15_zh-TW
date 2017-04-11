<properties 
    pageTitle="如何使用 SendGrid 電子郵件服務 (.NET) |Microsoft Azure" 
    description="瞭解如何傳送 Azure 中的 [SendGrid 電子郵件服務的電子郵件。 範例以 C# 撰寫的程式碼，並使用.NET API。" 
    services="app-service\web" 
    documentationCenter=".net" 
    authors="thinkingserious" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="01/14/2016" 
    ms.author="team-pi@sendgrid.com"/>





# <a name="how-to-send-email-using-sendgrid-with-azure"></a>如何使用 SendGrid 與 Azure 傳送電子郵件


## <a name="overview"></a>概觀

本文將示範如何執行 Azure SendGrid 電子郵件服務與程式的一般工作。 範例撰寫 C\#
並使用.NET API。 涵蓋的案例包含**建構電子郵件**、**傳送電子郵件**、**新增附件**和**使用篩選**。 如需有關 SendGrid 和傳送電子郵件的詳細資訊，請參閱[下一步][]] 區段。

## <a name="what-is-the-sendgrid-email-service"></a>什麼是 SendGrid 電子郵件服務？

SendGrid 是[以雲端為基礎的電子郵件服務]，提供可靠的[交易的電子郵件傳遞]、 延展性和彈性的 Api，讓您輕鬆地自訂整合以及即時分析。 一般 SendGrid 使用情況包括︰

-   自動將回條傳送給客戶。
-   管理通訊群組清單傳送給客戶，每月 e fliers 和特殊優惠。
-   收集項目，例如 [封鎖的電子郵件與客戶回應的即時的指標。
-   產生報告，找出趨勢。
-   轉接客戶查詢。
-   正在處理的內送電子郵件。

如需詳細資訊，請參閱[https://sendgrid.com](https://sendgrid.com)或我們[C# 程式庫][sendgrid 和 csharp]

## <a name="create-a-sendgrid-account"></a>建立 SendGrid 帳戶

[AZURE.INCLUDE [sendgrid-sign-up](../../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-net-class-library"></a>參考 SendGrid.NET 類別庫

[SendGrid NuGet 套件](https://www.nuget.org/packages/Sendgrid)是最簡單的方法，取得 SendGrid API，並設定您的應用程式與所有的相依性。 NuGet 是包含在 Microsoft Visual Studio 2015，可讓您更容易安裝和更新文件庫和工具 Visual Studio 副檔名。 

> [AZURE.NOTE] 若要安裝 NuGet，如果您執行的 Visual Studio 的版本早於 Visual Studio 2015，請造訪[http://www.nuget.org](http://www.nuget.org)，並按一下 [**安裝 NuGet** ] 按鈕。

若要安裝 SendGrid NuGet 套件應用程式中，執行下列動作︰

1.  建立新的專案。

    ![建立新專案][create-new-project]

2.  選取 [範本]。

    ![選取範本][select-a-template]

3.  在**方案總管]**中，**參照**，以滑鼠右鍵按一下，然後按一下 [**管理 NuGet 套件**]。

4.  搜尋**SendGrid** ，並在 [結果] 清單中選取**SendGrid**項目。

    ![SendGrid NuGet 套件][SendGrid-NuGet-package]

5.  按一下 [完成安裝作業，[**安裝**]，然後關閉 [這個對話方塊。

SendGrid 的.NET 類別文件庫，稱為**SendGridMail**。 包含下列命名空間︰

-   建立及使用電子郵件的項目**SendGridMail** 。
-   傳送電子郵件使用**SMTP**通訊協定] 或 [HTTP 1.1 通訊協定的**網頁/其餘**的**SendGridMail.Transport** 。

移至任何 C 頂端新增下列程式碼命名空間宣告\#您要以程式控制方式存取 SendGrid 電子郵件服務的檔案。
**System.Net**和**System.Net.Mail**是因為其包含您會經常使用 SendGrid Api 類型都包含在內的.NET Framework 命名空間。

    using System;
    using System.Net;
    using System.Net.Mail;
    using SendGrid;

## <a name="how-to-create-an-email"></a>如何︰ 建立電子郵件

若要建立電子郵件訊息中使用**SendGridMessage**物件。 訊息物件建立之後，您可以設定屬性和方法，包括電子郵件寄件者、 電子郵件收件者和主旨和電子郵件的本文。

下列範例會示範如何建立一個完整的電子郵件物件︰

    // Create the email object first, then add the properties.
    var myMessage = new SendGridMessage();

    // Add the message properties.
    myMessage.From = new MailAddress("john@example.com");

    // Add multiple addresses to the To field.
    List<String> recipients = new List<String>
    {
        @"Jeff Smith <jeff@example.com>",
        @"Anna Lidman <anna@example.com>",
        @"Peter Saddow <peter@example.com>"
    };

    myMessage.AddTo(recipients);

    myMessage.Subject = "Testing the SendGrid Library";

    //Add the HTML and Text bodies
    myMessage.Html = "<p>Hello World!</p>";
    myMessage.Text = "Hello World plain text!";

如需所有屬性和**SendGrid**類型支援的方法的詳細資訊，請參閱[sendgrid 和 csharp][]上 GitHub。

## <a name="how-to-send-an-email"></a>如何︰ 傳送電子郵件

建立電子郵件訊息之後, 您可以將它使用 SendGrid 所提供的 Web API。 或者，您可能會[使用。在文件庫中的網路的內建](https://sendgrid.com/docs/Code_Examples/csharp.html)。

傳送電子郵件會要求您提供您 （使用者名稱和密碼） SendGrid 帳戶認證或 SendGrid API 金鑰。 API 金鑰是慣用的方法。 如需有關如何設定 API 金鑰的詳細資訊，請造訪問題的[文件](https://sendgrid.com/docs/Classroom/Send/api_keys.html)

按一下 [設定，並新增金鑰/值組，在 [應用程式設定] 下的，您可能會透過 Azure 入口網站儲存這些認證。

 ![Azure 應用程式設定][azure_app_settings]

 然後，您可能會存取，如下所示︰ 
    
    var username = System.Environment.GetEnvironmentVariable("SENDGRID_USERNAME"); 
    var pswd = System.Environment.GetEnvironmentVariable("SENDGRID_PASSWORD");
    var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");

使用認證︰
    
    // Create network credentials to access your SendGrid account
    var username = "your_sendgrid_username";
    var pswd = "your_sendgrid_password";

    var credentials = new NetworkCredential(username, pswd);
    // Create an Web transport for sending email.
    var transportWeb = new Web(credentials);

使用 API 金鑰︰

    var apiKey = "your_sendgrid_api_key";  
    // create a Web transport, using API Key
    var transportWeb = new Web(apiKey);


下列範例會顯示如何傳送郵件使用 Web API。

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Create credentials, specifying your user name and password.
    var credentials = new NetworkCredential("username", "password");

    // Create an Web transport for sending email.
    var transportWeb = new Web(credentials);

    // Send the email, which returns an awaitable task.
    transportWeb.DeliverAsync(myMessage);

    // If developing a Console Application, use the following
    // transportWeb.DeliverAsync(mail).Wait();

## <a name="how-to-add-an-attachment"></a>如何︰ 新增附件

附件可以新增至郵件呼叫**AddAttachment**方法，並指定 [名稱] 和 [您想要附加的檔案路徑。
您可以包含多個附件，當您想要附加的每個檔案，則可電話這個方法。 下列範例會示範將附件新增至郵件︰

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.AddAttachment(@"C:\file1.txt");
    
您也可以從資料的**資料流**中新增附件。 可以在呼叫相同的方法為上述**AddAttachment**，但傳遞的資料，資料流中完成和檔名您想要顯示與郵件。 在此情況下，您必須新增 System.IO 文件庫。

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    using (var attachmentFileStream = new FileStream(@"C:\file.txt", FileMode.Open))
    {
        myMessage.AddAttachment(attachmentFileStream, "My Cool File.txt");
    }


## <a name="how-to-use-apps-to-enable-footers-tracking-and-analytics"></a>如何︰ 使用應用程式啟用頁尾]、 [追蹤] 和 [狀況分析

SendGrid 提供額外的電子郵件功能，透過應用程式使用。 這些是可以新增至電子郵件來啟用特定的功能，例如追蹤按一下 Google analytics、 追蹤，訂閱的設定，依此類推。 應用程式的完整清單，請參閱[應用程式設定][]。

使用方法實作**SendGrid**課程的一部分**SendGrid**電子郵件訊息，可以套用應用程式。

下列範例會示範頁尾，然後按一下 [追蹤篩選︰

### <a name="footer"></a>頁尾

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Add a footer to the message.
    myMessage.EnableFooter("PLAIN TEXT FOOTER", "<p><em>HTML FOOTER</em></p>");

### <a name="click-tracking"></a>按一下 [追蹤

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Html = "<p><a href=\"http://www.example.com\">Hello World Link!</a></p>";
    myMessage.Text = "Hello World!";
    
    // true indicates that links in plain text portions of the email 
    // should also be overwritten for link tracking purposes. 
    myMessage.EnableClickTracking(true);

## <a name="how-to-use-additional-sendgrid-services"></a>如何︰ 使用其他 SendGrid 服務

SendGrid 提供 web Api 及 webhooks 可供您能使用其他 SendGrid 功能，從 Azure 應用程式。 完整的詳細資訊，請參閱[SendGrid API 文件][]。

## <a name="next-steps"></a>後續步驟

現在，您學到的 SendGrid 電子郵件服務的基本概念，請遵循這些連結，瞭解更多。

*   SendGrid C\#文件庫 repo: [sendgrid 和 csharp][]
*   SendGrid API 文件︰ <https://sendgrid.com/docs>
*   Azure 客戶的 SendGrid 特殊優惠︰ [https://sendgrid.com](https://sendgrid.com)

  [後續步驟]: #next-steps
  [What is the SendGrid Email Service?]: #whatis
  [Create a SendGrid Account]: #createaccount
  [Reference the SendGrid .NET Class Library]: #reference
  [How to: Create an Email]: #createemail
  [How to: Send an Email]: #sendemail
  [How to: Add an Attachment]: #addattachment
  [How to: Use Filters to Enable Footers, Tracking, and Analytics]: #usefilters
  [How to: Use Additional SendGrid Services]: #useservices
  
  [special offer]: https://www.sendgrid.com/windowsazure.html
  
  [create-new-project]: ./media/sendgrid-dotnet-how-to-send-email/create_new_project.png
  [select-a-template]: ./media/sendgrid-dotnet-how-to-send-email/select_a_template.png
  [SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid_nuget.png
  [azure_app_settings]: ./media/sendgrid-dotnet-how-to-send-email/app_settings.png
  [sendgrid 和 csharp]: https://github.com/sendgrid/sendgrid-csharp
  [SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
  [應用程式設定]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
  [SendGrid API 文件]: https://sendgrid.com/docs
  
  [雲端架構電子郵件服務]: https://sendgrid.com/email-solutions
  [交易的電子郵件傳遞]: https://sendgrid.com/transactional-email
 
