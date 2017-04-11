<properties
    pageTitle="指定 Node.js 版本"
    description="瞭解如何指定 Node.js Azure 網站和雲端服務所使用的版本"
    services=""
    documentationCenter="nodejs"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="specifying-a-nodejs-version-in-an-azure-application"></a>Azure 應用程式中指定 Node.js 版本

當主機服務 Node.js 應用程式，您可以確保您的應用程式使用特定 Node.js 的版本。 有數種方式可完成這項作業裝載於 Azure 的應用程式。

##<a name="default-versions"></a>預設版本

Azure 所提供的 Node.js 版本會持續更新。 除非另行指定預設版本中指定`WEBSITE_NODE_DEFAULT_VERSION`會使用環境變數。 若要覆寫此預設值，請依照這篇文章的下列各節中的步驟

> [AZURE.NOTE] 如果您主控您的應用程式 Azure 雲端服務 （網頁或背景工作角色，） 中，您已經部署應用程式的第一次 Azure 會嘗試使用同一版本的 Node.js，當您安裝在您的開發環境符合其中一個提供 Azure 預設版本。

##<a name="versioning-with-packagejson"></a>使用 package.json 的版本設定

您可以指定 Node.js **package.json**檔案中加入所使用的版本︰

    "engines":{"node":version}

*版本*號碼在哪裡特定版本使用。 您可以可以指定更複雜的條件的版本，例如︰

    "engines":{"node": "0.6.22 || 0.8.x"}

由於 0.6.22 不是其中一個版本的主機服務的環境，0.8 會數列所提供的最新版本改用-0.8.4。

##<a name="versioning-websites-with-app-settings"></a>使用應用程式設定的版本設定網站
如果您是網站中的應用程式，您可以設定環境變數**WEBSITE_NODE_DEFAULT_VERSION**至所要的版本。 

##<a name="versioning-cloud-services-with-powershell"></a>使用 PowerShell 的版本設定雲端服務

如果您裝載雲端服務中的應用程式，並部署使用 PowerShell 的 Azure 應用程式，您可以使用**設定 AzureServiceProjectRole** PowerShell cmdlet 來覆寫預設 Node.js 版本。 例如︰

    Set-AzureServiceProjectRole WebRole1 Node 0.8.4

請注意，上述陳述式的參數會區分大小寫。  您可以驗證，請核取 [**引擎**] 屬性，在您的角色**package.json**中已選取正確的 Node.js 版本。

您也可以使用**取得 AzureServiceProjectRoleRuntime**擷取 Node.js 版本可用於裝載雲端服務的應用程式的清單。  一定要確認的 Node.js 取決於您的專案的版本是此清單中。

##<a name="using-a-custom-version-with-azure-websites"></a>使用 Azure 網站使用自訂的版本

雖然 Azure 提供的 Node.js 的多個預設版本，您可能會想要使用預設不會提供的版本。 如果做為 Azure 網站裝載您的應用程式，您可以使用**iisnode.yml**檔案完成。 下列步驟逐步 Azure 網站使用自訂的 Node.Js 版本的程序︰

1. 建立新的目錄，，並建立目錄內**server.js**檔案。 **Server.js**檔案應包含下列各項︰

        var http = require('http');
        http.createServer(function(req,res) {
          res.writeHead(200, {'Content-Type': 'text/html'});
          res.end('Hello from Azure running node version: ' + process.version + '</br>');
        }).listen(process.env.PORT || 3000);

    這會顯示您瀏覽網站時，使用 Node.js 版本。

2. 建立新的網站，並記下網站的名稱。 例如，下列範例使用[Azure 的命令列工具]建立新的 Azure 網站命名**覽**，並啟用給存放庫的網站。

        azure site create mywebsite --git

3. 建立新的目錄，為**bin**子項包含**server.js**檔案的目錄。

4. 下載特定**node.exe** （Windows 版本） 您想要使用您的應用程式的版本。 例如，下列範例使用**curl**下載版本 0.8.1:

        curl -O http://nodejs.org/dist/v0.8.1/node.exe

    將**node.exe**檔案儲存到先前所建立的 [**回收筒**] 資料夾。

5. 建立**iisnode.yml**檔**server.js**檔案，為相同的目錄，然後將下列內容加入**iisnode.yml**檔案︰

        nodeProcessCommandLine: "D:\home\site\wwwroot\bin\node.exe"

    此路徑是位置**node.exe**檔案，在專案中的都位於一旦發行至 Azure 網站的應用程式。

6. 發佈您的應用程式。 例如 [我在自己建立新的網站，就可以給參數與較舊版本，因為下列命令會將應用程式檔案新增到我本機給儲存機制，然後傳送至網站存放庫︰

        git add .
        git commit -m "testing node v0.8.1"
        git push azure master

    已發佈應用程式之後，請在瀏覽器中開啟網站。 您應該會看到訊息，說明 「 認識從 Azure 執行節點的版本︰ v0.8.1 」。

##<a name="next-steps"></a>後續步驟

瞭解如何指定 Node.js 應用程式所使用的版本，瞭解如何[使用模組]、[建立並部署 Node.js 網站]，以及[如何使用 for Mac 和 Linux 的 Azure 命令列工具]。

如需詳細資訊，請參閱[Node.js 開發人員中心](/develop/nodejs/)。

[如何使用 Azure 命令列工具 for Mac 和 Linux]: xplat-cli-install.md
[Azure 的命令列工具]: xplat-cli-install.md
[使用模組]: nodejs-use-node-modules-azure-apps.md
[建立並部署 Node.js 網站]: web-sites-nodejs-develop-deploy-mac.md
