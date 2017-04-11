<properties
    pageTitle="若要啟用自動提供 [使用者和群組的 Azure Active Directory 從應用程式使用 SCIM |Microsoft Azure"
    description="Azure Active Directory 可以自動佈建使用者和群組 fronted Web 服務介面 SCIM 通訊協定規格中定義任何應用程式或身分識別存放區"
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/09/2016"
    ms.author="asmalser-msft"/>

#<a name="using-scim-to-enable-automatic-provisioning-of-users-and-groups-from-azure-active-directory-to-applications"></a>若要啟用自動提供 [使用者和群組的 Azure Active Directory 從應用程式使用 SCIM

##<a name="overview"></a>概觀

使用者和群組 fronted Web 服務介面[SCIM 2.0 通訊協定規格](https://tools.ietf.org/html/draft-ietf-scim-api-19)中定義任何應用程式或身分識別存放區，自動佈建 azure Active Directory。 Azure Active Directory 可以傳送邀請來建立、 修改及刪除指派的使用者和群組此 Web 服務中，然後將作業的目標身分識別存放區翻譯這些要求。 

![][1]
*圖︰ 從 Azure Active Directory 佈建到 Web 服務透過身分識別存放區*

這項功能可以用於搭配使用的 「[將您自己的應用程式](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)」 功能中 Azure AD 啟用單一登入] 和 [自動佈建的應用程式，提供或 fronted SCIM web 服務的使用者。

有兩個 SCIM Azure Active Directory 中的使用案例︰

* **佈建使用者和群組應用程式的支援 SCIM** -支援 SCIM 2.0 和 OAuth 承載者權杖用於驗證的應用程式，可搭配 Azure AD] 方塊。

* **建立您自己佈建的解決方案的應用程式的支援其他 API 型佈建**-非 SCIM 應用程式，您可以建立 SCIM 端點之間 Azure AD SCIM 端點和項目翻譯 API 應用程式支援的使用者佈建。  為協助開發的 SCIM 端點，我們會提供程式碼範例，告訴您如何執行提供 SCIM 端點和翻譯 SCIM 訊息以及 CLI 文件庫。  

##<a name="provisioning-users-and-groups-to-applications-that-support-scim"></a>佈建使用者和群組] 可支援 SCIM 應用程式

Azure Active Directory 可以設定成自動佈建指派給使用者和群組的實作[跨網域 2 (SCIM) 的身分識別管理系統](https://tools.ietf.org/html/draft-ietf-scim-api-19)Web 應用程式服務並接受 OAuth 承載者權杖進行驗證。 內 SCIM 2.0 規格，應用程式必須符合下列需求︰

* 支援建立使用者及/或群組]，為每個區段 3.3 SCIM 通訊協定。  

* 支援修改使用者及/或依照 SCIM 通訊協定] 區段 3.5.2 修補程式要求的群組。  

* 擷取依照 SCIM 通訊協定] 區段 3.4.1 已知的資源的支援。  

*  支援查詢使用者及/或群組]，為每個區段 3.4.2 SCIM 通訊協定。  根據預設，externalId 查詢使用者和群組查詢來顯示名稱。  

* 查詢使用者識別碼，並依照 SCIM 通訊協定] 區段 3.4.2 管理員的支援。  

* 查詢群組識別碼，並依照 SCIM 通訊協定] 區段 3.4.2 成員的支援。  

* 接受 OAuth 承載者權杖，依照區段 2.1 SCIM 通訊協定的授權。

請連絡您的應用程式提供者或您的應用程式提供者的文件，這些需求與相容性的陳述式。
 
###<a name="getting-started"></a>快速入門

支援上述 SCIM 設定檔的應用程式可連線到 Azure Active Directory Azure AD 應用程式組件庫中使用 「 自訂 」 應用程式功能。 連線之後，Azure AD 執行同步處理程序每 5 分鐘位置，可以在這裡查詢應用程式的 SCIM 端點，指定的使用者和群組]，並建立或修改這些根據工作分派詳細資料。

**若要連線支援 SCIM 的應用程式︰**

