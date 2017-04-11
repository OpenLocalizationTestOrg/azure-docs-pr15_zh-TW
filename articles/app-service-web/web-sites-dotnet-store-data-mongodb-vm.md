<properties 
    pageTitle="連線到 MongoDB 虛擬機器上執行的 Azure 中建立 web 應用程式" 
    description="教學課程會指導您如何使用給 Azure 的應用程式服務部署 ASP.NET 應用程式，連線到 MongoDB Azure 虛擬機器上。"
    tags="azure-portal" 
    services="app-service\web, virtual-machines" 
    documentationCenter=".net" 
    authors="cephalin" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="02/29/2016" 
    ms.author="cephalin"/>


# <a name="create-a-web-app-in-azure-that-connects-to-mongodb-running-on-a-virtual-machine"></a>連線到 MongoDB 虛擬機器上執行的 Azure 中建立 web 應用程式

您可以使用給，部署 Azure 應用程式服務 Web 應用程式 ASP.NET 應用程式。 在此教學課程中，您會建立簡單的前端 ASP.NET MVC 連接到 MongoDB 資料庫中 Azure 虛擬機器上執行的工作清單應用程式。  [MongoDB] [MongoDB]是熱門開啟來源、 高效能 NoSQL 資料庫。 執行，測試開發電腦上的 ASP.NET 應用程式，您將上傳應用程式服務 Web 應用程式使用給應用程式。

>[AZURE.NOTE] 如果您想要開始使用 Azure 應用程式服務註冊 Azure 帳戶之前，請移至[嘗試應用程式服務](http://go.microsoft.com/fwlink/?LinkId=523751)，可以讓您立即建立短暫入門 web 應用程式在應用程式服務。 必要; 沒有信用卡沒有承諾。


## <a name="background-knowledge"></a>背景知識 ##

在此教學課程，雖然非必要，下列知識庫很有用︰

* C# 驅動程式 MongoDB 中。 如需開發 C# 針對 MongoDB 應用程式的詳細資訊，請參閱 MongoDB[和 CSharp 語言中心][MongoC#LangCenter]。 
* ASP.NET web 應用程式架構。 您可以瞭解所有相關[ASP.net 網站]在[ASP.NET]。
* ASP.NET MVC web 應用程式架構。 您可以瞭解所有相關[ASP.NET MVC 網站]在[MVCWebSite]。
* Azure。 您可以開始在[Azure]閱讀[WindowsAzure]。

## <a name="prerequisites"></a>必要條件 ##

- [Visual Studio Express 2013 web]  [VSEWeb]或[Visual Studio 2013] [VSUlt]
- [Azure SDK.NET](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409)
- 作用中的 Microsoft Azure 訂閱

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

<a id="virtualmachine"></a> 
## <a name="create-a-virtual-machine-and-install-mongodb"></a>建立虛擬機器並安裝 MongoDB ##

本教學課程，假設您已在 Azure 虛擬機器。 建立虛擬機器之後，您需要安裝 MongoDB 虛擬機器上︰

* 若要建立 Windows 虛擬機器並安裝 MongoDB，請參閱[在執行 Windows Server 中 Azure 虛擬機器上的安裝 MongoDB][InstallMongoOnWindowsVM]。


您有建立 Azure 虛擬機器，安裝 MongoDB 之後，請務必記得 MongoDB 端點中所指定的虛擬機器 (「 testlinuxvm.cloudapp.net 」，例如) 和外部連接埠 DNS 名稱。  您必須稍後的教學課程此資訊。

<a id="createapp"></a>
## <a name="create-the-application"></a>建立應用程式 ##

此區段中建立 ASP.NET 應用程式使用 Visual Studio 中稱為 「 我的工作清單] 並執行初始部署至 Azure 應用程式服務 Web 應用程式。 會在應用程式，但它會連線到您的虛擬機器上 Azure 及那里使用您建立的 MongoDB 執行個體。

1. 在 Visual Studio 中，按一下 [**新專案**]。

    ![開始頁面新專案][StartPageNewProject]

