<properties
    pageTitle="記憶體內 OLTP 皆可改善 SQL txn 效能 |Microsoft Azure"
    description="採用記憶體內 OLTP 改善現有的 SQL 資料庫交易的效能。"
    services="sql-database"
    documentationCenter=""
    authors="jodebrui"
    manager="jhubbard"
    editor="MightyPen"/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="jodebrui"/>


# <a name="use-in-memory-oltp-preview-to-improve-your-application-performance-in-sql-database"></a>若要改善您的應用程式的效能 SQL 資料庫中使用記憶體內 OLTP （預覽版本）

[記憶體內 OLTP](sql-database-in-memory.md)可用來改善 OLTP[進階版](sql-database-service-tiers.md)Azure SQL 資料庫中的工作負載的效能不效能層級增加。

遵循下列步驟在現有的資料庫中採用記憶體內 OLTP。

## <a name="step-1-ensure-your-premium-database-supports-in-memory-oltp"></a>步驟 1︰ 請確定您的進階資料庫支援記憶體內 OLTP

在 2015 年 11 月或更新版本所建立的進階資料庫支援在記憶體功能。 您可以確定您的進階資料庫是否支援藉由執行下列 SQL 陳述式中記憶體功能。 如果傳回的結果是 1 支援記憶體內 (不是 0):

```
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*XTP*代表*非常交易處理*

如果您現有的資料庫必須移到新的 V12 進階資料庫，您可以使用下列技巧匯出，並將資料匯入。

#### <a name="export-steps"></a>匯出步驟

將匯出生產資料庫至 bacpac 使用以下任一項︰

- [入口網站](https://portal.azure.com/)中的[匯出](sql-database-export.md)的功能。

- **匯出資料層應用程式**中的功能[最新版本的 SSMS.exe](http://msdn.microsoft.com/library/mt238290.aspx) (SQL Server Management Studio 中)。
 1. 在**物件總管]**中，展開 [**資料庫**節點。
 2. 以滑鼠右鍵按一下您的資料庫節點。
 3. 按一下 [**任務** > **匯出資料層應用程式**。
 4. 操作精靈視窗顯示。


#### <a name="import-steps"></a>匯入步驟

Bacpac 匯入新的進階資料庫。

1. 在 Azure[入口網站](https://portal.azure.com/)，
 - 瀏覽至伺服器。
 - 選取 [[匯入資料庫](sql-database-import.md)] 選項。
 - 選取 [價格層進階版]。

2. 若要匯入 bacpac 使用 SSMS:
 - 在**物件總管]**中，以滑鼠右鍵按一下 [**資料庫**] 節點。
 - 按一下 [**匯入資料層應用程式**]。
 - 操作精靈視窗顯示。


## <a name="step-2-identify-objects-to-migrate-to-in-memory-oltp"></a>步驟 2︰ 找出要移轉至記憶體內 OLTP 的物件

SSMS 包含 [**交易效能分析概觀**] 報表，您可以針對作用中的工作負載的資料庫執行。 報表會識別資料表和記憶體內 OLTP 移轉適合預存程序。

在 [SSMS 產生報告︰
- 在**物件總管]**中，以滑鼠右鍵按一下您的資料庫節點。
- 按一下 [**報表**] > **標準報表** > **交易效能分析概觀**。

如需詳細資訊，請參閱[判斷如果資料表或預存程序應該會所提供的記憶體內 OLTP](http://msdn.microsoft.com/library/dn205133.aspx)。


## <a name="step-3-create-a-comparable-test-database"></a>步驟 3︰ 建立相容的測試資料庫

假設報表表示您的資料庫受益會被轉換成記憶體最佳化資料表的資料表。 我們建議您先測試] 來確認指示進行測試。

您需要測試生產資料庫的複本。 測試資料庫必須是實際執行資料庫相同的服務層層級。

若要簡化測試，調整 [測試資料庫，如下所示︰

1. 使用 SSMS 連線到測試資料庫。

2. 若要避免需要在查詢中的 [與 （快照集）] 選項，設定下列 T SQL 陳述式中所示的資料庫選項︰
```
ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
```


## <a name="step-4-migrate-tables"></a>步驟 4︰ 移轉表格

您必須建立並填入記憶體最佳化複本您想要測試的資料表。 您可以建立，使用以下任一項︰

- 在 SSMS 方便記憶體最佳化精靈。
- 手動 T-SQL。


#### <a name="memory-optimization-wizard-in-ssms"></a>在 [SSMS 記憶體最佳化精靈

若要使用這個移轉選項︰

1. 連線至 SSMS 測試資料庫。

2. 在**物件總管]**中，在資料表，以滑鼠右鍵按一下，然後按一下**記憶體最佳化顧問**。
 - 會顯示**表格的記憶體最佳化顧問**精靈。

3. 在精靈中，按一下資料表是否有任何不支援的功能不支援的記憶體最佳化表格**移轉驗證**（或 [**下一步**] 按鈕）。 如需詳細資訊，請參閱︰
 - *記憶體最佳化檢查清單*中[的記憶體最佳化顧問](http://msdn.microsoft.com/library/dn284308.aspx)。
 - [不支援記憶體內 OLTP TRANSACT-SQL 建構](http://msdn.microsoft.com/library/dn246937.aspx)。
 - [移轉至記憶體內 OLTP](http://msdn.microsoft.com/library/dn247639.aspx)。

4. 如果資料表不受支援的功能，顧問可以為您執行的實際的結構描述和資料移轉。


#### <a name="manual-t-sql"></a>手動 T SQL

若要使用這個移轉選項︰

1. 連線到您測試資料庫使用 SSMS （或類似公用程式）。

2. 取得您的資料表和索引的完整 T SQL 指令碼。
 - 在 SSMS，以滑鼠右鍵按一下您表格的節點。
 - 按一下 [**以指令碼表格** > **建立** > **新的 [查詢] 視窗**。

3. 在 [指令碼] 視窗中，新增與 (MEMORY_OPTIMIZED = 開啟) CREATE TABLE 陳述式。

4. 如果有叢集的索引，請變更 NONCLUSTERED。

5. 使用 SP_RENAME 重新命名現有的資料表。

6. 執行您編輯建立表格的指令碼，以建立新的記憶體最佳化複本的表格。

7. 將資料複製到您的記憶體最佳化的表格，使用插入...選取 [* 至︰
    
```
INSERT INTO <new_memory_optimized_table>
        SELECT * FROM <old_disk_based_table>;
