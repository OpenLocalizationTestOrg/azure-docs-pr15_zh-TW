<properties
    pageTitle="Azure 應用程式服務-Node.js 升級行動服務"
    description="瞭解如何輕鬆升級您的行動電話服務應用程式至應用程式服務行動應用程式"
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="yochayk"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile"
    ms.devlang="node"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="upgrade-your-existing-nodejs-azure-mobile-service-to-app-service"></a>升級您現有的 Node.js Azure 行動訊息服務應用程式服務

行動應用程式服務是建立使用 Microsoft Azure 的行動應用程式的新方式。 若要深入瞭解，請參閱[哪些行動應用程式？]。

本主題說明如何從 Azure 行動服務升級現有的 Node.js 後端應用程式，以新的應用程式服務行動應用程式。 當您執行此升級時，可以繼續現有的行動電話服務應用程式運作。  如果您需要升級 Node.js 後端應用程式，請參閱[升級.NET 行動服務](./app-service-mobile-net-upgrading-from-mobile-services.md)。

當行動的後端升級至 Azure 應用程式服務時，它會有權存取所有的應用程式服務功能，並根據[應用程式服務價格]、 不有行動電話服務的價格向收費。

## <a name="migrate-vs-upgrade"></a>升級和移轉

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

>[AZURE.TIP] 建議您[執行移轉](app-service-mobile-migrating-from-mobile-services.md)之前進行升級。 如此一來，您可以將應用程式的兩個版本放在相同的應用程式服務方案，並會造成任何其他成本。

### <a name="improvements-in-mobile-apps-nodejs-server-sdk"></a>在行動應用程式 Node.js server SDK 的改良功能