1. 在 [**新專案**] 視窗的左窗格中，選取**Visual C#**，然後選取**網頁**。 在中間窗格中，選取 [ **ASP.NET Web 應用程式**。 在底部，命名 「 MyTaskListApp，「 專案，然後再按一下**[確定**]。

    ![新增專案] 對話方塊][NewProjectMyTaskListApp]

1. 在 [**新的 ASP.NET 專案**] 對話方塊中，選取**MVC**，，然後按一下**[確定]**。

    ![選取 MVC 範本][VS2013SelectMVCTemplate]

1. 如果您還沒有登入 Microsoft Azure，系統會提示您登入。 請依照提示登入 Azure。
2. 您的登入之後，您就可以開始設定您的應用程式服務 web 應用程式。 指定**Web 應用程式的名稱**、**應用程式服務計劃**、 [**資源] 群組**中和**區域**]，然後按一下 [**建立**]。

    ![](./media/web-sites-dotnet-store-data-mongodb-vm/VSConfigureWebAppSettings.png)

1. 專案建立完成後，等待**Azure 應用程式服務活動**視窗中的指示建立 Azure 應用程式服務中的 web 應用程式。 然後按一下 [**發佈 MyTaskListApp，現在此 Web 應用程式**]。

1. 按一下 [**發佈**]。

    ![](./media/web-sites-dotnet-store-data-mongodb-vm/VSPublishWeb.png)

    一旦您預設的 ASP.NET 應用程式發佈到 Azure 應用程式服務 Web 應用程式，它會在瀏覽器中啟動它。

## <a name="install-the-mongodb-c-driver"></a>安裝 MongoDB C# 驅動程式

MongoDB 提供用戶端支援 C# 透過驅動程式，您需要本機開發電腦上安裝應用程式。 C# 驅動程式是透過 NuGet。

若要安裝 MongoDB C# 驅動程式︰

