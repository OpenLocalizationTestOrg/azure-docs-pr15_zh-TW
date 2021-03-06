<properties
   pageTitle="Azure 應用程式損毀修復 |Microsoft Azure"
   description="技術概觀與設計損毀修復，在 Microsoft Azure 上的應用程式的相關資訊。"
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

#<a name="disaster-recovery-for-applications-built-on-microsoft-azure"></a>應用程式內建在 Microsoft Azure 損毀修復

可用性是關於暫時失敗管理，而損毀修復 (DR) 是嚴重影響應用程式功能。 例如，請考慮區域會中斷的情況。 在此情況下，您需要有一個方案可執行應用程式或存取您的資料 Azure 區域以外的位置。 執行此方案涉及人員與處理程序，支援讓函數系統的應用程式。 商務與技術的擁有者定義系統的操作模式損毀的人員也會決定期間損毀的層級服務的功能。 層級的功能可能需要幾個表單︰ 完全無法使用的部分可用 （降低功能或延遲處理），或完全可用。

##<a name="azure-disaster-recovery-features"></a>Azure 損毀修復功能

如同可用性考量 Azure 有[恢復技術指南](./resiliency-technical-guidance.md)的設計來支援損毀修復。 之間 Azure 與損毀復原可用性功能也有關聯性。 例如，管理角色故障網域的增加應用程式的可用性。 不該管理，因為內含無法處理的硬體會變成 「 損毀 」 案例。 正確的應用程式的可用性功能策略因此損毀校訂很重要的部分應用程式。 不過，本文超出開放問題更有 （與少數） 損毀的事件。

##<a name="multiple-datacenter-regions"></a>多個資料中心區域

Azure 會保留在世界各地的多個區域內的資料中心。 此基礎結構支援幾個損毀修復狀況，例如系統提供地理複寫 Azure 儲存體的第二個區域。 也就是說，您可以輕鬆地與低成本部署雲端服務至世界各地的多個位置。 比較此成本與多個區域執行您自己的資料中心的問題。 部署到多個區域的資料和服務，可協助保護您的應用程式從一個區域中的主要中斷。

##<a name="azure-traffic-manager"></a>Azure 流量管理員

地區特定發生錯誤時，您必須流量重新導向至服務或另一個區域中的部署。 您可以執行路由以手動方式，但使用自動化程序更有效率。 Azure 流量管理員被專為這項工作。 您可以使用它來自動管理使用者傳輸至另一個區域的容錯移轉，以防的主要區域會失敗。 因為流量管理的整體策略很重要的一部分，務必瞭解基本概念的流量管理員。

在下圖中，使用者連線至實際的網站 Url （__http://app1URL.cloudapp.net__和__http://app2URL.cloudapp.net__） 的流量管理員 (__http://myATMURL.trafficmanager.net__)，該摘要已指定的 URL。 根據您設定的準則來傳送使用者時，使用者便會傳送到正確的實際網站時原則規定。 原則選項是循環、 效能或容錯移轉。 這是因為本文中，我們會考慮與容錯移轉選項。

![路由透過 Azure 流量管理員](./media/resiliency-disaster-recovery-azure-applications/routing-using-azure-traffic-manager.png)

當您正在設定流量管理員時，您會提供新的資料傳輸管理員 DNS 前置詞。 這是 URL 前置詞，您會提供給您的使用者存取您的服務。 流量管理員現在抽取負載平衡一層級設定，而非區域層級。 流量管理員 DNS 會對應到它所管理的所有部署 CNAME。

流量管理員] 中，您指定使用者將路由至發生錯誤時，部署的優先順序。 主要部署失敗時，流量管理員監視部署和備忘稿的結束點。 在失敗，流量管理員分析的部署的優先順序的清單，並路由至下一個清單上的使用者。

雖然流量管理員決定前往容錯移轉中，您可以決定您的容錯移轉網域是不活躍或作用中，而您不在容錯移轉模式。 該功能沒有使用 Azure 流量管理員。 流量管理員會失敗偵測主要的網站，並會變換容錯移轉網站。 無論是否台目前提供服務的使用者是否變換流量管理員。

如需有關 Azure 流量管理員的運作方式的詳細資訊，請參閱︰

 * [流量管理員的概觀](../traffic-manager/traffic-manager-overview.md)
 * [設定容錯移轉路由方法](../traffic-manager/traffic-manager-configure-failover-routing-method.md)

##<a name="azure-disaster-scenarios"></a>Azure 損毀狀況

