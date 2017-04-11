<properties
   pageTitle="管理 Azure SQL Data Warehouse （概觀） 中的計算 power |Microsoft Azure"
   description="效能功能 Azure SQL Data Warehouse 延展。 藉由調整 DWUs 擴展或暫停並繼續儲存成本的計算資源。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/03/2016"
   ms.author="barbkess;sonyama"/>

# <a name="manage-compute-power-in-azure-sql-data-warehouse-overview"></a>管理運算能力中 Azure SQL Data Warehouse （概觀）

> [AZURE.SELECTOR]
- [概觀](sql-data-warehouse-manage-compute-overview.md)
- [入口網站](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [其餘](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)

SQL Data Warehouse 架構分隔儲存和計算，讓每一個調整大小。 如此一來，您可以調整出時，您下次需要它時，只支付效能儲存成本的效能。 

此概觀說明 SQL Data Warehouse 下列效能擴充功能，並提供建議，如何與何時使用。 

- 小數位數計算 power 藉由調整[倉儲單位 (DWUs) 的資料][]
- 暫停或繼續計算資源

<a name="scale-performance-bk"></a>

## <a name="scale-performance"></a>縮放比例效能

在 SQL Data Warehouse，您可以快速縮放或回以增加或減少的 CPU、 記憶體和 I/O 頻寬計算資源的效能。 若要縮放效能，您只需要是調整[倉儲單位 (DWUs) 資料][]的 SQL Data Warehouse 配置給您的資料庫的數目。 SQL Data Warehouse 快速進行變更，以及處理所有的基礎變更硬體或軟體。

已不再是必須研究何種類型的處理器、 記憶體或您要在您的資料倉庫有較佳的效能的何種類型的儲存空間。 將您的資料倉庫放在雲端，您不再需要處理低功率硬體問題。 不過，SQL Data Warehouse 要求您這個問題︰ 您要分析資料速度？ 

### <a name="how-do-i-scale-performance"></a>我要如何調整效能？

Elastically 增加或減少您運算能力，只要變更您的資料庫[倉儲單位 (DWUs) 的資料][]設定。 當您新增更多 DWU，將直線提高效能。  在較高的 DWU 層級，您需要新增更多不要超過 100 個 DWUs 注意到大幅改善效能。 若要協助您在 DWUs 選取有意義的跳轉，我們提供 DWU 層級會提供最佳的結果。
 
若要調整 DWUs，您可以使用任何一種個別的方法。

- [小數位數計算 power Azure 入口網站][]
- [小數位數計算 power 使用 PowerShell][]
- [使用 REST Api 的小數位數計算 power][]
- [小數位數計算 TSQL 電源][]

### <a name="how-many-dwus-should-i-use"></a>我應該使用多少 DWUs？
 
SQL Data Warehouse 效能調整直線，並從一個計算比例變更為另一個 （假設是從 100 到 2000 DWUs DWUs) 秒數會發生的情況。 這可讓您嘗試使用不同 DWU 設定，直到您判斷您的狀況最相符的彈性。

若要瞭解您理想的 DWU 值是什麼、 嘗試縮放向上和向下，以及執行幾個查詢載入資料之後。 由於您是快速縮放比例，您可以嘗試不同層級的效能在一小時之內的數字。 執行請記住，SQL Data Warehouse 設計用的大量資料，並請參閱調整其 true 功能，尤其是在較大的縮放比例我們提供時，您會想要使用大型資料集接近或超過 1 TB。

為您的工作量尋找最佳 DWU 建議︰

1. 針對開發中的資料倉庫，選取開始 DWUs 數量。  絕佳起點是 DW400 或 DW200。
2. 監控您的應用程式效能，觀察 DWUs 選取的數字相較於您所看到的效能。
3. 決定多少加快或放慢效能應該是您的連絡您的需求的最佳效能層級假設線性的小數位數。
4. 增加或減少數 DWUs 成比例如何更快或較慢您想要執行您的工作量。 會快速回應及調整 DWU 需求新的計算資源的服務。
5. 繼續進行調整，直到您到達可獲得最佳效能層級，您的業務需求。

