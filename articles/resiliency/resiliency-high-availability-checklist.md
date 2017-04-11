<properties
   pageTitle="可用性檢查清單 |Microsoft Azure"
   description="設定，以確保您，您可以採取的動作的快速檢查改善 Azure 與您的應用程式的可用性。"
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

#<a name="high-availability-checklist"></a>可用性檢查清單
使用 Azure 很棒的優點是增加雲端協助您應用程式的顯示狀態 （與延展性） 的功能。 若要確保您會充分利用這些選項，下列檢查清單是可協助您處理基礎結構的基本概念部分，以確保您的應用程式有彈性。 

>[AZURE.NOTE] 大部分的建議下列項目可以隨時在您的應用程式中執行，因此很適合 」 快速修正 」。 長期獲得最佳解決方案通常包含雲端內建應用程式的設計。  如需這些 （多個方向的設計區域，請詳讀我們的[可用性檢查清單](../best-practices-availability-checklist.md)。

###<a name="are-you-using-traffic-manager-in-front-of-your-resources"></a>您目前使用流量管理員前面資源？
使用流量管理員可協助您網際網路將流量路由傳送過 Azure 區域或 Azure 及內部部署的位置。 您可以將多個原因包括延遲和可用性。 如果您想要瞭解如何使用流量管理員來增加您恢復和分散到多個區域將流量的詳細資訊，請閱讀[在高可用性 Azure 上的多個資料中心的執行 Vm](../guidance/guidance-compute-multiple-datacenters.md)。

__如果您不是使用流量管理員，會發生什麼情況？__ 如果您沒有使用流量管理員，您的應用程式前，您受限於單一區域的資源。 這會限制您的比例增加延遲不接近您所選的地區的使用者，與您在全地區的服務中斷的情況下的保護。

###<a name="have-you-avoided-using-a-single-virtual-machine-for-any-role"></a>您有避免使用單一虛擬機器任何角色？
設計可避免任何單一的失敗。 這是很重要的所有服務設計 (內部部署或雲端中)，但您可以增加延展性和恢復透過擴展 （加入虛擬機器），而不是按比例縮放 （使用功能更強大的虛擬機器） 是在雲端尤其有用。 如果您想要找出可調整應用程式設計的詳細資訊，請閱讀[Microsoft Azure 應用程式的可用性](resiliency-high-availability-azure-applications.md)。

__如果您有單一虛擬機器角色的會發生什麼情況？__ 單一電腦單一的失敗，而不適用於[Azure 虛擬機器服務等級協定](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/)。 在最佳的情況下，應該執行應用程式，但這不具彈性的設計，不涵蓋 Azure 虛擬機器 SLA 及任何單一的失敗增加停機時間如果內容的機會失敗。

###<a name="are-you-using-a-load-balancer-in-front-of-your-applications-internet-facing-vms"></a>您使用的應用程式的具網際網路 Vm 前面負載平衡器？
負載平衡器可讓您應用程式的內送流量分散到任意數目的電腦。 您可以新增/移除電腦從您的負載平衡器在任何時間，以及搭配虛擬機器 （與也自動調整大小的虛擬機器縮放比例設定） 的運作，讓您輕鬆地處理增加流量或 VM 失敗。 如果您想要深入瞭解負載平衡器，請閱讀[Azure 負載平衡器概觀](../load-balancer/load-balancer-overview.md)及[執行多個 Vm 延展性及可用性 Azure 上](../guidance/guidance-compute-multi-vm.md)。

__如果您不使用您的網際網路的 Vm 前面負載平衡器，會發生什麼情況？__ 不負載平衡器，您將無法擴充 （新增更多的虛擬機器），則只能進行縮放 （增加您的網站具虛擬機器大小）。 您也會面臨的虛擬機器失敗單一點。 您也必須撰寫 DNS 程式碼，請注意，是否您遺失網際網路的電腦，並重新對應至新的電腦在您開始進行其 DNS 項目。

###<a name="are-you-using-availability-sets-for-your-stateless-application-and-web-servers"></a>是您使用可用性無應用程式與網路伺服器設定？
將您的電腦放入相同的應用程式層，在可用性設定可讓您 Vm Azure VM SLA 符合資格。 一部分可用性設定也可確保您的電腦都會放不同的更新的網域 （也就是不同的主機機器修補在不同的時間） 及網域 （也就是共用的常見電源和網路主機機器切換）。 不會在可用性設定，您 Vm 可能會位於相同的主機機器，因此有可能是單一的並不會看到您的失敗。 如果您想要瞭解有關提高使用可用性集您 Vm 的可用性的詳細資訊，請閱讀[管理虛擬機器中的可用性](../virtual-machines/virtual-machines-windows-manage-availability.md)。

