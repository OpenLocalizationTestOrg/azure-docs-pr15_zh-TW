<properties 
    pageTitle="如何 Redis 快取以建立 Web 應用程式 |Microsoft Azure" 
    description="瞭解如何建立 Web 應用程式與 Redis 快取" 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="hero-article" 
    ms.date="10/11/2016" 
    ms.author="sdanie"/>

# <a name="how-to-create-a-web-app-with-redis-cache"></a>如何建立 Web 應用程式與 Redis 快取

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

本教學課程中會顯示如何建立並部署 ASP.NET web 應用程式的 web 應用程式，Azure 應用程式服務的使用 Visual Studio 2015。 範例應用程式顯示小組統計資料從資料庫的清單，並顯示不同的方式來儲存及擷取快取中的資料使用 Azure Redis 快取。 當您完成教學課程則必須執行的 web 應用程式的讀取與寫入最佳化 Azure Redis 快取，並裝載於 Azure 資料庫。

您將學習︰

-   如何在 Visual Studio 建立 ASP.NET MVC 5 web 應用程式。
-   如何存取使用實體架構的資料庫的資料。
-   如何改善資料處理量，以及儲存和擷取資料使用 Redis 快取 Azure 減少資料庫載入。
-   如何使用意指排序擷取上方的 5 小組的設定。
-   如何佈建應用程式使用資源管理員範本 Azure 資源。
-   如何發佈 Azure 使用 Visual Studio 應用程式。

## <a name="prerequisites"></a>必要條件

若要完成教學課程，您必須使用下列先決條件。

