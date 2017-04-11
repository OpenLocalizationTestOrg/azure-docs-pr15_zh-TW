<properties
   pageTitle="SQL Data Warehouse 容量限制 |Microsoft Azure"
   description="連線、 資料庫、 表格和查詢的 SQL Data Warehouse 中最大值。"
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
   ms.date="09/01/2016"
   ms.author="sonyama;barbkess;jrj"/>

# <a name="sql-data-warehouse-capacity-limits"></a>SQL Data Warehouse 容量限制

下列表格包含各種元件 Azure SQL Data Warehouse 所允許的最大值。


## <a name="workload-management"></a>工作量管理

| 類別            | 描述                                       | 最大值            |
| :------------------ | :------------------------------------------------ | :----------------- |
| [資料倉庫單位 (DWU)][]| 最大值 DWU 的單一的 SQL Data Warehouse | 6000               |
| [資料倉庫單位 (DWU)][]| 最大值 DWU 單一的 SQL server         | 依預設 6000<br/><br/> 根據預設，每個 SQL server (例如 myserver.database.windows.net) 會有可讓最 6000 DWU 45,000 DTU 配額。 此配額是只要安全性限制。 您可以增加您[建立的支援票證][]，然後選取要求類型的*配額*的配額。  若要計算您 DTU 必須，7.5 乘以 DWU 所需的總計。 您可以在入口網站檢視您目前的 DTU 消耗從 SQL server 刀。 暫停和取消暫停資料庫計算至 DTU 配額。 |
| 資料庫連線 | 同時開啟的工作階段                          | 1024<br/><br/>我們支援上限為 1024年作用中的連線，每一種可以同時送出到 SQL Data Warehouse 資料庫的查詢。 請注意，有限制的實際可同時執行的查詢數。 並行限制超過時，要求進入內部佇列等待處理。|
| 資料庫連線 | 準備好陳述式的記憶體            | 20 MB              |
| [工作量管理][] | 最大同步的查詢                    | 32<br/><br/> 根據預設，SQL Data Warehouse 可以執行 32 同步查詢和佇列剩餘查詢的最大值。<br/><br/>當使用者被指派到較高的資源類別或 SQL Data Warehouse 設定低 DWU，可能會降低並行存取層級。 若要執行永遠允許某些查詢，例如 DMV 查詢。|
| [Tempdb][]          | 最大值的 Tempdb                                | 399 DW100 每 GB。 因此，DWU1000 Tempdb 大小為 3.99 TB |


## <a name="database-objects"></a>資料庫物件

| 類別          | 描述                                  | 最大值            |
| :---------------- | :------------------------------------------- | :----------------- |
| 資料庫          | 最大值                                     | 壓縮磁碟上的 240 TB<br/><br/>此空間無關的 tempdb 或記錄空間，因此這個空間致力於為永久資料表。  群組直的 columnstore 壓縮估計為 5 X。  此壓縮讓資料庫放大約 1 PB 所有資料表時直的 columnstore （預設表格類型）。|
| 表格             | 最大值                                     | 壓縮磁碟上的 60 TB   |
| 表格             | 每個資料庫表格                          | 2 億          |
| 表格             | 每個資料表的資料行                            | 1024 個資料行       |
| 表格             | 位元組每一個資料行                             | 資料行[資料類型][]而定。  限制為 char 資料類型，8000，4000 的 nvarchar 或 2 GB 最大值的資料類型。|
| 表格             | 每個資料列中，定義大小位元組                  | 8060 位元組<br/><br/>頁面壓縮是 SQL Server 時相同的方式計算的每個資料列的位元組數。 SQL Server，例如 SQL Data Warehouse 會支援這可讓按關閉列**變數長度的資料行**的資料列溢位儲存空間。 當列關閉推入變數長度列時，只 24 個位元組根會儲存的主要記錄。 如需詳細資訊，請參閱 MSDN 文章︰[資料列溢位資料超過 8 KB][] 。|
| 表格             | 每個資料表的磁碟分割區                    | 15000<br/><br/>高的效能，建議您最小化的數字的磁碟分割區您需要時仍支援您企業的需求。 當磁碟分割區的規模擴大時，資料定義語言 (DDL) 和資料操作語言 (DML) 作業開銷規模擴大時，並讓緩慢的效能。|
| 表格             | 分割邊界值每個字元。| 4000 |
| 索引             | 每個資料表的非叢集索引。        | 999<br/><br/>適用於僅限 rowstore 資料表。|
| 索引             | 群組直每個資料表的索引。            | 1<br><br/>適用於 rowstore 和 columnstore 的資料表。|
| 索引             | 索引鍵的大小。                          | 900 個位元組。<br/><br/>適用於只 rowstore 索引。<br/><br/>如果資料行中的現有資料不超過 900 個位元組索引建立時，您可以建立 varchar 資料行的多個 900 個位元組的最大的索引。 不過，稍後插入或更新導致的總大小超過 900 個位元組資料行的動作會失敗。|
| 索引             | 每個索引鍵資料行。                   | 16<br/><br/>適用於只 rowstore 索引。 群組直的 columnstore 索引中包含所有的欄。|
| 統計資料        | 合併資料行值的大小。      | 900 個位元組。         |
| 統計資料        | 每個統計資料物件的資料行。           | 32                 |
| 統計資料        | 每個資料表的資料行上建立的統計資料。 | 30000            |
| 預存程序 | 最大巢狀層級。               | 8                 |
| 檢視              | 每個檢視的資料行                         | 1024             |


