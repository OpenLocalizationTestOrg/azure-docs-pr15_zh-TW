<properties
    pageTitle="如何偵錯 Node.js web app 中 Azure 應用程式服務"
    description="瞭解如何偵錯 Node.js web app 中 Azure 應用程式服務。"
    tags="azure-portal"
    services="app-service\web"
    documentationCenter="nodejs"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="how-to-debug-a-nodejs-web-app-in-azure-app-service"></a>如何偵錯 Node.js web app 中 Azure 應用程式服務

Azure 提供內建的診斷，以協助偵錯 Node.js 裝載於[Azure 應用程式服務](http://go.microsoft.com/fwlink/?LinkId=529714)Web 應用程式的應用程式。 本文中，您將學習如何啟用 stdout 及 stderr 的記錄，請在瀏覽器中顯示錯誤資訊以及如何下載並檢視記錄檔。

由[IISNode]提供診斷裝載於 Azure Node.js 應用程式。 雖然這份文件收集診斷資訊討論的常見設定，它不提供完整的參考使用 IISNode。 如需有關如何使用 IISNode 的詳細資訊，請參閱上 GitHub 的[IISNode 讀我檔案]。

<a id="enablelogging"></a>
## <a name="enable-logging"></a>啟用記錄功能

根據預設，應用程式服務 web 應用程式只會擷取部署，例如當您部署使用給在 web 應用程式的診斷資訊。 如果您部署期間，例如無法安裝模組中**package.json**，參照時遇到問題，或者如果您使用的自訂部署指令碼，這項資訊會很有用。

若要啟用 stdout 和 stderr 資料流的記錄，您必須在 Node.js 應用程式的根目錄建立**IISNode.yml**檔，並新增下列︰

    loggingEnabled: true

這可讓 stderr 和 stdout 從 Node.js 應用程式的記錄。

**IISNode.yml**檔案也可控制是否好記的錯誤或開發人員錯誤會傳回瀏覽器時發生錯誤。 若要啟用開發人員錯誤，請先**IISNode.yml**檔案中加入的下列行︰

    devErrorsEnabled: true

一旦啟用此選項，IISNode 會傳回最後一個 64k 傳送至 stderr，而不是好記的錯誤，例如 「 發生內部伺服器錯誤 」 的資訊。

> [AZURE.NOTE] 雖然 devErrorsEnabled 實用開發期間診斷問題時，啟用它生產環境中可能會導致開發錯誤傳送給使用者。

如果您的應用程式中不存在**IISNode.yml**檔案，您必須重新發佈更新的應用程式之後的 web 應用程式。 如果您只變更先前已發佈的現有**IISNode.yml**檔中的設定，沒有重新啟動。

> [AZURE.NOTE] 如果您的 web 應用程式建立使用 Azure 命令列工具] 或 [Azure PowerShell Cmdlet，會自動建立預設**IISNode.yml**檔案。

重新啟動 web 應用程式，請選取 web 應用程式在[Azure 入口網站](https://portal.azure.com)，然後按 [**重新啟動**] 按鈕︰

![重新啟動] 按鈕][restart-button]

如果您的開發環境中安裝 Azure 命令列工具，您可以使用下列命令，重新啟動 web 應用程式︰

    azure site restart [sitename]

> [AZURE.NOTE] LoggingEnabled 和 devErrorsEnabled 都是最常用的 IISNode.yml 設定選項來擷取的診斷資訊，IISNode.yml 可以用於設定各種不同的主機服務環境的選項。 [設定] 選項的完整清單，請參閱[iisnode_schema.xml](https://github.com/tjanczuk/iisnode/blob/master/src/config/iisnode_schema.xml)檔案。

<a id="viewlogs"></a>
## <a name="accessing-logs"></a>存取記錄

診斷記錄可存取三種方式。使用檔案傳輸通訊協定 (FTP)，下載 Zip 封存中，或為即時更新的記錄 （也稱為尾端） 資料流。 下載 Zip 保存記錄檔，或檢視的即時資料流需要 Azure 命令列工具。 這些可以安裝使用下列命令︰

    npm install azure-cli -g

安裝後，就可以使用 「 azure] 命令來存取工具]。 使用 Azure 訂閱時，必須先設定命令列工具。 如需如何完成這項工作，請參閱**如何下載並匯入發佈設定**[如何使用 Azure 命令列工具](../xplat-cli-connect.md)文章] 區段。

