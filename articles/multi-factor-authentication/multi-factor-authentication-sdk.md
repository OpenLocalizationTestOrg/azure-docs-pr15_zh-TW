<properties 
    pageTitle="整合您的內部部署識別與 Azure Active Directory。"
    description="這是說明什麼是 Azure AD Connect 以及為何您想要使用它。"
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="building-multi-factor-authentication-into-custom-apps-sdk"></a>將自訂應用程式 (SDK) 建置多重因素驗證

> [AZURE.IMPORTANT]  如果您想要下載 SDK，您必須建立 Azure 多重因素驗證提供者，即使您有 Azure MFA、 AAD 進階版或 EMS 授權。  如果您建立此用途的 Azure 多重因素驗證提供者，並已授權，則需要建立的**每個使用者**模型的提供者，並包含 Azure MFA、 Azure AD 進階版或 EMS 授權的目錄連結提供者。  這樣就能確定您不計費除非您有多個唯一的使用者使用 SDK 超過您擁有的授權數。

Azure 多重因素驗證軟體開發套件 (SDK) 可讓您建立撥打電話和文字訊息驗證直接將登入或異動的程序 Azure AD 租用戶的應用程式。

多重因素驗證 SDK 適用於 C#、 Visual Basic (.NET)、 Java、 Perl、 PHP 和 [注音標示。 SDK 提供精簡的包裝多重因素驗證。 其包含您要撰寫程式碼，包括標為註的原始程式碼檔案、 範例檔案，以及詳細的讀我檔案的所有項目。 每個 SDK 也包含憑證和私密金鑰加密交易都是唯一多重因素驗證提供者。 只要您有一個提供者，您可以下載 SDK 多語言和格式，視需要。

多重因素驗證 sdk Api 的結構是相當簡單。 您進行單一函數呼叫的 API 使用多重因素選項參數，例如驗證模式和使用者的資料，例如要撥打的電話號碼或 PIN 碼以驗證。 Api 翻譯函數呼叫到 web 服務要求雲端 Azure 多重因素驗證服務。 所有呼叫必須都包含參照至每個 SDK 中所包含的私人憑證。

因為 Api 沒有註冊 Azure Active Directory 中的使用者存取權，您必須提供使用者資訊，例如電話號碼和 PIN 碼，檔案或資料庫。 此外，Api 不會提供註冊或使用者管理功能，所以您需要建立這些處理程序，將您的應用程式。






## <a name="download-the-azure-multi-factor-authentication-sdk"></a>下載 Azure 多重因素驗證 SDK

下載 Azure 多重因素 SDK 需要[Azure 多重因素驗證提供者](multi-factor-authentication-get-started-auth-provider.md)。  需要完整 Azure 的訂閱，即使擁有 Azure MFA、 Azure AD 進階版或企業版行動性套件的授權。  若要下載 SDK，您必須瀏覽至多重因素管理入口網站管理多重因素驗證提供者直接，或者按一下 MFA 服務設定] 頁面上的**「 前往入口網站]**連結。


### <a name="to-download-the-azure-multi-factor-authentication-sdk-from-the-azure-portal"></a>若要下載 Azure 多重因素驗證 SDK 從 Azure 入口網站