## <a name="loads"></a>載入

| 類別          | 描述                                  | 最大值            |
| :---------------- | :------------------------------------------- | :----------------- |
| Polybase 載入    | 每個資料列的位元組                                | 32768<br/><br/>Polybase 載入限於小於 32 K 載入兩個列，並無法載入 VARCHR(MAX)、 nvarchar （max） 或 varbinary （max）。  雖然這項限制今天存在，它將會移除相當推出。<br/><br/>


## <a name="queries"></a>查詢

| 類別          | 描述                                  | 最大值            |
| :---------------- | :------------------------------------------- | :----------------- |
| 查詢             | 在使用者資料表的佇列的查詢。               | 1000               |
| 查詢             | 同時檢視系統的查詢。          | 100                |
| 查詢             | 佇列的查詢系統檢視               | 1000               |
| 查詢             | 最大的參數                           | 2098               |
| 批次             | 最大值                                 | 65、 536 * 4096        |
| 選取的結果    | 每個資料列的欄                              | 4096<br/><br/>絕對不能超過 4096 每個資料列的欄中選取的結果。 不保證，您可以隨時可以 4096 有。 如果查詢計劃需要暫存的資料表，可能會套用 1024 個資料行，每個最大值的資料表。|
| 選取            | 巢狀子查詢                            | 32<br/><br/>絕對不能超過 32 的巢狀子查詢中的 SELECT 陳述式。 不保證，您可以隨時可以 32 有。 例如，聯結可以介紹子查詢計劃。 子查詢數目也受限於可用的記憶體。|
| 選取            | 每個加入資料行                             | 1024 個資料行<br/><br/>絕對不能 1024 個以上的欄中加入。 不保證，您可以隨時可以 1024年有。 如果加入方案需要更多的欄數比加入結果的暫時資料表，1024年限制適用於暫存的資料表。 |
| 選取            | 群組依據欄每個位元組。                  | 8060<br/><br/>GROUP BY 子句中的資料行可以有 8060 位元組的最大值。|
| 選取            | 每個順序循欄位元組                   | 8060 位元組。<br/><br/>ORDER BY 子句中的資料行可以有 8060 位元組的最大值。|
| 識別項和每個陳述式的常數 | 數參照的識別碼和常數。 | 65535<br/><br/>SQL Data Warehouse 限制數識別碼和常數，可以包含在單一查詢的運算式。 這項限制為 65535。 超過此數字產生 SQL Server 錯誤 8632。 如需詳細資訊，請參閱[內部錯誤︰ 運算式服務的限制達到][]。|


## <a name="metadata"></a>中繼資料

| 系統檢視                        | 最大值的資料列 |
| :--------------------------------- | :------------|
| sys.dm_pdw_component_health_alerts | 10000       |
| sys.dm_pdw_dms_cores               | 100          |
| sys.dm_pdw_dms_workers             | DMS 同事的最新的 1000年的總數 SQL 要求。 |
| sys.dm_pdw_errors                  | 10000       |
| sys.dm_pdw_exec_requests           | 10000       |
| sys.dm_pdw_exec_sessions           | 10000       |
| sys.dm_pdw_request_steps           | 總數量的最新的 1000 SQL 要求儲存於 sys.dm_pdw_exec_requests 步驟的詳細資訊。 |
| sys.dm_pdw_os_event_logs           | 10000       |
| sys.dm_pdw_sql_requests            | 最近儲存於 sys.dm_pdw_exec_requests 的 1000 SQL 要求。 |


## <a name="next-steps"></a>後續步驟
如需參考資訊，請參閱[SQL Data Warehouse 參照概觀][]。

<!--Image references-->

<!--Article references-->
[資料倉庫單位 (DWU)]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[SQL Data Warehouse 參照概觀]: ./sql-data-warehouse-overview-reference.md
[工作量管理]: ./sql-data-warehouse-develop-concurrency.md
[Tempdb]: ./sql-data-warehouse-tables-temporary.md
[資料類型]: ./sql-data-warehouse-tables-data-types.md
[建立支援票證]: /sql-data-warehouse-get-started-create-support-ticket.md

<!--MSDN references-->
[資料列溢位資料超過 8 KB]: https://msdn.microsoft.com/library/ms186981.aspx
[內部錯誤︰ 已達運算式服務的限制]: https://support.microsoft.com/kb/913050
