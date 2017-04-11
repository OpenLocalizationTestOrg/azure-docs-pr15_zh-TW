<properties
    pageTitle="SQL 資料庫效能調整秘訣 |Microsoft Azure"
    description="效能調整 Azure SQL 資料庫中透過評估和改進的秘訣。"
    services="sql-database"
    documentationCenter=""
    authors="v-shysun"
    manager="felixwu"
    editor=""
    keywords="sql 效能調整，資料庫效能調整，sql 效能調整秘訣，sql 資料庫效能調整"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="v-shysun"/>

# <a name="sql-database-performance-tuning-tips"></a>SQL 資料庫效能調整秘訣
您可以變更單一資料庫的[服務層級](sql-database-service-tiers.md)，或以改善效能，隨時增加彈性的資料庫集區的 eDTUs，但是您可能會想要識別改善和第一次最佳化查詢效能的商機。 遺漏索引和不佳最佳化的查詢是常見原因的效能不佳的資料庫。 本文提供效能調整 SQL 資料庫中的指示。

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="steps-to-evaluate-and-tune-database-performance"></a>資料庫的步驟來評估並調整效能
1.  在[Azure 入口網站](https://portal.azure.com)，按一下 [ **SQL 資料庫]**、 選取資料庫]，然後使用監控圖表資源接近其最大值的查詢。 根據預設，會顯示 DTU 消耗。 按一下 [**編輯**]，若要變更的時間範圍及所顯示的值。
2.  使用[查詢效能充分](sql-database-query-performance.md)評估使用 DTUs，查詢，然後使用[SQL 資料庫顧問](sql-database-advisor.md)若要檢視的建立與卸除索引、 參數化查詢，和結構描述問題的修正建議。
3.  您可以使用動態管理檢視 (Dmv)、 延伸事件 (Xevents) 和查詢中的存放區 SSMS 即時取得效能參數。 請參閱詳細的監視及調整秘訣[效能指引主題](sql-database-performance-guidance.md)。


    > [AZURE.IMPORTANT] 建議您永遠保持同步處理與更新 Microsoft Azure SQL 資料庫使用 Management Studio 中的最新版本。 [更新 SQL Server Management Studio 中](https://msdn.microsoft.com/library/mt238290.aspx)。


## <a name="steps-to-improve-database-performance-with-more-resources"></a>改善效能更多資源資料庫的步驟
1.  單一的資料庫，您可以[變更服務層](sql-database-scale-up.md)視需要以提升效能資料庫。
2.  多重資料庫，請考慮使用自動調整資源的[彈性的資料庫集區](sql-database-elastic-pool-guidance.md)。