### <a name="when-should-i-scale-dwus"></a>何時應縮放 DWUs？

當您需要更快獲得的結果時，增加您 DWUs 並支付更大的效能。  當您需要較少計算 power、 減少您 DWUs 並支付僅適用於您的需要。 

建議 DWUs 不按比例縮放的時機︰

1. 如果您的應用程式有變動的工作量，比例 DWU 層級的向上或向下容納尖峰和低點。 例如，如果您的工作量通常尖峰結尾的月份，計劃新增更多 DWUs 這些最大使用量天內，然後移到最大使用量期間後縮小。
2. 您執行大量的資料載入或轉換作業之前，不按比例縮放設定 DWUs 以便更快速地為使用您的資料。

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>暫停運算

[AZURE.INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

若要將游標停留在資料庫，使用任何一種個別的方法。

- [暫停計算 Azure 入口網站][]
- [使用 PowerShell 暫停運算][]
- [使用 REST Api 暫停運算][]

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>履歷表運算

[AZURE.INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

若要繼續資料庫，請使用任何一種個別的方法。

- [履歷表計算 Azure 入口網站][]
- [使用 PowerShell 履歷表運算][]
- [使用 REST Api 履歷表運算][]

## <a name="permissions"></a>權限

縮放比例資料庫會要求[變更資料庫][]中所述的權限。  暫停和履歷表會需要[SQL DB 參與者][]的權限，特別是 Microsoft.Sql/servers/databases/action。

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>後續步驟
請參閱下列文章，協助您瞭解一些額外的關鍵效能的概念︰

- [工作量及並行管理][]
- [表格設計概觀][]
- [表格通訊群組][]
- [編製索引的資料表][]
- [分割表格][]
- [表格統計資料][]
- [最佳作法][]

<!--Image reference-->

<!--Article references-->
[資料倉庫單位 (DWUs)]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units

[小數位數計算 power Azure 入口網站]: ./sql-data-warehouse-manage-compute-portal.md#scale-compute-bk
[小數位數計算 power 使用 PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#scale-compute-bk
[使用 REST Api 的小數位數計算 power]: ./sql-data-warehouse-manage-compute-rest-api.md#scale-compute-bk
[小數位數計算 TSQL 電源]: ./sql-data-warehouse-manage-compute-tsql.md#scale-compute-bk

[capacity limits]: ./sql-data-warehouse-service-capacity-limits.md

[暫停計算 Azure 入口網站]:  ./sql-data-warehouse-manage-compute-portal.md#pause-compute-bk
[使用 PowerShell 暫停運算]: ./sql-data-warehouse-manage-compute-powershell.md#pause-compute-bk
[使用 REST Api 暫停運算]: ./sql-data-warehouse-manage-compute-rest-api.md#pause-compute-bk

[履歷表計算 Azure 入口網站]:  ./sql-data-warehouse-manage-compute-portal.md#resume-compute-bk
[使用 PowerShell 履歷表運算]: ./sql-data-warehouse-manage-compute-powershell.md#resume-compute-bk
[使用 REST Api 履歷表運算]: ./sql-data-warehouse-manage-compute-rest-api.md#resume-compute-bk

[工作量及並行管理]: ./sql-data-warehouse-develop-concurrency.md
[表格設計概觀]: ./sql-data-warehouse-tables-overview.md
[表格通訊群組]: ./sql-data-warehouse-tables-distribute.md
[編製索引的資料表]: ./sql-data-warehouse-tables-index.md
[分割表格]: ./sql-data-warehouse-tables-partition.md
[表格統計資料]: ./sql-data-warehouse-tables-statistics.md
[最佳作法]: ./sql-data-warehouse-best-practices.md 
[development overview]: ./sql-data-warehouse-overview-develop.md

[SQL DB 參與者]: ../active-directory/role-based-access-built-in-roles.md#sql-db-contributor

<!--MSDN references-->
[變更資料庫]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[Azure portal]: http://portal.azure.com/
