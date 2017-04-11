<properties
   pageTitle="使用者定義的結構描述中 SQL Data Warehouse |Microsoft Azure"
   description="使用 TRANSACT-SQL 結構描述中 Azure SQL Data Warehouse 開發解決方案的秘訣。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/14/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="user-defined-schemas-in-sql-data-warehouse"></a>在 SQL Data Warehouse 使用者定義的結構描述

傳統資料倉儲通常會使用不同的資料庫來建立根據工作量、 網域或安全性的應用程式範圍。 例如，傳統的 SQL Server 資料倉庫可能會包含臨時資料庫、 資料倉庫資料庫及某些資料超市資料庫。 在此拓撲中每個資料庫的運作方式與的工作量及安全性架構中的邊界。

相反地，SQL Data Warehouse 執行在一個資料庫的整個資料倉庫工作量。 資料庫交叉聯結不允許。 因此 SQL Data Warehouse 預期倉庫用來儲存一個資料庫中的所有資料表。

> [AZURE.NOTE] SQL Data Warehouse 不支援任何類型的跨資料庫的查詢。 因此，運用此模式的資料倉庫實作需要修訂。

## <a name="recommendations"></a>建議

以下是建議使用使用者定義的結構描述合併負載、 安全性、 網域和功能的邊界

1. 若要執行整個資料倉庫工作量使用一個 SQL Data Warehouse 資料庫
2. 合併彙算現有的資料倉庫環境使用一個 SQL Data Warehouse 資料庫
3. 運用**使用者定義的結構描述可**提供先前使用資料庫來實作邊界。

如果使用者定義的結構描述未使用先前您可以從頭。 您使用者定義的結構描述可在 SQL Data Warehouse 資料庫作為基礎只要使用舊的資料庫名稱。

如果已經使用結構描述您有幾個選項︰

1. 移除舊版的結構描述名稱，然後重新開始
2. 保留舊版的結構描述名稱以周舊版的結構描述名稱的表格名稱
3. 保留舊版的結構描述名稱實作額外的結構描述，以重新建立舊的結構描述結構中資料表的檢視。

> [AZURE.NOTE] 在第一個檢查選項 [3 看起來可能像最睛的選項。 不過，捲風位於詳細資料。 只有在 SQL Data Warehouse 讀取檢視。 任何資料或表格的修改，就必須執行基底資料表。 選項 [3 也會將您的系統介紹檢視的圖層。 您可能會想要思考這其他如果您使用您架構中檢視已。


### <a name="examples"></a>範例︰

實作使用者定義的結構描述根據資料庫名稱

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the data warehouse
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create data warehouse tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

保留舊版的結構描述名稱以周這些資料表名稱。 使用結構描述的工作量邊界。

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the data warehouse boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the data warehouse boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

保留舊版的結構描述名稱使用檢視

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the data warehouse boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base data warehouse tables in the data warehouse boundary
(       CustKey BIGINT NOT NULL
,       ...
)
GO
CREATE VIEW [dim].[customer] -- create a view in the legacy schema name boundary for presentation consistency purposes only
AS
SELECT  CustKey
,       ...
FROM    [edw].customer
;
```

> [AZURE.NOTE] 結構描述策略中的任何變更所需的安全性模型檢閱資料庫。 在許多情況下，您可能會無法指派結構描述層級權限來簡化的安全性模型。 如果需要微調權限，則您可以使用資料庫角色。

## <a name="next-steps"></a>後續步驟
如需開發秘訣，請參閱[開發概觀][]。

<!--Image references-->

<!--Article references-->
[開發概觀]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->
