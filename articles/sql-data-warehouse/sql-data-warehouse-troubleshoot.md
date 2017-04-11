<properties
   pageTitle="疑難排解 Azure SQL Data Warehouse |Microsoft Azure"
   description="疑難排解 Azure SQL Data Warehouse。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/30/2016"
   ms.author="sonyama;barbkess"/>

# <a name="troubleshooting-azure-sql-data-warehouse"></a>疑難排解 Azure SQL Data Warehouse

本主題列出一些常見的疑難排解問題，我們聆聽我們的客戶。

## <a name="connecting"></a>連線

| 問題                              | 解決方法                                      |
| :----------------------------------| :---------------------------------------------- |
| 登入失敗使用者 ' NT AUTHORITY\ANONYMOUS 登入]。 (Microsoft SQL Server、 錯誤︰ 18456) | AAD 使用者嘗試連線到主資料庫中，但沒有母片使用者時，就會發生此錯誤。  若要修正此問題可能是指定您要連線至連線時，或將使用者新增至主版資料庫的 SQL Data Warehouse。  如需詳細資訊，請參閱[安全性概觀][]文章。|
|伺服器本金 「 MyUserName 」 不能在目前的安全性內容存取資料庫 」 母片]。 無法開啟使用者預設資料庫。 登入失敗。 登入失敗使用者 'MyUserName'。 (Microsoft SQL Server、 錯誤︰ 916) | AAD 使用者嘗試連線到主資料庫中，但沒有母片使用者時，就會發生此錯誤。  若要修正此問題可能是指定您要連線至連線時，或將使用者新增至主版資料庫的 SQL Data Warehouse。  如需詳細資訊，請參閱[安全性概觀][]文章。|
| CTAIP 錯誤                        | 登入已建立 SQL server 主要資料庫，但不是 SQL Data Warehouse 資料庫中時，會發生此錯誤。  如果發生此錯誤，看看[安全性概觀][]文章。  本文說明如何建立上母片，以及如何建立使用者 SQL Data Warehouse 資料庫中建立的登入和使用者。|
| 防火牆封鎖                |Azure SQL 資料庫受到保護的伺服器和資料庫層級的防火牆，以確保只有已知有資料庫的存取權的 IP 位址。 防火牆是安全的預設值，表示您必須明確啟用及 IP 位址或地址的範圍，才能連線。  若要設定您的防火牆存取，請遵循[設定伺服器防火牆存取您的用戶端 ip][] [提供指示][]的步驟進行。|
| 無法連線工具或驅動程式 | SQL Data Warehouse 建議使用[SSMS][]、 [Visual Studio 2015 SSDT][]或[sqlcmd][]來查詢您的資料。 如需驅動程式及連線至 SQL Data Warehouse 的詳細資訊，請參閱[Azure SQL Data Warehouse 驅動程式][]與[連線至 Azure SQL Data Warehouse][]文件。|


## <a name="tools"></a>工具

| 問題                              | 解決方法                                      |
| :----------------------------------| :---------------------------------------------- |
| 遺漏 AAD 使用者 visual Studio 物件檔案總管]。 | 這是已知的問題。  因應措施，請在[sys.database_principals][]檢視使用者。  請參閱[Azure SQL Data Warehouse 驗證][]，若要進一步瞭解使用 SQL Data Warehouse 的 Azure Active Directory。|

## <a name="performance"></a>效能

|  問題                             | 解決方法                                      |
| :----------------------------------| :---------------------------------------------- |
| 查詢效能疑難排解  | 如果您嘗試疑難排解的特定查詢，開始[學習如何監控您的查詢][]。|
| 不佳的查詢效能和計劃通常會造成的遺失統計資料   | 效能不佳的常見原因是缺乏表格的統計資料。  請參閱[統計資料-表格維護][Statistics]如需如何建立統計資料，以及為何您效能很重要詳細資訊。|
| 低並行 / 佇列中的查詢   | 若要瞭解如何使用並行平衡記憶體配置重要瞭解[工作量管理][]。|
| 如何實作最佳作法    | 最佳的位置開始，瞭解改善查詢效能的方法是[SQL Data Warehouse 最佳作法][]文件。|
| 如何改善效能與縮放比例  | 有時方案，以改善效能是直接新增多個運算 power 至您的查詢，以[調整您的 SQL Data Warehouse][]。|
| 當做不佳的索引品質不佳的查詢效能 | 一些時間查詢可以變慢，由於[不佳的 columnstore 索引品質][]。  請參閱本文的詳細資訊，以及如何[重建索引，以改善區段品質][]。|

## <a name="system-management"></a>系統管理

|  問題                             | 解決方法                                      |
| :----------------------------------| :---------------------------------------------- |
| 訊息 40847︰ 無法執行的作業，因為伺服器超過允許的資料庫交易單位的配額 45000。 | 您嘗試建立的資料庫或[要求配額增加][] [DWU][]減少。|
| 調查分鐘    | 若要瞭解您的系統的空間使用率的[表格大小][]，請參閱。|
| 協助管理資料表          | 請參閱[表格概觀][Overview]文件以協助管理您的資料表。  本文也包含連結[資料表的資料類型]等的詳細主題將[Data types]，[發佈表格][Distribute]，[編製索引的資料表][Index]，[分割表格][Partition]，[維護資料表統計][Statistics]和[暫時表格][Temporary]。|

