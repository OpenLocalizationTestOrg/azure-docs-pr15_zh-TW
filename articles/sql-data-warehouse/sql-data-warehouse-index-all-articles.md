<properties
    pageTitle="SQL Data Warehouse 服務的所有主題 |Microsoft Azure"
    description="Azure 服務的所有主題的資料表命名為 SQL Data Warehouse 存在於 http://azure.microsoft.com/documentation/articles/、 標題和描述。"
    services="sql-data-warehouse"
    documentationCenter=""
    authors="barbkess"
    manager="jhubbard"
    editor="MightyPen"/>

<tags
    ms.service="sql-data-warehouse"
    ms.workload="sql-data-warehouse"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="barbkess"/>


# <a name="all-topics-for-azure-sql-data-warehouse-service"></a>Azure SQL Data Warehouse 服務的所有主題

本主題列出直接適用於**SQL Data Warehouse**服務 Azure 的每個主題。 您可以使用**Ctrl + F**，尋找目前感興趣的主題中搜尋關鍵字此網頁。




## <a name="new"></a>新

| &nbsp; | 標題 | 描述 |
| --: | :-- | :-- |
| 1 | [SQL Data Warehouse 備份](sql-data-warehouse-backups.md) | 深入了解 SQL Data Warehouse 內建的資料庫備份，讓您能夠還原 Azure SQL Data Warehouse 還原點或不同的地理區域。 |


## <a name="updated-articles-sql-data-warehouse"></a>更新文件時，SQL Data Warehouse

此區段列出文件已更新，更新已大或嚴重的位置。 每個更新的文件，會顯示概略新增的 markdown 文字的程式碼片段。 文件已更新至**2016年-10-05** **2016年-08 22**的日期範圍內。

| &nbsp; | 文件 | 更新的文字、 程式碼片段 | 更新的時機 |
| --: | :-- | :-- | :-- |
| 2 | [將 SQL Data Warehouse (PolyBase) 將資料載入從 Azure blob 儲存體](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) | /-追蹤檔案和位元組選取 r.command、 s.request_id r.status，計數 (相異 input_name) 為 nbr_files，加總 (s.bytes_processed) / 1024年/1024年為從 sys.dm_pdw_exec_requests r 內部聯結 sys.dm_pdw_dms_external_work s 上 r.request_id gb_processed = s.request_id 位置 r。 標籤 = ' CTAS︰ 載入 cso。 DimProduct ' 或 r。 標籤 = ' CTAS︰ 載入 cso。 群組依據 r.command FactOnlineSales 的、 s.request_id、 nbr_files 遞減順序來 r.status gb_processed 遞減]。  | 2016-09-07 |
| 3 | [SQL Data Warehouse 還原](sql-data-warehouse-restore-database-overview.md) | **我可以還原暫停的資料倉庫？** 若要還原已暫停資料倉庫，您需要先將其重新連線。 資料倉庫恢復連線後，您必須選擇還原點七天。 **還原多餘地理區域**如果您使用的地理多餘的儲存空間，您可以還原資料倉庫，即成對的資料中心不同地理區域。 資料倉庫會從上一次的每日備份還原。 **還原時間表**您可以在過去七天內任何還原點還原資料庫。 快照開始每四到八小時，可以使用七天。 快照超過七天後，請將其到期，而且其還原點已不再提供。 **還原成本**還原的資料倉庫儲存費用被付費 Azure 進階版儲存空間的速度。 如果您將游標停留還原的資料倉庫，您會在 Azure 進階版儲存體工資率的儲存空間。 暫停的優點是您不是收費 | 2016-09-29 |





## <a name="get-started"></a>快速入門

