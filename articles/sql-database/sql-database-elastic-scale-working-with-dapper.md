<properties 
    pageTitle="彈性的資料庫用戶端文件庫使用 Dapper |Microsoft Azure" 
    description="使用 Dapper 彈性的資料庫用戶端文件庫。" 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="torsteng"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="torsteng"/>

# <a name="using-elastic-database-client-library-with-dapper"></a>使用 Dapper 彈性的資料庫用戶端文件庫 

這份文件是開發人員所依賴 Dapper 建置應用程式，但也會想要運用[彈性的資料庫工具](sql-database-elastic-scale-introduction.md)建立實作 sharding 來擴充其資料層的應用程式。  這份文件說明 Dapper 為基礎的應用程式所需整合與彈性的資料庫工具中的變更。 我們焦點在撰寫的彈性的資料庫晶怪管理及資料相依路由處理 Dapper。 

**範例**︰ [Azure SQL 資料庫 Dapper 整合彈性的資料庫工具](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-e19fc77f)。
 
Azure SQL 資料庫整合**Dapper**和**DapperExtensions**與彈性的用戶端資料庫很容易。 您的應用程式可以使用取決於路由變更建立並開啟新的[典型](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx)物件的資料，來使用[OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx)通話從[用戶端文件庫](http://msdn.microsoft.com/library/azure/dn765902.aspx)。 此限制變更為 [只有應用程式中的，其中會建立新連線，並將其開啟。 

## <a name="dapper-overview"></a>Dapper 概觀
**Dapper**是物件關聯的對應程式。 它會對應.NET 物件從應用程式至關聯式資料庫 （反之亦然）。 範例程式碼的第一個部分會說明如何將彈性的用戶端資料庫整合 Dapper 為基礎的應用程式。 第二部分範例說明如何整合時使用 Dapper 和 DapperExtensions。  

在 Dapper 中的對應程式功能提供簡化提交 T SQL 陳述式的執行查詢的資料庫的資料庫連線延伸方法。 例如，Dapper 可輕鬆對應的.NET 物件之間**執行**通話的 SQL 陳述式的參數，或使用**查詢**來電 Dapper.NET 物件到消耗 SQL 查詢的結果。 

使用 DapperExtensions，當您不再需要提供的 SQL 陳述式。 例如**GetList**或**插入**的副檔名方法，透過資料庫連線建立 SQL 陳述式的詳細說明。
 
Dapper 以及 DapperExtensions 的另一個優點是應用程式控制項建立的資料庫連線。 這可協助互動經紀人資料庫連線根據 shardlets 至資料庫的對應的彈性的資料庫用戶端程式庫。

若要取得 Dapper 的組件，請參閱[Dapper 點網路](http://www.nuget.org/packages/Dapper/)。 Dapper 擴充功能，請參閱[DapperExtensions](http://www.nuget.org/packages/DapperExtensions)。

## <a name="a-quick-look-at-the-elastic-database-client-library"></a>彈性的資料庫用戶端文件庫簡介

與彈性的資料庫用戶端文件庫中，定義稱為*shardlets*應用程式資料的磁碟分割區、 其對應到資料庫]，並依*sharding 索引鍵*會將其識別。 您可以有多資料庫，您需要您 shardlets 分散這些資料庫。 資料庫的 sharding 關鍵值對應儲存所提供的文件庫的 Api 晶怪地圖。 這項功能稱為**晶怪對應管理**。 代理人的資料庫連線的要求執行 sharding 鍵，同時也能晶怪地圖。 此功能被稱為**資料相依路由**。

![晶怪地圖與資料相依路由][1]

晶怪地圖管理員防止不一致的檢視 shardlet 資料同時 shardlet 管理作業所發生的資料庫時可能發生的使用者。 若要這麼做，晶怪地圖仲介處理資料庫的連線與文件庫內建應用程式。 當晶怪管理作業可能會影響 shardlet 時，這可讓晶怪地圖功能會自動刪除資料庫連線。 

而不是使用傳統方式建立的 Dapper 連線，我們需要使用[OpenConnectionForKey 方法](http://msdn.microsoft.com/library/azure/dn824099.aspx)。 這可確保所有驗證都發生，而連線的受管理的正確時擊碎間移動的任何資料。

### <a name="requirements-for-dapper-integration"></a>Dapper 整合需求

使用的彈性的資料庫用戶端文件庫和 Dapper Api 工作時，我們想要保留下列屬性︰

* **Scaleout**︰ 我們想要新增或移除應用程式的 sharded 視容量要求應用程式的資料層的資料庫。 

-    **一致性**︰ 我們的應用程式橫向使用 sharding，因為我們需要執行資料相依路由。 我們想要使用的資料相依路由功能的文件庫執行此作業。 特別是我們想要保留的驗證及一致性保證所提供的代理透過晶怪地圖管理員，以避免損毀或錯誤的查詢結果的連線。 這樣會拒絕連線至指定的 shardlet，或如果 shardlet 目前 （例如） 移至不同的晶怪，使用分割/合併 Api 停止。

-    **物件對應**︰ 我們想要保留的應用程式中的類別和基礎資料庫結構之間翻譯 Dapper 所提供的對應便利性。 

下一節提供這些需求，根據**Dapper**和**DapperExtensions**應用程式的指引。

## <a name="technical-guidance"></a>技術指南
### <a name="data-dependent-routing-with-dapper"></a>取決於路由 Dapper 處理資料 

應用程式 Dapper，就通常負責建立和開啟基礎資料庫的連線。 指定 T 型別應用程式，Dapper 傳回查詢結果類型 T Dapper 的.NET 集合執行從 T SQL 結果列對應的物件類型 t。同樣地，Dapper 會對應到 SQL 值或資料操作語言 (DML) 陳述式的參數.NET 物件。 Dapper 提供這項功能，透過延伸一般[典型](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx)物件從 ADO.NET SQL 用戶端文件庫上的方法。 彈性的縮放比例 Api 所傳回的 DDR SQL 連線，也會定期[典型](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx)物件。 這個選項可讓我們使用 Dapper 副檔名直接在用戶端程式庫 DDR API，所傳回類型為也很簡單的 SQL 用戶端連線時。

這些觀察值，請直接使用 Dapper 的彈性的資料庫用戶端程式庫所代理的連線。

（從隨附的範例） 此程式碼範例說明的方法由應用程式的文件庫以仲介處理右晶怪連線所提供的 sharding 鍵。   

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                     key: tenantId1, 
                     connectionString: connStrBldr.ConnectionString, 
                     options: ConnectionOptions.Validate))
    {
        var blog = new Blog { Name = name };
        sqlconn.Execute(@"
                      INSERT INTO
                            Blog (Name)
                            VALUES (@name)", new { name = blog.Name }
                        );
    }

[OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) API 通話會取代預設建立和開啟的 SQL 用戶端連線。 [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx)通話會採用所需的資料相依路由引數︰ 

-    若要存取資料相依路由介面晶怪地圖
-    若要識別 shardlet sharding 索引鍵
-    認證 （使用者名稱和密碼） 連線到晶怪

晶怪地圖物件建立保留指定的 sharding 鍵 shardlet 晶怪的連線。 彈性的資料庫用戶端 Api 也標記實作其一致性保證的連線。 由於[OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx)通話會傳回一般 SQL 用戶端連線物件，從 Dapper 後續通話**執行**擴充方法遵循標準 Dapper 練習。

查詢的運作方式非常相同 – 您第一次開啟使用[OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx)從用戶端 API 的連線。 然後您可以使用一般 Dapper 擴充方法對應到.NET 物件的 SQL 查詢的結果︰

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId1, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate ))
    {    
           // Display all Blogs for tenant 1
           IEnumerable<Blog> result = sqlconn.Query<Blog>(@"
                                SELECT * 
                                FROM Blog
                                ORDER BY Name");

           Console.WriteLine("All blogs for tenant id {0}:", tenantId1);
           foreach (var item in result)
           {
                Console.WriteLine(item.Name);
            }
    }

請注意，**使用**封鎖 DDR 連線的範圍內存放 tenantId1 一個晶怪區塊的所有資料庫作業。 部落格儲存在目前的晶怪，但並不會儲存在其他擊碎的只會傳回查詢。 

## <a name="data-dependent-routing-with-dapper-and-dapperextensions"></a>取決於路由 Dapper 與 DapperExtensions 資料

Dapper 隨附生態額外的延伸，可以進一步便利性及抽象從資料庫時提供開發資料庫應用程式。 DapperExtensions 是範例。 

應用程式中使用 DapperExtensions 不會變更如何建立及管理資料庫連線。 仍開啟連線的應用程式的責任和一般 SQL 用戶端連線物件預期的擴充方法。 我們可以依賴[OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) ，如以上所述。 下列程式碼範例顯示，請只變更是我們不再需要撰寫 T SQL 陳述式︰

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate))
    {
           var blog = new Blog { Name = name2 };
           sqlconn.Insert(blog);
    }

