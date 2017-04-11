<properties
    pageTitle="Azure SQL 資料庫及單一資料庫的效能 |Microsoft Azure"
    description="本文可協助您判斷哪一個，選擇您的應用程式層。 也建議以調整您的應用程式，以取得最從 Azure SQL 資料庫的方法。"
    services="sql-database"
    documentationCenter="na"
    authors="CarlRabeler"
    manager="jhubbard"
    editor="" />


<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="09/13/2016"
    ms.author="carlrab" />

# <a name="azure-sql-database-and-performance-for-single-databases"></a>Azure SQL 資料庫及單一資料庫的效能

Azure SQL 資料庫提供三種[服務層](sql-database-service-tiers.md)︰ 基本、 標準和進階版。 每個服務層級嚴格隔離資源 SQL 資料庫可以使用，並確保該服務等級可預期的效能。 本文中，我們會提供可協助您選擇為您的應用程式服務層級的指引。 我們也討論您可以調整您的應用程式，取得從 Azure SQL 資料庫的方法。

>[AZURE.NOTE] 本文著重於 Azure SQL 資料庫中的單一資料庫的效能指引。 效能相關彈性的資料庫集區，請參閱[彈性的資料庫集區的價格和效能考量](sql-database-elastic-pool-guidance.md)。 不過請注意，您可以用來微調建議事項，本文中的許多套用到彈性的資料庫集區中的資料庫，並取得類似的效能優點。