1. 在**方案總管]**中，以滑鼠右鍵按一下**MyTaskListApp**專案，然後選取 [**管理 NuGetPackages**。

    ![管理 NuGet 套件][VS2013ManageNuGetPackages]

2. 在 [**管理 NuGet 套件**] 視窗的左窗格中，按一下 [**線上**]。 在 [在右側 [**線上搜尋**] 方塊中，輸入 「 mongodb.driver 」。  按一下 [安裝驅動程式的 [**安裝**]。

    ![搜尋 MongoDB C# 驅動程式][SearchforMongoDBCSharpDriver]

3. 按一下 [**我接受]**接受 10gen，包括授權條款。

4. 安裝驅動程式之後，請按一下 [**關閉**]。
    ![安裝 MongoDB C# 驅動程式][MongoDBCsharpDriverInstalled]


立即安裝 MongoDB C# 驅動程式。  **MongoDB.Bson** **MongoDB.Driver**，與**MongoDB.Driver.Core**文件庫的參照已新增至專案。

![MongoDB C# 驅動程式參照][MongoDBCSharpDriverReferences]

## <a name="add-a-model"></a>新增模型 ##
在**方案總管]**中，以滑鼠右鍵按一下 [*模型*] 資料夾中] 和 [**新增**新的**類別**，並將其命名為 [ *TaskModel.cs*。  在*TaskModel.cs*，取代現有的程式碼下列程式碼︰

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using MongoDB.Bson.Serialization.Attributes;
    using MongoDB.Bson.Serialization.IdGenerators;
    using MongoDB.Bson;
    
    namespace MyTaskListApp.Models
    {
        public class MyTask
        {
            [BsonId(IdGenerator = typeof(CombGuidGenerator))]
            public Guid Id { get; set; }
    
            [BsonElement("Name")]
            public string Name { get; set; }
    
            [BsonElement("Category")]
            public string Category { get; set; }
    
            [BsonElement("Date")]
            public DateTime Date { get; set; }
    
            [BsonElement("CreatedDate")]
            public DateTime CreatedDate { get; set; }
    
        }
    }

## <a name="add-the-data-access-layer"></a>新增資料的存取層級 ##
在**方案總管]**中，以滑鼠右鍵按一下 [ *MyTaskListApp*專案] 和 [**新增**名為 「 *DAL***新資料夾**。  以滑鼠右鍵按一下*DAL*資料夾並**新增**新的**類別**。 類別檔案*Dal.cs*名稱。  在*Dal.cs*，取代現有的程式碼下列程式碼︰

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using MyTaskListApp.Models;
    using MongoDB.Driver;
    using MongoDB.Bson;
    using System.Configuration;
    
    
    namespace MyTaskListApp
    {
        public class Dal : IDisposable
        {
            private MongoServer mongoServer = null;
            private bool disposed = false;
    
            // To do: update the connection string with the DNS name
            // or IP address of your server. 
            //For example, "mongodb://testlinux.cloudapp.net"
            private string connectionString = "mongodb://mongodbsrv20151211.cloudapp.net";
    
            // This sample uses a database named "Tasks" and a 
            //collection named "TasksList".  The database and collection 
            //will be automatically created if they don't already exist.
            private string dbName = "Tasks";
            private string collectionName = "TasksList";
    
            // Default constructor.        
            public Dal()
            {
            }
    
            // Gets all Task items from the MongoDB server.        
            public List<MyTask> GetAllTasks()
            {
                try
                {
                    var collection = GetTasksCollection();
                    return collection.Find(new BsonDocument()).ToList();
                }
                catch (MongoConnectionException)
                {
                    return new List<MyTask>();
                }
            }
    
            // Creates a Task and inserts it into the collection in MongoDB.
            public void CreateTask(MyTask task)
            {
                var collection = GetTasksCollectionForEdit();
                try
                {
                    collection.InsertOne(task);
                }
                catch (MongoCommandException ex)
                {
                    string msg = ex.Message;
                }
            }
    
            private IMongoCollection<MyTask> GetTasksCollection()
            {
                MongoClient client = new MongoClient(connectionString);
                var database = client.GetDatabase(dbName);
                var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                return todoTaskCollection;
            }
    
            private IMongoCollection<MyTask> GetTasksCollectionForEdit()
            {
                MongoClient client = new MongoClient(connectionString);
                var database = client.GetDatabase(dbName);
                var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                return todoTaskCollection;
            }
    
            # region IDisposable
    
            public void Dispose()
            {
                this.Dispose(true);
                GC.SuppressFinalize(this);
            }
    
            protected virtual void Dispose(bool disposing)
            {
                if (!this.disposed)
                {
                    if (disposing)
                    {
                        if (mongoServer != null)
                        {
                            this.mongoServer.Disconnect();
                        }
                    }
                }
    
                this.disposed = true;
            }
    
            # endregion
        }
    }

## <a name="add-a-controller"></a>新增控制器 ##
開啟*Controllers\HomeController.cs*檔案**總管**] 中，並以下列取代現有的程式碼︰

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Web.Mvc;
    using MyTaskListApp.Models;
    using System.Configuration;
    
    namespace MyTaskListApp.Controllers
    {
        public class HomeController : Controller, IDisposable
        {
            private Dal dal = new Dal();
            private bool disposed = false;
            //
            // GET: /MyTask/
    
            public ActionResult Index()
            {
                return View(dal.GetAllTasks());
            }
    
            //
            // GET: /MyTask/Create
    
            public ActionResult Create()
            {
                return View();
            }
    
            //
            // POST: /MyTask/Create
    
            [HttpPost]
            public ActionResult Create(MyTask task)
            {
                try
                {
                    dal.CreateTask(task);
                    return RedirectToAction("Index");
                }
                catch
                {
                    return View();
                }
            }
    
            public ActionResult About()
            {
                return View();
            }
    
            # region IDisposable
    
            new protected void Dispose()
            {
                this.Dispose(true);
                GC.SuppressFinalize(this);
            }
    
            new protected virtual void Dispose(bool disposing)
            {
                if (!this.disposed)
                {
                    if (disposing)
                    {
                        this.dal.Dispose();
                    }
                }
    
                this.disposed = true;
            }
    
            # endregion
    
        }
    }

## <a name="set-up-the-styles"></a>設定樣式 ##
若要變更頁面頂端的標題，請開啟*Views\Shared\\_Layout.cshtml*檔案**總管**] 中，並 」 應用程式名稱] 中瀏覽列標題以取代 「 我任務清單應用程式]，讓它看起來像這樣︰

    @Html.ActionLink("My Task List Application", "Index", "Home", null, new { @class = "navbar-brand" })

若要設定工作清單] 功能表，開啟*\Views\Home\Index.cshtml*檔案，並以下列程式碼取代現有的程式碼︰
    
    @model IEnumerable<MyTaskListApp.Models.MyTask>
    
    @{
        ViewBag.Title = "My Task List";
    }
    
    <h2>My Task List</h2>
    
    <table border="1">
        <tr>
            <th>Task</th>
            <th>Category</th>
            <th>Date</th>
            
        </tr>
    
    @foreach (var item in Model) {
        <tr>
            <td>
                @Html.DisplayFor(modelItem => item.Name)
            </td>
            <td>
                @Html.DisplayFor(modelItem => item.Category)
            </td>
            <td>
                @Html.DisplayFor(modelItem => item.Date)
            </td>
            
        </tr>
    }
    
    </table>
    <div>  @Html.Partial("Create", new MyTaskListApp.Models.MyTask())</div>


若要新增可建立新的工作，請以滑鼠右鍵按一下*Views\Home\\*資料夾] 和 [**新增****檢視**。  [*建立*] 檢視的名稱。 程式碼取代下列動作︰

    @model MyTaskListApp.Models.MyTask
    
    <script src="@Url.Content("~/Scripts/jquery-1.10.2.min.js")" type="text/javascript"></script>
    <script src="@Url.Content("~/Scripts/jquery.validate.min.js")" type="text/javascript"></script>
    <script src="@Url.Content("~/Scripts/jquery.validate.unobtrusive.min.js")" type="text/javascript"></script>
    
    @using (Html.BeginForm("Create", "Home")) {
        @Html.ValidationSummary(true)
        <fieldset>
            <legend>New Task</legend>
    
            <div class="editor-label">
                @Html.LabelFor(model => model.Name)
            </div>
            <div class="editor-field">
                @Html.EditorFor(model => model.Name)
                @Html.ValidationMessageFor(model => model.Name)
            </div>
    
            <div class="editor-label">
                @Html.LabelFor(model => model.Category)
            </div>
            <div class="editor-field">
                @Html.EditorFor(model => model.Category)
                @Html.ValidationMessageFor(model => model.Category)
            </div>
    
            <div class="editor-label">
                @Html.LabelFor(model => model.Date)
            </div>
            <div class="editor-field">
                @Html.EditorFor(model => model.Date)
                @Html.ValidationMessageFor(model => model.Date)
            </div>
    
            <p>
                <input type="submit" value="Create" />
            </p>
        </fieldset>
    }

**方案總管] 中**看起來應該像這樣︰

