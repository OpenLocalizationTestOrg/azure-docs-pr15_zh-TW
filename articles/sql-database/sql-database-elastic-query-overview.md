<properties
    pageTitle="Azure SQL 資料庫彈性的資料庫查詢概觀 |Microsoft Azure"
    description="彈性的查詢功能的概觀"    
    services="sql-database"
    documentationCenter=""  
    manager="jhubbard"
    authors="torsteng"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/27/2016"
    ms.author="torsteng" />

# <a name="azure-sql-database-elastic-database-query-overview-preview"></a>Azure SQL 資料庫彈性的資料庫查詢概觀 （預覽版本）

彈性的資料庫查詢中的功能 （預覽版本） 可讓您執行 SQL 查詢橫跨多個資料庫中 Azure SQL 資料庫 (SQLDB)。 其可讓您執行遠端資料表，並連線 Microsoft 和協力廠商工具 （Excel、 中、 Tableau 等） 來查詢跨多個資料庫時的資料階層的跨資料庫查詢。 使用此功能，您就可以擴充至大型資料層 SQL 資料庫中的查詢，並以視覺化方式呈現商務智慧 (BI) 報表中的結果。

## <a name="documentation"></a>文件

* [開始使用跨資料庫查詢](sql-database-elastic-query-getting-started-vertical.md)
* [縮放出雲端資料庫之間的報表](sql-database-elastic-query-getting-started.md)
* [跨 sharded 雲端資料庫 （水平分割） 查詢](sql-database-elastic-query-horizontal-partitioning.md)
* [在雲端的資料庫間不同的結構描述 （垂直分割） 的查詢](sql-database-elastic-query-vertical-partitioning.md)
* [sp\_執行\_遠端](https://msdn.microsoft.com/library/mt703714)


## <a name="why-use-elastic-queries"></a>為什麼要使用彈性的查詢？

**Azure SQL 資料庫**

查詢跨完全中 T SQL Azure SQL 資料庫。 如此一來唯讀查詢的遠端資料庫。 這會提供目前內部部署的 SQL Server 客戶移轉應用程式使用三和 four part 名稱或連結的 SQL 資料庫伺服器的選項。

**可用的標準層**支援的進階版效能層除了在標準的效能層彈性的查詢。 請參閱下方的預覽限制較低的效能層的效能限制。

**推播到遠端資料庫**

彈性的查詢現在可以執行的遠端資料庫發送 SQL 參數。

**預存程序執行**

執行遠端預存程序呼叫或遠端函數使用[預存程序\_執行\_遠端](https://msdn.microsoft.com/library/mt703714)。

**彈性**

彈性的查詢包含外部表格，現在可參閱遠端資料表使用不同的結構描述或資料表名稱。

## <a name="elastic-database-query-scenarios"></a>彈性的資料庫查詢案例

目標是為了查詢的分析藍本多個資料庫產生單一結果整體參與列的位置。 透過使用者或應用程式直接或間接連線至資料庫的工具，可表示撰寫查詢。 建立報表，請使用商業 BI 或資料整合工具時，這是尤其有用，或不能變更任何應用程式。 使用彈性的查詢，您可以查詢到數個資料庫，例如 Excel、 中、 Tableau 或 Cognos 工具中使用熟悉的 SQL Server 連線體驗。
彈性的查詢可讓您輕鬆存取透過發出 SQL Server Management Studio 或 Visual Studio 中，查詢資料庫的整個集合，並加速跨資料庫查詢從實體架構或其他 ORM 環境。 圖 1 顯示的情況橫向擴充的資料層，是根據現有的雲端應用程式 （即使用[彈性的資料庫用戶端文件庫](sql-database-elastic-database-client-library.md)） 和彈性的查詢用來跨資料庫報告位置。

**圖 1**使用橫向擴充的資料層的彈性的資料庫查詢

![使用橫向擴充的資料層的彈性的資料庫查詢][1]

彈性的查詢的客戶案例會根據下列拓撲各自不同︰

* **垂直分割 – 跨資料庫查詢**（拓撲 1）︰ 資料將會在資料層資料庫的數字之間垂直分割。 一般而言，不同的表格集位於不同的資料庫。 這表示結構描述其他在不同的資料庫。 舉例來說，所有資料表庫存都了一個資料庫時會計相關的所有資料表第二個資料庫。 常見的使用情況下，使用此拓撲需要一到跨查詢或編譯報表跨多個資料庫中的資料表。
* **水平分割 – Sharding**（拓撲 2）︰ 若要分散出不按比例縮放的資料列水平分割資料層。 使用這個方法，架構是相同所有參與的資料庫。 此方法也稱為 「 sharding 」。 可執行 sharding 及受管理的使用 （1） 的彈性的資料庫工具] 的 [文件庫或 （2) 自我 sharding。 彈性的查詢用來查詢或跨多擊碎編譯報表。

> [AZURE.NOTE] 彈性的資料庫查詢最適合位置執行大部分的處理程序，在資料層偶爾發生的報表案例。 經常報表負載或資料倉儲案例更複雜的查詢，也請考慮使用[Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/)。


## <a name="elastic-database-query-topologies"></a>彈性的資料庫查詢拓撲

### <a name="topology-1-vertical-partitioning--cross-database-queries"></a>拓撲 1︰ 垂直分割 – 跨資料庫查詢

若要開始撰寫程式碼，請參閱[快速入門跨資料庫查詢 （垂直分割）](sql-database-elastic-query-getting-started-vertical.md)。

彈性的查詢可讓位於 SQLDB 資料庫才能使用其他 SQLDB 資料庫中的資料。 這個選項可讓查詢的任何其他遠端 SQLDB 資料庫中的資料表參照一個資料庫。 第一步是定義外部資料來源的每個遠端資料庫。 外部資料來源定義在您要存取位於遠端資料庫的資料表的本機資料庫。 不不需要在遠端資料庫上的任何變更。 一般垂直分割案例中，不同的資料庫會使用不同的結構描述，彈性的查詢可以用實作一般的使用情況下，例如 [參考資料的存取權，跨資料庫查詢。

**參考資料**︰ 拓撲 1 用於參考資料管理。 在下圖中的兩個資料表 （T1 和 T2） 參考資料會保留上專用的資料庫。 使用的彈性的查詢，您現在可以存取表格 T1 和 T2 遠端從其他資料庫，如下圖所示。 使用拓撲 1 至參照資料表的小型或遠端查詢參照資料表是否有選擇性述詞。

**圖 2**垂直分割-使用彈性的查詢以查詢參照的資料

![垂直分割-使用彈性的查詢以查詢參照的資料][3]

**跨資料庫查詢**︰ 彈性查詢啟用需要跨多個 SQLDB 資料庫查詢的使用案例。 圖 3] 顯示四個不同的資料庫︰ CRM、 庫存、 人力資源和產品。 在一個資料庫中執行的查詢也需要一個或所有其他資料庫的存取權。 使用的彈性的查詢，您可以設定您的資料庫，這種情況下的每個四個資料庫執行幾個簡單的 DDL 陳述式。 此一次性設定之後，來存取遠端資料表是非常簡單，參照區域的表格從 T SQL 查詢或您的 BI 工具。 如果遠端查詢無法傳回較大的結果，建議使用此方法。

**圖 3**垂直分割-使用各種不同的資料庫查詢至查詢的彈性

![垂直分割-使用各種不同的資料庫查詢至查詢的彈性][4]

### <a name="topology-2-horizontal-partitioning--sharding"></a>拓撲 2︰ 水平分割 – sharding

資料層彈性查詢來執行報表工作透過 sharded，亦即水平分割，需要將[彈性的資料庫晶怪地圖](sql-database-elastic-scale-shard-map-management.md)來代表資料層的資料庫。 一般而言，在這個案例中用於只在單一晶怪地圖，並有彈性的查詢功能的專用的資料庫做為報表查詢的進入點。 僅限此專屬的資料庫以需要存取晶怪地圖。 圖 4 說明此拓撲和彈性的查詢資料庫及晶怪地圖其設定。 在資料層資料庫可以是任何 Azure SQL 資料庫版本或版本。 如需有關彈性的資料庫用戶端文件庫和建立晶怪對應的詳細資訊，請參閱[晶怪對應管理](sql-database-elastic-scale-shard-map-management.md)。

**圖 4**水平分割-sharded 資料層在報表中使用彈性的查詢

![水平分割-sharded 資料層在報表中使用彈性的查詢][5]

> [AZURE.NOTE] 專用的彈性資料庫查詢資料庫必須是 SQL 資料庫 v12 資料庫。 不有任何限制擊碎本身。

若要開始撰寫程式碼，請參閱[快速入門彈性的資料庫查詢的水平分割 (sharding)](sql-database-elastic-query-getting-started.md)。

## <a name="implementing-elastic-database-queries"></a>實作彈性的資料庫查詢

下列各節討論實作垂直和水平分割案例的彈性查詢步驟。 他們也請參閱更詳細的文件，以不同的分割情況。

### <a name="vertical-partitioning---cross-database-queries"></a>垂直分割-跨資料庫查詢

下列步驟設定需要存取位於相同的結構描述的遠端 SQLDB 資料庫表格的垂直分割案例的彈性的資料庫查詢︰

*    [建立主索引鍵](https://msdn.microsoft.com/library/ms174382.aspx)mymasterkey
*    [建立資料庫範圍認證](https://msdn.microsoft.com/library/mt270260.aspx)mycredential
*    類型**RDBMS**的[外部資料來源建立放](https://msdn.microsoft.com/library/dn935022.aspx)mydatasource
*    我[建立放外部表格](https://msdn.microsoft.com/library/dn935021.aspx)的表格

執行之後 DDL 陳述式，您可以在當作本機表格存取遠端資料表 「 我的表格]。 Azure SQL 資料庫會自動開啟連線到遠端資料庫，處理您的要求遠端資料庫，然後傳回結果。
[垂直分割的彈性查詢](sql-database-elastic-query-vertical-partitioning.md)中找垂直分割案例所需的步驟的詳細資訊。  

### <a name="horizontal-partitioning---sharding"></a>水平分割-sharding

下列步驟設定 （通常是） 數個遠端 SQLDB 資料庫的水平分割案例需要存取表格的一組位於的彈性的資料庫查詢︰

*    [建立主索引鍵](https://msdn.microsoft.com/library/ms174382.aspx)mymasterkey
*    [建立資料庫範圍認證](https://msdn.microsoft.com/library/mt270260.aspx)mycredential
*    建立代表您使用的彈性的資料庫用戶端文件庫的資料層[晶怪地圖](sql-database-elastic-scale-shard-map-management.md)。   
*    類型**SHARD_MAP_MANAGER**的[外部資料來源建立放](https://msdn.microsoft.com/library/dn935022.aspx)mydatasource
*    我[建立放外部表格](https://msdn.microsoft.com/library/dn935021.aspx)的表格

當您執行這些步驟時，您可以存取水平分割的表格 「 我的表格 」 當作區域的資料表。 Azure SQL 資料庫會自動開啟多個平行的連線到遠端資料庫資料表的實際儲存位置，處理要求在遠端資料庫，然後傳回結果。
[水平分割的彈性查詢](sql-database-elastic-query-horizontal-partitioning.md)中找 [水平分割案例所需的步驟的詳細資訊。

## <a name="t-sql-querying"></a>T SQL 查詢
一旦您已經定義外部資料來源與外部表格，您可以使用一般的 SQL Server 連線字串連線至資料庫定義外部表格的位置。 您可以再執行 T SQL 陳述式外部表格在該連接使用以下所列的限制。 您可以找到詳細的資訊與範例 T SQL 查詢中的文件主題[設定分割的水平](sql-database-elastic-query-horizontal-partitioning.md)與[垂直分割](sql-database-elastic-query-vertical-partitioning.md)。

## <a name="connectivity-for-tools"></a>連線的工具
若要連線的外部表格的資料庫應用程式和 BI 或資料整合工具，您可以使用一般的 SQL Server 連線字串。 請確定 SQL Server 支援作為資料來源的工具。 連線之後，請參閱彈性的查詢資料庫及外部資料庫資料表中，就像您想要執行的任何其他與工具連線至 SQL Server 資料庫。

> [AZURE.IMPORTANT] 目前不支援使用彈性的查詢中的 Azure Active Directory 驗證。

## <a name="cost"></a>成本

彈性的查詢納入 Azure SQL 資料庫資料庫的成本。 請注意，支援的拓撲遠端資料庫會比彈性的查詢端點在不同的資料中心的位置，從遠端資料庫的資料出口負責一般[Azure 費率](https://azure.microsoft.com/pricing/details/data-transfers/)。

## <a name="preview-limitations"></a>預覽限制
* 執行您的第一個彈性查詢可以最多可能需要幾分鐘的時間標準效能層。 這次，則需要載入彈性的查詢功能;載入的效能提升以較高的效能層。
* 尚未支援指令碼的外部資料來源或從 SSMS 或 SSDT 外部表格。
* 匯入/匯出 sql 資料庫還不支援的外部資料來源與外部的資料表。 如果您需要使用匯入/匯出]，在匯出之前先放這些物件，然後匯入後重新建立這些。
* 彈性的資料庫查詢時，目前僅支援唯讀存取外部表格。 您仍可以使用完整 T SQL 功能資料庫定義外部表格的位置。 可使用，例如固定暫時使用，例如的結果，請選取 [< local_table >，將 [< column_list > 或是定義預存程序彈性的查詢資料庫的參照外部的資料表。
* Nvarchar （max），除了 LOB 類型不支援外部表格定義。 因應措施，您可以建立遠端資料庫轉換成 nvarchar （max） LOB 類型的檢視、 定義，而不是基底表格檢視外部表格，然後將它轉換回原始 LOB 類型，在查詢中。
* 目前不支援透過外部表格資料行統計資料。 表格統計資料受到支援，但必須以手動方式建立。

## <a name="feedback"></a>意見反應
請在您的體驗上的意見反應彈性的查詢與共用我們 Stackoverflow 或 Disqus 下列 MSDN 論壇上。 我們會感興趣的所有類型的服務 （瑕疵、 粗糙邊緣，功能差距） 的相關意見反應。

## <a name="more-information"></a>詳細資訊

您可以在下列文件中找到的跨資料庫查詢，然後垂直分割案例的詳細資訊︰

* [跨資料庫查詢及垂直分割概觀](sql-database-elastic-query-vertical-partitioning.md)
* 請嘗試將完整我們逐步教學課程以分鐘為單位執行的作業範例︰[快速入門跨資料庫查詢 （垂直分割）](sql-database-elastic-query-getting-started-vertical.md)。


以下有水平分割和 sharding 案例的詳細資訊︰

* [水平分割和 sharding 概觀](sql-database-elastic-query-horizontal-partitioning.md)
* 請嘗試將完整我們逐步教學課程以分鐘為單位執行的作業範例︰[快速入門彈性的資料庫查詢的水平分割 (sharding)](sql-database-elastic-query-getting-started.md)。


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
[2]: ./media/sql-database-elastic-query-overview/topology1.png
[3]: ./media/sql-database-elastic-query-overview/vertpartrrefdata.png
[4]: ./media/sql-database-elastic-query-overview/verticalpartitioning.png
[5]: ./media/sql-database-elastic-query-overview/horizontalpartitioning.png

<!--anchors-->
