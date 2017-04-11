<properties 
    pageTitle="彈性的資料庫用戶端文件庫使用實體架構 |Microsoft Azure" 
    description="用於彈性的資料庫用戶端文件庫和實體架構編碼資料庫" 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="torsteng" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="torsteng"/>

# <a name="elastic-database-client-library-with-entity-framework"></a>實體架構彈性資料庫的用戶端文件庫 
 
這份文件與[彈性的資料庫工具](sql-database-elastic-scale-introduction.md)整合所需的實體架構應用程式中顯示的變更。 焦點會放在撰寫[晶怪對應管理](sql-database-elastic-scale-shard-map-management.md)及[資料而異路由](sql-database-elastic-scale-data-dependent-routing.md)實體架構**的程式碼第一**種方法。 [程式碼第一個 – 新資料庫](http://msdn.microsoft.com/data/jj193542.aspx)教學課程中的 EF 做為範例來說執行整個這份文件。 隨附這份文件的範例程式碼是彈性的資料庫工具設定的 Visual Studio 程式碼範例中的範例。
  
## <a name="downloading-and-running-the-sample-code"></a>下載並執行程式碼範例
若要下載這份文件的程式碼︰

* 需要 visual Studio 2012 或更新版本。 
* 啟動 [Visual Studio。 
* 在 Visual Studio 中，選取 [檔案]-> [新的專案。 
* 在 [新專案] 對話方塊中，瀏覽至**線上範例**的**Visual C#** ]，然後輸入 「 彈性 db 」 到右上角的 [搜尋] 方塊。
    
    ![實體架構和彈性的資料庫範例應用程式][1] 

    選取 [樣本稱為**Azure SQL – 實體架構整合彈性資料庫工具**]。 接受授權之後, 會載入範例。 

若要執行的範例，您需要 Azure SQL 資料庫中建立三個空白資料庫︰

* 晶怪對應 Manager 資料庫
* 晶怪 1 資料庫
* 晶怪 2 資料庫

一旦您建立這些資料庫，填入預留位置中**Program.cs** Azure SQL 資料庫伺服器名稱、 資料庫名稱與您的認證以連線至資料庫。 建立 Visual Studio 中的解決方案。 Visual Studio 會下載的必要的 NuGet 套件的彈性的資料庫用戶端文件庫的實體架構及暫時性錯誤的處理建立程序的一部分。 請確定還原 NuGet 套件已啟用您的方案。 您可以啟用此設定，以滑鼠右鍵按一下方案檔案 Visual Studio 方案總管] 中。 

## <a name="entity-framework-workflows"></a>實體架構工作流程 

實體架構開發人員使用下列的四個工作流程來建立應用程式，並確定應用程式物件保存其中一項︰ 

* **程式碼第一個 （新的資料庫）**︰ EF 開發人員建立模型中的應用程式碼並將然後 EF 產生從該資料庫。 
* **程式碼第一個 （現有的資料庫）**︰ 開發人員可讓 EF 產生模型的應用程式碼，從現有的資料庫。
* **第一個模型**︰ 開發人員 EF 設計工具中建立模型，然後 EF 會建立資料庫的模型。
* **第一個資料庫**︰ 開發人員使用 EF 工具推斷從現有的資料庫的模型。 

這些方法都依賴 DbContext 類別以無障礙管理資料庫連線和應用程式的資料庫結構描述。 我們會在討論在文件，稍後詳細 DbContext 基底類別中的不同建構函式允許不同層級的控制連線建立資料庫啟動和結構描述建立。 挑戰因主要 EF 所提供的資料庫連線管理相交資料相依路由介面所提供的連線管理功能的彈性的資料庫用戶端程式庫。 

## <a name="elastic-database-tools-assumptions"></a>彈性的資料庫工具假設 

字詞定義，請參閱[彈性的資料庫工具詞彙](sql-database-elastic-scale-glossary.md)。

與彈性的資料庫用戶端文件庫，您可以定義稱為 shardlets 的應用程式資料的磁碟分割。 Shardlets 由 sharding 鍵，並會對應到特定的資料庫。 應用程式可能會有任何數量的資料庫，視需要和發佈 shardlets 提供足夠的容量或指定目前的業務需求的效能。 資料庫的 sharding 關鍵值對應儲存所提供的彈性的資料庫用戶端 Api 晶怪地圖。 我們可以連絡**晶怪地圖管理**或 SMM 此功能的簡短。 代理人的資料庫連線的要求執行 sharding 鍵，同時也能晶怪地圖。 我們會將為**資料而異路由**這項功能。 
 
晶怪地圖管理員防止不一致的檢視 shardlet 資料時所發生的同時 shardlet 管理作業 （例如重新資料從一個晶怪放置到另一個） 可能發生的使用者。 若要這麼做，晶怪地圖管理用戶端文件庫代理人應用程式的資料庫連線。 這個選項可讓晶怪地圖功能晶怪管理作業可能會影響所建立的連線 shardlet 時自動刪除資料庫連線。 此方法需要整合與 EF 的功能，例如從現有檢查資料庫有建立新的連線。 一般而言，我們觀察已標準 DbContext 建構函式只公司可靠的已關閉的資料庫連線的安全地來複製 EF 的工作。 彈性設計原則，而是資料庫的只仲介處理開啟的連線。 一個可能會想關閉之前交出 EF DbContext 以用戶端程式庫所代理的連線可能會解決此問題。 不過，關閉連線並依賴 EF 重新開啟，其中一個 foregoes 驗證和一致性檢查執行的文件庫。 移轉中的功能 EF，不過，使用這些連線管理基礎的資料庫結構描述會影響應用程式的方式。 理想的情況下，我們想要保留合併的所有功能彈性的資料庫用戶端文件庫與 EF 相同的應用程式。 下節說明這些屬性和的更多詳細資料的需求。 


## <a name="requirements"></a>需求 

使用 [彈性的資料庫用戶端文件庫] 和 [實體架構 Api 工作時，我們想要保留下列屬性︰ 

* **擴充**︰ 若要新增或移除應用程式的 sharded 視容量要求應用程式的資料層的資料庫。 這表示控制項上建立及刪除資料庫，然後使用彈性的資料庫晶怪對應管理資料庫及對應的 shardlets 管理員 Api。 

* **一致性**︰ 採用 sharding，應用程式，並使用資料相依路由功能的用戶端文件庫。 若要避免損毀或錯誤的查詢結果，連線被代理透過晶怪地圖管理員。 這也會保留驗證和一致性。
 
* **第一個程式碼**︰ 若要保留的 EF 的程式碼的第一個架構便利性。 在 [程式碼首先，應用程式中的類別對應透明到基礎資料庫結構。 應用程式碼會與遮罩與有關的基礎資料庫處理大部分層面的 DbSets 互動。
 
* **結構描述**︰ 初始資料庫結構描述建立和移轉到後續的結構描述功用的實體架構控點。 保留這些功能，以調整您的應用程式可以輕鬆隨著資料發展。 

以下提供的指導方針指示符合這些需求使用彈性的資料庫工具的程式碼第一個應用程式的方式。 

## <a name="data-dependent-routing-using-ef-dbcontext"></a>取決於路由使用 EF DbContext 資料 

實體架構的資料庫連線通常是透過**DbContext**的管理。 藉由衍生自**DbContext**建立這些子類別。 這是您定義實作資料庫備份您的應用程式的 CLR 物件集合您**DbSets**的位置。 在資料相依傳閱的內容中，我們可以找出幾個很有幫助的屬性，不需要保留其他 EF 程式碼第一個應用程式案例︰ 

* 資料庫已經存在，而且已經註冊彈性的資料庫晶怪對應中。 
* 已經部署的應用程式的結構描述至資料庫 （如下所述）。 
* 資料而異路由連線至資料庫所代理的晶怪地圖。 

若要整合**DbContexts**擴充的資料而異路由︰

1. 建立實體資料庫連線透過彈性的資料庫用戶端介面晶怪地圖管理員 
2. 自動換行與**DbContext**子類別的連線
3. 向下傳入**DbContext**基底類別，以確保 EF 端的所有處理也會都發生連線。 

下列範例說明這個方法。 （將此程式碼也是隨附的 Visual Studio 專案中）

    public class ElasticScaleContext<T> : DbContext
    {
    public DbSet<Blog> Blogs { get; set; }
    …

        // C'tor for data dependent routing. This call will open a validated connection 
        // routed to the proper shard by the shard map manager. 
        // Note that the base class c'tor call will fail for an open connection
        // if migrations need to be done and SQL credentials are used. This is the reason for the 
        // separation of c'tors into the data-dependent routing case (this c'tor) and the internal c'tor for new shards.
        public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
            : base(CreateDDRConnection(shardMap, shardingKey, connectionStr), 
            true /* contextOwnsConnection */)
        {
        }

        // Only static methods are allowed in calls into base class c'tors.
        private static DbConnection CreateDDRConnection(
        ShardMap shardMap, 
        T shardingKey, 
        string connectionStr)
        {
            // No initialization
            Database.SetInitializer<ElasticScaleContext<T>>(null);

            // Ask shard map to broker a validated connection for the given key
            SqlConnection conn = shardMap.OpenConnectionForKey<T>
                                (shardingKey, connectionStr, ConnectionOptions.Validate);
            return conn;
        }    

## <a name="main-points"></a>主要重點
* 新增一個取代 DbContext 子類別中的預設建構函式 
* 新的建構函式所需的資料相依路由透過彈性的資料庫用戶端文件庫的引數︰
    * 若要存取資料而異路由介面，晶怪地圖
    * 若要識別 shardlet，sharding 索引鍵
    * 使用資料而異路由連線晶怪認證連線字串。 
 
* 基底類別建構函式通話納入 detour 執行所有步驟的靜態方法所需的資料而異路由。 
   * 它會建立開啟連線晶怪地圖上使用彈性的資料庫用戶端介面 OpenConnectionForKey 的通話。
   * 晶怪地圖建立保留指定的 sharding 鍵 shardlet 晶怪開啟連線。
   * 此開啟連線則會傳回以表示此連線所要使用，而不是讓 EF 自動建立的新連線 EF DbContext 的基本類別建構函式。 這種方式連線已經加上彈性的資料庫用戶端 API，讓它可以保證下晶怪地圖管理作業的一致性。
 
  
使用新的建構函式 DbContext 子類別中，而不是在程式碼中的預設建構函式。 以下是範例︰ 

    // Create and save a new blog.

    Console.Write("Enter a name for a new blog: "); 
    var name = Console.ReadLine(); 

    using (var db = new ElasticScaleContext<int>( 
                            sharding.ShardMap,  
                            tenantId1,  
                            connStrBldr.ConnectionString)) 
    { 
        var blog = new Blog { Name = name }; 
        db.Blogs.Add(blog); 
        db.SaveChanges(); 

        // Display all Blogs for tenant 1 
        var query = from b in db.Blogs 
                    orderby b.Name 
                    select b; 
     … 
    }

新的建構函式隨即會開啟晶怪保留**tenantid1**的值來識別 shardlet 資料的連線。 **使用**區塊中的程式碼皆會保持不變，存取**DbSet** EF 晶怪上用**tenantid1**的部落格的。 這會變更中使用的程式碼的語意封鎖所有資料庫作業現在都限於存放**tenantid1**一個晶怪。 舉例來說，在部落格**DbSet** LINQ 查詢只傳回部落格儲存在目前的晶怪，但並不會儲存在其他擊碎上的。  

#### <a name="transient-faults-handling"></a>暫時錯誤處理
Microsoft 模式與作法小組發佈[暫時性錯誤處理應用程式區塊](https://msdn.microsoft.com/library/dn440719.aspx)。 文件庫使用彈性的縮放比例用戶端搭配 EF 的文件庫。 不過，確保任何暫時性的例外狀況傳回位置，我們可以確保，新建構函式正在使用的暫時性錯誤之後，好讓任何新連線嘗試使用我們有強制建構函式的位置。 否則，不一定正確晶怪的連線，且不保證發生變更晶怪對應至維持連線。 

下列程式碼範例說明如何使用 SQL 重試原則，周圍的新**DbContext**子類別建構函式︰ 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
    { 
        using (var db = new ElasticScaleContext<int>( 
                                sharding.ShardMap,  
                                tenantId1,  
                                connStrBldr.ConnectionString)) 
            { 
                    var blog = new Blog { Name = name }; 
                    db.Blogs.Add(blog); 
                    db.SaveChanges(); 
            … 
            } 
        }); 

**SqlDatabaseUtils.SqlRetryPolicy**上述程式碼中定義為 10 和 5 的秒數等待時間重試之間重試算**SqlDatabaseTransientErrorDetectionStrategy** 。 此方法很類似於指導 EF 和使用者啟動的交易 （請參閱[重試一次執行策略 (這時候開始 EF6) 的限制](http://msdn.microsoft.com/data/dn307226)。 兩個情況需要應用程式控制項暫時性的例外狀況會傳回的範圍︰ 若要重新開啟交易，或是 （如下） 重新建立的內容從適當的建構函式使用的彈性的資料庫用戶端文件庫。

若要控制位置暫時性的例外狀況回範圍帶我們需要也會妨礙隨附 EF 內建**SqlAzureExecutionStrategy**使用。 **SqlAzureExecutionStrategy**想重新開啟連線，但不是能使用**OpenConnectionForKey**及因此略過**OpenConnectionForKey**通話的部分做為執行的所有驗證。 相反地，程式碼範例會使用內建**DefaultExecutionStrategy**隨附也 EF。 而不是**SqlAzureExecutionStrategy**，其運作方式正確暫時性錯誤處理重試原則的組合。 執行原則設定**ElasticScaleDbConfiguration**類別中。 請注意，我們決定不使用**DefaultSqlExecutionStrategy** ，因為使用**SqlAzureExecutionStrategy**是否發生暫時性的例外狀況-導致錯誤的行為所述的建議。 如需有關不同重試原則和 EF 的詳細資訊，請參閱[連線恢復 EF 中](http://msdn.microsoft.com/data/dn456835.aspx)。     

#### <a name="constructor-rewrites"></a>建構函式重
上述的程式碼範例說明預設建構函式重寫所需的應用程式，才能使用相依路由實體架構的資料。 下表可以產生其他建構函式這種方法。 


目前的建構函式  | 資料重新寫入建構函式 | 基底建構函式 | 備忘稿
---------- | ----------- | ------------|----------
MyContext() |ElasticScaleContext （ShardMap、 TKey） |DbContext (DbConnection bool) |連線必須晶怪地圖與資料而異路由索引鍵的函數。 您需要 EF by-pass 自動連接建立，請改為使用晶怪對應仲介處理連線。 
MyContext(string)|ElasticScaleContext （ShardMap、 TKey） |DbContext (DbConnection bool) |晶怪地圖和資料而異路由鍵函數表示之連線。 固定的資料庫名稱或連線字串就不能成為其晶怪對應 by-pass 驗證。 
MyContext(DbCompiledModel) |ElasticScaleContext (ShardMap，TKey，DbCompiledModel) |DbContext (DbConnection，DbCompiledModel，bool) |使用提供的模型連線會建立指定晶怪地圖和 sharding 鍵。 已編譯的模型會傳遞給基底 c'tor。
MyContext (DbConnection bool) |ElasticScaleContext (ShardMap，TKey，bool) |DbContext (DbConnection bool) |連線需要推斷晶怪地圖與索引鍵。 其無法提供的輸入 （除非晶怪地圖和金鑰已使用該輸入）。 布林值會傳遞。 
MyContext （字串，DbCompiledModel） |ElasticScaleContext (ShardMap，TKey，DbCompiledModel) |DbContext (DbConnection，DbCompiledModel，bool) |連線需要推斷晶怪地圖與索引鍵。 其無法提供的輸入 （除非晶怪地圖與索引鍵，使用該輸入）。 已編譯的模型會傳遞。 
MyContext (ObjectContext bool) |ElasticScaleContext (ShardMap TKey，ObjectContext，bool) |DbContext (ObjectContext bool) |新的建構函式必須以確保傳遞 ObjectContext 中的任何連線會重新連線到受彈性的小數位數的連線。 詳細的討論 ObjectContexts 是這份文件的範圍之內。
MyContext (DbConnection，DbCompiledModel，bool) |ElasticScaleContext (ShardMap TKey，DbCompiledModel，bool)| DbContext (DbConnection，DbCompiledModel，bool); |連線需要推斷晶怪地圖與索引鍵。 （除非晶怪地圖和金鑰已使用該輸入），無法提供連線的輸入。 模型和布林值傳遞至基底類別建構函式。 

## <a name="shard-schema-deployment-through-ef-migrations"></a>透過 EF 移轉晶怪結構描述部署 

提供的實體架構便利性自動結構描述管理。 在使用彈性的資料庫工具應用程式中，我們想要保留資料庫會新增至 sharded 應用程式時，自動佈建的結構描述至新建立的擊碎這項功能。 主要的使用案例是增加 sharded 使用的應用程式 EF 資料層級的容量。 依賴結構描述管理 EF 的功能與 sharded 應用程式內建在 EF 減少資料庫管理工作。 

透過 EF 移轉的結構描述部署適合**未開啟的連線**。 這相較於資料相依案例路由所使用的彈性的資料庫用戶端 API 所提供的已開啟連線。 另一個差別一致性要求︰ 時希望確保所有的資料而異路由連線到防範同時晶怪地圖操作的一致性，它不會區分初始的結構描述部署到新的資料庫中有不在登錄中晶怪地圖，還及尚未已配置來保留 shardlets。 我們可以因此依賴一般資料庫連線，此情況下，而不是路由資料而異。  

這會導致一種方法其中結構描述部署 EF 移轉到會緊密之後新資料庫的註冊為應用程式的晶怪對應中晶怪。 這需要下列先決條件︰ 

* 已建立資料庫。 
* 資料庫是空值，它會保留沒有使用者結構描述和任何使用者資料。
* 資料庫還無法透過彈性的資料庫用戶端 Api 路由相關資料的存取。 

使用就地這些必要條件，我們可以建立以開始結構描述部署 EF 移轉正常未讀取的**典型**。 下列程式碼範例說明這個方法。 

        // Enter a new shard - i.e. an empty database - to the shard map, allocate a first tenant to it  
        // and kick off EF intialization of the database to deploy schema 

        public void RegisterNewShard(string server, string database, string connStr, int key) 
        { 

            Shard shard = this.ShardMap.CreateShard(new ShardLocation(server, database)); 

            SqlConnectionStringBuilder connStrBldr = new SqlConnectionStringBuilder(connStr); 
            connStrBldr.DataSource = server; 
            connStrBldr.InitialCatalog = database; 

            // Go into a DbContext to trigger migrations and schema deployment for the new shard. 
            // This requires an un-opened connection. 
            using (var db = new ElasticScaleContext<int>(connStrBldr.ConnectionString)) 
            { 
                // Run a query to engage EF migrations 
                (from b in db.Blogs 
                    select b).Count(); 
            } 

            // Register the mapping of the tenant to the shard in the shard map. 
            // After this step, data-dependent routing on the shard map can be used 

            this.ShardMap.CreatePointMapping(key, shard); 
        } 
 

此範例顯示**RegisterNewShard**晶怪對應中登錄晶怪、 部署 EF 移轉時，透過的結構描述和儲存晶怪 sharding 索引鍵的對應的方法。 它依賴採用 SQL 連線字串做為輸入**DbContext**子類別 (樣本中的**ElasticScaleContext** ) 的一個。 這個建構函式的程式碼是直接的如下列範例所示︰ 


        // C'tor to deploy schema and migrations to a new shard 
        protected internal ElasticScaleContext(string connectionString) 
            : base(SetInitializerForConnection(connectionString)) 
        { 
        } 

        // Only static methods are allowed in calls into base class c'tors 
        private static string SetInitializerForConnection(string connnectionString) 
        { 
            // We want existence checks so that the schema can get deployed 
            Database.SetInitializer<ElasticScaleContext<T>>( 
        new CreateDatabaseIfNotExists<ElasticScaleContext<T>>()); 

            return connnectionString; 
        } 
 
一個可能使用繼承自基底類別建構函式的版本。 但需要確保 EF 預設初始連線時使用的程式碼。 因此簡短 detour 到之前的連接字串的基本類別建構函式呼叫靜態的方法。 請注意，不同的應用程式網域或程序，以確保 EF 初始設定的設定沒有衝突應該執行的擊碎註冊。 


## <a name="limitations"></a>限制 

這份文件中所述的方法執行幾個限制︰ 

* 使用**LocalDb** EF 應用程式首先要移轉到一般的 SQL Server 資料庫，再使用彈性的資料庫用戶端文件庫。 彈性的比例縮放出 sharding 應用程式不能使用**LocalDb**。 請注意，開發仍然可以使用**LocalDb**。 

* 需要上所有的擊碎可幫助 EF 移轉應用程式會暗示資料庫結構描述變更的任何變更。 這份文件的範例程式碼不會示範如何進行此動作。 請考慮使用連接字串參數的更新資料庫，逐一查看所有擊碎;或擷取 T SQL 指令碼中使用更新資料庫的移轉與 – 指令碼] 選項，並將 T SQL 指令碼套用至您擊碎。  

* 指定要求，則假設的所有資料庫處理包含單一晶怪由要求所提供的 sharding 金鑰。 不過，這個假設並未永遠儲存，則為 true。 例如，當不可能提供 sharding 鍵。 若要解決這個問題，用戶端程式庫提供實作透過數種擊碎查詢連線抽象**MultiShardQuery**類別。 學習使用**MultiShardQuery**搭配 EF 已超出這份文件的範圍

## <a name="conclusion"></a>結論

此文件中所述的步驟，透過 EF 應用程式可以使用資料相依路由重構建構函式 EF 應用程式中使用**DbContext**子類別的彈性的資料庫用戶端文件庫的功能。 這會限制**DbContext**類別已經存在的這些部分所需的變更。 此外，EF 應用程式可以繼續受益自動結構描述部署，藉由組合叫用新的擊碎和晶怪對應中的對應的登錄必要 EF 移轉的步驟。 


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-use-entity-framework-applications-visual-studio/sample.png
 