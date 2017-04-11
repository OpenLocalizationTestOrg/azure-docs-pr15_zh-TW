<properties
    pageTitle="Azure SQL 資料庫資源限制"
    description="本頁面說明一些常見的資源限制 Azure SQL 資料庫。"
    services="sql-database"
    documentationCenter="na"
    authors="CarlRabeler"
    manager="jhubbard"
    editor="monicar" />


<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="10/13/2016"
    ms.author="carlrab" />


# <a name="azure-sql-database-resource-limits"></a>Azure SQL 資料庫資源限制

## <a name="overview"></a>概觀

Azure SQL 資料庫管理才能使用這兩個不同機制資料庫的資源︰**資源治理**與**強制執行的限制**。 本主題說明資源管理下列兩個主要區域。

## <a name="resource-governance"></a>資源管理
基本、 標準和進階版服務層級的設計目的是以 「 資料庫執行自己在電腦上，從其他資料庫完全隔離 Azure SQL 資料庫。 資源管理資料列傳回這個問題。 如果彙總的資源使用狀況達到最大可用的 CPU、 記憶體、 記錄 I/O 和資料 I/O 資源分派給資料庫，資源管理將佇列中執行的查詢，並將資源分派給佇列查詢，為他們設定。

為固定在電腦上，利用所有可用的資源將會導致目前正在執行查詢，用戶端上的命令逾時可能會產生更長的時間執行。 使用積極重試邏輯的應用程式與對高頻率資料庫執行查詢的應用程式時可能會出現錯誤訊息嘗試執行新增查詢時已達同時的要求的限制。

### <a name="recommendations"></a>建議︰
資源使用狀況，以及查詢的平均回應時間時監視接近使用資料庫的上限。 當遇到更高的查詢延遲通常會有三個選項︰

1.  減少傳入要求資料庫以防止逾時，設定堆的要求。

2.  指派至資料庫的較高的效能。

3.  最佳化來減少資源使用狀況，每個查詢的查詢。 如需詳細資訊，請參閱 Azure SQL 資料庫效能指引文件中的 [查詢調整/Hinting] 區段。

## <a name="enforcement-of-limits"></a>強制執行限制
CPU、 記憶體、 記錄 I/O 和資料 I/O 以外的資源會強制執行限制達到拒絕新的要求。 用戶端會收到[錯誤訊息](sql-database-develop-error-messages.md)根據已達的限制。

例如，連線到 SQL 資料庫的數目為 3 的同時的要求可以處理數受到限制。 SQL 資料庫可讓數大於數同時的要求支援連線共用資料庫的連線。 應用程式時，可以輕鬆地控制連線所提供的數量，同時平行要求數量，通常會困難估計和時間。 特別是在尖峰負載時可能會傳送要求太多或資料庫達到其資源的應用程式限制，並開始堆積執行緒以更長的時間執行查詢，因為可以發生錯誤。

## <a name="service-tiers-and-performance-levels"></a>服務層和效能層級

有服務層和效能層級的兩個獨立資料庫及彈性集區。

### <a name="standalone-databases"></a>獨立的資料庫

為獨立的資料庫，資料庫的限制是由資料庫服務層和效能層級所定義。 下表說明在不同的效能層級的資料庫的基本、 標準、 和付費的特性。

[AZURE.INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

### <a name="elastic-pools"></a>彈性的資料庫

[彈性的資料庫](sql-database-elastic-pool.md)在資料庫中的資料庫間共用資源。 下表描述基本、 標準和進階版的彈性的資料庫集區的特性。

[AZURE.INCLUDE [SQL DB service tiers table for elastic databases](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

展開所列的前一個資料表中每個資源的定義，請參閱[服務層功能和限制](sql-database-performance-guidance.md#service-tier-capabilities-and-limits)中的描述。 如需服務層的概觀，請參閱[Azure SQL 資料庫服務層和效能層級](sql-database-service-tiers.md)。

## <a name="other-sql-database-limits"></a>其他 SQL 資料庫限制

| 區域 | 限制 | 描述 |
|---|---|---|
| 使用每個訂閱的自動匯出的資料庫 | 10 | 自動化的匯出可讓您以建立自訂備份 SQL 資料庫的排程。 如需詳細資訊，請參閱[SQL 資料庫︰ 支援自動化 SQL 資料庫匯出](http://weblogs.asp.net/scottgu/windows-azure-july-updates-sql-database-traffic-manager-autoscale-virtual-machines)。|
| 每個伺服器的資料庫 | 多達 5000 筆 | 最 5000 資料庫允許的每個 V12 伺服器上的伺服器。 |  
| 每個伺服器 DTUs | 45000 | 45000 DTUs 可每個佈建資料庫、 彈性的資料庫及資料倉儲 V12 伺服器上的伺服器。 |



## <a name="resources"></a>資源

[Azure 訂閱及服務限制，配額和限制](../azure-subscription-service-limits.md)

[Azure SQL 資料庫服務層和效能層級](sql-database-service-tiers.md)

[SQL 資料庫用戶端程式的錯誤訊息](sql-database-develop-error-messages.md)