| &nbsp; | 標題 | 描述 |
| --: | :-- | :-- |
| 4 | [Azure SQL Data Warehouse 驗證](sql-data-warehouse-authentication.md) | Azure Active Directory (AAD) 和 SQL Server 驗證 Azure SQL Data Warehouse。 |
| 5 | [Azure SQL Data Warehouse 的最佳作法](sql-data-warehouse-best-practices.md) | 建議及最佳作法，您應該知道您為開發 Azure SQL Data Warehouse 解決方案。 這些可協助您順利。 |
| 6 | [Azure SQL Data Warehouse 驅動程式](sql-data-warehouse-connection-strings.md) | 連接字串和 SQL Data Warehouse 驅動程式 |
| 7 | [連線至 SQL Azure Data Warehouse](sql-data-warehouse-connect-overview.md) | 如何找到的伺服器名稱與連接字串您至 Azure SQL Data Warehouse |
| 8 | [Azure 電腦學習使用分析資料](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md) | 用於 Azure 電腦學習建立預測的電腦學習基礎 Azure SQL Data Warehouse 中儲存的資料模型。 |
| 9 | [查詢 Azure SQL Data Warehouse (sqlcmd)](sql-data-warehouse-get-started-connect-sqlcmd.md) | 使用命令列公用程式 sqlcmd 查詢 Azure SQL Data Warehouse。 |
| 10 | [使用 TRANSACT-SQL (TSQL) 來建立 SQL Data Warehouse 資料庫](sql-data-warehouse-get-started-create-database-tsql.md) | 瞭解如何使用 TSQL 建立 Azure SQL Data Warehouse |
| 11 | [如何建立 SQL Data Warehouse 支援票證](sql-data-warehouse-get-started-create-support-ticket.md) | 如何建立 Azure SQL Data Warehouse 支援票證。 |
| 12 | [Azure 資料工廠載入資料](sql-data-warehouse-get-started-load-with-azure-data-factory.md) | 瞭解如何載入 Azure 資料工廠的資料 |
| 13 | [在 SQL Data Warehouse PolyBase 載入資料](sql-data-warehouse-get-started-load-with-polybase.md) | 瞭解 PolyBase 功能，以及如何使用它倉儲案例的資料。 |
| 14 | [建立 SQL Azure Data Warehouse](sql-data-warehouse-get-started-provision.md) | 瞭解如何建立 Azure SQL Data Warehouse Azure 入口網站中 |
| 15 | [建立 SQL Data Warehouse 使用 PowerShell](sql-data-warehouse-get-started-provision-powershell.md) | 使用 PowerShell 來建立 SQL Data Warehouse |
| 16 | [使用 Power BI 的資料視覺化](sql-data-warehouse-get-started-visualize-with-power-bi.md) | 使用 Power BI 的 SQL Data Warehouse 資料視覺化 |
| 17 | [查詢 Azure SQL Data Warehouse (Visual Studio)](sql-data-warehouse-query-visual-studio.md) | 使用 Visual Studio SQL Data Warehouse 查詢。 |



## <a name="develop"></a>開發