以下是您可以選擇的三個 Azure SQL 資料庫服務層 (效能以資料庫處理量單位或[DTUs](sql-database-what-is-a-dtu.md)來計算︰

- **基本**。 [基本服務層提供每個資料庫的效能預測，透過小時小時。 在 [基本的資料庫，足夠的資源會支援效能沒有多重並行要求的小型資料庫中。
- **標準**。 標準服務層提供更佳的效能預測，並引發的多個同時的要求，例如工作群組 」 和 「 web 應用程式資料庫的列。 當您選擇標準服務層資料庫時，您可以調整大小根據如預期呈現效能，應用程式資料庫 minute 透過分鐘。
- **進階版**。 進階版服務層提供可預期的效能，第二個移到第二，每個進階資料庫。 當您選擇的進階版服務層時，您可以調整大小為該資料庫的最大使用量負載所根據的資料庫應用程式。 計劃中移除的效能差異可能會導致小型查詢會超過預期時間延遲區分作業中的情況。 此模型可大幅簡化需要進行最大使用量資源需求、 效能變異數或查詢延遲相關的強式陳述式的應用程式的開發與產品驗證週期。

每個服務層級，在您設定的效能層級，，好讓您僅適用於您所需要的容量支付的彈性。 您可以[調整容量](sql-database-scale-up.md)，向上或向下，為工作負載的變更。 例如，如果您的資料庫工作負載期間返回學校購物季節高，您可能會增加資料庫的效能層級設定的時間，年 7 月透過年 9 月的。 當您的最大使用量季節結束時，您可以縮減它。 您可以最小化支付最佳化您的企業的季節性您雲端環境。 此模型也適用於軟體產品發行週期。 測試團隊可能會配置容量時，它會測試便會執行，並完成這些測試，然後放開的容量。 在容量要求模型中，您是每年支付容量您需要它，並避免花上可能很少使用的專用資源。

## <a name="why-service-tiers"></a>為什麼服務層嗎？

每個資料庫工作負載可能不同，雖然服務層的目的是提供效能的各個層級的效能預測。 在其他專用的電腦環境中，就可以使用大型資料庫資源需求的客戶。

### <a name="common-service-tier-use-cases"></a>常見的服務層使用案例

#### <a name="basic"></a>基本

- **您才剛開始使用 Azure SQL 資料庫**。 應用程式所開發通常不需要高效能層級。 基本的資料庫的資料庫開發，低價格點的理想環境。
- **您有一位使用者使用的資料庫**。 通常與資料庫建立關聯單一使用者的應用程式沒有高並行和效能的需求。 這些應用程式的基本服務層的對象。

#### <a name="standard"></a>標準

- **資料庫有多個同時的要求**。 一次通常服務多個使用者的應用程式需要較高的效能層級。 例如，取得中等流量或需要更多資源部門應用程式的網站是不錯的標準服務層。

#### <a name="premium"></a>進階版

大部分的進階版服務層的使用案例有一或多個下列特性︰

- **高的最大使用量載入**。 需要大量 CPU、 記憶體或輸入輸出 (I/O)，以完成作業的應用程式需要專用、 高效能層級。 例如，消耗數個 CPU 核心一段時間的資料庫作業是進階版服務層級的候選。
- **許多同時的要求**。 有些資料庫應用程式，例如服務多同時的要求，，當做網站具有高流量。 基本和標準服務層級限制同時的要求，每個資料庫的數目。 需要更多連線的應用程式需要選擇適當的預約功能大小處理所需的要求的數目上限。
- **低延遲**。 部分應用程式需要保證資料庫的回應，最小的時間。 如果特定的預存程序呼叫狀況提出更廣義的客戶作業的一部分，您可能需要有從該通話傳回不超過 20 毫秒為單位 99%的時間。 此類型的應用程式的進階版服務層級，以確保必要的計算能力有的優點。

您需要 SQL 資料庫的服務層級取決於每個資源維度的最大使用量載入需求。 部分應用程式使用單一資源，一般的數量，但有有效的其他資源需求。

## <a name="service-tier-capabilities-and-limits"></a>服務層功能和限制
每個服務層和效能層級是不同的限制和效能特性與相關聯。 下表說明這些特性單一資料庫。

[AZURE.INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

下一節中有深入瞭解如何檢視這些限制相關的使用。

### <a name="maximum-in-memory-oltp-storage"></a>最大的記憶體內 OLTP 儲存空間

您可以使用**sys.dm_db_resource_stats**檢視監控 Azure 記憶體內儲存空間使用。 如需關於監控的詳細資訊，請參閱[監視器記憶體內 OLTP 儲存空間](sql-database-in-memory-oltp-monitoring.md)。

>[AZURE.NOTE] 目前，Azure 記憶體內的線上交易處理 (OLTP) preview 支援僅適用於單一的資料庫。 您無法將其用於彈性的資料庫集區中的資料庫。

### <a name="maximum-concurrent-requests"></a>最大同時要求

若要檢視的同時的要求，請執行此 TRANSACT-SQL 您 SQL 資料庫查詢︰

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R

若要分析內部部署的 SQL Server 資料庫的工作量，修改您要分析這個查詢篩選特定的資料庫。 例如，如果您有名為 MyDatabase 內部部署資料庫時，此 SQL 查詢會傳回一個要求的計數該資料庫中︰

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R
    INNER JOIN sys.databases D ON D.database_id = R.database_id
    AND D.name = 'MyDatabase'

這是只在單一點快照時間。 若要深入瞭解您的工作量和同時的要求需求，必須收集一段時間的許多範例。

### <a name="maximum-concurrent-logins"></a>最大的同時登入

您可以分析您的使用者和應用程式模式，若要瞭解的登入的頻率。 您也可以執行，請確定您不正中這或其他限制本文中，我們討論的測試環境中實際的載入。 目前沒有單一查詢動態管理檢視上可以顯示您同時登入計算或歷程記錄。

如果多個用戶端使用相同的連接字串，服務會驗證每個登入。 10 個使用者同時連線至資料庫使用相同的使用者名稱和密碼，如果有 10 同時登入。 這項限制僅適用於登入及驗證的工期。 如果相同的 10 個使用者連線至資料庫的循序，不會大於 1 的同時登入。

>[AZURE.NOTE] 目前，這項限制不適用於彈性的資料庫資料庫的資料庫。

### <a name="maximum-sessions"></a>最大的工作階段

若要查看的目前使用中的工作階段，執行此 TRANSACT-SQL 您 SQL 資料庫查詢︰

    SELECT COUNT(*) AS [Sessions]
    FROM sys.dm_exec_connections

如果您分析內部部署的 SQL Server 工作量，請修改查詢將焦點集中在特定的資料庫。 此查詢可協助您判斷資料庫可能的工作階段需求，如果您考慮將它移到 Azure SQL 資料庫。

    SELECT COUNT(*)  AS [Sessions]
    FROM sys.dm_exec_connections C
    INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
    INNER JOIN sys.databases D ON (D.database_id = S.database_id)
    WHERE D.name = 'MyDatabase'

同樣地，這些查詢傳回的時間點計數。 如果您是一段時間收集多個範例，則必須使用最佳瞭解您的工作階段。

SQL 資料庫分析，您可以在工作階段取得歷史統計資料。 查詢**sys.resource_stats**，並使用**active_session_count**資料行]。 請參閱下一節，如需有關使用此檢視。

## <a name="monitor-resource-use"></a>您可以監視使用資源
兩種檢視，可協助您監控 SQL 資料庫相對於其服務層級的資源用於︰

- [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
- [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

### <a name="sysdmdbresourcestats"></a>sys.dm_db_resource_stats
您可以使用 [ [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) ] 檢視中每個 SQL 資料庫。 **Sys.dm_db_resource_stats**檢視會顯示最近資源使用資料相對於服務層級。 平均百分比 CPU、 資料 I/O、 寫入記錄，和記憶體會記錄每 15 秒，而會維持 （1 小時）。

由於此檢視會提供更細微看資源使用，請使用**sys.dm_db_resource_stats**任何目前狀態分析的第一個或疑難排解。 例如，此查詢會顯示過去小時內目前資料庫的平均值 」 與 「 最大資源使用︰

    SELECT  
        AVG(avg_cpu_percent) AS 'Average CPU use in percent',
        MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
        AVG(avg_data_io_percent) AS 'Average data I/O in percent',
        MAX(avg_data_io_percent) AS 'Maximum data I/O in percent',
        AVG(avg_log_write_percent) AS 'Average log write use in percent',
        MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
        AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
        MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
    FROM sys.dm_db_resource_stats;  

為其他查詢中，請參閱[sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)中的範例。

### <a name="sysresourcestats"></a>sys.resource_stats

**主要**資料庫的[sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)檢視具有可協助您監控效能的 SQL 資料庫其特定服務層和效能層級的其他資訊。 資料收集每 5 分鐘，而保留大約 35 天。 此檢視最適合您的 SQL 資料庫如何使用資源的長期歷史分析。

下圖顯示一週的 CPU 資源使用進階資料庫 P2 效能階層的每個小時。 此圖形星期一開始、 顯示 5 個工作日，並顯示週末，當多較不會發生下列情況應用程式。

![SQL 資料庫資源的使用](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

從資料，此資料庫目前有最大使用量 CPU 載入時，只要超過 50%的 CPU 使用相對於 P2 效能層級 （中午星期二）。 如果 CPU 應用程式的資源設定檔中的主要因素，您可能決定 P2 是正確的效能層級，以確保工作量永遠符合。 如果您希望成長一段時間的應用程式時，最好還是有額外的資源緩衝應用程式時無法達到效能層級限制。 如果您增加效能層級，您可以協助避免當資料庫沒有足夠的電力有效，尤其是在區分延遲的環境中處理要求時，可能會發生的客戶顯示錯誤。 範例是支援的應用程式，來繪製網頁資料庫呼叫的結果為基礎的資料庫。

請注意其他應用程式類型可能以不同方式轉譯相同的圖形。 例如，如果應用程式嘗試處理薪資資料的每一天，而有相同的圖表，這種 「 批次工作 「 模型可能要執行 P1 效能層級。 P1 效能層級會有 100 DTUs 比較 200 DTUs P2 效能層級。 P1 效能層級提供 P2 效能層級的一半的效能。 因此，50%的 CPU 使用 P2 等於 100 %cpu 使用 P1。 如果應用程式沒有逾時，它可能重要如果工作需要 2 小時] 或 [2.5 小時，若要完成，如果取得今天完成。 此類別中的應用程式可能可以使用 P1 效能層級。 您可以利用是一段期間資源使用時較低，讓任何 「 大最大使用量] 可能會將其中一個 troughs 晚來延續一天的時間。 P1 效能層級，只要在每一天的時間完成工作，可能是很好的應用程式 （並儲存金錢），該類型。

Azure SQL 資料庫公開使用每個作用中的資料庫中每個伺服器的**主版**資料庫的**sys.resource_stats**檢視中的 [資源資訊。 在資料表中的資料會彙總的 5 分鐘的時間間隔。 使用基本、 標準和進階版服務層級，資料會超過 5 分鐘，此資料會越有用處歷史分析，而不是附近即時分析，以便在資料表中，顯示。 查詢在需要時，所要的**sys.resource_stats**檢視若要查看最近的資料庫的記錄與驗證是否為保留您選擇的項目送達效能。

>[AZURE.NOTE] 您必須連線至**主**邏輯 SQL 資料庫伺服器來查詢**sys.resource_stats**在下列範例中的資料庫。

此範例為您示範如何公開此檢視中的資料︰

    SELECT TOP 10 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![Sys.resource_stats 類別目錄檢視](./media/sql-database-performance-guidance/sys_resource_stats.png)

下一個範例會顯示不同的方式，您可以使用**sys.resource_stats**類別目錄檢視，以取得如何 SQL 資料庫使用資源的相關資訊︰

1. 若要查看 [週的資源資料庫 userdb1 使用，您可以執行下列查詢︰

        SELECT *
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND
              start_time > DATEADD(day, -7, GETDATE())
        ORDER BY start_time DESC;

2. 若要評估您的工作量符合效能層級的方式，您必須向下切入資源指標的每一個項目︰ CPU、 讀取、 寫入、 數字的同事及工作階段的數目。 以下是修改過的查詢，使用**sys.resource_stats**報告這些資源計量平均值、 最大值︰

        SELECT
            avg(avg_cpu_percent) AS 'Average CPU use in percent',
            max(avg_cpu_percent) AS 'Maximum CPU use in percent',
            avg(avg_data_io_percent) AS 'Average physical data I/O use in percent',
            max(avg_data_io_percent) AS 'Maximum physical data I/O use in percent',
            avg(avg_log_write_percent) AS 'Average log write use in percent',
            max(avg_log_write_percent) AS 'Maximum log write use in percent',
            avg(max_session_percent) AS 'Average % of sessions',
            max(max_session_percent) AS 'Maximum % of sessions',
            avg(max_worker_percent) AS 'Average % of workers',
            max(max_worker_percent) AS 'Maximum % of workers'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());

3. 關於平均] 和 [最大值的每個資源公制此資訊，您可以評估您的工作量符合您所選擇的效能層級的程度。 通常平均的值從**sys.resource_stats**讓您建議的比較基準，以便使用針對目標大小。 您應該您主要的度量單位搖桿。 例如，您可能會使用標準服務層 S2 效能層級。 平均使用百分比的 CPU 和 I/O 讀取和寫入下方的 40%、 同事的平均數目低於 50，而工作階段的平均數目低於 200。 您的工作量可能配合 S1 效能層級。 您可以輕鬆查看工作者及工作階段限制是否符合您的資料庫。 若要查看資料庫是否適合較低的效能層級有關 CPU、 讀取和寫入，分割的效能下層以目前的效能層級的 DTU DTU 數，然後再將結果乘以 100:

    * *S1 DTU / S2 DTU* 100 = 20 / 50* 100 = 40 * *

    結果是相對效能之間的差異百分比的兩個效能層級。 資源使用不超過此數量，如果您的工作量可能會調整至較低的效能層級。 不過，您需要查看的所有資源的使用值的範圍，並判斷頻率您資料庫的工作量想調整至較低的效能層級，以百分比]。 下列查詢輸出調整的百分比，每個資源維度，在我們在此範例中所計算的 40%的閥值︰

        SELECT
            (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());

    根據您資料庫服務層級的目標 (SLO)，您可以決定下層的效能是否符合您的工作量。 如果您的資料庫工作負載 SLO 是 99.9%前述查詢傳回的值大於 99.9%的所有三個資源維度，您可能的工作量符合下層的效能。

    尋找調整百分比，也可讓您深入了解您是否符合您 SLO 移到一層的效能。 例如，userdb1 顯示週下列 CPU 使用︰

  	| 平均 CPU 百分比 | 最大 CPU 百分比 |
  	|---|---|
  	| 24.5 | 100.00 |

    平均 CPU 是限制的一季的效能層級，也會配合資料庫的效能層級。 不過，最大值顯示資料庫達到效能層級的限制。 您需要移至一層的效能？ 您必須看看如何多次您的工作量到達 100%，然後以您的資料庫工作負載 SLO 進行比較。

        SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent’
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data I/O fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());

    如果這個查詢傳回的值小於 99.9%的三個資源維度] 下方，請考慮移至下一個較高的效能層級，或使用應用程式調整技巧來減少 SQL 資料庫的載入。

4. 這個練習也您規劃的工作量增加會在未來的考量。

## <a name="tune-your-application"></a>調整您的應用程式

在傳統的內部部署的 SQL Server 中的初始容量規劃程序通常分開的執行應用程式中生產程序。 請先購買硬體與產品授權與效能調整完畢之後。 當您使用 Azure SQL 資料庫時，最好先 interweave 執行應用程式，並調整的程序。 含有此模型的支付容量視需要，您可以調整您的應用程式使用最小值，而不是根據猜測未來的成長計劃的應用程式，這通常是 [不正確的硬體上 overprovisioning 現在需要的資源。 某些客戶可能選擇不調整應用程式，並請改為選擇 overprovision 硬體資源。 如果您不想要變更主要的應用程式，在忙碌期間，這種方法可能會建議。 不過，微調] 應用程式，可以最小化資源需求和較低的每月帳單當您使用的服務層 Azure SQL 資料庫。

### <a name="application-characteristics"></a>應用程式特性

雖然 Azure SQL 資料庫服務層設計用來改善效能穩定性和應用程式的預測，一些最佳作法可以協助您調整您的應用程式更有效地利用的資源效能層級。 雖然許多應用程式有明顯的效能提升只要，即可切換到較高的效能或服務層部分應用程式需要其他調整，以受益較高層級的服務。 為了提高效能，請考慮其他應用程式調整的應用程式，有下列特性︰

- **具有 「 談話 」 行為由於效能變慢的應用程式**。 冗長的應用程式進行敏感網路延遲的過多的資料存取作業。 您可能需要修改這些類型的應用程式，以減少到 SQL 資料庫的資料存取運算的數目。 例如，您可能會改善應用程式的效能使用技巧，例如批次處理臨機操作查詢，或將查詢移至 [預存程序。 如需詳細資訊，請參閱[批次查詢](#batch-queries)。
- **使用大量的工作量整個的單一電腦不支援的資料庫**。 超過進階版效能最高的資源資料庫可能會因出工作量縮放比例。 如需詳細資訊，請參閱[跨資料庫 sharding](#cross-database-sharding)和[分割功能](#functional-partitioning)。
- **應用程式的有不理想的查詢**。 應用程式，尤其是資料的存取層級]，有不良微調查詢中可能不受益較高的效能。 這包含缺少 WHERE 子句，有遺失的索引，或過期的統計資料查詢。 這些應用程式可以從標準查詢效能調整技巧。 如需詳細資訊，請參閱[遺失索引](#missing-indexes)和[查詢調整和提示](#query-tuning-and-hinting)。
- **不理想的資料有應用程式存取的設計**。 應用程式的既有資料存取並行方面的問題，例如死結，可能會受益較高的效能。 請考慮使用 Azure 快取服務或其他快取技術的用戶端上的快取資料減少往返 Azure SQL 資料庫。 請參閱[應用程式層快取](#application-tier-caching)。

## <a name="tuning-techniques"></a>調整技巧
在此區段中，我們看看一些您可以使用微調 Azure SQL 資料庫，取得您的應用程式的最佳效能，並執行最低可能效能層級的技巧。 其中一些技術符合傳統的 SQL Server 微調最佳作法，但其他人只適用於 Azure SQL 資料庫。 在某些情況下，您可以檢查使用的資源資料庫，以尋找要進一步調整和擴充傳統的 SQL Server 技術 Azure SQL 資料庫中工作的區域。

### <a name="azure-portal-tools"></a>Azure 入口網站的工具
您可以找到這兩種工具，可協助您 Azure 入口網站中的分析及修正 SQL 資料庫效能問題︰

- [查詢效能充分](sql-database-query-performance.md)
- [SQL 資料庫顧問](sql-database-advisor.md)

Azure 入口網站有兩個這些工具，以及如何使用它們的詳細資訊。 若要有效率地診斷並修正問題，建議您先嘗試 Azure 入口網站中的工具。 我們建議您使用手動調整我們接下來，討論遺失的索引和微調查詢，在特殊的情況下的方法。

### <a name="missing-indexes"></a>遺漏索引
常見的問題 OLTP 資料庫效能與實體資料庫設計。 通常，資料庫結構描述設計，而且商品銷售並運送沒有小數位數 （無論是在載入或資料量） 的測試。 很抱歉，查詢計劃的效能可能會在較小的規模可接受，但生產層級的資料區下大幅降低。 此問題的常見來源是適當的索引，以篩選或查詢中的其他限制來滿足缺乏。 通常，為表格的遺失索引資訊清單掃描時無法滿足索引搜尋。

在此範例中，選取的查詢計劃使用掃描時就足以搜尋︰

    DROP TABLE dbo.missingindex;
    CREATE TABLE dbo.missingindex (col1 INT IDENTITY PRIMARY KEY, col2 INT);
    DECLARE @a int = 0;
    SET NOCOUNT ON;
    BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO dbo.missingindex(col2) VALUES (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION;
    GO
    SELECT m1.col1
    FROM dbo.missingindex m1 INNER JOIN dbo.missingindex m2 ON(m1.col1=m2.col1)
    WHERE m1.col2 = 4;

![遺漏索引查詢計劃](./media/sql-database-performance-guidance/query_plan_missing_indexes.png)

Azure SQL 資料庫可協助您尋找並修正索引條件常見遺失。 Azure SQL 資料庫內建的 Dmv 看看查詢編譯的索引會大幅降低估計的成本] 以執行查詢。 在查詢執行時，SQL 資料庫追蹤每個查詢計劃執行時，和追蹤估計的間距執行查詢計劃和假設項目之間的索引位置存在的頻率。 您可以使用這些 Dmv 快速猜測實體資料庫設計的變更可能會改善整體的工作量成本資料庫和其實數的工作量。

您可以使用這個查詢評估潛在遺漏索引︰

    SELECT CONVERT (varchar, getdate(), 126) AS runtime,
        mig.index_group_handle, mid.index_handle,
        CONVERT (decimal (28,1), migs.avg_total_user_cost * migs.avg_user_impact *
                (migs.user_seeks + migs.user_scans)) AS improvement_measure,
        'CREATE INDEX missing_index_' + CONVERT (varchar, mig.index_group_handle) + '_' +
                  CONVERT (varchar, mid.index_handle) + ' ON ' + mid.statement + '
                  (' + ISNULL (mid.equality_columns,'')
                  + CASE WHEN mid.equality_columns IS NOT NULL
                              AND mid.inequality_columns IS NOT NULL
                         THEN ',' ELSE '' END + ISNULL (mid.inequality_columns, '')
                  + ')'
                  + ISNULL (' INCLUDE (' + mid.included_columns + ')', '') AS create_index_statement,
        migs.*,
        mid.database_id,
        mid.[object_id]
    FROM sys.dm_db_missing_index_groups AS mig
    INNER JOIN sys.dm_db_missing_index_group_stats AS migs
        ON migs.group_handle = mig.index_group_handle
    INNER JOIN sys.dm_db_missing_index_details AS mid
        ON mig.index_handle = mid.index_handle
    ORDER BY migs.avg_total_user_cost * migs.avg_user_impact * (migs.user_seeks + migs.user_scans) DESC

在此範例中，查詢會導致此建議︰

    CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  

建立之後，該相同的 SELECT 陳述式挑選不同的方案，會使用搜尋，而不是掃描，然後更有效率地執行方案︰

![更正後索引查詢計劃](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

索引鍵充分是 I/O 容量共用，商業系統的限制比的專用的伺服器電腦的多。 在最小化不必要的 I/O 充分發揮系統中的效能的各個層級 Azure SQL 資料庫服務層 DTU 有進階版。 適當的實體資料庫設計的選項可大幅改善個別查詢延遲改善的處理刻度] 單位，每一個要求和最小化符合查詢所需的成本。 如需有關遺失索引 Dmv 的詳細資訊，請參閱[sys.dm_db_missing_index_details](https://msdn.microsoft.com/library/ms345434.aspx)。

### <a name="query-tuning-and-hinting"></a>查詢調整和提示
Azure SQL 資料庫中的最佳化很類似傳統的 SQL Server 最佳化。 大部分的微調查詢，以及瞭解原因的最佳作法最佳化的模型限制也會套用到 Azure SQL 資料庫。 如果您調整 Azure SQL 資料庫中的查詢，您可能會收到減少彙總的資源需求的其他優點。 您的應用程式可能無法執行較低的成本大於 untuned 相等值，因為其可執行較低的效能層級。

範例的常見 SQL server，而這也適用於 Azure SQL 資料庫是如何最佳化 」 探查 」 參數。 在編譯期間，最佳化評估參數，來判斷是否可以產生更好的查詢計劃的目前值。 雖然這種方法通常可能會導致查詢計劃的會比已知的參數值不編譯的計劃更快，目前運作 imperfectly 同時在 SQL Server 和 Azure SQL 資料庫。 有時候會不會竊取參數，及參數側有時，但產生的計劃是不理想的完整的工作量參數值。 Microsoft 包含查詢提示 （指示詞），因此您可以更故意指定用途，並覆寫參數探查的預設行為。 通常，如果您使用提示時，您就可以修正的預設的 SQL Server 或 Azure SQL 資料庫行為是完美針對特定的客戶工作負載的情況。

下一個範例示範如何查詢處理器可以產生同時的效能與資源需求不理想計劃。 此範例所示，是否您使用查詢提示時，您可減少查詢執行時間與資源需求 SQL 資料庫︰

    DROP TABLE psptest1;
    CREATE TABLE psptest1(col1 int primary key identity, col2 int, col3 binary(200));

    DECLARE @a int = 0;
    SET NOCOUNT ON;
    BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO psptest1(col2) values (1);
        INSERT INTO psptest1(col2) values (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION
    CREATE INDEX i1 on psptest1(col2);
    GO

    CREATE PROCEDURE psp1 (@param1 int)
    AS
    BEGIN
        INSERT INTO t1 SELECT * FROM psptest1
        WHERE col2 = @param1
        ORDER BY col2;
    END
    GO

    CREATE PROCEDURE psp2 (@param2 int)
    AS
    BEGIN
        INSERT INTO t1 SELECT * FROM psptest1 WHERE col2 = @param2
        ORDER BY col2
        OPTION (OPTIMIZE FOR (@param2 UNKNOWN))
    END
    GO

    CREATE TABLE t1 (col1 int primary key, col2 int, col3 binary(200));
    GO

安裝程式碼會建立具有扭曲資料通訊群組的資料表。 最佳化查詢計劃，會根據選取的參數。 很抱歉，快取行為的方案不一定重新編譯最常見的參數值為基礎的查詢。 因此，則可能不理想的計劃快取及用於值，即使在不同的計劃可能是較佳的計劃選擇平均。 然後查詢計劃建立兩個是相同，但有特殊的查詢提示的預存程序。

**範例中，第 1 部分**

    -- Prime Procedure Cache with scan plan
    EXEC psp1 @param1=1;
    TRUNCATE TABLE t1;

    -- Iterate multiple times to show the performance difference
    DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp1 @param1=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END

**例如︰ 第 2 部分**

（我們建議以便，結果就會產生遙測資料的不同，請等候至少 10 分鐘的時間之前的範例中，第 2 部分）。

    EXEC psp2 @param2=1;
    TRUNCATE TABLE t1;

    DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp2 @param2=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END

此範例中的每一個部分嘗試執行的參數化的插入陳述式 1000 的時間 （以產生足夠用來作為測試資料集）。 執行預存程序時, 查詢處理器會檢查其 （參數 」 探查 」） 的第一個編譯期間的程序傳遞的參數值。 處理器快取結果的方案，並會將它用於更新引動，即使是不同的參數值。 最佳的計劃可能不能在所有的情況下。 有時候，您需要指南挑選適合一般的情況下，而不是特定的大小寫的第一次編譯查詢時的計劃最佳化。 在此範例中，初始的方案會產生讀取若要尋找符合參數每個值的所有資料列的 「 掃描 「 計劃︰

![查詢微調使用掃描計劃](./media/sql-database-performance-guidance/query_tuning_1.png)

因為我們所使用的值 1 執行的程序，產生的計劃最佳的值 1 但已不理想的資料表中所有其他的值。 可能的結果不是您想要的如果您選擇每個計劃隨機，因為計劃執行速度變慢，並使用更多資源。

如果您執行的測試`SET STATISTICS IO`設定為`ON`，在此範例中的邏輯掃描工作完成的詳細說明。 您可以查看有 1,148 讀取完成 （這是沒有效率，如果一般的情況下會傳回只有一個資料列） 的方案︰

![使用邏輯掃描調整的查詢](./media/sql-database-performance-guidance/query_tuning_2.png)

第二部分的範例使用查詢提示，告訴最佳化編譯程序時所使用的特定的值。 在此情況下，它會強制略過的值做為參數，傳遞查詢處理器，請改為要採用`UNKNOWN`。 這是指具有平均的頻率 （略過 skew） 資料表中的值。 產生的計劃是以搜尋為基礎的計劃更快且平均比計劃使用較少的資源，在此範例中的第 1 部分︰

![查詢微調使用查詢提示](./media/sql-database-performance-guidance/query_tuning_3.png)

您可以看到**sys.resource_stats**表格中的效果 （有測試，將資料填入表格的當您執行的時間延遲）。 此範例中，第 1 部分期間 22:25:00 時間視窗中，執行和第 2 部分 22:35:00 執行。 請注意，舊版的時間範圍更多資源在該時間] 視窗 （由於計劃效率改善） 以外的更新版本。

    SELECT TOP 1000 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![調整範例結果的查詢](./media/sql-database-performance-guidance/query_tuning_4.png)

>[AZURE.NOTE] 雖然在此範例中大量刻意小，則可大，尤其是在較大的資料庫不理想的參數的效果。 差異，在特殊的情況下，可以快速的情況下秒之間緩慢的情況下的時數。

您可以檢查**sys.resource_stats**判斷測試資源是否使用大於另一個測試多或較少的資源。 當您比較資料時，分隔測試的時間，使其不在相同 5 分鐘視窗的 [ **sys.resource_stats** ] 檢視中。 練習的目標是最小化使用，資源的總數量，而不要到最小化的最大使用量資源。 一般而言，最佳化的程式碼的延遲時間也會減少資源消耗。 請確定您的應用程式進行的變更會有必要，請所做的變更會不會影響應用程式中可能會使用查詢提示的任何人的客戶體驗。

如果工作負載有一組的重複查詢，通常這麼做，擷取，然後確認計劃選項的 optimality，因為它磁碟機裝載資料庫所需的最低資源大小單位。 您驗證後，有時重新檢查的方案，協助您確保他們擁有不降級。 您可以進一步瞭解[查詢提示 (SQL)](https://msdn.microsoft.com/library/ms181714.aspx)。

### <a name="cross-database-sharding"></a>跨資料庫 sharding
硬體上執行，Azure SQL 資料庫，因為單一資料庫的容量限制是低於在傳統的內部部署的 SQL Server 安裝。 有些客戶使用 sharding 技術分散到多個資料庫的資料庫作業，當作業不符合單一資料庫 Azure SQL 資料庫中的限制。 Azure SQL 資料庫中使用 sharding 技巧大部分客戶跨多個資料庫分割單一維度其資料。 這種方法，必須先瞭解 OLTP 應用程式通常會執行套用至只有一個資料列或一小組的結構描述中的資料列的交易。

>[AZURE.NOTE] SQL 資料庫現在提供協助 sharding 文件庫。 如需詳細資訊，請參閱[彈性的資料庫用戶端文件庫概觀](sql-database-elastic-database-client-library.md)。

例如，如果資料庫有客戶姓名、 [順序] 和 [訂單詳細資料 （例如 [傳統範例北風] 資料庫隨附於 SQL Server），您可能會分割此資料多個資料庫分組客戶的相關的順序和訂單詳細資訊。 您可以確保客戶的資料保持在單一資料庫。 應用程式會在有效地分配載入至跨多個資料庫的資料庫間分割不同的客戶。 使用 sharding，客戶不只可以避免的最大資料庫大小限制，但是 Azure SQL 資料庫也可以處理負載所大幅大於不同的效能層級的限制，只要符合其 DTU 的每個個別的資料庫。

雖然資料庫 sharding 不降低解決方案的彙總的資源容量，是相當有效在支援超大型分散到多個資料庫的解決方案。 每個資料庫可以在不同的效能層級執行支援很大，高資源需求的 「 有效 」 資料庫。

### <a name="functional-partitioning"></a>設定分割的功能
SQL Server 使用者經常合併單一資料庫中的許多功能。 例如，如果應用程式來管理庫存市集邏輯，該資料庫可能庫存，追蹤採購單、 預存程序，以及索引或具體管理結束的月報表的檢視與相關聯的邏輯。 這項技巧可讓您更容易管理資料庫的備份，例如作業，但也，您需要的大小來處理過的應用程式的所有功能的最大使用量負載硬體。

如果您使用 Azure SQL 資料庫中的擴充架構，最好先分割到不同的資料庫應用程式的不同的函數。 藉由使用此技巧，每個應用程式獨立縮放比例。 應用程式會變成忙碌 （及資料庫載入增加） 系統管理員可以選擇獨立的效能層級，每個函數在應用程式。 限制，請使用此結構，應用程式都大於單一炙手可熱的電腦可以處理，因為載入橫跨多部電腦。

### <a name="batch-queries"></a>批次查詢
使用大量的存取資料的應用程式，經常臨機操作查詢，獲得超乎回應花的時間是在網路應用程式層和 Azure SQL 資料庫層之間的通訊。 即使在應用程式和 Azure SQL 資料庫位於相同的資料中心，兩者之間的網路延遲可能會放大大量的資料來存取作業。 若要降低網路往返資料存取作業，請考慮使用 [批次的臨機操作的查詢，或為預存程序中進行編譯的選項]。 如果您批次的臨機操作的查詢，您可以傳送當作單一旅行中的一個大型批次的多個查詢到 Azure SQL 資料庫。 如果您編譯預存程序中的臨機操作查詢時，您無法達到相同的結果，如果您將它們批次。 使用預存程序也可讓您增加的快取 Azure SQL 資料庫中的查詢計劃，因此您可以一次使用預存程序的優點。

部分應用程式會寫入密切。 有時候，您可以考慮如何共同批次寫入減少總 I/O 載入資料庫上。 通常，這是非常簡單，使用明確的交易，而不自動認可交易預存程序及臨機操作批次。 您可以使用不同的技術評估，請參閱[Azure SQL 資料庫應用程式的批次處理技巧](https://msdn.microsoft.com/library/windowsazure/dn132615.aspx)。 嘗試使用您自己的工作量批次處理尋找正確的模型。 請務必瞭解模型可能稍有不同的交易一致性保證。 尋找最小化 [資源使用右工作量需要尋找正確的一致性和效能折衷方案組合。

### <a name="application-tier-caching"></a>應用程式層快取
有些資料庫應用程式有閱讀大量的工作量。 快取圖層，可能會降低資料庫載入，而且可能會減少支援使用 Azure SQL 資料庫的資料庫所需的效能層級。 具有[Azure Redis 快取](https://azure.microsoft.com/services/cache/)，如果您有閱讀大量的工作量，您可以讀取資料一次 (或可能是根據的設定方式而定的應用程式層電腦每一次)，然後儲存 SQL 資料庫的資料。 此方法可減少資料庫載入 （CPU 和讀取 I/O），但在交易的一致性效果因為被讀取從快取資料可能會在資料庫中的資料同步處理。 雖然許多應用程式中某些層級的不一致性是可接受，但不的所有工作負載均為 true。 完整執行應用程式層快取策略之前，您應該瞭解任何應用程式的需求。

## <a name="next-steps"></a>後續步驟

- 如需有關服務層級的詳細資訊，請參閱[SQL 資料庫選項和效能](sql-database-service-tiers.md)
- 如需有關彈性的資料庫資料庫的詳細資訊，請參閱[什麼是 Azure 彈性的資料庫資料庫？](sql-database-elastic-pool.md)
- 效能和彈性的資料庫集區的相關資訊，請參閱[時要考量的彈性的資料庫集區](sql-database-elastic-pool-guidance.md)