下列各節討論損毀的案例的數種不同類型。 整個區域的服務中斷不只全應用程式失敗的原因。 中斷也可能會導致不佳的設計] 或 [管理錯誤。 請務必考慮錯誤的可能原因，將 [設計] 與 [復原計劃的測試階段期間。 好計劃利用 Azure 功能，並將其與特定應用程式的策略。 應用程式的重要性，取決於所選的回應復原點目標 (RPO) 及復原時間目標 (RTO)。

###<a name="application-failure"></a>應用程式失敗

Azure 流量管理員會自動處理所產生的基礎的硬體或作業系統軟體主機虛擬機器中的錯誤。 Azure 運作的伺服器上建立新的執行個體的角色，並將它加入負載平衡器旋轉。 如果大於角色執行個體數目，則 Azure 會移到其他執行角色執行個體處理時取代失敗的節點。

有發生這種情形的任何硬體或作業系統失敗亦嚴重應用程式錯誤。 應用程式可能會因嚴重錯誤邏輯或資料完整性問題所造成的例外狀況而失敗。 使監控系統可以偵測失敗的狀況，並通知應用程式的系統管理員，則必須在程式碼加入足夠遙測。 完整的知識的損毀修復程序的系統管理員可以決定要叫用容錯移轉程序。 或者，以系統管理員只要接受要徑錯誤的解決可用性中斷。

###<a name="data-corruption"></a>資料損毀

Azure 自動儲存您 Azure SQL 資料庫和 Azure 儲存體資料三次多餘內的相同的區域中的不同錯誤網域。 如果您使用地理複寫，資料會儲存三個額外的時間，於不同區域。 不過，如果您的使用者或您的應用程式損毀的主要的複本中的資料，資料快速複製到其他複本。 很抱歉，這會導致三份損毀的資料。

若要管理潛在資料損毀，您有兩個選項。 首先，您可以管理自訂備份策略。 您可以儲存您的備份 Azure 或內部部署，根據您的業務需求或管理規定。 另一個選項是 SQL 資料庫中復原使用新的時間點還原選項。 如需詳細資訊，請參閱一節的[資料損毀修復策略](#data-strategies-for-disaster-recovery)。

###<a name="network-outage"></a>網路中斷

無法存取 Azure 網路的組件時，您可能無法存取您的應用程式或資料。 如果一或多個角色執行個體是因為網路問題而無法使用，Azure 所用的應用程式的剩餘可用的執行個體。 如果您的應用程式無法存取其資料，因為 Azure 網路中斷，您可以可能會降低在模式中執行本機使用快取的資料。 您需要設計損毀的復原策略在應用程式中的降級模式下執行。 某些應用程式中，這可能不是實用。

將資料儲存在其他位置，直到恢復連線時，是另一個選項。 如果降級的模式不是其中一個選項，其餘的選項會是應用程式當機或容錯移轉至其他的區域。 降級模式中執行的應用程式的設計是以技術一個多商務決策。 探討進一步在[降低應用程式功能](#degraded-application-functionality)] 區段中。

###<a name="failure-of-a-dependent-service"></a>相依的服務失敗的

Azure 提供許多可能會遇到定期停機時間的服務。 作為範例，請考慮[Azure Redis 快取](https://azure.microsoft.com/services/cache/)。 此多租用戶服務，提供您的應用程式的快取功能。 請務必考慮相依服務無法使用時，在您的應用程式中會發生什麼情況。 在許多方面，這種情況下是類似的網路中斷案例。 不過，考慮每個服務獨立產生整體計劃的潛在改良功能。

Azure Redis 快取提供您從您的優點損毀復原的雲端服務部署內的應用程式快取。 首先，服務現在上執行，是您的部署中的本機的角色。 因此，您可以更有效地監控和管理快取的狀態為雲端服務的整體管理程序的一部分。 這種快取也會公開的新功能。 其中一項新功能是快取資料的可用性。 這可協助保留快取的資料，如果單一節點失敗維護其他節點的複本。

請注意，高可用性將會減少處理量增加由於次要副本更新於寫入的延遲。 也會加倍記憶體用於每個項目數量，因此規劃的。 此特定的範例，每個相依服務可能會有改善您的整體可用性和嚴重失敗的風險性低的功能。

每個相依服務時，您應該瞭解服務中斷的含意。 在快取的範例中，可能會直接從資料庫存取資料，直到您還原您的快取。 這就是龐大的效能降級的模式，但會提供相關資料的完整功能。

###<a name="region-wide-service-disruption"></a>整個區域的服務中斷

上一個失敗主要已可在相同的 Azure 區域內的失敗次數。 不過，您也必須準備是整個區域的服務中斷的可能性。 如果整個區域的服務中斷的情況發生，您的資料的本機重複複製無法使用。 如果您已啟用地理複寫，有三個額外的列印份數的二進位大型物件和不同區域中的資料表。 如果 Microsoft 宣告遺失的區域，Azure 重新對應所有地理複製區域的 DNS 項目。

>[AZURE.NOTE] 請注意，您不需要任何控制此程序，它會出現僅適用於整個區域的服務中斷。 因此，您必須依賴達成最高層級的可用性其他特定應用程式的備份策略。 如需詳細資訊，請參閱一節的[資料損毀修復策略](#data-strategies-for-disaster-recovery)。

###<a name="azure-wide-service-disruption"></a>Azure 整個服務中斷

規劃損毀，您必須考量可能損毀的整個範圍。 其中一個最嚴重服務中斷可能同時包含所有 Azure 區域。 與其他服務中斷時，您可能會決定您會在該事件採取暫時停機時間的風險。 橫跨區域的廣泛的服務中斷應該很少見比隔離的服務中斷涉及相關的服務或單一的區域。

不過，某些重要的應用程式，您可能決定必須這種情況的備份計劃。 此事件的計劃可能包括無法透過[替代雲端](#alternative-cloud)或[混合式內部部署與雲端方案](#hybrid-on-premises-and-cloud-solution)的服務。

###<a name="degraded-application-functionality"></a>降級應用程式的功能

設計完善的應用程式通常會使用彼此透過實作彈性的資訊交換圖樣的模組的集合。 DR 易於應用程式需要模組層級的工作區隔。 這是為了防止相依服務的中斷停機整個應用程式。 例如，公司 y 考慮 web 商務應用程式。下列模組可能會造成應用程式︰

 * __產品目錄__可讓使用者瀏覽產品。
 * __購物車__可讓使用者新增/移除其購物車中的產品。
 * __順序狀態__會顯示使用者訂單的傳送狀態。
 * __送出訂單__對做購物工作階段最後處理送出付款的順序。
 * __順序處理__驗證資料完整性的順序，並執行數量可用性檢查。

當此應用程式中模組的相依性進入時，會模組運作方式鍵，直到該部分復原？ 架構完整之系統就會執行的工作區隔在設計階段和在執行階段透過隔離邊界。 您可以將分類為可復原和無法復原每個失敗。 向下模組，請帶非可修復的錯誤，但您可以減輕可修復的錯誤，透過替代方案。 [高可用性] 區段所述，您可以隱藏使用者的一些問題處理錯誤，並採取其他動作。 更嚴重的服務中斷，在應用程式可能完全無法使用。 不過，第三個選項可繼續服務降級模式中的使用者。

例如，如果資料庫中的主機服務訂單當機，順序處理模組將會遺失處理銷售交易的能力。 根據架構，可能難以或無法送出訂單和順序處理組件，以繼續進行的應用程式。 如果應用程式不是處理這種情況下，可能會離線使用整個應用程式。

不過，在此相同的案例中，可能是產品資料儲存在不同的位置。 在此情況下，[產品類別目錄] 模組仍可檢視產品。 降級模式中，在應用程式會繼續可供使用者使用可用的功能，例如檢視產品類別目錄。 其他部分的應用程式，但無法使用，例如排序或庫存查詢。

降級模式的另一種變化中心效能，而不是功能。 例如，請考慮位置產品類別目錄正在快取透過 Azure Redis 快取的案例。 如果您無法使用快取，應用程式可能會直接移至要擷取產品類別目錄資訊伺服器儲存空間。 但此 access 可能會比快取版本慢。 因此，直到完全還原快取服務降低應用程式的效能。

決定應用程式會繼續降級模式中的函數則商務決策和技術的決策。 應用程式也必須決定如何通知暫時性問題的使用者。 在此範例中，檢視產品，甚至將其新增至購物車，可能會讓應用程式。 不過，當使用者嘗試購買產品，應用程式會通知使用者銷售模組會暫時無法存取。 不理想的客戶，但無法將整個應用程式服務中斷。

##<a name="data-strategies-for-disaster-recovery"></a>資料損毀修復的策略

正確地處理資料是正確的辛苦區域中任何復原計劃。 還原資料也是修復程序，通常會採用的組件最多時間。 降低模式中不同的選擇會導致失敗後的難以挑戰資料修復的錯誤和一致性。

因素之一是需要還原或維護應用程式的資料的複本。 您會使用這項資料進行參考和交易的次要網站。 內部部署設定需要實作多地區損毀復原策略成本和冗長規劃程序。 在此便利地，大部分的雲端提供者，包括 Azure 隨時允許到多個區域的應用程式部署。 在多個區域服務中斷應該是很少發生這種方式散布這些區域。 如何處理資料各地區是成功的任何復原計劃的參與因素。

下列各節討論相關資料備份、 參考資料和交易資料損毀復原技巧。

###<a name="backup-and-restore"></a>備份與還原

定期備份的應用程式的資料可支援某些損毀修復狀況。 不同的儲存空間資源需要不同的方法。

基本、 標準和進階版 SQL 資料庫層中，您可以利用時間點還原] 來復原您的資料庫。 如需詳細資訊，請參閱[概觀︰ 雲端與 SQL 資料庫的業務連續性和資料庫損毀修復](../sql-database/sql-database-business-continuity.md)。 另一個選項是作用中的地理複寫用於 SQL 資料庫。 這會自動在相同的 Azure 地區或甚至在不同的 Azure 區域中的次要資料庫複寫資料庫的變更。 這會提供的一些其他手動資料同步處理技術本文內潛在替代方案。 如需詳細資訊，請參閱[概觀︰ SQL 資料庫作用中地理複寫](../sql-database/sql-database-geo-replication-overview.md)。

您也可以使用其他手動的方法在備份與還原。 您可以使用 [資料庫複製] 命令來建立資料庫的複本。 您必須使用這個命令來取得備份與交易的一致性。 您也可以使用 [匯入/匯出服務的 Azure SQL 資料庫。 這個選項支援匯出資料庫 BACPAC 檔案儲存於 Azure Blob 儲存體。

Azure 儲存空間的內建重複相同的區域中建立備份檔案的兩個複本。 不過，執行備份的程序的頻率決定您 RPO，這是您可能會遺失損毀案例中的資料量。 例如，假設您執行備份頂端的小時、 發生頂端的小時前的兩分鐘損毀。 您會遺失 58 分鐘發生後執行最後一個備份的資料。 此外，若要防止整個區域的服務中斷的情況，您應該將 BACPAC 檔案複製到替代的區域。 然後，您會有還原這些備份替代區域中的選項。 如需詳細資訊，請參閱[概觀︰ 雲端與 SQL 資料庫的業務連續性和資料庫損毀修復](../sql-database/sql-database-business-continuity.md)。

Azure 儲存空間，您可以開發您自己自訂的備份程序，或使用其中一個許多協力廠商備份工具。 請注意，大部分的應用程式設計額外複雜位置儲存資源彼此參考。 例如，請考慮 Azure 儲存體中有一個資料行連結至 blob 的 SQL 資料庫。 如果不同時發生備份，資料庫可能沒有失敗前備份 blob 的指標。 應用程式或復原計劃必須執行復原之後處理此不一致的程序。

###<a name="reference-data-pattern-for-disaster-recovery"></a>參考資料損毀修復模式

參考資料是唯讀的支援應用程式的功能。 通常不會變更常見問題。 雖然備份與還原處理整個區域的服務中斷的其中一個方法，RTO 是超長。 當您部署至第二個區域的應用程式時，某些策略可以改善的 RTO 參考資料。

因為參照的資料變更不常，您可以改善 RTO 維護的第二區域中的參考資料永久複本。 如此還原備份損毀事件所需的時間。 若要符合多個區域損毀復原需求，您必須部署應用程式與多個區域放在一起的參考資料。 [參考資料可用性模式](./resiliency-high-availability-azure-applications.md#reference-data-pattern-for-high-availability)所述，您可以將參考資料部署本身，外部的儲存空間，或組合的角色兩者。

參考資料部署模型內運算節點隱含滿足損毀復原需求。 參考資料部署到 SQL 資料庫需要您將一份參考資料部署至每個區域。 相同的策略適用於 Azure 儲存體。 您必須部署任何參考資料主要和次要區域 Azure 儲存體中所儲存的複本。

![主要和次要區域的參考資料出版物](./media/resiliency-disaster-recovery-azure-applications/reference-data-publication-to-both-primary-and-secondary-regions.png)

您必須執行您自己的特定應用程式的備份常式的所有資料，包括參考資料。 在區域的地理複寫複製只能用於整個區域的服務中斷的情況。 若要防止延長停機時間，請部署應用程式的資料的重要部分至第二個區域。 如需此拓撲的範例，請參閱[主動被動模型](#active-passive)。

###<a name="transactional-data-pattern-for-disaster-recovery"></a>交易資料損毀修復模式

功能完整的損毀模式策略實作需要非同步交易資料複寫到第二個區域。 在其中進行複寫的實際時間 windows 會決定 RPO 特性的應用程式。 您可能仍復原複寫期間遺失的主要區域的資料。 您也可以稍後合併與第二個區域。

下列架構範例處理容錯移轉情境中操作的交易資料的不同方式提供一些想法。 請務必請注意以下範例並不詳盡。 例如，例如佇列中繼的儲存位置可能會被取代 Azure SQL 資料庫。 本身的佇列可能 Azure 儲存體或 Azure 服務匯流排佇列 （請參閱[Azure 佇列服務匯流排佇列-比較和相對於](../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)）。 伺服器儲存空間目的地可能也會有所不同，如 Azure 資料表，而不是 SQL 資料庫。 此外，可能會插入工作者角色為媒介各種不同的步驟。 重點是不完全，模擬這些架構，但各種考慮恢復交易資料和相關的模組。

####<a name="replication-of-transactional-data-in-preparation-for-disaster-recovery"></a>交易資料損毀修復準備的複寫

請考慮使用 Azure 儲存體佇列保留交易資料的應用程式。 這個選項可讓工作者角色與處理程序中的低耦合架構伺服器資料庫交易的資料。 需要交易，使用某種暫時快取的前端角色需要立即引起其他查詢的資料。 根據容錯層級的資料遺失，您可以選擇複製佇列、 資料庫或所有的儲存空間資源。 只有資料庫複寫如果的主要區域當機，您可以仍復原佇列中的資料時的主要區域恢復。

下圖顯示架構伺服器資料庫位置同步處理過的區域。

![交易資料損毀修復準備的複寫](./media/resiliency-disaster-recovery-azure-applications/replicate-transactional-data-in-preparation-for-disaster-recovery.png)

最大的挑戰實作此架構是複寫策略之間的區域。 Azure SQL 資料同步處理服務可讓此類型的複寫。 不過，服務仍在預覽中，不建議使用生產環境。 如需詳細資訊，請參閱[概觀︰ 雲端與 SQL 資料庫的業務連續性和資料庫損毀修復](../sql-database/sql-database-business-continuity.md)。 生產應用程式，您必須投資協力廠商解決方案或程式碼中建立您自己的複寫邏輯。 根據架構，複寫可能雙向，也是更複雜。

一個可能的實作可能會使 [使用中繼佇列中的上一個範例。 處理資料的最後一個儲存目的地工作者角色可能的主要區域與第二區域中進行變更。 這些不是一般工作，並完成指導複寫碼超出本文的範圍。 很重要的一點是很多時間和測試應該著重於您如何複製資料到第二個區域。 其他處理及測試，可協助確保容錯移轉及修復程序正確地處理任何可能的資料不一致或重複的交易。

>[AZURE.NOTE] 這本白皮書的大部分著重於在平台服務 (PaaS)。 不過，混合式應用程式的其他複寫及可用性選項使用 Azure 虛擬機器。 這些混合式應用程式使用中 Azure 虛擬機器上裝載 SQL Server 服務 (IaaS) 為基礎結構。 如此傳統可用性方法在 SQL Server，例如 AlwaysOn 可用性群組] 或 [傳送記錄檔中。 一些技術，例如 AlwaysOn，之間執行工作只有內部部署的 SQL Server 執行個體和 Azure 虛擬機器。 如需詳細資訊，請參閱[可用性和損毀修復的 SQL Server Azure 虛擬機器中的商務連絡人](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md)。

####<a name="degraded-application-mode-for-transaction-capture"></a>交易擷取降級應用程式模式

降級模式中，請考慮的運作方式的第二個架構。 第二個區域上的應用程式會停用所有功能，例如報表、 商務智慧 (BI)，或清空佇列。 所定義的業務需求，則會接受只最重要的交易的工作流程類型。 系統會擷取交易，並將它們寫入佇列。 系統可能會延後服務中斷的初始階段期間處理資料。 如果主要區域中的系統預期的時間範圍內重新啟動，主要區域的工作者角色消耗佇列。 此程序不需要資料庫合併。 如果主要區域服務中斷超出可以接受視窗時，應用程式可以開始處理佇列。

在此案例中，在次要資料庫會包含必須合併後重新啟動主要的累加交易資料。 下圖顯示此策略暫時儲存交易資料，直到還原的主要區域。

![交易擷取降級應用程式模式](./media/resiliency-disaster-recovery-azure-applications/degraded-application-mode-for-transaction-capture.png)

如需詳細資料管理技術性 Azure 應用程式的資訊，請參閱[保全︰ 有彈性的雲端架構提供的指導](https://channel9.msdn.com/Series/FailSafe)。

##<a name="deployment-topologies-for-disaster-recovery"></a>部署拓撲損毀修復

您必須先準備重要的應用程式的整個區域的服務中斷的可能性。 您只要將多個區域部署策略合併至操作的規劃。

多個區域部署涉及發佈至第二個區域的應用程式和參考資料之後損毀, 的 IT 專業人員程序。 在應用程式需要立即容錯移轉，主動/被動設定或主動/主動設定，可能需要部署程序。 這種部署有現有的執行個體執行的其他區域中的應用程式。 Azure 流量管理員 」 等路由工具提供的 DNS 層級的負載平衡服務。 它可以偵測服務中斷，並將使用者傳送到時所需的不同區域。

成功 Azure 損毀復原的一部分從開始解決方案架構的復原。 雲端提供修復損毀期間失敗的傳統的主機服務提供者中並沒有提供的其他選項。 具體來說，您可以快速地以動態方式配置不同區域的資源。 因此，您就不會支付許多閒置資源在您等待發生失敗時。

下列各節討論不同的部署拓撲損毀修復。 一般而言，則表示折衷成本提高或複雜的額外的可用性。

###<a name="single-region-deployment"></a>單一區域部署

單一區域部署不是真正的損毀復原拓撲，但就是要與其他架構的對比。 單一區域部署有的 Azure 中的應用程式。 不過，這種部署不嚴重 contender 損毀復原計劃。

下圖說明的應用程式的單一 Azure 區域。 Azure 流量管理員和的錯誤和升級網域使用增加的區域內的應用程式的可用性。

![單一區域部署](./media/resiliency-disaster-recovery-azure-applications/single-region-deployment.png)

這裡是失敗的明顯資料庫是失敗的單一。 即使 Azure 會將資料複製到內部複本的不同故障網域，此所有就會發生相同的區域。 應用程式無法承受嚴重失敗。 如果地區當機，所有故障網域向下，包括所有服務執行個體和儲存資源。

對於所有但最重要的應用程式，您必須制訂計劃以將您的應用程式部署跨多個區域。 您也應該考慮 RTO，並在考慮使用哪個部署拓撲限制的成本。

讓我們來看看現在支援在不同區域的容錯移轉特定模式。 所有這些範例使用兩個區域描述程序。

###<a name="redeployment-to-a-secondary-azure-region"></a>重新部署至第二個 Azure 區域

在重新部署至第二個區域的圖樣，只的主要區域會有應用程式與執行的資料庫。 第二個地區沒有設定自動的容錯移轉。 因此當發生損毀，您必須微調設定新的區域中的服務的所有部分。 這包含上傳到 Azure 部署雲端服務，請還原資料，及變更重設流量的 DNS 的雲端服務。

雖然這是最實惠地的多個區域的選項，它會有表現最差 RTO 特性。 服務套件及資料庫備份的儲存此模型的 [內部部署或第二個區域的 Azure Blob 儲存體執行個體。 不過，您必須部署新的服務，並繼續作業之前，請還原資料。 即使您完全自動化從備份的儲存空間的資料傳輸，設定新的資料庫環境旋轉會耗費很多時間。 將資料從備份磁碟儲存空間移至 [空的資料庫，在第二個區域是最高還原程序。 您必須執行這個動作，不過，以顯示新的資料庫作業狀態，因為其無法複製。

最好的方法是將服務套件 Blob 儲存體中的第二區域中。 這不需要套件上傳至 Azure，也就是從內部部署開發電腦部署時，會發生什麼情況。 您可以快速服務套件部署至新的雲端服務從 Blob 儲存體使用 PowerShell 指令碼。

僅適用於非關鍵的應用程式的可容許高 RTO 實務，這個選項。 例如，這可能運作的應用程式，可以向下幾個小時但應該執行一次在 24 小時內。

![重新部署至第二個 Azure 區域](./media/resiliency-disaster-recovery-azure-applications/redeploy-to-a-secondary-azure-region.png)

###<a name="active-passive"></a>主動被動

主動被動模式是有許多公司喜好的選擇。 此模式重新部署模式以使用成本增加的較小的 RTO 提供改良。
在此案例中，有一次一個主要和次要 Azure 區域。 所有流量主要區域上移至 [作用中的部署。 因為兩個區域執行的資料庫損毀修復最好被準備第二個區域。 此外，同步處理機制是在它們之間的位置。 此備用方法可以包含兩個變化︰ 僅資料庫的方法或第二區域中的完成部署。

####<a name="database-only"></a>只有資料庫

在第一個變化的主動被動模式，請只的主要區域會有部署的雲端服務應用程式。 不過，重新部署圖樣與兩個區域會與同步處理資料庫的內容。 （如需詳細資訊，請參閱上[交易資料損毀修復模式](#transactional-data-pattern-for-disaster-recovery)的一節）。當發生損毀時，有較少的啟動要求。 您啟動應用程式中的次要地區、 新的資料庫，變更連接字串，以及變更重設流量的 DNS 項目。

重新部署圖樣，例如應該已儲存的服務套件中更快速部署的第二區域中的 Azure Blob 儲存體。 不同的重新部署模式中，於您不會造成大部分的資料庫還原作業需要開銷。 準備及執行的資料庫。 節省大量時間，讓此實惠地的 DR 圖樣。 這也是最常用的 DR 圖樣。

![只在主動被動資料庫](./media/resiliency-disaster-recovery-azure-applications/active-passive-database-only.png)

####<a name="full-replica"></a>完整複本

第二個變化的主動被動模式，請在主要的地區和次要地區擁有完整部署。 此部署包含雲端服務和同步處理的資料庫。 不過，只的主要區域正在處理網路要求使用者。 只在主要區域發生服務中斷時，才次要區域會變成作用中。 在此情況下，所有新的網路要求路由傳送至第二個區域。 Azure 流量管理員可以自動管理此容錯移轉。

容錯移轉就會發生比資料庫專用變化更快，因為服務已部署。 此模式提供極低 RTO。 第二個容錯移轉地區必須準備好緊接失敗的主要區域。

更快速地回應時間，以及這個模式的優點預先配置和部署備份的服務。 您不必擔心沒有配置新的執行個體中損毀的空間的區域。 這是重要事項如果您的第二個 Azure 地區即將容量。 不保證有 （服務等級協定），您會立即能夠部署新的雲端服務，任何區域中的數字。

含有此模型的最快的回應時間，您必須類似的小數位數 （角色執行個體數目） 在主要和次要區域]。 優點，還是支付未使用的計算執行個體高，而這可能不是最小心謹慎財務選項。 因此，是常見次要區域上使用雲端服務的位置略有縮小的版本。 然後您可以快速容錯以及延展次要部署，如有必要。 您應該自動化容錯移轉程序，以便無法存取的主要區域後，您必須啟動其他的執行個體，根據載入。 這可能需要使用像[虛擬機器縮放比例設定](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)為自動縮放機制。

下圖顯示主要和次要區域位置包含完整部署的雲端服務中主動被動圖樣的模型。

![主動被動、 完整複本](./media/resiliency-disaster-recovery-azure-applications/active-passive-full-replica.png)

###<a name="active-active"></a>作用中作用中

現在，您可能會找出模式的功用︰ 減少 RTO 會增加成本與複雜度。 主動-主動解決方案實際中斷此容易通信成本。

在作用中作用中的模式中，在雲端服務和資料庫會完全部署在兩個區域。 不同於主動被動模型，兩個區域會收到使用者流量。 這個選項會產生的最快修復時間。 服務已調整，以處理在每個區域載入的一部分。 若要使用的第二個地區已啟用 DNS。 決定如何將使用者傳送到適當的地區有其他的複雜性。 循環排程可能會。 有其他特定使用者想要使用其資料的主要複本的所在位置的特定區域。

容錯移轉時，若只要停 DNS 用到的主要區域。 這路由至第二個區域的所有流量。

即使是在此模式中，有一些變化。 例如下, 圖顯示模型的主要區域擁有資料庫的主要複本的位置。 在兩個區域中的雲端服務的寫入的主要資料庫。 從主要或複製的資料庫，可以讀取次要部署。 在此範例中的試驗，這時其中一個方法。

![作用中作用中](./media/resiliency-disaster-recovery-azure-applications/active-active.png)

有個缺點主動-主動架構在先前的圖表。 第二個區域必須存取的第一個區域中的資料庫，因為的正本那里所在。 效能大幅卸除關閉當您存取區域外的資料。 跨地區資料庫通話，您應該考慮某些類型的批次處理策略改善這些呼叫的效能。 如需詳細資訊，請參閱[如何使用批次來改善 SQL 資料庫應用程式的效能](../sql-database/sql-database-use-batching-to-improve-performance.md)。

替代的結構，可能需要直接存取其本身的資料庫每個區域。 在模型，某些類型的雙向複寫，才能同步處理中每個區域的資料庫。

主動-主動模式中，您可能不需要任何數量上執行個體的主要區域即主動被動模式中。 如果您有 10 個執行個體中主動被動架構的主要區域上時，您可能需要只 5 主動-主動架構中每個區域。 兩個區域現在共用載入。 如果您繼續使用暖備用被動式區域上 10 個執行個體等待容錯移轉，這可能是透過主動被動圖樣省下成本。

瞭解您要還原的主要區域，直到第二個區域可能會收到突然的新使用者。 如果有 10000 個使用者在每個伺服器上的主要區域發生服務中斷時，請次要地區突然必須處理 20000 的使用者。 監控次要區域上的規則必須偵測增加和雙執行個體中的第二個區域。 詳細資訊，請參閱[偵測](#failure-detection)上的 [] 區段。

##<a name="hybrid-on-premises-and-cloud-solution"></a>混合式內部部署與雲端解決方案

一個其他策略損毀修復是設計混合式執行應用程式，內部部署和雲端中。 根據應用程式的主要區域可能任一位置。 考慮在先前的架構，並假設主要或次要區域為內部部署的位置。

這些混合式架構中有部分的挑戰。 首先，大部分的本文已解決 PaaS 架構模式。 Azure 中的一般 PaaS 應用程式依賴 Azure 特定建構，例如角色、 雲端服務及流量管理員。 若要建立此類型的 PaaS 應用程式的內部部署解決方案需要明顯不同的結構。 看起來可能不可行管理或成本觀點。

不過，損毀修復的混合式解決方案有較少的挑戰，只是移至雲端的傳統架構。 這是對的架構，將 IaaS。 IaaS 應用程式會使用虛擬機器在雲端的能直接內部部署對應項目。 您也可以使用虛擬網路連線在雲端的電腦與內部部署網路資源。 這樣就無法使用 PaaS 專用的應用程式的可能性。 例如，SQL Server 可以利用損毀修復解決方案，例如 AlwaysOn 可用性群組和資料庫鏡像。 如需詳細資訊，請參閱[可用性和 Azure 虛擬機器中的 SQL server 損毀修復](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md)。

IaaS 解決方案也會提供更容易路徑內部部署應用程式 Azure 作為容錯移轉選項。 在 [現有的內部部署區域中，您可能需要完整的應用程式。 不過，如果您沒有維護的容錯移轉的地理位置不同區域的資源？ 您可能決定要使用的虛擬機器和虛擬網路取得 Azure 中執行的應用程式。 在此情況下，定義同步處理至雲端的資料的程序。 Azure 部署然後會變成要用於容錯移轉的第二個區域。 主要區域保持內部部署的應用程式。 如需有關 IaaS 架構與功能的詳細資訊，請參閱[虛擬機器文件](https://azure.microsoft.com/documentation/services/virtual-machines/)。

##<a name="alternative-cloud"></a>替代雲端

有位置甚至的 Microsoft Cloud 強固可能不符合內部法規遵循的規則] 或 [您的組織要求的原則的情況。 即使最佳的準備及實作備份系統期間損毀的設計及格如果全域的服務中斷雲端服務提供者。

您會想要比較的成本和複雜度提高可用性可用性要求。 執行風險分析，並定義 RTO 和 RPO 解決方案。 如果您的應用程式不允許任何停機時間，這可能會使適合您，請考慮使用另一種雲端解決方案。 除非整個網際網路當機，另一種雲端解決方案可能仍可如果 Azure 變成全域無法存取。

就跟混合式案例中，也可以在另一個雲端方案存在在上一個損毀復原架構的容錯移轉部署。 替代雲端 DR 網站應僅適用於其 RTO 可讓極少，如果有的話，停機時間的解決方案。 請注意，使用外部 Azure DR 網站的解決方案會需要更多設定、 開發、 部署及維護工作。 也很難實作中的跨雲端架構的最佳作法。 雖然雲端平台有類似高層級的概念，Api 與架構會不同。

如果您決定要分割您 DR 不同的平台之間，它會使應設計抽象解決方案的設計中的圖層。 如果這麼一來，您不需要開發和維護的不同雲端若損毀的平台的相同的應用程式的兩個不同版本。 為混合式案例中，使用 Azure 虛擬機器或 Azure 容器服務可能您更輕鬆地在這些情況下使用雲端特定 PaaS 設計。

##<a name="automation"></a>自動化

我們討論的模式的一些需要離線部署的快速啟動及還原系統的特定部分。 自動化，或指令碼、 支援的功能啟動視需要的資源，並快速部署解決方案。 在本文中 DR 相關自動化相等使用[PowerShell 的 Azure](https://msdn.microsoft.com/library/azure/jj156055.aspx)，但[服務管理 REST API](https://msdn.microsoft.com/library/azure/ee460799.aspx)也是其中一個選項。

開發指令碼協助管理 Azure 不透明處理的 DR 部分。 這會有每次，盡量人力錯誤製作一致的結果的優勢。 有預先定義的 DR 指令碼時，也會減少重建系統和構成部份在損毀的時間。 您不想要嘗試手動了解如何還原您的網站，會向下及遺失金錢每分鐘。

您可以建立指令碼之後，請先測試重複的完整流程。 驗證基本功能之後，請確定您進行中[損毀模擬](#disaster-simulation)進行測試。 這可協助發掘指令碼或程序中的問題。

自動化的最佳作法是，建立的 PowerShell 指令碼或命令列介面 (CLI) 的指令碼 Azure 損毀修復存放庫。 清楚標示，並輕鬆查閱分類。 指定人員管理存放庫和指令碼的版本設定。 他們也很簡單的參數和指令碼使用範例說明與文件。 同時請確定您保持這份文件同步與 Azure 部署。 此強調有一個人員將存放庫內的所有部份的目的。

##<a name="failure-detection"></a>錯誤偵測

若要正確處理可用性與損毀修復問題，您必須能夠偵測並診斷失敗。 您應執行進階的伺服器及部署監控，讓您可以快速瞭解系統或其部分時突然向下。 監視查看雲端服務，其相依性的整體健康情況的工具，可以執行這項工作的組件。 Microsoft 的一種工具是[系統管理中心 2016年](https://www.microsoft.com/en-us/server-cloud/products/system-center-2016/)。 協力廠商工具也可以提供監控功能。 大部分的監控解決方案追蹤關鍵效能計數器和服務的可用性。

雖然這些工具是很重要，但它們不會取代需要規劃的錯誤偵測和報告中雲端服務。 您必須正確地使用 Azure 診斷規劃。 自訂效能計數器或事件記錄項目也可以是整體策略的一部分。 這會提供更多資料期間失敗快速診斷問題，並還原完整功能。 同時也會提供其他指標監控工具可以用來決定應用程式健康狀況。 如需詳細資訊，請參閱[Azure 雲端服務中的 [啟用 Azure 診斷](../cloud-services/cloud-services-dotnet-diagnostics.md)。 如何規劃 」 狀況模型 」 的整體的討論，請參閱[保全︰ 有彈性的雲端架構提供的指導](https://channel9.msdn.com/Series/FailSafe)。

##<a name="disaster-simulation"></a>模擬損毀

模擬測試需要建立觀察小組成員要如何回應工作 floor 小型的實際狀況。 模擬也會顯示如何有效的解決方案是在復原計劃。 執行模擬的方式建立的分析藍本不會實際商務中斷時仍覺得實數的情況。

考慮一種來手動模擬可用性問題應用程式中的 「 切換表單 」。 例如，透過柔邊的參數，觸發程序排序的模組資料庫存取例外會發生問題。 您可以採取其他模組的類似輕量型方法網路介面層級。

模擬醒目提示任何已不夠解決的問題。 模擬分析藍本必須完全可控制。 這表示，即使復原計劃可能會失敗，您可以還原這種情況回 [標準不會導致任何有效的傷害。 請務必也會影響較高層級管理相關的模擬練習何時及如何將會執行。 此計劃應該包含時間或模擬測試執行時可能會掉的資源資訊。 當您正在測試接受您復原計劃時，也很重要定義成功測量的方式。

有數種測試損毀復原計劃，您可以使用其他技巧。 不過，大部分的是這些基本技巧只要變更之版本。 前這項測試主要動機是評估方式可行方式可行復原計劃是。 測試的重點在於探索文章中的基本復原計劃的詳細資料損毀修復。

##<a name="next-steps"></a>後續步驟

本文是一系列的文章著重於[損毀修復和內建在 Microsoft Azure 應用程式的可用性](./resiliency-disaster-recovery-high-availability-azure-applications.md)。 本系列前一篇文章是[Microsoft Azure 應用程式的可用性](./resiliency-high-availability-azure-applications.md)。
