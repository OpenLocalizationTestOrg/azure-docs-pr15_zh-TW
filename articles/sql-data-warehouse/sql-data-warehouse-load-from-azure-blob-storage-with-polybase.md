<properties
   pageTitle="從 Azure blob 儲存體載入 SQL Data Warehouse (PolyBase) |Microsoft Azure"
   description="瞭解如何使用 PolyBase 資料從 Azure blob 儲存體載入 SQL Data Warehouse。 公用資料載入 Contoso 零售 Data Warehouse 結構描述幾個資料表。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="ckarst"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/25/2016"
   ms.author="cakarst;barbkess;sonyama"/>


# <a name="load-data-from-azure-blob-storage-into-sql-data-warehouse-polybase"></a>將 SQL Data Warehouse (PolyBase) 將資料載入從 Azure blob 儲存體

> [AZURE.SELECTOR]
- [資料工廠](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md)
- [PolyBase](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

您可以使用 [PolyBase 及 T SQL 命令，從 Azure blob 儲存體中載入 Azure SQL Data Warehouse 的資料。 

若要保留簡單，本教學課程中會載入公用 Azure 儲存體 Blob 到 Contoso 零售 Data Warehouse 結構描述兩個資料表。 若要載入完整的資料集，請從 Microsoft SQL Server 範例存放庫中執行[載入完整的 Contoso 零售 Data Warehouse][]的範例。

在本教學課程中，您將會︰

1. 設定 PolyBase 載入從 Azure blob 儲存體
2. 載入您的資料庫中的公用資料
3. 完成載入後，請執行最佳化。


## <a name="before-you-begin"></a>開始之前
若要執行此教學課程中，您需要的既有的 SQL Data Warehouse 資料庫 Azure 帳戶。 如果您還沒有此，請參閱[建立 SQL Data Warehouse][]。

## <a name="1-configure-the-data-source"></a>1.設定資料來源

PolyBase 使用 T SQL 外部物件以定義的位置和外部資料的屬性。 外部物件定義會儲存在 SQL Data Warehouse。 本身的資料儲存在外部。

### <a name="11-create-a-credential"></a>1.1 （英文)。 建立認證

**略過此步驟**如果您正在載入 Contoso 公用資料。 您不需要安全存取的公用資料，因為它已經可以存取的任何人。

如果您使用的本教學課程中另存為範本載入自己的資料，請**不要略過此步驟**。 若要透過認證存取資料，建立資料庫範圍的認證，請使用下列指令碼，然後使用它定義資料來源的位置時。


```sql
-- A: Create a master key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.


CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;


-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure blob storage.
-- LOCATION: Provide Azure storage account name and blob container name.
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'wasbs://<blob_container_name>@<azure_storage_account_name>.blob.core.windows.net',
    CREDENTIAL = AzureStorageCredential
);
```

跳至步驟 2。

### <a name="12-create-the-external-data-source"></a>1.2。 建立外部資料來源

使用此[建立外部資料來源][]] 命令來儲存的位置資料，以及的資料類型。 

```sql
CREATE EXTERNAL DATA SOURCE AzureStorage_west_public
WITH 
(  
    TYPE = Hadoop 
,   LOCATION = 'wasbs://contosoretaildw-tables@contosoretaildw.blob.core.windows.net/'
); 
```

> [AZURE.IMPORTANT] 如果您選擇公開您 azure blob 儲存體容器，請記住，為資料擁有者，您需要付費資料出口費用時資料保留資料中心。 

## <a name="2-configure-data-format"></a>2.設定資料格式

資料已儲存文字檔中 Azure blob 儲存體，並使用分隔符號分隔每個欄位。 執行此[建立外部的檔案格式][]命令，以指定的資料格式中的文字檔案。 Contoso 資料是壓縮和管道分隔。

```sql
CREATE EXTERNAL FILE FORMAT TextFileFormat 
WITH 
(   FORMAT_TYPE = DELIMITEDTEXT
,   FORMAT_OPTIONS  (   FIELD_TERMINATOR = '|'
                    ,   STRING_DELIMITER = ''
                    ,   DATE_FORMAT      = 'yyyy-MM-dd HH:mm:ss.fff'
                    ,   USE_TYPE_DEFAULT = FALSE 
                    )
);
``` 

## <a name="3-create-the-external-tables"></a>3.建立外部資料表

現在您已指定的資料來源與檔案格式，是準備好要建立外部表格。 

### <a name="31-create-a-schema-for-the-data"></a>3.1。 建立資料結構描述。 

若要建立 Contoso 資料儲存資料庫的位置，建立結構描述。

```sql
CREATE SCHEMA [asb]
GO
```

### <a name="32-create-the-external-tables"></a>3.2 捨位。 建立外部的資料表。 

執行此指令碼來建立 [DimProduct] 和 [FactOnlineSales 的外部表格。 我們會在這裡做定義資料行名稱與資料類型，而繫結至的位置和 Azure blob 儲存檔案的格式。 定義會儲存在 SQL Data Warehouse 和仍在 Azure 儲存體 Blob 的資料。

**位置**參數是在 Azure 儲存體 Blob 的根資料夾底下的資料夾。 每個資料表位於不同的資料夾。


```sql

--DimProduct
CREATE EXTERNAL TABLE [asb].DimProduct (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL,
    [ProductDescription] [nvarchar](400) NULL,
    [ProductSubcategoryKey] [int] NULL,
    [Manufacturer] [nvarchar](50) NULL,
    [BrandName] [nvarchar](50) NULL,
    [ClassID] [nvarchar](10) NULL,
    [ClassName] [nvarchar](20) NULL,
    [StyleID] [nvarchar](10) NULL,
    [StyleName] [nvarchar](20) NULL,
    [ColorID] [nvarchar](10) NULL,
    [ColorName] [nvarchar](20) NOT NULL,
    [Size] [nvarchar](50) NULL,
    [SizeRange] [nvarchar](50) NULL,
    [SizeUnitMeasureID] [nvarchar](20) NULL,
    [Weight] [float] NULL,
    [WeightUnitMeasureID] [nvarchar](20) NULL,
    [UnitOfMeasureID] [nvarchar](10) NULL,
    [UnitOfMeasureName] [nvarchar](40) NULL,
    [StockTypeID] [nvarchar](10) NULL,
    [StockTypeName] [nvarchar](40) NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [AvailableForSaleDate] [datetime] NULL,
    [StopSaleDate] [datetime] NULL,
    [Status] [nvarchar](7) NULL,
    [ImageURL] [nvarchar](150) NULL,
    [ProductURL] [nvarchar](150) NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/DimProduct/' 
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;
 
--FactOnlineSales
CREATE EXTERNAL TABLE [asb].FactOnlineSales 
(
    [OnlineSalesKey] [int]  NOT NULL,
    [DateKey] [datetime] NOT NULL,
    [StoreKey] [int] NOT NULL,
    [ProductKey] [int] NOT NULL,
    [PromotionKey] [int] NOT NULL,
    [CurrencyKey] [int] NOT NULL,
    [CustomerKey] [int] NOT NULL,
    [SalesOrderNumber] [nvarchar](20) NOT NULL,
    [SalesOrderLineNumber] [int] NULL,
    [SalesQuantity] [int] NOT NULL,
    [SalesAmount] [money] NOT NULL,
    [ReturnQuantity] [int] NOT NULL,
    [ReturnAmount] [money] NULL,
    [DiscountQuantity] [int] NULL,
    [DiscountAmount] [money] NULL,
    [TotalCost] [money] NOT NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/FactOnlineSales/' 
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;
```

## <a name="4-load-the-data"></a>4.載入資料
沒有存取外部資料的不同方法。  您可以直接從外部表格資料的查詢，將資料載入到新的資料庫資料表，或新增外部資料至現有的資料庫資料表。  


### <a name="41-create-a-new-schema"></a>4.1。 建立新的結構描述

CTAS 會建立新的資料表，其中包含的資料。  首先，建立 contoso 資料結構描述。

```sql
CREATE SCHEMA [cso]
GO
```

### <a name="42-load-the-data-into-new-tables"></a>4.2。 將資料載入到新的資料表

若要從 Azure blob 儲存體載入資料，並將其儲存在您的資料庫資料表中，使用 [[建立資料表另存新檔選取 (SQL)][]陳述式。 載入 CTAS 以運用您剛建立強外部表格。若要將資料載入到新的資料表，使用每個資料表的一個[CTAS][]陳述式。 

CTAS 建立新的資料表，並填入 select 陳述式的結果。 CTAS 定義新的資料表的 select 陳述式結果與具有相同的資料行和資料類型。 如果您選取的所有資料行從外部表格時，新資料表會外部表格中的資料類型與欄的複本。

在此範例中，我們建立維度和為雜湊分散式的資料表的資料表。 


```sql
SELECT GETDATE();
GO

CREATE TABLE [cso].[DimProduct]            WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[DimProduct]             OPTION (LABEL = 'CTAS : Load [cso].[DimProduct]             ');
CREATE TABLE [cso].[FactOnlineSales]       WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[FactOnlineSales]        OPTION (LABEL = 'CTAS : Load [cso].[FactOnlineSales]        ');
```

### <a name="43-track-the-load-progress"></a>4.3 追蹤載入進度

您可以使用動態管理檢視 (Dmv) 您載入的進度。 

```sql
-- To see all requests
SELECT * FROM sys.dm_pdw_exec_requests;

-- To see a particular request identified by its label
SELECT * FROM sys.dm_pdw_exec_requests as r;
WHERE r.[label] = 'CTAS : Load [cso].[DimProduct]             '
      OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
;

-- To track bytes and files
SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files, 
    sum(s.bytes_processed)/1024/1024 as gb_processed
FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
        on r.request_id = s.request_id
WHERE 
    r.[label] = 'CTAS : Load [cso].[DimProduct]             '
    OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
GROUP BY
    r.command,
    s.request_id,
    r.status
ORDER BY
    nbr_files desc,
    gb_processed desc;
```

## <a name="5-optimize-columnstore-compression"></a>5。 最佳化 columnstore 壓縮

根據預設，SQL Data Warehouse 會將表格儲存為直的 columnstore 索引。 載入完成之後，資料列的一些可能不壓縮成 columnstore。  有不同的為什麼發生這種情形的原因。 若要深入瞭解，請參閱[管理 columnstore 索引][]。

若要最佳化查詢效能和 columnstore 壓縮載入之後，重建強制 columnstore 索引壓縮的所有資料列的資料表。 

```sql
SELECT GETDATE();
GO

ALTER INDEX ALL ON [cso].[DimProduct]               REBUILD;
ALTER INDEX ALL ON [cso].[FactOnlineSales]          REBUILD;
```

如需有關維護 columnstore 索引的詳細資訊，請參閱[管理 columnstore 索引][]文件。

## <a name="6-optimize-statistics"></a>6.最佳化統計資料

最好緊接載入建立單欄的統計資料。 有一些選擇統計資料。 例如，如果您在每個資料行建立單一資料行的統計資料，可能需要很長的時間，重建所有統計資料。 如果您知道特定欄不會在查詢述詞，您可以跳建立統計資料，這些資料行。

如果您決定要在每個資料表的每個資料行建立單一資料行的統計資料，您可以使用預存程序的程式碼範例`prc_sqldw_create_stats`[統計資料][]文件中。

下列範例會建立統計資料的絕佳起點。 在 [維度] 資料表中每個資料行和交易資料表中每個連結的資料行，它會建立單欄的統計資料。 您可以隨時新增單一或多個資料行的統計資料其他交易資料表資料行稍後。


```sql
CREATE STATISTICS [stat_cso_DimProduct_AvailableForSaleDate] ON [cso].[DimProduct]([AvailableForSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_BrandName] ON [cso].[DimProduct]([BrandName]);
CREATE STATISTICS [stat_cso_DimProduct_ClassID] ON [cso].[DimProduct]([ClassID]);
CREATE STATISTICS [stat_cso_DimProduct_ClassName] ON [cso].[DimProduct]([ClassName]);
CREATE STATISTICS [stat_cso_DimProduct_ColorID] ON [cso].[DimProduct]([ColorID]);
CREATE STATISTICS [stat_cso_DimProduct_ColorName] ON [cso].[DimProduct]([ColorName]);
CREATE STATISTICS [stat_cso_DimProduct_ETLLoadID] ON [cso].[DimProduct]([ETLLoadID]);
CREATE STATISTICS [stat_cso_DimProduct_ImageURL] ON [cso].[DimProduct]([ImageURL]);
CREATE STATISTICS [stat_cso_DimProduct_LoadDate] ON [cso].[DimProduct]([LoadDate]);
CREATE STATISTICS [stat_cso_DimProduct_Manufacturer] ON [cso].[DimProduct]([Manufacturer]);
CREATE STATISTICS [stat_cso_DimProduct_ProductDescription] ON [cso].[DimProduct]([ProductDescription]);
CREATE STATISTICS [stat_cso_DimProduct_ProductKey] ON [cso].[DimProduct]([ProductKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductLabel] ON [cso].[DimProduct]([ProductLabel]);
CREATE STATISTICS [stat_cso_DimProduct_ProductName] ON [cso].[DimProduct]([ProductName]);
CREATE STATISTICS [stat_cso_DimProduct_ProductSubcategoryKey] ON [cso].[DimProduct]([ProductSubcategoryKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductURL] ON [cso].[DimProduct]([ProductURL]);
CREATE STATISTICS [stat_cso_DimProduct_Size] ON [cso].[DimProduct]([Size]);
CREATE STATISTICS [stat_cso_DimProduct_SizeRange] ON [cso].[DimProduct]([SizeRange]);
CREATE STATISTICS [stat_cso_DimProduct_SizeUnitMeasureID] ON [cso].[DimProduct]([SizeUnitMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_Status] ON [cso].[DimProduct]([Status]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeID] ON [cso].[DimProduct]([StockTypeID]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeName] ON [cso].[DimProduct]([StockTypeName]);
CREATE STATISTICS [stat_cso_DimProduct_StopSaleDate] ON [cso].[DimProduct]([StopSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_StyleID] ON [cso].[DimProduct]([StyleID]);
CREATE STATISTICS [stat_cso_DimProduct_StyleName] ON [cso].[DimProduct]([StyleName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitCost] ON [cso].[DimProduct]([UnitCost]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureID] ON [cso].[DimProduct]([UnitOfMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureName] ON [cso].[DimProduct]([UnitOfMeasureName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitPrice] ON [cso].[DimProduct]([UnitPrice]);
CREATE STATISTICS [stat_cso_DimProduct_UpdateDate] ON [cso].[DimProduct]([UpdateDate]);
CREATE STATISTICS [stat_cso_DimProduct_Weight] ON [cso].[DimProduct]([Weight]);
CREATE STATISTICS [stat_cso_DimProduct_WeightUnitMeasureID] ON [cso].[DimProduct]([WeightUnitMeasureID]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CurrencyKey] ON [cso].[FactOnlineSales]([CurrencyKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CustomerKey] ON [cso].[FactOnlineSales]([CustomerKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_DateKey] ON [cso].[FactOnlineSales]([DateKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_OnlineSalesKey] ON [cso].[FactOnlineSales]([OnlineSalesKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_ProductKey] ON [cso].[FactOnlineSales]([ProductKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_PromotionKey] ON [cso].[FactOnlineSales]([PromotionKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_StoreKey] ON [cso].[FactOnlineSales]([StoreKey]);
```

## <a name="achievement-unlocked"></a>解除鎖定的成就 ！

您已成功公用將資料載入 Azure SQL Data Warehouse。 好了 ！

您現在可以開始查詢的資料表使用查詢，如下所示︰

```sql
SELECT  SUM(f.[SalesAmount]) AS [sales_by_brand_amount]
,       p.[BrandName]
FROM    [cso].[FactOnlineSales] AS f
JOIN    [cso].[DimProduct]      AS p ON f.[ProductKey] = p.[ProductKey]
GROUP BY p.[BrandName]
```

## <a name="next-steps"></a>後續步驟
載入完整 Contoso 零售 Data Warehouse 資料、 使用中的指令碼的更多開發秘訣，請參閱[SQL Data Warehouse 開發概觀][]。

<!--Image references-->

<!--Article references-->
[建立 SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Load data into SQL Data Warehouse]: sql-data-warehouse-overview-load.md
[SQL Data Warehouse 開發概觀]: sql-data-warehouse-overview-develop.md
[管理 columnstore 索引]: sql-data-warehouse-tables-index.md
[統計資料]: sql-data-warehouse-tables-statistics.md
[CTAS]: sql-data-warehouse-develop-ctas.md
[label]: sql-data-warehouse-develop-label.md

<!--MSDN references-->
[建立外部資料來源]: https://msdn.microsoft.com/en-us/library/dn935022.aspx
[建立外部的檔案格式]: https://msdn.microsoft.com/en-us/library/dn935026.aspx
[建立表格另存新檔選取 (TRANSACT-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[REBUILD]: https://msdn.microsoft.com/library/ms188388.aspx

<!--Other Web references-->
[Microsoft Download Center]: http://www.microsoft.com/download/details.aspx?id=36433
[載入完整的 Contoso 零售 Data Warehouse]: https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md
