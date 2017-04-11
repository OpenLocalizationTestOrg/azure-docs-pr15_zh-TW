<properties 
    pageTitle="瞭解 Node.js-DocumentDB Node.js 教學課程 |Microsoft Azure" 
    description="瞭解 Node.js ！ 教學課程-從裝載於 Azure 網站 Node.js Express web 應用程式中探索如何使用 Microsoft Azure DocumentDB 來儲存和存取資料。" 
    keywords="應用程式開發、 資料庫教學課程，瞭解 node.js node.js 教學課程，documentdb、 azure、 Microsoft azure"
    services="documentdb" 
    documentationCenter="nodejs" 
    authors="syamkmsft" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="hero-article" 
    ms.date="08/25/2016" 
    ms.author="syamk"/>

# <a name="_Toc395783175"></a>建立使用 DocumentDB Node.js web 應用程式

> [AZURE.SELECTOR]
- [.NET](documentdb-dotnet-application.md)
- [Node.js](documentdb-nodejs-application.md)
- [Java](documentdb-java-application.md)
- [Python](documentdb-python-application.md)

本 Node.js 教學課程中會顯示您如何使用 Azure DocumentDB 服務來儲存和存取裝載於 Azure 網站 Node.js Express 應用程式的資料。

建議觀看以下影片，您會在此瞭解如何佈建 Azure DocumentDB 資料庫帳戶和 JSON 文件儲存在 Node.js 應用程式中快速入門。 

> [AZURE.VIDEO azure-demo-getting-started-with-azure-documentdb-on-nodejs-in-linux]

然後回到這個 Node.js 教學課程中，您會在此瞭解下列問題的答案︰

- 如何搭配使用 documentdb npm 模組 DocumentDB？
- 如何將部署 Azure 網站 web 應用程式？

依照本教學課程中資料庫，您會建立簡單 web 式工作管理應用程式，可建立、 擷取及完成的工作。 工作會儲存為 Azure DocumentDB JSON 文件。

![此 Node.js 教學課程中建立的 「 我的待辦清單應用程式的螢幕擷取畫面](./media/documentdb-nodejs-application/image1.png)

不會有的時間才能完成教學課程，而且只是要取得完整的解決方案嗎？ 不是問題，您可以從[GitHub][]取得完整的範例解決方案。

## <a name="_Toc395783176"></a>必要條件

> [AZURE.TIP] 本教學課程中 Node.js 假設您已使用 Node.js 和 Azure 網站一些先前曾。

您應該之前遵循本文中的指示，請確定您有下列︰

- 使用中的 Azure 帳戶。 如果您沒有帳戶，您可以建立的免費的試用帳戶在幾分鐘。 如需詳細資訊，請參閱[Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/)。
- [Node.js][]版本 v0.10.29 或更新版本。
- [Express 產生器](http://www.expressjs.com/starter/generator.html)(您可以在此透過安裝`npm install express-generator -g`)
- [給][]。

## <a name="_Toc395637761"></a>步驟 1︰ 建立 DocumentDB 資料庫帳戶

現在就讓我們開始建立 DocumentDB 帳戶。 如果您已經有帳戶，您可以跳到[步驟 2︰ 建立新的 Node.js 應用程式](#_Toc395783178)。

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

[AZURE.INCLUDE [documentdb-keys](../../includes/documentdb-keys.md)]

## <a name="_Toc395783178"></a>步驟 2︰ 瞭解如何建立新的 Node.js 應用程式

現在讓我們來瞭解如何建立基本的 Hello 全球 Node.js 專案使用[Express](http://expressjs.com/)架構。

1. 開啟您最愛的終端機。

2. 使用快速產生器產生新的應用程式稱為**todo**。

        express todo

3. 開啟新的**todo**目錄，並安裝相依性。

        cd todo
        npm install

4. 執行新應用程式。

        npm start

5. 您可以藉由導覽[http://localhost:3000](http://localhost:3000)瀏覽器中檢視新應用程式。

    ![瞭解 Node.js-瀏覽器視窗中的什麼應用程式的螢幕擷取畫面](./media/documentdb-nodejs-application/image12.png)

## <a name="_Toc395783179"></a>步驟 3︰ 安裝其他模組

**Package.json**檔案是在專案的根目錄中建立的檔案。 這個檔案包含其他模組所需 Node.js 應用程式的清單。 更新版本中，當您部署 Azure 網站這個應用程式時，此檔案來判斷哪些模組需要安裝在 Azure 支援您的應用程式。 我們仍需要在此教學課程安裝兩個更多套件。

1. 在 terminal 中，安裝**非同步**模組透過 npm。

        npm install async --save

1. 安裝透過 npm **documentdb**模組。 這是的模組 DocumentDB 個神奇發生的位置。

        npm install documentdb --save

3. 快速檢查**package.json**檔案的應用程式應該會顯示其他的模組。 此檔案會告訴 Azure 哪些下載並安裝時執行您的應用程式套件。 它看起來應該像下面這個範例。

    ![[Package.json] 索引標籤的螢幕擷取畫面](./media/documentdb-nodejs-application/image17.png)

    這會告訴節點 （及更新版本 Azure），您的應用程式而定這些額外的模組。

## <a name="_Toc395783180"></a>步驟 4︰ 在節點應用程式中使用 DocumentDB 服務

處理所有的初始安裝和設定，現在讓我們來取得向下至為什麼我們可以在這裡，也就是一些使用撰寫程式碼 Azure DocumentDB。

### <a name="create-the-model"></a>建立模型

1. 在專案目錄，建立新名為**模型**的目錄。
2. 在**模型**目錄中，建立名為**taskDao.js**的新檔案。 這個檔案會包含的模型，我們的應用程式所建立的工作。
3. 在相同的**模型**目錄中，建立另一個名為**docdbUtils.js**的新檔案。 這個檔案會包含在我們的應用程式，我們將使用一些實用、 可重複使用，程式碼。 
4. 將下列程式碼中的複製到**docdbUtils.js**

        var DocumentDBClient = require('documentdb').DocumentClient;
            
        var DocDBUtils = {
            getOrCreateDatabase: function (client, databaseId, callback) {
                var querySpec = {
                    query: 'SELECT * FROM root r WHERE r.id= @id',
                    parameters: [{
                        name: '@id',
                        value: databaseId
                    }]
                };
        
                client.queryDatabases(querySpec).toArray(function (err, results) {
                    if (err) {
                        callback(err);
        
                    } else {
                        if (results.length === 0) {
                            var databaseSpec = {
                                id: databaseId
                            };
        
                            client.createDatabase(databaseSpec, function (err, created) {
                                callback(null, created);
                            });
        
                        } else {
                            callback(null, results[0]);
                        }
                    }
                });
            },
        
            getOrCreateCollection: function (client, databaseLink, collectionId, callback) {
                var querySpec = {
                    query: 'SELECT * FROM root r WHERE r.id=@id',
                    parameters: [{
                        name: '@id',
                        value: collectionId
                    }]
                };             
                
                client.queryCollections(databaseLink, querySpec).toArray(function (err, results) {
                    if (err) {
                        callback(err);
        
                    } else {        
                        if (results.length === 0) {
                            var collectionSpec = {
                                id: collectionId
                            };
                            
                            client.createCollection(databaseLink, collectionSpec, function (err, created) {
                                callback(null, created);
                            });
        
                        } else {
                            callback(null, results[0]);
                        }
                    }
                });
            }
        };
                
        module.exports = DocDBUtils;

    > [AZURE.TIP] createCollection 採用可用來指定集合提供類型的選用 requestOptions 參數。 如果沒有 requestOptions.offerType 值提供然後集合會使用預設提供類型來建立。
    >
    > DocumentDB 提供類型的詳細資訊請參閱[在 DocumentDB 效能](documentdb-performance-levels.md)層級 
        
3. 儲存並關閉**docdbUtils.js**檔案。

4. 開頭的**taskDao.js**檔案時，新增下列**DocumentDBClient**和我們先前所建立的**docdbUtils.js**參照︰

        var DocumentDBClient = require('documentdb').DocumentClient;
        var docdbUtils = require('./docdbUtils');

4. 接下來，您將加入定義和匯出工作物件的程式碼。 這是負責初始化我們工作物件及設定，我們將使用的資料庫及文件集合。

        function TaskDao(documentDBClient, databaseId, collectionId) {
          this.client = documentDBClient;
          this.databaseId = databaseId;
          this.collectionId = collectionId;
        
          this.database = null;
          this.collection = null;
        }
        
        module.exports = TaskDao;

5. 接下來，新增下列程式碼，定義任務，在物件上的其他方法可允許與 DocumentDB 中儲存的資料。

        TaskDao.prototype = {
            init: function (callback) {
                var self = this;
        
                docdbUtils.getOrCreateDatabase(self.client, self.databaseId, function (err, db) {
                    if (err) {
                        callback(err);
                    } else {
                        self.database = db;
                        docdbUtils.getOrCreateCollection(self.client, self.database._self, self.collectionId, function (err, coll) {
                            if (err) {
                                callback(err);
        
                            } else {
                                self.collection = coll;
                            }
                        });
                    }
                });
            },
        
            find: function (querySpec, callback) {
                var self = this;
        
                self.client.queryDocuments(self.collection._self, querySpec).toArray(function (err, results) {
                    if (err) {
                        callback(err);
        
                    } else {
                        callback(null, results);
                    }
                });
            },
        
            addItem: function (item, callback) {
                var self = this;
        
                item.date = Date.now();
                item.completed = false;
        
                self.client.createDocument(self.collection._self, item, function (err, doc) {
                    if (err) {
                        callback(err);
        
                    } else {
                        callback(null, doc);
                    }
                });
            },
        
            updateItem: function (itemId, callback) {
                var self = this;
        
                self.getItem(itemId, function (err, doc) {
                    if (err) {
                        callback(err);
        
                    } else {
                        doc.completed = true;
        
                        self.client.replaceDocument(doc._self, doc, function (err, replaced) {
                            if (err) {
                                callback(err);
        
                            } else {
                                callback(null, replaced);
                            }
                        });
                    }
                });
            },
        
            getItem: function (itemId, callback) {
                var self = this;
        
                var querySpec = {
                    query: 'SELECT * FROM root r WHERE r.id = @id',
                    parameters: [{
                        name: '@id',
                        value: itemId
                    }]
                };
        
                self.client.queryDocuments(self.collection._self, querySpec).toArray(function (err, results) {
                    if (err) {
                        callback(err);
        
                    } else {
                        callback(null, results[0]);
                    }
                });
            }
        };

6. 儲存並關閉**taskDao.js**檔案。 

### <a name="create-the-controller"></a>建立控制器

1. 在您的專案**路由**目錄中，建立名為**tasklist.js**的新檔案。 
2. 將下列程式碼新增至 [ **tasklist.js**。 這會載入 DocumentDBClient 和非同步模組，可使用**tasklist.js**。 這也定義**工作清單**，該函數會傳遞我們先前定義的 [**工作**] 物件的執行個體︰

        var DocumentDBClient = require('documentdb').DocumentClient;
        var async = require('async');
        
        function TaskList(taskDao) {
          this.taskDao = taskDao;
        }
        
        module.exports = TaskList;

3. 繼續新增**tasklist.js**檔案來新增**showTasks、 addTask**，及**completeTasks**的方法︰
        
        TaskList.prototype = {
            showTasks: function (req, res) {
                var self = this;
        
                var querySpec = {
                    query: 'SELECT * FROM root r WHERE r.completed=@completed',
                    parameters: [{
                        name: '@completed',
                        value: false
                    }]
                };
        
                self.taskDao.find(querySpec, function (err, items) {
                    if (err) {
                        throw (err);
                    }
        
                    res.render('index', {
                        title: 'My ToDo List ',
                        tasks: items
                    });
                });
            },
        
            addTask: function (req, res) {
                var self = this;
                var item = req.body;
        
                self.taskDao.addItem(item, function (err) {
                    if (err) {
                        throw (err);
                    }
        
                    res.redirect('/');
                });
            },
        
            completeTask: function (req, res) {
                var self = this;
                var completedTasks = Object.keys(req.body);
        
                async.forEach(completedTasks, function taskIterator(completedTask, callback) {
                    self.taskDao.updateItem(completedTask, function (err) {
                        if (err) {
                            callback(err);
                        } else {
                            callback(null);
                        }
                    });
                }, function goHome(err) {
                    if (err) {
                        throw err;
                    } else {
                        res.redirect('/');
                    }
                });
            }
        };


4. 儲存並關閉**tasklist.js**檔案。
 
### <a name="add-configjs"></a>新增 config.js

1. 在您的專案目錄中建立名為**config.js**的新檔案。
2. 將下列文字新增至**config.js**。 這會定義設定的設定和我們的應用程式所需的值。

        var config = {}
        
        config.host = process.env.HOST || "[the URI value from the DocumentDB Keys blade on http://portal.azure.com]";
        config.authKey = process.env.AUTH_KEY || "[the PRIMARY KEY value from the DocumentDB Keys blade on http://portal.azure.com]";
        config.databaseId = "ToDoList";
        config.collectionId = "Items";
        
        module.exports = config;

3. 在**config.js**檔案，更新主機和 AUTH_KEY 使用 DocumentDB 帳戶[Microsoft Azure 入口網站](https://portal.azure.com)上的按鍵刀中找到的值的值︰

4. 儲存並關閉**config.js**檔案。
 
### <a name="modify-appjs"></a>修改 app.js

1. 在 [專案] 目錄中，開啟**app.js**檔案。 建立快速 web 應用程式時，此檔案是較舊版本建立。
2. 移至**app.js**頂端新增下列程式碼
    
        var DocumentDBClient = require('documentdb').DocumentClient;
        var config = require('./config');
        var TaskList = require('./routes/tasklist');
        var TaskDao = require('./models/taskDao');

3. 將此程式碼定義要使用的設定檔，然後繼續閱讀登出推出，我們將使用一些變數此檔案中的值。
4. 取代**app.js**檔案中的下列兩行︰

        app.use('/', routes);
        app.use('/users', users); 

      使用下列的程式碼片段︰

        var docDbClient = new DocumentDBClient(config.host, {
            masterKey: config.authKey
        });
        var taskDao = new TaskDao(docDbClient, config.databaseId, config.collectionId);
        var taskList = new TaskList(taskDao);
        taskDao.init();
        
        app.get('/', taskList.showTasks.bind(taskList));
        app.post('/addtask', taskList.addTask.bind(taskList));
        app.post('/completetask', taskList.completeTask.bind(taskList));
        app.set('view engine', 'jade');



6. 這些線條定義新物件的執行個體我們**TaskDao** 、 DocumentDB （使用讀取**config.js**的值） 的新連線，與初始化工作物件，然後繫結表單動作，我們的**工作清單**控制器上的方法。 

7. 最後，儲存並關閉**app.js**檔案，我們幾乎完成。
 
## <a name="_Toc395783181"></a>步驟 5︰ 建立使用者介面

現在讓我們來開啟 [建立使用者介面，使用者可實際互動我們的應用程式，讓我們注意。 為檢視引擎會使用**碧眼**我們所建立的快速應用程式。 如碧眼詳細資訊，請參閱[http://jade-lang.com/](http://jade-lang.com/)。

1. **檢視**的目錄中的**layout.jade**檔案用於其他**.jade**檔案另存為通用範本。 您會在此步驟中修改，使用[Twitter 啟動安裝程式](https://github.com/twbs/bootstrap)，這是工具組，使其易於設計部份的外觀網站。 
2. 開啟**layout.jade**檔案在 [**檢視**] 資料夾中找到內容取代動作︰
    
        doctype html
        html
          head
            title= title
            link(rel='stylesheet', href='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/css/bootstrap.min.css')
            link(rel='stylesheet', href='/stylesheets/style.css')
          body
            nav.navbar.navbar-inverse.navbar-fixed-top
              div.navbar-header
                a.navbar-brand(href='#') My Tasks
            block content
            script(src='//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.2.min.js')
            script(src='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/bootstrap.min.js')



    這有效會告訴轉譯我們應用程式的一些 HTML**碧眼**引擎，並建立**區塊**一部分**的內容**，我們可以在其中提供我們內容頁面的版面配置。
    儲存並關閉此**layout.jade**檔案。

4. 現在開啟**index.jade**檔案時，會使用我們的應用程式，並以下列取代檔案內容的檢視︰

        extends layout
        
        block content
          h1 #{title}
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
                    td #{task.name}
                    td #{task.category}
                    - var date  = new Date(task.date);
                    - var day   = date.getDate();
                    - var month = date.getMonth() + 1;
                    - var year  = date.getFullYear();
                    td #{month + "/" + day + "/" + year}
                    td
                      input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
            button.btn(type="submit") Update tasks
          hr
          form.well(action="/addtask", method="post")
            label Item Name:
            input(name="name", type="textbox")
            label Item Category:
            input(name="category", type="textbox")
            br
            button.btn(type="submit") Add item

    這將延伸版面配置]，並提供我們先前所見**layout.jade**檔案中之**內容**版面配置區的內容。
    
    在此版面配置中，我們建立兩個 HTML 表單。 
    第一份表單中包含的表格資料，讓我們來張貼到**/completetask**方法我們控制站的更新項目] 按鈕。
    第二個表單中包含兩個輸入的欄位和] 按鈕，以讓我們來建立新的項目張貼至**/addtask**我們控制站的方法。
    
    應該是所有需要的應用程式搭配使用。

5. 開啟**style.css**檔案**public\stylesheets**目錄中，並以下列取代的程式碼︰

        body {
          padding: 50px;
          font: 14px "Lucida Grande", Helvetica, Arial, sans-serif;
        }
        a {
          color: #00B7FF;
        }
        .well label {
          display: block;
        }
        .well input {
          margin-bottom: 5px;
        }
        .btn {
          margin-top: 5px;
          border: outset 1px #C8C8C8;
        }

    儲存並關閉此**style.css**檔案。

## <a name="_Toc395783181"></a>步驟 6︰ 在本機上執行應用程式

1. 若要測試您的本機電腦上的應用程式，請執行`npm start`在 terminal 中，若要啟動您的應用程式，並啟動看起來像下方圖像的網頁瀏覽器︰

    ![在瀏覽器視窗中的 MyTodo 清單應用程式的螢幕擷取畫面](./media/documentdb-nodejs-application/image18.png)


2. 輸入資訊，請使用提供的項目、 項目名稱和類別欄位，然後按一下 [**新增項目**。

3. 頁面應該更新待辦清單中顯示新建立的項目。

    ![使用新的項目待辦清單中的應用程式的螢幕擷取畫面](./media/documentdb-nodejs-application/image19.png)

4. 若要完成工作，只要核取 [完成] 欄中的核取方塊，然後按一下**更新的任務**。

## <a name="_Toc395783182"></a>步驟 7︰ 將您的應用程式開發專案部署至 Azure 網站

1. 如果您未，啟用給存放庫 Azure 網站。 如何執行此動作[Azure 應用程式服務的本機給部署](../app-service-web/app-service-deploy-local-git.md)主題中，您可以找到相關指示。

2. 新增給遠端 Azure 網站。

        git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git

3. 部署至遠端推入。

        git push azure master

4. 幾秒數，就可以給會完成發佈 web 應用程式，並啟動瀏覽器，您可以在這裡看見您方便 Azure 中執行的工作 ！

## <a name="_Toc395637775"></a>後續步驟

恭喜您 ！ 您只要有內建的第一個 Node.js Express Web 應用程式使用 Azure DocumentDB，並將其發佈至 Azure 網站。

可透過[GitHub][]下載完整參考應用程式的程式碼。

如需詳細資訊，請參閱[Node.js 開發人員中心](https://azure.microsoft.com/develop/nodejs/)。

[Node.js]: http://nodejs.org/
[給]: http://git-scm.com/
[Github]: https://github.com/Azure-Samples/documentdb-node-todo-app
 
