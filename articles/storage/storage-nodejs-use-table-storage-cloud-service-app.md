<properties 
    pageTitle="資料表儲存體 (Node.js) web 應用程式 |Microsoft Azure" 
    description="教學課程，藉由新增 Azure 儲存體服務和 Azure 模組是根據快速教學課程 Web 應用程式。" 
    services="cloud-services, storage" 
    documentationCenter="nodejs" 
    authors="tamram" 
    manager="carmonm" 
    editor="tysonn"/>

<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="robmcm"/>

# <a name="nodejs-web-application-using-storage"></a>使用儲存空間的 Node.js Web 應用程式

## <a name="overview"></a>概觀

在本教學課程中，您就可以擴充[Node.js Web 應用程式使用快速]教學課程中建立與資料管理服務搭配使用 Node.js Microsoft Azure 用戶端程式庫的應用程式。 您就可以擴充您的應用程式建立的 web 式工作清單應用程式，您可以將 Azure 部署。 工作清單可讓使用者以擷取工作、 新增新的工作，並將工作標示為已完成。

工作項目會儲存在 Azure 儲存體。 Azure 儲存體提供的容錯和高度可用的非結構化的資料儲存區。 Azure 儲存包含數個位置儲存和存取資料，以及您可以使用從 Api Node.js 或 REST Api 透過 Azure SDK 中所包含的儲存空間服務的資料結構。 如需詳細資訊，請參閱[Storing 和存取 Azure 中的資料]。

本教學課程，假設您已完成的[Node.js Web 應用程式]，[快速與 Node.js][Node.js Web 應用程式使用快速]教學課程。

您將瞭解︰

-   如何使用碧眼範本引擎
-   如何使用 Azure 資料管理服務

下方，為已完成的應用程式的螢幕擷取畫面︰

![在 internet explorer 中已完成的網頁](./media/storage-nodejs-use-table-storage-cloud-service-app/getting-started-1.png)

## <a name="setting-storage-credentials-in-webconfig"></a>設定 Web.Config 儲存的認證

若要存取 Azure 儲存體，必須先通過認證儲存的認證。 若要這麼做，您可以利用 web.config 應用程式設定。
這些設定會傳遞為環境變數節點，這 Azure SDK 然後閱讀。

> [AZURE.NOTE] 應用程式部署至 Azure 時，只會使用儲存認證。 在模擬器執行時，應用程式會使用儲存空間模擬器。

若要擷取的儲存空間帳戶認證，並將其新增至 web.config 設定下列步驟執行︰

1.  如果尚未開啟，PowerShell 的 Azure **[開始**] 功能表上，以啟動展開**所有程式]、 Azure**、 **PowerShell 的 Azure**，以滑鼠右鍵按一下，然後選取**以系統管理員身分執行**。

2.  變更目錄来包含您的應用程式的資料夾。 例如，c:\\節點\\工作清單\\WebRole1。

3.  從 Azure] 視窗中，輸入下列 cmdlet 來擷取儲存帳戶資訊︰

        PS C:\node\tasklist\WebRole1> Get-AzureStorageAccounts

    這會擷取儲存帳戶和帳戶金鑰裝載的服務相關聯的清單。

    > [AZURE.NOTE] Azure SDK 會建立儲存帳戶，當您部署服務時，從部署中的上一個輔助線應用程式應該已存在於儲存帳戶。

4.  開啟包含環境設定應用程式部署到 Azure 時所使用的**ServiceDefinition.csdef**檔案︰

        PS C:\node\tasklist> notepad ServiceDefinition.csdef

5.  插入下列區塊下**環境**項目，以取代 {儲存帳戶}，{儲存便捷鍵} 帳戶名稱與您想要用於部署儲存帳戶的主索引鍵︰

        <Variable name="AZURE_STORAGE_ACCOUNT" value="{STORAGE ACCOUNT}" />
        <Variable name="AZURE_STORAGE_ACCESS_KEY" value="{STORAGE ACCESS KEY}" />

    ![Web.cloud.config 檔案內容](./media/storage-nodejs-use-table-storage-cloud-service-app/node37.png)

6.  將檔案儲存並關閉 [記事本]。

### <a name="install-additional-modules"></a>安裝的其他模組

