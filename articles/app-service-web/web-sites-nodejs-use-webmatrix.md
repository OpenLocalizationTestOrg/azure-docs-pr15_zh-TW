<properties 
    pageTitle="建立並部署 Node.js web 應用程式來使用 WebMatrix Azure" 
    description="此教學課程會指導您如何使用 WebMatrix 開發 Node.js 應用程式，並將其部署到 Azure 應用程式服務 Web 應用程式。" 
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


# <a name="build-and-deploy-a-nodejs-web-app-to-azure-using-webmatrix"></a>建立並部署 Node.js web 應用程式來使用 WebMatrix Azure

本教學課程教您如何使用 WebMatrix 開發 Node.js 應用程式，並將其部署至[Azure 應用程式服務](http://go.microsoft.com/fwlink/?LinkId=529714)Web 應用程式。 WebMatrix 是免費的 web 開發工具 microsoft 包含網站或 web 應用程式開發，您需要的所有項目。 WebMatrix 包含數個功能，讓您輕鬆地使用 Node.js 較少，包括程式碼完成、 預先建立的範本和編輯器碧眼，支援 CoffeeScript。 進一步瞭解[WebMatrix](https://www.microsoft.com/web/webmatrix/)。

完成本指南，您必須在 Azure 應用程式服務中執行的 Node.js web 應用程式。
 
下方，為已完成的應用程式的螢幕擷取畫面︰

![Azure 節點的網站][webmatrix-node-completed]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE] 如果您想要開始使用 Azure 應用程式服務註冊 Azure 帳戶之前，請移至[嘗試應用程式服務](http://go.microsoft.com/fwlink/?LinkId=523751)，可以讓您立即建立短暫入門 web 應用程式在應用程式服務。 必要; 沒有信用卡沒有承諾。

## <a name="sign-into-azure"></a>登入 Azure

請遵循這些步驟來建立 web 應用程式中 Azure 應用程式服務。

1. 啟動 WebMatrix
2. 如果這是第一次您已使用 WebMatrix，系統會提示您登入 Azure。  否則，您可以按一下 [**登入**] 按鈕，並選擇 [**新增帳戶**。  選取 [**登入**使用您的 Microsoft 帳戶。

    ![新增帳戶][addaccount]

3. 如果您已註冊 Azure 帳戶，您可能會使用登入您的 Microsoft 帳戶︰

    ![登入 Azure][signin]  


## <a name="create-a-site-using-a-built-in-template-for-azure"></a>建立網站的 Azure 使用內建的範本

1. 在 [開始] 畫面上按一下 [**新增**] 按鈕，然後選擇**範本藝廊**若要從 [範本] 庫中建立新的網站︰

    ![新的網站範本藝廊][sitefromtemplate]

2. 在 [**網站範本**] 對話方塊中選取**節點**，然後選取**Express 網站**。 最後，按一下 [**下一步**]。 如果您沒有任何**Express 網站**範本的必要條件，系統會提示您安裝。

    ![選取 [快速範本][webmatrix-templates]

3. 如果您登入 Azure，現在必須建立您的本機網站的應用程式服務 web 應用程式的選項。  選擇 [唯一的名稱，然後選取您希望您的應用程式服務 web 應用程式建立的資料中心︰ 

    ![Azure 上建立網站][nodesitefromtemplateazure]
    
4. WebMatrix 完成建立本機的網站，並建立應用程式服務 web 應用程式之後，會顯示 WebMatrix IDE。

    ![webmatrix ide][webmatrix-ide]

##<a name="publish-your-application-to-azure"></a>發佈至 Azure 應用程式

1. WebMatrix，在 [從**家用版**功能區顯示網站的 [**發佈預覽**] 對話方塊的 [**發佈**]。

    ![發佈預覽][webmatrix-node-publishpreview]

2. 按一下 [**繼續**]。 發佈完成後，應用程式服務 web 應用程式的 URL 會顯示在 WebMatrix IDE 底部

    ![發佈完成][webmatrix-publish-complete]

3. 按一下 [在瀏覽器中開啟應用程式服務 web 應用程式] 連結。

    ![Express web 應用程式][webmatrix-node-express-site]

##<a name="modify-and-republish-your-application"></a>修改並重新發佈應用程式

您可以輕鬆修改，並重新發佈應用程式。 在這裡，您就能變更為中的 [標題在**index.jade**的檔案，並重新發佈應用程式的簡單。

1. 在 WebMatrix，選取**檔案**，然後再展開 [**檢視**] 資料夾。 按兩下以開啟**index.jade**檔案。

    ![webmatrix 檢視 index.jade][webmatrix-modify-index]

2. 變更段落行如下︰

        p Welcome to #{title} with WebMatrix on Azure!

3. 儲存您的變更，然後按一下 [發佈] 圖示。 最後，按一下 [**繼續**]，請在 [**發佈預覽**] 對話方塊中，並要發佈的更新，請等候。

    ![發佈預覽][webmatrix-republish]

4. 發佈完成時，請使用傳回發佈程序完成若要查看更新後的應用程式服務 web 應用程式後的連結。

    ![Azure 節點 web 應用程式][webmatrix-node-completed]

##<a name="next-steps"></a>後續步驟

若要進一步瞭解 Azure，以及如何指定要搭配您的應用程式版本會提供 Node.js 版本，請參閱[指定 Node.js 版本 Azure 應用程式中](../nodejs-specify-node-version-azure-apps.md)。

如果部署 Azure 之後，您就會發生問題，您的應用程式，請參閱[如何偵錯 Node.js web app 中 Azure 應用程式服務](web-sites-nodejs-debug.md)診斷問題的詳細資訊。

## <a name="whats-changed"></a>變更的項目
* 若要變更的指南，從網站應用程式服務請參閱︰ [Azure 應用程式服務與程式影響現有 Azure 服務](http://go.microsoft.com/fwlink/?LinkId=529714)

[WebMatrix WebSite]: http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398
[WebMatrix for Azure]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409

[webmatrix-node-completed]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-complete.png
[webmatrix-templates]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-templates.png

[webmatrix-node-publishpreview]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publishpreview.png

[webmatrix-ide]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-ide.png
[webmatrix-publish-complete]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publish-complete.png
[webmatrix-node-express-site]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-express-webiste.png
[webmatrix-modify-index]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-edit.png
[webmatrix-republish]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-republish.png
[addaccount]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-add-account.png
[signin]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-sign-in.png
[sitefromtemplate]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-site-from-template.png
[nodesitefromtemplateazure]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-site-azure.png
 