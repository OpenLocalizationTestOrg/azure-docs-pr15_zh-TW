<properties 
    pageTitle="store-sendgrid-java-how-to-send-email-example" 
    description="如何使用從 Java SendGrid Azure 部署中傳送電子郵件" 
    services="" 
    documentationCenter="java" 
    authors="thinkingserious" 
    manager="sendgrid" 
    editor="mollybos"/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="10/30/2014" 
    ms.author="vibhork;dominic.may@sendgrid.com;elmer.thomas@sendgrid.com"/>

# <a name="how-to-send-email-using-sendgrid-from-java-in-an-azure-deployment"></a>如何使用從 Java SendGrid Azure 部署中傳送電子郵件

下列範例會顯示如何使用 SendGrid 網頁上裝載於 Azure 傳送電子郵件。 產生的應用程式會提示使用者的電子郵件的值，如以下的螢幕擷取畫面所示。

![電子郵件表單][emailform]

產生的電子郵件將類似以下的螢幕擷取畫面。

![電子郵件訊息][emailsent]

您必須執行下列動作，本主題中使用程式碼︰

1. 取得 javax.mail （每瓶），例如從<http://www.oracle.com/technetwork/java/javamail/index.html>。
2. 將您 Java 建立路徑 （每瓶）。
3. 如果您使用蝕建立此 Java 應用程式，您可以包含 SendGrid 文件庫中的應用程式部署檔案 （馬） 使用蝕的部署組件的功能。 如果您不使用蝕建立此 Java 應用程式，請確定是 Java 應用程式中，為相同的 Azure 角色中共用文件庫，並將其新增至您的應用程式的類別路徑。


您也必須自己 SendGrid 使用者名稱和密碼，才能傳送電子郵件。 若要開始使用 SendGrid，請參閱[如何使用從 Java SendGrid 傳送電子郵件](store-sendgrid-java-how-to-send-email.md)。

此外，易用的資訊在[建立 Azure 中蝕 Hello 全球應用程式](http://msdn.microsoft.com/library/windowsazure/hh690944)，或其他技術裝載 Java Azure 中的應用程式，如果您不使用蝕，我們建議。

## <a name="create-a-web-form-for-sending-email"></a>建立 web 表單傳送電子郵件

下列程式碼會顯示如何建立 web 表單來擷取傳送電子郵件的使用者資料。 為了這個內容的詳細資訊，JSP 檔名為**emailform.jsp**。

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Email form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Send this email</b>.</p>
     <br/>
      <form action="sendemail.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="emailTo">
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="emailFrom">
           </td>
         </tr>
         <tr>
           <td>Subject:</td>
           <td><input type="text" size=100 name="emailSubject" value="My email subject">
           </td>
         </tr>
         <tr>
           <td>Text:</td>
           <td><input type="text" size=400 name="emailText" value="Hello,<p>This is my message.</p>Thank you." />
           </td>
         </tr>
         <tr>
           <td>SendGrid user name:</td>
           <td><input type="text" name="sendGridUser">
           </td>
         </tr>
         <tr>
           <td>SendGrid password:</td>
           <td><input type="password" name="sendGridPassword">
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Send this email">
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## <a name="create-the-code-to-send-the-email"></a>建立要傳送電子郵件的程式碼

下列程式碼，稱為當您完成 emailform.jsp] 中的表單時，會建立電子郵件訊息，並將它傳送。 為了這個內容的詳細資訊，JSP 檔名為**sendemail.jsp**。

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" import="javax.activation.*, javax.mail.*, javax.mail.internet.*, java.util.Date, java.util.Properties" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Email processing happens here</title>
    </head>
    <body>
        <b>This is my send mail page.</b><p/>
     <%
     
     final String sendGridUser = request.getParameter("sendGridUser");
     final String sendGridPassword = request.getParameter("sendGridPassword");
     
     class SMTPAuthenticator extends Authenticator
     {
       public PasswordAuthentication getPasswordAuthentication()
       {
            String username = sendGridUser;
            String password = sendGridPassword;
          
            return new PasswordAuthentication(username, password);   
       }
     }
     try
     {
         
         // The SendGrid SMTP server.
         String SMTP_HOST_NAME = "smtp.sendgrid.net";
    
         Properties properties;
        
         properties = new Properties();
         
         // Specify SMTP values.
         properties.put("mail.transport.protocol", "smtp");
         properties.put("mail.smtp.host", SMTP_HOST_NAME);
         properties.put("mail.smtp.port", 587);
         properties.put("mail.smtp.auth", "true");
         
         // Display the email fields entered by the user. 
         out.println("Value entered for email Subject: " + request.getParameter("emailSubject") + "<br/>");        
         out.println("Value entered for email      To: " + request.getParameter("emailTo") + "<br/>");
         out.println("Value entered for email    From: " + request.getParameter("emailFrom") + "<br/>");
         out.println("Value entered for email    Text: " + "<br/>" + request.getParameter("emailText") + "<br/>");
    
         // Create the authenticator object.
         Authenticator authenticator = new SMTPAuthenticator();
         
         // Create the mail session object.
         Session mailSession;
         mailSession = Session.getDefaultInstance(properties, authenticator);
         
         // Display debug information to stdout, useful when using the
         // compute emulator during development.
         mailSession.setDebug(true);
    
         // Create the message and message part objects.
         MimeMessage message;
         Multipart multipart;
         MimeBodyPart messagePart; 
         
         message = new MimeMessage(mailSession);
         
         multipart = new MimeMultipart("alternative");
         messagePart = new MimeBodyPart();
         messagePart.setContent(request.getParameter("emailText"), "text/html");
         multipart.addBodyPart(messagePart);            
    
         // Specify the email To, From, Subject and Content. 
         message.setFrom(new InternetAddress(request.getParameter("emailFrom")));
         message.addRecipient(Message.RecipientType.TO, new InternetAddress(request.getParameter("emailTo")));
         message.setSubject(request.getParameter("emailSubject")); 
         message.setContent(multipart);
         
         // Uncomment the following if you want to add a footer.
         // message.addHeader("X-SMTPAPI", "{\"filters\": {\"footer\": {\"settings\": {\"enable\":1,\"text/html\": \"<html>This is my <b>email footer</b>.</html>\"}}}}");
    
         // Uncomment the following if you want to enable click tracking.
         // message.addHeader("X-SMTPAPI", "{\"filters\": {\"clicktrack\": {\"settings\": {\"enable\":1}}}}");
         
         Transport transport;
         transport = mailSession.getTransport();
         // Connect the transport object.
         transport.connect();
         // Send the message.
         transport.sendMessage(message,  message.getRecipients(Message.RecipientType.TO));
         // Close the connection.
         transport.close();
     
        out.println("<p>Email processing completed.</p>");
         
     }
     catch (Exception e)
     {
         out.println("<p>Exception encountered: " + 
                            e.getMessage()     +
                            "</p>");   
     }
    %>
    
    </body>
    </html>