| &nbsp; | 標題 | 描述 |
| --: | :-- | :-- |
| 18 | [最佳化 SQL Data Warehouse 的交易](sql-data-warehouse-develop-best-practices-transactions.md) | 撰寫有效的交易更新中 Azure SQL Data Warehouse 的最佳作法指引 |
| 19 | [在 SQL Data Warehouse 並行和工作負載的管理](sql-data-warehouse-develop-concurrency.md) | 瞭解在 Azure SQL Data Warehouse 並行和工作量管理開發解決方案。 |
| 20 | [在 SQL Data Warehouse，以選取 (CTAS) 中建立資料表](sql-data-warehouse-develop-ctas.md) | 建立資料表選取 (CTAS) 陳述式中 Azure SQL Data Warehouse 編碼開發解決方案的秘訣。 |
| 21 | [在 SQL Data Warehouse 動態 SQL](sql-data-warehouse-develop-dynamic-sql.md) | 使用動態 SQL Azure SQL Data Warehouse 中開發解決方案的秘訣。 |
| 22 | [群組依據在 SQL Data Warehouse 中的選項](sql-data-warehouse-develop-group-by-options.md) | 適用於開發解決方案實作 Azure SQL Data Warehouse 中的 [選項] 群組中的秘訣。 |
| 23 | [使用 SQL Data Warehouse 樂器查詢的標籤](sql-data-warehouse-develop-label.md) | 使用標籤樂器查詢中 Azure SQL Data Warehouse 開發解決方案的秘訣。 |
| 24 | [在 SQL Data Warehouse 迴圈](sql-data-warehouse-develop-loops.md) | TRANSACT-SQL 迴圈及取代的資料指標中 Azure SQL Data Warehouse 開發解決方案的秘訣。 |
| 25 | [在 SQL Data Warehouse 預存程序](sql-data-warehouse-develop-stored-procedures.md) | 秘訣實作預存程序 Azure SQL Data Warehouse 開發解決方案。 |
| 26 | [SQL Data Warehouse 交易](sql-data-warehouse-develop-transactions.md) | 針對開發解決方案實作 Azure SQL Data Warehouse 中的交易的秘訣。 |
| 27 | [在 SQL Data Warehouse 使用者定義的結構描述](sql-data-warehouse-develop-user-defined-schemas.md) | 使用 TRANSACT-SQL 結構描述中 Azure SQL Data Warehouse 開發解決方案的秘訣。 |
| 28 | [指派 SQL Data Warehouse 中的變數](sql-data-warehouse-develop-variable-assignment.md) | 指定 SQL Azure SQL Data Warehouse 變數開發解決方案的秘訣。 |
| 29 | [SQL Data Warehouse 中的檢視](sql-data-warehouse-develop-views.md) | 使用 TRANSACT-SQL 中的檢視 Azure SQL Data Warehouse 開發解決方案的秘訣。 |
| 30 | [決策和 SQL Data Warehouse 的編碼技術](sql-data-warehouse-overview-develop.md) | 開發概念、 決策、 建議和 SQL Data Warehouse 編碼技巧。 |



## <a name="manage"></a>管理

| &nbsp; | 標題 | 描述 |
| --: | :-- | :-- |
| 31 | [管理運算能力中 Azure SQL Data Warehouse （概觀）](sql-data-warehouse-manage-compute-overview.md) | 效能功能 Azure SQL Data Warehouse 延展。 藉由調整 DWUs 擴展或暫停並繼續儲存成本的計算資源。 |
| 32 | [管理 Azure SQL Data Warehouse （Azure 入口網站） 中的計算 power](sql-data-warehouse-manage-compute-portal.md) | Azure 入口網站的工作，管理計算 power。 縮放比例調整 DWUs 計算資源。 或者，將游標暫停於，然後繼續儲存成本的計算資源。 |
| 33 | [管理運算中 Azure SQL Data Warehouse (PowerShell) 的能力](sql-data-warehouse-manage-compute-powershell.md) | 若要管理的 PowerShell 工作計算 power。 縮放比例調整 DWUs 計算資源。 或者，將游標暫停於，然後繼續儲存成本的計算資源。 |
| 34 | [管理運算能力中 Azure SQL 資料倉庫 （剩餘）](sql-data-warehouse-manage-compute-rest-api.md) | 若要管理的 PowerShell 工作計算 power。 縮放比例調整 DWUs 計算資源。 或者，將游標暫停於，然後繼續儲存成本的計算資源。 |
| 35 | [管理運算能力中 Azure SQL Data Warehouse (T SQL)](sql-data-warehouse-manage-compute-tsql.md) | 擴充效能調整 DWUs TRANSACT-SQL (T SQL) 工作。 儲存成本回在非尖峰期間縮放比例。 |
| 36 | [監控您使用 Dmv 的工作量](sql-data-warehouse-manage-monitor.md) | 瞭解如何監視使用 Dmv 工作量。 |
| 37 | [管理 Azure SQL Data Warehouse 的資料庫](sql-data-warehouse-overview-manage.md) | 管理 SQL Data Warehouse 資料庫的概觀。 包含管理工具，DWUs 及擴充效能，疑難排解查詢效能，建立好的安全性原則，以及從資料損毀或地區的資料，請還原資料庫。 |
| 38 | [監控 Azure SQL Data Warehouse 中的使用者查詢](sql-data-warehouse-overview-manage-user-queries.md) | 考量、 最佳作法和監視 Azure SQL Data Warehouse 中的使用者查詢的工作概觀 |
| 39 | [SQL Data Warehouse 還原](sql-data-warehouse-restore-database-overview.md) | 復原資料庫中 Azure SQL Data Warehouse 資料庫還原選項的概觀。 |
| 40 | [還原 Azure SQL Data Warehouse （入口網站）](sql-data-warehouse-restore-database-portal.md) | 還原 Azure SQL Data Warehouse azure 入口網站的任務。 |
| 41 | [還原 Azure SQL Data Warehouse (PowerShell)](sql-data-warehouse-restore-database-powershell.md) | 還原 Azure SQL Data Warehouse PowerShell 任務。 |
| 42 | [還原 Azure SQL Data Warehouse (REST API)](sql-data-warehouse-restore-database-rest-api.md) | 還原 Azure SQL Data Warehouse REST API 工作。 |



