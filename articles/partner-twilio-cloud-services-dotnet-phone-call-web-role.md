<properties 
    pageTitle="如何從 Twilio (.NET) 撥打電話 |Microsoft Azure" 
    description="瞭解如何撥打電話和 Azure 上的 [傳送簡訊，Twilio API 服務。 .NET 撰寫程式碼範例。" 
    services="" 
    documentationCenter=".net" 
    authors="devinrader" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="05/04/2016" 
    ms.author="microsofthelp@twilio.com"/>




# <a name="how-to-make-a-phone-call-using-twilio-in-a-web-role-on-azure"></a>如何撥打電話使用 Twilio 在 web 上 Azure 角色

本指南示範如何使用 Twilio 網頁上裝載於 Azure 撥打電話。 下圖所示，產生的應用程式會提示使用者打電話值。

![使用 Twilio 和 ASP.NET azure 通話表單][twilio_dotnet_basic_form]

## <a name="twilio-prereqs"></a>必要條件

您將需要執行下列動作，本主題中使用程式碼︰

1. 取得 Twilio 帳戶與驗證 token。 若要開始使用 Twilio，註冊[https://www.twilio.com/try-twilio][try_twilio]。 您可以評估價格在[http://www.twilio.com/pricing][twilio_pricing]。 關於 API Twilio 所提供的資訊，請參閱[http://www.twilio.com/voice/api][twilio_api]。
2. 新增 Twilio.NET 文件庫至您網站的角色。 本主題稍後的請參閱 「 若要新增 Twilio 文件庫至您網站的角色專案，」。

您應該熟悉 Azure 建立基本的 web 角色。

## <a name="howtocreateform"></a>如何︰ 建立 web 表單的撥打電話

<a id="use_nuget"></a>若要新增至網頁角色專案的 Twilio 文件庫︰

1.  Visual Studio 中開啟您的方案。
2.  以滑鼠右鍵按一下 [**參考資料**。
3.  按一下 [**管理 NuGet 套件**]。
4.  按一下 [**線上**]。
5.  在 [搜尋線上] 方塊中，輸入*twilio*。
6.  Twilio 套件上，按一下 [**安裝**]。

下列程式碼會顯示如何建立 web 表單擷取使用者資料撥打電話。 在此範例中，建立名為**TwilioCloud** ASP.NET 網頁角色。

    <%@ Page Title="Home Page" Language="C#" MasterPageFile="~/Site.master"
        AutoEventWireup="true" CodeBehind="Default.aspx.cs"
        Inherits="WebRole1._Default" %>

    <asp:Content ID="HeaderContent" runat="server" ContentPlaceHolderID="HeadContent">
    </asp:Content>
    <asp:Content ID="BodyContent" runat="server" ContentPlaceHolderID="MainContent">
        <div>
            <asp:BulletedList ID="varDisplay" runat="server" BulletStyle="NotSet">
            </asp:BulletedList>
        </div>
        <div>
            <p>Fill in all fields and click <b>Make this call</b>.</p>
            <div>
                To:<br /><asp:TextBox ID="toNumber" runat="server" /><br /><br />
                Message:<br /><asp:TextBox ID="message" runat="server" /><br /><br />
                <asp:Button ID="callpage" runat="server" Text="Make this call"
                    onclick="callpage_Click" />
            </div>
        </div>
    </asp:Content>

## <a id="howtocreatecode"></a>如何︰ 建立撥號的程式碼
下列程式碼，稱為使用者完成表單時，會建立呼叫訊息，並產生通話。 在此範例中，執行程式碼] 按鈕的 onclick 事件處理常式中的表單。 （使用您的 Twilio 帳戶和驗證權杖，而不是指定給**accountSID**和**authToken**下列程式碼的版面配置區值）。

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Web.UI;
    using System.Web.UI.WebControls;
    using Twilio;

    namespace WebRole1
    {
        public partial class _Default : System.Web.UI.Page
        {
            protected void Page_Load(object sender, EventArgs e)
            {

            }

            protected void callpage_Click(object sender, EventArgs e)
            {
                // Call porcessing happens here.

                // Use your account SID and authentication token instead of
                // the placeholders shown here.
                string accountSID = "ACNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";
                string authToken =  "NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";

                // Instantiate an instance of the Twilio client.
                TwilioRestClient client;
                client = new TwilioRestClient(accountSID, authToken);

                // Retrieve the account, used later to retrieve the
                Twilio.Account account = client.GetAccount();
                string APIversuion = client.ApiVersion;
                string TwilioBaseURL = client.BaseUrl;

                this.varDisplay.Items.Clear();
                if (this.toNumber.Text == "" || this.message.Text == "")
                {
                    this.varDisplay.Items.Add(
                            "You must enter a phone number and a message.");
                }
                else
                {
                    // Retrieve the values entered by the user.
                    string to = this.toNumber.Text;
                    string myMessage = this.message.Text;

                    // Create a URL using the Twilio message and the user-entered
                    // text. You must replace spaces in the user's text with '%20'
                    // to make the text suitable for a URL.
                    String Url = "http://twimlets.com/message?Message%5B0%5D="
                            + myMessage.Replace(" ", "%20");

                    // Display the endpoint, API version, and the URL for the message.
                    this.varDisplay.Items.Add("Using Twilio endpoint "
                        + TwilioBaseURL);
                    this.varDisplay.Items.Add("Twilioclient API Version is "
                        + APIversuion);
                    this.varDisplay.Items.Add("The URL is " + Url);

                    // Instantiate the call options that are passed
                    // to the outbound call.
                    CallOptions options = new CallOptions();

                    // Set the call From, To, and URL values.                    
                    options.From = "+14155992671";
                    options.To = to;
                    options.Url = Url;

                    // Place the call.
                    var call = client.InitiateOutboundCall(options);
                    this.varDisplay.Items.Add("Call status: " + call.Status);
                }
            }
        }
    }

進行通話時，，並顯示 Twilio 端點、 API 版本，以及通話狀態。 以下螢幕擷取畫面顯示輸出範例執行。

![使用 Twilio 和 ASP.NET azure 通話回覆][twilio_dotnet_basic_form_output]

深入瞭解 TwiML 位於[http://www.twilio.com/docs/api/twiml][twiml]。 深入瞭解&lt;說&gt;和其他 Twilio 動作，請參閱[http://www.twilio.com/docs/api/twiml/say][twilio_say]。

## <a id="nextsteps"></a>後續步驟
將此程式碼提供以顯示您使用 Twilio Azure ASP.NET web 角色中的基本功能。 之前生產環境中部署 Azure，您可能會想要新增更多的錯誤處理或其他功能。 例如︰

* 而不是使用 web 表單，您可以使用 Azure Blob 儲存體或 Azure SQL 資料庫執行個體來儲存電話號碼，然後呼叫文字。 Azure 中使用 blob 相關資訊，請參閱[如何使用.net Azure Blob 儲存體服務][howto_blob_storage_dotnet]。 使用 SQL 資料庫的相關資訊，請參閱[如何使用.NET 應用程式中的 Azure SQL 資料庫][howto_sql_azure_dotnet]。
* 您可以使用 RoleEnvironment.getConfigurationSettings 擷取 Twilio 帳戶識別碼和驗證權杖，從您的部署設定的設定，而非硬式編碼表單中的值。 RoleEnvironment 類別的相關資訊，請參閱[Microsoft.WindowsAzure.ServiceRuntime 命名空間][azure_runtime_ref_dotnet]。
* 閱讀 Twilio 安全性指導方針，在[https://www.twilio.com/docs/security][twilio_docs_security]。
* 深入瞭解 Twilio 在[https://www.twilio.com/docs][twilio_docs]。

##<a name="seealso"></a>另請參閱
* [如何使用 Twilio 語音和 azure SMS 功能](twilio-dotnet-how-to-use-for-voice-sms.md)

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/voice/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#

[twilio_dotnet_basic_form]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form.png
[twilio_dotnet_basic_form_output]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form_output.png

[twiml]: http://www.twilio.com/docs/api/twiml



[howto_twilio_voice_sms_dotnet]: /develop/net/how-to-guides/twilio/

[howto_blob_storage_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/blob-storage/

[howto_sql_azure_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/sql-database/


[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say


[azure_runtime_ref_dotnet]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.serviceruntime.aspx