2. 安裝 [azure]，請使用下列命令 [節點-uuid]，[nconf] 和 [非同步] 模組本機一併儲存的項目， **package.json**檔案︰

        PS C:\node\tasklist\WebRole1> npm install azure-storage node-uuid async nconf --save

    這個命令的輸出應如下︰

        node-uuid@1.4.1 node_modules\node-uuid

        nconf@0.6.9 node_modules\nconf
        ├── ini@1.1.0
        ├── async@0.2.9
        └── optimist@0.6.0 (wordwrap@0.0.2, minimist@0.0.8)

        azure-storage@0.1.0 node_modules\azure-storage
        ├── extend@1.2.1
        ├── xmlbuilder@0.4.3
        ├── mime@1.2.11
        ├── underscore@1.4.4
        ├── validator@3.1.0
        ├── node-uuid@1.4.1
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)

##<a name="using-the-table-service-in-a-node-application"></a>在節點應用程式中使用表格服務

此區段中，您會擴充基本藉由新增**task.js**檔案所在的模型為任務建立的**快速**命令的應用程式。 您也可以修改現有的**app.js**然後建立新的**tasklist.js**檔案可使用模型。

### <a name="create-the-model"></a>建立模型

1. 在**WebRole1**目錄中，建立新名為目錄**模型**。

2. 在**模型**目錄中，建立名為**task.js**的新檔案。 這個檔案會包含您的應用程式所建立的工作的模型。

3. 開頭的**task.js**檔案時，新增下列程式碼，參照所需的文件庫︰

        var azure = require('azure-storage');
        var uuid = require('node-uuid');
        var entityGen = azure.TableUtilities.entityGenerator;

4. 接下來，您將加入定義和匯出工作物件的程式碼。 此物件負責連線至資料表。

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

