<properties
   pageTitle="將現有的 Azure SQL Data Warehouse 移轉到進階版儲存 |Microsoft Azure"
   description="移轉現有的 SQL Data Warehouse 進階版存放裝置的指示"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="happynicolle"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/24/2016"
   ms.author="nicw;barbkess;sonyama"/>

# <a name="migration-to-premium-storage-details"></a>移轉到進階版儲存詳細資料
SQL Data Warehouse 最近推出[更大的效能預測的進階版儲存空間][]。  我們已準備好要將目前的標準的儲存空間的現有資料倉儲移轉到進階版儲存空間。  如需詳細瞭解發生自動移轉的方式和時機及方式如果您想要控制停機時間發生時，自動移轉閱讀上。

如果您有多個 Data Warehouse，可用於[自動移轉排程][]下方也會移轉時，決定。

## <a name="determine-storage-type"></a>判斷儲存空間類型
如果您之前的日期下建立 DW，您目前正在使用標準的儲存空間。  受到自動移轉的標準存放在每個 Data Warehouse 具有 Data Warehouse 刀頂端的通知，表示在[Azure 入口網站][]，指出 「 進階版儲存*即將升級會中斷的要求。 瞭解更多]-> [*。 」

| **區域**          | **建立這個日期之前的 DW**   |
| :------------------ | :-------------------------------- |
| 澳大利亞東亞      | 進階版儲存空間無法使用 |
| 澳大利亞 Southeast | 2016 年 8 月 5日日                    |
| 巴西南部        | 2016 年 8 月 5日日                    |
| 加拿大中部      | 2016 可能 25 年                      |
| 加拿大東亞         | 2016 可能 26                      |
| 美國中部          | 2016 可能 26                      |
| 中國東亞          | 進階版儲存空間無法使用 |
| 北美 china （中國）         | 進階版儲存空間無法使用 |
| 中式地址           | 2016 可能 25 年                      |
| 設定適用於美國             | 2016 可能 26                      |
| 東亞 US2            | 2016 月 27日日                      |
| 印度 Central       | 2016 月 27日日                      |
| 印度南部         | 2016 可能 26                      |
| 印度西部          | 進階版儲存空間無法使用 |
| 日本東亞          | 2016 年 8 月 5日日                    |
| 日本西部          | 進階版儲存空間無法使用 |
| 北美美國中部    | 進階版儲存空間無法使用 |
| 北美歐洲        | 2016 年 8 月 5日日                    |
| 美國中部南美洲    | 2016 月 27日日                      |
| 東南亞      | 2016 年 5 月 24 日前                      |
| 西歐         | 2016 可能 25 年                      |
| 西部美國中部     | 2016 年 8 月 26                   |
| 西美制]。             | 2016 可能 26                      |
| 西 US2            | 2016 年 8 月 26                   |

## <a name="automatic-migration-details"></a>自動移轉詳細資料
根據預設，我們會移轉資料庫為您進行 6 pm 和您的地區當地時間 6 am 期間[自動移轉排程][]下方。  您現有的資料倉庫移轉將無法使用。  我們來估計，移轉會需要大約一小時每 TB 的儲存空間，每個 Data Warehouse。  我們也可確保您不會自動移轉的任何部分期間。

> [AZURE.NOTE] 您無法使用您現有的資料倉庫移轉過程。  移轉完成後，您 Data Warehouse 會重新連線。

下列詳細資料是 Microsoft 花費替您完成移轉，而不需要任何您參與的步驟。  在此範例中，假設您在標準的儲存空間的現有 DW 目前名為 「 MyDW 」。