-   [Azure 帳戶](#azure-account)
-   [使用.net Azure SDK 的 visual Studio 2015](#visual-studio-2015-with-the-azure-sdk-for-net)

### <a name="azure-account"></a>Azure 帳戶

您必須完成本教學課程 Azure 帳戶。 您可以︰

* [免費開啟 Azure 帳戶](/pricing/free-trial/?WT.mc_id=redis_cache_hero)。 您會收到可以用來嘗試付費 Azure 服務的信用額度總計。 即使將貸項總計用完之後，您可以保留帳戶，使用免費的 Azure 服務和功能]。
* [啟動 Visual Studio 訂閱者優點](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero)。 您的 MSDN 訂閱可讓您貸項總計付費 Azure 服務，您可以使用每個月。

### <a name="visual-studio-2015-with-the-azure-sdk-for-net"></a>使用.net Azure SDK 的 visual Studio 2015

教學課程是使用[.NET Azure SDK](../dotnet-sdk.md) 2.8.2 Visual Studio 2015 撰寫或更新版本。 [下載最新 Azure SDK 以下 Visual Studio 2015](http://go.microsoft.com/fwlink/?linkid=518003)。 如果您沒有 visual Studio 會自動安裝與 SDK。

如果您有 Visual Studio 2013 時，您可以[下載最新 Azure SDK Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkID=324322)。 某些畫面看起來可能不同於此教學課程中所顯示的圖例。

>[AZURE.NOTE] 根據幾 SDK 相依性您已經在電腦上，請安裝 SDK 花很長的時間，從幾分鐘在半小時或更多的時間。

## <a name="create-the-visual-studio-project"></a>建立 Visual Studio 專案

1. 開啟 Visual Studio，然後按一下**檔案**，**新增****專案**]。

2. 展開 [**範本**] 清單中的**Visual C#**節點、 選取**雲端**，然後按一下**ASP.NET Web 應用程式**。 確定已選取 [ **.NET Framework 4.5.2** 。  在 [**名稱**] 文字方塊中輸入**ContosoTeamStats** ，然後按一下**[確定]**。
 
    ![建立專案][cache-create-project]

3. 您可以選取 [ **MVC**作為專案類型。 清除**在雲端的 host （主機）**核取方塊。 在教學課程中會[佈建 Azure 資源](#provision-the-azure-resources)及後續步驟[發行 Azure 應用程式](#publish-the-application-to-azure)。 範例保留**在雲端的 host （主機）**核取 [提供從 Visual Studio 應用程式服務 web 應用程式，請參閱[Azure 應用程式服務中，使用 ASP.NET 和 Visual Studio Web Apps 快速入門](../app-service-web/web-sites-dotnet-get-started.md)。

    ![選取 [專案範本][cache-select-template]

4. 按一下**[確定**] 以建立專案。

## <a name="create-the-aspnet-mvc-application"></a>建立 ASP.NET MVC 應用程式

在本教學課程此區段中，您會建立基本的應用程式會讀取，並會顯示從資料庫小組統計資料。

-   [新增模型](#add-the-model)
-   [新增控制器](#add-the-controller)
-   [設定檢視](#configure-the-views)

### <a name="add-the-model"></a>新增模型

1. 以滑鼠右鍵按一下 [**模型****總管**] 中，選擇 [**新增**]**類別**。 

    ![新增模型][cache-model-add-class]

2. 輸入`Team`類別的名稱，然後按一下 [**新增**]。

    ![新增模型類別][cache-model-add-class-dialog]

3. 取代`using`頂端的陳述式`Team.cs`檔案以下列使用陳述式。


        using System;
        using System.Collections.Generic;
        using System.Data.Entity;
        using System.Data.Entity.SqlServer;


4. 取代的定義`Team`課程包含更新下列程式碼片段`Team`課程定義，以及某些其他實體架構協助類別。 如需有關在本教學課程中使用的實體架構的程式碼第一種方式的詳細資訊，請參閱[程式碼，第一次以新的資料庫](https://msdn.microsoft.com/data/jj193542)。


        public class Team
        {
            public int ID { get; set; }
            public string Name { get; set; }
            public int Wins { get; set; }
            public int Losses { get; set; }
            public int Ties { get; set; }
        
            static public void PlayGames(IEnumerable<Team> teams)
            {
                // Simple random generation of statistics.
                Random r = new Random();
        
                foreach (var t in teams)
                {
                    t.Wins = r.Next(33);
                    t.Losses = r.Next(33);
                    t.Ties = r.Next(0, 5);
                }
            }
        }
        
        public class TeamContext : DbContext
        {
            public TeamContext()
                : base("TeamContext")
            {
            }
        
            public DbSet<Team> Teams { get; set; }
        }
        
        public class TeamInitializer : CreateDatabaseIfNotExists<TeamContext>
        {
            protected override void Seed(TeamContext context)
            {
                var teams = new List<Team>
                {
                    new Team{Name="Adventure Works Cycles"},
                    new Team{Name="Alpine Ski House"},
                    new Team{Name="Blue Yonder Airlines"},
                    new Team{Name="Coho Vineyard"},
                    new Team{Name="Contoso, Ltd."},
                    new Team{Name="Fabrikam, Inc."},
                    new Team{Name="Lucerne Publishing"},
                    new Team{Name="Northwind Traders"},
                    new Team{Name="Consolidated Messenger"},
                    new Team{Name="Fourth Coffee"},
                    new Team{Name="Graphic Design Institute"},
                    new Team{Name="Nod Publishers"}
                };
        
                Team.PlayGames(teams);
        
                teams.ForEach(t => context.Teams.Add(t));
                context.SaveChanges();
            }
        }
        
        public class TeamConfiguration : DbConfiguration
        {
            public TeamConfiguration()
            {
                SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
            }
        }


2. 在**方案總管**] 中，按兩下 [ **web.config** ，將其開啟。

    ![Web.config][cache-web-config]

3.  新增下列連線字串`connectionStrings`一節。 連接字串的名稱必須符合所實體架構資料庫內容類別的名稱`TeamContext`。

        <add name="TeamContext" connectionString="Data Source=(LocalDB)\v11.0;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True" providerName="System.Data.SqlClient" />


    新增後,`connectionStrings`區段看起來應該像下列範例。


        <connectionStrings>
            <add name="DefaultConnection" connectionString="Data Source=(LocalDb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\aspnet-ContosoTeamStats-20160216120918.mdf;Initial Catalog=aspnet-ContosoTeamStats-20160216120918;Integrated Security=True"
                providerName="System.Data.SqlClient" />
            <add name="TeamContext" connectionString="Data Source=(LocalDB)\v11.0;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True"  providerName="System.Data.SqlClient" />
        </connectionStrings>

### <a name="add-the-controller"></a>新增控制器

1. 按下**f6 鍵**來建立專案。 
2. 在**方案總管]**中，以滑鼠右鍵按一下**控制站**資料夾，然後選擇 [**新增**]，**控制站**。

    ![新增控制器][cache-add-controller]

3. 選擇**MVC 5 控制器使用實體架構的檢視**，然後按一下 [**新增**]。 如果您收到錯誤，按一下 [**新增**] 後，請確定您已經建立專案第一次。

    ![新增控制器類別][cache-add-controller-class]

5. 從**模型類別**下拉式清單中選取 [**小組 (ContosoTeamStats.Models)** ]。 從**資料內容類別**下拉式清單中選取**TeamContext (ContosoTeamStats.Models)** 。 輸入`TeamsController`在**控制器**名稱] 文字方塊 （如果它不會自動填入）。 按一下 [**新增**]，以建立控制器類別，並將預設檢視]。

    ![設定控制站][cache-configure-controller]

4. 在**方案總管**] 中，展開**Global.asax**並按兩下**Global.asax.cs** ，將其開啟。

    ![Global.asax.cs][cache-global-asax]

5. 新增下列兩個使用陳述式頂端，在 [其他檔案的使用陳述式。


        using System.Data.Entity;
        using ContosoTeamStats.Models;


6. 新增下列程式碼的結尾`Application_Start`方法。


        Database.SetInitializer<TeamContext>(new TeamInitializer());


7. 在**方案總管]**中，展開`App_Start`按兩下`RouteConfig.cs`。

    ![RouteConfig.cs][cache-RouteConfig-cs]

8. 取代`controller = "Home"`下列程式碼中`RegisterRoutes`方法`controller = "Teams"`如下列範例所示。


        routes.MapRoute(
            name: "Default",
            url: "{controller}/{action}/{id}",
            defaults: new { controller = "Teams", action = "Index", id = UrlParameter.Optional }
        );


### <a name="configure-the-views"></a>設定檢視

1. 在**方案總管]**中，展開 [**檢視**] 資料夾，然後 [**共用**] 資料夾，然後按兩下**_Layout.cshtml**。 

    ![_Layout.cshtml][cache-layout-cshtml]

2. 變更內容`title`項目和取代`My ASP.NET Application`與`Contoso Team Stats`如下列範例所示。


        <title>@ViewBag.Title - Contoso Team Stats</title>


3. 在`body`區段中，更新第一個`Html.ActionLink`陳述式，取代`Application name`與`Contoso Team Stats`及取代`Home`與`Teams`。
    -   之前︰`@Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })`
    -   之後︰`@Html.ActionLink("Contoso Team Stats", "Index", "Teams", new { area = "" }, new { @class = "navbar-brand" })`

    ![程式碼變更][cache-layout-cshtml-code]

4. 按下**Ctrl + F5**建立和執行應用程式。 這個版本的應用程式會讀取結果，直接從資料庫。 請注意**具有檢視，使用實體架構 MVC 5 控制器**scaffold 由自動加入至應用程式的**新建立**、**編輯**、**詳細資料**，以及**刪除**動作。 在本教學課程的下一節中您要新增 Redis 最佳化資料存取，提供其他功能至應用程式的快取。

![入門應用程式][cache-starter-application]

## <a name="configure-the-application-to-use-redis-cache"></a>將應用程式使用 Redis 快取設定

在本教學課程此區段中，您可以設定範例應用程式來儲存和使用[StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis)快取用戶端從 Azure Redis 快取執行個體來擷取 Contoso 小組統計資料。

-   [設定應用程式使用 StackExchange.Redis](#configure-the-application-to-use-stackexchangeredis)
-   [傳回從快取或資料庫的結果 TeamsController 類別的更新](#update-the-teamscontroller-class-to-return-results-from-the-cache-or-the-database)
-   [更新的建立、 編輯和刪除的方法，使用快取](#update-the-create-edit-and-delete-methods-to-work-with-the-cache)
-   [更新 [小組索引] 檢視，以使用 [快取](#update-the-teams-index-view-to-work-with-the-cache)


### <a name="configure-the-application-to-use-stackexchangeredis"></a>設定應用程式使用 StackExchange.Redis

1. 若要在 Visual Studio 中使用 StackExchange.Redis NuGet 封裝設定用戶端應用程式，以滑鼠右鍵按一下 [專案**總管**] 中的，然後選擇 [**管理 NuGet 套件**。 

    ![管理 NuGet 套件][redis-cache-manage-nuget-menu]

2. [搜尋] 方塊中輸入**StackExchange.Redis**從結果，選取所要的版本，按一下 [**安裝**]。

    ![StackExchange.Redis NuGet 套件][redis-cache-stack-exchange-nuget]

    NuGet 套件下載，並新增必要的組件的參照用戶端應用程式使用 StackExchange.Redis 快取用戶端存取 Azure Redis 快取。 如果您想要使用強式**StackExchange.Redis**用戶端文件庫的版本，選擇 [ **StackExchange.Redis.StrongName**;否則，請選擇**StackExchange.Redis**。

3. 在**方案總管]**中，展開**控制站**資料夾，然後按兩下**TeamsController.cs** ，將其開啟。

    ![小組控制器][cache-teamscontroller]

4. 新增下列兩個使用**TeamsController.cs**陳述式。

        using System.Configuration;
        using StackExchange.Redis;

5. 新增下列兩個屬性，以`TeamsController`類別。

        // Redis Connection string info
        private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
        {
            string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
            return ConnectionMultiplexer.Connect(cacheConnection);
        });
    
        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }
  
1. 建立名為您電腦上的檔案`WebAppPlusCacheAppSecrets.config`並將其置於不會檢查於您決定要存回其位置應來源的程式碼範例應用程式中，使用的位置。 在此範例中`AppSettingsSecrets.config`檔案是位於`C:\AppSecrets\WebAppPlusCacheAppSecrets.config`。

    編輯`WebAppPlusCacheAppSecrets.config`檔案，並加入下列內容。 如果您在本機上執行應用程式這項資訊會用來連線到您 Azure Redis 快取的執行個體。 稍後在教學課程中，您會佈建 Azure Redis 快取執行個體及更新的快取名稱與密碼。 如果您不打算在本機上執行的範例應用程式您可以跳到建立此檔案，參照該檔案的後續步驟，因為應用程式部署 Azure 時擷取的快取連線資訊，從 Web 應用程式的應用程式設定，而不是從這個檔案。 由於`WebAppPlusCacheAppSecrets.config`不部署至 Azure 與應用程式，您不需要除非您要在本機上執行應用程式。


        <appSettings>
          <add key="CacheConnection" value="MyCache.redis.cache.windows.net,abortConnect=false,ssl=true,password=..."/>
        </appSettings>


2. 在**方案總管**] 中，按兩下 [ **web.config** ，將其開啟。

    ![Web.config][cache-web-config]

3. 新增下列`file`屬性設定為`appSettings`項目。 如果您使用不同的檔案名稱或位置，以取代這些範例所示的值。
    -   之前︰`<appSettings>`
    -   之後︰` <appSettings file="C:\AppSecrets\WebAppPlusCacheAppSecrets.config">`

    ASP.NET 執行階段外部檔案中的標記的內容合併`<appSettings>`項目。 執行階段會忽略檔案屬性，如果找不到指定的檔案。 您的密碼 （您的快取的連接字串） 不是應用程式的程式碼的一部分的。 當您將 web 應用程式部署到 Azure`WebAppPlusCacheAppSecrests.config`檔案無法部署 （亦即您想要）。 有數種方式可指定這些機密 Azure 中，在本教學課程，會自動為您設定當您在後續的教學課程中的 [[佈建 Azure 資源](#provision-the-azure-resources)步驟。 如需有關如何使用 Azure 中的機密資料的詳細資訊，請參閱[部署密碼及其他機密資料至 ASP.NET 和 Azure 應用程式服務的最佳作法](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure)。


### <a name="update-the-teamscontroller-class-to-return-results-from-the-cache-or-the-database"></a>傳回從快取或資料庫的結果 TeamsController 類別的更新

在此範例中，可以從資料庫或快取擷取小組統計資料。 小組統計資料會儲存在 [快取序列`List<Team>`，以及也使用意指的資料類型排序設定。 在項目擷取從排序時，您擷取一些，所有項目，或查詢的某些項目。 在這個範例中，您會查詢上方 5 小組獲勝數來的排序的設定。

>[AZURE.NOTE] 您不需要使用快取中的多個格式儲存的小組統計資料，才能使用 Azure Redis 快取。 本教學課程中使用多個格式會示範一些不同的方式，您可以使用快取資料的不同資料類型。



1. 新增下列使用陳述式以`TeamsController.cs`檔案與其他頂端使用陳述式。

        using System.Diagnostics;
        using Newtonsoft.Json;

2. 取代目前`public ActionResult Index()`具有下列實作的方法。


        // GET: Teams
        public ActionResult Index(string actionType, string resultType)
        {
            List<Team> teams = null;
        
            switch(actionType)
            {
                case "playGames": // Play a new season of games.
                    PlayGames();
                    break;
        
                case "clearCache": // Clear the results from the cache.
                    ClearCachedTeams();
                    break;
        
                case "rebuildDB": // Rebuild the database with sample data.
                    RebuildDB();
                    break;
            }
        
            // Measure the time it takes to retrieve the results.
            Stopwatch sw = Stopwatch.StartNew();
        
            switch(resultType)
            {
                case "teamsSortedSet": // Retrieve teams from sorted set.
                    teams = GetFromSortedSet();
                    break;
        
                case "teamsSortedSetTop5": // Retrieve the top 5 teams from the sorted set.
                    teams = GetFromSortedSetTop5();
                    break;
        
                case "teamsList": // Retrieve teams from the cached List<Team>.
                    teams = GetFromList();
                    break;
        
                case "fromDB": // Retrieve results from the database.
                default:
                    teams = GetFromDB();
                    break;
            }
        
            sw.Stop();
            double ms = sw.ElapsedTicks / (Stopwatch.Frequency / (1000.0));

            // Add the elapsed time of the operation to the ViewBag.msg.
            ViewBag.msg += " MS: " + ms.ToString();
        
            return View(teams);
        }


3. 新增下列三種方法`TeamsController`類別實作`playGames`， `clearCache`，及`rebuildDB`從先前的程式碼片段中新增切換陳述式的巨集指令類型。

    `PlayGames`方法模擬的遊戲季節更新小組統計資料、 將結果儲存資料庫，並清除快取中現在過時的資料。


        void PlayGames()
        {
            ViewBag.msg += "Updating team statistics. ";
            // Play a "season" of games.
            var teams = from t in db.Teams
                        select t;
    
            Team.PlayGames(teams);
    
            db.SaveChanges();
    
            // Clear any cached results
            ClearCachedTeams();
        }


    `RebuildDB`方法重新初始化小組的預設設定資料庫、 產生統計資料，並清除快取中現在過時的資料。
    
        void RebuildDB()
        {
            ViewBag.msg += "Rebuilding DB. ";
            // Delete and re-initialize the database with sample data.
            db.Database.Delete();
            db.Database.Initialize(true);

            // Clear any cached results
            ClearCachedTeams();
        }


    `ClearCachedTeams`方法會移除快取中的任何快取的小組統計資料。

    
        void ClearCachedTeams()
        {
            IDatabase cache = Connection.GetDatabase();
            cache.KeyDelete("teamsList");
            cache.KeyDelete("teamsSortedSet");
            ViewBag.msg += "Team data removed from cache. ";
        } 


4. 新增下列四種方法來`TeamsController`類別實作擷取小組統計資料快取與資料庫的各種方式。 這些方法會傳回`List<Team>`然後檢視所顯示的。

    `GetFromDB`方法會讀取資料庫中的小組統計資料。

        List<Team> GetFromDB()
        {
            ViewBag.msg += "Results read from DB. ";
            var results = from t in db.Teams
                orderby t.Wins descending
                select t; 
    
            return results.ToList<Team>();
        }


    `GetFromList`方法會讀取序列快取中的小組統計資料`List<Team>`。 如果有快取遺失，請小組統計資料從資料庫中讀取而然後儲存快取中的下一次。 在這個範例中，我們要使用 JSON.NET 序列化來序列化.NET 物件快取中往返。 如需詳細資訊，請參閱[如何使用.NET Azure Redis 快取中的物件](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache)。

        List<Team> GetFromList()
        {
            List<Team> teams = null;

            IDatabase cache = Connection.GetDatabase();
            string serializedTeams = cache.StringGet("teamsList");
            if (!String.IsNullOrEmpty(serializedTeams))
            {
                teams = JsonConvert.DeserializeObject<List<Team>>(serializedTeams);

                ViewBag.msg += "List read from cache. ";
            }
            else
            {
                ViewBag.msg += "Teams list cache miss. ";
                // Get from database and store in cache
                teams = GetFromDB();

                ViewBag.msg += "Storing results to cache. ";
                cache.StringSet("teamsList", JsonConvert.SerializeObject(teams));
            }
            return teams;
        }


    `GetFromSortedSet`方法會讀取快取排序設定中的小組統計資料。 快取遺失時，小組統計資料是從資料庫讀取及儲存的快取為排序的設定。


        List<Team> GetFromSortedSet()
        {
            List<Team> teams = null;
            IDatabase cache = Connection.GetDatabase();
            // If the key teamsSortedSet is not present, this method returns a 0 length collection.
            var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", order: Order.Descending);
            if (teamsSortedSet.Count() > 0)
            {
                ViewBag.msg += "Reading sorted set from cache. ";
                teams = new List<Team>();
                foreach (var t in teamsSortedSet)
                {
                    Team tt = JsonConvert.DeserializeObject<Team>(t.Element);
                    teams.Add(tt);
                }
            }
            else
            {
                ViewBag.msg += "Teams sorted set cache miss. ";
    
                // Read from DB
                teams = GetFromDB();
    
                ViewBag.msg += "Storing results to cache. ";
                foreach (var t in teams)
                {
                    Console.WriteLine("Adding to sorted set: {0} - {1}", t.Name, t.Wins);
                    cache.SortedSetAdd("teamsSortedSet", JsonConvert.SerializeObject(t), t.Wins);
                }
            }
            return teams;
        }


    `GetFromSortedSetTop5`方法會從快取排序設定讀取前 5 個小組。 同時也會檢查是否存在的快取`teamsSortedSet`鍵。 如果此按鍵不存在，`GetFromSortedSet`方法稱為閱讀小組統計資料，並將它們儲存在快取。 下一步快取排序的設定查詢所傳回的上方 5 小組。


        List<Team> GetFromSortedSetTop5()
        {
            List<Team> teams = null;
            IDatabase cache = Connection.GetDatabase();

            // If the key teamsSortedSet is not present, this method returns a 0 length collection.
            var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
            if(teamsSortedSet.Count() == 0)
            {
                // Load the entire sorted set into the cache.
                GetFromSortedSet();

                // Retrieve the top 5 teams.
                teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
            }

            ViewBag.msg += "Retrieving top 5 teams from cache. ";
            // Get the top 5 teams from the sorted set
            teams = new List<Team>();
            foreach (var team in teamsSortedSet)
            {
                teams.Add(JsonConvert.DeserializeObject<Team>(team.Element));
            }
            return teams;
        }


### <a name="update-the-create-edit-and-delete-methods-to-work-with-the-cache"></a>更新的建立、 編輯和刪除的方法，使用快取

屬於此範例中所產生的臨時平台程式碼包含方法來新增、 編輯及刪除小組。 新增、 編輯或移除小組時，隨時快取中的資料會變成過期。 此區段中，您將修改若要清除快取的小組，以便快取將不會與資料庫同步處理這三種方法。

1. 瀏覽至`Create(Team team)`中的方法`TeamsController`類別。 加入通話`ClearCachedTeams`方法，如下列範例所示。


        // POST: Teams/Create
        // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
        // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Create([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
        {
            if (ModelState.IsValid)
            {
                db.Teams.Add(team);
                db.SaveChanges();
                // When a team is added, the cache is out of date.
                // Clear the cached teams.
                ClearCachedTeams();
                return RedirectToAction("Index");
            }
    
            return View(team);
        }


2. 瀏覽至`Edit(Team team)`中的方法`TeamsController`類別。 加入通話`ClearCachedTeams`方法，如下列範例所示。


        // POST: Teams/Edit/5
        // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
        // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Edit([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
        {
            if (ModelState.IsValid)
            {
                db.Entry(team).State = EntityState.Modified;
                db.SaveChanges();
                // When a team is edited, the cache is out of date.
                // Clear the cached teams.
                ClearCachedTeams();
                return RedirectToAction("Index");
            }
            return View(team);
        }


3. 瀏覽至`DeleteConfirmed(int id)`中的方法`TeamsController`類別。 加入通話`ClearCachedTeams`方法，如下列範例所示。


        // POST: Teams/Delete/5
        [HttpPost, ActionName("Delete")]
        [ValidateAntiForgeryToken]
        public ActionResult DeleteConfirmed(int id)
        {
            Team team = db.Teams.Find(id);
            db.Teams.Remove(team);
            db.SaveChanges();
            // When a team is deleted, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }


### <a name="update-the-teams-index-view-to-work-with-the-cache"></a>更新 [小組索引] 檢視，以使用 [快取

1. 在**方案總管]**中，展開 [**檢視**] 資料夾，然後**小組**資料夾，然後按兩下**Index.cshtml**。

    ![Index.cshtml][cache-views-teams-index-cshtml]

2. 檔案頂端附近尋找下列段落項目。

    ![巨集指令的資料表][cache-teams-index-table]

    這是連結，以建立新的小組。 下表取代段落項目。 此表格中有動作建立新的小組、 播放遊戲新季節、 清除快取，從數種格式的快取擷取小組、 從資料庫]，擷取小組和重新建立全新的範例資料的資料庫的連結。


        <table class="table">
            <tr>
                <td>
                    @Html.ActionLink("Create New", "Create")
                </td>
                <td>
                    @Html.ActionLink("Play Season", "Index", new { actionType = "playGames" })
                </td>
                <td>
                    @Html.ActionLink("Clear Cache", "Index", new { actionType = "clearCache" })
                </td>
                <td>
                    @Html.ActionLink("List from Cache", "Index", new { resultType = "teamsList" })
                </td>
                <td>
                    @Html.ActionLink("Sorted Set from Cache", "Index", new { resultType = "teamsSortedSet" })
                </td>
                <td>
                    @Html.ActionLink("Top 5 Teams from Cache", "Index", new { resultType = "teamsSortedSetTop5" })
                </td>
                <td>
                    @Html.ActionLink("Load from DB", "Index", new { resultType = "fromDB" })
                </td>
                <td>
                    @Html.ActionLink("Rebuild DB", "Index", new { actionType = "rebuildDB" })
                </td>
            </tr>    
        </table>


3. 捲動到底部的**Index.cshtml**檔案，新增下列`tr`項目，將它在檔案中的最後一個資料表中的最後一列。

        <tr><td colspan="5">@ViewBag.Msg</td></tr>

    此列顯示的值`ViewBag.Msg`其中包含目前設定當您按一下其中一個動作連結的上一個步驟的作業有關的狀態報表。   

    ![狀態訊息][cache-status-message]

4. 按下**f6 鍵**來建立專案。

## <a name="provision-the-azure-resources"></a>佈建 Azure 資源

若要主控您的應用程式 Azure 中，您必須先佈建 Azure 服務應用程式需要。 在本教學課程的範例應用程式會使用下列 Azure 服務。

-   Azure 意指快取
-   應用程式服務 Web 應用程式
-   SQL 資料庫

若要將這些服務部署至您的選擇新的或現有的資源群組，按一下 [下列**部署至 Azure** ] 按鈕。

[![部署至 Azure][deploybutton]](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-redis-cache-sql-database%2Fazuredeploy.json)

此**部署至 Azure** ] 按鈕會使用[建立 Web 應用程式加上 Redis 快取加上 SQL 資料庫](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) [Azure 快速入門](https://github.com/Azure/azure-quickstart-templates)範本提供這些服務，並針對 SQL 資料庫和 Azure Redis 快取連線字串的應用程式設定的連接字串。

>[AZURE.NOTE] 如果您沒有安裝 Azure 帳戶，您可以[建立的免費 Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero)在幾分鐘。

按一下 [**部署至 Azure** ] 按鈕會帶您到 Azure 入口網站，並啟動程序建立的範本所述的資源。

![部署至 Azure][cache-deploy-to-azure-step-1]

1. 在**自訂部署**刀中，選取要使用，並選取 [現有的資源群組或建立一個新的 Azure 訂閱並指定資源群組的位置。
2. 在**參數**防禦，以指定的系統管理員帳戶名稱 (**ADMINISTRATORLOGIN** -不是使用**管理員**)，系統管理員登入密碼 (**ADMINISTRATORLOGINPASSWORD**) 和資料庫名稱 (**資料庫**)。 免費的應用程式服務裝載計劃，以及較低成本選項不隨附的免費層 SQL 資料庫及 Azure Redis 的快取設定其他參數。
3. 變更的任何其他設定如有需要，或保留預設值]，然後按一下**[確定]**。


![部署至 Azure][cache-deploy-to-azure-step-2]

1. 按一下 [**校閱法律字詞**]。
2. 閱讀在**購買**刀條款，然後按一下 [**購買**]。
3. 若要開始佈建資源，按一下 [**建立****自訂部署**刀上。

若要檢視您的部署的進度，請按一下 [通知] 圖示，按一下**部署開始**。

![部署入門][cache-deployment-started]

您可以**Microsoft.Template**刀上檢視您的部署中的狀態。

![部署至 Azure][cache-deploy-to-azure-step-3]

佈建完成後，您可以發佈您的應用程式至 Azure Visual Studio。

>[AZURE.NOTE] 提供程序可能會發生任何錯誤會顯示在**Microsoft.Template**刀。 常見的錯誤是太多的 SQL Server 或太多可用的應用程式服務裝載每個訂閱的方案。 任何錯誤的解決方法並重新啟動程序即可**重新部署** **Microsoft.Template**刀或**部署至 Azure** ] 按鈕在本教學課程。

## <a name="publish-the-application-to-azure"></a>發佈 Azure 應用程式

在此步驟的教學課程中，您會發佈 Azure 應用程式，並在雲端中執行。

1. 以滑鼠右鍵按一下 [在 Visual Studio **ContosoTeamStats**專案，然後選擇 [**發佈**]。

    ![發佈][cache-publish-app]

2. 按一下 [ **Microsoft Azure 應用程式服務**。

    ![發佈][cache-publish-to-app-service]

3. 選取訂閱，建立 Azure 資源時，使用、 展開包含資源的 [資源] 群組，選取所要的 Web 應用程式，然後按一下**[確定]**。 如果您是使用您的 Web 應用程式名稱開頭為**網站**後面接著一些額外的字元 [**部署至 Azure** ] 按鈕。

    ![選取 Web 應用程式][cache-select-web-app]

4. 按一下 [**驗證連線**以驗證您的設定]，然後按一下 [**發佈]**。

    ![發佈][cache-publish]

    發佈程序會完成在幾分鐘之後，並在瀏覽器就會啟動與執行範例應用程式。 如果您收到 DNS 錯誤時驗證或發佈，只是最近完成應用程式的 Azure 資源的佈建程序，稍後再試一次。

    ![新增快取][cache-added-to-application]

下表描述每個範例應用程式的 [動作] 連結。

| 巨集指令                  | 描述                                                                                                                                                      |
|-------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 建立新的              | 建立新的小組。                                                                                                                                               |
| 播放佳節             | 播放季節的遊戲，更新小組統計資料，並清除任何過期小組從快取資料。                                                                          |
| 清除快取             | 清除快取小組統計資料。                                                                                                                             |
| 從快取的清單         | 從快取擷取小組統計資料。 如果有快取遺失，請將資料庫中載入統計資料和儲存快取以供下次。                                        |
| 從快取排序的設定   | 從快取使用排序的集來擷取小組統計資料。 如果有快取遺失，請將資料庫中載入統計資料，然後儲存快取使用排序的設定。  |
| 從快取的頂端 5 小組  | 從快取使用排序的集來擷取上方的 5 小組。 如果有快取遺失，請將資料庫中載入統計資料，然後儲存快取使用排序的設定。 |
| 從 DB 載入            | 從資料庫中擷取小組統計資料。                                                                                                                       |
| 重建 DB              | 重建資料庫並重新載入範例小組資料。                                                                                                        |
| 編輯 / 詳細資料 / 刪除 | 編輯小組檢視小組的詳細資料、 刪除小組。                                                                                                             |


按一下某些動作，並嘗試使用來自不同來源擷取資料。 完成的各種方式，從資料庫並快取擷取資料的所需的時間內的差異。

## <a name="delete-the-resources-when-you-are-finished-with-the-application"></a>當您完成應用程式時，刪除資源

當您完成範例教學課程應用程式時，您可以刪除用來節省成本與資源的 Azure 資源。 如果您使用 [**部署至 Azure** ] 按鈕在[佈建 Azure 資源](#provision-the-azure-resources)] 區段中，所有資源的都包含相同的 [資源] 群組中，您可以刪除它們一起一次刪除資源群組。

1. 登入[Azure 入口網站](https://portal.azure.com)，然後按一下 [**資源群組**]。
2. **篩選項目...** ] 文字方塊中輸入資源群組的名稱。
3. 按一下 [ **...**右側的 [資源群組。
4. 按一下 [**刪除**]。

    ![刪除][cache-delete-resource-group]

5. 輸入資源群組的名稱，然後按一下 [**刪除**]。

    ![確認刪除][cache-delete-confirm]

在幾分鐘之後會刪除資源群組，所有它所含的資源。

>[AZURE.IMPORTANT] 注意刪除資源群組是無法回復，並將會永久刪除 [資源群組與中的所有資源。 請確定的沒有不小心刪除錯誤的資源群組或資源。 如果您建立的主機服務現有的資源群組內的這個範例資源時，您可以從其個別刀個別刪除每個資源。

## <a name="run-the-sample-application-on-your-local-machine"></a>在您的本機電腦上執行的範例應用程式

若要在您的電腦上的本機執行應用程式，您需要 Azure Redis 快取中的執行個體快取資料。 

-   如果您有發佈您的應用程式 Azure 前一節所述，您可以使用提供的步驟期間的 Azure Redis 快取執行個體。
-   如果您有其他現有的 Azure Redis 快取執行個體，您可以使用的本機執行這個範例。
-   如果您需要建立 Azure Redis 快取執行個體，您可以依照[建立快取](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache)中的步驟進行。

一旦您所選取或建立使用快取，請瀏覽至 Azure 入口網站中的快取，並擷取 [[主機名稱](cache-configure.md#properties)] 和 [快取的[便捷鍵](cache-configure.md#access-keys)。 如需相關指示，請參閱[設定 Redis 快取設定](cache-configure.md#configure-redis-cache-settings)。

1. 開啟`WebAppPlusCacheAppSecrets.config`您在本教學課程中使用您所選擇的編輯器] 中的[應用程式使用 Redis 快取設定](#configure-the-application-to-use-redis-cache)步驟所建立的檔案。

2. 編輯`value`屬性及取代`MyCache.redis.cache.windows.net`[主機名稱]](cache-configure.md#properties)的快取，並指定 [[主要或次要索引鍵](cache-configure.md#access-keys)的快取的密碼。


        <appSettings>
          <add key="CacheConnection" value="MyCache.redis.cache.windows.net,abortConnect=false,ssl=true,password=..."/>
        </appSettings>


3. 按下**Ctrl + F5**執行應用程式。

>[AZURE.NOTE] 請注意，因為應用程式，包括資料庫，在本機執行 Redis 快取裝載於 Azure，快取可能會出現在執行資料庫。 為獲得最佳效能，用戶端應用程式和 Azure Redis 快取執行個體應該在相同的位置。 

## <a name="next-steps"></a>後續步驟

-   進一步瞭解[ASP.NET](http://asp.net/)網站上的 [[快速入門 ASP.NET MVC 5](http://www.asp.net/mvc/overview/getting-started/introduction/getting-started) 。
-   更多範例服務應用程式中建立 ASP.NET Web 應用程式的詳細資訊，請參閱[建立並部署 ASP.NET web 應用程式中 Azure 應用程式服務](https://github.com/Microsoft/HealthClinic.biz/wiki/Create-and-deploy-an-ASP.NET-web-app-in-Azure-App-Service)從[HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 連線[示範](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/)。
    -   從 HealthClinic.biz 示範更多的快速入門，請參閱[Azure 開發人員工具快速入門](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts)。
-   進一步瞭解在本教學課程中使用的實體架構[的程式碼第一次為新資料庫](https://msdn.microsoft.com/data/jj193542)的方法。
-   深入瞭解[web 應用程式中 Azure 應用程式服務](../app-service-web/app-service-web-overview.md)。
-   瞭解如何[監控](cache-how-to-monitor.md)您 Azure 入口網站中的快取。

-   探索 Azure Redis 快取進階功能
    -   [如何設定持續進階版 Azure Redis 快取](cache-how-to-premium-persistence.md)
    -   [如何設定叢集進階版 Azure Redis 快取](cache-how-to-premium-clustering.md)
    -   [如何設定進階版 Azure Redis 快取的虛擬網路支援](cache-how-to-premium-vnet.md)
    -   請參閱[Azure Redis 快取常見問題集](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)，如需詳細資訊大小、 處理量，以及使用進階版的快取的頻寬。



<!-- IMAGES -->
[cache-starter-application]: ./media/cache-web-app-howto/cache-starter-application.png
[cache-added-to-application]: ./media/cache-web-app-howto/cache-added-to-application.png
[cache-create-project]: ./media/cache-web-app-howto/cache-create-project.png
[cache-select-template]: ./media/cache-web-app-howto/cache-select-template.png
[cache-model-add-class]: ./media/cache-web-app-howto/cache-model-add-class.png
[cache-model-add-class-dialog]: ./media/cache-web-app-howto/cache-model-add-class-dialog.png
[cache-add-controller]: ./media/cache-web-app-howto/cache-add-controller.png
[cache-add-controller-class]: ./media/cache-web-app-howto/cache-add-controller-class.png
[cache-configure-controller]: ./media/cache-web-app-howto/cache-configure-controller.png
[cache-global-asax]: ./media/cache-web-app-howto/cache-global-asax.png
[cache-RouteConfig-cs]: ./media/cache-web-app-howto/cache-RouteConfig-cs.png
[cache-layout-cshtml]: ./media/cache-web-app-howto/cache-layout-cshtml.png
[cache-layout-cshtml-code]: ./media/cache-web-app-howto/cache-layout-cshtml-code.png
[redis-cache-manage-nuget-menu]: ./media/cache-web-app-howto/redis-cache-manage-nuget-menu.png
[redis-cache-stack-exchange-nuget]: ./media/cache-web-app-howto/redis-cache-stack-exchange-nuget.png
[cache-teamscontroller]: ./media/cache-web-app-howto/cache-teamscontroller.png
[cache-web-config]: ./media/cache-web-app-howto/cache-web-config.png
[cache-views-teams-index-cshtml]: ./media/cache-web-app-howto/cache-views-teams-index-cshtml.png
[cache-teams-index-table]: ./media/cache-web-app-howto/cache-teams-index-table.png
[cache-status-message]: ./media/cache-web-app-howto/cache-status-message.png
[deploybutton]: ./media/cache-web-app-howto/deploybutton.png
[cache-deploy-to-azure-step-1]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-1.png
[cache-deploy-to-azure-step-2]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-2.png
[cache-deploy-to-azure-step-3]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-3.png
[cache-deployment-started]: ./media/cache-web-app-howto/cache-deployment-started.png
[cache-publish-app]: ./media/cache-web-app-howto/cache-publish-app.png
[cache-publish-to-app-service]: ./media/cache-web-app-howto/cache-publish-to-app-service.png
[cache-select-web-app]: ./media/cache-web-app-howto/cache-select-web-app.png
[cache-publish]: ./media/cache-web-app-howto/cache-publish.png
[cache-delete-resource-group]: ./media/cache-web-app-howto/cache-delete-resource-group.png
[cache-delete-confirm]: ./media/cache-web-app-howto/cache-delete-confirm.png