除了傳送電子郵件，emailform.jsp，請提供使用者; 的結果範例是下列的螢幕擷取畫面︰

![傳送郵件的結果][emailresult]

## <a name="next-steps"></a>後續步驟

部署至計算模擬器應用程式和在瀏覽器中執行 emailform.jsp 表單中輸入值、**傳送此電子郵件**，請然後按一下然後查看 [sendemail.jsp 中的結果。

以顯示您如何使用 java SendGrid Azure 上提供此程式碼。 之前生產環境中部署 Azure，您可能會想要新增更多的錯誤處理或其他功能。 例如︰ 

* 您可以使用儲存空間 Azure blob] 或 [SQL 資料庫來儲存電子郵件地址及電子郵件訊息，而不是使用 web 表單。 使用儲存 Azure blob java 相關資訊，請參閱[從 Java Blob 儲存體服務使用方式](https://azure.microsoft.com/develop/java/how-to-guides/blob-storage/)。 使用 SQL 資料庫 java 相關資訊，請參閱[Java 中使用 SQL 資料庫](https://azure.microsoft.com/develop/java/how-to-guides/using-sql-azure-in-java/)。
* 您可以使用`RoleEnvironment.getConfigurationSettings`從您的部署設定的設定，而不是使用 web 表單來擷取這些值擷取 SendGrid 使用者名稱和密碼。 如需的資訊`RoleEnvironment`課程，請參閱[使用 Azure 服務執行階段文件庫中 JSP](http://msdn.microsoft.com/library/windowsazure/hh690948)及<http://dl.windowsazure.com/javadoc>的 Azure 服務執行階段封裝文件。
* 如需有關使用 java SendGrid 的詳細資訊，請參閱[如何使用從 Java SendGrid 傳送電子郵件](store-sendgrid-java-how-to-send-email.md)。

[emailform]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailform.jpg
[emailsent]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailSent.jpg
[emailresult]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaResult.jpg
