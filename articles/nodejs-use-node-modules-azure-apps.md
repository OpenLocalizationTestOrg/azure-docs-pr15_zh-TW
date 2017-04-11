<properties
    pageTitle="使用 Node.js 模組"
    description="瞭解如何使用 Azure 應用程式服務或雲端服務時，使用 Node.js 模組。"
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


# <a name="using-nodejs-modules-with-azure-applications"></a>Azure 應用程式中使用 Node.js 模組

這份文件提供指引 Node.js 模組使用裝載於 Azure 的應用程式。 還提供確保您的應用程式，使用特定版本的模組，以及使用 Azure 中的原生模組。

如果您已經熟悉使用 Node.js 模組， **package.json**和**npm shrinkwrap.json**檔案，下列是快速摘要的本文中所討論的內容︰

* Azure 應用程式服務瞭解**package.json**和**npm shrinkwrap.json**檔案，並可以安裝這些檔案中的項目所根據的模組。
* Azure 雲端服務預期安裝開發環境中，在所有模組和**節點\_模組**目錄，以部署套件的一部分。 若要啟用安裝模組使用**package.json**或**npm shrinkwrap.json**檔案雲端服務，不過這需要使用雲端服務專案的預設指令碼的自訂的支援可能是。 如需如何完成這項工作的範例，請參閱[Azure 啟動工作執行 npm 安裝]，以避免部署節點模組](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown)

> [AZURE.NOTE] Azure 虛擬機器不討論本文中，為 VM 的部署體驗會裝載的虛擬機器的作業系統而定。

##<a name="nodejs-modules"></a>Node.js 模組

模組是載入 JavaScript 套件可提供您的應用程式中的特定功能。 模組通常被安裝使用**npm**命令列工具，不過提供部分 （例如 http 模組） 做為核心 Node.js 套件的一部分。

當安裝模組時，它們會儲存在**節點\_模組**目錄在您的應用程式目錄結構的根目錄。 在每一個模組**節點\_模組**目錄維護自己**節點\_模組**所在的模組而定，並再次重複的每一個模組往相依性鏈結目錄。 這個選項可讓安裝擁有自己的版本需求模組而定，不過它可能會造成很大的目錄結構中的每一個模組。

部署時**節點\_模組**與您的應用程式組件的目錄，它會增加相較於使用**package.json**或**npm shrinkwrap.json**檔案; 部署的大小不過，它並保證生產環境中使用的模組的版本是開發中所使用的相同。

###<a name="native-modules"></a>原生模組

只是純文字 JavaScript 檔案大部分模組時，某些模組是特定的二進位圖像。 這些模組編譯在安裝時，通常會使用 Python 和節點 gyp。 由於 Azure 雲端服務依賴**節點\_模組**部署的一部分安裝模組包含的應用程式、 任何原生模組一部分的資料夾應該使用雲端服務中，只要將其安裝和編譯 Windows 開發系統上。

Azure 應用程式服務不支援所有的原生模組，而且可能會在編譯有特定的先決條件失敗。 雖然 MongoDB 等一些常用模組都有選用的原生相依性] 和 [不應該工作，兩種解決方法證明成功與執行幾乎所有的原生模組目前可用︰

* 在已安裝的所有原生模組的必要條件的 Windows 電腦上，執行**npm 安裝**。 然後，將部署建立**節點\_模組**Azure 應用程式服務應用程式組件的資料夾。
* Azure 應用程式服務可以設定來執行自訂艦隊或命令介面指令碼部署期間，可讓您執行的自訂命令，精確地設定**npm 安裝**的方式有機會正在執行。 顯示如何執行此動作，請參閱[自訂網站部署指令碼 Kudu 與]視訊。

###<a name="using-a-packagejson-file"></a>使用 package.json 檔案

**Package.json**檔案可指定您的應用程式的需求，使相依性，而不是需要您將可以安裝在裝載的平台的最上層相依性**節點\_封包**部署的一部分的資料夾。 部署應用程式後，將會用於**npm 安裝**命令剖析**package.json**檔案，並安裝所列的相依性。

在開發期間，您可以使用**-儲存**， **-儲存開發**，或**-儲存選擇性**安裝模組以自動新增至**package.json**檔案的模組的項目時的參數。 如需詳細資訊，請參閱[npm 安裝](https://docs.npmjs.com/cli/install)。

一個可能的問題**package.json**檔案是只會指定最上層相依性的版本。 安裝每一個模組可能，或可能未指定的模組而定，版本，因此，有可能是，您可能會使用不同的相依性鏈結個用於開發。

> [AZURE.NOTE]
> 如果<b>package.json</b>檔案參考原生模組部署至 Azure 應用程式服務，您會看到類似以下錯誤發佈給應用程式時︰

>       npm ERR! module-name@0.6.0 install: 'node-gyp configure build'

>       npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1


###<a name="using-a-npm-shrinkwrapjson-file"></a>使用 npm shrinkwrap.json 檔案

嘗試地址模組**package.json**檔案的版本設定限制為不**npm shrinkwrap.json**檔案。 雖然**package.json**檔案僅包含最上層模組的版本， **npm shrinkwrap.json**檔案中包含完整的模組相依性鏈結的版本需求。

準備好進行生產應用程式時，您可以鎖定的版本需求，然後使用**npm shrinkwrap**命令來建立**npm shrinkwrap.json**檔。 這會使用目前安裝的版本**節點\_模組**資料夾，並記錄這些**npm shrinkwrap.json**檔案。 主控環境部署應用程式後，將會用於**npm 安裝**命令剖析**npm shrinkwrap.json**檔案，並安裝所列的相依性。 如需詳細資訊，請參閱[npm shrinkwrap](https://docs.npmjs.com/cli/shrinkwrap)。

> [AZURE.NOTE]
>如果<b>npm shrinkwrap.json</b>檔案參考原生模組部署至 Azure 應用程式服務，您會看到類似以下錯誤發佈給應用程式時︰

>       npm ERR! module-name@0.6.0 install: 'node-gyp configure build'

>       npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1


##<a name="next-steps"></a>後續步驟

現在，您瞭解如何使用 Azure Node.js 模組，瞭解如何[指定 Node.js 版本]、[建立及部署 Node.js web 應用程式]，以及[如何使用 for Mac 和 Linux Azure 命令列介面]。

如需詳細資訊，請參閱[Node.js 開發人員中心](/develop/nodejs/)。

[指定 Node.js 版本]: nodejs-specify-node-version-azure-apps.md
[如何使用 Azure 命令列介面 for Mac 和 Linux]: xplat-cli-install.md
[建立並部署 Node.js web 應用程式]: web-sites-nodejs-develop-deploy-mac.md
[Node.js Web Application with Storage on MongoDB (MongoLab)]: store-mongolab-web-sites-nodejs-store-data-mongodb.md
[Build and deploy a Node.js application to an Azure Cloud Service]: cloud-services-nodejs-develop-deploy-app.md
[自訂網站與 Kudu 的部署指令碼]: /documentation/videos/custom-web-site-deployment-scripts-with-kudu/
