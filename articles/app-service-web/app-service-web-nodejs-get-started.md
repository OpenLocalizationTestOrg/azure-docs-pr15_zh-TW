<properties
    pageTitle="Azure 應用程式服務中的 Node.js web apps 快速入門"
    description="瞭解如何部署 Node.js 應用程式中 Azure 應用程式服務 web 應用程式。"
    services="app-service\web"
    documentationCenter="nodejs"
    authors="cephalin"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="get-started-article"
    ms.date="07/01/2016"
    ms.author="cephalin"/>

# <a name="get-started-with-nodejs-web-apps-in-azure-app-service"></a>Azure 應用程式服務中的 Node.js web apps 快速入門

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

本教學課程中會顯示如何建立簡單的[Node.js]應用程式，並將其部署到[Azure 應用程式服務]中，從命令列的環境，例如 cmd.exe 或被。 可以執行 Node.js 任何作業系統上可以遵循此教學課程中的指示進行。

>[AZURE.INCLUDE [app-service-linux](../../includes/app-service-linux.md)] 

<a name="prereq"></a>
## <a name="prerequisites"></a>必要條件

- [Node.js]
- [Bower]
- [Yeoman]
- [給]
- [Azure CLI]
- Microsoft Azure 帳戶。 如果您沒有帳戶，可以[免費試用版註冊]] 或 [[啟動您的 Visual Studio 訂閱權益]。

## <a name="create-and-deploy-a-simple-nodejs-web-app"></a>建立並部署簡單的 Node.js web 應用程式

1. 開啟您所選擇的命令列終端機，和安裝[Express Yeoman 產生器]。

        npm install -g generator-express

2. `CD`使用目錄，並產生快速應用程式使用下列語法︰

        yo express
        
    選擇下列選項出現提示時︰  

    `? Would you like to create a new directory for your project?`**[是]**  
    `? Enter directory name`**{應用程式名稱}**  
    `? Select a version to install:`**MVC**  
    `? Select a view engine to use:`**碧眼**  
    `? Select a css preprocessor to use (Sass Requires Ruby):`**無**  
    `? Select a database to use:`**無**  
    `? Select a build tool to use:`**步兵**

3. `CD`以新的應用程式的根目錄並開始以確定您的開發環境中執行︰

        npm start

    在瀏覽器中瀏覽至<http://localhost:3000> ，請確定您可以看到 Express 的 [首頁] 頁面。 一旦您已正確地驗證您的應用程式就會執行，使用`Ctrl-C`使其停止。
    
1. 變更 ASM 模式並登入 Azure （您需要[Azure CLI](#prereq)）︰

        azure config mode asm
        azure login

    請依照提示您 Azure 訂閱的 Microsoft 帳戶在瀏覽器中，繼續登入。

2. 請確定您仍在您的應用程式的根目錄中，然後使用 [下一步] 命令以唯一的應用程式名稱 Azure 中建立的應用程式服務應用程式資源。 例如︰ http://{appname}.azurewebsites.net

        azure site create --git {appname}

    請依照提示選取要部署至此 Azure 區域。 如果您從未已設定給/FTP 部署認證，Azure 訂閱，您也會提示您建立。

3. 從您的應用程式的根目錄開啟./config/config.js 檔案，並變更生產連接埠`process.env.port`;您`production`中的屬性`config`物件看起來應該像下列範例︰

        production: {
            root: rootPath,
            app: {
                name: 'express1'
            },
            port: process.env.port,
        }

    這個選項可讓您回應至網頁上的預設連接埠要求該 iisnode 接聽的 Node.js 應用程式。
    
4. 開啟./package.json 並新增`engines`屬性，以[指定所要的 Node.js 版本](#version)。

        "engines": {
            "node": "6.6.0"
        }, 

4. 儲存變更，然後使用給將您的應用程式部署到 Azure:

        git add .
        git commit -m "{your commit message}"
        git push azure master

    Express 產生器已經提供.gitignore 檔案，因此您`git push`不使用頻寬嘗試上傳 node_modules / 目錄。

5. 最後，啟動瀏覽器中的即時 Azure 應用程式︰

        azure site browse

    您現在應該會看到即時 Azure 應用程式服務中執行 Node.js web 應用程式。
    
    ![例如，瀏覽功能至應用程式。][deployed-express-app]

## <a name="update-your-nodejs-web-app"></a>更新您的 Node.js web 應用程式

若要更新 Node.js web 應用程式在應用程式服務中執行，只要執行`git add`， `git commit`，及`git push`跟您第一次部署 web 應用程式時，您做的一樣。
     
## <a name="how-app-service-deploys-your-nodejs-app"></a>應用程式服務會 Node.js 應用程式的部署

Azure 的應用程式服務使用[iisnode]執行 Node.js 應用程式。 Azure CLI 和 Kudu 引擎 （給部署） 共同合作來為您提供精簡的體驗，當您開發及部署 Node.js 應用程式命令。 

- `azure site create --git`可辨識 server.js 或 app.js 的常見 Node.js 模式並 iisnode.yml 中建立您的根目錄。 若要自訂 iisnode，您可以使用這個檔案。
- 在`git push azure master`，Kudu 會自動執行下列部署工作︰

    - 如果 package.json 存放庫根目錄，請執行`npm install --production`。
    - 產生 Web.config 的 iisnode 指向您開始中的指令碼 package.json （例如 server.js 或 app.js）。
    - 自訂 Web.config 好偵錯節點檢查與應用程式。
    
## <a name="use-a-nodejs-framework"></a>使用 Node.js 架構

如果您使用的常見 Node.js 架構，例如[Sails.js] [SAILSJS]或[MEAN.js] [MEANJS]開發應用程式，您可以將這些應用程式服務部署。 熱門 Node.js 架構都有其特定的缺點，並保留更新套件相依性。 不過，應用程式服務讓 stdout 和 stderr 記錄，讓您知道完全有什麼新鮮事與您的應用程式與會相應地變更。 如需詳細資訊，請參閱[取得從 iisnode stdout 和 stderr 記錄](#iisnodelog)。

下列教學課程會告訴您如何使用應用程式服務中的特定架構︰

- [Azure 應用程式服務部署 Sails.js 的 web 應用程式]
- [建立 Azure 應用程式服務中的 Socket.IO Node.js 聊天室應用程式]
- [如何使用 io.js Azure 應用程式服務 Web 應用程式]

<a name="version"></a>
## <a name="use-a-specific-nodejs-engine"></a>使用特定的 Node.js 引擎

在您的一般工作流程，您可以告訴使用特定 Node.js 引擎，亦即 package.json 中的應用程式服務。
例如︰

    "engines": {
        "node": "6.6.0"
    }, 

Kudu 部署引擎會決定哪些 Node.js 引擎，若要以下列順序使用︰

- 首先，看看是否 iisnode.yml`nodeProcessCommandLine`指定。 如果是的然後使用的。
- 接下來，看看是否 package.json`"node": "..."`中指定`engines`物件。 如果是的然後使用的。
- 選擇預設的 [預設 Node.js 版本]。

>[AZURE.NOTE] 建議您明確定義您想要的 Node.js 引擎。 可以變更預設 Node.js 版本，您可能會收到錯誤 Azure web 應用程式中，因為預設 Node.js 版本不適用於您的應用程式。

<a name="iisnodelog"></a>
## <a name="get-stdout-and-stderr-logs-from-iisnode"></a>從 iisnode 取得 stdout 和 stderr 記錄檔

若要閱讀 iisnode 記錄，請遵循下列步驟。

> [AZURE.NOTE] 完成這些步驟之後，記錄檔可能不存在，直到發生錯誤。

1. 開啟 Azure CLI 提供 iisnode.yml 檔案。

2. 設定兩個下列參數︰ 

        loggingEnabled: true
        logDirectory: iisnode
    
    在一起，會告訴 iisnode 在應用程式服務 stdout 和 stderror 輸出放 D:\home\site\wwwroot\**iisnode** 目錄。

3. 儲存變更，然後使用下列給命令將您的變更推入 Azure:

        git add .
        git commit -m "{your commit message}"
        git push azure master
   
    iisnode 正在設定。 接下來的步驟會顯示如何存取這些記錄。
     
4. 在瀏覽器中存取 Kudu 偵錯主控台應用程式，也就是在︰

        https://{appname}.scm.azurewebsites.net/DebugConsole 

    此 URL 的加法與 web 應用程式 URL 「*.scm。*」 DNS 名稱。 如果您省略 URL 的元件，您會收到 404 錯誤。

5. 瀏覽至 D:\home\site\wwwroot\iisnode

    ![瀏覽至 iisnode 記錄檔的位置。][iislog-kudu-console-find]

6. 按一下您想要讀取的記錄檔的 [**編輯**] 圖示。 您也可以按一下**下載**或**刪除**如果您想要。

    ![開啟 iisnode 記錄檔。][iislog-kudu-console-open]

    現在您可以看到的記錄檔以協助您偵錯應用程式服務部署。
    
    ![檢查 iisnode 記錄檔。][iislog-kudu-console-read]

## <a name="debug-your-app-with-node-inspector"></a>偵錯節點檢查您應用程式

如果您使用節點檢查偵錯 Node.js 應用程式時，您可以即時應用程式服務應用程式中使用它。 節點檢查已預先安裝在 iisnode 安裝應用程式服務。 如果您透過給部署時，自動產生 Web.config Kudu 從已包含您需要啟用節點檢查的所有設定。

若要啟用節點檢查，請遵循下列步驟︰

1. 開啟在存放庫根目錄 iisnode.yml，然後指定下列參數︰ 

        debuggingEnabled: true
        debuggerExtensionDll: iisnode-inspector.dll

3. 儲存變更，然後使用下列給命令將您的變更推入 Azure:

        git add .
        git commit -m "{your commit message}"
        git push azure master
   
4. 現在，只要瀏覽至您的應用程式開始檔案所指定的開始中的指令碼您 package.json，有 /debug 加入 URL。 例如，

        http://{appname}.azurewebsites.net/server.js/debug
    
    或者，
    
        http://{appname}.azurewebsites.net/app.js/debug

## <a name="more-resources"></a>更多資源

- [Azure 應用程式中指定 Node.js 版本](../nodejs-specify-node-version-azure-apps.md)
- [最佳作法和 Node.js 應用程式上 Azure 疑難排解指南](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md)
- [如何偵錯 Node.js web app 中 Azure 應用程式服務](web-sites-nodejs-debug.md)
- [Azure 應用程式中使用 Node.js 模組](../nodejs-use-node-modules-azure-apps.md)
- [Azure 應用程式服務 Web 應用程式︰ Node.js](http://blogs.msdn.com/b/silverlining/archive/2012/06/14/windows-azure-websites-node-js.aspx)
- [Node.js 開發人員中心](/develop/nodejs/)
- [Azure 應用程式服務中的 web apps 快速入門](app-service-web-get-started.md)
- [探索把私人 Kudu 偵錯主控台]

<!-- URL List -->

[Azure CLI]: ../xplat-cli-install.md
[Azure 應用程式服務]: ../app-service/app-service-value-prop-what-is.md
[啟動您的 Visual Studio 訂閱優惠]: http://go.microsoft.com/fwlink/?LinkId=623901
[Bower]: http://bower.io/
[建立 Azure 應用程式服務中的 Socket.IO Node.js 聊天室應用程式]: ./web-sites-nodejs-chat-app-socketio.md
[Azure 應用程式服務部署 Sails.js 的 web 應用程式]: ./app-service-web-nodejs-sails.md
[探索把私人 Kudu 偵錯主控台]: /documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[快速 Yeoman 產生器]: https://github.com/petecoop/generator-express
[給]: http://www.git-scm.com/downloads
[如何使用 io.js Azure 應用程式服務 Web 應用程式]: ./web-sites-nodejs-iojs.md
[iisnode]: https://github.com/tjanczuk/iisnode/wiki
[MEANJS]: http://meanjs.org/
[Node.js]: http://nodejs.org
[SAILSJS]: http://sailsjs.org/
[註冊免費試用版]: http://go.microsoft.com/fwlink/?LinkId=623901
[web app]: ./app-service-web-overview.md
[Yeoman]: http://yeoman.io/

<!-- IMG List -->

[deployed-express-app]: ./media/app-service-web-nodejs-get-started/deployed-express-app.png
[iislog-kudu-console-find]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-navigate.png
[iislog-kudu-console-open]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-open.png
[iislog-kudu-console-read]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-read.png
