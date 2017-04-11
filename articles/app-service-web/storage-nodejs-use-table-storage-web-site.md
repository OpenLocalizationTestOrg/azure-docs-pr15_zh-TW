<properties
    pageTitle="使用 Azure 資料表服務 Node.js web 應用程式"
    description="本教學課程會指導您如何使用 Azure 資料表服務來裝載 Node.js 應用程式的資料儲存在 Azure 應用程式服務 Web 應用程式。"
    tags="azure-portal"
    services="app-service\web, storage"
    documentationCenter="nodejs"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="nodejs-web-app-using-the-azure-table-service"></a>使用 Azure 資料表服務 Node.js web 應用程式

## <a name="overview"></a>概觀

本教學課程教您如何使用 Azure 資料管理所提供的資料表服務來儲存和存取[Azure 應用程式服務](http://go.microsoft.com/fwlink/?LinkId=529714)Web 應用程式中裝載的[節點]應用程式的資料。 本教學課程假設您已使用節點和[給]先前經驗。

您將瞭解︰

* 如何安裝節點模組使用 npm （節點封裝管理員）

* 如何使用 Azure 資料表服務

* 如何使用 Azure CLI 建立 web 應用程式。

依照本教學課程中，您會建立簡單的 web 式允許建立、 擷取及完成工作的 「 待辦事項清單 」 應用程式。 工作會儲存在表格服務。

以下是完成的應用程式︰

![顯示空白的工作清單的網頁][node-table-finished]

>[AZURE.NOTE] 如果您想要開始使用 Azure 應用程式服務註冊 Azure 帳戶之前，請移至[嘗試應用程式服務](http://go.microsoft.com/fwlink/?LinkId=523751)，可以讓您立即建立短暫入門 web 應用程式在應用程式服務。 必要; 沒有信用卡沒有承諾。

## <a name="prerequisites"></a>必要條件

前遵循本文中的指示，請確定您已安裝下列︰

* [節點]版本 0.10.24 或更新版本

* [給]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## <a name="create-a-storage-account"></a>建立儲存的帳戶

建立 Azure 儲存體帳戶。 應用程式會使用此帳戶來儲存待辦事項項目。

1.  登入[Azure 入口網站](https://portal.azure.com/)。

2. 按一下左下角的入口網站上的 [**新增**] 圖示，然後按一下 [**資料 + 的儲存空間** > **儲存空間**。 為儲存帳戶唯一的名稱，然後為其建立新的[資源群組](../azure-resource-manager/resource-group-overview.md)]。

    ![[新增] 按鈕](./media/storage-nodejs-use-table-storage-web-site/configure-storage.png)

    儲存帳戶建立之後，[**通知**] 按鈕的閃爍綠色**成功**並儲存帳戶的刀已開啟，以顯示它屬於您建立的新資源群組。

5. 在儲存帳戶的刀，按一下 [**設定** > **鍵**。 複製到剪貼簿的主要便捷鍵。

    ![便捷鍵][portal-storage-access-keys]


##<a name="install-modules-and-generate-scaffolding"></a>安裝模組，並產生臨時平台

此區段中，您會建立新的節點應用程式，並使用 npm 新增模組套件。 這個應用程式中，您會使用[Express]和[Azure]模組。 Express 模組節點，提供一個模型檢視控制器架構，而 Azure 模組提供表格服務的連線。

### <a name="install-express-and-generate-scaffolding"></a>安裝 express 並產生臨時平台

1. 從命令列中，建立新的目錄，名稱為**工作清單**，並切換到該目錄。  

2. 輸入下列命令以安裝 Express 模組。

        npm install express-generator@4.2.0 -g

    視作業系統而定，您可能需要將指令之前 ' sudo:

        sudo npm install express-generator@4.2.0 -g

    輸出便會出現類似下面的範例︰

        express-generator@4.2.0 /usr/local/lib/node_modules/express-generator
        ├── mkdirp@0.3.5
        └── commander@1.3.2 (keypress@0.1.0)

    > [AZURE.NOTE] 「-G' 參數全域安裝模組。 如此一來，我們可以使用**快速**來產生 web 應用程式臨時平台，而不需輸入其他的路徑資訊。

4. 若要建立應用程式的臨時平台，請輸入**快速**命令︰

        express

    這個命令的輸出便會出現類似下面的範例︰

           create : .
           create : ./package.json
           create : ./app.js
           create : ./public
           create : ./public/images
           create : ./routes
           create : ./routes/index.js
           create : ./routes/users.js
           create : ./public/stylesheets
           create : ./public/stylesheets/style.css
           create : ./views
           create : ./views/index.jade
           create : ./views/layout.jade
           create : ./views/error.jade
           create : ./public/javascripts
           create : ./bin
           create : ./bin/www

           install dependencies:
             $ cd . && npm install

           run the app:
             $ DEBUG=my-application ./bin/www

    現在您有幾個新的目錄及**工作清單**目錄中的檔案。

### <a name="install-additional-modules"></a>安裝的其他模組

一個檔案的**快速**建立是**package.json**。 此檔案包含模組相依性的清單。 更新版本中，當您部署的應用程式至應用程式服務 Web 應用程式時，此檔案會決定哪些模組需要安裝在 Azure。

從命令列，輸入下列命令以安裝**package.json**檔案中所描述的模組。 您可能需要使用 「 sudo 」。

    npm install

這個命令的輸出便會出現類似下面的範例︰

    debug@0.7.4 node_modules\debug

    cookie-parser@1.0.1 node_modules\cookie-parser
    ├── cookie-signature@1.0.3
    └── cookie@0.1.0

    [...]


接下來，輸入下列命令以安裝[azure]與[節點 uuid]、 [nconf] [非同步]模組︰

    npm install azure-storage node-uuid async nconf --save

**-儲存**旗標將這些模組的項目新增至**package.json**檔案。

這個命令的輸出便會出現類似下面的範例︰

    async@0.9.0 node_modules\async

    node-uuid@1.4.1 node_modules\node-uuid

    nconf@0.6.9 node_modules\nconf
    ├── ini@1.2.1
    ├── async@0.2.9
    └── optimist@0.6.0 (wordwrap@0.0.2, minimist@0.0.10)

    [...]


## <a name="create-the-application"></a>建立應用程式

現在，我們已準備好建立應用程式。

### <a name="create-a-model"></a>建立模型

*模型*是物件，表示您的應用程式中的資料。 應用程式，只模型是任務的物件，代表待辦事項清單中的項目。 工作會有下列欄位︰

- PartitionKey
- RowKey
- 名稱 （字串）
- 類別 （字串）
- 完成 （布林值）

**PartitionKey**和**RowKey**服務所使用的資料表為資料表索引鍵。 如需詳細資訊，請參閱[瞭解表格服務資料模型](https://msdn.microsoft.com/library/azure/dd179338.aspx)。


1. 在**工作清單**目錄中，建立新名為目錄**模型**。

2. 在**模型**目錄中，建立名為**task.js**的新檔案。 這個檔案會包含您的應用程式所建立的工作的模型。

3. 開頭的**task.js**檔案時，新增下列程式碼，參照所需的文件庫︰

        var azure = require('azure-storage');
        var uuid = require('node-uuid');
        var entityGen = azure.TableUtilities.entityGenerator;

4. 新增下列程式碼來定義並匯出工作物件。 此物件負責連線至資料表。

        module.exports = Task;

        function Task(storageClient, tableName, partitionKey) {
          this.storageClient = storageClient;
          this.tableName = tableName;
          this.partitionKey = partitionKey;
          this.storageClient.createTableIfNotExists(tableName, function tableCreated(error) {
            if(error) {
              throw error;
            }
          });
        };

5. 下列程式碼新增任務，在物件上定義的其他方法可讓儲存在資料表中的資料互動︰

        Task.prototype = {
          find: function(query, callback) {
            self = this;
            self.storageClient.queryEntities(this.tableName, query, null, function entitiesQueried(error, result) {
              if(error) {
                callback(error);
              } else {
                callback(null, result.entries);
              }
            });
          },

          addItem: function(item, callback) {
            self = this;
            // use entityGenerator to set types
            // NOTE: RowKey must be a string type, even though
            // it contains a GUID in this example.
            var itemDescriptor = {
              PartitionKey: entityGen.String(self.partitionKey),
              RowKey: entityGen.String(uuid()),
              name: entityGen.String(item.name),
              category: entityGen.String(item.category),
              completed: entityGen.Boolean(false)
            };
            self.storageClient.insertEntity(self.tableName, itemDescriptor, function entityInserted(error) {
              if(error){  
                callback(error);
              }
              callback(null);
            });
          },

          updateItem: function(rKey, callback) {
            self = this;
            self.storageClient.retrieveEntity(self.tableName, self.partitionKey, rKey, function entityQueried(error, entity) {
              if(error) {
                callback(error);
              }
              entity.completed._ = true;
              self.storageClient.updateEntity(self.tableName, entity, function entityUpdated(error) {
                if(error) {
                  callback(error);
                }
                callback(null);
              });
            });
          }
        }

6. 儲存並關閉**task.js**檔案。

### <a name="create-a-controller"></a>建立控制器

*控制站*處理 HTTP 要求和呈現 HTML 回應。

1. 在**工作清單/路由**目錄中，建立名為**tasklist.js**的新檔案，然後在文字編輯器中開啟。

2. 將下列程式碼新增至 [ **tasklist.js**。 這會載入 azure 和非同步模組，可使用**tasklist.js**。 這也定義**工作清單**，該函數會傳遞我們先前定義的 [**工作**] 物件的執行個體︰

        var azure = require('azure-storage');
        var async = require('async');

        module.exports = TaskList;

3. 定義**工作清單**物件。

        function TaskList(task) {
          this.task = task;
        }


4. **工作清單**中加入下列方法︰

        TaskList.prototype = {
          showTasks: function(req, res) {
            self = this;
            var query = new azure.TableQuery()
              .where('completed eq ?', false);
            self.task.find(query, function itemsFound(error, items) {
              res.render('index',{title: 'My ToDo List ', tasks: items});
            });
          },

          addTask: function(req,res) {
            var self = this;
            var item = req.body.item;
            self.task.addItem(item, function itemAdded(error) {
              if(error) {
                throw error;
              }
              res.redirect('/');
            });
          },

          completeTask: function(req,res) {
            var self = this;
            var completedTasks = Object.keys(req.body);
            async.forEach(completedTasks, function taskIterator(completedTask, callback) {
              self.task.updateItem(completedTask, function itemsUpdated(error) {
                if(error){
                  callback(error);
                } else {
                  callback(null);
                }
              });
            }, function goHome(error){
              if(error) {
                throw error;
              } else {
               res.redirect('/');
              }
            });
          }
        }


### <a name="modify-appjs"></a>修改 app.js

1. 從**工作清單**的目錄，開啟**app.js**檔案。 執行**快速**命令以較舊版本建立此檔案。

2. 開頭的檔案時，新增下列載入 azure 模組，設定資料表名稱，而分割索引鍵，並設定此範例中所使用的儲存認證︰

        var azure = require('azure-storage');
        var nconf = require('nconf');
        nconf.env()
             .file({ file: 'config.json', search: true });
        var tableName = nconf.get("TABLE_NAME");
        var partitionKey = nconf.get("PARTITION_KEY");
        var accountName = nconf.get("STORAGE_NAME");
        var accountKey = nconf.get("STORAGE_KEY");

    > [AZURE.NOTE] nconf 會從環境變數或**config.json**檔案，但我們會於稍後建立載入的設定值。

3. 在 app.js 檔案中，捲動至您在這裡看見的下列行︰

        app.use('/', routes);
        app.use('/users', users);

    上述各行取代下方所顯示的程式碼。 這會初始化<strong>工作</strong>的執行個體以連線至您儲存的帳戶。 傳遞給<strong>工作清單</strong>，其會使用它來進行通訊表格服務︰

        var TaskList = require('./routes/tasklist');
        var Task = require('./models/task');
        var task = new Task(azure.createTableService(accountName, accountKey), tableName, partitionKey);
        var taskList = new TaskList(task);

        app.get('/', taskList.showTasks.bind(taskList));
        app.post('/addtask', taskList.addTask.bind(taskList));
        app.post('/completetask', taskList.completeTask.bind(taskList));

4. 儲存**app.js**檔案。

### <a name="modify-the-index-view"></a>修改 [索引] 檢視

1. 在文字編輯器中開啟**tasklist/views/index.jade**檔案。

2. 取代下列程式碼之檔案的完整內容。 定義檢視，顯示現有的工作，並包含新增工作與標示為已完成的現有的表單。

        extends layout

        block content
          h1= title
          br

          form(action="/completetask", method="post")
            table.table.table-striped.table-bordered
              tr
                td Name
                td Category
                td Date
                td Complete
              if (typeof tasks === "undefined")
                tr
                  td
              else
                each task in tasks
                  tr
                    td #{task.name._}
                    td #{task.category._}
                    - var day   = task.Timestamp._.getDate();
                    - var month = task.Timestamp._.getMonth() + 1;
                    - var year  = task.Timestamp._.getFullYear();
                    td #{month + "/" + day + "/" + year}
                    td
                      input(type="checkbox", name="#{task.RowKey._}", value="#{!task.completed._}", checked=task.completed._)
            button.btn(type="submit") Update tasks
          hr
          form.well(action="/addtask", method="post")
            label Item Name:
            input(name="item[name]", type="textbox")
            label Item Category:
            input(name="item[category]", type="textbox")
            br
            button.btn(type="submit") Add item

3. 儲存並關閉**index.jade**檔案。

### <a name="modify-the-global-layout"></a>修改全域版面配置

**檢視**的目錄中的**layout.jade**檔案是其他**.jade**檔案的通用範本。 您會在此步驟中修改，使用[Twitter 啟動安裝程式](https://github.com/twbs/bootstrap)，這是工具組，使其易於設計部份的外觀 web 應用程式。

下載並解壓縮的[Twitter 啟動安裝程式](http://getbootstrap.com/)的檔案。 啟動安裝**css**資料夾**bootstrap.min.css**檔複製到您的應用程式的**公用/樣式表**目錄。

[**檢視**] 資料夾中，開啟**layout.jade** ，並以下列取代整個內容︰

    doctype html
    html
      head
        title= title
        link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')
        link(rel='stylesheet', href='/stylesheets/style.css')
      body.app
        nav.navbar.navbar-default
          div.navbar-header
          a.navbar-brand(href='/') My Tasks
        block content

### <a name="create-a-config-file"></a>建立設定檔

若要執行應用程式，我們會放 Azure 儲存體認證設定檔。 建立一個名為 [與下列 JSON * *config.json**檔案︰

    {
        "STORAGE_NAME": "<storage account name>",
        "STORAGE_KEY": "<storage access key>",
        "PARTITION_KEY": "mytasks",
        "TABLE_NAME": "tasks"
    }

以您先前建立的儲存體帳戶名稱取代**儲存體帳戶名稱**並**儲存便捷鍵**以取代您儲存的帳戶的主要便捷鍵。 例如︰

    {
        "STORAGE_NAME": "nodejsappstorage",
        "STORAGE_KEY": "KG0oDd..."
        "PARTITION_KEY": "mytasks",
        "TABLE_NAME": "tasks"
    }

儲存此檔案*目錄高一個層級*比**工作清單**目錄，像這樣︰

    parent/
      |-- config.json
      |-- tasklist/

執行此動作的原因是為了避免將來源控制檢查設定檔位置可能會公用。 我們將應用程式部署到 Azure 時，我們將使用環境變數，而不是設定檔。


## <a name="run-the-application-locally"></a>在本機上執行應用程式

若要測試您的本機電腦上的應用程式，請執行下列步驟︰

1. 從命令列，請在**工作清單**目錄變更目錄。

2. 啟動本機的應用程式中使用下列命令︰

        npm start

3. 開啟網頁瀏覽器，然後瀏覽至 http://127.0.0.1:3000。

    網頁類似下列範例會出現。

    ![顯示空白的工作清單的網頁][node-table-finished]

4. 若要建立新的待辦事項項目，請輸入的名稱和類別，然後按一下 [**新增項目**。 

6. 若要將工作標示為完成，核取 [**完成**，然後按一下 [**更新任務**。

    ![工作清單中的新項目的圖像][node-table-list-items]

即使在本機上執行應用程式，它會將資料儲存 Azure 資料表服務中。

## <a name="deploy-your-application-to-azure"></a>部署至 Azure 應用程式

本節中的步驟來建立新 web 應用程式在應用程式服務中使用的 Azure 的命令列工具，，然後使用給部署應用程式。 若要執行這些步驟，您必須 Azure 的訂閱。

> [AZURE.NOTE] 也可以使用[Azure 入口網站](https://portal.azure.com/)執行這些步驟。 請參閱[建立及部署 Node.js web 應用程式中 Azure 應用程式服務]。
>
> 如果這是您所建立的第一個 web 應用程式，您必須使用 Azure 入口網站部署這個應用程式。

若要開始，請從命令列中輸入下列命令以安裝[Azure CLI] :

    npm install azure-cli -g

### <a name="import-publishing-settings"></a>發佈的設定匯入

在此步驟中，您將下載檔案包含您的訂閱的相關資訊。

1. 輸入下列命令︰

        azure account download

    這個命令啟動瀏覽器，並瀏覽至 [下載] 頁面。 如果出現提示，請登入 Azure 訂閱相關聯的帳戶。

    <!-- ![The download page][download-publishing-settings] -->

    會自動; 開始下載檔案如果沒有出現，您可以按一下 [手動下載檔案] 頁面的開頭的連結。 儲存檔案，並記下檔案路徑。

2. 輸入下列命令以匯入設定︰

        azure account import <path-to-file>

    在上一個步驟中，指定發佈您下載的設定檔的路徑和檔名。

3. 匯入設定之後，刪除發佈設定檔。 它不再需要並包含 Azure 訂閱相關的機密資訊。

### <a name="create-an-app-service-web-app"></a>建立應用程式服務 web 應用程式

1. 從命令列，請在**工作清單**目錄變更目錄。

2. 若要建立新 web 應用程式中使用下列命令。

        azure site create --git

    系統會提示您的 web 應用程式的名稱和位置。 提供一個唯一的名稱，然後選取要作為 Azure 儲存體帳戶相同的地理位置。

    `--git`參數 Azure 上建立給存放庫此 web 應用程式。 如果不存在，並新增名為 「 azure 」，這用來發佈應用程式，以 Azure[給遠端]，它也會初始化給存放庫中目前的目錄。 最後，它會建立**web.config**檔，其中包含 Azure 用來裝載的節點應用程式的設定。 如果您省略`--git`參數的目錄，但是包含給存放庫、 命令將仍建立 'azure' 遠端。

    完成這個命令後，您會看到類似以下的輸出。 請注意的開始**在建立網站**包含 web 應用程式的 URL。

        info:   Executing command site create
        help:   Need a site name
        Name: TableTasklist
        info:   Using location southcentraluswebspace
        info:   Executing `git init`
        info:   Creating default .gitignore file
        info:   Creating a new web site
        info:   Created web site at  tabletasklist.azurewebsites.net
        info:   Initializing repository
        info:   Repository initialized
        info:   Executing `git remote add azure https://username@tabletasklist.azurewebsites.net/TableTasklist.git`
        info:   site create command OK

    > [AZURE.NOTE] 如果這是您的訂閱的第一個應用程式服務 web 應用程式，您也需要建立 web 應用程式使用 Azure 入口網站。 如需詳細資訊，請參閱[建立及部署 Node.js web 應用程式中 Azure 應用程式服務]。

### <a name="set-environment-variables"></a>設定環境變數

在此步驟中，您將加入環境變數 Azure 在 web 應用程式設定。
從命令列中，輸入下列資訊︰

    azure site appsetting add
        STORAGE_NAME=<storage account name>;STORAGE_KEY=<storage access key>;PARTITION_KEY=mytasks;TABLE_NAME=tasks


取代**<storage account name>**名稱的儲存空間帳戶您先前建立並取代**<storage access key>**使用您儲存的帳戶的主要便捷鍵。 （您先前建立的 config.json 檔案使用相同的值）。

或者，您可以設定環境變數中[Azure 入口網站](https://portal.azure.com/)︰

1.  按一下 [**瀏覽]**來開啟 web 應用程式的刀 > **Web 應用程式**> web 應用程式名稱。

1.  在 web 應用程式的刀，按一下 [**所有設定** > **應用程式設定**。

    <!-- ![Top Menu](./media/storage-nodejs-use-table-storage-web-site/PollsCommonWebSiteTopMenu.png) -->

1.  捲動到 [**應用程式設定**] 區段，並新增金鑰/值組。

    ![應用程式設定](./media/storage-nodejs-use-table-storage-web-site/storage-tasks-appsettings.png)

1. 按一下 [**儲存**]。


### <a name="publish-the-application"></a>發佈應用程式

若要發佈應用程式，認可給的程式碼檔案，然後推入 azure/母片。

1. 設定您的部署認證。

        azure site deployment user set <name> <password>

2. 新增，並確認您的應用程式的檔案。

        git add .
        git commit -m "adding files"

3. 發送認可應用程式服務 web 應用程式︰

        git push azure master

    使用**母片**做為目標分支。 結尾的部署，您會看到類似以下的範例的陳述式︰

        To https://username@tabletasklist.azurewebsites.net/TableTasklist.git
         * [new branch]      master -> master

4. 完成推入作業後，請瀏覽至先前所傳回的 web 應用程式 URL`azure create site`命令，以檢視您的應用程式。


## <a name="next-steps"></a>後續步驟

雖然這份文件中的步驟說明使用表格服務儲存資訊，您也可以使用[MongoDB](https://mlab.com/azure/)。 

## <a name="additional-resources"></a>其他資源

[Azure CLI]

## <a name="whats-changed"></a>變更的項目
* 若要變更的指南，從網站應用程式服務請參閱︰ [Azure 應用程式服務與程式影響現有 Azure 服務](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- URLs -->

[建立並部署 Node.js web app 中 Azure 應用程式服務]: web-sites-nodejs-develop-deploy-mac.md
[Azure Developer Center]: /develop/nodejs/

[節點]: http://nodejs.org
[給]: http://git-scm.com
[Express]: http://expressjs.com
[for free]: http://windowsazure.com
[給遠端]: http://git-scm.com/docs/git-remote

[Azure CLI]: ../xplat-cli-install.md

[azure]: https://github.com/Azure/azure-sdk-for-node
[節點 uuid]: https://www.npmjs.com/package/node-uuid
[nconf]: https://www.npmjs.com/package/nconf
[非同步]: https://www.npmjs.com/package/async

[Azure Portal]: https://portal.azure.com

[Create and deploy a Node.js application to an Azure Web Site]: web-sites-nodejs-develop-deploy-mac.md
 
<!-- Image References -->

[node-table-finished]: ./media/storage-nodejs-use-table-storage-web-site/table_todo_empty.png
[node-table-list-items]: ./media/storage-nodejs-use-table-storage-web-site/table_todo_list.png
[download-publishing-settings]: ./media/storage-nodejs-use-table-storage-web-site/azure-account-download-cli.png
[portal-new]: ./media/storage-nodejs-use-table-storage-web-site/plus-new.png
[portal-storage-account]: ./media/storage-nodejs-use-table-storage-web-site/new-storage.png
[portal-quick-create-storage]: ./media/storage-nodejs-use-table-storage-web-site/quick-storage.png
[portal-storage-access-keys]: ./media/storage-nodejs-use-table-storage-web-site/manage-access-keys.png
[go-to-dashboard]: ./media/storage-nodejs-use-table-storage-web-site/go_to_dashboard.png
[web-configure]: ./media/storage-nodejs-use-table-storage-web-site/sql-task-configure.png
[app-settings-save]: ./media/storage-nodejs-use-table-storage-web-site/savebutton.png
[app-settings]: ./media/storage-nodejs-use-table-storage-web-site/storage-tasks-appsettings.png
