<properties
    pageTitle="如何使用 Node.js 後端伺服器 SDK 的行動應用程式 |Azure 應用程式服務"
    description="瞭解如何使用 Node.js 後端伺服器 SDK Azure 應用程式服務 Mobile 應用程式。"
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="node"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="how-to-use-the-azure-mobile-apps-nodejs-sdk"></a>如何使用 Azure 行動應用程式 Node.js SDK

[AZURE.INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

本文提供的詳細的資訊，以及顯示如何使用 Node.js 後端 Azure 應用程式服務行動應用程式中的範例。

## <a name="Introduction"></a>簡介

Azure 應用程式服務行動應用程式提供的行動最佳化資料存取 Web API 加入 web 應用程式的能力。  Azure 應用程式服務行動應用程式 SDK 供 ASP.NET 和 Node.js web 應用程式。  SDK 提供下列作業︰

- 存取資料的表格作業 （讀取、 插入、 更新、 刪除）
- 自訂 API 作業

所有 Azure 應用程式服務，包括社交身分識別提供者，例如 Facebook、 Twitter、 Google 和 Microsoft，以及 Azure Active Directory 企業身分識別所允許的身分識別提供者都提供驗證兩項作業。

您可以找到[GitHub 範例目錄]中每個使用案例範例。

## <a name="supported-platforms"></a>支援的平台

Azure 行動應用程式節點 SDK 支援目前 LTS 版本節點及更新版本。  撰寫，最新版的 LTS 是節點 v4.5.0。  其他版本的節點可能運作，但不是支援。

Azure 行動應用程式節點 SDK 支援兩個資料庫驅動程式-節點 mssql 驅動程式支援的 SQL Azure 和本機的 SQL Server 執行個體。  Sqlite3 驅動程式上一個執行個體只支援 SQLite 資料庫。

### <a name="howto-cmdline-basicapp"></a>如何︰ 建立基本 Node.js 後端使用命令列

每個 Azure 應用程式服務行動應用程式 Node.js 後端開始為 ExpressJS 應用程式。  ExpressJS 是最常用的 web 服務架構-[適用於 Node.js。  您可以建立基本[Express]的應用程式，如下所示︰

1. 在命令或 PowerShell 視窗中，建立您的專案的目錄。

        mkdir basicapp

2. 執行 npm 初始化初始化套件結構。

        cd basicapp
        npm init

    Npm 初始化] 命令會詢問一組初始專案的問題。  請參閱範例輸出︰

    ![Npm 初始化輸出][0]

3. 從 npm 存放庫安裝快速和 azure mobile 應用程式的文件庫。

        npm install --save express azure-mobile-apps

4. 建立實作基本行動伺服器 app.js 檔案。

        var express = require('express'),
            azureMobileApps = require('azure-mobile-apps');

        var app = express(),
            mobile = azureMobileApps();

        // Define a TodoItem table
        mobile.tables.add('TodoItem');

        // Add the mobile API so it is accessible as a Web API
        app.use(mobile);

        // Start listening on HTTP
        app.listen(process.env.PORT || 3000);

這個應用程式建立的單一端點行動最佳化的 WebAPI (`/tables/TodoItem`)，提供未經授權的存取使用動態結構描述基礎 SQL 資料存放區。  很適合用來追蹤用戶端文件庫的快速入門︰

- [Android 的用戶端快速入門]
- [Apache Cordova 用戶端快速入門]
- [iOS 用戶端快速入門]
- [Windows 市集用戶端快速入門]
- [Xamarin.iOS 用戶端快速入門]
- [Xamarin.Android 用戶端快速入門]
- [Xamarin.Forms 用戶端快速入門]

您可以找到程式碼，此[GitHub basicapp 範例]中的基本應用程式。

### <a name="howto-vs2015-basicapp"></a>如何︰ 使用 Visual Studio 2015 建立節點後端

Visual Studio 2015 需要開發 Node.js 應用程式內 IDE 副檔名。  若要開始，請安裝[的 Visual Studio Node.js 工具 1.1]。  Visual Studio Node.js 工具會安裝後，建立 Express 4.x 應用程式︰

1. 開啟**新的專案**] 對話方塊 (從**檔案** > **新增** > **專案...**)。

2. 展開**範本** > **JavaScript** > **Node.js**。

3. 選取 [**基本 Azure Node.js Express 4 應用程式**]。

4. 填入的專案名稱。  按一下*[確定]*。

    ![Visual Studio 2015 新專案][1]

