<properties 
    pageTitle="Azure 搜尋效能與最佳化考量 |Microsoft Azure" 
    description="調整 Azure 搜尋效能與設定最佳的小數位數" 
    services="search" 
    documentationCenter="" 
    authors="LiamCavanagh" 
    manager="pablocas" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="10/17/2016" 
    ms.author="liamca"/>

# <a name="azure-search-performance-and-optimization-considerations"></a>Azure 搜尋效能與最佳化考量

好的搜尋體驗是成功的許多行動電話及 web 應用程式鍵。 從房地產、 使用線上資料目錄，汽車市場快速搜尋與相關的結果會影響的客戶體驗。 這份文件旨在說明您探索如何充分利用 Azure 搜尋，尤其是複雜需求、 延展性進階案例多語言的最佳作法支援或自訂的排名。  此外，這份文件概述內部，並說明中實際的客戶應用程式的方法。

## <a name="performance-and-scale-tuning-for-search-services"></a>效能及縮放比例調整搜尋服務

所有用於搜尋引擎，例如 Bing 和 Google 及高效他們提供我們。  如此一來，客戶使用搜尋功能 web 或行動應用程式，將預期類似的效能特性。  將搜尋效能最佳化、 時的最佳方法的其中一個是著重於延遲，已完成，並傳回結果的查詢所花的時間。  最佳化搜尋延遲時很重要︰

1. 一般搜尋要求目標延遲 （或最大時間量） 需完成的選擇。

2. 建立並測試針對測量這些延遲工資率實際資料集的搜尋服務的實際工作量。

