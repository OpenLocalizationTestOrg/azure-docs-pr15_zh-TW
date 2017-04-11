<properties
    pageTitle="監控 XTP 記憶體內儲存 |Microsoft Azure"
    description="估計值和監視 XTP 記憶體內儲存使用，容量。解決容量錯誤 41823"
    services="sql-database"
    documentationCenter=""
    authors="jodebrui"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="jodebrui"/>


# <a name="monitor-in-memory-oltp-storage"></a>監視器 OLTP 記憶體內儲存空間

使用時[記憶體內 OLTP](sql-database-in-memory.md)，記憶體最佳化表格與資料表變數中的資料會保留在記憶體內 OLTP 儲存空間。 每個進階版服務層級的最大 OLTP 記憶體內儲存空間大小， [SQL 資料庫服務層文章](sql-database-service-tiers.md#service-tiers-for-single-databases)中記錄。 一旦超過此限制，插入和更新作業可能會開始失敗 （含錯誤 41823）。 此時，您會需要先將刪除回收記憶體的資料或升級您的資料庫的效能層。

## <a name="determine-whether-data-will-fit-within-the-in-memory-storage-cap"></a>決定是否資料範圍內記憶體內儲存在首字放大

決定儲存在首字放大︰ 不同的進階版服務層的儲存空間大寫字，請參閱[SQL 資料庫服務層文章](sql-database-service-tiers.md#service-tiers-for-single-databases)。

評估記憶體最佳化表格運作一樣的 SQL Server 時，會在 Azure SQL 資料庫的記憶體需求。 需要幾分鐘，才能檢閱[MSDN](https://msdn.microsoft.com/library/dn282389.aspx)上的主題。

請注意，表格及表格變數的資料列，以及索引，計算趨近於最大的使用者資料的大小。 此外，變更資料表都需要足夠的空間來建立新版本的整個資料表和索引。

## <a name="monitoring-and-alerting"></a>監視和警告

您可以監視[儲存覆蓋您效能層](sql-database-service-tiers.md#service-tiers-for-single-databases)Azure[入口網站](https://portal.azure.com/)中的百分比的記憶體內儲存使用︰ 

- 在資料庫刀中，找出 [資源使用狀況] 方塊中，按一下 [編輯]。
- 然後選取 [公制`In-Memory OLTP Storage percentage`。
- 若要新增提醒，按一下 [資源使用狀況] 方塊中，開啟公制刀，然後按一下 [新增提醒。

或者若要顯示的記憶體內儲存使用率使用下列查詢︰

    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats


## <a name="correct-out-of-memory-situations---error-41823"></a>正確的記憶體用完情況錯誤 41823

插入、 更新和建立作業失敗顯示任何錯誤訊息 41823 中執行的記憶體用完結果。

錯誤訊息 41823 表示的記憶體最佳化的資料表和資料表變數已超過大小上限。

此錯誤，[解決方式︰


- 刪除記憶體最佳化的表格，可能會將卸載傳統的磁碟的資料表; 資料的資料或，
- 將服務層升級至其中一個有足夠的記憶體內儲存，您需要將記憶體最佳化表格中的資料。

## <a name="next-steps"></a>後續步驟
其他資源，瞭解如何[監控 Azure SQL 資料庫使用動態管理檢視](sql-database-monitoring-with-dmvs.md)