升級至新的[行動應用程式 SDK](https://www.npmjs.com/package/azure-mobile-apps)提供許多改良功能，包括︰

- 根據[Express 架構](http://expressjs.com/en/index.html)，新的節點 SDK 精簡，而地與節點的新版本]，以設計。 您可以自訂應用程式的行為與 Express 介軟體。

- 比較行動服務 SDK 嚴重的效能提升。

- 您現在可以搭配您的行動後端; 使用主控網站同樣地，您可以輕鬆將 Azure 行動 SDK 新增至現有的 express.v4 應用程式。

- 內建的跨平台與本機開發，行動應用程式 SDK 先開發與本機 Windows、 Linux 和 OSX 平台上執行。 現在易於使用一般節點開發方法，像是執行之前部署[Mocha](https://mochajs.org/)測試。

## <a name="overview"></a>基本升級的概觀

若要協助升級 Node.js 後端，Azure 應用程式服務已提供相容性套件。  升級之後，您必須 niew 網站，可以部署至新的應用程式服務網站。

行動電話服務用戶端 Sdk 都**不**相容的新的行動應用程式伺服器 SDK。 若要提供的服務應用程式的連續性，發佈變更至目前服務已發佈的用戶端的網站。 不過，您應該建立新的行動應用程式，做為重複。 若要避免造成其他財務成本相同的應用程式服務方案，您可以將此應用程式。

然後，您會有兩個版本的應用程式︰ 的保持相同，並做造成，以及，另一個您可以升級和新的用戶端版本與目標發佈應用程式。 您可以移動及測試您的程式碼，您的速度，但請確定您進行任何錯誤修正取得套用至兩者。 一旦您認為肆虐的應用程式更新為最新版本的用戶端所要的數字，您可以刪除原始的移轉應用程式是否您想要。 它不會造成任何貨幣成本，如果裝載在您的行動應用程式相同的應用程式服務方案。

完整的外框的升級程序如下所示︰

1. 下載您現有的 （移轉） Azure 行動訊息服務。
2. 將專案轉換至 Azure 行動應用程式使用相容性套件。
3. 修正差異 （例如驗證設定）。
4. 在新的應用程式服務部署轉換的 Azure Mobile 應用程式專案。
4. 請在放開用戶端應用程式使用新的行動應用程式的新的版本。
5. （選用）刪除原始移轉行動訊息服務應用程式。

當您原始的移轉行動訊息服務上沒有看到任何流量時，就會發生刪除。

## <a name="install-npm-package"></a>安裝的必要條件

您應該在您的本機電腦上安裝 [節點]。  您也應該安裝相容性套件。  節點安裝之後，您可以執行下列命令，從新 cmd 或 PowerShell 提示中︰

```npm i -g azure-mobile-apps-compatibility```

## <a name="obtain-ams-scripts"></a>取得您的行動電話的 Azure 服務指令碼

- [Azure 入口網站]登入。
- 使用的**所有資源**或**應用程式服務**，尋找您的行動電話服務網站。
- 在網站上，按一下 [**工具]** -> **Kudu** -> **移**至開啟 Kudu 網站。
- 按一下 [**偵錯主控台** -> **PowerShell**以開啟偵錯主控台。
- 瀏覽至`site/wwwroot/App_Data/config`依序按一下每個目錄
- 按一下 [下載] 圖示旁`scripts`目錄。

這會下載 ZIP 格式中的指令碼。  在您的本機電腦上建立新的目錄，並解壓縮`scripts.ZIP`目錄內的檔案。  這會建立`scripts`目錄。

## <a name="scaffold-app"></a>Scaffold 新 Azure Mobile 應用程式後端

從目錄包含的指令碼目錄，請執行下列命令︰

```scaffold-mobile-app scripts out```

這會建立在 scaffolded 的 Azure Mobile 應用程式後端`out`目錄。  雖然非必要，但最好檢查`out`目錄到您所選擇的來源程式碼存放庫。

## <a name="deploy-ama-app"></a>部署您 Azure Mobile 應用程式後端

部署期間，您將需要執行下列動作︰

1. [Azure 入口網站]中建立新的行動應用程式。
2. 執行`createViews.sql`連線資料庫上的指令碼。
3. 連結會連結至您的行動訊息服務，您新增的應用程式服務的資料庫。
4. 連結至新的應用程式服務的任何其他資源 （例如通知集線器）。
5. 將產生的程式碼部署新的網站。

### <a name="create-a-new-mobile-app"></a>建立新的行動應用程式

1. 在[Azure 入口網站]登入。

2. 按一下 [ **+ 新增** > **Web + 行動** > **Mobile 應用程式**，然後提供您的行動應用程式後端的名稱。

3. **資源] 群組**中，選取現有的資源群組，或建立新的範本 （使用相同的名稱為您的應用程式）。 
 
    您可以選取另一個應用程式服務方案，或建立新的範本。 如需更多應用程式服務的相關計劃，以及如何建立新的方案在不同的價格層和您想要的位置，請參閱[Azure 應用程式服務方案在您採取進階的概觀](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)。

4. 用於**應用程式服務計劃**中，已選取預設計劃 （在[標準層](https://azure.microsoft.com/pricing/details/app-service/)）。 您也可以選取不同的方案，或[建立新的範本](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md#create-an-app-service-plan)。 應用程式服務方案的設定會決定[成本，並計算資源的位置，功能，](https://azure.microsoft.com/pricing/details/app-service/)您的應用程式。 

    決定方案之後，請按一下 [**建立**]。 這樣會建立 Mobile 應用程式後端。 


### <a name="run-createviewssql"></a>執行 CreateViews.SQL

Scaffolded 應用程式含有檔名`createViews.sql`。  針對目標資料庫，必須執行這個指令碼。  從**連接**字串**設定**刀移轉行動服務，您可以取得目標資料庫的連線字串。  名稱為`MS_TableConnectionString`。

您可以執行這個從 SQL Server Management Studio 或 Visual Studio 中的指令碼。

### <a name="link-the-database-to-your-app-service"></a>連結至您的應用程式服務的資料庫

連結至您的應用程式服務的現有的資料庫︰

- 在[Azure 入口網站]中，開啟您的應用程式服務。
- 選取 [**所有設定** -> **資料連線**。
- 按一下 [ **+ 新增**]。
- 在下拉式清單中，選取 [ **SQL 資料庫**
- **SQL 資料庫**] 下選取現有的資料庫，然後按一下 [上**選取**。
- 在 [**連線字串**下, 資料庫中，輸入使用者名稱和密碼，然後按一下 [**確定]**。
- 在**新增資料連線**刀中，按一下 [**確定]**。

檢視您移轉行動訊息服務中的目標資料庫中的連接字串可以找到使用者名稱和密碼。


### <a name="set-up-authentication"></a>設定驗證

Azure Mobile 應用程式可讓您設定 Azure Active Directory、 Facebook、 Google、 Microsoft 和 Twitter 驗證服務中。  自訂驗證，則必須分別開發。  請參閱[驗證概念]文件和[驗證快速入門]說明文件，如需詳細資訊。  

## <a name="updating-clients"></a>更新行動用戶端

操作的行動應用程式後端之後，您就可以使用使用您用戶端應用程式的新版本。 行動應用程式也包含新版本的用戶端 Sdk，並與伺服器升級上方，您需要移除之前安裝行動應用程式版本的行動服務 Sdk 的所有參考。

其中一個版本之間的主要變更是建構函式不再需要應用程式鍵。 您現在即可傳入您的行動應用程式的 URL。 例如，在.NET 用戶端上`MobileServiceClient`現在已建構函式︰

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net", // URL of the Mobile App
        );

您可以閱讀有關安裝新的 Sdk 和使用新的結構，透過下列連結︰

- [Android 2.2 或更新版本的版本](app-service-mobile-android-how-to-use-client-library.md)
- [iOS 版 3.0.0 或更新版本](app-service-mobile-ios-how-to-use-client-library.md)
- [.NET (Windows/Xamarin) 版本 2.0.0 或更新版本](app-service-mobile-dotnet-how-to-use-client-library.md)
- [Apache Cordova 2.0 或更新版本](app-service-mobile-cordova-how-to-use-client-library.md)

如果您的應用程式讓您使用的推入通知，請記下每個平台的特定註冊指示，請為有一些變更，也。

當您準備好新的用戶端版本時，[試試看針對您已升級的伺服器的專案。 完成驗證後其運作方式，您可以釋放為 [客戶] 應用程式的新版本。 最後，當您的客戶有機會接收更新，您可以刪除您的應用程式的行動電話服務版本。 此時，您已完全升級至應用程式服務行動應用程式使用最新的行動應用程式伺服器 SDK。

<!-- URLs. -->

[Azure 入口網站]: https://portal.azure.com/
[Azure classic portal]: https://manage.windowsazure.com/
[什麼是行動應用程式？]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /en-us/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Mobile App Server SDK]: https://www.npmjs.com/package/azure-mobile-apps
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /en-us/documentation/services/scheduler/
[Web Job]: ../app-service-web/websites-webjobs-resources.md
[How to use the .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[應用程式服務價格]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[.NET server SDK overview]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[驗證的概念]: ../app-service/app-service-authentication-overview.md
[驗證快速入門]: app-service-mobile-auth.md

[Azure 入口網站]: https://portal.azure.com/
[OData]: http://www.odata.org
[Promise]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[todo sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[samples directory on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js Tools 1.1 for Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js package]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS Middleware]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston
