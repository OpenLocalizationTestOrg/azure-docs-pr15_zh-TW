資料庫交易單位 (DTU) 是表示的資料庫在真實世界量值相對 power SQL 資料庫中的度量單位︰ 資料庫交易。 我們原本是一般的線上交易處理 (OLTP) 的要求，並多少交易完成每底下的第二個完全載入條件之作業的一組 （的是精簡版本，您可以閱讀[事先概觀](../articles/sql-database/sql-database-benchmark-overview.md)中的繁雜詳細資料）。 

例如 1750 DTUs 的進階版 P11 資料庫會提供更多 DTU x 350 計算電源超過 5 DTUs 的基本資料庫。 

![SQL 資料庫簡介︰ 單一層級，資料庫 DTUs。](./media/sql-database-understanding-dtus/single_db_dtus.png)

>[AZURE.NOTE] 如果您要移轉現有的 SQL Server 資料庫，您可以使用協力廠商工具， [Azure SQL 資料庫 DTU 計算機](http://dtucalculator.azurewebsites.net/)，若要取得效能層級的估計值與服務資料庫可能需要 Azure SQL 資料庫中的層。

### <a name="dtu-vs-edtu"></a>與 eDTU DTU

單一資料庫的 DTU 會直接將轉譯 eDTU 彈性的資料庫。 例如，資料庫中的基本的彈性資料庫集區提供最多 5 eDTUs。 這是以單一基本資料庫相同的效能。 不同的是彈性的資料庫不會取用資料庫中的任何 eDTUs，直到它。 

![SQL 資料庫簡介︰ 層彈性的資料庫。](./media/sql-database-understanding-dtus/sqldb_elastic_pools.png)

簡單範例可協助。 需要 1000 DTUs 基本的彈性資料庫集區拖 800 資料庫中。 只要在任何時候使用只 200 800 資料庫的時間 (5 DTU X 200 = 1000)，您就不會叫用的資料庫，容量，而且資料庫的效能不會降低。 此範例中是為了簡化。 實際的數學是有些複雜。 入口網站的數學運算，並可讓您根據歷史資料庫的使用狀況的建議。 請參閱[的彈性的資料庫資料庫價格與效能考量](../articles/sql-database/sql-database-elastic-pool-guidance.md)，瞭解如何使用建議，或您自己執行數學運算。 
