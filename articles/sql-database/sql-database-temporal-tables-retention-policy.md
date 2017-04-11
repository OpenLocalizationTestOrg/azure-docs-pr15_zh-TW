<properties
   pageTitle="歷史使用管理資料暫時資料表中的保留原則 |Microsoft Azure"
   description="瞭解如何使用暫時保留原則，讓您控制歷史資料。"
   services="sql-database"
   documentationCenter=""
   authors="bonova"
   manager="drasumic"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sql-database"
   ms.date="10/12/2016"
   ms.author="bonova"/>

#<a name="manage-historical-data-in-temporal-tables-with-retention-policy"></a>歷史使用管理資料暫時資料表中的保留原則

暫時資料表可能會增加資料庫大小多個一般的資料表，尤其是如果您的較長的時間週期保留歷程記錄的資料。 因此，歷史資料保留原則是重要的規劃及管理每個暫時資料表的生命週期。 Azure SQL 資料庫中的暫時表格了方便使用保留機制可協助您完成這項工作。

可以暫時歷程記錄保留在個別的表格層級設定，讓使用者建立彈性過時原則。 套用暫時保留很簡單︰ 需要只有一個參數，以在資料表建立或結構描述變更設定。

定義保留原則之後，就會開始 Azure SQL 資料庫，定期檢查是否有可供自動資料清理歷程記錄的資料列。 相符的資料列識別碼和其歷程記錄資料表中的移除出現透明，在背景工作排程及系統執行。 歷程記錄的資料表列的年齡條件會檢查根據代表 SYSTEM_TIME 期的結束日期的資料行。 保留期間，例如，設定為六個月，如果表格列有資格申請清理符合下列條件︰

````
ValidTo < DATEADD (MONTH, -6, SYSUTCDATETIME())
````

在上述範例中，我們假設**ValidTo**欄對應到 SYSTEM_TIME 期間的結尾。

##<a name="how-to-configure-retention-policy"></a>如何設定保留原則？

設定為暫時表格的保留原則之前，先檢查是否已啟用*資料庫層級*暫時歷史保留。

````
SELECT is_temporal_history_retention_enabled, name
FROM sys.databases
````

根據預設，資料庫標幟**is_temporal_history_retention_enabled**設定為 [開啟]，但是使用者可以變更含有 ALTER DATABASE 陳述式。 [還原時間點](sql-database-point-in-time-restore-portal.md)作業後，它會也會自動設定為關閉。 若要啟用暫時歷程記錄保留清理您的資料庫，請執行下列陳述式︰

````
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
````

> [AZURE.IMPORTANT] 您可以設定暫時表格的保留，即使**is_temporal_history_retention_enabled**為關閉，但不是會在此情況下觸發自動清理舊的資料列。


保留原則是由指定 HISTORY_RETENTION_PERIOD 參數值設定建立資料表時︰

````
CREATE TABLE dbo.WebsiteUserInfo
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH
 (
     SYSTEM_VERSIONING = ON
     (
        HISTORY_TABLE = dbo.WebsiteUserInfoHistory,
        HISTORY_RETENTION_PERIOD = 6 MONTHS
     )
 );
````

Azure SQL 資料庫可讓您使用不同的時間單位來指定保留期限︰ 天、 週、 月數與年數。 如果省略 HISTORY_RETENTION_PERIOD，則會假設無限期保留。 您也可以明確地使用無限關鍵字。

在某些情況下，可能會想要設定保留表格建立之後，或若要變更先前的設定值。 在這種情況下使用 ALTER TABLE 陳述式︰

````
ALTER TABLE dbo.WebsiteUserInfo
SET (SYSTEM_VERSIONING = ON (HISTORY_RETENTION_PERIOD = 9 MONTHS));
````

