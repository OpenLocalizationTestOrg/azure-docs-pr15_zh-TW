<properties 
    pageTitle="管理認證的彈性的資料庫用戶端文件庫中 |Microsoft Azure" 
    description="如何設定正確的層級認證，為唯讀，彈性的資料庫應用程式的管理員" 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="ddove" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="ddove"/>

# <a name="credentials-used-to-access-the-elastic-database-client-library"></a>用來存取彈性的用戶端資料庫的認證

[彈性的資料庫用戶端文件庫](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)可用於三種不同的認證存取[晶怪地圖管理員](sql-database-elastic-scale-shard-map-management.md)。 根據需要，請下層的存取權可能與使用認證。

* **管理認證**︰ 建立或管理晶怪地圖管理員。 （請參閱[字彙](sql-database-elastic-scale-glossary.md)。） 
* **存取認證**︰ 若要存取現有晶怪地圖管理員以取得擊碎的相關資訊。
* **連線認證**︰ 連線至擊碎。 

另請參閱[管理資料庫及 Azure SQL 資料庫中的登入](sql-database-manage-logins.md)。 
 
## <a name="about-management-credentials"></a>瞭解管理認證

若要建立[**ShardMapManager**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx)物件的操作晶怪地圖服務應用程式會使用管理認證。 （例如，請參閱[新增使用彈性的資料庫工具晶怪](sql-database-elastic-scale-add-a-shard.md)及[資料相依路由](sql-database-elastic-scale-data-dependent-routing.md)）彈性的縮放比例用戶端文件庫的使用者建立 SQL 使用者和 SQL 登入，並確定每個授與全域晶怪地圖資料庫及所有晶怪資料庫的讀/寫權限。 若要維持全域晶怪地圖與本機晶怪地圖執行晶怪地圖的變更時，會使用這些認證。 例如，使用 [管理認證建立晶怪地圖管理員物件 (使用[**GetSqlShardMapManager**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx): 

    // Obtain a shard map manager. 
    ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
            smmAdminConnectionString, 
            ShardMapManagerLoadPolicy.Lazy 
    ); 

變數**smmAdminConnectionString**是包含管理認證的連線字串。 使用者識別碼和密碼提供晶怪地圖資料庫和個別擊碎讀/寫權。 管理連接字串也包含的伺服器名稱及來識別全域晶怪地圖資料庫的資料庫名稱。 以下是一般的連接字串的用途︰

     "Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;” 

執行不使用的表單中的值"username@server"—instead就是使用 「 使用者名稱] 值。  這是因為認證必須針對晶怪地圖 manager 資料庫和可能會在不同伺服器的個別擊碎工作。

## <a name="access-credentials"></a>Access 的認證
  
在建立晶怪地圖管理員無法管理晶怪地圖的應用程式中，使用全域晶怪地圖有唯讀權限的認證。 從 [這些認證] 底下的全域晶怪對應擷取的資訊用於[資料而異路由](sql-database-elastic-scale-data-dependent-routing.md)和填入晶怪地圖快取在用戶端。 認證會提供相同的通話模式透過**GetSqlShardMapManager**如上所示︰ 

    // Obtain shard map manager. 
    ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
            smmReadOnlyConnectionString, 
            ShardMapManagerLoadPolicy.Lazy
    );  

請注意，以反映代表**非系統管理**使用者存取權的不同的認證使用**smmReadOnlyConnectionString**使用︰ 這些憑證不應提供全域晶怪地圖上的寫入權限。 

## <a name="connection-credentials"></a>連線的認證 

若要存取與 sharding 索引鍵關聯晶怪使用[**OpenConnectionForKey**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx)方法時需要其他的憑證。 這些認證必須提供的本機晶怪地圖資料表位於晶怪的唯讀存取權限。 這需要執行連線驗證晶怪路由資料而異。 此程式碼片段允許的資料相依路由內容中的資料存取︰ 
 
    using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>( 
    targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate)) 

在此範例中， **smmUserConnectionString**保留使用者認證的連接字串。 為 Azure SQL 資料庫，以下是一般的連接字串使用者認證︰ 

    "User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  

在表單中的不執行值與管理員認證， "username@server"。 不過，只要使用 「 使用者名稱 」。  另請注意伺服器名稱] 和 [資料庫名稱，並不會包含的連接字串。 這是因為**OpenConnectionForKey**通話會自動將正確的晶怪根據索引鍵的連線。 因此，不提供的資料庫名稱和伺服器名稱。 

## <a name="see-also"></a>另請參閱
[管理資料庫及 Azure SQL 資料庫中的登入](sql-database-manage-logins.md)

[保護您的 SQL 資料庫](sql-database-security.md)

[彈性的資料庫工作快速入門](sql-database-elastic-jobs-getting-started.md)

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 