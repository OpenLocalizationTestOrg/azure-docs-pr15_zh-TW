<properties 
    pageTitle="設定 web 應用程式中 Azure 應用程式服務" 
    description="如何設定 web 應用程式中 Azure 應用程式服務" 
    services="app-service\web" 
    documentationCenter="" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="configure-web-apps-in-azure-app-service"></a>設定 web 應用程式中 Azure 應用程式服務 #

本主題說明如何設定 web 應用程式使用[Azure 入口網站]。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="application-settings"></a>應用程式設定

1. 在[Azure 入口網站]中，開啟 web 應用程式的刀。
2. 按一下 [**所有設定**]。
3. 按一下 [**應用程式設定**]。

![應用程式設定][configure01]

**應用程式設定**刀有數種類別底下的設定。

### <a name="general-settings"></a>一般設定

**架構版本**。 如果您的應用程式使用任何這些架構，請設定這些選項︰ 

- **.NET Framework**︰ 設定.NET framework 版本。 
- **PHP**︰ 設定 PHP 版本] 或 [**關閉**]，若要停用 PHP。 
- **Java**︰ 選取 Java 版本] 或 [**關閉]** ，若要停用 Java。 若要選擇 Tomcat 和 Jetty 版之間使用**Web 容器**選項。
- **Python**︰ 選取 Python 版本] 或 [**關閉**]，若要停用 Python。

基於技術時，啟用 Java 應用程式會停用.NET PHP，與 Python 選項。

<a name="platform"></a>
**平台**。 選取您的 web 應用程式執行 32 位元或 64 位元的環境中。 64 位元環境需要基本] 或 [標準] 模式。 空閒和共用模式一律執行 32 位元環境中。

**Web 通訊端**。 設定**[開啟**]，以啟用 WebSocket 通訊協定。例如，如果您的 web 應用程式使用[ASP.NET SignalR]或[socket.io]。

<a name="alwayson"></a>
**永遠上**。 根據預設，如果他們是閒置一段時間，會卸載 web 應用程式。 這個選項可讓系統節省資源。 在瀏覽器或標準模式中，您可以啟用**永遠上**將應用程式載入任何時間。 如果您的應用程式執行連續的 web 工作，您應該啟用**永遠上**，或 web 工作可能不會執行可靠的方式。

**受管理的管線版本**。 設定 IIS[管線模式]。 除非您擁有的舊版應用程式所需的 IIS 較舊版本，請將此設定為整合 （預設）。

**自動切換**。 如果您啟用自動交換的部署位置時，應用程式服務將會自動交換 web 應用程式生產當您要的更新。 如需詳細資訊，請參閱 [部署至臨時 Azure 應用程式服務中的 web 應用程式的位置] (web-網站-分段-publishing.md)。

### <a name="debugging"></a>偵錯

**遠端偵錯**。 啟用遠端偵錯。 啟用時，您可以使用 [在 Visual Studio 遠端偵錯工具，直接連接到您的 web 應用程式。 遠端偵錯仍會維持啟用 48 小時。 

### <a name="app-settings"></a>應用程式設定

本節包含名稱/值組，您的 web 應用程式會開始在載入。 

- .NET 應用程式，這些設定會插入您的.NET 設定`AppSettings`在執行階段，覆寫現有的設定。 

- PHP，Python、 Java 和節點的應用程式可以存取這些設定為在執行階段環境變數。 對於每個應用程式的設定，建立兩個環境變數;一個應用程式設定項目，並以 APPSETTING_ 前置詞的另一個指定的名稱。 都包含相同的值。

### <a name="connection-strings"></a>連接字串

連結資源的連接字串。 

.NET 應用程式，這些連接字串會插入您的.NET 設定`connectionStrings`設定在執行階段，覆寫現有的項目鍵等於連結的資料庫名稱的位置。 

對於 PHP，Python、 Java 和節點的應用程式，這些設定將會以環境變數在執行階段，加上連線類型。 環境變數前置詞如下所示︰ 

- SQL Server:`SQLCONNSTR_`
- MySQL:`MYSQLCONNSTR_`
- SQL 資料庫︰`SQLAZURECONNSTR_`
- 自訂︰`CUSTOMCONNSTR_`

例如，如果 MySql 連線字串已命名`connectionstring1`，想要透過環境變數`MYSQLCONNSTR_connectionString1`。

### <a name="default-documents"></a>預設文件

預設文件是網頁顯示在根網站的 URL。  使用清單中的第一個相符的檔案。 

Web 應用程式可能會使用模組路由根據 URL，而不是提供靜態內容，這種情況下有會因此沒有預設文件。    

### <a name="handler-mappings"></a>處理常式對應

若要新增自訂指令碼處理的特定檔案副檔名要求的處理器使用此區域。 

- **副檔名**。 若要處理這種情況，例如 *.php 或 handler.fcgi 副檔名。 
- **指令碼處理器路徑**。 指令碼處理器絕對的路徑。 將指令碼處理要求符合副檔名的檔案。 使用路徑`D:\home\site\wwwroot`參照至您的應用程式的根目錄。
- **其他引數**。 選用的命令列引數的指令碼處理器 


### <a name="virtual-applications-and-directories"></a>虛擬應用程式和目錄 
 
若要設定虛擬應用程式和目錄，指定每個虛擬目錄和其對應的實體路徑相對於網站根目錄。 或者，您可以選取標示為應用程式虛擬目錄的**應用程式**核取方塊。


## <a name="enabling-diagnostic-logs"></a>啟用診斷記錄

若要啟用診斷記錄︰

1. 在 web 應用程式刀，按一下 [**所有設定**]。
2. 按一下 [**診斷記錄**]。 

[選項]，從 web 應用程式的支援記錄撰寫診斷記錄︰ 

- **應用程式的記錄**。 寫入檔案系統中的應用程式的記錄。 記錄 12 小時一段的持續時間。 

**層級**。 啟用記錄應用程式時，此選項會指定的資訊，將會記錄 （錯誤、 警告、 的詳細資訊或詳細資訊）。

**網頁伺服器的記錄**。 W3C 擴充的記錄的檔案格式儲存記錄檔。 

**詳細的錯誤訊息**。 儲存詳細的錯誤訊息︰.htm 檔案。 檔案會儲存在 /LogFiles/DetailedErrors 下。 

**失敗要求追蹤**。 記錄無法要求 XML 檔案。 檔案會儲存在 /LogFiles/W3SVC*len*，上述的唯一識別碼] 位於何處。 此資料夾包含的 XSL 檔案及一或多個 XML 檔案。 務必下載的 XSL 檔案，因為它的格式設定及篩選的 XML 檔案內容提供的功能。