## <a name="tables-and-indexes"></a>資料表和索引

| &nbsp; | 標題 | 描述 |
| --: | :-- | :-- |
| 43 | [SQL Data Warehouse 中資料表的資料類型](sql-data-warehouse-tables-data-types.md) | 開始使用 Azure SQL Data Warehouse 資料表的資料類型。 |
| 44 | [發送 SQL Data Warehouse 中的資料表](sql-data-warehouse-tables-distribute.md) | 快速入門發送 Azure SQL Data Warehouse 中的資料表。 |
| 45 | [在 SQL Data Warehouse 編製索引的資料表](sql-data-warehouse-tables-index.md) | 開始使用中 Azure SQL Data Warehouse 編製索引的資料表。 |
| 46 | [在 SQL Data Warehouse 表格概觀](sql-data-warehouse-tables-overview.md) | 開始使用 Azure SQL 倉庫資料表。 |
| 47 | [分割表格中 SQL Data Warehouse](sql-data-warehouse-tables-partition.md) | 開始使用資料表中 Azure SQL Data Warehouse 分割。 |
| 48 | [管理 SQL Data Warehouse 中資料表的統計資料](sql-data-warehouse-tables-statistics.md) | 開始使用 Azure SQL Data Warehouse 中資料表的統計資料。 |
| 49 | [在 SQL Data Warehouse 暫存的資料表](sql-data-warehouse-tables-temporary.md) | 開始使用中 Azure SQL Data Warehouse 暫存的資料表。 |



## <a name="integrate"></a>整合

| &nbsp; | 標題 | 描述 |
| --: | :-- | :-- |
| 50 | [使用 SQL Data Warehouse Azure 資料工廠](sql-data-warehouse-integrate-azure-data-factory.md) | Azure SQL Data Warehouse Azure 資料工廠 (ADF) 使用開發解決方案的秘訣。 |
| 51 | [使用 SQL Data Warehouse Azure 機器學習](sql-data-warehouse-integrate-azure-machine-learning.md) | 教學課程中的運用 Azure 電腦學習 Azure SQL Data Warehouse 開發解決方案。 |
| 52 | [使用 SQL Data Warehouse Azure 資料流狀況分析](sql-data-warehouse-integrate-azure-stream-analytics.md) | 適用於開發解決方案中使用 Azure SQL Data Warehouse Azure 資料流分析的秘訣。 |
| 53 | [使用 SQL Data Warehouse Power BI](sql-data-warehouse-integrate-power-bi.md) | 使用 Power BI Azure SQL Data Warehouse 具有開發解決方案的秘訣。 |
| 54 | [運用 SQL Data Warehouse 與其他服務](sql-data-warehouse-overview-integrate.md) | 工具和合作夥伴使用與 SQL Data Warehouse 整合的解決方案。  |



## <a name="load"></a>載入

