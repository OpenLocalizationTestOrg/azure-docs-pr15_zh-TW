<properties
    pageTitle="在以不同的結構描述的雲端資料庫間的查詢 |Microsoft Azure"
    description="如何設定在垂直分割的跨資料庫查詢"    
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

# <a name="query-across-cloud-databases-with-different-schemas-preview"></a>在不同的結構描述 （預覽版本） 的雲端資料庫間的查詢

![橫跨在不同的資料庫資料表查詢][1]

垂直分割資料庫會使用不同的資料庫上的組不同的表格。 這表示結構描述其他在不同的資料庫。 舉例來說，所有資料表庫存都了一個資料庫時會計相關的所有資料表第二個資料庫。 

## <a name="prerequisites"></a>必要條件

* 使用者必須擁有更改任何外部資料來源的權限。 此權限會隨附 ALTER DATABASE 權限。
* 若要參照的基礎資料來源需要更改任何外部資料來源的權限。

## <a name="overview"></a>概觀

**附註**︰ 與不同的水平分割，這些 DDL 陳述式不相依於定義與晶怪對應到彈性的用戶端資料庫的資料層。

1. [建立主索引鍵](https://msdn.microsoft.com/library/ms174382.aspx)
2. [建立資料庫範圍認證](https://msdn.microsoft.com/library/mt270260.aspx)
3. [建立外部資料來源](https://msdn.microsoft.com/library/dn935022.aspx)
4. [建立外部表格](https://msdn.microsoft.com/library/dn935021.aspx) 


## <a name="create-database-scoped-master-key-and-credentials"></a>建立資料庫範圍主索引鍵和認證 

認證彈性查詢用來連線到遠端資料庫。  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]
 
**附註**   確定*<username>*不會包含任何*"@servername"*後置字元。 

## <a name="create-external-data-sources"></a>建立外部資料來源

語法︰

    <External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH 
               (TYPE = RDBMS,
                LOCATION = ’<fully_qualified_server_name>’,
                DATABASE_NAME = ‘<remote_database_name>’,  
                CREDENTIAL = <credential_name> 
                ) [;] 

**重要**  輸入參數必須設定為**RDBMS**。 

### <a name="example"></a>範例 

下列範例說明將外部資料來源建立陳述式。 

    CREATE EXTERNAL DATA SOURCE RemoteReferenceData 
    WITH 
    ( 
        TYPE=RDBMS, 
        LOCATION='myserver.database.windows.net', 
        DATABASE_NAME='ReferenceData', 
        CREDENTIAL= SqlUser 
    ); 
 
若要擷取目前的外部資料來源的清單︰ 

    select * from sys.external_data_sources; 

### <a name="external-tables"></a>外部資料表 

語法︰

    CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name . ] table_name  
    ( { <column_definition> } [ ,...n ])     
    { WITH ( <rdbms_external_table_options> ) } 
    )[;] 
    
    <rdbms_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>, 
      [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 

### <a name="example"></a>範例  

    CREATE EXTERNAL TABLE [dbo].[customer]( 
        [c_id] int NOT NULL, 
        [c_firstname] nvarchar(256) NULL, 
        [c_lastname] nvarchar(256) NOT NULL, 
        [street] nvarchar(256) NOT NULL, 
        [city] nvarchar(256) NOT NULL, 
        [state] nvarchar(20) NULL, 
        [country] nvarchar(50) NOT NULL, 
    ) 
    WITH 
    ( 
           DATA_SOURCE = RemoteReferenceData 
    ); 

下列範例會示範如何從目前的資料庫擷取外部表格清單︰ 

    select * from sys.external_tables; 

### <a name="remarks"></a>註解

彈性的查詢延伸現有的外部表格語法，以定義外部使用類型 RDBMS 的外部資料來源的資料表。 垂直分割外部表格定義涵蓋下列特性︰ 

* **結構描述**︰ 外部表格 DDL 定義的結構描述，可以使用您的查詢。 在您的外部表格定義所提供的結構描述必須符合的實際資料儲存位置遠端資料庫中的資料表結構描述。 

* **遠端資料庫參考**︰ 外部 DDL 指的是外部資料來源的資料表。 外部資料來源指定邏輯的伺服器名稱及遠端資料庫存放實際的資料表資料的資料庫名稱。 

使用外部資料來源的前一節中所述，若要建立外部表格語法如下︰ 

DATA_SOURCE 子句定義用於外部表格外部資料來源 （亦即若垂直分割遠端資料庫）。  

SCHEMA_NAME 和物件 _ 名稱子句提供在遠端資料庫，不同的結構描述中為表格或使用不同的名稱，分別對應的外部表格定義的能力。 這是很有用，如果您想要定義在遠端資料庫 – 或任何其他位置的遠端資料表名稱已經存在本機的情況下的類別目錄檢視] 或 [DMV 外部的資料表。  

下列 DDL 陳述式去掉現有的外部表格定義從本機的目錄。 它不會影響遠端資料庫。 

    DROP EXTERNAL TABLE [ [ schema_name ] . | schema_name. ] table_name[;]  

**建立放外部表格的權限**︰ 變更任何外部資料來源的權限所需的外部表格 DDL 就也需要參照基礎資料來源。  

## <a name="security-considerations"></a>安全性考量
使用者有權存取外部表格自動存取基本遠端資料表底下 [外部資料來源定義中指定的認證。 您小心應該管理存取外部表格以避免不想要提高權限的權限透過外部資料來源的認證。 一般 SQL 權限可授與或撤銷外部資料表的存取權，就如同一般的資料表。  


## <a name="example-querying-vertically-partitioned-databases"></a>範例︰ 查詢垂直分割資料庫 

下列查詢執行的三種方式聯結兩個本機訂單] 和 [訂單行的資料表與遠端資料表之間的客戶。 彈性的查詢參照的資料使用大小寫的範例如下︰ 

    SELECT      
     c_id as customer,
     c_lastname as customer_name,
     count(*) as cnt_orderline, 
     max(ol_quantity) as max_quantity,
     avg(ol_amount) as avg_amount,
     min(ol_delivery_d) as min_deliv_date
    FROM customer 
    JOIN orders 
    ON c_id = o_c_id
    JOIN  order_line 
    ON o_id = ol_o_id and o_c_id = ol_c_id
    WHERE c_id = 100


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

已啟用的彈性查詢定義外部表格 SQL 資料庫伺服器上的資料庫連線 BI 和資料整合工具，您可以使用一般的 SQL Server 連線字串。 請確定 SQL Server 支援作為資料來源的工具。 然後參照至彈性的查詢資料庫，以及其外部的表格，就像任何其他您想要使用您的工具連線至 SQL Server 資料庫。 

## <a name="best-practices"></a>最佳作法 
 
* 請確定的彈性查詢端點資料庫已獲授權遠端資料庫來啟用 Azure 服務的存取，在 SQL DB 防火牆設定。 也請確定在外部資料來源定義所提供的認證可以順利登入遠端資料庫，並擁有的權限存取遠端資料表。  

* 彈性的查詢最適合查詢位置完成大部分的計算，在遠端資料庫。 通常，您會收到的最佳的查詢效能與選擇性的篩選器述詞可評估遠端資料庫或遠端資料庫可以完全執行的連接。 其他查詢模式可能需要從遠端資料庫載入的大量資料，並可能不佳執行。 


## <a name="next-steps"></a>後續步驟

若要查詢水平分割的資料庫 （也稱為 sharded 資料庫），請參閱[sharded 雲端資料庫 （水平分割） 之間的查詢](sql-database-elastic-query-horizontal-partitioning.md)。

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Image references-->
[1]: ./media/sql-database-elastic-query-vertical-partitioning/verticalpartitioning.png


<!--anchors-->
