<properties 
    pageTitle="使用 Socket.io Node.js 應用程式 |Microsoft Azure" 
    description="瞭解如何使用 socket.io 裝載於 Azure node.js 應用程式中。" 
    services="cloud-services" 
    documentationCenter="nodejs" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="build-a-nodejs-chat-application-with-socketio-on-an-azure-cloud-service"></a>Azure 雲端服務上建立與 Socket.IO Node.js 交談應用程式

Socket.IO 提供之間 node.js 伺服器和用戶端之間的即時通訊。 本教學課程中會引導您執行代管端。IO 依據聊天室 Azure 裝置上的應用程式。 如需有關 Socket.IO 的詳細資訊，請參閱<http://socket.io/>。

下方，為已完成的應用程式的螢幕擷取畫面︰

![在瀏覽器視窗，顯示裝載於 Azure 服務][completed-app]  

## <a name="prerequisites"></a>必要條件

請確定已安裝下列產品及版本順利完成這份文件中的範例︰

* 安裝[Visual Studio 2013](https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx)
* 安裝[Node.js](https://nodejs.org/download/)
* 安裝[Python 版本 2.7.10](https://www.python.org/)

## <a name="create-a-cloud-service-project"></a>建立雲端服務專案

下列步驟建立雲端服務專案會裝載 Socket.IO 應用程式。

1. 從**[開始] 功能表**或**[開始] 畫面**中，搜尋**Windows PowerShell**。 最後，以滑鼠右鍵按一下**Windows PowerShell** ，並選取 [**以系統管理員身分執行**。

    ![Azure PowerShell 圖示][powershell-menu]

2. 建立目錄，稱為**c:\\節點**。 
 
        PS C:\> md node

3. 變更目錄**c:\\節點**目錄
 
        PS C:\> cd node

4. 輸入下列命令以建立新的方案名稱為**chatapp**和命名**WorkerRole1**工作者角色︰

        PS C:\node> New-AzureServiceProject chatapp
        PS C:\Node> Add-AzureNodeWorkerRole

    您會看到下列回應︰

    ![新 azureservice 及新增 azurenodeworkerrolecmdlets 的輸出](./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png)

## <a name="download-the-chat-example"></a>下載聊天室範例

為此專案中，我們將使用[Socket.IO GitHub 存放庫]的聊天室範例。 執行下列步驟，以下載的範例，並將其新增至您先前建立的專案。

1.  使用 [**複製**] 按鈕，以建立存放庫的本機複本。 您也可以使用 [**郵遞區號**] 按鈕，下載的專案。

    ![在瀏覽器視窗檢視 https://github.com/LearnBoost/socket.io/tree/master/examples/chat，醒目提示 ZIP 下載圖示][chat-example-view]

3.  瀏覽目錄結構的本機存放庫，直到您到達**範例\\聊天室**目錄。 複製此目錄內容**c:\\節點\\chatapp\\WorkerRole1**先前建立的目錄。

    ![總管] 中，顯示內容的範例\\從封存解壓縮的聊天室目錄][chat-contents]

    醒目提示的項目，在螢幕擷取畫面上方會從複製的檔案**範例\\聊天室**目錄

4.  在 [ **c:\\節點\\chatapp\\WorkerRole1**目錄刪除**server.js**的檔案，然後**app.js**檔案重新命名**server.js**。 這會移除先前**新增 AzureNodeWorkerRole**指令程式所建立的預設**server.js**檔案，並加以取代應用程式檔案從聊天室的範例。

### <a name="modify-serverjs-and-install-modules"></a>修改 Server.js 並安裝模組

前 Azure 模擬器中測試應用程式，我們必須進行一些稍微修改。 執行下列步驟，以 server.js 檔案︰

1.  在 Visual Studio 或任何文字編輯器中開啟**server.js**檔案。

2.  尋找 server.js 開頭的**模組相依性**區段及變更包含**sio 行 = require('..//..lib//socket.io 」)**至**sio = require('socket.io')**如下所示︰

        var express = require('express')
        , stylus = require('stylus')
        , nib = require('nib')
        //, sio = require('..//..//lib//socket.io'); //Original
        , sio = require('socket.io');                //Updated

3.  若要確保正確的連接埠接聽應用程式，在記事本或您偏好使用的編輯器] 中開啟 server.js，變更的下列行**process.env.port**以取代**3000** ，如下所示︰

        //app.listen(3000, function () {            //Original
        app.listen(process.env.port, function () {  //Updated
          var addr = app.address();
          console.log('   app listening on http://' + addr.address + ':' + addr.port);
        });

儲存之後所做的變更來**server.js**，安裝必要的模組，請使用下列步驟，然後再測試 Azure 模擬器中的 [應用程式︰

1.  使用**PowerShell 的 Azure**，變更目錄**c:\\節點\\chatapp\\WorkerRole1**目錄與使用下列命令來安裝這個應用程式所需的模組︰

        PS C:\node\chatapp\WorkerRole1> npm install

    這將會安裝 package.json 檔案中所列的模組。 命令完成之後，您應該會看到類似以下的輸出︰

    ![輸出 npm 的安裝] 命令][The-output-of-the-npm-install-command]

4.  此範例中的原始 Socket.IO GitHub 存放庫的一部分，並直接參照的相對路徑的 [Socket.IO 文件庫，Socket.IO 未參考 package.json 檔案中，以便我們必須安裝發行下列命令︰

        PS C:\node\chatapp\WorkerRole1> npm install socket.io --save

### <a name="test-and-deploy"></a>測試並部署

1.  啟動模擬器發出下列命令︰

        PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch

2.  開啟瀏覽器，然後瀏覽至**http://127.0.0.1**。

3.  當瀏覽器視窗隨即開啟時，請輸入暱稱，然後按輸入。
    這會將所有您要為特定的暱稱張貼訊息。 若要測試多位使用者的功能，開啟其他的瀏覽器視窗使用相同的 URL，然後輸入不同的暱稱。

    ![顯示從 User1 和 User2 聊天室訊息的兩個瀏覽器視窗](./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png)

3.  測試應用程式後停止模擬器發出下列命令︰

        PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator

4.  若要部署 Azure 應用程式，使用 [**發佈 AzureServiceProject**指令程式。 例如︰

        PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch

    > [AZURE.IMPORTANT] 請務必使用唯一的名稱，否則發佈程序會失敗。 部署完成後，在瀏覽器會開啟，並瀏覽至部署的服務。
    > 
    > 如果您收到錯誤訊息指出提供的訂閱名稱不存在匯入的發佈設定檔中，您必須下載並匯入您的訂閱部署至 Azure 之前，先發佈的設定檔。 請參閱[建立並部署至 Azure 雲端服務的 Node.js 應用程式](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)的 [**部署至 Azure 應用程式**] 區段

    ![在瀏覽器視窗，顯示裝載於 Azure 服務][completed-app]

    > [AZURE.NOTE] 如果您收到錯誤訊息指出提供的訂閱名稱不存在匯入的發佈設定檔中，您必須下載並匯入您的訂閱部署至 Azure 之前發佈的設定檔。 請參閱[建立並部署至 Azure 雲端服務的 Node.js 應用程式](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)的 [**部署至 Azure 應用程式**] 區段

應用程式現在 Azure 上執行，並可以轉接使用 Socket.IO 不同的用戶端之間的訊息。

> [AZURE.NOTE] 為求，此範例會限制交談之間連線到相同的執行個體的使用者。 這表示，如果雲端服務上建立兩個工作者角色執行個體，使用者只能與其他人連線到相同的背景工作角色執行個體交談。 若要縮放的應用程式使用多個角色執行個體，您可以使用 [技術服務匯流排像共用 Socket.IO 市集狀態所有執行個體。 如需範例，請參閱[Azure SDK Node.js GitHub 存放庫](https://github.com/WindowsAzure/azure-sdk-for-node)中的服務匯流排佇列 」 和 「 主題使用狀況範例。

##<a name="next-steps"></a>後續步驟

在此教學課程中，您學到如何建立基本的交談應用程式裝載於 Azure 雲端服務中的內容。 若要瞭解如何裝載 Azure 網站中的此應用程式，請參閱[建立 Node.js 交談應用程式與 Socket.IO Azure 的網站上][chatwebsite]。

如需詳細資訊，請參閱[Node.js 開發人員中心](/develop/nodejs/)。

  [chatwebsite]: /develop/nodejs/tutorials/website-using-socketio/

  [Azure SLA]: http://www.windowsazure.com/support/sla/
  [Azure SDK for Node.js GitHub repository]: https://github.com/WindowsAzure/azure-sdk-for-node
  [completed-app]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
  [Azure SDK for Node.js]: https://www.windowsazure.com/develop/nodejs/
  [Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
  [Socket.IO GitHub 存放庫]: https://github.com/LearnBoost/socket.io/tree/0.9.14
  [Azure Considerations]: #windowsazureconsiderations
  [Hosting the Chat Example in a Worker Role]: #hostingthechatexampleinawebrole
  [Summary and Next Steps]: #summary
  [powershell-menu]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png

  [chat example]: https://github.com/LearnBoost/socket.io/tree/master/examples/chat
  [chat-example-view]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png
  
  
  [chat-contents]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png
  [The-output-of-the-npm-install-command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
  [The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-9.png
  
 
