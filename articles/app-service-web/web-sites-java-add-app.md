<properties 
    pageTitle="新增至 Azure 應用程式服務 Web 應用程式的 Java 應用程式" 
    description="本教學課程教您如何新增頁面或您的執行個體的 Azure 應用程式服務 Web 應用程式已設定為使用 Java 應用程式。" 
    services="app-service\web" 
    documentationCenter="java" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="add-a-java-application-to-azure-app-service-web-apps"></a>新增至 Azure 應用程式服務 Web 應用程式的 Java 應用程式

一旦您在[建立 Java web app 中 Azure 應用程式服務](web-sites-java-get-started.md)如初始化 Java web 應用程式中[Azure 應用程式服務][]，您可以放在您馬**webapps**資料夾中上傳您的應用程式。

導覽路徑**webapps**資料夾，會根據您如何設定 Web 應用程式執行個體。

- 如果您使用 Azure Marketplace 來設定您的 web 應用程式， **webapps**資料夾的路徑是在表單中**d:\home\site\wwwroot\bin\application\_server\webapps**，其中**應用程式\_伺服器**是作用中的 Web 應用程式執行個體的應用程式伺服器名稱。 
- 如果您使用的 Azure 設定 UI 設定 web 應用程式，表單**d:\home\site\wwwroot\webapps**表示**webapps**資料夾的路徑。 

請注意，您可以使用來源控制項上傳您的應用程式或網頁，包括[連續整合案例](app-service-continuous-deployment.md)。 FTP 也是上傳您的應用程式或網頁的選項。

Tomcat web 應用程式的附註︰ Tomcat 應用程式伺服器您已上傳後馬檔案至**webapps**資料夾會偵測到您已新增，並會自動載入。 請注意，是否您可以複製檔案 （非馬檔案） 的根目錄，應用程式伺服器需要重新啟動會使用這些檔案。 Azure 上執行的 Tomcat Java web 應用程式的自動載入功能為基礎的新馬檔案新增，或新的檔案或目錄新增至**webapps**資料夾。 

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱[Java 開發人員中心](/develop/java/)。

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- External Links -->
[Azure 應用程式服務]: http://go.microsoft.com/fwlink/?LinkId=529714
