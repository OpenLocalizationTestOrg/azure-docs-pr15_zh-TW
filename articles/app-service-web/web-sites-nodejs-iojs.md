<properties 
    pageTitle="如何使用 io.js Azure 應用程式服務 Web 應用程式" 
    description="瞭解如何使用 io.js Azure 應用程式服務的 web 應用程式。" 
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
    ms.author="robmcm" />

# <a name="how-to-use-iojs-with-azure-app-service-web-apps"></a>如何使用 io.js Azure 應用程式服務 Web 應用程式

常用的節點分叉[io.js]功能 Joyent 的 Node.js 專案，包括其他開啟的管理模型、 更快速的發行循環圖和新功能和實驗 JavaScript 功能更快速地採用的各種差異。

雖然[Azure 應用程式服務](http://go.microsoft.com/fwlink/?LinkId=529714)Web 應用程式有許多預先安裝的 Node.js 版本，您也可以為使用者提供 Node.js 二進位。 本文將告訴您啟用 io.js 應用程式服務 Web 應用程式的兩種方法︰ 使用延伸的部署指令碼，它會自動設定使用最新的可用 io.js 版本，以及手動上傳的二進位 io.js Azure。 

<a id="deploymentscript"></a>
## <a name="using-a-deployment-script"></a>使用部署指令碼

Node.js 應用程式的部署，在應用程式服務 Web 應用程式執行小型的命令，以確保環境經正確的數字。 使用部署指令碼，可以包含的下載和設定 io.js 自訂此程序。

使用上 GitHub [io.js 部署指令碼](https://github.com/felixrieseberg/iojs-azure)。 若要啟用 io.js 在 web 應用程式，只要將**.deployment**、 **deploy.cmd**和**IISNode.yml**複製到您的應用程式] 資料夾的根目錄，部署至 Web 應用程式。  

第一個檔案，也就是**.deployment**，指示執行**deploy.cmd**部署時的 Web 應用程式。 這個指令碼執行 Node.js 應用程式，所有的主要步驟，但也會下載最新版的 io.js。 最後， **IISNode.yml**設定 Web 應用程式，而不是預先安裝的 Node.js 二進位使用只要下載的 io.js 二進位。

> [AZURE.NOTE] 若要更新使用的 io.js 二進位，只要重新部署您的應用程式中的指令碼會下載 io.js 的新版本應用程式的部署每一次。

<a id="manualinstallation"></a>
## <a name="using-manual-installation"></a>使用手動安裝

手動安裝自訂 io.js 版本包含只有兩個步驟。 首先，請下載**win x64**二進位直接從[io.js 通訊群組]。 所需的兩個檔案- **iojs.exe**和**iojs.lib**。 將兩個檔案儲存至 web 應用程式，例如在**bin/iojs**內的資料夾中。

若要設定 Web 應用程式]，使用**iojs.exe** ，而非預先安裝的節點版本，請在您的應用程式的根目錄建立**IISNode.yml**檔案並新增的下列行。

    nodeProcessCommandLine: "D:\home\site\wwwroot\bin\iojs\iojs.exe"

<a id="nextsteps"></a>
## <a name="next-steps"></a>後續步驟

您已經學會如何使用的本文中 io.js 與應用程式服務 Web 應用程式，兩者都使用會提供部署指令碼以及為手動安裝。 

> [AZURE.NOTE] io.js 中經常開發而超過 Node.js 更新。 Node.js 模組的數字可能不適用於 io.js-請參閱[io.js GitHub 上的]進行疑難排解。

## <a name="whats-changed"></a>變更的項目
* 若要變更的指南，從網站應用程式服務請參閱︰ [Azure 應用程式服務與程式影響現有 Azure 服務](http://go.microsoft.com/fwlink/?LinkId=529714)

>[AZURE.NOTE] 如果您想要開始使用 Azure 應用程式服務註冊 Azure 帳戶之前，請移至[嘗試應用程式服務](http://go.microsoft.com/fwlink/?LinkId=523751)，可以讓您立即建立短暫入門 web 應用程式在應用程式服務。 必要; 沒有信用卡沒有承諾。

[io.js]: https://iojs.org
[io.js 通訊群組]: https://iojs.org/dist/
[在 GitHub io.js]: https://github.com/iojs/io.js
[io.js Deployment Script]: https://github.com/felixrieseberg/iojs-azure
 