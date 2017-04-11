<properties
    pageTitle="Node.js 快速入門指南 |Microsoft Azure"
    description="瞭解如何建立簡單的 Node.js web 應用程式，並將其部署至 Azure 雲端服務。"
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
    ms.topic="hero-article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="build-and-deploy-a-nodejs-application-to-an-azure-cloud-service"></a>建立並部署至 Azure 雲端服務的 Node.js 應用程式

> [AZURE.SELECTOR]
- [Node.js](cloud-services-nodejs-develop-deploy-app.md)
- [.NET](cloud-services-dotnet-get-started.md)

本教學課程中會顯示如何建立簡單的 Node.js 應用程式，在 Azure 雲端服務中執行。 雲端服務是 Azure 中可調整雲端應用程式的建置組塊。 它們允許分隔獨立管理和向外的應用程式的前端與後端元件。  雲端服務會提供強大的專用的虛擬機器可靠裝載每一個角色。

雲端服務的詳細資訊，及他們比較至 Azure 網站與虛擬機器的方式，請參閱[Azure 網站、 雲端服務與虛擬機器比較]。

>[AZURE.TIP] 尋找建立簡單的網站嗎？ 如果您的狀況包含只要簡單網站前端，請考慮[使用精簡的 web 應用程式]。 您可以輕鬆地升級雲端服務 web 應用程式的規模擴大時，並變更您的需求。

依照本教學課程中，您會建立簡單的 web 應用程式裝載於網頁角色。 您會使用計算模擬器測試您的應用程式中，然後將其使用 PowerShell 命令列工具部署。

應用程式是一個簡單的"hello 全球 」 應用程式︰

![顯示 Hello World 網頁上的網頁瀏覽器][A web browser displaying the Hello World web page]

## <a name="prerequisites"></a>必要條件

> [AZURE.NOTE] 本教學課程中會使用需要 Windows Azure PowerShell。

- 安裝和設定[Powershell 的 Azure]。
- 下載並安裝[.NET 2.7 Azure SDK]。 在安裝設定] 中，選取︰
    - MicrosoftAzureAuthoringTools
    - MicrosoftAzureComputeEmulator


## <a name="create-an-azure-cloud-service-project"></a>建立 Azure 雲端服務專案

執行下列任務，以建立新的 Azure 雲端服務專案，以及基本 Node.js 臨時平台︰

1. 執行**Windows PowerShell**以系統管理員身分;從**[開始] 功能表**或**[開始] 畫面**中，搜尋**Windows PowerShell**。

2. [連線 PowerShell]至您的訂閱。

3. 輸入下列 PowerShell cmdlet 來建立建立專案︰

        New-AzureServiceProject helloworld

    ![新增 AzureService helloworld 指令的結果][The result of the New-AzureService helloworld command]

    **新增 AzureServiceProject**指令程式會產生發佈到雲端服務的 Node.js 應用程式的基本結構。 它包含發佈至 Azure 所需的設定檔。 Cmdlet 也會變更您的工作目錄服務的目錄。

    Cmdlet 會建立下列檔案︰

    -   **ServiceConfiguration.Cloud.cscfg**、 **ServiceConfiguration.Local.cscfg**和**ServiceDefinition.csdef**: Azure 特定檔案所需的發佈您的應用程式。 如需詳細資訊，請參閱[建立裝載於 Azure 服務概觀]。

    -   **deploymentSettings.json**︰ 儲存本機的 PowerShell 的 Azure 部署指令程式所使用的設定。

4.  輸入下列命令以加入新的 web 角色︰

        Add-AzureNodeWebRole

    ![新增 AzureNodeWebRole 命令的輸出][The output of the Add-AzureNodeWebRole command]

    **新增 AzureNodeWebRole**指令程式會建立基本的 Node.js 應用程式。 修改的**.csfg**和**.csdef**檔案新增新角色的設定項目。

    > [AZURE.NOTE] 如果您未指定的角色名稱，則會使用預設的名稱。 您可以提供的名稱的第一個 cmdlet 參數︰`Add-AzureNodeWebRole MyRole`

在檔案**server.js**，位於 web 角色 (依預設**WebRole1** ) 的目錄中定義 Node.js 應用程式。 以下是程式碼︰

    var http = require('http');
    var port = process.env.port || 1337;
    http.createServer(function (req, res) {
        res.writeHead(200, { 'Content-Type': 'text/plain' });
        res.end('Hello World\n');
    }).listen(port);

將此程式碼基本上相同"Hello World 」 範例[nodejs.org]在網站上，但它會使用雲端環境所指定的連接埠號碼。