然後，以下是查詢的程式碼範例︰ 

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate))
    {
           // Display all Blogs for tenant 2
           IEnumerable<Blog> result = sqlconn.GetList<Blog>();
           Console.WriteLine("All blogs for tenant id {0}:", tenantId2);
           foreach (var item in result)
           {
               Console.WriteLine(item.Name);
           }
    }

### <a name="handling-transient-faults"></a>處理暫時錯誤

Microsoft 模式與作法小組發佈[暫時性錯誤處理應用程式區塊](http://msdn.microsoft.com/library/hh680934.aspx)以協助降低在雲端中執行時，會遇到的常見暫時性故障條件的應用程式開發人員。 如需詳細資訊，請參閱[堅持不懈、 所有勝利私人︰ 使用暫時性錯誤處理應用程式區塊](http://msdn.microsoft.com/library/dn440719.aspx)。

程式碼範例依賴來防範暫時錯誤的暫時性故障文件庫。 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
    {
       using (SqlConnection sqlconn = 
          shardingLayer.ShardMap.OpenConnectionForKey(tenantId2, connStrBldr.ConnectionString, ConnectionOptions.Validate))
          {
              var blog = new Blog { Name = name2 };
              sqlconn.Insert(blog);
          }
    });

**SqlDatabaseUtils.SqlRetryPolicy**上述程式碼中定義為 10 和 5 的秒數等待時間重試之間重試算**SqlDatabaseTransientErrorDetectionStrategy** 。 如果您使用交易，請確定您重試範圍返回至暫時性錯誤交易的開頭。

## <a name="limitations"></a>限制

這份文件中所述的方法執行幾個限制︰

* 這份文件的範例程式碼不會示範如何管理擊碎結構描述。
* 我們提供要求，假設其所有資料庫處理都包含在單一晶怪，以要求所提供的 sharding 金鑰。 不過，這個假設並未永遠維持，，例如，如果不提供 sharding 鍵。 若要解決這個問題，彈性的資料庫用戶端文件庫包含[MultiShardQuery 類別](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardexception.aspx)。 類別實作透過數種擊碎查詢連線抽象。 使用 MultiShardQuery 搭配 Dapper 是這份文件的範圍之內。

## <a name="conclusion"></a>結束時

應用程式使用 Dapper 和 DapperExtensions 可以輕鬆地受益彈性的資料庫工具 Azure SQL 資料庫。 透過此文件中所述的步驟，這些應用程式可用於工具的功能取決於路由變更建立並開啟新的[典型](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx)物件的資料使用[OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx)通話的彈性的資料庫用戶端文件庫。 這會限制應用程式所需的變更至這些是位置建立新的連線，將其開啟的位置。 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-working-with-dapper/dapperimage1.png
 