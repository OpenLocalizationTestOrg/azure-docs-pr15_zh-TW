<properties 
    pageTitle="如何使用語音及簡訊 (Java) Twilio |Microsoft Azure" 
    description="瞭解如何撥打電話及傳送簡訊訊息 Twilio API 服務上 Azure。 Java 以撰寫程式碼範例。" 
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

# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-java"></a>如何使用 Twilio 的語音及 SMS Java 功能

本指南將示範如何執行 Azure Twilio API 服務程式設計的一般工作。 涵蓋的案例包含撥打電話及傳送簡短訊息服務 (SMS) 訊息。 如需有關 Twilio 和您的應用程式中使用的語音及 SMS 的詳細資訊，請參閱 [[下一步](#NextSteps)] 區段。

## <a id="WhatIs"></a>什麼是 Twilio？
Twilio 是電話語音 web 服務 API，可讓您使用您現有的網站語言和技巧來建立語音及 SMS 應用程式。 Twilio 是協力廠商服務 （不 Azure 的功能和 Microsoft 產品）。

**Twilio 語音**可讓您的應用程式撥打與接聽電話。 **Twilio SMS**可讓您撥打及接收簡訊的應用程式。 **Twilio 用戶端**可讓您的應用程式，以啟用 [使用現有的網際網路連線，包括行動裝置連線的語音通訊。

## <a id="Pricing"></a>Twilio 價格和優惠
關於 Twilio 價格的資訊可在[Twilio 價格] [twilio_pricing]。 Azure 客戶接收[特殊優惠][special_offer]: 1000年文字免費信用卡或連入分鐘 1000年。 若要註冊這項優惠，或取得詳細資訊，請造訪[http://ahoy.twilio.com/azure][special_offer]。  

## <a id="Concepts"></a>概念
Twilio API 是 RESTful API 的應用程式提供語音及簡訊功能。 用戶端文件庫可在多種語言。如果是清單，請參閱[Twilio API 文件庫] [twilio_libraries]。

Twilio API 的重要是 Twilio 動詞和 Twilio 標記語言 (TwiML)。

### <a id="Verbs"></a>Twilio 動作
API 會使用 Twilio 動詞;例如，**&lt;說&gt;**動詞指示 Twilio 輪流電話傳送訊息。 

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
當您準備好要取得 Twilio 帳戶時，登入，[請嘗試 Twilio] [try_twilio]。 您可以開始使用免費的帳戶，並在稍後升級您的帳戶。

當您註冊 Twilio 帳戶時，您會收到的帳戶識別碼和驗證權杖。 同時將會需要進行 Twilio API 通話。 若要避免他人未經授權的存取您的帳戶，讓您驗證的權杖安全。 您的帳戶識別碼和驗證權杖為[Twilio 帳戶] 頁面上]的檢視 [twilio_account]，請在欄位**帳戶 SID**和**驗證權杖**，分別標記。

## <a id="create_app"></a>建立 Java 應用程式
1. 取得 Twilio JAR，並將其新增至您 Java 建立路徑和馬部署組件。 在[https://github.com/twilio/twilio-java][twilio_java]，您可以下載 GitHub 來源和建立您自己的 JAR，或下載預先建立的 JAR （包含或不相依性）。
2. 確保您 JDK **cacerts**採用包含 MD5 指紋 67:CB:9 D Equifax 安全憑證授權單位憑證︰ C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (序列 35:DE:F4:CF，而 SHA1 指紋的步驟會 D2:32:09:AD:23:D3:14:23:21:74:E4:0 D: 7F:9 D: 62:13:97:86:63:3A)。 這是[https://api.twilio.com]的憑證授權單位 (CA) 憑證[twilio_api_service]服務，當您使用 Twilio Api 要求。 如需有關確保您 JDK **cacerts**資訊採用包含正確的憑證，請參閱[新增 Java CA 憑證存放區的憑證][add_ca_cert]。

Java 使用 Twilio 用戶端文件庫的詳細的說明可如何[進行上 Azure Java 應用程式中的電話使用 Twilio][howto_phonecall_java]。

## <a id="configure_app"></a>設定您的應用程式使用 Twilio 文件庫
在您的程式碼，您可以新增**匯入**陳述式頂端的 [來源檔案 Twilio 套件或您想要使用應用程式中的類別。 

Java 來源檔案︰

    import com.twilio.*;
    import com.twilio.sdk.*;
    import com.twilio.sdk.resource.factory.*;
    import com.twilio.sdk.resource.instance.*;

Java 伺服器網頁 (JSP) 的來源檔案︰

    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"
根據哪些 Twilio 套件或類別您想要使用，您**匯入**陳述式可能會不同。

## <a id="howto_make_call"></a>如何︰ 用外撥打電話
以下示範如何用使用**CallFactory**類別外撥打電話。 將此程式碼也會使用 Twilio 所提供的網站，傳回 Twilio 標記語言 (TwiML) 回應。 取代值****] 及****電話號碼，並確保您之前執行程式碼 Twilio 帳戶驗證**的**電話號碼。

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Retrieve the account, used later to create an instance of the CallFactory.
    Account account = client.getAccount();

    // Use the Twilio-provided site for the TwiML response.
    String Url="http://twimlets.com/message";
    Url = Url + "?Message%5B0%5D=Hello%20World";

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN"); // Use your own value for the second parameter.
    params.put("To", "NNNNNNNNNN");   // Use your own value for the second parameter.
    params.put("Url", Url);

    // Create an instance of the CallFactory class.
    CallFactory callFactory = account.getCallFactory();

    // Make the call.
    Call call = callFactory.create(params);

如需有關**CallFactory.create**方法傳遞的參數的詳細資訊，請參閱[http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls]。

如上所述，將此程式碼會使用 Twilio 所提供的網站，傳回 TwiML 回應。 您改為無法使用您自己的網站，提供 TwiML 回應。如需詳細資訊，請參閱[Azure Java 應用程式中提供 TwiML 回應的方式](#howto_provide_twiml_responses)。

## <a id="howto_send_sms"></a>如何︰ 傳送簡訊形式的訊息
以下示範如何傳送簡訊訊息使用**SmsFactory**類別。 **從**數字， **4155992671**，由 Twilio 試用帳戶傳送簡訊提供。 **以**數字必須驗證 Twilio 帳戶之前執行程式碼。

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Retrieve the account, used later to create an instance of the SmsFactory.
    Account account = client.getAccount();

    // Send an SMS message.
    MessageFactory messageFactory = account.getMessageFactory();
    
    List<NameValuePair> params = new ArrayList<NameValuePair>();
    params.add(new BasicNameValuePair("To", "+14159352345")); // Replace with a valid phone number for your account.
    params.add(new BasicNameValuePair("From", "+14158141829")); // Replace with a valid phone number for your account.
    params.add(new BasicNameValuePair("Body", "Where's Wallace?"));
    
    Message sms = messageFactory.create(params);
        
如需有關**SmsFactory.create**方法傳遞的參數的詳細資訊，請參閱[http://www.twilio.com/docs/api/rest/sending-sms][twilio_rest_sending_sms]。

## <a id="howto_provide_twiml_responses"></a>如何︰ 提供 TwiML 回應從您的網站
當您的應用程式啟動通話 Twilio api 時，例如透過**CallFactory.create**的方法，Twilio 會傳送邀請預期會傳回 TwiML 回應的 url。 上述範例使用 Twilio 所提供的 URL [http://twimlets.com/message][twimlet_message_url]。 （時 TwiML Web 服務專為使用設計，您可以檢視 TwiML 在瀏覽器中。 例如，按一下 [ [http://twimlets.com/message] [twimlet_message_url]若要查看空**&lt;回應&gt;**項目。另一個範例中，按一下 [ [http://twimlets.com/message?Message%5B0%5D=Hello%20World] [twimlet_message_url_hello_world]若要查看**&lt;回應&gt;**包含項目**&lt;說&gt;**項目。)

不要依賴 Twilio 所提供的 URL，您可以建立自己的 URL 網站會傳回 HTTP 回應。 您可以在 HTTP 回應; 傳回任何語言建立網站本主題假設您會裝載於 JSP 頁面中的 URL。

下列 JSP 頁面產生 TwiML 回應指出**Hello 全球**通話。

    <%@ page contentType="text/xml" %>
    <Response> 
        <Say>Hello World</Say>
    </Response>

下列 JSP 頁面會顯示一些文字，有數種暫停，則資訊 Twilio API 版本和 Azure 角色名稱的 TwiML 回應。


    <%@ page contentType="text/xml" %>
    <Response> 
        <Say>Hello from Azure</Say>
        <Pause></Pause>
        <Say>The Twilio API version is <%= request.getParameter("ApiVersion") %>.</Say>
        <Say>The Azure role name is <%= System.getenv("RoleName") %>.</Say>
        <Pause></Pause>
        <Say>Good bye.</Say>
    </Response>

**ApiVersion**參數中會有 Twilio 語音要求 （不 SMS 要求）。 若要查看可用的要求參數 Twilio 語音和 SMS 要求，請參閱<https://www.twilio.com/docs/api/twiml/twilio_request>和<https://www.twilio.com/docs/api/twiml/sms/twilio_request>，分別。 **角色名稱**環境變數是可用的 Azure 部署的一部分。 (如果您想要新增自訂的環境變數，讓他們無法從挑選**System.getenv**，請參閱[其他角色設定的設定]環境變數節[misc_role_config_settings]。)

一旦您有提供 TwiML 回應您 JSP 頁面設定，作為傳入**CallFactory.create**方法的 URL JSP 頁面的 URL。 例如，如果您有 Web 應用程式名稱為 MyTwiML 部署至 Azure 裝載的服務，JSP 頁面名稱是 mytwiml.jsp，URL 可以傳遞給**CallFactory.create**下列所示︰

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN");
    params.put("To", "NNNNNNNNNN");
    params.put("Url", "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    CallFactory callFactory = account.getCallFactory();
    Call call = callFactory.create(params);

另一個選項以 TwiML 回應是透過**TwiMLResponse**類別，也就是**com.twilio.sdk.verbs**套件中可用。

如需瞭解如何使用 Twilio java Azure 中的詳細資訊，請參閱[如何進行上 Azure Java 應用程式中的電話使用 Twilio][howto_phonecall_java]。

## <a id="AdditionalServices"></a>如何︰ 使用其他 Twilio 服務
此處所示的範例，除了 Twilio 提供可用來運用從 Azure 應用程式的其他 Twilio 功能的 web 式 Api。 完整的詳細資訊，請參閱[Twilio API 文件] [twilio_api_documentation]。

## <a id="NextSteps"></a>後續步驟
現在，您學到的 Twilio 服務的基本概念，請遵循這些連結，深入瞭解︰

* [Twilio 安全性指導方針] [twilio_security_guidelines]
* [Twilio 如何和範例程式碼] [twilio_howtos]
* [Twilio 快速入門教學課程][twilio_quickstarts] 
* [在 GitHub Twilio] [twilio_on_github]
* [與 Twilio 支援] [twilio_support]

[twilio_java]: https://github.com/twilio/twilio-java
[twilio_api_service]: https://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[howto_phonecall_java]: partner-twilio-java-phone-call-example.md
[misc_role_config_settings]: http://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls
[twilio_rest_sending_sms]: http://www.twilio.com/docs/api/rest/sending-sms
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