> [AZURE.IMPORTANT]  設定 SYSTEM_VERSIONING 至 [關閉*不會保留*保留週期值。 不 HISTORY_RETENTION_PERIOD 明確結果中指定的無限期保留期限，設定 SYSTEM_VERSIONING 至 [開啟]。

若要查看目前狀態的保留原則，使用聯結的個別表格的保留期間使用資料庫層級暫時保留預先啟動啟用旗標的下列查詢︰

````
SELECT DB.is_temporal_history_retention_enabled,
SCHEMA_NAME(T1.schema_id) AS TemporalTableSchema,
T1.name as TemporalTableName,  SCHEMA_NAME(T2.schema_id) AS HistoryTableSchema,
T2.name as HistoryTableName,T1.history_retention_period,
T1.history_retention_period_unit_desc
FROM sys.tables T1  
OUTER APPLY (select is_temporal_history_retention_enabled from sys.databases
where name = DB_NAME()) AS DB
LEFT JOIN sys.tables T2   
ON T1.history_table_id = T2.object_id WHERE T1.temporal_type = 2
````


##<a name="how-sql-database-deletes-aged-rows"></a>刪除 SQL 資料庫的方式過時列？

清除程序取決於索引的版面配置的歷程記錄的資料表。 請務必請注意，*群組的索引 （B 樹狀目錄或 columnstore） 的唯一記錄資料表可以有有限的保留原則設定*。 建立執行舊的資料清理句點以保留所有暫時表格的背景工作。
清理邏輯的 rowstore （B 樹狀結構） 叢集索引刪除舊的資料列較小的區塊 （最多 10 K) 壓力資料庫的記錄與 I/O 子系統最小化。 雖然清理邏輯利用必要的索引，刪除比保留期間無法確實保證舊的資料列的順序。 因此，*不會在您的應用程式中的清理順序任何之間的相依性*。

群組直 columnstore 清理工作一次移除整個[資料列群組](https://msdn.microsoft.com/library/gg492088.aspx)（通常是包含的每一個資料列都有 1 百萬個），這是非常有效率，尤其是產生高的速度的歷程記錄的資料。

![群組直的 columnstore 保留](./media/sql-database-temporal-tables-retention-policy/cciretention.png)


絕佳資料壓縮並有效率保留清理使群組直條圖 columnstore 索引案例的最佳選擇當您的工作量快速產生的歷程記錄的資料。 模式就是一般的追蹤修訂及稽核、 趨勢分析或 IoT 資料 ingestion 大量的[交易處理工作負載的使用暫時資料表](https://msdn.microsoft.com/library/mt631669.aspx)。

##<a name="index-considerations"></a>索引考量

具有 rowstore 叢集索引的資料表清理工作需要起始對應 SYSTEM_TIME 期結束的欄索引。 如果沒有這類索引，您將無法設定以保留期限︰

*訊息 13765，層級 16 狀態 1 <br> </br>設定以保留期間系統建立版本的暫時表格 'temporalstagetestdb.dbo.WebsiteUserInfo' 失敗，因為記錄資料表 'temporalstagetestdb.dbo.WebsiteUserInfoHistory 」 不會包含必要的叢集的索引。請考慮建立直的 columnstore 或索引符合 SYSTEM_TIME 結尾的資料行啟動期間，在 [歷程記錄] 表格。*

請務必注意已經建立 Azure SQL 資料庫的預設記錄資料表具有群組直條圖是相容的保留原則的索引。 如果您嘗試移除有限保留期間的資料表的索引，操作會失敗，發生下列錯誤︰

*訊息 13766，層級 16 狀態 1 <br> </br>無法卸除叢集的索引 'WebsiteUserInfoHistory.IX_WebsiteUserInfoHistory'，因為它用於自動清理舊資料。請考慮 HISTORY_RETENTION_PERIOD 設定為無限對應系統建立版本的暫時資料表上，如果您要放此索引。*

清理直的 columnstore 索引適用於最佳化以遞增順序 （依照 period] 資料行的結尾順序），都是大小寫時記錄資料表只會填入 SYSTEM_VERSIONIOING 機制插入歷程記錄的資料列。 如果記錄資料表中的資料列不會依照結尾 period] 資料行 （這可能是因為您移轉現有記錄的資料），您應該重新建立直的 columnstore 索引 rowstore 索引的正確的排序，達到最佳效能的上方。