###<a name="ftp"></a>FTP

若要透過 FTP 存取的診斷資訊，請造訪[Azure 入口網站](https://portal.azure.com)、 選取您的 web 應用程式，然後選取**儀表板**。 在 [**快速連結**] 區段中，[ **FTP 診斷記錄**] 和 [ **FTP 診斷記錄**連結會提供使用 FTP 通訊協定記錄檔權限。

> [AZURE.NOTE] 如果您有不先前設定的使用者名稱和密碼的 FTP 或部署，您可以執行從 [**快速入門**管理] 頁面選取 [**設定部署認證**。

傳回儀表板中的 FTP URL 是**記錄檔**目錄，其中會包含下列的子目錄︰

* [部署方法](web-sites-deploy.md)-如果您是使用例如給部署方法，將會建立相同名稱的目錄，並包含部署的相關資訊。

* nodejs-Stdout 和 stderr 資訊擷取的應用程式的所有執行個體 （當 loggingEnabled，則為 true。）

###<a name="zip-archive"></a>Zip 封存

若要下載壓縮保存的診斷記錄，請使用下列命令，從 Azure 命令列工具︰

    azure site log download [sitename]

這會下載**diagnostics.zip**在目前的目錄。 此封存中包含下列目錄結構︰

* 部署-部署應用程式的相關資訊的記錄

* 記錄檔

    * [部署方法](web-sites-deploy.md)-如果您是使用例如給部署方法，將會建立相同名稱的目錄，並包含部署的相關資訊。

    * nodejs-Stdout 和 stderr 資訊擷取的應用程式的所有執行個體 （當 loggingEnabled，則為 true。）

###<a name="live-stream-tail"></a>即時資料流 （尾端）

若要檢視即時資料流診斷記錄資訊，請使用下列命令，從 Azure 命令列工具︰

    azure site log tail [sitename]

這會傳回伺服器上發生時都會更新的記錄事件的資料流。 此資料流當即會傳回部署資訊，以及 stdout 和 stderr 資訊 （loggingEnabled 是 true）。

<a id="nextsteps"></a>
## <a name="next-steps"></a>後續步驟

本文中，您學到如何啟用並存取 Azure 的診斷資訊的內容。 這項資訊用於瞭解發生問題的應用程式時，可能會指向的模組您使用的或 Node.js 應用程式服務 Web 應用程式所使用的版本是在您的部署環境中使用不同的問題。

在使用 [Azure 中的 [模組的資訊，請參閱[Azure 應用程式使用 Node.js 模組](../nodejs-use-node-modules-azure-apps.md)。

指定 Node.js 版本應用程式的詳細資訊，請參閱[指定 Node.js 版本 Azure 應用程式中]。

如需詳細資訊，請參閱[Node.js 開發人員中心](/develop/nodejs/)。

## <a name="whats-changed"></a>變更的項目
* 若要變更的指南，從網站應用程式服務請參閱︰ [Azure 應用程式服務與程式影響現有 Azure 服務](http://go.microsoft.com/fwlink/?LinkId=529714)

>[AZURE.NOTE] 如果您想要開始使用 Azure 應用程式服務註冊 Azure 帳戶之前，請移至[嘗試應用程式服務](http://go.microsoft.com/fwlink/?LinkId=523751)，可以讓您立即建立短暫入門 web 應用程式在應用程式服務。 必要; 沒有信用卡沒有承諾。

[IISNode]: https://github.com/tjanczuk/iisnode
[IISNode 讀我檔案]: https://github.com/tjanczuk/iisnode#readme
[How to Use The Azure Command-Line Interface]: ../xplat-cli-install.md
[Using Node.js Modules with Azure Applications]: ../nodejs-use-node-modules-azure-apps.md
[Azure 應用程式中指定 Node.js 版本]: ../nodejs-specify-node-version-azure-apps.md

[restart-button]: ./media/web-sites-nodejs-debug/restartbutton.png
 