若要檢視記錄檔，您必須建立 FTP 認證，如下所示︰

1. 在 web 應用程式刀，按一下 [**所有設定**]。
2. 按一下 [**部署認證**]。
3. 輸入使用者名稱和密碼。
4. 按一下 [**儲存**]。

![設定部署認證][configure03]

完整的 FTP 使用者名稱是 「 app\username 」*應用程式*所在的 web 應用程式名稱。 使用者名稱會列在 web 應用程式防禦，在**基礎**。  

![FTP 部署認證][configure02]

## <a name="other-configuration-tasks"></a>其他設定工作

### <a name="ssl"></a>SSL 

在瀏覽器或標準模式中，您可以上傳自訂網域的 SSL 的憑證。 如需詳細資訊，請參閱 [啟用 HTTPS 新增 web 應用程式]。 

若要檢視您上傳的憑證，請按一下 [**所有設定** > **SSL] 和自訂網域**。

### <a name="domain-names"></a>網域名稱

新增 web 應用程式的自訂網域名稱。 如需詳細資訊，請參閱 [Azure 應用程式服務中的 web 應用程式的自訂網域名稱設定]。

若要檢視您的網域名稱，請按一下 [**所有設定** > **SSL] 和自訂網域**。

### <a name="deployments"></a>部署

- 設定連續部署。 請參閱[使用給部署 Azure 應用程式服務中的 Web 應用程式](./web-sites-deploy.md)。
- 部署的位置。 請參閱[Azure 應用程式服務中的 Web 應用程式的臨時環境中部署]。


若要檢視您的部署位置，請按一下 [**所有設定** > **部署的位置**。

### <a name="monitoring"></a>監控

在瀏覽器或標準模式中，您可以測試 HTTP 或 HTTPS 端點，從最多三個分佈的地理位置的可用性。 如果 HTTP 回應碼錯誤 （4xx 或 5xx） 或回應所需的時間超過 30 秒數，則會失敗的監控測試。 端點會被視為監控測試成功從所有指定的位置，才能使用。 

如需詳細資訊，請參閱[如何︰ 監控 web 端點狀態]。

>[AZURE.NOTE] 如果您想要開始使用 Azure 應用程式服務註冊 Azure 帳戶之前，請移至[嘗試應用程式服務]，可以讓您立即建立短暫入門 web 應用程式在應用程式服務。 必要; 沒有信用卡沒有承諾。

## <a name="next-steps"></a>後續步驟

- [Azure 應用程式服務中設定自訂網域名稱]
- [啟用 HTTPS Azure 應用程式服務中的應用程式]
- [不按比例縮放 Azure 應用程式服務中的 web 應用程式]
- [監控 Azure 應用程式服務中的 Web 應用程式的基本概念]

<!-- URL List -->

[ASP.NET SignalR]: http://www.asp.net/signalr
[Azure 入口網站]: https://portal.azure.com/
[Azure 應用程式服務中設定自訂網域名稱]: ./web-sites-custom-domain-name.md
[Azure 應用程式服務中的 Web 應用程式部署臨時環境]: ./web-sites-staged-publishing.md
[啟用 HTTPS Azure 應用程式服務中的應用程式]: ./web-sites-configure-ssl-certificate.md
[如何︰ 監控 web 端點狀態]: http://go.microsoft.com/fwLink/?LinkID=279906
[監控 Azure 應用程式服務中的 Web 應用程式的基本概念]: ./web-sites-monitor.md
[管線模式]: http://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[不按比例縮放 Azure 應用程式服務中的 web 應用程式]: ./web-sites-scale.md
[socket.io]: ./web-sites-nodejs-chat-app-socketio.md
[請嘗試應用程式服務]: http://go.microsoft.com/fwlink/?LinkId=523751

<!-- IMG List -->

[configure01]: ./media/web-sites-configure/configure01.png
[configure02]: ./media/web-sites-configure/configure02.png
[configure03]: ./media/web-sites-configure/configure03.png
