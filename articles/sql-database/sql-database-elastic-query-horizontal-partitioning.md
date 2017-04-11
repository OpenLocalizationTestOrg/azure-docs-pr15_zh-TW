<properties
    pageTitle="縮放出雲端資料庫之間的報告 |Microsoft Azure"
    description="如何設定在水平分割的彈性的查詢"    
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
    ms.author="torsteng" />

# <a name="reporting-across-scaled-out-cloud-databases-preview"></a>報告跨縮放出雲端資料庫 （預覽版本）

![跨擊碎查詢][1]

Sharded 資料庫分散出不按比例縮放的資料列層。 結構描述是所有參與的資料庫，也就是水平分割完全相同。 您可以使用的彈性的查詢，來建立跨越 sharded 資料庫中的所有資料庫的報表。

快速入門中，請參閱[調整出雲端資料庫之間的報告](sql-database-elastic-query-getting-started.md)。

非 sharded 資料庫，請參閱[在使用不同的結構描述的雲端資料庫間的查詢](sql-database-elastic-query-vertical-partitioning.md)。 

 
## <a name="prerequisites"></a>必要條件

* 建立使用彈性的用戶端資料庫晶怪地圖。 請參閱[晶怪對應管理](sql-database-elastic-scale-shard-map-management.md)。 使用應用程式或範例中[開始使用彈性的資料庫工具](sql-database-elastic-scale-get-started.md)。
* 或者，請參閱[將現有的資料庫，以橫向擴充的資料庫](sql-database-elastic-convert-to-use-elastic-tools.md)。
* 使用者必須擁有更改任何外部資料來源的權限。 此權限會隨附 ALTER DATABASE 權限。
* 若要參照的基礎資料來源需要更改任何外部資料來源的權限。

## <a name="overview"></a>概觀

這些陳述式彈性的查詢在資料庫中建立 sharded 資料層的中繼資料表示。 