1. Azure 入口網站以系統管理員身分登入。
2. 在左側，選取 [Active Directory]。
3. 在 Active Directory 頁面上，按一下 [頂端的 [**多重因素驗證提供者**
4. 按一下 [底部的 [**管理**
5. 這會開啟新的頁面。  在左方的底部，按一下 [SDK]。
<center>![下載](./media/multi-factor-authentication-sdk/download.png)</center>
6. 選取您想要的語言按一下其中一個相關聯的連結。
7. 儲存下載。



### <a name="to-download-the-azure-multi-factor-authentication-sdk-via-the-service-settings"></a>若要下載 Azure 多重因素驗證 SDK 透過 [服務設定


1. Azure 入口網站以系統管理員身分登入。
2. 在左側，選取 [Active Directory]。
3. 按兩下您的 Azure AD 的執行個體。
4. 按一下頂端的 [**設定**
5. 多重因素驗證下選取 [**管理服務設定**
![下載](./media/multi-factor-authentication-sdk/download2.png)
6. 在 [服務設定] 頁面上在畫面底部按一下 [**移至入口網站**。
![下載](./media/multi-factor-authentication-sdk/download3a.png)
7. 這會開啟新的頁面。  在左方的底部，按一下 [SDK]。
8. 選取您想要的語言按一下其中一個相關聯的連結。
9. 儲存下載。

## <a name="contents-of-the-azure-multi-factor-authentication-sdk"></a>Azure 多重因素驗證 SDK 的內容
內 SDK，您會看到下列項目︰

- **讀我檔案**。 說明如何使用新的或現有的應用程式中的多因素驗證 Api。
- 多重因素驗證的**來源檔案**
- 您用來與多重因素驗證服務通訊的**用戶端憑證**
- **私密金鑰**的憑證
- **[撥號給結果。** 通話結果代碼清單。 若要開啟此檔案，請將應用程式使用 WordPad 等格式設定的文字。 若要測試和疑難排解應用程式中的多因素驗證實作中使用通話結果代碼。 他們無法驗證狀態碼。
- **範例。** 多重因素驗證的基本工作實作範例程式碼。


>[AZURE.WARNING]用戶端憑證是為您所產生的唯一私人憑證。 不要共用或遺失此檔案。 是您的金鑰以確保您的通訊與多重因素驗證服務的安全性。

## <a name="code-sample-standard-mode-phone-verification"></a>程式碼範例︰ 標準模式電話驗證

此程式碼範例會示範如何使用 Api Azure 多重因素驗證 SDK 中新增至您的應用程式的 [標準模式語音通話驗證。 標準模式是 # 鍵回應使用者的電話。

此範例使用 C#.NET 2.0 多重因素驗證 SDK C# 伺服器端邏輯的基本 ASP.NET 應用程式，但程序是以其他語言的簡單實作十分相似。 因為 SDK 包含來源檔案，而不執行檔時，您可以建立的檔案和參考這些或將其包含直接在您的應用程式中。

>[AZURE.NOTE]在實作多重因素驗證，作為其他因素第二或第三個驗證補充您主要的驗證方法。 這些方法不是作為主要的驗證方法所設計。

### <a name="code-sample-overview"></a>程式碼範例概觀
此範例的程式碼非常簡單的 web 示範應用程式使用 # 鍵回應完成使用者驗證撥打電話。 這個通話因素稱為多因素驗證在標準模式。

用戶端程式碼不包含任何多重因素驗證特定項目。 因為其他驗證因素無關的主要驗證，您可以將其新增而不變更現有的登入介面。 多重因素 sdk Api 可讓您自訂使用者經驗，但您可能不需要在進行任何變更。

伺服器端程式碼新增標準模式驗證，在 [步驟 2。 以所需的標準模式驗證的參數建立 PfAuthParams 物件︰ 使用者名稱、 電話號碼] 和模式中，用戶端憑證 (CertFilePath)，其中每個呼叫中需要的路徑。 如需示範 PfAuthParams 中的所有參數，請參閱 SDK 中的範例檔案。

接下來，程式碼將 PfAuthParams 物件傳遞給 pf_authenticate() 函數。 傳回值表示的成功或失敗的驗證。 將輸出的參數，callStatus 和 errorID，包含其他通話結果的資訊。 通話結果代碼會記錄在 SDK 通話結果檔案中。

可以在幾行中撰寫此最小的實作。 不過，如果生產程式碼中，您便會包含更複雜的錯誤處理與其他資料庫程式碼，加強的使用者經驗。

### <a name="web-client-code"></a>網頁用戶端程式碼

以下是示範頁面的網頁用戶端程式碼。


    <%@ Page Language="C#" AutoEventWireup="true" CodeFile="Default.aspx.cs" Inherits="_Default" %>

    <!DOCTYPE html>

    <html xmlns="http://www.w3.org/1999/xhtml">
    <head runat="server">
    <title>Multi-Factor Authentication Demo</title>
    </head>
    <body>
    <h1>Azure Multi-Factor Authentication Demo</h1>
    <form id="form1" runat="server">

    <div style="width:auto; float:left">
    Username:&nbsp;<br />
    Password:&nbsp;<br />
    </div>

    <div">
    <asp:TextBox id="username" runat="server" width="100px"/><br />
    <asp:Textbox id="password" runat="server" width="100px" TextMode="password" /><br />
    </div>

    <asp:Button id="btnSubmit" runat="server" Text="Log in" onClick="btnSubmit_Click"/>

    <p><asp:Label ID="lblResult" runat="server"></asp:Label></p>

    </form>
    </body>
    </html>


### <a name="server-side-code"></a>伺服器端程式碼

在下列伺服器端程式碼，多重因素驗證設定，而在步驟 2 中執行。 標準模式 (MODE_STANDARD) 是使用者回應 # 鍵撥打電話。

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Web.UI;
    using System.Web.UI.WebControls;

    public partial class _Default : System.Web.UI.Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {
        }

        protected void btnSubmit_Click(object sender, EventArgs e)
        {
            // Step 1: Validate the username and password
            if (username.Text != "Contoso" || password.Text != "password")
            {
                lblResult.ForeColor = System.Drawing.Color.Red;
                lblResult.Text = "Username or password incorrect.";
            }
            else
            {
                // Step 2: Perform multi-factor authentication

                // Add call details from the user database.
                PfAuthParams pfAuthParams = new PfAuthParams();
                pfAuthParams.Username = username.Text;
                pfAuthParams.Phone = "9134884271";
                pfAuthParams.Mode = pf_auth.MODE_STANDARD;

                // Specify a client certificate
                // NOTE: This file contains the private key for the client
                // certificate. It must be stored with appropriate file
                // permissions.
                pfAuthParams.CertFilePath = "c:\\cert_key.p12";

                // Perform phone-based authentication
                int callStatus;
                int errorId;

                if(pf_auth.pf_authenticate(pfAuthParams, out callStatus, out errorId))
                {
                    lblResult.ForeColor = System.Drawing.Color.Green;
                    lblResult.Text = "Multi-Factor Authentication succeeded.";
                }
                else
                {
                    lblResult.ForeColor = System.Drawing.Color.Red;
                    lblResult.Text = " Multi-Factor Authentication failed.";
                }
            }

        }
    }
