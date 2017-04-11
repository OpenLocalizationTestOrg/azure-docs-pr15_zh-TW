<properties
   pageTitle="什麼是 Azure SQL Data Warehouse？ |Microsoft Azure"
   description="企業級分散式能夠處理 petabyte 大量關聯式及非關聯式資料的資料庫。 產業的第一個雲端資料倉庫與放大、 縮小，並將游標暫停於秒數。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/27/2016"
   ms.author="lodipalm;barbkess;mausher;jrj;sonyama;kevin"/>


# <a name="what-is-azure-sql-data-warehouse"></a>什麼是 Azure SQL Data Warehouse？

Azure SQL Data Warehouse 是雲端、 擴充能夠處理龐大的大量資料內容，關聯式及非關聯式資料庫。 內建在我們的人連線平行處理 (MPP) 架構，SQL Data Warehouse 可以處理您企業的工作量。

SQL Data Warehouse:

- 將 SQL Server 關聯式資料庫 Azure 雲端擴充功能。 您可以增加、 減少、 暫停或繼續計算秒數。 您儲存在需要時擴展 CPU 和剪下回使用峰時段成本。
- 運用 Azure 平台。 它會很容易部署、 完美來維護，並完全錯誤容錯由於自動備份。
- 補充 SQL Server 生態。 您可以開發熟悉 SQL Server TRANSACT-SQL (T SQL) 和工具。

本文將說明 SQL Data Warehouse 重要功能。

## <a name="massively-parallel-processing-architecture"></a>可大規模平行處理架構

SQL Data Warehouse 是人連線平行處理 (MPP) 分散式資料庫系統。 將資料並處理跨多個節點的功能，SQL Data Warehouse 可以提供這些延展性-以外的任何單一系統。  在幕後 SQL Data Warehouse 散播跨多個無共用儲存及處理單位的資料。 資料會儲存進階版本機多餘的儲存空間，並連結至運算節點的查詢執行。 此結構，與 SQL Data Warehouse 所需的 「 除以和征服 」 的方法執行載入及複雜的查詢。 要求會收到的控制項節點、 最佳化，然後傳遞給他們處理平行運算節點。

藉由組合 MPP 架構和 Azure 儲存功能，SQL Data Warehouse 可以︰

- 放大或縮小獨立的計算的儲存空間。
- 放大或縮小計算，而不移動資料。
- 暫停計算容量，並讓資料保持不變。
- 履歷表計算的通知，表示在的容量。

下圖顯示架構的更多詳細資料。

![SQL 資料倉庫架構][1]


**控制項節點︰**控制節點管理及最佳化查詢。 是的前端所有應用程式和連線進行互動。 在 SQL Data Warehouse，控制節點由 SQL 資料庫]，並連線到其看起來有相同。 下面，控制節點座標的所有資料移動和分散式資料執行平行查詢所需的計算。 當您提交 SQL Data Warehouse T SQL 查詢時，[控制] 節點會將它轉換成個別平行各個運算節點執行的查詢。

**運算節點︰**運算節點做為前 SQL Data Warehouse power。 他們的年齡的儲存您的資料和處理您的查詢的 SQL 資料庫。 當您新增資料時，SQL Data Warehouse 分散運算節點您要的資料列。 運算節點是您的資料執行平行查詢同事。 處理之後，他們會將結果回控制項節點。 若要完成查詢，控制節點彙總的結果，並傳回最終結果。

**儲存空間︰**您的資料會儲存於 Azure Blob 儲存體。 何時計算您的資料，與他們撰寫及閱讀直接與 blob 儲存體互動的節點。 由於無障礙和大幅，則會展開 Azure 儲存空間，可以執行 SQL Data Warehouse 相同。 由於獨立計算，儲存空間，SQL Data Warehouse 可以自動調整計算，反之亦然縮放比例分開的儲存空間。 Azure Blob 儲存體也完全容錯，並會簡化回傳備份與還原程序。

**資料移動服務︰**資料移動服務 (DMS) 會節點之間移動資料。 DMS 可讓計算節點存取所需連接及彙總的資料。 DMS 不是 Azure 服務。 是 SQL 資料庫同時執行所有節點的 Windows 服務。 因為 DMS 是在幕後執行，您就不會直接使用了。 不過，當您檢視查詢計劃，您會發現這些工作包括某些 DMS 作業，因為移動資料才能平行執行的查詢。


## <a name="optimized-for-data-warehouse-workloads"></a>針對資料倉庫負載最佳化

MPP 方法被輔助數個資料倉儲特定效能最佳化，包括︰