3. 開始低數秒 (QPS) 的查詢，然後繼續增加測試中執行，直到的查詢延遲低於定義的目標延遲數字。  這是可協助您規劃的小數位數，當您的應用程式規模擴大時，在 [使用狀況重要事先。

4. 可能的話，重複使用 HTTP 連線。  如果您使用的 Azure 搜尋.NET SDK，這表示您應該重複使用的執行個體或[SearchIndexClient](https://msdn.microsoft.com/library/azure/microsoft.azure.search.searchindexclient.aspx)的執行個體，然後如果您使用的 REST API，您應該重複使用單一 HttpClient。
 
建立這些時測試負載，有的 Azure 搜尋要記住的一些特性︰

1. 就可以推入讓多個搜尋一次查詢中 Azure 搜尋服務可用的資源將會因為而不知所措。  發生這種情況下，您會看到 HTTP 503 回應代碼。  因此，最好開始各種範圍搜尋要求您新增更多搜尋要求時，請參閱延遲率的差異。

2. 上傳的 Azure 搜尋的內容會影響整體的效能和 Azure 搜尋服務的延遲。  如果您希望使用者正在執行搜尋時傳送資料，請務必測試中採取此至帳戶的工作量。

3. 並非每個搜尋查詢會執行相同的效能層級。  例如，文件的 [查閱] 或 [搜尋建議通常會執行速度比有大量的 facet 和篩選的查詢。  最好才會想要建立您的測試時，請參閱將帳戶的各種查詢。  

4. 無法與搜尋要求的變化相當重要，因為如果持續執行相同的搜尋要求時，開始的資料快取，讓查詢優於它可能使用更多不同查詢設定的效能。

> [AZURE.NOTE] [Visual Studio 載入測試](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release)是很適合分派工作的方式執行您基準測試，因為其可讓您執行 HTTP 要求，您必須執行 Azure 搜尋查詢時，並讓平行的要求。

## <a name="scaling-azure-search-for-high-query-rates-and-throttled-requests"></a>縮放比例高查詢率 Azure 搜尋和經流速控制要求

當您收到太多流速控制的要求，或超過從提高的查詢載入您目標延遲率時，您可以查詢減少延遲費率在兩種方法之一︰

1. **增加複本︰** 複本，就像對多份複本平衡要求載入 Azure 搜尋可讓您資料的複本。  所有負載平衡和 Azure 搜尋管理複寫複本間的資料，您可以變更在任何時間分派服務的複本數目。  您最多可以配置進位到 12 標準搜尋服務的複本和 3 的複本中的基本搜尋服務。  從[Azure 入口網站](search-create-service-portal.md)，或使用[Azure 搜尋管理 API](search-get-started-management-api.md)，可以調整複本。

2. **增加搜尋層︰** Azure 搜尋隨附[的階層數](https://azure.microsoft.com/pricing/details/search/)，每個這些層提供不同層級的效能。  在某些情況下，您可能必須有這麼多大複本時，即使您是在層無法提供充分低延遲率的查詢。  在此情況下，您可能要考慮使用其中一個較高的搜尋層級，例如是相當適合用於有大量文件和極高查詢負載案例 Azure 搜尋 S3 層。

## <a name="scaling-azure-search-for-slow-individual-queries"></a>縮放比例 Azure 搜尋變得很慢個別的查詢

延遲費率可變得很慢的另一個原因是從單一查詢時間太長，無法完成。  在此案例中，新增複本時，不會改善延遲費率。  在這個問題有兩個選項可用︰

1. **增加磁碟分割區**分割是額外的資源分割資料機制。  因此，當您新增第二個的磁碟分割，資料會分割成兩個。  第三個磁碟分割會將您的索引分割成三等。 這也有在某些情況下，變得很慢查詢會執行速度更快狀況的計算平行的效果。  有的何處我們瞭解此平行有低選擇性查詢的查詢非常好所使用的一些範例。  這包含符合多個文件，或透過大量文件，提供計數 faceting 需要時查詢。  由於有許多分數之文件的相關性或計算的文件的數字所需的計算，請新增額外的磁碟分割區可協助提供其他計算。  

   可以在標準搜尋服務 12 磁碟分割區的最大值，1 的磁碟分割中的基本搜尋服務。  從[Azure 入口網站](search-create-service-portal.md)，或使用[Azure 搜尋管理 API](search-get-started-management-api.md)，可以調整分割。

2. **限制高基數欄位︰**高基數欄位包含 facetable 或可篩選的欄位，有大量的唯一值，如此一來，需要大量資源來計算結果在文件。   例如，產品識別碼或描述將欄位設定了 facetable/會使高基數的因為大部分的文件中的值至文件是唯一。 可能的話，限制高基數欄位的數目。

3. **增加搜尋層︰** 移動最較高的 Azure 搜尋層可以是以提升效能變得很慢查詢的另一種方法。  每一個較高的層級也會提供更快的 CPU 和更多記憶體這種方法可以對查詢效能正產生的影響。

## <a name="scaling-for-availability"></a>可用的比例

複本不僅減少查詢延遲，也可以允許的可用性。  與單一複本，您應該會定期停機時間，因為伺服器重新開機，軟體更新後，或將發生的其他進行的維修作業事件。  如此一來，請務必考慮如果應用程式所需的搜尋 （查詢） 可用性以及寫入 （編製索引的事件）。  Azure 搜尋提供在具下列屬性的付費的搜尋方案的 SLA 選項︰

- 2 複本的唯讀負載 （查詢）。
- 高可用性讀寫工作負載 （查詢和編製索引） 的 3 個或多個複本

如需在此的詳細資訊，請造訪[Azure 搜尋服務等級協定](https://azure.microsoft.com/support/legal/sla/search/v1_0/)。

由於複本複製的資料，有多個複本可讓 Azure 搜尋執行電腦重新啟動和維護針對一個複本時讓查詢，以繼續對其他複本執行一次。  因此，您也必須考慮如何這個停機時間可能會影響現在即可對資料小於複本執行查詢。

## <a name="scaling-geo-distributed-workloads-and-provide-geo-redundancy"></a>縮放比例地理分散式負載和提供地理重複

地理分散工作負載的您會發現位於不裝載您 Azure 搜尋服務的資料中心的使用者會有較高的延遲費率。  因此，通常很重要深入瞭解靠近這些使用者的區域中有多個搜尋服務。  Azure 搜尋不目前提供自動化的方法地理複寫 Azure 搜尋索引的各區域，但可以讓此程序更簡單實作與管理可供某些技巧。 這些說明的章節。

地理分佈的一組搜尋服務的目標是在使用者提供的最低的延遲，此範例中所見 Azure 搜尋服務的傳送位置的兩個或多個區域內有可用的兩個或多個索引︰

   ![跨-] 索引標籤的服務，依地區][1]

### <a name="keeping-data-in-sync-across-multiple-azure-search-services"></a>在同步處理資料儲存跨多個 Azure 搜尋服務

有兩個選項可保留分散式的搜尋服務同步其中使用[Azure 搜尋索引子](search-indexer-overview.md)或推播 API （也稱為[Azure 搜尋 REST API](https://msdn.microsoft.com/library/dn798935.aspx)）。  

### <a name="azure-search-indexers"></a>Azure 搜尋索引子

如果您使用的 Azure 搜尋索引，您從中央的資料存放區，例如 Azure SQL 資料庫或 DocumentDB 的方式來已匯入資料的變更。 當您建立新的搜尋服務，您只要也建立新的 Azure 搜尋索引器，指向 [此相同的資料存放區的服務。 如此一來，當新的變更出現在資料存放區，他們會然後能編製索引的各種索引子。  

以下是範例該架構的外觀。

   ![分散的重新與服務的組合的單一資料來源][2]


### <a name="push-api"></a>推入 API 
如果您使用的 Azure 搜尋推播 API 更新[您的 Azure 搜尋索引中的內容](https://msdn.microsoft.com/library/dn798930.aspx)，您可以將保持各種搜尋服務同步每當有必要的更新，並變更推入所有搜尋服務。  執行此動作時請務必處理其中一個搜尋服務的更新失敗，而一或多個的更新失敗的狀況。

## <a name="leveraging-azure-traffic-manager"></a>運用 Azure 流量管理員

[Azure 流量管理員](../traffic-manager/traffic-manager-overview.md)可讓您傳送邀請，然後備份多個 Azure 搜尋服務的多個地理位置的網站。  流量管理員的優點之一是它可以探查 Azure 搜尋，以確保可，並將使用者傳送到停機時間時的替代搜尋服務。  此外，如果您路由搜尋要求透過 Azure 網站，Azure 流量管理員可讓您負載平衡的情況下，網站上，但不是 Azure 搜尋。  以下是哪些架構運用流量管理員的範例。

   ![交叉索引標籤的服務的區域，請使用中央流量管理員][3]

## <a name="monitoring-performance"></a>監控效能

Azure 搜尋可讓您分析，並監控您的服務，透過[搜尋流量分析 (STA)](search-traffic-analytics.md)的效能。 透過 STA，您可以選擇性地登個別的搜尋作業，以及彙總的指標然後可以處理分析或視覺化 Power BI 中的 Azure 儲存體帳戶。  您可以使用 STA 指標，請檢閱效能統計資料，例如 [查詢] 或 [查詢回應時間的平均數目。  此外，作業記錄可讓您深入瞭解特定的搜尋作業的詳細資料。

STA 是寶貴的工具，若要瞭解延遲工資率，該 Azure 搜尋觀點。  由於登入的查詢效能指標會根據查詢 （從要求時送出的時間） 完全處理 Azure 搜尋中所花的時間，您就可以使用此選項決定延遲問題是否從 Azure 搜尋服務邊或服務，例如從網路延遲。  

## <a name="next-steps"></a>後續步驟

若要進一步瞭解價格的各層和服務限制的每一個，請參閱[Azure 搜尋服務限制](search-limits-quotas-capacity.md)。

請造訪[容量計劃](search-capacity-planning.md)更深入的磁碟分割與複本的組合。

更多向下切入的效能，並查看如何實作最佳化本文所述的一些示範，觀看以下影片︰

> [AZURE.VIDEO azurecon-2015-azure-search-best-practices-for-web-and-mobile-applications]

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png