1.  Microsoft 重新命名以 「 MyDW_DO_NOT_USE_ [時間戳記] 」 的 「 MyDW 」
2.  Microsoft 暫停 」 MyDW_DO_NOT_USE_ [時間戳記] 」。  在這段時間，在備份。  如果我們此程序期間發生任何問題，您可能會看到多個的暫停履歷表。
3.  Microsoft 會建立新的 DW 命名 「 MyDW 」 進階版的儲存量從步驟 2 中的備份。  還原完成後，則 「 MyDW 」 不會出現為止。
4.  還原完成後，「 MyDW 」 會傳回相同的 DWUs 與移轉前的暫停] 或 [作用中狀態。
5.  Microsoft 移轉完成後，請刪除 「 MyDW_DO_NOT_USE_ [時間戳記] 」
    
> [AZURE.NOTE] 這些設定不會延續移轉的一部分︰
> 
>   -  稽核資料庫層級需要重新啟用
>   -  在**資料庫**層級的防火牆規則必須已被。  在**伺服器**層級的防火牆規則不會受到影響。

### <a name="automatic-migration-schedule"></a>自動移轉排程
自動移轉發生從下午 6-6 am （每個地區為當地時間） 期間下列中斷排程。

| **區域**          | **估計的開始日期**     | **估計結束日期**       |
| :------------------ | :--------------------------- | :--------------------------- |
| 澳大利亞東部      | 您尚未決定           | 您尚未決定           |
| 澳大利亞 Southeast | 2016 年 8 月 10日日              | 2016 年 8 月 24              |
| 巴西南部        | 2016 年 8 月 10日日              | 2016 年 8 月 24              |
| 加拿大中部      | 2016 年 6 月 23日日                | 2016 年 7 月 1日日                 |
| 加拿大東部         | 2016 年 6 月 23日日                | 2016 年 7 月 1日日                 |
| 美國中部          | 2016 年 6 月 23日日                | 2016 年 7 月 4日日                 |
| 中國東亞          | 您尚未決定           | 您尚未決定           |
| 北美 china （中國）         | 您尚未決定           | 您尚未決定           |
| 中式地址           | 2016 年 6 月 23日日                | 2016 年 7 月 1日日                 |
| 設定適用於美國             | 2016 年 6 月 23日日                | 2016 年 7 月 11日日                |
| 東亞 US2            | 2016 年 6 月 23日日                | 2016 年 7 月 8日日                 |
| 印度 Central       | 2016 年 6 月 23日日                | 2016 年 7 月 1日日                 |
| 印度南部         | 2016 年 6 月 23日日                | 2016 年 7 月 1日日                 |
| 印度西部          | 您尚未決定           | 您尚未決定           |
| 日本東亞          | 2016 年 8 月 10日日              | 2016 年 8 月 24              |
| 日本西部          | 您尚未決定           | 您尚未決定           |
| 北美美國中部    | 您尚未決定           | 您尚未決定           |
| 北美歐洲        | 2016 年 8 月 10日日              | 2016 年 8 月 31日日              |
| 美國中部南美洲    | 2016 年 6 月 23日日                | 2016 年 7 月 2日日                 |
| 東南亞      | 2016 年 6 月 23日日                | 2016 年 7 月 1日日                 |
| 西歐         | 2016 年 6 月 23日日                | 2016 年 7 月 8日日                 |
| 西部美國中部     | 2016 年 8 月 14日日              | 2016 年 8 月 31日日              |
| 西美制]。             | 2016 年 6 月 23日日                | 2016 年 7 月 7日日                 |
| 西 US2            | 2016 年 8 月 14日日              | 2016 年 8 月 31日日              |

## <a name="self-migration-to-premium-storage"></a>自我移轉到進階版儲存體
如果您想要控制您停機時間會發生時，您可以使用下列步驟，將現有的資料倉庫標準存放在移轉到進階版儲存空間。  如果您選擇要自動移轉時，若要避免發生衝突的自動移轉任何風險的區域中的自動移轉開始之前，您必須完成自我移轉 （請參閱[自動移轉排程][]）。

### <a name="self-migration-instructions"></a>自我移轉指示
如果您想要控制您停機時間，您可以自動將您的資料倉庫移轉使用備份/還原。  還原的部分的移轉預期每 TB 的儲存空間，每個 DW 大約一小時。  如果您想要保持相同的名稱，移轉完成後，請依照下列[步驟來重新命名移轉中][]的步驟。 

1.  [暫停][]您 DW 會自動備份
2.  [還原][]從最新的快照集
3.  刪除您在標準的儲存空間的現有 DW。 **如果您無法執行此步驟中，您需要付費兩個 DWs。**

> [AZURE.NOTE] 這些設定不會延續移轉的一部分︰
> 
>   -  稽核資料庫層級需要重新啟用
>   -  在**資料庫**層級的防火牆規則必須已被。  在**伺服器**層級的防火牆規則不會受到影響。

#### <a name="optional-steps-to-rename-during-migration"></a>可省略︰ 若要在移轉過程中重新命名的步驟 
在相同的邏輯伺服器上的兩個資料庫不能有相同的名稱。 SQL Data Warehouse 現在支援的功能，若要重新命名 DW。

