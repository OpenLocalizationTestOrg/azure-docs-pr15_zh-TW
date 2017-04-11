<properties
   pageTitle="將您的結構描述移轉到 SQL Data Warehouse |Microsoft Azure"
   description="移轉您的結構描述至 Azure SQL Data Warehouse，開發解決方案的秘訣。"
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
   ms.date="08/25/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="migrate-your-schema-to-sql-data-warehouse"></a>將您的結構描述移轉到 SQL Data Warehouse#

以下摘要可協助您瞭解可協助您移轉的資料庫的 SQL Server 和 SQL Data Warehouse 之間的差異。

## <a name="table-migration"></a>表格移轉

移轉您的資料表，當您需要熟悉 SQL Data Warehouse 表格的表格功能。  [表格概觀][]是絕佳起點。  本文會向您介紹最重要的考量時建立例如資料表統計資料，分配分割和編製索引的資料表。  同時說明一些[不支援的表格功能][]和因應措施。

SQL Data Warehouse 支援的常見的商務資料類型。  請參閱[資料類型][]的清單支援與[不受支援的資料類型][]。  [資料類型][]本文也包含查詢以識別[不支援的資料類型][]。  將轉換您的資料類型時，請務必查看的[資料類型的最佳作法][]。

## <a name="next-steps"></a>後續步驟
一旦您已成功移轉到 SQL Data Warehouse 的資料庫結構描述，前往下列文章︰

- [移轉您的資料][]
- [移轉您的程式碼][]

如需瞭解 SQL Data Warehouse 最佳作法的詳細資訊，請參閱[最佳作法][]。

<!--Image references-->

<!--Article references-->
[移轉您的程式碼]: ./sql-data-warehouse-migrate-code.md
[移轉您的資料]: ./sql-data-warehouse-migrate-data.md
[最佳作法]: ./sql-data-warehouse-best-practices.md
[表格概觀]: ./sql-data-warehouse-tables-overview.md
[不支援的表格功能]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[資料類型]: ./sql-data-warehouse-tables-data-types.md
[不支援的資料類型]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[資料類型的最佳作法]: ./sql-data-warehouse-tables-data-types.md#data-type-best-practices

<!--MSDN references-->


<!--Other Web references-->
