<properties 
    pageTitle="如何建立 Linux 上的應用程式服務的 Web 應用程式 |Microsoft Azure" 
    description="Web 應用程式建立工作流程 Linux 上的應用程式服務。" 
    keywords="azure 應用程式服務、 web 應用程式、 linux oss"
    services="app-service" 
    documentationCenter="" 
    authors="naziml" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016" 
    ms.author="naziml"/>

# <a name="create-a-web-app-with-app-service-on-linux"></a>建立 Web 應用程式上 Linux 的應用程式服務

## <a name="using-the-management-portal-to-create-your-web-app"></a>使用 [管理] 入口網站建立 web 應用程式
您可以開始建立您的 Web 應用程式上 Linux，從[管理入口網站](https://portal.azure.com)下圖所示。

![][1]

一旦您選取下列選項，您就會顯示建立刀下圖所示。 

![][2]

-   為您的 web 應用程式名稱]。
-   選擇現有的資源群組或建立新的項目。 （請參閱可用[的限制] 區段](./app-service-linux-intro.md)中的區域）。
-   選擇現有的應用程式服務方案，或建立新的其中一個 （請參閱應用程式服務方案附註在[限制] 區段](./app-service-linux-intro.md)中）。 
-   選擇您想要使用的應用程式堆疊。 您會收到選擇 Node.js 的多個版本或 PHP。 

建立的應用程式之後，您可以變更應用程式堆疊，從 [應用程式設定下圖所示即可。

![][3]

## <a name="deploying-your-web-app"></a>部署 web 應用程式

選擇 [部署選項]，從 [管理] 入口網站可讓您使用本機給存放庫或 GitHub 存放庫部署應用程式的選項。 指示來進行同樣的非 Linux web 應用程式，而且您可以依照 GitHub 我們[本機給部署](./app-service-deploy-local-git.md)或我們[連續部署](./app-service-continuous-deployment.md)文件中的指示進行。

您也可以使用 FTP 上傳您的應用程式至您的網站。 您可以取得 FTP 端點 web 應用程式從 [診斷記錄] 區段下圖所示。

![][4]


## <a name="next-steps"></a>後續步驟 ##

* [什麼是 Linux 上的應用程式服務？](./app-service-linux-intro.md)
* [Node.js linux 的 Web 應用程式中使用 PM2 設定](./app-service-linux-using-nodejs-pm2.md)

<!--Image references-->
[1]: ./media/app-service-linux-how-to-create-a-web-app/top-level-create.png
[2]: ./media/app-service-linux-how-to-create-a-web-app/create-blade.png
[3]: ./media/app-service-linux-how-to-create-a-web-app/application-settings-change-stack.png
[4]: ./media/app-service-linux-how-to-create-a-web-app/diagnostic-logs-ftp.png