1.  在網頁瀏覽器中啟動 Azure 管理入口網站在 https://manage.windowsazure.com。
2.  瀏覽至**Active Directory > 目錄 > [您的目錄] > 應用程式**，然後選取 [**新增 > 從圖庫新增應用程式**。
3.  選取左側的 [**自訂**] 索引標籤，輸入您的應用程式的名稱，按一下 [核取記號] 圖示，以建立應用程式物件。

![][2]

4.  在 [結果] 畫面中，選取 [第二個**設定帳戶佈建**] 按鈕。
5.  在 [**佈建端點 URL** ] 欄位中，輸入應用程式的 SCIM 端點的 URL。
6.  如果 SCIM 端點需要從 Azure AD 以外的發行者 OAuth 承載者權杖，然後將必要的 OAuth 承載者權杖複製到 [**驗證 Token （選用）** ] 欄位。 為此欄位保留空白，然後 Azure AD 會包含從每個要求 Azure AD 發出 OAuth 承載者權杖。 使用 Azure AD idenity 提供者可驗證此 Azure AD 的應用程式-發出權杖。
7.  按 [**下一步**，然後按一下 [**開始測試**] 按鈕，讓 Azure Active Directory 嘗試連線到 SCIM 結束點。 如果嘗試失敗，就會顯示的診斷資訊。  
8.  如果嘗試連線到應用程式成功，然後按一下 [**下一步**] 剩餘畫面，，然後按一下 [**完成**，結束對話方塊。
9.  在 [結果] 畫面中，選取 [第三個**指定的帳戶**] 按鈕。 [產生使用者和群組] 區段中，將指派給佈建到應用程式的使用者或您想要的群組。
10. 一旦指派使用者和群組]，按一下畫面頂端附近的 [**設定**] 索引標籤。
11. 在下**帳戶佈建**，確認 [狀態設定為 [上]。 
12. 在 [**工具**] 底下，按一下 [**重新啟動帳戶佈建**到著手進行佈建的程序。

請注意，可能會顯示 5 到 10 分鐘的時間之前提供的程序會開始傳送邀請給 SCIM 端點。  在應用程式的儀表板] 索引標籤上，提供連線嘗試的摘要，然後從目錄中的 [報表] 索引標籤，則可以下載佈建活動的報表和任何佈建錯誤。

##<a name="building-your-own-provisioning-solution-for-any-application"></a>建立您自己的任何應用程式提供解決方案

藉由建立 SCIM web 服務介面與 Azure Active Directory，您可以啟用單一登入和自動使用者佈建提供其餘或 SOAP 使用者佈建 API 幾乎任何應用程式。

運作方式如下︰

1.  Azure AD 提供一般語言基礎結構文件庫名稱為[Microsoft.SystemForCrossDomainIdentityManagement](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/)。 系統整合者及開發人員可以使用此文件庫來建立並部署 SCIM 為基礎的 web 服務端點能夠 Azure AD 連線至任何應用程式的身分識別存放區。
2.  對應實作中，將標準化的使用者結構描述對應到使用者結構描述和應用程式所需的通訊協定的 web 服務。
3.  在 [應用程式] 庫中的自訂應用程式的一部分 Azure AD 中註冊端點 URL。
4.  使用者和群組指派給此應用程式中 Azure AD。 在 [工作分派的放到目標應用程式同步處理佇列。 處理佇列中的同步處理程序執行每 5 分鐘。

###<a name="code-samples"></a>程式碼範例

若要讓此程序更容易，一組的[程式碼範例](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)會提供的建立 SCIM web 服務端點，並示範自動佈建了。 維護檔案以逗號分隔的值，代表使用者和群組的資料列的提供者為其中一個範例。  另一個是 Amazon Web 服務身分識別和存取管理服務的運作方式的提供者。  

**必要條件**

* Visual Studio 2013 或更新版本
* [Azure SDK.NET](https://azure.microsoft.com/downloads/)
* Windows 支援電腦 ASP.NET 架構 4.5 作為 SCIM 端點。 這台電腦都必須是雲端存取
* [Azure 試用或授權版本的 Azure AD 進階版訂閱](https://azure.microsoft.com/services/active-directory/)
* Amazon AWS 範例需要[Visual studio AWS 工具組](http://docs.aws.amazon.com/AWSToolkitVS/latest/UserGuide/tkv_setup.html)件庫。 請參閱包含範例，如需詳細資料的讀我檔案

###<a name="getting-started"></a>快速入門

實作可以接受佈建要求 Azure AD SCIM 結束點的最簡單方法是建立並部署輸出提供的使用者的逗點分隔值 (CSV) 檔案的程式碼範例。

**若要建立範例 SCIM 結束點︰**

1.  下載[https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)的程式碼範例封裝
2.  解壓縮套件，並將它放在您的 Windows 電腦的位置，例如 C:\AzureAD-BYOA-Provisioning-Samples\。
3.  在這個資料夾中，啟動 [Visual Studio 中的 FileProvisioningAgent 解決方案]。
4.  選取 [**工具 > 文件庫封裝管理員 > 封裝管理員主控台**，然後執行以下命令，以解決解決方案參照 FileProvisioningAgent 專案︰

    安裝套件 Microsoft.SystemForCrossDomainIdentityManagement 安裝套件 Microsoft.IdentityModel.Clients.ActiveDirectory 安裝套件 Microsoft.Owin.Diagnostics 安裝套件 Microsoft.Owin.Host.SystemWeb

5.  建置 FileProvisioningAgent 專案。
6.  啟動 （以系統管理員），在 Windows 中的命令提示字元 」 應用程式，並使用**cd**命令，將目錄變更您的**\AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug** ] 資料夾。
7.  執行下面使用 Windows 電腦的 IP 」 或 「 網域名稱取代 < ip 位址 > 的命令。

    FileAgnt.exe http://<ip-address>:9000 TargetFile.csv

8.  在 Windows 中**Windows 設定 > 網路和網際網路設定**，請選取**Windows 防火牆 > 進階設定**，並建立可讓輸入的存取連接埠 9000**輸入規則**。
9.  如果 Windows 電腦路由器後面，路由器必須設定為執行網路存取轉譯之間的連接埠 9000 公開至網際網路，與在 Windows 電腦上的連接埠 9000。 這是必要的 Azure AD 能夠存取此雲端中的端點。


**若要在 Azure AD 登錄範例 SCIM 結束點︰**

1.  在網頁瀏覽器中啟動 Azure 管理入口網站在 https://manage.windowsazure.com。
2.  瀏覽至**Active Directory > 目錄 > [您的目錄] > 應用程式**，然後選取 [**新增 > 從圖庫新增應用程式**。
3.  選取左側的 [**自訂**] 索引標籤，輸入名稱，例如 「 SCIM 測試應用程式，」，按一下 [建立應用程式物件的核取記號圖示。 請注意，應用程式物件建立要代表您想要佈建和實作單一登入，並不只是 SCIM 端點的目標應用程式。

![][2]

4.  在 [結果] 畫面中，選取 [第二個**設定帳戶佈建**] 按鈕。
5.  在對話方塊中，輸入的網際網路上公開 URL 和您 SCIM 端點連接埠。 這看起來應該像 http://testmachine.contoso.com:9000 或 http://<ip-address>:9000/，< ip 位址 > 哪裡網際網路上公開 IP 位址。  
6.  按 [**下一步**，然後按一下 [**開始測試**] 按鈕，讓 Azure Active Directory 嘗試連線到 SCIM 結束點。 如果嘗試失敗，就會顯示的診斷資訊。  
7.  如果連線到您的 Web 服務成功，接著在剩餘的畫面上，按一下 [**下一筆**，然後按一下 [**完成**，結束對話方塊。
8.  在 [結果] 畫面中，選取 [第三個**指定的帳戶**] 按鈕。 [產生使用者和群組] 區段中，將指派給佈建到應用程式的使用者或您想要的群組。
9.  一旦指派使用者和群組]，按一下畫面頂端附近的 [**設定**] 索引標籤。
10. 在下**帳戶佈建**，確認 [狀態設定為 [上]。 
11. 在 [**工具**] 底下，按一下 [**重新啟動帳戶佈建**到著手進行佈建的程序。

請注意，可能會顯示 5 到 10 分鐘的時間之前提供的程序會開始傳送邀請給 SCIM 端點。  在應用程式的儀表板] 索引標籤上，提供連線嘗試的摘要，然後從目錄中的 [報表] 索引標籤，則可以下載佈建活動的報表和任何佈建錯誤。

驗證這個範例中的最後一個步驟是在 Windows 電腦上的 [\AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug] 資料夾中開啟 TargetFile.csv 檔案。 一旦執行提供的程序後，此檔案會顯示所有的詳細資料指派和佈建後，使用者和群組]。

###<a name="development-libraries"></a>開發文件庫

若要開發自己符合 SCIM 規格的 Web 服務，先熟悉 Microsoft，協助加速開發程序所提供的下列文件庫︰ 

**1:** 根據的基礎結構，例如 C# 書寫的語言，一般語言基礎結構的文件庫會提供使用。  這些文件庫， [Microsoft.SystemForCrossDomainIdentityManagement.Service](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/)，其中宣告介面，Microsoft.SystemForCrossDomainIdentityManagement.IProvider 下, 圖所示。  開發人員使用的文件庫會實作介面的類別，也就是，一般提供者。  文件庫啟用輕鬆 SCIM 規格部署符合 Web 服務的開發人員，請裝載於 Internet Information Services 或任何可執行一般語言基礎結構的組件中。  要求的 Web 服務會成數提供者的方法，會由開發人員程式設計操作一些身分識別存放區上呼叫。    

![][3]

**2:**  [Express 路由處理常式](http://expressjs.com/guide/routing.html)可供剖析 node.js 要求物件代表 （如定義 SCIM 規格） 通話，對 node.js Web 服務。   

###<a name="building-a-custom-scim-endpoint"></a>建立自訂 SCIM 端點

使用上述的文件庫，請使用這些文件庫的開發人員可以主控 Internet Information Services 或內任何可執行的常見的語言基礎結構組件，其服務。  以下是範例程式碼服務中執行的組件，在 http://localhost:9000 的地址︰ 

    private static void Main(string[] arguments)
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IProvider and 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  
    
    Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
      new DevelopersMonitor();
    Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
      new DevelopersProvider(arguments[1]);
    Microsoft.SystemForCrossDomainIdentityManagement.Service webService = null;
    try
    {
        webService = new WebService(monitor, provider);
        webService.Start("http://localhost:9000");

        Console.ReadKey(true);
    }
    finally
    {
        if (webService != null)
        {
            webService.Dispose();
            webService = null;
        }
    }
    }

    public class WebService : Microsoft.SystemForCrossDomainIdentityManagement.Service
    {
    private Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor;
    private Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider;

    public WebService(
      Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitoringBehavior, 
      Microsoft.SystemForCrossDomainIdentityManagement.IProvider providerBehavior)
    {
        this.monitor = monitoringBehavior;
        this.provider = providerBehavior;
    }

    public override IMonitor MonitoringBehavior
    {
        get
        {
            return this.monitor;
        }

        set
        {
            this.monitor = value;
        }
    }

    public override IProvider ProviderBehavior
    {
        get
        {
            return this.provider;
        }

        set
        {
            this.provider = value;
        }
    }
    }

請務必請注意，這項服務必須具有 HTTP 地址和伺服器驗證憑證的根憑證授權單位是下列其中一項︰ 

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* Verisign
* WoSign

伺服器驗證憑證可以繫結至連接埠 Windows 主機使用網路命令介面公用程式，就像這樣︰ 

    netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}  
 
這裡提供給 certhash 引數的值都是憑證的指紋，提供給 appid 引數的值時任意全域唯一識別碼。  

若要裝載 Internet Information Services 中的服務，開發人員想要建立公用語言基礎結構的程式碼文件庫組件中名為 「 啟動的組件的預設命名空間的類別。  以下是範例這種類別︰ 

    public class Startup
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor and  
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter starter;

    public Startup()
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
          new DevelopersMonitor();
        Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
          new DevelopersProvider();
        this.starter = 
          new Microsoft.SystemForCrossDomainIdentityManagement.WebApplicationStarter(
            provider, 
            monitor);
    }

    public void Configuration(
      Owin.IAppBuilder builder) // Defined in in Owin.dll.  
    {
        this.starter.ConfigureApplication(builder);
    }
    }

###<a name="handling-endpoint-authentication"></a>處理端點驗證

Azure Active Directory 要求包含 OAuth 2.0 承載者權杖。   接收要求任何服務會驗證 Azure Active Directory 代表預期的 Azure Active Directory 租用戶，存取 Azure Active Directory 的圖形 Web 服務為簽發者。  在權杖，發行者由 iss 宣告，例如 「 iss 」: 「 https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/ 」。  在此範例中，宣告值] 中，基底地址 https://sts.windows.net，會識別為發行者 Azure Active Directory 相對的地址區段中，cbb1a5ac-f33b-45fa-9bf5-f37db0fed422 時發出權杖代表的 Azure Active Directory 租用戶的唯一識別碼。  如果存取 Azure Active Directory 圖表 Web 服務發出權杖，00000002-0000-0000-c000-000000000000，該服務的識別項應該 token aud 宣告的值。  

使用 Microsoft 建置 SCIM 服務所提供的一般語言基礎結構文件庫的開發人員可以驗證要求 Azure Active Directory 使用 Microsoft.Owin.Security.ActiveDirectory 套件遵循下列步驟︰ 

**1:** 在 [提供者，實作 Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior 屬性會傳回時啟動服務呼叫的方法︰ 

    public override Action\<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration\> StartupBehavior
    {
      get
      {
        return this.OnServiceStartup;
      }
    }

    private void OnServiceStartup(
      Owin.IAppBuilder applicationBuilder,  // Defined in Owin.dll.  
      System.Web.Http.HttpConfiguration configuration)  // Defined in System.Web.Http.dll.  
    {
    }

**2:** 下列程式碼加入該有任何服務的端點驗證成帶有由代表指定的租用戶，存取 Azure Active Directory 的圖形 Web 服務的 Azure Active Directory 的權杖平常的方式︰ 

    private void OnServiceStartup(
      Owin.IAppBuilder applicationBuilder IAppBuilder applicationBuilder, 
      System.Web.Http.HttpConfiguration HttpConfiguration configuration)
    {
      // IFilter is defined in System.Web.Http.dll.  
      System.Web.Http.Filters.IFilter authorizationFilter = 
        new System.Web.Http.AuthorizeAttribute(); // Defined in System.Web.Http.dll.configuration.Filters.Add(authorizationFilter);

      // SystemIdentityModel.Tokens.TokenValidationParameters is defined in    
      // System.IdentityModel.Token.Jwt.dll.
      SystemIdentityModel.Tokens.TokenValidationParameters tokenValidationParameters =     
        new TokenValidationParameters()
        {
          ValidAudience = "00000002-0000-0000-c000-000000000000"
        };

      // WindowsAzureActiveDirectoryBearerAuthenticationOptions is defined in 
      // Microsoft.Owin.Security.ActiveDirectory.dll
      Microsoft.Owin.Security.ActiveDirectory.
      WindowsAzureActiveDirectoryBearerAuthenticationOptions authenticationOptions =
        new WindowsAzureActiveDirectoryBearerAuthenticationOptions()    {
        TokenValidationParameters = tokenValidationParameters,
        Tenant = "03F9FCBC-EA7B-46C2-8466-F81917F3C15E" // Substitute the appropriate tenant’s 
                                                      // identifier for this one.  
      };

      applicationBuilder.UseWindowsAzureActiveDirectoryBearerAuthentication(authenticationOptions);
    }

##<a name="user-and-group-schema"></a>使用者和群組結構描述

Azure Active Directory 可以提供兩種類型的資源，SCIM Web 服務。  這些類型的資源是使用者和群組]。  

結構描述識別項，urn: ietf:params:scim:schemas:extension:enterprise:2.0:User，其中會包含在此通訊協定規格會識別使用者資源︰ http://tools.ietf.org/html/draft-ietf-scim-core-schema。  資料表 1，提供預設的對應的 Azure Active Directory 中的使用者屬性的 urn: ietf:params:scim:schemas:extension:enterprise:2.0:User 資源屬性下方。  

結構描述識別碼] 會識別群組資源 http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group。  資料表 2 下方會顯示預設對應的 Azure Active Directory 中的屬性 http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group 資源群組的屬性。  

###<a name="table-1-default-user-attribute-mapping"></a>表 1︰ 預設使用者屬性對應

| Azure Active Directory 使用者 | urn: ietf:params:scim:schemas:extension:enterprise:2.0:User |
| ------------- | ------------- |
| IsSoftDeleted | 作用中 |
| 顯示名稱 | 顯示名稱 |
| 傳真 TelephoneNumber | [輸入 eq 「 傳真 」] phoneNumbers.value |
| givenName | name.givenName |
| jobTitle | 標題 |
| 郵件 | [輸入 eq 「 工作 」] 的電子郵件.value |
| mailNickname | externalId |
| 管理員 | 管理員 |
| 行動裝置 | [輸入 eq 「 行動 」] phoneNumbers.value |
| objectId | 識別碼 |
| 郵遞區號 | 地址 [輸入 eq 「 工作 」].postalCode |
| proxy 位址 | [輸入 「 其他 」 eq] 的電子郵件。值 |
| 實體-傳遞-OfficeName | [輸入 「 其他 」 eq] 地址。格式設定 |
| streetAddress | 地址 [輸入 eq 「 工作 」].streetAddress |
| 姓氏 | name.familyName |
| 電話號碼 | [輸入 eq 「 工作 」] phoneNumbers.value |
| 使用者 PrincipalName | 使用者名稱 |


###<a name="table-2-default-group-attribute-mapping"></a>表 2︰ 預設群組屬性對應

| Azure Active Directory 群組 | http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group |
| ------------- | ------------- |
| 顯示名稱 | externalId |
| 郵件 | [輸入 eq 「 工作 」] 的電子郵件.value |
| mailNickname | 顯示名稱 |
| 成員 | 成員 |
| objectId | 識別碼 |
| proxyAddresses | [輸入 「 其他 」 eq] 的電子郵件。值 |


##<a name="user-provisioning-and-de-provisioning"></a>使用者佈建和不支援

下的圖顯示 Azure Active Directory 會傳送到 SCIM 服務，來管理使用者在另一個身分識別的生命週期的郵件儲存。  圖表也會顯示如何使用 Microsoft 建置這類服務所提供的常見的語言基礎結構文件庫實作 SCIM 服務會轉譯那些要求呼叫的提供者的方法。  

![][4]
*圖︰ 使用者佈建和不支援的順序*

**1:** Azure Active Directory 會 externalId 屬性值相符的 Azure Active Directory 中的使用者 mailNickname 屬性值的查詢，為使用者的服務。  查詢會以類似 jyoung 該處是 Azure Active Directory 中的使用者 mailNickname 的樣本超文字傳輸通訊協定要求︰ 

    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...

如果使用 Microsoft 實作 SCIM 服務所提供的一般語言基礎結構文件庫建立的服務，要求將會翻譯成查詢方法的服務提供者的通話。  以下是方法的簽章: 

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
      Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
      string correlationIdentifier);

Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters 介面的定義如下︰ 

    public interface IQueryParameters: 
      Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
        System.Collections.Generic.IReadOnlyCollection <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
        { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
      system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
      { get; }
      System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
      { get; }
      string SchemaIdentifier 
      { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IFilter
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IFilter AdditionalFilter 
          { get; set; }
        string AttributePath 
          { get; } 
        Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator FilterOperator 
          { get; }
        string ComparisonValue 
          { get; }
    }
    
    public enum Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator
    {
        Equals
    }

如果使用者有 externalId 屬性的指定值的查詢前述範例，傳遞查詢方法的引數的值會，如下所示︰ 

* 參數。AlternateFilters.Count: 1
* 參數。AlternateFilters.ElementAt(0)。AttributePath: 「 externalId 」
* 參數。AlternateFilters.ElementAt(0)。關係運算子︰ ComparisonOperator.Equals
* 參數。AlternateFilter.ElementAt(0)。ComparisonValue: 「 jyoung 」
* correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin。RequestId 」] 

**2:** 如果來回應查詢服務中的使用者有 externalId 屬性值相符的 Azure Active Directory 中的使用者 mailNickname 屬性值不會傳回任何使用者，Azure Active Directory 會要求服務佈建對應至 Azure Active Directory 中的使用者。  以下是這類要求的範例︰ 

    POST https://.../scim/Users HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
    {
      "schemas":
      [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
      "externalId":"jyoung",
      "userName":"jyoung",
      "active":true,
      "addresses":null,
      "displayName":"Joy Young",
      "emails": [
        {
          "type":"work",
          "value":"jyoung@Contoso.com",
          "primary":true}],
      "meta": {
        "resourceType":"User"},
       "name":{
        "familyName":"Young",
        "givenName":"Joy"},
      "phoneNumbers":null,
      "preferredLanguage":null,
      "title":null,
      "department":null,
      "manager":null}

Microsoft 實作 SCIM 服務所提供的一般語言基礎結構文件庫會轉換成建立方法的服務提供者的通話的要求。  建立方法具有此簽章︰ 

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(
      Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
      string correlationIdentifier);

若是佈建使用者要求，資源引數的值會 Microsoft.SystemForCrossDomainIdentityManagement 的執行個體。 定義 Microsoft.SystemForCrossDomainIdentityManagement.Schemas 文件庫中的 Core2EnterpriseUser 類別。  如果佈建使用者要求順利完成，然後執行方法預期會傳回的執行個體 Microsoft.SystemForCrossDomainIdentityManagement。 Core2EnterpriseUser 類別，[識別碼] 屬性設定為新佈建使用者的唯一識別碼的值。  

**3:** 若要更新已知會依 SCIM fronted 身分識別存放區中的使用者，Azure Active Directory 會繼續服務要求該使用者的目前狀態，要求如下所示︰ 

    GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...

在使用 Microsoft 實作 SCIM 服務所提供的一般語言基礎結構文件庫所建立的服務，要求將會翻譯成擷取方法的服務提供者的通話。  以下是簽章的擷取方法︰ 

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource and 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
    // are defined in Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> 
       Retrieve(
         Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
           parameters, 
           string correlationIdentifier);
    
    public interface 
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters:   
        IRetrievalParameters
        {
          Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
            ResourceIdentifier 
              { get; }
    }
    public interface Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier
    {
        string Identifier 
          { get; set; }
        string Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier 
          { get; set; }
    }

若是擷取使用者的目前狀態的要求前述範例中，做為參數引數的值提供的物件的屬性的值會，如下所示︰ 

* 識別碼: 「 54D382A4-2050-4C03-94D1-E769F1D15682 」
* SchemaIdentifier: 「 urn: ietf:params:scim:schemas:extension:enterprise:2.0:User 」

**4:** 如果參照屬性更新，然後 Azure Active Directory 會查詢的服務，來判斷參考屬性，識別存放區中的目前值 fronted 服務已符合值的 Azure Active Directory 中的屬性。  如果使用者只屬性的目前值查詢以這種方式是管理員屬性。  以下是要求的範例，來判斷特定使用者物件的管理員屬性目前是否有特定的值︰ 

    GET ~/scim/Users?filter=id eq 54D382A4-2050-4C03-94D1-E769F1D15682 and manager eq 2819c223-7f76-453a-919d-413861904646&attributes=id HTTP/1.1
    Authorization: Bearer ...

值屬性查詢參數的識別碼，代表，如果使用者物件符合提供做為篩選查詢參數的值的運算式，然後服務預期回覆與 urn: ietf:params:scim:schemas:core:2.0:User 或 urn: ietf:params:scim:schemas:extension:enterprise:2.0:User 資源，包括該資源的識別碼屬性的值。  當然，要求者已知識別碼屬性的值，包括在篩選查詢參數值。要求，目的是實際要求資源的最小表示法的滿足篩選運算式為這類的物件存在的指示。   

如果使用 Microsoft 實作 SCIM 服務所提供的一般語言基礎結構文件庫建立的服務，要求將會翻譯成查詢方法的服務提供者的通話。  做為參數引數的值提供的物件的屬性的值會如下所示︰ 

* 參數。AlternateFilters.Count: 2
* 參數。AlternateFilters.ElementAt(x)。AttributePath: 「 識別碼 」
* 參數。AlternateFilters.ElementAt(x)。關係運算子︰ ComparisonOperator.Equals
* 參數。AlternateFilter.ElementAt(x)。ComparisonValue: 「 54D382A4-2050-4C03-94D1-E769F1D15682 」
* 參數。AlternateFilters.ElementAt(y)。AttributePath: 「 管理員 」
* 參數。AlternateFilters.ElementAt(y)。關係運算子︰ ComparisonOperator.Equals
* 參數。AlternateFilter.ElementAt(y)。ComparisonValue: 「 2819c223-7f76-453a-919d-413861904646 」
* 參數。RequestedAttributePaths.ElementAt(0): 「 識別碼 」
* 參數。SchemaIdentifier: 「 urn: ietf:params:scim:schemas:extension:enterprise:2.0:User 」

在這裡，索引 x 的值可能會 0 的索引 y 值可能會是 1，或的 x 值可能會是 1，並的 y 值可能是 0，根據篩選查詢參數運算式的順序。   

**5:** 以下是範例要求從 Azure Active Directory 更新使用者 SCIM 服務︰ 

    PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
    {
      "schemas": 
      [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
      "Operations":
      [
        {
          "op":"Add",
          "path":"manager",
          "value":
            [
              {
                "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
                "value":"2819c223-7f76-453a-919d-413861904646"}]}]}

Microsoft 公用語言基礎結構文件庫的實作 SCIM 服務會轉換成更新方法的服務提供者的通話的要求。  以下是方法的簽章: 

    // System.Threading.Tasks.Tasks and 
    // System.Collections.Generic.IReadOnlyCollection<T>
    // are defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
    // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

    System.Threading.Tasks.Task Update(
      Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
      string correlationIdentifier);

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
    {
    Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
      PatchRequest 
        { get; set; }
    Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
      ResourceIdentifier 
        { get; set; }        
    }

    public class PatchRequest2: 
      Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
    {
    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
        Operations
        { get;}

    public void AddOperation(
      Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation operation);
    }

    public class PatchOperation
    {
    public Microsoft.SystemForCrossDomainIdentityManagement.OperationName 
      Name
      { get; set; }
    
    public Microsoft.SystemForCrossDomainIdentityManagement.IPath 
      Path
      { get; set; }

    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.OperationValue> Value
      { get; }

    public void AddValue(
      Microsoft.SystemForCrossDomainIdentityManagement.OperationValue value);
    }

    public enum OperationName
    {
      Add,
      Remove,
      Replace
    }

    public interface IPath
    {
      string AttributePath { get; }
      System.Collections.Generic.IReadOnlyCollection<IFilter> SubAttributes { get; }
      Microsoft.SystemForCrossDomainIdentityManagement.IPath ValuePath { get; }
    }

    public class OperationValue
    {
      public string Reference
      { get; set; }
      
      public string Value
      { get; set; }
    }



如果更新使用者的要求前述範例中，為修補程式引數的值提供的物件會有這些屬性值︰ 

* ResourceIdentifier.Identifier: 「 54D382A4-2050-4C03-94D1-E769F1D15682 」
* ResourceIdentifier.SchemaIdentifier: 「 urn: ietf:params:scim:schemas:extension:enterprise:2.0:User 」
* (PatchRequest 為 PatchRequest2)。Operations.Count: 1
* (PatchRequest 為 PatchRequest2)。Operations.ElementAt(0)。OperationName: OperationName.Add
* (PatchRequest 為 PatchRequest2)。Operations.ElementAt(0)。Path.AttributePath: 「 管理員 」
* (PatchRequest 為 PatchRequest2)。Operations.ElementAt(0)。Value.Count: 1
* (PatchRequest 為 PatchRequest2)。Operations.ElementAt(0)。Value.ElementAt(0)。參照︰ http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
* (PatchRequest 為 PatchRequest2)。Operations.ElementAt(0)。Value.ElementAt(0)。值︰ 2819c223-7f76-453a-919d-413861904646

**6:** 若要取消佈建 fronted SCIM 服務身分識別存放區中的使用者，Azure Active Directory 會傳送要求如下所示︰ 

    DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
    
如果使用 Microsoft 實作 SCIM 服務所提供的一般語言基礎結構文件庫建立的服務，要求將會翻譯成的服務提供者的 [刪除] 方法在通話。   這個方法具有此簽章︰ 

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
    System.Threading.Tasks.Task Delete(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
        resourceIdentifier, 
      string correlationIdentifier);
 
為 resourceIdentifier 引數的值提供的物件就必須解除佈建使用者要求前述範例若是這些屬性值︰ 

* ResourceIdentifier.Identifier: 「 54D382A4-2050-4C03-94D1-E769F1D15682 」
* ResourceIdentifier.SchemaIdentifier: 「 urn: ietf:params:scim:schemas:extension:enterprise:2.0:User 」

##<a name="group-provisioning-and-de-provisioning"></a>群組佈建和不支援

下的圖顯示 Azure Active Directory 會傳送給 SCIM 服務，來管理群組，以在另一個身分識別的生命週期的郵件儲存。  這些郵件異使用者三種方法的相關的郵件︰ 

* 群組資源的結構描述會被視為 http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group。  
* 若要擷取群組的要求會規定成員屬性是排除任何回應邀請中提供的資源。  
* 成員屬性相關的要求時，就會要求來判斷參考屬性是否為特定的值。  

![][5]
*圖︰ 群組佈建和不支援的順序*

##<a name="related-articles"></a>相關的文章

- [Azure Active Directory 中的應用程式管理文件索引](active-directory-apps-index.md)
- [自動化使用者佈建/取消提供 SaaS 應用程式](active-directory-saas-app-provisioning.md)
- [自訂使用者提供的屬性對應](active-directory-saas-customizing-attribute-mappings.md)
- [撰寫屬性對應的運算式](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [範圍使用者佈建的篩選](active-directory-saas-scoping-filters.md)
- [帳戶佈建通知](active-directory-saas-account-provisioning-notifications.md)
- [教學課程如何整合 SaaS 應用程式的清單](active-directory-saas-tutorial-list.md)


    
<!--Image references-->
[1]: ./media/active-directory-scim-provisioning/scim-figure-1.PNG
[2]: ./media/active-directory-scim-provisioning/scim-figure-2.PNG
[3]: ./media/active-directory-scim-provisioning/scim-figure-3.PNG
[4]: ./media/active-directory-scim-provisioning/scim-figure-4.PNG
[5]: ./media/active-directory-scim-provisioning/scim-figure-5.PNG