## <a name="polybase"></a>Polybase

|  問題                             | 解決方法                                      |
| :----------------------------------| :---------------------------------------------- |
| Utf-8 錯誤                        |  目前 PolyBase 只支援載入已 utf-8 編碼的資料檔案。  如需如何解決這項限制，請參閱[使用周圍 PolyBase utf-8 需求][]。|
| 載入失敗，因為大型資料列   | 目前 Polybase 沒有支援大型資料列。  這表示，如果資料表包含 varchar （max）、 nvarchar （max） 或 varbinary （max），外部表格無法載入您的資料。  載入大型資料列的是目前僅支援透過 Azure 資料工廠 （含 BCP)、 Azure 資料流分析、 SSIS、 BCP 或.NET SQLBulkCopy 類別。 在未來版本，將會新增 PolyBase 支援大型資料列。|
| 與最大值的資料類型的表格的 bcp 載入失敗 | 有這需要 varchar （max）、 nvarchar （max） 或 varbinary （max） 會放在某些情況下表結尾處的已知的問題。  請嘗試將最大值欄移至表格的結尾。|

## <a name="differences-from-sql-database"></a>從 SQL 資料庫的差異

|  問題                             | 解決方法                                      |
| :----------------------------------| :---------------------------------------------- |
| 不支援的 SQL 資料庫功能  | 請參閱[不支援的表格功能][]。|
| 不支援的 SQL 資料庫的資料類型  | 請參閱[不支援的資料類型][]。|
| 刪除和更新限制      | 請參閱[更新因應措施][][刪除因應措施][]，然後[使用 CTAS 解決不支援的更新及刪除語法][]。  |
| 不支援合併陳述式   | 請參閱[合併因應措施][]。|
| 預存程序的限制       | 若要瞭解預存程序的限制的部分的[預存程序的限制][]，請參閱。|
| Udf 不支援 SELECT 陳述式 | 這是目前我們 Udf 的限制。  我們支援的語法，請參閱[建立函數][]。   |

## <a name="next-steps"></a>後續步驟

如果您是無法找到您的問題上述解決方案，以下是您可以嘗試的一些其他資源。

- [部落格]
- [功能要求]
- [視訊]
- [貓小組部落格]
- [建立支援票證]
- [MSDN 論壇]
- [堆疊溢位論壇]
- [Twitter]

<!--Image references-->

<!--Article references-->
[安全性概觀]: ./sql-data-warehouse-overview-manage-security.md
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx
[Visual Studio 2015 SSDT]: ./sql-data-warehouse-install-visual-studio.md
[Azure SQL Data Warehouse 驅動程式]: ./sql-data-warehouse-connection-strings.md
[連線至 SQL Azure Data Warehouse]: ./sql-data-warehouse-connect-overview.md
[建立支援票證]: ./sql-data-warehouse-get-started-create-support-ticket.md
[調整您的 SQL Data Warehouse]: ./sql-data-warehouse-manage-compute-overview.md
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[要求增加的配額]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change 
[學習如何監控您的查詢]: ./sql-data-warehouse-manage-monitor.md
[佈建的指示]: ./sql-data-warehouse-get-started-provision.md
[針對您的用戶端 IP 設定伺服器防火牆存取權]: ./sql-data-warehouse-get-started-provision.md#create-a-new-azure-sql-server-level-firewall
[SQL Data Warehouse 最佳作法]: ./sql-data-warehouse-best-practices.md
[表格大小]: ./sql-data-warehouse-tables-overview.md#table-size-queries
[不支援的表格功能]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[不支援的資料類型]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[不佳的 columnstore 索引品質]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[重建索引，以改善區段品質]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[工作量管理]: ./sql-data-warehouse-develop-concurrency.md
[若要解決不支援的更新及刪除語法使用 CTAS]: ./sql-data-warehouse-develop-ctas.md#using-ctas-to-work-around-unsupported-features
[更新因應措施]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[刪除因應措施]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[合併因應措施]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[預存程序的限制]: ./sql-data-warehouse-develop-stored-procedures.md#limitations
[Azure SQL Data Warehouse 驗證]: ./sql-data-warehouse-authentication.md
[解決 PolyBase utf-8 需求]: ./sql-data-warehouse-load-polybase-guide.md#working-around-the-polybase-utf-8-requirement

<!--MSDN references-->
[sys.database_principals]: https://msdn.microsoft.com/library/ms187328.aspx
[建立函數]: https://msdn.microsoft.com/library/mt203952.aspx
[sqlcmd]: https://azure.microsoft.com/en-us/documentation/articles/sql-data-warehouse-get-started-connect-sqlcmd/

<!--Other Web references-->
[部落格]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[貓小組部落格]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[功能要求]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN 論壇]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse
[堆疊溢位論壇]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[視訊]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse

