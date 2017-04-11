<properties 
   pageTitle="Azure SQL 資料庫效能充分 |Microsoft Azure" 
   description="Azure SQL 資料庫提供的效能工具，可協助您找出可改善目前的查詢效能的區域。" 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="07/19/2016"
   ms.author="sstein"/>

# <a name="sql-database-performance-insight"></a>SQL 資料庫效能充分

Azure SQL 資料庫提供效能工具，可協助您找出並提供智慧微調動作和建議改善效能的資料庫。 

1. 瀏覽至資料庫中[Azure 入口網站](http://portal.azure.com)，然後按一下 [**所有設定** > **效能** > 開啟**效能**] 頁面的**概觀**。 


2. 按一下 [**建議**以開啟 [ [SQL 資料庫警告](#sql-database-advisor)中，然後按一下 [開啟[查詢效能充分](#query-performance-insight)的**查詢**。

    ![檢視效能](./media/sql-database-performance/entries.png)



## <a name="performance-overview"></a>效能概觀

按一下在 [**概觀**] 或 [**效能**圖磚上將您移至您的資料庫的效能儀表板。 此檢視會提供您資料庫的效能的摘要，並協助您與效能調整與疑難排解。 

![效能](./media/sql-database-performance/performance.png)

- [**建議**] 磚分解調整您的資料庫的建議 （頂端 3 建議顯示是否有更多）。 按一下此方塊會帶您到**SQL 資料庫顧問**。 
- **調整活動**磚提供進行中及調整資料庫的動作，讓您快速檢視的調整活動歷程記錄將已完成的摘要。 按一下此方塊會帶您到您的資料庫的完整微調歷程記錄檢視。
- [**自動調整**] 方塊會顯示您設定的資料庫 （哪些微調動作會自動套用至您的資料庫） 自動調整設定。 按一下此並排顯示開啟自動化設定] 對話方塊。
- [**資料庫查詢**] 方塊會顯示您的資料庫 （整體 DTU 使用狀況] 和 [頂端資源分成查詢） 的查詢效能的摘要。 按一下此方塊會帶您到**查詢效能充分**。



## <a name="sql-database-advisor"></a>SQL 資料庫顧問


[SQL 資料庫顧問](sql-database-advisor.md)提供可協助改善您的資料庫效能的智慧微調建議。 

- 哪些索引建立或卸除 （以及套用自動沒有任何使用者互動與自動回復有負面影響效能的建議索引建議選項） 的建議。
- 識別資料庫中的結構描述問題時的建議。
- 查詢可以受益參數化查詢時的建議。




## <a name="query-performance-insight"></a>查詢效能充分

[查詢效能充分](sql-database-query-performance.md)可讓您花在資料庫效能疑難排解提供較少的時間︰

- 到消耗您資料庫資源 (DTU) 的深入見解。 
- 使用查詢，可能會針對調整上方 CPU 改善效能。 
- 向下切入的查詢詳細資料的能力。 


## <a name="additional-resources"></a>其他資源

- [Azure SQL 資料庫效能指導單一資料庫](sql-database-performance-guidance.md)
- [何時使用彈性的資料庫資料庫？](sql-database-elastic-pool-guidance.md)