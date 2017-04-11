<properties 
    pageTitle="將您的 Node.js web 應用程式部署 5 分鐘中的 [Azure |Microsoft Azure" 
    description="瞭解如何輕鬆部署範例應用程式，在應用程式服務中執行此 web 應用程式。 啟動 [快速執行真正的開發，並立即查看結果。" 
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor=""
/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/13/2016" 
    ms.author="cephalin"
/>
    
# <a name="deploy-your-first-nodejs-web-app-to-azure-in-five-minutes"></a>將您的第一個 Node.js web 應用程式部署 5 分鐘中的 [Azure

本教學課程中，可協助您部署至[Azure 應用程式服務](../app-service/app-service-value-prop-what-is.md)的第一個 Node.js web 應用程式。
若要建立 web 應用程式、[行動應用程式返回結束](/documentation/learning-paths/appservice-mobileapps/)，及[API 應用程式](../app-service-api/app-service-api-apps-why-best-platform.md)，您可以使用應用程式服務。

您將會︰ 

- 建立 web 應用程式中 Azure 應用程式服務。
- 部署範例 Node.js 程式碼。
- 請參閱您執行的實際生產的程式碼。
- 更新您的 web 應用程式即[的發送給認可](https://git-scm.com/docs/git-push)以相同的方式。

## <a name="prerequisites"></a>必要條件

- [給](http://www.git-scm.com/downloads)。
- [Azure CLI](../xplat-cli-install.md)。
- Microsoft Azure 帳戶。 如果您沒有帳戶，可以[免費試用版註冊](/pricing/free-trial/?WT.mc_id=A261C142F)] 或 [[啟動您的 Visual Studio 訂閱權益](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。

>[AZURE.NOTE] 您可以[嘗試應用程式服務](http://go.microsoft.com/fwlink/?LinkId=523751)不 Azure 帳戶。 建立入門應用程式，並不必要的信用卡，沒有承諾，其播放達一小時。

## <a name="deploy-a-nodejs-web-app"></a>部署 Node.js web 應用程式

1. 開啟新的 Windows 命令提示字元、 PowerShell 視窗、 Linux 殼層或 OS X 終端機。 執行`git --version`和`azure --version`以驗證您的電腦上已安裝給和 Azure CLI。

    ![Azure 中測試安裝的第一個 web app CLI 工具](./media/app-service-web-get-started/1-test-tools.png)

    如果您還沒有安裝工具，請參閱下載連結[的先決條件](#Prerequisites)。

3. 登入 Azure 像這樣︰

        azure login

    請遵循說明郵件繼續登入程序。

    ![若要建立第一個 web 應用程式的 Azure 登入](./media/app-service-web-get-started/3-azure-login.png)

4. 變更 Azure CLI ASM 模式]，然後設定部署使用者應用程式服務。 您會部署稍後使用認證的程式碼。

        azure config mode asm
        azure site deployment user set --username <username> --pass <password>

1. 變更為使用目錄 (`CD`) 和複製範例應用程式，就像這樣︰

        git clone https://github.com/Azure-Samples/app-service-web-nodejs-get-started.git

2. 變更您的範例應用程式存放庫。

        cd app-service-web-nodejs-get-started

4. 建立應用程式服務應用程式資源的 Azure 中的唯一的應用程式名稱與您先前設定的部署使用者。 當您收到提示時，指定所要的區域數目。

        azure site create <app_name> --git --gitusername <username>

    ![Azure 中建立您的第一個 web 應用程式的 Azure 資源](./media/app-service-web-get-started-languages/node-site-create.png)

    您的應用程式中建立 Azure 現在。 此外，您目前的目錄做為給初始化，並且連線到新的應用程式服務應用程式就可以給遠端。
    您可以瀏覽至應用程式的 URL (http://&lt;app_name >。 azurewebsites.net) 查看美觀的預設 HTML 頁面，但現在就讓我們實際取得現在您的程式碼。

4. 等您想要發送給任何程式碼，請將範例程式碼部署至 Azure 應用程式中。 出現提示時，使用您先前設定的密碼。

        git push azure master

    ![推入 Azure 中第一個 web 應用程式的 [程式碼](./media/app-service-web-get-started-languages/node-git-push.png)

    `git push`程式碼會置於 Azure，不僅也會觸發部署引擎中的部署工作。 
    如果您在您的專案 （存放庫） 根 package.json，部署指令碼會還原為您所需的封裝。 

恭喜您，您的應用程式部署 Azure 應用程式服務。

## <a name="see-your-app-running-live"></a>請參閱執行即時應用程式

若要查看您執行即時 Azure 中的應用程式，請從任何您存放庫中的目錄中執行此命令︰

    azure site browse

## <a name="make-updates-to-your-app"></a>對您的應用程式的更新

您現在可以使用給若要從您的專案 （存放庫） 根隨時推 live 網站進行更新。 您執行的相同方式當您第一次部署您的程式碼。 例如，每當您想要從本機推播測試過的新變更，只要執行下列命令從您的專案 （存放庫） 根︰

    git add .
    git commit -m "<your_message>"
    git push azure master

## <a name="next-steps"></a>後續步驟

[建立、 設定和部署 Azure Node.js Express web 應用程式](app-service-web-nodejs-get-started.md)。 依照本教學課程中，您將瞭解基本技巧，您需要執行任何 Node.js web 應用程式中 Azure，例如︰

- 建立及設定 PowerShell/艦隊從 Azure 中的應用程式。
- 設定 Node.js 版本。
- 使用的不是根應用程式目錄開始檔案。
- 自動化與 NPM。
- 取得錯誤和輸出的記錄。

或執行更多第一個 web 應用程式。 例如︰

- 試試看[部署 Azure 程式碼的其他方法](../app-service-web/web-sites-deploy.md)。 例如，若要從您 GitHub 存放庫部署，只要選取**GitHub** ，而不是**本機給存放庫**中**部署選項**。
- 可讓您 Azure 應用程式到下一層級。 驗證您的使用者。 它會根據指定的小數位數。 如果需要設定的一些效能提醒。 只要按幾下滑鼠。 請參閱[新增至您的第一個 web 應用程式的功能](app-service-web-get-started-2.md)。