- 一個分散式的最佳化和一組複雜的統計資料，在所有資料。 使用資料的 [大小及通訊群組的資訊，服務就可以最佳化查詢來評估特定分散式的查詢作業成本。

- 進階的演算法和技術整合的資料移動程序有效率地移動之間運算資源所需執行查詢的資料。 這些資料移動作業建置，，而且所有最佳化資料移動服務都會自動執行。

- 依預設直**columnstore**索引。 藉由使用欄為基礎的儲存空間，SQL Data Warehouse 取得平均 5 x 壓縮獲得透過傳統列導向儲存空間，以及最 x 10 個或更多的查詢效能提升。 瀏覽大量的資料列工作上 columnstore 索引所需要的分析查詢。


## <a name="predictable-and-scalable-performance"></a>如預期呈現且可調整效能

SQL Data Warehouse 分隔儲存和計算，每一個調整大小。 SQL Data Warehouse 可以快速並輕鬆不按比例縮放的通知，新增其他計算資源。 互補這是使用 Azure Blob 儲存體。 二進位大型物件的即可輕鬆擴充較低的成本，提供穩定、 複製的儲存空間，不僅基礎結構。 使用此組合的間隔雲端儲存和 Azure 計算，SQL Data Warehouse 可讓您在需要時，支付查詢效能和儲存空間。 變更計算的量，只要在 Azure 入口網站中移動滑桿向左或向右或也排程使用 T SQL 與 PowerShell。

完全控制的計算亦儲存空間量的功能，以及 SQL Data Warehouse 可讓您完全暫停您資料倉庫，這表示您不是每年支付計算當您不需要它。 同時將您的儲存空間保留在原位，所有的計算被發佈到 Azure，儲存您金錢的主要集區。 需要時，只要繼續計算資料，並計算適用於您的工作量。

## <a name="data-warehouse-units"></a>資料倉庫單位

資源分派的資源，您的 SQL Data Warehouse 被以資料倉庫單位 (DWUs)。 DWUs 為基礎的資源，例如 CPU、 記憶體、 IOPS，配置給您的 SQL Data Warehouse 的量值。 增加 DWUs 數目會增加資源和效能。 具體來說，DWUs 協助確認︰

- 您就可以輕鬆地縮放您資料倉庫，而不需擔心基礎的硬體或軟體。

- 在您變更您的資料倉庫大小之前，您可以預測 DWU 層級的效能提升。

- 基礎硬體與軟體的執行個體可以變更或移動而不會影響您的工作負載的效能。

- Microsoft 進行調整基礎架構的服務而不會影響效能的工作量。

- Microsoft 可以快速改善效能 SQL Data Warehouse，可以調整及平均效果系統的方式。

資料倉庫單位提供高度相互關聯與資料倉儲工作負載的效能的三個精確計量量的值。 目標是下列重要的工作量指標會與您選擇為您的資料倉庫 DWUs 直線縮放。

**掃描/彙總︰**此工作負載計量所需的時間標準資料倉儲掃描大量的資料列，然後執行複雜的彙總的查詢。 這是 I/O CPU 大量與。

**載入︰**此公制測量能力內嵌到服務的資料。 從 Azure Blob 儲存體載入代表資料集的 PolyBase 與完成載入。 此計量是設計用來壓力網路和服務的 CPU 層面。

**選取 (CTAS) 以建立資料表︰**CTAS 測量複製表格的功能。 包括讀取中儲存的資料，將它分散到的應用裝置節點以及寫入到存放一次。 它會使用大量 CPU、 IO 和網路。

## <a name="pause-and-scale-on-demand"></a>暫停並視需要調整大小

當您需要更快獲得的結果時，增加您 DWUs 並支付更大的效能。 當您需要較少計算 power、 減少您 DWUs 並支付僅適用於您的需要。 您可能會想想變更您在這些情況下 DWUs:

- 當您不需要執行查詢，可能是晚上或週末、 靜止查詢。 然後按一下 [暫停您計算資源，以避免當您不需要這些支付 DWUs。

- 如果您的系統低視需要，請考慮減少 DWU 小的大小。 您仍然可以存取資料，但在節省大量的成本。

- 執行大量的資料載入或轉換作業時，您可能會想要不按比例縮放，讓您的資料是更快速地使用。

若要瞭解您理想的 DWU 值是什麼、 嘗試縮放向上和向下，以及執行幾個查詢載入資料之後。 由於您是快速縮放比例，您可以嘗試不同層級的效能在一小時之內的數字。  執行請記住，SQL Data Warehouse 設計用的大量資料，並請參閱調整其 true 功能，尤其是在較大的縮放比例我們提供時，您會想要使用大型資料集接近或超過 1 TB。


## <a name="built-on-sql-server"></a>內建在 SQL Server

SQL Data Warehouse 為基礎的 SQL Server 關聯式資料庫引擎，並包含許多企業資料倉庫預期的功能。 如果您已經知道 T SQL，輕鬆傳輸 SQL Data Warehouse 知識。 您進階是否或才剛開始，請在文件的範例可協助開始。 整體來說，您可以考慮我們已建構的 SQL Data Warehouse 語言元素，如下所示的方式︰

- SQL Data Warehouse T SQL 語法使用許多作業。 也支援廣泛的傳統的 SQL 建構，例如預存程序、 使用者定義函數、 資料表分割、 索引和定。

- SQL Data Warehouse 也包含數字較新的 SQL Server 功能，包括︰ 群組直條圖**columnstore**索引、 PolyBase 整合，以及稽核 （完整的威脅評估） 的資料。

- 少的資料倉儲負載，或較新的 SQL server 的特定 T SQL 語言項目可能無法目前可用。 如需詳細資訊，請參閱[移轉文件][]。

使用 SQL Server、 SQL Data Warehouse、 SQL 資料庫與分析平台系統之間 TRANSACT-SQL 和功能共同處，您可以開發解決方案適合您資料的需求。 您可以決定要保留您的資料，根據效能、 安全性和縮放比例需求的位置，並轉接不同系統之間所需的資料。

## <a name="data-protection"></a>資料保護

SQL Data Warehouse 儲存 Azure 進階版本機多餘的儲存空間的所有資料。 同步的多份複本資料會保留本機資料中心以確保透明資料保護若本地化失敗。 此外，SQL Data Warehouse 會自動備份作用中 （取消暫停） 資料庫定期使用 Azure 儲存空間的快照。 若要瞭解更多關於如何備份及還原運作，請參閱[備份與還原的概觀][]。

## <a name="integrated-with-microsoft-tools"></a>與 Microsoft 工具整合

SQL Data Warehouse 也整合許多工具使用者可能很熟悉的 SQL Server。 這些功能包括︰

**傳統的 SQL Server 工具︰**SQL Data Warehouse 已經與 SQL Server Analysis Services 整合服務與 Reporting Services 完全整合。

**雲端為基礎的工具︰**SQL Data Warehouse 可同時 Azure，包括資料工廠、 串流分析、 電腦學習和 Power BI 中的新工具的數字。 如需更完整的清單，請參閱[整合的工具概觀][]。

**協力廠商工具︰**大量協力廠商工具提供者有認證的 SQL Data Warehouse 其工具的整合。 如需完整的清單，請參閱[SQL Data Warehouse 解決方案合作夥伴][]。

## <a name="hybrid-data-sources-scenarios"></a>混合式資料來源的案例

使用 PolyBase SQL Data Warehouse 可讓使用者前所未有移動其生態，解除鎖定的功能設定進階的混合式案例與非關聯式資料和內部部署資料來源。

Polybase 可讓您運用您來自不同來源的資料使用熟悉的 T SQL 命令。 Polybase 可讓您以查詢非關聯式資料保留在 Azure Blob 儲存體，如同一般表格。 使用 Polybase 非關聯式資料查詢，或非關聯式資料匯入 SQL Data Warehouse。

- PolyBase 會使用外部表格來存取非關聯式資料。 表格定義會儲存在 SQL Data Warehouse，您可以使用 SQL 存取和工具，例如您想要存取標準的關聯式資料。

- Polybase 是在其整合。 它公開相同的功能支援的所有來源的功能。 閱讀 Polybase 資料可以在各種不同的格式，包括分隔符號的檔案或 ORC 檔案中。

- PolyBase 可用來存取也要用來當做儲存 HDInsight 叢集的 blob 儲存體。 這可讓您存取關聯式及非關聯式工具使用相同的資料。

## <a name="sla"></a>SLA

SQL Data Warehouse 提供產品層級服務等級協定 (SLA) 做為 Microsoft Online Services SLA 的一部分。 如需詳細資訊，請造訪[SLA SQL Data Warehouse][]。 其他所有產品的 SLA 有關您可以造訪的[服務等級協定]Azure 頁面或下載[大量授權][]] 頁面上。 

## <a name="next-steps"></a>後續步驟

現在，您知道有點 SQL Data Warehouse，瞭解如何快速[建立 SQL Data Warehouse][]並[載入範例資料][]。 如果您是新 Azure，您可能會發現[Azure 詞彙][]很有幫助當您遇到新的術語。 或者，您也可以查看下列其他 SQL 資料倉庫資源。  

- [客戶成功的故事]
- [部落格]
- [功能要求]
- [視訊]
- [客戶會談小組部落格]
- [建立支援票證]
- [MSDN 論壇]
- [堆疊溢位論壇]
- [Twitter]


<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[建立支援票證]: ./sql-data-warehouse-get-started-create-support-ticket.md
[載入範例資料]: ./sql-data-warehouse-load-sample-databases.md
[建立 SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[移轉文件]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse 解決方案合作夥伴]: ./sql-data-warehouse-partner-business-intelligence.md
[整合式的工具概觀]: ./sql-data-warehouse-overview-integrate.md
[備份與還原概觀]: ./sql-data-warehouse-restore-database-overview.md
[Azure 詞彙]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[客戶成功的故事]: https://customers.microsoft.com/search?sq=&ff=story_products_services%26%3EAzure%2FAzure%2FAzure%20SQL%20Data%20Warehouse%26%26story_product_families%26%3EAzure%2FAzure%26%26story_product_categories%26%3EAzure&p=0
[部落格]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[客戶會談小組部落格]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[功能要求]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN 論壇]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[堆疊溢位論壇]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[視訊]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SQL 資料倉庫 SLA]: https://azure.microsoft.com/en-us/support/legal/sla/sql-data-warehouse/v1_0/
[大量授權]: http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[服務等級協定]: https://azure.microsoft.com/en-us/support/legal/sla/