__如果您不是使用可用性無狀態應用程式與網頁伺服器設定，會發生什麼情況？__ 不使用可用性設定所代表的意義您無法以利用 Azure VM SLA。 這也表示，在該應用程式層電腦可能所有離線主機機器 （裝載您使用的的 Vm 的電腦，） 或常見的硬體失敗的更新時。

###<a name="are-you-using-virtual-machine-scale-sets-vmss-for-your-stateless-application-or-web-servers"></a>您使用您沒有狀態的應用程式或網頁伺服器的虛擬機器縮放比例設定 (VMSS)？
良好的調整，同時設計使用 VMSS，請確定，您可以放大/縮小的電腦在您的應用程式層數 （例如 web 層）。 VMSS 可讓您定義應用程式層縮放比例 （新增或移除根據您所選的準則的伺服器）。 如果您想要瞭解如何使用 Azure 虛擬機器縮放比例設定增加尖峰流量至您恢復功能的詳細資訊，請閱讀[虛擬機器縮放比例設定概觀](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)。

__如果您沒有我們虛擬機器我沒有狀態的應用程式的網頁伺服器的縮放設定，會發生什麼情況？__ 不 VMSS，您可以限制不按比例縮放沒有限制及最佳化您使用的資源的能力。 缺少 VMSS 設計有縮放比例的上限，都必須與其他程式碼 （或以手動方式） 來處理。 VMSS 缺乏也表示可以不輕鬆新增和移除 （不論小數位數） 的電腦，可協助您處理大型尖峰流量的應用程式 (例如在升級期間，或如果您的網站/應用程式/產品進入病毒)。

###<a name="are-you-using-premium-storage-and-separate-storage-accounts-for-each-of-your-virtual-machines"></a>您使用的進階版儲存與個別儲存帳戶的每個虛擬機器嗎？
最好用於生產虛擬機器中的進階版儲存空間。 此外，請確定您使用不同的儲存帳戶，每個虛擬機器 （此適用於小型部署。 較大的部署，您可以重複使用的儲存空間帳戶，但有多部電腦的是需要完成以確保您對稱更新網域與您的應用程式層平衡）。 如果您想要找出 Azure 儲存體效能與延展性詳細資訊，請閱讀[Microsoft Azure 儲存體效能與延展性檢查清單](../storage/storage-performance-checklist.md)。

