<properties 
    pageTitle="建立 Web 應用程式中使用 Azure SDK java Azure 應用程式服務" 
    description="瞭解如何使用 java Azure SDK 以程式設計方式 Azure 應用程式服務上建立 Web 應用程式。" 
    tags="azure-classic-portal"
    services="app-service\web" 
    documentationCenter="Java" 
    authors="donntrenton" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="02/25/2016" 
    ms.author="v-donntr"/>


# <a name="create-a-web-app-in-azure-app-service-using-the-azure-sdk-for-java"></a>建立 Web 應用程式中使用 Azure SDK java Azure 應用程式服務

<!-- Azure Active Directory workflow is not yet available on the Azure Portal -->

## <a name="overview"></a>概觀

這個逐步解說示範如何建立 Azure SDK [Azure 應用程式服務][]上建立 Web 應用程式的 Java 應用程式，然後部署至該應用程式。 它包含兩個部分︰

- 第 1 部分示範如何建立 Java 應用程式所建立的 web 應用程式。
- 第 2 部分示範如何建立簡單的 JSP"Hello World 」 應用程式，然後使用 FTP 用戶端程式碼部署至應用程式服務。


## <a name="prerequisites"></a>必要條件

### <a name="software-installations"></a>軟體安裝

本文中的 AzureWebDemo 應用程式碼撰寫時，使用 Azure Java SDK 0.7.0，您可以使用[Web 平台安裝程式][](WebPI) 來安裝。 此外，請務必使用最新版[的蝕 Azure 工具組][]。 安裝 SDK 之後，請執行**更新索引**中**Maven 存放庫**，來更新蝕專案中的相依性，然後重新新增每個封裝**相依性**] 視窗中的最新版本。 您可以按一下 [驗證您已安裝的軟體蝕中的版本**說明 > 安裝的詳細資訊**;您必須至少具備下列版本︰

- 封裝 java 0.7.0.20150309 Microsoft Azure 文件庫
- 蝕 Java 卻開發人員 4.4.2.20150219 IDE


### <a name="create-and-configure-cloud-resources-in-azure"></a>建立及設定 Azure 中的雲端資源

在開始此程序之前，必須有作用中的 Azure 訂閱，並設定 [預設的 Active Directory (AD) Azure 上。


### <a name="create-an-active-directory-ad-in-azure"></a>建立 Azure Active Directory (AD)

如果您還沒有 Active Directory (AD) Azure 訂閱，請登入您的 Microsoft 帳戶與[Azure 傳統入口網站][]。 如果您有多個訂閱，請按一下 [**訂閱**]，然後選取您要為此專案使用的訂閱的預設目錄。 然後按一下 [**套用**]，若要切換的訂閱檢視。

1. **Active Directory**從功能表選取 [左邊。 **按一下 [新增 > 目錄 > 建立自訂**。

2. 在 [**新增目錄**中，選取 [**建立新的目錄**。

3. 在 [**名稱**] 中，輸入目錄名稱。

4. 在 [**網域**] 中，輸入網域名稱。 這是與您的目錄，預設會包含基本的網域名稱有表單`<domain_name>.onmicrosoft.com`。 您可以將其目錄名稱或另一個您擁有自己的網域名稱命名。 之後，您可以新增您的組織已使用的另一個網域名稱。

5. 在 [**國家或地區**，選取您的地區設定。

如需有關 AD 的詳細資訊，請參閱[什麼是 Azure AD 目錄][]嗎？


### <a name="create-a-management-certificate-for-azure"></a>建立管理憑證以 Azure

Azure SDK java 使用管理憑證驗證 Azure 訂閱。 這些是的 x.509 v3 若您使用驗證使用服務管理 API 來代表訂閱擁有者，若要管理訂閱資源採取行動用戶端應用程式。

此程序中的程式碼會驗證 Azure 使用自我簽署的憑證。 此程序必須先建立憑證，然後上傳至[Azure 傳統入口網站][]事先。 這包含下列步驟︰

- 產生代表您的用戶端憑證 PFX 檔案並將其儲存至本機。
- 從 PFX 檔案中產生的管理憑證 （CER 檔案）。
- 將檔案上傳至您的 Azure 訂閱。
- 轉換為 PFX 檔案 JKS，因為 Java 使用憑證進行驗證使用的格式。
- 撰寫應用程式的驗證碼，參照到本機 JKS 檔案。

當您完成此程序時，CER 憑證都位於 Azure 訂閱並 JKS 憑證將存放在本機磁碟機上。 如需有關管理憑證的詳細資訊，請參閱[建立和上傳 Azure 管理憑證][]。


#### <a name="create-a-certificate"></a>建立憑證

若要建立您自己的自我簽署的憑證，請開啟您的作業系統上命令主控台並執行下列命令。

> **附註︰** 執行此命令的電腦必須安裝 JDK。 此外，keytool 路徑安裝 JDK 的位置而定。 如需詳細資訊，請參閱 Java 線上文件中的[索引鍵和憑證管理工具 (keytool)][] 。

若要建立.pfx 檔︰

    <java-install-dir>/bin/keytool -genkey -alias <keystore-id>
     -keystore <cert-store-dir>/<cert-file-name>.pfx -storepass <password>
     -validity 3650 -keyalg RSA -keysize 2048 -storetype pkcs12
     -dname "CN=Self Signed Certificate 20141118170652"

若要建立.cer 檔案︰

    <java-install-dir>/bin/keytool -export -alias <keystore-id>
     -storetype pkcs12 -keystore <cert-store-dir>/<cert-file-name>.pfx
     -storepass <password> -rfc -file <cert-store-dir>/<cert-file-name>.cer

位置︰

- `<java-install-dir>`是您安裝 Java 目錄的路徑。
- `<keystore-id>`採用的項目識別項 (例如， `AzureRemoteAccess`)。
- `<cert-store-dir>`是您要儲存憑證的目錄的路徑 (例如`C:/Certificates`)。
- `<cert-file-name>`憑證檔案的名稱 (例如`AzureWebDemoCert`)。
- `<password>`是您選擇保護憑證; 的密碼它必須至少有 6 個字元。 雖然這不建議使用，您可以輸入沒有密碼。
- `<dname>`alias （別名），與相關聯 X.500 辨別名稱，而會用來作為自我簽署憑證的發行者及主旨欄位。

如需詳細資訊，請參閱[建立和上傳 Azure 管理憑證][]。


#### <a name="upload-the-certificate"></a>上傳憑證

若要以 Azure 上載自我簽署的憑證，請移至 [傳統] 入口網站中的 [**設定**] 頁面，然後按一下 [**管理認證**] 索引標籤。 按一下 [在頁面底部的 [**上傳**]，然後瀏覽至您建立的檔案的位置。


#### <a name="convert-the-pfx-file-into-jks"></a>將 PFX 檔案轉換成 JKS

在 Windows 命令提示字元 （以系統管理員身分執行），包含憑證，然後執行以下命令，在目錄 cd 位置`<java-install-dir>`是您安裝 Java 您電腦的目錄︰

    <java-install-dir>/bin/keytool.exe -importkeystore
     -srckeystore <cert-store-dir>/<cert-file-name>.pfx
     -destkeystore <cert-store-dir>/<cert-file-name>.jks
     -srcstoretype pkcs12 -deststoretype JKS

1. 出現提示時，輸入目的地採用密碼;這是 JKS 檔案的密碼。

2. 出現提示時，請輸入來源採用的密碼;這是您指定 PFX 檔案的密碼。

兩個密碼沒有相同。 雖然這不建議使用，您可以輸入沒有密碼。


## <a name="build-a-web-app-creation-application"></a>建立 Web 應用程式建立應用程式

### <a name="create-the-eclipse-workspace-and-maven-project"></a>建立蝕工作區和 Maven 專案

此區段中，您建立工作區及網頁應用程式建立應用程式，名為 AzureWebDemo Maven 專案。

1. 建立新的 Maven 專案。 按一下 [**檔案 > 新增 > Maven 專案**。 在**新的 Maven 專案**中，選取 [**建立簡單的專案**，並**使用預設工作區位置**。

2. 在 [**新 Maven 專案**的 [第二個] 頁面中，指定下列設定︰

    - 群組識別碼︰`com.<username>.azure.webdemo`
    - 成品 ID: AzureWebDemo
    - 版本︰ 0.0.1-SNAPSHOT
    - 包裝︰ jar
    - 名稱︰ AzureWebDemo

    按一下 [**完成**]。

3. 在 [專案總管中開啟新的專案 pom.xml 檔案。 選取 [**相依性**] 索引標籤。 這是新的專案時，不封裝列尚未。

4. 開啟 [Maven 存放庫的檢視。 **按一下視窗 > 顯示檢視] > 其他 > Maven > Maven 存放庫**按一下**[確定]**。 **Maven 存放庫**檢視會顯示在 IDE 底部。

5. 開啟**全域存放庫**、**中央**存放庫中，以滑鼠右鍵按一下，然後選取**重建索引**。

    ![][1]
    
    此步驟可能需要幾分鐘，視您的連線速度而定。 當重建索引時，您應該會看到的**中央**Maven 存放庫中的 Microsoft Azure 套件。

6. 在**相依性**，請按一下 [**新增**]。 在 [ **Enter] 群組識別碼...**輸入`azure-management`。 選取基底管理及管理應用程式服務 Web 應用程式的套件︰

        com.microsoft.azure  azure-management
        com.microsoft.azure  azure-management-websites

    > **附註︰**如果您要更新的相依性，新的版本版本之後，您需要重新新增每個此清單中的相依性。
    > 按一下 [**新增]** ，然後選取每個相依性後，它會出現 [**相依性**] 清單中的新版本號碼。

按一下**[確定]**。 Azure 封包然後會出現在 [**相依性**清單。


### <a name="writing-java-code-to-create-a-web-app-by-calling-the-azure-sdk"></a>撰寫 Java 程式碼，則可電話 Azure SDK 建立 Web 應用程式

接下來，撰寫呼叫 Api java Azure SDK，建立應用程式服務 web 應用程式中的程式碼。

1. 建立 Java 類別，以包含主要進入點程式碼。 在 [專案總管] 中以滑鼠右鍵按一下專案節點，然後選取 [**新增 > 課程**。

2. 在**新的 Java 類別**中的類別命名`WebCreator`核取**公用靜態 void 主要**核取方塊。 選取項目應如下所示︰

    ![][2]

3. 按一下 [**完成**]。 WebCreator.java 檔案會出現在 [專案總管]。


### <a name="calling-the-azure-api-to-create-an-app-service-web-app"></a>呼叫 Azure API 建立應用程式服務 Web 應用程式


#### <a name="add-necessary-imports"></a>新增必要的匯入

在 WebCreator.java，新增下列匯入;這些匯入提供使用 Azure Api 的存取權的管理文件庫中的類別︰

    // General imports
    import java.net.URI;
    import java.util.ArrayList;
    
    // Imports for Exceptions
    import java.io.IOException;
    import java.net.URISyntaxException;
    import javax.xml.parsers.ParserConfigurationException;
    import com.microsoft.windowsazure.exception.ServiceException;
    import org.xml.sax.SAXException;
    
    // Imports for Azure App Service management configuration
    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.management.configuration.ManagementConfiguration;
    
    // Service management imports for App Service Web Apps creation
    import com.microsoft.windowsazure.management.websites.*;
    import com.microsoft.windowsazure.management.websites.models.*;
    
    // Imports for authentication
    import com.microsoft.windowsazure.core.utils.KeyStoreType;


#### <a name="define-the-main-entry-point-class"></a>定義的主要進入點類別

因為 AzureWebDemo 應用程式的目的是要建立應用程式服務 Web 應用程式，命名主課程此應用程式`WebAppCreator`。 本課程提供呼叫 Azure 服務管理的 API 建立 web 應用程式的主要進入點程式碼。

新增 web 應用程式和 webspace 下列參數定義。 您必須提供您自己的 Azure 訂閱識別碼和憑證資訊。

    public class WebAppCreator {
    
        // Parameter definitions used for authentication.
        private static String uri = "https://management.core.windows.net/";
        private static String subscriptionId = "<subscription-id>";
        private static String keyStoreLocation = "<certificate-store-path>";
        private static String keyStorePassword = "<certificate-password>";
    
        // Define web app parameter values.
        private static String webAppName = "WebDemoWebApp";
        private static String domainName = ".azurewebsites.net";
        private static String webSpaceName = WebSpaceNames.WESTUSWEBSPACE;
        private static String appServicePlanName = "WebDemoAppServicePlan";

位置︰

- `<subscription-id>`是您要在其中建立資源 Azure 訂閱識別碼。
- `<certificate-store-path>`是路徑和檔名，以您的本機認證存放區目錄 JKS 檔案。 例如，`C:/Certificates/CertificateName.jks`的 Linux 和`C:\Certificates\CertificateName.jks`在 Windows 版。
- `<certificate-password>`是您指定在您建立您的 JKS 憑證的密碼。
- `webAppName`可以是您所選擇的; 任何名稱此程序會使用名稱`WebDemoWebApp`。 完整網域名稱是`webAppName`與`domainName`附加，因此在這個案例完整的網域是`webdemowebapp.azurewebsites.net`。
- `domainName`應指定如上所示。
- `webSpaceName`應該是[WebSpaceNames][]類別中定義的值。
- `appServicePlanName`應指定如上所示。

> **附註︰**每次執行這個應用程式，您需要變更的值`webAppName`和`appServicePlanName`（或刪除 Azure 入口網站上的 web 應用程式） 才能執行一次的應用程式。 否則，請執行也會失敗，因為 Azure 上已經有相同的資源。


#### <a name="define-the-web-creation-method"></a>定義的網頁建立方式

接下來，定義建立 web 應用程式的方法。 這種方法， `createWebApp`，指定的 web 應用程式和 webspace 參數。 也會建立並設定所定義之[WebSiteManagementClient][]物件應用程式服務 Web 應用程式管理用戶端。 管理用戶端是建立 Web 應用程式的金鑰。 它提供 RESTful web 服務，讓應用程式，則可電話服務管理 API 管理 web 應用程式 （例如 [建立、 更新和刪除，請執行作業）。

    private static void createWebApp() throws Exception {

        // Specify configuration settings for the App Service management client.
        Configuration config = ManagementConfiguration.configure(
            new URI(uri),
            subscriptionId,
            keyStoreLocation,  // Path to the JKS file
            keyStorePassword,  // Password for the JKS file
            KeyStoreType.jks   // Flag that you are using a JKS keystore
        );

        // Create the App Service Web Apps management client to call Azure APIs
        // and pass it the App Service management configuration object.
        WebSiteManagementClient webAppManagementClient = WebSiteManagementService.create(config);

        // Create an App Service plan for the web app with the specified parameters.
        WebHostingPlanCreateParameters appServicePlanParams = new WebHostingPlanCreateParameters();
        appServicePlanParams.setName(appServicePlanName);
        appServicePlanParams.setSKU(SkuOptions.Free);
        webAppManagementClient.getWebHostingPlansOperations().create(webSpaceName, appServicePlanParams);

        // Set webspace parameters.
        WebSiteCreateParameters.WebSpaceDetails webSpaceDetails = new WebSiteCreateParameters.WebSpaceDetails();
        webSpaceDetails.setGeoRegion(GeoRegionNames.WESTUS);
        webSpaceDetails.setPlan(WebSpacePlanNames.VIRTUALDEDICATEDPLAN);
        webSpaceDetails.setName(webSpaceName);

        // Set web app parameters.
        // Note that the server farm name takes the Azure App Service plan name.
        WebSiteCreateParameters webAppCreateParameters = new WebSiteCreateParameters();
        webAppCreateParameters.setName(webAppName);
        webAppCreateParameters.setServerFarm(appServicePlanName);
        webAppCreateParameters.setWebSpace(webSpaceDetails);

        // Set usage metrics attributes.
        WebSiteGetUsageMetricsResponse.UsageMetric usageMetric = new WebSiteGetUsageMetricsResponse.UsageMetric();
        usageMetric.setSiteMode(WebSiteMode.Basic);
        usageMetric.setComputeMode(WebSiteComputeMode.Shared);

        // Define the web app object.
        ArrayList<String> fullWebAppName = new ArrayList<String>();
        fullWebAppName.add(webAppName + domainName);
        WebSite webApp = new WebSite();
        webApp.setHostNames(fullWebAppName);

        // Create the web app.
        WebSiteCreateResponse webAppCreateResponse = webAppManagementClient.getWebSitesOperations().create(webSpaceName, webAppCreateParameters);

        // Output the HTTP status code of the response; 200 indicates the request succeeded; 4xx indicates failure.
        System.out.println("----------");
        System.out.println("Web app created - HTTP response " + webAppCreateResponse.getStatusCode() + "\n");

        // Output the name of the web app that this application created.
        String shinyNewWebAppName = webAppCreateResponse.getWebSite().getName();
        System.out.println("----------\n");
        System.out.println("Name of web app created: " + shinyNewWebAppName + "\n");
        System.out.println("----------\n");
    }

程式碼會輸出 HTTP 回應狀態的成功或失敗，並且如果成功，輸出建立的 web 應用程式的名稱。


#### <a name="define-the-main-method"></a>定義 main 方法

提供呼叫 createWebApp() 建立 web 應用程式的 main 方法程式碼。

最後，來電`createWebApp`從`main`:

        public static void main(String[] args)
            throws IOException, URISyntaxException, ServiceException,
            ParserConfigurationException, SAXException, Exception {

            // Create web app
            createWebApp();

        }  // end of main()

    }  // end of WebAppCreator class


#### <a name="run-the-application-and-verify-web-app-creation"></a>執行應用程式，並確認建立 web 應用程式

若要確認執行應用程式，請按一下 [**執行 > 執行**。 執行應用程式完成，您應該會看到下列輸出蝕主控台中檢視︰

    ----------
    Web app created - HTTP response 200
    
    ----------
    
    Name of web app created: WebDemoWebApp
    
    ----------

登入 Azure 傳統入口網站，然後按一下 [ **Web 應用程式**。 新的 web 應用程式應該會出現在 Web 應用程式清單中，在幾分鐘內。


## <a name="deploying-an-application-to-the-web-app"></a>部署至 Web 應用程式的應用程式

您執行 AzureWebDemo 及建立新 web 應用程式之後，登入 [傳統] 入口網站**Web 應用程式**，然後按一下**Web 應用程式**清單中選取**WebDemoWebApp** 。 在 web 應用程式的儀表板頁面上，按一下 [**瀏覽**(或按一下 URL， `webdemowebapp.azurewebsites.net`)，以便瀏覽。 因為您尚未 web 應用程式已經不發行任何內容，您會看到 [空白版面配置區] 頁面。

下一步，您會建立 「 Hello World 」 應用程式，並將其部署到 web 應用程式。


### <a name="create-a-jsp-hello-world-application"></a>建立 JSP 什麼應用程式

#### <a name="create-the-application"></a>建立應用程式

若要將示範如何部署至 web 應用程式，下列程序會顯示您如何建立簡單的 「 Hello World 」 Java 應用程式，並將它上傳至您的應用程式建立應用程式服務 Web 應用程式。

1. 按一下 [**檔案 > 新增 > 動態 Web 專案**。 將其命名`JSPHello`。 您不需要變更這個對話方塊中的任何其他設定。 按一下 [**完成**]。

    ![][3]

2. 在 [專案總管] 中展開**JSPHello**專案，以滑鼠右鍵按一下**WebContent**，然後按一下**新增 > JSP 檔案**。 在 [新 JSP 檔案] 對話方塊中的新檔案的名稱`index.jsp`。 按一下 [**下一步**]。

3. 在 [**選取 JSP 範本**] 對話方塊中選取 [**新 JSP 檔案 (html)** ，然後按一下 [**完成]**。

4. 在 index.jsp，新增 [在下列程式碼`<head>`和`<body>`標記節︰

        <head>
          ...
          java.util.Date date = new java.util.Date();
        </head>
    
        <body>
          Hello, the time is <%= date %> 
        </body>


#### <a name="run-the-hello-world-application-in-localhost"></a>在本機執行什麼應用程式

執行這個應用程式之前，您需要設定幾個屬性。

1. 以滑鼠右鍵按一下**JSPHello**專案，然後選取 [**內容]**。

2. 在 [**屬性**] 對話方塊中︰ 選取**Java 建立路徑**、 選取**順序和匯出**] 索引標籤、 核取**JRE 系統文件庫**，然後按一下 [**設定**] 以將其移到清單頂端。

    ![][4]

3. 在 [**屬性**] 對話方塊︰ 選取**目標執行階段**，然後按一下 [**新增**]。

4. **新的伺服器執行階段環境**在對話方塊中，例如**Apache Tomcat v7.0**伺服器，然後按一下 [**下一步**。 在 [ **Tomcat Server** ] 對話方塊中設定**名稱**為`Apache Tomcat v7.0`，並安裝您想要使用的 Tomcat 伺服器的版本目錄設定**Tomcat 安裝目錄**。

    ![][5]

    按一下 [**完成**]。

5. 您就會傳回至**目標執行階段**頁面的 [**屬性**] 對話方塊。 選取**Apache Tomcat v7.0**，然後按一下**[確定**]。

    ![][6]

6. 在蝕**執行**] 功能表中，按一下 [**執行**]。 在 [**執行方式**] 對話方塊中，選取 [**在伺服器上執行**]。 在 [**在伺服器上執行**] 對話方塊中，選取**Tomcat v7.0 伺服器**︰

    ![][7]

    按一下 [**完成**]。

7. 應用程式執行時，您應該會看到**JSPHello**頁面顯示在本機視窗中，蝕 (`http://localhost:8080/JSPHello/`)，會顯示下列訊息︰

    `Hello World, the time is Tue Mar 24 23:21:10 GMT 2015`


#### <a name="export-the-application-as-a-war"></a>將應用程式匯出為一個

匯出 web 專案檔案另存為網頁封存 （馬） 檔案，讓您可以將其部署到 web 應用程式。 下列 web 專案檔案位於 WebContent 資料夾︰

    META-INF
    WEB-INF
    index.jsp

1. 以滑鼠右鍵按一下 WebContent 資料夾，然後選取 [**匯出**。

2. 在 [**選取 [匯出**] 對話方塊中，按一下 [ **Web > 馬**檔案，然後按一下 [**下一步**。

3. 在 [**匯出馬**] 對話方塊中，在目前專案中，選取 src 目錄，包含結尾馬檔案的名稱。 例如︰

    `<project-path>/JSPHello/src/JSPHello.war`

如需有關部署馬檔案的詳細資訊，請參閱[新增 Java Azure 應用程式服務 Web 應用程式至應用程式](web-sites-java-add-app.md)。


### <a name="deploying-the-hello-world-application-using-ftp"></a>部署使用 FTP 認識全球應用程式

選取第三方 FTP 用戶端發佈應用程式。 此程序說明這兩個選項︰ 內建於 Azure; Kudu 主控台然後 FileZilla，使用方便的圖形 UI 的常用工具。

> **附註︰**Azure 工具組蝕的支援儲存帳戶及雲端服務的部署，但目前不支援部署至 web 應用程式。 您可以部署儲存帳戶和使用 Azure 部署專案中[建立 Azure 中蝕 Hello 全球應用程式](http://msdn.microsoft.com/library/azure/hh690944.aspx)，所述的雲端服務，但無法以 web 應用程式。 使用其他方法，例如 FTP:// 或 GitHub 檔案傳送至您的 web 應用程式。

> **附註︰**我們不建議使用 Windows 命令提示字元 （命令列 FTP.EXE 公用程式隨附於 Windows） 中的 FTP。 使用 [作用中的 FTP，例如 FTP.EXE 的 FTP 用戶端通常無法透過防火牆工作。 作用中的 FTP 可指定內部 lan 地址的 FTP 伺服器可能會失敗連線。

如需有關使用 FTP 應用程式服務 web 應用程式的部署的詳細資訊，請參閱下列主題︰

- [部署使用 FTP 公用程式](web-sites-deploy.md)


#### <a name="set-up-deployment-credentials"></a>設定部署認證

請確定您已執行**AzureWebDemo**應用程式建立 web 應用程式。 您會在這個位置中傳輸檔案。

1. 登入 [傳統] 入口網站，然後按一下 [ **Web 應用程式**。 請確定**WebDemoWebApp**會出現在 web 應用程式，清單，並確認它正在執行。 按一下 [ **WebDemoWebApp** ，開啟其**儀表板**頁面]。

2. 在**儀表板**頁面，**快速瀏覽**下的 [**設定您的部署認證**（如果您已經部署認證，這會**重設您的部署認證**）。

    部署認證是以 Microsoft 帳戶相關聯。 您要指定的使用者名稱和密碼，您可以用來部署使用給和 FTP。 您可以使用這些認證部署在您的 Microsoft 帳戶相關聯的所有 Azure 訂閱中的任何 web 應用程式。 提供給和 FTP 部署認證] 對話方塊中的，與記錄的使用者名稱和密碼，以供日後使用。


#### <a name="get-ftp-connection-information"></a>取得 FTP 連線資訊

若要使用 FTP 部署至新建立的 web 應用程式的應用程式的檔案，您需要取得連線資訊。 有兩種方式取得連線資訊。 其中一個方法是瀏覽 web 應用程式的**儀表板**頁面。另一個方式是下載 web 應用程式的 [發佈] 的設定檔。 發佈設定檔是 XML 檔案，提供您的 web 應用程式中 Azure 應用程式服務的資訊，例如 FTP 主機名稱及登入認證。 Azure 帳戶，而不只是此項目相關聯的所有訂閱中的任何 web 應用程式部署，您可以使用這個使用者名稱和密碼。

若要從[Azure 入口網站][]中的 web 應用程式的刀取得 FTP 連線資訊︰

1. 在 [**基本資訊**，找出並複製**FTP 主機名稱**。 這是類似 URI `ftp://waws-prod-bay-NNN.ftp.azurewebsites.windows.net`。

2. 在 [**基本資訊**，找出並複製**FTP/部署使用者名稱**。 這會有表單*webappname\deployment 使用者名稱*。例如`WebDemoWebApp\deployer77`。

若要從發佈設定檔取得 FTP 連線資訊︰

1. 在 web 應用程式的刀中按一下 [**取得發佈的設定檔**]。 這將.publishsettings 檔案下載到本機磁碟機。

2. 在 XML 編輯器或文字編輯器中開啟.publishsettings 檔案，並尋找`<publishProfile>`元素包含`publishMethod="FTP"`。 看起來應該如下所示︰

        <publishProfile
            profileName="WebDemoWebApp - FTP"
            publishMethod="FTP"
            publishUrl="ftp://waws-prod-bay-NNN.ftp.azurewebsites.windows.net/site/wwwroot"
            ftpPassiveMode="True"
            userName="WebDemoWebApp\$WebDemoWebApp"
            userPWD="<deployment-password>"
            ...
        </publishProfile>

3. 請注意，web 應用程式的`publishProfile`設定地圖 FileZilla 網站管理員設定，如下所示︰

- `publishUrl`是**FTP 主機名稱**，您設定**主應用程式**中的值相同。
- `publishMethod="FTP"`表示您**FTP-檔案傳輸通訊協定**，並使用**純文字 FTP****加密**設定**通訊協定**。
- `userName`與`userPWD`實際的使用者名稱和密碼值所指定當您重設部署認證鍵。 `userName`相同**部署 / FTP 使用者**。 對應到**使用者**與 FileZilla 中的**密碼**。
- `ftpPassiveMode="True"`表示 FTP 站台使用被動式 FTP 傳輸;選取 [**轉接設定**] 索引標籤上的 [**被動**]。


#### <a name="configure-the-web-app-to-host-a-java-application"></a>設定 Web 應用程式來裝載 Java 應用程式

發佈應用程式之前，您需要變更幾個設定的設定，使 web 應用程式可以主控 Java 應用程式。

1. 在 [傳統] 入口網站中，移至 web 應用程式的**儀表板**頁面，按一下 [**設定**]。 在 [**設定**] 頁面中，指定下列設定。

2. **Java 版本**中預設為**關閉**。選取的 Java 版本，您的應用程式的目標。例如 1.7.0_51。 完成之後，也請確定**Web 容器**設定 Tomcat 伺服器的版本。

3. 在 [**預設文件**，新增 index.jsp 並將它移到清單頂端。 （web 應用程式的預設檔案是 hostingstart.html）。

4. 按一下 [**儲存**]。


#### <a name="publish-your-application-using-kudu"></a>發佈您的應用程式使用 Kudu

若要發佈應用程式的其中一個方法是使用內建於 Azure Kudu 偵錯主控台。 Kudu 已知會穩定，與應用程式服務 Web 應用程式與 Tomcat 伺服器。 您可以存取 web 應用程式的主控台瀏覽至下列形式的 URL:

`https://<webappname>.scm.azurewebsites.net/DebugConsole`

1. 此程序 Kudu 主控台位於下列 URL;瀏覽到此位置︰

    `https://webdemowebapp.scm.azurewebsites.net/DebugConsole`

2. 從上方的功能表中，選取 [**偵錯主控台 > CMD**。

3. 在主控台命令列上，瀏覽至`/site/wwwroot`(或按一下 [ `site`，然後`wwwroot`在頁面頂端的 [目錄] 檢視中):

    `cd /site/wwwroot`

4. 指定**Java 版本**之後，Tomcat 伺服器應該建立 webapps 目錄。 在主控台命令列上，瀏覽至 webapps 目錄︰

    `mkdir webapps`

    `cd webapps`

5. 拖曳從 JSPHello.war`<project-path>/JSPHello/src/`拖放到] 下的 Kudu 目錄檢視`/site/wwwroot/webapps`。 不將它拖曳到 「 拖曳到這裡上傳並 zip 」] 區域中，因為 Tomcat 會將它解壓縮。

  ![][8]

在第一個 JSPHello.war 出現在 [目錄] 區域中單獨︰

  ![][9]

短的時間 （可能是因為小於 5 分鐘） Tomcat 伺服器會將它解壓縮。 馬檔案到未解壓縮的 JSPHello 目錄。 按一下以查看 index.jsp 是否已解壓縮並那里複製的根目錄。 如果是這樣，瀏覽回 webapps 目錄，以查看是否已建立解除封裝的 JSPHello 目錄。 如果您沒有看到這些項目，請稍候，然後重複。

  ![][10]


#### <a name="publish-your-application-using-filezilla-optional"></a>發佈您的應用程式使用 FileZilla （選用）

您可以使用發佈應用程式的另一個工具是 FileZilla，常用的協力廠商 FTP 用戶端，使用方便的圖形 UI。 您可以從下載並安裝 FileZilla [http://filezilla-project.org/](http://filezilla-project.org/)如果您還沒有它。 如需有關如何使用用戶端的詳細資訊，請參閱的[FileZilla 文件](https://wiki.filezilla-project.org/Documentation)和此部落格文章上[FTP 用戶端-第 4 部分︰ FileZilla](http://blogs.msdn.com/b/robert_mcmurray/archive/2008/12/17/ftp-clients-part-4-filezilla.aspx)。

1. 在 [FileZilla，按一下 [**檔案 > 網站管理員**。
2. 在 [**網站管理員**] 對話方塊中，按一下 [**新網站**]。 新的空白 FTP 站台會出現在**選取的項目**提示您提供的名稱。 此程序將其命名`AzureWebDemo-FTP`。

    在 [**一般**] 索引標籤中，指定下列設定︰
    - **Host （主機):**輸入您所複製的儀表板的**FTP 主機名稱**。
    - **連接埠︰**（留白，這是被動式傳輸以及伺服器會決定要使用的連接埠。）
    - **通訊協定︰**FTP 檔案傳輸通訊協定
    - **加密︰**使用純文字 FTP
    - **登入類型︰**標準模式
    - **使用者︰**輸入部署 / FTP 您複製的儀表板的使用者。 這是完整的 FTP username 具有表單*webappname\username*。
    - **密碼︰**輸入您指定在您設定的部署認證的密碼。

    在 [**轉接設定**] 索引標籤上選取 [**被動**]。

3. 按一下 [**連線**]。 如果成功，FileZilla 的主控台會顯示`Status: Connected`訊息和問題`LIST`命令可列出目錄的內容。

4. 在**本機**的 [網站] 面板中，選取 [來源 JSPHello.war 檔案所在的目錄。路徑會類似下列動作︰

    `<project-path>/JSPHello/src/`

5. 在 [**遠端**網站] 面板中，選取目的資料夾。 部署馬檔案`webapps`目錄在 web 應用程式的根目錄。 瀏覽至`/site/wwwroot`，以滑鼠右鍵按一下`wwwroot`，然後選取 [**建立目錄**。 命名目錄`webapps`輸入該目錄。

6. 傳送至 JSPHello.war `/site/wwwroot/webapps`。 選取 JSPHello.war**本機**的檔案清單中，以滑鼠右鍵按一下它，選取 [**上傳**。 您應該會看到它會出現在`/site/wwwroot/webapps`。

7. 複製 JSPHello.war webapps 目錄之後，會自動解壓縮 Tomcat 伺服器 （解壓縮） 馬檔案中的檔案。 雖然解壓縮幾乎可以立即開始 Tomcat 伺服器，可能需要長時間 （可能是小時） FTP 用戶端中顯示的檔案。


#### <a name="run-the-hello-world-application-on-the-web-app"></a>在 Web 應用程式上執行什麼應用程式

1. 您已上傳馬檔案並驗證 Tomcat 伺服器已建立解壓縮後`JSPHello`目錄中，瀏覽] 以`http://webdemowebapp.azurewebsites.net/JSPHello`執行應用程式。

    > **附註︰**如果您按一下 [**瀏覽**] 從 [傳統] 入口網站，您可能會收到 [預設] 網頁的說 「 此 Java 基礎 web 應用程式已順利建立。 」 您可能必須重新整理才能檢視，而不是預設網頁應用程式輸出的網頁。

2. 應用程式執行時，您應該會看到下列輸出網頁︰

    `Hello World, the time is Tue Mar 24 23:21:10 GMT 2015`


#### <a name="clean-up-azure-resources"></a>清除 Azure 資源

此程序所建立的應用程式服務 web 應用程式。 計費資源存在於。 除非您要繼續使用 web 應用程式的測試或開發，您應該考慮停止或刪除。 已停止在 web 應用程式仍會對小型的費用，但您可以隨時重新啟動它。 刪除 web 應用程式，便會清除您上傳到它的所有資料。

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

  [1]: ./media/java-create-azure-website-using-java-sdk/eclipse-maven-repositories-rebuild-index.png
  [2]: ./media/java-create-azure-website-using-java-sdk/eclipse-new-java-class.png
  [3]: ./media/java-create-azure-website-using-java-sdk/eclipse-new-dynamic-web-project.png
  [4]: ./media/java-create-azure-website-using-java-sdk/eclipse-java-build-path.png
  [5]: ./media/java-create-azure-website-using-java-sdk/eclipse-targeted-runtimes-tomcat-server.png
  [6]: ./media/java-create-azure-website-using-java-sdk/eclipse-targeted-runtimes-properties-page.png
  [7]: ./media/java-create-azure-website-using-java-sdk/eclipse-run-on-server.png
  [8]: ./media/java-create-azure-website-using-java-sdk/kudu-console-drag-drop.png
  [9]: ./media/java-create-azure-website-using-java-sdk/kudu-console-jsphello-war-1.png
  [10]: ./media/java-create-azure-website-using-java-sdk/kudu-console-jsphello-war-2.png
 

[Azure 應用程式服務]: http://go.microsoft.com/fwlink/?LinkId=529714
[Web 平台安裝程式]: http://go.microsoft.com/fwlink/?LinkID=252838
[針對蝕 azure 工具組]: https://msdn.microsoft.com/library/azure/hh690946.aspx
[Azure 傳統入口網站]: https://manage.windowsazure.com
[什麼是 Azure AD 目錄]: http://technet.microsoft.com/library/jj573650.aspx
[建立及用 Azure 上傳管理憑證]: ../cloud-services/cloud-services-certs-create.md
[索引鍵和憑證管理工具 (keytool)]: http://docs.oracle.com/javase/6/docs/technotes/tools/windows/keytool.html
[WebSiteManagementClient]: http://azure.github.io/azure-sdk-for-java/com/microsoft/azure/management/websites/WebSiteManagementClient.html
[WebSpaceNames]: http://dl.windowsazure.com/javadoc/com/microsoft/windowsazure/management/websites/models/WebSpaceNames.html
[Azure 入口網站]: https://portal.azure.com
