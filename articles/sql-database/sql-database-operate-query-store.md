<properties
   pageTitle="作業系統 Azure SQL 資料庫中的查詢存放區"
   description="了解如何操作查詢中的存放區 Azure SQL 資料庫"
   keywords=""
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="sqldb-performance"
   ms.workload="data-management"
   ms.date="08/16/2016"
   ms.author="carlrab"/>

# <a name="operating-the-query-store-in-azure-sql-database"></a>作業系統查詢中的存放區 Azure SQL 資料庫 

Azure 中的查詢市集是完全受管理的資料庫功能的持續收集並顯示所有查詢歷程記錄的詳細的資訊。 您可以將查詢存放區的相關視為類似飛機航班資料錄製大幅簡化查詢效能疑難排解雲端的同時，內部部署的客戶。 本文說明方面作業系統 Azure 中的查詢存放區。 使用此預先收集的查詢資料，可以快速診斷並解決效能問題，因此都要花更多的時間，將焦點放在其企業。 

查詢儲存區已自 2015 年 11 月，[全域可用](https://azure.microsoft.com/updates/general-availability-azure-sql-database-query-store/)Azure SQL 資料庫。 查詢市集是效能分析及調整功能，例如[SQL 資料庫顧問和效能儀表板](https://azure.microsoft.com/updates/sqldatabaseadvisorga/)的基礎。 在發佈本文，查詢市集正在執行 Azure 中的多個 200000 使用者資料庫中收集的幾個月，而不會中斷的查詢的相關資訊。

> [AZURE.IMPORTANT] Microsoft 正在啟動查詢儲存為所有的 Azure SQL 資料庫 （用於現有及新）。 

## <a name="optimal-query-store-configuration"></a>最佳化查詢存放區設定

本節說明最佳化設定預設值，以確保可靠的操作，查詢市集和相關功能，例如[SQL 資料庫顧問和效能儀表板](https://azure.microsoft.com/updates/sqldatabaseadvisorga/)設計。 預設設定最適合連續資料集合的最小關閉/READ_ONLY 狀態中所花費的時間。

| 設定 | 描述 | 預設值 | 註解 |
| ------------- | ----------- | ------- | ------- |
| MAX_STORAGE_SIZE_MB | 指定查詢市集可以採取 z 客戶資料庫內的資料空格的限制 | 100 | 為新資料庫會強制執行 |
| INTERVAL_LENGTH_MINUTES | 定義期間收集執行階段查詢計劃的統計資料彙總而持續的時間範圍的大小。 每個作用中的查詢計劃的一段時間，使用此設定定義具有最一列 | 60   | 為新資料庫會強制執行 |
| STALE_QUERY_THRESHOLD_DAYS | 以時間為基礎的清理原則控制保留期間持續執行階段統計資料和非使用中的查詢 | 30 | 強制執行新的資料庫及用資料庫與先前的預設值 (367) |
| SIZE_BASED_CLEANUP_MODE | 指定是否自動資料清理查詢儲存資料大小接近限制 | 自動 | 強制執行所有資料庫 |
| QUERY_CAPTURE_MODE | 指定是否追蹤所有的查詢的子集 | 自動 | 強制執行所有資料庫 |
| FLUSH_INTERVAL_SECONDS | 指定最大期間擷取執行階段統計資料會保留在記憶體中，先清除磁碟 | 900 | 為新資料庫會強制執行 |
||||||

> [AZURE.IMPORTANT] 在查詢市集啟動所有 Azure SQL 資料庫 （請參閱前面重要附註） 中的最後一個階段，會自動套用下列預設值。 設定指示燈之後, Azure SQL 資料庫不會變更設定客戶的設定值除非它們產生負面影響主要的工作量或可靠的作業的查詢市集。

如果您想要保留您的自訂設定，可用於[變更資料庫查詢 Store 選項](https://msdn.microsoft.com/library/bb522682.aspx)還原先前的狀態設定。 若要瞭解如何頂端選擇最佳的設定參數查看[查詢存放區的最佳作法](https://msdn.microsoft.com/library/mt604821.aspx)。

## <a name="next-steps"></a>後續步驟

[SQL 資料庫效能充分](sql-database-performance.md)

## <a name="additional-resources"></a>其他資源

詳細資訊請參閱下列文章︰

- [資料庫的航班資料錄製器](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database) 

- [使用查詢存放區來監控效能](https://msdn.microsoft.com/library/dn817826.aspx)

- [查詢存放區的使用案例](https://msdn.microsoft.com/library/mt614796.aspx)

- [使用查詢存放區來監控效能](https://msdn.microsoft.com/library/dn817826.aspx) 
