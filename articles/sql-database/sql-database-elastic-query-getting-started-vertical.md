<properties
    pageTitle="開始使用跨資料庫查詢 （垂直分割） |Microsoft Azure"   
    description="垂直分割資料庫中使用彈性的資料庫查詢的方式"
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
    ms.date="05/23/2016"
    ms.author="torsteng" />

# <a name="get-started-with-cross-database-queries-vertical-partitioning-preview"></a>開始使用跨資料庫查詢 （垂直分割） （預覽版本）

Azure SQL 資料庫的彈性的資料庫查詢 （預覽版本） 可讓您執行 T SQL 查詢的橫跨多個資料庫時使用單一連接點。 本主題適用於[垂直分割資料庫](sql-database-elastic-query-vertical-partitioning.md)。  

完成時，您將會︰ 了解如何設定和使用 Azure SQL 資料庫執行橫跨多個相關的資料庫的查詢。 

如需彈性的資料庫查詢功能的詳細資訊，請參閱[Azure SQL 資料庫彈性的資料庫查詢概觀](sql-database-elastic-query-overview.md)。 

## <a name="create-the-sample-databases"></a>建立範例資料庫

開始使用，我們需要建立兩個資料庫]，[**客戶**] 和 [**訂單**，在相同或不同的邏輯伺服器中。   

請執行下列查詢**訂單**資料庫建立**OrderInformation**資料表，並輸入的範例資料。 

    CREATE TABLE [dbo].[OrderInformation]( 
        [OrderID] [int] NOT NULL, 
        [CustomerID] [int] NOT NULL 
        ) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8) 

現在，請執行下列查詢**客戶**資料庫建立**CustomerInformation**資料表，並輸入的範例資料。 

    CREATE TABLE [dbo].[CustomerInformation]( 
        [CustomerID] [int] NOT NULL, 
        [CustomerName] [varchar](50) NULL, 
        [Company] [varchar](50) NULL 
        CONSTRAINT [CustID] PRIMARY KEY CLUSTERED ([CustomerID] ASC) 
    ) 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (1, 'Jack', 'ABC') 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (2, 'Steve', 'XYZ') 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (3, 'Lylla', 'MNO') 

## <a name="create-database-objects"></a>建立資料庫物件
### <a name="database-scoped-master-key-and-credentials"></a>資料庫範圍主索引鍵和認證

1. 開啟 Visual Studio 中的 SQL Server Management Studio 或 SQL Server Data Tools。
2. 連線至訂單的資料庫，然後執行下列 T SQL 命令︰

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>'; 
        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred 
        WITH IDENTITY = '<username>', 
        SECRET = '<password>';  

    「 使用者名稱 」 和 「 密碼 」 應該是使用者名稱和密碼登入到客戶的資料庫。
    目前不支援使用彈性的查詢中的 Azure Active Directory 驗證。

### <a name="external-data-sources"></a>外部資料來源
若要建立外部資料來源，請在 [訂單資料庫執行下列命令︰ 

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH 
        (TYPE = RDBMS, 
        LOCATION = '<server_name>.database.windows.net', 
        DATABASE_NAME = 'Customers', 
        CREDENTIAL = ElasticDBQueryCred, 
    ) ;

### <a name="external-tables"></a>外部資料表
建立外部表格訂單資料庫，以符合 CustomerInformation 資料表的定義︰

    CREATE EXTERNAL TABLE [dbo].[CustomerInformation] 
    ( [CustomerID] [int] NOT NULL, 
      [CustomerName] [varchar](50) NOT NULL, 
      [Company] [varchar](50) NOT NULL) 
    WITH 
    ( DATA_SOURCE = MyElasticDBQueryDataSrc) 

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>執行範例彈性的資料庫 T SQL 查詢

一旦您定義外部資料來源，您現在可以使用 T SQL 查詢外部表格外部表格。 訂單資料庫，請執行下列查詢︰ 

    SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company 
    FROM OrderInformation 
    INNER JOIN CustomerInformation 
    ON CustomerInformation.CustomerID = OrderInformation.CustomerID 

## <a name="cost"></a>成本

彈性的資料庫查詢功能目前，納入 Azure SQL 資料庫的成本。  

價格資訊請參閱[SQL 資料庫價格](/pricing/details/sql-database)。 


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->

<!--anchors-->