5. 接下來，新增下列程式碼，定義任務，在物件上的其他方法可讓儲存在資料表中的資料互動︰

        Task.prototype = {
          find: function(query, callback) {
            self = this;
            self.storageClient.queryEntities(query, function entitiesQueried(error, result) {
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

### <a name="create-the-controller"></a>建立控制器

1. 在**WebRole1/路由**目錄中，建立名為**tasklist.js**的新檔案，然後在文字編輯器中開啟。

2. 將下列程式碼新增至 [ **tasklist.js**。 這會載入 azure 和非同步模組，可使用**tasklist.js**。 這也定義**工作清單**，該函數會傳遞我們先前定義的 [**工作**] 物件的執行個體︰

        var azure = require('azure-storage');
        var async = require('async');

        module.exports = TaskList;

        function TaskList(task) {
          this.task = task;
        }

2. 繼續新增**tasklist.js**檔案來新增**showTasks** **addTask**，與**completeTasks**所使用的方法︰

        TaskList.prototype = {
          showTasks: function(req, res) {
            self = this;
            var query = azure.TableQuery()
              .where('completed eq ?', false);
            self.task.find(query, function itemsFound(error, items) {
              res.render('index',{title: 'My ToDo List ', tasks: items});
            });
          },

          addTask: function(req,res) {
            var self = this      
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

3. 儲存**tasklist.js**檔案。

### <a name="modify-appjs"></a>修改 app.js

1. 在**WebRole1**目錄中，開啟文字編輯器中的**app.js**檔案。 

2. 開頭的檔案時，新增下列載入 azure 模組，並設定資料表的名稱和分割索引鍵︰

        var azure = require('azure-storage');
        var tableName = 'tasks';
        var partitionKey = 'hometasks';

3. 在 app.js 檔案中，捲動至您在這裡看見的下列行︰

        app.use('/', routes);
        app.use('/users', users);

    上述各行取代下方所顯示的程式碼。 這會初始化<strong>工作</strong>的執行個體以連線至您儲存的帳戶。 傳遞給<strong>工作清單</strong>，其中會使用它來進行通訊表格服務︰

        var TaskList = require('./routes/tasklist');
        var Task = require('./models/task');
        var task = new Task(azure.createTableService(), tableName, partitionKey);
        var taskList = new TaskList(task);

        app.get('/', taskList.showTasks.bind(taskList));
        app.post('/addtask', taskList.addTask.bind(taskList));
        app.post('/completetask', taskList.completeTask.bind(taskList));
    
4. 儲存**app.js**檔案。

### <a name="modify-the-index-view"></a>修改 [索引] 檢視

1. 變更目錄**檢視**的目錄，文字編輯器中開啟**index.jade**檔案。

2. 取代以下程式碼**index.jade**檔案的內容。 定義的檢視顯示現有的任務，以及新增工作與標示為已完成的現有的表單。

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
              if tasks != []
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

**檢視**的目錄中的**layout.jade**檔案用於其他**.jade**檔案另存為通用範本。 您會在此步驟中修改，使用[Twitter 啟動安裝程式](https://github.com/twbs/bootstrap)，這是工具組，使其易於設計部份的外觀網站。

1. 下載並解壓縮的[Twitter 啟動安裝程式](http://getbootstrap.com/)的檔案。 複製**bootstrap.min.css**檔案從**啟動安裝\\分散式\\css**資料夾**公用\\樣式表**應用程式工作清單的目錄。

2. [**檢視**] 資料夾中，在文字編輯器中開啟**layout.jade** ，並以下列取代內容︰

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

3. 儲存**layout.jade**檔案。

### <a name="running-the-application-in-the-emulator"></a>在模擬器中執行應用程式

使用下列命令來啟動應用程式在模擬器中。

    PS C:\node\tasklist\WebRole1> start-azureemulator -launch

在瀏覽器會開啟，並且顯示下列頁面︰

![網頁標題為 「 」 我的工作清單包含工作及欄位，以新增工作的表格。](./media/storage-nodejs-use-table-storage-cloud-service-app/node44.png)

使用表單新增項目，或移除現有項目以將其標示為已完成。

## <a name="publishing-the-application-to-azure"></a>發佈 Azure 應用程式


在 Windows PowerShell 視窗，來電下列 cmdlet 重新部署 Azure 您裝載的服務。

    PS C:\node\tasklist\WebRole1> Publish-AzureServiceProject -name myuniquename -location datacentername -launch

取代**myuniquename**此應用程式的唯一名稱。 取代**datacentername** Azure 資料中心，例如**西適用於美國**的名稱。

部署完成之後，您應該會看到類似以下的回應︰

    PS C:\node\tasklist> publish-azureserviceproject -servicename tasklist -location "West US"
    WARNING: Publishing tasklist to Microsoft Azure. This may take several minutes...
    WARNING: 2:18:42 PM - Preparing runtime deployment for service 'tasklist'
    WARNING: 2:18:42 PM - Verifying storage account 'tasklist'...
    WARNING: 2:18:43 PM - Preparing deployment for tasklist with Subscription ID: 65a1016d-0f67-45d2-b838-b8f373d6d52e...
    WARNING: 2:19:01 PM - Connecting...
    WARNING: 2:19:02 PM - Uploading Package to storage service larrystore...
    WARNING: 2:19:40 PM - Upgrading...
    WARNING: 2:22:48 PM - Created Deployment ID: b7134ab29b1249ff84ada2bd157f296a.
    WARNING: 2:22:48 PM - Initializing...
    WARNING: 2:22:49 PM - Instance WebRole1_IN_0 of role WebRole1 is ready.
    WARNING: 2:22:50 PM - Created Website URL: http://tasklist.cloudapp.net/.

為之前，因為您指定**-啟動**選項，在瀏覽器會開啟，並顯示完成發佈 Azure 中執行的應用程式。

![在瀏覽器視窗，顯示 [我的工作清單] 頁面。 URL 指出 Azure 現在會裝載的頁面。](./media/storage-nodejs-use-table-storage-cloud-service-app/getting-started-1.png)

## <a name="stopping-and-deleting-your-application"></a>停止，以及刪除您的應用程式

在部署您的應用程式之後，您可能要停用它，讓您避免成本或建立並部署免費的試用期間內的其他應用程式。

Azure 帳單 web 每小時的伺服器時間耗用的角色執行個體。
伺服器被使用部署您的應用程式，即使未在執行執行個體，而且會停止的狀態。

下列步驟將告訴您如何停止和刪除您的應用程式。

1.  在 Windows PowerShell 視窗中，停止服務部署建立前一節使用下列 cmdlet:

        PS C:\node\tasklist\WebRole1> Stop-AzureService

    停止服務，可能需要幾分鐘的時間。 停止服務時，您會收到訊息指出它已停止。

3.  若要刪除的服務，請連絡下列 cmdlet:

        PS C:\node\tasklist\WebRole1> Remove-AzureService contosotasklist

    出現提示時，請輸入**Y**刪除服務。

    刪除服務，可能需要幾分鐘的時間。 刪除服務之後您收到訊息，指出服務已遭刪除。

  [使用快速 Node.js Web 應用程式]: http://azure.microsoft.com/develop/nodejs/tutorials/web-app-with-express/
  [儲存和存取 Azure 中的資料]: http://msdn.microsoft.com/library/azure/gg433040.aspx
  [Node.js Web 應用程式]: http://azure.microsoft.com/develop/nodejs/tutorials/getting-started/
 
 