__如果您不是個別的儲存空間帳戶使用每個虛擬機器，會發生什麼情況？__ 儲存帳戶，例如許多其他資源是失敗的單一。 雖然有許多保護和 Azure 儲存體恢復功能，但單一絕不會是失敗的很好的設計。 比方說，如果帳戶叫用的儲存空間限制，或[IOPS 限制](../azure-subscription-service-limits.md#virtual-machine-disk-limits)達到，以取得損毀的存取權限，會影響所有使用該儲存帳戶的虛擬機器。 此外，如果有會影響包含該特定的儲存空間帳戶儲存戳記服務中斷，您可以讓多個影響的虛擬機器。

###<a name="are-you-using-a-load-balancer-or-a-queue-between-each-tier-of-your-application"></a>您目前使用負載平衡器或應用程式的每一個層級之間佇列？
使用負載平衡器或應用程式的每一個層級之間的佇列可讓您輕鬆地縮放每個應用程式層，輕鬆地與大小。 您應該選擇根據您的延遲複雜度，這些技術之間，需要通訊群組 （也就如何廣泛散發時您的應用程式）。 一般而言，佇列通常會有較高的延遲和新增複雜，但優點正在更有彈性，讓您可以透過較大的區域散佈應用程式 (例如各區域)。 如果您想要瞭解如何使用內部負載平衡器或佇列中，請閱讀[內部負載平衡器 」 概觀](../load-balancer/load-balancer-internal-overview.md)和[Azure 佇列和服務匯流排佇列-相對於和比較](../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)的詳細資訊。

__如果您沒有使用的負載平衡器或應用程式的每一個層級之間的佇列中，會發生什麼情況？__ 沒有負載平衡器或佇列中，每個應用程式層之間很難向上或向下調整您的應用程式，並將其載入分散在多部電腦。 不執行此動作可能會造成循列列印] 或 [下佈建資源與風險停機時間，或不佳的使用者體驗，如果您有非預期的變更，流量或系統失敗。
 
###<a name="are-your-sql-databases-using-active-geo-replication"></a>SQL 資料庫使用的作用中的地理試驗嗎？ 
作用中的地理複寫可讓您在相同或不同，區域內設定最多 4 可讀的次要資料庫。 第二個資料庫，可在服務中斷或無法連線至主要資料庫。 如果您想要進一步瞭解 SQL 資料庫作用中的地理試驗，請閱讀[概觀︰ SQL 資料庫作用中地理複寫](../sql-database/sql-database-geo-replication-overview.md)。
 
 __如果您不是作用中的地理複寫使用和 SQL 資料庫，會發生什麼情況？__ 沒有作用中地理-複寫，如果您的主要資料庫時離線，直到您就可以將您主要的資料庫，以重新連線狀況良好 （預定進行的維護、 服務中斷、 硬體失敗等） 應用程式資料庫將會離線。 
 
###<a name="are-you-using-a-cache-azure-redis-cache-in-front-of-your-databases"></a>您目前使用的快取 （Azure Redis 快取） 前面資料庫？
如果您的應用程式有高資料庫載入大部分的資料庫呼叫哪裡讀取，可以增加您的應用程式的速度，並減少載入您的資料庫實作前面卸載這些讀取的作業資料庫快取圖層。 您可以增加您的應用程式的速度，並減少資料庫負載 （因此增加小數位數，可以處理） 放在您的資料庫前面快取圖層。 如果您想要進一步瞭解 Azure Redis 快取，請閱讀[快取指引](../best-practices-caching.md)。
 
 __如果您沒有使用快取資料庫前，會發生什麼情況？__ 如果您的資料庫電腦足以處理流量載入您放置在您的應用程式將回應為標準，然後雖然這表示，在較低的載入您將會支付價格比必要的資料庫電腦。 如果您資料庫的電腦沒有強大足夠處理負載，然後您就會開始體驗不佳的使用者體驗與應用程式 （延遲逾時，可能是服務停擺）。
 
###<a name="have-you-contacted-microsoft-azure-support-if-you-are-expecting-a-high-scale-event"></a>如果您想要在高的縮放比例事件有連線到 Microsoft Azure 支援嗎？
Azure 支援可協助您改善您的服務限制，處理計劃高流量事件 （例如新產品上市或特殊的假日）。 Azure 支援也能夠協助您聯繫專家誰可以協助您與您的帳戶小組檢閱您的設計，並協助您尋找符合您高的小數位數的事件需求的最佳解決方案。 如果您想要瞭解如何連絡 Azure 支援的詳細資訊，請參閱[Azure 支援的常見問題集](https://azure.microsoft.com/support/faq/)。

__如果您沒有連絡 Azure 支援高階事件，會發生什麼情況？__ 如果您不進行通訊，或規劃時，高流量事件，風險正中[Azure 服務限制](../azure-subscription-service-limits.md)特定，如此會建立不佳的使用者體驗 （或更嚴重，停機時間） 期間您的事件。 建築檢閱並可在通訊，可協助減輕這類風險。

###<a name="are-you-using-a-content-delivery-network-azure-cdn-in-front-of-your-web-facing-storage-blobs-and-static-assets"></a>使用內容傳遞網路 (Azure CDN) 前面您的網站具儲存 blob 和靜態資產？
使用 cdn 到底，可協助您需要關閉您的伺服器載入快取在 CDN POP/邊緣位置位於世界各地的內容。 您可以透過以下減少延遲、 增加延展性、 減少伺服器負載及保護從拒絕 service(DOS) 攻擊的策略的一部分。 如果您想要瞭解如何使用 Azure CDN 增加您恢復功能，並減少您的客戶延遲的詳細資訊，請閱讀[概觀的 Azure 內容傳遞網路 (CDN)](../cdn/cdn-overview.md)。

__如果您不是使用 cdn 到底，會發生什麼情況？__ 如果您不使用 cdn 到底所有您的客戶流量其直接前往您的資源。 這表示您會看到較高的載入您將會減少其延展性的伺服器。 此外，您可能會發生更高的延遲為 Cdn 提供可能的往客戶世界各地的位置。

##<a name="next-steps"></a>後續步驟︰
如果您想要閱讀更多有關如何設計您的應用程式的可用性，請閱讀[Microsoft Azure 應用程式的可用性](resiliency-high-availability-azure-applications.md)。