## <a name="deploy-the-application-to-azure"></a>部署至 Azure 應用程式

    [AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

### <a name="download-the-azure-publishing-settings"></a>下載 Azure 發佈設定

若要部署 Azure 應用程式，您必須先下載發佈設定 Azure 訂閱。

1.  執行下列 PowerShell 的 Azure cmdlet:

        Get-AzurePublishSettingsFile

    這會使用您的瀏覽器來瀏覽至發佈設定下載頁面。 您可能會提示您登入 Microsoft 帳戶。 如果是這樣，請使用與您 Azure 訂閱相關聯的帳戶。

    將已下載的設定檔儲存到您可以輕鬆存取的檔案位置。

2.  執行下列 cmdlet 來匯入下載的發佈設定檔︰

        Import-AzurePublishSettingsFile [path to file]


    > [AZURE.NOTE] 匯入之後發佈設定，請考慮刪除下載的.publishSettings 檔案，因為其中含有可能會讓其他人存取您帳戶的資訊。

### <a name="publish-the-application"></a>發佈應用程式

若要發佈，請執行下列命令︰

    $ServiceName = "NodeHelloWorld" + $(Get-Date -Format ('ddhhmm'))   
    Publish-AzureServiceProject -ServiceName $ServiceName  -Location "East US" -Launch

- **-ServiceName**指定部署的名稱。 這必須是唯一的名稱，否則發佈程序會失敗。 [**開始日期**] 命令 tacks 上的日期/時間 」 字串，應該都讓唯一的名稱。

- **-位置**指定應用程式會裝載於資料中心。 若要查看可用的資料中心的清單，請使用**取得 AzureLocation**指令程式。

- **-啟動**開啟瀏覽器視窗，並部署完成後，瀏覽到裝載的服務。

發佈成功之後，您會看到類似以下的回應︰

![發佈 AzureService 命令的輸出][The output of the Publish-AzureService command]

> [AZURE.NOTE]
> 它可能需要幾分鐘的時間要部署及成為第一次發佈時可使用的應用程式。

完成部署後，在瀏覽器視窗就會開啟，然後瀏覽至雲端服務。

![在瀏覽器視窗，顯示認識全球頁面。URL 指出頁面裝載在 Azure。][A browser window displaying the hello world page; the URL indicates the page is hosted on Azure.]

Azure 立即執行您的應用程式。

**發佈 AzureServiceProject**指令程式會執行下列步驟︰

1.  建立部署套件。 套件包含您的應用程式] 資料夾中的所有檔案。

2.  如果沒有，會建立新的**儲存空間的帳戶**。 Azure 儲存體帳戶用來儲存部署期間的應用程式套件。 安全地部署完成之後，您可以刪除儲存的帳戶。

3.  如果不存在，會建立新的**雲端服務**。 **雲端服務**是部署至 Azure 時，會裝載您的應用程式的容器。 如需詳細資訊，請參閱[建立裝載於 Azure 服務概觀]。

4.  將部署套件發佈至 Azure。


## <a name="stopping-and-deleting-your-application"></a>停止，以及刪除您的應用程式

在部署您的應用程式之後，您可能要停用，因此可避免額外的成本。 Azure 帳單 web 每小時的伺服器時間耗用的角色執行個體。 伺服器被使用部署您的應用程式，即使未在執行執行個體，而且會停止的狀態。

1.  在 Windows PowerShell 視窗中，停止服務部署建立前一節使用下列 cmdlet:

        Stop-AzureService

    停止服務，可能需要幾分鐘的時間。 停止服務時，您會收到訊息指出它已停止。

    ![停止 AzureService 命令的狀態][The status of the Stop-AzureService command]

2.  若要刪除的服務，請連絡下列 cmdlet:

        Remove-AzureService

    出現提示時，請輸入**Y**刪除服務。

    刪除服務，可能需要幾分鐘的時間。 刪除服務之後您收到訊息，指出服務已遭刪除。

    ![移除 AzureService 命令的狀態][The status of the Remove-AzureService command]

    > [AZURE.NOTE] 刪除服務並不會刪除最初發行服務時, 所建立的儲存空間帳戶，您還是會繼續要付費使用的儲存空間。 如果還不使用儲存空間，您可能會想要將其刪除。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱[Node.js 開發人員中心]。

<!-- URL List -->

[Azure 網站、 Cloud Services 及虛擬機器比較]: ../app-service-web/choose-web-site-cloud-service-vm.md
[使用精簡的 web 應用程式]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
[Azure Powershell]: ../powershell-install-configure.md
[Azure SDK.NET 2.7]: http://www.microsoft.com/en-us/download/details.aspx?id=48178
[連線 PowerShell]: ../powershell-install-configure.md#how-to-connect-to-your-subscription
[nodejs.org]: http://nodejs.org/
[建立裝載的服務 Azure 的概觀]: https://azure.microsoft.com/documentation/services/cloud-services/
[Node.js 開發人員中心]: https://azure.microsoft.com/develop/nodejs/

<!-- IMG List -->

[The result of the New-AzureService helloworld command]: ./media/cloud-services-nodejs-develop-deploy-app/node9.png
[The output of the Add-AzureNodeWebRole command]: ./media/cloud-services-nodejs-develop-deploy-app/node11.png
[A web browser displaying the Hello World web page]: ./media/cloud-services-nodejs-develop-deploy-app/node14.png
[The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node19.png
[A browser window displaying the hello world page; the URL indicates the page is hosted on Azure.]: ./media/cloud-services-nodejs-develop-deploy-app/node21.png
[The status of the Stop-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node48.png
[The status of the Remove-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node49.png
