<properties
    pageTitle="管理及疑難排解伸展資料庫 |Microsoft Azure"
    description="瞭解如何管理及疑難排解伸展資料庫。"
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/27/2016"
    ms.author="douglasl"/>

# <a name="manage-and-troubleshoot-stretch-database"></a>管理及疑難排解伸展資料庫

若要管理及疑難排解伸展資料庫，使用的工具和本文所述的方法。

## <a name="manage-local-data"></a>管理本機資料

### <a name="LocalInfo"></a>取得本機資料庫及伸展資料庫啟用資料表的相關資訊
開啟類別目錄檢視**sys.databases**及**sys.tables**若要查看伸展的相關資訊\-啟用 SQL Server 資料庫及表格。 取得詳細資訊，請參閱[sys.databases (SQL)](https://msdn.microsoft.com/library/ms178534.aspx)和[sys.tables (SQL)](https://msdn.microsoft.com/library/ms187406.aspx)。

若要查看多少空間伸展\-啟用的表使用 SQL Server，執行下列陳述式中。

 ```tsql
USE <Stretch-enabled database name>;
GO
EXEC sp_spaceused '<Stretch-enabled table name>', 'true', 'LOCAL_ONLY';
GO
 ```
## <a name="manage-data-migration"></a>管理資料移轉

### <a name="check-the-filter-function-applied-to-a-table"></a>核取 [套用至表格的篩選函數
開啟類別目錄檢視**sys.remote\_資料\_封存\_表格**，並檢查的值**篩選\_述詞**識別函數伸展資料庫是用來選取要移轉的資料列的欄。 如果該值為 null，整個表格明明也會移轉。 詳細資訊，請參閱[sys.remote_data_archive_tables (SQL)](https://msdn.microsoft.com/library/dn935003.aspx) ，然後[選取 [使用 filter 函數移轉的列](sql-server-stretch-database-predicate-function.md)。

### <a name="Migration"></a>檢查資料移轉的狀態
選取 [**工作 |伸展 |監視器**監控伸展資料庫監視器中的資料轉換的 SQL Server Management Studio 中為資料庫。 如需詳細資訊，請參閱[監視器和疑難排解資料移轉 （伸展資料庫）](sql-server-stretch-database-monitor.md)。

或者，您也可以開啟動態管理檢視**sys.dm\_db\_rda\_移轉\_狀態**若要查看移轉批次及資料列數量。

### <a name="Firewall"></a>疑難排解資料移轉
疑難排解建議，請參閱[監視器和疑難排解資料移轉 （伸展資料庫）](sql-server-stretch-database-monitor.md)。

## <a name="manage-remote-data"></a>管理遠端資料

### <a name="RemoteInfo"></a>取得遠端資料庫及伸展資料庫所使用的資料表的相關資訊
開啟 [類別目錄檢視**sys.remote\_資料\_封存\_資料庫**和**sys.remote\_資料\_封存\_表格**若要查看的遠端資料庫及表格中儲存已移轉的資料的相關資訊。 取得詳細資訊，請參閱[sys.remote_data_archive_databases (SQL)](https://msdn.microsoft.com/library/dn934995.aspx)和[sys.remote_data_archive_tables (SQL)](https://msdn.microsoft.com/library/dn935003.aspx)。

若要查看多少空間 Azure，執行下列陳述式中使用伸展啟用表格。

 ```tsql
USE <Stretch-enabled database name>;
GO
EXEC sp_spaceused '<Stretch-enabled table name>', 'true', 'REMOTE_ONLY';
GO
 ```

### <a name="delete-migrated-data"></a>刪除移轉的資料  
如果您想要刪除已移轉至 Azure 的資料，請遵循[sys.sp_rda_reconcile_batch](https://msdn.microsoft.com/library/mt707768.aspx)所述的步驟。

## <a name="manage-table-schema"></a>管理表格的結構描述

### <a name="dont-change-the-schema-of-the-remote-table"></a>沒有變更遠端表格的結構描述
不會變更與設定伸展資料庫的 SQL Server 表格相關聯的遠端 Azure 表格的結構描述。 特別是，請勿修改名稱或資料行的資料類型。 遠端相對於 SQL Server 表格的結構描述表格的結構描述的各種假設滿足伸展資料庫功能。 如果您變更遠端結構描述，請伸展資料庫會停止變更表格的工作。

### <a name="reconcile-table-columns"></a>重新同步化表格欄  
如果您不小心從遠端資料表刪除資料行，執行**sp_rda_reconcile_columns**將欄新增至遠端資料表伸展存在於\-啟用 SQL Server 表格而不是在遠端資料表。 取得詳細資訊，請參閱[sys.sp_rda_reconcile_columns](https://msdn.microsoft.com/library/mt707765.aspx)。  

  > [!IMPORTANT] 當**sp_rda_reconcile_columns**重新建立您不小心從遠端資料表刪除的欄時，它不會還原先前已刪除的欄中的資料。

**sp_rda_reconcile_columns**並不會刪除欄從遠端資料表存在於遠端資料表中，而不是在伸展的\-啟用 SQL Server 資料表。 如果無法再存在於伸展遠端 Azure 資料表中的資料行\-啟用 SQL Server 資料表，這些額外的資料欄不會防止伸展資料庫無法正常運作。 您也可以手動移除額外的資料行。  

## <a name="manage-performance-and-costs"></a>管理效能與成本  

### <a name="troubleshoot-query-performance"></a>疑難排解查詢效能問題
查詢包含伸展\-啟用的資料表都必須執行速度比起之前資料表已啟用伸展的緩慢。 如果查詢效能大幅降低時，請檢閱下列可能發生的問題。

-   位於不同的地理區域，於您的 SQL Server Azure 伺服器？ 設定要在相同的地理區域，為您的 SQL Server，以減少網路延遲 Azure 伺服器。

-   您的網路條件可能有降低。 如最近的問題或中斷的相關資訊，請連絡您的網路系統管理員。

### <a name="increase-azure-performance-level-for-resource-intensive-operations-such-as-indexing"></a>增加 Azure 效能階層資源\-大量例如編製索引
當您建立、 重建，或重新組織設定為伸展資料庫的大型資料表索引，您將會在此期間內的移轉 Azure 中資料經常查詢時請考慮增加作業期間的相對應的遠端 Azure 資料庫的效能層級。 如需詳細瞭解效能層級和價格的詳細資訊，請參閱[SQL Server 伸展資料庫價格](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/)。

### <a name="you-cant-pause-the-sql-server-stretch-database-service-on-azure"></a>您無法將游標停留在 Azure SQL Server 伸展資料庫服務  
 請確定您選取適當的效能和價格層級。 如果您增加效能的層級暫時資源\-大量，將它還原為上一層級，在操作完成後。 如需詳細瞭解效能層級和價格的詳細資訊，請參閱[SQL Server 伸展資料庫價格](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/)。  

## <a name="change-the-scope-of-queries"></a>變更查詢的範圍  
 查詢伸展\-啟用的表格依預設，傳回本機與遠端資料。 您可以變更查詢的範圍，所有的查詢的所有使用者，或僅適用於系統管理員在單一查詢。  

### <a name="change-the-scope-of-queries-for-all-queries-by-all-users"></a>變更查詢的所有使用者的所有查詢的範圍  
 若要變更的所有使用者的所有查詢範圍，請執行預存程序**sys.sp_rda_set_query_mode**。 您可以減少查詢本機的資料、 停用所有的查詢，或還原預設值的範圍。 取得詳細資訊，請參閱[sys.sp_rda_set_query_mode](https://msdn.microsoft.com/library/mt703715.aspx)。  

### <a name="queryHints"></a>變更由系統管理員在單一查詢的查詢的範圍  
 若要變更單一 db_owner 角色的成員查詢的範圍，請新增**與\(REMOTE_DATA_ARCHIVE_OVERRIDE =*值* \)* * SELECT 陳述式的查詢提示。 REMOTE_DATA_ARCHIVE_OVERRIDE 查詢提示可以有下列的值。  
 -   **LOCAL_ONLY**。 查詢本機的資料。  

 -   **REMOTE_ONLY**。 查詢遠端的資料。  

 -   **STAGE_ONLY**。 查詢只表格伸展資料庫階段位置列移轉，移轉後指定期間內保留移轉的資料列中的資料。 此查詢提示是唯一的方式查詢暫存的資料表。  

例如，下列查詢會傳回本機的結果。  

 ```tsql  
 USE <Stretch-enabled database name>;
 GO
 SELECT * FROM <Stretch_enabled table name> WITH (REMOTE_DATA_ARCHIVE_OVERRIDE = LOCAL_ONLY) WHERE ... ;
 GO
```  

## <a name="adminHints"></a>讓系統管理員的更新和刪除  
 依預設無法更新或刪除列的移轉或已移轉，伸展中的資料列\-啟用表格。 如果您有來修正問題，db_owner 角色的成員可以藉由新增執行更新或刪除作業**與\(REMOTE_DATA_ARCHIVE_OVERRIDE =*值* \)* * 陳述式的查詢提示。 REMOTE_DATA_ARCHIVE_OVERRIDE 查詢提示可以有下列的值。  
 -   **LOCAL_ONLY**。 更新或刪除本機的資料。  

 -   **REMOTE_ONLY**。 更新或刪除遠端的資料。  

 -   **STAGE_ONLY**。 更新或刪除的表格伸展資料庫階段位置列移轉，移轉後指定期間內保留移轉的資料列中的資料。  

## <a name="see-also"></a>另請參閱

[監視器伸展資料庫](sql-server-stretch-database-monitor.md)

[備份伸展啟用資料庫](sql-server-stretch-database-backup.md)

[還原已啟用伸展的資料庫](sql-server-stretch-database-restore.md)