1. [建立主索引鍵](https://msdn.microsoft.com/library/ms174382.aspx)
2. [建立資料庫範圍認證](https://msdn.microsoft.com/library/mt270260.aspx)
3. [建立外部資料來源](https://msdn.microsoft.com/library/dn935022.aspx)
4. [建立外部表格](https://msdn.microsoft.com/library/dn935021.aspx) 

## <a name="11-create-database-scoped-master-key-and-credentials"></a>1.1 （英文） 建立資料庫範圍主索引鍵和認證 

認證彈性查詢用來連線到遠端資料庫。  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]
 
>[AZURE.NOTE] 請確定*」\<使用者名稱\>」*不會包含任何*"@servername"*後置字元。 

## <a name="12-create-external-data-sources"></a>1.2 建立外部資料來源

語法︰

    <External_Data_Source> ::=    
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH                                            
            (TYPE = SHARD_MAP_MANAGER,
                    LOCATION = '<fully_qualified_server_name>',
            DATABASE_NAME = ‘<shardmap_database_name>',
            CREDENTIAL = <credential_name>, 
            SHARD_MAP_NAME = ‘<shardmapname>’ 
                   ) [;] 

### <a name="example"></a>範例 

    CREATE EXTERNAL DATA SOURCE MyExtSrc 
    WITH 
    ( 
        TYPE=SHARD_MAP_MANAGER,
        LOCATION='myserver.database.windows.net', 
        DATABASE_NAME='ShardMapDatabase', 
        CREDENTIAL= SMMUser, 
        SHARD_MAP_NAME='ShardMap' 
    );
 
擷取目前的外部資料來源的清單︰ 

    select * from sys.external_data_sources; 

外部資料來源參照晶怪地圖。 彈性的查詢然後會使用外部資料來源及基礎晶怪地圖列舉參與資料層的資料庫。 若要閱讀晶怪地圖與彈性的查詢處理期間存取擊碎上的資料使用相同的認證。 

## <a name="13-create-external-tables"></a>1.3 建立外部資料表 
 
語法︰  

    CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name  
        ( { <column_definition> } [ ,...n ])     
        { WITH ( <sharded_external_table_options> ) }
    ) [;]  
    
    <sharded_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>,       
      [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 
      DISTRIBUTION = SHARDED(<sharding_column_name>) | REPLICATED |ROUND_ROBIN

**範例**

    CREATE EXTERNAL TABLE [dbo].[order_line]( 
         [ol_o_id] int NOT NULL, 
         [ol_d_id] tinyint NOT NULL,
         [ol_w_id] int NOT NULL, 
         [ol_number] tinyint NOT NULL, 
         [ol_i_id] int NOT NULL, 
         [ol_delivery_d] datetime NOT NULL, 
         [ol_amount] smallmoney NOT NULL, 
         [ol_supply_w_id] int NOT NULL, 
         [ol_quantity] smallint NOT NULL, 
         [ol_dist_info] char(24) NOT NULL 
    ) 
    
    WITH 
    ( 
        DATA_SOURCE = MyExtSrc, 
        SCHEMA_NAME = 'orders', 
        OBJECT_NAME = 'order_details', 
        DISTRIBUTION=SHARDED(ol_w_id)
    ); 

從目前的資料庫來擷取外部表格清單︰ 

    SELECT * from sys.external_tables; 

若要刪除外部資料表︰

    DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]

### <a name="remarks"></a>註解

資料\_來源子句定義用於外部表格外部資料來源 （晶怪對應表）。  

結構描述\_名稱和物件\_名稱子句會將外部表格定義對應到不同的結構描述中的表格。 如果省略，假設遠端物件的結構描述是 「 dbo 」，且其名稱假設與定義的外部表格名稱。 這是很有用的遠端表格名稱已經存在資料庫中您想要建立外部表格的位置。 例如，您想要定義外部的資料表，好讓彙總的類別目錄檢視或 Dmv 不按比例縮放出資料層。 類別目錄檢視及 Dmv 已存在於本機，因為您無法使用外部表格定義的名稱。 不過，使用不同的名稱，並使用 [目錄] 檢視或 DMV 的結構描述中名稱\_名稱及/或物件\_名稱子句。 （請參閱下面的範例中）。 

通訊群組子句指定此資料表所使用的資料分佈。 查詢處理器使用通訊群組子句來建立的最有效的查詢計劃中所提供的資訊。  

1. **SHARDED**代表資料水平分割資料庫內。 分割資料分配索引鍵是**< sharding_column_name >**參數。
2. **複寫**表示會出現在每個資料庫表格的複本。 您必須負責確保複本跨資料庫完全相同。
3. **ROUND\_ROBIN**表示的資料表已水平分割使用應用程式相關的通訊方法。 

**資料層參考**︰ 外部 DDL 指的是外部資料來源的資料表。 外部資料來源指定晶怪地圖提供外部表格資料層中找出所有資料庫所需的資訊。 


### <a name="security-considerations"></a>安全性考量 

使用者有權存取外部表格自動存取基本遠端資料表底下 [外部資料來源定義中指定的認證。 避免不想要提高權限的權限透過外部資料來源的認證。 外部表格使用授與或撤銷，就如同一般的資料表。  

一旦您已經定義外部資料來源與外部表格，您現在可以使用完整 T SQL 透過外部表格。

## <a name="example-querying-horizontal-partitioned-databases"></a>範例︰ 查詢水平分割的資料庫 

下列查詢會執行倉儲、 訂單和順序行之間的三種方式聯結，並使用多個彙總和選擇性的篩選。 假設 （1） 水平分割 (sharding) （2） 的倉儲、 訂單和順序線條 sharded 倉庫識別碼] 資料行中，以和彈性的查詢可以共同找出在擊碎連接及昂貴的組件上平行擊碎查詢的 [處理程序。 

    select  
         w_id as warehouse,
         o_c_id as customer,
         count(*) as cnt_orderline,
         max(ol_quantity) as max_quantity,
         avg(ol_amount) as avg_amount, 
         min(ol_delivery_d) as min_deliv_date
    from warehouse 
    join orders 
    on w_id = o_w_id
    join order_line 
    on o_id = ol_o_id and o_w_id = ol_w_id 
    where w_id > 100 and w_id < 200 
    group by w_id, o_c_id 
 
## <a name="stored-procedure-for-remote-t-sql-execution-spexecuteremote"></a>預存程序，供遠端 T SQL 執行︰ 預存程序\_execute_remote

彈性的查詢也引進了擊碎直接存取的預存程序。 預存程序稱為[預存程序\_執行\_遠端](https://msdn.microsoft.com/library/mt703714)，可以使用遠端資料庫上執行遠端預存程序或 T SQL 程式碼。 採用下列參數︰ 

* 資料來源名稱 (nvarchar): 輸入 RDBMS 的外部資料來源的名稱。 
* 查詢 (nvarchar): 若要執行的每個晶怪 T SQL 查詢。 
* 參數宣告 (nvarchar)-可省略︰ 使用查詢參數 （例如 sp_executesql) 中的參數的資料類型定義的字串。 
* 參數值清單的選用︰ 逗點分隔 （例如 sp_executesql) 的參數值清單。

Sp\_執行\_遠端執行遠端資料庫上的 [指定的 T SQL 陳述式中使用引動參數中所提供的外部資料來源。 它會使用外部資料來源的認證以連線至 shardmap manager 資料庫及遠端資料庫。  

範例︰ 

    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse' 

## <a name="connectivity-for-tools"></a>連線的工具  

使用一般的 SQL Server 連線字串連接您的應用程式，使用您的外部表格定義資料庫您 BI 及資料整合工具。 請確定 SQL Server 支援作為資料來源的工具。 然後參考彈性的查詢資料庫等任何其他的 SQL Server 資料庫連線到的工具及使用外部表格工具或應用程式從好像本機的資料表。 

## <a name="best-practices"></a>最佳作法 

* 請確定的彈性查詢端點資料庫已獲授權 shardmap 資料庫及所有擊碎透過 SQL DB 防火牆。  

* 驗證或強制執行由外部表格定義的資料分佈。 如果您實際資料分配不同於指定在您的表格定義通訊群組，您的查詢，可能會產生非預期的結果。 

* 彈性的查詢目前不會執行晶怪雜訊述詞 sharding 鍵在想要允許將安全地處理中排除特定擊碎時。

* 彈性的查詢最適合查詢位置完成大部分的計算，擊碎上。 您通常可以取得最佳的查詢效能與選擇性的篩選器述詞可評估擊碎或連接分割按鍵可執行所有擊碎上對齊磁碟分割的方式。 其他查詢模式可能需要從擊碎載入主節點的大量資料，可能不佳執行

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-horizontal-partitioning/horizontalpartitioning.png
<!--anchors-->