避免重建歷程記錄資料表以保留句點直的 columnstore 索引因為它可能會變更順序自然系統版本設定作業所設定的資料列群組。 如果您要重建歷程記錄資料表上的 [群組直的 columnstore 索引，請執行這項作業以重新建立相容索引，保留順序中所需的一般資料清理 rowgroups 上方。 若您建立具有群組直條圖的資料行索引不保證的資料順序的現有記錄資料表暫時表格，則應該採取相同的方法︰

````
/*Create B-tree ordered by the end of period column*/
CREATE CLUSTERED INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory (ValidTo)
WITH (DROP_EXISTING = ON);
GO
/*Re-create clustered columnstore index*/
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
````

當有限保留期間設定叢集的 columnstore 索引歷程記錄表格時，您無法建立其他非叢集索引該資料表︰

````
CREATE NONCLUSTERED INDEX IX_WebHistNCI ON WebsiteUserInfoHistory ([UserName])
````

嘗試執行上述陳述式會產生下列錯誤而失敗︰

*訊息 13772，層級 16 狀態 1 <br> </br>無法非叢集索引的資料表上建立暫時歷程記錄 」 WebsiteUserInfoHistory' 因為它沒有有限保留時間] 和 [叢集的 columnstore 索引定義。*

##<a name="querying-tables-with-retention-policy"></a>查詢與保留原則的資料表

暫時表格的所有查詢自動都篩選出歷史比對有限的保留原則，以避免發生無法預期和不一致的公式結果，因為可以清理工作，*點時間和任何順序中*刪除舊的資料列的資料列。

下圖顯示簡單查詢的查詢計劃︰

````
SELECT * FROM dbo.WebsiteUserInfo FROM SYSTEM_TIME ALL;
````

查詢計劃 」 群組直條圖索引掃描 」 運算子 （反白顯示） 的歷程記錄資料表中包含其他將篩選套用到 [period] 資料行 (ValidTo) 結尾。 假設該 1 個月保留期間設定 WebsiteUserInfo 表格。

![保留查詢篩選器](./media/sql-database-temporal-tables-retention-policy/queryexecplanwithretention.png)

不過，如果您直接查詢記錄資料表時，您可能會看到是早於指定保留期間，但不含任何保證可重複的查詢結果的資料列。 下圖顯示不含其他套用的篩選記錄資料表查詢的查詢執行計劃︰

![查詢不保留篩選的歷程記錄](./media/sql-database-temporal-tables-retention-policy/queryexecplanhistorytable.png)

不要依賴商務邏輯讀取超出保留期間記錄資料表，如您可能會出現不一致或非預期的結果。 建議使用 FOR SYSTEM_TIME 子句中的暫時查詢，來分析暫時表格中的資料。

##<a name="point-in-time-restore-considerations"></a>指向 [時間還原的考量事項

當您建立新的資料庫還原[至特定的時間點現有的資料庫](sql-database-point-in-time-restore-portal.md)時，它會有停用資料庫層級暫時保留。 （**is_temporal_history_retention_enabled**旗標設定為 [關閉]。） 此功能可讓您無須擔心查詢之前，會移除舊的資料列中檢查所有歷程記錄的資料列時還原。 您可以使用它來*檢查歷程記錄的資料，除了設定的保留期限*。

暫時資料表為例，指定期間的 1 個月保留。 如果您的資料庫建立進階版服務層級中，您都可以來建立資料庫複本的資料庫狀態設定 35 回過去的天數。 有效可讓您以分析直接查詢記錄資料表最 65 天以內的歷程記錄的資料列。

如果您想要啟動暫時保留清理，請執行下列 SQL 陳述式之後還原時間︰

````
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
````

##<a name="next-steps"></a>後續步驟

若要瞭解如何使用您的應用程式中的暫時資料表查看[Azure SQL 資料庫中的暫時表格快速入門](sql-database-temporal-tables.md)。

請造訪聆聽[實際的客戶暫時實作成功的故事](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions)及觀看[live 暫時示範](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016)頻道 9。

如需暫時表格的詳細資訊，檢閱[MSDN 文件](https://msdn.microsoft.com/library/dn935015.aspx)。