```


## <a name="step-5-optional-migrate-stored-procedures"></a>步驟 5 （選用）︰ 移轉預存程序

在記憶體功能也可以修改預存程序以改善效能。


### <a name="considerations-with-natively-compiled-stored-procedures"></a>原本就已編譯的預存程序的考量

原本就已編譯的預存程序必須具備其與 T SQL 子句的下列選項︰

- NATIVE_COMPILATION

- SCHEMABINDING︰ 表示預存程序不能有除非放預存程序，以任何方式會影響預存程序，變更其資料行定義的資料表。


原生模組必須使用一大[原子區塊](http://msdn.microsoft.com/library/dn452281.aspx)的交易管理。 有明確開始交易，或將交易復原沒有角色。 如果您的程式碼偵測到違反商務規則，可以終止原子含有[引發](http://msdn.microsoft.com/library/ee677615.aspx)的陳述式封鎖。


### <a name="typical-create-procedure-for-natively-compiled"></a>一般的建立程序，針對原本編譯

通常 T SQL 建立原本就已編譯的預存程序非常類似下列範本︰

```
CREATE PROCEDURE schemaname.procedurename
    @param1 type1, …
    WITH NATIVE_COMPILATION, SCHEMABINDING
    AS
        BEGIN ATOMIC WITH
            (TRANSACTION ISOLATION LEVEL = SNAPSHOT,
            LANGUAGE = N'your_language__see_sys.languages'
            )
        …
        END;
```

- TRANSACTION_ISOLATION_LEVEL，快照是最常見的值，原本就已編譯的預存程序。 不過，也支援其他值的子集合︰
 - 可重複讀取
 - 序列化


- [語言] 的值必須是 [sys.languages] 檢視中。


### <a name="how-to-migrate-a-stored-procedure"></a>如何移轉預存程序

移轉步驟如下︰


1. 取得一般解譯預存程序的建立程序指令碼。

2. 重寫標頭以符合前一個範本。

3. 請確定是否預存程序 T SQL 程式碼會使用任何原本就已編譯的預存程序不支援的功能。 如有必要，請執行解決方案。
 - 如需詳細資訊請參閱[移轉問題原本編譯預存程序](http://msdn.microsoft.com/library/dn296678.aspx)。

4. 使用 SP_RENAME 重新命名舊的預存程序。 或只要將它放。

5. 執行您編輯的建立程序 T SQL 指令碼。


## <a name="step-6-run-your-workload-in-test"></a>步驟 6︰ 測試中執行您的工作量

執行類似生產資料庫中執行的工作負載的測試資料庫中的工作量。 此應該會顯示在記憶體功能您使用的資料表和預存程序完成的效能提升。

工作負載的主要屬性包括︰

- 同時連線的數目。

- 讀/寫比例。


若要依需求自訂，並執行測試工作量，請考慮使用方便 ostress.exe 工具，可在[以下](sql-database-in-memory.md)所示。


若要最小化網路延遲執行您的測試資料庫所在的相同 Azure 地理區域中。


## <a name="step-7-post-implementation-monitoring"></a>步驟 7︰ 實作監控

請考慮監控效能的影響生產環境中將記憶體內實作︰

- [監視器記憶體內儲存空間](sql-database-in-memory-oltp-monitoring.md)。

- [使用動態管理檢視監控 Azure SQL 資料庫](sql-database-monitoring-with-dmvs.md)


## <a name="related-links"></a>相關的連結

- [記憶體 OLTP （在記憶體最佳化）](http://msdn.microsoft.com/library/dn133186.aspx)

- [原本就已編譯的預存程序的簡介](http://msdn.microsoft.com/library/dn133184.aspx)

- [記憶體最佳化顧問](http://msdn.microsoft.com/library/dn284308.aspx)

