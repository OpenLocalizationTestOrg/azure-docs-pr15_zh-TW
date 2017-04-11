<properties
   pageTitle="將您的方案移轉到 SQL Data Warehouse |Microsoft Azure"
   description="將您的方案 Azure SQL Data Warehouse 平台的移轉指南。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/30/2016"
   ms.author="barbkess;jrj;sonyama"/>

# <a name="migrate-your-solution-to-sql-data-warehouse"></a>將您的方案移轉到 SQL Data Warehouse

SQL Data Warehouse 是分散式的資料庫系統 elastically 縮放以符合您的需求。 若要維持效能和小數位數，並非所有功能，SQL Server 中的都實作 SQL Data Warehouse 內。 下列移轉主題輕觸上以 SQL Data Warehouse 移轉您的解決方案的主要因素。 設計資料倉儲刻度引進了不同的設計的圖樣與傳統，因此不一定最佳。 因此，您可能會發現的調整現有的方案，可確保您善用完整分散式 SQL Data Warehouse 所提供的平台。

也很重要記住 SQL Data Warehouse 會依據 Microsoft Azure 中的平台。 因此您移轉部分也可能會包含傳送資料至雲端。 資料傳輸是本身主旨和應該謹慎視為;特別是區增加。 資料傳輸和載入資料是不連續的主題。

## <a name="migration-guidance"></a>移轉指南

請確定您已閱讀下列文章，以確保之前進行移轉未來突破性了解部分產品與基本概念。

- [移轉您的結構描述][]
- [移轉您的資料][]
- [移轉您的程式碼][]

## <a name="next-steps"></a>後續步驟

貓 （客戶會談小組） 也有一些很棒的他們發佈到部落格的 SQL Data Warehouse 指引。  請查看其文章︰[移轉 Azure SQL Data Warehouse 在練習中資料][]的移轉額外指引。

<!--Image references-->

<!--Article references-->
[移轉您的結構描述]: sql-data-warehouse-migrate-schema.md
[移轉您的資料]: sql-data-warehouse-migrate-data.md
[移轉您的程式碼]: sql-data-warehouse-migrate-code.md


<!--MSDN references-->


<!--Other Web references-->
[Azure SQL Data Warehouse 在練習中移轉資料]: https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/
