<properties 
    pageTitle="如何從 Twilio (Java) 撥打電話 |Microsoft Azure" 
    description="瞭解如何從網頁上 Azure Java 應用程式中使用 Twilio 打電話。" 
    services="" 
    documentationCenter="java" 
    authors="devinrader" 
    manager="twilio" 
    editor="mollybos"/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="11/25/2014" 
    ms.author="microsofthelp@twilio.com"/>

# <a name="how-to-make-a-phone-call-using-twilio-in-a-java-application-on-azure"></a>如何撥打電話上 Azure Java 應用程式中使用 Twilio 

下列範例會顯示如何使用 Twilio 網頁上裝載於 Azure 撥打電話。 產生的應用程式會提示使用者使用電話的值，如以下的螢幕擷取畫面所示。

![使用 Twilio 和 Java azure 通話表單][twilio_java]

您必須執行下列動作，本主題中使用程式碼︰

1. 取得 Twilio 帳戶與驗證 token。 若要開始使用 Twilio，評估價格在[http://www.twilio.com/pricing][twilio_pricing]。 您可以在[https://www.twilio.com/try-twilio]註冊[try_twilio]。 關於 API Twilio 所提供的資訊，請參閱[http://www.twilio.com/api][twilio_api]。
2. 取得 Twilio JAR。 在[https://github.com/twilio/twilio-java][twilio_java_github]，您可以下載 GitHub 來源和建立您自己的 JAR，或下載預先建立的 JAR （包含或不相依性）。
使用預先建立的 TwilioJava 3.3.8-使用-相依性 JAR 撰寫本主題中的程式碼。
3. 將您 Java 建立路徑糖。
4. 如果您使用蝕建立此 Java 應用程式，包括 Twilio JAR 應用程式部署檔案中 （馬） 使用蝕的部署組件的功能。 如果您不使用蝕建立此 Java 應用程式，請確定 Twilio JAR 包括中 Azure 角色 Java 應用程式，相同，並新增至您的應用程式的類別路徑。
5. 確保您 cacerts 採用包含 MD5 指紋 67:CB:9 D Equifax 安全憑證授權單位憑證︰ C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (序列 35:DE:F4:CF，而 SHA1 指紋的步驟會 D2:32:09:AD:23:D3:14:23:21:74:E4:0 D: 7F:9 D: 62:13:97:86:63:3A)。 這是[https://api.twilio.com]的憑證授權單位 (CA) 憑證[twilio_api_service]服務，當您使用 Twilio Api 要求。 將這個 CA 憑證新增至您的 JDK cacert 存放區的相關資訊，請參閱[新增 Java CA 憑證儲存憑證][add_ca_cert]。

此外，熟悉的資訊在[建立 Hello 全球應用程式使用的蝕 Azure 工具組][azure_java_eclipse_hello_world]，或與裝載 Java Azure 中的應用程式，如果您不使用蝕其他技術，我們建議。

## <a name="create-a-web-form-for-making-a-call"></a>建立 web 表單的撥打電話

下列程式碼會顯示如何建立 web 表單擷取使用者資料撥打電話。 為了此範例中，建立新的動態 web 專案，名為**TwilioCloud**，，並**callform.jsp**已新增為 JSP 檔案。

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Automated call form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Make this call</b>.</p>
     <br/>
      <form action="makecall.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="callTo" value="" />
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="callFrom" value="" />
           </td>
         </tr>
         <tr>
           <td>Call message:</td>
           <td><input type="text" size=400 name="callText" value="Hello. This is the call text. Good bye." />
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Make this call" />
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## <a name="create-the-code-to-make-the-call"></a>建立要撥號的程式碼
下列程式碼，稱為使用者完成 callform.jsp 所顯示的表單時，會建立呼叫訊息，並產生通話。 為了此範例中，為**makecall.jsp** JSP 檔案]，已新增至**TwilioCloud**專案。 （使用您的 Twilio 帳戶和驗證權杖，而不是指定給**accountSID**和**authToken**下列程式碼的版面配置區值）。

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    import="java.util.*"
    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"
    pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Call processing happens here</title>
    </head>
    <body>
        <b>This is my make call page.</b><p/>
     <%
    try 
    {
         // Use your account SID and authentication token instead
         // of the placeholders shown here.
         String accountSID = "your_twilio_account";
         String authToken = "your_twilio_authentication_token";
     
         // Instantiate an instance of the Twilio client.     
         TwilioRestClient client;
         client = new TwilioRestClient(accountSID, authToken);

         // Retrieve the account, used later to retrieve the CallFactory.
         Account account = client.getAccount();

         // Display the client endpoint. 
         out.println("<p>Using Twilio endpoint " + client.getEndpoint() + ".</p>");
     
         // Display the API version.
         String APIVERSION = TwilioRestClient.DEFAULT_VERSION;
         out.println("<p>Twilio client API version is " + APIVERSION + ".</p>");
    
         // Retrieve the values entered by the user.
         String callTo = request.getParameter("callTo");  
         // The Outgoing Caller ID, used for the From parameter,
         // must have previously been verified with Twilio.
         String callFrom = request.getParameter("callFrom");
         String userText = request.getParameter("callText");
     
         // Replace spaces in the user's text with '%20', 
         // to make the text suitable for a URL.
         userText = userText.replace(" ", "%20");
     
         // Create a URL using the Twilio message and the user-entered text.
         String Url="http://twimlets.com/message";
         Url = Url + "?Message%5B0%5D=" + userText;
     
         // Display the message URL.
         out.println("<p>");
         out.println("The URL is " + Url);
         out.println("</p>");
    
         // Place the call From, To and URL values into a hash map. 
         HashMap<String, String> params = new HashMap<String, String>();
         params.put("From", callFrom);
         params.put("To", callTo);
         params.put("Url", Url);
     
         CallFactory callFactory = account.getCallFactory();
         Call call = callFactory.create(params);
         out.println("<p>Call status: " + call.getStatus()  + "</p>"); 
    } 
    catch (TwilioRestException e) 
    {
        out.println("<p>TwilioRestException encountered: " + e.getMessage() + "</p>");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    catch (Exception e) 
    {
        out.println("<p>Exception encountered: " + e.getMessage() + "");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    %>
    </body>
    </html>

除了進行通話，請 makecall.jsp 顯示 Twilio 端點、 API 版本，以及通話狀態。 範例是下列的螢幕擷取畫面︰

![使用 Twilio 和 Java azure 通話回覆][twilio_java_response]

## <a name="run-the-application"></a>執行應用程式
以下是高層級的步驟，以執行您的應用程式。詳細資料] 的步驟，請參閱[建立 Hello 全球應用程式使用的蝕 Azure 工具組][azure_java_eclipse_hello_world]。

1. 將您 TwilioCloud 馬匯出至 Azure **approot**資料夾。 
2. 修改**startup.cmd**解壓縮您 TwilioCloud 馬。
3. 編譯計算模擬器應用程式。
4. 在計算模擬器中進行部署。
5. 開啟瀏覽器中，並執行**http://localhost:8080/TwilioCloud/callform.jsp**。
6. 在表單中輸入值，**此撥打電話**，然後按一下然後中查看結果 makecall.jsp。

當您準備要部署至此 Azure，以供部署至雲端，重新編譯部署至 Azure，和在瀏覽器中執行 http://*your_hosted_name*.cloudapp.net/TwilioCloud/callform.jsp （取代*your_hosted_name*您值）。

## <a name="next-steps"></a>後續步驟
將此程式碼提供以顯示您使用 Azure Twilio Java 中的基本功能。 之前生產環境中部署 Azure，您可能會想要新增更多的錯誤處理或其他功能。 例如︰

* 而不是使用 web 表單，您可以使用儲存空間 Azure blob] 或 [SQL 資料庫來儲存電話號碼，然後呼叫文字。 使用儲存 Azure blob java 相關資訊，請參閱[如何使用從 Java Blob 儲存體服務][howto_blob_storage_java]。 使用 java SQL 資料庫的相關資訊，請參閱[使用 SQL 資料庫 java][howto_sql_azure_java]。
* 您可以使用**RoleEnvironment.getConfigurationSettings**擷取 Twilio 帳戶識別碼和驗證權杖，從您的部署設定的設定，而非硬式編碼 makecall.jsp 中的值。 為**RoleEnvironment**類別的相關資訊，請參閱[使用 Azure 服務執行階段文件庫中 JSP] [azure_runtime_jsp]和 Azure 服務執行階段套件文件在[http://dl.windowsazure.com/javadoc][azure_javadoc]。
* Makecall.jsp 程式碼指派 Twilio 提供 URL [http://twimlets.com/message][twimlet_message_url]， **Url**變數。 此 URL 提供如何繼續通話通知 Twilio Twilio 標記語言 (TwiML) 回應。 例如，則會傳回 TwiML 最多可以包含**&lt;說&gt;**唸來電者的文字會產生的動作。 而不是使用 Twilio 所提供的 URL，您可以建立自己的服務，來回應 Twilio 的要求。如需詳細資訊，請參閱[如何使用 Twilio 的語音及簡訊功能 Java][howto_twilio_voice_sms_java]。 深入瞭解 TwiML 位於[http://www.twilio.com/docs/api/twiml][twiml]，並深入瞭解**&lt;說&gt;**和其他 Twilio 動作，請參閱[http://www.twilio.com/docs/api/twiml/say][twilio_say]。
* 閱讀 Twilio 安全性指導方針，在[https://www.twilio.com/docs/security][twilio_docs_security]。

如需 Twilio 的詳細資訊，請參閱[https://www.twilio.com/docs][twilio_docs]。

## <a name="see-also"></a>另請參閱
* [如何使用 Twilio 的語音及 SMS Java 功能][howto_twilio_voice_sms_java]
* [將憑證新增至 Java CA 憑證存放區][add_ca_cert]

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_java_github]: http://github.com/twilio/twilio-java
[twimlet_message_url]: http://twimlets.com/message
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api_service]: http://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[azure_java_eclipse_hello_world]: http://msdn.microsoft.com/library/windowsazure/hh690944.aspx
[howto_twilio_voice_sms_java]: partner-twilio-java-how-to-use-voice-sms.md
[howto_blob_storage_java]: http://www.windowsazure.com/develop/java/how-to-guides/blob-storage/
[howto_sql_azure_java]: http://msdn.microsoft.com/library/windowsazure/hh749029.aspx
[azure_runtime_jsp]: http://msdn.microsoft.com/library/windowsazure/hh690948.aspx
[azure_javadoc]: http://dl.windowsazure.com/javadoc
[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say
[twilio_java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaCallForm.jpg
[twilio_java_response]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaMakeCall.jpg