| &nbsp; | 標題 | 描述 |
| --: | :-- | :-- |
| 55 | [從 Azure blob 儲存體中載入 Azure SQL Data Warehouse （Azure 資料工廠） 的資料](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md) | 瞭解如何載入 Azure 資料工廠的資料 |
| 56 | [將 SQL Data Warehouse (PolyBase) 將資料載入從 Azure blob 儲存體](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) | 瞭解如何使用 PolyBase 資料從 Azure blob 儲存體載入 SQL Data Warehouse。 公用資料載入 Contoso 零售 Data Warehouse 結構描述幾個資料表。 |
| 57 | [從 SQL Server 資料載入 Azure SQL Data Warehouse (AZCopy)](sql-data-warehouse-load-from-sql-server-with-azcopy.md) | 使用 bcp SQL Server 資料匯出到一般檔案，以匯入資料至 Azure blob 儲存體，AZCopy 和 PolyBase 至 Azure SQL Data Warehouse 將內嵌的資料。 |
| 58 | [從 SQL Server 資料載入 Azure SQL Data Warehouse （一般檔案）](sql-data-warehouse-load-from-sql-server-with-bcp.md) | 對於較小的資料的大小，使用 bcp 資料匯出到一般檔案的 SQL Server，並直接將 Azure SQL Data Warehouse 匯入資料。 |
| 59 | [將資料載入從 SQL Server 到 Azure SQL 資料倉庫 (SSIS)](sql-data-warehouse-load-from-sql-server-with-integration-services.md) | 顯示如何建立 SQL Server 整合服務 (SSIS) 套件，將 SQL Data Warehouse 從各種不同資料來源的資料。 |
| 60 | [在 SQL Data Warehouse PolyBase 載入資料](sql-data-warehouse-load-from-sql-server-with-polybase.md) | 使用 bcp SQL Server 資料匯出到一般檔案，以匯入資料至 Azure blob 儲存體，AZCopy 和 PolyBase 至 Azure SQL Data Warehouse 將內嵌的資料。 |
| 61 | [使用 SQL Data Warehouse PolyBase 指南](sql-data-warehouse-load-polybase-guide.md) | 指導方針，使用 PolyBase 在 SQL Data Warehouse 案例中的建議。 |
| 62 | [將 SQL Data Warehouse 載入範例資料](sql-data-warehouse-load-sample-databases.md) | 將 SQL Data Warehouse 載入範例資料 |
| 多為 63 | [載入 bcp 的資料](sql-data-warehouse-load-with-bcp.md) | 瞭解什麼 bcp，以及如何使用此資料倉儲案例。 |
| 64 | [將資料載入至 Azure SQL Data Warehouse](sql-data-warehouse-overview-load.md) | 瞭解資料載入到 SQL Data Warehouse 常見的情況。 包括使用 PolyBase、 Azure blob 儲存體、 一般檔案，以及磁碟傳送。 您也可以使用協力廠商工具。 |



## <a name="migrate"></a>移轉

| &nbsp; | 標題 | 描述 |
| --: | :-- | :-- |
| 65 | [將您的 SQL 程式碼移轉到 SQL Data Warehouse](sql-data-warehouse-migrate-code.md) | 適用於開發解決方案，移轉至 Azure SQL Data Warehouse 的 SQL 程式碼的秘訣。 |
| 66 | [移轉您的資料](sql-data-warehouse-migrate-data.md) | 適用於開發解決方案時，將您的資料移轉到 Azure SQL Data Warehouse 的秘訣。 |
| 67 | [資料倉庫移轉公用程式 （預覽版本）](sql-data-warehouse-migrate-migration-utility.md) | 移轉到 SQL Data Warehouse。 |
| 68 | [將您的結構描述移轉到 SQL Data Warehouse](sql-data-warehouse-migrate-schema.md) | 移轉您的結構描述至 Azure SQL Data Warehouse，開發解決方案的秘訣。 |
| 69 | [將您的方案移轉到 SQL Data Warehouse](sql-data-warehouse-overview-migrate.md) | 將您的方案 Azure SQL Data Warehouse 平台的移轉指南。 |



## <a name="partners"></a>合作夥伴

| &nbsp; | 標題 | 描述 |
| --: | :-- | :-- |
| 70 | [SQL Data Warehouse 商務智慧合作夥伴](sql-data-warehouse-partner-business-intelligence.md) | 使用支援的 SQL Data Warehouse 的解決方案的協力廠商商務智慧協力廠商的清單。 |
| 71 | [SQL Data Warehouse 資料整合合作夥伴](sql-data-warehouse-partner-data-integration.md) | 資料整合解決方案的支援 Azure SQL Data Warehouse 協力廠商的清單。 |
| 72 | [SQL Data Warehouse 資料管理合作夥伴](sql-data-warehouse-partner-data-management.md) | 使用支援的 SQL Data Warehouse 的解決方案的協力廠商資料管理協力廠商的清單。 |



## <a name="reference"></a>參照

| &nbsp; | 標題 | 描述 |
| --: | :-- | :-- |
| 73 | [SQL Data Warehouse 參考主題](sql-data-warehouse-overview-reference.md) | 參考資料的 SQL Data Warehouse 內容連結。 |
| 74 | [PowerShell cmdlet 和 SQL Data Warehouse 的 REST Api](sql-data-warehouse-reference-powershell-cmdlets.md) | Azure SQL Data Warehouse 包括來暫停或繼續資料庫的方法，尋找上方的 PowerShell 指令程式。 |
| 75 | [語言項目](sql-data-warehouse-reference-tsql-language-elements.md) | 使用 SQL Data Warehouse TRANSACT-SQL 語言項目的參考內容的連結清單。 |
| 76 | [TRANSACT-SQL 主題](sql-data-warehouse-reference-tsql-statements.md) | 使用 SQL Data Warehouse TRANSACT-SQL 主題參考內容的連結。 |
| 77 | [系統檢視](sql-data-warehouse-reference-tsql-system-views.md) | 連結至系統的 SQL Data Warehouse 檢視內容。 |



## <a name="security"></a>安全性

| &nbsp; | 標題 | 描述 |
| --: | :-- | :-- |
| 78 | [SQL Data Warehouse-下層用戶端支援 Masking 的稽核和動態資料](sql-data-warehouse-auditing-downlevel-clients.md) | 深入了解稽核資料的 SQL Data Warehouse 下層用戶端支援 |
| 79 | [在 SQL Azure Data Warehouse 稽核](sql-data-warehouse-auditing-overview.md) | 快速入門中 Azure SQL Data Warehouse 稽核 |
| 80 | [開始使用透明資料加密 (TDE) 中 SQL Data Warehouse](sql-data-warehouse-encryption-tde.md) | 在 SQL Data Warehouse 透明資料加密 (TDE) |
| 81 | [開始使用透明資料加密 (TDE)](sql-data-warehouse-encryption-tde-tsql.md) | 在 SQL Data Warehouse (T SQL) 中的透明資料加密 (TDE) |
| 82 | [保護資料庫安全之 SQL Data Warehouse 中](sql-data-warehouse-overview-manage-security.md) | 設定資料庫中 Azure SQL Data Warehouse 安全性開發解決方案的秘訣。 |



## <a name="miscellaneous"></a>其他

| &nbsp; | 標題 | 描述 |
| --: | :-- | :-- |
| 83 | [將的 SQL Data Warehouse Visual Studio 2015 及 SSDT](sql-data-warehouse-install-visual-studio.md) | Azure SQL Data Warehouse 安裝 Visual Studio 與 SQL Server 開發 Tools (SSDT) |
| 84 | [移轉到進階版儲存詳細資料](sql-data-warehouse-migrate-to-premium-storage.md) | 移轉現有的 SQL Data Warehouse 進階版存放裝置的指示 |
| 鍵入 [85 | [快速入門威脅偵測](sql-data-warehouse-security-threat-detection.md) | 如何開始使用威脅偵測 |
| 86 | [SQL Data Warehouse 容量限制](sql-data-warehouse-service-capacity-limits.md) | 連線、 資料庫、 表格和查詢的 SQL Data Warehouse 中最大值。 |
| 87 | [疑難排解 Azure SQL Data Warehouse](sql-data-warehouse-troubleshoot.md) | 疑難排解 Azure SQL Data Warehouse。 |