![方案總管][SolutionExplorerMyTaskListApp]

## <a name="set-the-mongodb-connection-string"></a>設定 MongoDB 連線字串 ##
在**方案總管]**中，開啟*DAL/Dal.cs*檔案。 尋找下列程式碼︰

    private string connectionString = "mongodb://<vm-dns-name>";

取代`<vm-dns-name>`您在此教學課程的[建立虛擬機器並安裝 MongoDB][]步驟以執行 MongoDB 的虛擬機器 DNS 名稱建立。  若要尋找您的虛擬機器 DNS 名稱，請移至 [Azure 入口網站的**虛擬機器**，，然後選取尋找**DNS 名稱**。

如果虛擬機器 DNS 名稱為 「 testlinuxvm.cloudapp.net 」 上的預設連接埠 27017 接聽 MongoDB，程式碼的連線字串行看起來像︰

    private string connectionString = "mongodb://testlinuxvm.cloudapp.net";

如果虛擬機器端點指定不同的外部連接埠的 MongoDB，您可以指定連線字串中的連接埠︰

    private string connectionString = "mongodb://testlinuxvm.cloudapp.net:12345";

如需有關 MongoDB 連接字串的詳細資訊，請參閱[連線][MongoConnectionStrings]。

## <a name="test-the-local-deployment"></a>測試本機部署 ##

若要開發電腦上執行應用程式，請從 [**偵錯**] 功能表中選取 [**啟動偵錯**或按**F5**。 IIS Express 啟動，並在瀏覽器會開啟，並啟動應用程式的 [首頁] 頁面。  您可以新增新的工作，將會新增至 MongoDB 資料庫中 Azure 虛擬機器上執行。

![我的工作清單應用程式][TaskListAppBlank]

## <a name="publish-to-azure-app-service-web-apps"></a>發佈至 Azure 應用程式服務 Web 應用程式

本節中您會將您的變更發佈至 Azure 應用程式服務 Web 應用程式。

1. 在 [方案總管**MyTaskListApp**以滑鼠右鍵按一下，然後按一下 [**發佈**]。
2. 按一下 [**發佈**]。

    您現在應該會看到您的 web app Azure 應用程式服務中執行，以及存取 MongoDB 資料庫 Azure 虛擬機器中的商務連絡人。

## <a name="summary"></a>摘要 ##

成功立即部署 ASP.NET 應用程式至 Azure 應用程式服務 Web 應用程式。 若要檢視的 web 應用程式︰

1. 登入 Azure 入口網站。
2. 按一下 [ **Web 應用程式**]。 
3. 在**Web 應用程式**清單中選取您的 web 應用程式。

如需開發 C# 針對 MongoDB 應用程式的詳細資訊，請參閱[和 CSharp 語言中心][MongoC#LangCenter]。 

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]
 

<!-- HYPERLINKS -->

[AzurePortal]: http://manage.windowsazure.com
[WindowsAzure]: http://www.windowsazure.com
[MongoC#LangCenter]: http://docs.mongodb.org/ecosystem/drivers/csharp/
[MVCWebSite]: http://www.asp.net/mvc
[ASP.NET]: http://www.asp.net/
[MongoConnectionStrings]: http://www.mongodb.org/display/DOCS/Connections
[MongoDB]: http://www.mongodb.org
[InstallMongoOnWindowsVM]: ../virtual-machines/virtual-machines-windows-classic-install-mongodb.md
[VSEWeb]: http://www.microsoft.com/visualstudio/eng/2013-downloads#d-2013-express
[VSUlt]: http://www.microsoft.com/visualstudio/eng/2013-downloads

<!-- IMAGES -->


[StartPageNewProject]: ./media/web-sites-dotnet-store-data-mongodb-vm/NewProject.png
[NewProjectMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/NewProjectMyTaskListApp.png
[VS2013SelectMVCTemplate]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013SelectMVCTemplate.png
[VS2013DefaultMVCApplication]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013DefaultMVCApplication.png
[VS2013ManageNuGetPackages]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013ManageNuGetPackages.png
[SearchforMongoDBCSharpDriver]: ./media/web-sites-dotnet-store-data-mongodb-vm/SearchforMongoDBCSharpDriver.png
[MongoDBCsharpDriverInstalled]: ./media/web-sites-dotnet-store-data-mongodb-vm/MongoDBCsharpDriverInstalled.png
[MongoDBCSharpDriverReferences]: ./media/web-sites-dotnet-store-data-mongodb-vm/MongoDBCSharpDriverReferences.png
[SolutionExplorerMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/SolutionExplorerMyTaskListApp.png
[TaskListAppBlank]: ./media/web-sites-dotnet-store-data-mongodb-vm/TaskListAppBlank.png
[WAWSCreateWebSite]: ./media/web-sites-dotnet-store-data-mongodb-vm/WAWSCreateWebSite.png
[WAWSDashboardMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/WAWSDashboardMyTaskListApp.png
[Image9]: ./media/web-sites-dotnet-store-data-mongodb-vm/RepoReady.png
[Image10]: ./media/web-sites-dotnet-store-data-mongodb-vm/GitInstructions.png
[Image11]: ./media/web-sites-dotnet-store-data-mongodb-vm/GitDeploymentComplete.png

<!-- TOC BOOKMARKS -->
[建立虛擬機器並安裝 MongoDB]: #virtualmachine
[Create and run the My Task List ASP.NET application on your development computer]: #createapp
[Create an Azure web site]: #createwebsite
[Deploy the ASP.NET application to the web site using Git]: #deployapp
 