在此範例中，假設您在標準的儲存空間的現有 DW 目前名為 「 MyDW 」。

1.  重新命名 「 MyDW 」 使用的 ALTER DATABASE 命令，如下所示內容等 「 MyDW_BeforeMigration 」。  這個命令刪除所有現有的交易，而且必須成功主要資料庫。
```
ALTER DATABASE CurrentDatabasename MODIFY NAME = NewDatabaseName;
```
2.  [暫停][]「 MyDW_BeforeMigration 」 會自動備份
3.  [還原][]您最近快照的資料庫與您用來有的名稱 (例如: 「 MyDW 」)
4.  刪除 「 MyDW_BeforeMigration 」。  **如果您無法執行此步驟中，您需要付費兩個 DWs。**

> [AZURE.NOTE] 這些設定不會延續移轉的一部分︰
> 
>   -  稽核資料庫層級需要重新啟用
>   -  在**資料庫**層級的防火牆規則必須已被。  在**伺服器**層級的防火牆規則不會受到影響。

## <a name="next-steps"></a>後續步驟
使用進階版儲存變更，我們也增加您 Data Warehouse 的基礎架構資料庫 blob 檔案的數目。  最大化這項變更的效能優點，我們建議您重建群組直條圖 Columnstore 索引使用下列指令碼。  以下的指令碼的運作方式強制您現有的資料的一些其他 blob。  如果您不執行任何動作，資料會自然時轉散佈一段時間為 Data Warehouse 表格中載入更多資料。

**必要條件︰**

1.  Data Warehouse 應該執行 1000 DWUs 或更高 （請參閱[小數位數計算 power][]）
2.  使用者執行指令碼應[mediumrc 角色][]或更高
    1.  若要新增此角色的使用者，請執行下列動作︰ 
        1.  ````EXEC sp_addrolemember 'xlargerc', 'MyUser'````

````sql
-------------------------------------------------------------------------------
-- Step 1: Create Table to control Index Rebuild
-- Run as user in mediumrc or higher
--------------------------------------------------------------------------------
create table sql_statements
WITH (distribution = round_robin)
as select 
    'alter index all on ' + s.name + '.' + t.NAME + ' rebuild;' as statement,
    row_number() over (order by s.name, t.name) as sequence
from 
    sys.schemas s
    inner join sys.tables t
        on s.schema_id = t.schema_id
where
    is_external = 0
;
go
 
--------------------------------------------------------------------------------
-- Step 2: Execute Index Rebuilds.  If script fails, the below can be rerun to restart where last left off
-- Run as user in mediumrc or higher
--------------------------------------------------------------------------------

declare @nbr_statements int = (select count(*) from sql_statements)
declare @i int = 1
while(@i <= @nbr_statements)
begin
      declare @statement nvarchar(1000)= (select statement from sql_statements where sequence = @i)
      print cast(getdate() as nvarchar(1000)) + ' Executing... ' + @statement
      exec (@statement)
      delete from sql_statements where sequence = @i
      set @i += 1
end;
go
-------------------------------------------------------------------------------
-- Step 3: Cleanup Table Created in Step 1
--------------------------------------------------------------------------------
drop table sql_statements;
go
````

如果您遇到任何問題，用於 Data Warehouse、[建立支援票證][]及參考 」 移轉到進階版存放區 」 為可能的原因。

<!--Image references-->

<!--Article references-->
[自動移轉排程]: #automatic-migration-schedule
[self-migration to Premium Storage]: #self-migration-to-premium-storage
[建立支援票證]: sql-data-warehouse-get-started-create-support-ticket.md
[Azure paired region]: best-practices-availability-paired-regions.md
[main documentation site]: services/sql-data-warehouse.md
[暫停]: sql-data-warehouse-manage-compute-portal.md/#pause-compute
[還原]: sql-data-warehouse-restore-database-portal.md
[若要重新命名移轉中的步驟]: #optional-steps-to-rename-during-migration
[縮放比例運算能力]: sql-data-warehouse-manage-compute-portal/#scale-compute-power
[mediumrc 角色]: sql-data-warehouse-develop-concurrency/#workload-management

<!--MSDN references-->


<!--Other Web references-->
[進階版更大的效能預測的儲存空間]: https://azure.microsoft.com/en-us/blog/azure-sql-data-warehouse-introduces-premium-storage-for-greater-performance/
[Azure 入口網站]: https://portal.azure.com
