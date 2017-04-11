<properties
    pageTitle="在伺服器上的資料庫不是目前提供、 連線至 SQL 資料庫 |Microsoft Azure"
    description="疑難排解上的 [資料庫伺服器不是目前可用的錯誤時，應用程式連線到 SQL 資料庫。"
    services="sql-database"
    documentationCenter=""
    authors="dalechen"
    manager="felixwu"
    editor=""
    keywords="在伺服器上的資料庫不是目前提供、 連線至 sql 資料庫"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/21/2016"
    ms.author="daleche"/>

# <a name="error-database-on-server-is-not-currently-available-when-connecting-to-sql-database"></a>錯誤] 資料庫伺服器上的不存在 」 連線至 sql 資料庫時
[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

當應用程式連線至 Azure SQL 資料庫時，您會收到下列錯誤訊息︰

```
Error code 40613: "Database <x> on server <y> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of <z>"
```

> [AZURE.NOTE] 此錯誤訊息是通常暫時性 」。

Azure 資料庫時，會發生此錯誤移動 （或重新設定） 和您的應用程式將會遺失其連線至 SQL 資料庫。 SQL 資料庫重新設定事件發生計劃的事件 （例如軟體升級） 或意外的事件 （例如，程序損毀，或負載平衡）。 大部分的重新設定事件通常短暫，應該最完成小於 60 秒數。 不過，這些事件有時候可能需要較長的時間完成，例如大型交易時使長時間執行復原。

## <a name="steps-to-resolve-transient-connectivity-issues"></a>若要解決暫時性連線問題的步驟
1.  核取 [ [Microsoft Azure 服務儀表板](https://azure.microsoft.com/status)的期間發生錯誤的應用程式時，發生任何已知問題。
2. 連線至雲端服務，例如 Azure SQL 資料庫應該預期重新設定週期性事件及實作的應用程式重試邏輯來處理這些錯誤，而非出現這些為應用程式錯誤的使用者。 檢閱的[暫時性錯誤](sql-database-connectivity-issues.md)區段及最佳作法和設計規則在[SQL 資料庫開發概觀](sql-database-develop-overview.md)如需詳細資訊和一般策略再試一次。 請參閱在[SQL 資料庫與 SQL Server 的連線文件庫](sql-database-libraries.md)的程式碼範例，如需詳細資訊。
3.  為資料庫接近資源限制，它看起來似乎是一個暫時性連線問題。 請參閱[疑難排解效能問題](sql-database-troubleshoot-performance.md)。
4.  如果持續連線發生問題，或的應用程式發生錯誤的工期超過 60 秒，或如果您看到的錯誤的多個項目中指定的日期，請選取[Azure 支援](https://azure.microsoft.com/support/options)網站上的 [**取得支援**檔案 Azure 支援要求。

## <a name="next-steps"></a>後續步驟
- 如果您收到不同的錯誤，評估線索原因[錯誤訊息](sql-database-develop-error-messages.md)。
- 如果問題持續性，請造訪[疑難排解常見連線問題到 Azure SQL 資料庫](sql-database-troubleshoot-common-connection-issues.md)中的指引。