5. 以滑鼠右鍵按一下**npm**節點，然後選取 [**安裝新的 npm 套件...**。

6. 您可能需要重新整理 npm 目錄，建立您的第一個 Node.js 應用程式。  如有必要，請按一下 [**重新整理**]。

7. 在 [搜尋] 方塊中輸入_azure mobile 應用程式_。  按一下**azure mobile-應用程式 2.0.0**套件，然後按一下 [**安裝套件**。

    ![安裝新的 npm 套件][2]

8. 按一下 [**關閉**]。

9. 開啟要新增的 Azure 行動應用程式 SDK 支援_app.js_檔案。  在列 6 at 底部的文件庫需要陳述式，請新增下列程式碼︰

        var bodyParser = require('body-parser');
        var azureMobileApps = require('azure-mobile-apps');

    在大約行 27 其他 app.use 陳述式之後，新增下列程式碼︰

        app.use('/users', users);

        // Azure Mobile Apps Initialization
        var mobile = azureMobileApps();
        mobile.tables.add('TodoItem');
        app.use(mobile);

    儲存檔案。

10. 請執行應用程式本機 （API 已傳送 http://localhost:3000） 或發佈至 Azure。

### <a name="create-node-backend-portal"></a>如何︰ 建立 Node.js 後端使用 Azure 入口網站

您可以在[Azure 入口網站]建立 Mobile 應用程式後端權限。 您可以依照下列步驟，或按照[建立行動應用程式](app-service-mobile-ios-get-started.md)的教學課程一起建立的用戶端和伺服器。 教學課程包含這些指示的簡化的版，最適合的概念專案證明。

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

回_開始_刀，在 [**建立表格 API**，底下選擇**Node.js**做為您的**後端語言**。 核取方塊 」**本人，這會覆寫所有網站內容。**」，然後按一下 [**建立 TodoItem 表格**。

### <a name="download-quickstart"></a>如何︰ 下載 Node.js 後端快速入門程式碼專案使用給

當您使用 [**快速入門**刀] 入口網站建立 Node.js Mobile 應用程式後端時，Node.js 專案會為您建立及部署至您的網站。 您可以新增表格和 Api，並編輯程式碼的 Node.js 後端入口網站中的檔案。 您也可以使用各種不同的部署工具，若要下載的後端專案，好讓您可以新增或修改資料表和 Api，然後重新發佈專案。 如需詳細資訊，請參閱[Azure 應用程式服務部署指南]。 下列程序會使用給存放庫，下載快速入門專案程式碼。

1. 如果您還沒有這麼做，請安裝給。 安裝給所需的步驟會視作業系統而有所不同。 請參閱[安裝給](http://git-scm.com/book/en/Getting-Started-Installing-Git)作業系統特定散佈和安裝指南。

2. 請遵循啟用後端網站，記下部署使用者名稱和密碼給存放庫的 [[啟用應用程式服務應用程式存放庫](../app-service-web/app-service-deploy-local-git.md#Step3)中的步驟進行。

3. 在您的行動應用程式後端的刀，記下**給複製 URL**的設定。

4. 執行`git clone`使用給] 命令複製 URL，輸入您的密碼時所需，如下列範例所示︰

        $ git clone https://username@todolist.scm.azurewebsites.net:443/todolist.git

5. 瀏覽至本機目錄，這在上述範例中是 /todolist，並注意 [已下載的專案檔案。 找出`todoitem.json`檔案中`/tables`目錄。  此檔案定義在表格上的權限。  也`todoitem.js`檔案在相同的目錄中，定義的 CRUD 作業指令碼的資料表。

6. 您對專案檔案所做的變更之後，請執行下列命令以新增，確認，然後再變更上傳至網站︰

        $ git commit -m "updated the table script"
        $ git push origin master

    當您新增檔案到專案時，您必須先執行`git add .`] 命令。

每次一組新的認可推入至網站時，會重新發佈網站。

### <a name="howto-publish-to-azure"></a>如何︰ 將您 Node.js 後端發佈至 Azure

Microsoft Azure 提供許多機制發佈您的 Azure 應用程式服務行動應用程式 Node.js 後端 Azure 服務。  包括利用部署工具整合 Visual Studio 與命令列工具，根據來源控制連續的部署選項。  如需有關本主題的詳細資訊，請參閱[Azure 應用程式服務部署指南]。

Azure 應用程式服務具有特定的建議，您應該先部署檢視的 Node.js 應用程式︰

- 如何[指定節點版本]
- 如何[使用節點模組]

### <a name="howto-enable-homepage"></a>如何︰ 啟用應用程式的 [首頁] 頁面

許多應用程式網頁與行動應用程式的組合，並 ExpressJS 架構可讓您合併兩個 facet。  有時候，不過，您可能會想要只實作行動裝置的介面。  則幫助以提供登陸頁面，以確保應用程式服務會執行。  您可以提供您自己的首頁或啟用暫存的 [首頁] 頁面。  若要啟用暫存的 [首頁] 頁面，請使用下列產生 Azure Mobile 應用程式︰

    var mobile = azureMobileApps({ homePage: true });

如果您只想使用此選項，本機開發時，您可以新增此設定您`azureMobile.js`檔案。

## <a name="TableOperations"></a>資料表操作 

Azure mobile 應用程式 Node.js 伺服器 SDK 提供機制公開儲存為 WebAPI Azure SQL 資料庫中的資料表。  提供五個作業。

| 作業 | 描述 |
| --------- | ----------- |
| 取得 /tables/_表格名稱_ | 在資料表中取得所有記錄 |
| 取得 /tables/_表格名稱_/:id | 在資料表中取得特定的記錄 |
| 文章 /tables/_表格名稱_ | 在資料表中建立一筆記錄 |
| 更新 /tables/_表格名稱_/:id | 更新表格中的記錄 |
| 刪除 /tables/_表格名稱_/:id | 刪除表格中的記錄 |

此 WebAPI 支援[OData]與延伸支援[離線資料同步處理]的表格結構描述。

### <a name="howto-dynamicschema"></a>如何︰ 定義資料表使用動態結構描述

使用表格之前，它必須定義。  使用靜態結構描述 （開發人員定義的結構描述中的資料行） 或動態，您可以定義資料表 （SDK 控制根據傳入的邀請的結構描述）。 此外，開發人員可以控制特定層面 WebAPI 將 Javascript 程式碼新增至定義。

最佳作法是，您應該 Javascript 檔案中的 [表格] 目錄中定義的每個資料表，然後使用 tables.import() 方法來匯入的資料表。  延伸 basic 應用程式，可以調整 app.js 檔案︰

    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Define the database schema that is exposed
    mobile.tables.import('./tables');

    // Provide initialization of any tables that are statically defined
    mobile.tables.initialize().then(function () {
        // Add the mobile API so it is accessible as a Web API
        app.use(mobile);

        // Start listening on HTTP
        app.listen(process.env.PORT || 3000);
    });

定義中的資料表。 / tables/TodoItem.js:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Additional configuration for the table goes here

    module.exports = table;

表格會依預設使用動態結構描述。  若要全面關閉動態結構描述，請將應用程式設定**MS_DynamicSchema**為 false Azure 入口網站中。

您可以[在 GitHub todo 範例]中尋找完整的範例。

### <a name="howto-staticschema"></a>如何︰ 定義資料表使用靜態結構描述

您可以明確定義要透過 WebAPI 公開的資料行。  Azure mobile 應用程式 Node.js SDK 會自動新增到您所提供的清單資料離線同步處理所需的任何其他資料行。  快速入門用戶端應用程式，例如需要兩個資料行的資料表︰ 文字 （字串），並完成 （布林值）。  
表格可以定義表格定義 JavaScript 檔案中 （位於表格的目錄中），如下所示︰

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off dynamic schema
    table.dynamicSchema = false;

    module.exports = table;

如果您靜態定義表格，您必須也呼叫 tables.initialize() 方法若要在啟動時建立資料庫結構描述。  Tables.initialize() 方法會傳回[承諾]，以便 web 服務不會服務要求之前初始化資料庫。

### <a name="howto-sqlexpress-setup"></a>如何︰ 在您的本機電腦上的開發資料儲存區的使用 SQL Express

Azure 行動應用程式 AzureMobile 應用程式節點 SDK 提供三個選項可以提供資料服務預設工作︰ SDK 提供為資料服務預設工作的三個選項︰

- 使用**記憶體**的驅動程式提供非持續範例存放區
- 使用**mssql**驅動程式開發提供的 SQL Express 資料存放區
- 使用**mssql**驅動程式提供生產 Azure SQL 資料庫的資料存放區

Azure 行動應用程式 Node.js SDK 使用[mssql Node.js 套件]，來建立及使用 SQL Express 和 SQL 資料庫的連線。  此套件會要求您啟用 TCP 連線，您 SQL Express 的執行個體。

> [AZURE.TIP]記憶體驅動程式不提供一組完整的功能，以進行測試。  如果您想要測試您的後端本機，建議使用 SQL Express 資料存放區及 mssql 驅動程式。

1. 下載並安裝[Microsoft SQL Server 2014 Express]。  請確定您以使用工具版來安裝 SQL Server 2014 Express。  除非您明確需要 64 位元支援，32 位元版本執行時，就會使用較少的記憶體。

2. 執行 [SQL Server 2014 組態管理員]。

  1. 展開左側的樹狀結構功能表中的**SQL Server 網路組態**節點。
  2. 按一下 [ **SQLEXPRESS 通訊協定**]。
  3. 以滑鼠右鍵按一下**TCP/IP** ，然後選取 [**啟用**。  在快顯對話方塊中，按一下**[確定]** 。
  4. 以滑鼠右鍵按一下**TCP/IP** ，然後選取 [**內容]**。
  5. 按一下 [ **IP 位址**] 索引標籤。
  6. 尋找**IPAll**節點。  在 [ **TCP 連接埠**] 欄位中，輸入**1433年**。

         ![Configure SQL Express for TCP/IP][3]

  7. 按一下**[確定]**。  在快顯對話方塊中，按一下**[確定]** 。
  8. 在左側的樹狀結構功能表中按一下 [ **SQL Server 服務**]。
  9. 以滑鼠右鍵按一下 [ **SQL Server (SQLEXPRESS)** ，然後選取 [**重新啟動**
  10. 關閉 [SQL Server 2014 組態管理員]。

3. 執行 SQL Server 2014 Management Studio 並連線到您本機的 SQL Express 執行個體

  1. 以滑鼠右鍵按一下 [物件瀏覽器中的執行個體，然後選取 [**內容**
  2. 選取 [**安全性**] 頁面。
  3. 請確定已選取的**SQL Server 和 Windows 驗證模式**
  4. 按一下**[確定]**

        ![設定 SQL 快速驗證][4]

  5. 展開 [**安全性** > 物件瀏覽器中**登入**
  6. 以滑鼠右鍵按一下 [**登入**，然後選取 [**新增登入**
  7. 輸入的登入名稱。  選取 [ **SQL Server 驗證**]。  輸入密碼，然後輸入相同的密碼，在 [**確認密碼**]。  密碼必須符合 Windows 複雜度需求。
  8. 按一下**[確定]**

        ![將新的使用者新增至 SQL Express][5]

  9. 以滑鼠右鍵按一下新的登入，然後選取 [**內容**
  10. 選取 [**伺服器角色**] 頁面
  11. 核取**擁有**伺服器角色
  12. 按一下**[確定]**
  13. 關閉 [SQL Server 2015 Management Studio 中

請確定您錄製的使用者名稱和您所選取的密碼。  您可能需要指派其他的伺服器角色或根據您的資料庫需求的權限。

Node.js 應用程式會讀取此資料庫的連線字串的**SQLCONNSTR_MS_TableConnectionString**環境變數。  在您的環境中，您可以將此變數。  例如，您可以使用 PowerShell 來設定此環境變數︰

    $env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"

透過 TCP/IP 連線存取資料庫，並提供使用者名稱和密碼連線。

### <a name="howto-config-localdev"></a>如何︰ 設定您的本機開發的專案

Azure Mobile 應用程式會讀取從本機檔案系統中稱為_azureMobile.js_ JavaScript 檔案。  不要使用此檔案至生產環境中設定 Azure 行動應用程式 SDK 改為使用中[Azure 入口網站]應用程式設定。  設定物件時，應該匯出_azureMobile.js_檔案。  是最常見的設定︰

- 資料庫的設定
- 診斷記錄設定
- 替代 CORS 設定

執行上述資料庫設定的範例_azureMobile.js_檔案所示︰

    module.exports = {
        cors: {
            origins: [ 'localhost' ]
        },
        data: {
            provider: 'mssql',
            server: '127.0.0.1',
            database: 'mytestdatabase',
            user: 'azuremobile',
            password: 'T3stPa55word'
        },
        logging: {
            level: 'verbose'
        }
    };

我們建議您新增_azureMobile.js_至_.gitignore_檔案 （或其他原始程式碼控制項略過的檔案） 無法儲存在雲端時，防止密碼。  [Azure 入口網站]中的應用程式設定中設定生產設定。

### <a name="howto-appsettings"></a>如何︰ 設定您的行動應用程式的應用程式設定

大部分的設定， _azureMobile.js_檔案中有相同的應用程式設定[Azure 入口網站]中。  若要在應用程式設定中設定您的應用程式中使用下列清單︰

| 應用程式設定                 | _azureMobile.js_設定  | 描述                               | 有效的值                                |
| :-------------------------- | :------------------------ | :---------------------------------------- | :------------------------------------------ |
| **MS_MobileAppName**        | 名稱                      | 應用程式的名稱                       | 字串                                      |
| **MS_MobileLoggingLevel**   | logging.level             | 要記錄的訊息的最小值的記錄層次      | 錯誤，詳細資訊、 偵錯無聊警告 |
| **MS_DebugMode**            | 偵錯                     | 啟用或停用偵錯模式              | 為 true，則為 false                                 |
| **MS_TableSchema**          | data.schema               | SQL 表格的預設結構描述名稱        | 字串 (預設︰ dbo)                       |
| **MS_DynamicSchema**        | data.dynamicSchema        | 啟用或停用偵錯模式              | 為 true，則為 false                                 |
| **MS_DisableVersionHeader** | （設定為未定義） 版本| 停用的 X ZUMO-伺服器版本標頭 | 為 true，則為 false                                 |
| **MS_SkipVersionCheck**     | skipversioncheck          | 用戶端 API 版本核取方塊可停用     | 為 true，則為 false                                 |

若要設定應用程式設定︰

1. [Azure 入口網站]登入。
2. 選取**所有資源**或**應用程式服務**，然後按一下您的行動應用程式的名稱。
3. 設定刀依預設會開啟。 如果沒有的話，請按一下 [**設定**]。
4. 按一下 [一般] 功能表中的 [**應用程式設定**]。
5. 捲動到 [應用程式設定] 區段。
6. 如果您已設定的應用程式，請按一下 [編輯值的應用程式設定值]。
7. 如果您的應用程式設定不存在，請輸入應用程式設定索引鍵] 方塊和 [值] 方塊中的值。
8. 完成後，按一下 [**儲存**]。

變更大部分的應用程式設定需要重新啟動服務。

### <a name="howto-use-sqlazure"></a>如何︰ 為您的產品資料儲存區使用 SQL 資料庫

<!--- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

使用 Azure SQL 資料庫做為資料存放區是相同在所有 Azure 應用程式服務應用程式類型。 如果您有不這麼做，請遵循這些步驟來建立 Mobile 應用程式後端。

1. [Azure 入口網站]登入。

2. 在視窗的左，按一下 [ **+ 新增**] 按鈕 > **Web + 行動** > **Mobile 應用程式**，然後提供您的行動應用程式後端的名稱。

3. 在 [**資源群組**] 方塊中，輸入相同的名稱為您的應用程式。

4. 已選取 [預設應用程式服務方案。  如果您想要變更您的應用程式服務方案，您可以執行即可應用程式服務方案 >**建立新的 +**。  提供的新的應用程式服務方案的名稱，然後選取適當的位置。  按一下價格層，然後選取適當的價格層的服務。 選取 [**檢視全部**]，檢視更多價格選項的詳細資訊，例如**空閒**和**共用**]。  當您選取的價格層時，按一下 [**選取**] 按鈕。  回到**應用程式服務方案**刀中，按一下**[確定]**。

5. 按一下 [**建立**]。 佈建行動應用程式後端可能需要幾分鐘。  一旦 Mobile 應用程式後端佈建後，入口網站隨即會開啟的行動應用程式後端**設定**刀。

行動應用程式後端建立後，您可以選擇 [將現有的 SQL 資料庫連線到您的行動應用程式後端] 或 [建立新的 SQL 資料庫。  在此區段中，我們建立 SQL 資料庫。

> [AZURE.NOTE]如果您已在相同位置行動應用程式後端資料庫，您可以改為選擇 [**使用現有的資料庫**，然後選取 [該資料庫。 使用資料庫中的其他位置的建議您不要由於更高的延遲。

6. 在新的行動應用程式後端，按一下 [**設定** > **Mobile 應用程式** > **資料** > **+ 新增]**。

7. 在**新增資料連線**刀中，按一下 [ **SQL 資料庫-設定必要的設定** > **建立新的資料庫**。  在 [**名稱**] 欄位中輸入新資料庫的名稱。

8. 按一下 [**伺服器**]。  在**新的伺服器**刀中，在 [**伺服器名稱**] 欄位中，輸入唯一的伺服器名稱，並提供適當**的登入伺服器管理員**和**密碼**。  請確定已核取 [**允許 azure 服務存取伺服器**。  按一下**[確定]**。

    ![建立 Azure SQL 資料庫][6]

9. 在**新的資料庫**刀中，按一下**[確定]**。

10. 回到上**新增資料連線**刀中，選取 [**連接字串**，輸入的登入及建立資料庫時，您所提供的密碼。  如果您使用現有的資料庫，請為該資料庫提供登入認證。  在輸入後，按一下**[確定]**。

11. 再次**新增資料連線**刀 [上一步，請按一下**[確定**]，建立資料庫。

<!--- END OF ALTERNATE INCLUDE -->

建立資料庫可能需要幾分鐘。  使用 [**通知**區域監控部署的進度。  不進度，直到已成功部署資料庫。  當成功部署，在您行動裝置的後端應用程式設定中的 SQL 資料庫執行個體建立連線字串。  您可以查看在**設定**此應用程式設定 > **應用程式設定** > **連接字串**。

### <a name="howto-tables-auth"></a>如何︰ 需要驗證的 access 資料表

如果您想要使用的資料表端點的應用程式服務驗證，您必須設定應用程式服務驗證[Azure 入口網站]中第一次。  如需設定驗證 Azure 應用程式服務中的詳細資訊，檢閱您想要使用的身分識別提供者設定指南︰

- [如何設定 Azure Active Directory 驗證]
- [如何設定 Facebook 驗證]
- [如何設定 Google 驗證]
- [如何設定 Microsoft 驗證]
- [如何設定 Twitter 驗證]

每個資料表有可用來控制存取資料表的 access 屬性。  下列範例會顯示需要驗證的靜態定義的資料表。

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off dynamic schema
    table.dynamicSchema = false;

    // Require authentication to access the table
    table.access = 'authenticated';

    module.exports = table;

Access 屬性可能需要三個值的其中一個

  - *匿名*指出讀取資料驗證不允許的用戶端應用程式
  - *驗證*指示的用戶端應用程式必須傳送有效的驗證權杖要求
  - *停用*會指出此表格目前已停用

如果 access 屬性未定義，允許未經授權的存取。

### <a name="howto-tables-getidentity"></a>如何︰ 使用您的資料表中的宣告驗證

您可以將各種宣告驗證設定後所設定。  這些宣告無法正常透過`context.user`物件。  不過，他們可以使用擷取`context.user.getIdentity()`方法。  `getIdentity()`方法會傳回解析為物件品。  驗證方法 （facebook、 google、 twitter、 microsoftaccount 或 aad） 物件做為索引鍵。

例如，如果您設定的 Microsoft 帳戶驗證和邀請電子郵件地址宣告，您可以新增電子郵件地址與下列表格控制站的記錄︰

    var azureMobileApps = require('azure-mobile-apps');

    // Create a new table definition
    var table = azureMobileApps.table();

    table.columns = {
        "emailAddress": "string",
        "text": "string",
        "complete": "boolean"
    };
    table.dynamicSchema = false;
    table.access = 'authenticated';

    /**
    * Limit the context query to those records with the authenticated user email address
    * @param {Context} context the operation context
    * @returns {Promise} context execution Promise
    */
    function queryContextForEmail(context) {
        return context.user.getIdentity().then((data) => {
            context.query.where({ emailAddress: data.microsoftaccount.claims.emailaddress });
            return context.execute();
        });
    }

    /**
    * Adds the email address from the claims to the context item - used for
    * insert operations
    * @param {Context} context the operation context
    * @returns {Promise} context execution Promise
    */
    function addEmailToContext(context) {
        return context.user.getIdentity().then((data) => {
            context.item.emailAddress = data.microsoftaccount.claims.emailaddress;
            return context.execute();
        });
    }

    // Configure specific code when the client does a request
    // READ - only return records belonging to the authenticated user
    table.read(queryContextForEmail);

    // CREATE - add or overwrite the userId based on the authenticated user
    table.insert(addEmailToContext);

    // UPDATE - only allow updating of record belong to the authenticated user
    table.update(queryContextForEmail);

    // DELETE - only allow deletion of records belong to the authenticated uer
    table.delete(queryContextForEmail);

    module.exports = table;

若要查看哪些宣告可用，請使用網頁瀏覽器來檢視`/.auth/me`網站的結束點。

### <a name="howto-tables-disabled"></a>如何︰ 停用存取特定的表格作業

除了出現在表格上，存取屬性可控制個別的作業。  有四種作業︰

  - *閱讀*是 RESTful 取得作業表格
  - *插入*是在表格上的 [RESTful 文章] 作業
  - *更新*是在表格上的 RESTful 修補作業
  - *刪除*是 RESTful 刪除作業表格

例如，您可能要提供唯讀未經認證的資料表︰

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Read-Only table - only allow READ operations
    table.read.access = 'anonymous';
    table.insert.access = 'disabled';
    table.update.access = 'disabled';
    table.delete.access = 'disabled';

    module.exports = table;

### <a name="howto-tables-query"></a>如何︰ 調整表格作業搭配使用的查詢

為表格作業的一般需求是提供資料的限制的檢視。  例如，您可能會提供，您僅可讀取或更新您自己的記錄，為標記的已驗證的使用者識別碼的資料表。  下列表格定義提供這項功能︰

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define a static schema for the table
    table.columns = {
        "userId": "string",
        "text": "string",
        "complete": "boolean"
    };
    table.dynamicSchema = false;

    // Require authentication for this table
    table.access = 'authenticated';

    // Ensure that only records for the authenticated user are retrieved
    table.read(function (context) {
        context.query.where({ userId: context.user.id });
        return context.execute();
    });

    // When adding records, add or overwrite the userId with the authenticated user
    table.insert(function (context) {
        context.item.userId = context.user.id;
        return context.execute();
    });

    module.exports = table;

以正常方式執行查詢的運算由查詢屬性，您可以調整位置與子句。 查詢屬性是用來將 OData 查詢轉換成資料後端可以處理的項目[QueryJS]物件。  （例如前一個） 的簡單相等情況下，可以用地圖。 您也可以新增特定的 SQL 子句︰

    context.query.where('myfield eq ?', 'value');

### <a name="howto-tables-softdelete"></a>如何︰ 設定柔刪除的表格

柔邊刪除並不會實際刪除記錄。  改為，將它們標示為資料庫中刪除刪除的欄設為 true。  除非行動用戶端 SDK 使用 IncludeDeleted() Azure 行動應用程式 SDK 會自動從結果移除柔刪除記錄。  若要設定柔刪除的表格，設定`softDelete`表格定義檔案中的屬性︰

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off dynamic schema
    table.dynamicSchema = false;

    // Turn on Soft Delete
    table.softDelete = true;

    // Require authentication to access the table
    table.access = 'authenticated';

    module.exports = table;

您應該建立清除記錄-從用戶端應用程式，透過 WebJob，Azure 函數或自訂的 API 的機制。

### <a name="howto-tables-seeding"></a>如何︰ 植資料庫的資料

在建立新的應用程式時，您可能會想要植資料的資料表。  這可以表格定義 JavaScript 檔案中，如下所示︰

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };
    table.seed = [
        { text: 'Example 1', complete: false },
        { text: 'Example 2', complete: true }
    ];

    // Turn off dynamic schema
    table.dynamicSchema = false;

    // Require authentication to access the table
    table.access = 'authenticated';

    module.exports = table;

種子的資料僅是 Azure 行動應用程式 SDK 建立資料表時。  如果資料庫中的資料表已存在，沒有資料會插入表格。  如果已開啟 [動態結構描述，從基本資料推斷出結構描述。

我們建議您明確呼叫`tables.initialize()`服務可讓您開始執行時建立資料表的方法。

### <a name="Swagger"></a>如何︰ 啟用 Swagger 支援

Azure 應用程式服務行動應用程式隨附的內建[Swagger]支援。  若要啟用 Swagger 支援，第一次安裝做為相依性的 swagger ui:

    npm install --save swagger-ui

安裝後，您可以啟用 Swagger 支援 Azure Mobile 應用程式建構函式︰

    var mobile = azureMobileApps({ swagger: true });

您可能只想来啟用 Swagger 支援開發版本。  您可以利用`NODE_ENV`應用程式設定︰

    var mobile = azureMobileApps({ swagger: process.env.NODE_ENV !== 'production' });

Swagger 端點位於 http://_yoursite_.azurewebsites.net/swagger。  您可以存取 Swagger 透過 UI`/swagger/ui`結束點。  如果您選擇要在整個應用程式需要驗證，Swagger 會產生錯誤。  為了獲得最佳結果，選擇 [允許未經驗證的要求，透過 Azure 應用程式服務驗證 / 授權設定]，然後驗證使用控制`table.access`屬性。

您也可以新增的 Swagger 選項您`azureMobile.js`檔案，如果您只想 Swagger 支援本機開發時。

## <a name="a-namepushpush-notifications"></a><a name="push">推播通知

Azure 通知集線器，讓您的裝置傳送目標推入通知，在所有的主要平台之間整合 Mobile 應用程式。 藉由使用通知集線器，您可以傳送推入通知，可在 iOS，Android 和 Windows 裝置。 若要進一步瞭解所有您可以處理通知集線器，請參閱[通知集線器概觀](../notification-hubs/notification-hubs-push-notification-overview.md)。

### </a><a name="send-push"></a>如何︰ 傳送推入通知

下列程式碼會顯示如何使用廣播推入通知傳送至已註冊的 iOS 裝置的推入物件︰

    // Create an APNS payload.
    var payload = '{"aps": {"alert": "This is an APNS payload."}}';

    // Only do the push if configured
    if (context.push) {
        // Send a push notification using APNS.
        context.push.apns.send(null, payload, function (error) {
            if (error) {
                // Do something or log the error.
            }
        });
    }

藉由建立範本推入註冊從用戶端，您可以改為傳送範本推入郵件給所有受支援的平台上的裝置。 下列程式碼會示範如何傳送範本通知︰

    // Define the template payload.
    var payload = '{"messageParam": "This is a template payload."}';

    // Only do the push if configured
    if (context.push) {
        // Send a template notification.
        context.push.send(null, payload, function (error) {
            if (error) {
                // Do something or log the error.
            }
        });
    }


###<a name="push-user"></a>如何︰ 已驗證的使用者使用標籤傳送推入通知

當已驗證的使用者註冊推入通知時，使用者識別碼標記會自動新增註冊。 藉由使用這個標記，您可以傳送推入通知登錄特定使用者的所有裝置。 下列程式碼會要求使用者的 SID，並將範本推入通知傳送至每個裝置註冊中，針對該使用者進行︰

    // Only do the push if configured
    if (context.push) {
        // Send a notification to the current user.
        context.push.send(context.user.id, payload, function (error) {
            if (error) {
                // Do something or log the error.
            }
        });
    }

當您註冊從經過驗證的用戶端的推入通知，請確定該驗證完成再嘗試登錄。

## <a name="CustomAPI"></a>自訂的 Api

###  <a name="howto-customapi-basic"></a>如何︰ 定義自訂的 API


資料存取 API 透過 /tables 端點，除了 Azure Mobile 應用程式可以提供自訂 API 涵蓋的範圍。  自訂 Api 定義類似的方式，定義，而且可以存取的所有相同功能，包括驗證。

如果您想要使用自訂 API 的應用程式服務驗證，您必須設定應用程式服務驗證[Azure 入口網站]中第一次。  如需設定驗證 Azure 應用程式服務中的詳細資訊，檢閱您想要使用的身分識別提供者設定指南︰

- [如何設定 Azure Active Directory 驗證]
- [如何設定 Facebook 驗證]
- [如何設定 Google 驗證]
- [如何設定 Microsoft 驗證]
- [如何設定 Twitter 驗證]

定義自訂的 Api 以表格 API 許多相同的方式。

1. 建立**api**目錄
2. 建立**api**目錄中的 API 定義 JavaScript 檔案。
3. 使用匯入方法匯入的**api**目錄。

以下是我們先前使用基本應用程式範例為基礎的原型 api 定義。

    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Import the Custom API
    mobile.api.import('./api');

    // Add the mobile API so it is accessible as a Web API
    app.use(mobile);

    // Start listening on HTTP
    app.listen(process.env.PORT || 3000);

讓我們來看範例會傳回使用_Date.now()_方法的伺服器日期的 API。  以下是 api/date.js 檔案︰

    var api = {
        get: function (req, res, next) {
            var date = { currentTime: Date.now() };
            res.status(200).type('application/json').send(date);
        });
    };

    module.exports = api;

每個參數是標準的 RESTful 動詞-取得、 文章、 修補程式或刪除。  方法是將所需的輸出傳送的標準[ExpressJS 介軟體]函數。

### <a name="howto-customapi-auth"></a>如何︰ 自訂的 API 的存取要求驗證

Azure 行動應用程式 SDK 會以相同的方式，[表格結束點] 及 [自訂 Api 實作驗證。  若要新增驗證 API 開發前一節中，新增**存取**屬性︰

    var api = {
        get: function (req, res, next) {
            var date = { currentTime: Date.now() };
            res.status(200).type('application/json').send(date);
        });
    };
    // All methods must be authenticated.
    api.access = 'authenticated';

    module.exports = api;

您也可以指定驗證在特定的作業︰

    var api = {
        get: function (req, res, next) {
            var date = { currentTime: Date.now() };
            res.status(200).type('application/json').send(date);
        }
    };
    // The GET methods must be authenticated.
    api.get.access = 'authenticated';

    module.exports = api;

需要驗證的自訂 Api 必須使用相同的權杖所用的表格端點。

### <a name="howto-customapi-auth"></a>如何︰ 處理大型檔案上傳

Azure 行動應用程式 SDK 使用[本文剖析器介軟體](https://github.com/expressjs/body-parser)，可接受和解碼您上傳的本文內容。  您可以預先設定接受較大的檔案上傳本文剖析器︰

    var express = require('express'),
        bodyParser = require('body-parser'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Set up large body content handling
    app.use(bodyParser.json({ limit: '50mb' }));
    app.use(bodyParser.urlencoded({ limit: '50mb', extended: true }));

    // Import the Custom API
    mobile.api.import('./api');

    // Add the mobile API so it is accessible as a Web API
    app.use(mobile);

    // Start listening on HTTP
    app.listen(process.env.PORT || 3000);

基底 64 編碼之前傳輸檔案。  這個動作會增加的實際的上傳大小 （和因此大小，必須考量的）。

### <a name="howto-customapi-sql"></a>如何︰ 執行自訂 SQL 陳述式

Azure 行動應用程式 SDK 可讓您存取整個內容透過要求物件，讓您輕鬆地執行定義的資料提供者的參數化的 SQL 陳述式︰

    var api = {
        get: function (request, response, next) {
            // Check for parameters - if not there, pass on to a later API call
            if (typeof request.params.completed === 'undefined')
                return next();

            // Define the query - anything that can be handled by the mssql
            // driver is allowed.
            var query = {
                sql: 'UPDATE TodoItem SET complete=@completed',
                parameters: [{
                    completed: request.params.completed
                }]
            };

            // Execute the query.  The context for Azure Mobile Apps is available through
            // request.azureMobile - the data object contains the configured data provider.
            request.azureMobile.data.execute(query)
            .then(function (results) {
                response.json(results);
            });
        }
    };

    api.get.access = 'authenticated';
    module.exports = api;

## <a name="Debugging"></a>偵錯、 簡單的表格和簡單的 Api

### <a name="howto-diagnostic-logs"></a>如何︰ 偵錯、 診斷，及疑難排解 Azure Mobile 應用程式

Azure 應用程式服務會提供幾個偵錯時，及疑難排解 Node.js 應用程式的技巧。
請參閱下列文章中疑難排解您 Node.js 行動後端, 的快速入門︰

- [監視 Azure 的應用程式服務]
- [啟用診斷記錄在 Azure 應用程式服務]
- [疑難排解在 Visual Studio 中 Azure 應用程式服務]

Node.js 應用程式有權存取廣泛的診斷記錄工具。  在內部，Azure 行動應用程式 Node.js SDK 將[著重]用於診斷記錄。  啟用偵錯模式或設定**MS_DebugMode**應用程式設定為 true [Azure 入口網站]中，會自動啟用記錄。 產生的記錄會出現在 [診斷記錄在[Azure 入口網站]。

### <a name="in-portal-editing"></a><a name="work-easy-tables"></a>如何︰ 使用 Azure 入口網站中的簡單的表格

簡單的表格，在入口網站可讓您建立並使用表格在入口網站的權限。 您甚至可以編輯表格作業使用應用程式服務編輯器。

當您按一下**簡單的表格**，您的後端網站設定] 中時，您可以新增、 修改或刪除表格。 您也可以查看資料表中的資料。

![使用簡單的表格](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-tables.png)

下列命令，可在命令列中的表格︰

+ **變更權限**]-修改已讀取的權限、 插入、 更新和刪除表格的作業。 
  若要允許匿名存取時，需要驗證]，或停用所有作業存取可以選項。 
+ **編輯指令碼**-表格的指令碼檔，隨即會開啟應用程式服務編輯器] 中。
+ **管理結構描述**-新增或刪除欄或變更表格索引。
+ **清除表格**-截斷現有的資料表會刪除所有資料列，但又結構描述不會變更。
+ **刪除列**-刪除個別資料列的資料。
+ **檢視串流記錄**-可讓您連線到您的網站串流記錄服務。

###<a name="work-easy-apis"></a>如何︰ 使用 Azure 入口網站中的簡單的 Api

輕鬆地在入口網站的 Api 可讓您建立並使用自訂的 Api 權限，在入口網站。 您可以編輯使用應用程式服務編輯器的 API 指令碼。

當您按一下**輕鬆 Api**後端網站設定中時，您可以新增、 修改或刪除自訂的 API 結束點。

![使用簡單的 Api](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-apis.png)

在入口網站，您可以變更指定的 HTTP 動作的存取權限、 編輯 API 指令碼檔案在應用程式服務編輯器] 中，或檢視資料流記錄。

###<a name="online-editor"></a>如何︰ 編輯程式碼在應用程式服務編輯器

Azure 入口網站可讓您編輯 Node.js 後端指令碼檔案在應用程式服務編輯器] 中，而不需要下載至您的本機電腦的專案。 若要編輯線上編輯器中的指令碼檔案︰

1. 在您的行動應用程式後端刀，按一下 [**所有設定**>**簡單的表格**或**輕鬆 Api**，都按一下資料表或 API，然後都按一下 [**編輯指令碼**。 在應用程式服務編輯器] 中開啟指令碼檔案。

    ![應用程式服務編輯器](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-visual-studio-editor.png)

2. 變更您在線上編輯器中的程式碼檔案。 當您輸入時，會自動儲存變更。


<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png
[1]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-new-project.png
[2]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-install-npm.png
[3]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-config.png
[4]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-authconfig.png
[5]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-newuser-1.png
[6]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/dotnet-backend-create-db.png

<!-- URLs -->
[Android 的用戶端快速入門]: app-service-mobile-android-get-started.md
[Apache Cordova 用戶端快速入門]: app-service-mobile-cordova-get-started.md
[iOS 用戶端快速入門]: app-service-mobile-ios-get-started.md
[Xamarin.iOS 用戶端快速入門]: app-service-mobile-xamarin-ios-get-started.md
[Xamarin.Android 用戶端快速入門]: app-service-mobile-xamarin-android-get-started.md
[Xamarin.Forms 用戶端快速入門]: app-service-mobile-xamarin-forms-get-started.md
[Windows 市集用戶端快速入門]: app-service-mobile-windows-store-dotnet-get-started.md
[HTML/Javascript Client QuickStart]: app-service-html-get-started.md
[離線資料同步處理]: app-service-mobile-offline-data-sync.md
[如何設定 Azure Active Directory 驗證]: app-service-mobile-how-to-configure-active-directory-authentication.md
[如何設定 Facebook 驗證]: app-service-mobile-how-to-configure-facebook-authentication.md
[如何設定 Google 驗證]: app-service-mobile-how-to-configure-google-authentication.md
[如何設定 Microsoft 驗證]: app-service-mobile-how-to-configure-microsoft-authentication.md
[如何設定 Twitter 驗證]: app-service-mobile-how-to-configure-twitter-authentication.md
[Azure 應用程式服務部署指南]: ../app-service-web/web-sites-deploy.md
[監視 Azure 的應用程式服務]: ../app-service-web/web-sites-monitor.md
[啟用診斷記錄在 Azure 應用程式服務]: ../app-service-web/web-sites-enable-diagnostic-log.md
[疑難排解在 Visual Studio 中 Azure 應用程式服務]: ../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md
[指定節點版本]: ../nodejs-specify-node-version-azure-apps.md
[使用節點模組]: ../nodejs-use-node-modules-azure-apps.md
[Create a new Azure App Service]: ../app-service-web/
[azure-mobile-apps]: https://www.npmjs.com/package/azure-mobile-apps
[Express]: http://expressjs.com/
[Swagger]: http://swagger.io/

[Azure 入口網站]: https://portal.azure.com/
[OData]: http://www.odata.org
[品]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[在 GitHub basicapp 範例]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[在 GitHub todo 範例]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[在 GitHub 範例目錄]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Visual Studio Node.js 工具 1.1 （英文）]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js 套件]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS 介軟體]: http://expressjs.com/guide/using-middleware.html
[著重]: https://github.com/winstonjs/winston
